# FICHE DE REVISION -- Modeles Lineaires (Controle)

---

# ========================================================
# PARTIE 1 : THEOREMES, COROLLAIRES ET PROPRIETES
# ========================================================

---

## CHAPITRE 1 -- Introduction a la PL

### Theoreme fondamental de la PL
> Si un programme lineaire a une solution optimale, alors au moins un **sommet** (point extreme) de la region realisable est optimal.

**Consequence** : il suffit d'examiner les sommets pour trouver l'optimum.

### Les 3 cas d'un PL (exhaustifs et mutuellement exclusifs)
1. **Infaisable** : region realisable vide
2. **Non borne** : z peut croitre indefiniment
3. **Solution optimale** : toujours atteinte en un sommet

---

## CHAPITRE 2 -- Methode du Simplexe

### Theoreme d'optimalite
> Un dictionnaire realisable est **optimal** ssi tous les coefficients des variables hors-base dans z sont **<= 0**.

### Propriete : Solutions multiples
> A l'optimal, si un coefficient c_j_barre = 0 pour une variable hors-base, alors il existe une **infinite de solutions optimales** (toute l'arete entre deux sommets optimaux).

---

## CHAPITRE 3 -- Degenerescence, Cyclage et Deux Phases

### Theoreme 1 : Cyclage
> Si la methode du simplexe ne se termine pas, alors elle doit **cycler**.

Le cyclage ne peut se produire qu'en presence de degenerescence.

### Theoreme 2 (Bland, 1977)
> La methode du simplexe se termine en un nombre fini d'iterations si les variables entrante et sortante sont selectionnees par la **regle du plus petit indice** a chaque iteration.

### Theoreme 3 : Methode lexicographique
> La methode du simplexe se termine en un nombre fini d'iterations si la variable sortante est selectionnee par la **regle lexicographique** a chaque iteration.

### Theoreme 4 : Probleme auxiliaire (Phase 1)
> Le PL original a une solution realisable **ssi** la valeur optimale du probleme auxiliaire est **zero** (w* = 0).

- w* = 0 : probleme **REALISABLE** -> passer a Phase 2
- w* < 0 : probleme **INFAISABLE** -> STOP

### Theoreme 5 : Theoreme fondamental de la PL (version complete)
> Tout PL sous forme standard :
> 1. S'il n'a pas de solution optimale => il est **infaisable** ou **non borne**
> 2. S'il a une solution realisable => il a une **solution de base realisable**
> 3. S'il a une solution optimale => il a une **solution de base optimale**

**Consequence** : le simplexe est **complet** (il trouvera l'optimum s'il existe).

### Complexite du simplexe
- **En pratique** : O(m) a O(3m) iterations
- **Pire cas** : 2^n (Klee-Minty 1972)
- **Alternatives polynomiales** : ellipsoide (Khachiyan 1979), points interieurs (Karmarkar 1984)

---

## CHAPITRE 4 -- Dualite

### Theoreme de dualite faible
> Pour toute solution realisable x du primal et y du dual :
> **c^T x <= b^T y**

### Corollaires de la dualite faible
1. Toute sol. duale realisable = **borne superieure** sur z*
2. Toute sol. primale realisable = **borne inferieure** sur w*
3. Si c^T x = b^T y pour des sol. realisables => **les deux sont optimales**
4. Si le primal est non borne => le dual est **infaisable**
5. Si le dual est non borne => le primal est **infaisable**

### Theoreme de dualite forte
> Si le primal a une solution optimale x*, alors le dual a aussi une solution optimale y*, et :
> **z* = c^T x* = b^T y* = w***

### Corollaire : Le dual du dual est le primal

### Theoreme des ecarts complementaires
> x* et y* sont optimales ssi elles sont realisables ET :
> 1. Si **xj* > 0** => la contrainte duale j est **saturee**
> 2. Si **yi* > 0** => la contrainte primale i est **saturee**

Resume : **"si l'une est lache, l'autre est nulle"**

### Corollaire : Combinaisons primal/dual possibles

|  | Dual Optimal | Dual Infaisable | Dual Non Borne |
|---|:---:|:---:|:---:|
| **Primal Optimal** | OUI (z*=w*) | NON | NON |
| **Primal Infaisable** | NON | OUI | OUI |
| **Primal Non Borne** | NON | OUI | NON |

- Les deux infaisables : **POSSIBLE**
- Les deux non bornes : **IMPOSSIBLE**

---

## CHAPITRE 5 -- Simplexe Revise

### Propriete : Prix fantomes = variables duales
> yi* = variation de z* quand bi augmente d'une unite

