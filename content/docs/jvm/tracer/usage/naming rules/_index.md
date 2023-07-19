---
title: Naming rules
weight: 2
---

# Position qualifiers
  <img src=../doubleBedroomsHouse.png height=220/>
  
  The code of model above is needless to see. For this injection:   

  <img src=quilt.png width=450/>

  ---
  * In `_Window_Bedroom_window`     
    - `Window`: type  
    - `Bedroom`: owner class name   
    - `window`: property name
  ---  
  * `__` : beginning with `__` means **outside**. In this case, `__Door` tells that referenced 
    element is outside `Bedroom`.  
  ---  
  * `˚`: Now we redesign `Pillow` as below. In class `Cotton`, tag after `˚` tells the nearest
    root or nodes.    
    <img src=level.png width=600/>

    ---

    In addition, `˚` always appears when the corresponding `root/nodes` class is `abstract/open`, 
    for being distinguished in subclasses as `bar` and `x` is traced below.   
    
    <img src=abstractLevel.png width=350/>
    
    ---
    
    <img src=_abstractLevel.png width="600"/>

# Type symbols conversion   
  <img src=typeSymbols.png />
  
  ---

  <img src=_typeSymbols.png />

  Type symbols are converted as above, because android forbids most 
  original common symbols in property names, making me choose these substitutes  `⍒` `❨` `❩` 
  `→` `‹` `›` `？` `，` `✶` `→` `↑` `↓` instead.

  ---
  Wildcard `*` would be tried converting to its bound and together with the covariance symbols `↑` 
  `↓` if needed, like from from `Array<*>` to `Array‹↓Any？›` and `List<*>` to 
  `List‹Any？›`. `Comparable<*>` is not convertible because its source code is `Comparable<in T>`.
  
  ---
  ```
  class A{
      class B
  }
  ```
  The nested class `A.B` would be converted to `A․B` in which `․` is not `.` <img src=dot.png 
  width="500"/>

{{< hint info >}}
These type symbol substitutes would be removed when each platform allow them in property names
in the future.   
{{< /hint >}}


# Generic types 
Generic types are converted to their bounds, and with `covariance` (`out`) if not redundant.  

{{< video src=generic >}}

# Multiple bounds
<img src=multiBounds.png />

---

<img src=_multiBounds.png />

`T` has two bounds which can be expressed in the first generated property. However, the second 
property type is limited by `Super` and `two bounds` can't be expressed, which is told by `✕` in 
the name.