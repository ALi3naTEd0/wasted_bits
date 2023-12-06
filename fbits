#!/bin/bash

me=$(basename "$0")
tdir=$(mktemp -d "/tmp/${me}.XXXX") || { echo "$me: error: can't create temporary directory" 1>&2; exit 1; }

tf=$(mktemp "${tdir}/${me}.XXXX") || { echo "$me: error: can't create temporary file" 1>&2; exit 1; }

checkbits (){
        local bps abps tbps=0 n=0
        bps="$( metaflac --show-bps "$1" )"
        flac -ac "$1" 2>/dev/null | grep 'wasted_bits' | cut -d '=' -f 3 | cut -f 1 > "$tf"
        while read wb; do
                tbps=$(( tbps + ( bps - wb ) ))
                ((n++))
        done < "$tf"
        abps=$(( ( ( tbps * 10 / n) + 5 ) / 10 )) # (* 10 + 5) / 10 for proper rounding
        printf "%2u/%2u bits\t%s\n" "$abps" "$bps" "$1"
}

for f in "$@"; do
    case "$f" in
        *.flac) checkbits "$f" ;;
        *) continue ;;
    esac
done

# Remove the temporary directory and its contents
rm -rf "$tdir"
