---
jupytext:
  formats: md:myst
  text_representation:
    extension: .md
    format_name: myst
    format_version: 0.13
    jupytext_version: 1.11.5
kernelspec:
  display_name: Python 3
  language: python
  name: python3
---

## Code Availability

The source code is available from the following GitHub repository:

```text
https://github.com/maruchitatsuki/python-based-Sequential-Monte-Carlo-method-with-likelihood-tempering
```

Clone the repository with:

```bash
git clone https://github.com/maruchitatsuki/python-based-Sequential-Monte-Carlo-method-with-likelihood-tempering.git
cd python-based-Sequential-Monte-Carlo-method-with-likelihood-tempering
```

---

## Quick Start

The minimum workflow for using this framework is as follows:

1. Prepare your input data.  
2. Edit `SMC_example/Micmem_settings.py` to define the estimation settings and data configuration.  
3. Rewrite `sim_particle` and the related parts so that they match your own model and likelihood calculation.  
4. Run the main script:

```bash
python SMC_example/Micmem_SMC_main.py
```

---

## Environment

This code was developed and tested using **Python 3.10.12**.

The required Python libraries are listed in:

```
requirements.txt
```

Install them before running the code:

```bash
pip install -r requirements.txt
```

---

## How to Use

### Execution Flow

When the script is executed:

1. Input data are loaded  
2. The SMC algorithm is executed  
3. The likelihood is evaluated for each particle  
4. The posterior distribution is obtained  

The posterior samples are stored in the variable `p_pred`.

---

### Output

The estimation result is automatically saved as:

```
Posterior_Distributions.png
```

This file contains histograms of the posterior distributions.

Additional outputs (e.g., particle states or intermediate results) can be added if needed.

---

### Core Function

The main program calls a function named `sim_particle`.

- Input:
  - all particles (parameter sets)

- Output:
  - likelihood values (required)
  - optional additional outputs (e.g., simulation results)

The likelihood must always be returned.

Parallel computation is supported.  
By default, parallelization is implemented using `ray`.

---

### Input Data

The input data format depends on the target problem.

In the provided example:

- Data are stored in:
  ```
  SMC_example/data/
  ```
- Files:
  ```
  mm_pseudo_data_0.csv ~ mm_pseudo_data_5.csv
  ```
- Each file contains:
  - time $t$
  - $S_{\text{true}}$
  - $P_{\text{true}}$
  - $P_{\text{obs}}$

In the estimation, only $t$ and $P_{\text{obs}}$ are used.

Users should replace these data files with their own dataset.

Note:  
In practical applications, it may be preferable to use only data after the system reaches steady state. The current setup is for demonstration purposes.

---

## Directory Structure

```
python-based-sequential-monte-carlo/
├── SMC_Algorithm/
│   ├── algorithm1.png
│   └── algorithm2.png
│
├── SMC_example/
│   ├── data/
│   ├── Micmem_likelihood.py
│   ├── Micmem_settings.py
│   ├── Micmem_SMC_main.py
│   ├── Micmen_generate_data.py
│   ├── mm_pseudo_data.csv
│   └── Posterior_Distributions.png
│
├── SMC_methanation/
│   ├── methanation_functions.py
│   ├── methanation_set_conditon.py
│   ├── methanation_set_likelihood.py
│   ├── SMC_methanation.py
│   ├── SMC_methanation_data.py
│   └── SMC_methanation_main.py
│
└── README.md
```

---

## Directory Roles

- `SMC_Algorithm/`  
  Algorithm figures used in the documentation  

- `SMC_example/`  
  Minimal working example based on the Michaelis–Menten model  

- `SMC_methanation/`  
  Code used in the publication (less modular and not intended as a template)  

---

## Example

`SMC_example` provides a complete and minimal implementation based on the Michaelis–Menten model.

Users are expected to use this example as the primary reference and modify it according to their specific problem.

---

## What to Modify

To adapt the code to a different problem, modify the following components:

### Model

- ODE or system dynamics  
- State variables and model parameters  

### Observation Model

- Definition of observed quantities  
- Measurement noise model (e.g., Gaussian noise)  

### Likelihood

- Implemented in `sim_particle`  
- Must return likelihood values  

### Parameters

- Select which parameters to estimate  
- Fix or estimate noise-related parameters if necessary  

### Settings

- `SMC_example/Micmem_settings.py`
  - SMC hyperparameters  
  - data configuration  

---

## Extensions

### Parallelization

This code is designed for computationally intensive estimation problems and assumes parallelization at the particle or sample level.

- Likelihood evaluations for each particle are independent  
- Parallelization can significantly reduce computation time  

By using parallelization libraries (e.g., Ray), the framework can handle:

- Larger numbers of particles  
- Increased data conditions  
- Higher-dimensional parameter spaces  

---

### Notes for Computationally Heavy Problems

When dealing with large-scale or computationally demanding problems, consider the following:

- ODE solver settings  
  Excessively strict tolerances may substantially increase computation time  

- Parameter identifiability  
  With limited or low-information data, estimation may become unstable  

- Parallelization overhead  
  Depending on problem size, parallelization may become inefficient  

- Memory usage  
  A large number of particles or stored intermediate results may cause memory bottlenecks  

---

## Summary

This code serves as a general template for parameter estimation problems, featuring:

- A modular structure that facilitates model replacement  
- A design assuming parallel computation  
- Scalability from small-scale to large-scale problems  

Users are expected to start from the example and extend the framework step by step according to their specific problem.