# Module 4 — Modélisation des données et diagrammes ERD

**Jour 1 · 15 h 45 – 16 h 30 · 45 minutes**, suivi de l'atelier 3 (30 minutes).

---

## Objectifs pédagogiques

À la fin de ce module, l'étudiant est capable de :

1. Distinguer entité, attribut, identifiant et association.
2. Déterminer la cardinalité d'une association et en déduire où placer la clé étrangère.
3. Reconnaître une relation plusieurs-à-plusieurs et la résoudre par une table intermédiaire.
4. Appliquer les trois premières règles de normalisation sans formalisme.
5. Expliquer quand et pourquoi on dénormalise.
6. Écrire un schéma en DBML et le faire dessiner par dbdiagram.io.

---

## 1. À quoi sert un diagramme

Un **diagramme entité-association** — ou **ERD**, pour Entity-Relationship Diagram — est le plan du bâtiment. Il montre les tables, leurs colonnes, et les liens entre elles.

Il sert à trois choses, et les trois comptent en entreprise :

1. **Se mettre d'accord avant de construire.** Un schéma se discute en une heure ; une base mal conçue se paie pendant des années.
2. **Comprendre une base qu'on découvre.** C'est le cas le plus fréquent dans votre future vie professionnelle : vous arriverez dans une entreprise avec une base existante de deux cents tables, et le schéma sera votre seule carte.
3. **Repérer les erreurs de conception avant qu'elles coûtent cher.** Beaucoup de défauts sautent aux yeux sur un diagramme et restent invisibles dans du code.

Le schéma que vous avez lu au module 2 et interrogé au module 3 est un ERD. Vous allez maintenant apprendre à en produire un.

---

## 2. Le vocabulaire

**Entité** : un type de chose dont on veut garder la trace. Un client, une commande, un produit. Devient une **table**.

**Attribut** : une propriété de l'entité. Le nom du client, la date de la commande. Devient une **colonne**.

**Identifiant** : l'attribut, ou le groupe d'attributs, qui distingue chaque occurrence. Devient la **clé primaire**, notée PK.

**Association** : le lien entre deux entités. « Un client passe des commandes. » Se traduit par une clé étrangère, ou par une table selon le cas.

**Cardinalité** : combien d'occurrences de chaque côté du lien. C'est la seule notion réellement nouvelle de ce module, et la seule qui compte vraiment.

### Comment repérer les entités dans un texte

Méthode pratique, applicable à l'atelier qui suit : dans l'énoncé d'un besoin, les **noms communs répétés** sont des entités candidates, et les **verbes qui les relient** sont des associations.

> « Nos **clients** passent des **commandes**. Une **commande** contient plusieurs **produits**. Chaque **produit** vient d'un **fournisseur**. »

Quatre noms répétés, quatre entités. Trois verbes, trois associations. La méthode n'est pas infaillible mais elle donne un premier jet en deux minutes, et c'est exactement ce qu'on attend de vous en réunion de cadrage.

---

## 3. Les cardinalités

### 3.1 Les trois cas

| Notation | Nom | Exemple dans notre base | Traduction en tables |
|---|---|---|---|
| 1 — 1 | un à un | un salarié et son badge d'accès | clé étrangère d'un côté, avec contrainte d'unicité |
| **1 — N** | un à plusieurs | un client passe plusieurs commandes ; une commande contient plusieurs articles | la clé étrangère va **du côté N** |
| **N — N** | plusieurs à plusieurs | une commande contient plusieurs produits, et un produit apparaît dans plusieurs commandes | il faut une **table intermédiaire** |

### 3.2 La règle du 1 — N

**La clé étrangère se place toujours du côté « plusieurs ».**

La table `orders` porte `customer_id` parce qu'une commande n'a qu'un client, alors qu'un client peut avoir plusieurs commandes.

L'inverse serait impossible : il faudrait mettre plusieurs valeurs dans une même case de la table `customers`. Or une case contient une valeur, et une seule.

**La question de vérification, qui fonctionne toujours :** *peut-on mettre plusieurs valeurs dans une seule case ? Non. Donc de quel côté doit aller la clé ?*

Posez-vous-la chaque fois que vous hésitez. Elle tranche à tous les coups.

### 3.3 Le cas N — N, et pourquoi il mérite un arrêt

Une commande contient plusieurs produits. Un produit est vendu dans plusieurs commandes. On ne peut placer la clé étrangère ni d'un côté ni de l'autre, puisque les deux côtés sont « plusieurs ».

