---
title: Annotations
weight: 1
---

# Root
Each class with `@Tracer.Root` is considered as the trace start point, followed by its interior
public/internal elements. You probably have seen this model in the home page. 

<img src=../../singleBedroomHouse.png width=300/>
<br>
{{< tabs >}}
{{< tab "Build" >}}
Annotate the source class. Next build via the gradle tool.
{{< video src="build" >}} 
{{< /tab >}}
{{< tab "Model" >}} 
Model with positions and the tracer interface. 
{{< video src="model" >}} 
{{< /tab >}}
{{< tab "Rebuild" >}} {{< video src="rebuild" >}} {{< /tab >}}
{{< tab "Inject" >}} {{< video src="inject" >}} {{< /tab >}}
{{< /tabs >}}
<br>

# Nodes
Now we add one bedroom and its some inner objects as below. As nodes grow on root or other nodes, 
`Door` `LivingRoom` and `WifiRouter` are all built once and each of them represents **node**; 
`Bedroom` is repeatedly built and represents **nodes**.      

<img src=../doubleBedroomsHouse.png />
<br><br>

{{< video src="_nodes" >}}
<br><br>

# Tip
`Tracer.Tip` represents [trace end](https://apollokwok.github.io/TracerTutorial/docs/usage/details/#trace-ends), 
meaning elements of annotated classes wouldn't be traced insides.

{{< video src="tip" >}}
{{<hint info >}}
Context and the tracer interface are needless in a simple test.
{{< /hint >}}
<br>

# Omit
Properties and super types with `@Tracer.Omit` would be omitted, which is generally used 
with some unsupported new syntaxes, like `T & Any` before `ksp 1.8.0-1.0.9`(exclusive), 
and `context receiver`. Super type trace rules are later explained on page 
[Details](https://apollokwok.github.io/TracerTutorial/docs/usage/details/#partial-traceable-super-types). 
<br> 
{{<video src="omit" >}}  
{{<hint info >}}
`abstract class` makes inside abstract properties and functions needless to implement, which eases 
the test.
{{< /hint >}}