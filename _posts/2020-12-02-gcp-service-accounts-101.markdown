---
layout: post
title:  "GCP Service Account 101"
date:   2020-12-01
categories: GCP IAM 101
---


Service Accounts are non-human identities used for Infrastructure and Resources.  Service Accounts do not have passwords.  You can optionally generate and export a Private Key for a Service Account. But you’ll only want to do this when you need to enable an external integrations, that is, authenticated API calls to your Resources. 

A Service Account is **Both** an Identity and a Resource.  
This might be the most difficult concept in GCP to initially grasp. Let me help break that down.

**Service Accounts as Resources**
Service Accounts are created and reside within a Project.  *You can think of a Service Account as having a ‘home’ Project.*  Just like a Compute Instance or a Storage Bucket ‘resides’ in a Project where its direct parent in the Resource Hierarchy is a Project.

Like a Resource, you can *setIAMPolicy* on a Service Account.  IAM on a Service Account include permissions like:
	* who can impersonate that Service Account
	* who can ActAs the Service Account
	* who can generate keys for the Service Account


**Service Accounts as Identities**


Like other types of Identities in GCP (i.e. Users and Groups), Service Accounts can have IAM Policy bindings at any node of the Resource Hierarchy.  Just because a Service Account lives in a Project, doesn’t mean it needs any permissions in that Project.  A Service Account can be assigned any Role, which can be applied at any node of the Resource Hierarchy, just like a User or Group.
When setting IAM Policy *ON* a Service Account, the WHO can be another Service Account! In fact, It might be a desirable pattern to have a Service Account, having permissions allowing it to impersonate another Service Account.
In this case, the Service Account doing the impersonating is the *WHO*, and is acting like an identity. While the Service Account being impersonated is the *Resource.*

