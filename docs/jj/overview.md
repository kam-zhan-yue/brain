## Installation
```
brew install jj
```

```
$ jj config set --user user.name "Alex Kam"
$ jj config set --user user.email "kamzhanyue@gmail.com"
```

```
autoload -U compinit
compinit
source <(jj util completion zsh)
```

## Introduction
After cloning the Hello World repository, the `jj st` shows

```
$ jj st
The working copy has no changes.
Working copy  (@) : kntqzsqt d7439b06 (empty) (no description set)
Parent commit (@-): orrkosyo 7fd1a60b master | (empty) Merge pull request #6 from Spaceghost/patch-1
```

There are two commits: Parent and working copy. Both are identified using two separate identifiers: the "change ID" and the "commit ID"

The parent commit, for example, has the change ID `orrkosyo` and the commit ID `7fd1a60b`. The change ID is a concept unique to Jujutsu

The working copy is an actual commit with a commit ID. When you make a change in the working copy, the working-copy commit gets automatically amended by the next `jj` command.

> This is a huge difference fro mGit where the working copy is a separate concept and not yet a commit