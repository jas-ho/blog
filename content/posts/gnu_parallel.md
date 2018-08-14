---
title: "gnu parallel: parallelize bash"
date: 2018-08-13T11:42:04+01:00
tags:
- bash
---

Do you know this? You need to extract the same information from many different files, or apply a transformation to many different files or calculate some function for many different inputs.

The good news:

> If you can use **bash** to get what you want **for a single input file**, you can use [**gnu parallel**](https://www.gnu.org/software/parallel/) to get what you want **for any number of input files.** It allows you to go from 'one' to 'many' in a straightforward and efficient way.

Let me explain how this works.

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
	$ grep 'date: ' gnu_parallel.md | head -n 1 | cut -d ' ' -f 2 
	2018-08-13T11:42:04+01:00
{{< / highlight >}}

- `grep` finds all lines containing 'date: '.
- `head -n 1` retains only the first line (in case there are more lines starting with "date: ")
- `cut -d ' ' -f 2` splits the output on a single white-space (`-d ' '`) and retains only the second field (`-f 2`)

Head over to [explainshell.com](https://explainshell.com/explain?cmd=grep+%27date%3A+%27+gnu_parallel.md+%7C+head+-n+1+%7C+cut+-d+%27+%27+-f+2+) for more information.

# apply it to many files
Now assume you have many files from which you'd like to extract the date.
{{< highlight bash >}}
$ find -name *md
./posts/code_sample.md
./posts/about.md
./posts/gnu_parallel.md
{{< / highlight >}}
`find -name *md` finds all markdown-files in or below your current working directory.

gnu parallel allows us to apply the above command to every single file in the list returned by `find`:
{{< highlight bash >}}
$ find -name *md | parallel "grep 'date: ' {} | head -n 1 | cut -d ' ' -f 2"
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
gnu parallel is an extremely convenient and versatile tool which allows you to

- **apply bash commands across a list** without writing explicit for loops
- **parallelize across all your cores** (and even across different machines!)

To find out more, see the [official docs](https://www.gnu.org/software/parallel/).
