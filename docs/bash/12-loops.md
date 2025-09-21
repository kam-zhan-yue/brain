## For Loops

The structure of a for loop is :

```shell
for var in ${list}
do
	<commands>
done
```

We can also use `for` to process a series of numbers.

```shell
#!/bin/bash

for num in {1..10}
do
	echo ${num}
done
```

## While Loops

The structure fo a while loop is similar to the `for` loop.

```shell
while [ your_condition ]
do
	<commands>
done
```

```shell
#!/bin/bash

counter=1
while [[ ${counter} -le 10 ]]
do
	echo ${counter}
	((counter++))
done
```

## Until Loops

```shell
until [[ your_condition ]]
do
    your_commands
done
```