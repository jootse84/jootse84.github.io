Recently I've been reading about [methods to examine data](http://infolab.stanford.edu/~ullman/mmds/ch3.pdf) for finding similar 'items' in sets. A measure frequently used in data mining for this purpose is called *Jaccard Index*.

The *Jaccard Index* is a statistic value often used to compare the similarity between *sets* for binary variables. It measures the size ratio of the intersection between the *sets* divided by the length of its union.

Jaccard(A, B) = $\frac{\|A \bigcap B\|}{\|A \bigcup B\|}$

For instance, if J(A,B) is the *Jaccard Index* between sets A and B and A = {1,2,3}, B = {2,3,4}, C = {4,5,6}, then:

- J(A,B) = 2/4 = 0.5
- J(A,C) = 0/6 = 0
- J(B,C) = 1/5 = 0.2

A way to better introduce this concept and its use is by a practical example.

Let’s think about Netflix movie recommendation, where users get suggestions of movies or series they may like depending on their previous selections.

The way to suggest a new movie to a user is by comparing his/her *taste* with that of other users. That is to say, if user *A* likes movies and series similar to the ones that user *B* likes, it makes sense to recommend to user *A* the movies that user *B* has liked but that user *A* still has not seen.

An extremely easy and simple way to record the data is by the use of booleans. If a user likes a movie/series you mark it as a *one*. If the user does not like a movie/series you mark it as a *zero*. In the following example, Luis likes *Stranger Things*, *X-Men*, *Jurassic Park* and *Inception*:


|                     | Manolo | Pepito | Luis   |
| ------------------- |:------:| :-----:|:------:|
| There Will Be Blood | 1      | 0      | 0      |
| Stranger Things     | 0      | 1      | 1      |
| Jurassic Park       | 0      | 0      | 1      |
| X-Men               | 0      | 1      | 1      |
| The Master          | 1      | 0      | 0      |
| Inception           | 1      | 1      | 1      |


Using this matrix we are going to calculate the *Jaccard Index* of Pepito with respect to the rest of users (Manolo and Luis). From now on, to make things easier, we will refer to this matrix as *M*.

# Calculating Jaccard

The *Jaccard Index* (between any two columns/users of the matrix *M*) is $\frac{a}{a+b+c}$, where:

- a = number of rows where both columns are 1
- b = number of rows where this and not the other column is 1
- c = number of rows where the other and not this column is 1

With [R](https://www.r-project.org/) we can calculate the *Jaccard Index* of two users using its [rowSums](http://stat.ethz.ch/R-manual/R-patched/RHOME/library/base/html/colSums.html) function, which returns a vector with the sum of its rows. For instance, for the following matrix:

| A | B | C |
|:-:|:-:|:-:|
| 1 | 0 | 0 |
| 1 | 1 | 1 |

the [rowSums](http://stat.ethz.ch/R-manual/R-patched/RHOME/library/base/html/colSums.html) will result in the vector *[1, 3]*.

If we perform the [rowSums](http://stat.ethz.ch/R-manual/R-patched/RHOME/library/base/html/colSums.html) of the matrix *M* for two users (columns) and we save the result in vector *v*, we will conclude following $\frac{a}{a+b+c}$ for the *Jaccard Index* calculation that:

- a = number of items in *v* with value equals to 2
- b + c = number of items in *v* with value equals to 1

Now, we can more easily code the function that calculates the *Jaccard Index* given two users (identified by the column number) for any matrix structured as *M*.

```R
jaccard <- function(M, user1, user2) {
  sums = rowSums(M[,c(user1, user2)])

  similarity = length(sums[sums==2])
  total = length(sums[sums==1]) + similarity
  
  similarity/total
}
```

By using the algorithm, we conclude that the *Jaccard Similarity Index* of Pepito and Luis is pretty high (3/4) while it is low between Pepito and Manolo (1/5). Our system, then, should recommend to Pepito movies that Luis already watched and liked.

Obviously, Netflix doesn't use the Jaccard similarity coefficient for its recommendation system; instead it uses the complex, but efficient **large-scale parallel collaborative filtering**. But I think using movie recommendations as an example is a good choice for simply introducing this concept.
