[[Design a Recommendation System]]

Allows us to make recommendations based on the characteristics or Attributes of the media they have already interacted with

The idea data we collect come from 
The characteristics come from metadata (e.g., genre, movie cast, synopsis, director, etc.) information and manually assigned media-descriptive-tags (e.g., visually striking, nostalgic, magical creatures, character development, winter season, quirky indie rom-com set in Oregon, etc.) by Netflix taggers.

We represent this as a vector of its attributes

## Example

![[Pasted image 20220413120131.png]]

## [[TF-IDF]] on this shit

![[Pasted image 20220413120241.png]]


TF-IDF puts the similarity between documents into perspective by considering the rarity of a feature throughout the corpus.


Now that we have the TF-IDF representation of each item we can recommend in these two ways

1) **Similarity with historical interactions**
    
    You can recommend movies to the user similar to those they have interacted (seen) with in the past. This can be achieved by computing the dot product of movies.

2) **Similarity between media and user profiles**

The media’s TF-IDF vectors can be viewed as their _profile_. Based on user preferences, which can be seen from its historical interactions with media, you can build _user profiles_ as well

![[Pasted image 20220413120750.png]]