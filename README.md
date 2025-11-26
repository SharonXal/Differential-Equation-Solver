# Chebyshev-Based Differential Equation Solving with QSVT

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
