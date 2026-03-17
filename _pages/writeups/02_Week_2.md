---
title: "02 Week 2"
permalink: /reports/vcg/02-week-2/
math: true
---

# Context

This week, I studied an [article](https://courses.cs.washington.edu/courses/cse490z/20sp/slides/Varian.pdf) by Google's Ex- Chief Economist Hal R Varian
### Market Design for Auction Markets

Two kinds of online ad auction formats are used primarily - GSP and VCG. The paper "Market Design for Auction Markets: The VCG Auction in Theory and Practice" by Varian and Harris shows how Google and other companies use different auction strategies, specifically Generalized Second Price and VCG. In 2012, Google switched from GSP to VCG auctions for their contextual ads.

The article goes into detail the differences between GSP and VCG's working.

### Broad Match

When an advertiser bids on a keyword, search engines don't just match it exactly. The keyword `[dog food]` matches the query "dog food" exactly, but also _broadly_ matches "pet food", "puppy nutrition", etc. So one bid covers a whole family of related queries. 

This creates a problem when a single advertiser now participates in many different auctions (one per query) but can only submit a single bid.  As the advertiser's value per click differs across different queries. A pet food company values the term "dog food" more than "generic pet supplies". 

In GSP auctions, the bids depend on the advertiser's position which changes from auction to auction depending on query. If the advertiser A ranks #1 for "dog food" but #3 for "pet food" the pricing logic breaks down as different bids are required for each auction but only a single bid is available. 

VCG on the other hand provides a more elegant solution. Since in VCG truthful bidding is optimal, the advertisers just bid the average value across all broad queries. The VCG mechanism absorbs the complexity.

### VCG Without Knowing Clickthrough Rates

The VCG Payment equation is : 
\[
p_1x_1=v_2(x_1-x_2)+v_3(x_2-x_3)+v_4x_3
\]
It looks like $x_1,x_2,x_3$ is required to know in advance to compute what advertiser 1 owes. But in reality how many clicks each slot will get is unknown till the auction ends. However the payment can be computed on a click-by-click basis as clicks arrive in real time.

Every time a click lands somewhere on the page,

| Event           | Action                                          |
| --------------- | ----------------------------------------------- |
| Click on slot 1 | **Charge** advertiser 1 the amount $b_2​$       |
| Click on slot 2 | **Pay back** advertiser 1 the amount $b_2-b_3$​ |
| Click on slot 3 | **Pay back** advertiser 1 the amount $b_3-b_4$  |

At the end of the day, total clicks on each slot accumulate to $x_1,x_2,x_3$ naturally, and the net bill is : 
\[
b_2x_1-(b_2-b_3)x_2-(b_3-b_4)x_3
\]
Hence no forecasting needed.

In the end, Google switched from GSP to VCG because of its flexibility and cross auction consistency.

This paper was incredibly useful in seeing a real world application of VCG auctions and how it can be used in advert auctions.

## Upcoming Plans

- To study Mechanism Design and when/why VCG breaks down in combinatorial settings.
- To see how spectrum sharing works using SATYA

## References
- [Varian, H.R. and Harris, C. (2014). The VCG Auction in Theory and Practice. American Economic Review, 104(5), pp.442–445](https://doi.org/10.1257/aer.104.5.442.)
- [Myerson, R.B. (1981). Optimal Auction Design. Mathematics of Operations Research, 6(1), pp.58–73.](https://doi.org/10.1287/moor.6.1.58.)



