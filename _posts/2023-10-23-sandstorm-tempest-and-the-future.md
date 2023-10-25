---
layout: post
title: Sandstorm, Tempest, and the Future
author: Jacob Weisz
authorUrl: https://github.com/ocdtrekkie
---

Over the last couple of months, we have had a lot of people asking for an update on
the project. We’ve had to take a bit of time for self-care before putting together
our plans going forward, culminating in this update.

*Note: Astute observers may notice this blog is on a new domain, sandstorm.org,
along with subtle changes to this version of the website. We will discuss why and
how this will impact our plans next week.*

## Remembering Ian Denhardt

As people closely following the project may already be aware, about three months
ago one of our core project contributors, [Ian Denhardt][1], sadly and unexpectedly
passed away. It is impossible to overstate the significance of Ian's contributions
to this project, from maintaining and fixing bugs in Sandstorm itself, to packaging,
re-packaging, and building a number of apps and tools, to starting a next-generation
platform for Sandstorm (more on this below). Ian has ultimately been the *primary*
contributor to our ecosystem as a whole for the past three years.

Ian was an incredibly gifted developer whose projects spanned from calendaring apps
and backup tools to operating system kernels and programming languages. But far more
than that, Ian was an incredibly humble and kind mentor and friend. Countless times
he helped me with problems that came down to something close to “How do I navigate a
Linux machine” without a hint of irritation. Steve Pomeroy [shared a story][2] about
Ian recently that exemplified the sort of thought and *care* that he put into
everything that he did.

We intend to honor Ian’s memory and vision by carrying forward his work and ensuring
that we continue to make self-hosting viable for everyone.

## Introducing Tempest

Since late last year, Ian had been working heavily on an experimental alternative
software to run Sandstorm apps that we refer to as [Tempest][3]. Tempest is a modern
take on the Sandstorm platform, supporting existing apps but dropping various
long-disused compatibility shims and embracing modern technologies and expectations.
It is written in Go, which is significantly more accessible to volunteer development
than Meteor and C++. Another key feature is the use of WASM to allow the client side
of the platform to also be written in Go and run Cap’n Proto directly in the browser.

Whereas Sandstorm was built with organization or SaaS provider scale as a focus,
Tempest development was planned with individual or small community use at the
forefront. One of the major goals Ian had intended to strive for with Tempest was the
ability to communicate with decentralized communication networks like ActivityPub and
Matrix, which has proven challenging for Sandstorm thus far.

As of this post, Tempest can run many Sandstorm apps and can import grains directly
from a Sandstorm install. However, it is still far from being a production-usable
application, and a lot of important API features for apps like external network access
and web publishing are not implemented.

Multiple community members have stepped forward in the past two months to let us know
that they would like to continue development of Tempest including Troy Farrell and Dan
Krol. We plan to bring the [current work][4] on the project under the Sandstorm
organization to coordinate that effort. If you are interested in contributing, please
reach out on the project repository or the Sandstorm [mailing list][5].

## Current Challenges with Sandstorm 

While Tempest has been exciting as a potential leap forward, everyone using Sandstorm
today is still using “Sandstorm classic”. Sandstorm development has moved more slowly,
but it has not stood still. Several security improvements have been added in the past
couple of years, including improved sandboxing both on the server and client side and
a number of small but impactful UI improvements. Last month, Sandstorm’s 308th release
included Ian’s final contribution as well as additional localization work that Troy
contributed.

Unfortunately, as we hope to continue to support Sandstorm long-term, we’ve had an
increasingly challenging problem with the legacy MongoDB database underneath, as Meteor
has dropped support for the version we use. Ensuring a seamless migration on end-user
servers has proven to be a challenging problem. Dependency upgrades are not fun and
this has become a fairly unpleasant project to undertake for unpaid open source
contributors.

## Moving Forward

Over the past few weeks, we have spoken to a number of potential outside parties about
taking on this project and building a migration so that we can update the databases of
existing Sandstorm servers and allow us to continue moving forward. We believe this is
the best way to ensure that Sandstorm servers continue to be the safest way to
self-host. The cost estimates we've discussed for the current Sandstorm aren't
unreasonable, but they are significant.

We are looking to raise the funding for this project through our [OpenCollective][6].
If you are using Sandstorm, especially at an organization level, and can help ensure
that we are able to provide long-term support, please donate to the project. If you
believe that you or your organization may be willing to fund this effort in large part
or its entirety and would like additional details about the amount we would need to fund
this, please [contact me directly][7].

Sandstorm is approaching its tenth birthday, and there's still nothing else like it.
Capability-based security and a tireless focus on abstracting away complexity from the
user continue to make it the only platform which holds hope of letting *everyone*, not
just developers and IT professionals, self-host. Thank you for your continuing interest
and support.

[1]: https://github.com/zenhack
[2]: https://staticfree.info/ian/
[3]: https://web.archive.org/web/20230728221625/https://zenhack.net/2023/01/06/introducing-tempest.html
[4]: https://github.com/troyjfarrell/tempest
[5]: https://groups.google.com/g/sandstorm-dev
[6]: https://opencollective.com/sandstormcommunity
[7]: mailto:inbox@jacobweisz.com
