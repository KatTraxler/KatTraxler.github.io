---
layout: post
title:  "GCP IAM Policy 101"
date:   2020-12-01
categories: GCP IAM 101
---


This GCP’s model for managing access to resources has three main parts: **Who, What Role and the Resource.**

To grant permissions in GCP, you combine the **WHO** and the **WHAT** to create a policy.  You apply this policy, or Bind the Role to a Resource to create scope. 

**Who**: Member in Policies. Users and Groups are managed in GSuite (Google Workspace), while Service Accounts and the Identity Namespace are wholly constructs of the Google Cloud Platform.  

**What Role**: A role is a collection of permissions. Permissions determine what actions you can take on a resource. When a role is assigned to a member, you grant all the permissions that the role contains. 

**Resource**: Who, has What Role is applied to a Resource to affect IAM Policy. A Member is assigned a Role which is then bound to a Resource to create an IAM policy.
In GCP, Each Node of the Organizational Hierarchy is a Resource which can have IAM Policy Bound.  This includes the Organizational Node, Folder and Project Nodes, in addition to all Resources contained within a Project.

