---
title: "07 Week 7"
permalink: /reports/vcg/07-week-7/
math: true
---

# The Truthful Set-Packing Auction

A plain walkthrough of what the mechanism does.

## What we are solving

Spectrum is cut into $m$ blocks. Each bidder wants a specific bundle of blocks and offers one price for the whole bundle, nothing for a part of it. Two bids clash if they want any block in common, because a block cannot go to two people. We want to accept a clash-free set of bids with the highest total value.

Written as an optimization:

$$\max_{x} \sum_{i} v_i x_i \quad \text{subject to} \quad \sum_{i \,:\, b \in S_i} x_i \le 1 \;\text{ for every block } b, \quad x_i \in \{0,1\}.$$

The best possible total is $V^*$. Finding it exactly is NP-hard, meaning it gets very slow as the number of bids grows. So at scale we use a fast approximation. The catch: a fast allocation with the wrong pricing rule lets bidders win by lying. Getting the allocation and the pricing right together is important.

## The example

Four blocks $\{1,2,3,4\}$, five bidders.

| Bidder | Bundle | Bid |
|--------|--------|-----|
| A | $\{1,2\}$ | 10 |
| B | $\{2,3\}$ | 9 |
| C | $\{3,4\}$ | 8 |
| D | $\{1\}$ | 5 |
| E | $\{4\}$ | 5 |

## The exact answer

Check the sensible combinations:

$\{A,C\}$ uses blocks $\{1,2\}$ and $\{3,4\}$, no clash, value $18$. $\{B,D,E\}$ uses $\{2,3\}$, $\{1\}$, $\{4\}$, no clash, value $19$. Everything else is worse or clashes.

So the best is $\{B, D, E\}$ with $V^* = 19$. In the code this is the exact solver, which just hands the optimization above to a solver.

## The fast approximation (greedy)

Greedy gives each bid a score, sorts by it, then makes one pass accepting any bid whose blocks are still free.

The score is value divided by the square root of bundle size, $v_i / \sqrt{|S_i|}$. The square root is a middle ground: it discounts bids that grab many blocks, but not too harshly. This exact choice is what gives the quality guarantee.

Scores:

| Bidder | Score |
|--------|-------|
| A | $10/\sqrt{2} \approx 7.07$ |
| B | $9/\sqrt{2} \approx 6.36$ |
| C | $8/\sqrt{2} \approx 5.66$ |
| D | $5.00$ |
| E | $5.00$ |

Walk down A, B, C, D, E:

A takes blocks 1,2 (free, accept). B needs block 2 (taken, skip). C takes blocks 3,4 (free, accept). D needs block 1 (taken, skip). E needs block 4 (taken, skip).

Greedy winners: $\{A, C\}$, value $18$. That is below the exact $19$, a ratio of $0.95$. The guarantee only promises greedy stays above $V^*/\sqrt{m} = 9.5$, so $18$ clears it easily. This is normal: the guarantee is loose, real performance is close to the best.

## How winners pay (critical value)

Each winner pays the lowest bid it could have made and still won, with everyone else's bids held fixed.

**Bidder A.** Lower A's bid and watch the order. A keeps winning as long as it stays ahead of B, because if B goes first it grabs block 2 and A can never complete its bundle. A stays ahead of B down to a bid of $9$ (ties go to A). So A pays $9$. A bid 10, keeps $10 - 9 = 1$.

**Bidder C.** A never depends on C, so A always goes first and blocks B. C's only rival left is E on block 4. C stays ahead of E while its score beats $5$, that is while its bid is at least $5\sqrt{2} \approx 7.07$. So C pays about $7.07$. C bid 8, keeps about $0.93$.

In the code this is found by binary search: try a bid, re-run greedy, see if the bidder still wins, narrow down to the exact cutoff. It lands on $9.00$ for A and $7.07$ for C, matching the hand numbers.

Why this makes honesty the best move: your bid decides only whether you win, never what you pay, because the price depends only on the other bids. Check A (true value 10, price fixed at 9):

Report 20, still first, still wins, still pays 9, utility 1. Report 9.5, still ahead of B, wins, pays 9, utility 1. Report 8, drops below B, loses, utility 0.

Nothing beats being honest. This works because greedy is monotone: bidding more never hurts your chances, which is exactly what critical-value pricing needs.

## Why the naive pricing fails

The standard rule (VCG) charges each winner the harm it causes others: the welfare without you, minus what the others get with you present. This is honest only when the allocation is exact. Put it on top of greedy and it breaks, on this same example.

Payment for A under the naive rule:

Greedy without A gives $\{B, D, E\}$, welfare $19$. The others' welfare with A present is just C, so $8$. Payment for A is $19 - 8 = 11$.

A bid 10 and is charged 11. Being honest gives A utility $10 - 11 = -1$. A would rather bid 0, lose on purpose, and keep 0. An honest mechanism can never punish an honest winner like that.

The reason: greedy without A reaches 19, but greedy with A only reaches 18, so A is billed for value the mechanism fails to deliver when A joins.

## The two takeaways

**The design rule.** DSIC, meaning honesty is every bidder's best strategy no matter what others do, comes from pairing a monotone allocation with critical-value payments. Exact allocation goes with VCG payments. Greedy allocation goes with critical-value payments. Mixing greedy with VCG is what breaks.

**What the scale test showed.** The value lost to greedy is small (ratio 0.95 to 1.00, far above the floor), truthfulness held at every size tested, and the real cost of being truthful sits in the payment step, not the allocation step.
