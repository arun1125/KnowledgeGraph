# Search Ranking
# Problem Statment
- Design a Search Relevance System for a search engine
## Clarifying Questions
Establish **Scope**, **Scale** and **Personalization**
## Define Problem Scope
Are we building a Generic or Product Sepcific search engine
Think Google vs Amazon products

**Building a Generic Search engine for now but similar techniques can be applied to a product specific search engine**

## Define Scale
So we're building a GENERIC search engine. We can assume we have **BILLIONs of documents to search** through and can assume we are getting **10k Queries Per Second**

## Define Personalization
Is the user logged in or not? because if the user is logged in we can use their profile information + search history in order to produce recommendations

**Lets assume the user is Logged in**

# Metrics
Before we go out at build a ML system we need to understand how to evaluate it
Search and Ranking problems have different [[Evaluation Metrics]] we can use to evalaute the performance of the system.

We have to evaluate our model in two phases Online and Offline

## Online
Metrics we could use 

- **[[Click Through Rate]]**
	- defined as $$ \frac{NumClicks}{NumImpressions} $$
	- What is an impression? 
		- it means a view eg: when a search engine results page loads and the user has seen the result you consider that as an impression
- **[[Successful Session Rate]]**
	- Time To Sucess

## Offline 
[[NDCG]] is the most relevant offline metric to search ranking

# Architecture 
We have take a **Funnel** approach to building our search engine results page (SERP)
Because running a massive deep learning model on 10 BIllion documents will have us waiting years for results lol

Using simple models to first filter search results
Then slightly more sophisticated models to reduce this down and so on

until we get a handful of results will be the best trade of between complexity and latency of our system - we are handling 10k queries per second after all
![[Pasted image 20220411112645.png]]


## Query Rewriter
**Spell Check:** People spell words wrong all the time so we need to correct them
**Query Expander/Reducer:** We can either add or reduce the search terms based on the search being formed so we can expand our inital set of results
## Query Understanding
We can find the **intent** behind the query to add either geographical, time etc 
Good restaurants near me -> local intent
Ukraine -> Newsy intent
Alexander the great -> Historical intent
## Document Selection
We use a simple model to reduce our inital set of Billions of documents to a more manageable say 100k documents
## Ranker
**Initial Ranker:** Using a very large complex deep learning model to evaluate 100k documents and rank them would be very difficult but we could use a less sophisticated model (but more sophisticated than the document selection model) to bring this down to a more manageable 500 to rank
**Sophisticated Ranker:**
Now we can use a fancy pants model to rank the 500 into the top 10 or n results.
## Blender + Filter
The blender combines various search silos like images, videos, blogs, websites etc into one page as this will engage the user more

And we can filter out any harmful content after blending our results

## Training Data Generation
We collect user engagement data from the SERP to generate more positive and negative training examples for our sophisticated models to improve our ranking system



# Feature Engineering
**4 Main parts of generating features**
1) Searcher
2) Query
3) Document
4) Context

**Main Types of Features**
- Searcher Specific
- Query Specific
- Document Specific
- Context Specific
- Searcher - Document 
- Query - Document

![[Pasted image 20220411120641.png]]

**Searcher Specific:** Since our user is logged in we can use their demographics like Age, Gender, Location, History etc

**Query Specific:**
	- Query Historical Engagement: For relatively popular queries historical engagement can be important. So you can use the historical interactions with this query as a feature
	- Query Intent: This will help us identify what scope the user is looking for i.e when searching for restaurants there would be a local intent

**Document Specific:**
	- Page Rank: Rank of document can serve as a feature. Can look at the number and quality of the documents that link to it
	- Document engagement radius: how far away is the document to the user i.e a coffee shop in seattle is more relevant to people in seattle vs toronto but something like the eiffel tower is relevant to everyone cause its famous

**Context Specific:**
	Time of search
	Recent Events
	History of recent searches
**Searcher - Document:** 
	Distance between searcher and matching locations
	The documents historical engagement -> relevant especiall when users are trying to 're-find' documents
**Query - Document:**
	- Text Match: Check the powerset of the search query
		- Using one word is unigram; any two is bigram; any three is trigram etc
	- Historical Engagment: 
		- Click Rate
	- Embeddings
		- Query Embedding + Document Embedding with cosine similarity to get a similarity score for each page 







# Training Data Generation
## Pointwise
- We aim to predict the relevance of a document directly!
- Either the documents rank score or a [[Binary Classification]] problem 1 being relevant and 0 being irrelevant
Assuming we go with the binary classification problem how can we generate training examples?

We look at the results on the SERP
and see how the user has interacted with each result

