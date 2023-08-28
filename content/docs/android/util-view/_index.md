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
    implementation 'io.github.shawxingkwok:android-util-view:1.0.5'
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
    implementation("io.github.shawxingkwok:android-util-view:1.0.5")
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
Take the example of this contact page.<br>
<img src="recyclerview_ui.png" width="200" alt="">

{{< codeImg recyclerview_dataclass.png >}}

{{< codeImg recyclerview_adapter1.png >}}
{{< codeImg recyclerview_adapter2.png >}}
<br>
{{< codeImg recyclerview_fragment.png >}}

> Layout resources are not displayed. The function `binding(FragmentMainBinding::bind)` is  
from {{< newTab "another library" "https://dylancaicoding.github.io/ViewBindingKTX/#/en/" >}} and independent with `KRecyclerViewAdapter`.

# Release notes
## 1.0.5 
1. Add an open function 'onUpdated' to `KRecyclerViewAdapter`.
2. Make 

# More 
## Future functionalities
- `KRecyclerViewAdapter`: drag, swipe and set stickHeader like Compose. 

## <a href="https://github.com/ShawxingKwok/AndroidUtil-View" target="_blank"> GitHub repo with demo</a>