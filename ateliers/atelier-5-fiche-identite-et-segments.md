# Atelier 5 — Fiche d'identité d'une variable et comparaison de segments

**Jour 2 · 13 h 30 – 14 h 30 · 60 minutes. Individuel ou en binôme. Tableur.**
**Module de rattachement : module 6.**
**Fichiers : `transactions-ecommerce.csv` et `mini-frais-port-11.csv`.**

---

## Contexte

Vous disposez d'un extrait réel de 3 000 commandes livrées d'une place de marché brésilienne, années 2017 et 2018. Les montants sont en réal brésilien (BRL). C'est le même périmètre que la base SQL d'hier : les 3 000 commandes sont exactement les mêmes.

Le cours a traité la variable `montant_total_brl`. **Cet atelier porte sur d'autres variables**, que vous analysez vous-mêmes en appliquant la même méthode. Ne recopiez pas les chiffres du support : ils ne répondent pas aux questions posées ici.

### Les colonnes

| Colonne | Signification |
|---|---|
| `id_commande` | identifiant court de la commande |
| `date_achat` | date de l'achat |
| `etat_client` | état brésilien du client (SP, RJ, MG...) |
| `categorie` | catégorie du produit le plus cher de la commande, en portugais |
| `categorie_en` | la même catégorie en anglais |
| `nb_articles` | nombre d'articles dans la commande |
| `prix_articles_brl` | somme du prix des articles, hors livraison |
| `frais_port_brl` | frais de port totaux |
| `montant_total_brl` | `prix_articles_brl` + `frais_port_brl` |
| `delai_livraison_j` | jours entre l'achat et la livraison |

---

## Partie A — À la main, sans ordinateur (10 minutes)

Le fichier `mini-frais-port-11.csv` contient les frais de port de 11 commandes, déjà triés :

```
8,53   12,79   13,71   14,10   14,39   14,85   16,05   24,58   38,12   58,92   73,63
```

Papier et calculatrice autorisés, tableur interdit.

**A.1** Calculez la **somme** et la **moyenne**. Arrondissez à deux décimales.

**A.2** Déterminez la **médiane**. Écrivez le raisonnement qui vous permet de désigner la bonne valeur, pas seulement le résultat.

**A.3** Déterminez le **mode**. Si vous rencontrez une difficulté, décrivez-la : c'est la réponse attendue.

**A.4** Déterminez **Q1 et Q3** sur cette série, puis l'**IQR**, puis le **seuil haut de Tukey**. Y a-t-il des valeurs au-delà ?

**A.5** **Dessinez la boîte à moustaches** de cette série, à main levée, en respectant les proportions. Placez et étiquetez : minimum, Q1, médiane, Q3, moustache haute, et les points isolés s'il y en a.

**A.6** En une phrase : comparez moyenne et médiane, et dites laquelle décrit le mieux le frais de port d'une commande ordinaire.

---

## Partie B — Deux fiches d'identité (20 minutes)

Ouvrez `transactions-ecommerce.csv` dans votre tableur.

Dans une nouvelle feuille nommée `fiche`, construisez la fiche d'identité complète des **deux** variables suivantes :

- `frais_port_brl`
- `delai_livraison_j`

Une colonne de résultats par variable, les lignes suivantes :

| # | Indicateur |
|---|---|
| 1 | Effectif et nombre de valeurs manquantes |
| 2 | Minimum et maximum |
| 3 | Moyenne |
| 4 | Médiane |
| 5 | Variance (convention échantillon) |
| 6 | Écart-type (convention échantillon) |
| 7 | Coefficient de variation, en pourcentage |
| 8 | Q1, Q3, et intervalle interquartile |
| 9 | Seuil bas et seuil haut de Tukey |
| 10 | Nombre et pourcentage de valeurs au-delà du seuil haut |
| 11 | Pourcentage de valeurs situées **sous la moyenne** |
| 12 | Part du total portée par les valeurs au-delà du seuil haut |

### Contraintes de réalisation

- **Aucune valeur saisie à la main.** Chaque cellule de résultat contient une formule qui pointe vers les données. Un correcteur doit pouvoir cliquer sur la cellule et voir la formule.
- Utilisez la **convention échantillon** : `VAR` et `ECARTTYPE` (ou `VAR.S` et `ECARTTYPE.STANDARD`), jamais les variantes en `.P`.
- Les seuils de Tukey sont calculés **par formule** à partir de Q1, Q3 et de l'IQR, jamais recopiés.
- Arrondissez l'affichage à deux décimales, sans arrondir les calculs intermédiaires.
- Indiquez l'unité de chaque ligne (BRL, jours, ou pourcentage).

### Point de vigilance annoncé

L'une des deux variables contient au moins une valeur égale à **zéro**. Quand vous la rencontrerez, ne la corrigez pas et ne la supprimez pas : notez-la, et traitez-la à la question D.2.

---

## Partie C — Comparer trois segments (18 minutes)

La comparaison de segments est le calcul le plus utile de la vie professionnelle. Vous en faites trois.

**C.1 — São Paulo contre le reste.** Pour les commandes dont `etat_client` vaut `SP` d'une part, et toutes les autres d'autre part, calculez : l'effectif, la moyenne et la médiane de `montant_total_brl`, la moyenne et la médiane de `frais_port_brl`.

**C.2 — Un article contre plusieurs.** Mêmes calculs sur `montant_total_brl`, en séparant `nb_articles = 1` de `nb_articles >= 2`.

