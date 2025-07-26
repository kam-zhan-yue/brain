In order to do 
```typescript
navigate({
  to: '/posts/$postId',
  params: { postId }
  state: {{ post: { id: '123'; name: 'foo' } }}
})
```

We first need to register these as types on the router

```typescript
// Register things for typesafety
declare module '@tanstack/react-router' {
  interface Register {
    router: typeof router;
  }
  interface HistoryState {
    post?: { id: string; name: string };
  }
}
```