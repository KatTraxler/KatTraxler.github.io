---
layout: post
title:  "GCP Resource Hierarchy 101"
date:   2020-12-01
categories: GCP IAM 101
---


Resources in Google Cloud are organized into a hierarchy with each level or node of this structure being called a Resource.  At the top of the hierarchy is the **Organization** followed by any number of nested **Folders** which then house **Projects**. At the lowest level of the hierarchy are the widgets the compose a GCP Project, things like a Pub/Sub topic, Cloud Compute instance or Cloud Buckets.  

Whether the Resource you are referring to is a Project, a Folder or a Key Ring, each resource has exactly one parent.


There are three characteristics of a GCP resource hierarchy to consider:

Resource ownership for Lifecycle management:  If you delete a Project, you delete all Resources contained within the Project. If a Folder is deleted, all Folders or Projects that inherit from it are deleted.

Resources serve as attachment points for IAM Policy and Organizational Policies.  IAM Policy can be bound at any node in the hierarchy.  

The resource hierarchy didcates how IAM Policy inheritance will affect access control. IAM Policy both applies to the resouce which it was attached, and all direct children. In GCP there is no concept of overriding IAM Policy inheritance.


