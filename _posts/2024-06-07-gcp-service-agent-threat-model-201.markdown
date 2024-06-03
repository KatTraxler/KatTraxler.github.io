---
layout: post
title:  "GCP IAM 201 - Service Agent Threat Model"
date:   2024-05-17
categories: GCP IAM 201
---

<br>
 Before continuing, please be sure to read the introduction the introduction blog post, [GCP IAM 201 - Service Agents](./gcp-service-agents-201.html).


## Limiting the Impact Between Tenants

The P4SA pattern limits cross-tenant impact by confining privilege escalation to the affected customer if a Service Agent/P4SA is compromised, preventing lateral movement between tenants. A compromised Service Agent/P4SA cannot be used as a lateral movement vehicle between multiple tenants through Google-configured access.

## Risk of Impersonation

Service Account impersonation is the ability to generate credentials, typically OAuth access tokens or OpenID Connect ID tokens for a service account, controlled by `iam.serviceAccount.getAccessToken` and `iam.serviceAccount.getOpenIdToken`. See the diagram below for an explanation as to why an adversary is unlikely to achieve this with a Service Agent/P4SA.

![Service Agents 1](/assets/images/service-agent-tm-1.png)

## Risk of P4SA Credential Access via GCE Metadata Server

The GCE metadata server is a process accessible from specific cloud resources that will vend the credentials of the assigned runtime service account. It doesn't require authentication or authorization; security is defined by logical network access. As per [Google Cloud documentation](https://cloud.google.com/compute/docs/metadata/overview#metadata_security_considerations):

> Any process that can query the metadata URL has access to all values in the metadata server.

You can query the metadata server at `http://metadata.google.internal/computeMetadata/v1/instance/service-accounts/default/token` to retrieve the access token of the default service account.


## Risk of Transitive Access