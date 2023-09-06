---
title: New UI design proposal
weight: 2
---

# Abstract
The implementation requires official assistance to design visual operation interfaces.
The goal is to make view source code from the UI department directly usable by IT developers, including visually
designed animations, which corresponding to a new imperative multiplatform UI system with Kotlin. If this could be 
implemented, it could reduce most code as well as negotiations between UI and IT developers, which is revolutionary 
without doubt.
{{< hint info >}}
Many expected effects are explained with abstract words. Hope you to be patient.
{{< /hint >}}

# All positions reference to borders 
All the traditional layouts are deprecated, e.g. frame, linear, relative, and constraint.
Each component is added to the panel with default position limits. For example, a `TextView` is added to the top-right 
area, and the panel shows its distances to top and right borders. (This is by default and editable.)
Besides, the distances are directly editable with mouse or keyboard in the panel. The two blue arrows should be 
removed.  
<img src=firstTextView.png width=300/>  

# Relative positions are displayed but actually excluded
Now I add a `button` at the top-right area near the added `TextView`. It's also linked to top and right borders by 
default. And the relative distance tagged with my red line should be also displayed, but excluded in the background 
source code.   
<img src=firstButton.png width=300 />  

# Independent components
Components can't be constrained with each other, but multi-select-editable. Components from another file are seen as 
a single component.
<img src=multi-select.png width=200/>  

# New attributes panel
The right-side attributes panel is replaced with source code of the selected component exception positions.  
From   
<img src=buttonAttributes.png width=200/>  
to manual code like    
<img src=buttonXml.png width=300/>  
There popups a progressbar when you type some attributes needing visual modifications like rotation and alpha.

# Custom shapes and animations
These are designed as in `PhotoShop` and `Figma`, but embeddable in mainstream systems and bound-able with foreign Kotlin 
data. It sounds difficult but doable for a qualified official team. Even if it's not realized, this proposal is 
significant enough to beat other patterns. 