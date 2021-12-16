---
layout: post
title: it's time for some color theory
description:
  an announcement post for a randomized text editor color scheme project, and a dive into how it
  works
categories: code
---

*(Meme reference: ["Guys, it's time for some game theory."][game-theory])*

[game-theory]: https://knowyourmeme.com/memes/its-time-for-some-game-theory

For the last few weeks, i've been working on a Vim color scheme randomizer called [mkcolor][]. It's
a flight of fancy i've had in my head for a while, but only recently have i had the opportunity and
the energy to actually see it to fruition. A lot had to come together to make this work, and i'd
like to spend some time here talking about what's going on.

[mkcolor]: https://quietmisdreavus.github.io/mkcolor/

The idea behind mkcolor started as an idle thought when i saw the [Random Color Contrasts][]
Mastodon bot. This bot periodically posts an image of two colors, and shows their contrast ratio, as
defined by the Web Content Accessibility Guidelines (WCAG). After a while of seeing these posts in
my Mastodon feed, i started thinking: could you take this concept of "the contrast ratio of two
random colors" and extend it to a full text editor theme?

[Random Color Contrasts]: https://botsin.space/@randomColorContrasts

The idea sat in my head for a long time before i did anything about it. However, recently i decided
to sit down and implement a contrast ratio calculator myself, as well as a random color generator.
Once i had sufficiently cloned the RCC bot (minus the color names), i started extending it to
generate more than two colors, and to display them not as two adjacent color blocks, but as a
[colorized source code display][first-code]. From there, the idea ran away from me, and i added more
and more colors, to try and fill in all the standard colors used by a Vim color scheme.

[first-code]: https://twitter.com/QuietMisdreavus/status/1299110715983376385

Before long, i started running into some issues. While i could relatively easily check to make sure
that a given text color had a certain contrast ratio against its background, it wasn't as simple to
make sure that different text colors actually "looked different" from each other. Or, as i started
adding highlight and UI colors, that the generated colors actually looked good together. Or, as i
got farther and farther along, that the web page i was using to generate and display the colors
actually looked reasonable as i added more colors and settings. This culminated in a journey of
learning color theory, web design, and the limitations of randomness.

## title drop

The first thing i had to figure out was: What do you even mean when you talk about "contrast ratio"?
The Random Color Contrasts bot mentioned the [WCAG definition of contrast ratio][wcag-cr], which
gives a few mathematical formulas to go from "two 24-bit RGB colors" to "a floating-point number
between 1 and 21". However, it also provides a theoretical background for these formulas: the [sRGB
color space][srgb] and the concept of [relative luminance][].

[wcag-cr]: https://www.w3.org/TR/WCAG20/#contrast-ratiodef
[srgb]: https://en.wikipedia.org/wiki/SRGB
[relative luminance]: https://en.wikipedia.org/wiki/Relative_luminance

Basically, even though computers represent color as individual red, green, and blue components, the
average human eye doesn't register them as equally bright. We tend to see green light the brightest,
red light a medium amount, and blue light a slight amount. If you want to see how bright two colors
are relative to each other, it's not enough to say "this one has higher RGB values than the other
one", you need to figure out how the human eye would see the combined color and compare those
calculations. This way, you get a better picture of what colors can go together without having to
visually compare them. The WCAG uses this calculation to provide accessibility recommendations about
what colors work best on a web page to be able to best see and understand text.

This works fine for plain text on a single background color, but in mkcolor i was wanting to find
out more than just "will this text be understandable on this background". In addition, as i started
adding colors for things like the line number gutter or the status bar, i wanted to be able to find
colors that were variations of the chosen background color, so that they [wouldn't stand out too
much][random-ui]. I figured that randomly tweaking the RGB values would likely yield a color that
was different in a strange way, based on what i'd read in the past about color spaces.

[random-ui]: https://twitter.com/QuietMisdreavus/status/1304207661077008384

At first, a contrast-ratio-based calculation of "are these text colors visibly different from each
other" worked out; if i require a minimum contrast ratio for any two text colors to have, then
*most* of the time, they'll look different enough to be fine. It wasn't perfect, but it worked when
i only had a single mechanism of comparing two colors on hand. Eventually, though, i knew i wanted
to find out more about a color than its relative luminance.

Enter the [HSV color space][hsv]. If you've ever seen a [color picker] that had a way to select a
base color, how bright it is, and how "grayish" it is, then you've seen an example of the HSV color
space. Instead of encoding the brightness of individual sub-pixels like RGB, HSV instead uses the
"hue" (the "base color", frequently encoded as degrees around a color wheel, starting with red at
zero degrees, then transitioning through green and blue before returning to red), "saturation" (the
"colorfulness" of a color, or how close to grayscale it is), and "value" (also called "brightness",
or how close to black a color is).

