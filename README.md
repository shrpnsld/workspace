# workspace

Simplifies workflow with projects that have several active repositories.

By batching commands for filtered repositories, this script allows to avoid using several terminal tabs or tmux panels in large projects with big amount of repositories, that often have dependent changes *(or at least it can help you to restore your working session – terminal tabs or tmux panels...)*.



# Examples

```bash
$ # For each project with uncommited changes (-u) run 'git status'
$ workspace status -u
$
$ # For each project with uncommited changes (-u)
$ # create branch 'feature/nice-69' and commit changes
$ workspace -u -- git checkout -b feature/nice-69 '&&' git commit -a
```

```bash
$ # For each project with branch 'feature/nice-69' (-s -b)
$ # merge latest changes in 'master' into 'feature/nice-69'
$ workspace -s -b feature/nice-69 -- \
$     git checkout master '&&' git pull \
$     '&&' git checkout feature/nice-69 '&&' git merge master
```
```bash
$ # You can also type string pattern without options (just to save keystrokes),
$ # and the script will match it with both project name and branch name
$ workspace 69 -- git push
```
```bash
$ # For each project with 'engine' in its name (-p) open terminal tab or tmux panel (-O)
$ workspace open -p engine -O
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
* `-U`, `--untracked` – match projects with untracked files
* `-d`, `--detached` – match projects with detached HEAD
* `-D`, `--detached-only` – match only projects with detached HEAD


### Options

* `-W`, `--workspace-path <path>` – search path [default: current directory]
* `-O`, `--tabs` – open projects in current terminal tabs/panels (macOS terminal, Gnome terminal, iTerm, tmux)
* `-M`, `--macos-terminal` – open projects in macOS terminal tabs
* `-G`, `--gnome-terminal` – open projects in Gnome terminal tabs
* `-I`, `--iterm` – open projects in iTerm2 tabs
* `-X`, `--tmux` – open projects in tmux panels
* `-i`, `--interactive` – ask to execute action for every matched project
* `-g`, `--ignore-failed` – do not prompt to deal with the error for failed commands in 'for each' action

#

### Additional Options

####

* `--clear-screen` – clear screen before printing main output
* `--progress-bar` – show progress bar while searching projects
* `--no-progress-bar` – do not show progress bar while searching projects
* `--abort-open-with <0..127>` – abort 'open' action with exit status [default: 66]

####

* `--print-message <command>` – print message with specified command
* `--print-interaction <command>` – print interactions with specified command
* `--print-warrning <command>` – print warning with specified command
* `--print-error <command>` – print message with specified command

####

* `--colored-output` – color output
* `--colored-interactions` – color interactions
* `--colored-warnings` – color warnings
* `--colored-errors` – color errors
* `--colored-everything` – all of the above

####

* `--no-colored-output` – do not color output
* `--no-colored-interactions` – do not color interactions
* `--no-colored-warnings` – do not color warnings
* `--no-colored-errors` – do not color errors
* `--no-colored-anything` – all of the above

####

* `-h`, `--help` – show help message

#

### Additional actions

* `install-completion-script` – install bash completion script for project and branch names

	* `--path` – path with shell completion scripts. Optional. Default: `/etc/bash_completion.d/`
	* `--split-project-names` – split project names into tokens.
 	* `--split-branch-names` – split branch names into tokens.

* `cache-workspace-info` – update project paths cache

#

### Notes

Project name is considered to be the name of the folder that is parent to `.git` folder.

If no `-s` or `-S` option is set, then `<pattern>` is searched as a substring of a project/branch name.

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
* `<options>` – workspace script options
* `--` – mandatory delimiter
* `<command>` – command to be executed
* `<arguments>` – arguments for the `<command>`
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
