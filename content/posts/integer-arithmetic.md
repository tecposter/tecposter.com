---
date: 2024-09-25T13:57:59.929995312Z
title: Integer Arithmetic
---
Study notes for Chapter 10 Integer Arithmetic of [book/Handbook of Elliptic and Hyperelliptic Curve Cryptography](/posts/book-handbook-of-elliptic-and-hyperelliptic-curve-cryptography/)

## Introduction

Every integer $u>0$ can be written in a unique way as the sum. Let $b \ge 2$ be an integer called the _base_ or the _radix_
$$
u = u_{n-1}b^{n-1} + ... + u_1b + u_0
$$
provided $0 \le u_i < b$ and $u_{n-1} \neq 0$ 

- This is what we will call the *base* b representation of u and will be denoted by $(u_{n−1} . . . u_0)_b$ .
- The $u_i$'s are the digits of $u$
- $u_{n-1}$: the most significant digit of $u$
- $u_0$: the least significant digit of $u$ 
- The _precision_ of $u$ is the largest $i$ such that $u_{i-1} > 0$, it corresponds to the length $n$ of $(u_{n−1} . . . u_0)_b$ and is denoted $|u|_b$ 
- In a computer, the base $b$ is usually a power of 2 and a number is internally stored as a sequence of 0 and 1 called bits.

The important elementary operations on bits, given two bits $x$ and $y$:

- complement: $\overline{x} = 1$ if and only if $x=0$, ($\overline{x} = b - 1 - x$, where $b$ denotes base ) 
- conjunction: $x \land y = 1$, if and only if $x$ and $y$ both equal 1
- disjunction: $x \lor y = 1$, if and only if at least one of $x$, $y$ equals 1
- exclusive disjunction: $x$ Xor $y$ = 1, if and only if exactly one of two values $x$, $y$ equals 1

_byte_: a sequence of 8 bits, which is the smallest quantity of main memory that a computer can address.

_word_: the size of a register, a processor can operate on several bytes at the same time by means of a register. Modern computers use words of 32 or 64 bits

## Internal representation

- A single precision integer, 1-word integer. For 32-bit architectures such integers belong to $[0, 2^{32}-1]$ 
- _multiprecision_, an array of consecutive words, n-word integer

The order of bytes within a word
- little endian: the least significant byte is stored first
- big endian: begins with the most significant byte


Example $262657 = 2^{18} + 2^9 + 1$
```
little endian: 00000001 00000010 00000100 00000000
big endian:    00000000 00000100 00000010 00000001
```

Example of multiple precision integers, big endian (the most significant word first)
- $u = (1128103691808033)_{10}$
- $u = (262657 \space 466742561)_{b}$ where $b=2^{32}$
- then $u$ has 51 digits in base 2
$$
u = (\frac{000000001000000001}{262657} \frac{00011011110100011110110100100001}{466742561})_2
$$
- 2-word integer, a double precision integer, or just double

Negative values
- In signed-magnitude notation
	- the sign of an integer is coded by a bit or byte, or word
	- 0 for positive, 1 or b-1 for negative
	- $u = (s, (u_{n-1}...u_0)_b)$
	- `0` has two representations: $(0, (0...0)_b)$ and $(b-1, (0..0)_b)$ 
- In complement format
	- if the highest bit of the most significant word is $0$ then _nonnegative_
		- $(u_{n-1}..u_0)_b = u_{n-1}b^{n-1}+...+u_0$ 
	- otherwise, _negative_
		- $(u_{n-1}..u_0)_b = -b^n + u_{n-1}b^{n-1}+...+u_0$
	- `0` has unique representation: $(0...0)_b$ 
	- the opposite of $u$, $(\overline{u_{n-1}}...\overline{u_0}) +1$, where $\overline{u_i}=b-1-u_i$ bitwise complement of $u_i$  

Example $(152)_{10}$ 
- in signed-magnitude format: $152$: $(0, (2124)_4)$,  $-152$: $(3, (2124)_4)$
- in complement notation
	- $152 = (02124)_4$ 
	- $-152 = (\overline{02120})_4 + 1=(31213)_4+1 = (31220)_4$ 
		- $= -4^4 + 4^3 + 2*4^2 + 2 * 4 + 0$  

> In base 2, complement notation is called two’s complement notation and the highest bit of an integer codes its sign in both representations. Therefore the same sequence of bits corresponds to different values. For instance $(10011000)_2$ is equal to $−24$ in signed-magnitude format whereas it is equal to $−104$ in two’s complement notation.


