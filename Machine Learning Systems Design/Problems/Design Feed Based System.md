# Design Feed System
# Problem Statment
Design a twitter feed system that will show the most relevant tweets for a user based on their social graph -> Applies to most feed systems like fb or instagram

### Why?
Feeds used to be chronologically ordered but due to the volume of data being posted on say twitter/fb/instagram users would potentially miss out on tweets they found engaging

Now feeds are based on a **Relevance Ranking** per user

## Scale
- Assume
	- 500 Million DAU
	- Each user is connected to 100 people
	- logs in 10 times a day

This all means that we need to call our ranking model 5 BILLION times... 

_“Given a list of tweets, train an ML model that predicts the probability of engagement of tweets and orders them based on that score”_

# Metrics
## User Actions
We want to measure user actions to come up with metrics, user actions can be broken down into
### Positive
- Time spent viewing tweet
- liking a tweet
- retweeting
- commenting on a tweet
### Negative
- hiding a tweet
- reporting a tweet

## User engagement
All these metrics can be viewed in aggregate with different visualizations and based on an analysts judgement we can decide how to move forward
![[Pasted image 20220412113033.png]]

## Metric to choose
Choosing a metric to optimize depends on the business context

- Do we want more people liking / commenting or retweeting? 
- Maybe we want to optimize time spent on the app

## Negative engagement / Counter metric
We need to find a way to incorporate negative metrics and we can do this by keeping track of counter metrics like number of reports on said tweet or block a user etc

We can aggregate these and call it the 'average negative action'
## Weighted engagement
Since we have many different metrics
	- likes
	- comments
	- retweets
	- reports
	- blocks 
etc

We can calculate a weighted score of these to show engagement. The weights are assigned depending on what we want to optimize 

![[Pasted image 20220412113427.png]]

Above we can see that likes are prioritized and we have included a negative weight for reports to show that its bad *Duh* lol 

The normalized score is the average engagement per user. The situation in the picture above is actually the following

we have a universe of 2k tweets and 1k users, we aggergate the number of likes, comments etc and then calculate a total engagement score the 49.5 and then divide it by 1000 to get the average per user.

Higher scores = Higher engagement but be careful when comparing two different populations you might just have more active users in one than another so it might not be a good way of comparing how engaging the tweet is. 



# Architecture 
![[Pasted image 20220412114938.png]]

## Components
**Tweet Getter:** 
- Fetches a list of tweets from who the user follows since their last login
- Tweets fed to ranker 

**Ranker:**
We can either 
- train 1 model to predict overall engagement
- Train multiple models to predict the probability of a user commenting/liking/how much time they might spend viewing the tweet ETC ETC

Benefits of training multiple models
	will allow us to have greater control over how we want to control the engagement score and what we want to prioritize

multiple model diagram:


**Training data Generator:**
- User engagement actions will help us collect positive and negative samples for further training

![[Pasted image 20220412115758.png]]

# Deep Dive
## Tweet Selection
Various post selection schemes
### New Tweets
Fetch tweets since the users most recent login - duh? 
Caveat - Could fetch a previously engaged with tweet due to new engagement activity
### New + unseen Tweets
mix new tweets and old _unseen_ tweets
picture this scenario:
- User A logs in at 10am and views maybe 5 tweets and logs off
- User A logs back in at 10:30 am because well theyre addicted to twitter (well done us!) but there arent that many new tweets so what do we do just show him an empty timeline? 
- NO! we have our cached results from the previous login at 10am and using those unseen tweets + the new tweets betwen 10-10:30 we can generate a new timeline

### User returns after a while
Okay what if our user hasn't logged into their timeline for a long time (like a few weeks)

Well .. 
Get all the tweets from the last few weeks but then limit it to say 500 tweets then perform the ranking on those tweets

### Network tweets + interest/popularity based tweets
Okay what if user A is friends w User B and user B is friends with Arun. We see that User A and Arun are closely related in interests but they dont follow each other we are wasting potential tweets to select from because User A might find what Arun follows interesting


So we can get tweets from outside User A's network but tweets that have a high probability of engaging with them

Engaging being
	- Aligns w user A's interests
	- Locally/Globally Trending
	- Engages User As network

- This can be good for new accounts w few followers
- Increases discoverability of the platform


## Ranking
The modelling problem here is 
Predict the probability that a user likes, comments and retweets 
each of which is essentially a classification problem

### Logisitc Regression w regularization
Train on the **dense** features we described 

**Pros:**
- easy to model
- quick to train
- enables you to iterate quickly to validate changes against AUC/classification error
- interpretable

**Cons:**
- Assuming linear relationship
- features have to be created manually unlike DL models or Trees which learn feature interactions

**Single model or multiple models?**
Features will be the same but the labels will be different
### Trees
**Pros:**
- Can generalize well with relatively few training samples - few million
- Can utilize nonlinear relations

**Cons:**
- More hyperparameters to choose from so this part increases in complexity

**Single model or multiple models?**
	- Again we have to choose between a single model and multiple models 
	... Why not both? 
	Use the one model output which predict probability of engagement as a feature to predict the single metrics of engagement; likes, comments, retweets as there is a sort of inherent transitivity between these. If someone retweets a tweet but dont press the like button they still probably like it
	- You can take this further and use the n trees from say a random forest predicting overall engagement as an entire feature vector for your random forests predicting single engagement metrics
### DL
**Pros:**
- Can learn complex patterns
- great to use with an abundance of data

**Cons:**
- Requires a lot of resources
- lots of time
- inference time takes a long time

What we can do is take a funnel approach and use simple models to find a subset of all the potential tweets to rank and then run our complex DL model to find the most relevant / rank them 

