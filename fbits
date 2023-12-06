#!/bin/bash

me=$(basename "$0")
tdir="$HOME/fbits_temp"

# Create a temporary directory
mkdir -p "$tdir" || { echo "$me: error: can't create temporary directory" 1>&2; exit 1; }

# Use the temporary directory for temporary files
tf=$(mktemp "${tdir}/${me}.XXXX")

if [ -z "$tf" ]; then
    echo "$me: error: can't create temporary file" 1>&2
    exit 1
fi

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

process_files() {
    for f in "$@"; do
        case "$f" in
            *.flac) checkbits "$f" ;;
            *) continue ;;
        esac
    done
}

process_directory() {
    local dir="$1"
    for f in "$dir"/*.flac; do
        [ -e "$f" ] || continue
        checkbits "$f"
    done
}

if [ $# -eq 0 ]; then
    echo "Usage: $me [file or directory ...]" 1>&2
    exit 1
fi

for target in "$@"; do
    if [ -f "$target" ]; then
        process_files "$target"
    elif [ -d "$target" ]; then
        process_directory "$target"
    else
        echo "$me: error: '$target' is not a valid file or directory" 1>&2
    fi
done

# Remove the temporary directory and its contents
rm -rf "$tdir"
