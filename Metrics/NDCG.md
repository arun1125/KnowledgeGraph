# NDCG - Normalized Discounted Cumulative Gains

What is this metric?
Before we get into that it is best if we build this metric up from its roots

## CG
CG is Cumulative Gain 

$$CG_p = \sum_{i}^{p} rel_i$$
$rel_i$ = Relevance rating of a document
$i$ = position of document
$p$ = position up to which the documents are ranked
(this p didnt make sense to me but each search query will return up to p results and CG and all subsequent metrics are relative to this p. Imagine we p = 4 vs 20 the relevance of the top 4 in both situations will be different!)

### Example
Say our Search enginer ranks Documents $D_1$ to $D_4$ with $D_1$ being the most relevant one and $D_4$ being the least.

Our human rater is shown this rating and then gives their own relevance score for $D_1$ to $D_4$ 
We then take the cumulative sum of these scores as the CG.

Documents | Ranking/Rating by Human Rater 
------------ | ------------
D1 | 3
D2 | 2
D3 | 3
D4 | 0

$CG_4 = 3 + 2 + 3 +0 = 8$

**Problems**
- This does not penalize the search engine for ranking more relevant documents later

in comes DCG

## DCG
Discounted CG we penalize lower ranked searches that were still relevant to us 

$$DCG_p = \sum_{i}^{p} \frac{rel_i}{log_2(i+1)}$$
so our above $CG_4$ of 8 will actually have a

$DCG_4 = \sum_{i}^{p} \frac{rel_i}{log_2(i+1)} = 3 + 1.262 + 1.5 + 0 = 5.762$

You see the third element doesnt have a value of 3 like CG instead it is discounted by its position in the ranking

This is great for evaluating one search but evaluating anything is meaningless unless we can compare it with others. How can we do this? well $NDCG_p$ ! but to before we get there we need to have a look at $IDCG$ 

## IDCG
is the ideal DCG which can be found using human rankers of relevant queries 

We take our ranking and then the human raters will come up with an IDEAL ranking order for our results 

Documents | Ranking/Rating by Human Rater | Ideal Ranking
------------ | ------------ | ------------ 
D1 | 3 | D1
D2 | 2 | D3
D3 | 3 | D2
D4 | 0 | D4

This will give us an 
$IDCG_p = 5.898$

## NDCG
Now we can compute a score between 0-1 for all our searches using $IDCG_p$
An $NDCG_p$ of 1 is good because that means our ranking was close to the human ranking

$$NDCG_p = \frac{DCG_p}{IDCG_p}$$

For our above example

$NDCG_p = \frac{DCG_p}{IDCG_p} = \frac{5.762}{5.898} = 0.976$

This value only represents one query though, to evaluate our entire model we use 

$NDCG = \frac {\sum_{i}^{N} NDCG_i}{N}$

Which is essentially the average NDCG across all queries performed


**Problems**
No metric is perfect and neither is this one
The downfalls of this metric are the fact that it does not penalize irrelevant search results so we're assuming our results are relevant to begin with.

The human rater could assign negative scores for these irrelevant results moving forward 

