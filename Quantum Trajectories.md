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

```python hide_input=true
# Not sure I like this, but it seems to be the norm...
from qutip import *

import numpy as np
import matplotlib.pyplot as plt

from IPython.display import HTML
from matplotlib import animation

plt.rcParams["animation.html"] = "jshtml"
plt.rcParams["figure.dpi"] = 75

from ipywidgets import interact
```

```python hide_input=true
# A handy function for later
def plot_x_and_E(states):
    plt.subplots(figsize=(12,4),dpi=100)
    plt.subplot(121)
    ex = expect(x,states)
    std = variance(x,states)
    plt.plot(t,ex)
    plt.fill_between(t, ex+std, ex-std, alpha=0.2)
    plt.subplot(122)
    ex = expect(H,states)
    std = variance(H,states)
    plt.plot(t,ex)
    plt.fill_between(t, ex+std, ex-std, alpha=0.2)

```

# The Hamiltonian and initial state: A Coherent state

```python
# Basic parameters
N = 35
w = 1 * 2 * np.pi              # 1 Hz oscillator frequency
t = np.linspace(0, 10, 401) # 4 periods for the time trace

Q = 10
kappa = w / Q

# Set up some operators
a = destroy(N)
n = num(N)
x = (a + a.dag())/np.sqrt(2)
p = -1j * (a - a.dag())/np.sqrt(2)
H = w * a.dag() * a

# The initial state
psi0 = coherent(N, 2.0)
```

# Decay: Collapse = annihilation operator

```python
# The collapse operator 
c_ops = [np.sqrt(kappa) * a]
```

## Lindblad

```python
me1 = mesolve(H, psi0, t, c_ops)
```

```python
plot_x_and_E(me1.states)
```

This is pretty much what we expect: the state decays as a function of time. 

```python
def update(i=0):
    plot_wigner(me1.states[i])
interact(update,i=(0,len(t)));
```

## Trajectories

```python
mc1 = mcsolve(H, psi0, t, c_ops)
```

The first ensemble member:

```python
def update(j=0):
    plot_x_and_E(mc1.states[j])
interact(update,j=(0,499));
```

What is going on? In the MC simulation, all ensemble members are the same since the coherent states commutes with the collapse operator!

```python
def update(i=0,j=0):
    plot_wigner(mc1.states[j][i])
interact(update,i=(0,len(t)),j=(0,499));
```

# Measure photon number, no decay

```python
# The collapse operator 
c_ops = [np.sqrt(kappa) * a.dag()*a]
```

## Lindblad

```python
me2 = mesolve(H, psi0, t, c_ops)
```

```python
plot_x_and_E(me2.states)
```

```python
def update(i=0):
    plot_wigner(me2.states[i])
interact(update,i=(0,len(t)));
```

Of course! Measuring photon number increases the uncertainty in phase!!!!!


## Trajectories

```python
mc2 = mcsolve(H, psi0, t, c_ops, ntraj=[500])
```

```python
def update(j=0):
    plot_x_and_E(mc2.states[j])
interact(update,j=(0,499));
```

```python
def update(i=0,j=0):
    plot_wigner(mc2.states[j][i])
interact(update,i=(0,len(t)-1),j=(0,499));
```

# Measure photon number, with decay, equal rates

```python
# The collapse operator 
c_ops = [np.sqrt(kappa) * a.dag()*a, np.sqrt(kappa) * a]
```

## Lindblad

```python
me3 = mesolve(H, psi0, t, c_ops)
```

```python
plot_x_and_E(me3.states)
```

```python
def update(i=0):
    plot_wigner(me2.states[i])
interact(update,i=(0,len(t)));
```

## Trajectories

```python
mc2 = mcsolve(H, psi0, t, c_ops, ntraj=[500])
```

```python
def update(j=0):
    plot_x_and_E(mc2.states[j])
interact(update,j=(0,499));
```

```python
def update(i=0,j=0):
    plot_wigner(mc2.states[j][i])
interact(update,i=(0,len(t)-1),j=(0,499));
```

# Measure photon number, with decay:  measure photons 10 times faster

```python
# The collapse operator 
c_ops = [np.sqrt(kappa*10) * a.dag()*a, np.sqrt(kappa) * a]
```

## Lindblad

```python
me4 = mesolve(H, psi0, t, c_ops)
```

```python
plot_x_and_E(me4.states)
```

```python
def update(i=0):
    plot_wigner(me2.states[i])
interact(update,i=(0,len(t)));
```

## Trajectories

```python
mc2 = mcsolve(H, psi0, t, c_ops, ntraj=[500])
```

```python
def update(j=0):
    plot_x_and_E(mc2.states[j])
interact(update,j=(0,499));
```

```python
def update(i=0,j=0):
    plot_wigner(mc2.states[j][i])
interact(update,i=(0,len(t)-1),j=(0,499));
```

```python

```
