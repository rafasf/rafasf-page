+++ 
draft = false
date = 2021-10-26T08:54:15-05:00
title = "Shorts: Shell script basic template"
description = "The little things to help building quality scripts"
slug = "" 
tags = ["shorts", "shell"]
categories = ["second-brain"]
externalLink = ""
series = []
+++

Writting a shell script to _"solve a problem"_ for me still the default, but it
does not happen everyday and I end-up forgetting some of the basics to include
to help me out and reduce the risk of weird behaviours.

## tl;dr

```bash {linenos=table}
#!/usr/bin/env bash

set -o errexit
set -o errtrace
set -o nounset
set -o pipefail

__dir="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"

function important_function() {
  echo "do things"
}

important_function
```

That combined with [`shellcheck`][shellcheck] is enough for me for most of the
time.

> [Here][b3boilerplate] is a more holistic / complete boilerplate if you are doing something
more serious.

### Explanation

* `set -o errexit` -- ensures the script exits when a command fails;
* `set -o errtrace` -- ensures the script exits when a command fails inside subshells or functions;
* `set -o nounset` -- ensures the script exits when undeclared variables are used;
* `set -o pipefail` -- ensures the script exits with a non-zero exit code when a
  piped command fails.
* `__dir` -- returns the directory where the shell scipts resides. It is useful
  when you need to use other resources relative to the script itself.

If you prefer a less explict version of the flags, you can replace lines 3-6
with:

```bash
set -eEuo pipefail
```

[shellcheck]: https://github.com/koalaman/shellcheck
[b3boilerplate]: https://bash3boilerplate.sh
