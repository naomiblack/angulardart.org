# Angulardart.org

To build the site locally, do the following:

1. If `which bundle` returns something, you have the prerequisites. Otherwise:
  1. Download rubygems [PENDING: say from where]
  2. Install bundler [PENDING: say from where]
2. Run `bundle install` from within the directory that contains the `Gemfile`
  * If you have issues installing posix-spawn
    (messages like "posix-spawn.c:6:19: error: errno.h: No such file or directory")
    do the following and then run `bundle install` again:

    `xcode-select --install`
3. Run `cd site`.
4. Run `jekyll serve --watch`. This runs the dev server.
   If you get a horrible set of messages, **make sure you're in site/.**
5. View the site at `localhost:4000`.
