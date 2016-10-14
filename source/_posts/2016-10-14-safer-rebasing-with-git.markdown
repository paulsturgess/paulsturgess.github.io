---
layout: post
title: "Safer rebasing with Git"
date: 2016-10-14 18:32
comments: false
categories:
---
Rebasing a [git](https://git-scm.com/) branch is great way to maintain a clean commit history and it's my
preferred way of getting changes into a feature branch (instead of merging). This
blog post will explain how I use it and how I make it safer to use.

## Caveat!
Rebasing is dangerous because you are re-writing the branch history!

Imagine someone else has pulled down your branch and then you re-write history –
it will *really hurt* when it comes to combining both your changes.

If your feature branch will be worked on by multiple people, I would strongly urge
you do not rebase.

The git book does a great job of [describing rebasing in detail](https://git-scm.com/book/ch3-6.html),
including the pitfalls of rebasing public branches and what to do if you do if you
get really stuck after rebasing.

## My work flow
For most projects I work on, feature branches are typically only contributed to
by one person – so rebasing is ok.

My work-flow for creating a feature branch from `master` is something like:

```
$ git checkout -b feature/new_thing
... I make various commits for feature related changes
... Meanwhile, master is also updated
$ git fetch
$ git pull --rebase origin master
```

This effectively re-writes the history of `feature/new_thing` to place my commits
after those made in `master`. There's no merge commit, so it's really neat and tidy.

I like the idea of opening a Pull Request as soon I start work on a feature.
I'll then make 'work in progress' commits as I go and push them up to the remote.
This gives people an early chance to review what I'm doing and lessens the chance
that my work might get lost if my computer blows up and my backup fails :)

But these commits can be messy and include changes I later revert, as I work on
the feature. I think it's good practice to try and keep commits as logical and
concise as is reasonably possible. Ideally they will read like documentation for
the feature so that `git log` is like a story.

So during development, I won't get too hung up on the commits. But eventually I
will use [interactive rebase](https://git-scm.com/book/ch7-6.html) to re-order or
squash my commits into shape.

My interactive rebase work-flow is typically:

```
$ git log
... Find the sha for the last commit I want to leave alone
$ git rebase -i sha-for-commit-to-leave-alone-goes-here
```


It will then show something like this:

```
pick f7f3f6d Initial work on feature
pick 310154e Additional work on feature
pick a5f4a0d Finish feature

# Commands:
#  p, pick = use commit
#  r, reword = use commit, but edit the commit message
#  e, edit = use commit, but stop for amending
#  s, squash = use commit, but meld into previous commit
#  f, fixup = like "squash", but discard this commit's log message
#  x, exec = run command (the rest of the line) using shell
#
# These lines can be re-ordered; they are executed from top to bottom.
```

Just edit `pick` to whatever action you want to perform and use `:wq` to save.

Now the next step is to push `feature/new_thing` to the remote and (other than the
first time I push) I'll need to force push and this is where it gets dangerous!

```
$ git push -f origin feature/new_thing
```

This will completely overwrite `feature/new_thing` whatever state it is in. Too bad if
someone else updated it, their changes are gone.

Accidentally type `feature/other_thing` and you've just blitzed the wrong branch!

## The safer way to force push

```
$ git push --force-with-lease feature/new_thing
```

Force with lease essentially checks the remote branch to see if it's been updated
upstream and if so rejects the push and shows you a nice error message.

Beware that if you fetch, but don't actually pull in the changes, `force-with-lease`
will not save you!

I want to always use `force-with-lease` over the standard `force` but this is
long to type so I have a git alias:

```
$ git config --global alias.pushf "push --force-with-lease"
```

Now I can safely use:

```
$ git pushf origin feature/new_thing
```

Got any git rebase tips? Feel free to contact me on Twitter [@paulsturgess](https://twitter.com/paulsturgess)
