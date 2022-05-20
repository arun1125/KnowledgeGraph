https://arxiv.org/pdf/1808.09781.pdf

https://engineering.linkedin.com/blog/2018/09/open-sourcing-tony--native-support-of-tensorflow-on-hadoop


## Previous Approaches
[[RNN]] - need dense data
[[CNN]]
[[Markov Chain]] - for sparse data and recommendations 
[[Matrix Factorization]]

## Current Approach 
[[Transformer]] but modified



## Related Approaches
1) general recommendation
2) temporal recommendation
3) sequential recommendation
4) attention mechanism



## Methodology
**Input**
- the first N-1 items

**output**
- Item 2 to N

### Embedding Layer
## [[Self-Attention]] Block
## Casuality 
- DAta Leakage
## point wise FFN 
Add nonlinearities 
## Stack self-attention block
input -> self attention -> FFN -> self attention -> FFN etc 

stacking the blocks causes over fitting, and model gets large AF

how to fix this? add [[Dropout]]
apply layer normalization on x -> apply g -> apply dropout on g output -> x + output 

## Prediction
?????
## Evaluation 
Binary [[Cross Entropy]] Loss
[[Adam]] Optimizer
## drawbacks
Cannot scale to long sequences 


## How SASrec relates to others
Generalization of [[Collaborative Filtering]] models - but how? 

- Factorized Markov Chains (worth its own page?)
	factorizes a first-order item transition matrix and predicts the next item j depending on the last item i
	
	$$P(j|i) \propto M_i^TN_j $$
	okay so how does SASrec related to the above? 
	if we set the self attention block to 0, use unshared item embeddings (thats right they share embeddings between ... layers? idk ) and remove the position embedding (position of item in sequence)
	SASrec reduces to FMC

- SASrec related to FPMC (Factorized Personalized markov chains)
	FPMC fuses MF with FMC


- how it relates to FISM


- MC Based Recommendations
- RNN based recommendations 