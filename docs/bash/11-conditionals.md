## `if` statements

The format of an `if` statement is as follows:

```bash
if [[ some_test ]]
then
	<commands>
elif
	<commands>
else
	<commands>
fi
```

You can test multiple conditions with an `if` statement.

In this example, we want to make sure that the user is neither the admin user nor the root user to ensure the script is incapable of causing too much damage.

```shell
#!/bin/bash

admin="kamzhanyue"

read -p "Enter your username? " username

if [[ ${username} == ${admin} ]] ; then
	echo "You are the admin user!"
else
	echo "You are not welcome here."
fi
```

## `switch` statements

```shell
case ${some_variable} in
	pattern_1)
		<commands>
		;;
	pattern_2)
		<commands>
		;;
	*)
		<default_commands>
		;;
esac
```

An example is

```shell
read -p "Enter the name of your car brand: " car

case ${car} in
	Tesla)
		echo -n "${car}'s factory is in the USA"
		;;
	BMW | Mercedes | Audi | Porsche)
		echo -n "${car}'s factory is in Germany"
		;;
	Toyota | Mazda | Mitsubishi | Subaru)
		echo -n "${car}'s factory is in Japan"
		;;
esac
```