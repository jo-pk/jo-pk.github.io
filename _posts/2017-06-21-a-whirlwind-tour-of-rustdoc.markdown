---
layout: post
title: a whirlwind tour of rustdoc
description:
  a walkthrough of the internals of Rust's documentation tool, rustdoc
categories: code
---

I'm generally a fan of `rustdoc`. I love that there's effectively a one-touch access to creating
good-looking documentation for my libraries. It's not without its own issues, but the fact that it's
distributed alongside rust, and produces pretty good output, is enough to make me happy about it.

But before we get too far, what even *is* rustdoc? Put simply, it's the thing that runs
under-the-hood whenever you run `cargo doc`. It takes a crate as input, and generates a pile of HTML
pages (and their supporting assets) as output. You can preview what the output looks like by
checking out [rust's standard library documentation][std-doc]. By default, it includes the theme
shown there, the search box that searches anything within that crate, and docs for all a crate's
dependencies, whose items are also included in that search index.

[std-doc]: https://doc.rust-lang.org/std/

(For a bit of history: rustdoc is pretty old. The [commit that added it][1360] dates to December
2011! That makes it almost as old as rustc itself (started in July 2009 and successfully self-hosted
in April 2011), well before Rust 1.0 landed in May 2015.)

[1360]: https://github.com/rust-lang/rust/pull/1360

## where oh where has my little crate gone

That's all well and good, but what happens if you want to tweak something about it? Say, there's a
formatting issue you want to fix, or it's not outputting docs for something you would expect it to
pick up. *Where* is rustdoc?

It actually doesn't have a repo of its own. It's situated [alongside the source for the rust
compiler][rustdoc-src], and is built alongside it whenever you build the compiler as a whole. This
means that if you want to tweak it and test out your change, you need to ~~submit yourself to~~ get
used to the compiler's build system. This can be an issue if you haven't dealt with rustbuild
before, or if you just want to test a small change.

[rustdoc-src]: https://github.com/rust-lang/rust/tree/master/src/librustdoc

## show me the way to docsville

So here's a bit of a crash course in `rustbuild`, the build system for the compiler. A better
introduction is available in [the main repo's README][rust-readme], but here are the basics:

[rust-readme]: https://github.com/rust-lang/rust/blob/master/README.md

All interaction with the build system is either done through git (if you need to deal with
submodules) or through a wrapper script called `x.py`. This provides a means to download known-good
versions of `rustc` and `cargo` to compile [the *actual* build system][bootstrap], written fully in
rust. To get to just the parts we care about, there are two commands worth knowing: `./x.py build`
and `./x.py doc`. The first just compiles the bits we ask for (or everything, if you don't ask for
something specific), and the second compiles everything and then renders the full suite of
documentation: standard library docs, the book, the reference, anything you see at
<https://doc.rust-lang.org>.  It's useful if your rustdoc change can be seen by looking at the
standard library docs instead of having to render a separate crate's docs for your test.

[bootstrap]: https://github.com/rust-lang/rust/tree/master/src/bootstrap

## follow the rusty-brick road

~~If you just run one of those commands by themselves, you'll start to notice something annoying: they
compile a *lot* of things. This is the nature of building the compiler: it goes through a
"bootstrap" sequence, where it starts with a known version (in this case, the most recent beta),
builds the compiler, then uses the result to build the compiler *again*, just to prove that
everything still works. (There's an optional *third* step involving building a third time to prove
its reproducible. This is turned off by default.)~~

~~If you were just working on the compiler or standard library, then you could get around this with
the `--stage` and `--keep-stage` flags, but [this doesn't quite work when testing rustdoc on the
standard library documentation][42686]. At least, as of this writing. So for now, if we want to test
rustdoc the easy way, we're stuck recompiling the whole stage to re-render the standard library
docs. I'll mention an alternate method later on.~~

[42686]: https://github.com/rust-lang/rust/issues/42686

~~To test changes to rustdoc by rendering the standard library documentation, make your changes to the
code, then call `./x.py doc --stage 1` to build rustdoc and render the library documentation. This
will compile just rustdoc on stage 0, then all of stage 1, then render the set of documentation. The
rendering will be available in the `build` directory in your rust clone, specifically
`build/$TRIPLE/doc`, where `$TRIPLE` is the "build triple" of the system you're working on. In my
case, it's `x86_64-unknown-linux-gnu`, since I do my Rust development on a 64-bit Arch Linux
system.~~

~~If you don't want to wait for the entire stage to build every time, or if you need to test something
that doesn't have a good representative case in the standard library, then you can take your custom
rustc and rustdoc and use them to render documentation for a separate crate. To do this, run `./x.py
build src/libtest --stage 1` as  your build step. This builds just enough that you get useable
binaries for the next steps.~~

(UPDATE: Thanks to [PR 43482], rustdoc is now outside the regular compilation flow! This means a lot
of the above gripes and instructions are invalid. To build standard library documentation, you can
just run `./x.py doc --stage 1 src/libstd`, and it will output the docs in `build/$TRIPLE/doc`,
where `$TRIPLE` is the build triple of the system you're working on. In my case, it's
`x86-unknown-linux-gnu`. This will compile stage 1 artifacts, but only the first time. After that,
only rustdoc itself will be rebuilt if that's all you changed. To build a rustdoc you can use to
compile other crates, run `./x.py build --stage 1 src/tools/rustdoc src/libstd`. Then set up a
linked toolchain like below.)

[PR 43482]: https://github.com/rust-lang/rust/pull/43482

If you're using rustup to manage your regular rust installs, it has a fantastic feature for people
who work on the compiler. You can "link" a local directory to create a custom toolchain. To do this,
run the command `rustup toolchain link local /path/to/build/$TRIPLE/stage1`. (Replace the path in
the last argument with the actual path to the `build` directory in your Rust clone. The
`build/$TRIPLE/stage1` is the actual structure that needs to be at the end of the path, much like
the standard library docs were in `build/$TRIPLE/doc`.)

Once you've set this up, you can use your "local" rustc and rustdoc just like you would use the
"stable" or "nightly" toolchains! The "local" in that rustup command earlier set the name for a new
toolchain. Therefore, you can generate documentation with `cargo +local doc`. If you've rebuilt your
rustdoc but haven't touched the other crate, you may need to run `cargo clean` beforehand so it
actually rebuilds everything.

(If you don't have rustup, don't fret! It's still possible to use your custom toolchain for a
one-off documentation run. It's just a little more complicated. Cargo recognizes two environment
variables, `RUSTC` and `RUSTDOC`, which point to their respective binaries. By setting these two
environment variables and calling `cargo doc`, you can make cargo use your custom toolchain even
without rustup!)

## the winding streets of the verdant rustdoc city

(caution: the only part of rustdoc i've extensively worked with is the part that generates the HTML;
i make no claims of accuracy for any other component of rustdoc `>_>`)

Rustdoc uses the compiler-internal libraries, so it can read a crate the same way the compiler
would. However, to guard itself against changes to the compiler, it takes the "raw" syntax tree and
converts it into its own simplified version before working on it. This "doctree" version of the
crate is then "cleaned" to strip the AST to only those elements that make sense for documentation
(for example, items inside function bodies are never visible outside that function, so they're
removed when crawling the AST).

With this "clean" version in hand, it then generates the search index, writes out the static files
that go with the output, and walks the crate, writing each item in turn to the appropriate folder
and file. The formatting is actually implemented as a bunch of `fmt::Display` impls, so the template
is [literally a big `write!` call][layout.rs] with the page template as the format string. This way,
modules render links to all their items, traits render a listing of their methods and implementors,
enums list their variants, and so on.

[layout.rs]: https://github.com/rust-lang/rust/blob/29bce6e220f6fd2292d13d65fe503af7bf4852b7/src/librustdoc/html/layout.rs

To render documentation comments, the Rust compiler first converts `///` and `//!` comments into
`#[doc]` attributes, then rustdoc collects all of these together and hands them off to a Markdown
renderer to convert to HTML. The resulting HTML is then used wherever the documentation for an item
needs to be.

At the moment, rustdoc defaults to using a renderer called Hoedown, but [we're currently testing an
alternative implementation][pulldown] called pulldown-cmark that conforms to the CommonMark
specification and allows for easier extension due to being written in Rust. As of Rust 1.18, you can
test the pulldown version by passing `--enable-commonmark` to rustdoc, like with
`RUSTDOCFLAGS="--enable-commonmark" cargo doc`.

[pulldown]: https://github.com/rust-lang/rust/pull/40338

If you're asking rustdoc to run documentation tests, it runs *most* of the steps above, but stops
short of emitting the final HTML. Instead, it collects all the documentation comments from the crate
and hands them to the Markdown renderer to see what it picks up as a code block. From these code
blocks, it reads through them to find whether it needs to remove a trailing `#` from hidden lines,
or add the required `extern crate` line and `fn main()` wrapper, or even needs to process it at all
due to a `no_run` or `ignore` flag on the block.

For those that need to run, it takes the code after processing and hands it off to the compiler to
build the test. For those that need to run, it then executes the test and checks the result to
evaluate the final test result.

## pay no attention to those maintainers behind the curtain

Rustdoc is an official Rust project, and falls under the resposibility of the Dev tools team. In
addition, there are three "rustdoc tool peers" who could be considered the primary maintainers of
rustdoc: @steveklabnik, @GuillaumeGomez, and myself (@QuietMisdreavus). Issues against rustdoc can
be posted on [the main rust-lang/rust issue tracker][rust-issues], and short-form discussion about
it can be had in the `#rust-dev-tools` IRC channel on the Mozilla IRC network.

[rust-issues]: https://github.com/rust-lang/rust/issues
