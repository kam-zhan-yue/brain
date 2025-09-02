## Weird Things to Manually Do
- Delete warehouses/extensionCheck/index
- Define CustomerPortal's NotFoundPage Component manually
- SiteConfig EventList gets 

```typescript
import { createFileRoute } from '@tanstack/react-router'
import { NotFoundPage } from '@uptick/customer-portal-app'
import { CustomerPortalAutocompletesPage } from '../../../../../../frontend/apps/workforce/src/backstage/autocompletes/customer-portal-autocompletes-page'

export const Route = createFileRoute('/_authenticated/backstage/autocompletes/customer-portal')({
  component: CustomerPortalAutocompletesPage,
  notFoundComponent: NotFoundPage,
})
```

## Problems
- [x] pricetiers/inlineupdate causing infinite render (useProxiedUrl) seems to be okay...
- [x] scheduler offcanvas not closing
- [x] Drill
- [x] throw notFound() not working (http://localhost:3000/tasks/tasks/373580/view/invoices/create)
- [ ] Accounts Update is not working properly (might need another generate)
- [ ] Sidebar highlight -> Seems like TSR adds 'active' to all `<a>` that are active. Honestly, TSR is a godsend for bringing this to our attention.
- [ ] Login button is not sending the user to the dashboard
	- [ ] Seems like when we login, we are navigating to the root, but it is not going to the hard redirect
	- [ ] When I'm already logged in, and I go to login, then I login, it will navigate me

```
TSR Navigate to /
```
- 
	- [ ] When I'm not logged in, and I go to login, then I login, it will navigate me with
	
```
TSR Navigate to /
```

It's the authenticated prop! We are not authenticated for some reason
![[Pasted image 20250813165732.png]]
After navigation, TSR instantly finds `isAuthed` to be false. However, this is set to true again. We want to invalidate the router if possible and trigger a re-render of the router here.

- [x] `useMatch` doesn't like pathnames, it takes `routeId`.
So we have to do things like this
```tsx
import { useRouter } from '@tanstack/react-router'
import { useMatch } from '@uptick/navigation'

const useCustomerPortalMatch = () => {
  const router = useRouter({ warn: false })
  const routeId = router
    ? '/_authenticated/customerportal/_customerPortalServices/$entityType/$entityId'
    : '/customerportal/:entityType/:entityId/*'
  const { entityId, entityType } = useMatch(routeId)?.params || {}

  return { entityId, entityType }
}

export { useCustomerPortalMatch }

```

- [x] Trailing Slashes in `useLocation`
The following code doesn't work if the search query ends with '/'. So in TSR's `useMatch`, we want to take out the trailing slash

```tsx
const MonthlyActivityView = () => {
  const location = useLocation()
  const currentUrlQuery = location.search
  const { data, loading } = usePartialSPA<PageData>(`/api/v2/customerportal/monthlyreport/${currentUrlQuery}`)
  const spaData = !loading ? data : undefined
  const pdfUrl = `/api/v2/customerportal/monthlyreport/pdf/${currentUrlQuery}`

```

- [ ] Navigating `../` doesn't work as it is intended to when used in Link
- In RR, when the URL is `client/1/properties/1` and we navigate to `../defect-quotes`, we get `client/1/defect-quotes`
- In TSR, when the URL is `client/1/properties/1` and we navigate to `../defect-quotes`, we get `client/1/properties/defect-quotes`

- [ ] `View` button is gone in Labour Rate List
- The wrong component was being exported. Gonna have a little bit of trouble with components with duplicate names

- [ ] Create Project Task from Service Quote not working
- It was getting projectId from `useSearch` and was returning `project=1/`
- The answer is to trim off the trailing slash

- [ ] Service Quote things not working
- Lots of places were using `window.location.reload`

- [ ] Dispatch is infinitely looping
- When the dispatch is finished, we should notify
## Navigation Behaviour
- `'./` will result in `tasks/`
- This is bad, I can't unit test it. It's completely unpredictable
- React Router will work fine, but implementing the behaviour in TSR is super hard because it doesn't accept ../
- I don't get why the above is even happening in the first place.........
- Unless it's where the component is rendered in the first place?

- Ok I have some replicable behaviour. Let's do it

```
Route Pathnames: ["/","/customerportal","/customerportal","/customerportal","/customerportal/client/1","/customerportal/client/1","/customerportal/client/1/properties/1"]
```

```
Tanstack Matches: __root__, /_authenticated, /_authenticated/customerportal, /_authenticated/customerportal/_customerPortalServices, /_authenticated/customerportal/_customerPortalServices/client/1, /_authenticated/customerportal/_customerPortalServices/client/1/_offcanvasContainer, /_authenticated/customerportal/_customerPortalServices/client/1/_offcanvasContainer/properties_/1
```

We can probably do the same, but it would be better if it were just a direct route. We can do the same.

## Navigate './'
Will navigate to the route that the component is at! That's why the outlet and modal outlet works!
### Solution

The flow is:
- useNavigate and Link and such will require us to go back when we do './' as well as support pathing. So they will go through `useTanstackResolvePath`
- `useResolvedPath` will not go through `useTanstackResolvePath` because they don't have the same behaviour with `'./`
## Notes
- [ ] We should set `trailingSlash` to always but for some reason it brings type errors
- [ ] 