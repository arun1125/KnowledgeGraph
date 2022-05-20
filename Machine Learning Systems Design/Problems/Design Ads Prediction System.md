# Design Ads Prediction System
# Problem Statment
We have two general contexts for ads
- Social Media platform ads where the ad is shown in the newfeed based on user interest
- Search engine like amazon or google where the query + user info can be used to show ads

## Scope
- We will need to narrow the scope of our bigger question
**How would you build an ML system to predict hte probability of engagement for Ads?**
**How would you build an Ads relevance system for a search engine?**
**How would you build an ads relevance system for a social network?**

### Search engine vs Social media
Social media - the context would come from the user history, interests, heirarchy of interets, location, demographic etc 
Search engine - the context would come from the user info but also the search query

The problem statement becomes
- _Predict the probability of engagement of an ad for a given user and context_
# Metrics
Goal is to increase revenue, provide value for our advertisers but also improve our overall platform
## Offline
Using offline metrics for inital model develpoment and quickl model comparison when developing future models

Hard to estimate impact on revenue so we go with the classic pred

### AUC
AUC is usually the go to metric for classification problems and it does a good job usually but it has 2 major pitfalls
1) It doesnt penalize "more" wrong answers than less wrong or "more" right answers vs less right. Hypothetically if you have a prediction of 0.51 and 0.7 @ threshold 0.5 then it would measure them equally
2) Insensitive to well-calibrated probabilities 

[[Probability Calibration]]

Since we need our probabilities to be well calibrated we will be going with 

## Log-Loss (cross entropy loss)
- Calibration sensitive
- Captures to what degree the expected probabilities diverge from class labels so it is a measure of quality, which accounts for generting well-calibrated probabilistic output

**why log loss works here and not AUC**
- if we weere to scale our predicted probabilities by a factor of 2 our AUC won't change but log loss will go down


**In the binary classification task**
0 - no engagement with ad
1 - engagement with ad

![[Pasted image 20220417084945.png]]
## Online 
### Overall Revenue
- important 
- but short term
- sum of the winning bid value when the predicted event happens 
	- If the big is 50p and the user clicks on the ad then the advertiser is charged 50p
### Overall Ads Engagment Rate
- [[Click Through Rate]]
- Downstream Action rate
	- measure the action rate of a particular action targeted by the advertiser 
		- add to cart rate
		- purchase rate
		- message rate

### Counter Metrics
We want to show ads but not take away from the overall experience of the service so we need to measure the **negative** impact of our ads too 

can do this with direct feedback such as 
- hide ad
- never see this ad
- report ad as inappropriate





# Architecture 
2 key flows here
1) **Advertiser Flow:** Advertisers create ads containing their context as well as their targeting, some targeting examples are:
	- Query Based targeting: Method shows ads based on user based query terms. Partial, full or expansive mathc
	- User-Based targeting: Ads subjective to a specific region, demographic, gender, age
	- Interest-based targeting: eg show sports ads to people who like sports in their interest heirarchy
	- Set-based targeting: Show ads to specific groups like previous buyers or people who have spent more than x minutes on a website
2) **User Flow**
	- Advertisers create ads and store in ads index
	- When user queries 
		- ads are selected from the index
		- then run through our ads prediction system
![[Pasted image 20220417091344.png]]


## Components
- **Ad Selection:** Fetches top K ads based on relevance and bid from the ads index
- **Ad prediction:** predicts the user engagement with ad given context, user info, ad, advertiser etc then ranks based on relevance score and bid
- **Auction:** determines which of the top K relevant ads are show to the user, what order they are shown in and the price if the action is taken on the ad
	- takes place EVERY ad request
	- Selects the ads with the top Ad Rank Score = Ad Predicted Score * bid 
		- Has several factors
			- Big
			- User Engagement rate
			- Ad Quality score
			- Budget
	- After you have sorted ads we calculate the CPE/CPC which is the cost per engagement/click. 
	- CPE = $\frac{AdRankOfAdBelow}{AdRankScore}+0.01$
	- Ad will cost the minimal price that still allows it to win the auction
- **Pacing:** Advertisers have daily budgets and as such can run through their budgets all in one go in the morning if there is high engagement rate so what we can do is dynamically spread the ad out throughout the day to get max ROI 
- **Training data generation:** Record every action taken to implicitly generate positive and negative samples for the ad prediction component

## Funnel Approach
![[Pasted image 20220417093408.png]]

**Example Flow:**
-   A thirty-year old male user issues a query “machine learning”.
-   The **ads selection** component selects all the ads that match the targeting criteria ( user demographics and query) and uses a simple model to predict the ad’s relevance score.
-   The **ads selection** component ranks the ads according to rr, where r = bid * relevancer=bid∗relevance and sends the top ads to our ads prediction system.
-   The **ads prediction** component will go over the selected ads and uses a highly optimized ML model to predict a precise calibrated score.
-   The **ads auction** component then runs the auction algorithm based on the bid and predicted ads score to select the top most relevant ads that are shown to the user.


# Feature Engineering
**4 Main Feature sources:**
- Ad
- Advertiser
- User
- Context

Features fall into these main categories
1.  **Ad specific features:**
	- ad_id - sparse
	- ad_content_raw_terms - sparse
	- historical_engagement_rate: can tell the model that a particular ad is performing well
		- ad_engagement_history_last_24_hrs
		- ad_engagement_history_last_days
	- ad_impression after certain amount of engagement
	- ad_negative_engagement_rate
	- ad_embedding
	- ad_age
	- ad_bid
2.  **Advertiser specific features:**
	- advertiser_domain
	- historical_engagement_rate
	- region_wise_engagement
