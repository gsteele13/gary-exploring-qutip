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
import numpy as np
import pylab as plt
from qutip import *
```

```python
N = 5
w = 1 * 2 * np.pi              # 1 Hz oscillator frequency
Q = 40
kappa = w / Q

# Set up some operators
a = destroy(N)
n = num(N)
x = (a + a.dag())/np.sqrt(2)
p = -1j * (a - a.dag())/np.sqrt(2)
H = w * a.dag() * a

#c_ops = [np.sqrt(kappa)*a, np.sqrt(kappa*10)*x]
#c_ops = [np.sqrt(kappa)*a]
c_ops = []

psi0 = fock(N,0)
tlist = np.linspace(0, 10, 500)
```

```python
corr = correlation_2op_1t(H, psi0, tlist, c_ops, x, x, solver='me')
```

```python
wlist1, spec1 = spectrum_correlation_fft(tlist, corr)
```

```python
plt.figure(figsize=(14,8))
plt.plot(tlist, np.real(corr))
plt.plot(tlist, np.imag(corr))
plt.show()

plt.figure(figsize=(14,8))
plt.plot(wlist1, np.abs(spec1))
plt.show()
```

```python
from qutip.ipynbtools import version_table

version_table()
```

```python

```
