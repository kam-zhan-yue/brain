To execute/run a bash script file with the bash shell interpreter, the first line of a script file must indicate the absolute path to the bash executable.

```bash
#!/bin/bash
```

This is also called a Shebang

All that the shebang does is to instruct the operating system to run the script with the `/bin/bash` executable. However, bash is not always in the `/bin/bash` directory. You might want to use:

```bash
#!/usr/bin/env bash
```

It searches for the bash executable in directories, listed in PATH environmental variable.