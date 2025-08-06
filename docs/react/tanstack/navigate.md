In TSR,
- `navigate({ to: '/tasks/' })` goes to an absolute
- `navigate({ to: 'tasks/' })` goes to a relative

BUT in RR,
- `navigate('/tasks/`) and `navigate('tasks')` both go to absolute
- It is only relative if you do `./`
- This may be an issue that I need to monitor... slightly scared about that

AHHHHHHHHHHHHHH the problem is the `useMatch`!
```typescript
const existingAppointmentId = useMatch('/tasks/scheduler/appointments/:id')?.params.id
const existingTaskSessionId = useMatch('/tasks/scheduler/task-sessions/:id')?.params.id
```

It is not setting the `params.id` properly. As such, it will not work!

Lol the arguments to `matchPath` were not correct