<!--
author:   (c) riadh BEN NESSIB
email:    riadhbennessib@gmail.com
version:  0.1.0
language: fr
logo:     https://raw.githubusercontent.com/pyTUNISIA/home/master/images/py/ScientificPythonLogos02.jpg
comment:  pyTUNISIA: Programmation Scientifique avec python..
script:   https://pyodide-cdn2.iodide.io/v0.15.0/full/pyodide.js
mode:  Textbook
@onload
window.languagePluginUrl = 'https://pyodide-cdn2.iodide.io/v0.15.0/full/'
window.pyodide_ready = false;
window.pyodide_modules = new Set()
window.py_packages = ["matplotlib", "numpy"]
window.loadModules = function() {
  languagePluginLoader.then(() => {
    console.log("pyodide is ready")
    if (window.py_packages) {

      for( let i = 0; i < window.py_packages.length; i++ ) {
        window.pyodide_modules.add(window.py_packages[i])
      }

      pyodide.loadPackage(window.py_packages).then(() => {
        console.log("all packages loaded")
        window.pyodide_ready = true;
      });
    }
    else {
      window.pyodide_ready = true;
    }
  })
}

window.loadModules()

@end


@Pyodide.eval: @Pyodide.eval_(@uid)

@Pyodide.eval_
<script>

function initPlot() {
try {

pyodide.runPython(`
import io, base64

try:
  img_str_
except NameError:
  img_str_ = {}

def plot(fig, id="plot-@0"):
  buf = io.BytesIO()
  fig.savefig(buf, format='png')
  buf.seek(0)
  img_str_[id] = "data:image/png;base64," + base64.b64encode(buf.read()).decode('UTF-8')
`)
} catch (e) {}
}

function copyPlot() {
  if ( pyodide.globals.img_str_["plot-@0"] ) {
    document.getElementById("plot-@0").src = pyodide.globals.img_str_["plot-@0"]
    document.getElementById("plot-@0").parentElement.style = ""
  }
}

////////////////////////////////////////////////////

function runPython() {
  if (window.pyodide_ready) {
    pyodide.globals.print = (...e) => { e = e.slice(0,-1); console.log(...e) };
    setTimeout(() => {
      try {
        initPlot()

        let fin = pyodide.runPython(`@input`)
        if (fin) {
          console.log(fin)
        }
        copyPlot()
        send.lia("LIA: stop")
      } catch(e) {
        //window.py_packages = ["matplotlib"]
        let module = e.message.match(/ModuleNotFoundError: No module named '([^']+)/g)
        if (! module) {
          console.error(e)
          //let msg = e.message.match(/File "<unknown>", line (\d+)\n.*\n.*\n.*/g)
          //window.console.log(msg[0])
          send.lia("LIA: stop")
        }
        else if (module.length != 0) {
          module = module[0].split("'")[1]
          if (window.pyodide_modules.has(module)) {
            console.error(e)
            send.lia("LIA: stop")
          } else {
            console.debug("downloading module =>", module)
            window.py_packages = [ module ]
            window.pyodide_ready = false
            window.loadModules()
            runPython()
          }
        }
        else {
          console.error(e)

          send.lia("LIA: stop")
        }
      }
    }, 100)
  } else {
    setTimeout(runPython, 234)
  }
}

runPython()

"LIA: wait";
</script>

<div id="pyplotdiv" style="display:none"><img id="plot-@0" /></div>

<script>
try {
if ( pyodide.globals.img_str_["plot-@0"] )
  document.getElementById("plot-@0").src = pyodide.globals.img_str_["plot-@0"]
  document.getElementById("plot-@0").parentElement.style = ""
} catch(e) {}

</script>

@end

-->

# Article 004: Bibliothèques de programmes (Les packages)

# Les bibliothèques de programmes packages.

