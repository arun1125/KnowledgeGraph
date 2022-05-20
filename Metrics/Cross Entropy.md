[[Loss Function]]
[[GANs]]
[[KL Divergence]]


The Cross entropy loss function is defined as

$H(P,Q) = -E_{x\sim P} [logQ(x)]$


Cross Entropy is usually used to identify specifically the negative log liklihood of a [[Bernoulli]] or [[Softmax]] [[Distributions]] but this isn't entirely accurate

Any loss consisting of a [[negative log liklihood]] is a cross entropy between the [[Empirical]] distribution defined by the training data and the probability distribution of the model. 

For Example

[[RMSE]] is the cross entropy between the empirical distribution and the [[Gaussian]] distribution

So I guess?? Cross entropy is a measure of how close one distribution is to another distribution

- Minimizing the KL divergence corresponds exactly to minimizing the cross entropy between distributions


## Binary Cross Entropy 

![[Pasted image 20220415120625.png]]