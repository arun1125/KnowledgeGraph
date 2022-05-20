[[Design Entity Linking System]]

# Micro Average Metrics

Say you have a classification problem with 3 classes
A,B,C

Micro average metrics would be a metric calculated based on the entire population rather than subsetting on individual classes and calculating metrics for them then averaging those out


so Micro Averaged Precision would be 
$$\frac{\sum_{i=1}^{n}TP_i}{\sum_{i=1}^{n}TP_i+\sum_{i=1}^{n}FP_i}$$
Where 
n = Number of documents
TP_i is the true positives in document I
FP_i false positive in document i


And Micro Averaged Recall would be

$$\frac{\sum_{i=1}^{n}TP_i}{\sum_{i=1}^{n}TP_i+\sum_{i=1}^{n}FN_i}$$


and Micro Averaged F1 would be

![[Pasted image 20220416121412.png]]




[[Macro Average Metrics]]
