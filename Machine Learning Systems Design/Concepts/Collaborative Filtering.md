# Collaborative Filtering
[[Design a Recommendation System]]
- Finding similar users based on movie preferences to generate candidate media 
- why use this?: If users share an opinion on a subset of movies then they probably have a similar opinion compared to a randomly selected user

#### [[KNN]] - The USER based approach
- Look at who likes the same kinds of movies 
- Find the K closest
- Based on their likes recommend movies to User

**HOW?**
![[Pasted image 20220413110132.png]]





To generate recommendations for user i we need to predict their feedback for all movies they havent seen. _collaborate_ with similuar users and use THEIR ratings as a proxy for how well user i would rate the unseen movie

so user i's feedback on movie j can be described as such 

![[Pasted image 20220413113647.png]]

- cosine similarity
- KNN(ui) is top k closest users to user i

**Cons**
- Doesnt scale well
- User preference always changing so will need to recompute frequently
- sparsity of matrix also causes problems 


## [[Matrix Factorization]] - the ITEM based approach
**Why move from KNN to Matrix Factorization:**
- can be computed offline and served without frequent retraining


Take our User-Item Matrix (nxm) and create two new matrices through factorization (explained a little later) - we then will have two matrices of latent variables

1) User profile matrix of size n x D
	- Each user in the user profile matrix is represented by a row  of latent dimension D
2) Item profile matrix of size D x m
	- Each column will represent an item of latent dimension D


We don't really know what these D variables mean


![[Pasted image 20220413114457.png]]



To find the preference of user i for item j we take the DOT PRODUCT of the ith row in the User profile matrix (which represents user i) and the Jth column in the Item matrix which represents item J


## Factorization part
As promised above this is the explanation of how the factors are created

fij is the user item preference for user i and item j

we initalise our user profile and item profile matrix 
take ui from user profile and tj from item profile and compute the dot product

then eij is the error between fij and dot(ui, tj)

$e_{ij} =  f_{ij} - dot(u_i, t_j)$

we use stochastic gradient decent to minimize eij

after n number of iterations our ui and tj vectors will be close enough to represent the fij user-item interaction
" As you continue to optimize the user and movie latent vectors, you will get a semantic representation of the users and movies, allowing us to find new recommendations that are closer in that space. "




