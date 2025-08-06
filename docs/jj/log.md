`jj log` has very similar functionality to `git log`.

## Handy
```
jj log -r ::@
```

```
$ jj log
@  mpqrykyp martinvonz@google.com 2023-02-12 15:00:22 aef4df99
│  (empty) (no description set)
○  kntqzsqt martinvonz@google.com 2023-02-12 14:56:59 5d39e19d
│  Say goodbye
◆  orrkosyo octocat@nowhere.com 2012-03-06 15:06:50 master 7fd1a60b
│  (empty) Merge pull request #6 from Spaceghost/patch-1
~
```

The `@` indicates the working copy commit. The first ID on a line is the change ID. The second ID is the commit ID. You can give either ID to commands that take revisions as arguments. We will generally prefer change IDs because they stay the same when the commit is rewritten.

By default, `jj log` lists your local commits, with some remote commits added for context. The `~` indicates that the commit has parents that are not included on the graph.

We can use the `--revisions/-r` flag to select a different set of revisions to list. The flag accepts a "revset", which is an expression in a simple language for specifying revisions. For example, `@` refers to the working copy commit, `root()` refers to the root commit, `bookmarks()` refers to all commits pointed to by bookmarks (similar to Git's branches).

We can combine expressions with `|` for union, `&` for intersection, and `~` for difference.

```
$ jj log -r '@ | root() | bookmarks()'
@  mpqrykyp martinvonz@google.com 2023-02-12 15:00:22 aef4df99
│  (empty) (no description set)
~  (elided revisions)
◆  orrkosyo octocat@nowhere.com 2012-03-06 15:06:50 master 7fd1a60b
│  (empty) Merge pull request #6 from Spaceghost/patch-1
~  (elided revisions)
◆  zzzzzzzz root() 00000000
```

> Hint: If the default `jj log` omits some commits you expect to see, you can always run `jj log -r ::` to see all the commits