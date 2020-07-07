---
layout: posts
title: "Deprecation of MapReduce Executor"
date: 2019-06-10 17:56:43 +0900
author: "dmikurube"
---

(Copied from [an old post in the embulk-announce mailing list](https://groups.google.com/d/msg/embulk-announce/NfR0mSfGfFk/eoszKSgaAgAJ).)

Hi Embulk users,

Let me announce our decision that MapReduce Executor will no longer be supported from next releases (`v0.9.18` -) of Embulk core.

This is because:

1. MapReduce Executor has been making plugins to require stronger assumptions in their library dependencies. MapReduce's own dependencies are unavoidably visible to plugins when running under MapReduce Executor. It has caused the situations to be much complicated.
2. Through we're going to make changes in Embulk's class loading mechanisms, MapReduce Executor needed to catch up for every change. It is expected to be really complicated.
3. We had no feedbacks for my request at [https://groups.google.com/forum/#!topic/embulk-announce/i26O7kT__OY](https://groups.google.com/forum/#!topic/embulk-announce/i26O7kT__OY). We expect there are no effective users of MapReduce Executor.

Instead, we will look for another approach to execute Embulk in a scalable distributed manner. It would need a certain amount of time, though.

Or, if we have a strong continuous contributor both on Embulk and MapReduce Executor, we MAY revive it from the next major version (`v0.10`). But please expect that it's unlikely.

Thanks.
