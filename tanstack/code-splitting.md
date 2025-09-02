[See documentation here](https://tanstack.com/router/latest/docs/framework/react/guide/automatic-code-splitting)

Tanstack's automatic code spitting works by transforming your route files both during 'development' and at 'build' time. It rewrites the route definitions to use lazy-loading wrappers for components and loaders, which allows the bundler to group these properties into separate chunks.

> A chunk is a file that contains a portion of your application's code, which can be loaded on demand. This helps reduce the initial load time of your application by only loading the code that is needed for the current route.

So when your application loads, it doesn't include all the code for every route. Instead, it only includes the code for the routes that are initially needed. As users navigate through your application, additional chunks are loaded on demand.

This happens seamlessly, without requiring you to manually split your code or manage lazy loading. The Tanstack Router bundler plugin takes care of everything, ensuring that your routes are optimised for performance.

### The transformation process
When automatic code splitting is enabled, the bundler splits by using static code analysis look at the code in your route files to transform them into optimised outputs. The transformation process produces two key outputs when each of your route files are processed:
1. Reference File: The bundler plugin takes your original route file and modifies the values for properties like `component` or `pendingComponent` to use special lazy-loading wrappers that'll fetch the actual code later. These wrappers point to a 'virtual' file that the bundler will resolve later on.
2. Virtual File: When the bundler sees a request for one of these virtual files, it intercepts it to generate a new, minimal on-the-fly file that only contains the code for the requested properties.

This process ensures that your original code is clean and readable, while the actual bundled output is optimised for initial bundle size.