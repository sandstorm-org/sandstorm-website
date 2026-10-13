---
layout: post
title: "A New Era for Sandstorm"
author: Jacob Weisz
authorUrl: https://github.com/ocdtrekkie
---

tl;dr: We are announcing our first new release of Sandstorm, a fork of the previous startup's code.

How we got here
---

Sandstorm has been code-frozen for over three years. The original startup closed, a core contributor of our project passed, and technical debt from the earliest days of Sandstorm reared its ugly head.

A longstanding problem for Sandstorm has been its use of MongoDB 2.6 as its underlying database. As Sandstorm uses an evergreen release model, all updates must be reliable and recoverable. Sandstorm servers have been deployed in diverse environments, making it challenging to build database migrations covering every scenario. For years, MongoDB 2.6 worked fine and work addressing this problem was deferred. Eventually, the Mongo driver for Meteor dropped support for MongoDB 2.6 and Sandstorm's version of Meteor and its dependencies stagnated.

Additionally, Sandstorm has always been signed and released by Kenton Varda, the founder of Sandstorm. Every server operator who has installed Sandstorm has arguably placed their trust in Kenton. It does not make sense for Sandstorm servers to automatically install code Kenton has not reviewed and cannot personally vouch for.

As a project, we've decided to move forward as a fork of Sandstorm, where server operators make a deliberate decision to install our version of Sandstorm. This break in the update cadence also provides an opportunity: We can ensure administrators are actively involved in the process and can make a backup before the upgrade performs a database migration. With this plan in place, we looked for ways out of the project's dependency-induced stagnation. At the same time, we started to spin up our own replacement infrastructure.

Recently, long-time contributor Michael Nutt, author of Davros and of Sandstorm's WebDAV support, contributed a MongoDB migration, major dependency upgrades, and a variety of other tools and improvements. We're ready to move forward with both updates and new features.

What to Expect
---

The latest Sandstorm build, 310, should be considered a *release candidate*. The visible changes in this release are few, but the underlying platform has substantial changes. There are upgrades to MongoDB, Meteor, and many package dependencies. We've replaced most references to sandstorm.io with sandstorm.org including the update sources and documentation. We believe it's a solid, serviceable release, but many changes also create opportunities for problems.

We created new release signing keys and immediately released a second build to validate that the automatic update mechanism works as intended. However, until recently, all of this had been unused for many years. If you choose to update now, we recommend watching for updates in the event that manual action is required.

We're releasing a [migration script](https://install.sandstorm.org/migrate.sh) which should download, update, and migrate existing Sandstorm.io installations to our version. We strongly encourage you to have a full backup of your Sandstorm install before you proceed.

What's Coming
---

Sandstorm has dependencies still in need of an update. After ensuring that the build is solid, we intend to continue updating the dependencies to the latest versions. Sandstorm also still uses Kenton's app market and app index.  We intend to replace those in the future.

We hope to support using passkeys as a login mechanism in a future release. We're still interested in bringing Tempest to feature-parity with Sandstorm and beginning to direct our focus towards that in the future, but we'd like to ensure we can support the existing Sandstorm platform for the long-term as well.
Separate from the core Sandstorm server, we're working on updates, performance improvements, and bug fixes for many existing apps. We have new dev tools in progress to enable using Lima for cross-platorm app development, automatic app builds with GitHub Actions, and more.

The FAQ
---

**Who am I trusting when I move to your fork?**

The Sandstorm community project is currently led by Troy J. Farrell, Daniel Krol, and Jacob Weisz. All three of us have been involved in the Sandstorm community for many years, and have been continuously present in regular meetings for the past few years. You can chat with us on [Zulip](https://sandstorm.zulipchat.com).

In June, we met in person for a key signing party, to verify each others' identities and to create Sandstorm's new release key.

**Is Sandstorm developed with AI now?**

Generally we consider ourselves to be skeptical of AI tools, but yes, AI development tools were a big part of building our way out of Sandstorm's stagnation. For Sandstorm itself, we consider the use of AI-written code to be risky and review it very carefully. In the case of this upgrade, most of the code written with AI-assisted tools only occurs during the database migration. We need it to run once. The remaining code is largely the same code as before with newer dependencies.

We're aware of the ethical and environmental concerns with generative AI, as well as the security and performance risks. As a small project with limited resources, we are taking advantage of the opportunity to keep the project alive and moving forward. We think Sandstorm's usefulness for security and software independence outweigh the negative impacts of working with these tools in the short term.

**Is Sandstorm still relevant in 2026?**

Intriguingly, Sandstorm is in a unique position of being the ideal place to run "vibe-coded" applications: They're aggressively sandboxed, and our goal of safely operating malicious apps means there are fewer security concerns when using AI tools to write Sandstorm apps.

In the last several years, many self-hosting platforms have been announced, but few isolate apps as well as Sandstorm does. Most are UI layers on top of normal Docker containers. Making apps work on Sandstorm requires more effort, but we believe the effort is worth the reward. We also believe that we can continue to make the process easier.