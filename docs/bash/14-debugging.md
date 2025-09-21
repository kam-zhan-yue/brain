To debug your bash scripts, you can use `-x` when executing your scripts.

```shell
bash -x ./your_script.sh
```

Or you can use `set -x` before the specific line you want to debug. This enables a mode of the shell where all executed command are printed to the terminal.

A good way to test your scripts is using [https://www.shellcheck.net/](https://www.shellcheck.net/)

## Handy Commands

- Delete everything from the cursor to the end of the line:

```
Ctrl + k
```

- Delete everything from the cursor to the start of the line:

```
Ctrl + u
```

- Delete one word backward from cursor:

```
Ctrl + w
```

- Search your history backward. This is probably the one that I use the most. It is really handy and speeds up my work-flow a lot:

```
Ctrl + r
```

- Clear the screen, I use this instead of typing the `clear` command:

```
Ctrl + l
```

- Stops the output to the screen:

```
Ctrl + s
```

- Enable the output to the screen in case that previously stopped by `Ctrl + s`:

```
Ctrl + q
```

- Terminate the current command

```
Ctrl + c
```

- Throw the current command to background:

```
Ctrl + z
```
