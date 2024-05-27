---
author: Petr Kotlan
hide:
  - navigation
---

# __Řešení úloh lineárního programování pomocí knihovny PuLP__

<!-- ![Python](https://img.shields.io/badge/python-3670A0?style=for-the-badge&logo=python&logoColor=ffdd54) -->

__Vypracoval__: Petr Kotlan

Materiál vznikl v rámci bakalářské práce *Optimalizace investičních prostředků z hlediska výnosu fotovoltaických elektráren*.

Tento tutoriál se zaměřuje na práci s knihovnou __PuLP__, která umožňuje řešit úlohy lineárního programování. Samotný materiál se problematikou lineárního programování nazabývá, pouze implementac9 a řešením úloh.
Předpokládá se základní znalost jazyka Python.

Zárověň se předpokládá, že čtenář umí pracovat s knihovnou `matplotlib`, která je využita pro vizualizaci výsledků.
 <!-- a s knihovnou `numpy`, která je využita pro práci s maticemi. -->

<!-- [Download :material-download:](index.txt){ .md-button } -->

## __Základy použití PuLP__

V této části si ukážeme základní použití knihovny __PuLP__ na jednoduchém příkladu. Jak pracovat s jednotlivými třídami, jak vytvořit úlohu lineárního programování a jak ji vyřešit.

Pro názornost si vše vyzkoušíme na následujícím příkladu.

$$
\begin{align*}
\max \quad & 600x_1 + 400x_2 \\
\text{za podmínek} \quad & x_1 + 0.25x_2 \leq 45 \\
& 2x_1 + 0.25x_2 \leq 45 \\
& 4x_1 + 2x_2 \leq 100 \\
& 3x_1 + x_2 \leq 300 \\
& x_1 + 4x_2 \leq 50 \\
& x_1, x_2 \geq 0
\end{align*}
$$

Jen pro motivaci si představme, že se jedná o podnik vyrábějící dva druhy výrobků. Výrobek 1 má zisk 600 Kč a výrobek 2 má zisk 400 Kč. Dále máme několik omezení, například omezení výrobních kapacit, které jsou vyjádřeny v jednotkách výrobků. Cílem je maximalizovat zisk.

### Instalace a import

Pro začátek je nutné nainstalovat knihovnu __PuLP__ pomocí některého balíčkovacího systému pro Python. Následující příklady ukazují instalaci pomocí balíčkovacích systémů Pip, Conda a Poetry.

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

Následně je nutné knihovnu __PuLP__ a další potřebné knihovny importovat.

```python
from pulp import *
import matplotlib.pyplot as plt # (1)!
```

1. Knihovna pro vizualizaci výsledků
<!-- import numpy as np # (2)!
2. Knihovna pro práci s maticemi  -->

### __Vytvoření úlohy lineárního programování__

Model úlohy lineárního programování se skládá z několika částí.

- __LpProblem__ - Definice úlohy
- __LpVariable__ - Definice proměnných
- __LpAffineExpression__ - Lineární kombinace proměnných
- __LpConstraint__ - Definice omezení

#### __LpProblem__

Základem celého problému je instance třídy `LpProblem`, která reprezentuje celou úlohu.
Do této instance se následně přidávají proměnné, omezení a cílová funkce.
Samotná třída `LpProblem` má dva nastavitelné parametry.

- `name` - Název úlohy
- `sense` - Typ optimalizace

Parametr `name` není nijak důležitý pro samotnou optimalizaci. Má své využití při ukládání problému do souboru, které si ukážeme později.

Parametr `sense` určuje o jaký typ optimalitace se jedná. Typ tohoto parametru je `int`, ale při řešení model vrátí chybovou hlášku, pokud `sense` není `1` nebo `-1`. __PuLP__ má pro tento parametr připravené dvě konstanty `LpMinimize` a `LpMaximize`, které reprezentují minimalizaci a maximalizaci. Defaultní hodnota je `LpMinimize`.

```python title="Vytvoření instance třídy LpProblem"
prob = LpProblem("Max_výroby", LpMaximize) # (1)!
```

1. Výtah z dokumentace. [Odkaz na dokumentaci](https://coin-or.github.io/pulp/technical/pulp.html#pulp.LpProblem)
``` python
class LpProblem(
    name: str = "NoName",
    sense: int = const.LpMinimize
    )
```
```markdown
Creates an LP Problem

This function creates a new LP Problem with the specified associated parameters

:param name: name of the problem used in the output .lp file
:param sense: of the LP problem objective. Either ~pulp.const.LpMinimize (default) or ~pulp.const.LpMaximize.
:return: An LP Problem

```

Inicializovali jsme maximalizační problém s názvem `Max_výroby`.
Třídá `LpProblem` má několik důležitých atributů a metod.

Mezi důležité atributy patří:

- `objective` - Cílová funkce problému
- `constraints` - Seřazený slovník omezení
- `status` - Stav řešení problému

Důležité metody:

- `solve()` - Vyřeší daný problém
- `variables()` - Vrátí seznam proměnných
- `toDict()` nebo `to_dict()` - Vrátí slovník s výsledky

Co vrací jednotlivé metody a atributy si zatím neukážeme, protože jsme zatím problému nepřiřadili žádné proměnné, omezení ani cílovou funkci. Vrátím se k nim později.

#### __LpVariable__

```python
x1 = LpVariable("x1", lowBound=0) # (1)!
x2 = LpVariable("x2", lowBound=0)
...
```

1. Výtah z dokumentace. [Odkaz na dokumentaci](https://coin-or.github.io/pulp/technical/pulp.html#pulp.LpVariable)
``` python
class LpVariable(
    name: str,
    lowBound: Any | None = None,
    upBound: Any | None = None,
    cat: str = const.LpContinuous,
    e: Any | None = None
)

```
```markdown
This class models an LP Variable with the specified associated parameters

:param name: The name of the variable used in the output .lp file
:param lowBound: The lower bound on this variable's range.
    Default is negative infinity
:param upBound: The upper bound on this variable's range.
    Default is positive infinity
:param cat: The category this variable is in, Integer, Binary or
    Continuous(default)
:param e: Used for column based modelling: relates to the variable's
    existence in the objective function and constraints

```

#### __LpAffineExpression__

```python
f = LpAffineExpression([(x1, 2), (x2, 3)]) # (1)!
```

1. Výtah z dokumentace. [Odkaz na dokumentaci](https://coin-or.github.io/pulp/technical/pulp.html#pulp.LpAffineExpression)
``` python
class LpAffineExpression(
    e: Any | None = None,
    constant: int = 0,
    name: Any | None = None
)

```
```markdown
A linear combination of LpVariables<LpVariable>. Can be initialised with the following:

#. e = None: an empty Expression
#. e = dict: gives an expression with the values being the coefficients of the keys (order of terms is undetermined)
#. e = list or generator of 2-tuples: equivalent to dict.items()
#. e = LpElement: an expression of length 1 with the coefficient 1
#. e = other: the constant is initialised as e

Examples:

>>> f=LpAffineExpression(LpElement('x'))
>>> f
1*x + 0
>>> x_name = ['x_0', 'x_1', 'x_2']
>>> x = [LpVariable(x_name[i], lowBound = 0, upBound = 10) for i in range(3) ]
>>> c = LpAffineExpression([ (x[0],1), (x[1],-3), (x[2],4)])
>>> c
1*x_0 + -3*x_1 + 4*x_2 + 0
```

#### __LpConstraint__

```python
c1 = LpConstraint(x1 + x2, sense=LE, rhs=4) # (1)!
c2 = LpConstraint(x1 - x2, sense=GE, rhs=1)
...
```

1. Výtah z dokumentace. [Odkaz na dokumentaci](https://coin-or.github.io/pulp/technical/pulp.html#pulp.LpConstraint)
``` python
class LpConstraint(
    e: Any | None = None,
    sense: int = const.LpConstraintEQ,
    name: Any | None = None,
    rhs: Any | None = None
)
```
```markdown
:param e: an instance of LpAffineExpression
:param sense: one of ~pulp.const.LpConstraintEQ, ~pulp.const.LpConstraintGE, ~pulp.const.LpConstraintLE (0, 1, -1 respectively)
:param name: identifying string
:param rhs: numerical value of constraint target
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


## Příklady
