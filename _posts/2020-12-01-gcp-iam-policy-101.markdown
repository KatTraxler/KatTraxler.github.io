---
layout: post
title:  "GCP IAM Policy 101"
date:   2020-12-01
categories: GCP IAM 101
---


The GCP model for managing access to resources has three main parts:

- **Who**: (the principal)
- **What**: (the role)
- **Resource**

In Google Cloud Platform (GCP), permissions are granted by combining the **WHO** (principal) and the **WHAT** (role) to form a policy. This policy is then applied, or 'bound', to a specific resource. By doing so, permissions are granted and a scope is created.   

**[Who](https://cloud.google.com/iam/docs/overview#concepts_related_identity)**: The principals in policy are called members and can be one of a handful of identities. Users and groups live and are managed in either Google Workspace (formerly GSuite) or Cloud Identity, while Service Accounts and the Identity Namespace live as resources in the Google Cloud Platform. A valid policy member can also be a domain or a special group such as 'AllUsers' or 'AllAuthenticatedUsers'.


**[Role](https://cloud.google.com/iam/docs/overview#roles)**: A role is a collection of permissions. Permissions determine what actions you can take on a resource. When a role is assigned to a member, you grant all the permissions contained in that role.

**[Resource](https://cloud.google.com/iam/docs/overview#resource-hierarchy)**: 
In GCP's resource hierarchy, each level — be it the top-level organization node, folder, or project node — is a resource capable of having an IAM policy attached. This applies not only to the organization/folder/project structure but also to all child resources of projects.  
Permissions are granted to a principal only when a policy is attached/bound to a resource and are inherited to all downstream child resources.
