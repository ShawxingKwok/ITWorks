---
title: Android
weight: 1
---

# Abstract
这是一种新架构，全称为 `Model-View-Bridge`。当前仅有安卓版本，但模式适用于每个平台。UI 部分采用传统的 `View`，
Kt 部分弃用了 `ViewModel`, `saveState`。关于常用的且写法稍复杂的 `RecyclerView Adapter`, 我已封装简化成 {{< newTab "KRecyclerViewAdapter" "/ITWorks/docs/android/util-view/#krecyclerviewadapter" >}}，
可像 `Compose` 一样排列子项。至此，`MVB` 中除去自定义 `View` 的地方并不比 `Compose` 差。

`Compose` 是 Declarative UI mode 中最简洁的写法，自定义 View 时也最强大，但重组机制不可避免地重复构造了大量对象，
性能的上限依然低出不少。

此外，关于 UI 部分我有一种新的设计模式，但需要官方辅助设计可视化操作界面，目标是 UI 部门的视图源码可以被 Android 开发人员直接取用。
在后面有详细介绍，如能实现，再结合这个架构，毫无疑问是革命性的成果。

# Usage
## rmb
This is the abbreviation of `remember`, meaning remembering the value across the device rotation in 
`ComponentActivity` / `Fragment`. 
{{< codeImg rmb.png >}}

{{< hint info >}}
Their getters are `synchronized`. 
{{< /hint >}}

## save
This is similar to `rmb`, the values are alive across the device rotation, and furthermore restorable from the process death. 
Saved value types should follow {{< newTab Parcelize "https://developer.android.com/kotlin/parcelize" >}} rules.
{{< codeImg save.png >}}

Or append `transform` after `delegator` with the converted values following {{< newTab Parcelize "https://developer.android.com/kotlin/parcelize" >}} rules.
{{< codeImg transform.png >}}

Inner parcelable components couldn't be distinguished unless they are put in `Array`. Pass the arg of 
`KClass<out Parcelable>` in these cases.
{{< codeImg parcelableComponent.png >}}

`MutableStateFlow`, `MutableShardFlow`, and `MutableLiveData` are so common that I made some shortcut functions for 
them.
<br>Take the example of `saveMutableStateFlow`. 
{{< codeImg saveMutableStateFlow.png >}} 

> You could reference the short source code to customize the way of saving your own common types. 

## observe
The delegated values via `rmb` / `save` are easily observable if it's `Flow` / `LiveData`. The latter lambda is active between every `onStart`
and `onStop`, which is generally used for linking `value` to `UI state`.
{{< codeImg rmbObserve.png >}}

{{< codeImg saveObserve.png >}}

> `LiveData` is also observable. However, I strongly suggest using `Flow` instead though it takes some time to learn.    

## Format  
Take the example of simulating the stopwatch page in the IOS clock.

In this way, `ViewModel` and `saveState` are needless.

# Proposals
- `View` 的所有更新都先检查一遍，是否和当前的值重合。
- New navigation mode allowing for parameters in `Activity/Fragment`. 