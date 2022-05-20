# t-Tests

Used becasue back in the day [[Permutation Test]]'s were infeasbile
SO
Statisticians found that a good approximation to the permutation (shuffled) distribution was the t-test, based on [[Students t distribution]]. It is used for the very common two-sample comparison—A/B test—in which the data is numeric. But in order for the t-distribution to be used without regard to scale, a [[#Standardized Form]] of the test statistic must be used.


# Standardized Form
## Degress of Freedom 
- The number of values that are free to vary - this is for calculated statistics only
- If you had 10 samples and calculated the Mean then the DoF is 9 because if you have 9 variables the 10th is fixed


We use DoF as input into statistical tests to remove bias but in most cases we have so much data this doesnt matter. 

There is one context, though, in which it is relevant: the use of factored variables in regression (including logistic regression). Some regression algorithms choke if exactly redundant predictor variables are present. This most commonly occurs when factoring categorical variables into binary indicators (dummies). Consider the variable “day of week.” Although there are seven days of the week, there are only six degrees of freedom in specifying day of week. For example, once you know that day of week is not Monday through Saturday, you know it must be Sunday. Inclusion of the Mon–Sat indicators thus means that also including Sunday would cause the regression to fail, due to a multicollinearity error.
# ANOVA
What if we are doing an A/B/C/D test? 
We have to compare AB, AC, AD, BC, BD, CD and then we will suffer from the multiplicity problem

instead we can compare ABCD at the same time with ANOVA

Rephrase the problem into "Could all the pages have the same underlying stickiness, and the differences among them be due to the random way in which a common set of session times got allocated among the four pages?"

The procedure used to test this is ANOVA. The basis for it can be seen in the following resampling procedure (specified here for the A/B/C/D test of web page stickiness):

1. Combine all the data together in a single box.
2. Shuffle and draw out four resamples of five values each.
3. Record the mean of each of the four groups.
4. Record the variance among the four group means.
5. Repeat steps 2–4 many (say, 1,000) times.

What proportion of the time did the resampled variance exceed the observed variance? This is the p-value.

## F-Statistic
Used back in the day when computers werent available to do ANOVA

$F statistic = \frac{MS(treatment)}{MS(error)}$

Where $MS = \frac{SumOfSquares}{DoF}$


## Two-Way ANOVA
The A/B/C/D test just described is a “one-way” ANOVA, in which we have one factor (group) that is varying. We could have a second factor involved—say, “weekend versus weekday”—with data collected on each combination (group A weekend, group A weekday, group B weekend, etc.)


We can see how this is leading on to Regression Modelling 
[[Linear Regression]]
[[Logisitc Regression]]



# Chi-Square Test
For count data

Build a contingency table (frequency table)

The Chi-Squared Statistic is 

$\sum{R^2}$
where R is 
$R = \frac{Observed - Expected}{\sqrt{Expected}}$

Across all rows and columns

Example 
![[Pasted image 20220502200928.png]]
![[Pasted image 20220502200945.png]]
![[Pasted image 20220502200954.png]]

We can test with this resampling algorithm:

1. Constitute a box with 34 ones (clicks) and 2,966 zeros (no clicks).

2. Shuffle, take three separate samples of 1,000, and count the clicks in each.

3. Find the squared differences between the shuffled counts and the expected

counts and sum them.

4. Repeat steps 2 and 3, say, 1,000 times.

5. How often does the resampled sum of squared deviations exceed the observed?

That’s the p-value.

### The actual chi-squared test if you cant resample
you standardize your statistic w the DoF = (r-1) x (c-1)


## Good for Feature selection