La solution est une **table intermédiaire** — aussi appelée table de jonction ou table d'association. Dans notre base, c'est `order_items` :

```
Table order_items {
  order_id       -- clé étrangère vers orders
  order_item_id  -- numéro de l'article dans la commande
  product_id     -- clé étrangère vers products
  seller_id      -- clé étrangère vers sellers
  price          -- attribut propre à la rencontre
  freight_value  -- attribut propre à la rencontre
}
```

Regardez ce qu'elle contient au-delà des deux clés : `price` et `freight_value`.

**Ces attributs n'appartiennent ni à la commande seule, ni au produit seul.** Ils appartiennent à la rencontre des deux : le prix de *ce produit* dans *cette commande*.

C'est le point le plus important du module, et voici pourquoi.

### 3.4 Le prix : une donnée d'événement, pas une donnée d'état

Le prix d'un produit change avec le temps : promotions, saisons, inflation. Il existe donc **deux prix distincts**, qui ne vivent pas au même endroit :

| Donnée | Où elle vit | Ce qu'elle décrit |
|---|---|---|
| Le tarif au catalogue aujourd'hui | table `products` | un **état présent**, qui change |
| Le prix effectivement facturé | table `order_items` | un **événement passé**, figé pour toujours |

Si le prix n'existait que dans `products`, alors le jour où le vendeur change son tarif, **le montant de toutes les commandes passées changerait rétroactivement**. La comptabilité deviendrait fausse, les factures émises ne correspondraient plus à la base, et l'historique serait irrécupérable.

Formulez-le ainsi, c'est la phrase à retenir : **une donnée qui décrit un état présent et une donnée qui décrit un événement passé sont deux données différentes, même si elles portent le même nom et la même valeur à un instant donné.**

Le jeu Olist stocke `price` dans `order_items` exactement pour cette raison. Ce n'est pas un choix arbitraire de ses concepteurs, c'est une nécessité. Vous devrez reproduire cette décision dans l'atelier qui suit.

### 3.5 Le cas 1 — 1, et pourquoi il est rare

Si deux entités sont vraiment en relation un à un, la question se pose de savoir pourquoi elles font deux tables et non une seule. Les raisons légitimes existent : séparer des données sensibles pour en restreindre l'accès, ou isoler des colonnes volumineuses rarement consultées.

Mais en première approche, une relation 1 — 1 est souvent le signe d'un découpage inutile. Méfiez-vous-en.

---

## 4. Normaliser, dénormaliser

### 4.1 Les trois règles, sans formalisme

**Normaliser** consiste à découper les tables pour que chaque information ne soit stockée qu'une fois. Trois règles suffisent largement à votre niveau.

**Règle 1 — une case contient une seule valeur.**
Pas de colonne `telephones` contenant « 0601020304 ; 0145678901 ». Si un client peut avoir plusieurs téléphones, il faut une table `telephones`.

**Règle 2 — chaque colonne dépend de la clé primaire entière.**
Dans `order_items`, dont la clé est `(order_id, order_item_id)`, on ne met pas le nom du client : il dépend de la commande seule, pas du couple. Sa place est dans `customers`.

**Règle 3 — aucune colonne ne dépend d'une autre colonne non clé.**
Si l'on stocke la ville et la région du client dans la même table, et que la région se déduit de la ville, alors la région est redondante. Sa place est dans une table des villes.

Ces trois règles portent des noms savants — première, deuxième et troisième forme normale. Retenez les règles, les noms vous seront donnés le jour où vous en aurez besoin.

### 4.2 Dénormaliser

L'opération inverse : on accepte volontairement de la redondance pour aller plus vite en lecture et simplifier l'usage.

C'est exactement ce que fait le fichier `transactions-ecommerce.csv` que vous utiliserez demain : une ligne par commande, avec la catégorie, l'état du client et le montant **déjà calculé**. Quatre tables ont été aplaties en une seule.

| | Base normalisée (`olist-pedagogique.db`) | Fichier dénormalisé (`transactions-ecommerce.csv`) |
|---|---|---|
| Structure | 8 tables reliées | 1 table plate |
| Lignes | 3 000 commandes, 3 418 articles | 3 000 lignes |
| Pour obtenir un montant | il faut une jointure et une somme | la colonne existe déjà |
| Pour changer un nom de catégorie | une seule ligne à modifier | toutes les lignes concernées |
| Bon pour | enregistrer, garantir la cohérence | analyser, construire un tableau de bord |

Ce n'est pas une faute, c'est une adaptation à l'usage. La règle générale tient en une phrase :

> **On normalise pour écrire, on dénormalise pour lire.**

