---
title: "Radon Nikodym Derivative"
date: 2026-02-08 12:00:00+09:00
tags: [radon nikodym derivative] # TAG names should always be lowercase
math: true
---

# Theorem

Let $\lambda$ and $\mu$ be positive finite measures on $\mathfrak{M}$.

- There exists a unique decomposition of positive finite measures $$\lambda = \lambda_a + \lambda_s$$ where $\lambda_a \ll \mu$ and $\lambda_s \perp \mu$.

- There exists a unique non-negative function $h \in L^1(\mu)$ such that

  $$\lambda_a(E) = \int_E h\, d\mu.$$

# Proof

## Uniqueness

If $(\lambda_a',\ \lambda_s')$ is another such decomposition, then $\lambda_a - \lambda_a' = \lambda_s' - \lambda_s$ is both absolutely continuous and singular with respect to $\mu$, hence it must be the zero measure.

## Riesz representation theorem

Let $\varphi = \lambda + \mu$.

- Observe that the mapping $T: L^2(\varphi) \to \mathbb{R}$ defined by $T(f) = \int_X f\, d\lambda$ is a bounded linear functional on the Hilbert space $L^2(\varphi)$.
- By the Riesz representation theorem, there exists a unique $g \in L^2(\varphi)$ such that

  $$T(f) = \int_X f g\, d\varphi$$

  for all $f \in L^2(\varphi)$.

- Since $\int_E g\, d\varphi = \lambda(E) \leq \varphi(E)$ for all $E \in \mathfrak{M}$, we have $0 \leq g \leq 1$ almost everywhere with respect to $\varphi$.

## Decomposition of $\lambda$

Let $$A = \{ x \in X : 0 \leq g(x) < 1 \}$$.

- Define measures $\lambda_a$ and $\lambda_s$ by $\lambda_a(E) = \lambda(E \cap A)$ and $\lambda_s(E) = \lambda(E \cap A^c)$.
- Then $\lambda = \lambda_a + \lambda_s$.

## Absolute continuity of $\lambda_a$

Note that $\int f(1 - g)\, d\lambda = \int fg\, d\mu$ for all $f \in L^2(\varphi)$.

- Take $f = (1 + g + \cdots + g^{n-1}) \chi_E$.
- The monotone convergence theorem implies that $$\lambda_a(E) = \int_E h\, d\mu$$, where $h = \frac{g}{1 - g} \chi_A \in L^1(\mu)$.

## Singularity of $\lambda_s$

- $\lambda_s \perp \mu$ since $\mu(A^c) = 0$.
