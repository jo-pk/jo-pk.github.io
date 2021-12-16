---
layout: post
title: git is a multiverse
description:
  a git tutorial, by way of describing how repositories have at least six spatial dimensions
categories: code
---

A while back i had [a shower thought] to the effect of "git repos are sixth-dimensional objects". I
expounded on it a little on Twitter, but i'd like to put proper paragraphs to it (and also give
myself an excuse to write a blog post about something other than Rust for a change).

[a shower thought]: https://twitter.com/QuietMisdreavus/status/909756698042056710

(Spoiler alert: This post is basically just me applying the thought process of [Imagining The Tenth
Dimension][10thdim] to source code, and sneaking in a tutorial of Git concepts along the way. It may
still be helpful to watch that video, because i'm going to borrow some of its ideas.)

[10thdim]: https://www.youtube.com/watch?v=zqeqW3g8N2Q

## some groundwork

This entire exercise is going to ignore the fact that technically, the entirely of GitHub can be
represented as a single, enormous byte stream. If it helps, we can imagine an idealized filesystem
driver that virtually extends a file buffer into infinity, reading nothing but null bytes if you
read off either end of a file or directory list.

Alternately, my reasoning is that "raw concatenated byte stream" is not how anyone but the operating
system interacts with a file system. When i think of my files, i see the organized tree (graph if
you include links) of files and folders, each "layer" containing a listing of files, and only once
you get into an individual file do you see the byte stream itself. The fact that a hard drive is
just a raw stream of bits is an implementation detail, like how we subvert a Turing machine by
limiting the amount of memory it can use.

## we start with a byte

Anyway, on to the metaphor. Like in the "Tenth Dimension" framework, it helps to organize ourselves
by establishing what we're indexing with all these dimensions. In the world of computers we've
settled on bits as our atomic unit, so that seems like a fine place to start. Actually, for the
purposes of this post i'll cheat a little and graduate to a whole byte instead, because no computer
commonly used today will let you extract a single bit from some data without some extra work. Since
the use of bytes is so widespread it shouldn't make much difference.

So we have in our hands an individual byte, which we can treat as our computational "point". As the
first spatial dimension introduces a second "point" and draws a line that contains them, so too can
we create a "line of bytes": a byte stream, or (for our purposes) an individual file. With our
"idealized filesystem driver" from earlier, a file is a line connecting the first and last byte of
the file, and extending off to infinity in either direction.

A helpful element of this version of the exercise is that i can provide a concrete example of each
dimension in action. I'll pull an example from one of my own projects, and expand it as we go. Let's
call out, say, the first `!` character in [this file][example]. I'll call out its "coordinate" over
time.

[example]: https://github.com/QuietMisdreavus/twitter-rs/blob/v0.8.x/src/lib.rs#L1

*Dimension 1: Byte `0x2`*

Moving on, the next dimension up is a matter of taking multiple of these "byte streams" and indexing
them side by side. Fortunately, we have a handy name for that already: a "directory". To be precise,
it's a "flat file listing", since we can only index files here. The notion of "subdirectories" is
not possible here. (I'm sure simplified filesystems exist that don't have any notion of
directories.)

*Dimension 2: File `lib.rs`, byte `0x2`*

As i alluded to, the "third dimension of bytes" is a full directory structure, a network of flat
file listings that can link to other flat file listings. This is what you see when you use a file
manager, navigating up and down a directory hierarchy to get to the file you need. A subdirectory
(and the notion of a "parent directory") is a means to navigate from one file listing to another,
trading one second-dimensional structure for another.

This idea of a "collection of lower-dimensional objects" is a common theme i build upon later. By
assembling a set of points you get a line; by assembling a set of lines you get a plane; by
assembling a set of planes you get a volume. In the same way, by assembling a set of bytes you get a
file; by assembling a set of files you get a file listing; by assembling a set of file listings you
get a filesystem.

*Dimension 3: Directory `src/`, file `lib.rs`, byte `0x2`*

## building up

Now i finally get to introduce Git to the picture. One of the fundamental units of Git is the
"commit", which encodes the state of the filesystem at a given moment. Commits also include a
comment, author and timestamp (and other details i'll bring in farther down), but the important
thing here is that a single Git commit is effectively a snapshot of the directory structure and the
files within.

The "Tenth Dimension" video speaks of the concept of a spatial dimension being made up of "slices"
of the next dimension down. Here that still holds true: a file can be sliced into bytes, a file
listing can be sliced into files, a directory structure can be sliced into file listings. So, too,
does Git treat entire filesystems as slices of a larger entity. The basic use of a Git repository is
to encode the history of a directory, building commits on top of each other as files and folders
change around. In effect, the "fourth dimension of bytes" is a commit history, treating the given
state of a filesystem as a "point" and encoding it through time.

*Dimension 4: Commit `48bdf12`, directory `src/`, file `lib.rs`, byte `0x2`*

Working up from here, a powerful feature of Git is the use of "branches", which allow you to create
parallel commit histories branching off from a single commit. Any given Git repository may have
several branches, each with their own commit histories. These are useful to experiment with a
feature without writing over a "known good" configuration, or to allow multiple people to work on
the same repository in parallel without writing over each other's work. This listing of branches, of
parallel commit histories, is itself a new dimension over the plain linear history of before.

*Dimension 5: Branch `v0.8.x`, commit `48bdf12`, directory `src/`, file `lib.rs`, byte `0x2`*

To move on, i need to dispense with my "unique coordinate" rhetoric because the next step isn't as
clear-cut as "a listing of the previous thing", and requires that i call out to the "Tenth
Dimension" video again. In it, the author calls out the idea that the spatial dimensions can be
grouped in threes, based on what you can do with them. In the lower spatial dimensions, the first
dimension is a line with no sense of divergence, and the second dimension is the ability to break
off and create other lines that cross the first one.

However, rather than thinking of the third dimension as "volume", you can instead call it out as a
space you can "fold the second dimension through" to bring disparate parts of it together. He calls
out the idea of an ant walking across a newspaper. From the "paper's perspective", the ant is moving
continuously across the plane. But if an outside actor were to bend the paper around so that two
distant parts were close enough for the ant to jump across, it has now spontaneously "jumped" from
one point to another, without travelling through the distance in between.

We can call out something similar in Git. The earlier concept of branches has a severe limitation:
Now you can have multiple active versions of the filesystem. At some point one of them needs to take
over as the "key" version, and potentially incorporate changes from the alternate branches. You
could try doing this by hand, writing out the change on the new branch and discarding the "alternate
history", but Git can also do this for you and preserve both histories. This is called "merging",
and when you do it you create a special commit that links back to both branches.

Within our conceptual framework, something remarkable happens here. You've started with a certain
point in time and split its history, generating parallel universes where different things happen to
the structure. Then, you bring the histories together, and rather than a waveform collapsing to one
thing or the other, it preserves *both* universes. The resulting structure shows the effects of
*both* sets of actions. For bringing the branches together, we require the space to move them
through, and that is where we draw our "sixth dimension of bytes" from.

*Dimension 6: Branch `v0.8.x`, commit `48bdf12` (co-parent of `627a440` merging branches `v0.8.x`
and `master`), directory `src/`, file `lib.rs`, byte `0x2`*

## putting "multi" in the verse

All this time, i've discussed this framework in terms of a single instance of a single repository.
It's still possible to move up from here, but i want to take a moment to discuss what we've built up
so far. In essence, a Git repository is a means of encoding not only the history of a filesystem,
but also many alternate histories, complete with the means to blend them together. This is the
source of Git's power, and the reason people who understand it (and other distributed version
control systems in the same vein) can get so excited about it.

Another important facet of the community built up around Git is the concept of a "repository fork",
where someone makes a copy of an existing repository so they can work on it independently. Sometimes
this is in the hopes of being able to "bridge between the multiverses" and get their work included
in the original repository, sometimes this is to create their own "personal parallel universe" that
has no aim of being bridged across.

Regardless, these forks are still products of the same "source universe" in the end; if you scroll
all the way back, they all started with the same `git init`. The "space to merge through" concept
still applies to forks, even though they manifest as separate instances. (That's the thing about
higher-dimensonal objects - they have the extra space to move around and manifest in different
places in our lowly three-dimensional world and one-dimensional hard drives!) To add a new dimension
here, we need the ability to access truly different universes, that started with different initial
conditions!

It's hopefully not much of a stretch to think of separate repositories, dedicated to distinct
sets of files and beginning with distinct origins, as a sixth-dimensional slice of a
seventh-dimensional structure. This means that not only are Git repositories sixth-dimensional
objects, but GitHub itself is a seventh-dimensional listing of sixth-dimensional objects!

*Dimension 7: Repository `QuietMisdreavus/twitter-rs`, branch `v0.8.x`, commit `48bdf12` (co-parent
of `627a440` merging branches `v0.8.x` and `master`), directory `src/`, file `lib.rs`, byte `0x2`*

Moving up from here, we can use other source code hosting providers (Bitbucket, GitLab, GNU
Savannah, etc) as an eighth-dimensional listing of seventh-dimensional objects.

*Dimension 8: Host `GitHub`, repository `QuietMisdreavus/twitter-rs`, branch `v0.8.x`, commit
`48bdf12` (co-parent of `627a440` merging branches `v0.8.x` and `master`), directory `src/`, file
`lib.rs`, byte `0x2`*

The "ninth dimension of bytes" could be considered a bit of a stretch, but it's still worth pointing
out that Git is not the only software offering this kind of "branching commit history with merging
potential" functionality. Simply stating that my project used Git restricted it along a "dimension
of bytes". It could have easily used Mercurial, or Subversion, or Bazaar, or some other kind of
version control system.

To elaborate, let's go all the way back down to our spatial dimensions, and specifically that "and
walking on a newspaper" example. The ant moving around can only move while there is a continuous
path from where it started and where it's going. If there's a break in the paper, it can't just "hop
across" the same way you could theoretically climb over a wall in the third dimension. (Assume that
the break is wide enough that it couldn't climb over. Again, we're speaking from the "paper's
perspective" here.) So it is that choosing a version control system restricts the locations
available in the rest of the dimensions. (Imagine trying to host a project using Team Foundation
Server on GitHub!)

*Dimension 9: VCS `Git`, host `GitHub`, repository `QuietMisdreavus/twitter-rs`, branch `v0.8.x`,
commit `48bdf12` (co-parent of `627a440` merging branches `v0.8.x` and `master`), directory `src/`,
file `lib.rs`, byte `0x2`*

## points of articulation

If you spend enough time in an online programming community, it's not hard to get the impression
that Git is hard to learn. I don't want to dispute that; on the contrary, pointing out all these
"dimensions of bytes" is also a way to highlight its complexity! I don't really have a good way to
wrap your head around this complexity, other than providing this kind of abstract framework that
sorta-kinda maps if you squint at it. I am amused, though, at the thought that whenever you run `git
init`, you're creating a little pocket universe on your hard drive.
