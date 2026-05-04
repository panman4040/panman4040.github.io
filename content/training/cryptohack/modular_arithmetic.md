+++
date = '2026-05-04T11:58:16+08:00'
draft = false
title = 'Modular Arithmetic'
description = 'Key notes I have summarised for this section, quite math-heavy (notes: need to review periodically to train my brain muscle as the maths for these problems are quite intuitive)'
showLastUpdated = true
+++
# Modular Arithmetic

This module (no pun intended) introduces modular arithmetic, which is a critical mathematical edge for cryptography. Here are my important remarks:

## Euclidean Algorithm
This is a very famous algorithm designed to calculate the GCD of two integers. I initially struggled trying to understand the intuition, but the following analogy helps:

You are a carpenter. You have two wooden planks of different lengths, say Plank A is 105 inches long, Plank B 24. Our goal is to find a "ruler" that measures perfectly both plank from end-to-end.

You first lay Plank B alongside Plank A as many times as see fits:

$$
105 = 24 \times 4 + 9 \text{ (the remainder is $9$)}
$$

Because our "ruler" measures perfectly both plank, it must also measure the gap (which is our remainder) perfectly as well. We therefore can ditch plank A, and measure our newly created 9-inch plank with Plank B. We do this recursively so on and so forth until we reach 0, meaning the previous length we used perfectly fits all. This is our GCD.

This leaves us with a general formula:

$$
r_{i - 2} = r_{i - 1} \cdot q_{i - 1} + r_i
$$

where $r_0 = a$ and $r_1 = b$. The reason why such formula came to be is because we divide the previous two remainders at each step.
### Bezout's Identity and Extended Euclidean Algorithm
Bezout's Identity states that the GCD $g$ of any two integers $a$ and $b$ can be expressed as a linear sum of $a$ and $b$.

$$
a \cdot s + b \cdot t = g
$$

Rearranging the Euclidean Algorithm formula, we get:

$$
r_i = r_{i - 2} - r_{i - 1} \cdot q_{i - 1}
$$

We want to prove that at *every* step $i$, we can write the remainder $r_i$ in the form of Bézout's identity:

$$r_i = a \cdot s_i + b \cdot t_i$$

Let's first look at the base case: The "remainder" is just our first number, $a$.

$$r_0 = a \cdot 1 + b \cdot 0$$

Therefore, our starting coefficients are $s_0 = 1$ and $t_0 = 0$. Similarly for our second number, $s_1 = 0$ and $t_1 = 1$.

Now, let's assume we have successfully written the previous two remainders, $r_{i-2}$ and $r_{i-1}$, as combinations of $a$ and $b$:
1.  $$r_{i-2} = a \cdot s_{i-2} + b \cdot t_{i-2}$$
2.  $$r_{i-1} = a \cdot s_{i-1} + b \cdot t_{i-1}$$

We then substitute this combination into the remainder formula:
$$r_i = (a \cdot s_{i-2} + b \cdot t_{i-2}) - (a \cdot s_{i-1} + b \cdot t_{i-1}) \cdot q_{i-1}$$

$$r_i = a \cdot (s_{i-2} - s_{i-1} \cdot q_{i-1}) + b \cdot (t_{i-2} - t_{i-1} \cdot q_{i-1})$$

with $s_i = s_{i-2} - q_{i-1} \cdot s_{i-1}$ and $t_i = t_{i-2} - q_{i-1} \cdot t_{i-1}$. This is the Extended Euclidean Algorithm.

(note: I want to understand the intuition behind these algorithms rather than memorising only, so this part may feel a bit too long for comfort)

## Finite Fields and Rings
Imagine we've picked a modulus $p$, where $p$ is prime. The integers modulo $p$ define a field, denoted $\mathbb{F}_p$. Within $\mathbb{F}_p$, every number is prime to the modulus. This means all four basic operations work as normal.

Whereas if $p$ is not prime, it is called a \textbf{ring}. In a ring, division is broken. For example, in modulo 6 math, you cannot divide by 2 or 3 because they share divisors with 6.

Note that while addition, subtraction, and multiplication works exactly as you thought it would do, division is multiplying by an inverse, that is every non-zero number must have an inverse that multiplies with it to make 1 (congruent to 1).

