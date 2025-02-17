---
layout: post
title:  "GCP IAM 201 - OAuth Scopes"
date:   2024-07-29
categories: GCP IAM 201
---

<br>
A scope is a feature of the OAuth 2.0 specification used when issuing tokens to limit the extent to which an access token can be used. In the context of authorization in Google-land, a scope defines which of the Google APIs and resources the access token can be used to access.  
If you only take away one thing about OAuth 2.0 Scopes for Google APIs, it it: <ins>do not use them as an access control mechanism<ins>.



## Google APIs and their Scopes

Over 250 [Google APIs](https://developers.google.com/identity/protocols/oauth2/scopes) can have their permissions delegated to a requesting application.  Applications and their APIs include well known products like Google Drive, Mail, Google Analytics and the Google Cloud SDK.

## Are OAuth Scopes Good For Access Control?

Given this potentially new found knowledge about OAuth 2.0 Scopes for Google APIs, you might be wondering if you can use this mechanism in conjunction with or instead of Cloud IAM for access control? My simple plea is, No.  When authorizing against Google Cloud APIs, allow the broad `https://www.googleapis.com/auth/cloud-platform` scope which is inclusive of all Google Cloud APIs and is the default requested when none is specified.


### Scope Restrictions Only Apply to Access Token

This may seem obvious but a scope set on an access token only restricts that credentials access to Google APIs.  Other mechanisms for accessing data on Google 


## Information Disclosure

There is very little sensitive information related to Service Agents/P4SAs themselves other than their credentials. They are constructed in a predictable pattern (`service-PROJECT_NUMBER@SERVICE_NAME.iam.gserviceaccount.com`), auto-assigned known roles, and can be created independently of API enablement with the [Service Usage API](https://cloud.google.com/iam/docs/create-service-agents#create).   

Information disclosure threats may arise from Service Agents/P4SAs when they are auto-assigned broad access to data.
For instance, the Compute Engine service agent (`service-PROJECT_NUMBER@compute-system.iam.gserviceaccount.com`) is assigned the `compute.serviceAgent` role, enabling it to interact with Cloud Storage objects. In scenarios where compute instances retrieve images from a cloud storage source, a malicious actor could utilize this access by leveraging the service agent's permissions by deploying instances to access data in a Cloud Storage bucket. As described in the repudiation section, tracing such misuse may be either challenging to impossible.


## Denial of Service

Denial of Service threats related to Service Agents could encompass removing the permissions assigned to them or the resource entirely.   
Removing or updating the IAM Roles bound to Service Agents is entirely within the control of consumer projects. In contrast, this risk of Service Agent deletion is considerably less likely since the underlying service account resource lives in a Google-managed project.   

## Elevation of Privilege

Privilege escalation risks within the Service Agent/P4SA pattern may occur due to unexpected transitive access. 
Transitive access occurs when a user gains access to resources not directly through their permissions but through the permissions of another entity.  

An example of privilege escalation was outlined in the Information Disclosure section.  Key to this abuse of transitive access is:

1. Automatic role assignments to Service Agents with excessive (typically project-level) access.
2. A services workflow exists that takes inputs from the end user.
3. Initiating a service agent's actions on 'X' only requires Service-level permissions and not permissions on the 'X' target. 

## A Note on Impact - Limiting the Blast Radius

The P4SA pattern limits cross-tenant impact by confining privilege escalation to the affected customer if a Service Agent/P4SA is compromised, preventing lateral movement between tenants. A compromised Service Agent/P4SA cannot be used as a lateral movement vehicle between multiple tenants through Google-configured access.


## Threat Model Wrap-Up

Successfully executing most threats articulated against the Service Agent/P4SA pattern depends on overcoming controls implemented by the Service Provider. The exception to this high bar may be threats leveraging an indirect/transitive access pattern or inadvertent access to temporary credentials.

