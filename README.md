# Introduction

Why Bash? Because it's ubiquitious and powerful.

When used correctly, Bash is like sorcery.

But oh! how it is to be led astray by the wrong spells. The anwer, of course, is the universal school of magic: Copy and Paste.

This repository collects essential Bash incanatations for all your infracoding needs.

# The magic spells

All the following snippets target Bash version 3, which is very old but is also the version shipped by macOS (thanks Apple!).

## Creating a temp directory

Add this at the top of your script:

```shell
# start out_temp_dir: v1
our_temp_dir=$(mktemp -d 2>/dev/null || mktemp -d -t 'our_temp_dir')
function cleanup_temp_dir() { rm -rf "$our_temp_dir" ; } && trap "cleanup_temp_dir" EXIT
# end out_temp_dir
```

Then you can use `$our_temp_dir`.

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

## Contributing

Suggest spells by submitting a PR!

Please file an issue if I failed to give appropriate credit for a spell.

This spellbook (c) Paulus Esterhazy <pesterhazy@gmail.com>

