+++
title = "Shorts: Secrets and command line"
date = 2018-12-17T11:30:25-05:00
tags = ["command-line", "macos", "security", "shorts"]
categories = ["second-brain"]
draft = false
+++

> Don't type passwords directly in your shell.

Have you been in the situation when running a command in the terminal which
requires typing the password along with it?

It may seem normal, but besides having your password exposed in plain sight it
becomes part of you shell history and it can be awkard to ask your pair to look
to the side when while you type your password.

If you use macOS, you can make use of the Keychain to avoid showing the password
when performing such tasks.

The steps:

1. Add the password to the Keychain
2. Retrive and use the password when needed
3. (Optional) Minimise how much typing is necessary

## Add Password to Keychain

Type the command below, and follow the instructions.

```sh
$ security add-generic-password -a ${USER} -s <service-name> -w
```

The `<service-name>` could be **Company-GitHub**. It represents which service
that password belongs to.

## Retrieve Password From Keychain

The command below prints the password in the terminal.

```sh
$ security find-generic-password -a ${USER} -s <service-name> -w
```

That means it can be used anywhere in the shell!

```sh
$ curl --user "me:$(security find-generic-password -a ${USER} -s <service-name> -w)" \
  http://protected.site
```

## Minimise Typing

The command `security find-generic-password -a ${USER} -s <service-name> -w`
might be a tad long to be typed everytime the password is needed.

I usually create functions and source them in `.zshrc` (or equivalent) to help
me with that, here is an example:

```sh
gh_pass() {
  security find-generic-password -a ${USER} -s personal-gh -w
}
```

And then just use the function when needed (e.g. the command above):

```sh
$ curl --user "me:$(gh_pass)" http://protected.site
```

Another tool which makes this and many other things possible is
[pass](https://www.passwordstore.org).