3.  **User specific features:**
	- User_previous_search_terms
	- user_search_terms
	- age
	- gender
	- language
	- embedding_last_k_ads
		![[Pasted image 20220417094608.png]]
	- engagement_content_type
	- engagement_days
	- platform_time_spent
	- region
4.  **Context specific features:**
	- current_region
	- time
	- device
		- screen-size
5.  **User-ad cross features:**
	- embedding_similarity: between user and advert
	- region_wise_engagement
	- user_ad_category_histogram
	- user_ad_subcategory_histogram
	- user_gender_ad_histogram
	- user_age_ad_histogram
6.  **User-advertiser cross features:**
	- embedding_similarity
	- user_gender_advertiser_histogram
	- user_age_advertiser_histogram

**NOTE**
Historical engagement features can be encoded as features in our models in two ways

1) Give a histogram of data along with the key/value to the model: 
	- Example: if you take engagement over the last 7 days we will pass the model 8 features, a list of the engagement rate over the last 7 days and then what day of the week it is
2) Create a single feature for our model that gives the current day engagemnt rate as a value

For NNs or Trees use 1 and for Linear models use 2.

NNs and Trees will be able to find extra feature interactions between data points whilst the linear model will need to provide the actual value for each day because they cant learn feature interactions


# Training Data Generation
## Generating data through online user engagement
- implicit feedback: User can engage with it or no action/negative action like reporitng ad
![[Pasted image 20220417095721.png]]

Also depends on the advetiser and what they determine as a positive sample they might want clicks to be counted as positive and dont care if the user ads to shopping cart or not

## Balancing Positive and Negative Samples
Engagement rates are usually low and so our implicit feedback will generate a crap tonne of negative examples 

so say we collect 100 Million data points, only 2-3 % of that will be positive and so we need to downsample the negative examples

## Model ReCalibration
Negative downsampling can accelarte trianing while enabling us to learn from both positive and negative examples. But our model output will now be in the downsampling space.

I.E say our engagement rate is 5% and we downsample our negative samples to only 10 % then our predicted engagement rate will be near 50%. 

The Auction componen uses this predicted rate to determine order and pricing so its critical to recalibrate our score before sending them to auction. 

Re Calibration can be done using 
![[Pasted image 20220417100213.png]]
## Train Test Split

Need to use Time series cross validation method because time features can leak data from test to train set.

# Modelling
## Ad Selection
Search engine: Narrow set based on user and query
Feed system: more on user interest vs query

![[Pasted image 20220417101243.png]]

### Phase 1: Ad Selection
_Reduce set from 100s of Millions of ads to a few ads that are targeted for the current user _

The key requirement of this part is QUICK SELECTION

We need an index structure that allows for optimal ad selection. So we need to index ads with all possible indexing terms that could potentially be used for selection

**Two Scenario Examples Search vs Feed**

1) Say a 25 yo searches Machine Learning and lives in San Fran
	![[Pasted image 20220417102039.png]]


2) feed based system would look like 
	![[Pasted image 20220417102108.png]]


### Phase 2: Narrow down selection set to top relevant ads

We could still have millions of ads left so running a complex model on 1 million ads would still be hard so we need to another step that reduces our 1 million ads to the top relevant ads!

The eventual ranking of ads will be bid * predicted score

**For prior advertisers:**
- bid * prior CPE score

**For new advertisers:**
- User a higher score (*score boost*) with a time decay to reduce it
- The ranking might look like the following with a new ad boost of 10% to CPE score for the first forty-eight hours of ad with time decay.

![[Pasted image 20220417102645.png]]

We can use these metrics to go from 1 mill to 10kish


### Phase 3: Ranking of selected ads using a simplistic model
Run a Logisitic Regression or MART to choose the top K relevant ads.

Use training data. + dense features to predict engagement scores better and minimize log loss

Reduces our ad set from 10k to 1k


### How does our system scale?
- Index is sharded and runs on multiple machines, each machine chooses the top K score based on prior score
- Run a simple logisitc regression with dense features to rank ads

![[Pasted image 20220417103211.png]]
Sharded index with four partitions. Each partition select the top five-hundred ads based on a prior score and then run logistic regression to select top fifty ads . The top fifty ads from each partition are fed to ad prediction component

## Ad Prediction
We have taken our 10 million ads reduced them to 1 million then further reduced that to 10k then further reduced that to a few 100.

Needs to be robust, adaptive and should be able to learn from massive data volume


**CAVEAT**
Our context is VERY dynamic as ads as shortlived so the model will degrade very quickly so we need to constantly update it!


**How do we keep our model updated?**
One approach to minimize this loss could be refreshing the model more frequently e.g. training it every day on new data accumulated since the previous day (like CI)

However, a more practical and efficient approach would be to keep refreshing the model with the latest impressions and engagements after regular internals (e.g. 15 mins, 30 mins, etc.). This is generally referred to as **online learning** or **active learning**.

Below is a system architecture that could work

![[Pasted image 20220417103750.png]]

The above system has a few key points
- Train a base model then keep adding examples on top of it with every ad impression, we can update the model every 30-60 minutes
- We need to collect data in real time, feed it into the training data generator which will generate our features
- Model trainer will recieve the features and refresh the model using [[SGD]]
- This forms a tightly closed loop where new changes in the feature distribution can be learned periodically 


[[Logisitc Regression]] is a greate model for online learning and can easily support it


### Auto Non-Linear Feature generation
The problem with logisitic regression is that it can learn complex feature interactions so instead we can use auto feature learning methods like Trees or NNs and take the features it learns and use it as an input to logisitc regression

**Additive trees**
- Say we have 100 trees with 4 leaf nodes each, we can create a binary vector that represents if a leaf node was activated or not and feed this feature into our logisitic regression

**Neural Networks**
![[Pasted image 20220417104451.png]]


![[Pasted image 20220417104557.png]]