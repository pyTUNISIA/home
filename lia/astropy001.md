<!--
author:   (c) riadh BEN NESSIB
email:    riadhbennessib@gmail.com
version:  0.1.0
language: fr
logo:     https://raw.githubusercontent.com/pyTUNISIA/home/master/images/astropy/astropyTUNISIA.png
comment:  astropyTUNISIA: Astronomie Numérique avec Python.
mode   :  Textbook
script:   https://pyodide-cdn2.iodide.io/v0.15.0/full/pyodide.js
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
>

## Python



```python
# ------------------------
# Exemple 01 : Hello World
# ------------------------
print('Hello World!')
# ------------------------
```
@Pyodide.eval


```python
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
plt.show()
plot(fig) 
# ------------------------
```
@Pyodide.eval
