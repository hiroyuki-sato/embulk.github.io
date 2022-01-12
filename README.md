www.embulk.org
===============

Maintained with [GitHub Pages and Jekyll](https://docs.github.com/ja/pages/setting-up-a-github-pages-site-with-jekyll/about-github-pages-and-jekyll).

Initial local setup
--------------------

```
$ bundle config set path 'vendor/bundle'
$ bundle install
```

Running Jekyll locally
-----------------------

```
$ bundle exec jekyll serve
```

Updating gems
--------------

Update `Gemfile` according to [GitHub Pages dependency versions](https://pages.github.com/versions/), then

```
$ bundle update
$ bundle install
```
