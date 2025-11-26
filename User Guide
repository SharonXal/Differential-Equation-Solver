## User Guide

### Author
This repository and accompanying tutorial notebooks were developed by **Sharon Xalxo** as part of ongoing research on quantum algorithms for differential equations, Chebyshev spectral methods, and Quantum Singular Value Transformation (QSVT).



### Overview

This repository contains a complete, research-grade implementation of a **Chebyshev–QSVT differential equation solver**, including:

- Construction of Chebyshev discretizations for linear ODEs/PDEs  
- Hermitian dilation and block encoding (Qiskit + PennyLane hybrid)  
- QSVT phase synthesis (PyQSP)  
- Linear-system solving via QSVT  
- Real-valued Hadamard-test extraction circuits  
- Sherman–Morrison normalization for boundary conditions  
- Classical post-processing utilities (Chebyshev transforms, Clenshaw evaluation, derivatives)

The code is written to match the structure and style of modern quantum-algorithm research papers and tutorials.



### Repository Structure

- **QSVT core implementation** — the main polynomial transformation engine  
- **Block encoding utilities** — PennyLane-based single-ancilla block encodings  
- **Chebyshev tools** — nodes, DCT-I transforms, Clenshaw evaluation, derivatives  
- **Differential-equation solvers** — ODE templates, normalization logic  
- **Real-valued extraction** — Hadamard-test LCU solver for real solutions  
- **Examples / Tutorials** — annotated Jupyter notebooks reproducing experiments  

The notebooks are self-contained and can be executed directly on standard Qiskit simulators (e.g., `AerSimulator`).



### Usage Notes

- Ensure that you have recent versions of **Qiskit**, **PennyLane**, **PyQSP**, **NumPy**, and **Matplotlib** installed.  
- The solver outputs both:
  - the **statevector-based solution** (high precision, noiseless mode), and  
  - the **measurement-based real solution** (ancilla-structured Hadamard-test output).  
- For differential equations of the form $u'(x) = \alpha\,u(x)$, the provided Sherman–Morrison utilities compute the correct normalization factor
  $$
  \gamma = 1 + v^\top z,
  $$
  and reconstruct the final Chebyshev coefficients and nodal solutions.



### How to Cite This Work

If you use this repository, its methods, or the QSVT–Chebyshev linear-system solver in academic work, please cite it as follows:

**Standard citation text:**

> This work uses the Chebyshev–QSVT differential equation solver developed by  
> **Sharon Xalxo (2025)**, including block-encoding methods, PyQSP-derived phase sequences, and real-valued Hadamard-test QSVT extraction circuits.


### BibTeX Citation

You may use the following BibTeX entry:

```bibtex
@misc{Sxal2025QSVTv1,
  author       = {Sharon Xalxo},
  title        = {Chebyshev-QSVT Differential Equation Solver: 
                  Block Encoding, Phase Synthesis, and 
                  Real-Valued Linear System Extraction},
  year         = {2025},
  howpublished = {GitHub repository},
  note         = {QSVT-based spectral method implementation for differential equations},
}
