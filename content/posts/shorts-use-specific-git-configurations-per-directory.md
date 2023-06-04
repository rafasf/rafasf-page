+++ 
draft = false
date = 2021-11-03T20:50:51-05:00
title = "Shorts: Use specific Git configuration per directory"
description = "Different repositories might require different configuration"
tags = ["shorts", "git", "security"]
categories = ["second-brain"]
series = []
+++

> Ah! I pushed some code using my personal email!

Using the same computer for collaborating in different projects will likely
require slightly different configurations for some of them. For version control
systems (e.g. Git) the most common is the author information.

The author information is the first thing I configure and I do it at the global
level, which lives in `~/.config/git/config` (or `~/.gitconfig`).

```ini
[user]
  name = Bob Happyperson
  email = bob@example.org
```

That information is used for **every** `git` command you execute. It is a great
default, but if you have multiple projects that information might not be true
for all of them.

> Besides it is a potential security risk, since you can expose personal or work
> or client related information to parties that should not know about it.

[`includeIf`][git-includeif] can help quite a bit with this problem. Assume you
have three "project categories": **Personal**, **Internal work**, **Main
product** and you organize them in the same way:

```bash
projects/
├─ personal/
├─ internal/
├─ main-product/
```

Assuming `main-product` requires a different author email, create a
configuration file with especific configuration for `main-product`.

In the example below, I set the email and which SSH key to use for repositories
under `~/projects/main-product`.

```ini
# File location: projects/main-product/gitconfig

[user]
  email = bob@main-product.com

[core]
  sshCommand = "ssh -i ~/.ssh/id_main_product"
```

Then I add a reference to that file in the main configuration so Git knows which
file to use to override my defaults based on the directory I'm running `git`.

```ini
# File location: ~/.config/git/config or ~/.gitconfig

# ... file content ...

[includeIf "gitdir:~/projects/main-product/"]
  path = "~/projects/main-product/gitconfig"

[includeIf "gitdir:~/projects/internal/"]
  path = "~/projects/internal/gitconfig"
```

{{< notice warning >}}
Do not forget the trailing **`/`** in `gitdir` so it applies for all the
directories under that path.
{{< /notice >}}

{{< notice info >}}
You can adust any Git configuration with this pattern. The customization is not
limited to the author information.
{{< /notice >}}

[git-includeif]: https://git-scm.com/docs/git-config#_includes
