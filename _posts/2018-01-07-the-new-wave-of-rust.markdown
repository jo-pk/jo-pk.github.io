---
layout: post
title: the new wave of rust
description: a wishlist of what i would like to accomplish with Rust in 2018
categories: code
---

With the new year comes a new chance to talk about your goals for the coming months. The Rust
community has issued a call for contributions to ask people what they want the Rust project’s
priorities to be in 2018. However, I’m generally terrible at big-picture planning, so instead I’d
like to talk about what I want to accomplish in the Rust world in 2018. Whether this slots into
anyone else’s planning is another matter, but I’ll leave that as an exercise for the reader.

## Rustdoc

Rustdoc is definitely the most high-profile project I work on, and I don’t intend to stop anytime
soon. Here’s a sampling of individual items that are currently in the air for me personally, and a
look ahead at slightly more nebulous goals that I can muster:

- Manish Goregaokar and I have been working back-and-forth recently on one of the most-requested
  items for rustdoc: [The ability to link to other items in your docs by using a Rust path instead
  of a file path][intra-paths]. There have definitely been some hiccups in this one, but we’ve made
  great strides so far, so it’s looking pretty good right now. I hope to have this one on nightly in
  a couple weeks.
- With the recent release of Rust 1.23.0, we’re nearly ready to move forward on one project that has
  taunted the doc team for a long time now: Standardizing the markdown parsing in rustdoc by
  [migrating its markdown parser from Hoedown to Pulldown][pulldown]. This has been sitting in
  nightly for a while now, but has only recently been able to move on because of issues where the
  same text would render differently between the different parsers. Rustdoc can detect these
  differences and emit warnings on it, but until recently it would only print those warnings if you
  used the new parser.  With the 1.23.0 release, these warnings now print by-default on the stable
  version of rustdoc, so it’s much more likely that people whose libraries are affected by it will
  notice the differences.
- [External docs] hit nightly fairly recently, so it’s time for people to start using that and
  putting it through its paces. It’s currently an unstable feature that requires a feature flag to
  unlock, so if you want to use it, your library will need to start being nightly-only for it. I’m
  hoping that if this feature works out, it can make documentation much easier to translate, opening
  up new horizons for the docs of the Rust community.
- I’d like to go back and revisit the ["Important Traits"] spotlight feature, to address the
  comments that have been made there. It’s a fairly niche feature, but I really want to get it
  stabilized so it can be used for other “fundamental” traits like Future without making it a
  nightly-only feature.
- A month or so before the impl period, I started making [a central list of all the issues that had
  been tagged with “rustdoc”][mega-list], so I could triage them based on their expected effort. As
  the impl period kicked off I neglected that list, and I’d like to revisit it at some point.
- Longer term, I want to add more things to rustdoc that allows it to display more and more
  cross-cutting information about a library, that need the compiler’s support to figure out.
  Outstanding feature requests like [transitive trait implementations (like the ToString trait, for
  example)][tostring] or [auto-traits], come to mind, but even something like reducing the noise
  around printing impl blocks around methods is something that I’ve thought about before.
- There is, of course, also the outstanding rewrite that Steve Klabnik is working on. He’s shown me
  some of the ideas that he wants to work into it, and I’m very excited to see what happens here. As
  far as I remember, some of the details here are slightly under wraps, but look forward to this one.

[intra-paths]: https://github.com/rust-lang/rust/pull/47046
[pulldown]: https://github.com/rust-lang/rust/issues/44229
[External docs]: https://github.com/rust-lang/rust/issues/44732
["Important Traits"]: https://github.com/rust-lang/rust/issues/45040
[mega-list]: https://github.com/nrc/dev-tools-team/issues/23
[tostring]: https://github.com/rust-lang/rust/issues/33772
[auto-traits]: https://github.com/rust-lang/rust/issues/17606

## Libraries

Other than documentation, some “bucket list” projects for Rust have been several libraries or
frameworks that either are undeveloped or under-developed, or just things that I want to iterate an
API on so I can point to something I know well when introducing people to Rust. Just so I can commit
my ideas to public text, here are some of the things I would like to work on:

- Arbitrary SOAP service client/server bindings. Most of my pre-Rust experience was in .NET, and a
  significant amount of time there was spent working with Windows Communication Foundation (WCF). WCF
  made it way simple to set up little internal services that passed some structured data around, but
  it also let me dig into the details when I needed to interface with some precisely-specified SOAP
  services, as both a client and as a server for webhooks. I would love to have something that simple
  and powerful in the Rust world.
  - I do have [a small implementation of a small SOAP client][soap-test], interfacing with the
    default WCF project on my own computer. I wrote up [my own limited extension to
    xml-rs][sulfate-xml] to make it work, which I can hopefully extend if I get back to this.
- Fluid music tag handling, and a “tag templating script” much like the Title Formatting scheme that
  foobar2000 and Winamp use. The last time I looked, the most I could find in this area was a binding
  of Taglib’s C API (which is much smaller than its C++ API), and a pure-rust ID3 (for MP3 files)
  crate for the standard ID3 tags. To fully scratch my itch, I also want to see handling of iTunes MP4
  audio tags, which I couldn’t find a single crate for, handling for even more tag varieties, like APE
  or FLAC or Ogg, and something to wrap them together so that they could be treated in similar ways.
  (Granted, it’s been a while since I’ve done a proper survey of this, but I didn’t expect this to be
  a hot area for Rust anyway. It’s primarily a personal itch so I can write a music player or library
  organizer. `>_>`)
  - I’ve gotten [a bit of a start][mp4tag-test] writing that iTunes MP4 tag parser. I basically got
    the proof of concept rolling and got distracted again. It would be nice to come back to this and
    finish it, but I would like to muse a bit more on the API, so I can make sure it works with my
    ideal tag wrapper.
- More synchronization experiments, either by extending my current ones, or integrating them into
  larger libraries like rayon or crossbeam, or something else. I’m less inspired here than in other
  places, but I do like working on synchronization problems, so hopefully I can come back to it.

[soap-test]: https://github.com/QuietMisdreavus/rust-soap-test
[sulfate-xml]: https://github.com/QuietMisdreavus/sulfate-xml
[mp4tag-test]: https://github.com/QuietMisdreavus/mp4tag-rs

## In sum

Like I said, I’m fairly terrible at offering generalized suggestions for the tools I work with. I’m
much more content to work around their foibles and within those constraints. Instead, I’m hoping to
spend the next year making slick docs and slick libraries. The hope is, if I can make these happen,
then we can bring more people into the Rust world.
