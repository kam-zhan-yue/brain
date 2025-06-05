### Install Git LFS

Make sure you have git lfs installed

```rust
git lfs install
```

[git: 'lfs' is not a git command unclear](https://stackoverflow.com/questions/48734119/git-lfs-is-not-a-git-command-unclear)

### Add Git Attributes

```rust
vim .gitattributes
```

### Troubleshooting

If you add the file type to .gitattributes but it is not showing, you might need to run

```rust
git add --renormalize --all
```

```python
git lfs track "*.jpg" "*.png"
```

### Another Command
```shell
cat .gitattributes
git add --all
git lfs ls-files
```

> NOTE! `git lfs ls-files` only works after you have added the files to the repository with `git add`
