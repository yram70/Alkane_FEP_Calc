
# Alkane Hydration Free Energy Calculations (C1–C20)

## Introduction

This repository contains molecular simulation input files, scripts, and output data
 for computing the hydration free energies (ΔG_hyd) of linear alkanes (C1–C20) in water, 
 with hexane (C6) used as an example system.

The simulations are performed using free energy perturbation (FEP) methods with the
 HH-Alkane force field and the TIP4P/2005 water model. Both shifted and unshifted
 Lennard-Jones potentials are included to evaluate the impact of potential truncation
 methods on calculated free energies.

---

##️ Methodology

- **Simulation engine:** LAMMPS  
- **Force field:** HH-Alkane (united atom)  
- **Water model:** TIP4P/2005  
- **Free energy method:** FEP with soft-core Lennard-Jones potential

The hydration free energy is calculated by gradually turning off interactions
 of the solute using a coupling parameter `lambda`, ranging from 0 to 1. 

---

## Repository Structure

```
Alkane_FEP_Calc    #alkane-hydration-free-energy/
│
├── Shifted/ # Subfolder for shifted potential C6 (Hexane)
│ ├── fep01/ # forward path (lambda 0 to 1)
│ │ ├── fep01_result.fep/ # result output file 
│ │ ├── input_script.in/  # FEP simulation input scripts
│ │ └── output_slurm.out/ # Simulation outputs
│ └── fep10/ # backward path (lambda 1 to 0)
│	├── fep01_result.fep/ # result output file 
│   ├── input_script.in/  # FEP simulation input scripts
│   └── output_slurm.out/ # Simulation outputs
│
├── Unshifted/ # Subfolder for unshifted potential C6 (Hexane)
│ ├── fep01/   # forward path (lambda 0 to 1)
│ │ ├── fep01_result.fep/ # result output file 
│ │ ├── input_script.in/  # FEP simulation input scripts
│ │ └── output_slurm.out/ # Simulation outputs
│ └── fep10/   # backward path (lambda 1 to 0)
│	├── fep01_result.fep/ # result output file 
│   ├── input_script.in/  # FEP simulation input scripts
│   └── output_slurm.out/ # Simulation outputs
│
└── README.md
```
> ⚠️ *Note: Due to size constraints, the LAMMPS trajectory/data files are not included in this repository.*

### Example System Setup (Hexane + Water)

| Alkane | Box Length [Å] | Number of Water Molecules | Number of Alkane Molecules |
|--------|----------------|---------------------------|----------------------------|
| C6     | 36             | 1500                      | 1                          |

---

## Input Script Breakdown (`input_script.in`)

### Key Parameters & Commands:

- **Lambda control:**
  ```lammps
  variable lambda equal ramp(0.0, 0.125)
  ```
  Sets the coupling parameter range. You can adjust based on available computational resources.

- **Adaptation during run:**
  ```lammps
  fix FADAPT all adapt/fep 4000000 pair lj/cut/soft lambda 1*2 3*4 v_lambda after yes
  ```
  Applies perturbation by modifying potential parameters during the run.

- **Delta lambda step:**
  ```lammps
  variable dlambda equal 0.025
  ```
  Smaller values yield higher accuracy but increase computation time. Choose an optimal value for your system.

- **Free energy calculation:**
  ```lammps
  compute FEP all fep 300 pair lj/cut/soft lambda 1*2 3*4 v_dlambda volume yes tail no
  ```
  Computes pair potential energy differences without changing atomic positions.

- **Time averaging and output:**
  ```lammps
  fix fFEP all ave/time 1000 3000 4000000 c_FEP[*] file fep01.fep
  ```
  Averages FEP data over time and writes to `fep01.fep`.

---

## Sample Output (`fep01.fep`)

```
# Time-averaged data for fix fFEP
# TimeStep c_FEP[1]      c_FEP[2]     c_FEP[3]
4000000    0.00206142     45314.0      45470.8
8000000    0.0136849      44451.9      45482.1
12000000   0.0382574      42658.1      45477.5
16000000   0.080745       39732.7      45474.3
20000000   0.144005       35773.2      45492.4
```

For more information about the FEP compute and its output, see the [LAMMPS documentation](https://docs.lammps.org/compute_fep.html).

---

## Post-Processing

After collecting the FEP output, you can integrate the results to compute the hydration free energy.

---

## Contact

For questions, please contact:

**Yalda Ramezani**  
PhD Candidate, Chemical and Biomolecular Engineering  
Ohio University  
yr9770@gmail.com

---

