## Overview
---
A *lifetime* is a construct that the borrow checker uses to ensure all borrows are valid. Specifically, a variable's lifetime begins when it is created and ends when it is destroyed. While lifetimes and scopes are often referred to together, they are not the same.

For example, when we borrow a variable via `&`, the borrow has a lifetime that is determined by where it is declared. As a result, the borrow is valid as long as it ends before the lender is destroyed. However, the scope of the borrow is determined by where the reference is used.

## Explicit Annotation
---
The borrow checker uses explicit lifetime annotations to determine how long references should be valid. In cases where lifetimes are not elided, Rust requires explicit annotations to determine what the lifetime of a reference should be.
```rust
foo<'a>
// `foo` has a lifetime parameter `'a`
foo<'a, 'b>
// The lifetime of `foo` cannot exceed that of either 'a or 'b
```
