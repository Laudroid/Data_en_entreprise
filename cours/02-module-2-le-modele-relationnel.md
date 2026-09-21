# Module 2 — Le modèle relationnel : tables, clés, jointures

**Jour 1 · 11 h 50 – 12 h 30 · 40 minutes.**
**Public : Bachelor 2, aucun prérequis.**

Ce module pose les fondations. Le module 3, juste après le déjeuner, les met en pratique en SQL.

---

## Objectifs pédagogiques

À la fin de ce module, l'étudiant est capable de :

1. Expliquer les trois défauts d'un tableau unique et pourquoi on découpe en tables.
2. Définir clé primaire, clé étrangère, clé composée, et dire à quoi sert l'intégrité référentielle.
3. Lire le schéma de la base Olist et nommer la table à interroger pour une question donnée.
4. Expliquer ce que fait une jointure et pourquoi elle est la contrepartie du découpage.
5. Énoncer ce que garantit ACID, et pourquoi cela compte.

---

## Minutage interne

| Temps | Séquence |
|---|---|
| 10 min | 1. Le problème : pourquoi pas un seul grand tableau |
| 10 min | 2. La réponse relationnelle : tables et clés |
| 10 min | 3. Le schéma de notre base |
| 7 min | 4. La jointure |
| 3 min | 5. ACID et les principaux systèmes |

---

## 1. Le problème : pourquoi pas un seul grand tableau ?

Posons le problème avec notre jeu e-commerce. Nous voulons enregistrer des commandes. Chaque commande a un client, contient un ou plusieurs articles, chaque article vient d'un vendeur et appartient à une catégorie.

Version naïve : un seul tableau, une ligne par article vendu, toutes les colonnes.

| commande | date | nom_client | ville_client | produit | categorie | prix | nom_vendeur | ville_vendeur |
|---|---|---|---|---|---|---|---|---|
| 40599d3d | 2017-01-05 | A. Souza | Curitiba | croquettes | pet_shop | 11,90 | Zoo+ | São Paulo |
| 40599d3d | 2017-01-05 | A. Souza | Curitiba | gamelle | pet_shop | 8,50 | Zoo+ | São Paulo |
| 743cc56c | 2017-01-11 | B. Lima | Londrina | grille-pain | eletroportateis | 97,99 | ElectroBR | Belo Horizonte |

Ce tableau fonctionne, et pourtant il est gravement défectueux. Trois défauts — **à faire trouver par la salle avant de les donner**.

### Défaut 1 — la redondance

« A. Souza », « Curitiba », « Zoo+ », « São Paulo » sont répétés à chaque ligne. Sur les 3 418 articles vendus de notre base, et sur les 112 650 du jeu complet, on stocke des millions de fois la même information. Coût de stockage, mais surtout coût de maintenance.

### Défaut 2 — l'incohérence

Le jour où A. Souza déménage, il faut modifier toutes ses lignes. Si une seule est oubliée, la base contient deux vérités contradictoires et plus personne ne sait laquelle est bonne.

Pire encore : si trois opérateurs saisissent « São Paulo », « Sao Paulo » et « SP », vous avez trois villes là où il y en a une. Aucun calcul par ville ne sera juste, et le défaut ne se voit pas à l'oeil sur un tableau de cent mille lignes.

### Défaut 3 — l'impossibilité d'exprimer certaines choses

Comment enregistrer un vendeur qui n'a encore rien vendu ? Il n'a aucune ligne, donc il n'existe pas. Comment enregistrer une catégorie de produit avant qu'elle contienne un produit ? Impossible.

Le tableau unique ne sait représenter que ce qui a déjà eu lieu. Il confond « les choses qui existent » et « les événements qui se sont produits ». C'est le défaut le plus profond des trois, et presque personne ne le trouve seul.

---

## 2. La réponse relationnelle

Le modèle relationnel, formalisé par Edgar Codd en 1970, répond en trois idées.

### Idée 1 — une table par type de chose

Les clients dans une table, les commandes dans une autre, les produits dans une troisième. Chaque information n'est stockée qu'à un seul endroit. C'est le principe de la **source unique de vérité** : pour changer l'adresse d'un client, il y a exactement une ligne à modifier.

### Idée 2 — la clé primaire

Chaque table possède une colonne, ou un groupe de colonnes, qui identifie chaque ligne de façon **unique** et **jamais vide**. On la note **PK**.

Dans la table des clients : `customer_id`. Dans la table des produits : `product_id`.

Une **clé composée** est une clé primaire formée de plusieurs colonnes. Notre table `order_items` en a une : `(order_id, order_item_id)`. Aucune des deux ne suffit seule, puisqu'une commande contient plusieurs articles et que le numéro d'article repart à 1 à chaque commande. C'est ensemble qu'elles identifient une ligne.

### Idée 3 — la clé étrangère

