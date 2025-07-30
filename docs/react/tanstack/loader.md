[See docs here](https://tanstack.com/router/v1/docs/framework/react/guide/data-loading)

Essentially, this is called when the route is matched. This would allow us to load all our data before showing our component!

There are a lot of advanced things to do with caching and invalidating data, too.


## Search Params
```typescript
// /routes/posts.tsx
export const Route = createFileRoute('/posts')({
  // Use zod to validate and parse the search params
  validateSearch: z.object({
    offset: z.number().int().nonnegative().catch(0),
  }),
  // Pass the offset to your loader deps via the loaderDeps function
  loaderDeps: ({ search: { offset } }) => ({ offset }),
  // Use the offset from context in the loader function
  loader: async ({ deps: { offset } }) =>
    fetchPosts({
      offset,
    }),
})
```