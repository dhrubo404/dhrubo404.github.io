---
title: "06 Week 5"
permalink: /reports/vcg/06-week-5/
math: true
---

# Context

This week I studied the paper "Physics-grounded Mechanism Design for Spectrum Sharing between Passive and Active Users" and also simulated its results.

# Introduction

Passive satellites listen to extremely faint natural microwave radiation to measure things. They are spectrum freeloaders i.e. they don't transmit, but just listen. However the bands they need to listen are in demand by 5G/6G providers who want to transmit there.

The current regulation says that the satellite band is owned by one user forever and that active users have to stay out. This wastes spectrum most of the time, since the satellite only looks at any given spot for approx. 25 seconds at a time.

The paper proposes treating spectrum as a commodity the satellite procures on a per-overpass marketplace. The satellite offers to compensate active users for staying quiet in a given frequency at a given time, with the offer price set by exactly how much that silence improves its measurement. This price is grounded in the physics of radiometry, specifically the radiometer equation. The marketplace clears via a VCG auction, which guarantees truth-telling. Since the exact VCG allocation is computationally expensive, the paper also develops a fast approximation algorithm that exploits a mathematical property called **submodularity**.

## What is EESS-passive radiometry?

A microwave radiometer is a highly sensitive antenna-receiver combo that measures the tiny amount of natural thermal radiation coming off the Earth.
Different frequencies correspond to different things:
- **23.8 GHz** → water vapor absorption line (used to measure atmospheric water vapor, IWV)
- **36.5 GHz** → sensitive to liquid water, wind roughening of ocean surface
- **89.0 GHz** → cloud liquid water, precipitation

The measured quantity is brightness temperature $T_b$ (in Kelvin), which is a proxy for how much thermal radiation is coming from a scene at that frequency.

## The Radiometer Equation and key physics

The measurement noise, i.e. the standard deviation of the brightness-temperature measurement, is:

$$\sigma_T = \frac{T_{\mathrm{sys}}}{\sqrt{B\tau}}$$

Where:
- $T_{\mathrm{sys}}$ = system noise temperature (a property of the hardware)
- $B$ = bandwidth you're integrating over (Hz)
- $\tau$ = integration time (seconds)

Squaring, we get the variance:

$$\sigma_T^2 = \frac{T_{\mathrm{sys}}^2}{B\tau} = \frac{\kappa}{B\tau}$$

**Why this matters?** Measurement noise decreases as one gets more bandwidth OR more time. This means bandwidth and time are fungible i.e. one second over 10MHz is the same noise reduction as 10 seconds over 1 MHz. Both give us a time-bandwidth product of $10 \\, \mathrm{MHz} \cdot \mathrm{s}$

The shape of this curve matters too. Since $\sigma^2 \propto 1/B$, going from $B=1$ to $B=2$ helps a lot, but going from $B=100$ to $B=101$ barely moves the needle. This is _diminishing returns_, and it shows up later as the mathematical property of submodularity that makes the whole greedy approximation tractable.

## From radiometer noise to "retrieval" error

What we actually care about is rarely brightness temperature itself. We want _geophysical products_ like total water vapor amount, wind speed, or sea surface temperature. These are estimated as weighted linear combinations of brightness temperatures across channels:

$$\hat{y}_k = c_k^T T_b = \sum_j c_{k,j} T_{b,j}$$

Where:

- $\hat{y}_k$ = estimate of product $k$ (e.g., water vapor)

- $c_{k,j}$ = sensitivity of product $k$ to channel $j$ (derived from atmospheric inversion theory)

- $T_{b,j}$ = brightness temperature measured in channel $j$

For water vapor retrieval, the sensitivity vector is roughly $c_{\mathrm{IWV}} = [0.45, -0.20, 0.05]$ across the three channels. The 23.8 GHz channel dominates because that's the water vapor absorption line.

Assuming channel noises are independent, the variance of the retrieved product is just weighted error propagation:

