# Introduction

Why Bash? Because it's ubiquitious and powerful.

When used correctly, Bash is like sorcery.

But oh! how it is to be led astray by the wrong spells. The anwer, of course, is the universal school of magic: Copy and Paste.

This repository collects essential Bash incanatations for all your infracoding needs.

# The magic spells

All the following snippets target Bash version 3, which is very old but is also the version shipped by macOS (thanks Apple!).

## Creating a temp directory

Add this at the top of your script:

```
# start out_temp_dir: v1
our_temp_dir=$(mktemp -d 2>/dev/null || mktemp -d -t 'our_temp_dir')
function cleanup_temp_dir() { rm -rf "$our_temp_dir" ; } && trap "cleanup_temp_dir" EXIT
# end out_temp_dir
```

Then you can use `$our_temp_dir`.

This works on Linux and macOS.

## Bash scripts rooted in your project home

If your script is located in `$PROJECT_ROOT/bin`, use the following magical incantation:

```
#!/usr/bin/env bash
set -euo pipefail && cd "$(dirname "${BASH_SOURCE[0]}")/.."
```

Make sure you adjust the relative path if the script is located at a different point in your project's folder hierarchy.

## Contributing

Suggest spells by submitting a PR!

This collection (c) Paulus Esterhazy <pesterhazy@gmail.com>