![](https://raw.githubusercontent.com/pyTUNISIA/home/master/images/py/ScientificPythonLogos02.jpg)<!--
style = "width: 400px;
        Height:300px;
        display: flex;
        align-items: center;
        justify-content: center;
        border: 5px solid;
         // filter: grayscale(100%);"
-->
## Introduction
> Introduction
Vous avez appris les fondamentaux de la programmation Python. Vous avez appris à écrire des scripts de calcul simple.Vous avez appris les structures de base comme les ensembles (set), les listes (list),les tuples (tuple) et les dictionnaires(dic). Vous avez appris à utiliser les boucles (for) et les conditions (If). Vous avez appris à définir des fonctions (def).

Maintenant, il est grand temps de renforcer ses connaissances de programmation scientifique avec Python par les bibliothèques de programmes (packages).

## Comment importer les bibliothèques de programmes?
> Comment importer les bibliothèques de programmes?

La biblio math contient des constantes et fonctions mathématiques de base. La biblio random manipule tout ce qui est aléatoire. La biblio statistics manipule les données.

Il y'a quatre façons d'utiliser ces packages, par exemple pour faire des calculs:

```python
# importe le nom de la biblio.
import math       
print(math.pi)

```
@Pyodide.eval

```python
# importe la constante pi et la fonction sin de la biblio.
from math import pi,sin 
print(sin(pi))

```
@Pyodide.eval

```python
 # importe toutes les constantes et fonctions de la biblio.
from math import * 
print(pi,e,sin(radians(45))) 

```
@Pyodide.eval

```python
# importe la biblio avec un alias.
import math as m    
print(m.pi)

```
@Pyodide.eval

## Les packages scientifiques de base
> Les packages scientifiques de base.

Maintenant, je vous présente les stars de packages en programmation scientifique! J'ai nommé : numpy, la biblio qui manipule les arrays (vecteurs, matrices et tenseurs); matplotlib, la biblio qui manipule les graphiques ; scipy, la biblio qui manipule les maths comme vous ne pouvez pas le faire avec votre stylo!; pandas, la biblio qui manipule les données à la pele!; sympy, la biblio qui manipule les formules mathématique au vrai sens du mot et enfin networkx, la biblio qui manipule les graphes comme beaucoup de programmeurs le souhaitent.

```python
# importer seulement les packages à utiliser
import sys
import numpy as np
import matplotlib as mp
# import scipy as sp
# import pandas as pd
# import sympy as sm
# import networkx as nx
# import matplotlib.pyplot as plt

```
@Pyodide.eval

```python
# afficher les versions de ces packages
print( 'python : {}'.format(sys.version))
print( 'numpy : {}'.format(np.__version__))
print( 'matplotlib : {}'.format(mp.__version__))
# print( 'scipy : {}'.format(sp.__version__))
# print( 'pandas : {}'.format(pd.__version__))
# print( 'sympy : {}'.format(sm.__version__))
# print( 'networkx : {}'.format(nx.__version__))

```
@Pyodide.eval

## Le Python Package Index (PyPi)
>  Python Package Index
![](https://raw.githubusercontent.com/pyTUNISIA/home/master/images/py/PyPi.PNG)<!--
style = "width: 400px;
        Height:200px;
        display: flex;
        align-items: center;
        justify-content: center;
        border: 5px solid;
         // filter: grayscale(100%);"
-->

Enfin, pour chercher des bibliothèques de programmes (modules et packages) spécifiques à vos domaines de recherche , par exemple en chimie, sciences physiques, biologie ou géologie, pour les utiliser dans vos recherches, le site https://pypi.org/ est la référence: juste un mot clé et tous les packages relatifs à ce mot sont affichés. Bien sûr, il faut installer les packages avant de pouvoir les utiliser.

## Références
> Références
Pour plus d'information sur les packages 'standards' je vous donne quelques références.
Bon travail et à bientôt sur pyTUNISIA.


1.  Numpy
    * https://fr.wikipedia.org/wiki/NumPy
    * https://numpy.org/
    * https://docs.scipy.org/doc/numpy/reference/
    * https://docs.scipy.org/doc/numpy/user/index.html#user
    * https://docs.scipy.org/doc/numpy/user/quickstart.html
2.  Matplotlib
    * https://fr.wikipedia.org/wiki/Matplotlib
    * https://matplotlib.org/
    * https://matplotlib.org/3.2.0/contents.html
    * https://matplotlib.org/3.1.1/users/index.html
    * https://matplotlib.org/3.1.1/tutorials/index.html
3.  Scipy
    * https://fr.wikipedia.org/wiki/SciPy
    * https://www.scipy.org/
    * https://docs.scipy.org/doc/scipy/reference/
    * https://docs.scipy.org/.../reference/tutorial/index.html
4.  Pandas
    * https://fr.wikipedia.org/wiki/Pandas
    * https://pandas.pydata.org/
    * https://pandas.pydata.org/docs/user_guide/index.html
    * https://pandas.pydata.org/docs/getting_started/index.html
5.  Sympy
    * https://fr.wikipedia.org/wiki/Sympy
    * https://www.sympy.org/en/index.html
    * https://docs.sympy.org/latest/index.html
    * https://docs.sympy.org/latest/tutorial/index.html
6.  Networkx
    * https://fr.wikipedia.org/wiki/NetworkX
    * https://networkx.github.io/
    * https://networkx.github.io/documentation/stable/
    * https://networkx.github.io/documenta.../stable/tutorial.html
