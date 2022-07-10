# Clink Gizmos

This is a collection of Lua scripts for use with [Clink](https://github.com/chrisant996/clink).

## How To Install

The easiest way to install it for use with your Clink is:

1. Make sure you have [git](https://www.git-scm.com/downloads) installed.
2. Clone this repo into a local directory via `git clone https://github.com/chrisant996/clink-gizmos local_directory`.
3. Tell Clink to load scripts from the repo via `clink installscripts local_directory`.
4. Start a new session of Clink.

## Files and Directories

The repo's root directory contains various useful scripts which are loaded when Clink is started.  They are discussed in the next section.

The "completions" subdirectory contains completion scripts for various commands, such as `attrib`, `curl`, `doskey`, `findstr`, [`less`](http://www.greenwoodsoftware.com/less/), [`premake5`](https://premake.github.io/), `robocopy`, and `xcopy`.

The "modules" subdirectory contains helper scripts that are used by the scripts in the other directories.

## Features

### auto_argmatcher.lua

### divider.lua

### fzf.lua

### i.lua

### luaexec.lua

### msbuild.lua

### z_dir_popup.lua

## Rough Prototype Features

Some of the included scripts are rough prototypes that can be useful, but are not fully functional and/or have potentially significant or dangerous limitations.  These prototype scripts are **disabled by default**, for safety and to avoid interference.  See below for information about each, and for how to enable each script if you wish.

### fishcomplete.lua

_Disabled by default.  To enable it, set the global variable `clink_gizmos_fishcomplete = true` in one of your Lua scripts that gets loaded before the clink-gizmos directory._

When a command is typed and it does not have an argmatcher, then fishcomplete automatically checks if there is a .fish file by the same name in the same
directory as the command program, or in the directory specified by the `fishcomplete.completions_dir` global variable.  If yes, then it attempts to parse the .fish file and create a Clink argmatcher from it.

The following global configuration variables in Lua control how this script functions:

Variable | Value | Description
-|-|-
`clink_gizmos_fishcomplete` | `true` or `false` | Set this to true to enable this script.  This script is disabled by default.
`fishcomplete.banner` | `true` or `false` | Whether to show feedback at top of screen when loading fish completions.
`fishcomplete.completions_dir` | A directory | Path to fish completions files.

> **Note:** The fishcomplete script does not yet handle the `-e`, `-p`, `-w`, or `-x` flags for the fish `complete` command.  It attempts to handle simple fish completion scripts, but it will likely malfunction with more sophisticated fish completion scripts.

### command_substitution.lua

_Disabled by default.  To enable it, set the global variable `clink_gizmos_command_substitutions = true` in one of your Lua scripts that gets loaded before the clink-gizmos directory._

This simulates very simplistic command substitutions similar to bash.  Any `$(command)` in a command line is replaced by the output from running the specified command.

For example, `echo $(date /t & time /t)` first runs the command `date /t & time /t` and replaces the `$(...)` with the output from the command.  Since the output is the current date and the current time, after command substitution the command line becomes something like `echo Sat 07/09/2022  11:08 PM`, and then finally the resulting command is executed.

The following global configuration variables in Lua control how this script functions:

Variable | Value | Description
-|-|-
`clink_gizmos_command_substitution` | `true` or `false` | Set this global variable to true to enable this script.  This script is disabled by default.

> **IMPORTANT WARNING:** This is a very simple and stupid implementation, and it does not (and cannot) work the same as bash.  It will not work quite as expected in many cases.  But if the limitations are understood and respected, then it can still be useful and powerful.

Here are some of the limitations:

- WHETHER a command substitution runs is different than in bash!
- The ORDER in which command substitutions run is different than in bash!
- Only a small subset of the bash syntax is supported.
- Nested substitutions are not supported; neither nested via typing nor nested via substitution.
- This spawns new cmd shells to invoke commands.  This means commands cannot affect the current shell's state:  changing env vars or cwd or etc do not affect the current shell.
- Newlines and tab characters in the output are replaced with spaces before substitution into the command line.
- CMD does not support command lines longer than a total length of about 8,000 characters.

Bash intelligently skips command substitutions that don't need to be performed, for example in an `else` clause that is not reached.  But this script stupidly ALWAYS performs ALL command substitutions no matter whether CMD will actually reach processing that part of the command line.

Bash intelligently performs command substitutions in the correct order with respect to other parts of the command line that precede or follow the command substitutions.  But this script stupidly performs ALL command substitutions BEFORE any other processing happens.  That means command substitutions can't successfully refer to or use outputs from earlier parts of the command line; because this script does not understand the rest of the command line and doesn't evaluate things in the right order.
