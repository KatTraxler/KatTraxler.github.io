---
layout: post
title:  "GCP Users and Group 101"
date:   2020-12-05
categories: GCP IAM 101
---


Users and Groups are two types of identities which can be assigned IAM Roles in GCP.  When Users and Groups are members in IAM Policy, they are referenced by their email addresses.

There is no directory in GCP containing Users or Groups.  Instead, Users and Groups live in your the GSuite directory for your registered Google Domain. 
An inventory of the Users and Groups in your domain is found in their own console,  `admin.google.com/ac/users `.  
This is a console completely apart from the GCP Cloud Console, `console.cloud.google.com`. 

In GCP, you can assign Roles directly to Users however, this pattern is bound to face management and scalability problems.  Instead, consider placing Users in Groups and assigning GCP IAM Roles to the Groups.

Defaults:
	- **ANY** Google User or Group in **ANY** domain can be assigned IAM Roles in **ANY** Organization or Project in any domain.
		* Example: The GCP Organization for ` test.com ` is not limited to only having IAM Roles assigned to Users and Groups with ` test.com ` email addresses.
		* To limit WHO can have IAM Roles in your GCP Organization, employ the Organizational Policy Constraint `constraints/iam.allowedPolicyMemberDomains` where by you can whitelist allowed domains.
	- If you have a GCP Organization associated to your GSuite Directory, all Users in your Directory can create Projects and configure billing by default.  These permissions are automatically inherited and need to be explicitly disabled in GCP at the Organization level.



References:
**Side Note**: GSuite was recently renamed Google Workspace
https://cloud.google.com/resource-manager/docs/organization-policy/restricting-domains
https://cloud.google.com/resource-manager/docs/default-access-control


