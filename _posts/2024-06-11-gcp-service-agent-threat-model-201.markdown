---
layout: post
title:  "GCP IAM 201 - Service Agents | What Can Go Wrong"
date:   2024-06-11
categories: GCP IAM 201
---

<br>
This post aims to answer the question, 'What can go wrong when using Google Cloud services that leverage Service Agents/P4SAs?' For an introduction to Service Agents, see [GCP IAM 201 - Service Agents](../../06/02/gcp-service-agents-201.html).   
Below, you'll find an overview of possible threats to Google Cloud customers due to the presence of Service Agents organized around the [STRIDE framework](https://owasp.org/www-community/Threat_Modeling_Process#stride).


## Spoofing

Managed impersonation brokered via Google Cloud IAM permissions (e.g., `.getAccessToken`, `.getOpenIdToken`) is an abuse path for Service Agents with a presumably high bar to clear and whose controls reside squarely on the provider side of the [Shared Responsibility Model](https://cloudsecurityalliance.org/blog/2020/08/26/shared-responsibility-model-explained). This is due to where Service Agents live in a Google-managed project, and assigning permissions requires `setIamPolicy` permission within the Google-controlled tenant.

In addition to managed impersonation, credential hijacking/theft should be considered a possible abuse case for Service Agents. Like all principals, they use OAuth tokens to authenticate to Google Cloud APIs, and spoofing may involve the theft of these temporary bearer tokens.

## Tampering

Tampering or maliciously modifying Service Agents would require an attacker to clear the same high bar described in the Spoofing category, as these resources reside within Google-managed projects.


## Repudiation

Non-repudiation refers to the ability to ensure that actions cannot be denied. In the cloud, this often involves ensuring that API calls are properly logged so there is an audit trail verifying what actions were taken and by whom.

Service Agent activity within a consumer project is logged alongside all other principals’ activities. However, the name of the principal who initiated the transaction is not written in the logs. For instance, an end user may deploy an application that uses a backing Service Agent. When the user creates a resource, the Service Agent may make a series of API calls within the project. Cloud Logging does not record who initiated the actions taken by the Service Agent. Determining this often requires manual log analysis to piece together an educated guess.


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

