---
title: "01 Week 1"
permalink: /reports/vcg/01-week-1/
---

#vcg #Auction #Game_Theory 

# Context

The long term objective is to understand and simulate VCG auction mechanism for different resource allocation problems.

Mechanism design plays a central role in efficient allocation. Understanding first-price, second-price and VCG auctions helps to form the theoretical foundation before moving to more applied resource sharing models.

My medium term goal:

- Develop a simulation capable of modeling a dynamic resource allocation under different auction schemes.
- Validate theoretical predictions computationally
- Extend towards spectrum-sharing models

# What I Did

## Theoretical Study

Reviewed Lecture Material on 
- First price auctions
- Second Price Auctions
- VCG Mechanism

Understood:
- Incentive compatibility in VCG
- Dominant strategy property
- Revenue equivalence intuition
- Difference between allocation rule and payment rule

### Computational Work

- Studied SciPy documentation
- Implemented a small scale VCG simulation using SimPy for Discrete Event Simulation
- Logged Bids, Order Statistics , Payments, Utilities
- Verified Results Empirically

# Insights

VCG payment formula becomes much clearer when implemented as :
remove bidder i -> recompute welfare -> compute externality

Incentives vs revenue are separate dimensions - Truthful bidding maximizes incentive compatibility but not necessarily maximize revenue.

Simulation infrastructure is reusable. The current framework can be extended to multi item VCG, spectrum allocation etc.

# Agenda for Next week

- Read article "The VCG Auction in Theory and Practice" by Hal R. Varian and Christopher Harris
- Extract model definition, assumptions ,allocation and payment rules.
- Reproduce baseline model in code
- Attempt simulation of the simplest version of that model

# References
- Levin Jonathan, Vickrey-Clarke-Groves Mechanisms, Stanford Economics 285, 2009 Ghttps://web.stanford.edu/~jdlevin/Econ%20285/Vickrey%20Auction.pdf
