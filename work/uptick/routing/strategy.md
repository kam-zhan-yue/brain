## Weird Things to Manually Do
- Define CustomerPortal's NotFoundPage Component manually

```typescript
import { createFileRoute } from '@tanstack/react-router'
import { NotFoundPage } from '@uptick/customer-portal-app'
import { CustomerPortalAutocompletesPage } from '../../../../../../frontend/apps/workforce/src/backstage/autocompletes/customer-portal-autocompletes-page'

export const Route = createFileRoute('/_authenticated/backstage/autocompletes/customer-portal')({
  component: CustomerPortalAutocompletesPage,
  notFoundComponent: NotFoundPage,
})
```