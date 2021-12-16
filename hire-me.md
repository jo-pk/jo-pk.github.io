---
layout: page
title: software ghost trying to make it in the world
permalink: /hire-me/
---

Hi! If you've made it to this page, then either you're camping my GitHub repos, or you're interested
in my technical background. If you have an actual job posting you'll think i'll be interested in
applying to, i can hand you my actual resume with proper names/locations/timeframes/etc on it that
actually fits on one page, but this serves as a longer-form spot to host it so i can provide a
little more context. I'm not super comfortable making a complete CV/resume totally public
information. Consider this a somewhat-less-formal hybrid of those.

I should note: **At this time, i'm not looking for a new position.** I originally wrote this page to
help look for a job, but i'm leaving it up now so that existing links won't go dark. I feel like
it's still valuable to have a public version of my technical background, even if the "hire me" URL
isn't 100% accurate any more.

If you're interested and want to reach out, you can email me at (victoria AT quietmisdreavus DOT
net).

## about me

I'm Victoria! Depending on what internet circles you frequent, you may know me from my work in the
Rust community, where i led the Rustdoc Team and the Docs.rs Team. I've been writing code
professionally since 2012, and personally since before then. I love to get to the bottom of a
system, finding exactly how something ticks so i can fix a bug or find just the right way to add a
feature. I love documentation - reading it and writing it!  Over the course of my career, i've
worked in varied enough spaces that i'm not super attached to any one platform or toolkit - i
believe there's a lot of merit to any setup that can get a system running.  Instead i like to figure
out the capabilities of whatever i'm working with, so i can use it most effectively.

Before i go into my background in detail, here are a few lists of tools i've worked with:

* **Programming languages i've been paid to write / am comfortable writing:**
  * Visual Basic .NET
  * A company-internal language used for scripting
  * C/C++
  * Swift
  * Rust
  * SQL (both on PostgreSQL and MS SQL)
  * Bash script
  * Vim script
* **Programming languages i've dabbled in that could get comfortable quickly:**
  * C#
  * Haskell
* **Programming languages i wrote a while back and could get up to speed with enough exposure:**
  * Python
  * JavaScript
  * Lua
  * Ruby

<!-- break up these lists -->

* **Tools and systems i'm comfortable using:**
  * Vim
  * Git / GitHub
  * Linux (my distro of choice is [Arch Linux], but i've also admin'd an Ubuntu server)
  * Visual Studio
  * Xcode

[Arch Linux]: https://archlinux.org

## history

I grew up in "a moderately-sized city in Texas". (I'm fairly public about naming it if you've
followed my Twitter account long enough.) I've been around computers for practically my whole life.
There's an old story in my family that i learned to recognize letters just enough at age 2.5 so that
i could find the right floppy disk to play Lemmings.

I didn't have a strong coding background until around high school, when i discovered that the TI-83
Plus graphing calculator had this weird feature where you could write BASIC on the calculator and it
would do things for you. Around the same time i took some "web design" classes, which introduced
some JavaScript to mess with the simple web pages we made. (This was the mid 2000's, so the web is
very different now!)

## college

I studied Computer Science at "a state school in Texas" for my Bachelor's degree. The intro classes
taught C++ and object-oriented design. (And pointer arithmetic, because that's kind of necessary at
some points in C++.)

Some classes that i took that i think were neat / have helped me out:

* An assembly language class that taught 16-bit x86
* A database class that taught the theory of database/query design rather than any specific system
* A programming languages class that had sections on Racket and Prolog
* A class that taught how the basics of using a Unix system, including the common coreutils
* A parallel programming class that had sections on [MPI] and CUDA
* A graduate-level "Reinforcement Learning" class i took as an elective that served as an applied
  intro to machine learning

[MPI]: https://en.wikipedia.org/wiki/Message_Passing_Interface

I graduated *Magna Cum Laude* (which at this school meant "GPA at least 3.75 out of 4") and moved
back home for my first job.

