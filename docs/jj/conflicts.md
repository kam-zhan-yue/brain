## Workflow
- Do your `jj rebase` 
- Checkout the change that has the conflict with `jj new`
- Resolve the conflict
- Squash the conflict with `jj squash`
- Check whether it's alright with `jj log`


## Example

We can make commits using `jj new` and the `--message / -m` option to set change descriptions right away.

```
# Start creating a chain of commits off of the `master` bookmark
$ jj new master -m A; echo a > file1
Working copy  (@) now at: nuvyytnq 00a2aeed (empty) A
Parent commit (@-)      : orrkosyo 7fd1a60b master | (empty) Merge pull request #6 from Spaceghost/patch-1
Added 0 files, modified 1 files, removed 0 files
$ jj new -m B1; echo b1 > file1
Working copy  (@) now at: ovknlmro 967d9f9f (empty) B1
Parent commit (@-)      : nuvyytnq 5dda2f09 A
$ jj new -m B2; echo b2 > file1
Working copy  (@) now at: puqltutt 8ebeaffa (empty) B2
Parent commit (@-)      : ovknlmro 7d7c6e6b B1
$ jj new -m C; echo c > file2
Working copy  (@) now at: qzvqqupx 62a3c6d3 (empty) C
Parent commit (@-)      : puqltutt daa6ffd5 B2
$ jj log
@  qzvqqupx martinvonz@google.com 2023-02-12 15:07:41 2370ddf3
│  C
○  puqltutt martinvonz@google.com 2023-02-12 15:07:33 daa6ffd5
│  B2
○  ovknlmro martinvonz@google.com 2023-02-12 15:07:24 7d7c6e6b
│  B1
○  nuvyytnq martinvonz@google.com 2023-02-12 15:07:05 5dda2f09
│  A
│ ○  kntqzsqt martinvonz@google.com 2023-02-12 14:56:59 5d39e19d
├─╯  Say goodbye
◆  orrkosyo octocat@nowhere.com 2012-03-06 15:06:50 master 7fd1a60b
│  (empty) Merge pull request #6 from Spaceghost/patch-1
~
```

We now have a few commits, where A, B1, and B2 modify the same file, while C modifies a different file. Let's now rebase B2 directly onto A. We can use the `--source/-s` option on the change ID of B2 and `--destination/-d` option on A

```
$ jj rebase -s puqltutt -d nuvyytnq  # Replace the IDs by what you have for B2 and A
Rebased 2 commits to destination
Working copy  (@) now at: qzvqqupx 1978b534 (conflict) C
Parent commit (@-)      : puqltutt f7fb5943 (conflict) B2
Added 0 files, modified 1 files, removed 0 files
Warning: There are unresolved conflicts at these paths:
file1    2-sided conflict
New conflicts appeared in 2 commits:
  qzvqqupx 1978b534 (conflict) C
  puqltutt f7fb5943 (conflict) B2
Hint: To resolve the conflicts, start by creating a commit on top of
the first conflicted commit:
  jj new puqltutt
Then use `jj resolve`, or edit the conflict markers in the file directly.
Once the conflicts are resolved, you can inspect the result with `jj diff`.
Then run `jj squash` to move the resolution into the conflicted commit.

$ jj log
@  qzvqqupx martinvonz@google.com 2023-02-12 15:08:33 1978b534 conflict
│  C
×  puqltutt martinvonz@google.com 2023-02-12 15:08:33 f7fb5943 conflict
│  B2
│ ○  ovknlmro martinvonz@google.com 2023-02-12 15:07:24 7d7c6e6b
├─╯  B1
○  nuvyytnq martinvonz@google.com 2023-02-12 15:07:05 5dda2f09
│  A
│ ○  kntqzsqt martinvonz@google.com 2023-02-12 14:56:59 5d39e19d
├─╯  Say goodbye
◆  orrkosyo octocat@nowhere.com 2012-03-06 15:06:50 master 7fd1a60b
│  (empty) Merge pull request #6 from Spaceghost/patch-1
~
```

- The `jj rebase` command said "Rebased 2 commits" because we asked it to rebase commit B2 with the `-s` option, which also rebases descendants (commit C in this case).
- B2 modified the same file (and word) as B1, rebasing it resulted in conflicts. 
- The conflicts did not prevent the rebase from completing successfully, nor did it prevent C from getting rebased on top

In order to resolve the conflict in B2, we can create a new commit on top of B2. Once we've resolved the conflict, we squash the conflict resolution into the conflicted B2.

```
$ jj new puqltutt  # Replace the ID by what you have for B2
Working copy  (@) now at: zxoosnnp c7068d1c (conflict) (empty) (no description set)
Parent commit (@-)      : puqltutt f7fb5943 (conflict) B2
Added 0 files, modified 0 files, removed 1 files
Warning: There are unresolved conflicts at these paths:
file1    2-sided conflict

$ jj st
The working copy has no changes.
Working copy  (@) : zxoosnnp c7068d1c (conflict) (empty) (no description set)
Parent commit (@-): puqltutt f7fb5943 (conflict) B2
Warning: There are unresolved conflicts at these paths:
file1    2-sided conflict
Hint: To resolve the conflicts, start by creating a commit on top of
the conflicted commit:
  jj new puqltutt
Then use `jj resolve`, or edit the conflict markers in the file directly.
Once the conflicts are resolved, you can inspect the result with `jj diff`.
Then run `jj squash` to move the resolution into the conflicted commit.

$ cat file1
<<<<<<< Conflict 1 of 1
%%%%%%% Changes from base to side #1
-b1
+a
+++++++ Contents of side #2
b2
>>>>>>> Conflict 1 of 1 ends

$ echo resolved > file1

$ jj st
Working copy changes:
M file1
Working copy  (@) : zxoosnnp c2a31a06 (no description set)
Parent commit (@-): puqltutt f7fb5943 (conflict) B2
Hint: Conflict in parent commit has been resolved in working copy

$ jj squash
Rebased 1 descendant commits
Working copy  (@) now at: ntxxqymr e3c279cc (empty) (no description set)
Parent commit (@-)      : puqltutt 2c7a658e B2
Existing conflicts were resolved or abandoned from 2 commits.

$ jj log
@  ntxxqymr martinvonz@google.com 2023-02-12 19:34:09 e3c279cc
│  (empty) (no description set)
│ ○  qzvqqupx martinvonz@google.com 2023-02-12 19:34:09 b9da9d28
├─╯  C
○  puqltutt martinvonz@google.com 2023-02-12 19:34:09 2c7a658e
│  B2
│ ○  ovknlmro martinvonz@google.com 2023-02-12 15:07:24 7d7c6e6b
├─╯  B1
○  nuvyytnq martinvonz@google.com 2023-02-12 15:07:05 5dda2f09
│  A
│ ○  kntqzsqt martinvonz@google.com 2023-02-12 14:56:59 5d39e19d
├─╯  Say goodbye
◆  orrkosyo octocat@nowhere.com 2012-03-06 15:06:50 master 7fd1a60b
│  (empty) Merge pull request #6 from Spaceghost/patch-1
~
```

Commit C got automatically rebased on top of the resolved B2, and C is also resolved.