[hsv]: https://en.wikipedia.org/wiki/HSL_and_HSV
[color picker]: https://duckduckgo.com/?t=ffab&q=color+picker&ia=answer

Figuring out the HSV values for a given color allowed me to figure out whether a given color was
"reddish" or "greenish". This was great for creating colors for things like spelling errors or diff
markers. However, while this would be fine for tweaking colors, i knew there were better options for
color spaces that "approximated human vision".

## different spaces for different phases

At this point i want to take a step back and talk about what a "color space" is. As i've mentioned,
there are many different ways to represent a color: as different components of light being combined
(RGB), as different ink colors being combined (CMYK), as a base color and a couple modifiers
(HSV/HSL). However, the next thing i added to mkcolor doesn't map itself neatly to its components
like the others.

In addition to thinking of a color representation as "ways to create a color from orthogonal
components", there are also representations that are more like "abstract coordinate systems that can
be traversed mechanically and produce meaningful results". Sometimes this property is used to allow
for errors in transmission to be meaningfully interpreted, like in the [YUV][] space used for color
television. (I used this property of the YUV color space many years ago when implementing a [color
extraction algorithm for images][PluginColorExtract], that emulated an interface used in iTunes back
then.)

[YUV]: https://en.wikipedia.org/wiki/YUV
[PluginColorExtract]: https://github.com/QuietMisdreavus/PluginColorExtract

In a similar vein is the [CIELAB color space][]. The main idea behind the CIELAB space is that
regardless of what color you start with, a similar numerical change in the components (e.g.
increasing one by 5) results in a similar amount of perceived difference in the resulting color.
This makes CIELAB perfect for taking a starting color, tweaking it with some kind of numerical
change, and getting something that looks different enough, regardless of what color you start with.
This allowed me to create [visually consistent UI colors][tweak-ui], even when working with
randomness.

[CIELAB color space]: https://en.wikipedia.org/wiki/CIELAB_color_space
[tweak-ui]: https://twitter.com/QuietMisdreavus/status/1306973558413889536

## randomness has clumps

Another issue i grappled with early on is the fussiness of random numbers. Without any constraints
of what colors to pick for what purpose, it's easy to find yourself in a situation where everything
is unreadable. This ties into the earlier discussion about contrast ratio - if i know what
background i'm going to be displaying some text over, i can keep pulling random numbers to generate
as many colors as i need until i find a combination that satisfies a certain contrast ratio.

...However, as i started adding more and more colors to the list i wanted to generate, i started
running into a problem. As i was putting more constraints on the system (any text/background
combination needs a certain contrast-ratio minimum, most text colors need to be a minimum contrast
ratio and CIELAB distance from each other, most "highlight" colors need to be a minimum contrast
ratio and CIELAB distance from the main background, etc), i started running into situations where it
was getting harder and harder to find even a single color scheme that worked. My code would run in
circles forever, endlessly generating random colors until Firefox eventually asked me to stop it.

The first solution i had was to place a limit on how many colors to try when creating a new text or
highlight color. If it was too difficult to add a new color to a set without clashing with an
existing one, i wanted it to start all the way over. This might create more net work in the end, but
i found it easier than trying to figure out how to write a check for "this color would create an
impossible condition".

On the other hand, this didn't stop me from trying. After a while, i found myself in a situation
where forcing a contrast ratio of at least 7 (what i called "high contrast" or what the WCAG would
call "AAA") meant that it was harder and harder to find *any* combination of colors that worked out.
After poking at it for a while, i started to realize something about the definition of contrast
ratio: Since it was ultimately a ratio of numbers between 0.05 and 1.05, there had to be some colors
where it was vanishingly rare, if not impossible, to find a corresponding color over a certain
ratio. Even though the ratio between black and white is 21, the most you'll find for any given color
may only be something like 10, shared between only a handful of similar colors.

Eventually i wanted to do some ahead-of-time computing and see whether i could find something in
common between the main background colors of the schemes that were failing. I stopped short of fully
modeling them and performing things like convex-hull calculations or looking for outliers, but after
a while i started to have a hunch: Could there be a segment of colors where it's so unlikely to find
enough distinct colors with a good contrast ratio that it's not worth even trying it? Looking at the
HSV and CIELAB values of a lot of the background colors that failed started to point at a possible
optimization.

