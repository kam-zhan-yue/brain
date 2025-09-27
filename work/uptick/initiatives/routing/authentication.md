* When going into a route when not authenticated, it should look like
```typescript
export const Route = createFileRoute('/dispatches/*')({
  component: () => {
    const isAuthed = !!useAuth()?.user
    if (!isAuthed)
	    return <Login />
    return <Dispatches />
  },
})
```

Then, in `Dispatches`, we could get rid of the `NotFoundPage` route since TanStack will handle that for us!

We're gonna need the following:
- Hard coded auth route. If auth is required, then we add the following to the file
- How to deal with public routes? It would have to ignore the layout, fuck

```typescript
  component: () => {
    const isAuthed = !!useAuth()?.user
    if (!isAuthed)
	    return <Login />
    return <Component />
  },
```

## Solution
- All the public routes need to go into `<PublicRoutes />`. These need to have a prefix like `dispatches_.dispatches.$uuid.public.tsx`
- Routes that require authentication will have to have a pathless layout. 
OHHH

```typescript
{isAuthed && <Route path="*" element={<AuthRoutes />} />}
{!isAuthed && <Route path="*" element={<NonAuthRoutes />} />}
<Route path="*" element={<PublicRoutes />} />
```

Then this will spit out

### `_authentication.tsx`

```typescript
component: () => {
    const isAuthed = !!useAuth()?.user
    if (!isAuthed)
	    return <Login />
    return <Component />
}
```

http://localhost:3000/dispatches/dispatches/694c80e8-5272-4062-972c-d7f26377e80a/public/
