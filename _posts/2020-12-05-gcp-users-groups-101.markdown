---
layout: post
title:  "GCP Users and Group 101"
date:   2020-12-05
categories: GCP IAM 101
---


**Users and Groups** are two types of principals that can be granted access in GCP. When users and groups are members in IAM Policies, they are referenced by their Google email addresses.

You won't find a directory in Google Cloud listing users or groups. Instead, users and groups are managed within the Google Workspace directory (formerly GSuite) of your registered Google Domain. Alternatively, groups and group memberships can be managed using [Cloud Identity](https://cloud.google.com/identity/docs/reference/rest), a service within Google Cloud.

An inventory of the users and groups within your domain can be accessed through the Google Workspace [Admin Console](https://admin.google.com/ac/users).


**Defaults**

1. Cross-Organization Access Allowed   

**ANY** Google user or group in **ANY** domain can be assigned IAM Roles in **ANY** organization or project in any domain.   

 - Example: The GCP Organization for the domain `test.com` can have IAM Policy granting principals from `example.org` access to their resources.
 - To limit **WHO** can be a member in IAM Policy, employ the Organizational Policy Constraint `[constraints/iam.allowedPolicyMemberDomains](https://cloud.google.com/resource-manager/docs/organization-policy/restricting-domains)` where by you can define an allowed list of approved domains.

1. All Users can Create GCP Projects and Resources      

By default, when GCP is enabled within Google Workspace, users have the ability to create projects and create a billing account. Consequently, creators of projects are automatically granted the powerful owner role for the projects they create.

To manage this default behavior:

- First, create designated billing accounts and project creators.
- Then, [manually remove](https://cloud.google.com/resource-manager/docs/default-access-control#removing-default-roles) the default roles (Project Creator and Billing Account Creator) from the organization node.

