## Introduction
[See article here](https://v5.chriskrycho.com/essays/jj-init/)
```
@  lltoxuws alexander.kam@uptickhq.com 2025-08-01 15:50:27 0324313f
│  (no description set)
○    srtosppy alexander.kam@uptickhq.com 2025-08-01 15:50:27 git_head() 113f65ba
├─╮  (empty) (no description set)
│ ○  umklmwpu alexander.kam@uptickhq.com 2025-08-01 15:50:27 2b4a067e
│ │  refactor(routes): run generate AccreditationRoutes
│ ○  smsstvlt alexander.kam@uptickhq.com 2025-08-01 15:29:19 tw-7122/2-generate 6bc7b14d
│ │  chore: run generate AccountsRoutes
│ ○  vslywvqs kamzhanyue@gmail.com 2025-08-01 15:29:10 0c4d6d9d
│ │  refactor(routes): run generate PropertiesRoutes
│ ○  ktyksrym kamzhanyue@gmail.com 2025-08-01 15:29:02 db85e81b
│ │  refactor(routes): run generate DashboardRoutes
○ │  povlwlvs alexander.kam@uptickhq.com 2025-08-01 15:23:48 tw-7122/2-exports fb0907bb
│ │  chore: expose password exports
○ │  ypqwpswr kamzhanyue@gmail.com 2025-08-01 15:22:50 546fc346
├─╯  refactor(general): expose HardRedirect component
```

- Create an empty change above the merges (srtosppy)
- When you create a commit, you can rebase it to the head of the branch using
```
jj rebase -r um -A sms
```

pre-commit run --all-files

## Rebasing Parents
[See article here](https://ofcr.se/jujutsu-merge-workflow)
First, make a merge commit using `jj new`
```
❯ jj new zoz qkl
Working copy now at: orllnptq 5ea75c06 (empty) (no description set)
Parent commit : zozvwmow ea93486e ssh-openssh | git: update error message for SSH error to stop referencing libssh2
Parent commit : qklyrnvv 579ecb73 push-qklyrnvvuksv | cli: print conflicted paths whenever the working copy is changed
Added 0 files, modified 14 files, removed 0 files
```

Now, the merge commit is at `orl`

To add a new parent from an existing commit, we can do 

```
❯ jj rebase -s orl -d "all:orl-" -d wtm
```

To rebase all parents, we can do 

```
❯ jj rebase -s 'all:roots(main..@)' -d main
```

To add a new parent (e.g. rwq) from new changes, say we have
```
❯ jj log
@ ovypxnus benjamin@dev.ofcr.se 1 minute ago e0c160c9
│ (empty) (no description set)
◉ rwqywnzl benjamin@dev.ofcr.se 1 minute ago HEAD@git 919fae76
│ new: avoid manual `unwrap()` call
◉ orllnptq benjamin@dev.ofcr.se 15 minutes ago 6e4f5799
├─┬─╮ (empty) (no description set)
```

And we want to make `rwq` a parent of `orl`. We do 
```
❯ jj rebase -s orl -d "all:orl-" -d rwq
❯ jj log@ ovypxnus benjamin@dev.ofcr.se 18 seconds ago 7f278c0d
│ (empty) (no description set)
◉ orllnptq benjamin@dev.ofcr.se 18 seconds ago HEAD@git 7b028dc9
├─┬─┬─╮ (empty) (no description set)
│ │ │ ◉ rwqywnzl benjamin@dev.ofcr.se 47 seconds ago 402f7ad8
│ │ │ │ new: avoid manual `unwrap()` call
```

### Removing Parents
We can use `jj rebase` to remove parents from a merge commit.

Previously, when adding new parents, we can specify the destinations using `-d. "all:orl" -d NEW_PARENT_ID`. Now we specify the destinations using `-d "all:orl- ~ qkl"`. The new argument for the destination highlights more of the revset. language, in particular the set difference operator. `orl-` evaluates to the set of all parents of `orl`, but `~ qkl` now subtracts `qkl` from that set.

```
❯ jj rebase -s orl -d "all:orl- ~ qkl"
Rebased 2 commits
Working copy now at: uyllouwm 521e9749 (empty) (no description set)
Parent commit : orllnptq 090ffb0d (empty) (no description set)
Added 0 files, modified 9 files, removed 0 files
```