**C.3 — Livraison rapide contre livraison lente.** Séparez les commandes livrées en **10 jours ou moins** de celles livrées en **plus de 10 jours**. Comparez l'effectif, la moyenne et la médiane de `frais_port_brl`, et la moyenne de `montant_total_brl`.

Un tableau croisé dynamique est autorisé et recommandé.

### C.4 — Interprétation

Rédigez **trois paragraphes de quatre phrases maximum chacun**, un par comparaison. Chaque paragraphe doit :

- énoncer l'écart constaté **avec ses chiffres** ;
- proposer une explication plausible du mécanisme en jeu ;
- énoncer explicitement ce que vos chiffres **ne permettent pas** de conclure ;
- indiquer quelle **donnée supplémentaire** vous réclameriez pour trancher.

> **Si vous avez fait l'atelier SQL d'hier**, vous avez déjà un élément de réponse pour la comparaison C.1. Utilisez-le et citez-le : c'est exactement ce qu'on attend d'un analyste, faire parler ensemble deux sources qu'il a produites à deux moments différents.

---

## Partie D — Décider (12 minutes)

**D.1 — Le sort des valeurs extrêmes.** Pour `frais_port_brl` :

1. Recalculez la moyenne et la médiane **sans** les valeurs au-delà du seuil haut de Tukey, et indiquez de combien chacune se déplace, en valeur et en pourcentage.
2. Laquelle des deux se déplace le plus ? Était-ce prévisible ?
3. Décidez : les excluez-vous, oui ou non ? Justifiez en trois lignes, en précisant à laquelle des trois situations vues en cours — erreur de saisie, réalité hors sujet, réalité centrale — vous rattachez ces valeurs.

**D.2 — Le cas des zéros.** Traitez la ou les valeurs nulles repérées en partie B.

1. Combien y en a-t-il ?
2. Que représentent-elles, selon vous, dans la réalité commerciale de cette place de marché ?
3. La règle de Tukey les a-t-elle signalées ? Pourquoi ?
4. En une ligne : est-ce un problème, et pourquoi ?

**D.3 — La note de synthèse.** Rédigez un paragraphe de **six phrases maximum**, adressé au directeur de la logistique, qui répond à cette question unique :

> Faut-il piloter les frais de port sur leur moyenne ou sur leur médiane, et pourquoi ?

Le paragraphe doit contenir **au moins trois chiffres** issus de vos calculs et se terminer par une **recommandation d'action**.

Conservez ce paragraphe : il vous servira de base pour la restitution orale de fin de journée.

---

## Livrables

1. **`atelier5-calculs.xlsx`** (ou `.ods`) : le classeur avec la feuille `fiche`, les tableaux de la partie C et les calculs de la partie D. Formules présentes et lisibles.
2. **`atelier5-partieA.md`** : les six réponses de la partie A, avec le raisonnement de la médiane rédigé et la boîte à moustaches photographiée ou décrite.
3. **`atelier5-interpretation.md`** : les trois paragraphes de C.4, les réponses de D.1 et D.2, et la note de synthèse D.3.
4. **`USAGE-IA.md`** : selon le modèle en annexe.


## Annexe 1 — Aide-mémoire des fonctions de tableur

| Besoin | Fonction |
|---|---|
| Effectif de valeurs numériques | `NB` |
| Effectif de cellules non vides | `NBVAL` |
| Somme | `SOMME` |
| Moyenne | `MOYENNE` |
| Médiane | `MEDIANE` |
| Mode | `MODE` |
| Variance d'échantillon | `VAR` ou `VAR.S` |
| Écart-type d'échantillon | `ECARTTYPE` ou `ECARTTYPE.STANDARD` |
| Variance de population | `VAR.P` |
| Écart-type de population | `ECARTTYPE.PEARSON` |
| Quartile | `QUARTILE` (second argument : 1 pour Q1, 3 pour Q3) |
| Minimum, maximum | `MIN`, `MAX` |
| Compter sous condition | `NB.SI` |
| Sommer sous condition | `SOMME.SI` |
| Moyenne sous condition | `MOYENNE.SI` |

Pour plusieurs critères, cherchez les variantes en `.ENS`. Pour la médiane d'un sous-ensemble, la fonction `MEDIANE` n'a pas de variante conditionnelle simple : un tableau croisé dynamique ou une colonne auxiliaire vous rendront service.

---

## Annexe 2 — Fichier `USAGE-IA.md` (obligatoire)

```markdown
# Usage de l'IA — Atelier 5
Nom(s) :

## Avons-nous utilisé une IA ?
[oui / non]

## Si oui, pour chaque usage
### Usage 1
- Outil utilisé :
- Quand, à quel moment de l'atelier :
- Pourquoi : ce que nous cherchions à obtenir
- Ce que l'IA a répondu, en résumé :
- Ce que nous avons gardé, modifié, ou rejeté, et pour quelle raison :

## Réponses que nous avons refusées
- Réponse refusée :
- Motif du refus :

## Corrections que nous avons dû apporter
- Erreur ou approximation détectée dans une réponse de l'IA :
- Correction apportée :
```

**Avertissement.** Si vous demandez à une IA de calculer des statistiques à partir d'un fichier, vérifiez ses résultats dans le tableur. Les écarts entre une valeur annoncée par un modèle et la valeur réelle du fichier sont fréquents, particulièrement sur les quartiles et les comptages conditionnels. **Toute valeur non reproductible par une formule de votre classeur sera comptée comme fausse.**
