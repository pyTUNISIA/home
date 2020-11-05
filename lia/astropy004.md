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


# astropy004: Galilée et le mouvement parabolique
![](https://raw.githubusercontent.com/pyTUNISIA/home/master/images/astropy/astropyTUNISIA.png)


# Introduction à Galilée

![Galilée.png](https://raw.githubusercontent.com/pyTUNISIA/home/master/images/astro/scientifiques/Galilée.jpg)

Galilée est, d'abord et avant tout, un phisicien: 

Il a étudié:
* Le pendule
* Le principe d'inertie
* Le plan incliné
* La chute des corps
* Le mouvement parabolique

Galilée est aussi un astronome de premier plan: 

Il a découvert: 
* Les satellites de la planète Jupiter
* Les phases de la planètes vénus
* Les montagnes lunaires
* Les anneaux de la planète Saturne
* La voie lactée notre galaxie


# Présentation des activités python

Le but de cet atelier d'astronomie numérique avec python est de se familiariser avec la démarche scientifique qui consiste à définir les concepts astronomiques du problème en question, de modéliser les lois physiques qui régissent le phènomènes et enfin simuler le phènomène avec python.

 * Utiliser les bibliothèques scientifiques numpy et matplotlib.
 * Utiliser la méthode d'Euler pour résoudre une équation différentielle
 * Représenter graphiquement les résultats
 * Modifier les paramètres et comparer les résultats
 * Comprendre les limites des méthodes numériques 


 
# Le pendule simple
## Concepts
1. Un pendule est un corps de masse m suspendue en un point fixe O par un fil de longueur l et de masse négligable.
2. La position du pendule est répérée à tout instant t par son angle $\theta (t)$ par rapport à sa position d'équilibre.
3. Le mouvement du pendule est régie par la loi fondamentale de la dynamique.
## Modèle
L'équation du mouvement du pendule est : $ \ddot\theta=- \omega_0 ^{2} \sin(\theta)$ avec $\omega_0= \sqrt\frac{g}{l} $

**Notes** :
* C'est une équation différentielle d'ordre 2 (on a une dérivée seconde).
* C'est une équation différentielle non linéaire (on a un $\sin(\theta)$).


## Simulation
Posons $X=[\theta, \dot \theta]$
donc $\dot X=[\dot\theta, \ddot \theta]=[\dot\theta ,- \omega_0 ^{2} \sin(\theta)]$


Soit $F(X)=F([x0,x1])=[x1,- \omega_0 ^{2} \sin(x0)]$

```python
#- Importation des bibliothèques scientifiques numpy et matplotlib
#----------------------------------------------------------------
import numpy as np
import matplotlib.pyplot as plt
```
@Pyodide.eval

```python
#- Définition de l'équation du mouvement
#---------------------------------------
g=9.81 # m/s^2
l=1.2 # m
OMEGA=g/l # s^-2
def F(t,X):
    return np.array([X[1],-OMEGA * np.sin(X[0])])

```
@Pyodide.eval

```python
#- Méthode d'Euler
#-----------------
def Euler(F,a,b,X0,n):
    '''
    Méthode Explicite
    X'=F(t,X)
    [a,b] 
    X0=X(a)
    n Nombre de points
    output : T,X
    '''
    h=(b-a)/n # pas
    t=np.linspace(a,b,n) # Array t : intervalle [a,b] discrétisé
    X=np.zeros((n,2))    # Array X : solution cherchée
    
    X[0,0]=X0[0]
    X[0,1]=X0[1]
    for i in range(n-1):
        X[i+1]= X[i]+h*F(t[i],X[i]) 
    return t,X

```
@Pyodide.eval


```python
#- Résolution de l'équation différentielle
#-----------------------------------------
T=12 # secondes
a,b,X0,n=0,T,np.array([np.radians(20),0]),10000 
t,X=Euler(F,a,b,X0,n)# X la solution Euler

x0=X[:,0] # x0 est la soluton Euler de x 
x1=X[:,1] # x1 est la soluton Euler de la dérivée de x

```
@Pyodide.eval



```python
#- Affichage des paramètres du pendule
#-------------------------------------
def afficheParametres():
    print('Les paramètres du pendule sont: ')
    print('a=',a)
    print('b=',b)
    print('X0=',X0)
    print('n=',n)

afficheParametres()

```
@Pyodide.eval


```python
#- Affichage du mouvement
#------------------------
def plotMouvement():
    fig1=plt.figure()
    plt.title('Mouvement du pendule')
    plt.plot(t,x0)
    plt.xlabel("$ t $")
    plt.ylabel("$\Theta $")
    plt.show()
    plot(fig1)

plotMouvement()

```
@Pyodide.eval


```python
#- Affichage du diagramme des phases
#-----------------------------------
def plotDiagramme():
    fig2=plt.figure()
    plt.title('Diagramme de phase')
    plt.plot(x0,x1)
    plt.xlabel("$\Theta $")
    plt.ylabel("$\dot\Theta $")
    plt.show()
    plot(fig2)

plotDiagramme()

```
@Pyodide.eval


### Notes 
D'autres types de pendules peuvent être étudier , par exemple:
* Le pendule amortie

* Le pendule double

* Le pendule inversée



# La chute des corps
## Concepts
## Modèle
## Simulation



# Le mouvement parabolique
## Concepts
## Modèle
## Simulation

