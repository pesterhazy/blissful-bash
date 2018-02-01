# Introduction

Why Bash? Because it's ubiquitious and powerful.

When used correctly, Bash is like sorcery.

But oh! how easy it is to be led astray when guided by the wrong spells. The anwer, of course, lies in the universal school of magic: the School of Copy and Paste.

This repository collects essential Bash incanatations for all your infracoding needs.

# The magic spells

The following snippets target Bash version 3, which is very old but is also the version shipped by macOS (thanks Apple!).

Any external tool used in this collection is likely to be preinstalled on most reasonable Unixes (notably macOS and Linux).

Snippets are written in such a way that they can be conveniently pasted into a shell script. They are versioned and look like this:

```
# start name_of_snippet: v1

# .. code ..

# end name_of_snippet
```

That way you can easily see where the snippet starts and ends. The version number allows you to check if a newer version of this script is available (with improvements or bugfixes). Hopefully updates won't be necessary too frequently.

## Creating a temp directory

Add this at the top of your script:

```shell
# start our_temp_dir: v1
our_temp_dir=$(mktemp -d 2>/dev/null || mktemp -d -t 'our_temp_dir')
function cleanup_temp_dir() { rm -rf "$our_temp_dir" ; } && trap "cleanup_temp_dir" EXIT
# end our_temp_dir
```

Then you can use the directory `$our_temp_dir` to store files. The directory will be automatically deleted when the script exits.

This works on Linux and macOS. Credit for the macOS trick goes to [Krinkle](https://unix.stackexchange.com/a/84980/133951).

## Strict Mode

Make a habit of starting all your scripts with these two lines:

```shell
#!/usr/bin/env bash
set -euo pipefail
```

This convention is unofficially called [Strict Mode](http://redsymbol.net/articles/unofficial-bash-strict-mode/). Some peole will say that flags like `-e` and `-u` are inherently broken. They're not wrong, but Bash is even more broken without these flags.

## Situated Bash scripts

Many scripts expect to be called in the context of a project root (usually the directory containing the `.git` folder). I call these "situated scripts". In situtated scripts, it's useful to `chdir` into the project root at the top of the file, so you can rely on using relative paths throughout your code.

If your script is located in the `$PROJECT_ROOT/bin` subdirectory, use the following magical incantation (instead of Strict Mode):

```shell
#!/usr/bin/env bash
set -euo pipefail && cd "$(dirname "${BASH_SOURCE[0]}")/.."
```

Make sure to adjust the relative path (`/..`) if the script is located at a different point in your project's folder hierarchy.

## Where am I?

Even if changing the project root is not the right choice for your script, it can still be useful to use paths relative to the directory containing the script:

```shell
our_project_root="$(dirname "${BASH_SOURCE[0]}")/.."
```

Again this assumes that the script is located in a direct subfolder of the project root. If that's not true in your project, change the `/..` part accordingly.

Sometimes you need an absolute path to your project root:

```
our_abs_project_root="$(python -c "import os,sys; print os.path.abspath(sys.argv[1])" "$our_project_root")"
```

The `abspath` operation ensures that the path starts with a slash. We [use python](https://stackoverflow.com/questions/284662/how-do-you-normalize-a-file-path-in-bash/3373298#3373298) here because it's available on both macOS and Linux. You can use `os.path.realpath` instead of `os.path.abspath` if you need to resolve symbolic links in addition to finding an absolute path to the folder. In my experience, however, making paths absolute is usually what you want, not canonizing them.

## Identifiers

You need an identifier? You could use a timestamp (accurate to the second):

```
id="$(date -u '+%Y%m%d%H%M%S')"
```

or a random UUID:

```
id="$(python -c 'import uuid; print uuid.uuid4()')"
```

or generate atomic IDs through an API call ([try it](http://numeri.xyz/)):

```
id="$(curl --silent --show-error https://api.numeri.xyz/v1/255c994e-f77f-4ff6-b1d9-edab1f763fdb/increase)"
```

## Command line argument parsing

Command line argument parsing is complicated. Here we opt for a simple and pragmatic approach. This snippet supports

- flags like `--help` or `-h`
- and arguments like `--path PATH`
- and any number of positional arguments

But we

- don't support coalescing flags like many Unix commands `ls -Arlt`
- or use the `getopt` or `getopts` builtins, which add complexity.

```shell
# start our_parse_args: v1

parse_args() {
    args=("$@")
    positional=()
    while [ "${#args[@]}" -gt 0 ]; do
        handled=0

        if declare -f handle_arg > /dev/null; then
            handle_arg
        fi

        if [[ "$handled" == 1 ]]; then
            args=("${args[@]:1}")
        else
            case "${args[0]}" in
                -*)
                    echo "unknown option: ${args[0]}" >&2
                    exit 1
                    ;;
                *)
                    positional+=("${args[0]}");
                    args=("${args[@]:1}")
                    ;;
            esac
        fi
    done
}

# end our_parse_args: v1
```

To use this snippet, define a function called `handle_arg` and set global variables depending on the arguments passed. Positional parameters are stored in the array `"${positional[@]}"`. For example:

```shell
# set defaults

help=0
foo=bar

handle_arg() {
    case "${args[0]}" in
        --help)
            help=1
            handled=1
            ;;
        --foo)
            args=("${args[@]:1}") # shift
            foo="${args[0]}"
            handled=1
            ;;
    esac
}

parse_args "$@"
echo "help=$help, foo=$foo"
echo "positional: ${positional[@]}"
```

## Contributing

Suggest spells by submitting a PR!

Please file an issue if I failed to give appropriate credit for a spell.

This spellbook (c) Paulus Esterhazy <pesterhazy@gmail.com>