## job 1: "the VB.NET job"

After graduating, i started working for "a law firm in Texas", helping them write line-of-business
software used internally. These were desktop applications written in VB.NET, connecting to an MS SQL
database. There were only a handful of software developers working there, and while the firm had
offices all around the state, there were still plenty of other people working out of the office i
was in. It was great being able to see and talk to the people who were using the software we were
writing - there were moments where some bugfix i had worked on made it out to everyone, and someone
would stop me in the hallway and thank me for making their job easier!

Over time i became known as the "special projects" person, because i was the one who was willing to
dive into docs and specs and the like. Some of the projects i helped out with were:

* Integrate the system with a then-new statewide service (a large SOAP service) for electronically
  filing court documents, which saved the firm hundreds of dollars a month after we became the first
  firm that integrated directly with the service instead of going through a third-party processor
* Polish and integrate a system which cleaned up information we received from a client, which
  frequently needed de-duplicating or typo fixes but had enough records to clean that doing so by
  hand was too cumbersome

## job 2: "we can't really hire for experience with this language that only exists inside the company"

In 2016, i left job #1 and moved to "a suburb of a large/well-known city in Texas" to start the next
one. The position was at "a large medical-tech company", working on a machine that ran medical tests
in labs all around the world. The specific area i was brought on for was the maintenance scripts
used by the instrument, which were written in a company-internal language that was parsed and
executed by the system itself.

Because of the nature of the work - a device that was used to make medical decisions for patients -
there was a vastly different process for approving code changes, due to the regulations involved. It
didn't take me long to gain an appreciation of this process - there was also a lot of training
material involved that helped to explain it in those terms. Another effect of the position within
the team itself was that i was frequently working with people outside of the software team to use
the scripts in some new context. It turns out, these maintenance scripts were useful for more than
just instrument upkeep, as i would quickly learn.

Much like in job #1, the projects i wound up working on would get more and more specialized, until i
was not just "the M&D person" (the scripts were known as "maintenance and diagnostic scripts", or
just "M&Ds") but also "the one who was willing to go really deep into the system to make things
work". Some standout projects i worked on here were:

* Integrate new hardware by working with the teams designing the hardware to ensure the instrument
  could properly use the new hardware in its maintenance scripts. In some cases, new scripts were
  written to verify that the add-on was working after it was installed.
* Worked with factory integration engineers to modify a test fixture used in the assembly process.
  The test fixture used the maintenance scripts but wasn't a full instrument, so the style of code
  was different.
* Used the maintenance scripts as a platform to allow users to configure printers for the
  instrument. Since the platform was running Windows underneath the custom UI, this involved using a
  helper utility to interact with the Windows API for configuring printers. There were existing
  tools available (written in VB6) to assist the maintenance scripts that acted as a kind of
  extension to the language and runtime, so these were extended to provide a wrapper that the
  maintenance script "UI" could call to configure the printers for the user.

## rust

Sort of parallel to job #2 was when i got into the Rust community. I'd been interested in Rust for a
while, but hadn't really jumped into learning the language before this. The new surroundings let me
focus some extra energy onto getting started. I started writing [a Twitter library][egg-mode] in
2016 as a learning project.

[egg-mode]: https://github.com/egg-mode-rs/egg-mode

Over time, i started to hang out in some of the Rust team IRC channels, including the one for the
Documentation Team. When i noticed weird things about the docs for egg-mode as i was writing it, i
started to ask around about where the documentation tool's source code was. I was helpfully pointed
to `rustdoc`, and before long i posted [my first PR to it]. One PR quickly became several, as the
change i made could be readily applied to more places. Over time, i got more and more comfortable
working with rustdoc, and since i was already in the habit of finding things to update, i started
posting more PRs to make bigger and bigger changes.

[my first PR to it]: https://github.com/rust-lang/rust/pull/36679

