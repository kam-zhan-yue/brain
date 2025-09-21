## Syntax

An understanding of Bash starts with an understanding of the syntax. The full syntax for a Bash command is:

```bash
command [options] [arguments]
```

- Bash treats the first string it encounters as a a command.
- Arguments often accompany Bash commands
- Options, also called flags, give a command more specific instructions

```bash
ls
ls /etc
ls /etc -a
```

To learn about the options for a command, use the `man` (for "manual") comand.

```bash
man mkdir
```

## Wildcards

Wildcards are symbols that represent one or more characters in Bash commands. The most frequently used wildcard is the asterisk. It represents zero characters or a sequence of characters.

```bash
ls *.png
```

Here's one way to list all the JPEG files

```bash
ls *.jpg *.jpeg
ls *.jp*g
```