Pour relier une commande à son client, la table des commandes ne recopie pas le nom du client : elle stocke la **clé primaire** du client. Cette colonne s'appelle une **clé étrangère**, notée FK.

Elle porte une garantie que la base fait respecter elle-même : on ne peut pas enregistrer une commande dont le client n'existe pas, ni supprimer un client qui a des commandes. C'est l'**intégrité référentielle**.

Cette garantie n'est pas un détail technique. C'est ce qui fait qu'une base relationnelle reste cohérente après dix ans d'usage par des centaines de personnes, alors qu'un ensemble de fichiers tableur ne le reste jamais.

### Comment les trois idées règlent les trois défauts

| Défaut | Ce que le modèle relationnel y oppose |
|---|---|
| Redondance | le nom du client est stocké une fois, dans `customers` |
| Incohérence | une seule ligne à modifier ; l'intégrité référentielle interdit les références orphelines |
| Impossibilité | un vendeur existe dans `sellers` même sans vente, une catégorie existe sans produit |

---

## 3. Le schéma de notre base

Voici la base que vous allez interroger cet après-midi. Huit tables, restreintes aux 3 000 commandes de notre échantillon.

```
customers (3 000)                    orders (3 000)
  customer_id        PK  <-------FK--  customer_id
  customer_unique_id                   order_id                      PK  --+
  customer_city                        order_status                        |
  customer_state                       order_purchase_timestamp            |
                                       order_delivered_customer_date       |
                                       order_estimated_delivery_date       |
                                                                           |
order_items (3 418)                                                        |
  order_id       PK, FK  <--------------------------------------------------+
  order_item_id  PK                     products (2 488)                   |
  product_id     FK  ----------------->   product_id             PK        |
  seller_id      FK  ---------+           product_category_name  FK --+    |
  price                       |           product_weight_g            |    |
  freight_value               |           product_photos_qty          |    |
                              |                                       |    |
sellers (975)                 |     product_category_name_translation (64) |
  seller_id      PK  <--------+       product_category_name         PK <---+
  seller_city                         product_category_name_english        |
  seller_state                                                             |
                                    order_payments (3 115)                 |
order_reviews (3 021)                 order_id            FK  <------------+
  review_id                           payment_sequential  PK               |
  order_id       FK  <-----------------------------------------------------+
  review_score                        payment_type
  review_creation_date                payment_installments
                                      payment_value
```

### Trois observations à faire remarquer

**La table `customers` contient `customer_id` et `customer_unique_id`.** Le premier est propre à une commande, le second identifie la personne à travers ses commandes. Un client ayant commandé trois fois a trois `customer_id` et un seul `customer_unique_id`.

C'est un piège classique et coûteux : compter les `customer_id` revient à compter les commandes, pas les clients. Sur notre base, vous découvrirez cet après-midi que le nombre de clients distincts n'est pas 3 000.

**`order_items` a 3 418 lignes pour 3 000 commandes.** Parce qu'une commande peut contenir plusieurs articles. Toute requête qui joint `orders` à `order_items` sans agréger produira donc plus de lignes qu'il n'y a de commandes. C'est la source d'erreur numéro un des débutants en SQL.

**`order_reviews` a 3 021 lignes pour 3 000 commandes.** Quelques commandes ont reçu deux avis. Même piège, et celui-là est plus sournois car l'écart est faible : une moyenne de notes calculée en joignant les avis aux commandes surpondère discrètement les commandes doublement notées.

### Le vocabulaire de lecture d'un schéma

| Question | Où regarder |
|---|---|
| De quoi cette table parle-t-elle ? | son nom, et sa clé primaire |
| À quoi est-elle reliée ? | ses clés étrangères |
| Combien de lignes par entité parente ? | comparer les effectifs |
| Où trouver une information donnée ? | chercher la table dont c'est le sujet |

Entraînez-vous tout de suite. Dans quelle table trouve-t-on l'état du client ? Et l'état du vendeur ? Et le prix d'un article ? Les trois réponses sont dans trois tables différentes, et c'est exactement le genre de question que le SQL va vous permettre de croiser.

---

## 4. La jointure

Le découpage en tables a un prix : l'information est éparpillée. La **jointure** est l'opération qui la rassemble.

Le principe tient en une phrase : **on recolle deux tables en s'appuyant sur la correspondance entre une clé étrangère et une clé primaire.**

Reprenons. Je veux le montant de chaque commande avec l'état du client. Trois tables sont concernées :

- `orders` porte `order_id` et `customer_id` ;
- `order_items` porte les prix, reliée par `order_id` ;
- `customers` porte `customer_state`, reliée par `customer_id`.

La jointure suit le chemin des clés : `order_items` → `orders` → `customers`. C'est exactement ce que vous écrirez cet après-midi.

### L'image à retenir

