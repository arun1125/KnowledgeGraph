Related to [[Statistical Significance and P values]], [[AB Test]]

### Confidence Intervals
focus on the observation instead of the null hypothesis.

Thought exercise:
Given the observation what values of the null hypothesis would lead to a decision not to reject assuming we specify a 5% false positive rate? 

The observation is 55% heads in 100 flips in our case so do not reject the null hypothesis. and we wouldnt if the values were 47.5, 50, 60% theres a whole range of values where we wouldnt reject the null 

This range of values is a confidence interval ... 
The set of values under the null hypothesis that would not result in a rejection given the data from the test.


We have a 95% confidence interval since we have a 5% significance level 

Interpretation is that ... 
under repeated experiments 95% of the time the true value will be covered by our confidence interval 


There is an equivalence between the confidence interval and the p-value, and both lead to the same decision: the 95% confidence interval does not cover the null value if and only if the p-value is less than 0.05, and in both cases we reject the null hypothesis of no effect.