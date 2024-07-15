---
title: Heap sort
date: 2024-07-13T13:46:22.046695155Z
---
Heap sort is a comparison-based sorting by using max-heap or min-heap.

# max-heap and min-heap

A max-heap is a complete [binary
tree](https://en.wikipedia.org/wiki/Binary_tree) in which the value of
each internal node is greater than or equal to the values of that
node\'s children.

Same as max-heap, but in min-heap, the value of the parent node is
lesser than or equal to the values of the children nodes.

As a complete binary tree, we can represent the max-heap or min-heap as
an array. Starting from mapping the root node to index `0`{.verbatim},
we map each node from top to bottom and from left to right in the tree
to a index in the array with one-to-one correspondence.

Then we\'ll find that if the parent node is at the index `i`{.verbatim},
the left child will be at the index `2*i+1`{.verbatim} and the right
child will be at the index `2*i+2`{.verbatim}.

``` shell
          ┌───100(0)────┐
          │             │
    ┌───19(1)──┐     ┌──36(2)──┐
    │          │     │         │
 ┌─17(3)─┐   3(4)   25(5)    1(6)
 │       │
2(7)   7(8)

    ┌───┬──┬──┬──┬─┬──┬─┬─┬─┐
    │100│19│36│17│3│25│1│2│7│
    └───┴──┴──┴──┴─┴──┴─┴─┴─┘
     0   1  2  3  4 5  6 7 8
```

## Heapify

As we know, in max-heap, each node should follow the max-heap property,
which means each node\'s value should be greater than or equal to its
children\'s.

First, let\'s assume that one node\'s both child sub-tree follow the
max-heap property, and the process that maintain this node\'s max-heap
property is called max-heapify. Similarly, min-heap has min-heapify.

The process of max-heapify for one node consists the steps below

1.  Compare the node\'s value with its children\'s, if it meets the
    max-heap property, stop.
2.  If the node violdates the max-heap property, swap this node with one
    child node with max value and then go back to step 1 to repeat
    again.

Here is the rust function below that implements the max-heapify. The
following code could help us understand the heapify process more
precisely.

``` rust
fn max_heapify<T: Ord + Copy>(arr: &mut [T], i: usize) {
  let mut largest = i;
  let left = 2 * i + 1;
  let right = 2 * i + 2;

  let len = arr.len();
  if left < len && arr[left] > arr[largest] {
    largest = left;
  }
  if right < len && arr[right] > arr[largest] {
    largest = right;
  }
  if largest != i {
    let tmp = arr[i];
    arr[i] = arr[largest];
    arr[largest] = tmp;

    max_heapify(arr, largest);
  }
}
```

## Build heap

If there\'s only one node violates the heap property. We only need to
heapify this node to convert the tree to heap. As the process of heapify
won\'t break the heap properties of other nodes.

But if there are more than one invalid node, it will not fix the other
invaid nodes by just heapfiying one of them.

``` shell
          One invalid node                    Two invalid nodes
          ┌───100(0)────┐                     ┌───100(0)────┐
          │             │                     │             │
    ┌───*5(1)──┐     ┌──36(2)──┐        ┌───*5(1)──┐     ┌──36(2)──┐
    │          │     │         │        │          │     │         │
 ┌─17(3)─┐   3(4)   25(5)    1(6)    ┌─*7(3)─┐   3(4)   25(5)    1(6)
 │       │                           │       │
2(7)   7(8)                         2(7)  17(8)
```

We can build heap by heapify all the nodes one by one from right to left
and from bottom to top.

As all the leaf nodes have no children, they all meet the heap
properties, so we don\'t need to heapify and can just skip them. And we
can treat them as valid sub heap with only one node.

Now we can find that all the sub trees whose height is 1 are valid sub
heaps, so we can heapify all of their parent nodes one by one to turn
all the sub trees whose height is 2 to valid sub heaps.

In this way, we keep heapifying nodes with valid child sub heaps from
right to left and from bottom to top until to the root node to heapify
the whole tree.

Here is the rust code below that heapify a whole tree.

For tree with `n`{.verbatim} nodes, the index of the last node is
`n-1`{.verbatim}, then the index of its parent is
$\lfloor{\frac{n-2}{2}}\rfloor=\lfloor{\frac{n}{2}}\rfloor-1$, so the
node with index $\lfloor{\frac{n}{2}}\rfloor-1$ is the last node that
with children.

``` rust
let len = arr.len();
for i in (0..(len / 2)).rev() {
  max_heapify(arr, i);
}
```

# Heap sort {#heap-sort-1}

The Heap sort consists of the following steps:

1.  Build a heap (max-heap for ascending order, min-heap for descending
    order) from a complete binary tree.
2.  Sperate the array to 2 parts, first part is heap, and the second
    part is sorted list. All the elements are in the heap at this point.
3.  Switch the root node with the last node in the heap
4.  Reduce the size of heap by excluding the current last node, which
    means move the node with max/min value in max-heap/min-heap to the
    beginning of the sorted list.
5.  Heapify the current root node and go back to step 3 to repeat it
    again and again til all the nodes moved to sorted list.

Here is the rust code for max heap sort.

``` rust
fn max_heapsort<T: Ord + Copy>(arr: &mut [T]) {
  let len = arr.len();
  // from 0 to (len/2 - 1)
  for i in (0..(len / 2)).rev() {
    max_heapify(arr, i);
  }

  // from (len-1) to 0
  for j in (1..len).rev() {
    // switch root node with last node
    let tmp = arr[0];
    arr[0] = arr[j];
    arr[j] = tmp;
    // exlude last node j from heap
    max_heapify(&mut arr[0..j], 0);
  }
}
```

# Time complexity

## Time complexity of heapify method

The best case for heapify would happen when the node meets the heap
property. After two comparisons, there are no extra operations, so the
runtime in the best case would be $O(1)$.

### Solution 1

The worst case would happen when we need to walk through the tree from
top to bottom. Assume the height of the tree is $H$, then the time
complexity in worst case is $O(H)$

As we know the size of a full binary tree (or perfect binary tree) with
height $H$ is

$$
\begin{aligned}
S_{full}(H) &= 2^{0} + 2^{1} + 2^{2} + ... 2^{H} \\\
2S_{full}(H) &= 2^{1} + 2^{2} + 2^{3} + ... 2^{H} + 2^{H+1} \\\
\implies S_{full}(H) &= 2S_{full}(H) - S_{full}(H) = 2^{H+1} - 1
\end{aligned}
$$

As a complete binary tree, the size of heap follows the inequation below

$$
\begin{aligned}
S_{full}(H-1) &< S_{heap}(H) \leq S_{full}(H) \\\
2^{H} - 1 &< S_{heap}(H) \leq 2^{H+1} - 1 \\\
\text{Assume: } S_{heap}(H) &= n \\\
\text{then: } 2^{H} - 1 &< n \leq 2^{H+1} - 1 \\\
\end{aligned}
$$

Then we can get the height $H$ from the size of a heap $n$

$$
\begin{aligned}
2^{H} - 1 &< n \leq 2^{H+1} - 1 \\\
\implies log_{2}(n+1) - 1 &\leq H < log_{2}(n+1) \\\
\implies H &= \lceil{log_{2}(n+1)}\rceil - 1
\end{aligned}
$$

So the complexity of heapify is

$$
\begin{aligned}
T(n) = O(H) = O(\lceil{log_{2}(n+1)}\rceil - 1) = O(log_{2}n)
\end{aligned}
$$

### Solution 2

The recurrence of heapify should be something as below. The cost of
swaping elements to walk through one level of tree is constant, so it
can be represented as $\Theta(1)$

$$
\begin{aligned}
T(n) &= T(?) + \Theta(1)
\end{aligned}
$$

Then we need to figure it out what\'s the size of the sub-problem
$T(?)$.

In the best case, size of the child sub trees are equal, then the size
of the sub-problem is $T(\frac{n-1}{2})$

In the worst case, both sub trees are full binary tree, but the height
of left sub tree is 1 level higher than the right.

``` shell
       The best case                   The worst case

      ┌──────(0)────┐                 ┌──────(0)────┐
      │             │                 │             │
 ┌───(1)──┐      ┌──(2)──┐      ┌─── (1)──┐      ┌──(2)──┐
 │        │      │       │      │         │      │       │
(3)      (4)    (5)     (6)  ┌─(3)─┐   ┌─(4)─┐  (5)     (6)
                             │     │   │     │
                            (7)   (8) (9)   (10)
```

Then let\'s assume the height of right sub tree is $H$, and the height
of left one is $H+1$.

So we can get that:

-   the size of left sub tree is $2^{H+2}-1$.
-   the size of right sub tree is $2^{H+1}-1$
-   the total size of heap is
    $(2^{H+2}-1)+(2^{H+1}-1)+1=2^{H+2}+2^{H+1}-1$

The ration of left sub tree to heap is

$$
\begin{aligned}
\frac{2^{H+2}-1}{2^{H+2}+2^{H+1}-1} = \frac{2-\frac{1}{2^{H+1}-1}}{2+1-\frac{1}{2^{H+1}-1}}
= \frac{2-\frac{1}{2^{H+1}-1}}{3-\frac{1}{2^{H+1}-1}}
\end{aligned}
$$

So now we can get the recurrence of the heapify, and it meets the case 2
of master theorem.

$$
\begin{aligned}
T(n) & \leq T(2n/3) + \Theta(1) \\\
\implies T(n) &= O(log_{2}n)
\end{aligned}
$$

## Time complexity of build heap

If one node\'s index is $i$, then the index of its parent is
$\lfloor{\frac{i-1}{2}}\rfloor$.

For tree with $n$ nodes, the index of the last node is $n-1$, then the
index of the last node\'s parent is $\lfloor{\frac{n-2}{2}}\rfloor$, the
parent of the last node also is the last node whose height is 1.

Then last node with height 1 is $\lfloor{\frac{n}{2}}\rfloor-1$, the
first node with height 1 is $\lfloor{\frac{n}{4}}\rfloor$.

For height $H$, last node is $\lfloor{\frac{n}{2^{H}}}\rfloor-1$, first
node $\lfloor{\frac{n}{2^{H+1}}}\rfloor$, so the number of nodes with
height $H$ is

$$
\begin{aligned}
(\lfloor{\frac{n}{2^{H}}}\rfloor-1) - (\lfloor{\frac{n}{2^{H+1}}}\rfloor) + 1 =
\lfloor{\frac{n}{2^{H}}}\rfloor - \lfloor{\frac{n}{2^{H+1}}}\rfloor \leq
\lceil{\frac{n}{2^{H+1}}}\rceil
\end{aligned}
$$

And the index of root node is

$$
\begin{aligned}
\lfloor{\frac{n}{2^{H}}}\rfloor - 1 &= \lfloor{\frac{n}{2^{H+1}}}\rfloor = 0 \\
\end{aligned}
$$

So the height of the root

$$
\begin{aligned}
\implies log_{2}n - 1 &< H \leq log_{2}n \\
\implies H &= \lfloor{log_{2}n}\rfloor
\end{aligned}
$$

Then we can conclude that the time complexity is

$$
\begin{aligned}
T(n) &= \sum_{h=0}^{\lfloor{log_{2}n}\rfloor}{\lceil{\frac{n}{2^{h+1}}}\rceil{O(h)}} \\\
&= O(\sum_{h=0}^{\lfloor{log_{2}n}\rfloor}{\lceil{\frac{n}{2^{h+1}}}\rceil{h}}) \\\
1 \leq \frac{n}{2^{h+1}} \leq \lceil{\frac{n}{2^{h+1}}}\rceil & < \frac{n}{2^{h+1}} + 1 < \frac{n}{2^{h}} \\\
\implies T(n) &= O(n\sum_{h=0}^{\lfloor{log_{2}n}\rfloor}{\frac{h}{2^{h}}}) \\\
&\leq O(n\sum_{h=0}^{\infty}{\frac{h}{2^{h}}}) = O(n\frac{1/2}{[1-(1/2)]^{2}}) = O(2n) = O(n)
\end{aligned}
$$

Summation formular of an infinite geometric series.

$$
\begin{aligned}
\sum_{k=0}^{\infty}{x^{k}} = \frac{1}{1-x}, \text{when } |x| < 1
\end{aligned}
$$
Summation formular of an infinite differentiating geometric series

$$
\begin{aligned}
\sum_{k=0}^{\infty}{kx^{k-1}} = \frac{1}{(1-x)^{2}}, \text{when } |x| < 1
\end{aligned}
$$
## Time complexity of heap sort

The first step of heap sort is build heap, the time complexity of this
step is $O(n)$.

The second step is swap root node with last node of heap, the time
complexity is $O(1)$.

The third step is exlude the last node, and heapify the new root node,
the time complexity $O(h)$, where h is the height of the heap.

Then go back to the second step and repeat it again and again til no
node left in heap.

```math
\begin{aligned}
T(n) &= O(n) + \sum_{i=n-1}^{1}{[O(1)+O(log_{2}i)]} \\\
&= \sum_{i=1}^{n-1}{O(log_{2}{i})} \\\
&= O(log_{2}{[1*2*...(n-3)*(n-2)*(n-1)]} = log_{2}{[(n-1)!]}) \\\
&= O((n-1)\log_{2}{(n-1)} - n\log_{2}{e} + \log_{2}{n}) \\\
&= O(n\log_{2}{n})
\end{aligned}
```

[Stirling\'s approximation](https://en.wikipedia.org/wiki/Stirling%27s_approximation)

$$
\begin{aligned}
\ln(n!) &= n\ln{n} - n + \Theta(\ln{n}) \\\
\log_{2}{(n!)} &= n\log_{2}{n} - n\log_{2}{e} + \Theta{(\log_{2}{n})}
\end{aligned}
$$

# Summary

-   The time complexity of heap sort is $O(n\log_{2}{n})$.
-   The space complexity of heap sort is $O(1)$, so it is an in-place
    algorithm
-   Its typical implementation is not stable, but can be made stable
    (Read
    [this](https://www.geeksforgeeks.org/stability-in-sorting-algorithms/)).

# Ref

-   [asciiflow](https://asciiflow.com/)
-   [Time & Space Complexity of Heap Sort](https://iq.opengenus.org/time-complexity-of-heap-sort/)
-   [Heaps](https://web.stanford.edu/class/archive/cs/cs161/cs161.1168/lecture4.pdf)
-   [Heapsort](https://en.wikipedia.org/wiki/Heapsort)
-   [Stirling's approximation](https://en.wikipedia.org/wiki/Stirling%27s_approximation)
-   [Factorial](https://en.wikipedia.org/wiki/Factorial)
-   [Derivative of Geometric Sequence](https://proofwiki.org/wiki/Derivative_of_Geometric_Sequence)
-   <https://www.happycoders.eu/algorithms/heapsort/>
