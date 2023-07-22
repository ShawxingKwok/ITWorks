---
title: Naming rules
weight: 2
---

# Position qualifiers
{{< rimImg src=../doubleBedroomsHouse.png height=220 >}}
  
The code of model above is needless to see. For this injection:   

  {{< rimImg src=quilt.png width=450 >}}

---
In `_Window_Bedroom_window`     
  - `Window`: type  
  - `Bedroom`: owner class name   
  - `window`: property name
---  

`__` : beginning with `__` means **outside**. In this case, `__Door` tells that referenced 
  element is outside `Bedroom`.  

---
`˚`: Now we redesign `Pillow` as below. In class `Cotton`, tag after `˚` tells the nearest root or nodes.    
{{< rimImg src=level.png width=600 >}}

---

  In addition, `˚` always appears when the corresponding `root/nodes` class is `abstract/open`, 
  for being distinguished in subclasses as `bar` and `x` is traced below.   

{{< rimImg src=abstractLevel.png width=350 >}}

{{< rimImg src=_abstractLevel.png width="600" >}}

# Type symbols conversion   
Android forbids most original common symbols in property names, making me choose these substitutes  `⍒` `❨` `❩`
`→` `‹` `›` `？` `，` `✶` `→` `↑` `↓` instead.
{{< rimImg src=typeSymbols.png >}}
  
{{< rimImg src=_typeSymbols.png >}}

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

# Generic types 
Generic types are converted to their bounds, and with `covariance` (`out`) if not redundant.  

{{< video src=generic >}}

# Multiple bounds
{{< rimImg src=multiBounds.png >}}

{{< rimImg src=_multiBounds.png >}}

`T` has two bounds which can be expressed in the first generated property. However, the second 
property type is limited by `Super` and `two bounds` can't be expressed, which is told by `✕` in 
the name.