---
title: Android
weight: 1
---
# Abstract
`MVB` is my new architecture standing for `Model-View-Bridge`. It has only an Android implementation at present, 
but the pattern is applicable to every platform that supports Kotlin. The UI part uses the traditional `View` without 
`DataBinding`, and the Kt part deprecates `ViewModel` and `outState`, resulting in much less code, looser coupling and 
clearer logic.

Other points:
- `Compose` is the most concise declarative UI framework, and also the most flexible in terms of custom views. However, 
its recombination mechanism inevitably reconstructs massive objects, resulting in its low upper performance limit.

- Regarding `navigation parameters` for `Activity` / `Fragment`, I have a proposal on 
{{< newTab Reddit "https://www.reddit.com/r/androiddev/comments/15pa5qf/proposal_android_could_support_activityfragment/" >}}.

- `RecyclerView.Adapter` is the most common among components with complex writing styles, whereas my encapsulated 
{{< newTab "KRecyclerViewAdapter" "/ITWorks/docs/android/util-view/#krecyclerviewadapter" >}} arranges sub-items as Compose. 
Therefore, MVB does not fall short compared to Compose apart from custom views at present. 

- As for the UI part, I have a {{< newTab "new design pattern proposal" "../proposal" >}}. 

Overall, I suggest using the traditional `View` with `MVB` rather than `Compose` at present, and switching to the other 
imperative UI mode based on that proposal implementation in which `MVB` would still work.

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
    implementation 'io.github.shawxingkwok:mvb-android:1.0.0'
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
    implementation("io.github.shawxingkwok:mvb-android:1.0.0")
}
```
{{< /tab >}}
{{< /tabs >}}

# Usage
{{< hint info >}}
Examples are all with `Fragment`, but also applicable with `ComponentActivity` which is one superclass of `AppCompatActivity`.  
{{< /hint >}}

## rmb
This is the abbreviation of `remember`, meaning the value is alive across the configuration change (e.g. rotation) 
in`ComponentActivity` / `Fragment`. 
{{< codeImg rmb.png >}}
<p style="text-align:center;">↓</p>
{{< codeImg rmb_.png >}}
{{< hint info >}}
Initialization of the property declared with `val` is `synchronized`. 
{{< /hint >}}

## save
### Direct
Saved values are not only alive across the configuration change, but also restorable from the process death. 
{{< hint warning >}}
Directly saved value types should follow {{< newTab Parcelize "https://developer.android.com/kotlin/parcelize" >}} rules.
{{< /hint >}}
Switch from the traditional way as below. 
{{< tabs "save" >}} 

{{< tab `Common` >}}
{{<codeImg saveParcelable.png >}}
<p style="text-align:center;">↓</p>
{{<codeImg saveParcelable_.png >}}
{{< /tab >}}

{{< tab `Flow` >}}
{{<codeImg saveFlow.png >}}
<p style="text-align:center;">↓</p>
{{<codeImg saveFlow_.png >}}
{{< /tab >}}

{{< tab `LiveData` >}}
{{<codeImg saveLiveData.png >}}
<p style="text-align:center;">↓</p>
{{<codeImg saveLiveData_.png >}}
{{< /tab >}}

{{< /tabs >}}

{{< hint info >}}
I strongly suggest using `Flow` instead of `LiveData`, though it takes some time to learn.
{{< /hint >}}
{{< hint info >}}
Rules below are same to `saveMutableStateFlow`, `saveMutableSharedFlow`, `saveMutableLiveData`, and not limited with 
`val` / `var`.
{{< /hint >}}

### Transform
You could also save values with any type by appending `transform` after `save` with the converted values following 
{{< newTab Parcelize "https://developer.android.com/kotlin/parcelize" >}} rules.
{{< codeImg saveWithTransform.png >}}

### Parcelable component
Sometimes you need to pass `KClass<out Parcelable>` as the parcelable component. 
{{< codeImg saveWithParcelableComponent.png >}}

{{< hint info >}}
You could reference the short source code of `saveMutable...` to customize functions to save values of which the types 
are common but not directly savable.
{{< /hint >}}

## observe
The delegated values via `rmb` / `save` are easily observable if it's `Flow` / `LiveData`. The latter observe lambdas 
are active between every `onStart` and `onStop`, which is generally used for linking `value` to `UI state`.
{{< codeImg observe.png >}}
<p style="text-align:center;">↓</p>
{{< codeImg observe_.png >}}

## mvbScope
This is `viewModelScope` of `MVBViewModel` hidden in `ComponentActivity` / `Fragment`.
{{< codeImg mvbScope.png >}}

## Format  
Take the example of simulating the stopwatch page in the IOS clock with concise code. 

<video height="400" controls>
  <source src="stopwatch.mov" type="video/mp4">
</video>

### Components
{{< codeImg stopwatchUtil.png >}}
<br> See {{< newTab "KRecyclerViewAdapter" "/ITWorks/docs/android/util-view/#krecyclerviewadapter" >}} if it's new to you.
{{< codeImg stopwatchAdapter.png >}}

### Static processing
Declare components and enable them in `Activity.onCreate` / `Fragment.onViewCreated` 
{{< codeImg stopwatchFragment.png >}}

### Bridge
Declare variable data sources via `rmb` and `save`, and link them to `UI state` via appending `observe`.
{{< codeImg stopwatchBridge1.png >}}
{{< codeImg stopwatchBridge2.png >}}

### Fixed listeners
I suggest putting fixed listeners here or below.
{{< codeImg stopwatchSetFixedListeners.png >}}

{{< hint info >}}
Some functions from other libraries are independent with `MVB`, e.g. 
{{< newTab "binding(XxBinding::bind)" "https://dylancaicoding.github.io/ViewBindingKTX/#/en/" >}}, 
{{< newTab onClick "/ITWorks/docs/android/util-view/#viewonclick" >}}, 
and {{< newTab updateIf "/ITWorks/docs/multiplatform/kt-util/html/-kt-util/pers.shawxingkwok.ktutil/update-if.html" >}}.
{{< /hint >}}

# <a href="https://github.com/ShawxingKwok/MVB" target="_blank"> GitHub repo with demo</a>