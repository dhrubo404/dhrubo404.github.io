---
title: "Week 3"
permalink: /reports/vcg/week-3/
math: true
---

# Context

## SALSA

I spent this week going through a paper tackling secondary level spectrum allocation. The methodology used was called SALSA : Strategyproof online spectrum admission. Essentially, a primary user owns the exclusive right to use a radio frequency (RF) channel for a time window $[0,T]$. They want to rent it out to secondary users to make money.

The secondary users show up at a time each wanting to use the channel starting *now* for $t_i$ slots and is willing to pay $b_i$ per time slot.

The central authority which is acting on behalf of the primary user must decide immediately whether to approve this purchase or not.

**Why this is difficult?**

Because the future is unknown! Maybe a low-value request is accepted now but a higher value one shows right after. 


**Request structure**

Each request is a tuple $r_i = (b_i,a_i,t_i)$, where:
- $b_i$ = bid value per unit time
- $a_i$ = arrival time
- $t_i$ = time duration

The total value of request $r_i$ to the user is $v_i = b_i\times t_i$

If admitted, the user's utility is:
$U(i) = x_i\cdot v_i-p_i$

where $x_i = 1$ if admitted, 0 otherwise and $p_i$ is what they actually pay.

## SATYA

Another mechanism which I studied this week was SATYA (Sanskrit for 'Truth'). 

This mechanism is useful for auctioning short-term spectrum leases and letting some users share the channels. Eg. A wifi router doesn't care if another nearby device uses the same channel sometimes as it has a MAC protocol to handle contention, but a TV station broadcasting continuously needs exclusive access. 

SATYA mechanism essentially forces each bidder to describe themselves with a tuple, containing a variety of information. Such as, how often am I active? How much bandwith do I need? What's my penalty if the channel is blocked? Etc. 

The auction then allocates channels to maximize total expected value across all users, accounting for interference 

# What I did this week

## Theoretical Study

- Studied Paper - SALSA : Strategy-proof online spectrum admission for Wireless Networks
- Studied paper - Enabling Sharing in Auctions for Short-Term Spectrum Licenses (SATYA)
- Studied Paper - Failures of VCG Mechanism in Combinatorial Auctions and Exchanges


## Computational Work

- Simulation of SALSA mechanism using Python (WIP)
- A showcase of how VCG breaks during combinatorial auctions.


# Insights

Both SATA and SALSA are mechanism which are user for short-term spectrum allocation for secondary users. Truthful bidding is the dominant strategy via Myerson's Monotone allocation(SATYA) or VCG Payments (SALSA)

Both the mechanism utilize conflict graphs as an interference model and both require monotonicity 

However key differences are in the area of auction timing - SATYA uses batch/periodic, where all bids are collected and then cleared simultaneously while SALSA is sequential. SALSA also is exclusive and prohibits sharing among excessive users on the same channel.

Moreover, SATYA requires way more information in its bidding model to work as it allows sharing of RF space, while SALSA gets by with simpler models. 

# Upcoming plans

- Simulation of SATYA Mechanism
- Study of Efficient sequential assignment with incomplete information
