---
layout: post
title: From .IO to .ORG
author: Jacob Weisz
authorUrl: https://github.com/ocdtrekkie
---

Sandstorm has been on a fairly slow and gradual transition from a dead startup
to a community-operated open source project. Last week we [posted our first update][1]
about our plans for the future of Sandstorm here on sandstorm.org, and now we're going
to explain what the domain change means going forward.

## .IO doesn't fit who we are today

The .io TLD has been a popular choice for startups for a number of years. However, it
is a [problematic TLD choice][2]. One of our earliest key sponsors, draw.io,
[moved away from the domain][3] a few years ago.

While the .org TLD has had its own [share of problems][4], it's large enough and
established enough that the watchful eyes of the Internet have so far prevailed in
protecting it. And the sandstorm.org domain better represents us as a nonprofit
project mission-focused on bringing self-hosting to everyone.

## A change of liability

Software that keeps itself updated, or "evergreen" is convenient and popular today,
but it comes with a big risk: You trust the developer not just for the code they ship
today, but for years into the future. You also are placing trust in the developer not
to sell or give away that trust to someone else. Evergreen software like browser
extensions have been known to be bought out in order to inject adware on unsuspecting
users down the road.

Every Sandstorm server installed in the past ten years has entrusted Kenton Varda or
Sandstorm Development Group with that level of access. While the members of our
community have contributed to Sandstorm's ecosystem for a number of years, we can't
ethically take over maintenance of Sandstorm servers without permission. While we are
looking to extend and grow the ecosystem, server operators deserve the right to choose
whether to trust us.

So as we develop a community-based path forward, we are going to ask server operators
to opt-in to utilizing our releases and services, rather than taking them over.

## Why are we doing this now?

The final, practical reason to make this fork now, is ultimately because of the project
we are undertaking to upgrade Sandstorm's database. This is a project we have found
uncomfortable to automatically deploy, in case it leaves someone's server in a
non-working state.

By releasing that upgrade as a "fork", it will allow administrators to take a backup of
their server prior to both agreeing to entrust our team with future updates as well as
migrating to the new version of the database. We plan to replace the references in the
code to services currently hosted at sandstorm.io with sandstorm.org alternatives at
the same time. And that minimizes the disruptive work of this process.

## What about my Sandcats domain?

Sandcats is one of Sandstorm's few "centralized services" which still runs today, so
transferring it to our community would require a handoff of user data. We don't want to
do that without consent, so if we take over administration of the sandcats.io domain,
we'll provide significant advance notice and ensure only consenting users' data is
included.

The other issue with Sandcats is that its [current infrastructure][5] is fairly old, and
before we look at taking over operation of the service, we need to update the platform
and stand up a modern version of it.

This will be a very important process to get right, so we will talk about it often as we
get closer to making any change that impacts users.

## A thank you

Finally, I wanted to say thank you to our new backers who have contributed to our
[OpenCollective][6]. We still have a ways to go in order to fund our database upgrade,
but we are a lot closer today than we were a few weeks ago. Some people have reached out
to ask how they can contribute to Tempest or app development, and it has been a delight
to work with some great open source developers on app updates this month.

[1]: /news/2023-10-23-sandstorm-tempest-and-the-future
[2]: https://www.beep.blog/io/
[3]: https://www.drawio.com/blog/move-diagrams-net
[4]: https://arstechnica.com/tech-policy/2020/05/icann-blocks-controversial-sale-of-org-domain-to-a-private-equity-firm/
[5]: https://github.com/sandstorm-io/sandcats
[6]: https://opencollective.com/sandstormcommunity