---
layout: post
title: not a layer cake analogy
description: a discussion of what "intermediate docs" means, and a wishlist of such docs for Rust
categories: code
---

I've been following some of the #Rust2018 blog posts with some excitement. It's kinda fun getting a
sense of how we can build on our past successes to reach new heights. Closer to home, the docs team
started trying to put their own goals together for the year, and that discussion combined with a few
of the community blog posts brought something to my attention:

Several people have recognized that there's a bit of a gap between "how you use the language"
*beginner* tutorials, and "minute esoterica"/"cumulative design planning" *advanced* discussion.
Everyone called out a lack of *intermediate* docs, but... i wasn't actually sure what fell into that
gap. `>_>` So i did what came naturally, and [i asked/rambled about it on twitter][thread].

[thread]: https://twitter.com/QuietMisdreavus/status/950835431163289603

My initial thought was that such documentation wouldn't be *about* Rust specifically, but more about
some other subject matter, just with the added window dressing of using Rust libraries and code
samples. In other words, "intermediate" docs wouldn't be framed as "docs" at all, but instead a
deep-dive *blog post* that's mainly about something else, other than Rust specifically. As someone
who hasn't really been attached to any specific area in computing, the idea of trying to write one
of these myself felt rather intimidating.

There were some great responses to the Twitter thread. Here are some responses for specific ideas
that people considered to be "intermediate" docs, or things that were generally lacking that were
more advanced than TRPL itself:

- A sense of what was considered "idiomatic". Once you have a grasp of what knobs do what in the
  language, how do you learn what's considered "proper", or what structures people have found to
  make future maintenance easier? Right now that is partly solved by clippy, but short of [running
  through the entire list of lints][clippy-all], it's hard to see these opinions without (1) knowing
  about clippy in the first place, and (2) getting it "wrong" in your own crate so that clippy can
  correct you. Moreover, clippy lints are still about small details. It won't help you grasp the
  best way to structure data in your project, or other larger issues that are mainly solved by
  experience, or informed by...
- A sense of how classical design patterns map (or don't map) to Rust. I'll focus more on this
  farther down, but one problem with the beginner-level docs (that it's not meant to solve on its
  own) is how to structure larger programs. While some of the basic patterns have some folk
  knowledge if you extrapolate from how things are implemented in the standard library and beyond,
  having a place where this is explicitly codified can help a ton in getting more people to write
  good software, in Rust and beyond.
  - Related to this, it can help to augment classical design patterns with new ones that arise from
    Rust's set of restrictions and features. How do i maintain proper ownership for this data? How
    to i manage these borrows? How do i ensure this library doesn't needlessly copy data around? How
    can i properly use a library that makes this claim?
- Language features that don't fit in a beginner tutorial, but don't require as much background
  knowledge as (say) higher-ranked lifetime bounds. Features like FFI or even macros are useful, but
  they don't have a proper place in the same document that's meant to be a user's first introduction
  to Rust.
- Deep-dives of large libraries, discussing how to fully integrate something into a larger
  application. The tiny examples in a crate's documentation are often too small to be useful if
  you're not making a tiny program yourself. If the crate has larger examples in its repository,
  that can be helpful, but if they're devoid of context, they can also become too cumbersome to
  read, or are just collections of more tiny examples themselves.

[clippy-all]: https://rust-lang-nursery.github.io/rust-clippy/master/index.html

Working from there, i dreamed up a few concrete suggestions that seem to address these:

- A sort of "phrasebook" about Rust idioms (which can be sourced from compiler lints and clippy),
  and a larger "guidebook" talking about design patterns. (I liked the "travel guide" metaphor here,
  helping drive home the idea that this is helping around people who aren't familiar with the
  landscape.)
- A guide on managing concurrency. There are mentions of small things Rust does (specifically,
  Send/Sync and how it meshes with ownership), but no sense of "i have all this data and want to
  work it in parallel, what should i use?" or "i want to manage these requests concurrently, how
  should i manage that?", so a concrete set of suggestions regarding these and other concurrency
  questions had been specifically requested.
- A survey of major/popular libraries, and how to work with them in a "large application" context.
  Frankly, this exists right now as the [Rust Cookbook], so bringing this into prominence is mainly
  a matter of expanding and promoting it more.
- A series of case studies of large applications written in Rust, or production users recounting the
  issues they had and how they worked through (or around) them. There's a book called [The
  Architecture of Open Source Applications][aosa] that takes a look at major open source
  applications through the lens of architecture, noting the structural decisions that were made in
  their development. I'm imagining something much like this, but specifically with an eye toward
  whether/how Rust helped (or even harmed, or at least guided) the development.

[Rust Cookbook]: https://rust-lang-nursery.github.io/rust-cookbook/
[aosa]: http://www.aosabook.org/en/index.html

After looking at these suggestions and ideas, i noticed something about the difference between these
proposals and the existing literature, both official and unofficial, that serves as Rust
documentation. I have a sense of beginner and advanced documentation both focusing on small details.
With beginner documentation, the "small details" they focus on are things like basic syntax,
specific language features, or how certain things interact in specific cases. With advanced
documentation, you still get "small details", but now they're about language features that require a
lot of context, or the implications of a certain feature, or implementation details of some library
or feature or the like.

The key difference is one of context. The beginner documentation only needs a sense of programming
topics, some experience with another language (or not even that if you're focusing on introducing
someone to programming in the first place), but nothing about Rust specifically. The advanced
documentation, on the other hand, builds on extensive knowledge of the rest of the language, or the
implementation of some system, or background knowledge of some field, or the like.

"Intermediate" documentation, on the other hand, seems to focus more on The Big Picture. In other
words, beginner docs show you a set of tools, advanced docs show you their logical underpinnings,
but intermediate docs would tell you how they work together, how to build something with them. Hence
all the focus earlier on "large programs".

([I then made a sword metaphor to highlight this difference.][sword])

[sword]: https://twitter.com/QuietMisdreavus/status/951132798936592384

Without this kind of corpus, we create a sense that Rust is a language for toy programs, or for
people who already know about program design from a different space. There's no way to learn how to
write big programs with Rust without just doing it yourself, and that can be a risk people are
unwilling to take. Worse, several kinds of designs from other languages (especially those that lean
heavily on object-oriented design) are downright painful in Rust, and without a written game plan
for how to accommodate that it can be a tremendous stumbling block for people learning Rust.

If this all feels like a massive oversimplification, it probably is. This is mainly a summary of
yesterday's Twitter thread for my own purposes, so i can have that list of ideas that came out of
it. If you have more specific ideas for documentation that could fill this space, let me know! I'd
love to see some of this come together this year.