### Propriete : Lecture de la solution duale
> yi* = -(coefficient de la variable d'ecart x_{n+i} dans z du dictionnaire optimal)

---

# ========================================================
# PARTIE 2 : REGLES ET PROCEDURES METHODE PAR METHODE
# ========================================================

---

## METHODE 1 : MISE EN FORME STANDARD

### Quand : Toujours, avant le simplexe

### 3 conditions de la forme standard
1. **Maximisation**
2. Toutes les contraintes en **<=**
3. Toutes les variables **>= 0**

### Regles de conversion

| Situation | Transformation |
|-----------|---------------|
| min z | max (-z), puis z* = -z'* a la fin |
| Contrainte >= | Multiplier par -1 => <= |
| Contrainte = | Separer en <= et >= (puis convertir >=) |
| Variable x <= 0 | Poser y = -x, y >= 0 |
| Variable x libre | Poser x = x+ - x-, avec x+, x- >= 0 |

---

## METHODE 2 : SIMPLEXE (dictionnaire)

### Initialisation
- Ajouter 1 **variable d'ecart** par contrainte <= (xi = bi - ...)
- Variables d'ecart = **base initiale**
- **Condition** : tous les bi >= 0 (sinon => deux phases)

### Iteration : 3 etapes

**Etape 1 -- Variable ENTRANTE :**
- Regarder les coeff des hors-base dans z
- Choisir le **plus grand coefficient positif**
- Si tous <= 0 => **OPTIMAL, STOP**

**Etape 2 -- Variable SORTANTE (ratio minimum) :**
- Pour chaque var de base ou la var entrante a un coefficient reel **> 0** (elle apparait avec un **-** dans le dictionnaire) :
  - ratio = bi_barre / coeff_reel
- Choisir le **plus petit ratio**
- Egalite => **plus petit indice** (Bland)
- Si aucun coeff reel > 0 => **NON BORNE, STOP**

**Etape 3 -- Pivoter :**
1. Isoler la var entrante dans l'equation de la var sortante
2. Substituer dans toutes les autres equations + z
3. Retour a l'etape 1

### Detection rapide

| Signal | Conclusion |
|--------|-----------|
| Tous coeff de z <= 0 | **OPTIMAL** |
| Aucun ratio positif | **NON BORNE** |
| Un coeff = 0 dans z a l'optimal | **SOLUTIONS MULTIPLES** |

---

## METHODE 3 : REGLE DE BLAND (anti-cyclage)

### Quand : degenerescence (var de base = 0)

### 2 regles
1. **Entrante** : parmi les coeff positifs dans z => **plus petit INDICE**
2. **Sortante** : en cas d'egalite de ratio => **plus petit INDICE**

---

## METHODE 4 : METHODE LEXICOGRAPHIQUE (anti-cyclage)

### Procedure
1. Entrante : regle classique (plus grand coefficient)
2. Sortante : construire un **vecteur** par ligne candidate :
   - Vecteur = (bi, coeffs des hors-base) / |coeff de la var entrante|
3. Comparer en **ordre lexicographique**
4. Choisir le **minimum lexicographique**

---

## METHODE 5 : METHODE DE PERTURBATION (anti-cyclage)

### Procedure
1. Remplacer bi par bi + epsilon^i (0 < epsilon_m << ... << epsilon_1 << 1)
2. Simplexe normal (plus d'egalites dans les ratios)
3. A la fin, epsilon -> 0

---

## METHODE 6 : METHODE DES DEUX PHASES

### Quand : au moins un bi < 0 dans le dictionnaire initial

### PHASE 1 : Trouver une solution realisable

**1) Former le probleme auxiliaire :**
- Ajouter **+x0** a chaque contrainte
- Nouvel objectif : **max w = -x0**

**2) Pivot d'initialisation (PAS une iteration) :**
- x0 **entre** en base
- La variable avec le **bi le plus negatif sort**
- Isoler x0, substituer => tous les bi deviennent >= 0

**3) Simplexe sur w :**
- Regle speciale : si x0 peut sortir (egalite de ratio) => **toujours choisir x0**
- Continuer jusqu'a l'optimal

**4) Resultat :**
- w* = 0 => **REALISABLE**, passer a Phase 2
- w* < 0 => **INFAISABLE**, STOP

### PHASE 2 : Optimiser

**1) Preparer :**
- Supprimer x0 du dictionnaire
- Remplacer w par z original
- Exprimer z en fonction des hors-base actuelles (substituer les vars de base)

**2) Simplexe classique jusqu'a l'optimal**

---

## METHODE 7 : VARIABLES ARTIFICIELLES (variante Phase 1)

### Quand : contraintes >= ou = (pas de var de base naturelle)

| Type contrainte | Variable d'ecart | Variable artificielle |
|-----------------|-----------------|----------------------|
| **<=** | +xi (ecart) | Pas besoin |
| **>=** | -xi (surplus) | +yi (artificielle) |
| **=** | Rien | +yi (artificielle) |

