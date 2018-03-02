+++
date = "2017-05-12T16:33:12-04:00"
draft = false
title = "Find Pending Work with Command Line"

+++

Recently I came across a bit of manual work for provisioning new instances in AWS. Given the set of constraints, it end-up being an exciting journey with the company of command line utilities.

During this journey, I learned about `comm` and here is how:

There was a list of new machines, represented by IP address, application name and the environment it belongs. The goal was to run a script against each of those, update the load balancer and retire the old machine.

Here is an example of the list, let's call it the source:

```
10.0.0.1 development app-one
10.0.0.3 uat app-one
10.0.0.2 development app-two
10.0.0.43 uat app-two
10.0.0.4 development app-three
10.0.0.12 uat app-three
```

Given that file format, I had a script that would read specific lines and run the script against the machine. That script would create an entry in another file after completion, here is how the output file looked like:

```
[2017-05-10:11:02:56] 10.0.0.1 development app-one
[2017-05-10:11:02:56] 10.0.0.43 uat app-two
```

This semi-manual work spanned a few days, and it was getting hard to look at the source and the output file to figure out which machine was up next. Then I start using a few tools that I was familiar with like `diff` and `cut` to filter out the noise for me. But I wasn't happy with the output.

```
$ diff -u <(cat source | cut -d' ' -f1 | sort -u) <(cat output | cut -d' ' -f2 | sort -u)

3,6d2
< 10.0.0.12
< 10.0.0.2
< 10.0.0.3
< 10.0.0.4
```

That's when I eventually found out about `comm`, a tool that compares two files line by line and allows selection of the side of the difference to show, so replacing `diff` with `comm`, I got:

```
$ comm -23 <(cat source | cut -d' ' -f 1 | sort -u) <(cat output | cut -d' ' -f 2 | sort -u)

10.0.0.12
10.0.0.2
10.0.0.3
10.0.0.4
```

From this point on, I could use the output of that command as input to other supporting scripts to get the work done.

btw, check the full explanation of the command above [here](https://explainshell.com/explain?cmd=comm+-23+%3C%28cat+source+%7C+cut+-d%27+%27+-f+1+%7C+sort+-u%29+%3C%28cat+output+%7C+cut+-d%27+%27+-f+2+%7C+sort+-u%29).
