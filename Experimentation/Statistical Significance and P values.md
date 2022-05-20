Statistical Significance is closely related to the type 1 error in [[Binary Classification Errors]] 

## False Positives and Statisitcal Significance
After setting up our [[AB test]] etc we need to establish an acceptable false positive rate. By convention this is 5% meaning

For tests where there isn't a meaningful difference between the treatment and control group we will falsely reject the null hypothesis 5% of the time.

Tests that are conducted with this 5% false positive rate are said to be run at a 5% significance level


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

### Rejection Regions
The decision rule we spoke of before was this whole p value being less than the significance level

but we can come up with an alternative decision rule : Rejection Regions! 
Rejection Regions are the set of values for which we'd conclude that we can reject the null hypothesis. 

The rejection reigon is the set of least likely outcomes with probabilities that sum to no more than 0.05 so essentially they consist of the outcomes that are the MOST extreme.

The rejection reigon for our coin flip is defined by the BLUE bars in the above diagram. ~ 40 adn 60% heads.

**There is an equivalence between the rejection region and the p-value, and both lead to the same decision: the p-value is less than 0.05 if and only if the observation lies in the rejection region.**