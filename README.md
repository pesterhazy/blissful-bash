# Introduction

Why Bash? Because it's ubiquitious and powerful.

When used correctly, Bash is like sorcery.

But oh! how easy it is to be led astray when guided by the wrong spells. The anwer, of course, lies in the universal school of magic: the School of Copy and Paste.

This repository collects essential Bash incanatations for all your infracoding needs.

# The magic spells

All the following snippets target Bash version 3, which is very old but is also the version shipped by macOS (thanks Apple!).

All the tools used in this collection are likely to be preinstalled on most reasonable Unixes (notably macOS and Linux).

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

## Contributing

Suggest spells by submitting a PR!

Please file an issue if I failed to give appropriate credit for a spell.

This spellbook (c) Paulus Esterhazy <pesterhazy@gmail.com>