- If the user clicks on the links and spends more than 10 seconds on the page then we get 1
- otherwise we get a 0

This is under the metric of [[Successful Session Rate]] if we used [[Click Through Rate]] instead then if the user clicks on the link it is a positive training example

**Problems**
- Well what if the user only searches and clicks on a few pages where a majority of the results would have been positive? 
- We need to find a way to generate negative samples 
- We can generate negative samples by taking the low ranked results and using them like on page 50 for google

**Amount of Data Generated**
So if we were to query "paris Tourism" and paris.com, eiffeltower.com and museumlouvre.com were all returned and we had a no click, click and click interaction then 3 rows of data have been generated. 

Extrapolating this idea out say we receive 5 Million Queries a day and on average we generate two rows of data per query (one positive and one negative) then we are generating 10 million examples a day.

Say there is intraweek seasonality so we take a weeks worth of data => 70 Million samples

### Train Test Split
Conventioanl train test split will cause time related features to leak into the test set so we need to use a time-series CV type train test split. Choose the first 2 weeks of search results as train then the next week as validation and the final week as test. 


## Pairwise
The loss function here looks at the pairwise scores of the documents and aims to minimize the number of inversions.

Inversions means => the number of cases where the pair of results are in the wrong order relative to the ground truth

How can we get ground truth? 

1) Human Raters
	- Manual
	- Expensive
2) Online user engagement
	- Hypothetically if we are using Successful session rate and for our paris example paris.com was ignored, effieltower.com the user went in and booked and then with museamlouvre.com the user went in for 20 seconds and left then our ratings would be 

	0, 4, 3 on a scale of 4 to -1 where 4 is perfect and -1 is bad


# Deep Dive
## Document Selection
We need to go from Billions of pages to 100k pages how? 

**Inverted Index**
	We first create an inverted index of terms to pages
	The inverted index is 
		 a data structure that stores a mapping from content to the location in a set of documents
	![[Pasted image 20220411115140.png]]

Once we have this data structure we can construct a relevance score of each page
**Relevance Score**
We can take a linear combination of these features 
1) Term Match - Use the IDF score from [[TF-IDF]]
2) Document popularity - Popularity from the stored index
3) Query intent match - say the query has a local intent then the documents that are deemed 'local' will have a higher relevance score
5) Personalization match - Lets us know how relevant the documents score is to the users search requirements. This can be broken down even further to age, gender, location, history etc

To compute a relevance score.
The weight of each factor can either be tuned through intution of we can learn these weights 

## Ranking
So our Billions of results has been reduced to about 100k
Now we need to rank these but how? 

LTR - Learning to Rank - our pointwise and pairwise (in Training Data Generation) speak more about this but they fall into this LTR which is a supervised way of approaching the ranking problem

**Simple Rank**
- optimizes for [[Recall]]
- use the pointwise approach

Model this as a binary classification problem
use [[Logisitc Regression]] or a small Tree based model ([[Random Forest]] or [[XGBoost]])
-> Quick scoring and low misses are what we want to optimize here
Using [[AUC]] as model evaluation metric

**Sophisticated Rank**
- optimizes for [[Precision]]
- Use the pairwise approach
- Not trying to minimize classification error but instead trying to get as many pairs of documents in the right order as possible


Two potential Algorithms we could use
- [[LambdaMART]]
	- Use if you have a moderate training set (few million examples)
	- Can generalize fairly well with moderate data
	- Essentially a tree based algorithm with a change in objective to improve pairwise ranking
	- Great if we are optimizing for NDCG based of human data
- [[LambdaRank]]
	- [[Neural Network]] approach
	- slower
	- needs more data but if we are using the user engagement method of generating training samples this is fine 
	- Training data looks like this
		- (i,j) where the rank of i is greater than j




## Filtering Results
We went from 1 Billion examples to 100 K examples to 500 examples and then even ranked them

Arent we done? 
WELL! 
We need to filter some results for 

- hate
- inappropriate results
- misinformation
- offensive
- not appropriate for children
- inconsiderate towards to a particular group

Even if they have good engagement they should be removed for user safety!

**What we want to have**
We want to have a specialized model to filter these results

We can collect training data for this model through again

1) Human raters: We can ask people if its fucked up or not
2) Online data collections: Have a report result/page option to crowd source results

Features we could use
- similar features to our ranker
- but also!
	- Website report history
	- sexually explicit terms used
	- domain name
	- website description
	- images on website etc etc

We want to build a classifier 
- Logisitc Regression
- MART
- Neural Network


What we build depends
- Amount of data we have
- Capacity requirements
- Experiments to see how much gain we get in reducing bad content vs model complexity 
