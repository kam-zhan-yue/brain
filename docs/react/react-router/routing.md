There are three distinct differences between the following:

### `path="*"`
This will match all the paths specified under the parent, EXCLUDING the index!
```typescript
<Route path="scheduler/" element={<TaskSchedulerPage />}>
	<Route path="*" element={<Offcanvas />}>
	  <Route path="appointments/" element={<TaskSchedulerAppointmentUpdate />} />
	</Route>
</Route>

```
- The `Offcanvas` will not show on `scheduler/`
- It will show on any of its child routes (i.e.`scheduler/appointments` or `scheduler/blah`)

### `path=""`
This will match all the paths specified in its children, INCLUDING the index!
```typescript
<Route path="scheduler/" element={<TaskSchedulerPage />}>
	<Route path="" element={<Offcanvas />}>
	  <Route path="appointments/" element={<TaskSchedulerAppointmentUpdate />} />
	</Route>
</Route>

```
- The `Offcanvas` will show on `scheduler/`
- It will show on any of its child routes (i.e.`scheduler/appointments`)
- It will not show on any unmatched routes (i.e.`scheduler/blah`)

### `no path`
This will only match the paths specified in its children
```typescript
<Route path="scheduler/" element={<TaskSchedulerPage />}>
	<Route element={<Offcanvas />}>
	  <Route path="appointments/" element={<TaskSchedulerAppointmentUpdate />} />
	</Route>
</Route>

```
- The `Offcanvas` will not show on `scheduler/`
- It will show on any of its child routes (i.e.`scheduler/appointments`)
- It will not show on any unmatched routes (i.e.`scheduler/blah`)