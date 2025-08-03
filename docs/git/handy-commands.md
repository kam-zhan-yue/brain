## Capitalisation Problem

For some reason, git doesn't see the change between capitalisation of files. The below command fixes that.

```shell
git config core.ignorecase false
```

## Rebasing
To accept all incoming changes during a rebase, you can use the -X option with the theirs strategy. This option tells Git to resolve all conflicts by preferring changes from the branch you are rebasing onto.

```shell
git rebase -X theirs tw-6842/4-2-use-location
```

This command will start the rebase process and automatically resolve any conflicts by accepting the incoming changes from the `target-branch`.
## Rebasing, but lazy
```
git rebase main -Xtheirs
```
