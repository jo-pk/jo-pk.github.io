---
layout: post
title: how the ~~sausage~~ doctests get made
description: breaking down how rustdoc turns a code sample into a test
categories: code
---

One of rustdoc's greatest features is the ability to take code samples within your documentation and
run them like tests. This ensures that all your samples stay up to date with your library's API
changes. However, there are some steps that need to happen to massage these "doctests" into
something that can be compiled and run like a regular program.

To understand why we need to modify code samples at all, let's take a look at a typical one. Here's
a small sample from [the front page of the `rand` crate][rand-docs]:

```rust
use rand::Rng;

let mut rng = rand::thread_rng();
if rng.gen() { // random bool
    println!("i32: {}, u32: {}", rng.gen::<i32>(), rng.gen::<u32>())
}
```

The code is written such that you could copy/paste it into your own function to generate some random
numbers. You can't really take this and compile it as a standalone executable; it's missing the
crate import for `rand` or a main function. To make things even more complicated, farther down [that
same page][rand-docs] there are two more examples that each feature a handwritten main function, so
we can't just blindly wrap everything with `fn main`.

[rand-docs]: https://docs.rs/rand/0.4.2/rand/

For these and other reasons, rustdoc has built up [a few rules][make_test] it applies to doctests
before handing them off to the test runner. I want to take the rest of this post taking that linked
function apart, and walking a doctest from "handwritten code sample" to "test code ready to compile
and run". There are some hidden features that get exposed here, and I'd like to show them off.

[make_test]: https://github.com/rust-lang/rust/blob/063deba92e44809125a433ca6e6c1ad0993313bf/src/librustdoc/test.rs#L338-L404

The very first thing it does is one of these lesser-known features. Did you know you can inject
arbitrary crate-level attributes into all your tests? If you add `#![doc(test(attr(...)))]` to the
root of your crate, rustdoc will pick up that `...` and copy that as a series of attributes in any
doctests in that crate. For example, [the standard library][libstd-doctestattr] has
`#![doc(test(attr(deny(warnings), allow(dead_code, deprecated, unused_variables, unused_mut))))]` to
run several lints on their doctests, but allow several that would otherwise distract from the
example.

[libstd-doctestattr]: https://github.com/rust-lang/rust/blob/063deba92e44809125a433ca6e6c1ad0993313bf/src/libstd/lib.rs#L218-L219

Anyway, the very first thing that rustdoc does is inspect this list of attributes, if it's there,
and insert them into the beginning of the final test. However, if these attributes are *not*
provided, rather than insert nothing, it inserts `#![allow(unused)]` instead. `unused` is a lint
group that includes some usual things you would expect to hit in a short code example - dead code,
unused variables, unused mutability, things like that. These warnings are masked in doctests to
clean up the test output.

So for that sample from `rand` above, there are no attributes to add from
`#![doc(test(attr(...)))]`, so instead it adds `#![allow(unused)]` and moves on:

```rust
#![allow(unused)]
use rand::Rng;

let mut rng = rand::thread_rng();
if rng.gen() { // random bool
    println!("i32: {}, u32: {}", rng.gen::<i32>(), rng.gen::<u32>())
}
```

Next, it looks at the sample and sees whether it has any `#![top_attributes]` or `extern crate
declarations;` at the beginning, and also sticks those at the top of the final test. Inner
attributes at the beginning of a test are usually `#![feature(...)]` attributes, which can only be
at the very top of a crate. If we stick these inside a generated function, the test won't even
compile! As for crate imports, those *can* go inside a function declaration, but then something
unusual happens when you try to use them: they're not automatically imported into scope. So if you
wanted to pull something from the crate without a `use` statement, you'd have to write
`self::some_crate::SomeType` instead. So we pull those out too, to save that headache.

[(That last part was only merged very recently, and is currently only available on nightly.)][48106]

Our demo sample doesn't have any attributes or crate imports to speak of, so this step is skipped.

[48106]: https://github.com/rust-lang/rust/pull/48106

Next, rustdoc adds a crate import for the containing crate. There are actually a few conditions it
checks before adding this, to make sure there are no potential clashes:

1. There are no `extern crate` statements manually written into the doctest. Some samples will want
   to deliberately show off importing the crate, or may want to apply a `#[macro_use]` statement to
   the import, so rustdoc needs to look through the sample for crate imports. If it finds one, it
   assumes that the crate was manually imported already, so it won't add a duplicate import.
2. The crate doesn't add the `#![doc(test(no_crate_inject))]` attribute in its root. This attribute
   is used by the standard library facade to keep tests from getting the wrong crate imported when a
   test is relocated from (for example) `core` to `std`.
3. The crate being documented isn't named `std`. The compiler automatically imports `std` anyway, so
   manually adding it would just clash with that. (`std` also adds `no_crate_inject` so this check
   is slightly redundant, but in case someone else writes a custom libstd, this will cover them.)
4. We're documenting a crate, and not a standalone Markdown file. This is slightly assumed
   throughout this post, but rustdoc can also run tests on standalone Markdown files, so in that
   case there's no "containing crate" to link against.
5. The sample in question uses the name of the crate somewhere. Adding a crate import would be
   redundant if the sample in question doesn't use it, so rustdoc will skip this step if it doesn't
   see the crate name used in the sample.

If all of these conditions hold, then it will add an `extern crate my_crate;` to the final test.
Most of these are fairly easy to hit and only meant for rather niche cases, but these are the
situations rustdoc has had to deal with before. All told, our sample from `rand` passes the test, so
the crate import gets added behind the scenes:

```rust
#![allow(unused)]
extern crate rand;
use rand::Rng;

let mut rng = rand::thread_rng();
if rng.gen() { // random bool
    println!("i32: {}, u32: {}", rng.gen::<i32>(), rng.gen::<u32>())
}
```

Next, we want to see whether the test wrote in its own `fn main`. Many small examples don't need to
specify their own entry point, so they just write under the assumption that they're in a function
already. However, we need to compile the sample as if it were a standalone binary. So rustdoc scans
through the sample to make sure it doesn't define its own `fn main`, and if it doesn't find one, it
wraps the remaining part of the original sample in a new one.

So with our sample from `rand`, this is the final output that gets compiled:

```rust
#![allow(unused)]
extern crate rand;
fn main() {
    use rand::Rng;

    let mut rng = rand::thread_rng();
    if rng.gen() { // random bool
        println!("i32: {}, u32: {}", rng.gen::<i32>(), rng.gen::<u32>())
    }
}
```

(...it actually doesn't indent the generated main function, but I did that here for the sake of
legibility. `>_>`)

Rustdoc stores this final result as the representation of the test that it compiles and runs. This
way, when the test runner comes to this slot in the test sequence, it can take the text it saved
earlier and hand it off to the compiler to build.
