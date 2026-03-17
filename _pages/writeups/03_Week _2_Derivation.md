---
title: "03 Week  2 Derivation"
permalink: /reports/vcg/03-week--2-derivation/
---

This is a simulation check to verify the code output.

## Setup

Slots (no of clicks on each slot): 
$$x_1 = 100\ clicks; x_2=80\ clicks;x_3=50\ clicks$$
Values (Advertiser Values per click):
$$v_1=10,v=7,v_3=5,v_4=2$$

## GSP Minimum-Revenue Equilibrium

**Slot 3**: $p_3x_3 = v_4x_3=2\times50=100\implies p_3=2.00$

**Slot 2**: $p_2x_2 = v_3(x_2-x_3)+p_3x_3=5(80-50)+100 = 150+100 = 250 \implies p_2 = 250/80 = 3.125$

**Slot 1**: $p_1x_1 = v_2(x_1-x_2)+p_2x_2=7(100-80)+250=140+250=390 \implies p_1 = 390/100 = 3.90$

Utilities: $u_s=v_sx_s - p_sx_s$
- Adv 1: $10 \times 100 - 390 = 610$
- Adv 2: $7 \times 80 - 250 = 310$
- Adv 3: $5 \times 50 -100 = 150$

**Total GSP Revenue=** 390+250+100 = 740

## VCG via Externality
For each winner, compute: (welfare of others without me) −- − (welfare of others with me).

**Advertiser 1 payment:**

- With adv 1 present, others earn: $v_2 x_2 + v_3x_3 = 7(80) + 5(50) = 560 + 250 = 810$
- Without adv 1, everyone shifts up: $v_2 x_1 + v_3 x_2 + v_4 x_3 = 700 + 400 + 100 = 1200$
- Payment = $1200−810=3901200 - 810 = 390 1200−810=390$

**Advertiser 2 payment:**

- With adv 2 present, others earn: $v_1 x_1 + v_3 x_3 = 1000 + 250 = 1250$
- Without adv 2:$v_1 x_1 + v_3 x_2 + v_4 x_3 = 1000 + 400 + 100 = 1500$
- Payment = $1500−1250=250$

**Advertiser 3 payment:**

- With adv 3 present, others earn: $v_1 x_1 + v_2 x_2 = 1000 + 560 = 1560$
- Without adv 3:$v_1 x_1 + v_2 x_2 + v_4 x_3 = 1000 + 560 + 100 = 1660$
- Payment =$1660 - 1560 = 100$

**Total VCG revenue** = $390+250+100 =740$ which matches GSP exactly.

## Broad Matching

Query A values: $v_A = [10,8,5,2] ->$ efficient order: $1>2>3>4$

Query B values: $v_B = [7,11,3,6] ->$ efficient order: $2>1>4>3$

Average : $\bar{v} = [8.5,9.5,4.0,4.0] ->$ ranking: $2>1>3\ge 4$

On Query A, the broad bid assigns adv 2 to slot 1 but the efficient allocation puts adv 1 there which causes a misallocation. 

On Query B, slot 3 goes to adv 3 (bid =4.0) when the efficient winner is adv 4 (bid = 6.0) again a misallocation

### VCG for Query A 
- Slot 1: 8(20)+5(30)+2(50)=160+150+100=410
- Slot 2: 5(30)+2(50)=150+100=250
- Slot 3: 2(50)=1002(50) = 100
### VCG for Query B
- Slot 1: 7(20)+6(30)+3(50)=140+180+150=470
- Slot 2: 6(30)+3(50)=180+150=330
- Slot 3: 3(50)=150

## Strategic Bid Shading

True values $v = [10,7,5,2]$, shaded bids $b=[8.5,6.2,4.8,2.0]$

Ranking by bids: 8.5>6.2>4.8>2.0
Using the min-revenue recursion on $b=[8.5,6.2,4.8,2.0]$

- $p_3 x_3 = 2.0 \times 50 = 100 \;\Rightarrow\; p_3 = 2.00$
- $p_2 x_2 = 4.8(30) + 100 = 144 + 100 = 244 \;\Rightarrow\; p_2 = 244/80 = 3.05$
- $p_1 x_1 = 6.2(20) + 244 = 124 + 244 = 368 \;\Rightarrow\; p_1 = 368/100 = 3.68$

Revenue = 368+244+100 = 712 (down from 740)

