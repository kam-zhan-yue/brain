A change is a commit that can evolved while keeping a stable identifier. In other words, you can make changes to files in a change, resulting in a new commit hash, but the change ID will remain the same.

The clone operation makes a new change like so:
```
Working copy  (@) : kntqzsqt d7439b06 (empty) (no description set)
```

This new change has the ID `kntqzsqt` and it is currently empty (contains no changes compared to the parent) and has no description.

## Example Change

Let's say we want to change the README file in the repo to say "Goodbye" instead of "Hello". We start by describing the change (adding a commit message) so we don't forget what we're working on:

```
jj describe
// Then write down what you want to change
```

Note that you didn't have to tell Jujutsu to add the change like you would with `git add`. You don't even need to tell it when you add new files or remove existing files.

The commit hash also changes.

To see the diff, run `jj diff`

The working copy's commit's ID changed both when we edited the description and when we edited the README. However, the parent commit stayed the same. Each change to the working copy commit amends the previous version. So how do we tell Jujutsu that we are done amending the current change and want to start working on a new one?

`jj new` will create a new commit on top of your current working-copy commit. This new commit is for the working-copy changes.

```
$ jj new
Working copy  (@) now at: mpqrykyp aef4df99 (empty) (no description set)
Parent commit (@-)      : kntqzsqt 5d39e19d Say goodbye
$ jj st
The working copy has no changes.
Working copy  (@) : mpqrykyp aef4df99 (empty) (no description set)
Parent commit (@-): kntqzsqt 5d39e19d Say goodbye
```

If we later realise that we want to make further changes, we can make them in the working cop and then run `jj squash`. That command squashes (moves) the changes from a given commit into its parent commit.

Alternatively, we can use `jj edit <commit>` to resume editing a commit in the working copy. Any further changes in the working copy will then amend the commit. Whether you choose to create a new change and squash, or to edit, typically depends on how done you are with the change. If the change is almost done, it makes sense to use `jj new` so you can easily review your adjustments with `jj diff` before running `jj squash`

To view how a change has evolved over time, we can use `jj evolog` to see each recorded change for the current commit. This records changes to the working copy, messages, squashes, rebases, etc.

