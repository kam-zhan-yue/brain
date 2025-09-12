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

### What gets code split?
The decision of what to split into separate chunks is crucial for optimising your application's performance. TSR uses a concept called "Split Groupings" to determine how different parts of your route should be bundled together.

Split groupings are arrays of properties that tell TSR how to bundle different parts of your route together. Each grouping is a list of property names that you want to bundle together into a single lazy-loaded chunk. The available properties to split are:
- `component`
- `errorComponent`
- `pendingComponent`
- `notFoundComponent`
- `loader`

By default, TSR uses the following split groupings:
```tsx
[
	['component'],
	['errorComponent'],
	['notFoundComponent'],
]
```

This means that it creates three lazy-loaded chunks for each route, resulting in:
- One for the main component
- One for the error component
- One for the not-found component

## Granular Control
For most applications, the default behaviour is sufficient. However, there are several options to customise how your routes are split into chunks, allowing you to optimise specific use cases or performance needs.

### Global Code Splitting Behaviour

You can change how TSR splits your routes by changing the `defaultBehaviour` option in your bundler plugin configuration. This allows you to define how different properties of your routes should be bundled together.

For example, to bundle all UI-related components into a single chunk, you could do
```tsx
tanstackRouter({
      autoCodeSplitting: true,
      codeSplittingOptions: {
        defaultBehavior: [
          [
            'component',
            'pendingComponent',
            'errorComponent',
            'notFoundComponent',
          ], // Bundle all UI components together
        ],
      },
    }),
```

### Advanced Programmatic Control (`splitBehaviour`)

For complex rulesets, you can use the `splitBehaviour` function to programatically define how routes should be split into chunks based on their `routeId`. This function allows you to implement custom logic for grouping properties together.

```tsx
tanstackRouter({
      autoCodeSplitting: true,
      codeSplittingOptions: {
        splitBehavior: ({ routeId }) => {
          // For all routes under /posts, bundle the loader and component together
          if (routeId.startsWith('/posts')) {
            return [['loader', 'component']]
          }
          // All other routes will use the `defaultBehavior`
        },
      },
    }),
```

### Per-Route Overrides (`codeSplitGroupings`)

For ultimate control, you can override the global configuration directly inside a route file buy adding a `codeSplitGroupings` property. This is useful for routes that have unique optimisation neesd.

```tsx
export const Route = createFileRoute('/posts')({
  // For this specific route, bundle the loader and component together.
  codeSplitGroupings: [['loader', 'component']],
  loader: () => loadPostsData(),
  component: PostsComponent,
})
```

### Configuration Order

TSR follows the following order of precedence
1. Per-route overrides
2. Programmatic split behaviour
3. Default behaviour

### Splitting the Data Loader

The `loader` function is responsible for fetching data needed by the route. BY default , it is bundled into your "reference file" and loaded in the initial bundle. However, you can also split the `loader` into its own chunk if you want to optimise further.

> Caution: Moving the loader into its own chunk is a performance trade-off. It introduces an additional trip to the server before the data can be fetched, which can lead to slower initial page loads. This is because the `loader` must be fetched and executed before the route can render its component. It is recommended to keep the `loader` in the initial bundle unless you have a specific reason to split it.