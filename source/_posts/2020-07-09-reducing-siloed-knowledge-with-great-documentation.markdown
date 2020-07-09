---
layout: post
title: "Reducing siloed knowledge with great documentation"
date: 2020-07-09 19:21
comments: false
categories:
---
Talk to most software engineers and they'll tell you they've worked on projects with poor
documentation and know first-hand that it causes frustration, bottle-necks and general inefficiency.
Yet if you ask software engineers what they enjoy doing, writing documentation is either not on
their list or it's right down the bottom.

The tech team at Rail Europe places documentation very high up the list of priorities. This isn't
because we only employ people that enjoy writing documentation (although some of us clearly do! Myself
most certainly included). Documentation is baked into the heart of our way of working.

However, I can assure you we haven't all decided to write documentation in the name of productivity gains.
The main benefit of documentation to remote teams is flexibility...

## Avoiding blockers

Siloed information is the enemy of flexible remote work. We're an internationally distributed team
that believes in [asynchronous communication](/blog/2020/03/20/nothing-is-urgent-asynchronous-communication/).
We want everyone to be able to work to their schedule, not a rigid 9-5. It's no good if certain people
have to be available and online all the time, to be around to answer questions and interject at
any moment.

Once you trust each-other to manage your own time to get the work done, you have to optimise for it.
Good clear documentation is the bedrock of this approach, as it enables people to be self-sufficient.
Rather being blocked waiting for so-and-so to become available to answer a question, you
can read the docs and make progress.

## Onboarding

Not everyone gets an in-person induction, but there's definitely plenty to read! Our onboarding
documents are extensive and full of answers to questions that new starters ask. We know this
because we encourage each new starter to update and enhance the documentation. If they can't find the
answer they're after, they're empowered to update the docs. We use Github’s wiki feature, everyone
has write access from day one.

One of the main benefits of this is it avoids repetition and wasted time explaining the same
processes and concepts. It also means the new starter can read and digest at their pace and doesn't
have to sit through multiple meetings taking copious amounts of notes.

## Git as a living history

We love Ruby because it's so expressive and the code itself is often self-documenting. But there's
a long-held tradition of well written commit messages in the Rail Europe (formerly Loco2) codebase.
It's a testiment to the engineers that pioneered the application, that the commits tell the story of
not only what changed, but frequently why it changed, what alternative approaches were considered
and other jewels of information around decisions that were made many years ago by people that no
longer work at the company.

Because the example set was so strong, each new engineer has embraced it. Once code has been reviewed
and is ready to be shipped, interactive rebasing and squashing is used so that "work in progress"
commits are not included on the main branch. This means that "git log" reads like a well-written story.

This fits with our approach of continuous integration as each commit is atomic, which makes them easy
to rollback if required (fortunately not often!).

How to use Git can be a hotly disputed topic within engineering teams. Our belief is that Github PR's
are great for discussing and reviewing changes and the "work in progress" commits make perfect sense
in the context of the history of a PR. We don't encourage squashing during a code review, but personally
I like the idea mentioned on this blog post from [Future Learn](https://www.futurelearn.com/info/blog/telling-stories-with-your-git-history) that just as we refactor code before merging, we should refactor our commits too.

On writing the commit messages themselves, the article [A Note About Git Commit Messages](https://tbaggery.com/2008/04/19/a-note-about-git-commit-messages.html) basically sums it up for me.

## Why does feature x work like that?

An adhoc form of documentation comes as a side-effect from our desire to work without meetings. [This
previous blog post I wrote](/blog/2020/04/11/remote-working-without-meetings/) goes into the detail,
but to briefly summarise and mention one benefit... we openly discuss large new features before
implementation using a Basecamp message board. This kind of blew my mind when I joined the company,
as it meant I could go back in time and review the discussions that were had by the engineers right
when they were discussing how certain features might work.

This kind of documentation is obviously not "curated" and designed as a long-term living document, like
our wiki. But it can still provide valuable pieces of information that can otherwise be lost in the
mists of time.

## Summary

Documentaton comes in many forms and ultimately I think it's one of the most under-rated and overlooked
priorities that every software engineering team can benefit from.

