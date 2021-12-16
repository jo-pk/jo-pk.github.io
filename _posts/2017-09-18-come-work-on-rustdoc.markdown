---
layout: post
title: come work on rustdoc!
description:
  an invitation for new contributors to work on rustdoc, as part of 2017's "impl period", including
  a walkthrough of writing Pull Requests on GitHub
categories: code
---

To participate in the Rust core team's ["impl period"][], i'm helping out by organizing for
contributions to [rustdoc][], the first-party documentation generator!

["impl period"]: https://blog.rust-lang.org/2017/09/18/impl-future-for-rust.html
[rustdoc]: /code/2017/06/21/a-whirlwind-tour-of-rustdoc/

Before i get too far, i want to point out some behind-the-scenes info. "rustdoc" actually refers to
two projects now. Steve Klabnik has been working on [a separate project][neo-rustdoc] that is
intended to replace the current rustdoc. That project is also soliciting contributions! However, i'm
not heading that up; check out the CONTRIBUTING.md file in that repo for more information.

[neo-rustdoc]: https://github.com/steveklabnik/rustdoc

What i'm concerned with is the rustdoc that currently ships with the rust distribution, the one
that's found in the compiler's repo. I've written [a general overview][rustdoc] of it before, which
includes some specific tips to build and run it yourself. If you're interested in how it works,
definitely check that out.

However, one thing i don't mention in there is that there are [a *lot* of outstanding bug reports
and feature requests for rustdoc][A-rustdoc]. Being almost as old as the compiler itself, and a tool
that ships with every Rust install, it's had a lot of opportunities for people to find its
shortcomings. Some of these only require printing information that rustdoc already has, while some
require more information to be loaded, or some change farther upstream in the compiler itself. Most
of the time, though, a change just needs someone to put in the legwork necessary to plug one part to
another.

[A-rustdoc]: https://github.com/rust-lang/rust/issues?q=is%3Aopen+is%3Aissue+label%3AA-rustdoc

That's where you come in! I've started [assembling a list of outstanding issues][backlog] based on
what kind of effort i think they'd take. It's nowhere near complete, but it's a good view of the
first 25% of the current issues. I'll try to add to this over time, as i get more opportunities to
perform triage. The listing there categorizes issues based on whether i felt they needed significant
structural work, or consensus-building, or just some time by someone who knows (or has been shown,
\*wink\*) the layout of the code.

[backlog]: https://github.com/nrc/dev-tools-team/issues/23

I would love to see this list of issues get cut down over the next few months, and i would be
*elated* to pull in people who are interested in making the docs the best they can be. rustdoc just
needs some love, would you like to help out?

-----

If that sounds like something you'd like to get involved with, i've joined with the rest of the impl
period working groups to have [our own Gitter channel][WG-docs-rustdoc] and a [planning
document][paper] to round up the work and chart out milestones along the way. The work is basically
"cut down the `A-rustdoc` issue backlog", and i'm willing to mentor and organize to help people who
have never worked with the rust compiler repo get involved. Over time, i'd like to pick out issues
with the least implementation effort, but for now, you can scan through the central list of all
`A-rustdoc` issues and pick one out that you'd like to see added. I can give it a look and see what
bits of rustdoc need to get messed with for it.

[WG-docs-rustdoc]: https://gitter.im/rust-impl-period/WG-docs-rustdoc
[paper]: https://paper.dropbox.com/doc/Rustdoc-issue-roundup-ZSIIXNDGEPozTM9axn0BO

In the interests of having a concise guide for getting set up, here's the basic steps to getting
into the codebase. It's... not the most simple thing, but i hope i can at least provide a quick
reference for people to get started.

1. If you haven't already, sign up for GitHub and fork [the Rust repo][rust-lang/rust].

   All of the rust project is handled over GitHub, so if you want your own name and information
   attached to the contribution, that's a "step zero" requirement. Forking the repository gives you
   a place you can push to, since only team members can push to the repo, and only [bors][] (our
   benevolent bot overlord) can push to the master branch that nightly compiler builds are made
   from. You can create your own "fork" of the project via the "Fork" button on the top right of the
   repository page.

   This also includes [cloning it][clone] to get the source code onto your local system to edit and
   make local builds. There are some changes that can be done from GitHub only, but those are mainly
   documentation changes that don't necessarily benefit from a full rebuild. For changes to rustdoc,
   most often we'll want to be able to run it on something to test it out, and that requires being
   able to build it. Which brings me to...

