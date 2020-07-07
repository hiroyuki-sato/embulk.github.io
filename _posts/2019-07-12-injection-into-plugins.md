---
layout: posts
title: "Injection into plugins"
date: 2019-07-12 02:41:14 +0900
author: "dmikurube"
---

(Copied from [an old post in the embulk-announce mailing list](https://groups.google.com/d/msg/embulk-announce/55FyUcg_KVU/0X7Hki7KAQAJ).)

Hi Embulk users,

We think about changing the existing injection mechanisms for plugins. In short, if your plugin is getting a system instance injected, it may not work from a certain version of Embulk.

As far as we know, very very few OSS Embulk plugins are getting injected. The MapReduce Executor is an example, but it has already gotten obsolete.

If your plugin is getting something injected, please leave a comment in the GitHub Issue below. We'll consider about that.

[https://github.com/embulk/embulk/issues/1164](https://github.com/embulk/embulk/issues/1164)

Thanks.