## Elementary operations

the single $u$, $v$ 
- comparison
- Bitwise complement: $\overline{u} = b - 1 - u$ 
- Bitwise conjunction, disjunction and exclusive disjunction
	- $u \land v$, $u \lor v$, $u$ Xor $v$ 
- shift bits
	- right shift t bits , $u >> t$, $\lfloor u/2^t \rfloor$ 
	- left shift t bits, $u << t$, $u2^t \mod b$  
- Addition $u + v = kb + w$
	- k: a carry bit equal to 0 or 1
	- a single w
- Subtraction, $u-v$, giving a result a single $w$ and a carry $k$
	- If $u \ge v$, then $w = u - v$ and $k = 0$
	- otherwise, $w = b + u - v > 0$ and $k= -1$, The nonnegative quantity −k is sometimes called the borrow bit.
- Multiplication of two singles u and v giving a double $w = u × v$ .
- Division of a double u by a single v, when the quotient $q = \lfloor u/v \rfloor$ and the remainder $r = u \mod v$ are both singles. This operation computes q and r simultaneously.


# Addition

Algorithm 10.3 Addition of non-negative multi-precision integers
- input: Two n-word integers $u=(u_{n-1}...u_0)_b$ and $v=(v_{n-1}...v_0)_b$ 
- output: (n+1)-word integer $w = (w_n...w_0)_b$ such that $w=u+v$, $w_n= 0 \text{ or } 1$ 

## pseudo code
```shell
k <- 0                           // k is the carry
for i = 0 to n-1 do
  wi <- (ui + vi + k) mod b      // 0 <= wi < b
  k <- floor[(ui + vi + k)/b]    // k = 0 or 1
wn <- k
return (wn...w0)b
```

## Rust implementation
RustCrypto/crypto-bigint/src/uint/add.rs
https://github.com/RustCrypto/crypto-bigint/blob/db9d587899c27d291e0a2d1fafe5bbc97c38b4ac/src/uint/add.rs#L10

```rust
impl<const LIMBS: usize> Uint<LIMBS> {
    /// Computes `a + b + carry`, returning the result along with the new carry.
    #[inline(always)]
    pub const fn adc(&self, rhs: &Self, mut carry: Limb) -> (Self, Limb) {
        let mut limbs = [Limb::ZERO; LIMBS];
        let mut i = 0;

        while i < LIMBS {
            let (w, c) = self.limbs[i].adc(rhs.limbs[i], carry);
            limbs[i] = w;
            carry = c;
            i += 1;
        }

        (Self { limbs }, carry)
    }
}
```

RustCrypto/crypto-bigint/src/primitives.rs
https://github.com/RustCrypto/crypto-bigint/blob/db9d587899c27d291e0a2d1fafe5bbc97c38b4ac/src/primitives.rs#L21C1-L30C2
```rust
#[inline(always)]
pub const fn adc(lhs: Word, rhs: Word, carry: Word) -> (Word, Word) {
    // We could use `Word::overflowing_add()` here analogous to `overflowing_add()`,
    // but this version seems to produce a slightly better assembly.
    let a = lhs as WideWord;
    let b = rhs as WideWord;
    let carry = carry as WideWord;
    let ret = a + b + carry;
    (ret as Word, (ret >> Word::BITS) as Word)
}
```

zkcrypto/ff/src/lib.rs
https://github.com/zkcrypto/ff/blob/b853db2c05a5901a8199012f80f5ee3784f52549/src/lib.rs#L485C1-L490C6
```rust
    /// Computes `a + b + carry`, returning the result and the new carry over.
    #[inline(always)]
    pub const fn adc(a: u64, b: u64, carry: u64) -> (u64, u64) {
        let ret = (a as u128) + (b as u128) + (carry as u128);
        (ret as u64, (ret >> 64) as u64)
    }
```

# Subtraction

Algorithm 10.5 Subtraction of non-negative multi-precision integers
- input: Two n-word integers $u=(u_{n-1}...u_0)_b$ and $v=(v_{n-1}...v_0)_b$  
- output: The n-word integer $w=(w_{n-1}..w_0)_b$ such that $w=u-v$ 
## pseudo code
```shell
k <- 0                        // k is the carry
for i = 0 to n-1 do
  wi <- (ui - vi + k) mod b   // 0 <= wi < b
  k <- floor[(ui - vi + k)/b] // k = 0 or -1
return (w{n-1}...w0)b         // if k = -1 then u < v
```

