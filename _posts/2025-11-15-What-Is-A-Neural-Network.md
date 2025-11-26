---
title: My Neural Network (Part 1)
date: 2025-11-10 7:00:00 -500
math: true
categories: [python programming, articial intelligence,MNIST dataset, Feedforward Neural Network]
tags: [python programming,articial intelligence,MNIST dataset, Feedforward Neural Network]
---

# Background

I have always found AI fastinating. I remember watching youtube videos of people explaining how a neural network worked, what they were used for, and how they were improving. Recently I decided to fully plung into the world of artificial intelligence. The challenge that I wanted to take on was making a network from scratch. This network would be trained on the MNIST dataset, which is a large dataset of handrawn digits on a greyscale 28 x 28 grid. There are many great tools out there for these types of projects, like [TensorFlow](https://www.tensorflow.org/), [Keras](https://keras.io/), [PyTorch](https://pytorch.org/), and more. These were rather easy to use however, and I wanted to actually understand how the math was happening and how that was translated into code. So I am building my own network.

A neural network is a series of algorithms that is used to produce a desired response based on a vairety of inputs. This isn't as advanced as ChatGPT or any other large language model in any way, but is still the most difficult challenge I have taken on in my time coding. This first post I hope to explain what a neural network is, and how the type I am using works. Then in the second post I will show how I turned the equations into code, and show off the trained network.

I taught myself most of this from the textbook *Deep Learning* by Ian Goodfellow, Yoshue Bengio, and Aaron Courville. The math required to be able to make something like this I learned from random YouTube videos. I am certainly not fully capable in calculus, especially multivariable calculus or gradients, but I understand enough to translate these equations and processes into code!

# The Fundamentals

To understand what I am building, I am going to first go through the fundamentals for what I am making. I am making a **Feedforward Neural Network (FNN)**, but there are many other types of networks, like Convolutional Neural Networks, Generative Adversarial Networks, and Recurrent Neural Networks. For what I want to do—digit recognition—an FNN works perfectly.

## Structure and Process

A Feedforward Neural Network can be broken down structurally into three parts: the **input layer**, **output layer**, and **hidden layer(s)**. These are all constructed of **nodes**, which work together to create an output that reduces the cost. The process for an FNN can be broken down into two major parts. 

First, the network **forward propagates** information from the input neurons to the hidden layers, applying the respective weights, biases, and activation functions. This will all be discussed in more detail later. Then, at least for this function, the outputs have an extra non-linear function applied, which turns the outputs into probabilities.

The second step is referred to as **backpropagation**—although this name is a little misleading because more than just “sending gradients backward” happens here. The outputs are compared to the correct answers, and the **loss** is calculated. Then a lot of calculus happens, including partial derivatives, the chain rule, and multivariable calculus. This allows us to find the gradient and update the weights and biases using **gradient descent**. In simple terms, we can determine how important every node was to the output, and then adjust the weights and biases so that the output will be closer to the correct answer next time.

There is much more to what a feedforward neural network is, but this provides enough context to understand the more detailed explanations below.

## Training

Training a network involves exposing it to data to make it more accurate. This is done by reducing the error between what the network predicts and what the correct answer is. Imagine doing a math problem, then checking how you did, and taking note of the difference before trying again. Doing this repeatedly will make you better at math—the same is true for a neural network.

I am training my FNN on the **MNIST dataset**: 70,000 handwritten digits. These are then subdivided into training and testing sets. The training set has 60,000 digits, and the testing set has the remaining 10,000.  

The training set is used to teach the model patterns—like that a “3” curves and a “7” has a sharp angle. Continuing the math analogy, training is like doing problems with the answer key. The testing set is like the final exam, where the model is evaluated on **new digits it has never seen before**. This tells us how well the parameters work, rather than tweaking them.

## Nodes 

Nodes are the core of the neural network. The activation layer, output layer, and all hidden layers are constructed of nodes. The equation for a node is below:

$$
a = \sigma\left(\sum_{i=1}^{n} w_i x_i + b\right)
$$

Here:  
- $a$ is the **output** of the node, which is sent to the next layer.  
- $\sigma$ is the **activation function**, a fixed nonlinear function that squashes or gates the weighted sum. Without nonlinearity, the network would just be a single linear transformation no matter how many layers you stack.  
- $w_i$ is the **weight** applied to input $x_i$ from the previous layer.  
- $b$ is the **bias** added after summing the weighted inputs.  

Two common activation functions are:

**Sigmoid:**
$$
\sigma(x) = \frac{1}{1+e^{-x}}
$$

**ReLU (Rectified Linear Unit):**
$$
\sigma(x) = \max(0, x)
$$

The sum $\sum_{i=1}^n w_i x_i$ can be expanded as:
$$
w_1 x_1 + w_2 x_2 + \cdots + w_n x_n
$$

After adding the bias $b$ and applying $\sigma$, the node passes its output $a$ to the next layer.

## Layers

There are three types of layers in a neural network: **input**, **hidden**, and **output**.  

- **Input layer:** Entry point for the data. It has no weights or biases. In my model, there are 784 input nodes (for the flattened 28×28 pixel image).  
- **Output layer:** Final layer of nodes. For MNIST, there are 10 output nodes, each representing one digit. The raw outputs (logits) are passed through the **softmax** function to convert them into probabilities.

Let the output layer be layer $L$, with:
$$
\mathbf{z}^{[L]} = W^{[L]} \mathbf{a}^{[L-1]} + \mathbf{b}^{[L]}
$$
Softmax is:
$$
a^{[L]}_i = \frac{e^{z^{[L]}_i}}{\sum_{j=1}^{K} e^{z^{[L]}_j}}, \quad i = 1, \dots, K
$$
Where:  
- $K$ is the number of output classes (10 for MNIST).  
- $\mathbf{a}^{[L]}$ is the output probability vector.  
- The outputs satisfy $0 < a^{[L]}_i < 1$ and $\sum_{i=1}^K a^{[L]}_i = 1$.  

- **Hidden layers:** In between input and output. They have weights, biases, and nonlinear activations. In my network, the first hidden layer has 128 nodes (ReLU), and the second has 64 nodes (ReLU).

## Backpropagation

Forward pass pushes information forward. Backpropagation sends the error signal backward so each weight and bias can be updated in a way that reduces loss.

### Constants and notation
- $L$: number of layers (input is 1, output is $L$)  
- $n^{[l]}$: number of neurons in layer $l$  
- $W^{[l]} \in \mathbb{R}^{n^{[l]}\times n^{[l-1]}}$: weights for layer $l$  
- $b^{[l]} \in \mathbb{R}^{n^{[l]}\times 1}$: biases for layer $l$  
- $Z^{[l]} = W^{[l]}A^{[l-1]} + b^{[l]}$: pre-activation at layer $l$  
- $A^{[l]} = f^{[l]}(Z^{[l]})$: post-activation at layer $l$  
- $\hat{Y} = A^{[L]}$: network outputs  
- $Y$: true labels  
- $m$: batch size  
- $\alpha$: learning rate  
- $\odot$: element-wise product  

### Loss functions
**Binary cross-entropy (sigmoid + BCE):**
$$
\mathcal{L} = -\frac{1}{m}\sum_{i=1}^{m}\Big[y^{(i)}\log \hat{y}^{(i)} + (1-y^{(i)})\log(1-\hat{y}^{(i)})\Big]
$$

**Multi-class cross-entropy (softmax + CE):**
$$
\mathcal{L} = -\frac{1}{m}\sum_{i=1}^{m}\sum_{k=1}^{K} y^{(i)}_k \log \hat{y}^{(i)}_k
$$

### Output layer gradients
For both sigmoid+BCE and softmax+CE:
$$
dZ^{[L]} = A^{[L]} - Y
$$
Then:
$$
dW^{[L]} = \frac{1}{m}\, dZ^{[L]}(A^{[L-1]})^\top
$$
$$
db^{[L]} = \frac{1}{m}\sum_{j=1}^m dZ^{[L]}_{:,j}
$$

### Hidden layers
Backpropagate:
$$
dA^{[l]} = (W^{[l+1]})^\top dZ^{[l+1]}
$$
$$
dZ^{[l]} = dA^{[l]} \odot f'^{[l]}(Z^{[l]})
$$
Then:
$$
dW^{[l]} = \frac{1}{m}\, dZ^{[l]}(A^{[l-1]})^\top
$$
$$
db^{[l]} = \frac{1}{m}\sum_{j=1}^m dZ^{[l]}_{:,j}
$$

Activation derivatives:
- Sigmoid: $\sigma'(z)=\sigma(z)(1-\sigma(z))$  
- tanh: $1-\tanh^2(z)$  
- ReLU: $1$ if $z>0$, else $0$ (conventionally $0$ at $z=0$)  

### Update step
For each layer $l$:
$$
W^{[l]} \leftarrow W^{[l]} - \alpha\, dW^{[l]}
$$
$$
b^{[l]} \leftarrow b^{[l]} - \alpha\, db^{[l]}
$$

### Explanation

Now this is a lot of math, so lets break this down and explain how it "teaches" the network. The first step is calculating the loss, which is where the network determines how wrong it was. We compare $\hat{Y}$ to the true labels $Y$ using a **loss function:**

* Binary cross-entropy (sigmoid + BCE):
$$
\mathcal{L} = -\frac{1}{m}\sum_{i=1}^{m}\Big[y^{(i)}\log \hat{y}^{(i)} + (1-y^{(i)})\log(1-\hat{y}^{(i)})\Big]
$$

* Multi-class cross-entropy (softmax + CE):
$$
\mathcal{L} = -\frac{1}{m}\sum_{i=1}^{m}\sum_{k=1}^{K} y^{(i)}_k \log \hat{y}^{(i)}_k
$$

The loss is a *single number* that tells the network how bad the predictions are.

The next step is sending the error signal backwards.

For the output layer:
$$
dZ^{[L]} = A^{[L]} - Y
$$
This is the derivative of the loss with respect  to the pre-activation $Z^{[L]}$.
It tells us how much did each node's raw score contribute to being wrong.

Then we calculate:
$$
dW^{[L]} = \frac{1}{m}\, dZ^{[L]}(A^{[L-1]})^\top
$$
$$
db^{[L]} = \frac{1}{m}\sum_{j=1}^m dZ^{[L]}_{:,j}
$$
* $dW^{[L]}$ is what tells us how much the weights of a specific node ought to change.
* $db^{[L]}$ is what tells us how much the bias of a specific node ought to change.

This process os repeated for each hidden layer $l = L - 1, L - 2, \dots, 1$:

Pass the error backward:

$$dA^{[l]} = (W^{[l+1]})^\top$$

This takes the error from the next layer and pulls it back to see how much each node in the current layer contributed. This is important for being able to determine how much the network ought to change each node. If a node had minimal input, it's much less important than a node that had massive contibution to the network.

Then the derivative of the activation function has to be applied.
$$
dZ^{[l]} = dA^{[l]} \odot f'^{[l]}(Z^{[l]})
$$
That is the full cicle for that step, but then the next the derivatives of the activations have not been applied yet, so that happens next.

Activation derivatives:
- Sigmoid: $\sigma'(z)=\sigma(z)(1-\sigma(z))$  
- tanh: $1-\tanh^2(z)$  
- ReLU: $1$ if $z>0$, else $0$ (conventionally $0$ at $z=0$)  

There is one more step however. We have to find the weights and bias gradients. This is the updating step. For each layer $l$:
$$
W^{[l]} \leftarrow W^{[l]} - \alpha\, dW^{[l]}
$$
$$
b^{[l]} \leftarrow b^{[l]} - \alpha\, db^{[l]}
$$

This is then repeated until for a certain number of cycles, called epochs. Then the model is trained, and the weights and biases can be saved and then loaded and reused.

# Conclusion

This is a ton of math, and at an extremely high level. And the craziest bit is that this only scratches the surface of the madness that FNN and other style neural networks possess. The chalenge now is taking all of these raw equations and ideas, and turning them into code. The idea is very scary, and I will be covering all of that in the next course.

