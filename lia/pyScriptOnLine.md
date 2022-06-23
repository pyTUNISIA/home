<!--
author:   (c) riadh BEN NESSIB
email:    riadhbennessib@gmail.com
version:  0.2.0
language: fr
logo:     https://raw.githubusercontent.com/pyTUNISIA/home/master/images/logos/LogoPython.png
comment:  pyScript : Python on the browser!.
mode   :  Textbook
script:   https://javascript_resourse_url

script:   https://cdn.jsdelivr.net/gh/liatemplates/pyscript@0.0.4/dist/pyscript.min.js

link:     https://cdn.jsdelivr.net/gh/liatemplates/pyscript@0.0.4/dist/pyscript.css

persistent:  true   


@PyScript.env
<lia-keep>
<py-env>
@0
</py-env>
</lia-keep>
@end

@PyScript.repl: @PyScript.replWith( ,```@0```)

@PyScript.replWith
<lia-keep>
<style>
  .output {
    font-style: inherit;
    font-weight: inherit;
    font-size: inherit;
    line-height: inherit;
    margin-left: 0px;
    float: left;
  }

  .mt-2 {
    margin-left: 0px !important;
    margin-right: 0px !important;
    margin-bottom: 15px !important;
  }

  .editor-box {
    border: 1px solid black;
  }
</style>
<py-repl @0>@1</py-repl>
</lia-keep>
@end
-->


# pyScript: Python in the browser!
![](https://raw.githubusercontent.com/pyTUNISIA/home/master/images/logos/pyscript01.png)

## Introduction

Welcome to Python on the web!

![](https://raw.githubusercontent.com/pyTUNISIA/home/master/images/logos/pyscript03.png)


## pyScript editor



```python @PyScript.repl
# ------------------------
# Exemple 01 : Welcome to pyScript!
# ------------------------
print('Welcome to  pyScript!')
# ------------------------
```

``` python @PyScript.env
- matplotlib
- numpy
```

```python @PyScript.repl
# ----------------------
# Exemple 02 : Graphique
# Exercice: Essayer les fonctions sin, cos, tanh, exp(-x**2) etc...
# ----------------------
import numpy as np
import matplotlib.pyplot as plt
t = np.arange(0.0, 2.0, 0.01)
s = np.sin(2 * np.pi * t)
fig, ax = plt.subplots()
ax.plot(t, s)
ax.grid(True, linestyle='-.')
ax.tick_params(labelcolor='r', labelsize='medium', width=3)
fig
# ------------------------
```
