[[Design a Recommendation System]]


- Similar to what we did in a [[Past Project at work]] with BFS
- related to [[Matrix Factorization]]

instead of randomly initializiing the vecotrs and computing dot product then fidning the error and doing sdg to find the optimal vectors we can use a neural network to generate the embeddings! 

This is better because we can use a wide array of user and item features sparse and dense in order to come up with the embedding

What we do is have a network which we feed in sparse and dense ITEM ONLY features and come up with an embedding

Another network where we do the same but for Users 

Take the activitation of the last layers as the embedding and then dot product them in order to get a user-item preference 

We optimize these 2 networks with respect to this loss function

![[Pasted image 20220413121618.png]]

The actual feedback of this loss function will be positive when the media aligns with the user and negative when they do not

once we generate user and product embeddings we can apply KNN to select candidates for each user... (how? )

![[Pasted image 20220413121854.png]]



