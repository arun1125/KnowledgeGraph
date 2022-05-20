[[Design Entity Linking System]]
https://arxiv.org/pdf/1802.05365.pdfs
# Embeddings from Language models

Used to create contextual embeddings

Starts with words as vectors can use
- character-level CNN 
	- enables us to capture similarity in the structure of the word like learn and learning
	- makes good raw vectors for out of vocabulary words by looking at their similarity with existing vocab in our training phase
- context-independant word embedding like word2vec


Then Pass that to a Bi Directional [[LSTM]] layer trained on a laugage modelling objective. 
This layer has a forward and backward pass to get context from both sides

![[Pasted image 20220416133134.png]]
**More Layers**
will give us deeper contextual semantics whilst the shallow layers above will help us identify grammar and syntactic rules

**ELMo Weighted Sum Layer**

![[Pasted image 20220416133341.png]]

The weights here are trainable

**Fixed mean pooling layer** - takes word embeddings and makes sentence embeddings


**Bi Directional LSTM**
Zooming in on the bidirection LSTM layer we have
We could also use the outputs from the LSTM layers as embeddings

![[Pasted image 20220416132943.png]]


This is considered a **SHALLOW** model because of _how it achieves bidirectionality_ the forward and backward passes are trained independantly



Theres something better tho [[BERT]]
