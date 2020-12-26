---
layout: post
title:  "GCP Roles and Permissions 101"
date:   2020-12-25
categories: GCP IAM 101
---

Its ultimately Cloud IAM Permissions which grant access to resources.  However, in GCP you do **not** and **cannot** assign individual permissions to an Identity. Instead, permissions are grouped together to form Roles. Its the Role, not the permission, that is granted to an Identity.  
Sometimes, a Role will only contain a single permission, other times it will contain hunderds of permissions so that the Role can enable some broader functionality.

**Permissions**

> A permission will always be detailed in the following three-part format: `[api].[resource].[action]`. Here's how that works. 
>
> The Cloud IAM API has a permission allowing Identities to update Roles in GCP, `iam.roles.update`.  The Container API also has a permission to update Roles, `container.roles.update`.  
> These two permissions do not allow for the configuration of the same resource. The Cloud IAM API and the Container API allow for management of two different types of Roles.

**Roles**
In GCP, Roles come in three flavors, Primitive, Pre-Defined Roles and Custom Roles.

###### Primitive Roles

> Prior to Cloud IAM's introduction in 2016, these were the only types of Roles. Primitive Roles consist of an Owner, Editor and Viewer and are most simply described as coarse grained.  Each of these Primitive Roles makes up concentric circles of permissions, where the Role, Viewer contains a subset of permissions that the Editor Role does and Editor contains a subset of permissions of the Owner Role. 
> The Editor Role is used ubiqutiously in GCP as the default Role assigned to the Default Compute and Default AppEngine Service Accounts.
> Google advises against using Primitive Roles and so do I.

###### Pre-Defined Roles

> These Roles are collections of permissions currated by Google. Updates to Pre-Defined Roles are pushed ~weekly and can be monitored in the Cloud IAM Permissions Change Log: 
> https://cloud.google.com/iam/docs/permissions-change-log
> Pre-Defined Roles tend to map to job functions and provide a good menu of granular options. 
>
> Not all Roles can be assigned at all Resource levels.  For example you can bind the Role `cloudsql.admin` at the Project Level, allowing an end user to admin all SQL instances in a Project, but you cannot assign that Role to an individual instance.  The lowest Resource level possible for the `cloudsql.admin`  Role is the Project.

###### Custom Roles

> GCP gives its customers the ability to craft their own Roles.  Custom Roles, with your own unique collection of permissions can be defined at the Organization and be bound at various points in the hierarchy. 
> Not all permissions can be used in Custom Roles. Just like Pre-Defined Roles, not all permissions can be assigned at all Resource levels.



**References**
_Side Note_: GCP has recently rebranded Primitive Roles and now calls them Basic Roles.
[ref]:
Primitive Role Definitions: 
https://cloud.google.com/iam/docs/understanding-roles#primitive_role_definitions

