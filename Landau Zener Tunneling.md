---
jupyter:
  jupytext:
    formats: ipynb,md
    text_representation:
      extension: .md
      format_name: markdown
      format_version: '1.1'
      jupytext_version: 1.2.2
  kernelspec:
    display_name: Python 3
    language: python
    name: python3
---

```python
from qutip import *
import numpy as np
import matplotlib.pyplot as plt
```

# Landau-Zener tunneling

In this notebook, I will try to explore non-adiabatic time evolution using a fast ramp through an avoided crossing, which should result in Landau-Zener tunneling:

https://en.wikipedia.org/wiki/Landau%E2%80%93Zener_formula

For a slow (diabatic) ramp, we should remain in the ground state, whereas a fast (non-adiabatic) ramp, we should end up in the excited state.


## Hamiltonian

Two level system with detuning and tunneling:

$$
|L\rangle  = 
\begin{pmatrix}
1 \\
0
\end{pmatrix}
$$

$$
|R\rangle  = 
\begin{pmatrix}
0 \\
1
\end{pmatrix}
$$

Time-independent tunneling with amplitude $\Delta$:

$$
H_0 = 
\begin{pmatrix}
0 & \Delta \\
\Delta &  0 
\end{pmatrix}
$$

Time-dependent detuning:

$$ 
H_1 = 
\begin{pmatrix}
\epsilon (t) & 0 \\
0 & 0 
\end{pmatrix}
$$

```python
N = 2

# Pick out time and energy  units: Delta = 1
H_0 =  sigmax()

# The  operator for our  time dependent Hamiltonian
H_1 = Qobj([[1,0],[0,0]])

# The function for our time dependent Hamiltonian. 
# We will take the shortcut  and use global variables to 
# define it's  parameters instead of using args
# Our function will be a ramp from -eps_amp to eps_amp in  
# a total ramp time of total_time

def f(t,args):
    return -eps_amp + 2*eps_amp * t/total_time

# The Hamiltonian object
H  =  [H_0, [H_1,  f]]

# Our initial state: always  start  in left well

psi0 = basis(N,0)
```

```python
H_0
```

```python
H_1
```

```python
psi0
```

```python
def calculate_crossing():
    t = np.linspace(0,total_time,100)
    r = sesolve(H,psi0,t)
    s =  r.states
    print("Initial state:\n")
    print(s[0])
    print()
    print("Final state:\n")
    print(s[-1])
    print()
    print("Final state probabilities:\n")
    print(np.abs(s[-1])**2)
    return s[-1], s
```

<!-- #region -->
# Try some ramps

## Slow ramp

Start with negative $\epsilon$ in the left well, then ramp epsilon slowly,  see where we end up. If total time is long enough, I should end up in right well.


Total time will be 100 times slower than the characteristic time $1/\Delta$. 
<!-- #endregion -->

```python
eps_amp = 10
total_time = 100
f,s = calculate_crossing()
```

OK, good: this is a slow ramp, we should be diabatic.  We start in the ground state at $t=0$ (left well) and end up in the new ground state (right well) at the end of the ramp.


## Fast ramp

Let's try a fast ramp: 100 times faster than the characteristic time.

```python
eps_amp = 10
total_time = 1e-2
f,s = calculate_crossing()
```

OK, this works: with a fast ramp, we end up in the excited state (ie. we stay in the left well). Nice!
