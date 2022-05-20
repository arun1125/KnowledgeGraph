# What is it? 
This is a procedure where we resample the data and recompute the statistic many many many times and see how often the actual statistic from the [[AB Test]] was greater than your resampled ones. 

The number of times the AB test statistic was greater than your resampled ones is your P value - from [[Statistical Significance and P values]] 

# Why? 
We do this resampling to asses the random variability in the statistic you are calculating 

# How? 
1. Combine your results from groups A and B - this is equivalent of your NULL HYPOTHESES

2. Shuffle the combined data and then randomly draw (without replacement) a resample of the same size as group A (clearly it will contain some data from the other groups), then group B 
3. Whatever statistic or estimate was calculated for the original samples (e.g., difference in group proportions), calculate it now for the resamples, and record; this constitutes one permutation iteration.

4. Repeat the previous steps R times to yield a permutation distribution of the test statistic.

5. Now take the observed difference from groups A and B. If this observed difference is in the range of the differences from your permutation test then the difference was just due to chance but if the difference of A and B was outside the range of the resampled then your results are statistically significant. 

6. You can calculate your P value by seeing the number of differences from your permutation test that are greater than your observed difference

**The Above is called a RANDOMIZED PERMUTATION TEST**

# Exhaustive and Boostrap Permutation Tests
## Exhaustive
- You find ALL possible combinations of the data being shuffled - practical only for small sample sizes 

If you do enough random shufflings of the randomized permutation test then this approaches the exhaustive in the limit

## Bootstrap
- The sampling is done **with** replacement 
- In this way the resampling procedure models not just the random element in the assignment of treatment to subject but also the random element in the selection of subjects from a population


# Example

