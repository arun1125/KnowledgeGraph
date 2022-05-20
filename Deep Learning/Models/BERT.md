[[Design Entity Linking System]]
# Bidirectional encoder representations from transformers

- Take input sequence of multiple sentences separated by a "SEP" tag
- Convert each word into an embedding then fed into the [[Transformer]] encoder layer
- The transformer encoder layer can process all the words simaltaneously so embeddings are inherently bi-directional
- The output is passed to another transformer layer and this is repeated for all transformer layers
- The final layer outputs the contextualized represnetation of each word


![[Pasted image 20220416133850.png]]

The transformer encoder block uses the concept of [[Self-Attention]] to compute the respresnetation of the word in an input sequence


### Objective
#### Masked language modeling (MLM)
- A few words are masked in the input and the model predits them based on the bi-directional context

![[Pasted image 20220416134434.png]]

[[ELMo]] has solves this problem through sequential prediction from left to right and right to left, independently. This is suboptimal because it results in shallow bi-directionality.



#### Next Sentence Prediction (NSP)
Two sentences A and B are given and the model has to predict if B comes after A in the corpus of is it just a random sentence



## Variations of BERT

![[Pasted image 20220416134620.png]]