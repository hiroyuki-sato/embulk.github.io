---
layout: posts
title: `embulk gem install` may fail in Embulk v0.9: incompatibility with RubyGems
date: 2023-07-28
last_modified_at: 2023-07-28
description: `embulk gem install` may start failing in Embulk v0.9 even if the target plugin Gem exists at rubygems.org. This article explains the issue, and how to resolve it.
author: "hiroyuki-sato"
---

## Overview

[RubyGems.org has removed some of its old APIs.](https://blog.rubygems.org/2023/02/22/dependency-api-deprecation.html) The `gem` command in JRuby 9.1.15.0, the old JRuby embedded in Embulk v0.9, used the old API. As a result, `embulk gem install` started to fail even if the specified Gem exists on RubyGems.org.

This article explains how to resolve/workaround this issue.

## Example of an error case

Look at the example output below, which is a message when trying to install `embulk-output-mysql` v0.10.2. It is [the latest release as of Jul 28, 2023](https://rubygems.org/gems/embulk-output-mysql/versions/0.10.2-java).

```
$ embulk gem install embulk-output-mysql -v 0.10.2

================================== [ NOTICE ] ==================================
 Embulk v0.11.0 will be released soon, planned for June 2023.

 This v0.11.0 will contain a lot of incompatible changes from v0.9.
 Many plugins are expected to stop working with v0.11.0.

 Try v0.10.48 or later, Release Candidate for v0.11, before v0.11.0 is official.

 See: https://www.embulk.org/articles/2023/04/13/embulk-v0.11-is-coming-soon.html
================================================================================

2023-07-26 21:41:33.966 +0900: Embulk v0.9.25

Gem plugin path is: /Users/user/.embulk/lib/gems

io/console on JRuby shells out to stty for most operations
ERROR:  Could not find a valid gem 'embulk-output-mysql' (= 0.10.2) in any repository
ERROR:  Possible alternatives: embulk-output-mysql
```
In the example above, you can find that Embulk 0.9.25 raised an error `Could not find a valid gem` even though the Gem exists in RubyGems.org.

This is because Embulk v0.9 used the older `gem` command, which uses the deprecated API.

## Solutions / Workarounds

### Option A: Use Embulk v0.11

Embulk's new stable versions, v0.11, has been available since Jun 13, 2023. It allows users to use the latest `gem` command with the newer JRuby engine.

The Embulk maintainers recommend this as the first option.

### Option B: Use Bundler

If you cannot start using Embulk v0.11 for some reasons, the other option is using Bundler with Embulk v0.9, instead of the `gem` command. Bundler has been used with Embulk v0.9 or earlier for a long time to manage Embulk plugin installations.

To start using Bundler with Embulk v0.9, you'll have to create `Gemfile` like the following.

```
source 'https://rubygems.org'

gem "embulk-output-mysql", "= 0.10.2"
```
Then run `embulk bundle` in the same directory once `Gemfile` is ready.

## See also

* [RubyGems.org Dependency API Deprecation](https://blog.rubygems.org/2023/02/22/dependency-api-deprecation.html)
* [Dependency API Deprecation Delayed](https://blog.rubygems.org/2023/04/07/dependency-api-deprecation-delayed.html)
* (Japanese) [embulk gem (v0.9.x)でプラグインが見つからない、あるはずなのに..そんな時は](https://zenn.dev/hiroysato/articles/16f84886524a3d)
