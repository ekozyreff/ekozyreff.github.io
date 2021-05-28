---
layout: post
title:  "How to fool a neural network"
date:   2021-05-21 17:09:00 -0300
categories: posts
---

This post is based on the paper "Deep neural networks and mixed integer linear optimization", by M. Fischetti and J. Jo (https://doi.org/10.1007/s10601-018-9285-6). I implemented the model described in the paper to generate adversarial examples using Tensorflow and GurobiPy. The code is availabe here.

You have probably heard about the MNIST dataset of handwritten digits. It has been used by many (many!) studies to test new ideas regarding neural networks. If not, it is a set of 70,000 images of digits (from 0 to 9) written by people and used for image recognition experiments.

The paper by Fischetti and Jo uses this dataset and investigates the application of a mixed integer programming (MIP) model to generate instances that will confuse a trained neural network and force it to make a mistake.

We start by training the network with the architecrure displayed below. The input layer has 784 units (because all images are grayscale with 28 x 28 pixels). Then there are 3 hidden layers with 8 units each activated with ReLU, and finally an output layer with 10 units activated with ReLU followed by a softmax activation. The entire netwotk is fully connected.

<p style="text-align:center;"><img src="/images/post_20210521_1.png" alt="neural networt architecture" width="600"></p>

The trained network achieves 91.8% accuracy on the test set, which is pretty good for such a simple network. The first five images below are examples of correctly classified images by the model, while the last one is incorrectly classified (it predicts a 3 instead of a 2).

<p style="text-align:center;"><img src="/images/post_20210521_2.png" alt="original digits"></p>

Now enters the MIP model that will modify those images enough to have the neural network classify them as another digit, which is chosen beforehand. Let's call it the target digit. In summary (and at a very high level), the model does two main things:

1. It introduces constraints enforcing the activation of the target digit (on the last layer) to be bigger than the activations of all other digits.
2. It uses the original image as input and modifies its pixels as little as possible, i.e., the objective function is the sum of these (absolute) changes.

The result is usually an image with some dots and lines around the digit, which can still be identified by a human. The following images are the previous ones with the modifications caused by the MIP solution. For each one of them, a different target digit was chosen. Oddly, in the last image, it looks like a single black dot above the digit was enough to have the network classify it correctly as a 2.

<p style="text-align:center;"><img src="/images/post_20210521_3.png" alt="modified digits"></p>

An advantage of the proposed method is its flexibility, in the sense that choosing a target digit for the modified image is done by changing only a few parameters in the model.

Another strength is the fact that, since the problem is solved via an exact method, the image generated is guaranteed to have the least amount of differences in comparison with the original one.

A possible downside is the fact that the modified images are not part of the same population that the network was trained with, since the original images do not have dark pixels floating around the digits.

The connections between Machine Learning and Mathematical Optimization are a fruitful and blooming area of research. The paper considered in this post explored an interesting application of MIP to Neural Networks and is an initial step for other applications that will surely derive from it.

---

This article was originally published <a href="https://www.linkedin.com/pulse/how-fool-neural-network-ern%25C3%25A9e-kozyreff-filho" target="_blank">here</a>.
