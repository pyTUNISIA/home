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


# Astropy 002: Le tout gravite autour de l'ordinateur
![](https://raw.githubusercontent.com/pyTUNISIA/home/master/images/astropy/astropyTUNISIA.png)


# Introduction

Dans l’Univers de l’informatique, les logiciels qui tournent autour de l’astronomie sont nombreux. le logiciel de représentation du ciel Stellarium par exemple est une belle invitation à l’observation du ciel nocturne, on peut l’utiliser aussi comme base de données astronomiques.Celestia, le célèbre logiciel des amateurs d’astronomie, vous présente la carte d’identité universelle du système solaire avec toutes les données numériques et les caractéristiques des différents planètes.

En fait, s’ils sont nombreux, beaux à voir avec leurs images numériques illustratifs des phénomènes régnant les objets qu’ils présentent, ces logiciels ne présentent pas les données astronomiques sous forme propice à la compréhension d’un concept, à l’élaboration d’un modèle et à la réalisation d’une simulation.

En choisissant, le concept bien délimité de gravitation, la question précise que je pose alors et à laquelle j’essaye de répondre dans cet article est : Comment utiliser l’ordinateur pour passer de l’image qu’on se fait du ciel à la théorie de la gravitation universelle?

A l’heure actuelle, je ressent très fortement la nécessité d’une approche culturelle, scientifique et créative ayant pour objectif le développement de la capacité à appliquer la méthode scientifique dans un contexte pédagogique et simplifié.

Si la science utilise du vocabulaire spécifique et pointu, génère de grands nombres et des échelles de d’espace-temps qui dépassent notre imagination , sa vision du monde reste néanmoins qu’un ensemble de modèles et c’est ainsi que l’Homme explore le réel. Sur la base de ce constat simple, je divise mon approche au problème en trois étapes: Concept, Modèle et Simulation.

# La notion de Concept.

La théorie se fonde sur certains concepts comme la masse,la force, ou la charge électrique. ces concepts délimitent un certain champ de représentation qui est le monde que l’on veut décri et se représenter à partir de ces concepts. Il est clair par exemple que la notion de courant électrique échappe au champ de représentation de la mécanique newtonienne, puisque le concept de charge électrique n’existe pas dans la mécanique newtonienne.

A l’intérieur d’un champ de représentation , on élabore des lois à partir de ses concepts. Ces lois constituent l’ossature du modèle par lequel un phénomène est représenté.

# La notion de Modèle.

Un modèle est une représentation, en général simplifié, d’un phénomène ou d’un objet. Son élaboration succède à un ensemble d’observations qui circonscrivent, dénombrent et relient entre eux les divers concepts et propriétés du phénomène ou de l’objet. La comparaison et la confrontation du modèle aux observations testent et validité et la justesse de la représentation du phénomène ou de l’objet. L’acceptation de celui-ci est préservé tant que de nouvelles observations ne remettent pas en question validité. L’astronomie et la physique ont données naissance à de nombreux modèles. les plus simples mais aussi les plus révolutionnaires sont ceux de l’atome d’hydrogène et du système solaire.Ses deux systèmes ont de tout temps intrigué les Homme car ils représentent l’infiniment petit et l’infiniment grand, et à ce propos, je vous invite à lire excellent petit livre intitulé ‘Macro-Micro je mesure l’Univers’, que la Cité des Science à Tunis édite en langue arabe sous le titre ‘أقيس الكون’.

# La notion de Simulation.

La simulation désigne la mise en œuvre d’un modèle à l’aide de moyens informatiques (ordinateurs et logiciels). Elle se caractérise par la très grande souplesse qu’offre le calcul informatique à:

Incorporer les équations de la physique régissant le phénomène.
Remplacer les calculs impossibles à résoudre analytiquement par des approximations numériques.
Manipuler d’énormes volumes de nombres et d’opérations arithmétiques.
Visualiser les résultats sous formes graphiques.
Varier les conditions aux limites et les paramètres physiques d’un phénomène.
Compresser le temps de déroulement du phénomène étudié.

Parmi les triomphes de la simulation on a l’exemple du problème à N-corps. C’est l’étude des mouvements d’un grand nombre de corps en interaction gravitationnelle tels qu’on les rencontre dans les amas globulaires et dans les galaxies. Ce problème est fondamental en mécanique céleste. Pour N=2 (étoile double, étoile-Planète, planète-satellite) et N=3 (avec la condition que la masse d’un des corps soit négligeable par rapports aux autres, le système Soleil-Terre-Lune par exemple) on a pu exhibé des solutions analytiques mais pour N dépassant 3, le problème devient insoluble autrement que par des méthodes numériques utilisant des simulations sur ordinateurs. Grace à ces simulations, la forme et l’évolution dynamique de ces amas et de ces galaxies deviennent prévisibles et comparables à ce que révèle l’observation.

# Conclusion

Les ordinateurs offrent un moyen puissant de simulation de phénomènes astronomiques. Les programmer via un langage de programmation comme Python est une approche scientifique bénéfique pour les apprenants et amateurs d’astronomie.

Enfin, si la simulation vous tente, pour commencer, vous pouvez allez voir une des simulations étonnamment réalistes sous un ciel nocturne à 4000 étoiles, ou pourquoi pas survoler de près la planète Mars, et c’est bien sur au Planétarium de la Cité des Sciences à Tunis


# Bibliographie

1. Pierre-Lena, ‘Les sciences du ciel’, Flammarion 1996.p.85-89,462-463,560-561.
2. Jean Dunin-Borkowski, ‘ L’astronomie sur un écran d’ordinateur’, Cahiers Clairaut n°46,p.23-25.
3. Jean-Paul Rosenstiehl, ‘De Newton à Kepler … avec une calculette’, Cahiers Clairaut n°21,p.23-26.
4. Frédéric Neuville, ‘La gravitation’, Science et vie micro n°36,p.73-74.
5. Daniel Durand, ‘La systémique’, Que sais-je? presse universitaire de France 1992,p71-78.
6. Bruno Jarrosson, ‘Invitation à la philosophie des sciences’, collection Points sciences Editions du Seuil 1992,p.155-194.



