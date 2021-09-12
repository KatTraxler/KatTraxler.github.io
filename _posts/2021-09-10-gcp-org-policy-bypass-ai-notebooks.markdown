---
layout: post
title:  "Bypassing GCP Org Policy with Custom Metadata"
date:   2021-09-10
categories: GCP Hacking
menus: header
---

**TLDR;**<br>
Google makes use of custom metadata to authorize access to AI Notebooks and their web UIs.  
Individuals granted access via custom metadata need not have any IAM permissions on the compute instance, on the service account running the Notebook or even be a member of the Organization.  Authorization via custom metadata, bypasses a specific [**Organization Policy Constraint**](https://cloud.google.com/resource-manager/docs/organization-policy/restricting-domains) which restricts cross-domain resource sharing.<br>
This vulnerability was awarded $1337.00 by the Google VRP Review Panel.


**What Are AI Notebooks?**<br>
AI Notebooks are Google Clouds' offering for managed Jupyter Notebooks.  The underlying infrastructure for AI Notebooks are Compute instances that live as resources in a customers Project.  Create an AI Notebook, a Compute instance will be spun up in the background to support it. 

The AI Notebooks API serves as an abstraction layer on top of the GCP Compute API.  The same ML optimized, deep learning VM can be created with the Compute APIs as it can with the Notebooks APIs, albeit with more friction and fuss.


**The Jupyter Notebooks Web Interface**<br>
The value proposition of several Google Products including AI Notebooks is their automagically generated web interfaces. These PaaS Services register instances with Googles [**Inverting Proxy**](https://github.com/google/inverting-proxy) to expose a GUI for the the underlying compute instance. 
Chances are, it you are interacting with content served from the domain `googleusercontent.com`, it is being served through the [**Inverting Proxy**](https://github.com/google/inverting-proxy).


**The Vulnerability**<br>
AI Notebooks, when deployed in Single-User Mode includes the following metadata tag: `proxy-user-mail`
The value of `proxy-user-mail` indicates to the [**Inverting Proxy**](https://github.com/google/inverting-proxy), who is authorized to access the Jupyter Notebook UI.

Upon the creation of a Jupyter Notebook, the Notebooks API checks if the user specified in the `proxy-user-mail` metadata property has the 'Service Account User' Role on the Service Account serving as the identity for the AI Notebook Instance. By performing this check, the AI Notebooks code ensures Notebooks in Single-User Mode can only be used to grant access to users which are assigned minimally assigned the [**`iam.serviceAccounts.actAs`**](https://cloud.google.com/iam/docs/service-accounts-actas) permission on the Compute Instance Service Account.

Unfortunately, this check was only performed at the time of Notebook creation, when it is initially registered with the [**Inverting Proxy**](https://github.com/google/inverting-proxy).  
However,  `proxy-user-mail` metadata property can be changed AFTER the initial creation of the AI Notebook.
By Stopping and Restarting the AI Notebook Instance, one could force the notebook instance to re-register with the Inverting Proxy.<br>
When re-registering, the Notebooks API **does not check** if the user specified in the `proxy-user-mail` metadata property has the [Service Account User Role](https://cloud.google.com/iam/docs/service-accounts#user-role).


![Backdoored VM](/assets/images/backdoored-VM.png)

**Bypassing Org Policy**<br>
As showcased in the demo, this vulnerability 'bypassed' the Org Policy Constraint: `iam.allowedPolicyMemberDomains`.  
This Org Policy allows administrators to constrain what members can have IAM Policy in a GCP Organization. A common way to leverage this Org Policy is a company would restrict who is eligible to have access to resouces to only members in their domain.<br>
i.e., only user with '@companyX.com' email addresses would be allowed to be granted acess to resourcess.<br>
The fine-print on this, and all Organization Policy constraints are, they only affect IAM Policy.  When access control is defined by some other mechanisms, all bets are off.

**The Demo**<br>
<p allign="center">
<iframe width="560" height="315" src="https://www.youtube.com/embed/RgSsQpCoj_w" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
</p>

**Identifying If You Were Affected**<br>
Their does not seem to be any ongoing risk with this issue as outlined in my follow up report detailing [rememdiation](https://kattraxler.github.io/gcp/hacking/2021/09/11/gcp-ai-notebooks-vulnerability-remediation-update.html).
The remaining interesting question for GCP Customers is?,<br> "Were you affected? At somepoint, was an attacker using this vulnerability as a backdoor"<br>

To answer that question I suggest querying your Cloud Audit Logs and reviewing any `setMetadata` events.  If any `setMetadata` events set the `proxy-user-mail` metadata property to a user outside your domain, this should be pursued as suspicious.

```
gcloud logging read "resource.type=gce_instance AND logName:projects/[PROJECT-ID]/logs/cloudaudit.googleapis.com%2Factivity"  --format json
```

**Reporting Timeline**<br>
	-- 05/15/21: Originally reported this issue via the [**Google Vulnerability Rewards Program**](https://www.google.com/about/appsecurity/reward-program/) from corporate email address.<br>
	-- June 2021: Was verbally informed the fix would roll out first week in July.<br>
	-- July 2021: Lost access to original report as it was submitted using corporate email address and I changed jobs :) <br>
	-- 07/27/2021: Re-reported the same issue via the [**Google Vulnerability Rewards Program**](https://www.google.com/about/appsecurity/reward-program/), this time with personal email account. Assigned ticket number: 194849420 <br>
	-- 07/28/2021: Identified as an Abuse Risk and triaged to the Google Trust & Safety team. Categorized as a Priority P1, Severity S4.<br>
	-- 7/29/2021: Google VRP communicated that the fix was rolled out that week.<br>
	-- 7/29/2021: I informed Google that their fix was insufficient and AI Notebooks and the Inverting Proxy was still vulnerable.<br>
	-- 08/05/2021: Provided further details on how this issue bypassed the Domain Restriction Constraint and confirmed that I knew that the Domain Restriction Constraint is NOT retroactive and that quirk of Organization Policy was not relevant.<br>
	-- 08/09/2021: From Google VRP: ":Thanks for your report and sharing all the extra details - 🎉 Nice catch! I've connected with the product team and they confirmed that the issue you described is different from the issue you reported to us in May."<br>
	-- 08/09/2021: I disagree.  The  AI Notebooks and the Inverting Proxy issue I reported in May is the same as I reported in July. But whatever.<br>
	-- 08/11/2021: Given an estimate of 3 weeks to fix.<br>
	-- 08/11/2021: Accepted the 3 week timeline and informed Google VRP I would disclose on September 10th.<br>
	-- 08/22/2021: Google VRP thanked me for my flexibility.<br>
	-- 08/19/2021: Informed of the Bug Bounty Award. "The VRP panel has decided to issue a reward of $1337.00 for your report. Congratulations"<br>
	-- 09/09/2021: Asked for an update as the issue remained open and reconfirmed my intensions to publish on September 10th.<br>
	-- 09/10/2021: Google VRP informed me the patch was being pushed to servers today and would be rolled out to all servers by EOD.<br>
	-- 09/10/2021: Informed Google VRP of the publishing of this report, despite the issue not being resolved.
	-- 09/11/2021: Informed Google that I agreed their patch plus the expiration of the 'DATALAB_TUNNEL_TOKEN' cookie resulted in a complete fix of the issue. 



