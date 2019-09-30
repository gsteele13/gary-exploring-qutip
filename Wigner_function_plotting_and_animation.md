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
# Not sure I like this, but it seems to be the norm...
from qutip import *

import numpy as np
import matplotlib.pyplot as plt
```

```python
from IPython.display import HTML
from matplotlib import animation
```

```python
plt.rcParams["animation.html"] = "jshtml"
```

```python
from ipywidgets import interact
```

# Starting point

http://qutip.org/tutorials.html

In particular, the lectures at the bottom!



# Plotting Wigner functions

Start trying to make harmonic oscillator states and plotting their Wigner functions

https://nbviewer.jupyter.org/github/jrjohansson/qutip-lectures/blob/master/Lecture-16-Gallery-of-Wigner-functions.ipynb

```python
N = 20
```

```python
#psi = basis(N, 5)
#psi = coherent(N,2)
psi = coherent(N,-3) +  coherent(N,3)
plot_wigner(psi)
```

# Wigner animations

Based on mesolve! 

##  Undamped coherent state (TDSE)

https://nbviewer.jupyter.org/github/jrjohansson/qutip-lectures/blob/master/Lecture-9-Squeezed-states-of-harmonic-oscillator.ipynb

```python
N = 35
w = 1 * 2 * np.pi              # oscillator frequency
tlist = np.linspace(0, 4, 101) # periods
```

```python
# operatorsss
a = destroy(N)
n = num(N)
x = (a + a.dag())/np.sqrt(2)
p = -1j * (a - a.dag())/np.sqrt(2)
```

```python
# the quantum harmonic oscillator Hamiltonian
H = w * a.dag() * a
```

```python
c_ops = []

# uncomment to see how things change when disspation is included
# c_ops = [np.sqrt(0.25) * a]
```

```python
psi0 = coherent(N, 2.0)
```

```python
result = mesolve(H, psi0, tlist, c_ops, [])
```

```python
n = 20
plot_wigner(result.states[0]);
```

```python
fig, ax = plt.subplots(1, 1, figsize=(6,6))

def update(n):
    ax.cla()
    plot_wigner(result.states[n],fig=fig,ax=ax)

#interact(update,n=(0,len(result.states)))
anim = animation.FuncAnimation(fig, update, frames=len(result.states))

plt.close(fig)
```

```python
anim
```

```python
def update2(n=0):
    plot_wigner(result.states[n])

interact(update2,n=(0,len(result.states)))

```

##  Damped coherent state (Lindblad)


```python
c_ops = [1.0 * a]
```

```python
psi0 = coherent(N, 2.0)
```

```python
result = mesolve(H, psi0, tlist, c_ops, [])
```

```python
fig, ax = plt.subplots(1, 1, figsize=(6,6))
def update(n):
    ax.cla()
    plot_wigner(result.states[n],fig=fig,ax=ax)
#interact(update,n=(0,len(result.states)))
anim = animation.FuncAnimation(fig, update, frames=len(result.states))

plt.close(fig)
```

```python
anim
```

Calculate some expectation values:

```python
plt.subplots(figsize=(14,4))
plt.subplot(121)
plt.plot(expect(x,result.states),label="x")
plt.plot(expect(p,result.states),label="p")
plt.axhline(0,ls=':',c='grey')
plt.legend()
plt.subplot(122)
plt.plot(expect(a.dag()*a,result.states),label=r"n")
plt.axhline(0,ls=':',c='grey')
plt.legend()
```
