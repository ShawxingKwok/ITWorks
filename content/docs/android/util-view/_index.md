---
title: Util-view
weight: 2
---

# Setup
{{< tabs >}}
{{< tab "Groovy" >}}

```groovy
android{
    buildFeatures {
        viewBinding true
    }
}

tasks.withType(org.jetbrains.kotlin.gradle.tasks.KotlinCompile).configureEach{
    kotlinOptions.freeCompilerArgs += "-Xcontext-receivers"
}

dependencies {
    implementation 'io.github.shawxingkwok:android-util-view:1.0.8'
}
```
{{< /tab >}}
{{< tab "Kotlin" >}}

```kotlin
android{
    buildFeatures {
        viewBinding = true
    }
}

tasks.withType<org.jetbrains.kotlin.gradle.tasks.KotlinCompile>().configureEach {
    kotlinOptions.freeCompilerArgs += "-Xcontext-receivers"
}

dependencies {
    implementation("io.github.shawxingkwok:android-util-view:1.0.8")
}
```
{{< /tab >}}
{{< /tabs >}}

# Functions
## Flow.collectOnResumed 
Is used in `Fragment.onCreateView` or `Fragment.onViewCreated` to 
collect `flow` with `collector` after every `Fragment.onResume`.

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

    flow.collectOnResumed{
        ...
    }
}
```

## Flow.collectOnStarted
{{< hint info >}}
This is similar to [Flow.collectOnResumed](#flowcollectonresumed).
{{< /hint >}}

## View.onClick
Sets a `View.OnClickListener` with a more precise `View`.
{{< codeImg onClick.png >}}

# KRecyclerViewAdapter
Take the example of this contact page.<br>
<img src="recyclerview_ui.png" width="200" alt="">

{{< codeImg recyclerview_dataclass.png >}}
<br>
{{< codeImg recyclerview_adapter1.png >}}
{{< codeImg recyclerview_adapter2.png >}}
<br>
{{< codeImg recyclerview_fragment.png >}}

> Layout resources are not displayed. The function `binding(FragmentMainBinding::bind)` is  
from {{< newTab "another library" "https://dylancaicoding.github.io/ViewBindingKTX/#/en/" >}} and independent with `KRecyclerViewAdapter`.

# More 
## Future functionalities
- `KRecyclerViewAdapter`: drag, swipe and set stickHeader like Compose. 

## <a href="https://github.com/ShawxingKwok/AndroidUtil-View" target="_blank"> GitHub repo with demo</a>