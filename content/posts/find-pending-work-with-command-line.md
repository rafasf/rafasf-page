+++
date = "2017-05-12T16:33:12-04:00"
draft = true
title = "Find Pending Work with Command Line"

+++

Recently I came across a bit of manual work for provisioning new instances in AWS. Given the set of contrainst it end-up being an interesting journey with the company of command line utilities.

The information available was a bunch of instance IPs and names, which correlates to specific applications and
environments. With a little bit of mapping this was the result:

```
10.0.0.1 development app-one
10.0.0.3 uat app-one
10.0.0.2 development app-two
10.0.0.43 uat app-two
10.0.0.4 development app-three
10.0.0.12 uat app-three
```

This list grew as new instances were being createad, since all the current instances would be replaced with new ones.

In order to keep track of which instances were already provisioned, in the script that trigger the provisioning there
was a line to append to a file which instance was provisioned, the output looks like this:

```
[2017-05-10:11:02:56] 10.0.0.1 development app-one
[2017-05-10:11:02:56] 10.0.0.43 uat app-two
```

As the list of available serves grew, it became hard to know what was missing. That is when some fun with the command line started.

* How do I find out the pending work?

First thought was to do a diff between the files, but there aren't exactly the same file but still possible by making those to look a-like at diff time

Getting the IP from file one: `cat all-servers | cut -d' ' -f1` would return:

```
10.0.0.1
10.0.0.3
10.0.0.2
10.0.0.43
10.0.0.4
10.0.0.12
```

Getting the IP from file two is similar: `cat work-done | cut -d' ' -f2` would return:

```
10.0.0.1
10.0.0.2
```

With that figured out it I could diff the files but the output wasn't as friendly as I would have liked, the default
output shows a lot of information:

```
$ diff -u <(cat all-servers | cut -d' ' -f1 | sort -u) <(cat work-done | cut -d' ' -f2 | sort -u)
```

```
3,6d2
< 10.0.0.12
< 10.0.0.2
< 10.0.0.3
< 10.0.0.4
```

Using a couple of the options available, it is possible to get that closer to the output which would be useful. Passing
`-u` to `diff` it shows the `+` and `-` which makes a bit easier to identify what is pending. That is a good option but
the goal is to just have what is pending.

This was the first time I made use of `comm`, this tool allows different printing outputs, one of those being everything
that is in one file but not in the other.

```
$ comm -23 <(cat all-servers | cut -d' ' -f 1 | sort -u) <(cat work-done | cut -d' ' -f 2 | sort -u)
```

```
10.0.0.12
10.0.0.2
10.0.0.3
10.0.0.4
```

Since the lines in `all-servers` is the input of a script, there is one more step to complete the flow. Getting the
whole line based on the last output.

```
$ output | xargs -I {} grep -s {} all-servers
```

```
10.0.0.12 uat app-three
10.0.0.2 development app-two
10.0.0.3 uat app-one
10.0.0.43 uat app-two
10.0.0.4 development app-three
```
