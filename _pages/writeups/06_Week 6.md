---
title: "06 Week 6"
permalink: /reports/vcg/06-week-6/
math: true
---

# Context

As per the last discussion, I decided to spend some time reading about Knapsack auctions and see if it can be useful. I prepared a proof of concept and simplified the model heavily. I also run a VCG auction with it as a baseline to try and test it.

In parallel, I also read about EV charging allocation using VCG auctions. I recently submitted my Discrete Event & Hybrid Systems (SE 733) project and that was on something similar but using an Approximate Dynamic Programming approach. So I will be using the framework built in that to further test this theory.

# Introduction

## What is a knapsack auction?

A knapsack auction is an auction where the auctioneer has a limited capacity and must choose a subset of bids that maximize total value.

Example: suppose a satellite has $W = 100\ \text{MHz}$ available.

| Bidder | Requested Bandwidth | Bid |
| ------ | ------------------- | ----- |
| A | 50 MHz | \$100 |
| B | 40 MHz | \$90 |
| C | 30 MHz | \$70 |
| D | 20 MHz | \$40 |

The possible allocations are:

- A + B: bandwidth 50 + 40 = 90 MHz, value \$100 + \$90 = \$190
- B + C + D: bandwidth 40 + 30 + 20 = 90 MHz, value \$90 + \$70 + \$40 = \$200
- A + C + D: bandwidth 50 + 30 + 20 = 100 MHz, value \$100 + \$70 + \$40 = \$210

The best allocation is A + C + D, so the winners are $\{A, C, D\}$ and total welfare is \$210.

The winner determination problem is literally

<div>
$$
\max_{x_i \in \{0,1\}} \sum_i v_i x_i \qquad \text{s.t.} \qquad \sum_i a_i x_i \le W
$$
</div>

where $v_i$ is the bid value, $a_i$ is the requested resource, and $W$ is the capacity. This is the 0-1 knapsack problem.

# Setup

We model access to one section of 5G spectrum sold as a single shared resource. There is one spectrum owner, the auctioneer, who has a fixed amount of bandwidth to allocate, and a set of active users (telecom bidders) who compete for it.

Let $N = \{1, 2, \dots, n\}$ be the set of bidders and let $W$ be the total available bandwidth. Each bidder $i$ requests an amount of bandwidth $a_i$ and submits a private value $v_i$ for being served. Let $x_i$ be the decision variable where

<div>
$$
x_i = \begin{cases} 1, & \text{if bidder } i \text{ wins} \\ 0, & \text{otherwise} \end{cases}
$$
</div>

The auctioneer chooses winners to maximize total reported value,

<div>
$$
\max_{x_i \in \{0,1\}} \sum_i v_i x_i \qquad \text{s.t.} \qquad \sum_i a_i x_i \le W
$$
</div>

This is the 0-1 knapsack problem.

## From a single capacity to a block grid

The model above collapses each bidder's request into one number $a_i$ and asks only whether the total fits in $W$. A more realistic model keeps the individual blocks. Let $M = \{1, 2, \dots, m\}$ be the set of time-frequency blocks that make up the band, indexed by $j$, and let each bidder $i$ request a specific bundle $B_i \subseteq M$. Each block can be sold to at most one bidder, so the single capacity constraint becomes one constraint per block $j$:

<div>
$$
\max_{x_i \in \{0,1\}} \sum_i v_i x_i \qquad \text{s.t.} \qquad \sum_{i:\, j \in B_i} x_i \le 1 \quad \forall\, j \in M
$$
</div>

This is no longer a pure 0-1 knapsack but a combinatorial auction over named blocks, where the winner-determination problem is weighted set packing.

## Combinatorial VCG auction on the grid

We run this on a fixed instance: a $3 \times 4$ grid (3 frequency channels, 4 time slots, so $m = 12$ blocks) and $n = 6$ bidders, each requesting a bundle. The code solves the winner-determination problem exactly by searching all subsets of bidders and keeping the highest-value subset with no overlapping bundles. The optimum has welfare $W^{*} = 41$, winners $\{P, R, S\}$ and losers $\{Q, T, V\}$.

We then compute VCG payments. Each winner pays the externality it imposes, the welfare the others lose because it is present. With $W^{*}_{-i}$ the optimal welfare without bidder $i$,

<div>
$$
p_i = W_{-i}^* - \left(W^{*} - v_i\right)
$$
</div>

Here $p_P = 10$, $p_R = 9$, $p_S = 14$ (revenue $33$), with utilities $u_P = 2$, $u_R = 2$, $u_S = 4$. Every utility is non-negative, so no winner overpays (individual rationality), and since a bidder's payment does not depend on its own bid, truthful bidding is a dominant strategy (DSIC).

## Does block granularity matter?

The grid raises a question the capacity model hides: how large should a block be? Coarse blocks force bidders to round demand up to whole blocks; fine blocks reduce that rounding but add more guard bands between blocks. Both waste spectrum. We fix one $100$ MHz section of the $36.5$ GHz EESS-passive band and sweep the block size $g$.

At granularity $g$, bidder $i$ needs $k_i = \lceil a_i / g \rceil$ blocks, and the band holds $m = \lfloor W / (g + \delta) \rfloor$ usable blocks, where $\delta$ is the guard band per block. The auction is a knapsack in block units:

<div>
$$
W^{*}(g) = \max_{x_i \in \{0,1\}} \sum_i v_i x_i \qquad \text{s.t.} \qquad \sum_i k_i\, x_i \le m
$$
</div>

Sweeping $g$ from $2$ to $50$ MHz, welfare is low at both extremes and peaks in between, at $g = 9$ MHz with welfare $182$. Block size is not a neutral choice: there is a best granularity, and the grid itself is a design parameter.

## Where this points

VCG is only DSIC when the allocation is exactly optimal, which is exponential here, so a natural next step is approximate allocation rules that stay strategyproof. And if granularity matters, the auction could choose $g$ from the bids rather than fixing it, which would require making the grid-selection rule truthful as well. Both feed into extending this static baseline toward the dynamic (ADP) setting.

# Upcoming Plans

I plan to study and see if we can incorporate a more approximate dynamic programming framework, where bidders and spectrum availability keep changing over time with allocation and payment decisions made online. I also plan to try out an EV charging allocation using knapsack and VCG to test it out.