As a point of curiosity, i started trying to figure out a sort of "CR midpoint", a color that is
equally contrasting against black and white. Eventually i found the color `#CF0DCC`, a sort of dim
magenta which can only ever have a contrast ratio of up to 4.58 against any other RGB color. As i
started logging the contrast ratios of the colors that failed against this color, i began to notice
that many of them were fairly close to it in terms of relative luminance. If it was going to be hard
to find a certain contrast ratio from a specific color, it wasn't worth trying to create a color
scheme around it.

Unfortunately, since there are some settings in mkcolor that create some overly restrictive
constraints, it's still possible to force it to generate and throw away a lot of color schemes
before it finds one that works. I wanted to keep those settings in there, so i had to tweak some of
my thresholds over time to make sure that adding more colors or more constraints wouldn't make it
impossible to create a color scheme. It may take a while, but it should still create something
usable!

## the philosophy of colors in text editing

All this color theory and experimentation is all well and good, but there's another major question
that i've skipped over: How do you decide *which* things to color? I'm one of those that enjoys a
colorful color scheme, but it's one thing to appreciate looking at something and another to know how
to create it.

When i started, i went with the obvious suspects: one color for main text, another color for
comments. Stopping here would be a completely valid way to orient your editor! Some people
appreciate this brand of minimalism for not creating a distracting menagerie of color every time you
write some code. I knew i wanted to make something more than this, though, so i had to figure out
what to color next.

Since i use Vim as my main editor, i started taking a look at what it was doing. After a while of
looking, i found out that there's a way to look up the "highlighting group" used for a specific
thing in a file. From here, i found a basic taxonomy: Literals (numbers, text, booleans, etc),
Keywords, Identifiers, and Types. A couple extra ones snuck in because of the Rust sample i was
using: Preprocessor commands, used there for attributes, and "Special tokens", used for doc
comments. Each language's syntax highlighting definition uses these groups differently, but if you
have these basics covered, then you have a relatively complete color scheme.

...That is, if all you were interested in was coloring the files themselves, and not any extra
editor things. If you look at mkcolor today, you can see that it generates *way* more colors than
just those. A long-shot goal i had when starting mkcolor was that it should generate a complete
color scheme file for Vim. But this brings me to an even bigger version of the question from before:
What things are even available to color?

I've spoken on Twitter before about the double-edged sword of Vim's configuration being a dedicated
scripting language. That extends to syntax highlighting definitions and color schemes, too! While
you can look at the things a color scheme defines, some of the more popular ones tend to wrap up the
definitions with other kinds of code structures, and also, it can be hard to connect the name of a
highlight group to what it's actually used for.

For that, i owe the completion of mkcolor to a project called [vim.ink][], which is a beautiful Vim
color scheme designer that allows you to visualize the colors as you set them, and interact with the
samples to change a highlight group on the fly. Vim.ink offers a wonderfully exhaustive look at the
myriad things that Vim can display and colorize. It felt like a lot of things to commit to, but it
also meant that i had a finite list of things to generate, after which i could be reasonably
comfortable that i would have covered the majority of use cases.

[vim.ink]: http://vim-ink.github.io/

As i mentioned earlier, when i started getting the "UI frame" in place, i started running into [a
problem][random-ui]: There was no guarantee the colors that were being picked actually looked good
together! I tried a couple things at first, but ultimately implemented the "tweak" behavior with the
CIELAB color space. Since most UI colors don't need to overlap with any of the body text, they can
mainly just be concerned with their own text counterpart, and not add additional undue constraints
to the rest of the colors being generated.

## wrapup

mkcolor is one of those things that felt like a big project when i started, feels like it was a big
project now, but was ultimately built up a little bit at a time. As things were added, more features
were inspired or made necessary by the complex and chaotic system that was being built.  Since most
of the system was built on random numbers, a lot of behavior could be changed by adding another item
to a list, or by changing a threshold by a minor arbitrary amount. It made it fun, in a way; a small
tweak could create more interesting outcomes, or block or unblock another process.

At this point, mkcolor feels like a sort of interactive art project. You dial in a few settings,
push a button, and (hopefully!) something neat-looking comes out. The fact that i was able to bolt
on a functional Vim color-scheme exporter onto it makes it kind of funny, to me. It's possible to
convert this logic to make it run natively within Vim! Imagine having these kinds of settings inside
your editor, and having a command or a trigger to completely recolor your environment! For the
moment, i want to leave that as an exercise for someone else. I personally recommend implementing it
in something other then Vim script, though; it was starting to run against performance walls in
JavaScript, which caused it to lag out my browser when i was running some curious calculation or i
hit a situation where it kept throwing away colors because nothing worked. Having something that can
run asynchronously to the main Vim execution engine seems like a better idea, in that case.
