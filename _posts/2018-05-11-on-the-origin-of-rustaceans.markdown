---
layout: post
title: on the origin of rustaceans
description: an origin story of how i started working with, and later on, Rust
categories: code
---

(full title: *"on the origin of rustaceans by means of viral community, or the preservation of
contributor growth in the struggle for maintenance"*)

The ["Increasing Rust's Reach"][reach] projects are kicking off! With it, the Community Team is
asking for people to describe how they contribute to Rust, to demonstrate the breadth of talent and
perspective in the community. So here's my personal Rust story!

[reach]: http://reach.rust-lang.org/

## step by step, day by day

I first heard of Rust a few months before 1.0, around the beginning of 2015. I saw someone talk
about it on Hacker News, and i proceeded to read the Guide (the "official tutorial" at the time)
from start to finish. At that point, things were getting nailed down for the final release, but
breaking changes were still a regular occurrence! I tried to download the compiler to write a small
project, but a combination of not having a good grasp of the problem i was working on (ID3 tag
parsing) and my own slow working pace led me to drop the project. I started passively following the
community, but didn't write anything else for a while.

Skip forward a year and a half (mid-2016), and some life changes led me to have some more time on my
hands. I decided to pick Rust back up, this time to start something i would maybe have an easier
time with: an IRC bot. The `irc` crate was fairly solid, and i picked up the `csv` crate to parse
the data i needed to support the bot. Everything worked out pretty well! I was fairly happy to have
finished a project, but i also knew i had a lot more to learn. I started lurking in the
`#rust-beginners` IRC chatroom and learned vicariously through the other people asking questions.
This was also when i stared writing `egg-mode`!

It didn't take very long from this point to start getting commits into the official Rust project.
While writing something for `egg-mode` i did some hunting and couldn't figure out whether the
`#[must_use]` attribute was available outside the standard library. Some asking around told me that
it was, but i couldn't find it in the Reference at the time. Thus started [my very first PR to
Rust][must use pr]: a single line in the documentation. It wasn't much, but it introduced me to two
crucial things: the process of getting a change merged into Rust, and the feeling of having a change
debated upon in your inbox.

[must use pr]: https://github.com/rust-lang/rust/pull/35754

Leaning on this experience, i felt emboldened the next time i wanted to touch something in the Rust
code. In `egg-mode`, i was writing a function that had a particularly long signature, and at the
time, rustdoc didn't have any facility to line-wrap any "code-like" samples it generated. This led
to the function being difficult to read in the docs, so i asked around to find out where that was
happening. After some time digging through the rustdoc code and poking around at trying some
changes, i opened [my second PR][fn break pr], which turned out to be even bikesheddier than the
first! I opened a few other "reformatting" pull requests to rustdoc over the next few months as i
saw more items to tweak.

[fn break pr]: https://github.com/rust-lang/rust/pull/36679

Around the same time, i had started to break out from just passively listening in `#rust-beginners`
to asking and even answering questions. Over time i started looking at some other channels to hang
out in and started lurking in several of the teams' channels, to "see behind the curtain" and check
out the process of planning stuff in the Rust project. Before long i found myself in `#rust-docs`,
the Documentation team's channel, and started watching as their weekly meetings happened, and they
talked about what they thought about this or that docs issue.

Over time, as i chimed in on more and more issues, Steve Klabnik (the lead of the docs team) started
soliciting my responses more often. I remember a very specific exchange we had, where i tried to
diminish my response by saying i wasn't on the team, and he said something along the lines of
"that's the point". It's important to get a sense of what people outside the "inner circle" think,
especially for something like documentation where these people are the target audience! So i kept
chiming in. It wasn't long before i was considered "unofficially" on the team, and soon enough i was
approached about joining the roster.

### addendum: rustdoc versus the teams page

This got me onto the [teams page][], but there's a bit more to the story because of rustdoc's
history of ownership. Within weeks of getting into the docs team, the dev-tools team was just
starting to get spun up. This gave rustdoc its own set of "owners" rather than the nebulous
ownership it had under the previous Tools team, as the new "dev-tools peers" would have dedicated
maintainers for rustdoc. My position in the docs team and my continued PRs for rustdoc let me get in
on the ground floor as this new team was getting started.

[teams page]: https://www.rust-lang.org/en-US/team.html

As the Dev-tools team progressed in its new organization, it was decided that they would start to
restructure the peers based on some of the ideas that had come out of the 2017 "impl period" and the
new Community team "sub-teams", which had created smaller focused groups around a single topic. With
this in hand, earlier this year [the dev-tools team created several "working groups" and sub-teams
of its own][tools in 2018]. Notably for this post, it meant that rustdoc got its own team.

[tools in 2018]: https://ncameron.org/blog/dev-tools-in-2018/

As the details of this were getting finalized, Nick Cameron approached me and asked whether i would
be willing to act as "team lead", coordinating things like issue triage and handling meetings. At
the time, Steve, Guillaume Gomez, and i were fairly equal "owners" of rustdoc, so it came down to
which one of us was willing to handle the "boring admin work", as we called it. I figured i already
helped out with this some on the docs team, so i accepted, and now i'm the lead of the Rustdoc Team!

## life on the inside

So that's a lot of origin story, but what is it *actually* like contributing to the Rust project?
What do i actually *do* with this?

Strictly speaking, membership on a team really amounts to the following:

* The expectation to keep doing whatever it was that got you onto the team in the first place. This
  line is intentionally vague because "contribution" doesn't always mean "writing code"! In my case,
  i wrote much less docs than i edited or revised, so i mainly benefitted from the next point:
* For teams with "ownership" over some repo, reviewer rights on that repo. For repos whose
  contributions are overseen by the `highfive` bot, you'll often get placed into its reviewer
  rotation as well. This lets you participate in the ownership of the project in question, by
  reviewing contributions as they come in.
* For teams that expect to handle Final Comment Periods on RFCs or other situations, a checkbox in
  `rfcbot`'s listing for that team.
* Participation in the team's meetings. A common thing for the teams to do is have a regular
  timeslot where they can triage new issues as they come in, discuss problems that they want the
  whole team to provide input on, and check in on major projects.  There's a general agreement that
  perfect attendance isn't necessary, especially for people who are here on a volunteer basis, but
  e.g. scheduling the timeslot is usually done with the team members' time zones in mind.

Now, i say all this, but in all honesty, my contributions have pared back a lot since starting, as
my life situation changed over time. I spend most of my Rust time nowadays reviewing contributions
to rustdoc, and the occasional documentation PR as it gets handed to me. I haven't stopped *reading*
the code, or dreaming of ways to implement high-profile features, and i continue to offer to mentor
people around the rustdoc codebase. But my specific "measurables" (so to speak - there's no official
concept of keeping a "score" for people!) have changed from "opened PRs" to "mentored
issues/reviewed PRs". I still enjoy this, though! I get to watch my little project grow over time as
we add more cool things to it. And since my roots in Rust also involved a lot of teaching back in
`#rust-beginners`, i really like when i get to show people through the code.

I like to describe the Rust organization as a kind of "organic bureaucracy", where groups can crop
up, grow, and break apart as they need. It's really fascinating to watch the project grow over time,
as more people find their way into it. It's fun to think that all these people coming together have
brought their own experiences into this wonderful community. I'm very happy to be a part of it.
