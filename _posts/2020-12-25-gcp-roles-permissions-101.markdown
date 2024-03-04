---
layout: post
title:  "GCP Roles and Permissions 101"
date:   2020-12-25
categories: GCP IAM 101
---


In GCP, access to resources is ultimately governed by Cloud IAM Permissions however, individual permissions are not directly assigned to principals. Instead, permissions are grouped together to create roles. These roles, rather than individual permissions, are then granted to principals in an IAM Policy.

Roles can range from containing just a single permission to encompassing thousands of permissions, such as the coarse-grained `Editor` and `Owner` [roles](https://cloud.google.com/iam/docs/understanding-roles#basic).


### **Permissions**

Permissions are always described in the three-part format: `[service].[resource].[action]`.

Examples:

- `resourcemanager.projects.setIamPolicy`: This permission, associated with the <u>Resource Manager</u> service, enables principals to modify IAM policies attached at the project-level.
- `iam.serviceAccounts.setIamPolicy`: This permission, associated with the <u>IAM service</u>, grants principals the ability to modify IAM policies related solely to service accounts.
- `spanner.databases.setIamPolicy`: This permission, associated to the <u>Spanner service</u>, allows principals to modify IAM policies related exclusively to Spanner databases.

While all these permissions pertain to updating IAM policies, they target different resources, impacting distinct scopes.


### **Roles**

Roles come in three flavors, Basic (née Primitive), Pre-Defined Roles and Custom Roles.

##### Basic Roles

Before Cloud IAM's introduction in 2013, only [Basic Roles](https://cloud.google.com/iam/docs/understanding-roles#basic) were available for policy assignments. The three Basic Roles (Owner, Editor, and Viewer) are coarse-grained, Google-managed roles each containing thousands of permissions.

These three Basic Roles form concentric circles of permissions, with Viewer containing a subset of Editor's permissions, and Editor containing a subset of Owner's permissions.

Editor is widely used in GCP as the default role** assigned to the Default Compute and Default App Engine Service Accounts. Despite this ubiquity, Google generally advises against using Basic Roles in most situations, as do I.

##### Pre-Defined Roles

[Pre-defined Roles](https://cloud.google.com/iam/docs/understanding-roles#predefined) are sets of permissions curated and bundled as roles by Google. Updates to these roles are pushed weekly and can be tracked in the [Cloud IAM Permissions Change Log](https://cloud.google.com/iam/docs/permissions-change-log). 

Pre-defined Roles are designed to align with specific job functions and offer more granularity compared to Basic Roles.
 

##### Custom Roles

Finally, Google allows end users to create their own Custom Roles for GCP. [Custom Roles](https://cloud.google.com/iam/docs/creating-custom-roles) enable you to define unique collections of permissions, which can be crafted at the organization level and then assigned at various points in the hierarchy, or created within a project for use exclusively within that resource.

It's important to note that not all permissions can be used in Custom Roles, and some permissions may not be assignable at all resource levels. For more details, refer to the [documentation](https://cloud.google.com/iam/docs/custom-roles-permissions-support).


### Role Assignment Resource levels

In GCP, not all roles can be assigned at every resource level. Typically, every role has a lower boundary which it can be assigned. 

For example, the `cloudsql.admin` role can be assigned at the Project Level, allowing a user to administer all SQL instances within that project. However, this role cannot be assigned to child resources of the project, such as individual SQL instances.

When it comes to Basic Roles like Owner, Editor, and Viewer, there are both upper and lower boundaries for policy attachments. These roles can only be assigned at the Project-Level; they cannot be in an IAM policy and attached to a parent resource like the organization node, nor to child resources within the project.

**As a best practice, roles should be assigned at the lowest resource level possible to reduce the scope of the permissions.**

