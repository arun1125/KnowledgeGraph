# Design Entity Linking System

# Problem Statment
_Given a text and knowledge base, find all the entity mentions in the text (recognize) and then link them to the corresponding correct entry in the knowledge base(disambiguate)_
### Potential Interview Questions
These are some of the questions that an interviewer can put forth.
1.  How would you build an entity recognizer system?
2.  How would you build a disambiguation system?    
3.  Given a piece of text, how would you extract all persons, countries, and businesses mentioned in it?
4.  How would you measure the performance of a disambiguator/entity recognizer/entity linker?
5.  Given multiple disambiguators/recognizers/linkers, how would you figure out which is the best one?

## NER
- Multiclass classification problem
- Want to correctly detect and classify entities mentioned in the text
- Could include: Celebrities, Places, famous shit, organizations, locations, medical code etc
## Disambiguation
After finding the entities we need to correctly figure out which entitiy is what (might not make sense)

Say you have "Michael Jordan is a great ML prof at UC Berkley" then hopefully our NER can correctly find out Michael Jordan and UC Berkley. After you find these then you have to figure out what michael jordan we are even talking about? The NBA GOAT or the prof at UC Berkley 

## Use Cases
- Semantic Search
- Virtual Assistants
- Content Analysis
# [[Evaluation Metrics]]
## Offline
### NER
With NER we have multiple different cases as to what could happen when we make a prediction
What does it mean to be 'correct' 
	Only if the NER has an EXACT match of the entitiy in the labelled data
	So if we match Michael but not Jordan thats wrong
	If we match Michael Jordan but classify him as a bird thats wrong too
![[Pasted image 20220416115135.png]]

[[Precision]] - in this context $\frac{num.OfCorrectlyRecognizedNamedEntities}{num.OfTotalRecognizedNamedEntities}$

[[Recall]] - in this context $\frac{num.OfCorrectlyRecognizedNamedEntities}{num.OfNamedEntitiesInCorpus}$

[[F1-Score]] - The harmonic mean still 

### Disambiguation
This component receives the entity mentions in the text and links them to entities in the knowledge base.
3 Cases here
	Correct Entity
	Wrong Entity
	Doesnt match

[[Precision]] - in this context $\frac{Num.OfMentionsCorrectlyLinked}{NumOfTotalMentions}$

Why no recall? the total true positives here will either be an object or a null so the concept of recall doesnt contribute anything really

### Whole system
What are the TP, TN, FP, FN 

![[Pasted image 20220416115929.png]]
#### Micro and Macro Metrics
![[Pasted image 20220416120801.png]]


#### [[Micro Averaged Metrics]]

![[Pasted image 20220416120859.png]]

#### [[Macro Average Metrics]]

![[Pasted image 20220416120926.png]]


## Online Metrics - Depends on Usecase 
### Search
- [[Successful Session Rate]]

### Virtual Assistant
- User Satisfaction - hard to measure can use implicit or explicit feedback but in the overall aim is to improve the current model


# Architecture
Includes both training and inference flows 
Inference at the bottom
![[Pasted image 20220416122846.png]]
## Components
### Model Training
- **Training Data Generation** - detailed below
- **NER** - builds the NER model 
- **NED** - Split into two components
	- Candidate Generation - Finds potential matches for the entitiy mentions by reducing the size of the KB 
	- Linking - From the reduced set of mentions use a more complex model to link candidates to entities from the KB
- **Metrics** - NER, NED and Whole system all separate metrics
### Model Inference
Sentence goes in to the NER model, we get our entities which goes into the disambiguation model which will result in text with linked entities 




# Training Data Generation
You can either have manual labelers label text data with tags or use open source datasets

### NER
opensource datasets include the [CoNLL-2003](https://www.clips.uantwerpen.be/conll2003/ner/)
uses the IOB Tagging scheme
`I` - An inner token of a multi-token entity  
`O` - A non-entity token  
`B` - The first token of a multi-token entity; The B-tag is used only when a tag is followed by a tag of the same type without “O” tokens between them. For example, if for some reason the text has two consecutive locations (type LOC) that are not separated by a non-entity

### NED
You can use [AIDA CoNLL-YAGO Dataset](https://www.mpi-inf.mpg.de/departments/databases-and-information-systems/research/yago-naga/aida/downloads/)


Combine open source datasets with extra data with human labellers for your specific task 
- Expensive
- Time consuming
- Could open source it to the public?

# Modelling
**Why Context Matters?** 
Say you have two sentences
1) Michael Jordan is a prof of ML and UC Berkley
2) One of the Greatest of all time NBA players, Michael Jordan 

We can extract Michael Jordan from both of these sentences but the context gives michael jordan such different meanings and the context for the first sentence comes after the entitiy but the context for the second sentence comes before so we need to find a way of creating an embedding to represent our word using the context from both directions

We can get context from these two models
### [[ELMo]] 
### [[BERT]]
## NER MODELLING
Using BERT or ELMo half the work is done because they can generate contextual word embeddings

We can use [[Transfer Learning]] to now utilize the embeddings in a NER classifier

![[Pasted image 20220416134840.png]]
We can obviously take a variation of bert and customize the top K layers to fine tune the model to our specific problem if need be

## Disambiguation Modelling
Okay so we have the entities we want to link what can we do? 
We need to have an efficient index that stores our entire knowledge base
Split up our modelling into 2 phases
- **Candidate generation**
- **Linking**

### Candidate Generation
This process requires an index to retrieve candidate entities
How do we build this index?
- You need to figure out what terms should be used to index each entitiy in the KB
Before we try to build an index remember to construct the index with HIGH RECALL in mind

The index should include all terms that could possibly refer to an entity, even if it is something as trivial as a nickname or a less frequently used term for an entity
why?
You want to return as many possible VIABLE candidates for the linking part

- Link all possible names / relations to your target
	- “Michael Irwin Jordan”, “Michael Jordan”, “Michael”, “Irwin”, “Jordan”, “Jordan Michael”, and so on
- Use the Anchor text in the KB data as well as referrals 

You can use an embedding method where terms like “Michael Irwin Jordan”, “Michael Jordan”, “Michael”, “Irwin”, “Jordan”, “Jordan Michael”, and so on are represented close to each other in the latent space

Use [[KNN]] as the index?

### Linking
Okay so we have candidates now

In the linking stage, you will build a model that will give us the probability of a candidate being the true match for a recognized entity

potential inputs to the model
-   the recognized entity mention that you want to link
-   the type of the entity mention, e.g., location, person etc.
-   the whole input sentence in which the entity mention is recognised
-   the candidate entity from the knowledge base
-   the prior: for a certain entity mention, how many times the candidate entity under consideration is actually being referred to. For example, the anchor text “Harrison Ford”, referred to the entity [American actor](https://en.wikipedia.org/wiki/Harrison_Ford) 98% of the time instead of the silent film actor “Harrison Edward Ford”. It’s important to tell the model that priors favor a certain entity more.

Feed it into a DNN classifier modelling P(Candidate|Features (term, type, sentence etc))
Focus on PRECISION

![[Pasted image 20220416140406.png]]
**CAVEAT**
(kinda)

Important to remember how you are representing your inputs.
The best represnetations are ones given by contextualized embeddings which are generated through BERT/ELMo

You can provide your DNN with embeddings of the sentence, the entire input sequence etc etc You can even use the embeddings from your KB 

All of these together with the prior and entity type should be enough to build a decent model

