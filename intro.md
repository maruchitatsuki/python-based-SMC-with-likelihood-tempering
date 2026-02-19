# Python based sequential monte carlo method with likelihood tempering


The purpose of this website is to offer guidance for deepening your understanding of Sequential Monte Carlo (SMC) methods with likelihood tempering, as well as to provide a simple explanation of our SMC implementation, its hyperparameters, usage examples, and instructions for use.

Author: Tatsuki Maruchi
Affiliation: Nagoya University  

Last update: 2026-01-26

---

## Purpose of This Page

Sequential Monte Carlo (SMC) is a  
**method for approximating probability distributions by tracking a population of particles**  
over time.

Compared with MCMC, the two key differences are:

- Instead of a single sample path, SMC handles an entire **population of particles** simultaneously  
- The distribution **gradually evolves over “time” (steps)**

Because of this structure, a single SMC run provides both the **overall distribution** and its **uncertainty** at the same time.

SMC methods can be broadly categorized into two approaches:

- **Data tempering**  
- **Likelihood tempering (bridge approach)**

Data tempering sequentially adds data to transition the distribution. It is suitable for time-series data, but the order in which data are added can affect estimation accuracy.  
Likelihood tempering, which is less commonly used, smoothly shifts the distribution from the prior to the posterior by gradually increasing the influence of the likelihood.

---

## Differences from MCMC

When explaining SMC, it is essential to compare it with MCMC.

---

## Core Components of SMC

Detailed explanations of the SMC algorithm can be found in the [GitHub repository](https://github.com/maruchitatsuki/python-based-Sequential-Monte-Carlo-method-with-likelihood-tempering/tree/main/SMC_Algorithm), or by reading the source code directly.

### Particle
A *particle* is a sample in the parameter space.  
In SMC, we represent the distribution not by a single point, but by a collection of many particles.

### Weights

Each particle is assigned a weight that represents  
*how well that particle fits the current target distribution*.  
In practice, this is determined using the likelihood.

When the weights become extremely imbalanced, the particle system suffers from **degeneracy**.  
To mitigate this, we introduce the concept of ESS.

### Resampling

When particle weights are highly imbalanced, most particles become effectively useless.  
To avoid this, SMC performs **resampling**, an operation that duplicates effective particles and discards ineffective ones.

If there exists a particle with an extremely large weight, diversity of the particle population may collapse, resulting in **degeneracy**.  
To prevent this, the indicator {ref}`ESS<SMC_main_ESS>` plays an essential role.  
In our algorithm, the influence of the likelihood is adjusted dynamically based on this ESS value.

(SMC_main_ESS)=
### ESS

ESS stands for *Effective Sample Size* and is defined as:

$\[
\mathrm{ESS} = \frac{1}{\sum_{m=1}^{N_p} w_m^2}
\]$

This metric quantifies how imbalanced the weights are and represents the number of effectively contributing particles.  
Consider the following extreme cases:

- If one particle dominates all the weight among $\(N_p\)$ particles, then ESS = 1.  
- If all $\(N_p\)$ particles contribute equally, then ESS = $\(N_p\)$.

In our algorithm, we control the progression of estimation depending on whether the ESS exceeds the threshold {ref}`ESS_limit <HP_main_ESS_limit>` (\(ESS_{\mathrm{limit}}\)).

### Prior, Intermediate, and Posterior Distributions

SMC does not attempt to directly handle a complex target distribution from the beginning.

Instead, it transitions smoothly from:

- an initial **easy** distribution  
- to the **complex target** distribution

This transition is realized through tempering (adjusting the temperature parameter) or increasing the number of data points.


## Likelihood Tempering

In likelihood tempering, the influence of the likelihood on the intermediate distributions is gradually increased.

\[
\pi(\theta \mid y) \propto p(y \mid \theta)^{\gamma_i}\, p(\theta)
\]

Here, \(\pi(\theta \mid y)\) represents the intermediate distribution,  
\(p(y \mid \theta)\) is the likelihood,  
and \(p(\theta)\) is the prior distribution.

The parameter \(\gamma_i\) is called the *tempering factor*.  
A larger value of \(\gamma_i\) places more emphasis on the likelihood in the intermediate distribution.  
The index \(i\) denotes the SMC iteration, and at \(i = 0\), \(\gamma_i = 0\).  
When the estimation process ends, \(\gamma_i = 1\).

## Data Tempering

In data tempering, the data are gradually introduced in small portions,  
thereby increasing the strength of observational information step by step.

Both approaches share the same fundamental idea:

**They transition from an easier distribution to a more complex one.**

---

## SMC Workflow

SMC consists of four major steps:

1. Initialization  
2. Likelihood calculation  
3. Resampling  
4. Mutation  

After performing Initialization, steps 2–4 are repeated to obtain the posterior distribution.

### 1. Initialization

In the Initialization step, we first define the prior distribution \(p(\theta)\)  
and sample particles from this prior.

### 2. Likelihood Calculation

Using the parameters stored in each particle, we compute the likelihood.  
Then, particle weights are updated according to their likelihood values.

### 3. Resampling

Particles are probabilistically duplicated or discarded according to their weights.  
Suppose the total number of particles is \(N_p\), and the weights are normalized.

- A particle with weight \(1/(2N_p)\) has a 50% chance of being discarded and a 50% chance of surviving.  
- A particle with weight \(1.5/N_p\) has a 50% chance of producing two copies and a 50% chance of remaining as one.

### 4. Mutation

After resampling, many particles may share identical parameter values.  
To restore diversity, we apply a Metropolis–Hastings mutation step to each particle,  
allowing particles to transition probabilistically.

---

## Advantages of SMC

SMC provides the posterior distribution of parameters,  
allowing uncertainty to be quantified.  
This is a major advantage compared with methods that only perform point estimation.

---

## What This Site Provides

This website offers:

- an example estimation problem using the Michaelis–Menten model,  
- detailed explanations of hyperparameters,  
- and instructions on how to use our SMC code.

```{tableofcontents}
