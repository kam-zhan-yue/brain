#rc## Shell Plus into a Production Server

```jsx
tkf sp <server>
```

### Copy the contents of a DB

```jsx
tkf db copy -s source -d destination
```

### Backup DB

```jsx
tkf db backup <server>
```

### List DBs

```jsx
tkf db list-backups <server>
```

### Copy DB

```jsx
tkf db download-backup <server>
```

This will create a `.dump` in our uptick-cluster repository. Then, we need to move this file into our `var` folder and call

```jsx
inv dbreset -d <server>
```