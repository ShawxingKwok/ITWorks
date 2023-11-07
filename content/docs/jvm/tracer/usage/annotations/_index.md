---
title: Annotations
weight: 1
---

{{< hint warning >}}
Tracer annotations package is `pers.shawxingkwok.tracer` rather than that in videos.  
{{< /hint >}}

# Root
Each class with `@Tracer.Root` is considered as the trace start point, followed by its interior
public/internal elements. The code sample is displayed in the {{< newTab "home page" "../../" >}}. 

# Nodes
Now we add one bedroom and its some inner objects as below. As nodes grow on root or other nodes, 
`Door` `LivingRoom` and `WifiRouter` are all built once and each of them represents **node**; 
`Bedroom` is repeatedly built and represents **nodes**.      

<img src=../doubleBedroomsHouse.png />
<br><br>

{{< video src="_nodes" >}}
<br>

# Tip
`Tracer.Tip` represents the trace end in which elements wouldn't be traced.

{{< video src="tip" >}}
{{<hint info >}}
Context and the tracer interface are needless in a simple test.
{{< /hint >}}

# Omit
Properties and super types with `@Tracer.Omit` would be omitted, which is generally used 
with some unsupported new syntaxes, like `context receiver`. 
Super type trace rules are later explained on page 'Details'.  
<br> 
Source code
{{< codeImg omit.png >}}
<br>
`i` with `context receiver` is omitted in generated code now. 
{{< codeImg _omit.png >}}
{{<hint info >}}
`Tracer interfaces` are not essential to implement in simple tests. 
{{< /hint >}}