$$\mathrm{Var}[\hat{y}_k] = \sum_j c_{k,j}^2 \sigma_j^2 = \sum_j c_{k,j}^2 \cdot \frac{\kappa_j}{B_j \tau}$$

**Why this matters?** Some channels are more "valuable" than others for a given product. This cross-channel weighting is the second lever the paper exploits: if 23.8 GHz tiles are expensive in an interference trap, the auction can substitute cheaper 36.5 GHz tiles, paying the price of lower sensitivity by buying more of them.

## Spectrum sharing and VCG Auction

The paper considers three regimes of spectrum sharing.

**Regime 1: Static partitioning** (current practice). The satellite band is permanently allocated to passive sensing. Active users excluded forever. Simple, but wastes spectrum most of the time.

**Regime 2: Time-dynamic sharing.** All channels muted during overpasses. Less wasteful but still overkill — it blocks high-value bands during the overpass even when the satellite isn't actually using them.

**Regime 3: Fully flexible sharing** (the paper's proposal). Discretize the time–frequency plane into tiles. The satellite buys only the cheapest subset of tiles needed to hit its accuracy target. Scattered across the grid, dynamically chosen per overpass.

The mechanism that clears this market is a **Vickrey-Clarke-Groves (VCG) reverse auction**. Three properties make VCG the gold standard:

1. **Allocative efficiency**: the chosen allocation maximizes total social welfare.

2. **Dominant-strategy incentive compatibility (DSIC)**: truth-telling is each seller's best strategy _regardless of what others do_.

3. **Individual rationality**: no participant is worse off by joining than by staying out.


Here it's a _reverse_ auction (procurement): the satellite is the buyer, active users are the sellers offering silence. Seller $i$'s payment is:

$$p_i = \underbrace{C_i(S_i^*)}_{\text{cost reimbursed}} + \underbrace{\left[ W(S^*) - W_{-i}(S^*_{-i}) \right]}_{\text{information rent}}$$

The information rent term answers: how much better off is the rest of the world because seller $i$ exists?



## Submodularity - diminishing returns as a math property



A set function $F : 2^\Omega \to \mathbb{R}$ is **submodular** if for any nested sets $A \subseteq Q$ and any new element $z \notin Q$:

$$F(A \cup \\{z\\}) - F(A) \geq F(Q \cup \\{z\\}) - F(Q)$$



In words: adding $z$ to a smaller set gives at least as much benefit as adding it to a larger set. This is diminishing returns expressed as a set-function property.



**Why this matters?** Submodularity is a magic word in combinatorial optimization. Greedy algorithms give provably near-optimal results for submodular objectives:

- For **maximization** under a cardinality constraint, greedy achieves $(1-1/e) \approx 63\\%$ of optimal (Nemhauser-Wolsey-Fisher 1978).

- For **covering** (the case in this paper — cover a target utility at minimum cost), greedy is within a logarithmic factor (Wolsey 1982).



This is what rescues the paper from solving an NP-hard combinatorial auction exactly. The radiometer equation gives $\sigma^2 \propto 1/B$, which is convex in $B$. Convexity of $1/x$ produces submodularity of the variance-reduction set function. Submodularity gives the greedy approximation bound. Each step is forced by the previous one. The physics hands the economics exactly the structure it needs.



# Physics Model



## Setting up the tile grid



During one overpass of duration $\tau$, the time–frequency plane is discretized into a finite set of non-overlapping tiles $\Omega$. Each tile $x \in \Omega$ has:

- $\delta f_x$ = bandwidth of the tile (Hz)

- $\delta t_x \leq \tau$ = time duration (s)

- $\alpha_x \in [0,1]$ = duty cycle, i.e. the fraction of the tile's duration that is actually quiet

- $j(x) \in \\{1, \dots, J\\}$ = which radiometer channel the tile belongs to



Active users (sellers) control the tiles. A tile is "quiet" if its controlling seller mutes emissions to satisfy a prescribed interference mask $M_{j(x)}$ for duty cycle $\alpha_x$.



For a chosen quiet set $S \subseteq \Omega$, the **effective clean bandwidth** in channel $j$ is:

$$B_j(S) = B_j^{(0)} + \frac{1}{\tau} \sum_{\substack{x \in S \\\\ j(x) = j}} \alpha_x \cdot \delta t_x \cdot \delta f_x$$



Where:

- $B_j^{(0)} > 0$ is a small baseline (permanently protected bandwidth, ensures $B_j > 0$ so $1/B_j$ is well-defined)

- The sum accumulates the _spectral volume_ $\alpha_x \delta t_x \delta f_x$ in Hz·s of all purchased tiles in channel $j$

- Dividing by $\tau$ converts the accumulated Hz·s back into an equivalent averaged Hz



This is where the fungibility of bandwidth and time becomes concrete. Two tiles of $(10 \\, \mathrm{MHz} \times 1 \\, \mathrm{s})$ give the same $B_j$ contribution as twenty tiles of $(1 \\, \mathrm{MHz} \times 1 \\, \mathrm{s})$. Only the spectral volume matters.



## Noise + residual interference model



Channel $j$ has two sources of error.

**Thermal noise** from the radiometer equation:

$$\sigma_{j,th}^2(S) = \frac{\kappa_j}{B_j(S) \cdot \tau}$$

Where $\kappa_j \propto T_{sys,j}^2$ is a hardware constant.

**Residual RFI**: even with active users complying with the emission mask $M_j$, a small amount of interference leaks through. This is modeled as a convex function in units of brightness temperature variance:

$$\phi_j(M_j) = \gamma_j P_{\mathrm{RFI},j}(M_j) + \beta_j P_{\mathrm{RFI},j}^2(M_j) \quad [K^2]$$

The linear term captures the stochastic noise contribution, the quadratic term captures squared bias. Together they constitute mean squared error. The function $\phi_j$ is convex and decreasing under tighter masks.

**Total channel variance**:

$$\sigma_j^2\left(B_j(S), M_j\right) = \frac{\kappa_j}{B_j(S) \\, \tau} + \phi_j(M_j)$$

## Retrieval error propagation



Plugging the channel variance into the weighted-sum retrieval and propagating error:

$$\mathrm{Var}[\hat{y}_k](S) = \sum_{j=1}^J c_{k,j}^2 \left[\frac{\kappa_j}{B_j(S) \tau} + \phi_j(M_j)\right]$$

The **mission constraint** is that retrieval variance stays below a target for every product:

$$\mathrm{Var}[\hat{y}_k](S) \leq \varepsilon_k^2 \quad \forall k$$



This defines the **feasible allocation set** $\mathcal{A} = \\{S \subseteq \Omega : \mathrm{Var}[\hat{y}_k](S) \leq \varepsilon_k^2 \\; \forall k\\}$.



For example, the IWV mission might require RMSE of $0.5 \\, \mathrm{g/m^2}$, giving $\varepsilon_{\mathrm{IWV}}^2 = 0.25$. Any allocation $S$ that achieves this is feasible; the auction picks the cheapest one.


# The VCG Mechanism

## Social Welfare



The market has one buyer (the radiometer) and $N$ sellers (active users). Each seller $i$ controls a portfolio of tiles $\Omega_i \subset \Omega$. For any global allocation $S$, the local contribution of seller $i$ is $S_i = S \cap \Omega_i$.


The **buyer's valuation** is endogenous to the retrieval physics. It rewards safety margin below the error threshold:

$$v_0(S) = \begin{cases} \lambda_0 \sum_k w_k \left(\varepsilon_k^2 - \mathrm{Var}[\hat{y}_k](S)\right) & \text{if } S \in \mathcal{A} \\\\ -\infty & \text{otherwise} \end{cases}$$

The $-\infty$ outside $\mathcal{A}$ is a hard mission constraint: no infeasible allocation is ever chosen. The $\lambda_0$ converts variance reduction to dollars, and $w_k$ are relative weights across products.


The **seller cost** is private. Each seller has a non-decreasing cost function $C_i(S_i; \theta_i)$ with $C_i(\emptyset) = 0$, where $\theta_i$ is their private type (traffic load, QoS priority, etc.). The seller valuation is just the negative cost:

$$v_i(S; \theta_i) = -C_i(S \cap \Omega_i; \theta_i)$$


The **social welfare** is buyer utility minus aggregate seller cost:

$$W(S; \theta) = v_0(S) + \sum_{i=1}^N v_i(S; \theta_i) = v_0(S) - \sum_{i=1}^N C_i(S_i; \theta_i)$$

For infeasible $S$, $W = -\infty$.


The paper also makes a **non-pivotal feasibility assumption**: no single seller is essential. For any $i$, the restricted feasible set $\mathcal{A}_{-i} = \\{S \in \mathcal{A} : S_i = \emptyset\\}$ is non-empty. This guarantees that pivot payments are finite.



## Allocation Rule



The mechanism picks the feasible allocation that maximizes reported social welfare:

$$S^*(\hat{\theta}) \in \arg\max_{S \in \mathcal{A}} \left[ v_0(S) - \sum_{i=1}^{N} \hat{C}_i(S_i) \right]$$



Basically, maximize scientific value minus total cost over all allocations that meet the mission constraint.



## Payment Rule


The payment to seller $i$ follows the **Clarke pivot rule**, adapted for procurement:

$$p_i(\hat{\theta}) = \hat{C}_i(S_i^*) + \left[ W(S^*;\hat{\theta}) - W_{-i}\left(S_{-i}^*;\hat{\theta}_{-i}\right) \right]$$

Where

$$W_{-i}\left(S_{-i}^*\right) = \max_{S' \in \mathcal{A}_{-i}} \left[ v_0(S') - \sum_{j \neq i} \hat{C}_j\left(S'_j\right) \right]$$

is the maximum welfare achievable if seller $i$ were absent.


The first term reimburses the seller's reported cost. The second term, the **information rent** is the externality seller $i$ imposes on the rest of the economy by being present. If seller $i$'s presence makes the world better by \$X for everyone else, $i$ pockets $X above their cost.



The non-pivotal assumption guarantees $W_{-i}$ is finite, so the payment is well-defined.

## Why does this induce truth-telling? (DSIC proof sketch)

The crown jewel of VCG. Seller $i$'s utility under any reported cost $\hat{C}_i$ is payment minus _true_ cost:

$$u_i = p_i - C_i(S_i^*)$$

Substituting the payment rule:

$$u_i = \left[ \hat{C}_i\left(S_i^*\right) - C_i\left(S_i^*\right) \right] + W\left(S^*;\hat{\theta}\right) - W_{-i}\left(\hat{\theta}_{-i}\right)$$



Under truth-telling ($\hat{C}_i = C_i$), the first bracket vanishes:

$$u_i^{\text{truth}} = W(S^*; \theta_i, \hat{\theta}_{-i}) - W_{-i}(\hat{\theta}_{-i})$$



**The key observation**: $W_{-i}(\hat{\theta}_{-i})$ depends only on _other sellers'_ reports. It is a constant from seller $i$'s perspective — independent of what $i$ does.



So to maximize $u_i$, seller $i$ must maximize $W(S^*; \theta_i, \hat{\theta}_{-i})$. But the mechanism _already_ picks $S^*$ to maximize reported welfare. If $i$ reports truthfully, the mechanism's objective is exactly $i$'s true welfare contribution. Any lie distorts the mechanism into picking a suboptimal $S^*$ from $i$'s actual perspective.



Therefore, truth-telling is a **dominant strategy** — it is optimal regardless of what the other sellers report. No strategy required, no equilibrium calculation, just honesty. **Allocative efficiency** follows immediately: with truth-telling, $S^*$ maximizes the true welfare. **Individual rationality** also follows, because $W(S^*; \theta) \geq W_{-i}(\theta_{-i})$ (the unrestricted max is at least as large as the restricted max), so $u_i \geq 0$.
