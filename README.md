# Monte Carlo Simulation with Geometric Brownian Motion (MC-GBM)

[![Python 3.8+](https://img.shields.io/badge/python-3.8+-blue.svg)](https://www.python.org/downloads/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Math: Stochastic Calculus](https://img.shields.io/badge/math-Itô_Calculus-red.svg)]()

> **A rigorous implementation of exact Geometric Brownian Motion simulation for financial risk quantification, derivatives pricing, and probabilistic forecasting.**

---

## Table of Contents
1. [The Problem](#1-the-problem)
2. [The Solution](#2-the-solution)
3. [Mathematical Foundation](#3-mathematical-foundation)
4. [Implementation](#4-implementation)
5. [Usage Examples](#5-usage-examples)
6. [Installation](#6-installation)
7. [Testing & Validation](#7-testing--validation)
8. [Extensions](#8-extensions)

---

## 1. The Problem

### What We Are Solving
Traditional deterministic financial models (e.g., compound interest $S_T = S_0e^{\mu T}$) fail to capture:
- **Path-wise uncertainty**: Markets exhibit continuous random shocks, not smooth deterministic growth
- **Tail risk**: No mechanism to quantify extreme event probabilities ("Black Swans")
- **Nonlinear effects**: Volatility drag and compounding create asymmetric distributions that linear projections miss
- **Path-dependency**: Options with barriers, lookbacks, or Asian features require full trajectory sampling

### Why It Matters
- **Risk Management**: Calculating Value-at-Risk (VaR) requires the complete loss distribution, not just mean/variance
- **Derivative Pricing**: No closed-form solutions exist for path-dependent options under realistic market conditions
- **Capital Allocation**: Strategic decisions require confidence intervals and quantile estimates, not point forecasts

---

## 2. The Solution

We implement **Monte Carlo integration over the probability measure induced by Geometric Brownian Motion**.

### Key Features
- **Exact Discretization**: No Euler-Maruyama approximation error—samples directly from the analytical solution
- **Risk-Neutral & Physical Measures**: Supports both real-world risk forecasting ($\mathbb{P}$) and derivative pricing ($\mathbb{Q}$)
- **Variance Reduction**: Antithetic variates and quasi-random sequences (Sobol) for faster convergence
- **Statistical Validation**: Built-in tests for lognormality, martingale preservation, and convergence rates

---

## 3. Mathematical Foundation

### 3.1 Stochastic Differential Equation

Under the physical measure $\mathbb{P}$:

$$dS_t = \mu S_t dt + \sigma S_t dW_t, \quad S_0 = s_0$$

Where:
- $\mu$: Expected instantaneous return (drift)
- $\sigma$: Instantaneous volatility (diffusion)
- $W_t$: Wiener process with $\mathbb{E}[W_t] = 0$, $\text{Var}(W_t) = t$

### 3.2 Exact Analytical Solution

Applying Itô's Lemma to $Y_t = \ln S_t$:

$$d(\ln S_t) = \left(\mu - \frac{\sigma^2}{2}\right)dt + \sigma dW_t$$

Integrating from $0$ to $T$:

$$S_T = S_0 \exp\left[\left(\mu - \frac{\sigma^2}{2}\right)T + \sigma W_T\right]$$

Since $W_T \sim \mathcal{N}(0, T)$, the terminal price is **lognormally distributed**:
- $\ln(S_T) \sim \mathcal{N}\left(\ln S_0 + (\mu - \frac{\sigma^2}{2})T, \sigma^2 T\right)$

### 3.3 Risk-Neutral Measure ($\mathbb{Q}$)

For derivative pricing, apply Girsanov's theorem to eliminate drift:

$$dS_t = rS_t dt + \sigma S_t d\tilde{W}_t$$

Where $r$ is the risk-free rate. This ensures the discounted price $e^{-rt}S_t$ is a martingale.
