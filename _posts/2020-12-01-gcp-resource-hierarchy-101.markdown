---
layout: post
title:  "GCP Resource Hierarchy 101"
date:   2020-12-01
categories: GCP IAM 101
---


Google Cloud is organized into a hierarchy with each level or node of this structure being a resource.  At the top of the hierarchy is the **Organization** followed by any number of nested **Folders** which then house **Projects**. At the lowest level of the hierarchy are the widgets the compose a GCP Project. Resources including Pub/Sub topics, Cloud Compute instances, Cloud Buckets or Services Accounts are all child resources of projects.  

Whether the resource is a Project, a Folder or a Key Ring, each resource has exactly one parent.

There are two characteristics of a GCP resource hierarchy to consider when creating a resource hierarchy.

1. Resource Ownership for Lifecycle management:  Deleting a project deletes all resources contained within the project. If a Folder is deleted, all Folders or Projects that inherit from it are deleted.

2. Policy Inheritance: Resources function as attachment points for both IAM Policies and Organizational Policy Constraints. IAM Policies can be attached at any level within the hierarchy. The hierarchical structure of resources (with its child/parent relationships) determines how IAM Policy inheritance impacts access control. An IAM policy affects both the resource to which it's attached and all of its direct children


