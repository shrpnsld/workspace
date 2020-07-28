# workspace

Simplifies workflow with projects that have several active repositories.

By batching commands for filtered repositories, this script allows to avoid using several terminal tabs or tmux panels in large projects with big amount of repositories, that often have dependent changes *(or at least it can help you to restore your working session – terminal tabs or tmux panels...)*.



# Examples

```bash
$ # Show git status for all repositories
$ # that have 'feature' in their current branch name (-B)
$ workspace status -B feature
```

```bash
$ # Show diff in every project with uncommited changes (-u)
$ workspace -u -- git diff
$
$ # Ask (-i) to stash changes
$ # in every project with uncommited changes (-u)
$ workspace -i -u -- git stash
```

```bash
$ # In every project that has branch 'master' (-s -b)
$ # checkout branch 'master' and pull changes
$ workspace -s -b master -- \
$     git checkout master '&&' git pull
$
$ # and then...
$ # In every project that has branch 'feature/some' (-s -b)
$ # checkout branch 'feature/some' and merge it with branch 'master'
$ workspace -s -b feature/some -- \
$     git checkout feature/some '&&' git merge master
```

```bash
$ # Open in bash every project that has 'engine' in its name (-p)
$ workspace open -p engine
```

```bash
$ # You can also type pattern with no additional options (just to save keystrokes),
$ # and script will match your pattern with both project names and branch names
$ workspace -- feature/some -- git push
$ workspace open engine
```

---

But, if you don't want to dive deep into yet another utility, just copy-paste (and adjust to your flavour) the following commands to recover your working session

```bash
$ # Ask (-i) to open in Gnome Terminal tabs (-A)
$ # every project that has 'feature' in its current branch name (-B)
$ workspace open -A -i -B feature
$
$ # Same, but in tmux panels (-X)
$ workspace open -X -i -B feature
```

```bash
$ # Open in Gnome Terminal tabs (-G)
$ # projects that have 'engine' in their current project name (-p)
$ workspace open -O -p engine
$
$ # Same, but in iTerm tabs (-I)
$ workspace open -I -p engine
```

# Features

* Compatible with bash 3.x (works on macOS)
* No external dependencies (uses only standard command line utilities)
* Supports autocompeltion for branch and project names
* Customizable (to some extend)
* Free for all ([MIT license](LICENSE.txt))


# Usage

`workspace` has standard actions, that specify what to do with projects. Projects are filtered by name or git repository state (branch name, uncommited changes, detached HEAD, or custom predicate).

### Actions

`status` – show git status.

`list` – list project names and paths.

`open` – open matched projects in shell (default), Apple Terminal, iTerm2, Gnome-Terminal tabs or tmux panels. When opening projects with bash, if you want to abort this action, you can exit with code 66. You can change this value with option `--abort-open-with <0..127>`.

`` (name not specified) – run command for each matched project. Command should be separeted with `--`.


### Predicates

* `<pattern>` – catchall pattern for both project names and local branch names (can save a couple of keystrokes)
* `-p`, `--project <pattern>` – project name pattern
* `-B`, `--branch <pattern>` – current branch name pattern
* `-b`, `--branches <pattern>` – branch name pattern for local branches
* `-r`, `--remote-branches <pattern>` – branch name pattern for remote branches
* `-a`, `--all-branches <pattern>` – branch name pattern for all branches
* `-T`, `--tag <pattern>` – current tag name pattern
* `-t`, `--tags <pattern>` – tag name pattern
* `-s`, `--strict` – match next pattern strictly
* `-n`, `--negate` – negate next predicate
* `-P`, `--predicate <command>` – use shell command as a predicate and its return value as a result
* `-N`, `--has-output <command>` – use shell command as a predicate and availability of its output as a result
* `-u`, `--uncommited` – match projects with uncommited changes
* `-U`, `--untracked` – match projects untracked files
* `-d`, `--detached` – match projects with detached HEAD
* `-D`, `--detached-only` – match projects with only detached HEAD


### Options

