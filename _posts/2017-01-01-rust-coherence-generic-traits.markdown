---
layout: post
title: coherence versus generic traits in rust
description:
  breaking down how Rust's From trait allows for custom implementations, by talking about its
  coherence system
categories: code
---

There was a question in #rust-beginners a while back where someone was confused about how anyone
could write an impl for `From` when an `impl<T> From<T> for T` already existed. For the most
part, if you impl something `for T`, then coherence rules generally enforce that no other specific
impls can exist, since they'd overlap with the generalized impl. (At least, until specialization
shakes itself out.)

Initially, I'd thought that it was the preliminary specialization work that had allowed people to
write their own `From<SomeType> for OtherType` impls. My mental rule is just what I'd written above:
An impl `for T` precludes any other specific impl. It turns out, though, that specialization has no
part in this; this rule's been there the entire time, so it had to have worked before specialization
was a thing!

The proper way to think about it is that `From<T>` isn't really *one* trait. It's a sort of trait
category, since `T` could really be anything. So `impl<T> From<T> for T` isn't as broad as it seems.
The situations covered by this line only account for things like `impl From<Thing> for Thing`, which
has a trivial and obvious implementation: Just pass the `Thing` through.

So if you write a discrete `From<SomeType> for OtherType` impl, it won't clash with the generic impl
above, and everything works out. And since this is how the "one error enum to rule them all" pattern
works, it's rather nice that this is available.

(This impl can still get in the way, though. If you want to write a generic `impl<From, To>
From<From> for To`, regardless of what constraints you put on, it will clash with the above impl,
since - in Rust 1.14, at least - you can't specify that `From != To`. Until you can add that
constraint in, impls of this kind are off-limits, sadly.)

[discuss on /r/rust](https://www.reddit.com/r/rust/comments/5lhlen/coherence_versus_generic_traits_in_rust/)
