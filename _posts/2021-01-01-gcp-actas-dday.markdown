---
layout: post
title:  "GCP .actAs d-day > How not to remediate"
date:   2021-01-01
categories: GCP IAM 101

---

On January 27, 2021 a major, potentially breaking change is coming to GCP.  If you're using the default service account as the backing idenity for several of GCP's data science PaaS services, the end user will be required to have the `.actAs` permission on the default service account.

The PaaS services in question are:

* Cloud Composer
* Dataflow
* Dataproc
* Dataprep
* Data Fusion

**Big deal right ?** 

Well, if you're running these services with the default service account as their underlying identities **and** your end users **do not** have the `.actAs` permission on that default service account, January 28 will be a no-good-rotten-day for operations.

Google PSO published a tool, the [permission-dependency-finder](https://github.com/GoogleCloudPlatform/professional-services/tree/master/tools/permission-discrepancy-finder) which can help identify these conditions in your *projects*. Google will have also notified the contact for your *organization* if this insecure condition exists in any of your *projects*.

**What is the `.actAs` permission ?** 

When an end user has access to some infrastructure, most notably, a *compute instance*,  it follows that they have unauthenticated access to its metadata API and the service account its running as. Naturally, giving an end user access to a service account, its access token and all of its roles and permissions could be a privilege escalation risk.  Therefore, when we need to enable an end user to operate a resource,  we want to be <u>explict.</u>  That's where the `.actAs` permission comes in.

The `.actAs` permission can be thought of as a gatekeeper permission.  It's a check against IAM which can authorize or deny access to a service account. It **should** be required whenever the possibility exists for an end user to gain access to the metadata API. Accessing the metadata API can be accomplished directly say by SSH-ing into a *compute instance* or indirectly, by controling code which the instance executes.

When you assign a service account to a *compute instance*, Google requires the end user to have the `.actAs` permission on that service account.  When an end user creates a new Cloud Function, they need the `.actAs` permission on the service account running that Cloud Function.  

The `.actAs` permission is included in the `Service Account User` role and is a mechanism for us, the Cloud Adminstrators, a chance to be crystal clear about who can and who cannot access the roles and permissions assigned to a service account.

On the other side of the orderly, perdicable requirement for the `.actAs` permission, there is the class of GCP PaaS products which, inexplicably, did not require the end user to have the `.actAs` permission on their service accounts ?

**History**

It's not necessary to dive overly deep into the back story.  The history of the missing `.actAs` permission check was effectively narrated by Dylan Ayrey in his blog post [Fixing a Google Vulnerability](https://security.love/blog/gcp/2020/11/22/lateral-movement-and-privesc-in-GCP.html).  Dylan, was the first to report this issue to Google in 2019 as a security vulnerability and worked with Google on helping them understand the security considerations they were not......considering.

While 2019 was the year Dylan began working with Google, seeminly, holding their hand down a path through the 'coming-to-jesus' forest; 2020 was the year for this issue to be aired in public. First with his own public talk at [BSides San Francisco](https://www.youtube.com/watch?v=z5hPU3g2aZ8) where he and his collaborator, Allison Dononvan presented on "Security Considerations for the metadata API in GCP".  The associated [blog post](https://github.com/dxa4481/AttackingAndDefendingTheGCPMetadataAPI) is another great archive for understanding how attacks on the metadata API can fuel a GCP *project* takeover.

In 2020 I gave three talks that highligted related security issues on the platform, [Privilege Escalation in GCP - A Transitive Path](https://www.sans.org/webcasts/privilege-escalation-gcp-transitive-path-114705) which is an academic look on using customer-managed service accounts as a lateral movovement path in a GCP *project*, [GCP Primitive Roles, An indictment](https://www.youtube.com/watch?v=YGT_AmCA-eA) at [fwd:CloudSec](https://fwdcloudsec.org/index.html#intro), I dug into the most dangerous of permissions included in GCP *Primitive Roles*, with the `.actAs` permission being a primary concern. And finally, [A Bug Hunters Guide To GCP](https://www.youtube.com/watch?v=I7QWQmS2Mpg) at the SANS Cloud & DevOps Security Summit.

Last but not least, Spence Geizten, formerly of Rhino Security and of AWS Pacu fame, has a well researched [piece](https://rhinosecuritylabs.com/gcp/privilege-escalation-google-cloud-platform-part-1/) outlining specifc privilege escalation paths with the `.actAs` permission and services not requiring the `.actAs` permission.

Finally, in July 2020, Google sent a notification to impacted customers informing them that the certain data science related PaaS products have 'legacy behavior' which allowed the end user to use them without the `.actAs` permission and that behavior was to change on January 27 2021.

**January 27, 2021**

Despite my best Googling, I cannot find any public notice from Google committing to the January 27, 2021 date although it has been widely reported by customers. You might ask your GCP Org Admin if they received an emailed notice from Google near the end of July informing them of the upcoming changes.

Since the notice to customers was issued, there is only one piece of public [GCP documentation](https://cloud.google.com/iam/docs/service-accounts-actas) regarding this issue. 

Layered in doublespeak, the page can be head scratching to parse.  Here are to two headlines:

* Certain PaaS services did not require the `.actAs` permission on the service account to operate (Composer, AppEngine, Dataflow, Dataproc, Data Fusion).  Going forward, when a product-level role is assigned to an end user, they will additionally need the `.actAs` permission to really use the intended functionality.  That is, to attach service accounts to the inderlying `compute instances`, a required procedure when spinning up a new service.

  

* End users who are **currently** assigned roles allowing for the deployment of AppEngine applications, Composer environments or Dataflow jobs with the default service account <u>still are not</u> required to have the `.actAs` permission on the default service account.

This will change on January 27, 2021.

 At last, the `.actAs` permission will be required, even when using the default *compute* or default *AppEngine* service accounts as the backing identity.

**How NOT to remediate**

Google provides a predefined role called `iam.serviceAccountUser` which contains the `.actAs` permission. If you were to bind this role at the *project* level, you would be granting the `.actAs` permission for all service accounts, current and future.

Do not bind the `.actAs` permission at the *project* level.  If this is how an *organization* addresses the `.actAs` breaking change,  they might end up with a worse security posture. 

**How to remediate**

Google PSO published a nifty tool called the [**permission-discrepancy-finder**](https://github.com/GoogleCloudPlatform/professional-services/tree/master/tools/permission-discrepancy-finder) . You can use this tool to identiify conditions in your *project* or *organization* where an end user is using one of these PaaS Servcies without the `.actAs` permission on the backing service account.

Once you've identified **who** needs the `.actAs` permission, you can assign them the `iam.serviceAccountUser` role *on* the service account. Binding the role at the resource-level will confine the `.actAs` permission to that specific service account.

Be mindful that when the `.actAs` permission is assigned, you are effectively agreeing to the idea that one identity can assume the roles and permissions of another.  To reduce the blast radius of potential service account abuse,  assign unique customer-managed service accounts to the *compute instance* backing your PaaS Services.  

Trim down the roles and permissions assigned to your service accounts.  While always good advice, it becomes even more important when another identity will be allowed to impersonate or use that service account.  Google did a good job of documenting the minimum roles required for running Dataflow, Dataproc and Data Fusion.  Any additional roles needed would be unique to your workflows.

**How did this happen** ?

This next part is highly editorizalied and <u>should be considered a personal opinion piece.</u>

You may be asking yourself as I did, how could a technical powerhouse like Google design a path for privilege escalation and coditfy it as a pattern? I have no doubt that this debacle wasn't a bug or an egregious oversight but a decision.

In Dylan Ayrey's [post](https://security.love/blog/gcp/2020/11/22/lateral-movement-and-privesc-in-GCP.html), he asks the question 'how did we get here' and describes a scenario where the Google juggernaught may have yeilded a disappointing result but ultimately operated correctly.  That is, the Product Managers for Cloud IAM and the data science PaaS products have enaged over the security issues but ultimately each product has chosen a path they feel is best suited for them. The result? Security issues formed not as result of tech debt or failed processes but as a *byproduct* of the process.

I've found myself asking the question, how insular must an organization be to make such a miscalcuation of risk?

Google's notification to customers left me with even more questions. The tone of their announcement makes it seem as if the **core security considerations** of their products are still lost on them.

> To make onboarding easier, the following services have used the Compute Engine default service account as the deafult and have relied on product-level IAM permissions without requiring the `iam.serviceAccount.actAs` permission:
>
> * Cloud Composer
> * Dataflow
> * Dataproc
> * Dataprep
> * Data Fusion



**Where was Google's security department?**

Given what we know, that this cavernous security flaw existed in GCP for years, I can only conclude that Security @ Google lacks the all important **Oh Shit** button every security department needs.  

Whether this is by design or because their is a lack of trust, I have no inclanations, but it doesn't leave me feeling optimistic for the idea that security is a core design principal in GCP.  Or, that when the rubber meets the road, Security @ Google can force different design decisions on behalf of securing their customers workloads.