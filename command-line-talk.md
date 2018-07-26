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


## Traversing directories

Your shell has the concept of a working directory, which is what all
relative paths are relative to.

To view your current working directory, use the `pwd` command ("present
working directory).

    pwd

To change your working directory, use the `cd` command ("change
directory"). `cd` takes a path, either absolute or relative, which is
what you want your working directory to be. For example, to change your
working directory to the `mydir` directory:

    cd mydir

You can `cd` into directories multiple levels deep as well:

    cd mydir/mysubdir/mysubsubdir

The parent directory is denoted by `..` and can be used as a component
of a path. For example, to go up two directories:

    cd ../..

Your home directory is denoted by `~` and can be used as the first
component of a path. For example, to go into the `Documents` directory
inside your home directory:

    cd ~/Documents

Simply doing `cd` with no arguments takes you to your
home directory:

    cd

Your shell remembers the last directory you were in. You can `cd` there
by passing `-`:

    cd -

If you want to remember more than that, you can make use of the
directory stack, managed by the `pushd`, `popd` and `dirs` commands. The
`pushd` command will push the current directory to the directory stack
and change to the directory you specify. The `popd` command changes to
the directory on the top of the stack and pops it off the stack. The
`dirs` command will show you the contents of the stack, with the
left-most directory being the top of the stack.

    pushd ~
    pwd
    pushd /
    pwd
    dirs
    popd
    popd
    pwd

### What's the difference between a directory and a folder?

The term "folder" is a higher-level concept than "directory". A folder
is an object that holds documents, whereas a directory is an object that
lists documents. The term "folder" tends to be used for GUI objects and
"directory" in the command line.


## Running a command in multiple directories

To run the same command in multiple directories, you can use a `for`
loop. The general idea is this: for each directory, `cd` into the
directory and run your commands. The following will run `npm install` in
`dir1`, `dir2` and `dir3`:

    for dir in dir1 dir2 dir3; do (cd "$dir" && npm install); done

In this example, `dir` is the name of the variable that will hold the
directory in each iteration. `dir1`, `dir2` and `dir3` are the paths to
the directories in which you want to run the command. The body of the
`for` loop, `(cd "$dir" && npm install)` runs the command in a subshell
(the `()` denotes a subshell and is not part of the loop structure) so
that the effect of `cd` is not seen outside the subshell. If your
commands require setting a variable, you can't use a subshell since
changes to variables aren't seen outside subshells; use `pushd` and
`popd` instead.

If you want to run a command in all directories directly under the
current directory, you can use the `*` glob to match everything (note
that this will not catch hidden directories):

    for dir in *; do (cd "$dir" && npm install); done

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


## Reading from and writing to files

Programs have a number of file descriptors from which they can read and
to which they can write. These file descriptors are identified by
numbers. Three of them are noteworthy:

- 0 (stdin, for reading),
- 1 (stdout, for output), and
- 2 (stderr, for error output).

To write the output of a command to a file, use the output redirect
operator, `>`, which takes a file path. It will create the file if it
doesn't exist or truncate it (remove the content) if it does exist, then
write the program's output to the file. This effectively overwrites the
file.

For example, to save all the words in the dictionary that start with "a"
to the file `awords`:

    grep '^a' /usr/share/dict/words >"awords"

You'll often see a space after the `>` in this case, which is also
valid. You might opt not to add a space for consistency with the other
uses of the redirect operator.

If you want to append to a file instead, use the append operator, `>>`.

    grep '^a' /usr/share/dict/words >>"awords"

By default, the file descriptor for the output redirect operators is 1,
stdout. To capture stderr instead, put its file descriptor number, 2,
immediately before it. This works for all other open file descriptors
set for writing. For example, to capture the error messages for `ls` on
a directory you don't have permission to read to the file
`error-message`:

    ls /root 2>"error-message"

If you want to send both stdout and stderr to a file, redirect stdout to
a file, then redirect stderr to stdout. To redirect one file descriptor,
_n_, to another file descriptor, _m_, do `n>&m`. Do not add spaces.

    ls ~ /root >"output" 2>&1

Note that the order matters. If you reverse the order and do `2>&1 >"output"` instead,
stderr will first redirect to what stdout points to (the terminal), then
stdout will be redirected to the file; stderr will output to the
terminal and not to the file.

To read a file into stdin, use the input redirect operator, `<`, which
also takes a file path. For example, to uppercase the contents of the
file `text` read from stdin:

    tr 'a-z' 'A-Z' <"text"

You can also combine redirect operators. This will uppercase the
contents of the file `text` read from stdin and write the output to the
file `uppercased`:

    tr 'a-z' 'A-Z' <"text" >"uppercased"

### Useless use of `cat`

Doing `cat file | command` is often called a useless use of `cat`. This
is usually fine for interactive use because you're only running it once,
but you should avoid it especially for scripts. Using `cat` in a
pipeline like that no only spawns a process for `cat`, but it also
spawns a subshell, which may lead to unintended results if you're
setting variables.

Commands often take in a file to read as an argument, as is the case for
`grep`:

    grep '^a' /usr/share/dict/words

If not, you can use the input redirect operator, like in the case for
`tr`:

    tr 'A-Z' 'a-z' </usr/share/dict/words

If you're using `cat` to have the file at the beginning, you can move
the redirect operator to the beginning. Where you put the redirect
doesn't matter.

    </usr/share/dict/words tr 'A-Z' 'a-z'

## Viewing files without an editor

If the file is short, you can use `cat` to print the contents of the
file to stdout. This is a bit of a misuse of the command since its
purpose is to concatenate files, but it's a convenient side effect.

    cat shortfile

If you only want to see the first few lines or the last few lines, you
can use the `head` and `tail` commands. The default is 10 lines, but
that can be changed by passing the `-n` flag with a number.

    head /usr/share/dict/words
    tail /usr/share/dict/words

The preferred way to properly view a file is to use a pager.
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


## Getting help

Many commands will take a `--help` flag and print a short usage
message.

For example, to view the various flags of `ls`:

    ls --help

For more information, look up the command's manual page using the `man`
command. For `ls`:

    man ls

You can get a one-line description of a command using `man -f` or
`whatis`:

    man -f ls
    whatis ls

You can also search these one-line descriptions using `man -k` or
`apropos`:

   man -k "list"
   apropos "list"

On Linux, a more comprehensive manual may be read using the `info`
command, especially for GNU utilities. If there is no `info` manual for
the command, it will fall back to the command's man page.

    info ls
