**

Question: The background of tweet search is that given a query we retrieve a set of tweet candidates. Then, we rank these candidates using an ML model and present the end results to users. The ML model uses supervised learning, which means we need to first train a model, deploy the model, and then let the model serve production traffic.

  

The business and product requirements for tweet search are to:

-   increase the daily usage of the search functionality
    
-   promote healthy conversations within search results.
    

  

-   Search DAU, Number of Successful Searches
    
-   To define a successful search:
    

-   Interaction with a tweet like/comment/retweet/share 
    
-   Clicking and viewing the tweet
    

  

-   Unsuccessful search:
    

-   Number of consecutive searches 
    
-   impression + no interaction = negative search
    
-   report/hide tweet / any explicit negative interaction provided twitter
    

  
  

Weighting classes:

+ve Interaction with a tweet like/comment/retweet/share 

-ve report/hide tweet / any explicit negative interaction provided twitter

  

We’re going to have more +ve than -ve samples

Don’t want to throw away any of the data via downsampling majority classes because these are strong impressions 

Instead we could alter the class weights 

  
  

promote healthy conversations within search results

  

-   Filter tweets based on language
    

-   No racial slurs
    
-   No bad words
    

  

-   Toxicity classifier 
    

## When we use multiple objectives, how should we coordinate among these objectives for the final ranking?

-   Engagement
    
-   Toxicity
    

  

-   Linear Combination of the different factors that make up engagement + toxicity
    

-   TOxiicity score comes from a model 
    
-   But we can breakdown engagement into 
    

-   Prob(like)
    
-   prob(comment)
    
-   prob(retweet)
    
-   prob(share)
    

-   And take a linear combination of all of these 
    

  
  

Testing

Offline

Calculate your offline metrics find best model 

online

You got your best model run an AB test on it with the existing model to figure out if you should deploy new model or not given feasibility and infra constraints

  
  
  

For a specific objective, what features can be beneficial to this objective and how do we design different features to achieve this objective?

  

Modeling P(Like|features)

What are features?

  

Main Actors:

Searcher

Author

Query

Tweet 

  

Searcher x Author history

Searcher x Tweet relevancy 

Query x Tweet embedding

  

Feature Categories:

Searcher

-   Search demographics: Age, location, etc
    
-   Searcher history
    
-   followers
    

  

Author:

-   Demographics 
    
-   Author tweet post history
    

  

Query:

-   Content
    

  

Tweet:

-   Interactions over a given time period 4h, 24h, 7day etc
    
-   Impressions 
    
-   Content of tweet
    
-   Topic
    

  
  
  
  
  
**