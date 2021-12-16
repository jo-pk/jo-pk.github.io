---
layout: post
title: the union of parallel universes
description:
  breaking down how rustdoc can display docs from multiple platforms at the same time, and why
  that's significant
categories: code
---

Rustdoc has a pretty powerful feature that feels pretty unknown. It doesn't help that it's currently
restricted by a nightly feature gate, but it's still cool enough that I want to talk about it.

If you've taken a look at [the standard library documentation for OS-specific
functionality][std::os], you may see something striking. There are pages there for Linux *and*
Windows! This may not seem all that impressive, but consider: Rustdoc needs to partially compile a
crate to be able to document it. The step in compilation that would take out one of those modules is
well before the part where rustdoc can get useful information out of it. So what's the secret?

[std::os]: https://doc.rust-lang.org/nightly/std/os/

It turns out, the standard library [cheats a little][cfg-dox]. When generating the standard library
documentation, the Rust compiler build system adds [a special flag][bootstrap-dox] to its call to
rustdoc that creates a new condiditional compilation setting. This `--cfg dox` allows the library to
set up special conditions that only happen when documentation is being generated. This is used in
the `std::os` module to expose multiple OS submodules to documentation.

[cfg-dox]: https://github.com/rust-lang/rust/blob/master/src/libstd/os/mod.rs
[bootstrap-dox]: https://github.com/rust-lang/rust/blob/fedce67cd21dc08ece5a484fe1a060346acac98a/src/bootstrap/bin/rustdoc.rs#L44-L45

By itself, this might be enough, but there's another key element in play. Because of the way that
`--cfg dox` is added, it's used every time rustdoc is called - even for doctests. This means that if
there's something in `std::os::windows` that has a doctest, and that doctest wants to link and run
against some Windows-specific API, rustdoc would try to link that every time, even if it's not being
run on Windows! So now what?

The answer involves some other attributes that got added at the same time. If you go back to the
`std::os` source, there's an extra attribute on the `pub mod linux` statement: `#[doc(cfg(target_os
= "linux"))]`. This is a little signal to rustdoc that the contents of this module are only supposed
to compile on Linux. The net effect of this does two things:

1. When building documentation for the crate, rustdoc will stick a little flag on the item that says
   "This is supported on (this configuration) only." (In short listings, like on a module page, it
   will just print the configuration, not the full statement.)
2. When running doctests for the crate, it will only run doctests on items with this tag if the
   current build environment includes the given configuration. No more running your Windows tests on
   Linux, your ARM tests on x86, and so on.

`#[doc(cfg(...))]` is [an unstable feature][doc-cfg] right now, meaning you can only use this
attribute with the nightly compiler. However, there's nothing stopping this feature from getting
some more testing "in the wild" and becoming stabilized! There are basically two steps to getting
this feature to work in your own crate:

[doc-cfg]: https://github.com/rust-lang/rust/issues/43781

1. Get rustdoc to see the item in the first place. The standard library does this with that `--cfg
   dox` trick, which is valid even on stable, but you could also create a `"documentation"` feature
   in cargo that you use when you generate docs, either for hosting or on docs.rs. Either way, you
   need to extend your conditional compilation attributes to allow "when i'm documenting" as well as
   "when this feature should be actually used".
   * (Don't worry about rustdoc compiling some invalid code when it builds your crate. It cheats
     when compiling anyway - it doesn't finish the job, so it doesn't get to the point where it
     would codegen and link anything, but also, it uses a special pass in the compiler to remove
     function bodies, so that it doesn't have a chance to process much in the first place. Literally
     all it sees is the function and type definitions, so it won't have a chance to build anything
     that properly touchese the platform.)
2. Tag your items or modules with `#[doc(cfg(...))]` giving it the same information you would to a
   regular `#[cfg(...)]` attribute. Tagging a module will apply that information to all its children
   as well, so you don't need to duplicate that everywhere. When printing documentation for the
   item, it will take all the combined flags to create the final string for the docs. It has some
   handling in place to pretty-print common processor architecture and operating system names, as
   well as ways to display complicated `any(thing, all(this, that))` combinations.

And that's how you get rustdoc to print all your platform- or feature-specific docs all at once! I
would love to see this feature get some use outside the standard library docs.
