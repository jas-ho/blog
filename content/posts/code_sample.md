---
title: "Code_sample"
date: 2018-08-12T16:28:43+01:00
---

blalbl

{{< highlight python "hl_lines=8 15-17,linenostart=1" >}}
	df = pd.DataFrame(dict('a': range(10)))
{{< / highlight >}}


{{< highlight bash "linenos=table,hl_lines=8 15-17,linenostart=1">}}
# quicker check for duplicates
# function to describe content of a file (for use with checknamed)
describe(){
    if [[ $# -ne 1 ]] ; then
        echo 'Expect a single argument (path of file to be fixed)'
        [ -v PS1 ] && return 1 || exit 1
    fi
    path="$1"

    if [[ -L "$path"  ]]; then
        content=$(readlink -f "$path")
        content="LINK $content"
    elif [[ -d "$path" ]]; then
        content=$(ls -m "$path" | tr "\n" " " | sort)
        content="DIR $content"
    elif [[ -f "$path" ]]; then
        content=$(sha1sum "$path" | cut -c1-40)
        content="FILE $content"
    fi
    echo "${content}  ${path}"
}
export -f describe

# find all paths (both files and directories) matching a certain pattern, describe their contents using the custom 'describe' function, and merge on common descriptions
checknamed(){
    # awk -F'[[:space:]][[:space:]]' \
    # 'NF>1{a[$1] = a[$1]"\n"$2};END{for(i in a){print i""a[i] "\n"}}' \
    # perl keeps order
    perl -F'[[:space:]][[:space:]]' -anle 'next if /^$/;$h{$F[0]} = $h{$F[0]}."\n ".$F[1];
        END{print $_,$h{$_},"\n" for sort keys %h}' \
    <(finamed "$@" | parallel describe | sort)
}
export -f checknamed
{{< / highlight >}}