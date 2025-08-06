[See documentation here](https://jj-vcs.github.io/jj/latest/bookmarks/)

## Bookmark Create
```
jj bookmark create <name>
```

```
$ # List all available bookmarks, as we want our colleague's bookmark.
$ jj bookmark list --all
$ # Find the bookmark.
$ # [...]
$ # Actually track the bookmark.
$ jj bookmark track <bookmark name>@<remote name> # Example: jj bookmark track my-feature@origin
$ # From this point on, <bookmark name> will be imported when fetching from <remote name>.
$ jj git fetch --remote <remote name>
$ # A local bookmark <bookmark name> should have been created or updated while fetching.
$ jj new <bookmark name> # Do some local testing, etc.
```

## Bookmark Rename
```
jj bookmark rename OLD NEW
```

## Bookmark Move
```
jj bookmark move [OPTIONS] <NAMES|--from <REVSETS>> -t 
```

## Bookmark Forget
```
jj bookmark forget [OPTIONS] <NAMES>...
```