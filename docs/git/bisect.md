```
$ git bisect start
$ git bisect bad                 # Current version is bad
$ git bisect good v2.6.13-rc2    # v2.6.13-rc2 is known to be good
```

You should now compile the checked-out version and test it. If that version works correctly, type

```
$ git bisect good
```

If that version is broken, type

```
$ git bisect bad
```