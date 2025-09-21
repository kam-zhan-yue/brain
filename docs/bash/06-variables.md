A variable in Bash can contain numbers as well as characters as there are no data types.

To assign a value to a name, use the equals sign.

```bash
name="Alex"
```

> You cannot have spaces before and after the `+` sign

To access the variable, you have to use the `$` and reference it as shown below:

```bash
echo $name
```

Wrapping the variable between curly brackets is not required, but it is considered good practice.

```bash
echo ${name}
```

You can also add variables in the CLI. We read the inputs as `${1}, ${2}, ..., ${n}`