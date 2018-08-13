---
title: "gnu parallel: parallelize bash"
date: 2018-08-13T11:42:04+01:00
tags:
- bash
---

Have you encountered this situation? You need to extract some information from a file ... and then do the same thing for a ton of files.

In this post, I'll show a simple example task and explain how you can use gnu parallel to very conveniently achieve your task for many files once you've figured out how to do it for a single file.

# example task: extract date from text file
Say the file is called gnu_parallel.md and looks like that
{{< highlight markdown >}}
	---
	title: "gnu parallel: parallelize bash"
	date: 2018-08-13T11:42:04+01:00
	draft: true
	---
	some text...
{{< / highlight >}}

To extract the date you could do the following in bash:
{{< highlight bash >}}
	$user@machine: grep 'date: ' gnu_parallel.md | head -n 1 | cut -d ' ' -f 2 
	2018-08-13T11:42:04+01:00
{{< / highlight >}}

- `grep` finds all lines containing 'date: '.
- `head -n 1` retains only the first line.
- `cut -d ' ' -f 2` 
	- splits the output on a single white-space (`-d ' '`) and then
	- retains only the second field (`-f 2`)

# apply it to many files
Now assume you have many files from which you'd like to extract the date.
{{< highlight bash >}}
$user@machine: find -name *md
./posts/code_sample.md
./posts/about.md
./posts/gnu_parallel.md
{{< / highlight >}}
`find -name *md` finds all markdown-files in or below your current working directory.

I'd like to apply the command we had above to every single file in the list. gnu parallel allows us to do exactly that:
{{< highlight bash >}}
$user@machine: find -name *md | parallel "grep 'date: ' {} | head -n 1 | cut -d ' ' -f 2"
2018-08-12T16:28:43+01:00
2018-08-12T16:05:10+01:00
2018-08-13T11:42:04+01:00
{{< / highlight >}}
You can think of this as follows:
{{< highlight bash >}}
outputs_a_list | parallel "command {input_from_list}"
{{< / highlight >}}
`parallel` will run `command item` on every single `item` from the list produced by the command `outputs_a_list`.

# Summary
gnu parallel is an extremely convenient tool which allows you to

- apply bash commands across a whole list without writing for loops
- parallelize across all your cores
- much _much_ more. See the official docs.