# workspace

Simplifies workflow with projects that have several active repositories.

By batching commands for filtered repositories, this script allows to avoid using several terminal tabs or tmux panels in large projects with big amount of repositories, that often have dependent changes *(or at least you can use it to open all your terminal tabs or tmux panels...)*.



# Examples

```bash
$ # Show git status for all repositories
$ # that have 'feature' in their current branch name (-B)
$ workspace status -B feature
```

```bash
$ # Show diff in every project with uncommited changes (-u)
$ workspace git -u -- diff
$
$ # Ask (-i) to stash changes
$ # in every project with uncommited changes (-u)
$ workspace git -i -u -- shash
```

```bash
$ # In every project that has branch 'master' (-s -b)
$ # checkout branch 'master' and pull changes
$ workspace foreach -s -b master -- \
$     git checkout master '&&' git pull
$
$ # and then...
$ # In every project that has branch 'feature/some' (-s -b)
$ # checkout branch 'feature/some' and merge it with branch 'master'
$ workspace foreach -s -b feature/some -- \
$     git checkout feature/some '&&' git merge master
```

```bash
$ # Open in bash every project that has 'engine' in it's name (-p)
$ workspace open -p engine
```

---

But, if you don't want to dive deep into yet another utility, just copy-paste the following commands to recover your workspace

```bash
$ # Ask (-i) to open in Gnome Terminal tabs (-t)
$ # every project that has 'feature' in it's current branch name (-B)
$ workspace open -t -i -B feature
$
$ # Same, but in tmux panels (-x)
$ workspace open -x -i -B feature
```

```bash
$ # Open in Gnome Terminal tabs (-t)
$ # projects that have 'engine' in their current project name (-p)
$ workspace open -t -p engine
$
$ # Same, but in tmux panels (-x)
$ workspace open -x -p engine
```


# Usage

`workspace` has basic commands, that specify actions to run with projects. Projects are filtered by name or git repository state (branch name, uncommited changes, detached HEAD).

### Commands

* `status` – show git status for each matched project
* `git` – run git command for each matched project
* `foreach` – run command for each matched project
* `open` – open matched projects in bash (default), Gnome-Terminal tabs or tmux panels

### Additional commands

* `install-completion-script` – install bash completion script for project and branch names
* `cache-workspace-info` – cache project paths and store project and branch names for completion script



### Options

* `-W`, `--workspace-path <path>` – search path [default: current directory]
* `-p`, `--project <pattern>` – project name pattern
* `-B`, `--branch <pattern>` – current branch name pattern
* `-b`, `--branches <pattern>` – branch name pattern for local branches
* `-r`, `--remote-branches <pattern>` – branch name pattern for remote branches
* `-a`, `--all-branches <pattern>` – branch name pattern for all branches
* `-s`, `--strict` – match next pattern strictly
* `-S`, `--strict-all` – match all patterns strictly
* `-n`, `--negate` – negate next pattern
* `-N`, `--negate-all` – negate all patterns
* `-u`, `--uncommited` – match projects with uncommited changes
* `-d`, `--ignore-detached` – do not match projects with detached HEAD
* `-t`, `--tabs` – open projects in Gnome Terminal tabs
* `-x`, `--tmux` – open projects in tmux panels
* `-i`, `--interactive` – ask to do your stuff with every matched project
* `-h`, `--help` – show this message



# Notes

Project name is considered to be the name of the folder that is parent to `.git` folder.

If no `-s` or `-r` option is set, then `<pattern>` is searched as a part of a project/branch name.

Use quotes for `|`, `;`, `&&` and `||` inside `<commands>` for `foreach`.

If any `<command>` in `git` or `foreach` failes during run, the script will prompt to open that project in bash to deal with issue manually.
