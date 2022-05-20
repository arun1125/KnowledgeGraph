A/B Testing is a little like [[SGD]] where you are trying to find the right direction to move in to get to your global optima!


# Intro
AB TESTING and EXPERIMENTATION SCALES allowing you to evaluate decision making at scale and make the right choice with empirical data. 

PRocess
- Form a hypothesis
- Gather empirical data from experiements 
- Evaluate it against our hypoetheses and then make conclusions
- Then generate new hypothesis 

# What is an AB Test?
Okay so we have this new idea that we think is going to revolutionize netflix (it can be small too and usually is as incremental decisions are the best (learning rate in [[SGD]]))

For this new idea we need to set up an experiment where 
- one group - the treatment group - gets this new change and 
- another group - the control group - keeps the old product feature
- WE define what metrics we want to measure! <- This is SUPER important! because if you dont have a proper measure that you are well ... measuring then you will lead yourself to false conclusions or miss out on true conclusions so when setting up the experiment you need to ...
1) First - Come up with the idea
2) Second - Figure out how you would accurtately measure the idea
3) set up your control and treatment groups



We randomly select the treatment group and control group to ensure there isnt any bias in our samples that might skew the results in one way or another

Say we run the experiment half way through the month and start collecting data and see this:
![[Pasted image 20220427082735.png]]
(product B is the treatment group)

We would probably think wow! our change made a huge difference BUT
BUT BUT BUT

what if there was something else that caused this spike? 
This is a netflix context so what if there was a new show that cause this spike or a new movie release or social media buzz?? 


How would you be able to tell ... 

you wouldn't. We need to figure out the correct Causal Chain [[Causal Inference]]

How can we do that? 
Well run the experiment with the treatment and control group at the same time and over a longer duration

![[Pasted image 20220427083310.png]]

We run AB Tests to find CAUSAL conclusions 
 'If we roll out an experience to everyone and simply measure a metric before and after the change, there can be relevant differences between the two time periods that prevent us from making a causal conclusion.'

Along with the primary metric there usually are secondary metrics measuring other effects to help us identify the causal chain and how users behave

Once we are confident in what we have found we can either roll out or keep the existing product 








**THERE ARE TWO TYPES OF ERRORS FALSE POSITIVES (TYPE 1) AND FALSE NEGATIVES (TYPE2) WE WILL BE DISCUSSING TYPE 1 error**


# Interpreting Results: False POsitives and Statisitcal significance



A False negative error is when the data from the experiment shows that there isn't any significant difference between treatment and control when there actually is

These types of errors from AB tests are ANALGOUS to [[Binary Classification]] metrics like TN, FP, FN, TP

![[Pasted image 20220427091450.png]]




## False Positives and Statisitcal Significance
After setting up our AB test etc we need to establish an acceptable false positive rate. By convention this is 5% meaning

For tests where there isn't a meaningful difference between the treatment and control group we will falsely reject the null hypothesis 5% of the time.

