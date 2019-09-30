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

# Quantum Trajectories

https://www.nature.com/articles/nature05589

https://nbviewer.jupyter.org/github/jrjohansson/qutip-lectures/blob/master/Lecture-6-Quantum-Monte-Carlo-Trajectories.ipynb



## Decaying coherent state

```python
N = 30               # number of basis states to consider
kappa = 1.0/0.129   # coupling to heat bath

tlist = np.linspace(0,0.6,100)

a = destroy(N)      # cavity destruction operator
H = a.dag() * a     # harmonic oscillator Hamiltonian

psi0 = coherent(N,2)

c_op_list = [np.sqrt(kappa)* a]
mc = mcsolve(H, psi0, tlist, c_op_list)
```

```python
plt.plot(expect(a.dag()*a,mc.states[0]))
```

What!? Why are there no quantum jumps in the ensemble members?!!?


## Decaying Fock state

```python
psi0 = fock(N,4)

c_op_list = [np.sqrt(kappa)* a]
mc = mcsolve(H, psi0, tlist, c_op_list)
```

```python
plt.plot(expect(a.dag()*a,mc.states[0]))
```

OMG, what is going on? 

As Rik pointed out, coherent states always  decay smoothly... since the a operator commutes with a coherent state, therefore after collapsing and renormalising, nothing happens!

But: what is going on in the Haroche experiment then: they started with a coherent state!!!!

Answer is actually a bit subtle! But first, let's try some stuff out. 


## Photon number collapse operator on a coherent state

```python
N = 30               # number of basis states to consider
kappa = 1.0/0.129   # coupling to heat bath

tlist = np.linspace(0,0.6,100)

a = destroy(N)      # cavity destruction operator
H = a.dag() * a     # harmonic oscillator Hamiltonian

psi0 = coherent(N,2)

c_op_list = [np.sqrt(kappa)* a, np.sqrt(10*kappa) * H]
mc = mcsolve(H, psi0, tlist, c_op_list)
```

```python
plt.figure(figsize=(18,10))
for i in range(50):
    plt.plot(expect(a.dag()*a,mc.states[i]),'.-')

```

# Position measurement on a coherent state

```python
N = 30               # number of basis states to consider
kappa = 1.0/0.129   # coupling to heat bath
kappa /= 10

tlist = np.linspace(0,0.6,100)

a = destroy(N)      # cavity destruction operator
H = a.dag() * a     # harmonic oscillator Hamiltonian

psi0 = coherent(N,2)

c_op_list = [np.sqrt(kappa)* (a+a.dag())]
mc = mcsolve(H, psi0, tlist, c_op_list)
```

```python

def update(i=0):
    plt.figure(figsize=(18,10))
    plt.plot(expect(a.dag()*a,mc.states[i]),'.-')
    
interact(update,i=(0,len(mc.states)-1))
```

```python

def update(i=0):
    plt.figure(figsize=(18,10))
    plt.plot(expect(a.dag()+a,mc.states[i]),'.-')
    
interact(update,i=(0,len(mc.states)-1))
```

```python
result = mesolve(H, psi0, tlist, c_op_list)
```
