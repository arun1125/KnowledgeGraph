[[Word Embeddings]]

Will be using a list of tweets as my Corpus

if we want to calculate TFIDF vectors for this corpus we have to do a few steps

1) Process Text
	- Tokenize
	- Decapitalize
	- Remove punctuation and numbers
	- Stemming so loving and loves become lov
	- Abbreivaiations so don't becomes do not
	- First get our bag of words which means get all the 

2) Calculate TF = Term Frequency

$TF(d_i^j) = \frac{NumofTimes(d_i^j) ind_i}{NumofWordsIn(d_i)}$
okay so the latex is a bit fucked but what its saying is number of times the jth word $d_i^j$ appeared in the tweet $d_i$. 

Do this for all words


3) Calcualte IDF = Inverse Document Frequency

$IDF(word) = log(\frac{num Tweets}{numTweetsThatContainWord})$
for each word in your bag of words

then multiple tf and idf

https://www.askpython.com/python/examples/tf-idf-model-from-scratch

# NEED TO IMPLEMENT MY SELF THIS IMPLEMENTATION IS GIVING ME STRESSSS LOL

https://github.com/mayank408/TFIDF/blob/master/TFIDF.ipynb
https://jaketae.github.io/study/tf-idf/

### Code
```
#assume you preprocessed your text

#get word counts
word_set = set(word_set)

def count_dict(tweets):
	word_count = {}
	for word in word_set:
		word_count[word] = 0
		for tweet in tweets:
			if word in tweet:
				word_count[word] += 1
	return word_count

#could probs do this with collections.counter() if you preprocess all your tweets and store them in one big variable

word_count = collections.count(allwords) or count_dict(tweets) (if you have a list of tweets)


#calculate TF

def termFreq(tweet, word):
	N = len(tweet)
	occurance = len([token for token in document if token == word])
	return occurance/N


def IDF(word):
	try:
		word_occurance = word_count[word] + 1?
	except:
		word_occurance = 1
	return np.log(total_)



```



## Why does TF-IDF work?

TFIDF carries a "bit of information" attached to the term x document pair

so what its doing is quantifying the relevance of a term per document