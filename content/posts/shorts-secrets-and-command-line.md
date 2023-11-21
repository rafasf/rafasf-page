+++
title = "Shorts: Secrets and the command line"
date = 2018-12-17T11:30:25-05:00
tags = ["command-line", "macos", "security", "shorts"]
draft = false
+++

> You don't need to type passwords directly in your terminal.

Whether you're at home, in your office, in a coffee shop, or sharing your screen
in some way, avoid making any secrets easily visible. The majority of people
won't memorise it, especially if it's a strong secret; however, it can be easily
retrieved from a screenshot or picture.

## macOS: Keychain (out-of-box)

The steps:

1. Add the password to the Keychain
2. Retrive and use the password when needed
3. Minimise how much typing is necessary

### 1 Add Password to Keychain

Type the command below and follow the instructions.

```sh
$ security add-generic-password -a ${USER} -s <service-name> -w
```

The `<service-name>` could be **Company-GitHub**. It represents which service
that password belongs.

### 2 Retrieve Password From Keychain

The command below prints the password in the terminal.

```sh
$ security find-generic-password -a ${USER} -s <service-name> -w
```

That means it can be used anywhere in the shell!

```sh
$ curl \
    --user "username:$(security find-generic-password -a ${USER} -s <service-name> -w)" \
    http://protected.site
```

### 3 Minimise Typing

The command `security find-generic-password -a ${USER} -s <service-name> -w`
might be a tad long to be typed everytime the password is needed.

Create a function and source them in `.zshrc` (or equivalent) to solve that,
here is an example:

```sh
gh_pass() {
  security find-generic-password -a ${USER} -s personal-gh -w
}
```

And then just use the function when needed, for example:

```sh
$ curl --user "username:$(gh_pass)" http://protected.site
```

## Cross-platform: `pass` (open source)

This is a common password management tool and an open-source alternative that
can be used across multiple platforms.

Check how to set up and use on [their website](https://www.passwordstore.org).

## Cross-platfrom: 1Password (paid)

1Password continuously expands their command-line tooling support. It's worth
checking the possibilities [here](https://developer.1password.com/docs/cli).
