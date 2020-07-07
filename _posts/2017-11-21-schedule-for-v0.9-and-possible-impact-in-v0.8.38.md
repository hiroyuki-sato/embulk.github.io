---
layout: posts
title: "Schedule for v0.9, and possible impact in v0.8.38"
date: 2017-11-21 14:50:00 +0900
author: "dmikurube"
---

(Copied from [an old post in the embulk-announce mailing list](https://groups.google.com/d/msg/embulk-announce/vj33_9uc75U/jVAL376aCAAJ).)

Hi,

We have released Embulk `v0.8.37` just now, and release `v0.8.38` then. It will be a small but possibly-impacting change to remove JRuby engine instantiation from Embulk's bootstrap code ([#773](https://github.com/embulk/embulk/pull/773)).

We actually expect no impacts on users in `v0.8.38`. If you see any problems in `v0.8.38`, please let us know in GitHub Issues.

Then, "Feature freeze" for `v0.9` is planned after `v0.8.38`. We'd release `v0.9` expectedly after ~1 month from the feature freeze with some Release Candidates before that.

Let me recap the previous announcement about `v0.9` and its incompatibility below.

----

### tl;dr for Users

The biggest change is that Embulk `v0.9` would expect Java 8. Embulk core would be tested only in Java 8 (and possibly Java 9 in addition sooner or later... *hopefully*). Plugins compiled with Java 8 features are welcome then!

Next, we stop providing `embulk.gem` from `v0.9`. If you are using `embulk.gem` and/or `require 'embulk'` from Ruby, please see and comment in: [https://github.com/embulk/embulk/issues/628](https://github.com/embulk/embulk/issues/628) and/or [https://github.com/embulk/embulk/issues/766](https://github.com/embulk/embulk/issues/766).

Then, please try Embulk `v0.8.36` soon with the plugins and the usages which you actually operate. You'd see some warning messages like below on your console (`stderr`) if your usage expects compatibility breaks.

```
===== Example warning message =====
[WARN] Plugin uses deprecated constructor of org.embulk.spi.time.TimestampFormatter.
[WARN] Report plugins in your config at: https://github.com/embulk/embulk/issues/***
===== Example warning message =====
```

If you see such warning messages, please report in the GitHub Issues URL in your case. We'd provide how to workaround, notify the developer of the plugin, or reconsider the breakage then.

### for Plugin Developers

`v0.9` would introduce 6 types of plugin SPI incompatibilities as of now. They are related with JRuby which we're removing from Embulk's deeply-tightened dependencies in `v0.9`. Some of them are related with Timestamps which have been unnecessarily depending on JRuby.

#### 1. Some constructors of `TimestampParser` and `TimestampFormatter` ([#745](https://github.com/embulk/embulk/issues/745))

It could be the most impactful on plugins. `TimestampParser` and `TimestampFormatter` have been constructed with JRuby instances, but the constructors will be removed in `v0.9`. The GitHub Issue above describes how to workaround this incompatibility.

#### 2. `AbstractDynamicColumnSetter#setRubyObject` ([#799](https://github.com/embulk/embulk/issues/799))

We expect no plugins are calling this method directly. Ruby's `PageBuilder` had been calling it inside Embulk core, but the part is now replaced to pure-Ruby implementation. Please ping us if your plugin is calling the method directly.

#### 3. `RubyValueApi` ([#802](https://github.com/embulk/embulk/issues/802))

We expect no plugins are using this class directly. It had been a static utility class to convert internal MessagePack values and JRuby values inside Embulk core, but the part is now replaced to pure-Ruby implementation. Please ping us if your plugin is using this class directly.

#### 4. `Timestamp#getRubyTime` or `Timestamp.fromRubyTime` ([#820](https://github.com/embulk/embulk/issues/820))

We expect no plugins are using these methods directly. The methods had been used to convert Embulk `Timestamp`s and JRuby's time objects inside Embulk core, but the part is now replaced to pure-Ruby implementation. Please ping us if your plugin is calling the methods directly.

#### 5. Some constructors of `PagePrinter` ([#827](https://github.com/embulk/embulk/issues/827))

We expect no plugins are using `PagePrinter`. The class had been used from embedded `StdoutOutputPlugin`. `StdoutOutputPlugin` now calls `PagePrinter`'s new constructor.

#### 6. `TimestampFormat`: `newFormatter` and `newParser` ([#828](https://github.com/embulk/embulk/issues/828))

We expect no plugins are using these methods to create `TimestampFormatter` and/or `TimestampParser`. They have been `@Deprecated` for a long time since `v0.6.14`.