I was eventually tapped to lead the then-new Rustdoc Team. After a while, when we started supporting
Docs.rs in an official capacity, we spun off a separate Docs.rs Team, and i stepped down from
leading the Rustdoc Team to lead that instead. I've already written the rest of this story (in terms
of being a team member in the Rust project) in the posts ["on the origin of rustaceans"][origin] and
["rust ghost, signing off"][off].

[origin]: https://quietmisdreavus.net/code/2018/05/11/on-the-origin-of-rustaceans/
[off]: https://quietmisdreavus.net/self/2020/02/17/rust-ghost-signing-off/

Some of the major things i worked on while working on Rustdoc and Docs.rs were:

* Working with Manish Goregaokar (@ManishEarth), we wrote the initial implementation of ["intra-doc
  links"], which allowed users to reference items in documentation by its type name instead of a
  file on disk.
* Wrote the initial implementation of the [`#[doc(include)]` attribute][doc-include], allowing users
  to separate documentation into a separate file from the code that was being documented.
* Wrote the initial [README][] for rustdoc's source code, which eventually became the [rustdoc page
  in the rustc-dev-guide][dev-guide].
* Updated Docs.rs to [stop using a custom-built Rust toolchain][upstream-rustdoc],
  allowing it to build documentation for many libraries which had grown to depend on newer features
  than the custom toolchain would allow.
