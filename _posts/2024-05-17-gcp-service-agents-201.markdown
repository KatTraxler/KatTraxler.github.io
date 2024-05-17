---
layout: post
title:  "GCP IAM 201 - Service Agents"
date:   2024-05-17
categories: GCP IAM 201
---

<br>
[Service Agents](https://cloud.google.com/iam/docs/service-agents) are nothing more than a type of a [Service Account](https://cloud.google.com/iam/docs/overview#service_account).   
Also known as 'P4SAs' (Per-Project, Per-Product Service Account), Service Agents are typically created automatically whenever their associated Google API is enabled in a Project.   


## Service Agent and Service Account Comparison


|    | Service Accounts | P4SAs |
| ---- | ------ | ------ |
| Managed By: | Customer-managed | Google-managed |
| Lives Where: | Resource resides in a consumer project | Resource resides in a google project |
| Typical Usage: | Used by the customer at their discretion | Used as service identity |
| Typical Role Assignments: | Can be assigned any role | Automatically assigned 'ServiceAgent' roles, typically at the Project-level |
| Credentials: | Customers have several options for [authenticating](https://cloud.google.com/iam/docs/service-account-creds) Service Accounts | Credentials should not be accessible to customers |


## Customer-Managed Versus Google-Managed

Service Accounts are often called 'customer-managed,' where 'managed' refers to the Project in which the Service Account resides.   
A customer-managed service account is a resource whose parent is a consumer project, i.e., one you own. As it is a resource in your project, you have full authority over its creation, deletion, and  permissions.   
On the other hand, any Service Account that resides in a Google-owned project is considered 'Google-managed'. A Service Agent/P4SA is one such example of a 'Google-managed' resource.


## Common Usage and Permissions

A single Service Agent/P4SA (Per-Project, Per-Product Service Account) is created for every project and product requiring Cloud IAM permissions. This architecture creates a one-to-one ratio between the P4SA and consumer projects, restricting a possible path for multi-tenant abuses.

Typically, a P4SA is granted roles at the project-level to act as the identity for services performing background tasks. To view the roles assigned to P4SAs in the GCP console, navigate to the IAM screen and check the box labeled 'Include Google-provided role grants'. Without checking this box, the project-level roles assigned to P4SAs are hidden from view.

![check the box](/assets/images/check-the-box.png)

### Examples of P4SAs and Their Usage

- The [Eventarc Service Agent](https://cloud.google.com/iam/docs/service-agents#eventarc-service-agent) needs project-level permissions to publish events to Pub/Sub topics.
- The [Workflows Service Agent](https://cloud.google.com/iam/docs/service-agents#cloud-workflows-service-agent) requires project-level permissions to generate access tokens for other service accounts and inject the Authorization header for the runtime service account when sending authenticated requests from a workflow.
- The [Cloud Build Service Agent](https://cloud.google.com/iam/docs/service-agents#cloud-build-service-agent) needs multiple project-level permissions to create Binary Authorization attestations and impersonate the Build runtime service account.


## How are Permissions Assigned?

The [Service Agent Manager Service Account](https://cloud.google.com/iam/docs/service-account-types#role-manager), `service-agent-manager@system.gserviceaccount.com`, is responsible for granting roles to Service Agents in consumer projects. Although its permissions are not visible to end users, it has been observed assigning Cloud IAM roles to Service Agents at the project-level.


## But Wait There's More

For more on the risk and potential impact of compromise, see the blog post: [GCP IAM 201 - Service Agent Threat Model](./gcp-service-agent-threat-model-201.html)





