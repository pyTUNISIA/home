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


# astropy003: Calcul du jour julien
![](https://raw.githubusercontent.com/pyTUNISIA/home/master/images/astropy/astropyTUNISIA.png)

Dans ce qui suit, je vous présente une méthode de conversion de dates données dans le calendrier Julien ou le calendrier Grégorien, vers le nombre Jour Julien correspondant, et vice versa.

# Généralités

Le jour julien (JJ) est un compte continu de jours et de fractions de jours depuis le début de l’année -4712. Par tradition et pour des raisons astronomiques, le JJ commence à minuit Greenwich, c.-à-d. à 12h temps Universel.

![](https://raw.githubusercontent.com/pyTUNISIA/home/master/images/astro/scientifiques/jfherschel.jpg)

C'est à l'astronome Anglais John Frederick William Herschel que l'on doit la création du Jour Julien tel que nous le connaissons aujourd'hui. Il le fait en 1849 dans un ouvrage qui fait référence chez les astronomes "Outlines of Astronomy".John Frederick William Herschel (1792-1871), créateur du Jour julien, était le fils du célébre astronome anglais William Herschel (1738-1822), fondateur de l'astronomie stellaire moderne à qui on lui doit la découverte d'Uranus et de deux de ses plus gros satellites. 

Le calendrier julien a été établit par l’empire romain par Jules César en l’an -45 et arrivé à sa forme finale aux  alentours de l’an +8. Nous suivons la pratique des astronomes qui consiste à extrapoler le calendrier julien indéfiniment au passé. Dans ce système, on peut parler, par exemple, d’une éclipse solaire du 28 Août de l’année -1203, bien qu’en ce temps là l’empire romain n’était pas fondé, encore moins le mois d’Août qui n’était pas conçu !

Dans la méthode décrite ci-dessous, la réforme grégorienne est prise en compte ; c.-à-d. le jour suivant le 4 Octobre 1582 (Calendrier Grégorien). Pour des recherches historiques il faut garder à l’esprit que cette réforme n’a pas était adopter officiellement par tous les pays en même temps. Elle n’entra en vigueur en 1582 qu’en Italie, en Espagne et au Portugal. La France ne l’adopta que deux ans plus tard, la Grande-Bretagne en 1752, et les autres pays d’Europe progressivement tout au long des siècles suivants.

Il y’a désaccord entre astronomes et historiens sur la manière de compter les années précédent l’année 1. Dans ce qui suit, les années avant  Jésus-Christ seront comptés astronomiquement. Ainsi, l’année avant l’année +1 est l’année zéro, celle d’avant est l’année -1. L’année que les historiens appellent 585 av JC est l’année -584 astronomique.

Le calcul arithmétique est le moyen le plus convenable pour le calcul astronomique. Ainsi le Jour Julie (JJ) permet de calculer facilement le nombre de jours entre deux dates aussi bien négatives que positives, de calculer les phases de la Lune, de situer sur un graphique un phénomène évoluant sur une longue période de temps, de révéler les années bissextiles, ou de déterminer le jour de semaine d’une date donnée…etc.

# Le Calcul astronomique

Pour calculer ce jour julien, je vous passe l’adresse de l’institut de mécanique céleste et de calcul des éphémérides (IMCCE) à la page Calcul du Jour Julien, il suffit de faire entrer la date et de cliquer sur calcul pour avoir le résultat. Et pour calculer ce jour julien, ou pour le programmer par vous-même, veuillez voir les deux algorithmes suivants.

## La méthode de calcul du Jour Julien.

1. Soit A l’année, M le mois et J le Jour avec ses décimales de la date à convertir en jour julien.
2. Si M=1 ou 2 alors remplacer A par A-1 et M par M+12.
3. Dans le calendrier Grégorien (date à partir du 15/10/1582), calculer a=Int( A / 100) et b= 2-a +Int(a/4)
4. Dans le calendrier Julien, prendre b=0.
5. Le Jour Julien cherché est JJ=Int (365.25 (A+4716))  + Int (30.6001 (M+1)) + J + b-1524.5

Exemple : Calculer le Jour julien correspondant au 1 er Janvier 2000.Réponse JJ=2451544.5.


## Calcul de dates à partir de jours juliens.

1. Ajouter 0.5 à JJ et prendre z sa partie entière et f sa partie fractionnaire (décimale)
2. Si  z < 2299161 alors prendre a = z
    Sinon calculer al = Int ((z – 1867216.25) / 36524.25)
    Et a = z + 1 + al – Int (al / 4)
3. Calculer  b = a + 1524    et c= Int ((b-122.1) / 365.25)d= Int (365.25 c)e= Int ((b-d) / 30.6001)
4. La date cherchée estJ= b-d-Int ( (30.6001 e) + f)
    Si e<14 alors M=e-1 sinon M=e-13Si M>2 alors A=c-4716 sinon A=c-4715.Exemple : Calculer la date correspondante au jour julien JJ=2440424Réponse : J=21.5, M=7 et A=1969, il s’agit du 21 Juillet 1969, date du premier débarquement humain sur la Lune.


# Programmation Python.
Commençons par deux routines qui déterminent si une date (j,m,a) est dans le calendrier Julien ou Grégorien.

```python @PyScript.repl
# Définitions des fonctions
#--------------------------
def isAfter15101582(j,m,a):
    if (a>1582): return True
    else:
        if (a==1582 and m>10): return True
        else:
            if (a==1582 and m==10 and j>=15): return True
            else: return False    
#    Méthode directe
def isBefore15101582(j,m,a):
    delta = a + m / 100 + j / 10000
    if delta  < 1582.1015: return True
    else: return False
```



```python @PyScript.repl
#----------------------
# Calcul du jour julien
#----------------------

# importer les fonctions floor et int du module math
from math import floor

def jmaTojj(j,m,a):
    '''
        Calcul jour julien jj à partir d'une date (j,m,a)
        j : jour décimal  (1..31)  avec virgule
        m : mois (1..12)
        a : année (-4712 ..+100000)
    
    '''
    if (m<3):
        m=m+12
        a=a-1
    _a=floor(a/100)
    if isAfter15101582(j,m,a):  
         b=2- _a + floor(_a/4)        
    else:
         b=0
    jj=floor(365.25*(a+4716))+ floor(30.6001*(m+1))+ j+b-1524.5
    return jj

def jjTojma(jj):
    Z=floor(jj+0.5)
    F=jj+0.5-Z
    if (Z< 2299161):
        A=Z
    else:
        alpha=floor( (Z - 1867216.25) / 36524.25  )
        A= Z +1 +alpha - floor(alpha/4)
    B= A + 1524
    C= floor( (B-122.1) / 365.25)
    D=floor(365.25*C)
    E=floor((B-D)/30.6001)
    
    j= B-D-floor(30.6001 *E)+F   # j : jour décimal
    
    if (E<14): m=E-1 
    else: m=E-13
    
    if (m>2): 
      a=C-4716
    else: a=C-4715
    return [j,m,a]

```

Faisons des tests sur des dates particulières :

```python @PyScript.repl
# Execution et tests
#--------------------
print('----------- Dates mémorables ---------------------')
print('Spoutnik : 04.81/10/1957  --> ',jmaTojj(4.81,10,1957))
print('J2000    : 01.5/01/2000   --> ',jmaTojj(1.5,1,2000))


print('----------- Date de départ  Jour Julien 0 --------')
print('Date de départ :  1.5/01/-4712  --> ',jmaTojj(1.5,1,-4712))


print('---------- Dates Spoutnik et J2000 -------------')
print(jjTojma(2436116.31))
print(jjTojma(2451545))

```



# Les applications 

**Calcul du nombre de jours écoulés  entre deux dates.**

La Comète Halley a passé au périhélie(le point le plus proche du soleil) le 20 Avril 1910 et le 9 février 1986, combien de jours se sont écoulés entre ces deux passages.

Réponse :
    
    20 Avril 1910   — >                  JJ =2418781.5
    
    9 février 1986  —- > JJ=2446470.5
    
    La différence est 27689 jours


**Trouver la date exacte, 10 000 jours après le 21 Mars 1996.**

Réponse :
    
21 Mars 1996 —- > JJ=2450163.5

On ajoute 10 000 jours
    
On trouve JJ=2460163.5

Qui correspond à  7 Août 2023.
    

**Calcul du jour de la semaine.**

A partir de la date (à 0 h), calculer le jour julien JJ,  ajouter 0.5 et diviser par 7. Le reste de la division indiquera le jour de la semaine comme suit :0 pour Lundi, 1 Mardi, 2 Mercredi, 3 Jeudi,  4 Vendredi, 5 Samedi  et 6 pour Dimanche.

Exemple :


14 janvier  2011  — > JJ=2455575,5


Jour de la semaine = (2455575,5+0.5) mod  7 = 4 ;


c’est un Vendredi et c’est le jour de la révolution tunisienne !


# Références 

1. https://fr.wikipedia.org/wiki/Jour_julien
2. Jean Meeus, Astronomical algorithms,Ed Willman-Bell 1991, p.59-66.
3. http://www.imcce.fr/fr/grandpublic/systeme/promenade/pages2/278.html


