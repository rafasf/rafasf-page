+++ 
draft = true
date = 2021-10-27T15:25:29-05:00
title = "Shorts: Working with multiple repositories"
description = "Reducing the friction of working with multiple repositories"
tags = ["shorts", "git"]
categories = ["second-brain"]
series = []
+++

## The routine

```bash
$ cd projects
~/projects $ cd app1
~/projects/app1 $ git pull [--rebase]
~/projects/app1 $ cd ../app2
~/projects/app2 $ git pull [--rebase]
~/projects/app2 $ cd ../app3
~/projects/app3 $ git pull [--rebase]
.
.
.
```

This routine only gets worse as the number of applications grows, the time spent
on it will also increase and your willingness to be up-to-date might actually go
down.

It can be better though! :slightly_smiling_face: It can look like:

```bash
$ gr git pull [--rebase]
```

The tool [`gr`][gr] is one of many that helps people working with multiple
repositories. Besides allowing the execution of commands across repositories,
you can annotate each of them with one or more tags.

Here is how I set it up:

```bash
$ npm install -g git-run
$ cd ~/projects
$ gr tag discover
```

Then I tag each of the repositories in a way that helps my current workflow,
mostly by product and was able to make my morning routine much nicer.

On top of that, another thing I like to do is to check what changed since the
previous day and to do that with `gr` I only need one command:

```bash
$ gr git log --oneline --since=yesterday
```

And if I am more interested for a subset of the repositories, I would do:

```bash
$ gr @product1 git log --oneline --since=yesterday
```

## Other tools

- https://github.com/nosarthur/gita
- https://myrepos.branchable.com
- https://github.com/isacikgoz/gitbatch

_and many more..._


[gr]: https://github.com/mixu/gr
