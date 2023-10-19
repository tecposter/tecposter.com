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