* Coordinated with the Infrastructure Team to migrate the Docs.rs server to a different provider (so
  that it could be managed by the infra team) and helped maintain it before and after the migration
  (This one doesn't have a PR link, sorry)

["intra-doc links"]: https://github.com/rust-lang/rust/pull/47046
[doc-include]: https://github.com/rust-lang/rust/pull/44781
[README]: https://github.com/rust-lang/rust/pull/48283
[dev-guide]: https://rustc-dev-guide.rust-lang.org/rustdoc.html
[upstream-rustdoc]: https://github.com/rust-lang/docs.rs/pull/243

I've also given [several talks] about the things i worked on as part of the Rust project,
specifically "the dark secrets lurking inside `cargo doc`", "Is This Magic!? Ferris Explores
Rustc!", and "The Death and Rebirth of Docs.rs".

[several talks]: https://quietmisdreavus.net/slides/

## the recurse center

In 2019, my partner convinced me to apply for the [Recurse Center] so i could focus on my Rust work.
I'd heard of RC before, but had hesitated to apply because it would mean moving to New York for
three months, and i didn't have that kind of money to drop everything and move across the country.
However, that year RC announced [fellowship grants] for women and trans and non-binary programmers
to come and work on open-source projects during a batch. That combined with my partner's support got
me to apply, and i got accepted! In the fall of 2019, i left job #2, packed up my apartment, and
flew to New York to start my batch.

[Recurse Center]: https://www.recurse.com/
[fellowship grants]: https://www.recurse.com/blog/149-10000-fellowships-for-women-trans-non-binary-programmers

My original goal at RC was to work on the Rust project full-time, and to help teach my batch-mates
about Rust while i was there. The plan was, by leaving my job behind and having a dedicated
sabbatical to work on open-source, i could get excited about it again. While that did still happen,
plans changed when i got to experience the social environment of RC in-person! I really enjoyed
getting to know everyone during my batch, and hearing about the kinds of projects they were working
on!

Some things that i did specifically at RC included:

* Wrote the talk ["The Death and Rebirth of Docs.rs][docsrs-talk] and presented it at Rust Belt
  Rust.
* Oversaw the creation of the [Docs.rs Team], to centralize work on the Rust documentation host.
* Wrote and presented several "non-technical talks", a kind of lightning talk that happened weekly
  at RC to present less-code-focused topics.

[docsrs-talk]: https://www.youtube.com/watch?v=jlqMNwVun7Q
[Docs.rs Team]: https://blog.rust-lang.org/inside-rust/2019/12/09/announcing-the-docsrs-team.html

## hiatus

After finishing my batch at RC in December of 2019, i moved to Denver to live with my partner and
started settling in. By now, after a few months away from work, a growing realization had dawned on
me: I was terribly burned out. The combination of doubling up job #2 with increasing open-source
work, as well as splitting my identity into a kind of double life as i was starting to transition
online but not in-person, created an immense drain on my mental resources. RC did help out a bit,
but after leaving New York i realized that i needed a break not only from my job, but also from the
feeling of being "obligated" to write code. I dedicated the first few months of 2020 to recovering
from burnout, and regaining my love for code and documentation.

Over time, i started tinkering on projects, both old and new. A brief summary of my personal
projects over this time include:

* Worked through the book [Finding Success (And Failure) In Haskell][haskell-book], at my partner's
  suggestion, as an accelerated introduction to Haskell. Afterward, i picked up the book [Building
  Git] and worked through the first third or so of it, writing it in Haskell instead of the book's
  native Ruby. [I posted my code online.][haskgit]
* Dove "Way Too Deep" into Vim configuration and script-writing, updating [my personal Vim
  configuration][vimfiles] and culminating in exporting a [custom status-line and
  tab-line][ghostline] and [a personal MRU-list script][mru] into separate plugins.
* Revisited [the Twitter library i wrote][egg-mode] and added [several major features and
  refactors][egg-prs].
* Created three new websites ([micro], [FOSS Pride], [links]), and in the process learned a lot
  about Jekyll and Ruby.
* Created [a Vim color scheme randomizer][mkcolor], learning a lot about color theory and web design
  along the way.

[haskell-book]: https://leanpub.com/finding-success-in-haskell
[Building Git]: https://shop.jcoglan.com/building-git/
[haskgit]: https://github.com/QuietMisdreavus/haskgit
[vimfiles]: https://github.com/QuietMisdreavus/vimfiles
[ghostline]: https://github.com/QuietMisdreavus/ghostline
[mru]: https://github.com/QuietMisdreavus/misdreavus-mru
[egg-prs]: https://github.com/egg-mode-rs/egg-mode/pulls?q=is%3Apr+author%3AQuietMisdreavus+created%3A2020-05-01..2020-08-01
[micro]: https://micro.quietmisdreavus.net
[FOSS Pride]: https://fosspride.org
[links]: https://links.quietmisdreavus.net
[mkcolor]: https://quietmisdreavus.github.io/mkcolor/

## job 3: "you might have heard of this one"

After a year-long hiatus, i wrote everything on this page up to this point and posted it on Twitter.
It caught the eye of a manager at "a very large consumer technology company", who oversaw a team
working on documentation tooling for Swift. I decided to sign up, and joined the team working on
what would become [Swift-DocC][docc]. Since i had gained a fair amount of compiler-development experience
working on rustdoc, i was tasked with improving its Swift compiler integration.

[docc]: https://github.com/apple/swift-docc/

It was a lot of fun working on something that took the ideas of a documentation tool and extended
them farther than rustdoc did. And since it reached much farther than the "data-visualization" of
pure reference documentation, the compiler integration turned out to be one component of a much
larger system, which gave its own set of concerns when coordinating with other teammates and
components.

When it came time to announce DocC to the world, i got to help record one of its introductory WWDC
sessions: [Meet DocC documentation in Xcode][meet-docc].

[meet-docc]: https://developer.apple.com/videos/play/wwdc2021/10166/

Eventually, Swift-DocC [was released to GitHub as open source][docc-oss], and i became one of the
main maintainers of some of its lower-level libraries:

[docc-oss]: https://www.swift.org/blog/swift-docc/

* [SymbolKit], a library to ingest and process "symbol graphs" containing API information.
* [Swift Markdown], a wrapper library for parsing Markdown with extensions used by Swift-DocC.
* A fork of [cmark-gfm], the GitHub-Flavored Markdown library, with additions to support Swift-DocC.

[SymbolKit]: https://github.com/apple/swift-docc-symbolkit
[Swift Markdown]: https://github.com/apple/swift-markdown
[cmark-gfm]: https://github.com/apple/swift-cmark/tree/gfm
