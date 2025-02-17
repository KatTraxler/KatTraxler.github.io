---
layout: post
title:  "GCP IAM 201 - OAuth Scopes"
date:   2025-02-16
categories: GCP IAM 201
---

<br>
In the OAuth 2.0 specification, a *scope* defines the limits of an access token.  When applied to Google APIs, a scope specifies which APIs and resources the token can access.   
If you only take away one thing about OAuth 2.0 Scopes for Google APIs, it is: <ins>do not use them as a primary access control mechanism<ins>.


## Google APIs and their Scopes

More than 250 [Google APIs](https://developers.google.com/identity/protocols/oauth2/scopes) are available, and many allow applications to request delegated access to user data.  These APIs power well-known Google products like Drive, Gmail, Analytics, and the Google Cloud SDK.  

To find the OAuth 2.0 scopes available for a specific API, use the [Google API Discovery Service](https://developers.google.com/discovery/?csw=1). This service provides metadata for public Google APIs.  [Google's OAuth 2.0 Playground](https://developers.google.com/oauthplayground/getScopes) can also be used to explore available scopes, though it may not provide an exhaustive list.  


## Are OAuth Scopes Suitable for Access Control?

Now that you understand OAuth 2.0 scopes for Google APIs, you might be wondering if they can be used in place of, or in addition to, Cloud IAM for access control.  The short answer is no.  For Google Cloud APIs, it's generally best to use the broad `https://www.googleapis.com/auth/cloud-platform` scope. This scope covers all Google Cloud APIs and is often the default if no scope is specified 


## Scope Restrictions Apply Only to Access Tokens

It might seem obvious, but it's important to emphasize: OAuth 2.0 scopes applied to an access token only limit that *token's* access to Google APIs.  Other methods of accessing Google Cloud data, such as those using web cookies, are not affected by these scopes.  