## Rust implementation
RustCrypto/crypto-bigint/src/uint/sub.rs
https://github.com/RustCrypto/crypto-bigint/blob/db9d587899c27d291e0a2d1fafe5bbc97c38b4ac/src/uint/sub.rs#L11
```rust
impl<const LIMBS: usize> Uint<LIMBS> {
    /// Computes `a - (b + borrow)`, returning the result along with the new borrow.
    #[inline(always)]
    pub const fn sbb(&self, rhs: &Self, mut borrow: Limb) -> (Self, Limb) {
        let mut limbs = [Limb::ZERO; LIMBS];
        let mut i = 0;

        while i < LIMBS {
            let (w, b) = self.limbs[i].sbb(rhs.limbs[i], borrow);
            limbs[i] = w;
            borrow = b;
            i += 1;
        }

        (Self { limbs }, borrow)
    }
}
```

RustCrypto/crypto-bigint/src/primitives.rs
https://github.com/RustCrypto/crypto-bigint/blob/db9d587899c27d291e0a2d1fafe5bbc97c38b4ac/src/primitives.rs#L39C1-L47C2
```rust
/// Computes `self - (rhs + borrow)`, returning the result along with the new borrow.
#[inline(always)]
pub const fn sbb(lhs: Word, rhs: Word, borrow: Word) -> (Word, Word) {
    let a = lhs as WideWord;
    let b = rhs as WideWord;
    let borrow = (borrow >> (Word::BITS - 1)) as WideWord;
    let ret = a.wrapping_sub(b + borrow);
    (ret as Word, (ret >> Word::BITS) as Word)
}
```

zkcrypto/ff/src/lib.rs
https://github.com/zkcrypto/ff/blob/b853db2c05a5901a8199012f80f5ee3784f52549/src/lib.rs#L478C5-L483C6
```rust
/// Computes `a - (b + borrow)`, returning the result and the new borrow.
    #[inline(always)]
    pub const fn sbb(a: u64, b: u64, borrow: u64) -> (u64, u64) {
        let ret = (a as u128).wrapping_sub((b as u128) + ((borrow >> 63) as u128));
        (ret as u64, (ret >> 64) as u64)
    }
```

# Multiplication

The complexity of a multiplication algorithm is important  for a complete arithmetic system.
- the efficiency of division algorithms depends to a large extent on the speed of the multiplication

$M(n)$: the number of elementary operations necessary to multiply two n-word integers

Algorithms
- Schoolbook multiplication
- Karatsuba multiplication
- Other multiplication algorithms, like Toom–Cook or Fast Fourier Transform methods [K NU 1997] based on interpolation are asymptotically more efficient, but the gain occurs only for very large n, out of the range of the sizes used nowadays for cryptosystems based on elliptic and hyperelliptic curves.

## Schoolbook multiplication

$M(n) = O(n^2)$, as the number of elementary multiplications is $nm$ 

Algorithm 10.8 Multiplication of positive multiprecision integers
- input: An m-word integers $u=(u_{m-1}...u_0)_b$ and an n-word integer $v=(v_{n-1}...v_0)_b$  
- output: The (m+n)-word integer $w=(w_{m+n-1}..w_0)_b$ such that $w=uv$ 


### pseudo code
```shell
for i = 0 to n-1 do wi <- 0
for i = 0 to n-1 do
  k <- 0                          // k is the carry
  if vi = 0 then w{m+i} <- 0      // optional test
    else
    for j = 0 to m-1 do
      t <- vi * ui + w{i+j} + k   // 0 <= t < b^2
      w{i+j} <- t mod b           // 0 <= w{i+j} < b
      k <- floor[t/b]             // 0 <= t < b
    w{m+i} <- k
return (w{m+n-1}...w0)b
```


### Rust implementation

