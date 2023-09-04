---
title: New UI design proposal
weight: 2
---

{{< hint danger >}}
UNFINISHED
{{< /hint >}}

# Abstract
The implementation requires official assistance to design visual operation interfaces.
The goal is to make view source code from the UI department directly usable by IT developers, including visually
designed animations. If this could be implemented, it could reduce most code
as well as negotiations between UI and IT developers, which is revolutionary without doubt.
{{< hint info >}}
Most of the expected effects are explained in abstract words. Hope you to be patient.
{{< /hint >}}

# Layout
Take the example of this console from `Android studio`. All the traditional layouts are deprecated, e.g. frame, linear, 
relative, and constraint.

![](wholePanel.png)

1.Each component is added to the panel with default position limits. For example, a `TextView` is added to the top-right 
area, and the panel shows its distances to top and right borders. (This is by default and editable.)
Besides, the distances are directly editable with mouse or keyboard in the panel. The two blue arrows should be 
removed.  
<img src=firstTextView.png width=300/>  
2. Now I add a component at the top-right area near the added `TextView`. It's also linked to top and right borders by 
default. And the relative distance tagged with my red line should be also displayed, but excluded in the background 
source code.   
<img src=firstButton.png width=300 />  
3. Components can't be constrained with each other unless they are from other files and referenced in this panel,
which means they become a single component. However, you could select them and edit together.  
<img src=multi-select.png width=200/>  
4. The right-side attributes panel is replaced with partial source code of the selected component.  
From   
<img src=buttonAttributes.png width=300/>  
to   
<img src=buttonXml.png width=300/>  
There popups a progressbar when you type some attributes needing visual modifications like rotation and alpha