---
title: Details
weight: 3
---

# Suggested code order
{{< rimImg src="order.png" width="650" >}}  

# Lazy
Use `lazy` when you need to construct some objects based on common injected elements.   
{{< rimImg src="lazy.png" width="620" >}}

# Override style
{{< rimImg src="override style.png" width="350" >}}

- Put it at the bottom to look better.
- Use `get() =` instead of `=` to avoid `null` when [passing args](#pass-args).

# Pass args
{{< rimImg src="pass args.png" width="480" >}}

<p align="center">↓</p>

{{< rimImg src="_pass args.png" width="620" >}}  

# Tracer interface conversion
{{< video src=conversion >}}
This would be needless in the expected new syntax.

# Elements built times display
{{< video src="builtTimes" >}}
<br>

# Java, K/N & K/JS
`Java`, `Kotlin/Native` and `Kotlin/JS` are not supported, because they lack `context receiver` 
which is essential in this tool. But it's absolutely safe to reference `Java classes and functions` 
in Kotlin files.

# Internally generated
Makes all generated code internal, which is commonly used in submodules.
  {{< video src="internal" >}}

# Partial traceable super types
{{< rimImg src=superTypes.png width="600" >}}

{{< rimImg src=_superTypes.png >}}

# Type alias
Alias types are always converted to its actual types.

{{< rimImg src="alias.png" width=300 >}}

{{< rimImg src="_alias.png" width="680" >}}
&nbsp;&nbsp;

[//]: # (## Var)

[//]: # (Source code)

[//]: # (```kotlin)

[//]: # (@Tracer.Root)

[//]: # (class VarSample {)

[//]: # (  var x = 1)

[//]: # (})

[//]: # (```)

[//]: # (Generated code)

[//]: # (```)

[//]: # (public var VarSampleTracer.`_Int_VarSample_x` )

[//]: # (  inline get&#40;&#41; = `_VarSample`.`x` as Int)

[//]: # (  inline set&#40;value&#41;{ `_VarSample`.`x` = value })

[//]: # (```)

[//]: # (Injection sample)

[//]: # (```kotlin )

[//]: # (context &#40;VarSampleTracer&#41;)

[//]: # (class Foo{)

[//]: # (  private var x: Int)

[//]: # (      get&#40;&#41; = _Int_VarSample_x)

[//]: # (      set&#40;value&#41; { _Int_VarSample_x = value }  )

[//]: # (})

[//]: # (```  )

[//]: # ({{<hint info >}})

[//]: # (Expected reference after official support.)

[//]: # (  ```kotlin)

[//]: # (  context &#40;VarSampleTracer&#41;)

[//]: # (  class Foo{)

[//]: # (    private var _x: Int by this@VarSampleTracer::_Int_VarSample_x)

[//]: # (  })

[//]: # (  ```)

[//]: # ({{< /hint >}})