RustCrypto/crypto-bigint/src/uint/mul.rs
https://github.com/RustCrypto/crypto-bigint/blob/db9d587899c27d291e0a2d1fafe5bbc97c38b4ac/src/uint/mul.rs#L12C1-L52C2
```rust
/// Implement the core schoolbook multiplication algorithm.
///
/// This is implemented as a macro to abstract over `const fn` and boxed use cases, since the latter
/// needs mutable references and thus the unstable `const_mut_refs` feature (rust-lang/rust#57349).
///
/// It allows us to have a single place (this module) to improve the multiplication implementation
/// which will also be reused for `BoxedUint`.
// TODO(tarcieri): change this into a `const fn` when `const_mut_refs` is stable
macro_rules! impl_schoolbook_multiplication {
    ($lhs:expr, $rhs:expr, $lo:expr, $hi:expr) => {{
        if $lhs.len() != $lo.len() || $rhs.len() != $hi.len() {
            panic!("schoolbook multiplication length mismatch");
        }

        let mut i = 0;
        while i < $lhs.len() {
            let mut j = 0;
            let mut carry = Limb::ZERO;
            let xi = $lhs[i];

            while j < $rhs.len() {
                let k = i + j;

                if k >= $lhs.len() {
                    ($hi[k - $lhs.len()], carry) = $hi[k - $lhs.len()].mac(xi, $rhs[j], carry);
                } else {
                    ($lo[k], carry) = $lo[k].mac(xi, $rhs[j], carry);
                }

                j += 1;
            }

            if i + j >= $lhs.len() {
                $hi[i + j - $lhs.len()] = carry;
            } else {
                $lo[i + j] = carry;
            }
            i += 1;
        }
    }};
}
```

RustCrypto/crypto-bigint/src/primitives.rs
https://github.com/RustCrypto/crypto-bigint/blob/db9d587899c27d291e0a2d1fafe5bbc97c38b4ac/src/primitives.rs#L58C1-L67C2
```rust
/// Computes `a + (b * c) + carry`, returning the result along with the new carry.
#[inline(always)]
pub(crate) const fn mac(a: Word, b: Word, c: Word, carry: Word) -> (Word, Word) {
    let a = a as WideWord;
    let b = b as WideWord;
    let c = c as WideWord;
    let carry = carry as WideWord;
    let ret = a + (b * c) + carry;
    (ret as Word, (ret >> Word::BITS) as Word)
}
```

zkcrypto/ff/src/lib.rs
https://github.com/zkcrypto/ff/blob/b853db2c05a5901a8199012f80f5ee3784f52549/src/lib.rs#L492C1-L497C6
```rust
    /// Computes `a + (b * c) + carry`, returning the result and the new carry over.
    #[inline(always)]
    pub const fn mac(a: u64, b: u64, c: u64, carry: u64) -> (u64, u64) {
        let ret = (a as u128) + ((b as u128) * (c as u128)) + (carry as u128);
        (ret as u64, (ret >> 64) as u64)
    }
```

## Karatsuba multiplication

$R = b^n$, $d = 2n$

$u=(u_{d-1}...u_0)_b$, $v=(v_{d-1}...v_0)_b$ ($u$, $v$ are 2 d-word integers) 

Split both $u$ and $v$ to the least and most significant parts
- $u = U_1R + U_0$
- $v = V_1R + V_0$

$uv = U_1V_1R^2 + ((U_0 + U_1)(V_0+V_1) - U_1V_1 - U_0V_0)R + U_0V_0$
- multiplication by $R$ is bits shift
- 3 multiplications
- some additions

Algorithm 10.11 Karatsuba multiplication of positive multiprecision integers
- input: 
	- $u=(u_{m-1}...u_0)_b$ , an m-word
	- $v=(v_{n-1}...v_0)_b$  , an n-word
	- $d=\max(m, n)$, 
	- $d_0$ a threshold depending essentially on the processor used
- output: The (m+n)-word integer $w=(w_{m+n-1}..w_0)_b$ such that $w=uv$ 

> In practice, the schoolbook multiplication is faster when $d$ becomes smaller than a threshold $d_0$ , depending essentially on the processor used. Granlund performed tests with GMP on several architectures to determine the optimal value of $d_0$ . Results spread from $8$ up to more than $100$ [G RA 2004, GMP].

> The number of elementary operations required by Algorithm 10.11 to multiply two n-word integers shall be denoted by $K(n)$. As $K(n) \le 3 K(n/2) + cn/2$  for some constant $c$, one finds by induction that $K(n) = O(n^{\log_2(3)} ) \approx O(n^{1.585})$ .
### pseudo code
```shell
if d <= d0 then return uv         // use schoolbook multiplication
p <- floor[d/2] and q <- ceil[d/2]
U0 <- (u{q-1}...u0)b   and V0 <- (v{q-1}...v0)b
U1 <- (u{p+q-1}...uq)b and V1 <- (v{p+q-1}...vq)b
Us <- U0 + U1          and Vs <- V0 + V1
compute recursively U0*V0, U1*V1, and Us*Vs
return U1*V1*b^{2q} + ((Us*Vs - U1*V1 - U0*V0))*b^q + U0*V0
```

