---
title: Android-view
weight: 1
---

{{< hint info >}}
Skip if you are using `Compose`. 
{{< /hint >}}

{{< hint info >}}
Since the static {{< newTab AppContext "/ITWorks/docs/android/util-core/#appcontext">}} is
already provided, dependency injection is not so needful on Android. Use this tracer extension if you 
are familiar with tracer and interested.  
{{< /hint >}}

# Setup
Add this dependency below {{< newTab "tracer-compiler" "../../setup" >}}.
```kotlin
// Never put this above 'tracer-compiler'
ksp ("io.github.shawxingkwok:tracer-android-view-compiler:1.0.0")
```

# Effect
Connects with `activity`/`parentFragment`'s interior elements with automatic 
`requireActivity`/`requireParentFragment`.

{{< video src=effect >}}

# ViewModel
I suggest to annotate your subclass of `ViewModel` with `Tracer.Tip` to avoid tracing insides. 

# <a href="https://github.com/ShawxingKwok/Tracer-AndroidView" target="_blank"> GitHub repo with demo</a>