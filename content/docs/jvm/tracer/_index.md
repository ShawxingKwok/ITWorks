---
title: Tracer
weight: 1
bookCollapseSection: true
---

# Abstract
With `Kotlin-JVM`, `KSP` and little configuration, `Tracer` generates extensional properties 
oriented to interior traceable elements, which realizes **dependency injection** in a novel way. 

# Main advantages
- Little-config
- Powerful: generic types and variances are parsable, and super types are also traceable.   
- Fast: no cache or runtime reflection

# Basic sample
<image src="singleBedroomHouse.png" width=300></image>

{{< tabs >}}
{{< tab "General" >}} <image src=general.png ></image> {{< /tab >}}
{{< tab "Tracer" >}} 
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
{{< /tab >}}
{{< /tabs >}}