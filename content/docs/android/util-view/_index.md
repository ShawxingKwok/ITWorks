---
title: Util-view
weight: 2
---

# Setup
{{< tabs >}}
{{< tab "Groovy" >}}

```groovy
tasks.withType(org.jetbrains.kotlin.gradle.tasks.KotlinCompile).configureEach{
    kotlinOptions.freeCompilerArgs += "-Xcontext-receivers"
}

dependencies {
    implementation 'io.github.shawxingkwok:android-util-view:1.0.0'
}
```
{{< /tab >}}
{{< tab "Kotlin" >}}

```kotlin
tasks.withType<org.jetbrains.kotlin.gradle.tasks.KotlinCompile>().configureEach {
    kotlinOptions.freeCompilerArgs += "-Xcontext-receivers"
}

dependencies {
    implementation("io.github.shawxingkwok:android-util-view:1.0.0")
}
```
{{< /tab >}}
{{< /tabs >}}

# Functions
## Flow.collectOnResume
Is used in `Fragment.onCreateView` or `Fragment.onViewCreated` to 
collect `flow` with `collector` every `Fragment.onResume`.

Switch from
```kotlin
override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
    super.onViewCreated(view, savedInstanceState)

    viewLifecycleOwner.lifecycleScope.launch {
        repeatOnLifecycle(Lifecycle.State.RESUMED) {
            flow.collect {
                ...
            }
        }
    }
}
```
to
```kotlin
override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
    super.onViewCreated(view, savedInstanceState)

    flow.collectOnResume{
        ...
    }
}
```

## View.onClick
Sets a `View.OnClickListener` with a more precise `View`.
{{< codeImg onClick.png >}}

# KRecyclerViewAdapter
## Core usage
{{< mp4 src=recyclerview width=250 >}} 
{{< hint info >}}
The reply which appears one second later is out of simulation rather than bad performance.
{{< /hint >}}

{{< codeImg msg.png >}}

{{< codeImg recyclerview_adapter.png >}}
<br>
{{< codeImg recyclerview_fragment.png >}}

`MainViewModel` with the simulated initial data is not displayed.

Those functions
{{< newTab "binding" "https://dylancaicoding.github.io/ViewBindingKTX/#/en/" >}}, {{< newTab "fastLazy" "../../multiplatform/kt-util/html/-kt-util/pers.shawxingkwok.ktutil/fast-lazy.html" >}}, `collectOnResume` and `onClick` 
are independent with `KRecyclerViewAdapter`.

# More 
## On the way
- `KRecyclerViewAdapter`: sticky header, drag

## <a href="https://github.com/ShawxingKwok/AndroidUtil-View" target="_blank"> GitHub repo with demo</a>