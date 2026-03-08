# Chapitre 3 -- Degenerescence, Cyclage et Methode des Deux Phases

**Cours : Modeles Lineaires de la Recherche Operationnelle**  
**Universite Clermont Auvergne -- Clermont Auvergne INP -- ISIMA**  
**Professeur : Herve KERIVIN**  
**Annee : 2024-2025**

---

## Table des matieres

1. [Resume](#1-resume)
2. [Explication detaillee](#2-explication-detaillee)
   - [Les trois pieges du simplexe](#20-les-trois-pieges-de-la-methode-du-simplexe)
   - [Degenerescence](#21-degenerescence)
   - [Cyclage et methodes anti-cyclage](#22-cyclage-et-methodes-anti-cyclage)
   - [Methode des deux phases](#23-methode-des-deux-phases)
   - [Methode du grand M](#24-methode-du-grand-m)
   - [Theoreme fondamental et complexite](#25-theoreme-fondamental-et-complexite)
3. [Exercices corriges](#3-exercices-corriges)
4. [Quiz](#4-quiz)

---

## 1. Resume

### Concepts cles

| Concept | Definition |
|---------|-----------|
| **Solution degeneree** | Solution de base realisable ou au moins une variable de base vaut 0 |
| **Iteration degeneree** | Iteration du simplexe ou la valeur de z ne change pas (t* = 0) |
| **Cyclage** | Phenomene ou le simplexe revisite la meme base apres plusieurs iterations degenerees (boucle infinie) |
| **Regle de Bland** | Regle anti-cyclage : choisir la variable entrante/sortante avec le plus petit indice |
| **Methode lexicographique** | Regle anti-cyclage : comparer des vecteurs de ratios dans l'ordre lexicographique |
| **Methode de perturbation** | Regle anti-cyclage : perturber les bi avec des epsilon pour eliminer la degenerescence |
| **Methode des deux phases** | Technique pour trouver une solution de base realisable initiale quand b contient des valeurs negatives |
| **Methode du grand M** | Alternative aux deux phases utilisant une penalite M >> 0 |
| **Variable artificielle** | Variable ajoutee pour obtenir un dictionnaire initial realisable |

### Quand utiliser la methode des deux phases ?

Quand le dictionnaire initial n'est pas realisable, c'est-a-dire quand au moins un **bi < 0** apres mise en forme standard.

### Complexite du simplexe

- **En pratique** : tres rapide, environ O(m) a O(3m) iterations
- **Au pire cas** : exponentiel (exemple de Klee-Minty)
- **Alternatives polynomiales** : methode de l'ellipsoide, methodes de points interieurs

---

## 2. Explication detaillee

### 2.0 Les trois pieges de la methode du simplexe

La methode du simplexe repose sur trois etapes, et chacune peut poser un probleme :

| Etape | Question | Piege |
|-------|----------|-------|
| **Initialisation** | Comment obtenir un dictionnaire realisable de depart ? | L'origine n'est pas toujours realisable (bi < 0) |
| **Iteration** | Peut-on toujours choisir une variable entrante/sortante et pivoter ? | La degenerescence peut bloquer la progression |
| **Terminaison** | Le simplexe termine-t-il toujours ? | Le cyclage peut creer une boucle infinie |

Ce chapitre traite systematiquement ces trois difficultes :
- **Sections 2.1-2.2** : Degenerescence et cyclage (pieges d'iteration et terminaison)
- **Sections 2.3-2.4** : Methode des deux phases et Grand M (piege d'initialisation)
- **Section 2.5** : Theoreme fondamental et complexite

> **Important :** La degenerescence abonde dans les problemes de PL issus d'applications pratiques. Lorsqu'elle se produit, la methode peut **stagner** en traversant plusieurs iterations degenerees consecutives ; ce bloc se termine par une **percee** representee par une iteration non-degeneree.

---

### 2.1 Degenerescence

#### Definition

Une **solution de base realisable** est **degeneree** si au moins une variable de base vaut **0**.

Une **iteration degeneree** est une iteration du simplexe qui **ne change pas la solution de base** (la valeur de z reste inchangee).

**Geometriquement :** un sommet est degenere si plus de n contraintes sont actives (saturees) en ce point. Autrement dit, le sommet est a l'intersection de plus d'hyperplans que necessaire.

> **Pourquoi c'est un probleme ?** La motivation du simplexe est d'augmenter z a chaque iteration. Une iteration degeneree ne fait pas progresser z, ce qui peut conduire a un blocage (stagnation), voire un cyclage.

#### Exemple detaille de degenerescence (du cours)

**Dictionnaire realisable de depart :**
```
x4 = 1  - 2x3
x5 = 3  - 2x1 + 4x2 - 6x3
x6 = 2  + x1  - 3x2 - 4x3
z  = 2x1 - x2 + 8x3
```

**Variable entrante :** x3 (coefficient +8, le plus grand dans z).

**Ratios pour la variable sortante** (coefficients negatifs de x3 uniquement) :
- x4 : coeff de x3 = **-2** < 0, ratio = 1/2 = **0.5**
- x5 : coeff de x3 = **-6** < 0, ratio = 3/6 = **0.5**
- x6 : coeff de x3 = **-4** < 0, ratio = 2/4 = **0.5**

**Egalite parfaite** entre les trois ratios ! Par convention (plus petit indice), x4 sort. Apres pivot :

```
x3 = 1/2 - (1/2)x4
x5 = 0   - 2x1 + 4x2 + 3x4
x6 = 0   + x1  - 3x2 + 2x4
z  = 4   + 2x1 - x2  - 4x4
```

**Observation :** x5 et x6 valent 0 car elles ont atteint leur borne en meme temps que x4. **La solution est degeneree !** Mais z est passe de 0 a 4 : c'est une **iteration non degeneree** (z a augmente).

**Iteration suivante :** x1 entre (coeff +2 dans z).

Ratios (coefficients negatifs de x1) :
- x3 : pas de x1 dans l'equation (coeff = 0), pas de limite
- x5 : coeff de x1 = **-2** < 0, ratio = 0/2 = **0**
- x6 : coeff de x1 = **+1** > 0, on ignore (x6 augmente)

x5 sort (ratio = 0). Apres pivot :

```
x1 = 0   + 2x2 + (3/2)x4 - (1/2)x5
x3 = 1/2 - (1/2)x4
x6 = 0   - x2  + (7/2)x4 - (1/2)x5
z  = 4   + 3x2 - x4      - x5
```

**Solution inchangee :** x3 = 1/2, tous les autres a 0, z = 4. C'est une **iteration degeneree** : la solution n'a pas change.

> **Rappel important :** Dans le dictionnaire, on ne calcule le ratio que pour les lignes ou le coefficient de la variable entrante est **negatif** (car c'est cette variable de base qui diminue et risque de devenir negative). Les lignes a coefficient positif ne posent aucune limite car la variable de base augmente.

---

### 2.2 Cyclage et methodes anti-cyclage

#### Le phenomene de cyclage

Le **cyclage** se produit quand le simplexe effectue une serie d'iterations degenerees qui le ramenent a une base deja visitee. Il entre alors dans une **boucle infinie** sans jamais progresser vers l'optimum.

> **Theoreme 1 :** Si la methode du simplexe ne se termine pas, alors elle doit cycler.

**Le cyclage est rare en pratique** mais theoriquement possible. Le cyclage **ne peut se produire qu'en presence de degenerescence**. Pour le prevenir, on utilise des regles anti-cyclage.

#### Exemple classique de cyclage (6 iterations)

Voici l'exemple classique du cours qui montre un **vrai cyclage** en 6 iterations.

**Dictionnaire de depart :**
```
x5 = 0   - (1/2)x1 + (11/2)x2 + (5/2)x3 - 9x4
x6 = 0   - (1/2)x1 + (3/2)x2  + (1/2)x3 - x4
x7 = 1   - x1
z  = 10x1 - 57x2 - 9x3 - 24x4
```

Regles appliquees : variable entrante = plus grand coefficient dans z ; variable sortante = plus petit indice en cas d'egalite.

Solution courante : (0,0,0,0,0,0,1), z = 0. La solution est **degeneree** (x5 = 0, x6 = 0).

**Iteration 1 :** x1 entre (coeff +10).
- x5 : coeff de x1 = -1/2, ratio = 0/(1/2) = **0**
- x6 : coeff de x1 = -1/2, ratio = 0/(1/2) = **0**
- x7 : coeff de x1 = -1, ratio = 1/1 = 1
- Egalite entre x5 et x6 au ratio 0. Plus petit indice : **x5 sort**.

On isole x1 dans x5 : x1 = -2x5 + 11x2 + 5x3 - 18x4
```
x1 = 0   + 11x2 + 5x3 - 18x4 - 2x5
x6 = 0   - 4x2  - 2x3 + 8x4  + x5
x7 = 1   - 11x2 - 5x3 + 18x4 + 2x5
z  = 0   + 53x2 + 41x3 - 204x4 - 20x5
```
Bilan : base = {x1, x6, x7}. Solution inchangee (0,0,0,0,0,0,1). **z = 0** (degenere).

**Iteration 2 :** x2 entre (coeff +53).
- x1 : coeff de x2 = +11 > 0, pas de limite.
- x6 : coeff de x2 = -4, ratio = 0/4 = **0**
- x7 : coeff de x2 = -11, ratio = 1/11
- **x6 sort** (ratio 0).

On isole x2 dans x6 : x2 = -(1/4)x6 - (1/2)x3 + 2x4 + (1/4)x5
```
x2 = 0   - (1/2)x3 + 2x4 + (1/4)x5 - (1/4)x6
x1 = 0   - (1/2)x3 + 4x4 + (3/4)x5 - (11/4)x6
x7 = 1   + (1/2)x3 - 4x4 - (3/4)x5 + (11/4)x6
z  = 0   + (29/2)x3 - 98x4 - (27/4)x5 - (53/4)x6
```
Bilan : base = {x1, x2, x7}. Solution (0,0,0,0,0,0,1). **z = 0** (degenere).

**Iteration 3 :** x3 entre (coeff +29/2, seul positif).
- x2 : coeff de x3 = -1/2, ratio = 0/(1/2) = **0**
- x1 : coeff de x3 = -1/2, ratio = 0/(1/2) = **0**
- x7 : coeff de x3 = +1/2 > 0, pas de limite.
- Egalite entre x1 et x2. Plus petit indice : **x1 sort**.

Bilan : base = {x2, x3, x7}. **z = 0** (degenere).

**Iterations 4, 5, 6 (la boucle fatale) :**

| Iter. | Entrante | Sortante | Solution | z |
|-------|----------|----------|----------|---|
| 4 | x4 | x2 | (0,0,0,0,0,0,1) | 0 |
| 5 | x5 | x3 | (0,0,0,0,0,0,1) | 0 |
| 6 | x6 | x4 | (0,0,0,0,0,0,1) | 0 |

Apres 6 iterations, on retombe sur le **dictionnaire initial** avec la base {x5, x6, x7}. **C'est le cyclage !** L'algorithme va repeter ces 6 iterations indefiniment sans jamais progresser.

> **Comment s'en sortir ?** En appliquant la **regle de Bland** pour le choix de la variable **entrante** (et pas seulement la sortante), la sequence de pivots est differente et le cyclage disparait.

#### Regle de Bland (plus petit indice)

La regle la plus simple pour garantir l'absence de cyclage :

1. **Variable entrante** : parmi les variables hors-base avec un coefficient positif dans z, choisir celle de **plus petit indice**
2. **Variable sortante** : en cas d'egalite dans le test du ratio, choisir la variable de base de **plus petit indice**

> **Theoreme 2 (Bland, 1977) :** La methode du simplexe se termine tant que les variables entrante et sortante sont selectionnees par la regle du plus petit indice a chaque iteration.

**Inconvenient :** La regle de Bland peut etre computationnellement inefficace (long chemin du simplexe). On ne l'utilise que si necessaire.

##### Exemple : Application de la regle de Bland

Considerons le dictionnaire degenere suivant :

```
x4 = 0 - x1 - 2x2 + x3
x5 = 0 - 3x1 - x2 + 2x3
x6 = 6 - 2x1 + x2 - 3x3
z  = 4x1 + x2 + 2x3
```

Solution courante : x1 = x2 = x3 = 0, x4 = 0, x5 = 0, x6 = 6, z = 0.
C'est une solution **degeneree** (x4 = 0 et x5 = 0 sont des variables de base nulles).

**Iteration 1 :**
- **Variable entrante (Bland)** : parmi x1 (coeff +4), x2 (coeff +1), x3 (coeff +2), toutes positives dans z. On choisit celle de **plus petit indice** : **x1 entre**.
- **Variable sortante** : on cherche les lignes ou le coefficient de x1 est **negatif** (car augmenter x1 fait diminuer ces variables de base) :
  - x4 : coeff de x1 = **-1** < 0, ratio = 0/1 = **0**
  - x5 : coeff de x1 = **-3** < 0, ratio = 0/3 = **0**
  - x6 : coeff de x1 = **-2** < 0, ratio = 6/2 = **3**
- **Egalite** dans le ratio minimum (0 = 0) entre **x4** et **x5**. Bland : on choisit la variable de base de **plus petit indice** : **x4 sort** (4 < 5).

> Sans Bland, un choix arbitraire entre x4 et x5 pourrait potentiellement mener a un cyclage.

Pivot x1 entrant / x4 sortant. De x4 = 0 - x1 - 2x2 + x3, on tire x1 = -x4 - 2x2 + x3 :
```
x1 = 0 - x4 - 2x2 + x3
x5 = 0 + 3x4 + 5x2 - x3
x6 = 6 + 2x4 + 5x2 - 5x3
z  = 0 - 4x4 - 7x2 + 6x3
```

z reste a 0 (**iteration degeneree**). La base est maintenant {x1, x5, x6}.

**Iteration 2 :**
- Variables a coeff positif dans z : x3 (+6). Seul candidat : **x3 entre**.
- Lignes ou le coefficient de x3 est **negatif** :
  - x5 : coeff de x3 = **-1** < 0, ratio = 0/1 = **0**
  - x6 : coeff de x3 = **-5** < 0, ratio = 6/5 = **1.2**
  - x1 : coeff de x3 = **+1** > 0, on ignore (x1 augmente).
- Ratio minimum = 0 pour x5. **x5 sort**.

Pivot x3 entrant / x5 sortant. De x5 = 3x4 + 5x2 - x3, on tire x3 = 3x4 + 5x2 - x5 :
```
x3 = 0 + 3x4 + 5x2 - x5
x1 = 0 + 2x4 + 3x2 - x5
x6 = 6 - 13x4 - 20x2 + 5x5
z  = 0 + 14x4 + 23x2 - 6x5
```

z reste a 0 (**encore une iteration degeneree**). Base = {x1, x3, x6}.

**Iteration 3 :**
- Variables a coeff positif dans z : x4 (+14), x2 (+23). Bland : **x2 entre** (2 < 4).
- Lignes ou le coefficient de x2 est **negatif** :
  - x6 : coeff de x2 = **-20** < 0, ratio = 6/20 = **0.3**
  - x1 : coeff de x2 = **+3** > 0, on ignore.
  - x3 : coeff de x2 = **+5** > 0, on ignore.
- Seule x6 est candidate. **x6 sort**. Ratio = 0.3 : **z va augmenter de 23 x 0.3 = 6.9**.

Cette fois z progresse : **iteration non degeneree** ! Le simplexe a traverse 2 iterations degenerees grace a Bland sans cycler, puis a atteint une iteration productive.

#### Methode de perturbation

> **Idee :** Ajouter une petite valeur positive a chacune des valeurs du membre droit afin que ces petites valeurs n'operent pas a la meme echelle.

**Etape 1 :** Choisir m scalaires positifs epsilon_1, epsilon_2, ..., epsilon_m tels que :
```
0 < epsilon_m << epsilon_{m-1} << ... << epsilon_2 << epsilon_1 << 1
```

**Etape 2 :** Appliquer le simplexe au probleme perturbe ou chaque bi est remplace par bi + epsilon_i.

**Propriete cle :** Aucune combinaison lineaire des epsilon_i ne peut produire un nombre du meme ordre que les donnees, ni faire depasser epsilon_i par epsilon_j pour i > j. Cela **elimine toute egalite** dans les ratios.

##### Exemple : Application de la methode de perturbation

Reprenons le meme dictionnaire degenere :

```
x4 = 0 - x1 - 2x2 + x3
x5 = 0 - 3x1 - x2 + 2x3
x6 = 6 - 2x1 + x2 - 3x3
z  = 4x1 + x2 + 2x3
```

**Etape 1 : Perturbation des membres droits.** On choisit epsilon > 0 tres petit et on remplace chaque bi par bi + epsilon^i :

```
x4 = epsilon^1 - x1 - 2x2 + x3       (b1 = 0 -> 0 + epsilon)
x5 = epsilon^2 - 3x1 - x2 + 2x3      (b2 = 0 -> 0 + epsilon^2)
x6 = 6 + epsilon^3 - 2x1 + x2 - 3x3  (b3 = 6 -> 6 + epsilon^3)
z  = 4x1 + x2 + 2x3
```

Maintenant x4 = epsilon > 0 et x5 = epsilon^2 > 0 : **plus de degenerescence**.

**Etape 2 : Simplexe classique sur le probleme perturbe.**

Variable entrante x1 (plus grand coefficient +4 dans z). Ratios (coefficients negatifs de x1) :
- x4 : coeff = -1, ratio = epsilon / 1 = **epsilon**
- x5 : coeff = -3, ratio = epsilon^2 / 3 = **epsilon^2 / 3**
- x6 : coeff = -2, ratio = (6 + epsilon^3) / 2 ~ **3**

Pour epsilon suffisamment petit : epsilon^2/3 < epsilon < 3.
Le ratio minimum est **epsilon^2/3** : **x5 sort** (pas d'ambiguite !).

> **Remarque :** La perturbation donne un resultat different de Bland (qui faisait sortir x4). C'est normal : les methodes anti-cyclage ne donnent pas forcement le meme pivot, mais elles **garantissent toutes** l'absence de cyclage.

**Etape 3 : Passage a la limite.** A la fin, on fait tendre epsilon -> 0 dans la solution optimale obtenue, ce qui donne la solution optimale du probleme original.

**Remarque pratique :** On n'a pas besoin de choisir une valeur concrete pour epsilon. On raisonne symboliquement en traitant epsilon comme un infiniment petit, ce qui revient a la methode lexicographique.

#### Methode lexicographique

> **Theoreme 3 :** La methode du simplexe se termine tant que la variable sortante est selectionnee par la regle lexicographique a chaque iteration.

**Principe :** Pour chaque contrainte i, au lieu de calculer un seul ratio bi/|aie|, on construit un **vecteur** en divisant toute la ligne i par la **valeur absolue** du coefficient (negatif) de la variable entrante. On compare ensuite ces vecteurs dans l'**ordre lexicographique** et on choisit le **minimum**.

> **Ordre lexicographique :** Une expression r = (r0, r1, ..., rm) est lexicographiquement plus petite que s = (s0, s1, ..., sm) s'il existe un plus petit indice k tel que rk != sk et rk < sk.

##### Exemple : Application de la methode lexicographique

Reprenons le meme dictionnaire degenere :

```
x4 = 0 - x1 - 2x2 + x3       (ligne L1)
x5 = 0 - 3x1 - x2 + 2x3      (ligne L2)
x6 = 6 - 2x1 + x2 - 3x3      (ligne L3)
z  = 4x1 + x2 + 2x3
```

**Iteration 1 :** Variable entrante x1 (plus grand coefficient dans z).

On ne garde que les lignes ou le coefficient de x1 est **negatif** (candidates au ratio) et on divise par |coeff| :

| Ligne | Coeff de x1 | Vecteur = (bi, coeffs des autres hors-base) / |coeff| | Resultat |
|-------|-------------|-------------------------------------------------------|----------|
| L1 (x4) | -1 | (0, -2, 1) / 1 | **(0, -2, 1)** |
| L2 (x5) | -3 | (0, -1, 2) / 3 | **(0, -1/3, 2/3)** |
| L3 (x6) | -2 | (6, 1, -3) / 2 | **(3, 1/2, -3/2)** |

Comparaison lexicographique (on cherche le minimum) :
- L1 = (0, ...) vs L2 = (0, ...) vs L3 = (3, ...) : premiere composante, L1 et L2 egalite a 0, L3 = 3 (elimine).
- Deuxieme composante : L1 a **-2**, L2 a **-1/3**. Comme **-2 < -1/3**, L1 est lexicographiquement plus petit.

**L1 est le minimum lexicographique** : **x4 sort**.

L'avantage de cette methode : les egalites dans le ratio classique sont **toujours brisees** par les composantes suivantes du vecteur. Contrairement a Bland, on choisit toujours la variable entrante avec le plus grand coefficient, donc pas de penalite sur la vitesse.

#### Recapitulatif : Prevention du cyclage

| Methode | Auteur | Principe | Avantage | Inconvenient |
|---------|--------|----------|----------|--------------|
| **Plus petit indice (Bland)** | Bland (1977) | Choisir le plus petit indice pour entrante ET sortante | Simple a implementer | Peut rallonger le chemin du simplexe |
| **Perturbation** | Charnes (1952) | Perturber les bi pour eliminer la degenerescence | Elimine toute egalite | Couteux a implementer |
| **Lexicographique** | Dantzig, Orden, Wolfe (1955) | Comparer des vecteurs de ratios | Efficace en pratique | Plus complexe a programmer |

**En pratique :** Rencontrer des problemes qui cyclent est **extremement rare**. Les erreurs d'arrondi dans les implementations informatiques font que les membres droits mis a jour ne sont presque jamais exactement nuls. Les implementations modernes du simplexe tendent donc a ignorer la possibilite de cyclage.

---

### 2.3 Methode des deux phases

#### Probleme : quand le dictionnaire initial n'est pas realisable

La methode du simplexe necessite un **dictionnaire realisable** pour demarrer. Si certains membres droits bi sont negatifs, l'origine n'est pas realisable et on ne peut pas partir du dictionnaire initial standard.

**Geometriquement :** le point de depart (0,0,...,0) se trouve en dehors de la zone des solutions autorisees.

#### Le probleme auxiliaire

On construit un **probleme auxiliaire** en ajoutant une variable artificielle x0 :

1. On **soustrait x0** de toutes les contraintes (le probleme auxiliaire utilise -x0)
2. On definit un nouvel objectif : **maximiser w = -x0**
3. On fait entrer x0 dans la base en la pivotant avec la ligne qui a le **bi le plus negatif** (ce pivot d'initialisation n'est pas une iteration du simplexe)

Le probleme auxiliaire est :
```
maximiser   w = -x0
sujet a     somme(aij * xj) - x0 <= bi    pour i = 1,...,m
            x0 >= 0, xj >= 0
```

**Pourquoi le probleme auxiliaire est plus facile ?**
- Il est **toujours faisable** : il suffit de prendre x1 = x2 = ... = xn = 0 et x0 >= max(-bi)
- Le pivot d'initialisation rend tous les bi positifs -> dictionnaire realisable
- Si x0 est candidat a sortir de la base, on le choisit **toujours** comme variable sortante

> **Theoreme 4 :** Le probleme de PL original a une solution realisable si et seulement si la valeur optimale du probleme auxiliaire est zero.

**A la fin de la Phase 1 :**
- Si w* = 0 (x0 est hors-base, vaut 0) -> le probleme original est **realisable**, on passe a la Phase 2
- Si w* < 0 (x0 est en base, vaut > 0) -> le probleme original est **infaisable**, STOP

#### Exemple detaille : Resolution complete en deux phases

**Probleme :**
```
maximiser   z = x1 - x2 + x3
sujet a     2x1 - x2 + 2x3 <= 4
            2x1 - 3x2 + x3 <= -5
            -x1 + x2 - 2x3 <= -1
            x1, x2, x3 >= 0
```

**Dictionnaire initial :**
```
x4 = 4  - 2x1 + x2  - 2x3
x5 = -5 - 2x1 + 3x2 - x3
x6 = -1 + x1  - x2  + 2x3
z  = x1 - x2 + x3
```

**x5 = -5 < 0** et **x6 = -1 < 0** : l'origine n'est **pas realisable**.

##### Phase 1 : Probleme auxiliaire

On ajoute -x0 a chaque contrainte et on maximise w = -x0 :
```
x4 = 4  - 2x1 + x2  - 2x3 + x0
x5 = -5 - 2x1 + 3x2 - x3  + x0
x6 = -1 + x1  - x2  + 2x3 + x0
w  = -x0
```

**Pivot d'initialisation :** x0 entre, x5 sort (c'est la variable la plus infaisable : b = -5).

On isole x0 dans la ligne de x5 : x0 = 5 + 2x1 - 3x2 + x3 + x5. On substitue dans les autres :
```
x0 = 5  + 2x1 - 3x2 + x3  + x5
x4 = 9        - 2x2 - x3   + x5
x6 = 4  + 3x1 - 4x2 + 3x3 + x5
w  = -5 - 2x1 + 3x2 - x3  - x5
```

Dictionnaire realisable : x0 = 5, x4 = 9, x6 = 4. On applique le simplexe sur w.

**Iteration 1 (Phase 1) :** x2 entre (coeff +3 dans w, le plus grand).
- x0 : coeff de x2 = -3, ratio = 5/3
- x4 : coeff de x2 = -2, ratio = 9/2 = 4.5
- x6 : coeff de x2 = -4, ratio = 4/4 = 1 <-- **minimum**
- **x6 sort**.

Apres pivot :
```
x2 = 1 + (3/4)x1 + (3/4)x3 + (1/4)x5 - (1/4)x6
x0 = 2 - (1/4)x1 - (5/4)x3 + (1/4)x5 + (3/4)x6
x4 = 7 - (3/2)x1 - (5/2)x3 + (1/2)x5 + (1/2)x6
w  = -2 + (1/4)x1 + (5/4)x3 - (1/4)x5 - (3/4)x6
```

**Iteration 2 (Phase 1) :** x3 entre (coeff +5/4 dans w).
- x0 : coeff de x3 = -5/4, ratio = 2/(5/4) = 8/5 = 1.6
- x4 : coeff de x3 = -5/2, ratio = 7/(5/2) = 14/5 = 2.8
- x2 : coeff de x3 = +3/4 > 0, pas de limite.
- Minimum = 1.6 -> **x0 sort** (et en plus, x0 est toujours prioritaire pour sortir).

Apres pivot :
```
x3 = 1.6 - 0.2x1 + 0.2x5 + 0.6x6 - 0.8x0
x2 = 2.2 + 0.6x1 + 0.4x5 + 0.2x6 - 0.6x0
x4 = 3   - x1    - x6    + 2x0
w  = -x0
```

Tous les coefficients de w sont <= 0 -> **w* = 0**. Le probleme original est **realisable** !

Solution de Phase 1 : x1 = 0, x2 = 2.2, x3 = 1.6, x4 = 3.

##### Phase 2 : Optimisation

On supprime x0 du dictionnaire et on remplace w par l'objectif original z = x1 - x2 + x3.

On exprime z en fonction des variables hors-base en substituant x2 et x3 :
```
x3 = 1.6 - 0.2x1 + 0.2x5 + 0.6x6
x2 = 2.2 + 0.6x1 + 0.4x5 + 0.2x6
x4 = 3   - x1    - x6
z  = x1 - (2.2 + 0.6x1 + 0.4x5 + 0.2x6) + (1.6 - 0.2x1 + 0.2x5 + 0.6x6)
   = -0.6 + 0.2x1 - 0.2x5 + 0.4x6
```

Le simplexe continue sur ce dictionnaire. Coefficients positifs : x1 (+0.2), x6 (+0.4). On continue les iterations normalement jusqu'a l'optimal.

#### Ce que decouvrent les deux phases

- **Phase 1** : decouvre si le probleme est **infaisable**. Si le probleme est faisable, la Phase 1 livre une **solution de base realisable**.
- **Phase 2** : decouvre si le probleme est **non borne**. Si le probleme n'est pas non borne, la Phase 2 livre une **solution de base optimale**.

#### Variante : Variables artificielles (contraintes = et >=)

Quand le probleme contient des **contraintes d'egalite** ou des **inegalites >=**, il n'y a pas de variable de base evidente. On introduit alors des **variables artificielles**.

##### Quand utiliser les variables artificielles ?

| Type de contrainte | Variable d'ecart | Variable artificielle |
|---|---|---|
| **<=** | On ajoute +xi (ecart) | Pas besoin |
| **>=** | On soustrait -xi (surplus) | On ajoute yi (artificielle) |
| **=** | Pas de variable d'ecart | On ajoute yi (artificielle) |

##### Exemple avec contraintes mixtes

**Probleme :**
```
maximiser   z = x1 + 4x2
sujet a     x1 + 2x2 <= 14
            -x1 + 2x2 >= 5
            2x1 + 4x2 = 18
            x1, x2 >= 0
```

**Mise sous forme d'egalites :**
```
x1 + 2x2 + x3     = 14       (x3 : variable d'ecart)
-x1 + 2x2 - x4    = 5        (x4 : variable de surplus)
2x1 + 4x2         = 18       (pas de variable d'ecart)
```

Les 2 dernieres equations n'ont pas de variable de base naturelle -> on ajoute des **variables artificielles** y1 et y2.

**Probleme artificiel (Phase 1) :**
```
minimiser   w = y1 + y2     (ou equivalemment maximiser w = -y1 - y2)
sujet a     x1 + 2x2 + x3         = 14
            -x1 + 2x2 - x4 + y1   = 5
            2x1 + 4x2       + y2   = 18
            x1, x2, x3, x4, y1, y2 >= 0
```

Base de depart faisable : B = {x3, y1, y2} avec x3 = 14, y1 = 5, y2 = 18.

##### Resolution detaillee de la Phase 1

**Etape 0 : Dictionnaire initial.**
On exprime w en fonction des variables hors-base (x1, x2, x4) en substituant y1 et y2 :
```
w = -(5 + x1 - 2x2 + x4) - (18 - 2x1 - 4x2) = -23 + x1 + 6x2 - x4
```

Dictionnaire :
```
x3 = 14 - x1  - 2x2
y1 = 5  + x1  - 2x2 + x4
y2 = 18 - 2x1 - 4x2
w  = -23 + x1 + 6x2 - x4
```

**Iteration 1 :** x2 entre (coeff +6, le plus grand dans w).
- x3 : coeff de x2 = -2, ratio = 14/2 = 7
- y1 : coeff de x2 = -2, ratio = 5/2 = **2.5** <-- minimum
- y2 : coeff de x2 = -4, ratio = 18/4 = 4.5

**y1 sort.** On isole x2 = 5/2 + (1/2)x1 + (1/2)x4 - (1/2)y1 :
```
x2 = 5/2 + (1/2)x1 + (1/2)x4 - (1/2)y1
x3 = 9   - 2x1     - x4      + y1
y2 = 8   - 4x1     - 2x4     + 2y1
w  = -8  + 4x1     + 2x4     - 3y1
```

**Iteration 2 :** x1 entre (coeff +4 dans w).
- x2 : coeff de x1 = +1/2 > 0, pas de limite.
- x3 : coeff de x1 = -2, ratio = 9/2 = 4.5
- y2 : coeff de x1 = -4, ratio = 8/4 = **2** <-- minimum

**y2 sort.** On isole x1 = 2 - (1/2)x4 + (1/2)y1 - (1/4)y2 :
```
x1 = 2   - (1/2)x4 + (1/2)y1 - (1/4)y2
x2 = 7/2 + (1/4)x4 - (1/4)y1 - (1/8)y2
x3 = 5                        + (1/2)y2
w  = 0   - y1      - y2
```

**w = 0** et tous les coefficients de w sont <= 0 -> **optimal**. Les variables artificielles y1 et y2 sont hors-base (valent 0).

**Conclusion Phase 1 :** Le probleme original est **realisable**. Solution : x1 = 2, x2 = 3.5, x3 = 5.

##### Phase 2 : Optimisation

On raye y1 et y2, on restaure z = x1 + 4x2 et on substitue :
```
x1 = 2   - (1/2)x4
x2 = 7/2 + (1/4)x4
x3 = 5
z  = (2 - (1/2)x4) + 4(7/2 + (1/4)x4) = 2 - (1/2)x4 + 14 + x4 = 16 + (1/2)x4
```

x4 a un coefficient **+1/2 > 0** dans z. On peut encore augmenter z. On continue le simplexe :
- x4 entre. Ratios :
  - x1 : coeff = -1/2, ratio = 2/(1/2) = 4
  - x2 : coeff = +1/4 > 0, pas de limite
  - x3 : pas de x4, pas de limite
- x1 sort (ratio = 4, x4 = 4).

```
x4 = 4 - 2x1
x2 = 9/2 - (1/2)x1
x3 = 5
z  = 18 - x1
```

Tous les coefficients de z sont <= 0 -> **optimal** !

**Solution optimale : x1 = 0, x2 = 4.5, z* = 18.**

#### Procedure de Phase 1 avec variables artificielles

1. Resoudre le probleme artificiel par le simplexe
2. Si w* > 0 : le probleme original est **infaisable**
3. Si w* = 0 : le probleme original est **faisable**
   - Si toutes les variables artificielles sont hors-base : resoudre le probleme original depuis la base optimale de la Phase 1
   - Si certaines variables artificielles restent de base avec valeur zero (dictionnaire degenere) : tenter de les faire sortir par une serie de pivots ; il peut arriver que certaines doivent rester de base !

#### Algorithme complet de la methode du simplexe a deux phases

```
PHASE 1 -- Initialisation
  1. Former le probleme auxiliaire (ou artificiel)
  2. Construire un dictionnaire realisable par un pivot unique :
     x0 entre, la variable de base la plus infaisable sort
  3. Appliquer le simplexe au probleme auxiliaire
     (regle : x0 sort en priorite si ex-aequo)
  4. Si w* < 0 : le probleme est INFAISABLE -- STOP
  5. Si w* = 0 : extraire le dictionnaire realisable pour le probleme original

PHASE 2 -- Optimisation
  1. Exprimer la fonction objectif originale en variables hors-base
  2. Appliquer le simplexe jusqu'a ce que tous les coefficients dans z soient <= 0
  3. Si aucun candidat sortant n'existe : le probleme est NON BORNE -- STOP
  4. Sinon : la solution actuelle est OPTIMALE
```

---

### 2.4 Methode du grand M

#### Principe

La methode du grand M combine les Phases 1 et 2 en un seul probleme de PL.

**Etapes :**
1. Ecrire le probleme sous forme standard (maximisation avec toutes les contraintes <=)
2. Si la contrainte i a un membre droit bi negatif, soustraire une variable artificielle yi >= 0
3. Soit M un tres grand nombre positif ; ajouter -M*yi a la fonction objectif pour chaque variable artificielle
4. Eliminer les variables artificielles de la derniere ligne du dictionnaire ; appliquer le simplexe

> **Si une variable artificielle apparait comme variable de base non nulle dans la solution optimale, le probleme original est infaisable.**

#### Exemple : Application du grand M

**Probleme :**
```
maximiser   z = x1 + 2x2
sujet a     2x1 + 3x2 >= 6     =>     -2x1 - 3x2 <= -6
            3x1 + 2x2 <= 12
            x1, x2 >= 0
```

La premiere contrainte (sous forme <=) a un membre droit negatif (-6). On introduit y1 :
```
-2x1 - 3x2 + y1 = -6     (y1 >= 0)
```

La fonction objectif modifiee devient :
```
maximiser   z = x1 + 2x2 - M*y1
```

ou M >> 0 penalise fortement toute solution avec y1 > 0. Le simplexe va naturellement faire sortir y1 de la base.

#### Avantages et inconvenients

| | Deux Phases | Grand M |
|---|---|---|
| **Avantage** | Exact, pas de parametre a choisir | Un seul probleme a resoudre |
| **Inconvenient** | Deux problemes a resoudre | Erreurs numeriques possibles si M trop grand |
| **Preference** | Theorie et implementations | Calculs a la main |

---

### 2.5 Theoreme fondamental et complexite

#### Theoreme fondamental de la programmation lineaire

> **Theoreme 5 :** Tout probleme de PL sous forme standard possede les trois proprietes suivantes :
> 1. S'il n'a pas de solution optimale, alors il est soit **infaisable** soit **non borne**.
> 2. S'il a une solution realisable, alors il a une **solution de base realisable**.
> 3. S'il a une solution optimale, alors il a une **solution de base optimale**.

**Implication pratique :** Ce theoreme justifie pourquoi la methode du simplexe -- qui explore uniquement les solutions de base -- est **complete** : si une solution optimale existe, elle sera trouvee.

#### Complexite du simplexe

**En pratique :** Le simplexe est extremement rapide. Le nombre d'iterations est generalement de l'ordre de **O(m)** a **O(3m)** ou m est le nombre de contraintes. C'est bien inferieur au nombre total de sommets.

**Au pire cas (Klee-Minty, 1972) :** Il existe des exemples artificiels ou le simplexe visite **tous les 2^n sommets** d'un polyedre a n variables. Le simplexe est donc **exponentiel dans le pire cas**.

#### Le probleme de Klee-Minty

**Formulation generale :**
```
maximiser   somme_{j=1}^{n} 10^{n-j} * xj
sujet a     2 * somme_{j=1}^{i-1} 10^{i-j} * xj + xi <= 100^{i-1}   pour i = 1,...,n
            xj >= 0
```

La region realisable est une **distorsion du cube en n dimensions**.

- Avec la **regle du plus grand coefficient**, le simplexe passe par **tous les 2^n sommets**
- Avec la **regle de la plus grande augmentation**, le simplexe ne necessite que **2 dictionnaires** (1 iteration !)

##### Illustration sur un exemple a n = 3 variables

```
maximiser   z = 100x1 + 10x2 + x3
sujet a     x1 <= 1
            20x1 + x2 <= 100
            200x1 + 20x2 + x3 <= 10000
            x1, x2, x3 >= 0
```

| Regle | Nombre d'iterations | Complexite pire cas |
|-------|--------------------|--------------------|
| Plus grand coefficient | 7 (= 2^3 - 1) | 2^n (Klee-Minty, 1972) |
| Plus grande augmentation | 1 | exponentiel (Jeroslow, 1973) |
| Plus petit indice (Bland) | variable | exponentiel (Avis-Chvatal, 1978) |

> **Conclusion :** Aucune regle de pivot connue ne garantit un nombre polynomial d'iterations dans le pire cas. Mais en pratique le simplexe reste tres rapide.

#### Alternatives polynomiales

Deux algorithmes garantissent une complexite polynomiale :

1. **Methode de l'ellipsoide (Khachiyan, 1979)** : O(n^4 L) ou L est la taille de l'entree. Theoriquement polynomial mais lent en pratique.

2. **Methodes de points interieurs (Karmarkar, 1984)** : O(n^3.5 L). Competitives avec le simplexe pour les tres grands problemes.

**En pratique, le simplexe reste l'algorithme le plus utilise** car :
- Il est rapide sur la plupart des instances reelles
- Il fournit des informations utiles (solution duale, analyse de sensibilite)
- Les implementations sont tres optimisees

---

### Points cles a retenir

- La **degenerescence** conduit a des iterations qui ne font pas progresser z
- Le **cyclage** ne se produit qu'en presence de degenerescence et est evite par la regle de Bland ou la methode lexicographique
- La **methode a deux phases** permet de traiter les problemes sans origine realisable
- Le **theoreme fondamental** garantit que toute solution optimale est une solution de base optimale
- Le simplexe n'est **pas polynomial** dans le pire cas, mais reste tres efficace en pratique

---

## 3. Exercices corriges

### Exercice 1 : Identification de degenerescence

**Enonce :**
Pour le dictionnaire suivant :
```
x4 = 1  - 2x3
x5 = 3  - 2x1 + 4x2 - 6x3
x6 = 2  + x1  - 3x2 - 4x3
z  = 2x1 - x2 + 8x3
```

1. La solution de base actuelle est-elle degeneree ? Justifier.
2. Effectuer une iteration en choisissant x3 comme variable entrante. Le pivot est-il degenere ?
3. Effectuer une deuxieme iteration en choisissant x1 comme variable entrante. Ce pivot est-il degenere ? Pourquoi ?

#### Solution

**1.** Solution courante : x1 = x2 = x3 = 0 (hors-base), x4 = 1, x5 = 3, x6 = 2. Toutes les variables de base sont > 0. La solution **n'est pas degeneree**.

**2.** Variable entrante x3 (coeff +8 dans z). Ratios (coefficients negatifs de x3) :
- x4 : coeff = -2, ratio = 1/2 = **0.5**
- x5 : coeff = -6, ratio = 3/6 = **0.5**
- x6 : coeff = -4, ratio = 2/4 = **0.5**

Egalite parfaite ! Par convention (plus petit indice), x4 sort. t* = 0.5 > 0 donc z augmente de 8 x 0.5 = 4. **Le pivot est non degenere** (z passe de 0 a 4).

Apres pivot :
```
x3 = 1/2 - (1/2)x4
x5 = 0   - 2x1 + 4x2 + 3x4
x6 = 0   + x1  - 3x2 + 2x4
z  = 4   + 2x1 - x2  - 4x4
```

**Observation :** x5 = 0 et x6 = 0. La nouvelle solution **est degeneree**.

**3.** Variable entrante x1 (coeff +2). Ratios (coefficients negatifs de x1) :
- x3 : pas de x1 (coeff = 0), pas de limite
- x5 : coeff = -2, ratio = 0/2 = **0**
- x6 : coeff = +1 > 0, on ignore

x5 sort (ratio = 0). t* = 0 donc z ne change pas. **Le pivot est degenere** car la solution reste la meme (x3 = 1/2, tout le reste = 0, z = 4).

---

### Exercice 2 : Methode a deux phases

**Enonce :**
Resoudre par la methode du simplexe a deux phases :
```
maximiser   z = x1 - x2 + x3
sujet a     2x1 - x2 + 2x3 <= 4
            2x1 - 3x2 + x3 <= -5
            -x1 + x2 - 2x3 <= -1
            x1, x2, x3 >= 0
```

1. Identifier pourquoi l'origine n'est pas realisable.
2. Former le probleme auxiliaire et construire le premier dictionnaire realisable.
3. Resoudre la Phase 1. Quelle est la valeur optimale w* ?
4. Deduire le dictionnaire de depart pour la Phase 2 et resoudre.

#### Solution

**1. Dictionnaire initial et identification du probleme**

On introduit les variables d'ecart x4, x5, x6 :
```
x4 = 4  - 2x1 + x2  - 2x3
x5 = -5 - 2x1 + 3x2 - x3
x6 = -1 + x1  - x2  + 2x3
z  = x1 - x2 + x3
```
La solution de base courante est x1 = x2 = x3 = 0, d'ou x4 = 4, x5 = -5, x6 = -1. Comme **x5 < 0** et **x6 < 0**, l'origine n'est pas realisable. On ne peut pas demarrer le simplexe directement.

**2. Probleme auxiliaire et pivot d'initialisation**

On ajoute la variable x0 >= 0 a chaque contrainte et on maximise w = -x0 :
```
x4 = 4  - 2x1 + x2  - 2x3 + x0
x5 = -5 - 2x1 + 3x2 - x3  + x0
x6 = -1 + x1  - x2  + 2x3 + x0
w  = -x0
```

**Pivot d'initialisation :** x0 entre en base. La ligne avec le membre droit le plus negatif est x5 (b = -5), donc **x5 sort**. On exprime x0 a partir de la ligne x5 :

x0 = 5 + 2x1 - 3x2 + x3 + x5

Substitution dans les autres lignes :
```
x0 = 5  + 2x1 - 3x2 + x3  + x5
x4 = 9        - 2x2 - x3   + x5
x6 = 4  + 3x1 - 4x2 + 3x3 + x5
w  = -5 - 2x1 + 3x2 - x3  - x5
```

Verification : x0 = 5, x4 = 9, x6 = 4 >= 0. Premier dictionnaire realisable. ✓

**3. Phase 1 : maximiser w = -x0**

**Iteration 1.** w = -5 - 2x1 + 3x2 - x3 - x5. Coefficient positif : x2 (+3). **x2 entre.**

Ratio test (on cherche les coefficients negatifs de x2) :
- x0 : coeff = -3, ratio = 5/3 ≈ 1.67
- x4 : coeff = -2, ratio = 9/2 = 4.5
- x6 : coeff = -4, ratio = 4/4 = **1** ← minimum

**x6 sort.** t* = 1, w augmente de 3 × 1 = 3 (passe de -5 a -2).

On exprime x2 a partir de la ligne x6 :

x2 = 1 + (3/4)x1 + (3/4)x3 + (1/4)x5 - (1/4)x6

Substitution :
```
x0 = 2  - (1/4)x1 - (5/4)x3 + (1/4)x5 + (3/4)x6
x4 = 7  - (3/2)x1 - (5/2)x3 + (1/2)x5 + (1/2)x6
x2 = 1  + (3/4)x1 + (3/4)x3 + (1/4)x5 - (1/4)x6
w  = -2 + (1/4)x1 + (5/4)x3 - (1/4)x5 - (3/4)x6
```

Solution : x0 = 2, x4 = 7, x2 = 1, w = -2.

**Iteration 2.** w = -2 + (1/4)x1 + (5/4)x3 - (1/4)x5 - (3/4)x6. Coefficients positifs : x1 (+1/4), x3 (+5/4). Plus grand : **x3 entre**.

Ratio test (coefficients negatifs de x3) :
- x0 : coeff = -5/4, ratio = 2/(5/4) = 8/5 = **1.6** ← minimum
- x4 : coeff = -5/2, ratio = 7/(5/2) = 14/5 = 2.8
- x2 : coeff = +3/4 > 0, on ignore (x2 augmente quand x3 augmente)

**x0 sort.** t* = 1.6, w augmente de (5/4) × 1.6 = 2 (passe de -2 a 0).

On exprime x3 a partir de la ligne x0 :

x3 = 8/5 - (1/5)x1 + (1/5)x5 + (3/5)x6 - (4/5)x0

Substitution :
```
x3 = 8/5  - (1/5)x1 + (1/5)x5 + (3/5)x6 - (4/5)x0
x4 = 3    - x1                 - x6      + 2x0
x2 = 11/5 + (3/5)x1 + (2/5)x5 + (1/5)x6 - (3/5)x0
w  = -x0
```

w = -x0 : comme x0 >= 0, on a w <= 0. Avec x0 = 0 (hors-base), **w* = 0**. Le probleme original est realisable !

Solution de Phase 1 : x1 = 0, x2 = 11/5, x3 = 8/5, x4 = 3, x5 = 0, x6 = 0.

**4. Phase 2 : optimiser z = x1 - x2 + x3**

On supprime la colonne x0 du dictionnaire et on remplace w par l'objectif original z = x1 - x2 + x3. Comme x2 et x3 sont en base, on substitue :

z = x1 - (11/5 + (3/5)x1 + (2/5)x5 + (1/5)x6) + (8/5 - (1/5)x1 + (1/5)x5 + (3/5)x6)

z = (1 - 3/5 - 1/5)x1 + (-2/5 + 1/5)x5 + (-1/5 + 3/5)x6 + (-11/5 + 8/5)

```
x3 = 8/5  - (1/5)x1 + (1/5)x5 + (3/5)x6
x4 = 3    - x1                 - x6
x2 = 11/5 + (3/5)x1 + (2/5)x5 + (1/5)x6
z  = -3/5 + (1/5)x1 - (1/5)x5 + (2/5)x6
```

z = -3/5 actuellement (x1 = x5 = x6 = 0).

**Iteration 1 (Phase 2).** z = -3/5 + (1/5)x1 - (1/5)x5 + (2/5)x6. Coefficients positifs : x1 (+1/5), x6 (+2/5). Plus grand : **x6 entre**.

Ratio test (coefficients negatifs de x6) :
- x3 : coeff = +3/5 > 0, on ignore
- x4 : coeff = -1, ratio = 3/1 = **3** ← seul candidat
- x2 : coeff = +1/5 > 0, on ignore

**x4 sort.** t* = 3, z augmente de (2/5) × 3 = 6/5 (passe de -3/5 a 3/5).

On exprime x6 a partir de la ligne x4 :

x6 = 3 - x1 - x4

Substitution :
```
x3 = 17/5 - (4/5)x1 + (1/5)x5 - (3/5)x4
x6 = 3    - x1                 - x4
x2 = 14/5 + (2/5)x1 + (2/5)x5 - (1/5)x4
z  = 3/5  - (1/5)x1 - (1/5)x5 - (2/5)x4
```

Tous les coefficients dans z sont **negatifs** → **solution optimale atteinte !**

**Solution optimale :**
| Variable | Valeur |
|----------|--------|
| x1 | 0 |
| x2 | 14/5 = 2.8 |
| x3 | 17/5 = 3.4 |
| **z*** | **3/5 = 0.6** |

**Verification :**
- z = 0 - 2.8 + 3.4 = 0.6 ✓
- Contrainte 1 : 2(0) - 2.8 + 2(3.4) = 4 <= 4 ✓ (saturee, x4 = 0)
- Contrainte 2 : 2(0) - 3(2.8) + 3.4 = -5 <= -5 ✓ (saturee, x5 = 0)
- Contrainte 3 : -(0) + 2.8 - 2(3.4) = -4 <= -1 ✓ (marge = 3 = x6)

---

### Exercice 3 : Methode des deux phases (probleme a deux variables)

**Enonce :**
Resoudre par la methode du simplexe a deux phases :
```
maximiser   z = -2x1 - x2
sujet a     -x1 + x2 <= -1
             x1 - 2x2 <= 4
             x1, x2 >= 0
```

1. Identifier pourquoi l'origine n'est pas realisable.
2. Former le probleme auxiliaire et construire le premier dictionnaire realisable.
3. Resoudre la Phase 1.
4. Deduire le dictionnaire de depart pour la Phase 2 et trouver la solution optimale.

#### Solution

**1. Dictionnaire initial et identification du probleme**

On introduit les variables d'ecart x3 et x4 :
```
x3 = -1 + x1 - x2
x4 = 4  - x1 + 2x2
z  = -2x1 - x2
```

La solution de base courante est x1 = x2 = 0, d'ou x3 = -1 et x4 = 4. Comme **x3 = -1 < 0**, l'origine n'est pas realisable. On ne peut pas demarrer le simplexe directement.

> **Remarque :** La contrainte -x1 + x2 <= -1 equivaut a x1 - x2 >= 1. Avec x1 = x2 = 0, on a 0 >= 1 qui est faux. L'origine viole cette contrainte.

**2. Probleme auxiliaire et pivot d'initialisation**

On ajoute la variable x0 >= 0 a chaque contrainte (avec un signe +) et on maximise w = -x0 :
```
x3 = -1 + x1 - x2 + x0
x4 = 4  - x1 + 2x2 + x0
w  = -x0
```

**Pivot d'initialisation :** x0 entre en base. La seule ligne avec un membre droit negatif est x3 (b = -1), donc **x3 sort**. On exprime x0 a partir de la ligne x3 :

x0 = 1 - x1 + x2 + x3

Substitution dans les autres lignes :
```
x0 = 1  - x1 + x2  + x3
x4 = 4  - x1 + 2x2 + (1 - x1 + x2 + x3) = 5 - 2x1 + 3x2 + x3
w  = -(1 - x1 + x2 + x3) = -1 + x1 - x2 - x3
```

Verification : x0 = 1, x4 = 5 >= 0. Premier dictionnaire realisable pour le probleme auxiliaire.

**3. Phase 1 : maximiser w = -x0**

**Iteration 1.** w = -1 + x1 - x2 - x3. Seul coefficient positif : x1 (+1). **x1 entre.**

Ratio test (on cherche les coefficients negatifs de x1) :
- x0 : coeff = -1, ratio = 1/1 = **1**
- x4 : coeff = -2, ratio = 5/2 = **2.5**

Minimum = 1 pour x0. De plus, x0 est toujours prioritaire pour sortir. **x0 sort.**

On exprime x1 a partir de la ligne x0 :

x1 = 1 + x2 + x3 - x0

Substitution :
```
x1 = 1  + x2  + x3  - x0
x4 = 5  - 2(1 + x2 + x3 - x0) + 3x2 + x3 = 3 + x2 - x3 + 2x0
w  = -1 + (1 + x2 + x3 - x0) - x2 - x3 = -x0
```

w = -x0 : comme x0 >= 0, on a w <= 0. Avec x0 = 0 (hors-base), **w* = 0**. Tous les coefficients de w sont <= 0 (il n'y a que -1 devant x0). L'algorithme s'arrete.

**Conclusion Phase 1 :** Le probleme original est **realisable**.

Solution de Phase 1 : x1 = 1, x2 = 0, x3 = 0, x4 = 3.

> **Verification de la realisabilite :** x1 = 1, x2 = 0 donne : contrainte 1 : -(1) + 0 = -1 <= -1 (saturee). Contrainte 2 : 1 - 0 = 1 <= 4.

**4. Phase 2 : optimiser z = -2x1 - x2**

On supprime la colonne x0 du dictionnaire. Le dictionnaire realisable est :
```
x1 = 1 + x2 + x3
x4 = 3 + x2 - x3
```

On remplace w par l'objectif original z = -2x1 - x2. Comme x1 est en base, on substitue :

z = -2(1 + x2 + x3) - x2 = -2 - 2x2 - 2x3 - x2 = -2 - 3x2 - 2x3

```
x1 = 1  + x2  + x3
x4 = 3  + x2  - x3
z  = -2 - 3x2 - 2x3
```

Tous les coefficients dans z sont **negatifs** (-3 pour x2, -2 pour x3) → **solution optimale atteinte immediatement** ! Aucune iteration de Phase 2 n'est necessaire.

**Solution optimale :**
| Variable | Valeur |
|----------|--------|
| x1 | 1 |
| x2 | 0 |
| x3 | 0 (ecart contrainte 1, saturee) |
| x4 | 3 (ecart contrainte 2) |
| **z*** | **-2** |

**Verification :**
- z = -2(1) - 0 = -2 ✓
- Contrainte 1 : -(1) + 0 = -1 <= -1 ✓ (saturee, x3 = 0)
- Contrainte 2 : 1 - 2(0) = 1 <= 4 ✓ (marge = 3 = x4)

> **Observation :** Dans ce probleme, la fonction objectif z = -2x1 - x2 a tous ses coefficients negatifs dans les variables originales. Intuitivement, on veut minimiser x1 et x2. Mais la contrainte x1 - x2 >= 1 force x1 >= 1. Le minimum est donc atteint en x1 = 1, x2 = 0 (on minimise x1 au maximum permis par les contraintes). La Phase 1 a suffi a trouver ce point, et la Phase 2 a confirme son optimalite immediatement.

---

### Exercice 4 : Probleme avec variables artificielles

**Enonce :**
Resoudre par la methode a deux phases (avec variables artificielles) :
```
maximiser   z = x1 + 4x2
sujet a     x1 + 2x2 <= 14
            -x1 + 2x2 >= 5
            2x1 + 4x2 = 18
            x1, x2 >= 0
```

**Indication :** La contrainte -x1 + 2x2 >= 5 devient x1 - 2x2 <= -5 en forme standard, ce qui necessite une variable artificielle.

#### Solution

**Mise sous forme standard.** On convertit toutes les contraintes en egalites :
```
x1 + 2x2 + x3       = 14       (x3 : variable d'ecart, contrainte <=)
-x1 + 2x2     - x4  = 5        (x4 : variable de surplus, contrainte >=)
2x1 + 4x2           = 18       (egalite, pas de variable d'ecart)
```

Les equations 2 et 3 n'ont pas de variable de base naturelle (x4 a un coefficient -1, pas +1). On introduit des **variables artificielles** y1 et y2.

**Phase 1 : Probleme artificiel**

```
maximiser   w = -y1 - y2
sujet a     x1 + 2x2 + x3         = 14
            -x1 + 2x2 - x4 + y1   = 5
            2x1 + 4x2       + y2   = 18
```

Base initiale : B = {x3, y1, y2}, avec x3 = 14, y1 = 5, y2 = 18.

**Dictionnaire initial.** On exprime w en fonction des hors-base en substituant y1 et y2 :

w = -(5 + x1 - 2x2 + x4) - (18 - 2x1 - 4x2) = -23 + x1 + 6x2 - x4

```
x3 = 14 - x1  - 2x2
y1 = 5  + x1  - 2x2 + x4
y2 = 18 - 2x1 - 4x2
w  = -23 + x1 + 6x2 - x4
```

**Iteration 1.** x2 entre (coeff +6, le plus grand).

Ratio test (coefficients negatifs de x2) :
- x3 : coeff = -2, ratio = 14/2 = 7
- y1 : coeff = -2, ratio = 5/2 = **2.5** ← minimum
- y2 : coeff = -4, ratio = 18/4 = 4.5

**y1 sort.** x2 = 5/2 + (1/2)x1 + (1/2)x4 - (1/2)y1.

```
x2 = 5/2 + (1/2)x1 + (1/2)x4 - (1/2)y1
x3 = 9   - 2x1     - x4      + y1
y2 = 8   - 4x1     - 2x4     + 2y1
w  = -8  + 4x1     + 2x4     - 3y1
```

**Iteration 2.** x1 entre (coeff +4 dans w).

Ratio test (coefficients negatifs de x1) :
- x2 : coeff = +1/2 > 0, on ignore
- x3 : coeff = -2, ratio = 9/2 = 4.5
- y2 : coeff = -4, ratio = 8/4 = **2** ← minimum

**y2 sort.** x1 = 2 - (1/2)x4 + (1/2)y1 - (1/4)y2.

```
x1 = 2   - (1/2)x4 + (1/2)y1 - (1/4)y2
x2 = 7/2 + (1/4)x4 - (1/4)y1 - (1/8)y2
x3 = 5                        + (1/2)y2
w  = 0   - y1      - y2
```

w = 0, tous les coefficients de w sont <= 0 → **optimal**. Les variables artificielles y1 et y2 sont hors-base (valent 0) → **w* = 0**, le probleme est realisable.

Solution Phase 1 : x1 = 2, x2 = 7/2 = 3.5, x3 = 5.

**Phase 2 : Optimisation de z = x1 + 4x2**

On supprime les colonnes y1 et y2 et on restaure z en substituant :

z = x1 + 4x2 = (2 - (1/2)x4) + 4(7/2 + (1/4)x4) = 2 - (1/2)x4 + 14 + x4 = 16 + (1/2)x4

```
x1 = 2   - (1/2)x4
x2 = 7/2 + (1/4)x4
x3 = 5
z  = 16  + (1/2)x4
```

Le coefficient de x4 est **+1/2 > 0** dans z → on peut encore augmenter z. x4 entre.

Ratio test (coefficients negatifs de x4) :
- x1 : coeff = -1/2, ratio = 2/(1/2) = **4** ← seul candidat
- x2 : coeff = +1/4 > 0, on ignore
- x3 : pas de x4, pas de limite

**x1 sort.** x4 = 4 - 2x1. Substitution :

```
x4 = 4   - 2x1
x2 = 9/2 - (1/2)x1
x3 = 5
z  = 18  - x1
```

Tous les coefficients de z sont **<= 0** → **solution optimale atteinte !**

**Solution optimale :**
| Variable | Valeur |
|----------|--------|
| x1 | 0 |
| x2 | 9/2 = 4.5 |
| x3 | 5 (ecart contrainte 1) |
| x4 | 4 (surplus contrainte 2) |
| **z*** | **18** |

**Verification :**
- z = 0 + 4(4.5) = 18 ✓
- Contrainte 1 : 0 + 2(4.5) = 9 <= 14 ✓ (marge = 5 = x3)
- Contrainte 2 : -0 + 2(4.5) = 9 >= 5 ✓ (surplus = 4 = x4)
- Contrainte 3 : 2(0) + 4(4.5) = 18 = 18 ✓

---

### Exercice 5 : Regle de Bland

**Enonce :**
Reprendre l'exemple de cyclage de la Section 2.2 et appliquer la **regle de Bland** (plus petit indice pour la variable entrante et la variable sortante).

1. Montrer que l'algorithme ne cycle plus.
2. Comparer le nombre d'iterations avec la regle du plus grand coefficient.

#### Solution

**Dictionnaire de depart :**
```
x5 = 0   - (1/2)x1 + (11/2)x2 + (5/2)x3 - 9x4
x6 = 0   - (1/2)x1 + (3/2)x2  + (1/2)x3 - x4
x7 = 1   - x1
z  = 10x1 - 57x2 - 9x3 - 24x4
```

**Avec Bland :** Variable entrante = plus petit indice avec coefficient positif dans z.

Seul x1 a un coefficient positif (+10). x1 entre (meme choix que la regle classique a l'iteration 1).

- x5 : coeff = -1/2, ratio = 0/(1/2) = 0
- x6 : coeff = -1/2, ratio = 0/(1/2) = 0
- x7 : coeff = -1, ratio = 1/1 = 1
- Egalite entre x5 et x6. Bland : x5 sort (5 < 6).

Apres pivot (meme dictionnaire que l'iteration 1 classique) :
```
x1 = 0   + 11x2 + 5x3 - 18x4 - 2x5
x6 = 0   - 4x2  - 2x3 + 8x4  + x5
x7 = 1   - 11x2 - 5x3 + 18x4 + 2x5
z  = 0   + 53x2 + 41x3 - 204x4 - 20x5
```

**Iteration 2 (Bland) :** Variables a coeff positif dans z : x2 (+53), x3 (+41). Plus petit indice : **x2 entre** (au lieu de x2 aussi dans la regle classique, mais la regle est bien appliquee).

- x1 : coeff de x2 = +11 > 0, pas de limite
- x6 : coeff de x2 = -4, ratio = 0/4 = 0
- x7 : coeff de x2 = -11, ratio = 1/11

x6 sort. Meme resultat que la regle classique pour cette iteration.

La difference apparait a l'**iteration 3** : avec Bland, le choix de la variable entrante (plus petit indice) et sortante (plus petit indice en cas d'egalite) produit une sequence de pivots differente qui **empeche le retour au dictionnaire initial**. L'algorithme finit par atteindre une iteration non degeneree et converge.

**Comparaison :** Avec la regle du plus grand coefficient, le simplexe **cycle indefiniment** (6 iterations en boucle). Avec Bland, il **termine en un nombre fini d'iterations**.

---

## 4. Quiz

### Questions de comprehension

**Q1.** Qu'est-ce qu'une solution de base degeneree ?

<details>
<summary>Reponse</summary>

C'est une solution de base realisable ou au moins une **variable de base vaut 0**. Geometriquement, le sommet correspondant a plus de contraintes actives que necessaire.
</details>

---

**Q2.** Qu'est-ce que le cyclage et pourquoi est-il problematique ?

<details>
<summary>Reponse</summary>

Le cyclage est le phenomene ou le simplexe effectue une serie d'iterations degenerees (z ne change pas) et finit par revenir a une base deja visitee, entrant dans une **boucle infinie**. Le simplexe ne converge alors jamais vers la solution optimale.
</details>

---

**Q3.** Enoncez la regle de Bland.

<details>
<summary>Reponse</summary>

1. **Variable entrante** : parmi les variables hors-base avec coefficient positif dans z, choisir celle de **plus petit indice**
2. **Variable sortante** : en cas d'egalite dans le ratio test, choisir celle de **plus petit indice**

Cette regle garantit que le simplexe termine en un nombre fini d'iterations (pas de cyclage).
</details>

---

**Q4.** Quand doit-on utiliser la methode des deux phases ?

<details>
<summary>Reponse</summary>

Quand le dictionnaire initial n'est pas realisable, c'est-a-dire quand au moins un membre droit **bi < 0** dans le dictionnaire initial. La Phase 1 trouve une solution realisable (ou detecte l'infaisabilite), et la Phase 2 optimise.
</details>

---

**Q5.** Comment sait-on que le probleme original est infaisable a la fin de la Phase 1 ?

<details>
<summary>Reponse</summary>

Si la valeur optimale du probleme auxiliaire est **w* < 0** (equivalemment, si la variable artificielle x0* > 0 a l'optimum). Cela signifie qu'il est impossible de satisfaire toutes les contraintes du probleme original.
</details>

---

**Q6.** Quelle est la difference entre la methode des deux phases et la methode du grand M ?

<details>
<summary>Reponse</summary>

- **Deux phases** : on resout d'abord un probleme auxiliaire (Phase 1) pour trouver une base realisable, puis on optimise le vrai objectif (Phase 2). Methode exacte.
- **Grand M** : on ajoute des variables artificielles avec un coefficient -M (tres grand) dans l'objectif et on resout un seul probleme. Plus simple mais peut poser des problemes numeriques si M est mal choisi.
</details>

---

**Q7.** Le simplexe est-il un algorithme polynomial ?

<details>
<summary>Reponse</summary>

**Non.** Le simplexe est exponentiel dans le pire cas (exemple de Klee-Minty : 2^n iterations pour n variables). Cependant, en pratique il est tres rapide avec environ O(m) a O(3m) iterations. Des algorithmes polynomiaux existent : methode de l'ellipsoide (1979) et methodes de points interieurs (1984).
</details>

---

**Q8.** Qu'est-ce que l'exemple de Klee-Minty ?

<details>
<summary>Reponse</summary>

C'est un hypercube legerement deforme construit par Klee et Minty en 1972. Pour n variables, le simplexe (avec la regle du plus grand coefficient) visite les **2^n sommets** avant d'atteindre l'optimum. Cela prouve que le simplexe est exponentiel dans le pire cas. Avec la regle de la plus grande augmentation, une seule iteration suffit.
</details>

---

**Q9.** Vrai ou Faux : Une iteration degeneree implique toujours un cyclage.

<details>
<summary>Reponse</summary>

**Faux.** Une iteration degeneree signifie simplement que z ne change pas a cette iteration. Le cyclage necessiterait que le simplexe revienne a une base deja visitee, ce qui est rare et evitable avec les regles anti-cyclage (Bland, lexicographique, perturbation).
</details>

---

**Q10.** Pourquoi la methode du simplexe est-elle encore utilisee malgre sa complexite exponentielle au pire cas ?

<details>
<summary>Reponse</summary>

Parce qu'en **pratique**, le simplexe est tres rapide sur les instances reelles (complexite moyenne polynomiale). Il fournit aussi des informations supplementaires precieuses (solution duale, analyse de sensibilite). Les cas exponentiels sont des constructions artificielles qui ne se rencontrent pas dans les applications reelles.
</details>

---

*Chapitre precedent : [Chapitre 2 -- La Methode du Simplexe](Chapitre2_Methode_Simplexe.md)*  
*Chapitre suivant : [Chapitre 4 -- La Theorie de la Dualite](Chapitre4_Dualite.md)*
