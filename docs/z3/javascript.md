This is a formulate that establishes there is no number both above 9 and below 10.

```javascript
const x = Z3.Int.const('x')
Z3.solve(Z3.And(x.ge(10), x.le(9)))

> unsat
```

Javascript bindings wrap z3 expressions into Javascript options that support methods for building new expressions. The Z3.solve method takes a sequence of predicates and checks if there is a solution. If there is a solution, it returns a model.

## Integer Arithmetic

Solves `x > 2 and y < 10 and x + 2y = 7`
```javascript
const x = Z3.Int.const('x')
const y = Z3.Int.const('y')
Z3.solve(x.gt(2), y.lt(10), x.add(y.mul(2).eq(7)))
```