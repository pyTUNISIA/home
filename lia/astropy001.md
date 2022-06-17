<!--
author:   (c) riadh BEN NESSIB
email:    riadhbennessib@gmail.com
version:  0.2.0
language: fr
logo:     https://raw.githubusercontent.com/pyTUNISIA/home/master/images/astropy/astropyTUNISIA.png
comment:  astropyTUNISIA: Astronomie Numérique avec Python.
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


# astropy001: astropyTUNISIA
![](https://raw.githubusercontent.com/pyTUNISIA/home/master/images/astropy/astropyTUNISIA.png)

## Astronomie
![](https://raw.githubusercontent.com/pyTUNISIA/home/master/images/astro/DeLaTerreAUnivers.jpg)

> Introduction
L'astronomie est la science qui étudit les planètes,les étoiles et les galaxies.
> Le système solaire
Autour du Soleil , des planètes gravitent sur un même plan appelé l'écliptique.
> Les étoiles 
Une étoile est une boule de gaz très chaud.


> Les galaxies
Une galaxie est un ensemble de milliards d'étoiles.

## Maths

> La loi de gravitation universelle stipule que deux masses M et m s'attirent mutuellement avec une force proportionnelle aux masses et inversement proportionnelle au carré de la distance r qui les sépare:
>  $$ \vec{F} = - \frac{ G M m }{r^2} \vec{u}$$


## Python



```python @PyScript.repl
# ------------------------
# Exemple 01 : Hello World
# ------------------------
print('Hello World!')
# ------------------------
```

``` python @PyScript.env
- matplotlib
- numpy
```

```python @PyScript.repl
# ----------------------
# Exemple 02 : Graphique
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
