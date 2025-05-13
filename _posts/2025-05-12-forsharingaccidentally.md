---
layout: post
title: "forSharingAccidentally: How Permissive Defaults and Unclear Documentation Cause Mistakes"
author: Jacob Weisz
authorUrl: https://github.com/ocdtrekkie
---

**tl;dr: We found a vulnerability in a few apps allowing unauthorized access to grains via API tokens. The last section has recommended actions.**

We think of Sandstorm first and foremost as a security product. This means, in practice, we think of the security implications first, and the usability concerns second.

From time to time, when poking around our own project, we question the assumptions we make day-to-day. In this case, we decided to test a claim in the documentation, and it revealed an information-disclosure issue in multiple apps.

The problem
-----------

The issue in question is regarding Sandstorm's use of ["offer templates"](https://docs.sandstorm.io/en/latest/developing/http-apis/), which are a way for apps to distribute API keys to users, often to connect client apps. Offer templates are a text template defined by the app which specifies how an API key will be shown to a user. The template, with the API key inserted, is then displayed by Sandstorm in an iframe. This approach is intended to prevent Sandstorm apps from reading their own API keys.

Offer templates are created by apps with some parameters. Sandstorm requires `rpcId` which identifies the template in case your app has multiple, and `template` which contains the actual template text. Several optional parameters are also available to describe the API key, style the template, and most critically set its authorization. `roleAssignment` restricts the key's permissions by tying it to an app-defined "role". `forSharing` determines whether the key meant to be given to someone *other than the user*, but what does this imply exactly?

In the documentation, it clearly states that if you use the `forSharing` option, the token in the offer template is detached from the user and can be reconfigured into a grain sharing link, in the format of `https://SERVER/shared/TOKEN`. This presents the natural assumption (and fatal mistake) that if you do not use `forSharing`, you cannot use the token in the form of a grain sharing link.

However, that assumption is incorrect. Regardless of the existence of the `forSharing` option, *every API token works as a share token*. Therefore, in the minimum offer template configuration, where neither `roleAssignment` nor `forSharing` is set, an API token can be used to access the full UI of the Sandstorm grain, at the same permissions as the user who created the token.

Another assumption which one might casually make, if API keys are just API keys, is that you can restrict the bounds of an API key using the app's `apiPath`. This setting limits the root URL that an API call can make *when used as an API key*. But because the API tokens can be used to access the grain UI, that assumption is also perilous. API tokens are, fundamentally, granted capabilities, where knowing the resource also conveys the ability to use the resource.

Affected apps
-------------

In the case of most Sandstorm apps, this is actually not a significant issue: Users expect their client apps to have full access to their grains. For instance, a few apps use offer templates for Git clients and grant full access. This is okay because for these apps, each grain contains a single repository and full write access is expected. However, there's a small category of Sandstorm apps where the token from the offer template is published publicly: Analytics tools.

We identified two analytics tools for Sandstorm which do not appropriately restrict access to the tracking token: [Hummingbird](https://apps.sandstorm.io/app/4mfserfc04wtcevvgn0jw27hvwfntmt8j468y3ma55kj8d5tj9kh) and [Sandstorm Error Collector](https://apps.sandstorm.io/app/wa8sgzkj7hsvwnf2qfsqx47a0gxgn5vcjysu6szn4rhxfcydt14h). Notably, the Sandstorm Error Collector is used in the Sandstorm install script to allow people who fail to install Sandstorm to send the error code to us. It is possible to take the token from the installer script and use it to open the grain and see error reports. However, no user-identifying data is collected by this app.

Hummingbird is a website analytics tool. Anybody visiting a website that uses Hummingbird can view its source and obtain the API token. Hummingbird does define a "trackee" role, but for whatever reason the offer template did not end up using it for `roleAssignment`. As a result, it would be possible to use the API token to open the grain and watch analytics from *currently active* visitors to the site (*not* historical data). This includes location data which is obtained by the visitors' IP addresses. While IP addresses are not directly exposed, the exposed location data could in theory be used to identify a "record" in the app's GeoIP database which is associated with a set of possible IP addresses. The number of IP addresses in a record varies. More than 99% of IP addresses are in a record with 100 or more IP addresses. About 15,000 IP addresses are in a record with 10 or less. 911 IP addresses are in their own record, and could thus be identified uniquely by the visitor's location. This is unfortunately non-zero exposure, but ultimately quite limited. (Daniel Krol did most of the investigation on the impact for this application.)

Additionally, the RMM app I am developing, [XRF Sync](https://apps.sandstorm.io/app/txfm99kff7q68u04dj4cvcshg633s021d8yf7w2a17k33edf88dh), also should have used tokens with less permissive roles. However, in general different customers or tenants should be confined to their own grains, so the impact here is similarly limited.

Remediation and recommendations
-------------------------------

We've patched Sandstorm Error Collector and XRF Sync. We've also developed a fix for Hummingbird, but the author has not yet been reached to assist with publishing.

**Suggested actions for users**: If you use Sandstorm Error Collector or XRF Sync and are concerned about access to the grain, you should update your app(s), revoke your API tokens in the "webkey" menu, and create new tokens for your use. If you use Hummingbird, we can only recommend that you revoke all API tokens and stop using the app for now.

We will update Sandstorm's documentation to highlight that an API token can always be used to access the grain UI in the form of a sharing URL.

Critically for the future, we do not think security-critical properties should be optional. We're going to move to start including `roleAssignment` in all offer templates, even when all permissions are expected, with a goal of making it a required configuration in Tempest. In the interim, app review will no longer accept submissions of packages which use offer templates without defining `roleAssignment` explicitly.