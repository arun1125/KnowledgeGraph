# Recommendation System
# Problem Statment
Design a recommendation system for stuff like
- Amazon products
- Netflix movies
- Pinterest Pins

Context we will be using will be netflix but most recommendation systems follow the same pattern

_Design a movie recommendation system for a netflix user such that their chance of watching the movies are maximized_

## Scope
- Number of subscribers are 150 Million
- 50 Million DAU

## Formulation 
Can model this problem as a classification problem

_Given the user and context predict the probabbility of engagement for each movie and order movies using that score_

### Reason
- We are basing our recommendations on _implicit_ feedback rather than _explicit_
Implicit: if the user watches the movie 1 otherwise 0

Explicit: would be something like ratings

Modeling this problem with explicit feedback would have us predict ratings and then show the user which movies we think they would rate highly but if we model with implicit feedback instead then we can collect WAY MORE data 

Explicit Feedback faces the missing not at random (MNAR) problem
- Users will generally rate those media recommendations that they liked so 4 or 5/5 is more likely to appear in our dataset than 1, 2 or 3 / 5. 


# Metrics
## Online vs Offline
- **Online:** Measured on live data during [[AB testing]]
- **Offline:** Metrics like NDCG or AUC for classification problems etc 
## Process
- Train multiple models and evaluate them on offline metrics with a hold out test set to 'simulate' production enviornment 
- If one of your models performs well enough then you can send it to the online enviornment for an A/B test with live data

**Caveat:** If our model performed well on test data but not live data then we need to see what biases we introduced or other potential situations where we could have gone wrong



## Online
- Engagement Rate: 
	- sessions w clicks / total sessions 
	- just measures clicks what happens if someone clicks then leaves?
- Videos Watched
	- unsuccessful clicks can be taken into account with videos watched or num videos watched  >n minutes but this could be bad cause they might only watch a few minutes and find it boring 
	- In the case of tv shows peopel can watch one ep but not others so thats bad? 
- Session Time
	- measures total time spent watching recommended content in a session

## Offline metrics:
- [[mAP @ N]]: mean average precision ; n is recomendation list
- [[mAR @ N]]: mean average recall; n is num of items in recommendation list

Can be combined to create F1-Score

![[Pasted image 20220413094927.png]]


## Metric for ratings? 
![[Pasted image 20220413095046.png]]


# Architecture 
Split our system into two parts due to large corpus of movies

- **Candidate Generation** - first filter to find relevant movies
- **Ranking of generated candidates** - more complex model to do the heavy lifting after we reduced our set


![[Pasted image 20220413095718.png]]

## System Components
- **Candidate Generation:** Focuses on recall, generate as many viable candidates whilst reducing population - uses historical interactions with media and context
- **Ranker:** Focus on precision, Scores each of the viable candidates and sorts them - uses other dense/sparse features 
- **Training Data Generation:** Collect implicit/explicit data for both ranker and candidate generator


# Deep Dive
## Candidate Generation
### [[Collaborative Filtering]]
### [[Content Based Filtering]]
### Generating Embeddings using NNs
#### [[Two Tower Approach]]

### Summary 
Collaborative filtering: 
- Requires domain knowledge to create user and media profiles
- can capture aspects of the data that are difficult to find based on content based filtering
- Suffers from cold start problem

Neural Network Embedding:
- Also suffers from cold start problem due to lack of training examples to learn embeddings from
- but doesnt require as much domain knowledge to generate 
- embeddings are more flexible

Content-based Filtering:
- Good for cold start problems
- Does require initial input from user regarding preferecnes to generate candidates though
	- But input can be obtained as part of the onboarding process i.e select top 5 genres you like etc
- Once we have inital input it can create and then match the users profiles with media profiles 
- Media profiles can be built immediatley as their description is provided manually 

## Ranking
Take your top candidates from candidate generation and then rank them 
What models can we use to rank them?

Our goal is to model 

$P(watch|(user,media))$

can be done as a classification model

Baseline modelling approach 

### **Logisitic Regression or Random Forest**
use if: 
	- Training data is limited
	- Limited training and model evaluation capacity
	- Model explainability is key
	- Need initial baseline

![[Pasted image 20220413123122.png]]

 
### **Deep NN with sparse and dense features**
use if 
	- 100's of Millions of training examples
	- Capacity to train, evaluate and serve these in a production setting
	- interpretability is not key

![[Pasted image 20220413123710.png]]

Whats going on in the bottom left :

![[Pasted image 20220413123756.png]]
why tho? 

Two powerful sparse features you want to use when training your network is to have the history of movies watched and the users search terms but since they can vary in length we need to create some sort of way so that they can work with our FIXED network size

This is why we do this preprocessing step and learn embeddings of these vectors 

This is a similar approach to the pooling layers in [[CNN]]s and simply just average the watch id and search text term embeddings before giving it to your network

**Network Structure:**
- Start with 2/3 hidden layers with a RELU based activation unit then start to play around with adding more layers. This does improve results until it doesnt (which happens faster than you might think) + computation costs :( 

**Reranking**
Once you have your recommended videos and have ranked them take the top 10 and RERANK them 
why? 
Because the top 10 ARE SUPER IMPORTANT and most people dont really watch anything/give anything other than the top 10 a chance. 

Also if you are recommending previously recommended videos we can use this chance to filter them out


# Feature Engineering
- **Main Factors to generate features**
	- Logged-in User
	- Movie/show
	- Context

- **Features** - from the main factors
	- **User Features**
	- **Context Features**
	- **Media Features**
	- **Media-user cross features**


## User Features
- Age
- Gender
- Language
- Country
- average session time
- last genre watched
- User actor histogram
	- ![[Pasted image 20220413101117.png]]
- user genre histogram
- user language histogram (my mum loves korean stuff how can we encode that)


## Context features
- season of the year
- upcoming holiday
- days to upcoming holiday
- time of day
- day of week
- device

## Media features
- public-platform rating
- revenue: generated by movie before coming onto netflix
- time passed since release date
- time on platform
- media watch history last n hours
- genre
- movie duration
- content set time period
- content tags
- show season number
- country of origin
- release country
- release year
- release type
- maturity rating

## Media - User Features
- user genre historical interactions last n months
- user and movie embedding similarity
- user actor
- user director
- user language match
- user age match
- sparse features
	- movie id
	- title of media
	- synopsis
	- original title
	- distributor
	- creator
	- original language
	- director
	- first release year
	- music composer
	- actors

**Can make sparse features into dense features**
some examples
- Dense represtion of distributor feature: Does the distributor of this movie fall in the list of top ten distributors
- Dense representation of synopsis feature: Perform text summarization of synopsis to pull out keywords

But this requires extra effort and is it worth it?

# Training Data Generation
## Generating Data
Generating data through implicit feedback has a lot of nuances
what if someone just watches 55% of a movie
	did they like it or not?

When genreating postivie and negative samples we need to have a high degree of confidence that the samples we generate are either positive or negative sooo

- for positive samples only take samples with a watch time of > 80%
- negative samplles take a watch time of < 10%
- Ignore the samples in the range of 10-80 % high chance they will introduce noise to us


## Balancing positive or negative samples
After doing this we will still get a LARGE number of negative samples because there are many many recommendations but users can only watch a finite number of shows 

so we will need to randomly downsample the negative samples to balance out our data

## Weighted training examples
if someone watches 80% of an episode of friends vs 80% of a movie thats 2+hours we should weight the movie more in our training dataset because of our final metric which is session watch time 


## Train test split

Use time series CV approach because of seasonality components and we are forecasting recommendations into the future



