## Chebyshev-Based Differential Equation Solving with QSVT

This tutorial demonstrates how to use **Quantum Singular Value Transformation (QSVT)** as the core engine of a **Chebyshev-based differential equation solver**.

The overall pipeline is:

- We start from a **differential equation** that is discretized in a **Chebyshev polynomial basis**, yielding a linear system: $Ax = b$
- The matrix $A$ encodes the Chebyshev-discretized operator (e.g., a differential operator with boundary conditions).
- We implement a **QSVT-based linear system solver** that approximates the inverse function $(f(x) \approx \frac{1}{x})$ on the spectrum of a suitably scaled operator.
- The method currently performs the **Chebyshev transform** (between physical and Chebyshev coefficient space) **classically** using FFT-based routines (not shown explicitly here). The long-term goal is to replace this step with a **fully quantum Chebyshev transform circuit**, so that the entire pipeline is quantum-native.

Key features of this notebook:

- **QSVT as a linear system solver.** We implement QSVT for both **scalars** and **matrices**, using a polynomial approximation to $\frac{1}{x}$ built by **PyQSP**.
- **Non-Hermitian support via Hermitian dilation.** General (non-Hermitian) matrices are treated via a Hermitian dilation, allowing us to apply standard QSVT techniques.
- **Block encoding via PennyLane.** All quantum logic is written in **Qiskit**, but the block encoding of matrices is delegated to **PennyLane’s `qml.BlockEncode()`**, which provides a single-ancilla block-encoding.
- **Phase-convention conversion.** PyQSP returns phases in the **Wx convention**; we convert these phases internally to the **Reflection convention** used by our QSVT implementation.
- **Measurement and interpretation.** We show how to interpret the measurement probabilities to recover the normalized solution vector, including the relevant normalization factors.
- **Numerical observations.** All tests here are run on a **noiseless `AerSimulator`**, and the QSVT-based solutions match classical solutions with high precision. In practice, we observe that the method remains stable even when the matrix condition number is up to roughly **twice the chosen $\kappa$** used for phase generation.

We will build up the solver in stages:

1. Generate QSVT phases for the function $(f(x) \approx \frac{1}{x})$.
2. Implement a reusable **QSVT helper class** supporting scalar and matrix inputs.
3. Construct test matrices $A$ and right-hand sides $\ket{b}$.
4. Use QSVT to approximate $A^{-1}$ directly.
5. Solve linear systems with **full complex amplitudes** (tomography-based readout).
6. Solve **real-valued** linear systems using a **Hadamard-test-style LCU construction**, so that the solution can be recovered directly from measurement statistics.
7. Conclude with a **roadmap for future work**, including quantum Chebyshev transforms and multi-ancilla block encodings.

## Acknowledgements

I gratefully acknowledge the foundational tools and open-source frameworks that made this work possible, including:  
**Qiskit** for quantum circuit synthesis and simulation, **Pennylane** for block-encoding support, and **PYQSP** for phase-sequence generation in Quantum Singular Value Transformation (QSVT).

This work also benefited from discussions with my supervisor **Prof. Anil Shaji** (Professor at School of Physics, IISER TVM) and community resources in quantum algorithms, spectral methods, and block-encoding techniques.  
The implementation and tutorial presented here were developed independently by **Sharon Xalxo** (2025) as part of ongoing research into quantum algorithms for differential equations, Chebyshev spectral discretizations, 
and QSVT-based linear system solvers.

Users of this repository or associated implementations are kindly asked to cite this work using the recommended citation or Bib\TeX{} entry provided in the documentation.

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
  $\gamma = 1 + v^\top z$ and reconstruct the final Chebyshev coefficients and nodal solutions.


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
  note         = {QSVT-based spectral method implementation for differential equations. \href{https://github.com/SharonXal/Differential-Equation-Solver}{Code}},
}
