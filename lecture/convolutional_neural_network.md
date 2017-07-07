# Convolutional Neural Network

## What is image classification?

Transform an image into a label.

## Why is image classification hard?

The amount of input data is large.
The classification has to be robust with the existence of all the deformations of the images.

## What does an image classifier look like?

```python
def predict(image):
  # Something
  return class_label
```

## What is data driven approach?

1. Collect data of images and labels
2. Train the classifier with the training data
3. Test the classifier with test data

## Is there any examples of data-driven classifier?

Nearest neighbor algorithm.

## How does nearest neighbor work?

Define the distance between images (examples include L1 distance and Euclidean distance ).
For each test image, find the image with the smallest distance from it, and output the corresponding label.

## What is hyperparameter?

Global parameter set for the classifier that is fixed in the training process.

## What is k-nearest neighbor method?

Find the k-nearest neighobrs, let them vote on the label.

## How do we set the hyperparameters?

Try what hyperparameters work best on test data.
For example, separate the training data into several folds, and try out (train the classifier with) **all candidates of** hyperparameters on **each** fold, then test the classifier on the test data, and take the average for each candidate. Take the candidate hyperparameter with best performance.

## What is over-fitting?

The classifier works very well on training data, but works very bad on test data.

## Is k-nearest neighbor actually used?

No! Terrible performance in practise. The dimension of the input data is too high.

## What is the idea of parametric approach?

Think f(x,W) as a function that takes the image x and parameters W and outputs the label from a specific set.

## What is linear classifer?

For example, x can be thought of as 1000x1 vector, and W is set to be 10x1000 matrix.
Take f(x,W)=Wx, the result is a 10x1 vector, indicating the evaluated score of this image for each label. If for a cat image x, f(x,W) produces a vector with the entry for cat higher than others, then f(x,W) is a good classifier.

Further we can set f(x,W)=Wx+b, where b is the bias vector.

## What is loss function?

Loss function of parameter W measures how bad a classifer f with parameter W works.
Assume that f(x,W)=Wx+b and it produces a vector of probabilities on cat image x, with label dog having the highest score 10, and cat has score 9, then the loss of cat can be considered as 9-10=1 in this case, with respect to training image x.

## What is Multiclass SVM loss?

Take the scores of another label, minus the score of the true label, add one, then clamp up to zero. Sum up these for each label that is not the true label.

## Is there any problem with SVM loss?

If initialized with W=0, then the loss is small.
There is a large set of W such that L is 0, which largely differs from each other.

## How to solve this problem?

Add the weight of W to the loss function, which is called regularization.

## What is softmax classifier?

A generalization of Multinominal Logistic Regression into higher dimensions.

Take the scores of all labels, take the exponential, normalize the L1 norm to 1,  take negative log. The loss function is defined as the result corresponding to the correct label.

## What is mini-batch Gradient Descent?

Trying to do the gradient descent on the entire training data is too slow.
Each time sample a small portion of the training data and take the gradient descent with them.

## How to use computational graph to compute gradient descent?

Forward the input along the graph and get the evaluation at each node.

Set the gradient at the final node to 1, and back-propagate along the path to the inputs.

On each node, the back-propagation result is based on the type of the node, and the evaluation of the inputs of the node.

Specifically, assume a node computes function `f` on inputs `x` and `y` and outputs `z`. In the forward propagation, directly computes `z=f(x,y)`. In backward propagation, the gradient input computes from the output `z` direction,  compute the partial directive of `f(x,y)` with respect to `x` (in analytical way) which may be related to the value of `x`, `y`, `z` and the gradient from output, and hard-coded in the program of this node. Compute the partial directive out and back-propagate it back to the `x` input. Do the same thing for `y`.

## What is the difference if the input and output are vectorized?

The evaluated values will be vectors correspondingly.
The gradient are vectors of the same size.

## Why would the hidden layer with huge number of parameters be able to do something more interesting?

