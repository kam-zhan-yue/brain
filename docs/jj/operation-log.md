Jujutsu keeps a record of all changes you've made to the repo in what's called the 'operation log'. Use the `jj op` family of commands to interact with it.

`jj op log` lists all of the operations

```
$ jj op log
@  d3b77addea49 martinvonz@vonz.svl.corp.google.com 3 minutes ago, lasted 3 milliseconds
│  squash commits into f7fb5943a6b9460eb106dba2fac5cac1625c6f7a
│  args: jj squash
○  6fc1873c1180 martinvonz@vonz.svl.corp.google.com 3 minutes ago, lasted 1 milliseconds
│  snapshot working copy
│  args: jj st
○  ed91f7bcc1fb martinvonz@vonz.svl.corp.google.com 6 minutes ago, lasted 1 milliseconds
│  new empty commit
│  args: jj new puqltutt
○  367400773f87 martinvonz@vonz.svl.corp.google.com 12 minutes ago, lasted 3 milliseconds
│  rebase commit daa6ffd5a09a8a7d09a65796194e69b7ed0a566d and descendants
│  args: jj rebase -s puqltutt -d nuvyytnq
[many more lines]
```

The most useful command is `jj undo` which will undo an operation (defaulted to the most recent operation)

## Example

We've seen how `jj squash` can combine the changes from two commits into one. There are several other commands for changing the contents for existing commands.

Let's do the following:
```
$ jj new master -m abc; printf 'a\nb\nc\n' > file
Working copy  (@) now at: ztqrpvnw f94e49cf (empty) abc
Parent commit (@-)      : orrkosyo 7fd1a60b master | (empty) Merge pull request #6 from Spaceghost/patch-1
Added 0 files, modified 0 files, removed 1 files

$ jj new -m ABC; printf 'A\nB\nc\n' > file
Working copy  (@) now at: kwtuwqnm 6f30cd1f (empty) ABC
Parent commit (@-)      : ztqrpvnw 51002261 ab

$ jj new -m ABCD; printf 'A\nB\nC\nD\n' > file
Working copy  (@) now at: mrxqplyk a6749154 (empty) ABCD
Parent commit (@-)      : kwtuwqnm 30aecc08 ABC

$ jj log -r master::@
@  mrxqplyk martinvonz@google.com 2023-02-12 19:38:21 b98c607b
│  ABCD
○  kwtuwqnm martinvonz@google.com 2023-02-12 19:38:12 30aecc08
│  ABC
○  ztqrpvnw martinvonz@google.com 2023-02-12 19:38:03 51002261
│  abc
◆  orrkosyo octocat@nowhere.com 2012-03-06 15:06:50 master 7fd1a60b
│  (empty) Merge pull request #6 from Spaceghost/patch-1
~
```

We forgot to capitalise "c" in the second commit when we capitalised the other letters. We then fixed that in the third commit when we also added "D". It would be cleaner to move the capitalisation of "c" into the second commit.

We can do that by doing `jj squash -i` (interactive) option on the third commit. `jj squash` moves all the changes from one commit into its parent. `jj squash -i` moves only part of the changes into its parent

The child change (ABCD) will have the same content state after the `jj squash`. It just means you can move any changes you want into the parent change, even if they touch the same word, and it won't change any conflicts.

`jj diffedit` lets you edit changes in a commit without checking it out.