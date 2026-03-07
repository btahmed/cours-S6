# Chapitre 2 -- La Methode du Simplexe

**Cours : Modeles Lineaires de la Recherche Operationnelle**  
**Universite Clermont Auvergne -- Clermont Auvergne INP -- ISIMA**  
**Professeur : Herve KERIVIN**  
**Annee : 2024-2025**

---

## Table des matieres

1. [Resume](#1-resume)
2. [Explication detaillee](#2-explication-detaillee)
   - [Variables d'ecart et dictionnaires](#21-variables-decart-et-dictionnaires)
   - [L'iteration du simplexe](#22-literation-du-simplexe)
   - [Critere d'optimalite](#23-critere-doptimalite)
   - [Detection de cas particuliers](#24-detection-de-cas-particuliers)
   - [Le tableau du simplexe](#25-le-tableau-du-simplexe)
3. [Exercices corriges](#3-exercices-corriges)
4. [Quiz](#4-quiz)

---

## 1. Resume

### Idee generale

La methode du simplexe est un algorithme iteratif pour resoudre un PL en forme standard. Elle se deplace de **sommet en sommet** le long des aretes du polyedre, en ameliorant la valeur de z a chaque etape.

### Concepts cles

| Concept | Definition |
|---------|-----------|
| **Variable d'ecart** | Variable ajoutee pour transformer une inegalite <= en egalite |
| **Dictionnaire** | Systeme d'equations ou les variables de base sont exprimees en fonction des variables hors-base |
| **Variable de base** | Variable exprimee dans le dictionnaire (a gauche des egalites) |
| **Variable hors-base** | Variable fixee a 0 dans la solution courante (a droite des egalites) |
| **Solution de base realisable** | Solution obtenue en mettant les variables hors-base a 0, avec toutes les variables de base >= 0 |
| **Pivot** | Operation qui echange une variable entrante (hors-base → base) et une variable sortante (base → hors-base) |

### L'algorithme en bref

1. **Initialiser** : partir du dictionnaire initial (variables d'ecart en base)
2. **Test d'optimalite** : si tous les coefficients de z sont <= 0, STOP (optimal)
3. **Choix de la variable entrante** : coefficient positif le plus grand dans z
4. **Test de non-borne** : si aucun coefficient positif dans la colonne, STOP (non borne)
5. **Choix de la variable sortante** : test du ratio minimum (min bi/aij, aij > 0)
6. **Pivot** : mettre a jour le dictionnaire
7. **Retourner a l'etape 2**

### Criteres importants

- **Optimalite** : tous les coefficients des variables hors-base dans z sont **<= 0**
- **Non-borne** : la variable entrante n'a **aucun coefficient positif** dans les equations des variables de base
- **Solutions multiples** : un coefficient vaut exactement **0** dans z a l'optimal

---

## 2. Explication detaillee

### 2.1 Variables d'ecart et dictionnaires

#### Variables d'ecart

Pour un PL en forme standard :
```
max z = c1*x1 + c2*x2
s.c.  a11*x1 + a12*x2 <= b1
      a21*x1 + a22*x2 <= b2
      x1, x2 >= 0
```

On ajoute une **variable d'ecart** par contrainte pour transformer les inegalites en egalites :

```
a11*x1 + a12*x2 + x3 = b1     (x3 >= 0 : ecart de la contrainte 1)
a21*x1 + a22*x2 + x4 = b2     (x4 >= 0 : ecart de la contrainte 2)
```

**Interpretation :** La variable d'ecart xi mesure la "marge" entre le membre gauche et le membre droit de la contrainte i. Si xi = 0, la contrainte est **saturee** (active). Si xi > 0, il reste de la marge.

#### Dictionnaire initial

On reecrit le systeme en isolant les variables d'ecart (variables de base) :

```
x3 = b1 - a11*x1 - a12*x2
x4 = b2 - a21*x1 - a22*x2
z  = c1*x1 + c2*x2
```

**Variables de base** : x3, x4 (a gauche, exprimees en fonction des autres)  
**Variables hors-base** : x1, x2 (a droite, fixees a 0 dans la solution courante)

**Solution de base realisable initiale** : x1 = 0, x2 = 0, x3 = b1, x4 = b2, z = 0.

> **Condition :** Ce dictionnaire est realisable si et seulement si **b1 >= 0 et b2 >= 0** (tous les membres droits sont non-negatifs). Sinon, il faut utiliser la methode des deux phases (Chapitre 3).

#### Dictionnaire general

Apres plusieurs iterations, le dictionnaire a la meme forme mais avec des coefficients differents :

```
xi = bi_barre - somme(aij_barre * xj)     pour chaque variable de base xi
z  = z_barre + somme(cj_barre * xj)       pour les variables hors-base xj
```

Les `bi_barre` sont les valeurs courantes des variables de base, et les `cj_barre` sont les **coefficients reduits** des variables hors-base dans z.

---

### 2.2 L'iteration du simplexe

Chaque iteration du simplexe effectue un **pivot** qui echange une variable hors-base (entrante) avec une variable de base (sortante).

#### Etape 1 : Choix de la variable entrante

On cherche une variable hors-base xj dont le coefficient dans z est **strictement positif** (cj_barre > 0). Augmenter cette variable fera augmenter z.

**Regle classique :** Choisir la variable avec le **plus grand coefficient positif** dans z (regle du plus grand accroissement marginal).

> **Si aucun coefficient n'est positif** (tous <= 0), la solution courante est **optimale**. STOP.

#### Etape 2 : Choix de la variable sortante (test du ratio minimum)

Supposons que xe est la variable entrante.

**Attention a la convention de signe dans le dictionnaire !**

Le dictionnaire est ecrit sous la forme :
```
xi = bi_barre - aie_barre * xe - ...
```

Le coefficient **reel** de xe dans la contrainte est **aie_barre** (le nombre qui apparait **apres le signe moins**). C'est ce coefficient qu'on utilise pour le ratio test.

> **Piege courant :** Dans le dictionnaire, on voit `-aie_barre * xe` (avec un moins devant). Le coefficient reel est **aie_barre** lui-meme (positif). C'est normal : le "-" fait partie de la structure du dictionnaire `xi = bi - ...`, pas du coefficient.

Pour chaque variable de base xi dont le coefficient reel **aie_barre > 0** (c'est-a-dire xe apparait avec un **signe -** dans le dictionnaire) :

```
ratio_i = bi_barre / aie_barre
```

La variable sortante est celle qui donne le **plus petit ratio** :

```
t* = min { bi_barre / aie_barre  |  aie_barre > 0 }
```

**Pourquoi le ratio minimum ?** Quand xe augmente, les variables de base **diminuent** (car xi = bi - aie*xe). Le ratio bi/aie donne la valeur de xe pour laquelle xi atteint 0. Le minimum garantit qu'aucune variable de base ne devient negative.

**Si xe apparait avec un signe "+" dans le dictionnaire** (coefficient reel negatif), cette variable de base **augmente** quand xe augmente → pas de limitation → on ignore cette ligne.

> **Si aucun coefficient reel n'est positif** (xe apparait partout avec un "+" ou n'apparait pas), le probleme est **non borne**. STOP.

#### Etape 3 : Le pivot

On effectue les operations suivantes :

1. Dans l'equation de la variable sortante xs, on isole la variable entrante xe
2. On substitue cette expression dans toutes les autres equations (y compris z)
3. Resultat : xe est maintenant en base, xs est hors-base

**Exemple concret :**

Dictionnaire avant pivot :
```
x3 = 6 - x1 - x2
x4 = 8 - 2x1 - x2
z  = 3x1 + 2x2
```

Variable entrante : x1 (coefficient +3 dans z, le plus grand).

Ratios : 6/1 = 6, 8/2 = 4. Minimum = 4, donc x4 sort.

On isole x1 dans l'equation de x4 :
```
x4 = 8 - 2x1 - x2
2x1 = 8 - x4 - x2
x1 = 4 - x4/2 - x2/2
```

On substitue dans les autres equations :
```
x3 = 6 - (4 - x4/2 - x2/2) - x2 = 2 + x4/2 - x2/2
x1 = 4 - x4/2 - x2/2
z  = 3(4 - x4/2 - x2/2) + 2x2 = 12 - 3x4/2 + x2/2
```

Nouveau dictionnaire :
```
x3 = 2 + x4/2 - x2/2
x1 = 4 - x4/2 - x2/2
z  = 12 - 3x4/2 + x2/2
```

Nouvelle solution : x2 = 0, x4 = 0, x1 = 4, x3 = 2, z = 12.

On continue car x2 a un coefficient positif (+1/2) dans z...

---

### 2.3 Critere d'optimalite

**Theoreme :** Un dictionnaire realisable est **optimal** si et seulement si tous les coefficients des variables hors-base dans l'equation de z sont **<= 0**.

```
z = z_barre + c1_barre * x_j1 + c2_barre * x_j2 + ...
```

Si tous les ci_barre <= 0, alors augmenter n'importe quelle variable hors-base ne peut que **diminuer** (ou laisser inchangee) la valeur de z. Donc la solution courante (variables hors-base = 0) est la meilleure.

**Valeur optimale :** z* = z_barre (le terme constant dans l'equation de z).

---

### 2.4 Detection de cas particuliers

#### Non-borne

Lors du choix de la variable sortante, si **tous les coefficients** de la variable entrante dans les equations de base sont **<= 0**, alors cette variable peut croitre indefiniment sans violer aucune contrainte.

```
xe entre, et dans chaque equation :
xi = bi_barre - ... - aie_barre * xe - ...
```

Si tous les aie_barre <= 0, augmenter xe ne fait que rendre les xi plus grands (ou les laisser inchanges). On peut donc augmenter xe a l'infini et z croit sans limite.

**Conclusion :** Le probleme est **non borne**.

#### Solutions multiples

A l'optimal, si un coefficient cj_barre = 0 pour une variable hors-base xj, alors :
- La solution courante est optimale
- Mais on peut faire entrer xj en base sans changer z
- On obtient un autre sommet optimal
- Toute combinaison convexe de ces deux sommets est aussi optimale

**Consequence :** Il existe une **infinite de solutions optimales** (toute l'arete entre les deux sommets).

---

#### Exemple complet : Probleme non borne

```
maximiser   z = 2x1 + x2
sujet a     -x1 + x2 <= 2
             x1 - 2x2 <= 4
             x1, x2 >= 0
```

**Dictionnaire initial** (ajout de x3, x4 variables d'ecart) :
```
x3 = 2 + x1 - x2
x4 = 4 - x1 + 2x2
z  = 2x1 + x2
```

Solution initiale : x1 = 0, x2 = 0, x3 = 2, x4 = 4, z = 0.

**Iteration 1 :**
- Variable entrante : x1 (coefficient +2 dans z, le plus grand).
- Ratios : on regarde comment x1 apparait dans chaque equation :
  - x3 = 2 **+ x1** - x2 → coefficient de x1 est **+1** (signe +). Quand x1 augmente, x3 **augmente**. Pas de limitation.
  - x4 = 4 **- x1** + 2x2 → coefficient de x1 est **-1** (signe -). Quand x1 augmente, x4 **diminue**. Ratio = 4/1 = 4. Variable sortante : x4.

Pivot : isoler x1 dans l'equation de x4 :
```
x4 = 4 - x1 + 2x2
x1 = 4 - x4 + 2x2
```

Substituer dans les autres equations :
```
x3 = 2 + (4 - x4 + 2x2) - x2 = 6 - x4 + x2
x1 = 4 - x4 + 2x2
z  = 2(4 - x4 + 2x2) + x2 = 8 - 2x4 + 5x2
```

Nouveau dictionnaire :
```
x3 = 6 - x4 + x2
x1 = 4 - x4 + 2x2
z  = 8 - 2x4 + 5x2
```

Solution : x4 = 0, x2 = 0, x3 = 6, x1 = 4, z = 8.

**Iteration 2 :**
- Variable entrante : x2 (coefficient +5 dans z).
- Ratios : on regarde comment x2 apparait dans chaque equation :
  - x3 = 6 - x4 **+ x2** → coefficient de x2 est **+1** (signe +). Quand x2 augmente, x3 **augmente**. Pas de limitation.
  - x1 = 4 - x4 **+ 2x2** → coefficient de x2 est **+2** (signe +). Quand x2 augmente, x1 **augmente**. Pas de limitation.

**Aucune variable de base n'est limitee** quand x2 augmente. On peut donc augmenter x2 a l'infini.

Comme le coefficient de x2 dans z est +5, z = 8 + 5x2 croit sans limite.

**Conclusion : Le probleme est NON BORNE.** Il n'existe pas de solution optimale finie.

> **Comment verifier geometriquement ?** Les contraintes definissent une region non bornee. La direction d'augmentation de x2 (avec x1 = 4 + 2x2, x3 = 6 + x2, x4 = 0) est un rayon qui reste dans la region realisable et le long duquel z → +∞.

---

#### Exemple complet : Solutions multiples

```
maximiser   z = 2x1 + 4x2
sujet a     x1 + 2x2 <= 8
             x1 + x2 <= 5
             x1, x2 >= 0
```

**Dictionnaire initial** (ajout de x3, x4 variables d'ecart) :
```
x3 = 8 - x1 - 2x2
x4 = 5 - x1 - x2
z  = 2x1 + 4x2
```

Solution initiale : x1 = 0, x2 = 0, x3 = 8, x4 = 5, z = 0.

**Iteration 1 :**
- Variable entrante : x2 (coefficient +4, le plus grand).
- Ratios : 8/2 = 4, 5/1 = 5 → minimum = 4, x3 sort.
- Pivot : isoler x2 dans l'equation de x3 :

```
x3 = 8 - x1 - 2x2
2x2 = 8 - x1 - x3
x2 = 4 - x1/2 - x3/2
```

Substituer :
```
x4 = 5 - x1 - (4 - x1/2 - x3/2) = 1 - x1/2 + x3/2
z  = 2x1 + 4(4 - x1/2 - x3/2) = 16 - 2x3
```

Nouveau dictionnaire :
```
x2 = 4 - x1/2 - x3/2
x4 = 1 - x1/2 + x3/2
z  = 16 + 0*x1 - 2x3
```

**Test d'optimalite :** Les coefficients dans z sont :
- x1 : **0** (exactement zero !)
- x3 : **-2** (negatif)

Tous les coefficients sont <= 0, donc la solution est **optimale**.

**Solution optimale 1 :** x1 = 0, x2 = 4, z = 16.

**Mais** le coefficient de x1 dans z vaut exactement **0**. Cela signifie que faire entrer x1 en base ne changera pas la valeur de z. On peut trouver un deuxieme sommet optimal :

**Iteration supplementaire (optionnelle) :**
- Variable entrante : x1 (coefficient 0 dans z — z ne changera pas).
- Ratios : 4/(1/2) = 8, 1/(1/2) = 2 → minimum = 2, x4 sort.
- Pivot : isoler x1 dans l'equation de x4 :

```
x4 = 1 - x1/2 + x3/2
x1/2 = 1 - x4 + x3/2
x1 = 2 - 2x4 + x3
```

Substituer :
```
x2 = 4 - (2 - 2x4 + x3)/2 - x3/2 = 3 + x4 - x3
z  = 16 + 0*(2 - 2x4 + x3) - 2x3 = 16 - 2x3
```

Nouveau dictionnaire :
```
x1 = 2 - 2x4 + x3
x2 = 3 + x4 - x3
z  = 16 + 0*x4 - 2x3
```

**Solution optimale 2 :** x1 = 2, x2 = 3, z = 16.

La valeur optimale est **toujours z = 16**, confirmant les solutions multiples.

**Toutes les solutions optimales** sont de la forme :

```
(x1, x2) = λ(0, 4) + (1-λ)(2, 3)  pour tout λ ∈ [0, 1]
         = (2 - 2λ,  3 + λ)
```

Par exemple :
- λ = 0 → (2, 3), z = 16
- λ = 1/2 → (1, 3.5), z = 16
- λ = 1 → (0, 4), z = 16
  
> **Geometriquement :** La droite z = 2x1 + 4x2 = 16 est **parallele** a la contrainte x1 + 2x2 <= 8 (les coefficients de z sont proportionnels a ceux de la contrainte : 2/1 = 4/2). L'ensemble des solutions optimales est l'arete entiere du polyedre entre les sommets (0,4) et (2,3).

---

### 2.5 Le tableau du simplexe

Le tableau du simplexe est une representation compacte du dictionnaire, utile pour les calculs a la main.

#### Structure du tableau

```
         | x1   x2   x3   x4  | b
    -----|-----|-----|-----|-----|-----
    x3   | a11  a12   1    0   | b1
    x4   | a21  a22   0    1   | b2
    -----|-----|-----|-----|-----|-----
    z    | c1   c2    0    0   | 0
```

- **Lignes** : une par variable de base + une pour z
- **Colonnes** : une par variable + une pour les membres droits (b)
- **La colonne de la variable entrante** est la colonne pivot
- **La ligne de la variable sortante** est la ligne pivot
- **L'element pivot** est a l'intersection

#### Operations de pivot sur le tableau

1. **Diviser la ligne pivot** par l'element pivot (pour obtenir un 1 a la position pivot)
2. **Eliminer** : pour chaque autre ligne, soustraire un multiple de la ligne pivot pour obtenir un 0 dans la colonne pivot

C'est l'elimination de Gauss-Jordan sur la colonne pivot.

---

## 3. Exercices corriges

### Exercice 1 : Simplexe complet

**Enonce :**
```
maximiser   z = 5x1 + 4x2 + 3x3
sujet a     6x1 + 4x2 + 2x3 <= 240
            3x1 + 2x2 + 5x3 <= 270
            x1, x2, x3 >= 0
```

#### Solution

**Dictionnaire initial** (ajout de x4, x5 variables d'ecart) :
```
x4 = 240 - 6x1 - 4x2 - 2x3
x5 = 270 - 3x1 - 2x2 - 5x3
z  = 5x1 + 4x2 + 3x3
```

Solution initiale : x1 = x2 = x3 = 0, x4 = 240, x5 = 270, z = 0.

**Iteration 1 :**
- Variable entrante : x1 (coefficient +5, le plus grand)
- Ratios : 240/6 = 40, 270/3 = 90 → minimum = 40, x4 sort
- Pivot : isoler x1 dans l'equation de x4

```
x1 = 40 - 2x2/3 - x3/3 - x4/6
x5 = 270 - 3(40 - 2x2/3 - x3/3 - x4/6) - 2x2 - 5x3
   = 150 - 4x3 + x4/2
z  = 5(40 - 2x2/3 - x3/3 - x4/6) + 4x2 + 3x3
   = 200 + 2x2/3 + 4x3/3 - 5x4/6
```

Nouveau dictionnaire :
```
x1 = 40 - 2x2/3 - x3/3 - x4/6
x5 = 150 - 4x3 + x4/2
z  = 200 + 2x2/3 + 4x3/3 - 5x4/6
```

Solution : x1 = 40, x5 = 150, z = 200.

**Iteration 2 :**
- Variable entrante : x3 (coefficient +4/3, plus grand que +2/3)
- Ratios : 40/(1/3) = 120, 150/4 = 37.5 → minimum = 37.5, x5 sort
- Pivot : isoler x3 dans l'equation de x5

```
x3 = 37.5 - x5/4 + x4/8
x1 = 40 - 2x2/3 - (37.5 - x5/4 + x4/8)/3 - x4/6
   = 27.5 - 2x2/3 + x5/12 - 5x4/24
z  = 200 + 2x2/3 + 4(37.5 - x5/4 + x4/8)/3 - 5x4/6
   = 250 + 2x2/3 - x5/3 - 2x4/3
```

Nouveau dictionnaire :
```
x1 = 27.5 - 2x2/3 + x5/12 - 5x4/24
x3 = 37.5 - x5/4 + x4/8
z  = 250 + 2x2/3 - x5/3 - 2x4/3
```

**Iteration 3 :**
- Variable entrante : x2 (coefficient +2/3)
- Ratios : 27.5/(2/3) = 41.25. x3 n'a pas de coefficient positif pour x2 (coeff = 0)
- x4 sort : x1 = 27.5 - 2x2/3... → t* = 41.25

On continue les pivots jusqu'a ce que tous les coefficients dans z soient <= 0.

(La solution optimale finale est x1 = 0, x2 = 41.25, x3 = 37.5, z = 250 + 2(41.25)/3 = 277.5 -- les details du dernier pivot sont laisses en exercice.)

---

### Exercice 2 : Simplexe avec 2 variables

**Enonce :**
```
maximiser   z = 3x1 + 2x2
sujet a     x1 + x2 <= 4
            x1 + 3x2 <= 6
            x1, x2 >= 0
```

#### Solution

**Dictionnaire initial :**
```
x3 = 4 - x1 - x2
x4 = 6 - x1 - 3x2
z  = 3x1 + 2x2
```

**Iteration 1 :**
- Entrante : x1 (coeff +3)
- Ratios : 4/1 = 4, 6/1 = 6 → min = 4, x3 sort
- Pivot sur x1 dans equation de x3 :

```
x1 = 4 - x3 - x2
x4 = 6 - (4 - x3 - x2) - 3x2 = 2 + x3 - 2x2
z  = 3(4 - x3 - x2) + 2x2 = 12 - 3x3 - x2
```

Dictionnaire :
```
x1 = 4 - x3 - x2
x4 = 2 + x3 - 2x2
z  = 12 - 3x3 - x2
```

**Test d'optimalite :** Les coefficients dans z sont -3 (x3) et -1 (x2), tous <= 0.

**Solution optimale : x1* = 4, x2* = 0, z* = 12**

---

### Exercice 3 : Detection de non-borne

**Enonce :**
```
maximiser   z = x1 - x2 + x3
sujet a     -2x1 + x2 + x3 <= 4
            x2 - x3 <= 2
            x1, x2, x3 >= 0
```

#### Solution

**Dictionnaire initial :**
```
x4 = 4 + 2x1 - x2 - x3
x5 = 2 - x2 + x3
z  = x1 - x2 + x3
```
 
Variable entrante : x1 (coefficient +1 dans z).

Regardons les coefficients de x1 dans les equations de base :
- x4 : coefficient de x1 est **-(-2) = +2** ? Non, dans le dictionnaire c'est `-(-2x1) = +2x1`, donc le coefficient est **-2** (avec le signe - devant). En fait : x4 = 4 + 2x1 - x2 - x3, donc le coefficient de x1 est **+2** (positif... non attendons).

Reformulons. Le dictionnaire est :
```
x4 = 4 - (-2x1) - x2 - x3 = 4 + 2x1 - x2 - x3
```

Hmm, le coefficient de x1 dans la contrainte originale est -2. Dans le dictionnaire standard xi = bi - somme(aij*xj), on a :
```
x4 = 4 - (-2)*x1 - 1*x2 - 1*x3 = 4 + 2x1 - x2 - x3
```

Le coefficient est +2 (signe inverse car c'est bi - aij*xj). Pour le ratio test, on a besoin du coefficient **tel qu'il apparait dans le dictionnaire avec le signe -**. On regarde : x4 = 4 + 2x1... donc quand x1 augmente, x4 **augmente** aussi. Pas de limitation de ce cote.

x5 = 2 - 0*x1 - x2 + x3. Coefficient de x1 = 0. Pas de limitation non plus.

**Aucun coefficient de x1 n'est "limitant"** (x1 peut croitre sans rendre aucune variable de base negative). Le probleme est **non borne**.

---

## 4. Quiz

### Questions de comprehension

**Q1.** A quoi sert une variable d'ecart ?

<details>
<summary>Reponse</summary>

Une variable d'ecart transforme une inegalite <= en egalite. Elle mesure la "marge" restante dans une contrainte. Si elle vaut 0, la contrainte est saturee (active).
</details>

---

**Q2.** Comment sait-on qu'on a atteint la solution optimale dans le simplexe ?

<details>
<summary>Reponse</summary>

Quand tous les coefficients des variables hors-base dans l'equation de z sont **<= 0**. Augmenter n'importe quelle variable hors-base ne ferait que diminuer (ou laisser inchangee) la valeur de z.
</details>

---

**Q3.** Quelle est la regle pour choisir la variable entrante ?

<details>
<summary>Reponse</summary>

On choisit une variable hors-base dont le coefficient dans z est **strictement positif**. La regle classique est de prendre celle avec le **plus grand coefficient positif** (plus grande amelioration marginale).
</details>

---

**Q4.** Quelle est la regle pour choisir la variable sortante ?

<details>
<summary>Reponse</summary>

On effectue le **test du ratio minimum** : pour chaque variable de base xi ayant un coefficient positif aie pour la variable entrante, on calcule le ratio bi/aie. La variable sortante est celle qui donne le plus petit ratio : `t* = min{bi/aie | aie > 0}`.
</details>

---

**Q5.** Pourquoi fait-on le test du ratio minimum ?

<details>
<summary>Reponse</summary>

Pour determiner la valeur maximale que la variable entrante peut prendre tout en gardant **toutes les variables de base >= 0** (donc en restant dans la region realisable). Le ratio minimum correspond a la premiere variable de base qui atteint 0 quand la variable entrante augmente.
</details>

---

**Q6.** Comment detecte-t-on qu'un probleme est non borne ?

<details>
<summary>Reponse</summary>

Lors du choix de la variable sortante, si **tous les coefficients** de la variable entrante dans les equations de base sont **<= 0**, alors la variable entrante peut croitre indefiniment sans violer aucune contrainte. Le probleme est non borne.
</details>

---

**Q7.** Comment detecte-t-on des solutions optimales multiples ?

<details>
<summary>Reponse</summary>

A l'optimal (tous les coefficients dans z sont <= 0), si un coefficient d'une variable hors-base vaut exactement **0**, alors on peut faire entrer cette variable sans changer la valeur de z. On obtient un autre sommet optimal, et tous les points de l'arete entre les deux sont aussi optimaux.
</details>

---

**Q8.** Quelle est la difference entre "variable de base" et "variable hors-base" ?

<details>
<summary>Reponse</summary>

- **Variable de base** : exprimee a gauche dans le dictionnaire, sa valeur est le terme constant de son equation (generalement > 0)
- **Variable hors-base** : fixee a 0 dans la solution courante, elle apparait a droite dans les equations

Chaque iteration echange une variable hors-base (entrante) avec une variable de base (sortante).
</details>

---

**Q9.** Dans le dictionnaire suivant, quelle est la variable entrante et la variable sortante ?
```
x3 = 10 - 2x1 - x2
x4 = 6 - x1 - 3x2
z  = 4x1 + 5x2
```

<details>
<summary>Reponse</summary>

- Variable entrante : **x2** (coefficient +5 dans z, le plus grand)
- Ratios : 10/1 = 10, 6/3 = 2 → minimum = 2
- Variable sortante : **x4**
</details>

---

**Q10.** Vrai ou Faux : Le simplexe visite tous les sommets du polyedre avant de trouver l'optimum.

<details>
<summary>Reponse</summary>

**Faux.** Le simplexe ne visite qu'un sous-ensemble des sommets, en se deplacant toujours vers un sommet adjacent ayant une meilleure valeur de z. En pratique, il est tres efficace et ne visite qu'un petit nombre de sommets (en general environ 2m a 3m iterations pour m contraintes).
</details>

---

### Exercice d'entrainement

**E1.** Resolvez par le simplexe :
```
maximiser   z = 2x1 + 3x2
sujet a     x1 + 2x2 <= 8
            3x1 + 2x2 <= 12
            x1, x2 >= 0
```

<details>
<summary>Reponse</summary>

Dictionnaire initial :
```
x3 = 8 - x1 - 2x2
x4 = 12 - 3x1 - 2x2
z  = 2x1 + 3x2
```

Iteration 1 : x2 entre (coeff +3). Ratios : 8/2 = 4, 12/2 = 6. x3 sort (t* = 4).
```
x2 = 4 - x1/2 - x3/2
x4 = 12 - 3x1 - 2(4 - x1/2 - x3/2) = 4 - 2x1 + x3
z  = 2x1 + 3(4 - x1/2 - x3/2) = 12 + x1/2 - 3x3/2
```

Iteration 2 : x1 entre (coeff +1/2). Ratios : 4/(1/2) = 8, 4/2 = 2. x4 sort (t* = 2).
```
x1 = 2 - x4/2 + x3/2
x2 = 4 - (2 - x4/2 + x3/2)/2 - x3/2 = 3 + x4/4 - 3x3/4
z  = 12 + (2 - x4/2 + x3/2)/2 - 3x3/2 = 13 - x4/4 - 5x3/4
```

Tous les coefficients dans z sont <= 0 : **optimal**.

**x1* = 2, x2* = 3, z* = 13**
</details>

---

*Chapitre precedent : [Chapitre 1 -- Introduction a la PL](Chapitre1_Introduction_PL.md)*  
*Chapitre suivant : [Chapitre 3 -- Degenerescence, Cyclage et Methode des Deux Phases](Chapitre3_Degenerescence_Deux_Phases.md)*
