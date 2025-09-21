## `ls` command

Lists all the contents of your directory or the directory specified in an argument to the command. By itself, it lists the files and directories in the current directory.

```bash
ls
```

## `cat` command

You can use the `cat` command to see what's inside of a file.

## `sudo` command

Some Bash commands can only be run by the root user, a system administrator or superuser. If you try one of these commands without sufficient privileges, it fails. For example, only users logged in as a superuser can use `cat` to display the contents of `/etc/at.deny`

```bash
sudo cat /etc/at.deny
```

## `cd`, `mkdir`, and `rmdir` commands
- `cd` stands for "change directory" and changes the current directory to another
- `mkdir` stands for "make directory" and creates directories
- `rmdir` removes a directory only if it's empty

## `rm` command
The `rm` command is short for "remove".
- Running `rm` with an `-i` flag lets you think before you delete
- The `-r` flag allows you to delete a directory even if it is not empty
- The `-f` flag "forces" the command by suppressing prompts
- The `rm -rf /` deletes every file on an entire drive

## `cp` command
The `cp` command copies not just files, but entire directories (and subdirectories) if you want.

To copy all the files in a subdirectory named "photos" into a subdirectory called "images", do:

```bash
cp photos/* images
```

## `ps` command
The `ps` command gives you a snapshot of all the currently running processes. It shows all the shell processes, but `-e` shows all running processes.

```bash
ps -e
ps aux
```

## `w` command
When an employee leaves to pursue other opportunities, the sysadmin is called upon to ensure that the worker can no longer sign into the company's computer systems. They are also expected to know who's logged in, and who shouldn't be.

To find out who's on. your servers, `w` ("who") displays information about the users currently on the computer system and those user's activities. `w` shows user names, their IP addresses, when they logged in, what processes they're currently running, and how much time those processes are consuming.


## Bash I/O Operators

You can do a lot in Linux by exercising Bash commands and their options. You can combine them using I/O operators:
- `<` for redirecting input to a source other than the keyboard
- `>` for redirecting output to a destination other than the screen
- `>>` for doing the same, but appending rather than overwriting
- `|` for piping output from one command to the input of another

The following lists everything in the current directory and captures the output in a file.
```bash
ls > listing.txt
```

If listing.txt already exists, it gets overwritten, use the `>>` operator to append instead.
```bash
ls >> listing.txt
```

The piping operator is powerful (and often used). 

You can use `more` to pause when the screen is full until you select `Enter` to show the next line.

```bash
px aux | more
```

You can use `head` to see just the first several lines.

```bash
ps aux | head
```

Or suppose you want to filter the output to include only the lines that include the word "daemon". One way to do that is by piping the output from `ps` to Linux's useful `grep` tool.

```bash
ps aux | grep daemon
```

## Killing a Process

Find `ps aux | grep {process}` to find the PID for the bad process.

The `kill` command kills a running process based on its PID. When you call `kill`, you have to decide what kind of "signal" to use to kill the process.

To list all the signal types:

```bash
kill -l
```

If you were killing a daemon process (one that runs in the background and provides vital services to the operating system), you might want to kill it and immediately restart it. You would use the `SIGHUP` signal, which corresponds to the number 0.

```bash
kill -9 PID
```