**Single model or multiple models?**
- The problem is training 3 independant complex NNs will get costly and time intensive

- We can create a model to learn MULTI TASK problems
	- a neural network with SHARED and SPECIFIC layers for multi task problems
	- The loss would be 
		- total loss = like_loss + retweet_loss + comment_loss

This process allows us to share knowledge and train 1 network instead of 3 which is great!

![[Pasted image 20220412143028.png]]


### Stacking Models and Online Learning
What we can do is leverage the featurizing powers of Trees and Neural Networks and use those as inputs in a logisitc regression model!

![[Pasted image 20220412143457.png]]
- Here we take whether the leaf nodes of trees of the random forest fired or not which will give us a binary vector
	![[Pasted image 20220412143707.png]]
- The final layer of the neural network
- The dense and sparse features of our data 

Throw these all in a logsitic regression problem

So in this way we get the training power of Trees and NNs with the modelling flexibility of logisitc regression

What do I mean by modelling flexibility? 
- the logisitic regression model can be changed easily whilst the model is online for real-time user interactions that happens with tweets! This will help the model re learn the weight of all tree leaves as well
- Logisitc regression can also use sparse features!

**NOTE:** This kind of training will NEED to be done in a distributed enviornment because of the size of the features and that our sparse features will be EXTREMELY sparse.









## Diversity
Okay so have our 100k tweets, our logisitc regression has found a subset to rank and our neural network has ranked them! 

Everything is great!

Except that the first few tweets are all by the same person and thats to say anything bad about your model, it did exactly what it was supposed to do find the most relevant tweets based of the features you gave it and you might be really good friends with this person and love their tweets but everyone gets bored of repetition so we need to introduce some diversifying elements in our ranking to allow the timeline to be more varied 

You can either 
- introduce a repetition penalty 
- or have a scheme that move tweets that are the same format like contains a video down n spaces.

# Feature Engineering
Writing this after the tweet selectionc deepdive but before ranking
So we have our tweets ... what features could we generate from them?

4 Main factors that we will derive features from 
1) The Logged in User
2) The Tweet
3) Tweets Author
4) Context

## User-Author Features
Features that capture the user - author relationship
### User-Author historical interactions
- num posts the user liked in the last 3 months
- num posts the user liked in the last year
Normalize these with the users whole interactions 
### User-Author Similarity
- common followees (users and hashtags)
- topic similarity : td-idf based similarity between the hashtags
- tweet content embedding similarity
- social embedding similarity
## Author Features
### Degree of influence
- verified
- author social rank (similar to google page rank)
- num followers of author (or a normalized version of this)
- follower to following ratio
### Historical Trend of interactions on the authors tweets
- author engagement rate past 3 months
- topic engagement rate past 3 months
## User-Tweet Features
- topic similarity
- embedding similarity
## Tweet Features
### Features based on Tweets Content
- tweet length
- recency
- is image or video
- is url
### Features based on Tweets interactions
- number of total interactions (potentially with time decay)
- interactions within different windows (1 hour, 1 day, 1 week etc)
### Separate features for different engagements
- likes in last n days
- comements in last n days
- reshares/retweets in last n hours

Can even segment this to just the users network
## Context-based Features
- day of week
- time of day
- current user location
- season
- latest k tag interactions
- approaching holiday
## Sparse Features
can be useful sometimes - https://stats.stackexchange.com/questions/430184/what-are-the-benefits-of-sparse-representations-and-sparse-parameters

- uni/bi grams of tweets
- user id
- tweet id
 
# Training Data Generation
How can we generate training data for the model?
keep in mind we could have gone with the multi model approach to predict different parts of the engagement which we ultimately combine into a score


## User Engagement
- User Scrolls through timeline and you can record all engagements they have with tweets
	- Note: if they see the tweet but dont engage its still an impression

How can we balance positive and negative samples?
	- a vast majority of samples will not be seen by the user but are they really negative? 
	- 5% engagement rate => 5 million positive samples and 95 Million negative thats kinda fucked
	- if you randomly select 10Million samples that still wont work
	- You must choose all 5 million positive samples and randomly sample 5 million negative samples

This generates 10 million samples a day for you to train from

**Caveat: Model Calibration** 
- Our model Calibration will suck because of the changed sampling of the training data. A tweet that only got 5% engagement, the model might predict 50%.
- Wont matter rn because of the actual modelling problem, ranking not actual scoring so the scores we generate are relative to other samples but other problems this will become an issue! 


## Train Test Split
- Again there will be seasonality so we collect training data for over a week or few weeks
- Train test split cannot be random but should follow the time series cv methodology otherwise test set leakage with time related features



# Online Experimentation

## STEP 1 - Training Different Models
- We have so many different modelling methodlogies, features and hyperparameters for our models that we can train a bunch of different models


## STEP 2 - Validating Models offline
- Calculate the AUCs/ Whatever metric you are using validate the models on our validation and test set

## STEP 3 - Online Experimenation
- Take the model you're going to use and retrain with the whole dataset - train, val, test just so you have an updated model before live testing
- Now we need to see if this model actually performs better than the model currently in prodcution which is our old friend the reverse chronological order 
- We do this with [[AB testing]] 
	- Pick a sample of your population - you can choose sample size with a [[statistical power test]]
	- Run the control model (the original) on one half and the variation on another half\

## STEP 4 - To Deploy or not to Deploy
- Capture the results of your business metrics ie user engagement
- Calculate the gain
- Perform a statistical test with say a [[p-value]] to ensure the gain is real and not random

Then determine whether you should deploy your model or not and the gain isnt the only factor that plays an effect... 

Is the model complexity even worth it? 
How hard is it to maintain this model? 
Is the gain worth it for the complexity? 