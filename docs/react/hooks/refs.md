[See documentation here](https://react.dev/learn/referencing-values-with-refs)

When you want a component to "remember" some information, but you don't wnat that information to trigger new renders, you can use a ref.

The value of `ref.current` is intentionally mutable. Ref can point to anything, a string, an object, or even a function. Ref is a plain Javascript object with the `current` property that you can read and modify.

A component using a ref doesn't re-render every time it changes. Like state, refs are retained by React between re-renders. However, setting a state re-renders a component. Changing a ref does not.

### When to use refs

Typically, you will use a ref when your component needs to "step outside" React and communicate with external APIs. For instance:
-  Storing timeout IDs
- Storing and manipulating DOM elements
- Storing other objects that aren't necessary to calculate the JSX

If your component needs to store some value, but it doesn't impact the rendering logic, choose refs.

### Best practices for refs

- Treat refs as an escape hatch. Refs are useful when you work with external systems or browser APIs. If much of your application logic and data flow relies on refs, you might want to rethink your approach.
- Don't read or write `ref.current` during rendering. If some information is needed during rendering, use state instead. Since React doesn't know when. `ref.current`changes, even reading it while. rendering makes your component's behaviour difficult to predict.

### Manipulating the DOM with Refs

[See documentation here](https://react.dev/learn/manipulating-the-dom-with-refs)

The most common use case for a ref is to access a DOM element. React automatically updates the DOM to match your render output, so your components won't often need to manipulate it. However, you might need access to DOM elements managed by React (e.g. focusing on a node, scrolling to it, measuring its size and position). You will need a *ref* to the DOM node.