### Rust implementation
`RustCrypto/crypto-bigint/src/uint/mul/karatsuba.rs`
https://github.com/RustCrypto/crypto-bigint/blob/db9d587899c27d291e0a2d1fafe5bbc97c38b4ac/src/uint/mul/karatsuba.rs#L174C1-L176C35

# Squaring


## Schoolbook squaring

$M(n)=O(n^2)$, the number of elementary multiplications is $(n^2 + n)/2$ (against $n^2$ for general algorithm)
- In practice, 20% faster than the general algorithm (the standard multiplication $u * u$)
$$
(\sum_{i=0}^{n-1} u_ib^i)^2 = \sum_{i=0}^{n-1} u_i^2b^{2i} + 2\sum_{i<j} u_i u_j b^{i+j}
$$

Algorithm 10.14 Squaring of a positive multiprecision integer
- input: an n-word integer $u=(u_{n-1}...u_0)_b$  
- output: The (2n)-word integer $w=(w_{2n-1}..w_0)_b$ such that $w=u^2$ 

### pseudo code

```shell
for i=0 to 2n-1 do wi <- 0
for i=0 to n-1 do
  t <- ui^2 + w{2i}
  w{wi} <- t mod b and k <- floor[t/b]
  for j=i+1 to n-1 do
    t <- 2*ui*uj + w{i+j} + k
    w{i+j} <- t mod b and k <- floor[t/b]
  w{i+n} <- k
return (w{2n-1}...w0)b
```

### Rust implementation
- RustCrypto/crypto-bigint/src/uint/mul.rs
- https://github.com/RustCrypto/crypto-bigint/blob/db9d587899c27d291e0a2d1fafe5bbc97c38b4ac/src/uint/mul.rs#L54C1-L131C2

## Karatsuba squaring

### Rust implementation
- `RustCrypto/crypto-bigint/src/uint/mul/karatsuba.rs`
- https://github.com/RustCrypto/crypto-bigint/blob/db9d587899c27d291e0a2d1fafe5bbc97c38b4ac/src/uint/mul/karatsuba.rs#L116C1-L172C2


# Modular reduction

Reduce a number modulo an integer N. 
- In practice N will often be prime, and the corresponding reduction is an essential operation for prime field arithmetic

## Barrett method

$N$: be an n-word integer
$R(N) = \lfloor b^{2n}/N \rfloor$ : the reciprocal integer of N
$u$: 2n-word integer
$$
\begin{align*}
 q & = \LARGE \lfloor \frac{u}{N} \rfloor \newline
   & = \LARGE \lfloor \frac{u}{N} \frac{b^{2n}}{b^{n+1}b^{n-1}} \rfloor \newline
   & = \LARGE \lfloor \frac{u}{b^{n-1}} \frac{b^{2n}}{N} \frac{1}{b^{n+1}} \rfloor \newline
   & = \LARGE  \lfloor \dfrac{\frac{u}{b^{n-1}} \frac{b^{2n}}{N}}{b^{n+1}} \rfloor
\end{align*}
$$
which can be approximated by 
$$
\LARGE \widehat{q} =  \lfloor \dfrac{\lfloor \frac{u}{b^{n-1}} \rfloor  R}{b^{n+1}} \rfloor
$$
In addition $q-2 \le \widehat{q} \le q$
- $\widehat{q} = q$ ~ 90%
- $\widehat{q} = q-2$ ~ 1%

So the approximation of modular reduction is $\widehat{u}=u - \widehat{q}N$ 
If $\widehat{u} > N$ need at most 2 subtrucations by N 

### Modular reduction - remainder 

Algorithm 10.17 Division-free modulo of positive multiprecision integers
- input: 
	- 2n-word integer $u=(u_{2n-1}...u_0)_b$ 
	- n-word integer $N = (N_{n-1}...N_0)_b$ , $N_{n-1} \ne 0$ 
	- $R = \lfloor b^{2b} / N \rfloor$ is pre-computed
- output: The n-word integer $r = (r_{n-1}...r_0)_b$ such that $u \equiv r (\mod N)$ 

#### pseudo code

```shell
q_hat <- floor[floor[u / b^{n-1}] * R / b^{n+1}]  // q-2 <= q_hat <= q

r1 <- u mod b^{n+1}, r2 <- (q_hat * N) mod b^{n+1}
r <- r1 - r2

if r < 0 then r <- r + b^{n+1}
while r >= N do r <- r - N

return r
```

