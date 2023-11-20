+++ 
draft = false
date = 2021-11-03T20:50:51-05:00
title = "Shorts: Use specific Git configuration per directory"
description = "Different repositories might require different configuration"
tags = ["shorts", "git", "security"]
categories = ["second-brain"]
series = []
+++

> Ah! My commit has the wrong author name and email!!

Using the same computer for different projects that need different identities or
settings can sometimes accidentally leak unwanted information, not reconise you
in the system, etc.

The good news is that Git makes it easy to manage configurations at a folder
level, here you'll find a way to tailor the configuration to your need.

A typical setup has the author information configured at the global level, which
lives in `~/.config/git/config` (or `~/.gitconfig`), for example:

```ini
[user]
  name = Bob Happyperson
  email = bob@example.org
```

This is used for **every** `git` command you execute, but that's not quite what
you want. You want to control that at the folder level and to do that you'll use
[`includeIf`][git-includeif].

Let's go through an example:

Assume you have three project categories, **Personal**, **Internal work** and
**Client One**, where each of them require a different `author email` and `SSH
key`.

```bash
projects/
├─ personal/
├─ internal/
├─ client-one/
```

With that, create a `gitconfig` inside of each of these folders with the values
you'd like to overwrite.

Here is an example for **Client One**:

```ini
# File location: projects/client-one/gitconfig

[user]
  email = bob@client-one.com

[core]
  sshCommand = "ssh -i ~/.ssh/id_client_one"
```

After creating all the specific configuration files, go to your global Git
configuration and add the following:

```ini
# File location: ~/.config/git/config or ~/.gitconfig

# ... file content ...

[includeIf "gitdir:~/projects/personal/"]
  path = "~/projects/personal/gitconfig"

[includeIf "gitdir:~/projects/internal/"]
  path = "~/projects/internal/gitconfig"

[includeIf "gitdir:~/projects/client-one/"]
  path = "~/projects/client-one/gitconfig"
```

From now on, Git will use the values you added in the more specific file instead
of the global one.

{{< notice warning >}}
Do not forget the trailing **`/`** in `gitdir` so it applies for all the
directories under that path.
{{< /notice >}}

{{< notice info >}}
You can adust any Git configuration with this pattern. The customization is not
limited to the author information.
{{< /notice >}}

[git-includeif]: https://git-scm.com/docs/git-config#_includes
