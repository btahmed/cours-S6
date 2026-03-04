# Chapitre 4 -- La Theorie de la Dualite

**Cours : Modeles Lineaires de la Recherche Operationnelle**  
**Universite Clermont Auvergne -- Clermont Auvergne INP -- ISIMA**  
**Professeur : Herve KERIVIN**  
**Annee : 2024-2025**

---

## Table des matieres

1. [Resume](#1-resume)
2. [Explication detaillee](#2-explication-detaillee)
   - [Le probleme dual](#21-le-probleme-dual)
   - [Correspondances primal-dual](#22-correspondances-primal-dual)
   - [Theoremes de dualite](#23-theoremes-de-dualite)
   - [Ecarts complementaires](#24-ecarts-complementaires)
   - [Lecture du dual dans le dictionnaire](#25-lecture-du-dual-dans-le-dictionnaire)
3. [Exercices corriges](#3-exercices-corriges)
4. [Quiz](#4-quiz)

---

## 1. Resume

### Idee de la dualite

A chaque programme lineaire (le **primal**) est associe un autre programme lineaire (le **dual**). Les deux problemes sont intimement lies : la solution de l'un fournit des informations sur l'autre.

### Tableau de correspondances (forme standard)

| Primal (max) | Dual (min) |
|---|---|
| Maximiser c^T x | Minimiser b^T y |
| m contraintes <= | m variables y >= 0 |
| n variables x >= 0 | n contraintes >= |
| Coefficients objectif c | Membres droits du dual |
| Membres droits b | Coefficients objectif du dual |
| Matrice A | Matrice A^T |

### Theoremes fondamentaux

| Theoreme | Enonce |
|----------|--------|
| **Dualite faible** | Pour toute solution realisable primale x et duale y : c^T x <= b^T y |
| **Dualite forte** | Si l'un a une solution optimale, l'autre aussi, et z* = w* |
| **Ecarts complementaires** | x* et y* sont optimaux ssi : xi*(contrainte duale i saturee ou non) et yj*(contrainte primale j saturee ou non) verifient les conditions EC |

### Consequences pratiques

- La solution duale se lit directement dans le dictionnaire optimal du simplexe
- Les ecarts complementaires permettent de verifier l'optimalite sans re-executer le simplexe
- Le dual du dual est le primal

---

## 2. Explication detaillee

### 2.1 Le probleme dual

#### Construction du dual (cas standard)

Soit le probleme primal en forme standard :
```
(P)  maximiser   z = c^T x = c1*x1 + c2*x2 + ... + cn*xn
     sujet a     A*x <= b
                 x >= 0
```

Le **dual** associe est :
```
(D)  minimiser   w = b^T y = b1*y1 + b2*y2 + ... + bm*ym
     sujet a     A^T * y >= c
                 y >= 0
```

**En clair :**
- Le primal a n variables et m contraintes
- Le dual a m variables et n contraintes
- La matrice des contraintes du dual est la **transposee** de celle du primal
- Les coefficients objectif du primal deviennent les membres droits du dual (et vice versa)
- Le sens d'optimisation s'inverse (max ↔ min)
- Le sens des inegalites s'inverse (<= ↔ >=)

#### Exemple

**Primal :**
```
max   z = 2x1 + 3x2
s.c.  x1 + x2 <= 10
      2x1 - x2 <= 8
      x1, x2 >= 0
```

**Dual :**
```
min   w = 10y1 + 8y2
s.c.  y1 + 2y2 >= 2    (contrainte pour x1)
      y1 - y2  >= 3    (contrainte pour x2)
      y1, y2 >= 0
```

**Verification de la construction :**
- A = [[1, 1], [2, -1]], A^T = [[1, 2], [1, -1]]
- c = (2, 3) deviennent les membres droits du dual
- b = (10, 8) deviennent les coefficients objectif du dual

---

### 2.2 Correspondances primal-dual

#### Tableau general des correspondances

Le tableau complet pour tous les cas (pas seulement la forme standard) :

**Si le primal est un probleme de maximisation :**

| Element primal | Element dual |
|---|---|
| Maximiser | Minimiser |
| Contrainte i en **<=** | Variable yi **>= 0** |
| Contrainte i en **>=** | Variable yi **<= 0** |
| Contrainte i en **=** | Variable yi **libre** |
| Variable xj **>= 0** | Contrainte j en **>=** |
| Variable xj **<= 0** | Contrainte j en **<=** |
| Variable xj **libre** | Contrainte j en **=** |

**Moyen mnemotechnique :**
- Contrainte "contrainte" (<=, >=, =) du primal → variable avec signe correspondant dans le dual
- Variable avec signe du primal → contrainte correspondante dans le dual
- Le sens "naturel" pour max est <= et >= 0, qui donnent >= 0 et >= dans le dual

#### Le dual du dual est le primal

> **Theoreme :** Si on ecrit le dual du probleme dual, on retrouve exactement le probleme primal original.

Cela confirme la symetrie de la relation de dualite.

---

### 2.3 Theoremes de dualite

#### Theoreme de dualite faible

> **Theoreme :** Soit x une solution realisable du primal (P) et y une solution realisable du dual (D). Alors :
> ```
> c^T x  <=  b^T y
> (valeur primale <= valeur duale)
> ```

**Consequences immediates :**

1. **Toute solution duale realisable fournit une borne superieure** sur la valeur optimale du primal
2. **Toute solution primale realisable fournit une borne inferieure** sur la valeur optimale du dual
3. Si on trouve x et y realisables avec c^T x = b^T y, alors **les deux sont optimaux**
4. Si le primal est non borne (z → +infini), le dual est **infaisable**
5. Si le dual est non borne (w → -infini), le primal est **infaisable**

**Preuve (intuitive) :**
- x realisable : Ax <= b, x >= 0
- y realisable : A^T y >= c, y >= 0
- c^T x <= (A^T y)^T x = y^T A x <= y^T b = b^T y

#### Theoreme de dualite forte

> **Theoreme :** Si le probleme primal (P) a une solution optimale x*, alors le probleme dual (D) a aussi une solution optimale y*, et :
> ```
> z* = c^T x* = b^T y* = w*
> ```

**Les valeurs optimales du primal et du dual sont egales.**

C'est un resultat beaucoup plus fort que la dualite faible. Il dit qu'il n'y a pas de "gap" entre les deux.

#### Corollaires

| Situation | Consequence |
|---|---|
| Primal optimal | Dual optimal, z* = w* |
| Primal non borne | Dual infaisable |
| Primal infaisable | Dual infaisable OU non borne |
| Dual non borne | Primal infaisable |

**Tableau des combinaisons possibles :**

|  | Dual Optimal | Dual Infaisable | Dual Non Borne |
|---|:---:|:---:|:---:|
| **Primal Optimal** | Possible (z*=w*) | Impossible | Impossible |
| **Primal Infaisable** | Impossible | Possible | Possible |
| **Primal Non Borne** | Impossible | Possible | Impossible |

> **Attention :** "Primal infaisable et dual infaisable" est **possible** (les deux peuvent etre infaisables simultanement).  
> "Primal non borne et dual non borne" est **impossible**.

---

### 2.4 Ecarts complementaires

#### Conditions d'ecarts complementaires

Les conditions d'ecarts complementaires (Complementary Slackness) donnent des relations entre les solutions optimales du primal et du dual :

> **Theoreme :** x* et y* sont des solutions optimales du primal et du dual respectivement **si et seulement si** elles sont realisables et verifient :

**Condition (1) -- pour chaque variable primale xj :**
```
Si xj* > 0, alors la contrainte duale j est saturee (egalite)
```
Equivalemment : `xj* * (contrainte duale j - cj) = 0`

**Condition (2) -- pour chaque variable duale yi :**
```
Si yi* > 0, alors la contrainte primale i est saturee (egalite)
```
Equivalemment : `yi* * (bi - contrainte primale i) = 0`

#### En clair

Pour chaque paire (variable, contrainte correspondante dans l'autre probleme) :
- **Si la variable est strictement positive**, alors la contrainte correspondante dans l'autre probleme doit etre **saturee** (tenue avec egalite)
- **Si la contrainte n'est pas saturee** (il y a de la marge), alors la variable correspondante dans l'autre probleme doit etre **nulle**

On peut le resumer par : **"si l'une est lache, l'autre est nulle"**.

#### Utilite pratique

Les ecarts complementaires permettent de :

1. **Trouver la solution duale** a partir de la solution primale (et vice versa)
2. **Verifier l'optimalite** d'une solution candidate sans re-executer le simplexe
3. **Prouver qu'une solution n'est pas optimale** si les conditions EC sont violees

**Methode pour trouver y* a partir de x* :**
1. Identifier quelles contraintes primales sont saturees/non saturees
2. Condition (2) : si contrainte i non saturee → yi* = 0
3. Identifier quelles variables xj* > 0
4. Condition (1) : si xj* > 0 → contrainte duale j saturee (egalite)
5. Resoudre le systeme d'equations resultant
6. Verifier que la solution y* est realisable pour le dual

---

### 2.5 Lecture du dual dans le dictionnaire

#### Regle de lecture

Dans le dictionnaire optimal du simplexe, la solution duale se lit directement :

> **Regle :** La valeur de la variable duale yi* est l'**oppose du coefficient** de la variable d'ecart x_{n+i} dans l'equation de z du dictionnaire optimal.

```
z = z* + ... + (coeff de x_{n+i}) * x_{n+i} + ...
yi* = -(coeff de x_{n+i})
```

**Exemple :**
Si le dictionnaire optimal est :
```
z = 15 - 3x4 - 2x5
```

Avec x4 = variable d'ecart de la contrainte 1 et x5 = variable d'ecart de la contrainte 2 :
- y1* = -(-3) = 3
- y2* = -(-2) = 2

**Verification par dualite forte :** w* = b1*y1* + b2*y2* doit etre egal a z* = 15.

#### Pourquoi ca marche

Dans le dictionnaire optimal, les coefficients des variables d'ecart dans z representent la "sensibilite" de z par rapport aux membres droits bi. C'est exactement la definition des variables duales (prix fantomes, cf. Chapitre 5).

---

## 3. Exercices corriges

### Exercice 1 : Ecriture du dual

**Enonce :**
```
maximiser   z = 2x1 + 3x2 - x3
sujet a     x1 + x2 + x3 <= 10
            2x1 - x2 + 3x3 <= 15
            x1, x2, x3 >= 0
```

#### Solution

**Correspondances :**

| Primal | Dual |
|---|---|
| max | min |
| 2 contraintes <= | 2 variables y1, y2 >= 0 |
| 3 variables >= 0 | 3 contraintes >= |
| b = (10, 15) | coefficients objectif dual |
| c = (2, 3, -1) | membres droits dual |

**Dual :**
```
minimiser   w = 10y1 + 15y2
sujet a     y1 + 2y2 >= 2      (colonne de x1)
            y1 - y2  >= 3      (colonne de x2)
            y1 + 3y2 >= -1     (colonne de x3)
            y1, y2 >= 0
```

**Remarque :** La 3e contrainte (y1 + 3y2 >= -1) est automatiquement satisfaite car y1, y2 >= 0 implique y1 + 3y2 >= 0 > -1.

**Le dual du dual :** En appliquant les correspondances au dual, on retrouve exactement le primal.

---

### Exercice 2 : Verification par ecarts complementaires

**Enonce :**  
Pour le probleme :
```
maximiser   z = 5x1 + 4x2
sujet a     6x1 + 4x2 <= 24
            x1 + 2x2  <= 6
            x1, x2 >= 0
```
Verifier si x1* = 3, x2* = 3/2 est optimal.

#### Solution

**Dual :**
```
minimiser   w = 24y1 + 6y2
sujet a     6y1 + y2  >= 5
            4y1 + 2y2 >= 4
            y1, y2 >= 0
```

**Verification de la realisabilite primale :**
- 6(3) + 4(3/2) = 18 + 6 = 24 <= 24 **saturee**
- 3 + 2(3/2) = 3 + 3 = 6 <= 6 **saturee**

**Application des ecarts complementaires :**

Condition (2) : les deux contraintes primales sont saturees → y1* et y2* ne sont pas contraints a etre 0.

Condition (1) :
- x1* = 3 > 0 → contrainte duale 1 saturee : **6y1* + y2* = 5**
- x2* = 3/2 > 0 → contrainte duale 2 saturee : **4y1* + 2y2* = 4**

**Resolution du systeme :**
```
6y1 + y2  = 5    ... (1)
4y1 + 2y2 = 4    ... (2)

(2) simplifie : 2y1 + y2 = 2, donc y2 = 2 - 2y1

Dans (1) : 6y1 + (2 - 2y1) = 5
           4y1 = 3
           y1* = 3/4

y2* = 2 - 3/2 = 1/2
```

**Verification realisabilite duale :**
- y1* = 3/4 >= 0
- y2* = 1/2 >= 0
- 6(3/4) + 1/2 = 9/2 + 1/2 = 5 >= 5
- 4(3/4) + 2(1/2) = 3 + 1 = 4 >= 4

**Verification dualite forte :**
- z* = 5(3) + 4(3/2) = 15 + 6 = **21**
- w* = 24(3/4) + 6(1/2) = 18 + 3 = **21**
- z* = w* = 21

**Conclusion : x1* = 3, x2* = 3/2 est bien optimal, avec z* = 21.**

---

### Exercice 3 : Solution non optimale detectee par EC

**Enonce :**  
Meme probleme que l'exercice 1 avec la solution candidate x1* = 5, x2* = 5, x3* = 0.

#### Solution

**Realisabilite :** 5 + 5 + 0 = 10 <= 10 (saturee), 10 - 5 + 0 = 5 <= 15 (non saturee, ecart = 10).

**Ecarts complementaires :**
- Contrainte 2 non saturee → **y2* = 0**
- x1* = 5 > 0 → 6y1* + y2* = 5 → y1* + 0 = 2 → **y1* = 2**
- x2* = 5 > 0 → y1* - y2* = 3 → 2 - 0 = 2 ≠ 3 → **CONTRADICTION !**

Le systeme est inconsistant. **La solution x1* = 5, x2* = 5, x3* = 0 n'est PAS optimale.**

Verification : z = 2(5) + 3(5) - 0 = 25. En essayant x1 = 0, x2 = 10, x3 = 0 : z = 30 > 25.

---

### Exercice 4 : Analyse des combinaisons primal/dual

**Determiner si les situations suivantes sont possibles ou impossibles :**

**1. Primal optimal (z* = 10) et dual optimal (w* = 8) :**  
**IMPOSSIBLE.** Par la dualite forte, z* = w*. Or 10 ≠ 8. De plus, la dualite faible donne z* <= w*, donc 10 <= 8 est faux.

**2. Primal non borne et dual optimal :**  
**IMPOSSIBLE.** Si le dual est realisable (a fortiori optimal), la dualite faible donne une borne superieure sur z. Le primal ne peut pas etre non borne.

**3. Primal infaisable et dual infaisable :**  
**POSSIBLE.** Exemple : max z = x1, s.c. x1 <= -1, x1 >= 0.

**4. Solution primale realisable (z = 7) et solution duale realisable (w = 9) :**  
**POSSIBLE.** Par la dualite faible, 7 <= 9 est coherent. Ces solutions ne sont pas necessairement optimales.

**5. Primal non borne et dual non borne :**  
**IMPOSSIBLE.** Si le primal est non borne, le dual est infaisable (pas non borne).

---

### Exercice 5 : Lecture dans le dictionnaire optimal

**Enonce :**  
Dictionnaire optimal :
```
x1 = 4 - 2x3 + x5
x2 = 1 + x3 - x4
z  = 15 - 3x4 - 2x5
```
(Variables d'ecart : x3 pour contrainte 1, x4 pour contrainte 2, x5 pour contrainte 3)

#### Solution

**Solution primale :** x1* = 4, x2* = 1, z* = 15.

**Solution duale (coefficients opposes des variables d'ecart dans z) :**

Attention : x3 n'apparait pas dans z, x4 a coefficient -3, x5 a coefficient -2.

- y1* = -(coefficient de x3 dans z) = -(0) = **0**
- y2* = -(coefficient de x4 dans z) = -(-3) = **3**
- y3* = -(coefficient de x5 dans z) = -(-2) = **2**

**Verification des ecarts complementaires :**
- y1* = 0 : coherent si la contrainte 1 n'est pas saturee (x3 en base, x3 pourrait etre > 0)
- y2* = 3 > 0 : la contrainte 2 doit etre saturee (x4 = 0, hors-base)
- y3* = 2 > 0 : la contrainte 3 doit etre saturee (x5 = 0, hors-base)
- x1* = 4 > 0 : la contrainte duale 1 doit etre saturee
- x2* = 1 > 0 : la contrainte duale 2 doit etre saturee

**Verification dualite forte :** w* = b1(0) + b2(3) + b3(2) = 3b2 + 2b3 devrait egaler 15.

---

## 4. Quiz

### Questions de comprehension

**Q1.** Donnez le dual du probleme suivant :
```
max z = 3x1 + 2x2, s.c. x1 + x2 <= 5, 2x1 + x2 <= 8, x1, x2 >= 0
```

<details>
<summary>Reponse</summary>

```
min w = 5y1 + 8y2
s.c. y1 + 2y2 >= 3    (colonne x1)
     y1 + y2  >= 2    (colonne x2)
     y1, y2 >= 0
```
</details>

---

**Q2.** Enoncez le theoreme de dualite faible.

<details>
<summary>Reponse</summary>

Pour toute solution realisable x du primal et toute solution realisable y du dual : **c^T x <= b^T y**. Autrement dit, la valeur de toute solution realisable du primal est inferieure ou egale a la valeur de toute solution realisable du dual.
</details>

---

**Q3.** Enoncez le theoreme de dualite forte.

<details>
<summary>Reponse</summary>

Si le primal a une solution optimale x*, alors le dual a aussi une solution optimale y*, et les valeurs optimales sont egales : **z* = c^T x* = b^T y* = w***.
</details>

---

**Q4.** Si on a une solution realisable du primal avec z = 20 et une solution realisable du dual avec w = 20, que peut-on conclure ?

<details>
<summary>Reponse</summary>

Les deux solutions sont **optimales**. Par la dualite faible, z <= w pour toutes solutions realisables. Si z = w = 20, alors aucune autre solution ne peut faire mieux, donc z* = w* = 20.
</details>

---

**Q5.** Qu'est-ce que les conditions d'ecarts complementaires ?

<details>
<summary>Reponse</summary>

Ce sont des conditions necessaires et suffisantes d'optimalite pour une paire de solutions realisables (x*, y*) :
1. Si xj* > 0, la contrainte duale j est saturee
2. Si yi* > 0, la contrainte primale i est saturee

Resume : "si l'une est lache, l'autre est nulle".
</details>

---

**Q6.** Comment lit-on la solution duale dans le dictionnaire optimal du simplexe ?

<details>
<summary>Reponse</summary>

La variable duale yi* est l'**oppose du coefficient** de la variable d'ecart x_{n+i} dans l'equation de z. Si z = z* + ... + alpha_i * x_{n+i} + ..., alors yi* = -alpha_i.
</details>

---

**Q7.** Est-il possible que le primal ET le dual soient non bornes ?

<details>
<summary>Reponse</summary>

**Non, c'est impossible.** Si le primal est non borne, le dual est necessairement infaisable (pas non borne). La dualite faible interdit que les deux soient non bornes simultanement.
</details>

---

**Q8.** Une contrainte primale est non saturee a l'optimum. Que peut-on dire de la variable duale correspondante ?

<details>
<summary>Reponse</summary>

Par les ecarts complementaires (condition 2), si la contrainte primale i n'est pas saturee (il y a de la marge), alors **yi* = 0**.
</details>

---

**Q9.** Le dual d'un probleme de minimisation avec contraintes >= et variables >= 0 est un probleme de quelle forme ?

<details>
<summary>Reponse</summary>

C'est un probleme de **maximisation** avec contraintes **<=** et variables **>= 0**. C'est la correspondance standard inversee.
</details>

---

**Q10.** x1* = 0, x2* = 4 est solution realisable d'un primal. La contrainte duale pour x2 est 3y1 + y2 >= 5. L'ecart complementaire impose-t-il que cette contrainte soit saturee ?

<details>
<summary>Reponse</summary>

**Oui.** x2* = 4 > 0, donc par la condition (1), la contrainte duale correspondante doit etre saturee : 3y1* + y2* = 5.
</details>

---

*Chapitre precedent : [Chapitre 3 -- Degenerescence et Deux Phases](Chapitre3_Degenerescence_Deux_Phases.md)*  
*Chapitre suivant : [Chapitre 5 -- Le Simplexe Revise](Chapitre5_Simplexe_Revise.md)*
