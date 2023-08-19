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
- Copy plugin: copies source code with optional replacements.
- Navigation helper on Android 

# Updates 
## Kt util
### `1.0.1`
Remove the super interfaces `ReadOnlyProperty` from `KReadOnlyProperty`, 
and `ReadWriteProperty` from `KReadWriteProperty`.

When `KReadOnlyProperty` is cast to `ReadOnlyProperty`, its function `provideDelegate`
would not work, and neither `onDelegate`. 