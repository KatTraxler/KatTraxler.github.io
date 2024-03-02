---
layout: post
title:  "GCP Service Account 101"
date:   2020-12-01
categories: GCP IAM 101
---


**[Service Accounts](https://cloud.google.com/iam/docs/overview#service_account)** are identities used for authenticating infrastructure and resources, typically for non-human entities. Service Accounts do not utilize passwords. While you have the option to generate and export a private key for a service account, it's generally unnecessary and can pose a [security risk](https://jryancanty.medium.com/stop-downloading-google-cloud-service-account-keys-1811d44a97d9) in most cases. The only valid scenario where generating and exporting a private key may be necessary is to enable external integrations in instances where [OIDC](https://docs.github.com/en/actions/deployment/security-hardening-your-deployments/configuring-openid-connect-in-google-cloud-platform) is not supported.
 

## Service Accounts as Resources

Service accounts are created within and associated with a specific project. *Think of a service account as having a 'home' project,* similar to how a compute instance or a storage bucket 'resides' in a project within the resource hierarchy.

Similar to other resources, you can use the *[setIAMPolicy](https://cloud.google.com/iam/docs/reference/rest/v1/projects.serviceAccounts/setIamPolicy)* method to attach IAM policies to a service account. IAM policies for a service account typically include permissions such as:

- generate an OAuth or OIDC token for the service account (impersonate)   
  - (.getAccessToken) (.getOpenIdToken)   
- attach a service account to a resource.   
  - (.actAs)
- generate keys for the service account.   
  - (iam.serviceAccountKeys.create)   
- manage the service account.    
  - (.create) (.delete) (.setIamPolicy)   


## Service Accounts as Identities**

In GCP, service accounts, like other identity types such as Users and Groups, can be included as members in IAM Policies and granted permissions at any level within the resource hierarchy. Service accounts are not restricted to receiving permissions only within their home project. Similar to users and groups, service accounts can have cross-project permissions or even have access to resources in other organizations.  

## Attaching a Policy to a Service Account

When configuring IAM Policies for a Service Account, the entity granted permissions (the WHO) can be another Service Account. It can be advantageous to establish a pattern where a Service Account is granted permissions to impersonate another Service Account.

In such scenarios, the Service Account performing the impersonation is the **WHO**, functioning as an identity. Meanwhile, the Service Account being impersonated serves as the target resource.