* `-W`, `--workspace-path <path>` – search path [default: current directory]
* `-O`, `--tabs` – open projects in current terminal tabs/panels (Apple terminal, Gnome terminal, iTerm, tmux)
* `-A`, `--apple-terminal` – open projects in macOS terminal tabs
* `-G`, `--gnome-terminal` – open projects in Gnome terminal tabs
* `-I`, `--iterm` – open projects in iTerm tabs
* `-X`, `--tmux` – open projects in tmux panels
* `-i`, `--interactive` – ask to do your stuff with every matched project
* `-g`, `--ignore-failed` – do not prompt to deal with error for failed commands in 'for each'

#

### Additional Options

* `--abort-open-with <0..127>` – abort 'open' action with exit status [default: 66]
* `--progress-bar` – show progress bar while searching projects
* `--no-progress-bar` – do not show progress bar while searching projects
* `--print-message <command>` – print message with command
* `--print-interaction <command>` – print interactions with command
* `--print-warrning <command>` – print warning with command
* `--print-error <command>` – print message with command
* `--colored-output` – use colored output.
* `--colored-interactions` – use colored output for interactions.
* `--colored-warnings` – use colored output for warnings.
* `--colored-errors` – use colored output for errors.
* `--colored-everything` – use colored output for everything.
* `--no-colored-output` – do not use colored output.
* `--no-colored-interactions` – do not use colored output for interactions.
* `--no-colored-warnings` – do not use colored output for warnings.
* `--no-colored-errors` – do not use colored output for errors.
* `--no-colored-anything` – do not use colored output for anything.

#

* `-h`, `--help` – show help message


### Additional actions

* `install-completion-script` – install bash completion script for project and branch names

	* `<path>` – path with shell completion scripts. Optional. Default: `/etc/bash_completion.d/`
	* `--split-project-names` – split project names into tokens.
 	* `--split-project-names` – split branch names into tokens.

* `cache-workspace-info` – update project paths cache

### Notes

Project name is considered to be the name of the folder that is parent to `.git` folder.

If no `-s` or `-S` option is set, then `<pattern>` is searched as a part of a project/branch name.

Use quotes for `|`, `;`, `&&` and `||` inside `<commands>` for `for each`.

If any `<command>` in `for each` failes during run, the script will prompt to open that project in shell, so you can deal with issue and run your command manually.



# Configuration

Configuration files can be stored at the following locations which are searched in the order of the list:

* `$PWD/.workspace-script/`
* `$HOME/.config/workspace-script/`
* `/etc/workspace-script/`

All configuration files can contain comments and empty lines.


### Implicit arguments

Implicit arguments is the way to configure script behavior. They are stored in `implicit-arguments` file. For example:

```bash
# let the cow say stuff
--print-message cowsay

# let the confused cow report errors
--print-error 'cowsay -f moose -e Oo -T "U "'

# let the abort open exit code be 73
--abort-open-with 73
```



### Ignoring paths

Ignored paths are stored in `ignore-paths` file and can use glob patterns. For example:

```bash
# ignoring external modules
*external*

# ignoring some specific project
specific-project

```



# Completion

There's a bash completion script, that you can install with `install-completion-script` action. It works for project and branch names when using options `-p`, `-B`, `-b`, `-r`, `-a`. On large amount of projects completion can be slow, so you can specify list of projects for completion script to track in the file `completion-paths`.



# Custom actions

Custom actions are stored in `custom-actions` file in configuration locations.

The syntax for defining custom action is the following:

```bash
<name> <options...> -- <command> <arguments...> # <description>
```

where

* `<name>` – name of the action
* `<options>` – options for workspace script
* `--` – mandatory delimiter
* `<command>` – command to be executed
* `<arguments>` – arguments for `<command>`
* `# <description>` – will be shown in the help message (if not specified, then `<command>`, `<options>` and `<arguments>` will be printed)

### Example:

Defining

```bash
commit -u -- git commit -a
push -- git push # push everything!
```

Using

```bash
$ # Plain and simple
$ workspace commit
$
$ # You can also pass workspace script options (-B master)
$ # and <command> arguments (-f) to your custom action
$ workspace push -B master -- -f
```

How it looks in the help message

```bash
$ workspace --help
USAGE
  workspace <action> [<options>...]
  
ACTIONS
  ...
  commit    $ workspace -u [<...>] -- git commit -a [<...>]
  push      push everything!
  ...
```