### Procedure
- Objectif Phase 1 : **max w = -somme(yi)**
- Base initiale : ecarts (<=) + artificielles (>= et =)
- Exprimer w en substituant les artificielles
- Si w* = 0 et toutes artificielles hors-base => **realisable**
- Si w* < 0 => **infaisable**

---

## METHODE 8 : METHODE DU GRAND M

### Quand : alternative aux deux phases

### Procedure
1. Pour chaque contrainte avec bi < 0 : ajouter artificielle yi
2. Objectif : ajouter **-M * yi** (M tres grand)
3. Simplexe en un seul coup
4. Si artificielle en base a l'optimal => **INFAISABLE**

| | Deux Phases | Grand M |
|---|---|---|
| Avantage | Exact | Un seul probleme |
| Inconvenient | Deux problemes | Erreurs si M mal choisi |

---

## METHODE 9 : ECRITURE DU DUAL

### Primal max => Dual min

| PRIMAL (max) | DUAL (min) |
|---|---|
| Contrainte <= | Variable yi >= 0 |
| Contrainte >= | Variable yi <= 0 |
| Contrainte = | Variable yi libre |
| Variable xj >= 0 | Contrainte j >= |
| Variable xj <= 0 | Contrainte j <= |
| Variable xj libre | Contrainte j = |
| Coeff objectif c | Membres droits dual |
| Membres droits b | Coeff objectif dual |
| Matrice A | Matrice A^T |

---

## METHODE 10 : ECARTS COMPLEMENTAIRES

### Pour trouver y* a partir de x*
1. Verifier x* realisable
2. Contrainte primale i **non saturee** => yi* = 0
3. xj* > 0 => contrainte duale j **saturee** (ecrire egalite)
4. Resoudre le systeme
5. Verifier y* realisable pour le dual
6. Verifier z* = w* (dualite forte)

Si systeme inconsistant => x* **n'est PAS optimale**

---

## METHODE 11 : LECTURE DU DUAL DANS LE DICTIONNAIRE

### Regle
> yi* = -(coefficient de la var d'ecart x_{n+i} dans z)

Exemple : z = 15 - 3x4 - 2x5
- y1* = -(-3) = **3**
- y2* = -(-2) = **2**
- Si x_ecart n'apparait pas dans z => yi* = 0

---

## METHODE 12 : SIMPLEXE REVISE

### Les 5 etapes

| Etape | Quoi | Formule |
|-------|------|---------|
| 1 | Prix fantomes | y^T B = c_B^T |
| 2 | Couts reduits | c_j_barre = c_j - y^T a_j |
| 3 | Direction | B d = a_j |
| 4 | Ratio minimum | t* = min{(x_B)_i / d_i, d_i > 0} |
| 5 | Mise a jour | echange var entrante/sortante |

### Formules cles
```
x_B = B^{-1} b          (valeurs de base)
y^T = c_B^T B^{-1}      (prix fantomes)
c_j_barre = c_j - y^T a_j   (couts reduits)
d = B^{-1} a_j          (direction)
z = c_B^T B^{-1} b      (valeur objectif)
z_new = z + t* * c_j_barre  (nouvelle valeur)
```

### Criteres
- Tous c_j_barre <= 0 => **OPTIMAL**
- Tous d_i <= 0 => **NON BORNE**

---

## METHODE 13 : ANALYSE DE SENSIBILITE

### Variation d'un membre droit
```
z*(t) = z* + yi* * t
```
Valable tant que B^{-1}(b + t*ei) >= 0.

### Decision achat/vente
- **Acheter** ressource i si prix < yi*
- **Vendre** ressource i si prix > yi*
- Benefice net = |yi* - prix| x quantite

---

# ========================================================
# PARTIE 3 : PIEGES COURANTS A EVITER
# ========================================================

1. **Signe dans le dictionnaire** : xi = bi - aie*xe. Le coeff reel est aie (apres le -). C'est lui pour le ratio.

2. **Ratio test** : seulement pour les lignes ou coeff reel > 0 (var entrante avec -). Les lignes avec + ne posent pas de limite.

3. **Phase 1 pivot initial** : PAS une iteration. C'est juste pour rendre le dictionnaire realisable.

4. **Phase 2** : ne pas oublier de SUPPRIMER x0 et RE-EXPRIMER z en fonction des hors-base.

5. **Dualite faible** : c^T x <= b^T y. Si egalite => les deux sont optimales.

6. **EC dans les deux sens** : primal->dual ET dual->primal.

7. **Cout reduit negatif** : ne veut PAS dire que le produit n'est pas rentable. Ca veut dire que les ressources seraient mieux ailleurs.

8. **min -> max** : reinverser z* a la fin.

9. **Solutions multiples** : coeff exactement = 0 dans z a l'optimal.

10. **Non-negativite** : ce sont aussi des contraintes, ne pas les oublier.
