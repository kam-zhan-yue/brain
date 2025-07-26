## Layout Stuff

```typescript

<Route path=":entityType/:entityId/*" element={<Dashboard />}>
	<Route path="*" element={<OffcanvasContainer />}>
		<Route path="asset" element={<AssetLifeCycleView />} />
		<Route path="contract" element={<ContractPerformanceView />} />
		<Route path="defect-report" element={<DefectReportView />} />
		<Route path="properties" element={<PropertyListView />} />
		<Route path="properties/:propertyId" element={<PropertyDetailView />} />
	</Route>
</Route>
```

```json
"path": "$entityType",
      "type": "layout",
      "children": [
        {
          "path": "",
          "type": "layout",
          "component": "OffcanvasContainer",
          "children": [
            {
              "path": "contract-performance",
              "type": "route",
              "component": "ContractPerformanceView",
              "children": []
            },
```

Evidently, the `Dashboard` is not in the layout. lmao

## Auth Routes
```typescript
const App = () => {
  const isAuthed = !!useAuth()?.user
  return (
    <Routes>
      {getPublicRoutes()}
      {isAuthed && <Route path="*" element={<AuthRoutes />} />}
      {!isAuthed && <Route path="*" element={<Login />} />}
    </Routes>
  )
}
```

- We need to go into `getPublicRoutes` and extract out the routes and make them into their own files
- Inside of `AuthRoutes`, there will be references to a `NotFoundPage`. We want to get out of that