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


#  GreenNight2020:  Introduction à l'Astronomie Numérique avec Python
![](https://liamd.informatik.tu-freiberg.de/uploads/upload_fe6de3c082f613f5cfc46aac8a5356c3.png)

----
>  * Conférence:  Introduction à l'Astronomie Numérique avec Python 
   * Date : 27 novembre 2020 
   * Lieu : Cité des Sciences à Tunis
   * Présentateur: riadh BEN NESSIB
   
   


----
# Qui suis-je?

> ![](https://raw.githubusercontent.com/pyTUNISIA/home/master/images/logos/riadhbennessib.png)
>
> (c) riadh BEN NESSIB
> 
> Médiateur scientifique en chef au planétarium de la Cité des Sciences à Tunis.



# Introduction à l'Astronomie Numérique avec Python

![Image astroTUNISIA.png](https://raw.githubusercontent.com/pyTUNISIA/home/master/images/astropy/astropyTUNISIA.png)

> La science se fonde sur la théorie. L'expérience vient confirmer ou infirmer les connaissances. La simulation vient nous faciliter l'expérience et confirmer la science! 
> 
> (mai 2019, riadh BEN NESSIB)




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


> ![](https://liamd.informatik.tu-freiberg.de/uploads/upload_5756d1eff5db2deeb6b4575c12efd0dc.jpeg)

>  Galilée : l'Univers est écrit en langage mathématique.
>  La philosophie est écrite dans cet immense livre qui se tient toujours ouvert devant nos yeux, je veux dire l’Univers, mais on ne peut le comprendre si l’on ne s’applique d’abord à en comprendre la langue et à connaître les caractères avec lesquels il est écrit. Il est écrit dans la langue mathématique et ses caractères sont des triangles, des cercles et autres figures géométriques, sans le moyen desquels il est humainement impossible d’en comprendre un mot. Sans eux, c’est une errance vaine dans un labyrinthe obscur.(Galilée, L'Essayeur, 1623.)


> ![](https://liamd.informatik.tu-freiberg.de/uploads/upload_17fe1fbd53485d5fe840b93cc201cab5.jpg)

> Issac Newton (1687): La loi de gravitation universelle stipule que deux masses M et m s'attirent mutuellement avec une force proportionnelle aux masses et inversement proportionnelle au carré de la distance r qui les sépare:
>  $$ \vec{F} = - \frac{ G M m }{r^2} \vec{u}$$




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


# Coder, coder et coder!

![](https://raw.githubusercontent.com/pyTUNISIA/home/master/images/py/CoderCedricVillany.png)<!--
style = "width: 500px;
        Height:400px;
        display: flex;
        align-items: center;
        justify-content: center;
        border: 5px solid;
         // filter: grayscale(100%);"
-->

__*Coder, Coder et Coder!*__
J'aime beaucoup ce qu'a dit le mathématicien Cédric Villani. __*Commencez dès maintenant à programmer!*__  Voici une liste d'outils pour commencer. 
Installez l'une de ces distributions et vous êtes dans l'Univers de la programmation en Python!

* http://python.org
* https://winpython.github.io/
* https://www.anaconda.com/distribution/


> Références :
* https://fr.wikipedia.org/wiki/Python_(langage)
* https://winpython.github.io/
* https://en.wikipedia.org/wiki/Anaconda_(Python_distribution)
* https://fr.wikipedia.org/wiki/C%C3%A9dric_Villani



# Le tout gravite autour de l'ordinateur

Dans l’Univers de l’informatique, les logiciels qui tournent autour de l’astronomie sont nombreux. Le logiciel de représentation du ciel **Stellarium** par exemple est une belle invitation à l’observation du ciel nocturne, on peut l’utiliser aussi comme base de données astronomiques. **Celestia**, le célèbre logiciel des amateurs d’astronomie, vous présente la carte d’identité universelle du système solaire avec toutes les données numériques et les caractéristiques des différentes planètes.

En fait, s’ils sont nombreux, beaux à voir avec leurs images numériques illustratifs des phénomènes qu’ils présentent, ces logiciels ne présentent pas les données astronomiques sous forme propice à la compréhension d’un *concept*, à l’élaboration d’un *modèle* et à la réalisation d’une *simulation*.

En choisissant, le concept bien délimité de gravitation, la question précise que je pose alors et à laquelle j’essaye de répondre est : 

> Comment utiliser l’ordinateur pour passer de l’image qu’on se fait du ciel à la théorie de la gravitation universelle? 

A l’heure actuelle, je ressent très fortement la nécessité d’une approche culturelle, scientifique et créative ayant pour objectif le développement de la capacité à appliquer la méthode scientifique dans un contexte pédagogique et simplifié.

Si la science utilise du vocabulaire spécifique et pointu, génère de grands nombres et des échelles d’espace-temps qui dépassent notre imagination , sa vision du monde reste néanmoins qu’un ensemble de modèles et c’est ainsi que l’Homme explore le réel. Sur la base de ce constat simple, je divise mon approche au problème en trois étapes: Concept, Modèle et Simulation.

## La notion de Concept.

La théorie se fonde sur certains concepts comme la masse, la force, ou la charge électrique. Ces concepts délimitent un certain champ de représentation qui est le monde que l’on veut décrire et se représenter à partir de ces concepts. Il est clair par exemple que la notion de courant électrique échappe au champ de représentation de la mécanique newtonienne, puisque le concept de charge électrique n’existe pas dans la mécanique newtonienne.

A l’intérieur d’un champ de représentation , on élabore des lois à partir de ses concepts. Ces lois constituent l’ossature du modèle par lequel un phénomène est représenté.

## La notion de Modèle.

Un modèle est une représentation, en général simplifié, d’un phénomène ou d’un objet. Son élaboration succède à un ensemble d’observations qui circonscrivent, dénombrent et relient entre eux les divers concepts et propriétés du phénomène ou de l’objet. La comparaison et la confrontation du modèle aux observations testent et validité et la justesse de la représentation du phénomène ou de l’objet. L’acceptation de celui-ci est préservé tant que de nouvelles observations ne remettent pas en question validité. 

L’astronomie et la physique ont donné naissance à de nombreux modèles. Les plus simples mais aussi les plus révolutionnaires sont ceux de l’atome d’hydrogène et du système solaire. Ses deux systèmes ont de tout temps intrigué les Hommes car ils représentent l’infiniment petit et l’infiniment grand, et à ce propos, je vous invite à lire l'excellent petit livre intitulé ‘Macro-Micro je mesure l’Univers’, que la Cité des Science à Tunis édite en langue arabe sous le titre ‘أقيس الكون’.

## La notion de Simulation.

La simulation désigne la mise en œuvre d’un modèle à l’aide de moyens informatiques (ordinateurs et logiciels). Elle se caractérise par la très grande souplesse qu’offre le calcul informatique à:

1. Incorporer les équations de la physique régissant le phénomène.
2. Remplacer les calculs impossibles à résoudre analytiquement par des approximations numériques.
3. Manipuler d’énormes volumes de nombres et d’opérations arithmétiques.
4. Visualiser les résultats sous formes graphiques.
5. Varier les conditions aux limites et les paramètres physiques d’un phénomène.
6. Compresser le temps de déroulement du phénomène étudié.

Parmi les triomphes de la simulation on a l’exemple du problème à N-corps. C’est l’étude des mouvements d’un grand nombre de corps en interaction gravitationnelle tels qu’on les rencontre dans les amas globulaires et dans les galaxies. Ce problème est fondamental en mécanique céleste. Pour N=2 (étoile double, étoile-Planète, planète-satellite) et N=3 (avec la condition que la masse d’un des corps soit négligeable par rapports aux autres, le système Soleil-Terre-Lune par exemple) on a pu exhibé des solutions analytiques mais pour N dépassant 3, le problème devient insoluble autrement que par des méthodes numériques utilisant des simulations sur ordinateurs. Grace à ces simulations, la forme et l’évolution dynamique de ces amas et de ces galaxies deviennent prévisibles et comparables à ce que révèle l’observation.






# Galilée et le pendule simple

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

Nous évaluons la valeur de X en $t_{n+1}$ à partir de la valeur X en $t_n$ et de la pente dX/dt en $t_n$. En le faisant, nous avons commis une erreur égale à $X_{n+1}$exacte - $X_{n+1}$euler. 

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


# Conclusion

Les ordinateurs offrent un moyen puissant de simulation de phénomènes astronomiques. Les programmer via un langage de programmation comme Python est une approche scientifique bénéfique pour les apprenants et amateurs d’astronomie.

Enfin, si la simulation vous tente, vous pouvez commencer à lire les articles du site Github (astropyTUNISIA) et de s'abonner à la page facebook de Programmation Scientifique avec Python (pyTUNISIA) et au groupe d'Astronomie Numérique avec Python (astropyTUNISIA).

** Le site Github:**

![](https://liamd.informatik.tu-freiberg.de/uploads/upload_b86773c70784904570e5b3265366bd86.PNG)<!--
style = "width: 800px;
        Height:400px;
        display: flex;
        align-items: center;
        justify-content: center;
        border: 5px solid;
         // filter: grayscale(100%);"
-->


** La page facebook:**

![](https://raw.githubusercontent.com/pyTUNISIA/home/master/images/py/pyTUNISIA.png)

** Le groupe facebook:**

![](https://raw.githubusercontent.com/pyTUNISIA/home/master/images/astropy/astropyTUNISIA.png)




# Bibliographie Simulation

1. Pierre-Lena, ‘Les sciences du ciel’, Flammarion 1996.p.85-89,462-463,560-561.
2. Jean Dunin-Borkowski, ‘ L’astronomie sur un écran d’ordinateur’, Cahiers Clairaut n°46,p.23-25.
3. Jean-Paul Rosenstiehl, ‘De Newton à Kepler … avec une calculette’, Cahiers Clairaut n°21,p.23-26.
4. Frédéric Neuville, ‘La gravitation’, Science et vie micro n°36,p.73-74.
5. Daniel Durand, ‘La systémique’, Que sais-je? presse universitaire de France 1992,p71-78.
6. Bruno Jarrosson, ‘Invitation à la philosophie des sciences’, collection Points sciences Editions du Seuil 1992,p.155-194.







