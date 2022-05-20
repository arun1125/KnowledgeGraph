[[Design Entity Linking System]]

Say you have a classification problem with 3 classes
A,B,C

And you want to see how well your classifier did but you see that it sucked for A but was good for B,C

Then what you could do is calculate Macro averaged Metrics which are calculate your metrics for each sub sample then Average them out 

Macro Averaged Precision would be

$$\frac{\sum_{i=1}^n P_{di}}{n}$$
where P_di = precision over document i

and Macro Averaged Recall would be

$$\frac{\sum_{i=1}^n R_{di}}{n}$$

Where R_di is the recall over document I


and F1-Score would be
![[Pasted image 20220416121642.png]]


[[Micro Averaged Metrics]]