A perceptron classifier is a simple model of a neuron. This machine learning algorithm is used as a supervised learning binary classifier - a function that given a set of elements can decide if a new element belongs to a predefined category.

We can compare the way it works just as a *simple* biological neuron: the dentrites recieves signals and the cell body process them. After that processing, the axon's neuron will send signals out to other neurons.

Now you will think, how does the classifier makes this processing?

Given an input element, each feature/column is multiplied by a *weight* value that is assigned to this particular feature. For instance, if the perceptron has a total of five inputs, then five weights will be adjusted for each of these inputs (or features).

All those signals, after multiplied by its weights, will be summed up to a single value. The neural network also adjusts the offset or *bias* of those calculations.

Finally, the result will turn into the output signal. Depending on the sum of the previous calculations an *activation function* (or *transfer function*) will classify the result to the category predicted. 

We can think about perceptrons as the most basic form of a neural network. This following illustration can help better to understand the process:

![alt Perceptron]({{ site.url }}/assets/images/perceptrons_post.png)

# Activation function

The basic form of activation function is the simple binary function that has as a possible results 0 or 1. It basically returns one if the output is positive while zero if it is negative.

f(s) = $\begin{cases} 1 & \textrm{if } s \ge 0 \\ 0 & \textrm{otherwise} \end{cases}$

# Calculate weights and bias of the perceptron

The most tricky part of a perceptron is how to calculate the vector of weights. I am going to use Python and a simple set of points to try to explain in a simple way how it works -- thanks to [@fedeisas](https://twitter.com/fedeisas) for guiding me in this process.

|   | col1 | col2 | target |
|:-:|:----:|:----:|:------:|
| 0 | 1    | 15   | -1     |
| 1 | 2    | 8    | -1     |
| 2 | 3    | 9    | -1     |
| 3 | 4    | 16   | -1     |
| 4 | 4    | 1    | 1      |
| 5 | 3    | 4    | 1      |
| 6 | 2    | 9    | 1      |
| 7 | 1    | 5    | 1      |


The following code creates our *fake* training test data with pandas based on the previous table.


```python
import numpy as np
import random
import matplotlib.pyplot as plt
import matplotlib.cm as cm
import pandas as pd

d = {
  'col1': [1,2,3,4,4,3,2,1],
  'col2': [15,8,9,16,1,4,9,5],
  'target': np.array([-1]*4+[1]*4)
}

df = pd.DataFrame(data=d)

pos_mask = df['target'] == 1
neg_mask = df['target'] == -1

plt.scatter(x=df[neg_mask]['col1'], y=df[neg_mask]['col2'], color='r')
plt.scatter(x=df[pos_mask]['col1'], y=df[pos_mask]['col2'], color='b')

plt.show()
```

![alt Scatter Points]({{ site.url }}/assets/images/perceptrons_scatter_post.png)

The next learning algorithm is the one responsible to train the weights and bias of our perceptron. It initializes all the weights and bias to zero. Then it iterates for all our elements in the training dataset a *maxiter* number of times, computing the result of the perceptron *activation*.

For each loop, the function consider that there is an error when the multiplication of the calculated activation and the real output/result is below zero: which means there is an error in the prediction. In that case, it updates the weights and bias.

```python
def perceptron_train(max_iter = 10, step = 0.1):
    col_names = df.columns.values[:-1]
    w = dict((name,0) for name in col_names)
    b = 0
    step = 0.1
    for i in range(max_iter):
        for index, row in df.iterrows():
            a = np.sum([row[col] * w[col] + b for col in col_names])
            if row['target'] * a <= 0: # is the prediction good?
                b += row['target'] * step
                for key in w.keys():
                    w[key] += row['target'] * row[key] * step
    return w, b

weights, bias = perceptron_train()

```

This training algorithm is considered **online**: instead of considering the entire dataset, it looks example by example. After trained our weights and bias, we can test our perceptron and make predictions:

```python
def predict(weights, bias, row):
    activation = 0
    for key in weights.keys():
        activation += weights[key] * row[key] + bias
    return np.sign(activation)
``` 

# Issues and notes about perceptrons

The following list are notes I have been taken during the research and study of perceptrons:

- The perceptron is a linear model that cannot solve the [XOR problem](http://toritris.weebly.com/perceptron-5-xor-how--why-neurons-work-together.html).

- The perceptron has a lot to gain by feature combination in comparation with decision trees.

- Constructing meta features for perceptrons from decision trees: the idea is to train a decision tree on the training data. From that decision tree, you can extract meta features by looking at feature combinations along branches. You can then add only those feature combinations as meta features to the feature set for the perceptron.

