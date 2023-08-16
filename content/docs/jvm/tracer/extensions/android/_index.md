---
title: Android-view
weight: 1
---

{{< hint info >}}
Skip if you are using `Compose`. 
{{< /hint >}}

{{< hint info >}}
Since the static `applicationContext` could be {{< newTab provided "/ITWorks/docs/android/util-core/#appcontext-trick">}}, `dependency injection` is not so needful on Android now. Use this tracer extension if you 
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

{{< codeImg main.png >}}
<br>
Generated code
{{< codeImg childFragmentTracer.png >}}

For the example of public `SharedViewModel` in `MainActivity`, I suggest annotating the subclass of `ViewModel` 
with `Tracer.Tip` to avoid tracing insides. 

# <a href="https://github.com/ShawxingKwok/Tracer-AndroidView" target="_blank"> GitHub repo with demo</a>