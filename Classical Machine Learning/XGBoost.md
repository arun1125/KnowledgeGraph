# XGBoost
# Intuitive Explanation
1) Build model
2) Calculate loss
3) Find next model that reduces the loss the most
repeat 2&3 

# Technical Explanation
XGBoost is a variant of Gradient [[Boosting]] 

But it adds the following things
- It includes regularization taken from - [[Regularized Greedy Forest]]
- Includes a shrinkage parameter
- Column Subsampling taken from [[Random Forest]] and [[Bagging]]
- Better split finding algorithims 
	- Greedy
	- Approximate
		- Weighted Quantile Sketch
	- Sparsity Aware
	 
**Note on Shrinkage:** 

Defined as $\eta$  this scales newly added weights after each step of tree boosting

*Similar to a learning rate in [[SGD]], shrinkage reduces the influence of each indvidual tree and leaves space for future trees to improve the model*


# Related Concepts
# References


