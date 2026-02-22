---
title: "Black Scholes Equation"
date: 2026-02-01 12:00:00+09:00
tags: [black scholes equation] # TAG names should always be lowercase
math: true
---

# Statement

$$
\frac{\partial V}{\partial t} + r S \frac{\partial V}{\partial S} + \frac{1}{2} \sigma^2 S^2 \frac{\partial^2 V}{\partial S^2} - rV = 0.
$$

# Assumptions

## Stock price dynamics

The underlying stock follows a geometric Brownian motion:

$$
dS = \mu S dt + \sigma S dW.
$$

## Self-financing portfolio

Consider a self-financing portfolio $\Pi$ consisting of

- a long position in one derivative whose value is $V = V(S_t, t)$, and
- a short position in $\Delta = \frac{\partial V}{\partial S}$ shares of the stock.

The portfolio value is

$$
\Pi = V - \Delta S.
$$

## Ito's lemma

Applying Ito's lemma to $V(S,t)$, we have

$$
\begin{align*}
d\Pi &= dV - \frac{\partial V}{\partial S}dS\\
&= \left( \frac{\partial V}{\partial t} + \frac{1}{2} \sigma^2 S^2 \frac{\partial^2 V}{\partial S^2} \right) dt.
\end{align*}
$$

Therefore the portfolio is risk-free.

## No-arbitrage condition

The portfolio must earn the risk-free rate $r$:

$$
d\Pi = r\Pi dt.
$$

Substituting $\Pi = V - \frac{\partial V}{\partial S} S$ yields the Black-Scholes equation.
