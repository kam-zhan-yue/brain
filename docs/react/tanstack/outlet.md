## `useOutlet`
TSR's `Outlet` works by checking two things
- Is the parent valid? If not, throw a not found error
- Is there a valid child route? If so, return a `Match`

```typescript
  const childMatchId = useRouterState({
    select: (s) => {
      const matches = s.matches
      const index = matches.findIndex((d) => d.id === matchId)
      return matches[index + 1]?.id
    },
  })

  if (parentGlobalNotFound) {
    return renderRouteNotFound(router, route, undefined)
  }

  if (!childMatchId) {
    return null
  }

  const nextMatch = <Match matchId={childMatchId} />
```

If we can find a way to use the `childMatchId`, we can probably determine there is something being rendered in the outlet!

Time to play around with code :^)
```typescript
const matchId = React.useContext(matchContext)
```