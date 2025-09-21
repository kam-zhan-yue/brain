We can write short bash scripts or create custom commands known as aliases.

### Example

If we need to check connections to our web server often, we can use the `netstat` command. If a server is having issues with the connections to ports 80 or 443, we can check if there are any services listening on those ports and the number of connections to the port.

```shell
netstat -plant | grep '80\|443' | grep -v LISTEN | wc -l
```

To avoid that, we can create an alias, so rather than typing the whole command, we could just type a short command instead.

```shell
alias conn="netstat -plant | grep '80\|443' | grep -v LISTEN | wc -l'
```

Now, we can just run `conn`

	To make the change persistent, you need to add the `alias` to your shell profile line. This would be the `~/.bashrc` file. For some operating systems, this might be `~/.bash_profile`

To list all the available aliases, you can run

```shell
alias
```