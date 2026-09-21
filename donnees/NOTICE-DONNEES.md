# Notice de provenance des données

Cette notice documente l'origine exacte, les transformations appliquées et les limites des quatre artefacts de données utilisés pendant les deux journées. Elle est destinée au formateur et peut être distribuée aux étudiants : elle constitue en elle-même un exemple de documentation de jeu de données.

Date de constitution : septembre 2026.

---

## 1. Les quatre artefacts, et pourquoi ils sont cohérents entre eux

| Fichier | Forme | Utilisé par |
|---|---|---|
| `olist-pedagogique.db` | base SQLite, 8 tables normalisées | modules 2 à 5, ateliers 2 et 3 |
| `olist-pedagogique.sql` | même base, en instructions SQL (repli) | atelier 2 si l'import `.db` échoue |
| `transactions-ecommerce.csv` | 1 table plate, 3 000 lignes | modules 6 et 7, atelier 5 |
| `bonheur-mondial-2019.csv` | 156 pays | atelier 6 |

**Contrainte de construction essentielle : la base SQLite et le fichier tableur portent exactement les mêmes 3 000 commandes.** Ce n'est pas approximatif, c'est exact — la correspondance a été vérifiée identifiant par identifiant.

Conséquence pédagogique, à exploiter en séance : une requête SQL écrite au jour 1 retrouve **au centime près** le chiffre calculé au tableur le jour 2.

| Contrôle | SQL (jour 1) | Tableur (jour 2) |
|---|---|---|
| Chiffre d'affaires total | 487 545,87 BRL | 487 545,87 BRL |
| Montant moyen par commande | 162,52 BRL | 162,52 BRL |
| Montant maximal | 3 602,47 BRL | 3 602,47 BRL |
| Commandes au-dessus de 361,25 BRL | 218 | 218 |

C'est aussi la démonstration concrète de « on normalise pour écrire, on dénormalise pour lire » : deux formes du même contenu, adaptées à deux usages.

**Une seule exception, volontaire et instructive : le délai de livraison moyen.** Le SQL annonce 12,54 jours, le tableur 12,07. Les deux sont justes : le SQL compte les fractions de journée (`julianday`), le fichier tableur contient des jours entiers tronqués. C'est traité explicitement au module 3, et c'est la meilleure illustration possible de « demander la définition avant de comparer deux chiffres ».

---

## 2. `olist-pedagogique.db` — la base SQL

### Origine

Jeu de données public **Brazilian E-Commerce Public Dataset by Olist** :
https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce

Activité réelle d'une place de marché brésilienne entre septembre 2016 et octobre 2018. Le téléchargement depuis Kaggle exigeant un compte connecté, les tables ont été récupérées via un miroir public GitHub ; **ce sont les tables originales non modifiées**, mêmes noms de colonnes, mêmes effectifs.

### Contenu de la base

| Table | Lignes | Rôle |
|---|---|---|
| `orders` | 3 000 | les commandes |
| `order_items` | 3 418 | les articles, table intermédiaire du N-N |
| `order_payments` | 3 115 | les paiements |
| `order_reviews` | 3 021 | les avis |
| `customers` | 3 000 | les clients |
| `products` | 2 488 | les produits |
| `sellers` | 975 | les vendeurs |
| `product_category_name_translation` | 64 | les libellés de catégorie |

Les trois écarts d'effectifs par rapport aux 3 000 commandes sont **pédagogiquement utiles** et exploités en cours : 3 418 articles (une commande peut en contenir plusieurs), 3 021 avis (20 commandes en ont reçu deux), 3 115 paiements (une commande peut être réglée en plusieurs fois).

### Transformations appliquées

1. **Filtrage sur les commandes livrées** (statut `delivered`), puis sur celles ayant au moins un article, puis sur les années **2017 et 2018**. L'année 2016 ne compte que 267 commandes livrées, volume trop faible pour être comparable.
2. **Échantillonnage aléatoire de 3 000 commandes**, graine fixée à **2026**, donc reproductible à l'identique.
3. **Restriction des tables de dimensions** à ce qui est réellement référencé : seuls les clients, produits et vendeurs concernés par ces 3 000 commandes figurent dans la base.
4. **Allègement des colonnes.** Les colonnes jamais utilisées en TP ont été retirées pour garder un schéma lisible par un débutant. Notamment absentes : `order_approved_at`, `order_delivered_carrier_date`, `shipping_limit_date`, les dimensions physiques des produits, les textes d'avis, les codes postaux.
5. **Déclaration explicite des clés.** Clés primaires et clés étrangères sont déclarées dans le schéma, y compris la clé composée `(order_id, order_item_id)`. Les étudiants peuvent donc lire la structure dans la base elle-même.
6. **Contrôle d'intégrité référentielle** exécuté après chargement : aucune violation.