Cette phrase est la charnière entre le jour 1 et le jour 2. Demain matin, quand nous parlerons d'OLTP et d'OLAP, vous verrez qu'elle décrit en réalité deux systèmes distincts dans l'entreprise.

---

## 5. DBML et dbdiagram.io

### 5.1 Le principe

**dbdiagram.io** utilise un langage textuel appelé **DBML** : vous écrivez les tables, l'outil dessine le diagramme et place les liens automatiquement. C'est plus rapide qu'un outil de dessin, et le résultat est modifiable.

L'outil est accessible sans compte pour dessiner. L'enregistrement, lui, demande une connexion : vous livrerez donc votre travail sous forme d'export image ou de code DBML copié dans un fichier.

### 5.2 La syntaxe

```
Table customers {
  customer_id    varchar [pk]
  customer_state varchar
}

Table orders {
  order_id    varchar [pk]
  customer_id varchar [ref: > customers.customer_id]
  order_date  datetime
}
```

Voilà l'essentiel. Trois éléments :

- `[pk]` marque la clé primaire ;
- `[ref: > customers.customer_id]` crée le lien, le chevron indiquant le sens « plusieurs `orders` vers un `customers` » ;
- les types (`varchar`, `integer`, `decimal`, `datetime`, `text`) sont indicatifs à ce stade.

### 5.3 Les trois notations de lien

| Écriture | Signification |
|---|---|
| `[ref: > table.colonne]` | plusieurs vers un — le cas courant |
| `[ref: - table.colonne]` | un vers un |
| `[ref: <> table.colonne]` | plusieurs vers plusieurs |

**Évitez le `<>`.** Il laisse l'outil masquer la table intermédiaire, alors que c'est justement elle qui porte les attributs de la rencontre. Faites toujours apparaître explicitement la table de jonction : c'est ce qui sera construit dans la vraie base, et c'est ce que le correcteur attend.

### 5.4 La clé primaire composée

Pour une clé formée de deux colonnes, la syntaxe est un bloc `indexes` :

```
Table order_items {
  order_id      varchar [ref: > orders.order_id]
  product_id    varchar [ref: > products.product_id]
  quantite      integer
  prix_pratique decimal

  indexes {
    (order_id, product_id) [pk]
  }
}
```

### 5.5 Méthode de travail recommandée

1. **Sur papier d'abord.** Listez les entités, leurs identifiants, les associations et leurs cardinalités. Cinq minutes.
2. **Repérez les N — N** et transformez-les en tables intermédiaires avant de toucher au clavier.
3. **Écrivez le DBML** entité par entité, sans les liens.
4. **Ajoutez les liens** en dernier, un par un, en vérifiant le diagramme à chaque ajout.
5. **Relisez avec la question de vérification** : chaque case contient-elle une seule valeur ?

Cette progression évite l'erreur la plus fréquente, qui consiste à se lancer dans l'outil avant d'avoir identifié les entités, et à découvrir au bout de vingt minutes qu'il manque une table.

---

## Ce qu'il faut retenir du module 4

1. Un ERD sert à se mettre d'accord avant de construire, et à comprendre une base qu'on découvre — le cas le plus fréquent en entreprise.
2. Entité devient table, attribut devient colonne, identifiant devient clé primaire.
3. Dans une relation un à plusieurs, la clé étrangère va toujours du côté « plusieurs ». Vérification : peut-on mettre plusieurs valeurs dans une case ? Non.
4. Une relation plusieurs à plusieurs exige une table intermédiaire, et cette table porte les attributs propres à la rencontre.
5. Un prix facturé est une donnée d'événement : il se fige dans la ligne de commande, jamais seulement dans la fiche produit.
6. Trois règles de normalisation : une valeur par case, dépendance à la clé entière, pas de dépendance entre colonnes non clés.
7. On normalise pour écrire, on dénormalise pour lire.

---

## Vocabulaire du module 4

**ERD** : diagramme entité-association.
**Entité, attribut, identifiant, association** : les quatre briques d'un modèle conceptuel.
**Cardinalité** : nombre d'occurrences de part et d'autre d'une association (1-1, 1-N, N-N).
**Table intermédiaire** (ou de jonction) : table résolvant une relation plusieurs à plusieurs.
**Normalisation** : découpage des tables pour supprimer la redondance.
**Formes normales** : les trois premières règles de normalisation.
**Dénormalisation** : réintroduction volontaire de redondance pour accélérer la lecture.
**DBML** : langage textuel de description de schéma utilisé par dbdiagram.io.
