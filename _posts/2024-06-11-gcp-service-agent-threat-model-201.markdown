---
layout: post
title:  "GCP IAM 201 - Service Agents | What Can Go Wrong"
date:   2024-06-11
categories: GCP IAM 201
---

<br>
This post aims to answer the question, 'What can go wrong?' when using Google Cloud services that leverage Service Agents/P4SAs. For an introduction to Service Agents, see [GCP IAM 201 - Service Agents](./gcp-service-agents-201.html). Below, is enumerated a set of possible threats to Google Cloud customers due to the presence of Service Agents, organized using the STRIDE framework.


## Spoofing

Impersonation managed by Google Cloud IAM permissions (e.g., `getAccessToken`, `GetOidcToken`) is an abuse path with presumably a high bar to clear, residing squarely on the provider side of the Shared Responsibility Model. This is because the Service Agent resource resides in a Google-managed project, and assigning any permissions requires `setIamPolicy` permission within the Google-controlled tenant.

In addition to managed impersonation, credential hijacking/theft should be considered a possible abuse case for Service Agents. P4SAs/Service Agents use OAuth tokens to authenticate to Google Cloud APIs, and spoofing may involve the theft of these temporary bearer tokens.

# Tampering

Tampering or maliciously modifying Service Agents would require the same high bar for access as these resources reside within Google-managed projects.


# Repudiation

Non-repudiation refers to the ability to ensure that actions cannot be denied. In the context of the cloud, this often involves ensuring that API calls are properly logged so there is an audit trail verifying what actions were taken and by whom.

Service Agent activity within a consumer project is logged alongside all other principal activity. However, what is not written in logs is the principal who initiated the transaction. For instance, an end user may deploy an application that uses a backing Service Agent. When the user creates a resource, it may trigger the Service Agent to make a series of API calls within the project. Cloud Logging does not record who initiated the actions taken by the Service Agent. Determining this often requires manual log analysis to piece together an educated guess.


## Information Disclosure

There is very little sensitive information related Service Agents/P4SAs themselves other than their credentials.  They are construction in a predictable pattern (`service-PROJECT_NUMBER@SERVICE_NAME.iam.gserviceaccount.com`), auto-assigned known roles and can be created independently of API enablement with the [Service Usage API](https://cloud.google.com/iam/docs/create-service-agents#create).   

Information disclosure threats may arise from Service Agents/P4SAs when their auto-assigned broad access to data in a consumer project.
For instance, the Compute Engine service agent (`service-PROJECT_NUMBER@compute-system.iam.gserviceaccount.com`) is assigned the `compute.serviceAgent` role, enabling it to interact with Cloud Storage objects. In scenarios where compute instances access Cloud Storage when retrieving raw image sources, a malicious actor could exploit this access by deploying a compute instance and leverage the service agent's permissions to access data in a Cloud Storage bucket. As described in the repudiation section, tracing such misuse may be challenging or impossible.


## Denial of Service

Denial of Service threats related to Service Agents could encompass removing the permissions assigned to them or the service account itself.   
Removing or updating the IAM Roles bound to Service Agents is entirely within the control of consumer projects. In contrast, since the service account resource lives in a google-managed project, this risk is considerably less likely.   

## Elevation of Privilege

Privilege escalation risks within the Service Agent/P4SA pattern may occur due to unexpected transitive access. 
Transitive access occurs when a user gains access to resources not directly through their own permissions, but through the permissions of another entity.  

An example of Privilege escalation resulting in information disclosure was provided above.  Key to this abuse of access is:
A. 

## A Note on Impact - Limiting the Blast Radius

The P4SA pattern limits cross-tenant impact by confining privilege escalation to the affected customer if a Service Agent/P4SA is compromised, preventing lateral movement between tenants. A compromised Service Agent/P4SA cannot be used as a lateral movement vehicle between multiple tenants through Google-configured access.


## Threat Model Wrap-Up

Successfully executing most threats articulated against the Service Agent/P4SA pattern depends on overcoming controls implemented by the Service Provider, CSP. The exception to this high bar may be threats leveraging an indirect/transitive access pattern or inadvertent access to temporary credentials.
