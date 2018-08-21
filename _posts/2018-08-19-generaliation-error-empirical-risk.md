---
layout: post
title:  "ML Diaries #1: Generalization Error vs. Empirical Risk"
date:   2018-08-19 19:52:00 -0500
permalink: generaliation-error-empirical-risk
tags:
  - machine learning
  - generalization
  - empirical risk minimization
  - ml diaries
---

### Why should you care about it? ###
The basic idea is that there are two fundamental sources of error and you can usually only improve one at the expense of the other. Understanding these two phenomena and how a learning algorithm strikes the balance between them helps us (i) pick the right algorithm for the right task and (ii) tune that right algorithm properly to approach minimal error.

### Brief Description ###
Without bringing in the fancy lingo yet, let's look into the key ideas that everyone who has dipped their toes into the world of machine learning is familiar with. What is the fundamental goal of a learning algorithm? Extracting the representative information about whatever we are interested in while leaving out the noise and uninformative details.

A learning algorithm that is trained to recognize dog faces from images is trying to learn what makes a dog face a dog face without getting distracted by the trees and clouds in the background, right? Well it is easy to give a mile-high overview, but once you really think about the details, the line that separate what is informative/representative and what is not is veru blurry. The bias-variance tradeoff is capturing this optimization problem of trying to find that sweet spot and placing that line in a way to squeeze out as much of the "information" from the data that is not "contaminated" by noise.

### Let's Get (Slightly) Fancier: Some Statistical Learning Theory ###
Let's revisit this problem from a statistical learning theory point of view, more specifically within the Empirical Risk Minimization (ERM) framework. The key idea behind this huge research domain is to partition the "real" loss we would suffer from unseen datapoints (a.k.a. population risk) into two: (i) empirical risk and (ii) generalization error. What's next? Well, since we have these two (sort of) separate entities, we can try to minimize and bound them individually to get a guarantee for the overall loss.

So,
(i) Training error: how much information you extracted so that you would recognize the same points when you see them again?
(ii) Generalization error: how much noise did you capture that would confuse you in the face a new datapoint by contaminating your representation of this phenomenon?

When you think about how to tune our imaginary line that separates information and noise, the most straight-forward approach is to decide on how much detail to extract and how much to leave behind. Since we generally can't pinpoint which specific details are relevant and which are not, the best we can do is to decide on how detailed we want our model's representation to be. Remember boys and girls, the devil (a.k.a. noise) is in the details!

So, how do you capture the "detail" of a representation? Think about a single perceptron, which has just two parameters to tune; it's bias and input weights. Naturally, it's not possible to fit so much information in a handful of tunable parameters and the simplicity, or the lack of complexity, makes it even unable to perform the simple binary XOR function. Basically, the complexity of the learning model dictates how detailed of a picture it can capture of the underling phenomena we are trying to approximate.

Let's think about the other extreme and try to train a fancy deep neural network to perform binary XOR. There are 4 possible input values, so we are essentially trying to approximate a look-up table of four mappings. Due to the randomness in training and the number of passes to ensure the weights properly approach their optimal values, this is an incredibly redundant choice and probably won't work well. On the other hand, if you fire a 2-3 perceptron network and you are all set in a few seconds. Now imagine if I wasn't trying to approximate a deterministic function, but a noisy observation instead. Think of how much deviation it would cost in each of the thousands of parameters of the deep network.

It seems like it is important to pick a learning model whose complexity fits our problem is crucial and it is. Many of the more advanced topics in statistical learning theory is trying to help you with just that. If you have heard of the following things and wondered what they are for, well now you know.

* Vapnik-Chervonenkis (VC) Dimension
* Rademacher Complexity
* Probably Approximately Correct (PAC) Learning
* Learning via Uniform Convergence

These are all frameworks and sets of theoretical guarantees to describe the complexity of a family of learning models or to use these descriptions to derive theoretical guarantees on learning algorithms. 

Hopefully I'll have enough energy to cover them on this blog, as well.