# Comparing different Word Embeddings 
https://www.capitalone.com/tech/machine-learning/understanding-tf-idf/
## Contents
#### Word Vectors
- Bag of Words
- TF-IDF
- word2vec 
#### Language Modelling
- BERT


## Bag of Words
mainly extracts just unigrams (one word/terms) to create an unordered list of words. 

**CONS**
- no POS tag - part of speech 
- no syntax
- no semantics
- no position
- no bigrams
- no trigrams
- no context 

So literally its just a python set() of all your terms lol ...

## Vector Space model 
The vector space model aims to represent this python set numerically as vectors in a vector space

- Most Trivial - Binary value which is most associated with BOW
	- 1 if the word is present and 0 if it isn't
- Slightly better Term Frequency - indicates the count of the number of times a term occured in the document

we can extend the Term frequency idea by using a
- [[TF-IDF]] representation 

This representation helps convey the relevance of a term per document

These work well but the embeddings they create are sparse, how can we create dense word embeddings?


## Word 2 Vec
Is actually a shallow [[Neural Network]] that creates word embeddings

It also can capture some relationships with words like
Queen - Woman + Man = King

But the problem comes with Words like play, are we going out to play? or going to see a play? in these context play means totally different things!

**Pros**
- Better than BOW 
- Not Sparse

**Cons**
- no context
- no semantics? 
- no position? 

**The above ideas are all creating vectors for words but we run into the context problem. What NLP researchers have been focusing on recently is lanugage modelling. Creating a model that can understand the core concepts of language and context in which words are used so embeddings can become more flexible** 

The key difference between word-vectors and contextual language models such as transformers is that word vectors model **lexical types**, rather than _tokens_. If you have a list of terms with no context around them, a transformer model like BERT can’t really help you. BERT is designed to understand language **in context**, which isn’t what you have. A word vectors table will be a much better fit for your task. However, if you do have words in context – whole sentences or paragraphs of running text – word vectors will only provide a very rough approximation of what the text is about.

Word vectors are also very computationally efficient, as they map a word to a vector with a single indexing operation. Word vectors are therefore useful as a way to **improve the accuracy** of neural network models, especially models that are small or have received little or no pretraining. In spaCy, word vector tables are only used as **static features**. spaCy does not backpropagate gradients to the pretrained word vectors table. The static vectors table is usually used in combination with a smaller table of learned task-specific embeddings.


## Language Modelling 
What we want to do is find a model that can be trained on a massive set of data so that it understands the basics of language instead of just picking up patterns in data

Once we have the language model we can then do [[Transfer Learning]] to train the last few layers for our specific task


First big breakthrough was 
### [[ELMo]] - Embeddings from language models
Created vectors for words based on CONTEXT! by using 2 bidirectional [[LSTM]] layers.

- First a simple vector for each word is passed through
- Then the first layer trains LSTM forwards then backwards and concatenates the output
- The second layer does the same
- A weighted average of the 3 vectors; simple, first layer and second layer are taken and used as the vector representation

so the same word 
ie PLAY 
can have different meanings in different sentences like 
- who wants to go see a play
- who wants to play with me

**Pros**
- Captures Context
**Cons**
- Slow to train because of the LSTM layers
- Embeddings are considered shallow .. .but I dont know why? 


**NEXT COMES**


### [[Transformer]]
Encoder Decoder architecture but openAI went straight for Decoder
uses [[Self-Attention]] + masked output 

Pros
- Fast

Cons
- Only captures forwards context


### [[BERT]] = ELMo + Transformer
Two objectives
Masked lanugage modelling - randomly mask words and try and predict them
Next sentence prediction - given two sentences A and B return if B follows A


Bert has MANY MANY variations that allow it to be used in different contexts and some easier to engineer models.

 Pros
 - Faster than ELMo
 - Captures bidirectional context


Cons
- If two key words are masked then we get syntactically correct sentences BUT! ... it might not make any sense 


However, in the case of ELMo and BERT, since they are context dependent, we need the model that was used to train the vectors even after training, since the models generate the vectors for a word based on context. - https://www.quora.com/What-is-the-difference-between-bag-of-words-TF-IDF-and-vector-space-model

