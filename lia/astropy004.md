<!--
author:   (c) riadh BEN NESSIB
email:    riadhbennessib@gmail.com
version:  0.1.0
language: fr
logo:     https://raw.githubusercontent.com/pyTUNISIA/home/master/images/astropy/astropyTUNISIA.png
comment:  Astropy 004: Galilée et le pendule simple
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
////////////////////////////////////////////////

# astropy004: Galilée et le pendule simple

![Image astroTUNISIA.png](https://raw.githubusercontent.com/pyTUNISIA/home/master/images/astropy/astropyTUNISIA.png)


# Introduction à Galilée

![Galilée.png](https://raw.githubusercontent.com/pyTUNISIA/home/master/images/astro/scientifiques/Galilée.jpg)


Galilée (Galileo Galilei), né à Pise en 1564 et mort à Arcetri près de Florence le 8 janvier 1642 (77 ans), est un mathématicien, géomètre, physicien et astronome italien du XVIIe siècle. 

Galilée est, d'abord et avant tout, un physicien: 

Il a étudié:
* Le pendule simple
* Le principe d'inertie
* Le plan incliné
* La chute des corps
* Le mouvement parabolique

Galilée est aussi un astronome de premier plan: 

Il a découvert: 
* Les satellites de la planète Jupiter
* Les phases de la planète Vénus
* Les montagnes lunaires
* Les anneaux de la planète Saturne
* La nature de la voie lactée notre galaxie


# Présentation des activités python

Le **but de cet atelier** d'astronomie numérique avec python est de se familiariser avec la démarche scientifique qui consiste à définir les concepts astronomiques du problème en question, de modéliser les lois physiques qui régissent le phènomène et enfin de simuler le phènomène avec python.

Les ***objectifs spécifiques de cet atelier *** sont:

 * Utiliser les bibliothèques scientifiques numpy et matplotlib.
 * Utiliser la méthode d'Euler pour résoudre une équation différentielle
 * Représenter graphiquement les résultats
 * Modifier les paramètres et comparer les résultats
 * Comprendre les limites des méthodes numériques 


 
# Le pendule simple



## Concepts
![](https://liamd.informatik.tu-freiberg.de/uploads/upload_346167a177be75a996d3df5c1d8fb21d.jpg)

1. Un pendule est un corps M de masse m suspendue en un point fixe O par un fil de longueur l et de masse négligable.
2. La position du pendule est répérée à tout instant t par son angle $\theta (t)$ par rapport à sa position d'équilibre.
3. Le mouvement du pendule est régie par la loi fondamentale de la dynamique.


## Modèle

L'équation du mouvement du pendule est : $$ \ddot\theta=- \omega_0 ^{2} \sin(\theta)$$ avec $\omega_0= \sqrt\frac{g}{l} $

**_Notes**_ :
* C'est une équation différentielle d'ordre 2 (on a une dérivée seconde).
* C'est une équation différentielle non linéaire (on a un $\sin(\theta)$).


Cette équation différentielle peut être établie par 3 méthodes :

1. L'équation fondamentale de la dynamique: $$ \vec{F}= m \vec{a}$$
2. Le théorème de la conservation de l'énergie mécanique : $$ \frac{dE_t}{dt}=0 $$ avec $$E_t=E_c + E_p= \frac{1}{2} m v^2 - mgh$$
3. Le théorème du moment cinétique: La dérivée du moment cinétique par rapport à o est égale à la somme des moments des forces extérieures par rapport à o. $$\frac{d\vec{L_{ /o}}}{dt}= \sum \vec{M_{F/o}}$$ avec :   $$ \vec{L_{ /o}}  = \vec{OM} \wedge \vec{mv} $$ $$\vec{M_{F/o}}=\vec{M_{T/o}}+\vec{M_{P/o}}$$ $$ \vec{M_{T/o}} = \vec{OM} \wedge \vec{T} = \vec{0}$$ $$ \vec{M_{P/o}} = \vec{OM} \wedge \vec{mg} $$

## Simulation

**Etape 01 : Importation des bibliothèques scientifiques numpy et matplotlib **

```python
#- Importation des bibliothèques scientifiques numpy et matplotlib
#----------------------------------------------------------------
import numpy as np
import matplotlib.pyplot as plt
```
@Pyodide.eval

----



**Etape 02 : Introduction à la Méthode d'Euler**


Soit à résoudre l'équation différentielle $$\dot X= F(t,X)$$ avec la condition initiale $X(0)=X_0$.

Nous avons donc : 

$$ dX= F(t,X) dt $$ c'est à dire


$$ \begin{array}{cc}
        X(0)=X_0                  \\ 
        X_1=X_0 + F(t_0,X_0)dt    \\
        X_2=X_1 + F(t_1,X_1)dt    \\ 
        X_3=X_2 + F(t_2,X_2)dt    \\
        \dots                     \\
        X_{n+1}=X_n+ F(t_n,X_n)dt \\
  \end{array}$$

Nous évaluons la valeur de X en n+1 à partir de la valeur X en n et de la pente dX/dt en $t_n$. En se faisant, nous avons commis une erreur égale à $X_{n+1}$exacte - $X_{n+1}$euler.  
![](https://liamd.informatik.tu-freiberg.de/uploads/upload_cd4997a1f087a3417541300138589e7f.png)


Méthode Euler explicite est d’ordre 1: 

$X_0$ étant donnée
$X_{n+1} = X_n + F(t_n, X_n)dt$ 

La méthode peut s’interpréter de plusieurs manières : 

1. Via les formules d’intégration numérique : la méthode est le résultat de l’application de la formule des rectangles basée au point xn . 
2. Géométriquement : la méthode revient à remplacer localement en chaque point xn la courbe solution par sa tangente. 
3. Via les développements de Taylor : la méthode provient du développement de Taylor d’ordre 1 de la fonction y au voisinage de xn .


La fonction python suivante implémente cette méthode façile mais peut précise! Cette méthode s'appelle la méthode d'**Euler explicite**.

```python
#- Méthode d'Euler Explicite
#---------------------------
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


----

**Etape 03 : Définition de l'équation du mouvement **

Posons $X=[\theta, \dot \theta]$
donc $\dot X=[\dot\theta, \ddot \theta]=[\dot\theta ,- \omega_0 ^{2} \sin(\theta)]$


Soit $F(X)=F([x0,x1])=[x1,- \omega_0 ^{2} \sin(x0)]$

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


----
**Etape 04 : Résolution de l'équation différentielle**


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


----

**Etape 05 : Affichage des paramètres du pendule**

```python
#- Affichage des paramètres du pendule
#-------------------------------------
def afficheParametres():
    print('Les paramètres du pendule sont: ')
    print('g=',g)
    print('l=',l)
    print('a=',a)
    print('b=',b)
    print('X0=',X0)
    print('n=',n)

afficheParametres()

```
@Pyodide.eval


----


**Etape 06 : Affichage du mouvement du pendule**

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


----


**Etape 07 : Affichage du diagramme des phases**


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



----

**Etape 08 : Interprétations et manipulations**


1. Doublez la longueur du fil ( Etape 03 ) et observez la période du pendule (Etape 06). Je vous rapelle que la période du pendule simple est :
$$ T= 2 \pi \sqrt(\frac{l}{g}) $$

2. Augmentez l'angle $\theta_0$ initial (Etape 04) et observez le comportement du pendule (Etape 06)

3. Augmentez le temps de la simulation T (Etape 04 ) et observez le diagramme des phases (Etape 07).

4. Est ce que cette méthode conserve l'énergie totale du pendule?

5. Simplifiez l'équation différentielle (Etape 03) en prenant  $\sin \theta = \theta $ quand l'angle $\theta$ est petit et refaite les manipulations 1,2,3 et 4.


## Résumé 


La méthode d'Euler Explicite est façile a mettre en œuvre mais elle n'est pas précise et ne conserve pas l'énergie d'un système. D'autres méthodes de résolution numérique d'équations différentielles sont plus performantes que celle-çi mais plus délicates à programmer! Vous avez par exemple :

* Euler Implicite
* Runge Kutta 4
* Méthode Leapfrog
* Méthode Verlet
* et bien d'autres.

D'autres types de pendules peuvent être étudier , par exemple:
* Le pendule amortie

* Le pendule double

* Le pendule inversée


