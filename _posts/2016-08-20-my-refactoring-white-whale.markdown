---
layout: post
title: my refactoring white whale
description: a discussion of refactoring the "egg-mode" library through a series of compiler errors
categories: code
---

I've been writing [a Twitter library for Rust][egg-mode]
during down times at work for the last few weeks.
I've spent a non-trivial amount of effort making sure that actually using the library is seamless,
and that it doesn't introduce implementation details into client code when it doesn't need to.
But I've also written up a lot of internal infrastructure code
so that that promise also holds up for my own work on the libary itself.

[egg-mode]: https://github.com/QuietMisdreavus/twitter-rs

One pattern I noticed while implementing a few of the user lookup methods
was that you can pass either a "screen name"
(the name you @mention someone with)
or a numeric ID to identify a user.
To wrap around this detail,
I made a helper enum to abstract around this:

```rust
pub enum UserID<'a> {
    ID(i64),
    ScreenName(&'a str),
}

//...followed by From impls for i64 and &str...
```

This is really nice for accepting either one when one is needed.
Just accept `T: Into<UserID<'a>>` and you're all set:

```rust
///Lookup user information for a single user.
///(elided from user::show in the actual code)
pub fn show<'a, T: Into<UserID<'a>>>(acct: T)
    -> Result<Response<TwitterUser>, Error> {...}
```

However, this gets a little thorny when you want to accept a collection of these IDs.
Trying to accept `&[T] where T: Into<UserID<'a>>` becomes a little frustrating
when it comes time to actually use the elements:

```rust
fn lookup<'a, T: Into<UserID<'a>>>(list: &[T]) {
    for acct in list {
        //error: the trait bound `UserID<'_>: std::convert::From<&T>` is not satisfied
        match acct.into() {
            UserID::ID(id) => println!("ID {}", id),
            UserID::ScreenName(name) => println!("Name: {}", name),
        }
    }
}
```

Okay, let's just dereference the IDs before calling `into()`:

```rust
fn lookup<'a, T: Into<UserID<'a>>>(list: &[T]) {
    for acct in list {
        //error: cannot move out of borrowed content
        match (*acct).into() { //<-- note (*acct)
            UserID::ID(id) => println!("ID {}", id),
            UserID::ScreenName(name) => println!("Name: {}", name),
        }
    }
}
```

...right, we borrowed the slice, ripping the values out of it wouldn't be very sporting.
Well, the first error message also suggested changing up my trait bounds,
so let's try that.

```rust
//(skipping a couple iterations here, since the compiler literally gave the answers)

//Additions: where clause, `T: 'a`, and `list` now being `&'a [T]`
fn lookup<'a, T: 'a>(list: &'a [T])
    where &'a T: Into<UserID<'a>>
{
    for acct in list {
        match acct.into() {
            UserID::ID(id) => println!("ID {}", id),
            UserID::ScreenName(name) => println!("Name: {}", name),
        }
    }
}
```

Now it compiles, but there's also now a lot of noise around the type signature.
I'm willing to put up with that,
with some additional notes in documentation about how it can be used.
But we're still not out of the woods yet.
With how UserID is implemented so far,
one expected use case falls flat.
I expect this to occasionally get called with user input,
or with owned Strings otherwise pulled from whatever source.
But when we try to do just that,
we get not one but *three* scary-looking errors:

```rust
let mut list: Vec<String> = Vec::new();

list.push("QuietMisdreavus".into());
list.push("rustlang".into());

//error: the trait bound `str: std::marker::Sized` is not satisfied
//error: mismatched types
//  note: expected type `&[str]`
//  note:    found type `&std::vec::Vec<std::string::String>`
//error: the trait bound `str: std::marker::Sized` is not satisfied
//  note: slice and array elements must have `Sized` type
lookup(&list);
```

Wait, what?
I'm not trying to hand you raw `str`s!
You should just be able to take references to them anyway!

What it looks like is that it sees the `From<&str>` from way up top,
and the `&T: Into<UserID>` on the function itself,
and decides that since I'm calling `into()` on `&T`,
I must be trying to pass a `&[str]`!
But when it tries to deref-coerce all the way down,
it hits the third error,
saying that slices need to have `Sized` elements.

There's an easy way out,
but it's not quite clearly marked here.
Since I control the implementation of `UserID`,
why don't I just toss `From<&String>` on it?
This is one of those cases where automatic deref-coercion can't solve everything.
At least the implementation is fairly trivial.

```rust
impl<'a> From<&'a String> for UserID<'a> {
    fn from(name: &'a String) -> UserID<'a> {
        UserID::ScreenName(name.as_str())
    }
}
```

Now with the same use code from earlier, we're golden!
But what if we have a slice of IDs?
Surely that won't be a problem, right?

```rust
let mut list: Vec<i64> = Vec::new();

list.push(1234);
list.push(2345);

//error[E0277]: the trait bound `UserID<'_>: std::convert::From<&i64>` is not satisfied
lookup(&list);
```

...oh.
At least that's fairly trivial, too.
I have one final use case, though this one is slightly more contrived.
In my example code, I wanted to try having a Vec of straight UserID's,
so I could see how Twitter reacted to seeing both kinds of identifiers.
How does that hold up?

```rust
let mut list: Vec<UserID> = Vec::new();

list.push(1234.into());
list.push("rustlang".into());

//error[E0277]: the trait bound `UserID<'_>: std::convert::From<&UserID<'_>>` is not satisfied
lookup(&list);
```

This one's a bit odd, but it's also fairly simple to navigate through.
If I `#[derive(Clone)]` on UserID,
then make a `From<&UserID>` impl that calls it,
then I get a working trait bound for that version, too.

So that's where I'm at.
After three extra From impl's and some extra generics appeasement,
I'm finally able to combine the [three user lookup functions][lookup] into one.
This one stumped me for a while,
until I started writing this post and just started tinkering with it on the [Playground][].

[lookup]: https://github.com/QuietMisdreavus/twitter-rs/blob/da0a320ea41e9a223cf644fbd49a72e9ddaf8a20/src/user/mod.rs#L66-L118
[Playground]: https://play.rust-lang.org/?gist=e862159f3dbaf2fa295a28c1dcfb4868&version=stable&backtrace=0

I've been properly writing Rust for a few months now,
and it's been a fun ride just hanging out on IRC and tinkering on this library.
I honestly started writing this post with the intent of laying out the initial issue of combining those lookup functions,
but then after working through it I came upon the solutions in here.
I'm not sure whether the extra generics noise is completely worth it,
but I figure if I fill out the documentation with plenty of sample code,
then it should work itself out.

*Update*: [The specific refactor][refactor] is live! ([Plus with docs!][docs])

[refactor]: https://github.com/QuietMisdreavus/twitter-rs/commit/98b70429fac382b8f40921e7178261b6f8f1fff1
[docs]: https://github.com/QuietMisdreavus/twitter-rs/commit/c757221a22cf1e8cd5f75ba71b5ca91b98a07d61
