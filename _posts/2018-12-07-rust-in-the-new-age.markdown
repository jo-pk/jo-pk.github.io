---
layout: post
title: rust in the new age
description: a wishlist of what i would like to accomplish with Rust in 2019
categories: code
---

With the end of a year comes a new call for reflection, and a new chance to orient ourselves for the
coming year. As with previous years, the Rust Teams have called for [a new set of roadmap
posts][roadmap-call] to help guide the teams' priorities in 2019. I've been doing a little prepwork
myself, and have a few ideas for where to take my own work. Consider this less of a request for the
organization as a whole, and more of a preview for what i'm considering for focusing the Rustdoc
Team on.

[roadmap-call]: https://blog.rust-lang.org/2018/12/06/call-for-rust-2019-roadmap-blogposts.html

## preamble

Before i get into dreaming about the future, i'd like to state a little bit about where i'm coming
from. Pretty much all my Rust writing nowadays is on either `rustdoc` or on docs.rs. I've largely
stepped back from any other Rust code i've done, just out of the interest of focusing my time and
energy onto something where i can more easily get rolling. However, both of these projects are so
well-established that it's really easy to get absorbed in their own little world and ignore what's
going on elsewhere! It's fun to take a step back during events like this and see how other people
are interacting with Rust.

That's all to say that anything i'm writing here is from the perspective of "documentation tools
managed by the Rustdoc Team" (namely, `rustdoc` and docs.rs). Organizationally speaking, i want to
start focusing our efforts to *make Rustdoc the best documentation system for any language*, and
possibly *the best means to navigate Rust source* short of your favorite text editor and flawless
[RLS][] integration. I feel like we've already got a good position, but there's always room to
improve, and there are always more places to draw inspiration from.

[RLS]: https://github.com/rust-lang/rls

That said, let's dive in!

## finish what we started

Of all the features that rustdoc has that still require Nightly, there are a few i would call
"major" features. I would love to be able to push these to stabilization this year:

* **Intra-doc links.** For background, this is the feature that allows you to specify a link using
  the name of a type, rather than trying to link to its output HTML page. I consider this the
  biggest unstable feature in rustdoc right now, but there's at least one *major* blocker preventing
  this from reaching its full potential: The ability to re-export an item with intra-doc links on
  it, while preserving the targets of those links. This requires coordination with the Compiler
  Team, as it's a limitation in the current implementation of the name resolver. With that major
  feature and some more polish, this feature can shine.
* **Conditional compilation support with `#[doc(cfg)]`.** This has been sitting idle for a while
  now, and needs some more attention. There are some potential issues remaining with
  platform-specific types that escape the `everybody-loops` pass, but i feel like with some more
  attention, this can be ready to go.
* **External docs via `#[doc(include)]`.** Like `#[doc(cfg)]`, this has been sitting idle, and needs
  some remaining attention regarding reporting line numbers in doctests, but i feel like this is
  also close to prime-time.

In effect, the theme here is "elbow grease". These features just need some dedicated time to
investigate and iron out any last wrinkles before we send them out the door.

## a map in a labyrinth

One long-neglected long-standing feature in rustdoc is the source-code view. It's a powerful feature
to be able to click a link from nearly anything in the docs and see just how it was implemented.
However, [up until recently][source-sidebar-pr], the source-code pages were a bit of a dead-end.
Sure, you could look at the implementation of a thing, but you couldn't look around at any other
file in the source! This was especially painful in a place like the standard library, where the
*true* implementation of something could be squirreled away in a different module, due to how the
platform-specific implementations are set up.

[source-sidebar-pr]: https://github.com/rust-lang/rust/pull/55707

