[See documentation here](https://jj-vcs.github.io/jj/latest/revsets/)
JJ supports a functional language for selecting a set of revisions. Expressions in this language are called "revsets". The language consists of symbols, operators, and functions.

Most `jj` commands accept a revset, or multiple.

## Hidden Revisions
Most revsets search only the visible commits. Other commits are only included if you explicitly mention them (e.g. by commit ID, `<name>@<remote>` symbol, or `at_operation()` function).

If hidden commits are specified, their ancestors also become available to the search space. They are included in `all()`, `x..`, etc, but not in `..visible_heads()`. For example, `hidden_id | all()` is equivalent to `hidden_id | ::(hidden_id | visible_heads())`

## Symbols
- The `@` expression refers to the working copy commit in the current workspace. Use `<workspace name>@` to refer to the working-copy commit in another workspace. Use `<name>@<remote>` to refer to a remote-tracking bookmark.
- A full commit ID refers to a single commit. A unique prefix of the full commit ID can also be used.
- A full change ID refers to all visible commits with that change ID. A unique prefix of the full change ID can also be used.
- Use single or double quotes to prevent a symbol from being interpreted as an expression. E.g. `"x-"` is the symbol "x-", not the parents of symbol `x`

**Priority**
JJ attempts to resolve a symbol in the following order
1. Tag Name
2. Bookmark Name
3. Git Ref
4. Commit ID or change ID