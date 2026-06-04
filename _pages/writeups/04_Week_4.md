---
title: "04 Week 4"
permalink: /reports/vcg/04-week-4/
math: true
---

# Context

This week I studied the paper "Efficient Sequential Assignment with incomplete information" by Gershkov, Moldovanu
## Introduction

The paper asks the question, supposedly there is a fixed set of heterogeneous objects; for eg: n hotel rooms of different quality, and customers arrive one at a time over some time horizon randomly. Each customer has a privately known type and it must be decided on arrival whether and which room to assign them. The customer cannot be called back later and there is a deadline T after which the unassigned room becomes worthless. 

The tension lies on the fact if one should give a good room to the current customer, or hold it in reserve hoping a higher value customer arrives later?  If the assignment is done too early,  the premium rooms are wasted on mediocre customers, if held too long, rooms can go unassigned when the deadline hits.

The paper builds on  a foundational result by Albright(1974) where under complete information, the optimal policy has an elegant structure: there exist cutoff functions $y_1(t)>y_2(t)>...y_n(t)$ that parition the type space into intervals. 

When an agent of type x arrives at time t:
- If $x\ge y_1(t)$ : assign the best available object
- If $y_2 \le x<y_1(t)$: assign the second best available object
- If $x<y_n(t)$ : assign nothing

An important property is that these cutoff functions don't depend on the qualities of the object but only on the arrival rpocess and the distribution of types. This means that after selling one object, the remaning cutoffs are just the top (n-1) curves from before, unchanged.

## The Dynamic VCG Mechanism

The paper's first contribution is in showing that this fficient allocation remains implementable even when agents' types are _private information_. The mechanism works like a dynamic version of the VCG auction where each agent who receives an object pays the _externality_ they impose on future agents i.e. the expected welfare loss their assignment causes to everyone who comes after them.

Specifically, an agent who gets the j-th best object at time t pays: 
$$\sum_{i=j}^{k_t}(q_{(i)}-q_{(i+1)})\cdot y_i(t)$$
This price makes the marginal agent (the one whose type exactly equals the cutoff) indifferent between getting the object or not, which ensures that truthful reporting is optimal. 

## Welfare Analysis

#### Theorem 3 : More variability in types increases welfare. 

This is somewhat counterintuitive. If you take two distributions of agent types with the same mean, but one with higher variance, the higher variance distribution yields higher expected welfare. This is because with more variable types, you're more likely to see extremely high-value agents who are excellent matches for your best objects. The option value of waiting becomes higher when there's more spread in possible types. 

#### Theorem 4: Cost of sequentiality. 

Theorem 4 compares the sequential assignment (where one must decide immediatly) to a hypothetical one where you can wait until the deadline and optimally match everyone. The welfare gap between these two scenarios is characterized using majorization theory and this gap vanishes as the number of identical objects grow large. Because with many identical objects, there's no reason to turn anyone away.

## Budget Balance

VCG mechanism collects payments but doesn't naturally distribute them. The paper addresses this with two mechanisms: 

- The Online (DEON) mechanism which gives each arriving agent a refund equal to what the previous agent paid. This never runs a deficit but may leave a surplus if no one arrives after the last sale. The expected surplus vanishes as the arrival rate $\lambda \to \infty$.

- The offline mechanism allows payments to be deferred until the deadline. The full payment is redistributed equally among all agents who arrived after the sale, achieving exact budget balance. The trade-off is that individual rationality can be violated ex-post (though it holds in expectation). 

## Infinite Horizon Extension

Appendix B treats the case with no deadline but exponential discounting $(r(t) = e^{-αt})$. Here the cut-off curves become _constants_ rather than time-varying functions, and the model extends to general renewal arrival processes.

An additional result shows that increased variability in _inter-arrival times_ (not just agent types) also increases expected welfare. This is because longer gaps create more urgency, raising the option value of assignment


# Upcoming Plans

-  Study - "Geographic and Statistical Analysis of EESS-Passive Satellite Overpasses for Spectrum Coexistence"
- 