### R: the reciprocal integer of N

R is pre-computed, thus the performance is not crucial
$$
\begin{align*}
\LARGE R = 2R - \frac{R^2}{R} \leftarrow  & \LARGE 2R -  \frac{R^2}{\lfloor b^{2n}/N \rfloor}  = 2R - \lfloor \frac{NR^2}{b^{2n}} \rfloor \newline
\LARGE R \leftarrow & \LARGE 2R - \lfloor \frac{N \lfloor \frac{R^2}{b^n} \rfloor}{b^n}  \rfloor
\end{align*}
$$
- init: $R \leftarrow b^n$ 
- $N < b^n$ 

Algorithm 10.18 Reciprocation of positive multiprecision integers (inspired by the Newton method)
- input: An n-word integer $N = (N_{n-1}...N_0)_b$
- output: The (n+2)-word integer $R = \lfloor b^{2n} / N \rfloor$
#### pseudo code

```shell
R <- b^n

repeat
  s <- R
  R <- 2 * R - floor[N * floor[R^2 / b^n] / b^n] # discrete Newton iteration
until R <= s

t <- b^{2n} - NR
while t < 0 do R <- R - 1 and t <- t + N # performed at most twice
return R
```

Algorithm 10.18 Reciprocation of positive multiprecision integers

### Newton method

For real numbers $N$, the inverse of $N$ is often computed with _Newton method_:
$$
\LARGE x \leftarrow x - x(Nx - 1)
$$




## Montgomery reduction

[Montgomery reduction](/posts/montgomery-reduction/)

*Definition 10.21* Let $R$ be some integer greater than $N$ and co-prime with it. The Montgomery representation of $x \in [0, N-1]$ is $[x] = (xR) \mod N$. The Montgomery reduction of $u \in [0, RN-1]$ is $Redc(u) = (uR^{-1}) \mod N$.


When $R$ is a power of the radix $b$ there is an efficient algorithm to perform the reduction of u.
- let $N' = (-N^{-1}) \mod R$
- let unique integer $k \in [0, N-1]$
	- such that $k \equiv uN' \ (mod \ R)$ 
	- Then clearly $(u + kN)$ is a multiple of $R$
- Let $t = (u + kN) / R$ 
	- As $N$ and $R$ are relatively prime, 
	- this implies that $t \equiv uR^{-1} (\mod N)$
- Finally, $0 \le u < RN$ by assumption and it can be easily shown that $0 \le t < 2N$ 
	- so that: $Redc(u)$ = $t$ or $t - N$

Why $k \equiv uN' (\mod R)$ $\implies$  $(u + kN)$  is a multiple of $R$?
$$
\begin{align*}
k & \equiv uN' \mod R  \newline
xR + k &= -u/N \newline
xRN + kN &= -u \newline
u + kN &= (-xN)R
\end{align*}
$$

Why $t = (u + kN)/R$ $\implies$ $t \equiv uR^{-1} \ (mod \ N)$?
$$
\begin{align*}
t &= (u + kN)/R \newline
t &= uR^{-1} + kNR^{-1} \newline \newline
& (uR^{-1} + kNR^{-1}) \mod N \newline
& = (uR^{-1} \mod N) + (kR^{-1}N \mod N) \newline
& = uR^{-1} \mod N
\end{align*}
$$


Algorithm 10.22 Montgomery reduction Redc of multiprecision integers
- input
	- An n-word integer $N = (N_{n-1}...N_0)b$
		- such that $gcd(N,b) = 1$
	- $R = b^n$
	- $N' = (-N^{-1}) \mod b$
	- A 2n-word integer $u = (u_{2n-1}...u_0)_b < RN$
- Output: The n-word integer $t = (t_{n-1}...t_0)_b$ 
	- such that $t = Redc(u) = (uR^{-1}) \mod N$ 

### pseudo code






$$
\begin{flalign*}
& (t_{2n-1} ... t_0 )_{b}  \leftarrow ( u_{2n-1} ... t_0)_b & \newline
& for \ \ i=0 \ \ to \ \  n-1 \ \ do & \newline
& \ \ \ \ k_i \leftarrow (t_iN') \ mod \ b & \newline
& \ \ \ \ t \leftarrow t + k_iNb^i & \newline
& t \leftarrow t / R & \newline
& if \ \ t \ge N \ \ then \ \ t \leftarrow t - N & \newline
& return \ t
\end{flalign*}
$$

test 



