# IoU

[[Design Image Segmentation]]
Offline Metric


Area of Intersection/Area of Union

![[Pasted image 20220415094240.png]]

Metric ranges from 0-1 or 0-100% where 1 or 100% means a perfect overlap and 0 means they were missed

For each image you can have multiple objects to segment so this is a **Multi class segmentation** problem.

For each image we want to find the _average_ IoU

Example:
![[Pasted image 20220415095616.png]]

![[Pasted image 20220415095552.png]]
