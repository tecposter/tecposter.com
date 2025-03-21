---
date: 2024-09-15T14:36:36.476557816Z
title: Montgomery reduction
---

The Montgomery representation of $x \in [0, N-1]$ is $\overline{x} = xR \ mod \ N$ 
- $N$: an n-word integer
- $R$: some integer greater than $N$ and co-prime with $N$, $gcd(R, N)=1$
	- for a efficient algorithm, $R$ is always a power of the radix $b$, for example, $R = b^n$ 

Montgomery reduction is used to speed up modular multiplication.

Assume 2 integers $a$ and $b$,  the *goal* is to compute $c = ab \ mod \ N$.

Select $R \in \Bbb{N}$ such that $R > N$ and $gcd(R, N) = 1$

First, transfer $a$ and $b$ to Montgomery form
- $\overline{a} = aR \ mod \ N$
- $\overline{b} = bR \ mod \ N$

Then compute $c = ab \ mod \ N$ from $\overline{a}$ and $\overline{b}$

$$
\begin{align*}
x & = \overline{a} \overline{b} \newline
  & = (aR \ mod \ N)(bR \ mod \ N) \newline
  & \equiv abR^{2} \ mod \ N \newline
x \ mod \ N &= abR^2 \ mod \ N \newline
\newline
\overline{c} &= abR \ mod \ N \newline
& = xR^{-1} \ mod \ N \newline
\newline
c & = ab \ mod \ N \newline
& = \overline{c}R^{-1} \ mod \ N \newline
\end{align*}
$$

where $R^{-1}$ such that $RR^{-1} \equiv 1 \ mod \ N$ 

# Montgomery reduction - REDC algorithm

The Montgomery reduction of $u \in [0, RN-1]$ is $Redc(u) = uR^{-1} \ mod \ N$

So the computation from $\overline{a}$ and $\overline{b}$ to $c = ab \ mod \ N$ can be rewrite as 
- $\overline{c} = Redc(\overline{a} \overline{b})$
- $c = Redc(\overline{c})$ 

The naive operation $uR^{-1} \ mod \ N$ need to multiply by $R^{-1}$ and divide by $N$. The problem is that the division by $N$ is expensive on most computer hardware. 

The major idea of **REDC** is try to avoid dividing by $N$ but by $R$. As $R$ can be chosen to be a power of 2. The division of $2^n$ can be replaced by shifting which are fast.

$$
\begin{align*}
let \ \hat{u} &= Redc(u) = uR^{-1} \ mod \ N \newline
\implies \hat{u}R & \equiv u \ mod \ N
\end{align*}
$$
The we can find a integer $\hat{s}$ such that $\hat{u}R = u + \hat{s}N$.

How to find the integer $\hat{s}$?
$$
\begin{align*}
\hat{u}R & = u + \hat{s}N \newline
\implies 
  \hat{s} & = \frac{\hat{u}R - u}{N} \newline
    & = \frac{(uR^{-1} \ mod \ N)R - u}{N}
\end{align*}
$$
Try 
$$
\begin{align*}
let \ k &= \frac{(R^{-1} \ mod \ N)R - 1}{N} \newline
let \ s &= k u \ mod \ R
\end{align*}
$$
Replace $\hat{s}$ with $s$.

$$
\begin{align*}
u + sN & = u + (ku \ mod \ R)N \newline
  & \equiv u + (kuN \ mod  \ R) \newline
  & \equiv u(1+kN) \ mod \ R \newline
  & \equiv  u(1 + \frac{(R^{-1} \ mod \ N)R - 1}{N}N) \ mod \ R \newline
let \ R^{-1} \ mod \ N &= R^{-1} - xN \newline
\implies
  & \equiv u(1 + \frac{(R^{-1} - xN)R - 1}{N} N) \ mod \ R \newline
  & \equiv u(1 + R^{-1}R - xNR - 1) \ mod \ R \newline
  & \equiv uR(R^{-1}-xN) \ mod \ R \newline
  & \equiv 0 \ mod \ R
\end{align*}
$$

So $u + sN$ is a multiple of $R$ and $u + sN \equiv u \ mod \ N$.

Finally
$$
\begin{align*}
let \ v & = \frac{u+sN}{R} \newline
  & \equiv \frac{u+sN}{R} RR^{-1} \ mod \ N \newline
  & \equiv (uR^{-1} + sNR^{-1}) \ mod \ N \newline
  & \equiv uR^{-1} \ mod \ N
\end{align*}
$$

If $0 \le v < n$ then $\hat{u} = Redc(u) = v$. Otherwise $\hat{u} = Redc(u) = v - n$
$$
\begin{align*}
s = ku \ mod \ R 
  & \implies 0 \le s < R \newline
  & \implies 0 \le sN < RN \newline
u \in [0, RN-1] 
  & \implies 0 \le u + sN < 2RN \newline
  & \implies 0 \le \frac{u+sN}{R} < 2N
\end{align*}
$$


refer
- https://www.nayuki.io/page/montgomery-reduction-algorithm
- https://en.wikipedia.org/wiki/Montgomery_modular_multiplication
