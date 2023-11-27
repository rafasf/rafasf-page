+++ 
draft = false
date = 2021-10-27T15:25:29-05:00
title = "Shorts: Working with multiple repositories"
description = "Reducing the friction of working with multiple repositories"
tags = ["shorts", "git"]
+++

Have you experienced something the the flow below?

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

If you have, you know how painful and boring that gets. The good news is that
[`mani`][mani] exists making the flow much better.

After configuring, the following will get you the same result:

```bash
$ mani run pull
```

## The setup

### 1 Install

Use your favourite package manager or [manually install][mani_manual_install].

```bash
$ brew install mani
```

### 2 Initialise

Run the command below in the parent folder where your repositories reside. The
tool will create an entry for each in a file named `mani.yml`.

```bash
$ mani init
```

### 3 Create your tasks

Now that you have all the repositories in your `mani.yml` file, you can create
the tasks to be executed across them.

```yaml
# ... repositories ...

tasks:
  pull:
    cmd: git pull --rebase
```

This is a simple command, but the tool will allow you to be very creative on
those.

### 4 Sharing

In case you are changing computers, backup the `mani.yml` file, then in the new
computer you can run `mani sync` to clone all the repositories from scratch.

You can also share that file with collegues instead of telling them to clone
five repositories.

### 5 Being creative

[Check the documentation][mani_docs] to learn more.

A few of the things you can do:

- Group repositories and have tasks targeted to specific groups
- Visualise your repositories as a tree
- Tweak the visual output

## Other tools

- https://github.com/nosarthur/gita
- https://myrepos.branchable.com
- https://github.com/isacikgoz/gitbatch

_and many more..._

[gr]: https://github.com/mixu/gr
[comby]: https://comby.dev
[mani]: https://github.com/alajmo/mani
[mani_manual_install]: https://github.com/alajmo/mani#installation
[mani_docs]: https://manicli.com/examples
