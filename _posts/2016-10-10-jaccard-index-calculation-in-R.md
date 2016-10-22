Recently I've been reading about [methods to examine data](http://infolab.stanford.edu/~ullman/mmds/ch3.pdf) for finding similar 'items' on sets. A frequently mesure used in data mining for this purpose is called *Jaccard index*.

The *Jaccard index* is a statistic value often used to compare the similarity between *sets* for binary variables. It measures the size ratio of the intersection between the *sets* divided by the length of its union.

Jaccard(A, B) = $\frac{\|A \bigcap B\|}{\|A \bigcup B\|}$

For instance, if J(A,B) is Jaccard index between sets A and B and A = {1,2,3}, B = {2,3,4}, C = {4,5,6}, then:

- J(A,B) = 2/4 = 0.5
- J(A,C) = 0/6 = 0
- J(B,C) = 1/5 = 0.2

A good way to introduce better this concept and its use is by a practical example. Let's think about Netflix movie recommendation, where users get suggestions of movies or series they may like, depending on their previous selections.

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


With the use of the previous matrix, we are going to try to calculate the Jaccard index of Pepito respect the rest of users (Manolo and Luis). From now onwards, for easier and fast convenience, we will reffer the prevous matrix as *M*.

# Calculating Jaccard

The Jaccard index (between any two columns/users of the matrix M) is $\frac{a}{a+b+c}$, where:

- a - number of rows where both columns are 1
- b - number of rows where this and not the other column is 1
- c - number of rows where the other and not this column is 1

With [R](https://www.r-project.org/), we can calculate the Jaccard index of 2 users using its [rowSums](http://stat.ethz.ch/R-manual/R-patched/RHOME/library/base/html/colSums.html) function, that returns a vector with the summatory of its rows. For instance, for the following matrix:

| A | B | C |
|:-:|:-:|:-:|
| 1 | 0 | 0 |
| 1 | 1 | 1 |

the [rowSums](http://stat.ethz.ch/R-manual/R-patched/RHOME/library/base/html/colSums.html) will result in the vector *[1, 3]*.

If we perform the [rowSums](http://stat.ethz.ch/R-manual/R-patched/RHOME/library/base/html/colSums.html) of the matrix *M* for 2 users (columns), and we save the result in vector *v*, we will conclude following $\frac{a}{a+b+c}$ for the Jaccard index calculation that:

- a - number of items in *v* with value equals to 2
- b + c - number of items in *v* with value equals to 1

Now, we can easier code the function that calculates the Jaccard index given two users (identified by the column number) for any matrix structured as *M*.

```R
jaccard <- function(M, user1, user2) {
  sums = rowSums(M[,c(user1, user2)])

  similarity = length(sums[sums==2])
  total = length(sums[sums==1]) + similarity
  
  similarity/total
}
```

By using the algorithm, we conclude that the Jaccard similarity index of Pepito and Luis is pretty high (3/4) while it is low between Pepito and Manolo (1/5). Our system, then, will be wise if recommends to Pepito movies that Luis already watched and liked.

Obviously, Netflix doesn't use the Jaccard similarity coefficient for its recommendation system, instead it uses the complex but efficient **large-scale parallel collaborative filtering**. But I think using movie recommendation as an example it is a good choice for simply introduce this concept.
