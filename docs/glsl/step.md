Generates a step function by comparing two values
```c++
float step(float edge, float x)
```

- edge specifies the location of the edge of the step function
- x specifies the value to be used to generate the step function

If x < edge, then return 0, edge return 1