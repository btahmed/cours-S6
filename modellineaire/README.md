# Modeles Lineaires - Cours S6

**Cours : Modeles Lineaires de la Recherche Operationnelle**  
**Universite Clermont Auvergne -- Clermont Auvergne INP -- ISIMA**  
**Professeur : Herve KERIVIN**  
**Annee : 2024-2025**

---

## Contenu

Ce dossier contient les fichiers source du cours (.tex/.pdf) ainsi que **5 fichiers markdown** qui reprennent chaque chapitre avec :
- Un **resume** des concepts cles
- Une **explication detaillee** de la theorie
- Des **exercices corriges** (tires des corrections du cours)
- Un **quiz** de 10 questions avec reponses cachees

---

## Chapitres

| # | Fichier Markdown | Source .tex | Contenu |
|---|-----------------|-------------|---------|
| 1 | [Chapitre1_Introduction_PL.md](Chapitre1_Introduction_PL.md) | `modelelineaire.tex` | Formulation de PL, forme standard, methode graphique, 6 exercices + 10 quiz + 2 exercices d'entrainement |
| 2 | [Chapitre2_Methode_Simplexe.md](Chapitre2_Methode_Simplexe.md) | `cours2_methode_simplexe.tex` | Dictionnaires, pivots, critere d'optimalite, detection non-borne et solutions multiples (avec exemples complets), 3 exercices + 10 quiz + 1 exercice d'entrainement |
| 3 | [Chapitre3_Degenerescence_Deux_Phases.md](Chapitre3_Degenerescence_Deux_Phases.md) | `CM3ML_cours.tex` | Degenerescence, cyclage, regle de Bland, methode des deux phases, methode du grand-M, exemples de Klee-Minty, 3 exercices + 10 quiz |
| 4 | [Chapitre4_Dualite.md](Chapitre4_Dualite.md) | `dualite cours.tex` | Dual d'un PL, dualite faible/forte, ecarts complementaires, interpretation economique, 5 exercices + 10 quiz |
| 5 | [Chapitre5_Simplexe_Revise.md](Chapitre5_Simplexe_Revise.md) | `simplex_revise.tex` | Formulation matricielle, prix marginaux, couts reduits, analyse de sensibilite, 4 exercices + 10 quiz |

---

## Fichiers de corrections

| Fichier | Couvre |
|---------|--------|
| `correction.tex` / `.pdf` | Exercices des chapitres 1-2 (formulation, methode graphique, simplexe) |
| `dualite_corrections.tex` / `.pdf` | Exercices du chapitre 4 (dualite) |
| `simplex_revise_corrections.tex` / `.pdf` | Exercices du chapitre 5 (simplexe revise) |

---

## Notes importantes

- **Convention de signe dans les dictionnaires (Chapitre 2)** : Dans le dictionnaire `xi = bi - aie*xe`, le coefficient `aie` est le coefficient reel (positif). Le signe `-` fait partie de la structure du dictionnaire, pas du coefficient. C'est un piege classique pour le ratio test.

- **Ordre de lecture recommande** : Chapitres 1 -> 2 -> 3 -> 4 -> 5 (ils sont construits progressivement).

- Les fichiers markdown contiennent des balises `<details>` pour cacher les reponses du quiz (fonctionnent sur GitHub et dans la plupart des lecteurs Markdown).

---

## Comment utiliser

1. Lire chaque chapitre markdown dans l'ordre
2. Comprendre le resume, puis l'explication detaillee
3. Faire les exercices AVANT de regarder les corrections
4. Tester ses connaissances avec le quiz
5. En cas de doute, consulter les fichiers .pdf du cours original
