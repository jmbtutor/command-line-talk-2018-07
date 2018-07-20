# Command Line Tips

## Keybindings

Keybindings are provided by the readline utility and use emacs bindings
by default.

<http://readline.kablamo.org/emacs.html>

The ones I use most often are:

- Ctrl-a: Go to the beginning of the line
- Ctrl-e: Go to the end of the line
- Meta-f: Go forward one word
- Meta-b: Go back one word
- Meta-d: Cut forward one word into the kill-ring
- Meta-Backspace: Cut backwards one word into the kill-ring
- Ctrl-w: Cut backwards until the next space into the kill-ring
- Ctrl-k: Cut from the cursor to the end of the line into the kill-ring
- Ctrl-u: Cut from the cursor to the beginning of the line into the
          kill-ring
- Ctrl-y: Paste the contents of the kill-ring
- Ctrl-\_: Undo

The Meta key is usually mapped to the `Alt` key. On a macOS, use `Esc`
instead (tap, don't hold), or enable the [setting][meta osx] to use the
option key as the meta key.

[meta osx]: http://osxdaily.com/2013/02/01/use-option-as-meta-key-in-mac-os-x-terminal/


## Running a command in multiple directories

## Environment variables

Environment variables provide a simple way to pass data to an application.

You can see all the environment variables defined for your current shell
using the `env` command:

    env

Some important environment variables are:

- `PATH`: the search path for executables
- `VISUAL` and `EDITOR`: the path your preferred editor
- `HOME`: the path to your home directory
- `PWD`: the path of your current working directory
- `SHELL`: the name of your current shell
- `PS1`: the format of your prompt

You can access the contents of a variable by prepending a `$`. For
example, to print out the contents of the `PATH` environment variable:

    echo "$PWD"

Do this to set a variable:

    EDITOR="/usr/bin/vim"

This will put the string `/usr/bin/vim` into the `EDITOR` variable. Note
that variables are all strings and it is up to the application to parse
it as another type (e.g. number) if it needs it.

However, this will only make it available to the current shell. To make
it available to any spawned processes, you must export it:

    export EDITOR

Note that you don't add a `$` since you want to refer to the variable
itself, not its contents.

To make this permanent, add it to your shell's config file.

To temporarily set an environment variable for the current command,
prepend the command with variable assignments or use the `env` command.
There is a script called `echo-editor` in the `scripts` folder that will
echo out the value of the `EDITOR` variable.

    EDITOR=/usr/bin/nano ./scripts/echo-editor

To delete a variable, use the `unset` command:

    unset EDITOR

## Executables

Commands like `ls` and `cp` aren't built-in commands. They're external
binaries found in the filesystem.

## Viewing files without an editor