### Notes techniques

- Les dates sont stockées en texte au format `AAAA-MM-JJ HH:MM:SS`, comme dans le jeu d'origine. SQLite n'a pas de type date natif ; `julianday()` les convertit.
- Trois index sont créés sur les clés étrangères les plus sollicitées, pour que les jointures restent instantanées dans un navigateur.
- Taille du fichier : environ 3 Mo, compatible avec un import dans un éditeur SQL en ligne.

### Limite connue

`products` contient **36 produits sans catégorie**. Ce n'est pas un défaut de préparation, c'est l'état réel du jeu d'origine, et c'est **volontairement conservé** : le module 3 s'en sert pour démontrer la différence entre `JOIN` et `LEFT JOIN`.

---

## 3. `transactions-ecommerce.csv` — l'extrait tableur

3 000 lignes, 10 colonnes, une ligne par commande. Construit à partir des mêmes tables et du même échantillon.

### Transformations

1. **Agrégation à la commande** : jointure `orders` × `order_items`, puis somme du prix et des frais de port. C'est exactement la requête présentée au module 3 §3.2.
2. **Catégorie retenue** : celle du **produit le plus cher** de la commande. C'est un choix, pas une vérité — une autre règle (catégorie la plus fréquente, première ligne) donnerait des résultats légèrement différents.
3. **Renommage en français** des colonnes conservées.
4. **Troncature des identifiants** aux 8 premiers caractères, vérifiée sans collision.
5. **Délai de livraison en jours entiers** (troncature). D'où l'écart avec le SQL, exploité en cours.

### Représentativité, vérifiée

| Indicateur sur `montant_total_brl` | Population (96 478 livrées) | Échantillon (3 000) | Écart |
|---|---|---|---|
| Moyenne | 159,83 | 162,52 | +1,7 % |
| Médiane | 105,28 | 107,95 | +2,5 % |
| Écart-type | 218,79 | 215,65 | −1,4 % |
| Q1 | 61,85 | 62,82 | +1,6 % |
| Q3 | 176,26 | 182,19 | +3,4 % |

Écarts de 1 à 3 % : l'échantillon est fidèle. Ce n'est pas un miracle, c'est ce que produit un tirage vraiment aléatoire de taille suffisante.

### Limites à énoncer aux étudiants

- **Données pseudonymisées.** Identifiants remplacés par des codes aléatoires ; dans le jeu d'origine, les noms de vendeurs ont été remplacés par des noms de fiction. C'est cette pseudonymisation qui a rendu la publication possible.
- **Biais du survivant.** Le jeu ne contient que les commandes passées. Aucun panier abandonné, aucune recherche infructueuse, aucun client parti à la concurrence.
- **Périmètre.** Une seule place de marché, un seul pays, deux années. Rien ne permet de généraliser au commerce en ligne.
- **Devise.** Réal brésilien (BRL), aucune conversion appliquée.
- **Colonnes volontairement absentes** du CSV, et dont l'absence est exploitée à l'atelier 5 : l'état du vendeur, le poids des produits, le moyen de paiement, les notes d'avis. Les étudiants sont censés en réclamer certaines — et découvrir que plusieurs se trouvent en réalité dans la base SQL du jour 1.

---

## 4. Les deux mini-jeux

### `mini-frais-port-11.csv` — distribué

11 lignes extraites par tirage aléatoire (graine 404), frais de port triés par ordre croissant. Sert au calcul à la main de la partie A de l'atelier 5.

Les onze valeurs sont **toutes distinctes**, ce qui rend le mode inexistant : c'est **volontaire** et constitue la réponse attendue à la question A.3.

Effectif **impair**, pour que la médiane soit une valeur de la série — à distinguer du cas pair traité en cours.

### `mini-transactions-12.csv` — non distribué

