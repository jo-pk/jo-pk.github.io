# quiet misdreavus miniblog

These are the source files used to build my blog at <https://quietmisdreavus.net>. The site is a
[Jekyll] blog, though it's not hosted on GitHub Pages. Instead, i have the site hosted at
[NearlyFreeSpeech], since i'm comfortable with that host.

[Jekyll]: https://jekyllrb.com
[NearlyFreeSpeech]: https://www.nearlyfreespeech.net/

The blog's theme is [Vida], which was a lightweight theme i found when i was putting my site
together the first time. I've made a couple modifications to it since then, but the primary setup is
still roughly the same.

[Vida]: https://github.com/syaning/vida

To build this blog, install Jekyll, then run `jekyll build` or whatever other stock Jekyll command
you would like to use. For my own convenience, i have a command in my [`bashrc`] that combines this
with uploading it to the web host:

[`bashrc`]: https://github.com/QuietMisdreavus/dotfiles

```bash
blog_sync() {
    if [ $# -ne 1 ] ; then
        echo "usage: blog_sync host"
        echo ""
        echo "builds a jekyll blog in the current directory then pushes it to the given host as if it were a NFSN site"
    else
        jekyll build && rsync -avz _site/ $1:/home/public
    fi
}
```

With this in place, running `blog_sync some-site` (where `some-site` is set in my SSH config) will
both build the site's files and upload it to the server.

## theme modifications

### RSS feeds

The theme itself didn't come with RSS feeds, and i thought it would be nice to include some. The
various `feed.xml` files in this repo came from a sample i found on the internet somewhere. I
modified it into the category-specific feeds alongside it. I didn't want to dynamically create the
category-specific feeds, since the process seemed too cumbersome to bother with, and i only have the
two for now.

### OpenGraph data for posts

I [updated the default layout][og-info] file to add OpenGraph information for posts, so that they
would create a nice info block when i shared the links with my friends on Discord. (I also [tweaked
the layout in a second commit][og-info2] which changed the display a little.)

[og-info]: https://github.com/QuietMisdreavus/quietmisdreavus.net/commit/8a59f087289fe75c83ace8c76184819fee9e6979
[og-info2]: https://github.com/QuietMisdreavus/quietmisdreavus.net/commit/c00d56d399c5e3a0dfa523b15934e8097f92f601

### post descriptions

Since the previous change also gave me the slot to use post descriptions, i thought it would be nice
to [display those directly on the blog index page][index-desc]. If i was going to write one-liner
summaries of my blog posts, they might as well be useful to people beyond Discord links.

[index-desc]: https://github.com/QuietMisdreavus/quietmisdreavus.net/commit/552dc41e0718f66332fb960123c1bc0da2133e99

### blog-index-specific page title

I got weirded out by the fact that the home page was called "main | quiet misdreavus miniblog", so i
[modified the page layout][index-title] to remove the page's title for just the index page. Now it
just shows the site title, "quiet misdreavus miniblog".

[index-title]: https://github.com/QuietMisdreavus/quietmisdreavus.net/commit/3f437904b37df47f602cddab2fca2ab29a2d37f8
