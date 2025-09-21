To initialise an array, assign values divided by spaces and close in `()`

```shell
my_array=("value 1" "value 2" "value 3" "value 4")
```

To access the elements in the array, you need to reference them by their numeric index.

```shell
echo ${my_array[0]} # First element
echo ${my_array[-1]} # Last element
echo ${my_array[@]} # Returns all elements
echo ${#my_array[@]} # Returns total number of elements in the aray
```

Bash doesn't support true array slicing, you can achieve similar results by using a combination of array indexing and string slicing:

```shell
#!/bin/bash

array=("A", "B", "C", "D", "E")

# Print the entire array
echo "${array[@]}"

# Print a range of elements
echo "${array[@]:1:3}"

# Print from an index to the end
echo "${array[@]:3}"
```

String slicing is as follows

```shell
${string:start:length}
```