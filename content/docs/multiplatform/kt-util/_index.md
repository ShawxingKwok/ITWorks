---
title: Kt util
weight: 1
---

# Setup
Repository: `mavenCentral()`  
Dependency: `"io.github.shawxingkwok:kt-util:1.0.2"`

# <a href="html/-kt-util/pers.shawxingkwok.ktutil/" target="_blank"> Document </a>

# <a href="https://github.com/ShawxingKwok/KtUtil" target="_blank"> GitHub repo </a>

# Release notes
## Kt util
### `1.0.1`
Remove the super interfaces `ReadOnlyProperty` from `KReadOnlyProperty`,
and `ReadWriteProperty` from `KReadWriteProperty`.

When `KReadOnlyProperty` was cast to `ReadOnlyProperty`, its function `provideDelegate`
would not work, and neither `onDelegate`. 

### `1.0.2`
Solves an internal bug in `fastLazy`. 