2. Set up your system for building the rust compiler.

   The prerequisites for building the compiler are listed [in the top-level readme][build-prereqs],
   but in essence, it comes down to "a C++ compiler", "Python", "CMake", and "curl". (Git is also
   listed, but i'm assuming that from the previous step. `>_>`)

   On Windows, in my experience, curl often comes with Git, depending on how you got it. [CMake][]
   and [Python][] can be installed from their respective websites. A note about Python: The full
   test suite uses some scripts that won't run on Python 3. However, the ones we care about (`x.py`,
   the bootstrap script, and `htmldocck.py`, the rustdoc test runner) can work with either version.
   I build and run rustc and rustdoc with Python 3 just fine, but just keep that in mind if you
   happen to run `python x.py test` without any restrictions.

   The "C++ compiler" part of the list is where it gets interesting on Windows. Because Rust has two
   ABIs on Windows, it can be built for either of them. There's a full explanation [in the
   readme][windows-abi], but i would like to add one note. By default, it will try to build with the
   GNU ABI, even if you have the MSVC one properly set up. To make sure it uses the proper setting,
   you can either manually pass a `--build` flag, or set up a `config.toml` file with your desired
   setting. (The latter is what i've done on Windows.)

3. Perform your first build with `python x.py build`.

   I'm considering this part of the setup because the first build always takes way longer than any
   subsequent builds, even when you pull in new changes from other contributors. This is because you
   need to build LLVM the first time, which (in my experience) takes about as long as everything
   else. When i've done this, it takes between 1-2 hours. If you have a fancy processor with lots of
   single-core performance (or with a few cores? I didn't see a lot of parallelization when i ran it
   on my 4-core laptop, but i wasn't paying that much attention `>_>`), this may be lower.

   (protip: the configuration file has a "low-priority" setting that lowers the process priority of
   the build. Setting this means that it won't steal up all your resources if you want to run
   something else at the same time on that computer.)

4. Start exploring!

   At this point, you're all set! Start jumping through the various subfolders of `src/` to get a
   sense of all the stuff in the compiler and standard library. The code specific to rustdoc is in
   `src/librustdoc/`. The stuff that involves rendering the HTML pages is mostly in
   `src/librustdoc/html/render.rs`, if you'd like a specific place to start. Whenever you start
   making a change, remember to make a new branch for it.

   (I'd *like* to point to something on GitHub's help pages for this, but they didn't have anything
   for it when i was looking, so here's the "short, short version": Branches are parallel versions
   of the codebase. People like to isolate related changes to their own branch so that they can be
   viewed in isolation, without clobbering and getting clobbered by other people's work. Make a new
   branch and switch to it automatically with `git checkout -b some-new-branch-name`. The GitHub
   Desktop application probably has a button for this, but i'm so used to using Git on the
   command-line that i haven't checked. `>_>` Atlassian has [a pretty nice tutorial for
   branches][branch-tut], if you want a big overview of the concept and a handful of command
   examples.)

   I've written more rustdoc-specific stuff back in [a whirlwind tour of rustdoc][rustdoc], but the
   commands to remember are `python x.py build --stage 1 src/libstd src/tools/rustdoc` to build just
   rustdoc and libstd for use on a separate crate, and `python x.py doc --stage 1 src/libstd` to
   generate just the standard library documentation, if your change can be seen there.

5. If your code is ready to go, set up a [Pull Request][]!

   The way we track individual changes to Rust is via Pull Requests. They provide a quick interface
   for checking out a change and discussing it. To set one up, make sure to [push your code to
   GitHub][push] first. Once that's done, you can head to either the page for the main Rust repo or
   for your own fork; either way, a "Compare and pull request" button should appear with your
   freshly-pushed branch. (If not, there's [an overview of creating a PR from scratch
   here][make-a-pr].)

   The link earlier to [Rust's contributing guidelines][Pull Request] shows some tips for working
   with the bots we use to manage PRs. Generally speaking, though, they're still useful if you
   forget about them until you see highfive doing something with the metadata. (The bots are mainly
   for us to deal with, and to help guide people to make sure everything gets addressed. We don't
   want a PR to sit un-viewed for an eternity!)

6. If you come back later on and want to use fresh code, remember to [update your fork][update]!

   Your fork of a repository doesn't automatically keep up with all the happenings on the main one!
   If you want to make sure you work with the latest and greatest code (or if you need to fix a
   merge conflict) then you'll need to make sure your `master` branch gets updated. Whenever you
   update your fork, if you see a bunch of changes in `git status` talking about "new commits", you
   can run `git submodule update --init` to sync all the submodules back up. The build system does
   this automatically, but i like to do it by hand so i can have a clean `git status` whenever i
   change branches or update my `master`.

[rust-lang/rust]: https://github.com/rust-lang/rust
[bors]: https://buildbot2.rust-lang.org/homu/
[clone]: https://help.github.com/articles/cloning-a-repository/
[build-prereqs]: https://github.com/rust-lang/rust#building-from-source
[CMake]: https://cmake.org/download/
[Python]: https://www.python.org/downloads/
[windows-abi]: https://github.com/rust-lang/rust#building-on-windows
[branch-tut]: https://www.atlassian.com/git/tutorials/using-branches
[Pull Request]: https://github.com/rust-lang/rust/blob/master/CONTRIBUTING.md#pull-requests
[push]: https://help.github.com/articles/pushing-to-a-remote/
[make-a-pr]: https://help.github.com/articles/creating-a-pull-request/
[update]: https://help.github.com/articles/syncing-a-fork/