12 montants de commande (graine 7), effectif **pair**. Sert à la démonstration du cours au module 6 : calcul de la médiane dans le cas pair, puis remplacement de la plus grande valeur par 20 000 pour montrer la sensibilité de la moyenne.

**Ne pas confondre les deux**, ils n'ont ni le même effectif, ni la même variable, ni le même usage.

---

## 5. `bonheur-mondial-2019.csv`

### Origine

**World Happiness Report**, édition 2019 :
https://www.kaggle.com/datasets/unsdsn/world-happiness

Récupéré via un miroir public GitHub. 156 pays. Renommage des colonnes en français, **sans aucune modification des valeurs**.

### Avertissement important sur la colonne `region`

**La colonne `region` ne figure pas dans le fichier Kaggle d'origine de l'édition 2019.** Elle a été ajoutée par le miroir utilisé, qui a également renommé les colonnes par rapport aux intitulés Kaggle (`Overall rank`, `Country or region`, `Score`, `GDP per capita`...).

Conséquence pratique : un étudiant qui télécharge le fichier directement depuis Kaggle **ne trouvera pas la colonne `region`** et verra d'autres noms de colonnes. Les regroupements régionaux sont cohérents avec ceux des éditions antérieures du rapport, et la colonne est parfaitement exploitable pour l'atelier — mais son origine doit être connue.

Si vous tenez à une traçabilité stricte jusqu'à la source officielle, retirez cette colonne et remplacez la dimension régionale de l'atelier 6 par un regroupement que vous constituez vous-même.

### Ce que mesurent réellement les colonnes

Point contre-intuitif, à énoncer aux étudiants.

- `score_bonheur` est une moyenne nationale de réponses à une question d'auto-évaluation de la vie sur une échelle de 0 à 10. C'est un **indicateur déclaratif**.
- Les six colonnes suivantes ne sont **pas** des mesures brutes de PIB, d'espérance de vie ou de liberté. Ce sont les **contributions estimées** de chaque facteur au score, calculées par le modèle du rapport. Elles ne s'additionnent pas exactement au score total.

Présenter `pib_hab` comme « le PIB par habitant » est donc inexact. L'atelier 6 signale ce point et valorise les étudiants qui le relèvent.

### Limites

- **Une seule année.** Aucune évolution possible.
- **Unité d'analyse : le pays.** Les moyennes nationales masquent entièrement les inégalités internes.
- **156 pays sur environ 195.** Les absents ne le sont pas au hasard : les pays en conflit ou fermés aux enquêtes manquent plus souvent. Biais de sélection, et point bonus de l'atelier 6.
- **Comparabilité culturelle.** La façon de répondre à une échelle de 0 à 10 varie selon les cultures.
- **Effectifs régionaux très déséquilibrés** : 2 pays pour Australia and New Zealand, 40 pour Sub-Saharan Africa. C'est le piège central de l'atelier 6.

---

## 6. Reproductibilité

Les scripts figurent dans `donnees/scripts/`, et les tables d'origine dans `donnees/sources/`.

```bash
cd donnees
python3 scripts/01-construire-la-base-sql.py            # -> olist-pedagogique.db et .sql
python3 scripts/02-construire-les-extraits-tableur.py sources   # -> les trois CSV
python3 scripts/03-statistiques-de-reference.py > statistiques-de-reference.txt
```

Les graines aléatoires sont fixées (2026, 7, 404) : l'exécution reproduit les fichiers à l'identique. Seuls `pandas` et `sqlite3` (inclus dans Python) sont requis.

**Toute valeur citée dans les supports de cours et dans les corrigés est reproductible par ces scripts.** Le fichier `statistiques-de-reference.txt` contient l'ensemble des valeurs pour vérification rapide, sans exécuter de code.

---

## 7. Licences et réutilisation

Les deux jeux sont publiés publiquement sur Kaggle à des fins d'usage libre, notamment pédagogique. Vérifiez les conditions affichées sur les pages Kaggle avant toute diffusion hors d'un cadre de formation :

- Olist : https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce
- World Happiness Report : https://www.kaggle.com/datasets/unsdsn/world-happiness

Citez la source dans tout support ou tableau de bord produit à partir de ces données. L'atelier 6 en fait d'ailleurs une exigence notée.
