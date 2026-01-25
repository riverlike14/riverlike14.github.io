---
title: "Stirling Approximation"
date: 2026-01-25 14:00:00+09:00
tags: [stirling approximation] # TAG names should always be lowercase
math: true
---

# Statement

$$
n! \sim \sqrt{2 \pi n} \left( \frac{n}{e} \right)^{n}
$$

# Proof

## Asymptotic behavior of $\log (n!)$

1. Using monotonicity of $$\log x$$, we have

   $$
   \sum_{k=1}^{n-1} \log k \leq \int_1^n \log x \, dx \leq \sum_{k=2}^{n} \log k.
   $$

   Evaluating the integral,

   $$
   n \log n - n + 1 \leq \log (n!) \leq (n + 1) \log n - n + 1.
   $$

2. Define $d_n = \log (n!) - ((n + \frac{1}{2} ) \log n - n + 1)$.

   Then

   $$
   \begin{align*}
   d_n - d_{n+1} &= \left(\left( n + \frac{1}{2} \right) \log \left( 1 + \frac{1}{n} \right) + \log(n + 1) - 1\right) - \log(n + 1)\newline
   &= \left( n + \frac{1}{2} \right) \log \left( \frac{1 + \frac{1}{2n + 1}}{1 - \frac{1}{2n + 1}} \right) - 1 \newline
   &= \left( n + \frac{1}{2} \right) \left( 2 \left( \frac{1}{2n + 1} + \frac{1}{3 (2n + 1)^3} + \frac{1}{5 (2n + 1)^5} + \cdots \right) \right) - 1 \newline
   &= \frac{1}{3 (2n + 1)^2} + \frac{1}{5 (2n + 1)^4} + \cdots .\newline
   \end{align*}
   $$

3. The right-hand side is positive and bounded by $\sum_k \frac{1}{3(2n + 1)^{2k}} = \frac{1}{12n(n+1)}$. Hence,
   - $$\{d_n\}$$ is decreating,
   - $$\left\{d_n - \frac{1}{12 n}\right\}$$ is increasing.

   Therefore the sequence $$\{ d_n \}$$ converges to some constant $d$, and

   $$
   n! \sim e^{d+1} \sqrt{n} \left(\frac{n}{e} \right)^n.
   $$

## Wallis's formula

1. Wallis's formula gives

   $$
   \lim_{n \to \infty} \frac{1}{\sqrt{n}} \frac{(2n)!!}{(2n - 1)!!} = \sqrt{\pi}.
   $$

2. Using $$(2n)!! = 2^n n!$$ and $$ (2n-1)!! = \frac{(2n)!}{2^n n!} $$,

   $$
   \begin{align*}
   \sqrt{\pi} &= \lim_{n \to \infty} \frac{1}{\sqrt{n}} \frac{(2^n n!)^2}{(2n)!} \newline
   &= \lim_{n \to \infty} \frac{1}{\sqrt{n}} \frac{\left( 2^n e^{d+1} \sqrt{n} \left( \frac{n}{e} \right)^n \right)^2}{e^{d+1} \sqrt{2n} \left( \frac{2n}{e} \right)^{2n}} \newline
   &= \frac{e^{d+1}}{\sqrt{2}}.
   \end{align*}
   $$

   Thus $e^{d+1} = \sqrt{2 \pi}$, and

   $$
   n! \sim \sqrt{2 \pi n} \left( \frac{n}{e} \right)^{n}.
   $$
