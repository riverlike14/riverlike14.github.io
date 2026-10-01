---
title: "Delta Hedge Development Log"
date: 2026-09-28 06:40:00+09:00
categories: [Trading]
tags: [options, delta hedge] # TAG names should always be lowercase
math: true
---

## Concepts

### Implementation Design

1. Choose an exchange and fetch all the available options from it
1. For each underlying asset
   1. Rebalance current options positions
   1. Look for new mispriced options

### Rebalance

- If the position's delta is not close to zero (over the threshold), then hedge the position by trading futures.

### Volatility Trading

- Calculate options value with realized volatility.
- Trade options
  - If bid > value + margin, sell the options.
  - If ask < value - margin, buy the options.

## Working Log

| Date       | Task          | Details                                             |
| ---------- | ------------- | --------------------------------------------------- |
| 2026-09-30 | Minor updates | Remove hard-coded values, update configuration file |
| 2026-09-28 | Minor updates | Rename some variables                               |
