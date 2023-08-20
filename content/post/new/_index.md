---
menu:
  after:
    name: New
    weight: 2
title: New
---

# On the way
- MVB: a new architecture on Android view.
- Ksp util
- Copy plugin: generate source code with optional replacements, which is useful when 
you design functions for `Array<*>`, `IntArray`, `CharArray` and others.

# Updates 
## Kt util
### `1.0.1`
Remove the super interfaces `ReadOnlyProperty` from `KReadOnlyProperty`, 
and `ReadWriteProperty` from `KReadWriteProperty`.

When `KReadOnlyProperty` was cast to `ReadOnlyProperty`, its function `provideDelegate`
would not work, and neither `onDelegate`. 