<!--

author:   (c) riadh BEN NESSIB
email:    riadhbennessib@gmail.com
version:  0.1.0
language: fr
logo: https://raw.githubusercontent.com/pyTUNISIA/home/master/images/py/pyTunisiaBooks.png
comment:  pyTUNISIA: Programmation Scientifique avec python.
mode: Textbook
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

# Article 003: Bibliographie python

## Bibliographie Python.

![](https://raw.githubusercontent.com/pyTUNISIA/home/master/images/py/pyTunisiaBooks.png)<!--
style = "width: 500px;
        Height:250px;
        display: flex;
        align-items: center;
        justify-content: center;
        border: 5px solid;
         // filter: grayscale(100%);"
-->
## Introduction
> Introduction
Nous sommes dans le monde du digital. Tout est sur internet. On trouve des videos tutoriels, des cours pdf, des exemples de code, des applications et des articles web sur les différentes facettes du langage de programmation python, on trouve tout  sur internet. Il reste que, en tout les cas mon opinion personnel,  le livre est  la meilleure ressource pour apprendre le Python, et sur intenet, il y'a de toutes les couleurs et de tous les niveaux. 
## Comment choisir ses livres de programmation python?
Voici donc, ma vision des classes de livres de l'écosystème Python:

0. Les livres sont en anglais à 95%, quelques bouquins sont rédigés en français.
1. Le niveau est de trois : beginner, intermediate and expert.
2. Les séries et éditeurs qui excellent dans la bibliographie scientifique sont par exemple : O'reilly, Packt, Manning, Springer, Apress, Ellipses, Eyrolles et Dunod.
3. Python le fondamental : la synthaxe, les structures de données, les conditions, les boucles, les fichiers, les modules....
4. Python les packages   : les bibliothèques python (les packages) sont les briques de construction des applications écrites en python par exemple  numpy, matplotlib, scipy, sympy, pandas...
5. L'algorithmique: tri, recherche, codage, compression, pile,file,arbre,graphe....
6. Python POO et FP : La programmation orienté objet et la programmation fonctionelle sont les deux approches et méthodologies de développement d'applications(logiciels) incontournables pour les ingénieurs informaticiens  et de niveaux avancés.
7. Les livres à thèmes comme les Computational sciences : Computational physics (pysique informatique), Computational biology(bioinformatique) , Computational Geology (GIS: Geographic Information System), Numerical python (Analyse numérique) et la finance.
8. Les livres pour les enfants (Python for Kids): parce que la programmation est aussi importante que les mathématiques ou les sciences physiques!
9. Python pour le web : le Web Scrapping, le Web Hacking, la cryptologie.
10. Data science: de nos jours,le machine learning et le Deeplearning sont les deux domaines technologiques les plus recherchés dans l'industrie informatique.

## Références
> Références :
Maintenant, à vous de lire et de pratiquer Python. __*Comment?*__ Clickez et enrichissez votre bibliothèque Python!

* GitHub https://github.com/oddsun/Free-Python-Books
* freecomputerbooks http://freecomputerbooks.com/langPythonBooks.html
* pdfdrive https://www.pdfdrive.com/
* libgen https://libgen.is/

