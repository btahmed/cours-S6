# FICHE DE REVISION COMPLETE — Optimisation dans les Reseaux

> **Cours de F. Bendali-Mailfert — L3 Informatique — S6 2025**
> Tous les theoremes, definitions, algorithmes, formules et methodes d'examen en un seul fichier.

---

## TABLE DES MATIERES

1. [Graphes : Definitions et Notations](#1-graphes--definitions-et-notations)
2. [Reseaux : Definitions](#2-reseaux--definitions)
3. [Complexite](#3-complexite)
4. [Probleme du Plus Court Chemin (PCC)](#4-probleme-du-plus-court-chemin-pcc)
5. [Probleme du Flot Maximum](#5-probleme-du-flot-maximum)
6. [Probleme du Flot de Cout Minimum](#6-probleme-du-flot-de-cout-minimum)
7. [Arbres et Arbre Couvrant de Poids Minimum (MST)](#7-arbres-et-arbre-couvrant-de-poids-minimum-mst)
8. [Connexite et Robustesse des Reseaux](#8-connexite-et-robustesse-des-reseaux)
9. [Algorithmes de Diffusion dans les Reseaux Sans Fil](#9-algorithmes-de-diffusion-dans-les-reseaux-sans-fil)
10. [Methodes d'Examen : Procedures Detaillees](#10-methodes-dexamen--procedures-detaillees)
11. [Tableau Recapitulatif : Quel Algorithme Utiliser ?](#11-tableau-recapitulatif--quel-algorithme-utiliser-)
12. [Formules et Resultats a Connaitre par Coeur](#12-formules-et-resultats-a-connaitre-par-coeur)

---

# 1. Graphes : Definitions et Notations

## 1.1 Graphe non oriente

**Definition :** G = (V, E) avec :
- V = {v1, v2, ..., vn} : ensemble de **sommets**
- E = {e1, e2, ..., em} : ensemble d'**aretes**
- Chaque arete ei est une **paire** {u, v} d'elements de V

## 1.2 Graphe oriente

**Definition :** G = (V, E) avec :
- V : ensemble de **sommets**
- E : ensemble d'**arcs**
- Chaque arc ei est un **couple** (u, v) d'elements de V
- I(e) = u : extremite **initiale**
- T(e) = v : extremite **terminale**

## 1.3 Graphe simple

Pas de boucle (arete reliant un sommet a lui-meme) ni d'arete multiple.

## 1.4 Incidence et Adjacence

| Terme | Definition |
|-------|-----------|
| **Incidence** | Un sommet v et une arete e sont incidents si v est une extremite de e |
| **Adjacence (sommets)** | Deux sommets sont adjacents s'ils sont incidents a une meme arete |
| **Adjacence (aretes)** | Deux aretes sont adjacentes si elles sont incidentes a un meme sommet |

## 1.5 Matrices de representation

### Matrice d'adjacence A = (aij)

- **Non oriente :** aij = nombre d'aretes entre i et j. A est **symetrique**. Valeurs dans {0, 1} pour graphe simple.
- **Oriente :** idem mais **non symetrique** en general.

### Matrice d'incidence B = (bij) — Cas non oriente

- Ligne i = sommet vi, Colonne j = arete ej
- bij = nombre de fois ou vi est incidente a ej
- Valeurs dans {0, 1, 2}
- **Propriete :** somme de chaque colonne = 2 (chaque arete a 2 extremites)

### Matrice d'incidence B = (bij) — Cas oriente

```
bij = +1  si vi = I(ej)  (extremite initiale, depart)
bij = -1  si vi = T(ej)  (extremite terminale, arrivee)
bij =  0  sinon
```

- Valeurs dans {-1, 0, +1}
- **Propriete :** somme de chaque colonne = 0

## 1.6 Degre d'un sommet

**Definition :** d(v) = nombre d'aretes incidentes au sommet v

- delta(G) = min{d(v), v in V} : degre **minimum**
- Delta(G) = max{d(v), v in V} : degre **maximum**

### PROPRIETE FONDAMENTALE (a connaitre)

```
Somme des degres = 2 * |E|
```

**Consequence :** Le nombre de sommets de degre impair est toujours **pair**.

## 1.7 Graphe complet

**Definition :** Graphe ou deux sommets quelconques sont adjacents. Notation : **Kn**

| Propriete | Formule |
|-----------|---------|
| Nombre d'aretes | \|E\| = n(n-1)/2 |
| Degre de chaque sommet | d(v) = n - 1 |
| delta(Kn) = Delta(Kn) | n - 1 |

Exemples : K3 = 3 aretes, K4 = 6 aretes, K5 = 10 aretes.

## 1.8 Sous-graphes

| Type | Definition |
|------|-----------|
| **Sous-graphe general** | H = (U, F) avec U ⊆ V, F ⊆ E, extremites de F dans U |
| **Sous-graphe engendre par U** | GU = (U, F) avec F = **toutes** les aretes entre sommets de U |
| **Sous-graphe engendre par F** | G[F] = (U, F) avec U = extremites des aretes de F |
| **Graphe partiel** | H = (V, F) avec F ⊆ E — on garde **tous** les sommets |

**Difference cle :**
- Sous-graphe engendre par U : on prend TOUTES les aretes entre sommets de U
- Graphe partiel : on garde tous les sommets, on enleve des aretes

## 1.9 Stables et Graphes bipartis

**Stable :** Sous-ensemble S de sommets tel que le sous-graphe GS est vide d'aretes (aucune adjacence entre sommets de S).

**Graphe k-parti :** Ensemble de sommets partitionnable en k stables.

**Graphe biparti :** Graphe 2-parti. Sommets partitionnables en V1 et V2 tels que toute arete relie V1 a V2.

### THEOREME : Caracterisation des graphes bipartis

> **Un graphe est biparti ⟺ il ne contient pas de cycle de longueur impaire.**

Exemples bipartis : arbres, cycles de longueur paire, K_{m,n}
Non-bipartis : K3 (triangle), tout cycle impair

## 1.10 Cheminements

### Graphes non orientes

| Terme | Definition |
|-------|-----------|
| **Chaine** | Suite alternee v0 e1 v1 e2 ... vk. Extremites de ei = vi-1 et vi |
| **Chaine simple** | Toutes les **aretes** sont differentes |
| **Chaine elementaire** | Tous les **sommets** sont differents (⟹ simple) |
| **Cycle** | Chaine **fermee** simple (v0 = vk) |
| **Longueur** | Nombre d'aretes |

### Graphes orientes

| Terme | Definition |
|-------|-----------|
| **Chemin** | Suite v0 e1 v1... ou ei a vi-1 comme initiale et vi comme terminale (on **respecte le sens**) |
| **Chemin simple** | Tous les **arcs** differents |
| **Chemin elementaire** | Tous les **sommets** differents |
| **Circuit** | Chemin **ferme** simple |

**Difference fondamentale :** Dans un graphe oriente, on **respecte le sens** des arcs. Dans une chaine, on peut ignorer le sens.

## 1.11 Connexite

**Connexe :** Entre tout couple de sommets existe une chaine.

**Fortement connexe (oriente) :** Entre tout couple de sommets existe un chemin (dans les deux sens).

### Coupe (Cocycle)

**Definition :** Coupe associee a S ⊆ V :

```
delta(S) = [S, V\S] = {e = uv in E : u in S, v in V\S}
```

= ensemble des aretes qui "traversent" la frontiere entre S et V\S.

Si on supprime toutes les aretes de delta(S), le graphe est deconnecte.

---

# 2. Reseaux : Definitions

**Definition :** Un reseau est un graphe oriente G = (V, E) dont les arcs ou noeuds sont munis de valeurs (couts, capacites, demandes).

### Notations

**Pour les sommets (∀ i in V) :**
- bi < 0 : sommet i a une **demande** (puits, destination)
- bi > 0 : sommet i a une **offre** (source, origine)
- bi = 0 : sommet i est un **noeud de transit**

**Pour les arcs (∀ (i,j) in E) :**
- cij : **cout unitaire** de l'arc (i,j)
- uij : **capacite superieure** de l'arc (i,j)
- lij : **capacite inferieure** de l'arc (i,j)

### Conservation du flot

```
Somme(bi, i in V) = 0
```

Somme des offres = somme des demandes. Ce qui entre dans le reseau doit en sortir.

---

# 3. Complexite

| Notation | Nom | Exemple |
|----------|-----|---------|
| O(1) | Constante | Acces tableau |
| O(log n) | Logarithmique | Recherche dichotomique |
| O(n) | Lineaire | Parcours liste |
| O(n log n) | Linearithmique | Tri fusion |
| O(n^2) | Quadratique | Tri a bulles |
| O(n^3) | Cubique | Produit matrices naif |
| O(2^n) | Exponentielle | Ensemble des parties |
| O(n!) | Factorielle | TSP force brute |

**Polynomial** (O(n^k)) = efficace. **Exponentiel** (O(2^n)) = impraticable pour grandes instances.

Pour n = 50 : O(n^2) = 2 500 operations vs O(2^n) ≈ 10^15 operations.

---

# 4. Probleme du Plus Court Chemin (PCC)

## 4.1 Description

**Donnees :** G = (V, E) oriente, source s, destination p, couts cij.
**Objectif :** Trouver un chemin de s a p de cout total minimum.

## 4.2 Formulation lineaire

**Variables :** xij = 1 si l'arc (i,j) est dans le chemin, 0 sinon.

**Contraintes (conservation du flot) :**
```
Somme(xij, j) - Somme(xji, j) = +1  si i = s (source)
                                  -1  si i = p (destination)
                                   0  sinon (transit)
```

**Objectif :** min Somme(cij * xij)

### Proprietes importantes

1. **Circuits negatifs :** Si circuit de cout negatif → objectif = -∞ (non borne).
2. **Relaxation lineaire :** Donne une solution **entiere** automatiquement (matrice d'incidence totalement unimodulaire).

## 4.3 Algorithmes — Quel algorithme choisir ?

| Algorithme | Contexte | Complexite |
|------------|---------|------------|
| **Bellman (par niveaux / topologique)** | DAG (graphe sans circuit) | O(\|V\| + \|E\|) |
| **Dijkstra** | Poids ≥ 0 | O(\|E\| + \|V\| log \|V\|) |
| **Bellman-Ford** | Cas general (poids negatifs OK) | O(\|V\| · \|E\|) |

### REGLE DE CHOIX (EXAMEN) :
1. **Le graphe est un DAG ?** → Algorithme par niveaux (relaxation topologique). Meme avec poids negatifs !
2. **Tous les poids ≥ 0 ?** → Dijkstra
3. **Poids negatifs possibles, pas DAG ?** → Bellman-Ford (detecte aussi les circuits negatifs)

## 4.4 Algorithme de Bellman (Relaxation topologique sur DAG)

**Quand :** Graphe **sans circuit** (DAG).

### Procedure etape par etape :

```
1. Determiner les niveaux du graphe :
   - Niveau 0 : sommets sans predecesseur (sources)
   - Niveau k : sommet dont le plus long chemin depuis une source = k arcs
   Methode : un sommet v est au niveau max(niveau predecesseurs) + 1

2. Initialisation :
   d[s] = 0
   d[v] = +∞ pour tout v ≠ s

3. Traiter les sommets PAR ORDRE DE NIVEAU (croissant) :
   Pour chaque sommet u au niveau courant :
     Pour chaque arc (u, v) sortant de u :
       Si d[u] + c(u,v) < d[v] :
         d[v] = d[u] + c(u,v)
         predecesseur[v] = u

4. Resultat : d[v] = plus courte distance de s a v
```

**Complexite :** O(|V| + |E|) — un seul passage suffit !

### Exemple d'examen (Examen A, Ex.1) :

Graphe G1 avec arc negatif 1→4 : -3.

Niveaux : L0={s}, L1={1,2}, L2={4}, L3={3,5}, L4={6}

| Sommet | s | 1 | 2 | 3 | 4 | 5 | 6 |
|--------|---|---|---|---|---|---|---|
| d(·) | 0 | 2 | 1 | 0 | -1 | 1 | 2 |

Chemin optimal vers 6 : s→1→4→6 (cout = 2 + (-3) + 3 = 2)

Arborescence : pi[1]=s, pi[2]=s, pi[4]=1, pi[3]=4, pi[5]=4, pi[6]=4

## 4.5 Algorithme de Dijkstra

**Quand :** Tous les poids ≥ 0. **JAMAIS** si poids negatifs.

### Procedure etape par etape :

```
1. Initialisation :
   d[s] = 0
   d[v] = +∞ pour tout v ≠ s
   Marques = {} (ensemble des sommets traites)

2. Repeter |V| fois :
   a) Extraire u = sommet NON MARQUE avec d[u] MINIMUM
   b) Marquer u
   c) Pour chaque arc (u, v) avec v non marque :
      Si d[u] + c(u,v) < d[v] :
        d[v] = d[u] + c(u,v)
        predecesseur[v] = u

3. Resultat : d[v] = plus courte distance de s a v
```

**Complexite :** O(|V|^2) version simple, O(|E| + |V| log |V|) avec tas de Fibonacci.

### Exemple d'examen (Examen B, Ex.1.3) :

Graphe G2, tous poids ≥ 0 :

| Etape | s | 1 | 2 | 3 | 4 | 5 | 6 | Extrait |
|-------|---|---|---|---|---|---|---|---------|
| Init | **0** | ∞ | ∞ | ∞ | ∞ | ∞ | ∞ | — |
| 1 | 0 | **1** | 2 | ∞ | ∞ | ∞ | ∞ | s (d=0) |
| 2 | 0 | 1 | **2** | 3 | ∞ | ∞ | ∞ | 1 (d=1) |
| 3 | 0 | 1 | 2 | **3** | 3 | ∞ | ∞ | 2 (d=2) |
| 4 | 0 | 1 | 2 | 3 | **3** | ∞ | 6 | 3 (d=3) |
| 5 | 0 | 1 | 2 | 3 | 3 | **5** | 6 | 4 (d=3) |
| 6 | 0 | 1 | 2 | 3 | 3 | 5 | **6** | 5 (d=5) |

## 4.6 Algorithme de Bellman-Ford

**Quand :** Cas general, poids negatifs possibles. Detecte les circuits negatifs.

### Procedure etape par etape :

```
1. Initialisation :
   d[s] = 0
   d[v] = +∞ pour tout v ≠ s

2. Repeter |V| - 1 fois :
   Pour chaque arc (u, v) dans E :
     Si d[u] + c(u,v) < d[v] :
       d[v] = d[u] + c(u,v)
       predecesseur[v] = u

3. Detection de circuit negatif :
   Pour chaque arc (u, v) dans E :
     Si d[u] + c(u,v) < d[v] :
       → CIRCUIT NEGATIF DETECTE !

4. Si pas de circuit negatif : d[v] = distances optimales
```

**Complexite :** O(|V| · |E|)

---

# 5. Probleme du Flot Maximum

## 5.1 Description

**Donnees :** G = (V, E) oriente, source s, puits t, capacites uij ≥ 0.
**Objectif :** Maximiser le flot envoye de s a t.

## 5.2 Formulation lineaire

**Variables :** xij = valeur du flot sur l'arc (i,j)

**Contraintes :**
```
Conservation du flot :
  Somme(xij, j) - Somme(xji, j) = +v  si i = s
                                    -v  si i = t
                                     0  sinon

Capacites :
  0 ≤ xij ≤ uij  pour tout (i,j) in E
```

**Objectif :** max v

## 5.3 THEOREME FLOT-MAX / COUPE-MIN (FONDAMENTAL)

> **La valeur du flot maximum de s a t = la capacite de la coupe minimum separant s et t.**
>
> max{flot} = min{capacite d'une coupe}

**Coupe (S, T) :** S contient s, T contient t, S ∪ T = V

**Capacite de la coupe :**
```
cap(S, T) = Somme(uij, i in S, j in T, (i,j) in E)
```

**ATTENTION :** On ne compte que les arcs allant de S vers T, PAS ceux de T vers S.

## 5.4 Algorithme de Ford-Fulkerson

### Procedure etape par etape :

```
1. Initialisation : flot = 0 sur tous les arcs

2. Construire le GRAPHE RESIDUEL G_r :
   Pour chaque arc (u,v) avec capacite uij et flot xij :
   - Arc AVANT (u,v) : capacite residuelle = uij - xij (si > 0)
   - Arc ARRIERE (v,u) : capacite residuelle = xij (si > 0)

3. Chercher un CHEMIN AUGMENTANT de s a t dans G_r
   (utiliser BFS pour Edmonds-Karp)

4. Si chemin trouve :
   - delta = min des capacites residuelles sur le chemin
   - Augmenter le flot de delta le long du chemin :
     * Arc avant : xij += delta
     * Arc arriere : xij -= delta
   - Retourner a l'etape 2

5. Si pas de chemin augmentant :
   - FLOT MAXIMUM ATTEINT
   - Les sommets atteignables depuis s dans G_r forment S
   - (S, V\S) est la coupe minimum
```

**Complexite :**
- Ford-Fulkerson : O(|E| · f*) ou f* = flot max
- **Edmonds-Karp (BFS)** : O(|V| · |E|^2) — polynomial garanti

## 5.5 Algorithme Push-Relabel

- Principe : approche locale par preflot
- Peut violer temporairement la conservation du flot
- Complexite : O(|V|^2 · |E|) base, O(|V|^3) amelioree

## 5.6 Verification d'optimalite d'un flot

**Un flot est maximum ⟺ il n'existe pas de chemin augmentant dans le graphe residuel.**

Pour verifier un flot donne a/b (flot courant / capacite) :
1. Calculer le graphe residuel
2. Chercher un chemin de s a t dans le residuel
3. Si aucun chemin → flot maximum confirme

---

# 6. Probleme du Flot de Cout Minimum

## 6.1 Description

**Donnees :** G = (V, E) oriente, bi (offre/demande), lij (cap. inf.), uij (cap. sup.), cij (cout unitaire).
**Objectif :** Satisfaire toutes les demandes au cout minimum.

## 6.2 Formulation lineaire

```
Variables : xij = flot sur l'arc (i,j)

Contraintes :
  Conservation : Somme(xij, j) - Somme(xji, j) = bi  pour tout i
  Capacites :    lij ≤ xij ≤ uij                     pour tout (i,j)

Objectif : min Somme(cij * xij)
```

**Condition de faisabilite :** Somme(bi) = 0

## 6.3 Generalisation

Le flot de cout minimum **generalise** les deux autres problemes :

| Probleme | Comment le formuler en flot cout min |
|----------|-------------------------------------|
| **Plus court chemin** | bs = +1, bt = -1, autres = 0, pas de capacites |
| **Flot maximum** | Ajouter arc retour (t,s) de capacite ∞ et cout -1 |

## 6.4 Algorithmes

1. **Simplexe sur reseau** : variante specialisee, tres efficace
2. **Chemins augmentants** : chemins augmentants de cout negatif
3. **Primal-Dual**
4. **Scaling de capacite** : polynomial fort

---

# 7. Arbres et Arbre Couvrant de Poids Minimum (MST)

## 7.1 Definition et Proprietes d'un arbre

**Definition :** Un arbre A = (V, E) est un graphe **connexe sans cycle**.

### PROPRIETES EQUIVALENTES (THEOREME) — a connaitre

> Les proprietes suivantes sont equivalentes pour un graphe G :
> 1. |E| = |V| - 1 (nombre d'aretes = nombre de sommets - 1)
> 2. Il existe **exactement une chaine** entre tout couple de sommets
> 3. C'est un graphe **connexe minimal** : supprimer n'importe quelle arete le deconnecte
> 4. C'est un graphe **sans cycle maximal** : ajouter n'importe quelle arete cree un cycle

**Terminologie :**
- Feuille : sommet de degre 1
- Noeud interne : sommet qui n'est pas une feuille

## 7.2 Algorithme de Kruskal (MST)

**Objectif :** Trouver un arbre couvrant de poids minimum.

### Procedure etape par etape :

```
1. Trier toutes les aretes par POIDS CROISSANT

2. Pour chaque arete (par ordre croissant) :
   - Si l'arete NE CREE PAS DE CYCLE : l'ajouter a l'arbre
   - Si l'arete CREE UN CYCLE : l'ignorer
   
   Pour verifier le cycle : deux sommets dans la meme composante ?
   (utiliser Union-Find)

3. S'arreter quand |V| - 1 aretes sont selectionnees
```

**Complexite :** O(|E| log |E|)

### Exemple d'examen (Examen A, Ex.2) :

Graphe G2, noeuds {a,b,c,d,e,f,g}.

Aretes triees : a-e(3), a-c(4), c-g(5), e-f(6), f-g(6), c-d(7), a-b(8), b-c(8), b-d(8), ...

| Etape | Arete | Poids | Action |
|-------|-------|-------|--------|
| 1 | a-e | 3 | Ajouter |
| 2 | a-c | 4 | Ajouter |
| 3 | c-g | 5 | Ajouter |
| 4 | e-f | 6 | Ajouter |
| 5 | f-g | 6 | **CYCLE** → ignorer |
| 6 | c-d | 7 | Ajouter |
| 7 | a-b (ou b-c ou b-d) | 8 | Ajouter (3 MST possibles) |

**Cout total MST = 3 + 4 + 5 + 6 + 7 + 8 = 33**

Note : Il peut exister **plusieurs MST de meme cout** si des aretes ont le meme poids.

## 7.3 Algorithme de Prim (MST)

```
1. Choisir un sommet de depart s. Arbre = {s}.

2. Repeter |V| - 1 fois :
   - Parmi les aretes ayant UNE extremite dans l'arbre et l'autre hors de l'arbre :
     choisir celle de POIDS MINIMUM
   - Ajouter cette arete et le nouveau sommet a l'arbre

3. Resultat : arbre couvrant de poids minimum
```

**Complexite :** O(|E| log |V|)

## 7.4 Modele lineaire pour le MST

**Variables :** xe = 1 si l'arete e est choisie, 0 sinon.

```
Contraintes :
  Somme(xe, e in E) = |V| - 1                         (nombre d'aretes)
  Somme(xe, e in delta(W)) ≥ 1  pour tout W ⊂ V, W ≠ ∅  (connexite)

Objectif : min Somme(ce * xe)
```

Le nombre de contraintes de coupe est exponentiel (2^|V| - 2), on les ajoute dynamiquement (generation de coupes).

## 7.5 Puissance d'un noeud dans un arbre (reseaux sans fil)

**Definition :** p(v) = poids maximal des aretes incidentes a v dans l'arbre.

**Puissance totale :** P = Somme(p(v), v in V)

**Attention :** Deux MST de meme cout peuvent avoir des puissances totales differentes !

---

# 8. Connexite et Robustesse des Reseaux

## 8.1 Problemes de connexite

| Concept | Definition |
|---------|-----------|
| **Point d'articulation** | Sommet v dont la suppression augmente le nombre de composantes connexes |
| **Ensemble d'articulation** | Ensemble W ⊂ V dont la suppression augmente le nombre de composantes |
| **k-arete-connexe** | Pour chaque paire s,t, il existe k chaines aretes-disjointes |
| **k-(sommet-)connexe** | Pour chaque paire s,t, il existe k chaines sommets-disjointes |

## 8.2 Reseau k-connexe

**Definition :** Un reseau k-connexe resiste a la destruction d'au plus **k-1 cables**.

| Niveau | Signification |
|--------|--------------|
| 1-connexe | Connexe simple (arbre suffisant) |
| 2-connexe | Resiste a la panne d'1 cable |
| k-connexe | Resiste a la panne de k-1 cables |

**Verification 2-connexe :**
- Pas de point d'articulation
- Pour toute paire, 2 chemins sommet-disjoints
- Supprimer n'importe quel sommet → reste connexe

## 8.3 THEOREME DE MENGER (1927)

### Version arcs (reseaux)

> Soit R = (V, E, c) un reseau avec capacites unitaires, source s, puits t.
>
> **(a)** La valeur du flot maximum = nombre maximum de st-chemins **arcs-disjoints**.
>
> **(b)** La capacite de la coupe minimum = nombre minimum d'arcs dont la suppression detruit tous les st-chemins.

### Version graphes non orientes

> Soit G = (V, E) avec deux sommets non adjacents s et t, et k un entier.
>
> Il existe k chaines de s a t **sommets-disjointes** (resp. aretes-disjointes)
> ⟺
> t reste connecte a s apres suppression de k-1 sommets (resp. aretes) quelconques.

**En resume :** nb max chemins disjoints = nb min aretes/sommets a supprimer pour separer s de t.

## 8.4 THEOREME DE WHITNEY (1932)

> Un graphe non oriente G (≥ 2 sommets) est **k-arete-connexe** ⟺ pour chaque paire s, t, il existe k chaines aretes-disjointes.
>
> Un graphe non oriente G (≥ k+1 sommets) est **k-(sommet-)connexe** ⟺ pour chaque paire s, t, il existe k chaines sommets-disjointes.

## 8.5 Types de noeuds selon les requetes

Chaque noeud j a une requete rj ∈ {0, 1, 2} :

| Type | Requete | Signification |
|------|---------|--------------|
| **Special** | ri = 2 | Haut degre de surete (hopitaux, urgences). 2 chemins disjoints requis |
| **Ordinaire** | ri = 1 | Simplement connecte |
| **Optionnel** | ri = 0 | Peut ne pas etre dans le reseau final |

Entre noeuds i et j : il faut min(ri, rj) chaines disjointes.

## 8.6 Cas particuliers et complexite

| Valeurs de ri | Probleme | Complexite |
|---------------|----------|------------|
| ri = 1 ∀i | **Arbre couvrant (MST)** | **Polynomial** O(\|E\| log \|V\|) |
| rs = rt = 1, autres = 0 | **Plus court chemin** s-t | **Polynomial** |
| rs = rt = k (k≥2), autres = 0 | **k plus courts chemins disjoints** | **Polynomial** |
| ri ∈ {0, 1} | **Arbre de Steiner** | **NP-difficile** |
| ri = 2 ∀i | **Reseau 2-connexe de cout min** | **NP-difficile** |

## 8.7 Modele lineaire pour reseau 2-connexe

```
Variables : xe ∈ {0, 1}

Contraintes :
  Somme(xe, e in delta(W)) ≥ 2  pour tout W ⊂ V, W ≠ ∅, W ≠ V

Objectif : min Somme(ce * xe)
```

**Difference avec MST :** Le membre de droite est **≥ 2** au lieu de ≥ 1.

## 8.8 Methode de resolution : Branch & Cut

```
Etape 1 : Relaxation
  - Relaxer xe ∈ {0,1} en xe ∈ [0,1]
  - Resoudre le programme lineaire

Etape 2 : Generation de coupes
  - Si solution non entiere, chercher contraintes violees
  - Ajouter dynamiquement les contraintes de coupe
  - Algorithme de separation : recherche de coupe minimale

Etape 3 : Branchement
  - Si toujours non entier, brancher (Branch & Bound)
```

## 8.9 Exemple : Reseau Belgacom

52 centres connectes par un cycle hamiltonien → panne = long detour.
Solution : topologie 2-aretes-connexe avec cycles bornes (longueur 3 a 6).
Avantage : reroutage local dans un petit cycle.

---

# 9. Algorithmes de Diffusion dans les Reseaux Sans Fil

## 9.1 Problematique

**Propriete des reseaux sans fil :** Quand un noeud emet, **tous ses voisins recoivent** simultanement.

**Broadcast Storm (Tempete de diffusion) :** Si tous les noeuds retransmettent :
- Collisions, congestion, gaspillage d'energie
- Redondance ~80% dans un reseau de 100 noeuds, degre moyen 10

**Probleme du Broadcast Minimum :**
- Donnees : G = (V, E), source s
- Objectif : minimiser le nombre de relais R ⊆ V\{s} tout en couvrant tous les noeuds
- **NP-difficile** en general → on utilise des heuristiques

## 9.2 Flooding (Inondation) Simple

```
1. s envoie le message a tous ses voisins N(s)
2. Pour chaque noeud u ≠ s :
   - Premiere reception : marquer + retransmettre a tous les voisins
   - Receptions suivantes : ignorer
```

| Critere | Valeur |
|---------|--------|
| Couverture garantie | **OUI** |
| Nb transmissions | O(\|E\|) — tres eleve |
| Info requise | Aucune |
| Delai | O(diametre(G)) |

**Flooding avec TTL :** Ajouter compteur decroissant. TTL = k → seuls les noeuds a distance ≤ k recoivent.

## 9.3 Diffusion Probabiliste

```
1. s envoie le message
2. Chaque noeud recevant pour la 1ere fois :
   - Avec probabilite p : retransmettre
   - Avec probabilite 1-p : ne pas retransmettre
```

Nb moyen de retransmissions : p · (|V| - 1)

| p | Couverture | Retransmissions |
|---|-----------|----------------|
| 1.0 (flooding) | 100% | 400 |
| 0.8 | 99% | 320 |
| 0.6 | 94% | 240 |
| 0.4 | 75% | 160 |
| 0.2 | 42% | 80 |

**Regle empirique :** p ≈ 0.6 dans les reseaux denses.

**Couverture garantie : NON**

## 9.4 Schema Base sur le Compteur (Counter-Based)

```
1. s envoie le message
2. Pour chaque noeud u :
   - Initialiser count(u) = 0
   - A chaque reception : count(u) += 1
   - Apres un delai tau :
     * Si count(u) ≤ C_threshold : retransmettre
     * Sinon : ne pas retransmettre
```

Compteur faible = peu de voisins ont retransmis = u doit retransmettre.

Le delai tau permet d'observer combien de voisins ont deja retransmis avant de decider.

**Couverture garantie : NON**

## 9.5 Schema Base sur la Distance

```
1. s envoie le message avec sa position GPS
2. Noeud u recoit de v :
   - Calculer d(u, v) = distance geographique
   - Si d(u, v) ≥ d_threshold : retransmettre
   - Sinon : ne pas retransmettre
```

**Variante avec delai :** tau(u) = (d_max - d(u,v)) / d_max * tau_max
→ Les noeuds les plus eloignes retransmettent EN PREMIER (plus grande zone de couverture).

**Couverture garantie : NON.** Necessite positions GPS.

## 9.6 Dominant Connexe (Connected Dominating Set — CDS)

### Definitions

**Dominant :** D ⊆ V tel que tout u ∈ V\D a un voisin dans D.

**Dominant connexe :** D est dominant ET le sous-graphe induit G[D] est connexe.

→ D forme un **backbone** : seuls les noeuds de D retransmettent.

### Algorithme de diffusion via CDS

```
1. Construire le dominant connexe D
2. s envoie le message
3. Tout noeud u ∈ D qui recoit : retransmettre (une seule fois)
4. Tout noeud u ∉ D : ne pas retransmettre
```

**Couverture garantie : OUI** (D connexe ⟹ message atteint tout D ; D dominant ⟹ tout V\D a un voisin dans D)

### Construction gloutonne d'un CDS

```
1. Initialisation :
   - Choisir v* = sommet de DEGRE MAXIMUM
   - D = {v*}
   - Domine = N[v*] (voisinage ferme = v* + ses voisins)

2. Tant que Domine ≠ V :
   - Parmi les VOISINS de D (non dans D), choisir u qui maximise
     |N(u) \ Domine| (nombre de nouveaux noeuds domines)
   - D = D ∪ {u}
   - Domine = Domine ∪ N[u]
   
   IMPORTANT : choisir un VOISIN de D assure la connexite !

3. Resultat : D est un dominant connexe
```

**CDS minimum : NP-difficile.** Approximation gloutonne : O(ln Delta) fois le minimum.

### Exemple (Examen B, Ex.4 : stations radio)

- Graphe G5 : villages et distances
- G5^1 : garder aretes de poids ≤ 50 km
- Appliquer heuristique gloutonne pour ensemble dominant minimum
- Si rayon etendu a 70 km : plus d'aretes → moins de stations necessaires

## 9.7 MPR : Multipoint Relaying (protocole OLSR)

### Principe

Chaque noeud u selectionne un sous-ensemble MPR(u) ⊆ N(u) tel que **tous les voisins a 2 sauts** de u soient couverts.

**Voisinage a 2 sauts :**
```
N2(u) = {w ∈ V : w ∉ N[u] et ∃ v ∈ N(u), (v,w) ∈ E}
```

### Algorithme de selection des MPR

```
1. MPR(u) = ∅, Couvert = ∅

2. Etape obligatoire :
   Pour tout w ∈ N2(u) qui n'a qu'UN SEUL voisin v dans N(u) :
   → Ajouter v a MPR(u) (obligatoire, pas de choix)
   Mettre a jour Couvert

3. Tant que Couvert ≠ N2(u) :
   - Choisir v ∈ N(u) \ MPR(u) maximisant
     |N(v) ∩ (N2(u) \ Couvert)|
   - MPR(u) = MPR(u) ∪ {v}
   - Couvert = Couvert ∪ (N(v) ∩ N2(u))
```

**Complexite :** O(|N(u)| · |N2(u)|) — entierement local.

### Diffusion via MPR

```
Regle : u retransmet un message recu de v ⟺ u ∈ MPR(v)
```

**Couverture garantie : OUI.** Nombre de retransmissions < flooding.

Requiert la connaissance du voisinage a 2 sauts.

## 9.8 Tableau comparatif des algorithmes de diffusion

| Algorithme | Couverture garantie | Retransmissions | Info requise | Complexite/noeud |
|------------|:------------------:|----------------|-------------|-----------------|
| Flooding | **OUI** | O(\|E\|) | Aucune | O(1) |
| Probabiliste | NON | p · \|V\| | Aucune | O(1) |
| Compteur | NON | Variable | Aucune | O(1) |
| Distance | NON | Variable | Positions GPS | O(\|N(u)\|) |
| CDS | **OUI** | \|D\| | Voisinage 1 saut | O(\|V\| · \|E\|) |
| MPR (OLSR) | **OUI** | Somme \|MPR(u)\| | Voisinage 2 sauts | O(\|N(u)\| · \|N2(u)\|) |

### Regle de choix pratique :
- **Couverture garantie + reseau dense** → MPR (OLSR)
- **Reseau clairseme + simple** → Flooding
- **Couverture non critique + economie d'energie** → Probabiliste (p ≈ 0.6)
- **Infrastructure stable** → CDS pre-calcule

---

# 10. Methodes d'Examen : Procedures Detaillees

## 10.1 Algorithme BIP (Broadcast Incremental Power)

**Contexte :** Construire un arbre couvrant dans un reseau sans fil en minimisant la puissance totale.

**Principe :** A chaque etape, ajouter l'arete qui minimise le **cout additionnel** (puissance supplementaire necessaire).

### Procedure etape par etape :

```
1. Choisir un noeud source s. Arbre = {s}. p(s) = 0.

2. A chaque etape, pour chaque arete (u,v) avec u dans l'arbre et v hors de l'arbre :
   Calculer le COUT ADDITIONNEL :
     cout_add(u,v) = max(C(u,v) - p(u), 0)
   
   C(u,v) = poids de l'arete
   p(u) = puissance actuelle du noeud u
   
   Si C(u,v) ≤ p(u) : cout additionnel = 0 (pas de puissance supplementaire !)

3. Choisir l'arete avec COUT ADDITIONNEL MINIMUM.

4. Mettre a jour :
   p(u) = max(p(u), C(u,v))
   p(v) = C(u,v)  (nouveau noeud, sa puissance = poids de l'arete qui l'ajoute)
   
   Ajouter v a l'arbre.

5. Repeter jusqu'a ce que tous les noeuds soient dans l'arbre.
```

### Exemple d'examen (Examen A, Ex.2, depuis c) :

| Etape | Arete | Cout add. | p() mis a jour |
|-------|-------|-----------|----------------|
| 1 | c-a (4) | 4-0 = 4 | p(c)=4, p(a)=4 |
| 2 | a-e (3) | max(3-4,0) = **0** | p(a) inchange, p(e)=3 |
| 3 | c-g (5) | 5-4 = 1 | p(c)=5, p(g)=5 |
| 4 | c-d (7) | 7-5 = 2 | p(c)=7, p(d)=7 |
| 5 | b-d (8) | 8-7 = 1 | p(d)=8, p(b)=8 |
| 6 | e-f (6) | 6-3 = 3 | p(e)=6, p(f)=6 |

Puissance totale BIP = 4 + 8 + 7 + 8 + 6 + 6 + 5 = **44** (vs MST T1 = 47)

**BIP ne garantit pas l'optimalite globale, mais donne souvent une meilleure puissance totale que le MST.**

## 10.2 BIP Adapte (avec cout a(u) + a(v))

**Variante :** Le cout a minimiser est a(u) + a(v) ou :
- a(u) = max(C(u,v) - p(u), 0) : cout additionnel pour u
- a(v) = C(u,v) : cout pour le nouveau noeud v (puisque p(v) = 0 initialement)

Voir Examen B, Ex.2 pour exemple complet.

## 10.3 Chemins arcs-disjoints via Flot Maximum

**Probleme :** Trouver le nombre maximum de st-chemins arcs-disjoints.

### Procedure :

```
1. Affecter la CAPACITE 1 a chaque arc
2. Resoudre le probleme de FLOT MAXIMUM de s a t
3. Le flot max = nombre max de chemins arcs-disjoints (Menger)
4. Pour identifier les chemins : decomposer le flot en chemins
5. La coupe minimum confirme l'optimalite (Flot-Max = Coupe-Min)
```

### Justification :
Flot entier avec capacites unitaires → chaque arc utilise 0 ou 1 fois → decomposition en chemins arcs-disjoints.

### Exemple (Examen A, Ex.3) :

3 chemins arcs-disjoints :
- P1 : s → a → i → t
- P2 : s → b → f → h → t
- P3 : s → c → f → g → t (ERREUR si f est partage — verifier les arcs, pas les noeuds !)

Coupe minimum : {(i,t), (h,t), (g,t)}, capacite = 3. Confirme flot max = 3.

## 10.4 Chemins sommets-disjoints : Dedoublement de noeud

**Probleme :** Trouver des chemins **sommets-disjoints** (pas seulement arcs).

### Technique de dedoublement :

```
Pour chaque noeud intermediaire v (v ≠ s, v ≠ t) :
  Remplacer v par deux noeuds v_in et v_out
  Ajouter arc (v_in, v_out) de capacite 1
  Tous les arcs ENTRANT en v → arrivent a v_in
  Tous les arcs SORTANT de v → partent de v_out

Puis resoudre le flot maximum dans le nouveau graphe.
```

## 10.5 Capacite nodale (contrainte sur un sommet)

**Probleme :** Le sommet v ne peut transmettre qu'un flot ≤ k (capacite nodale).

### Technique :

```
Remplacer v par v_in et v_out
Arc (v_in, v_out) de capacite k
Tous les arcs entrant en v → v_in
Tous les arcs sortant de v → v_out
```

Si le flot optimal dans le graphe original passait ≤ k par v → flot max inchange.
Si > k → recalculer avec la nouvelle contrainte.

## 10.6 Determination des niveaux d'un DAG

### Procedure pour montrer qu'un graphe est sans circuit :

```
1. Trouver les sommets sans predecesseur → Niveau 0 (sources)
2. Pour chaque sommet restant :
   Niveau(v) = max(Niveau(u) + 1) pour tous les predecesseurs u de v
3. Si TOUS les sommets ont un niveau :
   → Graphe SANS CIRCUIT (DAG) ✓
   → Tous les arcs vont d'un niveau inferieur vers un niveau superieur
4. Si certains sommets n'ont pas de niveau :
   → Graphe avec circuit
```

## 10.7 Nombre de chemins dans un DAG complet

**Resultat (Examen B) :** Dans un graphe G = (V, E) avec V = {v1, ..., vn} et E = {(vi, vj) : i < j}, le nombre de chemins elementaires de v1 a vn est :

```
2^(n-2)
```

(Chaque sous-ensemble de {v2, ..., v_{n-1}} donne un chemin unique en ordre croissant.)

## 10.8 Heuristique Gloutonne pour Ensemble Dominant

### Procedure :

```
1. D = ∅, Non_couverts = V

2. Tant que Non_couverts ≠ ∅ :
   - Choisir le sommet v qui COUVRE LE PLUS de sommets non couverts
     (v lui-meme + ses voisins dans Non_couverts)
   - D = D ∪ {v}
   - Non_couverts = Non_couverts \ N[v]

3. D est un ensemble dominant (pas forcement minimum)
```

**NP-difficile.** Approximation : O(ln Delta) fois le minimum.

---

# 11. Tableau Recapitulatif : Quel Algorithme Utiliser ?

## Plus Court Chemin

| Situation | Algorithme | Complexite |
|-----------|-----------|------------|
| DAG (sans circuit), meme avec poids negatifs | **Relaxation topologique (Bellman)** | O(V + E) |
| Poids tous ≥ 0 | **Dijkstra** | O(E + V log V) |
| Poids negatifs, graphe general | **Bellman-Ford** | O(V · E) |
| Detection circuit negatif | **Bellman-Ford** (iteration supplementaire) | O(V · E) |

## Flot Maximum

| Situation | Algorithme | Complexite |
|-----------|-----------|------------|
| General | **Ford-Fulkerson** | O(E · f*) |
| Polynomial garanti | **Edmonds-Karp (BFS)** | O(V · E^2) |
| Efficace en pratique | **Push-Relabel** | O(V^2 · E) |

## Arbre Couvrant

| Situation | Algorithme | Complexite |
|-----------|-----------|------------|
| MST classique | **Kruskal** (trier + Union-Find) | O(E log E) |
| MST classique | **Prim** (tas + extraction min) | O(E log V) |
| Min puissance sans fil | **BIP** | — |

## Connexite / Chemins disjoints

| Situation | Methode |
|-----------|---------|
| Max chemins arcs-disjoints | Flot max avec capacites unitaires |
| Max chemins sommets-disjoints | Dedoublement de noeuds + flot max |
| Capacite nodale | Dedoublement du noeud concerne |
| k-connexite | Theoreme de Menger / Whitney |

## Diffusion sans fil

| Situation | Algorithme |
|-----------|-----------|
| Simple, couverture garantie | Flooding |
| Reduction transmissions, pas critique | Probabiliste (p ≈ 0.6) |
| Reseau dense, couverture garantie | MPR (OLSR) |
| Infrastructure stable | CDS pre-calcule |

---

# 12. Formules et Resultats a Connaitre par Coeur

## Formules fondamentales

| Formule | Contexte |
|---------|---------|
| Somme(d(v)) = 2\|E\| | Somme des degres = 2 × nb aretes |
| \|E\| = \|V\| - 1 | Propriete d'un arbre |
| \|E\|(Kn) = n(n-1)/2 | Aretes dans un graphe complet |
| Nb chemins v1→vn dans DAG complet = 2^(n-2) | Denombrement par sous-ensembles |
| Somme(bi) = 0 | Conservation du flot |

## Theoremes

| Theoreme | Enonce |
|----------|--------|
| **Flot-Max / Coupe-Min** | max{flot} = min{capacite coupe} |
| **Menger (1927)** | Nb max chemins disjoints = nb min aretes/sommets a supprimer |
| **Whitney (1932)** | G est k-connexe ⟺ k chaines disjointes entre toute paire |
| **Biparti** | G biparti ⟺ pas de cycle impair |
| **Arbre** | Connexe sans cycle ⟺ \|E\|=\|V\|-1 ⟺ unique chaine ⟺ connexe minimal ⟺ acyclique maximal |
| **Unimodularite** | Matrice incidence orientee → relaxation PCC donne solution entiere |

## Problemes NP-difficiles

| Probleme | Statut |
|----------|--------|
| Arbre de Steiner | NP-difficile |
| Reseau 2-connexe de cout minimum | NP-difficile |
| CDS minimum (dominant connexe) | NP-difficile |
| Broadcast minimum (nb relais min) | NP-difficile |

## Problemes polynomiaux

| Probleme | Complexite |
|----------|------------|
| MST (Kruskal/Prim) | O(E log V) |
| PCC (Dijkstra) | O(E + V log V) |
| PCC sur DAG | O(V + E) |
| Flot max (Edmonds-Karp) | O(V · E^2) |
| k chemins disjoints s-t | O(k · E log V) |

---

## CONSEILS D'EXAMEN

1. **Toujours justifier le choix de l'algorithme** : DAG ? poids negatifs ? poids ≥ 0 ?
2. **Montrer le tableau d'execution** pour Dijkstra (colonne par sommet, ligne par etape)
3. **Pour Kruskal** : trier les aretes, noter chaque etape (ajout/cycle)
4. **Pour BIP** : bien calculer le cout ADDITIONNEL = max(C(u,v) - p(u), 0)
5. **Pour les chemins disjoints** : capacites unitaires + flot max
6. **Pour la coupe min** : verifier que capacite coupe = flot max (confirmation)
7. **Pour un DAG** : montrer les niveaux pour prouver l'absence de circuit
8. **Puissance d'un noeud** = max des poids des aretes incidentes dans l'arbre
9. **Dedoublement de noeud** pour capacite nodale ou chemins sommet-disjoints
10. **CDS / Dominant** : toujours choisir le sommet couvrant le plus de non-couverts

---

> **Bon courage pour l'examen !**
> Cours d'Optimisation dans les Reseaux — F. Bendali-Mailfert — S6 2025
