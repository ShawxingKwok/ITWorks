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

## KClass\<out ViewBinding>.inflate
Returns a `viewBinding` via reflection, which helps you design view utils.
{{< codeImg inflate.png >}}

{{< codeImg inflate1.png >}}

# KFragment
## ViewBinding
{{< codeImg kfragment_binding.png >}}

{{< hint info>}}
You may have seen other libraries realized only via a generic parameter, which is, however, unsafe or 
time-consuming.
```kotlin
abstrac class VBFragment<VB: ViewBinding> : Fragment()
```
{{< /hint >}}
 
## withView
Delegates a value alive between inclusive `onViewCreated` and exclusive `onDestroyView`.

Usage sample:
```
val adapter by withView{ Adapter() }
var x by withView{ ... }
```

# KRecyclerViewAdapter
## Core usage
{{< mp4 src=recyclerview height=500 >}}
{{< codeImg recyclerview_adapter.png >}}
<br>
{{< codeImg recyclerview_fragment.png >}}

The `layoutManager` is required. But I prefer putting it in the `xml`.
{{< codeImg recyclerview_layoutManger.png >}}

`KRecyclerViewAdapter` is not bound to `KFragment` but usable in general `Activity`/`Fragment`. 
And you could replace `withView`, `collectOnResume`, and `onClick`.

## update
{{< codeImg recyclerview_update_src.png >}}
<br>
{{< codeImg recyclerview_update_notify.png >}}

These functions `notify...`  are replaced with `update` now.
{{< hint info >}}
`update` may be called too frequently, which makes some previous `onFinish` omitted.
{{< /hint >}}

## onHoldersCreated
In case you need to process `ViewHolder` after its automatic creation regardless of `position`.
{{< codeImg recyclerview_onHoldersCreated.png >}}

Here is allowed to set `Listeners` on `view` with `adapterPosition` to get `data`, which is, however, unclear. Just
take this step in `arrange` which costs ignorable more memories.

## payloads 
As this function is inherently uncommon, just skip if you aren't familiar with it
which is made `final` since the background calculation is efficient enough. 
{{< codeImg recyclerview_payloads.png >}}

# More 
## On the way
1. `KRecyclerViewAdapter`: sticky header 
2. `KFragment`: delegates data stored in the hidden `viewModel` with observation. 

## <a href="https://github.com/ShawxingKwok/AndroidUtil-View" target="_blank"> GitHub repo with demo</a>