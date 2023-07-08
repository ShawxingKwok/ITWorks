---
title: Util-view
weight: 2
---

# Setup
Add this part directly, rather than insert messily.
{{< tabs >}}
{{< tab "Groovy" >}}

```groovy
//region shawxingkwok: android-util-view
android.lintOptions.disable 'NonConstantResourceId'

tasks.withType(org.jetbrains.kotlin.gradle.tasks.KotlinCompile).configureEach{
    kotlinOptions.freeCompilerArgs += "-Xcontext-receivers"
}

dependencies {
    implementation 'io.github.shawxingkwok:android-util-view:1.0.0'
}
//endregion
```
{{< /tab >}}
{{< tab "Kotlin" >}}

```kotlin
//region shawxingkwok: android-util-view
android.lintOptions.disable("NonConstantResourceId")

tasks.withType<org.jetbrains.kotlin.gradle.tasks.KotlinCompile>().configureEach {
    kotlinOptions.freeCompilerArgs += "-Xcontext-receivers"
}

dependencies {
    implementation("io.github.shawxingkwok:android-util-view:1.0.0")
}
//endregion
```
{{< /tab >}}
{{< /tabs >}}

# Flow.collectOnResume
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

# KFragment

# KRecyclerViewAdapter

# <a href="https://github.com/ShawxingKwok/AndroidUtil-View" target="_blank"> GitHub repo </a>