Let's take a step back for a minute. Before i came to Rust, i had a strong background in the .NET
world (specifically Visual Basic .NET, but that's less relevant here). One really cool thing that
.NET has is the [Reference Source browser][ref-src], which is an interactive viewer of a subset of
the .NET Framework. This is a really powerful resource, both for investigating how bits of the .NET
Framework operate, but also for *how* it lets you view the source: Types can be clicked to navigate
to their definition, functions can be queried to see where they're called, items can be searched for
by name. It's as if you have a powerful IDE just to browse this code!

[ref-src]: https://referencesource.microsoft.com/

Can we take this and apply it to the Rust world? It turns out, there's already a project that tries
to do this! Enter [cargo-src][]. This is a project to take the same information the RLS uses to
power its IDE integration, and use it to render a powerful source-code browser instead. As a proof
of concept, it's beautiful. It lets you do anything you can do in an IDE - jump to definition, find
all references, find type or trait impls, and all sorts of goodies.

[cargo-src]: https://github.com/rust-dev-tools/cargo-src

So i'm naturally led to the question: Can we introduce this to rustdoc, or have some work on it to
make it an official component as a source browser? `cargo-src` is currently server-based - it starts
a web server to serve its pages and operate its search functionality. This runs counter to how
rustdoc currently works, which is as more of a static-site generator, with any server needing to be
separately laid on top. However, rustdoc currently has at least as much information as the RLS does,
so if we can find a way to adapt our information into something that can use, we may be able to make
it work.

## transmutation

One more area i would love to see work on deals with one of the core elements of rustdoc. As i
mentioned, rustdoc is a kind of static-site generator: You feed it input in the form of your crate,
and it spits out a bunch of HTML files based on that input. This model has some great advantages -
mainly, that you don't need to start or operate a special server to host Rust documentation. The
ability to use `file://` URLs for local docs, or to store docs in a plain file hosting service like
Amazon S3, allows for docs to be hosted and used without much overhead.

However, there are also drawbacks to this approach. Any dynamic content needs to be written as
JavaScript and distributed alongside the docs. Rustdoc's design of giving each item its own URL
means that each item needs its own page. If a crate happens to have a lot of items (say it's a
particularly big crate or hosts a lot of bindings like `winapi`), now we're creating a whole lot of
files! (Combined with additional "redirect pages" to support old-style URLs, now we're creating
*two* files for each item!) In addition, the fact that the HTML is rustdoc's *only* output format
means that it can't easily integrate into broader documentation systems like Kapeli's [Dash][] or
systems that use its package format.

[Dash]: https://kapeli.com/dash

Is there a way to deal with these downsides without also discarding the "static-site generator"
model that gives us these strengths? I can think of two parallel approaches that can help this:
Restoring rustdoc's JSON output, and adding support for a system that can read files from an archive
format. The former is easy enough to fathom - way back in the early days, rustdoc used to be able to
output JSON that corresponded to its model of crate information. This has been taken out, but it's
possible to leverage the `save-analysis` data that the RLS uses to bring it back. I wrote [a thread
about this idea][json-thread] on the Interals forum recently.

[json-thread]: https://internals.rust-lang.org/t/design-discussion-json-output-for-rustdoc/8271

JSON output would help integrating rustdoc with other frontends, by providing a means to create a
machine-readable format that can be converted to whatever system is desired. But that's a little
abstract, and doesn't provide an immediate solution for the "file explosion" problem. There's a
recent project by @killercup that aims to solve exactly this problem: [`static-filez`][].

[`static-filez`]: https://github.com/killercup/static-filez

This is an experiment to create a web server that loads pre-compressed files from an archive, and
serves them without having to unpack and recompress them. With a formalized archive format (it
currently uses a custom archive/index format that it packs itself), rustdoc could natively output
one of these archives, so instead of creating thousands of files, it would now create two. Combined
with a potential new Cargo command to load up this archive with the `static-filez` server, a user
wouldn't see the difference! I'm hoping this combination would create a huge speedup on systems
where creating mass numbers of files is a performance bottleneck.

## docs-r-us

So far, this post has been focused on "upstream rustdoc", but i'd also like to give [docs.rs][] a
mention. Docs.rs has made some great strides this year! Until recently, docs.rs was primarily
maintained and operated by one person, and had accumulated plenty of technical debt that had kept it
from keeping up with the Rust community. However, this year we were able to give it the work it
needed to reach its full potential. It can only go up from here! Without going into much detail (i
kinda want to make a breakdown of docs.rs its own post), there's more technical debt to tackle, and
we're working to properly move the server to something that's officially managed by the Rust
Project, instead of run by one person.

[docs.rs]: https://docs.rs/

## some witty heading about wrapping things up

So that's what's on my mind. Consider this a preview of the Rustdoc Team's personal roadmap, as the
Core Team works to assemble everyone's input into next year's project-wide roadmap. I'm hopeful that
in the coming year, we can take the work we've put into 2018 and use it as a jumping-off point to
greater heights. We've got the [Edition release][] set up, with other banner features waiting in the
wings. Let's take this moment to sketch out something special.

[Edition release]: https://blog.rust-lang.org/2018/12/06/Rust-1.31-and-rust-2018.html
