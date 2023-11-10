---
title: Basic
weight: 1
---

{{< hint warning >}}
Tracer annotations package is `pers.shawxingkwok.tracer` rather than that in videos.  
{{< /hint >}}

# Introduction
<image src="singleBedroomHouse.png" width=300></image>

{{< codeImg general.png >}}

The pictures above highlight the disadvantages of the general model style. Now, with `Kotlin-JVM`, `KSP`,
and minimal configuration, `Tracer` generates extension properties oriented toward internal traceable
elements, which realizes **dependency injection** in a novel way.

`Tracer` has several main advantages over common DI tools, as listed below:
- Minimal configuration.
- Powerful: generic types and variances are parsable, and it can trace super types as well.
- Fast: operates without runtime cache or reflection.

Contents below tell how `Tracer` models and extends relationships as shown in the top picture.

# Root
Each class with `@Tracer.Root` is considered as the trace start point, followed by its interior
public/internal elements. 

1. Annotate the source class (`House` in this case) with `Tracer` annotations.
2. Build.
3. Model with the generated tracer interfaces (`HouseTracer`).
   {{< video src=model >}}

4. Rebuild. There generates element traces as below.
   {{< codeImg generation.png >}}
5. Inject
   {{< video src=inject >}}

{{< hint info >}}
The renaming problem could be solved by an IDE plugin that has not come out yet.
{{< /hint >}}

# Nodes
There are some traced properties with the same type. If all their interior properties are traced with recurse from the root class, the 
generated traces would be huge and chaotic. Considering from a different perspective, repeatedly constructed 
objects should maintain independence in design, so that they do not require deep exploration when called. 
Take the example of a car model, you might access the cost of the wheels from the outside, but there is no need 
to drill down to the specific prices of the tires and axles.

Now we add one bedroom and its some inner objects as below. As nodes grow on root or other nodes, 
`Door` `LivingRoom` and `WifiRouter` are all built once and each of them represents **node**; 
`Bedroom` is repeatedly built and represents **nodes**.      

Now objects in the left picture are traced in the file `HouseElements`, and objects in the right picture 
are traced in the file `BedroomElements`. Note that, `OuterHouseTracer` is extended by `BedroomTracer`, making 
`HouseElements` starting with double `_` acquirable from `BedroomTracer`.

<img src=../doubleBedroomsHouse.png />
<br><br>

{{< video src="_nodes" >}}
<br>

Generated code
{{< tabs nodes >}}
{{< tab House >}}
{{< codeImg houseTracers.png >}}
<br>
{{< codeImg houseElements.png >}}
{{< /tab >}}

{{< tab Bedroom >}}
{{< codeImg bedroomTracers.png >}}
<br>
{{< codeImg bedroomElements.png >}}
{{< /tab >}}
{{< /tabs >}}

# Tip
`Tracer.Tip` represents the trace end, which means the carrier's elements are never traced. 

{{< video src="tip" >}}
{{<hint info >}}
Context and the tracer interface are needless in a simple test.
{{< /hint >}}

Elements or their type classes with features below are also considered as trace ends, meaning
they are never traced insides. Like classes with `Tracer.Root`, they should be designed independently
and avoid being exploring too insides.
- `Tracer.Root`, `Tracer.Nodes`
- Interface, enum, object
- Nullable
- Foreign (from other modules, generated code, or Java files)
- Rebuilt symbols
    - Constructor is not single and empty.
    - Generic
    - Abstract / open

{{< rimImg src="trace ends.png" >}}

No `_Int` appears.  
{{< rimImg src="_trace ends.png" width=680 >}}

# Omit
`Properties` and `super types` with `@Tracer.Omit` would be omitted, which is generally used 
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