Découper en tables, c'est ranger sa cuisine : chaque chose à sa place, rien en double. Joindre, c'est sortir ce dont on a besoin pour une recette précise. On ne laisse pas tous les ingrédients sur le plan de travail sous prétexte qu'on cuisinera un jour.

**On normalise pour écrire, on joint pour lire.** Retenez cette phrase : elle reviendra demain sous une autre forme, quand nous parlerons d'entrepôts de données.

---

## 5. ACID et les principaux systèmes

### 5.1 Ce que garantit une base relationnelle

Quatre garanties, un acronyme. Elles expliquent pourquoi les banques ne quittent pas le relationnel.

- **A**tomicité : une opération composée de plusieurs écritures se fait en entier ou pas du tout. Un virement ne peut pas débiter sans créditer.
- **C**ohérence : la base ne passe jamais dans un état qui viole ses propres règles.
- **I**solation : deux opérations simultanées ne se mélangent pas.
- **D**urabilité : ce qui est validé survit à une panne de courant.

Retenez l'atomicité, c'est celle qui parle le plus. Les trois autres se comprennent au besoin.

### 5.2 Les principaux systèmes

**PostgreSQL** et **MySQL / MariaDB** : libres, très répandus, du site web à l'application d'entreprise.
**Microsoft SQL Server** et **Oracle Database** : propriétaires, monde de la grande entreprise.
**SQLite** : embarqué dans les applications et les téléphones, sans serveur, la base entière tient dans un fichier. C'est celui que vous utiliserez cet après-midi — le fichier `olist-pedagogique.db` est une base SQLite complète.

Tous parlent SQL, avec des dialectes légèrement différents. Ce que vous apprendrez cet après-midi fonctionne à l'identique sur les cinq.

---

## Ce qu'il faut retenir du module 2

1. On découpe en tables pour supprimer la redondance, éviter l'incohérence, et pouvoir représenter des choses qui existent sans avoir encore servi.
2. La clé primaire identifie une ligne de façon unique et non vide ; elle peut être composée de plusieurs colonnes.
3. La clé étrangère référence la clé primaire d'une autre table, et la base fait respecter cette référence : c'est l'intégrité référentielle.
4. Une jointure recolle deux tables sur la correspondance clé étrangère / clé primaire. On normalise pour écrire, on joint pour lire.
5. Attention aux effectifs : 3 418 articles et 3 021 avis pour 3 000 commandes. Joindre sans agréger multiplie les lignes.
6. ACID garantit qu'une opération se fait en entier ou pas du tout, et que ce qui est validé survit.

---

## Vocabulaire du module 2

**Table, ligne, colonne** : structure de base. Une ligne est aussi appelée enregistrement, une colonne un champ.
**Clé primaire (PK)** : colonne identifiant chaque ligne de façon unique et non vide.
**Clé composée** : clé primaire formée de plusieurs colonnes.
**Clé étrangère (FK)** : colonne référençant la clé primaire d'une autre table.
**Intégrité référentielle** : garantie qu'une clé étrangère pointe toujours vers une ligne existante.
**Jointure** : opération de recollement de deux tables sur une correspondance de clés.
**Source unique de vérité** : principe selon lequel une information n'est stockée qu'à un seul endroit.
**ACID** : atomicité, cohérence, isolation, durabilité.
**SGBD** : système de gestion de base de données (PostgreSQL, MySQL, SQLite...).

---

## Notes pour le formateur

**Faites trouver les trois défauts par la salle.** C'est la séquence la plus rentable du module. La redondance vient en trente secondes, l'incohérence avec un indice (« et si le client déménage ? »), le troisième presque jamais (« et un vendeur qui n'a encore rien vendu ? »). Cette découverte justifie le modèle relationnel au lieu de l'imposer, et la différence d'adhésion est nette.

**Projetez le schéma et posez trois questions de localisation** avant de passer à la jointure : où est l'état du client, où est l'état du vendeur, où est le prix. Trois tables, trois réponses. Cela prépare directement les requêtes de l'après-midi et fait manipuler le schéma au lieu de le regarder.

**Ne développez pas ACID.** Trois minutes, l'atomicité illustrée par le virement, et vous passez. Le détail des trois autres lettres n'apporte rien à ce niveau et coûte cher en attention.

**Le piège des effectifs (section 3) est le plus utile de tout le module 2.** 3 418 articles pour 3 000 commandes : écrivez les deux nombres au tableau et laissez-les visibles pendant l'atelier SQL. La moitié des erreurs de l'après-midi viendront de là, et un étudiant qui aperçoit les deux nombres au tableau se corrige tout seul.

**Transition vers le déjeuner et le module 3.** « Vous savez lire le schéma. Après le déjeuner, vous apprenez à l'interroger — et vous écrirez vos propres requêtes sur cette base. »
