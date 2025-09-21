You can pass arguments to your shell script when you execute it. To pass an argument, you just need to write it right after the name of your script.

```bash
#!/bin/bash

echo "Argument one is $1"
echo "Argument two is $2"
echo "Argument three is $3"
```

```shell
./arguments.sh dog cat bird
```

To reference all arguments, you can use `$@`

```bash
#!/bin/bash

echo "All arguments: $@
```

You can use `$0` to reference the script itself.

```bash
#!/bin/bash

echo "The name of the file is: $0 and it is going to be self-deleted."

rm -f $0
```