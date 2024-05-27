---
hide:
  - navigation
---

# Řešení úloh lineárního programování pomocí knihovny PuLP

<!-- ![Python](https://img.shields.io/badge/python-3670A0?style=for-the-badge&logo=python&logoColor=ffdd54) -->

Tento tutoriál se zaměřuje na práci s knihovnou `PuLP`. `PuLP` je knihovna pro Python, která umožňuje řešit úlohy lineárního programování. Samotný materiál se problematikou lineárního programování nazabývá do hloubky. Předpokládá se základní znalost jazyka Python.

Zárověň se předpokládá, že čtenář umí pracovat s knihovnou `matplotlib`, která je využita pro vizualizaci výsledků a s knihovnou `numpy`, která je využita pro práci s maticemi.

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

Následně je nutné knihovnu PuLP a další potřebné knihovny importovat.

```python
from pulp import *
import matplotlib.pyplot as plt # (1)!
import numpy as np # (2)!
```

1. Knihovna pro vizualizaci výsledků
2. Knihovna pro práci s maticemi

## Vytvoření úlohy lineárního programování

Model úlohy lineárního programování se skládá z několika částí.

- **LpProblem** - Definice úlohy
- **LpVariable** - Definice proměnných
- **LpAffineExpression** - Definice účelové funkce
- **LpConstraint** - Definice omezení

## LpProblem



```python
prob = LpProblem("Název úlohy", LpMinimize)
```

## LpVariable

```python
x1 = LpVariable("x1", lowBound=0)
x2 = LpVariable("x2", lowBound=0)
...
```

## LpAffineExpression

```python
f = LpAffineExpression([(x1, 2), (x2, 3)])
prob += f
```

## LpConstraint

```python
c1 = LpConstraint(x1 + x2, sense=LE, rhs=4)
c2 = LpConstraint(x1 - x2, sense=GE, rhs=1)
...

prob += c1
prob += c2
```

## Řešení úlohy

```python
status = prob.solve()
print(LpStatus[status])
print(f"x1 = {value(x1)}, x2 = {value(x2)}")
print(f"Optimalizovaná hodnota: {value(prob.objective)}")
```

```plaintext title="Output"
Optimal
x1 = 0.0, x2 = 0.0
Optimalizovaná hodnota: 0.0
```

### Řešení úlohy

```python
status = prob.solve()
print(LpStatus[status])
print(f"x1 = {value(x1)}, x2 = {value(x2)}")
print(f"Optimalizovaná hodnota: {value(prob.objective)}")
```

```plaintext title="Output"
Optimal
x1 = 0.0, x2 = 0.0
Optimalizovaná hodnota: 0.0
```

## Příklady
