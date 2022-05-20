Statistical power is closely related to the type 2 error in [[Binary Classification Errors]] 

# False Negatives and POWER
False negative is incorrectly identifying a true example as a false one

statistical power is 1-False negative rate

with false positives and statistical significance we looked at the distribution of values if the null hypothesis was true

with false negatives and statistical power we look at the distribution of values if the alternative hypothesis was true

![[Pasted image 20220428071736.png]]


so in the above situation we are considering a fair coin (H0) the null distribution and then an unfair coin (H1) (at 64% heads) the alternative distribution

Statistical power is the fraction of the alternative distribution that lies **beyond** the critical values under the null hypothesis (the blue line) (so to the right of the blue line) - 80% of the alternative distribution lives on the right of the blue line

A small percentage lives on the left that would be the false negative region? I guess ... 

This 80% and 64% heads numbers werent just made up. 
like the 5% significance level the 80% is deemed convention and so we postulate (come up with afterwards) the % of heads

The interpretation is as follows: if the coin has probability of heads equal to 64%, and we repeatedly run the experiment of flipping 100 times and making a decision at the 5% significance level, then we will correctly reject the null hypothesis that the coin is fair in about 4 out of every 5 experiments. And 20% of those repeated experiments will result in a false negative: we’ll not reject the null hypothesis that the coin is fair, even though it is unfair.


### How to increase power?
Goal is to make as much of the alternative distribution lie on the right hand side of the critical values as possible how can we do that? 


1) Larger sample size - this will make each distribution more peaky and so the % of the distribution to the right of the crticial value will be larger
	- in a business context run larger AB tests


2) Effect size - running an [[AB test]] to check whether a coin was 51% heads would be much harder, especially to avoid False negatives but if the true value of the coin was 80% heads it would be much easier. 
	- In a business context; make more Bold changes! 

3) The underlying metric you are measuring might have variance and so itll be harder to determine. 
	- say you are trying to measure the time from clicking play to the video starting different clients and different internet speeds will have an effect on this so itll be harder to determine if the changes you made had an actual effect vs just their internet being good or bad on that specific day