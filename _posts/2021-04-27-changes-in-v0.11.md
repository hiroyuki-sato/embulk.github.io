---
layout: posts
title: "For Embulk users: What will change in v0.11 and v1.0?"
date: 2021-04-27 18:33:55 +0900
author: "dmikurube"
---

Hi Embulk users,

An year has passed since [our last announcement about Embulk v0.10](https://www.embulk.org/articles/2020/03/13/embulk-v0.10.html). We are approaching Embulk v0.11 now!

Embulk v0.11, and following v1.0, will have big changes from the last stable v0.9. This article summarizes the big changes in v0.11.

### Embulk System Properties, and directories

#### Embulk System Properties

Embulk v0.11 has "Embulk System Properties" instead of "System Config" used for configurating Embulk system-wide. Embulk System Properties is based on [Java Properties](https://docs.oracle.com/javase/8/docs/api/java/util/Properties.html), not on Embulk's `ConfigSource` like the old System Config.

Java Properties actually have weaker expressiveness than `ConfigSource`, which is equivalent to JSON. Then, why Java Properties?  The reasons are because Java Properties can be loaded without any additional help of a third-party library, and can be loaded in the very beginning of a Java process without any initialization.

Embulk's old System Config had not used the strong expressiveness of `ConfigSource`. The command line option `-X` for System Config was just to set a string value for a string key, which was equivalent to Properties. Some internal System Configs had used more complex structures like lists, but they could represented as strings. In fact, we did not need the expressiveness of `ConfigSource`.

From user's point of view, you won't see clear loss from the old System Config. On the other hand, you'll receive benefits from the power of Properties as described below.

#### Embulk home

Embulk v0.11 introduced a new concept of "Embulk home" directory, which is equivalent to Embulk v0.9's `~/.embulk/` that was hard-coded in the Embulk core.

Unless you have a special setting, or a special file, Embulk v0.11 works the same with v0.9 to consider `~/.embulk/` as Embulk home. The Embulk home directory is chosen in the following rule:

1. If an Embulk System Property `embulk_home` is set from the command line (`-X`), the Embulk home directory is set to that in the highest priority. It should be an absolute path, or a relative path from the working directory ([Java `user.dir`](https://docs.oracle.com/javase/tutorial/essential/environment/sysprop.html)).
2. If an environment variable `EMBULK_HOME` is set, the Embulk home directory is set to that in the second priority. It should be an absolute path.
3. If neither 1 nor 2 is set, Embulk v0.11 iterates up over parent directories from the working directory ([Java `user.dir`](https://docs.oracle.com/javase/tutorial/essential/environment/sysprop.html)) to find a directory that is named `.embulk/`, and has a regular file named `embulk.properties` just under the directory. If found, the Embulk home directory is set to that in the third priority.
    * If the working directory is under user's home directory ([Java `user.home`](https://docs.oracle.com/javase/tutorial/essential/environment/sysprop.html)), the iteration stops at user's home directory. Otherwise, the iteration goes up to the root directory.
4. If none of the above does not work, the Embulk home directory is set to the traditional predefined `~/.embulk`.

Then, `embulk.properties` (in [Java `.properties` file format](https://docs.oracle.com/javase/8/docs/api/java/util/Properties.html#load-java.io.Reader-)) just under the Embulk home directory is automatically loaded as Embulk System Properties.

The Embulk System Property `embulk_home` is finally reset forcibly to an absolute path of the directory identified.

#### `m2_repo`, `gem_home`, and `gem_path`

The next important directory settings are: where Embulk plugins are loaded from. They are configured in the Embulk System Properties `m2_repo`, `gem_home`, and `gem_path`, and from the Embulk home directory.

The Embulk System Properties `gem_home` and `gem_path` are used to configure JRuby by calling [`Gem.use_paths`](https://www.rubydoc.info/stdlib/rubygems/Gem.use_paths). Note that environment variables `GEM_HOME` and `GEM_PATH` are untouched. The Gem configurations could be reset unexpecteldy with the environment variables in case `Gem.clear_paths` is called somewhere.

1. If Embulk System Properties `m2_repo`, `gem_home`, or `gem_path` are set from the command line (`-X`), the Embulk System Propertis are set to those in the highest priority. The settings should be absolute paths, or relative paths from the working directory ([Java `user.dir`](https://docs.oracle.com/javase/tutorial/essential/environment/sysprop.html)). Then, these Embulk System Properties are reset to their absolute paths.
2. If Embulk System Property `m2_repo`, `gem_home`, or `gem_path` are set in the `embulk.properties` file, the Embulk System Properties are set to those in the second priority. The settings should be absolute paths, or relative paths from the Embulk home directory. Then, these Embulk System Properties are reset to their absolute paths.
3. If environment variables `M2_REPO`, `GEM_HOME`, or `GEM_PATH` are set, the corresponding Embulk System Properties are set to those in the third priority. The settings should be absolute paths.
4. If none of the above does not match, `m2_repo` is set to `${embulk_home}/lib/m2/repository`, `gem_home` is set to `${embulk_home}/lib/gems`, and `gem_path` is set to empty.

The Embulk System Properties `m2_repo`, `gem_home`, and `gem_path` are finally reset forcibly to absolute paths of the directories identified.

### JRuby

JRuby is no longer bundled with Embulk executable binaries. To use JRuby with Embulk (almost all the cases as of now), users will have to download a JRuby complete `jar` file from [JRuby Downloads](https://www.jruby.org/download), and set the Embulk System Property `jruby` to point the JRuby complete `.jar` with a `file:` URL, such as `-X jruby=file:///your/path/to/jruby-complete-9.1.15.0.jar`. (Only `file:` works as of now.)

It may seem annoying for you. But, a good point is that you can specify your preferred JRuby version by yourself. Although the Embulk core team has tested only with JRuby 9.1.15.0 yet, our important contributor, Hiroyuki, [had a quick check that JRuby 9.2.14.0 worked fine with some plugins](https://twitter.com/hiroysato/status/1339919262798864384).

On the other hand, we are going to encourage Maven-based pure-Java plugins throughout v0.11 and later versions so that we won't need JRuby in most cases.

#### `embulk.gem` and `msgpack.gem`

In addition to configuring `jruby`, users will have to install [`embulk.gem`](https://rubygems.org/gems/embulk) with the same versio
n as the Embulk core. Note that this `embulk.gem` (after v0.10) is totally different from `embulk.gem` of the ancient v0.8 age. The new ones contain only `.rb` files to bridge the Embulk core and Ruby gem-based plugins.

Users will have to install [`msgpack.gem`](https://rubygems.org/gems/msgpack) explicitly as well. Although the Embulk core team has tested only with `msgpack:1.1.0-java`, we expect that the later versions (Java variant) should work fine.

#### Bundler and Liquid

[Bundler](https://bundler.io/) and [Liquid](https://shopify.github.io/liquid/) are no longer bundled with Embulk, too. Users who want to use Bundler and/or Liquid will have to install them by themselves.

Alhtough the Embulk core has tested only with Bundler 1.16.0 and Liquid 4.0.0, later versions may work. Your contributions are welcome to check if they work.

### Examples of Embulk System Properties

If you want to use Embulk v0.11 almost in the same manner with v0.9, you will just have to download [JRuby 9.1.15.0](https://www.jruby.org/files/downloads/9.1.15.0/index.html), and create `~/.embulk/embulk.properties` like below:

```
jruby=file:///your/path/to/jruby-complete-9.1.15.0.jar
```

Or, you can use different sets of Embulk plugin installation per directory like below. If you run Embulk under `set1/` (or add a command line option `-X embulk_home=.../embulk_working/set1/`), plugins installed in `set1/.embulk` are used.

```
+ embulk_working/
  + set1/
    + .embulk/
      + embulk.properties
  + set2/
    + .embulk/
      + embulk.properties
  + set3/
    + .embulk/
      + embulk.properties
```

We believe that Embulk System Properties and Embulk home directory would provide a powerful flexibility to you. Give it a try to find good a configuration!

### Plugin compatibility

As announced last year, compatibility of Embulk plugins would be seriously lost in v0.11. Many legacy plugins would not work with Embulk v0.11 without some catch-up work. The Embulk core team will maintain plugins under [https://github.com/embulk](https://github.com/embulk) soon.

For other plugins developed out of [https://github.com/embulk](https://github.com/embulk), please try them out, and tell their maintainers about this article: [For Embulk plugin developers: Get ready for v0.11 and v1.0!](https://dev.embulk.org/topics/get-ready-for-v0.11-and-v1.0.html)

### Pre-release

We have uploaded a pre-release version, [v0.11.0-ALPHA.1](https://github.com/embulk/embulk/releases/tag/v0.11.0-ALPHA.1), in GitHub Releases. Remember that you have to install the corresponding `embulk-0.11.0.alpha.1-java.gem` with `--local`.

Note that this is only a pre-release version. The code included there is not tested perfectly, nor reviewed yet. We may change things before upcoming releases. But, we believe that this pre-release would give you hints that how big changes v0.11 will have.

### What comes during v0.11?

Once Embulk v0.11.0 is out, we plan to work on the following topics before v1.0. Stay tuned!

* Fix bugs based on your feedbacks
* Support plugin developers to catch up
* Provide better support for Maven-based pure-Java plugins, especially about installation
* Provide an alternative to Bundler, including support for Maven-based pure-Java plugins
    * The plan is to provide a new Gradle plugin to install a set of plugins, deploy `embulk.properties`, and so on.
* Develop a new better plugin testing framework, maybe with JUnit 5
* Consider better support for later Java, maybe Java 11 (or Java 17)
* Refactor and clean-up the code
