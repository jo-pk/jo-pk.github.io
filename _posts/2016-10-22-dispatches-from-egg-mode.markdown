---
layout: post
title: notes on building a medium-sized library in rust
description: a handful of lessons learned from writing the "egg-mode" twitter library
categories: code
---

The last few months, I've been building up a [Twitter library in Rust][egg-mode].
Now that I have it to a fairly-fleshed-out state,
I'd like to take the time to write up a little about its internal structure,
and along the way shed some light into the way a project grows from nothing
to a small-to-medium-sized codebase.

[egg-mode]: https://crates.io/crates/egg-mode

### "oh no, what's wrong?"

From lurking in #rust-beginners long enough,
I've noticed that when people get started learning Rust's error handling,
they inevitably run into the issue of handling many different errors in one function.
There's a common enough pattern in libraries that are big enough
where they make an enum that gloms all their various errors together,
then `impl From<LibraryError>` for whatever error varieties they might come across.
[egg-mode is no exception.][error-enum]
Having "one error to rule them all" lets you leverage the most powerful aspect of the `try!()` macro;
it doesn't return the error value as-is.
Instead, it calls `into()` on the value,
allowing it to be converted into the actual Err type,
should a `From<ThatError> for ActualError` impl exist.

[error-enum]: https://shiva.icesoldier.me/doc/egg_mode/error/enum.Error.html

### "it's like having a custom prelude"

There are a few pieces in egg-mode that are so pervasive nearly every module uses them.
These are the functions and types that deal with assembling a request and parsing the response.
As I added more outward-facing modules to the library,
I wanted to keep these elements together and make them easy to use from inside the project.
So I created a "common" module to hold them.
Even though the actual folder contains several different files,
I took care to "flatten" the module by glob-importing the submodules into the parent one.
This way, all the other modules in the crate just need to `use common::*;` to use these "common" methods.
In talking about this on IRC, I've likened it to having a "custom prelude",
since you have a common set of items that have been brought into the module's namespace,
just like how [Rust includes some common items from std][prelude] in every file it compiles.

[prelude]: https://doc.rust-lang.org/std/prelude/

### "custom traits for custom magic"

One of my favorite tricks I developed for egg-mode is [FromJson][].
Nearly everything that needs to parse a response from Twitter goes through this trait.
Not only do the high-level outward-facing structs implement this,
but I've also written several implementations for primitives, vectors, pairs, even Options.
There are two major functions that leverage this trait to make writing the library really easy:
[one meant to parse a value for a larger struct][field]
and [one meant to load a full response into a result][parse_response].
I love using this to leverage Rust's generics
to make "two small functions" apply themselves to dozens of situations,
without having to care about those foundational layers once I've set them up.
(It's probably also a leading contributor to my compile times,
but I'll take it for the immense wins to productivity it's given me otherwise.)

[field]: https://github.com/QuietMisdreavus/twitter-rs/blob/67aa9a38c6dd415078afe23d6d07b03b2398ba16/src/common/from_json.rs#L159-L161
[parse_response]: https://github.com/QuietMisdreavus/twitter-rs/blob/67aa9a38c6dd415078afe23d6d07b03b2398ba16/src/common/response.rs#L376-L385
[FromJson]: https://github.com/QuietMisdreavus/twitter-rs/blob/master/src/common/from_json.rs

This setup came about because I caught myself repeating myself when parsing my first few structs.
I'd made the FromJson trait because I'd noticed a ToJson in rustc-serialize,
but found no counterpart trait to denote a conversion back out.
I'd wanted to use the proper deserialization system to load these,
but eventually realized that was not going to work
when I noticed the following remark in [Twitter's documentation][]:

[Twitter's documentation]: https://dev.twitter.com/overview/api/users

> Consumers of Users should tolerate the addition of new fields and variance in
> ordering of fields with ease. Not all fields appear in all contexts. It is
> generally safe to consider a nulled field, an empty set, and the absence of a
> field as the same thing.

In other words, "the layout and structure of a TwitterUser is malleable and subject to change at any time."
This does not match the nature of a serialization library,
which usually wants you to control the structure of data entering and leaving your program.
With that in mind, I changed my tactics
to manually load each field whenever I needed to create one of these structs.

I'd initially intended FromJson to just be written
for the full structs that would be returned from a hypothetical library call.
This worked initially, but led to [a handful of specific methods][] to pull specific types for a field.
Over time, especially after I wanted to [load *other* high-level structs as fields][cursor-init],
I'd made a general "field" method that ran `from_json` for whatever it wanted to pull.
More and more FromJson impls started accumulating,
before I realized that I could [just blanket FromJson over *everything*][impl FromJson],
and make struct parsing a whole lot easier.
The result is code that could probably be generated from a macro,
but I haven't taken that step yet.

[a handful of specific methods]: https://github.com/QuietMisdreavus/twitter-rs/blob/57727ef0ad65b4bfbe18fa27ad2e330ee1536c54/src/common.rs#L145-L159
[cursor-init]: https://github.com/QuietMisdreavus/twitter-rs/commit/686475dec299a13674952de54c212923a547241f
[impl FromJson]: https://github.com/QuietMisdreavus/twitter-rs/commit/e6917228b05e07eca099b6e428dbce839bd2a211

The other wonderful thing that comes out of leaning on this trait
is that [actual method calls][] turn into "assemble parameters, call Twitter, load the response".
I don't have to specialize the result of these generic calls
because Rust's fantastic type inference already knows I want a TwitterUser in the final result.
This is one of the joys of Rust programming,
and one of the reasons I'm able to add new integrations with relative ease;
much of the tedium can be left to lower layers of the library.

[actual method calls]: https://github.com/QuietMisdreavus/twitter-rs/blob/67aa9a38c6dd415078afe23d6d07b03b2398ba16/src/user/fun.rs#L386-L395

[discussion on /r/rust](https://www.reddit.com/r/rust/comments/58w5u3/notes_on_building_a_mediumsized_library_in_rust/)
