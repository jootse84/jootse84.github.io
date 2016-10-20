The *Jaccard index* is a statistic value often used to compare the similarity between *sets* for binary variables. It is simply the ratio of the size of the intersection of the *sets* and the size of the union of those *sets*.

jaccard(A, B) = $\frac{\|A \bigcap B\|}{\|A \bigcup B\|}$

eg. if J(A,B) is Jaccard index between sets A and B and A = {1,2,3}, B = {2,3,4}, C = {4,5,6}, then:

- J(A,B) = 2/4 = 0.5
- J(A,C) = 0/6 = 0
- J(B,C) = 1/5 = 0.2

A good way to introduce this concept and its use is by a simple example. Let's think about Netflix movie recommendation, where users get suggestions of movies or series they may like, depending on their previous selections.

The way to suggest a new movie to an user, is by comparing his/her *taste* with other users. That is to say, if user *A* likes similar movies and series than user *B* likes, it is pretty intuitive to recommend to user *A* movies that user *B* liked but user *A* still have no seen.

An extremly easy and simple way to record the data is by the use of booleans. If user likes a movie/serie it will be record as a *one* its relationship, while it will with a *zero* if contrary. In the following example, Luis likes *Stranger things*, *X-Men*, *Jurassic Park* and *Inception*:


|                     | Manolo | Pepito | Luis   |
| ------------------- |:------:| :-----:|:------:|
| There will be blood | 1      | 0      | 0      |
| Stranger things     | 0      | 1      | 1      |
| Jurassic Park       | 0      | 0      | 1      |
| X-Men               | 0      | 1      | 1      |
| The master          | 1      | 0      | 0      |
| Inception           | 1      | 1      | 1      |


For the following example, now it is possible to calculate the Jaccard index of Pepito respect the rest of users (Manolo and Luis). From now onwards, we will talk about matrix *M*, which has recorded the previous data of movies

# Calculating Jaccard

We know that Jaccard (computed between any two columns) is $\frac{a}{a+b+c}$, where:

- a - number of rows where both columns are 1
- b - number of rows where this and not the other column is 1
- c - number of rows where the other and not this column is 1

With [R](https://www.r-project.org/), we can calculate the Jaccard index of 2 users using its *rowSums* function, that returns a vector with the summatory of its rows. For instance, for the following matrix:

| A | B | C |
|:-:|:-:|:-:|
| 1 | 0 | 0 |
| 1 | 1 | 1 |

the *rowSums* will be the vector [1, 3].

If we calculate the rowSums of the matrix *M*, and we save the result in vector *v*, we will conclude following $\frac{a}{a+b+c}$ that:

- a - number of items in *v* with value equals to 2
- b + c - number of items in *v* with value equals to 1

Now, we can simply code with R, a function to calculate the Jaccard index given two users (columms), and the matrix with the relevant data.

```R
jaccard <- function(M, user1, user2) {
  sums = rowSums(M[,c(user1, user2)])

  similarity = length(sums[sums==2])
  total = length(sums[sums==1]) + similarity
  
  similarity/total
}
```

We will conclude, by using the algorithm, that the Jaccard similarity index of Pepito and Luis is pretty high (3/4) while it is low between Pepito and Manolo (1/5). Our system, then, will be wise if recommends to Pepito movies that Luis already watched and liked.

Obviously, Netflix doesn't use the Jaccard similarity coefficient for its recommendation system, instead it uses the complex but efficient **large-scale parallel collaborative filtering**. But I think using movie recommendation as an example it is a good choice for simply introduce this concept.
