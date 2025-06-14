## Estimates

### Core Tasks

- Refactor 43 different route trees in `abas/apps/core/frontend/app/app.tsx`
- Refactor 23 different instances of `ModalContainer`
- Refactor 10-20 different instances of parameters passed within routes (`Form`, etc)

###  Blockers

- Will regularly have to upgrade the generation algorithm
	- Edge cases and issues
	- Error handling
- Example: Found that barrel files were not being processed. Needed a day to fix
- Estimate will encounter 5-10 such instances, adding up to 5-10 extra days.

### Reasoning

301 hours for the different route trees
- Estimated at 5 development hours + 2 review hours per route tree
- Granted, some route trees are very simple, but others are extremely complex (Tasks)

69 hours for the modal containers
- Estimated at 2 development hours + 1 review hour per modal container
- Changing to `RoutedModal` will take some time at first, but will become easier as we go

30 hours for handling route parameters
- Estimated at 1 development hour + 30 minutes of review per instance
- Should be significantly easier than others, but will require testing

10 extra days for handling blockers and edge cases that come up.

Multiply by 1.5 to account for testing, waiting for reviews, productivity, etc.

Total: 480 hours (60 days)