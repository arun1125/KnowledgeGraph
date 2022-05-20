# What is it? 
An A/B test is simply an experiment to see the effect of a change on a treatment group vs the control group. 

We want to see if the treatment group is actually effected by the change we made or if that change happened due to random chance. 

We run AB Tests to find CAUSAL conclusions 
 'If we roll out an experience to everyone and simply measure a metric before and after the change, there can be relevant differences between the two time periods that prevent us from making a causal conclusion.'


GOOD EXPERIMENTS HAVE:
- Have replication so you can measure variability
- Measure variability and compare it to the signal they are looking for
- Generalize to the problem you care about
- Transparent in code and data


Prediction != Inference

# How do we do it?
1) First come up with the idea lol - you need to know what you want to change!
2) Figure out how you're going to measure the change, is there a KPI you're keeping track of? CTR, successful searches, conversion rate etc
	- Along with the primary metric there usually are secondary metrics measuring other effects to help us identify the causal chain and how users behave
	- you will lead yourself to false conclusions or miss out on true conclusions if you don't set this up properly
3) Create your 2 samples - Try not to introduce any bias. If there is a variable you can't control for **Randomize it**
	- Treatment group 
	- Control group 
4) Collect data 
5) Conduct your A/B test

# [[Statistical Significance and P values]]
# [[Confidence Intervals]]
# [[Statistical Power]]

## Balancing Power and Significance
5% false positive rate is a widely accepted convention and the rule of thumb is to go for 80% false negative rate for a reasonable and meaningful effect size. 

so 

1) we postulate an effect size 
2) design the experiment - mainly be adjusting the sample size
	- such that if the true impace of the treatment experience is as we've postulated the test will correctly identify that there is an effect 80% of the time and 20% of the time the result will be a false negatvie 


Reasonable effect size is tricky and requires domain knowledge, common sense and some history.
 Given an understanding of past effect sizes, as well as the analysis strategy, we can set the sample size to ensure the test has 80% power for a reasonable metric movement.


Also dont work on problems that will yield trash results like a 0.000001% increase in user login like thats just not worth it
if the effect sizes seen in tests in a given innovation area are consistently immaterial to the user experience or the business, that’s a sign that experimentation resources can be more efficiently deployed elsewhere.

# Confidence - how do we make the decision? 
-   **Do the results align with the hypothesis?** If the hypothesis was about optimizing compute resources for back-end infrastructure, and results showed a major and statistically significant increase in user satisfaction, we’d be skeptical. The result may be a false positive — or, more than likely, the result of a bug or error in the execution of the experiment ([Twyman’s Law](https://en.wikipedia.org/wiki/Twyman%27s_law)). Sometimes surprising results are correct, but more often than not they are either the result of implementation errors or false positives, motivating us to dig deep into the data to identify root causes.

- Does the metrics story work together?  In evaluating test results, it’s important to look at changes in these secondary metrics, which are often specific to a particular experiment, to assess if any changes in the primary metric follow the hypothesized causal chain.

**Is there additional supporting or refuting evidence, such as consistent patterns across similar variants of an experience**? It’s common to test a number of variants of an idea within a single experiment. For example, with something like the Top 10 experience, we may test a number of design variants and a number of different ways to position the Top 10 row on the homepage. If the Top 10 experience is great for Netflix members, we’d expect to see similar gains in both primary and secondary metrics across many of these variants. Some designs may be better than others, but seeing broadly consistent results across the variants helps build that case in favor of the Top 10 experience.

**Do results repeat**? Finally, the surest way to build confidence in a result is to see if results repeat in a follow-up test. If results of an initial A/B test are suggestive but not conclusive, we’ll often run a follow-up test that hones in on the hypothesis based on learnings generated from the first test.



# Extentions
What if you wanted to test out N different effects? Try the [[Multi Arm Bandit]]
