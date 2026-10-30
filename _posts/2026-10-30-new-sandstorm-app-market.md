---
layout: post
title: "The new and improved Sandstorm App Market"
author: Jacob Weisz
authorUrl: https://github.com/ocdtrekkie
---

We have launched a new version of the Sandstorm App Market at [apps.sandstorm.org](https://apps.sandstorm.org) and we recommend updating your server to use it. (If you've already moved to our new builds of Sandstorm, we will do this for you in an upcoming release.)

Massive appreciation goes to Michael Nutt for tackling a project in a couple weeks that I wasn't expecting to complete before 2027.

<img src="/news/images/new-market.jpg">

It... looks a lot like the old market. We're excited to talk about the differences though, because the major improvements happened under the hood. The biggest visible change is that you can now see an app's changelog right on the app page.

Underneath, however, the new app market is entirely new. The previous architecture involved two components, an [app index written in C++](https://github.com/sandstorm-io/sandstorm/tree/master/src/sandstorm/app-index) which is part of the Sandstorm codebase, and lives on a Sandstorm server, and the app market itself, a Meteor app which did not run inside Sandstorm but operated as a [replacement front end for a Sandstorm install](https://github.com/sandstorm-io/sandstorm-app-market#release-process-details). The App Market used to have a system for users to post reviews, but today it lives as a static site which is generated from the app index.

The new version of the app is a single unified Sandstorm app, written in Rust, which publishes both the market interface and the app index using static web publishing. It is a fully-compatible drop-in replacement. It is [open source](https://github.com/sandstorm-org/sandstorm-marketplace), and we expect to publish the app in the app market itself, so you can operate your own separate app index without having to build it yourself.

New app review interface
-----

The biggest changes lie on the administrative side of the application. The old app index printed all of the package metadata for apps in the review queue in a single long-running list. There was no sorting or navigation, and no way to look at existing published packages. All metadata from full changelogs to base64-encoded PGP keyrings simply print in a single pane that could get so long, navigation was only possible using Ctrl-F in the browser.

<img src="/news/images/old-app-review.jpg">

In comparison, the new app market provides a clean navigation interface allowing access to the entire package history of every package published to it. Actions are auditable and settings for notifications and pinned apps are available directly in one unified settings panel.

<img src="/news/images/new-app-review.jpg">

Handling deprecated apps
-----

If you noticed two MediaWiki listings in the screenshot above, you have discovered the other big thing the new market handles better! We can now deprecate apps that you should not install without making them inaccessible in the case you need to restore a grain that depends on them.

Occasionally a package author decides to fully break compatibility when repackaging an existing app. This often occurs when a package is rebuilt after a very long time, and the upstream app does not handle migrations across that timespan well. We do not want to ever publish an app update which loses your data, so if the upgrade is not compatible, it must be published as a separate app.

The new app market can now mark apps as deprecated, and recommend a replacement application.

<img src="/news/images/deprecated-app.jpg">

We used to advertise *two* versions of CodiMD, including one called SandMD, but now visiting both app pages will recommend you use [HedgeDoc](https://apps.sandstorm.org/app/4uda6a9yfxk4v3x2mpxjdn2md9y54tj104091480qjr8h758u4dh/) instead. The old apps remain installable in case you need the app to restore an old grain or complete a mass transfer.

Sandstorm apps are generally safe even if very outdated, so note that we will not deprecate apps because they are abandoned or old, deprecation is reserved for apps that have been fully replaced by a newer version.