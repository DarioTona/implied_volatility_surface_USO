# implied_volatility_surface_USO

# Implied volatility surface construction from US crude oil ETF option chains

In this notebook we build and visualize an **implied volatility surface** for **USO** (United States Oil Fund), an ETF designed to track the price of **crude oil**.  
We download option quotes across multiple **expiries** and **strikes**, compute the **Black–Scholes implied volatility** for each quote by numerically inverting the pricing formula, and then interpolate the results to obtain a smooth **IV surface**.

**Important note:** USO options are typically **American-style**, but we compute **Black–Scholes implied volatilities** as a standardized way to represent market option prices.

---

## Black–Scholes European Call Price

The Black–Scholes price of a **European call option** is:

$$
C = S_0 e^{-qT}N(d_1) - K e^{-rT}N(d_2)
$$

with

$$
d_1 = \frac{\ln(S_0/K) + (r - q + \tfrac{1}{2}\sigma^2)T}{\sigma\sqrt{T}},
$$



$$
d_2 = d_1 - \sigma\sqrt{T}
$$

### Variables

- $S_0$: current underlying price (spot)  
- $K$: strike price  
- $T$: time to maturity (in years)
- $r$: Risk-free rate  
- $q$: Dividend yield   
- $\sigma$: volatility of the underlying stock (annualized)


## What is Implied Volatility?

**Implied volatility (IV)** is the value of volatility $\sigma$ that makes an option pricing model (e.g., Black–Scholes) match the **observed market price** of an option.

In other words, instead of *assuming* a volatility and computing a theoretical option price, we **invert** the pricing model: we take the market option price as input and solve for the volatility that satisfies

$$
P_{\text{theory}}(S, K, T, r, \sigma, q) = P_{\text{actual}}.
$$



## Newton - Rhapson Method
The Newthon method to find the zeros of a function $f(x)$ consists on calculating

$$
x^g_{(n+1)} = x^g_n - \frac{f(x^g_n)}{f'(x^g_n)}
$$

with $x^g_n$ being the guess of the volatility at iteration $n$.

We can solve for the implied volatility with this simple iterative routine (Newton–Raphson). The idea is to adjust $\sigma$ until the Black–Scholes theoretical price matches the observed market price.

### Algorithm (Newton–Raphson for Implied Volatility)

We solve for the implied volatility $\sigma$ such that the theoretical price matches the observed market price.

1. **Initialize** a starting guess for the volatility, $\sigma_0$.

2. **Compute the residual (pricing error)**:

$$
\mathcal{L}(\sigma_n) = P_{\text{theory}}(\sigma_n) - P_{\text{market}}.
$$

3. **Check convergence**:

$$
\left|\mathcal{L}(\sigma_n)\right| < \epsilon
$$

- If **true**, stop and return $\sigma_n$.
- If **false**, continue.

4. **Compute the derivative w.r.t. volatility** (Vega / gradient):

$$
\frac{\partial \mathcal{L}}{\partial \sigma}(\sigma_n).
$$

5. **Update volatility** using Newton–Raphson:

$$
\sigma_{n+1} =\sigma_n - \frac{\mathcal{L}(\sigma_n)} {\frac{\partial \mathcal{L}}{\partial \sigma}(\sigma_n)}.
$$

6. **Repeat** steps 2–5 until convergence or until reaching the maximum number of iterations, $N_{\text{iter}}$.

<img width="838" height="636" alt="image" src="https://github.com/user-attachments/assets/340eaeb7-fe74-4341-a587-3d529ce549b3" />

**Sources**

https://www.youtube.com/watch?v=h6fMYoig2Pg

https://www.youtube.com/watch?v=g-efwWZZBvA&t=340s

