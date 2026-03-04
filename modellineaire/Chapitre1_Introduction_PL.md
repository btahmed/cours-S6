# Chapitre 1 -- Introduction a la Programmation Lineaire

**Cours : Modeles Lineaires de la Recherche Operationnelle**  
**Universite Clermont Auvergne -- Clermont Auvergne INP -- ISIMA**  
**Professeur : Herve KERIVIN**  
**Annee : 2024-2025**

---

## Table des matieres

1. [Resume](#1-resume)
2. [Explication detaillee](#2-explication-detaillee)
   - [Qu'est-ce qu'un programme lineaire ?](#21-quest-ce-quun-programme-lineaire-)
   - [Les 3 etapes de formulation](#22-les-3-etapes-de-formulation)
   - [Forme standard](#23-forme-standard)
   - [Conversions vers la forme standard](#24-conversions-vers-la-forme-standard)
   - [Interpretation geometrique](#25-interpretation-geometrique)
   - [Les 3 cas d'un PL](#26-les-3-cas-dun-pl)
3. [Exercices corriges](#3-exercices-corriges)
4. [Quiz](#4-quiz)

---

## 1. Resume

### Definitions fondamentales

| Terme | Definition |
|-------|-----------|
| **Programme lineaire (PL)** | Probleme d'optimisation avec fonction objectif lineaire, contraintes lineaires, variables reelles |
| **Solution realisable** | Point qui satisfait toutes les contraintes |
| **Region realisable** | Ensemble de toutes les solutions realisables (c'est un polyedre) |
| **Solution optimale** | Solution realisable qui maximise (ou minimise) la fonction objectif |
| **Valeur optimale** | Valeur de z a la solution optimale |

### Les 3 etapes de formulation

1. **Variables de decision** -- que cherche-t-on ?
2. **Contraintes** -- quelles sont les limites ?
3. **Fonction objectif** -- que veut-on optimiser ?

### Forme standard

Un PL est en forme standard si :
- **Maximisation**
- Toutes les contraintes en **<=**
- Toutes les variables **>= 0**

### Conversions

| Situation | Transformation |
|-----------|---------------|
| min w | max (-w) |
| contrainte >= | multiplier par -1 |
| contrainte = | deux inegalites (<= et >=) |
| x <= 0 | x = -y, y >= 0 |
| x libre | x = x+ - x-, x+, x- >= 0 |

### Les 3 cas possibles

1. **Infaisable** : region realisable vide
2. **Non borne** : z peut croitre indefiniment
3. **Solution optimale** : toujours atteinte en un sommet

---

## 2. Explication detaillee

### 2.1 Qu'est-ce qu'un programme lineaire ?

Un **programme lineaire** (PL) est un probleme mathematique ou l'on cherche a **optimiser** (maximiser ou minimiser) une fonction lineaire, sous des contraintes qui sont aussi lineaires.

**Forme generale :**

```
maximiser   z = c1*x1 + c2*x2 + ... + cn*xn

sujet a     a11*x1 + a12*x2 + ... + a1n*xn  <=  b1
            a21*x1 + a22*x2 + ... + a2n*xn  <=  b2
            ...
            am1*x1 + am2*x2 + ... + amn*xn  <=  bm

            x1, x2, ..., xn >= 0
```

Ou :
- **x1, x2, ..., xn** sont les **variables de decision** (ce qu'on cherche)
- **z** est la **fonction objectif** (ce qu'on optimise)
- **c1, ..., cn** sont les **coefficients de la fonction objectif**
- **aij** sont les **coefficients des contraintes**
- **b1, ..., bm** sont les **membres droits** (right-hand side)

**Pourquoi "lineaire" ?**
- La fonction objectif est une combinaison lineaire des variables (pas de x^2, pas de x1*x2, etc.)
- Chaque contrainte est une inegalite ou egalite lineaire

**Exemples de fonctions NON lineaires (interdites en PL) :**
- `x1 * x2` (produit de variables)
- `x1^2` (terme quadratique)
- `sin(x1)` (fonction trigonometrique)
- `|x1|` (valeur absolue -- mais on peut parfois la lineariser)

---

### 2.2 Les 3 etapes de formulation

La methodologie pour transformer un probleme reel en PL suit 3 etapes :

#### Etape 1 : Definir les variables de decision

C'est la question : **que devons-nous determiner ?**

Les variables representent les decisions a prendre. Elles doivent etre :
- Clairement definies (avec unites)
- Completes (couvrir toutes les decisions)
- Independantes

> **Exemple :** Une usine fabrique des tables et des chaises.  
> - x1 = nombre de tables a produire par semaine  
> - x2 = nombre de chaises a produire par semaine

#### Etape 2 : Identifier les contraintes

C'est la question : **quelles sont les limitations ?**

Les contraintes traduisent :
- Les **ressources limitees** (heures, matieres, budget) → inegalites <=
- Les **exigences minimales** (production minimale, normes) → inegalites >=
- Les **egalites** (equilibre offre/demande) → egalites =
- La **non-negativite** des variables : xi >= 0

> **Exemple (suite) :**  
> - Contrainte de bois : 10*x1 + 5*x2 <= 200 (200 unites de bois disponibles)  
> - Production minimale : x1 >= 5 (au moins 5 tables)  
> - Non-negativite : x1, x2 >= 0

#### Etape 3 : Definir la fonction objectif

C'est la question : **que voulons-nous optimiser ?**

- **Maximiser** le profit, le revenu, la production...
- **Minimiser** le cout, le temps, les dechets...

> **Exemple (suite) :**  
> - Profit par table : 80 euros  
> - Profit par chaise : 50 euros  
> - Objectif : maximiser z = 80*x1 + 50*x2

---

### 2.3 Forme standard

La **forme standard** est la forme de reference pour appliquer la methode du simplexe (Chapitre 2).

Un PL est en forme standard si et seulement si :

1. C'est un probleme de **maximisation**
2. Toutes les contraintes sont des **inegalites <=**
3. Toutes les variables sont **>= 0**

```
maximiser   z = c^T * x

sujet a     A * x <= b

            x >= 0
```

**Pourquoi cette forme ?**
- Elle permet d'ajouter des variables d'ecart pour obtenir un systeme d'egalites
- Le simplexe travaille sur ce systeme d'egalites

---

### 2.4 Conversions vers la forme standard

Tout PL peut etre converti en forme standard grace aux transformations suivantes :

#### Conversion 1 : Minimisation → Maximisation

```
minimiser z = 2x1 - 3x2
```

est equivalent a :

```
maximiser z' = -2x1 + 3x2
```

**Regle :** `min(z) = -max(-z)`. A la fin, inverser le signe de la valeur optimale.

#### Conversion 2 : Contrainte >= → Contrainte <=

```
3x1 + 2x2 >= 10
```

devient :

```
-3x1 - 2x2 <= -10
```

**Regle :** Multiplier les deux cotes par -1 (ce qui inverse le sens de l'inegalite).

#### Conversion 3 : Egalite → Deux inegalites

```
x1 + x2 = 5
```

devient :

```
x1 + x2 <= 5
-x1 - x2 <= -5
```

**Regle :** Une egalite est equivalente a "<= et >=", et on convertit le >= en <=.

#### Conversion 4 : Variable negative (x <= 0)

Si x2 <= 0, on pose **y2 = -x2** avec **y2 >= 0**.

On remplace x2 par -y2 partout dans le probleme.

#### Conversion 5 : Variable libre (x sans contrainte de signe)

Si x3 est libre, on la decompose en :

```
x3 = x3+ - x3-    avec    x3+ >= 0,    x3- >= 0
```

On remplace x3 par (x3+ - x3-) partout dans le probleme.

> **Attention :** Le nombre de variables augmente (une variable libre donne deux variables non-negatives).

#### Exemple complet de conversion

Probleme original :
```
minimiser   z = 2x1 - 3x2 + x3
sujet a     x1 + x2 - x3 = 5
            2x1 - x2 + 3x3 >= 10
            x1 >= 0,  x2 <= 0,  x3 libre
```

**Apres toutes les conversions :**

1. `min z` → `max z' = -2x1 + 3x2 - x3`
2. `x2 <= 0` → poser `y2 = -x2`, `y2 >= 0`
3. `x3 libre` → poser `x3 = x3+ - x3-`, `x3+, x3- >= 0`
4. Egalite → deux inegalites
5. Contrainte >= → multiplier par -1

Resultat en forme standard :
```
maximiser   z' = -2x1 - 3y2 - x3+ + x3-
sujet a     x1 - y2 - x3+ + x3-  <= 5
           -x1 + y2 + x3+ - x3-  <= -5
           -2x1 - y2 - 3x3+ + 3x3- <= -10
            x1, y2, x3+, x3- >= 0
```

**Pour retrouver la solution originale :**
- x1* = x1* (directement)
- x2* = -y2*
- x3* = x3+* - x3-*
- z* = -z'*

---

### 2.5 Interpretation geometrique

#### Demi-espaces et polyedres

Chaque contrainte lineaire definit un **demi-espace** dans R^n :

```
a1*x1 + a2*x2 + ... + an*xn <= b
```

C'est l'ensemble des points d'un cote de l'hyperplan `a1*x1 + ... + an*xn = b`.

La **region realisable** est l'**intersection** de tous les demi-espaces (y compris les non-negativites). Cette intersection forme un **polyedre** (polygone en 2D, polyedre en 3D, polytope en general).

#### Methode graphique (2 variables)

En 2 dimensions (x1, x2), on peut resoudre un PL graphiquement :

1. **Tracer les axes** et le premier quadrant (x1, x2 >= 0)
2. **Tracer chaque contrainte** comme une droite, identifier le bon cote
3. **Identifier la region realisable** (intersection de tous les demi-plans)
4. **Tracer les courbes de niveau** de z = c1*x1 + c2*x2 = k pour differentes valeurs de k
5. **Deplacer la courbe de niveau** dans la direction du gradient (c1, c2) jusqu'au dernier point de contact avec la region realisable

**Le point optimal est toujours un sommet** du polyedre (sauf si toute une arete est optimale -- solutions multiples).

#### Comment trouver les sommets ?

Un sommet est un point d'intersection de **n contraintes actives** (saturees, c'est-a-dire satisfaites avec egalite) dans R^n.

En 2D, un sommet est l'intersection de 2 droites de contraintes.

**Procedure :**
1. Enumerer toutes les paires de contraintes
2. Resoudre le systeme 2x2 pour chaque paire
3. Verifier que le point satisfait **toutes** les autres contraintes
4. Evaluer z a chaque sommet realisable
5. Le sommet avec la meilleure valeur est optimal

---

### 2.6 Les 3 cas d'un PL

Tout programme lineaire tombe dans exactement un de ces trois cas :

#### Cas 1 : Infaisable

La region realisable est **vide** -- il n'existe aucun point satisfaisant toutes les contraintes simultanement.

**Exemple :**
```
max   z = x1 + x2
s.c.  x1 + x2 <= 2
      x1 + x2 >= 5
      x1, x2 >= 0
```

Les deux premieres contraintes sont contradictoires : on ne peut pas avoir x1 + x2 <= 2 ET x1 + x2 >= 5 en meme temps.

#### Cas 2 : Non borne

Le probleme est realisable mais la fonction objectif peut croitre **indefiniment**. Il n'y a pas de maximum fini.

**Exemple :**
```
max   z = 2x1 + x2
s.c.  x1 - x2 <= 1
      x1, x2 >= 0
```

La region realisable est non bornee (elle s'etend a l'infini) et le gradient de z pointe dans une direction ou on peut toujours avancer.

#### Cas 3 : Solution optimale

Le cas le plus courant. Le PL a une solution realisable et z est bornee. D'apres le **Theoreme fondamental de la PL** :

> **Theoreme :** Si un programme lineaire a une solution optimale, alors au moins un **sommet** (point extreme) de la region realisable est optimal.

**Consequence pratique :** Il suffit d'examiner les sommets pour trouver l'optimum. C'est exactement ce que fait la methode du simplexe (Chapitre 2), mais de maniere intelligente sans enumerer tous les sommets.

---

## 3. Exercices corriges

### Exercice 1 : Formulation d'un probleme

**Enonce :**  
Une entreprise fabrique deux produits A et B.
- Le produit A necessite 2 heures de main-d'oeuvre et rapporte 30 euros de profit
- Le produit B necessite 3 heures de main-d'oeuvre et rapporte 40 euros de profit
- L'entreprise dispose de 120 heures de main-d'oeuvre par semaine
- Elle doit produire au moins 10 unites de A et 15 unites de B

**Question :** Formulez ce probleme comme un programme lineaire.

#### Solution

**Etape 1 : Variables de decision**
- x1 = nombre d'unites du produit A a produire par semaine
- x2 = nombre d'unites du produit B a produire par semaine

**Etape 2 : Contraintes**
- Main-d'oeuvre : `2x1 + 3x2 <= 120`
- Production minimale A : `x1 >= 10`
- Production minimale B : `x2 >= 15`
- Non-negativite : `x1, x2 >= 0` (deja inclus dans x1 >= 10 et x2 >= 15)

**Etape 3 : Fonction objectif**
- Maximiser le profit : `z = 30x1 + 40x2`

**Modele complet :**
```
maximiser   z = 30x1 + 40x2
sujet a     2x1 + 3x2 <= 120    (main-d'oeuvre)
            x1 >= 10              (minimum A)
            x2 >= 15              (minimum B)
            x1, x2 >= 0
```

**Resolution graphique -- sommets et valeurs :**

| Sommet | Coordonnees | z = 30x1 + 40x2 |
|--------|------------|-----------------|
| 1 | (10, 15) | 900 |
| 2 | (10, 100/3) | 1633 |
| 3 | (22.5, 25) | **1675 (OPTIMAL)** |
| 4 | (30, 15) | 1500 |

**Solution optimale : x1* = 22.5, x2* = 25, z* = 1675 euros**

---

### Exercice 2 : Conversion en forme standard

**Enonce :**  
Convertir en forme standard :
```
minimiser   z = 2x1 - 3x2 + x3
sujet a     x1 + x2 - x3 = 5
            2x1 - x2 + 3x3 >= 10
            x1 >= 0,  x2 <= 0,  x3 libre
```

#### Solution

**Transformation 1 -- Fonction objectif :**
```
max z' = -2x1 + 3x2 - x3
```

**Transformation 2 -- Variable x2 <= 0 :**  
Poser y2 = -x2, y2 >= 0. Remplacer x2 = -y2 partout.

**Transformation 3 -- Variable x3 libre :**  
Poser x3 = x3+ - x3-, x3+, x3- >= 0.

**Transformation 4 -- Egalite :**  
`x1 - y2 - x3+ + x3- = 5` se separe en deux inegalites <=.

**Transformation 5 -- Contrainte >= :**  
Multiplier par -1.

**Resultat final :**
```
maximiser   z' = -2x1 - 3y2 - x3+ + x3-
sujet a     x1 - y2 - x3+ + x3-   <= 5
           -x1 + y2 + x3+ - x3-   <= -5
           -2x1 - y2 - 3x3+ + 3x3- <= -10
            x1, y2, x3+, x3- >= 0
```

**Recuperation de la solution originale :**
- x2* = -y2*
- x3* = x3+* - x3-*
- z* = -z'*

---

### Exercice 3 : Resolution graphique

**Enonce :**
```
maximiser   z = 3x1 + 2x2
sujet a     x1 + x2 <= 4
            x1 + 2x2 <= 6
            x1, x2 >= 0
```

#### Solution

**Sommets de la region realisable :**

| Sommet | Coordonnees | Intersection de | z = 3x1 + 2x2 |
|--------|------------|-----------------|---------------|
| A | (0, 0) | x1 = 0, x2 = 0 | 0 |
| B | (0, 3) | x1 = 0, x1 + 2x2 = 6 | 6 |
| C | (2, 2) | x1 + x2 = 4, x1 + 2x2 = 6 | 10 |
| D | (4, 0) | x1 + x2 = 4, x2 = 0 | **12 (OPTIMAL)** |

**Verification du sommet D = (4, 0) :**
- x1 + x2 = 4 <= 4 (saturee)
- x1 + 2x2 = 4 <= 6 (non saturee, marge de 2)
- x1, x2 >= 0

**Solution optimale : x1* = 4, x2* = 0, z* = 12**

**Contraintes actives au point optimal :**
- x1 + x2 = 4 (saturee)
- x2 = 0 (saturee)

**Contraintes inactives :**
- x1 + 2x2 = 4 < 6 (marge de 2)

---

### Exercice 4 : Detection d'infaisabilite

**Enonce :**
```
max   z = x1 + x2
s.c.  x1 + x2 <= 2
      x1 + x2 >= 5
      x1, x2 >= 0
```

**Solution :** Le probleme est **infaisable**. Les contraintes x1 + x2 <= 2 et x1 + x2 >= 5 sont contradictoires. L'intersection des demi-plans correspondants est vide. Il n'existe aucun point (x1, x2) satisfaisant simultanement les deux contraintes.

---

### Exercice 5 : Probleme non borne

**Enonce :**
```
max   z = 2x1 + x2
s.c.  x1 - x2 <= 1
      x1, x2 >= 0
```

**Solution :** Le probleme est **non borne**. La region realisable s'etend a l'infini (par exemple le point (0, k) est realisable pour tout k >= 0 car 0 - k = -k <= 1). La fonction objectif z = 2(0) + k = k croit sans limite. Donc z n'a pas de maximum fini.

---

### Exercice 6 : Solutions multiples

**Enonce :**
```
max   z = 2x1 + 4x2
s.c.  x1 + 2x2 <= 8
      2x1 + x2 <= 10
      x1, x2 >= 0
```

**Solution :** Le gradient de z = (2, 4) est proportionnel au vecteur normal de la contrainte x1 + 2x2 <= 8 (qui est (1, 2)). Les courbes de niveau de z sont paralleles a cette contrainte. Donc **tous les points de l'arete** correspondant a x1 + 2x2 = 8 dans la region realisable sont optimaux. Il y a une **infinite de solutions optimales** avec la meme valeur z* = 16.

---

## 4. Quiz

### Questions de comprehension

**Q1.** Quelles sont les 3 caracteristiques d'un PL en forme standard ?

<details>
<summary>Reponse</summary>

1. Maximisation
2. Toutes les contraintes en <=
3. Toutes les variables >= 0
</details>

---

**Q2.** Comment convertir `minimiser z = 5x1 - 2x2` en un probleme de maximisation ?

<details>
<summary>Reponse</summary>

On maximise l'oppose : `maximiser z' = -5x1 + 2x2`. A la fin, z* = -z'*.
</details>

---

**Q3.** Comment traiter une variable x3 qui est libre (sans contrainte de signe) ?

<details>
<summary>Reponse</summary>

On la decompose : x3 = x3+ - x3-, avec x3+ >= 0 et x3- >= 0. On remplace x3 par (x3+ - x3-) partout dans le probleme.
</details>

---

**Q4.** Quels sont les 3 cas possibles pour un programme lineaire ?

<details>
<summary>Reponse</summary>

1. **Infaisable** : la region realisable est vide
2. **Non borne** : la fonction objectif peut croitre indefiniment
3. **Possede une solution optimale** : atteinte en au moins un sommet du polyedre
</details>

---

**Q5.** Pourquoi suffit-il d'examiner les sommets du polyedre pour trouver l'optimum ?

<details>
<summary>Reponse</summary>

D'apres le Theoreme fondamental de la PL : si un PL a une solution optimale, alors au moins un sommet (point extreme) de la region realisable est optimal. La fonction objectif lineaire atteint toujours son maximum en un sommet du polyedre convexe.
</details>

---

**Q6.** On a le PL :
```
max z = x1 + x2,  s.c. x1 + x2 <= 3, x1 - x2 >= 7, x1, x2 >= 0
```
Que peut-on dire de ce probleme ?

<details>
<summary>Reponse</summary>

Le probleme est **infaisable**. La contrainte x1 + x2 <= 3 avec x1 >= 0, x2 >= 0 impose x1 <= 3. La contrainte x1 - x2 >= 7 avec x2 >= 0 impose x1 >= 7. On ne peut pas avoir x1 <= 3 et x1 >= 7 simultanement.
</details>

---

**Q7.** Comment convertir la contrainte `3x1 + 2x2 >= 12` en forme standard ?

<details>
<summary>Reponse</summary>

On multiplie par -1 : `-3x1 - 2x2 <= -12`.
</details>

---

**Q8.** Comment convertir la contrainte `x1 + x2 = 5` en forme standard ?

<details>
<summary>Reponse</summary>

On la remplace par deux inegalites :
- `x1 + x2 <= 5`
- `-x1 - x2 <= -5`
</details>

---

**Q9.** En methode graphique, comment determine-t-on la direction dans laquelle z augmente ?

<details>
<summary>Reponse</summary>

On suit le **gradient** de z. Pour z = c1*x1 + c2*x2, le gradient est le vecteur (c1, c2). Les courbes de niveau z = k sont des droites perpendiculaires au gradient. On deplace la courbe de niveau dans la direction du gradient pour augmenter z.
</details>

---

**Q10.** Vrai ou Faux : Si un PL a une solution optimale, elle est toujours unique.

<details>
<summary>Reponse</summary>

**Faux.** Un PL peut avoir une infinite de solutions optimales (solutions multiples). Cela se produit quand la fonction objectif est parallele a une arete du polyedre. Tous les points de cette arete sont optimaux avec la meme valeur de z.
</details>

---

### Exercices d'entrainement

**E1.** Formulez le PL suivant :  
Un boulanger fabrique des croissants et des pains au chocolat. Un croissant coute 0.50 euros de matieres premieres et se vend 1.20 euros. Un pain au chocolat coute 0.80 euros et se vend 1.80 euros. Il dispose de 100 euros de budget et de 8 heures de travail. Un croissant prend 2 minutes et un pain au chocolat prend 3 minutes. Formulez le PL pour maximiser le profit.

<details>
<summary>Reponse</summary>

Variables : x1 = nb de croissants, x2 = nb de pains au chocolat.

```
maximiser   z = 0.70*x1 + 1.00*x2      (profit unitaire)
sujet a     0.50*x1 + 0.80*x2 <= 100   (budget matieres)
            2*x1 + 3*x2 <= 480          (temps en minutes, 8h = 480 min)
            x1, x2 >= 0
```
</details>

---

**E2.** Convertir en forme standard :
```
minimiser   z = x1 + 2x2
sujet a     x1 - x2 >= 3
            x1 + x2 = 7
            x1 >= 0, x2 libre
```

<details>
<summary>Reponse</summary>

1. max z' = -x1 - 2x2
2. x2 libre → x2 = x2+ - x2-, x2+, x2- >= 0
3. x1 - x2+ + x2- >= 3 → -x1 + x2+ - x2- <= -3
4. x1 + x2+ - x2- = 7 → deux inegalites

```
maximiser   z' = -x1 - 2x2+ + 2x2-
sujet a     -x1 + x2+ - x2-  <= -3
             x1 + x2+ - x2-  <= 7
            -x1 - x2+ + x2-  <= -7
             x1, x2+, x2- >= 0
```
</details>

---

## Erreurs courantes a eviter

1. **Oublier de verifier tous les sommets** -- toujours evaluer z a TOUS les sommets realisables
2. **Confondre sommet et point optimal** -- un sommet peut ne pas etre optimal
3. **Mal tracer les demi-plans** -- utiliser un point test (souvent l'origine) pour determiner le bon cote
4. **Erreur dans les intersections** -- toujours verifier que le point calcule satisfait TOUTES les contraintes
5. **Oublier les contraintes de non-negativite** -- elles definissent aussi des demi-plans
6. **Se tromper dans le sens de l'inegalite** apres multiplication par -1

---

*Chapitre suivant : [Chapitre 2 -- La Methode du Simplexe](Chapitre2_Methode_Simplexe.md)*
