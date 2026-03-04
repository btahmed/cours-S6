# Chapitre 5 -- Le Simplexe Revise

**Cours : Modeles Lineaires de la Recherche Operationnelle**  
**Universite Clermont Auvergne -- Clermont Auvergne INP -- ISIMA**  
**Professeur : Herve KERIVIN**  
**Annee : 2024-2025**

---

## Table des matieres

1. [Resume](#1-resume)
2. [Explication detaillee](#2-explication-detaillee)
   - [Formulation matricielle](#21-formulation-matricielle)
   - [Les 5 etapes de l'iteration](#22-les-5-etapes-de-literation)
   - [Interpretation economique](#23-interpretation-economique)
   - [Analyse de sensibilite](#24-analyse-de-sensibilite)
   - [Comparaison standard vs revise](#25-comparaison-standard-vs-revise)
3. [Exercices corriges](#3-exercices-corriges)
4. [Quiz](#4-quiz)

---

## 1. Resume

### Idee generale

Le simplexe revise est une version matricielle du simplexe. Au lieu de maintenir le dictionnaire complet, on travaille avec la **matrice de base B** et on calcule uniquement les informations necessaires a chaque iteration.

### Notation matricielle

| Symbole | Signification |
|---------|--------------|
| **B** | Matrice de base (m x m) : colonnes de A correspondant aux variables de base |
| **N** | Matrice hors-base : colonnes de A correspondant aux variables hors-base |
| **c_B** | Vecteur des coefficients objectif des variables de base |
| **c_N** | Vecteur des coefficients objectif des variables hors-base |
| **x_B** | Vecteur des variables de base |
| **x_N** | Vecteur des variables hors-base |
| **y** | Vecteur des variables duales (prix fantomes) |

### Les 5 etapes d'une iteration

1. **Resoudre** y^T B = c_B^T (trouver les prix fantomes)
2. **Calculer les couts reduits** : c_j_barre = c_j - y^T a_j pour chaque variable hors-base
3. **Resoudre** B d = a_j (direction du pivot)
4. **Test du ratio minimum** : t* = min{x_Bi / d_i | d_i > 0}
5. **Mise a jour** de la base

### Formules cles

```
Valeurs de base :      x_B = B^{-1} b
Prix fantomes :        y^T = c_B^T B^{-1}
Couts reduits :        c_j_barre = c_j - y^T a_j
Direction :            d = B^{-1} a_j
Valeur objectif :      z = c_B^T x_B = c_B^T B^{-1} b
```

---

## 2. Explication detaillee

### 2.1 Formulation matricielle

#### Partition base / hors-base

Considerons un PL en forme standard avec m contraintes et n variables (plus m variables d'ecart) :

```
max z = c^T x,  s.c. Ax <= b, x >= 0
```

Apres ajout des variables d'ecart, on a le systeme :
```
[A | I] [x ; x_e] = b
```

A chaque iteration du simplexe, les variables sont partitionnees en :
- **Variables de base** (m variables) : x_B, avec matrice B (m x m)
- **Variables hors-base** (n variables) : x_N, avec matrice N (m x n)

Le systeme s'ecrit :
```
B x_B + N x_N = b
```

En mettant x_N = 0 (solution de base) :
```
x_B = B^{-1} b
```

La solution est **realisable** si x_B = B^{-1} b >= 0.

#### Valeur de la fonction objectif

```
z = c_B^T x_B + c_N^T x_N = c_B^T B^{-1} b    (car x_N = 0)
```

---

### 2.2 Les 5 etapes de l'iteration

#### Etape 1 : Calculer les prix fantomes (variables duales)

On resout le systeme :
```
y^T B = c_B^T
```

C'est-a-dire y = (B^{-1})^T c_B, ou de maniere equivalente y^T = c_B^T B^{-1}.

**Les composantes de y sont les prix fantomes** (shadow prices) : yi represente la variation de z quand bi augmente d'une unite.

#### Etape 2 : Calculer les couts reduits

Pour chaque variable hors-base xj, on calcule :
```
c_j_barre = c_j - y^T a_j
```

- Si **tous les c_j_barre <= 0** : la solution courante est **optimale**. STOP.
- Sinon, on choisit la variable entrante xe avec le plus grand c_j_barre > 0.

**Interpretation du cout reduit :**
- c_j_barre > 0 : faire entrer xj en base augmenterait z de c_j_barre par unite
- c_j_barre < 0 : faire entrer xj diminuerait z (pas interessant)
- c_j_barre = 0 : pas d'effet sur z (solution alternative possible)

#### Etape 3 : Calculer la direction

On resout le systeme :
```
B d = a_j     (ou a_j est la colonne de la variable entrante)
```

d = B^{-1} a_j. Les composantes de d indiquent comment les variables de base changent quand la variable entrante augmente.

#### Etape 4 : Test du ratio minimum

Pour chaque composante d_i > 0 :
```
ratio_i = (x_B)_i / d_i
```

```
t* = min { (x_B)_i / d_i  |  d_i > 0 }
```

La variable sortante est celle qui realise le minimum. Si **aucun d_i > 0**, le probleme est **non borne**.

#### Etape 5 : Mise a jour de la base

- La variable entrante prend la valeur t*
- Les variables de base sont mises a jour : (x_B)_i ← (x_B)_i - t* * d_i
- La variable sortante quitte la base (elle vaut 0)
- On met a jour le "basis heading" (la liste des variables en base)
- La matrice de base B est mise a jour (on remplace la colonne de la variable sortante par a_j)

**Nouvelle valeur objectif :** z ← z + t* * c_j_barre

---

### 2.3 Interpretation economique

#### Prix fantomes (shadow prices)

Les prix fantomes y1*, y2*, ..., ym* a l'optimum representent la **valeur marginale** de chaque ressource :

> yi* = variation de z* quand bi augmente d'une unite (toutes choses egales par ailleurs)

**Interpretation :** C'est le prix maximal qu'on devrait etre pret a payer pour une unite supplementaire de la ressource i.

**Exemples :**
- yi* = 3 : une unite supplementaire de la ressource i augmente le profit de 3 dollars
- yi* = 0 : la ressource i n'est pas utilisee a pleine capacite (contrainte non saturee)

#### Couts reduits

Le cout reduit c_j_barre d'une variable hors-base xj represente le **cout d'opportunite** de produire une unite de xj :

```
c_j_barre = c_j - y^T a_j
           = (profit de xj) - (valeur des ressources consommees par xj)
```

- Si c_j_barre < 0 : les ressources consommees par xj valent **plus** que le profit de xj. Il n'est pas rentable de produire xj.
- Si c_j_barre > 0 : il serait rentable de produire xj (on n'est pas a l'optimum).
- Si c_j_barre = 0 : on est indifferent (solution alternative).

> **Important :** Un cout reduit negatif ne signifie PAS que le produit n'est pas rentable en absolu. Il signifie que les ressources qu'il consomme seraient mieux utilisees par les produits deja en production.

#### Formule de substitution

Quand on introduit une unite de la variable hors-base xj :
```
z_nouveau = z - c_j_barre * xj
```

Chaque unite de xj consomme des ressources qui auraient pu etre utilisees pour produire une combinaison de (d1, d2, ..., dm) unites des variables de base, ou d = B^{-1} a_j.

---

### 2.4 Analyse de sensibilite

#### Sensibilite sur les membres droits

Si on modifie le vecteur b en b + t * e_i (augmentation de t unites de la ressource i) :

```
z*(t) = z* + yi* * t
```

Cela est valable tant que la base reste optimale, c'est-a-dire tant que :
```
x_B(t) = B^{-1}(b + t * e_i) >= 0
```

**Plage de validite :** On peut determiner l'intervalle de t pour lequel la base ne change pas en resolvant les inegalites ci-dessus.

#### Exemple pratique

Probleme de l'atelier avec :
- b1 = 225 (travail), b2 = 117 (metal), b3 = 420 (bois)
- Prix fantomes : y1* = 2 (travail), y2* = 1 (metal), y3* = 3 (bois)

**Questions typiques :**

1. **Acheter 5 heures de travail a 10 $/h ?**
   - Gain en profit : y1* x 5 = 2 x 5 = 10 $
   - Cout : 10 x 5 = 50 $
   - Benefice net : 10 - 50 = -40 $ → **NON, trop cher**

2. **Vendre 2 unites de metal a 6 $/unite ?**
   - Perte en profit : y2* x 2 = 1 x 2 = 2 $
   - Revenu : 6 x 2 = 12 $
   - Benefice net : 12 - 2 = +10 $ → **OUI, rentable**

3. **Si le bois passe de 420 a 430 ?**
   - Delta z* = y3* x 10 = 3 x 10 = +30 $
   - Nouveau profit : 1827 + 30 = 1857 $

**Regle generale :** Acheter la ressource i si son prix < yi*. Vendre si son prix > yi*.

---

### 2.5 Comparaison standard vs revise

| Aspect | Simplexe standard | Simplexe revise |
|--------|-------------------|-----------------|
| **Representation** | Dictionnaire complet (tableau) | Matrice de base B |
| **Mise a jour** | Tout le dictionnaire a chaque iteration | Seulement B^{-1} |
| **Memoire** | O(m x n) | O(m^2) |
| **Calcul par iteration** | O(m x n) | O(m^2) si B^{-1} maintenu |
| **Avantage** | Simple a comprendre et a la main | Plus efficace pour les grands problemes |
| **Usage** | Enseignement, petits problemes | Implementations logicielles |

**Quand le simplexe revise est-il avantageux ?**
- Quand n >> m (beaucoup plus de variables que de contraintes)
- Quand la matrice A est creuse (beaucoup de zeros)
- Pour les problemes de grande taille

---

## 3. Exercices corriges

### Exercice 1 : Formulation matricielle

**Enonce :**
```
maximiser   z = 3x1 + 2x2 + 5x3
sujet a     x1 + 2x2 + x3 <= 6
            2x1 + x2 + 3x3 <= 10
            x1, x2, x3 >= 0
```
Variables d'ecart x4, x5. Base proposee B = {x1, x3}.

#### Solution

**Matrices de la base {x1, x3} :**
```
B = | 1  1 |      c_B = | 3 |      b = | 6  |
    | 2  3 |            | 5 |          | 10 |
```

**Inverse de B :**
```
det(B) = 3 - 2 = 1

B^{-1} = |  3  -1 |
         | -2   1 |
```

**Valeurs des variables de base :**
```
x_B = B^{-1} b = |  3  -1 | | 6  | = | 18-10 | = |  8 |
                  | -2   1 | | 10 |   | -12+10|   | -2 |
```

x1 = 8, x3 = -2. **x3 < 0 : base non realisable !**

**Prix fantomes :**
```
y^T B = c_B^T : [y1, y2] | 1  1 | = [3, 5]
                          | 2  3 |

y1 + 2y2 = 3
y1 + 3y2 = 5  →  y2 = 2, y1 = -1

y^T = [-1, 2]
```

y1 = -1 < 0 : la solution duale est aussi non realisable.

**Couts reduits des variables hors-base (x2, x4, x5) :**

| Variable | c_j | y^T a_j | c_j_barre |
|----------|-----|---------|-----------|
| x2 | 2 | [-1,2].[2,1]^T = -2+2 = 0 | 2-0 = **2** |
| x4 | 0 | [-1,2].[1,0]^T = -1 | 0-(-1) = **1** |
| x5 | 0 | [-1,2].[0,1]^T = 2 | 0-2 = **-2** |

**Conclusion :** La base {x1, x3} n'est pas realisable. On doit repartir de la base initiale {x4, x5}.

#### Resolution complete depuis la base initiale

**Base {x4, x5} :** B = I, c_B = [0,0], x_B = [6,10], z = 0.

y^T = [0,0]. Couts reduits : c_1_barre = 3, c_2_barre = 2, c_3_barre = 5.

x3 entre (c_3_barre = 5 max). d = B^{-1} a_3 = I.[1,3]^T = [1,3]^T.

Ratios : 6/1 = 6, 10/3 = 3.33 → t* = 10/3, x5 sort.

**Nouvelle base {x4, x3} :**
```
B = | 1  1 |     x_B = | x4 | = | 8/3  |     z = 50/3
    | 0  3 |            | x3 |   | 10/3 |
```

y^T B = [0,5] → y1 = 0, y2 = 5/3.

Couts reduits :
- c_1_barre = 3 - [0, 5/3].[1,2]^T = 3 - 10/3 = **-1/3 < 0**
- c_2_barre = 2 - [0, 5/3].[2,1]^T = 2 - 5/3 = **1/3 > 0** → x2 entre
- c_5_barre = 0 - [0, 5/3].[0,1]^T = **-5/3 < 0**

x2 entre. B^{-1} = | 1  -1/3 |.  d = B^{-1}.[2,1]^T = [5/3, 1/3]^T.
                    | 0   1/3 |

Ratios : (8/3)/(5/3) = 8/5, (10/3)/(1/3) = 10 → t* = 8/5, x4 sort.

**Nouvelle base {x2, x3} :**
```
x2 = 8/5, x3 = 10/3 - (1/3)(8/5) = 14/5
z = 2(8/5) + 5(14/5) = 86/5 = 17.2
```

B = | 2  1 |,  c_B = [2,5]. y^T B = [2,5] → y1 = 1/5, y2 = 8/5.
    | 1  3 |

Couts reduits :
- c_1_barre = 3 - [1/5, 8/5].[1,2]^T = 3 - 17/5 = **-2/5 < 0**
- c_4_barre = 0 - [1/5, 8/5].[1,0]^T = **-1/5 < 0**
- c_5_barre = 0 - [1/5, 8/5].[0,1]^T = **-8/5 < 0**

**Tous les couts reduits <= 0 : OPTIMAL !**

```
x2* = 8/5 = 1.6,  x3* = 14/5 = 2.8,  z* = 86/5 = 17.2
```

Prix fantomes : y1* = 1/5 (contrainte 1), y2* = 8/5 (contrainte 2).

---

### Exercice 2 : Deux iterations completes

**Enonce :**
```
maximiser   z = 4x1 + 3x2
sujet a     x1 + x2 <= 4
            2x1 + x2 <= 6
            x1, x2 >= 0
```
Base initiale : {x3, x4}.

#### Solution

**Iteration 1 :**

Base {x3, x4} : B = I, c_B = [0,0], x_B = [4,6], z = 0.

Etape 1 : y^T = [0,0].

Etape 2 : c_1_barre = 4, c_2_barre = 3. x1 entre (max).

Etape 3 : d = I.[1,2]^T = [1,2]^T.

Etape 4 : Ratios : 4/1 = 4, 6/2 = 3. t* = 3, x4 sort.

Etape 5 : x1 = 3, x3 = 4 - 3(1) = 1. z = 12. Nouvelle base {x3, x1}.

**Iteration 2 :**

B1 = | 1  1 |, c_B = [0,4], x_B = [1,3], z = 12.
     | 0  2 |

Etape 1 : y^T B1 = [0,4]
- y1 = 0, y1 + 2y2 = 4 → y2 = 2. y^T = [0,2].

Etape 2 :
- c_2_barre = 3 - [0,2].[1,1]^T = 3 - 2 = **1 > 0** → x2 entre
- c_4_barre = 0 - [0,2].[0,1]^T = -2 < 0

Etape 3 : B1^{-1} = | 1  -1/2 |. d = B1^{-1}.[1,1]^T = [1/2, 1/2]^T.
                     | 0   1/2 |

Etape 4 : Ratios : 1/(1/2) = 2, 3/(1/2) = 6. t* = 2, x3 sort.

Etape 5 : x2 = 2, x1 = 3 - 2(1/2) = 2. z = 14. Nouvelle base {x2, x1}.

**Test d'optimalite :**

B2 = | 1  1 |, c_B = [3,4], x_B = [2,2], z = 14.
     | 1  2 |

y^T B2 = [3,4] → y1 + y2 = 3, y1 + 2y2 = 4 → y2 = 1, y1 = 2.

Couts reduits :
- c_3_barre = 0 - [2,1].[1,0]^T = -2 < 0
- c_4_barre = 0 - [2,1].[0,1]^T = -1 < 0

**Tous <= 0 : OPTIMAL en 2 iterations !**

```
x1* = 2,  x2* = 2,  z* = 14
```

Verification : 2 + 2 = 4 <= 4, 2(2) + 2 = 6 <= 6. Les deux contraintes sont saturees.

Solution duale : y1* = 2, y2* = 1. w* = 4(2) + 6(1) = 14 = z*.

---

### Exercice 3 : Prix fantomes et analyse de sensibilite

**Enonce :** Probleme de l'atelier :
```
max z = 19x1 + 13x2 + 12x3 + 17x4
s.c. 3x1 + 2x2 + x3 + 2x4 <= 225  (travail)
     x1 + x2 + x3 + x4    <= 117  (metal)
     4x1 + 3x2 + 3x3 + 4x4 <= 420  (bois)
```

Base optimale : {x1, x3, x4}. Solution : x1 = 39, x3 = 48, x4 = 30, z* = 1827.

#### Prix fantomes

```
y^T B* = c_B^T = [19, 12, 17]

B* = | 3  1  2 |
     | 1  1  1 |
     | 4  3  4 |

Systeme :
3y1 + y2 + 4y3 = 19
y1 + y2 + 3y3 = 12
2y1 + y2 + 4y3 = 17

L1 - L3 : y1 = 2
Dans L2 : 2 + y2 + 3y3 = 12 → y2 + 3y3 = 10
Dans L1 : 6 + y2 + 4y3 = 19 → y2 + 4y3 = 13
Soustraction : y3 = 3, y2 = 1
```

**Prix fantomes : y1* = 2 (travail), y2* = 1 (metal), y3* = 3 (bois)**

Verification : w* = 225(2) + 117(1) + 420(3) = 450 + 117 + 1260 = 1827 = z*.

#### Interpretation economique

- **y1* = 2** : une heure de travail supplementaire augmente le profit de **2 $**
- **y2* = 1** : une unite de metal supplementaire augmente le profit de **1 $**
- **y3* = 3** : une unite de bois supplementaire augmente le profit de **3 $**

Le bois est la ressource la plus precieuse a la marge.

#### Decisions

**Acheter 5h de travail a 10 $/h ?**
- Gain : 2 x 5 = 10 $. Cout : 10 x 5 = 50 $. Net = -40 $.
- **NON.** Le prix (10 $/h) depasse la valeur marginale (2 $/h).

**Vendre 2 unites de metal a 6 $/unite ?**
- Perte en profit : 1 x 2 = 2 $. Revenu : 6 x 2 = 12 $. Net = +10 $.
- **OUI.** Le prix de vente (6 $) depasse la valeur marginale (1 $).

**Bois passe de 420 a 430 ?**
- Delta z* = 3 x 10 = +30 $. Nouveau profit : 1857 $.

---

### Exercice 4 : Interpretation des couts reduits

**Pourquoi le cout reduit des bureaux (x2) est negatif (-5/2) alors que leur profit unitaire est positif (13 $) ?**

#### Solution

Un bureau consomme : 2h de travail, 1 unite de metal, 3 unites de bois.

Avec les prix fantomes y = [7/2, 17/2, 0] (a la premiere iteration) :

```
Valeur des ressources consommees = y^T a_2 = (7/2)(2) + (17/2)(1) + 0(3) = 7 + 8.5 = 15.5 $
```

```
Cout reduit = c_2 - y^T a_2 = 13 - 15.5 = -2.5 $
```

**Interpretation :** Les ressources qu'un bureau consomme (valeur : 15.50 $) depassent le profit qu'il genere (13 $). Il vaut mieux utiliser ces ressources pour les bibliotheques et chaises, qui les exploitent plus efficacement.

**Lecon :** Un cout reduit negatif ne signifie pas qu'un produit est non rentable en absolu. Il signifie que les ressources qu'il consomme seraient **mieux utilisees** par les produits deja en production optimale.

---

## 4. Quiz

### Questions de comprehension

**Q1.** Qu'est-ce que la matrice de base B ?

<details>
<summary>Reponse</summary>

C'est la matrice m x m formee par les colonnes de A (et de la matrice identite pour les variables d'ecart) correspondant aux variables de base. Elle est inversible et permet de calculer les valeurs des variables de base : x_B = B^{-1} b.
</details>

---

**Q2.** Donnez les 5 etapes d'une iteration du simplexe revise.

<details>
<summary>Reponse</summary>

1. Resoudre y^T B = c_B^T (prix fantomes)
2. Calculer les couts reduits c_j_barre = c_j - y^T a_j
3. Resoudre B d = a_j (direction de pivot)
4. Test du ratio minimum t* = min{(x_B)_i / d_i | d_i > 0}
5. Mise a jour de la base (echange variable entrante/sortante)
</details>

---

**Q3.** Qu'est-ce qu'un prix fantome (shadow price) ?

<details>
<summary>Reponse</summary>

C'est la valeur marginale d'une ressource a l'optimum. yi* represente l'augmentation de z* quand bi augmente d'une unite. C'est le prix maximal qu'on devrait accepter de payer pour une unite supplementaire de la ressource i.
</details>

---

**Q4.** Qu'est-ce que le cout reduit d'une variable hors-base ?

<details>
<summary>Reponse</summary>

c_j_barre = c_j - y^T a_j. C'est la difference entre le profit de xj (c_j) et la valeur des ressources qu'il consomme (y^T a_j). C'est le cout d'opportunite : combien on gagnerait (ou perdrait) en introduisant une unite de xj dans la production.
</details>

---

**Q5.** Si le prix fantome du travail est y1* = 5 $/h, devrait-on acheter des heures supplementaires a 3 $/h ?

<details>
<summary>Reponse</summary>

**OUI.** Le prix d'achat (3 $/h) est inferieur a la valeur marginale (5 $/h). Chaque heure achetee rapporte un benefice net de 5 - 3 = 2 $. On devrait acheter tant que le prix reste inferieur a 5 $ et que la base ne change pas.
</details>

---

**Q6.** Un produit a un profit unitaire de 20 $ et un cout reduit de -3 $. Faut-il le produire ?

<details>
<summary>Reponse</summary>

**Non, pas dans la solution optimale actuelle.** Bien que le produit rapporte 20 $ par unite, les ressources qu'il consomme valent 23 $ (aux prix fantomes courants). Ces ressources seraient mieux utilisees pour les produits deja en base. Le cout reduit negatif indique que sa production reduirait le profit total.
</details>

---

**Q7.** Quelle est la formule pour la valeur objectif dans le simplexe revise ?

<details>
<summary>Reponse</summary>

z = c_B^T x_B = c_B^T B^{-1} b. C'est le produit scalaire entre les coefficients objectif des variables de base et leurs valeurs.
</details>

---

**Q8.** Pourquoi le simplexe revise est-il plus efficace que le simplexe standard pour les grands problemes ?

<details>
<summary>Reponse</summary>

Le simplexe standard met a jour le dictionnaire complet a chaque iteration (cout O(m x n)). Le simplexe revise ne travaille qu'avec la matrice B (m x m), ce qui coute O(m^2). Quand n >> m, l'economie est considerable. De plus, il exploite mieux la structure creuse des problemes reels.
</details>

---

**Q9.** Comment verifie-t-on la dualite forte avec le simplexe revise ?

<details>
<summary>Reponse</summary>

On verifie que w* = b^T y* = z* = c_B^T x_B. Si les deux valeurs sont egales, la dualite forte est confirmee et les solutions sont optimales.
</details>

---

**Q10.** Le bois a un prix fantome y3* = 3 $. On peut acheter 10 unites de bois a 2 $/unite. Quel est le benefice net ?

<details>
<summary>Reponse</summary>

- Gain en profit : 3 x 10 = 30 $
- Cout d'achat : 2 x 10 = 20 $
- **Benefice net : +10 $**

C'est rentable car le prix d'achat (2 $) est inferieur au prix fantome (3 $). Attention : cela n'est valable que si la base reste la meme (petite perturbation).
</details>

---

*Chapitre precedent : [Chapitre 4 -- La Theorie de la Dualite](Chapitre4_Dualite.md)*