A finite field $\mathbb{F}_p$ is the set of integers $0, 1, \dots, p - 1$, and under both addition and multiplication there are inverse elements $b_+$ and $b_*$ for every element $a$ in the set, such that $a + b_+ = 0$ and $a \cdot b_* = 1$.

## Fermat's little theorem
It's best to remember this as it will soon be needed for RSA cryptography.

Fermat's little theorem states that if $p$ is a prime number, then for any integer $a$, the number $a^p - a$ is an integer multiple of $p$:

$$a^p \equiv a \pmod p.$$

If $a$ is not divisible by $p$; that is, if $a$ is coprime to $p$, then Fermat's little theorem is equivalent to the statement that $a^{p-1} - 1$ is an integer multiple of $p$, or in symbols:

$$a^{p-1} \equiv 1 \pmod p.$$

### Finding inverse
For all elements $g$ in the field, there exists a unique integer $d$ such that $g \cdot d \equiv 1 \pmod p.$ 

This is the multiplicative inverse of $g$.

This is a direct application of Fermat's little theorem. To find $d$, we simply need to find $g^{p-2}$ modulo $p$ and that's our $d$.

## Quadratic Residue
We say that an integer $x$ is a Quadratic Residue if there exists an $a$ such that $a^2 \equiv x \pmod p$. If there is no such solution, then the integer is a Quadratic Non-Residue.

If $x$ is a Quadratic Residue, we say that the square root of $x$ is $a$.

For the elements of $\mathbb{F}_p^*$, not every element has a square root. In fact, roughly one half of the elements of $\mathbb{F}_p^*$, there is no square root.

Interesting properties of quadratic (non-)residues:

- Quadratic Residue * Quadratic Residue = Quadratic Residue

- Quadratic Residue * Quadratic Non-residue = Quadratic Non-residue

- Quadratic Non-residue * Quadratic Non-residue = Quadratic Residue

### Legendre Symbol
The Legendre Symbol gives an efficient way to determine whether an integer is a quadratic residue modulo an odd prime $p$.

Legendre's Symbol: $(a/p) \equiv a^{(p-1)/2} \pmod p$ obeys:

- $(a/p) = 1$ if $a$ is a quadratic residue and $a \not\equiv 0 \pmod p$

- $(a/p) = -1$ if $a$ is a quadratic non-residue $\pmod p$

- $(a/p) = 0$ if $a \equiv 0 \pmod p$

Which means given any integer $a$, calculating $a^{(p-1)/2} \pmod p$ is enough to determine if $a$ is a quadratic residue.

To find the square roots themselves, this problem is a great example: https://cryptohack.org/courses/modular/root1/. This requires some clever application of Fermat's little theorem, and the fact that p mod 4 = 3 to solve (should review this periodically).

What if p mod 4 = 1? This problem (https://cryptohack.org/courses/modular/tonelli-shanks/) introduces formally the Tonelli-Shanks algorithm, which is quite complex. Thus, we are advised to use Sage built-in function. 

## Chinese Remainder Theorem
In cryptography, we commonly use the Chinese Remainder Theorem to help us reduce a problem of very large integers into a set of several, easier problems.

Given a set of arbitrary integers $a_i$, and pairwise coprime integers $n_i$, such that the following linear congruences hold:

$$x \equiv a_1 \pmod{n_1}$$
$$x \equiv a_2 \pmod{n_2}$$
$$\dots$$
$$x \equiv a_n \pmod{n_n}$$

There is a unique solution $x \equiv a \pmod N$ where $N = n_1 \cdot n_2 \cdot \dots \cdot n_n$.

To find the solution for $a$, this article is a great read: https://cp-algorithms.com/algebra/chinese-remainder-theorem.html

## Final Problems (must review)
### Adrien's Signs
https://cryptohack.org/courses/modular/adrien/

I will not go into details on how to solve this, but the approach to this is novel (to me at least). There is something special hidden within a and p itself, and I should take note of these small details in future CTFs rather than diving into the maths and hitting deadends immediately.

### Modular Binomials
https://cryptohack.org/courses/modular/bionomials/

This problem requires intuitive rearrangement of the c1 and c2 equations (hint: use modulo q rather than N), and GCD findings. I had much trouble solving this and had to relegate to editorials, need to brush up on my maths asap