Tests that are conducted with this 5% false positive rate are said to be run at a 5% significance level
(we'll label a non cat picture a cat 5% of the time)

## P values - urgh
P-Value: The P value is the probability of seeing an outcome given that the null hypothesis is true



### P Values and Statistical Significance

To illustrate the idea we use a coin flip example

Say we wanted to see whether a coin was fair or not after 100 flips 

and we got 55 heads 

Would you consider it fair? and at what number of heads (or tails) would you consider the coin to be unfair

We need to come up with a decision rule and understand the assocaited false positive rate


H0 = The Null hypothesis = the coin is fair

We calculate the probability of EVERY outcome possible given that the null is true
so for the coin flipping example its 
prob of seeing 0 heads, 1 head, 2 heads, 3, heads ... 100 heads

to get a distribution of probabilities of outcomes given that the null hypothesis is True

To quantify if this observation is compelling evidence that the coin is not fair, we count up the probabilities associated with **_every outcome that is less likely than our observation_**

We've made no assumption about heads > tails or vice versa so we need to take a two tail approach
We sum up the probabilities of getting more thatn 55% heads and 55% tails 
![[Pasted image 20220427175825.png]]

Solid red is 55% heads
dashed red is 55% tails


Our mythical p value is essentially (i think ive mentioned it before) - the probability of seeing this outcome given that the null hypothesis was true

the p value here is 0.32

that means if we repeated this experiment 100 times then 32% of those experiments would have at least 55 heads (this is all with fair coin though)

How do we use the p value tho? 
comes back to our SIGNIFICANCe LEVEL! which was 5% (Set as a default)

we reject the null hypothesis and accept H1 if the p value of our experiment is less than the significance level. but since our p value is 0.32 we can say that "THERE ISNT SUFFICIENT EVIDENCE TO REJECT THE NULL HYPOTHESIS" THIS IS NOT THE SAME AS SAYING H0 IS TRUE! just that we dont have enough evidence to reject H0


## Confidence Intervals and Rejection Reigons



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












# Confidence - how do we make the decision? 
-   **Do the results align with the hypothesis?** If the hypothesis was about optimizing compute resources for back-end infrastructure, and results showed a major and statistically significant increase in user satisfaction, we’d be skeptical. The result may be a false positive — or, more than likely, the result of a bug or error in the execution of the experiment ([Twyman’s Law](https://en.wikipedia.org/wiki/Twyman%27s_law)). Sometimes surprising results are correct, but more often than not they are either the result of implementation errors or false positives, motivating us to dig deep into the data to identify root causes.

- Does the metrics story work together?  In evaluating test results, it’s important to look at changes in these secondary metrics, which are often specific to a particular experiment, to assess if any changes in the primary metric follow the hypothesized causal chain.

**Is there additional supporting or refuting evidence, such as consistent patterns across similar variants of an experience**? It’s common to test a number of variants of an idea within a single experiment. For example, with something like the Top 10 experience, we may test a number of design variants and a number of different ways to position the Top 10 row on the homepage. If the Top 10 experience is great for Netflix members, we’d expect to see similar gains in both primary and secondary metrics across many of these variants. Some designs may be better than others, but seeing broadly consistent results across the variants helps build that case in favor of the Top 10 experience.

**Do results repeat**? Finally, the surest way to build confidence in a result is to see if results repeat in a follow-up test. If results of an initial A/B test are suggestive but not conclusive, we’ll often run a follow-up test that hones in on the hypothesis based on learnings generated from the first test.

# Further Topics to investigate
https://netflixtechblog.com/experimentation-is-a-major-focus-of-data-science-across-netflix-f67923f8e985

[[Bayesian inference]]
[[Group Sequential Testing]]
[[Bayesian Decision Theory]]
[[Difference in differences techniques]]
[[Gaussian Bayesian Inference]]
[[Adaptive Testing]]
[[inverse propensity scores]]
[[doubly robust estimators]]
[instrumental variables] ? 
[longitudinal analyses] ?

[synthetic controls]?
[[Structural Models]]

[[Sensitivity]]

[[Bandits]]
[[Contextual Bandits]]

Type S error - sign error -> type s error implies we would have selected the wrong treatment to promote to produciton
Type M error - Magnitude error -> means that we are overestimating the effect of this impact

Visualizing quantiles
nflx use high performance bootstrap methods for compressed data to estimate distributions and quantile treatment effects


**Alternatives to A/B testing**. The Open Connect engineering team faces numerous measurement challenges. [Congestion can cause interactions](https://arxiv.org/abs/2110.00118) between treatment and control groups; in other cases we are unable to randomize due to the nature of our traffic steering algorithms. To address these and other challenges, we are investing heavily in [quasi-experimentation methods](https://netflixtechblog.com/quasi-experimentation-at-netflix-566b57d2e362). We use [Metaflow](https://netflixtechblog.com/open-sourcing-metaflow-a-human-centric-framework-for-data-science-fa72e04a5d9) to pair existing infrastructure for metric definitions and data collection from our Experimentation Platform with custom analysis methods that are based on a difference-in-difference approach. This workflow has allowed us to quickly deploy self-service tools to measure changes that cannot be measured with traditional A/B testing. Additionally, our modular approach has made it easy to scale quasi-experiments across Open Connect use cases, allowing us to swap out data sources or analysis methods depending on each team’s individual needs.


https://research.netflix.com/business-area/personalization-and-search

https://netflixtechblog.com/a-b-testing-and-beyond-improving-the-netflix-streaming-experience-with-experimentation-and-data-5b0ae9295bdf

https://docs.metaflow.org/getting-started/tutorials

https://www.msi.org/wp-content/uploads/2020/06/MSI_Report_15-122.pdf

https://netflixtechblog.com/tagged/causal-inference