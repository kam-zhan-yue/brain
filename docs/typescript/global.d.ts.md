[See documentation here](https://www.typescriptlang.org/docs/handbook/declaration-files/templates/global-d-ts.html)

A *global* library is one that can be accessed from the global scope (i.e. without using any form of import). Many libraries simply expose one or more global variables for use.

> For example, jQuery uses the `$` variable by simply referring to it.

The most popular globally-accessible libraries are actually written as UMD libraries.

  The reality is: you cannot have both - full type safety with the route tree AND no circular dependencies with the current architecture where route files import components.

  Here's what you need to choose:

  Option 1: Full Type Safety (No autocomplete, but builds work)

  Keep what we have now - no TS6307 errors, but limited/no route autocomplete.

  Option 2: Full Type Hints (With circular dependency errors at build time)

  Restore the original setup. Types work perfectly in your editor, but tsc --build will have errors. The errors won't affect runtime or bundling, just the type checking build.

  Option 3: Fix the Architecture (Recommended long-term)

  The root cause is abas/apps/routes/** files importing from abas/apps/*/frontend/containers/**. These should either:
  - Move out of abas into their own package
  - Stop using relative imports and use runtime-only imports
  - Be excluded from TypeScript compilation

  Which would you prefer? Or would you like me to help implement Option 3?