+++ 
draft = false
date = 2021-11-03T20:50:51-05:00
title = "Shorts: Use specific Git configuration per directory"
description = "Different repositories might require different configuration"
tags = ["shorts", "git", "security"]
categories = ["second-brain"]
series = []
+++

> Ah! I pushed some work code using my personal information!

Using the same computer for collaborating in different projects will likely
require slightly different configurations for some of them. For version control
systems (e.g. Git) the most common is the author information.

The author information is the first thing to configure at the global level,
which lives in `~/.config/git/config` (or `~/.gitconfig`).

```ini
[user]
  name = Bob Happyperson
  email = bob@example.org
```

This is used for **every** `git` command you execute. It's a great default, but
if you have multiple projects that information might not be true for all of
them.

[`includeIf`][git-includeif] helps with that.

Assume you have three "project categories": **Personal**, **Internal work**,
**Client One** and you organize them in the same way:

```bash
projects/
├─ personal/
├─ internal/
├─ client-one/
```

Here `client-one` requires a different author email, create a
configuration file with especific configuration for `client-one`.

In the example below, I set the email and which SSH key to use for repositories
under `~/projects/client-one`.

```ini
# File location: projects/client-one/gitconfig

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

[includeIf "gitdir:~/projects/client-one/"]
  path = "~/projects/client-one/gitconfig"

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
