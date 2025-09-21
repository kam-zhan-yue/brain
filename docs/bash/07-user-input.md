```bash
#!/bin/bash

echo "What is your name?"
read name
echo "Hi there ${name}"
```

To reduce the code, we can do:
```bash
read -p "What is your name? " name
echo "Hi there ${name}"
```