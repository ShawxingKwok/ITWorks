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
    implementation "io.github.shawxingkwok:kt-util:1.0.0"
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

## withView
Delegates a value in `ComponentActivity`/`Fragment`.   

When in `ComponentActivity`, it's initialized after `onCreate` or when you call its `getter` before that.  

When in `Fragment`, it is initialized after `onStart` or when you call its `getter` before that, 
and destroyed when `onDestroyView`.

Usage sample:
```kotlin
class MainFragment : Fragment(){
    val adapter by withView{ MyAdapter(requireContext()) }
}
```
It could be also `mutable`. 
```kotlin
var x by withView{ ... }
```

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

The function `binding` is from {{< newTab "another library" "https://dylancaicoding.github.io/ViewBindingKTX/#/en/" >}}.

`KRecyclerViewAdapter` is not bound to `KFragment` but usable in general `Activity`/`Fragment`.
And you could replace `withView`, `collectOnResume`, and `onClick` in above display.

## update
{{< codeImg recyclerview_update_notify.png >}}
<br>
{{< codeImg recyclerview_update_src.png >}}

These functions `notify...`  are replaced with `update` now.
{{< hint info >}}
`update` may be called too frequently, which makes some previous `onFinish` omitted.
{{< /hint >}}

## onHoldersCreated
In case you need to make some fixed processing of  `ViewHolder` after its automatic creation regardless of `position`.
{{< codeImg recyclerview_onHoldersCreated.png >}}

Here is allowed to set `Listeners` on `view` with `adapterPosition` to get `data`, which is, however, unclear. Just
take this step in `arrange` which costs ignorable more memories.

## Final functions 
{{< codeImg recyclerview_finalFunctions.png >}}

These functions are made `final` in `KRecyclerViewAdapter`.

# More 
## On the way
- `KRecyclerViewAdapter`: sticky header, drag

## <a href="https://github.com/ShawxingKwok/AndroidUtil-View" target="_blank"> GitHub repo with demo</a>