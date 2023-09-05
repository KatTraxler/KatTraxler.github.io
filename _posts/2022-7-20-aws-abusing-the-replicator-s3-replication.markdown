---
layout: post
title:  "Abusing the Replicator: Silently Exfiltrating Data with the AWS S3 Replication Service"
date:   2022-07-20
categories: AWS Cloud Research
menus: header
---


**But how would you distinguish between legitimate backup activity and malicious data exfiltration on AWS S3?**<br>

In this [blog post](https://www.vectra.ai/blogpost/abusing-the-replicator-silently-exfiltrating-data-with-the-aws-s3-replication-service), I walkthrough a malicious use of the S3 Replication service and how selective data event logging on the S3 service will result in a gap in S3 exfiltration visibility since the `PutObject` event, indicating data movement event will not be written in the Source Account.




