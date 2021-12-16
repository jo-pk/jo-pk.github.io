---
layout: post
title: rust ghost, signing off
description:
  announcing my departure from Rust team organizations and contributions
categories: self
---

To keep my usual long-windedness from obscuring the point of this post, i'll put the summary up
front: *I'm stepping down from my positions as lead of the Docs.rs Team, and member of the Rustdoc
and Documentation Teams, and ceasing my contributions for the immediate future.* The Docs.rs Team
will be co-led by Pietro Albini (`@pietroalbini`) and Joshua Nelson (`@jyn514`). The Rustdoc Team
continues to be led by Guillaume Gomez (`@GuillaumeGomez`), and the Docs Team by Steve Klabnik
(`@steveklabnik`).

-----

## some brief history

The story of my involvement in the Rust project is in some ways tied to the rise of Rustdoc's
distinction within the Rust project team structure. I've previously written about how i got involved
in the project in the post ["on the origin of rustaceans"][history]. Since that was written almost
two years ago, however, there's more story to tell!

[history]: https://quietmisdreavus.net/code/2018/05/11/on-the-origin-of-rustaceans/

Around the time of that post, the Rustdoc Team was working on getting Docs.rs to the point where we
could consider it to be "officially maintained by the project". The first big part of that effort
was to [refactor it to use upstream nightly Rust to build crates][upstream-pr]. This solved the
biggest problem facing Docs.rs at that time and allowed many crates to start building where they had
previously failed. By the end of 2018 we were also able to migrate the server running Docs.rs onto
infrastructure managed by the Rust Infrastructure Team, too, fully bringing it into the fold of
official team maintenance! (I talk more about this era in Docs.rs history in my talk ["The Death and
Rebirth of Docs.rs"][docsrs-talk].)

[upstream-pr]: https://github.com/rust-lang/docs.rs/pull/243
[docsrs-talk]: https://www.youtube.com/watch?v=jlqMNwVun7Q

Over time, my focus went more and more toward Docs.rs and less toward Rustdoc itself. This
culminated in [the decision to formally break off the Docs.rs Team][docsrs-team], which let me only
lead the one project and worry less about having to keep up with both. This was mostly a
formalization of the existing situation, though - Guillaume had been running Rustdoc for a while.

[docsrs-team]: https://blog.rust-lang.org/inside-rust/2019/12/09/announcing-the-docsrs-team.html

## undercurrents

Something i think about a lot in open-source in general is how much of it is contributed on a
volunteer basis. Other people have already pointed out how this tends to favor people with existing
surpluses of free time and energy, though, so i won't belabor that point. In my specific case, it
meant that my "energy to write/read code" had to be split between my job (which had nothing to do
with Rust) and my hobby (which over time got me more notoriety and exposure, and even a little money
via Patreon and GitHub Sponsors, but has yet to completely support my living).

Over time, as Rust allowed me to travel to wonderful places and meet great people, the tension
between these two opposite tasks became more and more visible to me. This travel often brought it to
the foreground; since my job had very little knowledge of the extent of my involvement in Rust, i
never asked to have my travel reimbursed by them. (Since my Rust activities were effectively totally
disconnected from my work activities, it would have made little sense for them to contribute.) This
often meant that my ability to attend conferences or contributor events depended on my ability to
use vacation time and to pay for the travel costs or have them reimbursed by the event.

The past year brought all this tension to a head when i decided to quit my job and attend a batch at
the Recurse Center. I was beginning to sense that i was less satisfied with my job and my life
situation at that point, and the offer of the fellowship stipend for being a trans person in tech
made the move easier to swallow. Unfortunately, it turned out that i wasn't just dissatisfied with
my job - i was burned out from living and working in two lives.

And i learned firsthand that burnout doesn't disappear overnight. The overarching sense of
obligation of having to return to the mountain of GitHub notifications i had let pile up; the
feeling of having to account for my growing absence as i focused more of my job (and my life) over
the summer and toward the end of my employment; the realization that i had lost the sense of joy in
writing code, in the one environment seemingly most suited to fostering it; these combined to make
me feel worse and worse about writing Rust.

## immediate future

So like i said in the intro: I'm saying goodbye. It's best for my health and for the sake of the
project that i stop being a contributor *in absentia*. I know i'm leaving the project in good hands;
the teams i've been a part of have grown into great stewards in their own right, and they have been
able to take things to greater heights than i could have imagined.

As for myself, i'm going to be backing off open-source contributions for a while. I don't think i'll
be gone forever - there will always be itches to scratch - but for now i need to take a break and
know that i don't have to come back to anything. I'm taking this moment to clear my head and refocus
myself. When i'm sure i can enjoy programming as much as i used to, i'll first focus on re-entering
the job market before i try to enter (or re-enter) any open-source project structure.

## closing thoughts

I don't mean for this post to apply broadly to the Rust open-source project structure as a whole, or
open-source software contributions generally. Any trends beyond my own story will have to be
collated by others - the only thing i want to chronicle here is my own story of burnout. There has
been plenty of discussion about the nature of the Rust project over time and its effect on the
people who support it, though i'm not sure how much of it has been fully committed to written word
yet.

I would like to point out a pattern i noticed while writing this, though. One thing that helped me
gain stature as quickly as i felt i did was the fact that documentation tooling was fairly lightly
maintained when i started with Rust. That's not to say that no one was working on it - for one,
other people wrote the first versions of Rustdoc and Docs.rs - but there was not much of a
contribution structure in place when i got started. This was a double-edged sword, though; it helped
me get a lot of "low-hanging fruit" changes in to get familiar with their respective codebases, but
it also meant that when i had a position of responsibility, it created a sense of obligation around
keeping things going. It felt hard to justify taking breaks and shifting my attention around.

In the end, this sense of obligation was my downfall. In the height of my burnout and depression, i
felt horrible that i was forsaking the community that has done so much for me. In a sense, i still
feel like i'm letting people down by stepping back. But i'm hoping that i can both take this
opportunity to take care of myself, and also to provide an example to show that project structures
that depend on a lot of volunteer work need to be resilient against volunteer churn. I am not the
first or most prominent Rust contributor to have stepped down, and i know i won't be the last.
However, as i said before, i know i'm leaving the project in good hands.

I want to thank everyone for all the wonderful friendships and experiences over the years. I've been
able to meet some delightful people and go to some amazing places as a part of the Rust community,
and i wouldn't trade those experiences for anything. The people are truly what makes Rust special,
and it means a lot to me that i got to be a part of that.
