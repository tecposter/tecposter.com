---
title: "Sigmoid"
date: 2023-10-19T18:39:30+08:00
draft: false
---

Sigmoid is often used in machine learning and neural networks. It converts any input into a probability between 0 and 1, which makes it very useful for binary classification tasks. The sigmoid function is defined as:

$$
\begin{align*}
& f(x) = sigmoid(x) = \frac{1}{1 + e^{-x}} = \frac{e^x}{1+e^x} \\
\end{align*}
$$

Derivative

$$
\begin{align*}
f'(x) &= (\frac{1}{1 + e^{-x}})' \newline
\frac{d}{dx}f(x) &= \frac{d}{dx}(\frac{1}{1 + e^{-x}}) \newline
  &= \frac{-1}{(1 + e^{-x})^2} \frac{d}{dx}(1 + e^{-x}) \newline
  &= -(1+e^{-x})^{-2}(\frac{d}{dx}(1) + \frac{d}{dx}(e^{-x})) \newline
  &= -(1+e^{-x})^{-2}(e^{-x} \frac{d}{dx}(-x)) \newline
  &= -(1+e^{-x})^{-2}(-e^{-x}) \newline
  &= \frac{e^{-x}}{(1+e^{-x})^2} \newline
  &= \frac{1}{(1+e^{-x})} \frac{e^{-x}}{(1+e^{-x})} \newline
  &= \frac{1}{(1+e^{-x})} \frac{1+e^{-x} - 1}{(1+e^{-x})} \newline
  &= \frac{1}{(1+e^{-x})} (1 - \frac{1}{(1+e^{-x})}) \newline
  &= f(x)(1-f(x))
\end{align*}
$$

pytorch

```python
import torch
import torch.nn.functional as F

input = torch.tensor([[1,2],[3,4], [5,6]])
output = F.sigmoid(input)
print(output)

# output
tensor([[0.7311, 0.8808],
        [0.9526, 0.9820],
        [0.9933, 0.9975]])
```

python math

```python
import math

def sigmoid(x):
    return 1 / (1 + math.exp(-x))

print([[sigmoid(1), sigmoid(2)], [sigmoid(3), sigmoid(4)], [sigmoid(5), sigmoid(6)]])

# output
[[0.7310585786300049, 0.8807970779778823], 
 [0.9525741268224334, 0.9820137900379085], 
 [0.9933071490757153, 0.9975273768433653]]
```
