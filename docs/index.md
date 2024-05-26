---
hide:
  - navigation
---

# Tutoriál k řešení úloh lineárního programování pomocí knihovny PuLP

<!-- ![Python](https://img.shields.io/badge/python-3670A0?style=for-the-badge&logo=python&logoColor=ffdd54) -->

Tento tutoriál se zaměřuje na práci s knihovnou PuLP. PuLP je knihovna pro Python, která umožňuje řešit úlohy lineárního programování. Samotný materiál se problematikou lineárního programování nazabývá do hloubky. Předpokládá se základní znalost jazyka Python.

## Základy použití PuLP

### Instalace a import

Pro začátek je nutné nainstalovat knihovnu PuLP pomocí některého balíčkovacího systému pro Python. Následující příklady ukazují instalaci pomocí balíčkovacích systémů Pip, Conda a Poetry.

=== "Pip"
    ```bash
    pip install pulp
    ```
=== "Conda"
    ```bash
    conda install pulp
    ```
=== "Poetry"
    ```bash
    poetry add pulp
    ```

Následně je nutné knihovnu PuLP importovat do skriptu.

```python
from pulp import *
```

## Vytvoření úlohy lineárního programování

### Proměnné

```python
x1 = LpVariable("x1", lowBound=0)
x2 = LpVariable("x2", lowBound=0)
x3 = ...
```

### Účelová funkce

```python
prob += c1*x1 + c2*x2
```

### Omezení

```python
prob += a1*x1 + a2*x2 <= b1
prob += a3*x1 + a4*x2 >= b2
prob += a5*x1 + a6*x2 == b3
prob += ...
```

### Řešení úlohy

```python
status = prob.solve()
print(LpStatus[status])
print(f"x1 = {value(x1)}, x2 = {value(x2)}")
print(f"Optimalizovaná hodnota: {value(prob.objective)}")
```

```plaintext
Optimal
x1 = 0.0, x2 = 0.0
Optimalizovaná hodnota: 0.0
```

## Příklady
