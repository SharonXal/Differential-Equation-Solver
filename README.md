# Differential-Equation-Solver
## 0. Chebyshev-Space Formulation and Linear System Construction

Before we focus on the QSVT implementation itself, it is useful to spell out how the linear system arises from the Chebyshev spectral discretization of a differential equation, and how the quantum output is interpreted back in physical space.

### 0.1 Differential Equation and Chebyshev Domain

The motivating differential equation in this project is the simple first-order ODE:

$$
u'(x) = \alpha u(x) \space \space (\alpha > 1), \qquad x \in [-1,1], \qquad u(-1) = 0.367.
$$

The choice of the interval $[-1,1]$ matches the natural domain of Chebyshev polynomials $T_n(x)$.  
We work with a truncated expansion of degree $N$ (e.g., $N=3$ for a 4×4 toy model), so that

$$
u(x) \approx \sum_{n=0}^N a_n T_n(x).
$$

There are two equivalent representation spaces:

**Nodal (collocation) space:** values of $u(x)$ at Chebyshev–Gauss–Lobatto nodes

$$
x_j = \cos\left(\frac{j\pi}{N}\right), \qquad j = 0,1,\dots,N,
$$

collected into a vector

$$
u_{\text{nodal}} = [u(x_0), u(x_1), \dots, u(x_N)]^\top.
$$

**Coefficient (spectral) space:** Chebyshev coefficients

$$
a = [a_0, a_1, \dots, a_N]^\top.
$$

They are related by the discrete Chebyshev transform:

$$
u_{\text{nodal}} = T a, \qquad a = T^{-1} u_{\text{nodal}},
$$

where $T$ is the $(N+1)\times(N+1)$ Chebyshev Transform (DCT-I) matrix with entries $T_{j,k} = T_k(x_j)$.



### 0.2 Differentiation and Construction of the Linear System

In coefficient space, differentiation acts via the matrix $D_c$ derived from Chebyshev recurrence:

$$
a'_{\text{spec}} = D_c \, a.
$$

Imposing $u'(x) = \alpha u(x)$ in spectral space:

$$
D_c \, a = \alpha a.
$$

Rewriting gives the linear system:

$$
(D_c - \alpha I)\, a = 0.
$$

This is ill-posed because the trivial solution $a = 0$ satisfies it.  
We fix this by imposing the boundary condition.



We enforce the initial condition at $x = -1$.  
For $N=3$, the Chebyshev–Gauss–Lobatto nodes are:

$$
x_0 = 1, \quad x_1 = \tfrac{1}{2}, \quad x_2 = -\tfrac{1}{2}, \quad x_3 = -1.
$$

The last node corresponds to $x=-1$.

Define the Lagrange cardinal vector:

$$
\ell = [0, 0, 0, 1]^\top.
$$

The boundary condition becomes the nodal vector:

$$
r = 0.367 \, \ell = [0,0,0,0.367]^\top.
$$

Transform to coefficient space:

$$
c = T^{-1} r.
$$

We now solve for an intermediate vector $z$:

$$
A z = c, \qquad A := D_c - \alpha I.
$$

This is the linear system targeted by the QSVT solver.

- Classically:
  $$
  z = A^{-1} c.
  $$

- Quantum (QSVT):
  Block-encode $A$ and apply the QSVT polynomial for $\frac{1}{x}$, so the amplitudes encode $z$ up to normalization.



### 0.3 Normalization and Final Chebyshev Coefficients

Sherman–Morrison rank-one correction gives a scalar normalization:

$$
\gamma = 1 + v^\top z.
$$

Final Chebyshev coefficients:

$$
a = \frac{1}{\gamma} z.
$$

Quantum produces $z$ (up to scale).  
Classical post-processing computes $\gamma$ and rescales.



### 0.4 Data Extraction and Reconstruction in Physical Space

There are two extraction modes:

1. **Statevector / tomography mode**

   The first column of the unitary (or output state) gives amplitudes proportional to $z$.
   After scaling: $a = \frac{z}{\gamma}$

2. **Measurement-based, real-valued mode**

   A Hadamard-test / LCU construction encodes the real part of $z$ directly into measurement probabilities.
   After scaling, we recover $z$ without tomography.



Once $a$ is known, the physical-space solution is:

$$
u_{\text{nodal}} = T a.
$$

This approximates $u(x)$ at the collocation points and constitutes the final output of the Chebyshev spectral solver.


