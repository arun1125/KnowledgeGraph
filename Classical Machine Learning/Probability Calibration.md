important in [[Design Ads Prediction System]]

Probability Calibration 

When the empircal probablity matches the predicted probability 

so if we're saying that a user has 20% chance of having something then 20% of the time from our data we can measure that they have that specific thing

**Calibration**: Measure the ratio of average predicted rate and average empircal rate. 
i.e: The ratio of the number of expected actions to the number of actually observed actions


Why do we need calibration? 
If we have a big class imbalance then our predicted probabilities will be skewed so we need to calibrate our model to estimate the likelihood of a data point belonging to a class

![[Pasted image 20220417084430.png]]
