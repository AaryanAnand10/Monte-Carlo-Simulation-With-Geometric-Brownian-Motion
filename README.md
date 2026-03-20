# Stock Price Simulation Using Monte Carlo & Geometric Brownian Motion (GBM)

---

## Problem Statement
Financial markets are inherently **unpredictable and volatile**. Investors,
traders, and risk managers face the challenge of estimating where a stock
price might be in the future — not just one possible outcome, but a full
**range of possible outcomes** with associated probabilities.

> **The core question we are solving:**
> *"Given today's stock price, what is the distribution of possible
>   prices at a future point in time?"*

Traditional models assume a single deterministic outcome, which is
unrealistic. We need a model that **embraces randomness** and quantifies
uncertainty.

---

## Why GBM?
Geometric Brownian Motion is the industry-standard model for stock prices
because it captures two key real-world behaviors:

- ✅ Stock prices **cannot go negative**
- ✅ Stock prices exhibit **random fluctuations** around a general trend
- ✅ Returns are **normally distributed** (log-normal prices)
- ✅ It forms the backbone of the famous **Black-Scholes** options pricing model

---

## Mathematical Foundation

### 1. The GBM Stochastic Differential Equation (SDE)
The continuous-time dynamics of a stock price $$S(t)$$ are governed by:

$$dS = \mu S \, dt + \sigma S \, dW_t$$

Where:
- $$S$$ = Current stock price
- $$\mu$$ = Drift coefficient (expected annual return)
- $$\sigma$$ = Volatility coefficient (standard deviation of returns)
- $$dW_t$$ = Wiener process increment (random shock) ~ $$\mathcal{N}(0, dt)$$

---

### 2. The Closed-Form Solution
Solving the SDE using **Itô's Lemma**, we get the exact solution:

$$S(t) = S_0 \cdot \exp\left[\left(\mu - \frac{\sigma^2}{2}\right)t + \sigma W_t\right]$$

Where:
- $$S_0$$ = Initial stock price at time $$t = 0$$
- $$\left(\mu - \frac{\sigma^2}{2}\right)$$ = The **Itô correction term** (adjusted drift)
- $$W_t = \sqrt{t} \cdot Z$$ where $$Z \sim \mathcal{N}(0,1)$$

---

### 3. Discrete-Time Approximation
For simulation purposes, we discretize the GBM over small time steps $$\Delta t$$:

$$S_{t+\Delta t} = S_t \cdot \exp\left[\left(\mu - \frac{\sigma^2}{2}\right)\Delta t + \sigma \sqrt{\Delta t} \cdot Z\right]$$

Where:
- $$\Delta t = \frac{T}{N}$$ — time step size
- $$T$$ = Total time horizon (e.g., 1 year)
- $$N$$ = Number of time steps (e.g., 252 trading days)
- $$Z \sim \mathcal{N}(0,1)$$ — a standard normal random variable

---

## Why Monte Carlo?
A single GBM path gives one possible future — but we need to understand
the **full distribution** of outcomes. Monte Carlo simulation solves this by:

- Running the GBM simulation **thousands of times** (e.g., 10,000 paths)
- Each simulation uses a **different set of random numbers** $$Z$$
- The collection of all paths forms a **probability distribution** of future prices

---

## How We Simulate: Step-by-Step

| Step | Description |
|------|-------------|
| **1** | Collect historical stock data to estimate $$\mu$$ and $$\sigma$$ |
| **2** | Define simulation parameters: $$S_0$$, $$T$$, $$N$$, number of paths |
| **3** | For each path, generate $$N$$ random samples $$Z \sim \mathcal{N}(0,1)$$ |
| **4** | Apply the discrete GBM formula at each time step |
| **5** | Record the final price $$S_T$$ for each path |
| **6** | Analyze the distribution of all $$S_T$$ values |

---

## Estimating Drift and Volatility From Data

$$\mu = \frac{1}{N} \sum_{i=1}^{N} r_i \quad \text{(mean daily return)}$$

$$\sigma = \sqrt{\frac{1}{N-1} \sum_{i=1}^{N} (r_i - \mu)^2} \quad \text{(std of daily returns)}$$

Where daily log returns are computed as:

$$r_i = \ln\left(\frac{S_i}{S_{i-1}}\right)$$

---

## What We Extract From the Simulation

After running $$M$$ Monte Carlo paths, we can compute:

- **Expected future price:**
$$\mathbb{E}[S_T] = \frac{1}{M} \sum_{i=1}^{M} S_T^{(i)}$$

- **Value at Risk (VaR) at confidence level** $$\alpha$$:
$$\text{VaR}_\alpha = S_0 - \text{Percentile}(S_T, 1-\alpha)$$

- **Confidence Intervals** — the range within which prices are expected
  to fall with a given probability (e.g., 95%)

