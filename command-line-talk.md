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

Normally, you'd have to specify a path to be able to run the executable.
However, there is a colon-separated list of paths stored in the
environment variable called `PATH`; if your command does not have a
path, the shell will search for the executable name in the paths listed
in `PATH` in order and it will run the first one it finds.

For example, if `~/bin` holds your personal scripts, you can call any of
your scripts without a path by adding `~/bin` to your `PATH`:

    export PATH="${HOME}/bin:${PATH}"

The above will add `~/bin` to the start of your `PATH`. Since `~/bin`
will be searched first, you should be careful not to accidentally shadow
important commands like `ls`.

Shadowing commands is a very good reason not to add `.` (the current
directory) to your `PATH`. You might unsuspectingly have a malicious
script in your current directory (e.g. from an untrusted tarball) that
is named the same as a frequently used command like `ls`. If `.` is in
your `PATH`, especially if it's first, you'll run the malicious
script instead of the real `ls`.


## Viewing files without an editor

To view text files in a terminal, the preferred way is to use a pager.
The pager included with most Unix-like systems is `less`.

You can pass the file as an argument to `less` to view the file. For
example, to view the file `/usr/share/dict/words`:

    less /usr/share/dict/words

You can also pipe command output to `less` to page its output. For
example, to view in `less` all the words in `/usr/share/dict/words` that
contain the substring `term`:

    grep 'term' /usr/share/dict/words | less

You can move around in `less` using your arrow keys or using vi-like
movements. Exit by pressing `q`.

`less` is a powerful pager with lots of features. Press `h` to see the
help or read the man page. Some of the more useful features are:

- `-S` flag: chop long lines
- `-R` flag: interpret ANSI escape sequences (useful for coloured output)
  - e.g. `grep --color=always 'term' | less -R`
- `/` and `?`: search forward and backward respectively
- `&`: show only matching lines
- `v`: open the file in `$VISUAL`/`$EDITOR`/`vi`
