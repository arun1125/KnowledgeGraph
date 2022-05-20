
# Why? - Problems with AB testing

You can probably perceive several difficulties with that approach. First, our answer may be inconclusive: “effect not proven.” In other words, the results from the experiment may suggest an effect, but if there is an effect, we don’t have a big enough sample to prove it (to the satisfaction of the traditional statistical standards). What decision do we take? Second, we might want to begin taking advantage of results that come in prior to the conclusion of the experiment. Third, we might want the right to change our minds or to try something different based on additional data that comes in after the experiment is over. The traditional approach to experiments and hypothesis tests dates from the 1920s and is rather inflexible.

- What do you do if you have multiple treatments you would like to try?
- What if you need to make RAPID decisions? 

You could run [[AB Test]] on all your possible treatment combinations but that would take FOREVER and potentially we could find the results out of the experiment a lot faster than the actual length of the AB test! 

imagine if your treatment was garbage you can probably find that out within the first week or so instead of waiting 3 months for your AB test

So How can you Encorporate your online learnings into your AB test? 

# MULTI ARM BANDITS!
- Allows more flexibility
- Allows us to reduce exposure of trash options
- Can test N variations

Deploy all variations and have a prob chance to give each of your A/B/C/D/... variations 

Each have a uniform prob of being chosen
Then the better ones get higher prob but the ones that are worse still have a chance of being chosen 

So good ones will be chosen more often but everyone will have a fair chance of being chosen. If you are trash then you wont be chosen often 


Many algoirthms for choosing the variation we show next to customers 
[[Thompson Sampling]] is an advanced one 

But 
for simplicity we use

the epsilon-greedy algorithm for an A/B test:

1. Generate a uniformly distributed random number between 0 and 1.

2. If the number lies between 0 and epsilon (where epsilon is a number between 0 and 1, typically fairly small), flip a fair coin (50/50 probability), and:

	a. If the coin is heads, show offer A.

	b. If the coin is tails, show offer B.

3. If the number is ≥ epsilon, show whichever offer has had the highest response rate to date.

Epsilon is the single parameter that governs this algorithm. If epsilon is 1, we end up with a standard simple A/B experiment

If epsilon is 0, we end up with a purely greedy algorithm—one that chooses the best available immediate option