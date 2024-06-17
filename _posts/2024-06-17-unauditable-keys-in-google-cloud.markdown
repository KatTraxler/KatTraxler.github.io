---
layout: post
title:  "Working as Intended - The Unauditable, Unmanageable Keys in Google Cloud"
date: 2024-06-17
categories: CloudVuln
---

<br>
This [blog](https://www.vectra.ai/blog/working-as-intended-the-unauditable-unmanageable-keys-in-google-cloud) outlines three vulnerabilities with user-associated HMAC keys in Google Cloud.

1. Vulnerability #1 - Insufficient Logging

2. Vulnerability #2 - Unmanagable Long-Term Credentials

3. Vulnerability #3 - Unauditable Long-Term Credentials

## TLDR;

- HMAC keys serve a practical purpose. They can be used to create Sigv4 signed headers used to authenticate against the Cloud Storage XML API. for up to 7 days after initial creation.
- Google Cloud Audit Logs do not record HMAC key creation or deletion events when associated with user accounts.
No Google Cloud API is available to administrators, preventing them from auditing the existence of HMAC keys associated with user accounts.
- No Cloud IAM permissions are available to restrict the creation, deletion or usage of HMAC keys.
- This issue has been reported via Google’s Vulnerability Reward Program and they have closed the issue without providing a fix citing that the reported behavior is working as intended.

##### For the full article please refer to the original posting at [vectra.ai/blog](https://www.vectra.ai/blog/working-as-intended-the-unauditable-unmanageable-keys-in-google-cloud).