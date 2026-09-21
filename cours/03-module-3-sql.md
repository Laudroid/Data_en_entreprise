# Module 3 — SQL : lire et écrire des requêtes

**Jour 1 · 13 h 30 – 14 h 30 · 60 minutes**, suivi de l'atelier 2 (60 minutes).
**Public : Bachelor 2, aucun prérequis. Première écriture de code de la formation.**

> **Toutes les requêtes de ce module ont été exécutées sur la base `olist-pedagogique.db`** et les résultats affichés sont les résultats réels. Les étudiants doivent retrouver les mêmes.

---

## Objectifs pédagogiques

À la fin de ce module, l'étudiant est capable de :

1. Écrire une requête `SELECT ... FROM ... WHERE` et en prédire le nombre de lignes.
2. Utiliser les cinq fonctions d'agrégation et comprendre ce que `GROUP BY` transforme.
3. Écrire une jointure entre deux puis trois tables en suivant le chemin des clés.
4. Distinguer `WHERE` et `HAVING`, et `JOIN` de `LEFT JOIN`.
5. Reconnaître les trois erreurs classiques : la multiplication des lignes, le comptage d'identifiants non uniques, la moyenne de moyennes.
6. Vérifier son propre résultat par un ordre de grandeur avant de le publier.

---

## 0. L'outil

Nous utilisons **sqliteonline.com**, un éditeur SQL qui fonctionne dans le navigateur, sans installation et sans compte.

**Chargement de la base :**

1. Ouvrez https://sqliteonline.com/
2. Dans le bandeau de gauche, ouvrez le menu de la base et choisissez l'ouverture d'un fichier existant (bouton d'import acceptant les fichiers `.db` et `.sqlite`).
3. Sélectionnez le fichier `olist-pedagogique.db` qui vous a été remis.
4. Les huit tables apparaissent dans l'arborescence de gauche. Cliquez sur l'une d'elles pour voir ses colonnes.

**Si l'import du fichier `.db` échoue**, un second fichier vous est fourni, `olist-pedagogique.sql`. Il contient les mêmes données sous forme d'instructions SQL : ouvrez-le par l'import de fichier `.sql` et exécutez-le. C'est plus lent, une à deux minutes, mais cela produit la même base.

**Exécuter une requête :** écrivez-la dans l'éditeur central, puis lancez l'exécution (bouton d'exécution, ou `Ctrl+Entrée`). Le résultat s'affiche en dessous.

Notre base est du **SQLite**. Ce que vous apprenez ici fonctionne à l'identique sur PostgreSQL, MySQL, SQL Server et Oracle, aux détails de dialecte près.

---

## 1. La forme d'une requête

### 1.1 Le squelette

Une requête de lecture suit toujours le même ordre d'écriture :

```sql
SELECT   -- quelles colonnes je veux voir
FROM     -- dans quelle table
WHERE    -- en ne gardant que les lignes qui remplissent une condition
GROUP BY -- en regroupant par une colonne
HAVING   -- en ne gardant que les groupes qui remplissent une condition
ORDER BY -- et en triant le résultat
LIMIT    -- en n'affichant que les N premières lignes
```

Toutes les clauses sont facultatives sauf `SELECT` et `FROM`.

### 1.2 La requête minimale

```sql
SELECT order_id, order_purchase_timestamp
FROM orders
LIMIT 3;
```

Résultat :

| order_id | order_purchase_timestamp |
|---|---|
| 53cdb2fc8bc7dce0b6741e2150273451 | 2018-07-24 20:41:37 |
| dcb36b511fcac050b97cd5c05de84dc3 | 2018-06-07 19:03:12 |
| b276e4f8c0fb86bd82fce576f21713e0 | 2018-07-29 23:34:51 |

Deux habitudes à prendre dès maintenant :

- **Toujours commencer par `LIMIT`** quand on découvre une table. Vous n'avez pas besoin de 3 000 lignes pour comprendre la structure.
- **Ne jamais écrire `SELECT *` dans une requête finale.** C'est pratique pour explorer, illisible dans un livrable, et cela transporte des colonnes inutiles.

### 1.3 Filtrer avec `WHERE`

```sql
SELECT COUNT(*)
FROM order_items
WHERE price > 500;
```

Résultat : **85**. Sur 3 418 articles vendus, 85 dépassent 500 BRL.

Les opérateurs de condition utiles :

| Opérateur | Sens | Exemple |
|---|---|---|
| `=` `<>` | égal, différent | `customer_state = 'SP'` |
| `>` `<` `>=` `<=` | comparaisons | `price >= 100` |
| `BETWEEN a AND b` | dans un intervalle, bornes comprises | `price BETWEEN 50 AND 100` |
| `IN (...)` | dans une liste | `customer_state IN ('SP','RJ')` |
| `LIKE` | motif textuel, `%` remplace n'importe quoi | `customer_city LIKE 'sao%'` |
| `IS NULL` / `IS NOT NULL` | valeur absente ou présente | `product_category_name IS NULL` |
| `AND` `OR` `NOT` | combinaisons | `price > 100 AND customer_state = 'SP'` |

**Un point qui piège tout le monde : `NULL` n'est pas une valeur.** C'est l'absence de valeur. `price = NULL` ne renvoie jamais rien, même s'il y a des valeurs absentes. Il faut écrire `price IS NULL`. Et `NULL` n'est ni égal ni différent de quoi que ce soit : une comparaison avec `NULL` ne renvoie ni vrai ni faux.

Vérifions sur notre base :

```sql
SELECT COUNT(*) FROM products WHERE product_category_name IS NULL;
```

Résultat : **36** produits sur 2 488 n'ont pas de catégorie renseignée. Retenez ce nombre, il servira en section 4.

### 1.4 Compter le distinct

```sql
SELECT COUNT(DISTINCT customer_state) FROM customers;
```

Résultat : **26** états brésiliens représentés.

Et maintenant la requête qui illustre le piège annoncé au module 2 :

```sql
SELECT COUNT(*)                        AS lignes_client,
       COUNT(DISTINCT customer_unique_id) AS personnes
FROM customers;
```

Résultat : **3 000** lignes, mais **2 997** personnes distinctes. Trois clients seulement ont commandé plusieurs fois dans cet échantillon.

Ce nombre est intéressant en soi — la fidélisation de cette place de marché est faible — mais le point technique compte davantage : **compter les lignes et compter les personnes sont deux requêtes différentes**, et celui qui confond les deux annonce 3 000 clients là où il y en a 2 997. Ici l'écart est négligeable ; dans une base réelle de commerce, il est souvent de 30 %.

---

## 2. Agréger

### 2.1 Les cinq fonctions

| Fonction | Ce qu'elle fait | Ignore les NULL ? |
|---|---|---|
| `COUNT(*)` | compte les lignes | non, compte tout |
| `COUNT(colonne)` | compte les valeurs présentes | oui |
| `SUM(colonne)` | additionne | oui |
| `AVG(colonne)` | moyenne | oui |
| `MIN` / `MAX` | plus petite, plus grande valeur | oui |

La différence entre `COUNT(*)` et `COUNT(colonne)` n'est pas une subtilité : sur une colonne à trous, les deux donnent des résultats différents, et c'est presque toujours `COUNT(colonne)` qui est correct quand on veut savoir combien de valeurs on a réellement.

### 2.2 Agréger sans regrouper

```sql
SELECT COUNT(*)            AS nb_articles,
       ROUND(MIN(price),2) AS prix_mini,
       ROUND(AVG(price),2) AS prix_moyen,
       ROUND(MAX(price),2) AS prix_maxi
FROM order_items;
```

Résultat :

| nb_articles | prix_mini | prix_moyen | prix_maxi |
|---|---|---|---|
| 3 418 | 3,00 | 122,51 | 3 549,00 |

Une seule ligne de résultat : les 3 418 lignes ont été réduites à un résumé. `ROUND(x, 2)` arrondit à deux décimales, et `AS` renomme la colonne affichée.

### 2.3 `GROUP BY` : une ligne par groupe

`GROUP BY` est la notion centrale du module. Il transforme *n* lignes en *une ligne par valeur distincte* de la colonne de regroupement.

```sql
SELECT customer_state, COUNT(*) AS nb_clients
FROM customers
GROUP BY customer_state
ORDER BY nb_clients DESC
LIMIT 5;
```

Résultat :

| customer_state | nb_clients |
|---|---|
| SP | 1 228 |
| RJ | 402 |
| MG | 323 |
| RS | 176 |
| PR | 149 |

3 000 lignes sont devenues 26 lignes, une par état, dont on affiche les 5 premières.

**La règle qui évite 90 % des erreurs de débutant :** toute colonne du `SELECT` doit être soit dans le `GROUP BY`, soit à l'intérieur d'une fonction d'agrégation. Si vous écrivez `SELECT customer_state, customer_city, COUNT(*) ... GROUP BY customer_state`, la ville affichée sera une ville arbitraire parmi celles du groupe. SQLite l'accepte silencieusement ; PostgreSQL refuserait la requête. Ne comptez pas sur l'outil pour vous protéger.

### 2.4 Un exemple qui dit quelque chose

```sql
SELECT payment_type,
       COUNT(*)                   AS nb,
       ROUND(AVG(payment_value),2) AS montant_moyen
FROM order_payments
GROUP BY payment_type
ORDER BY nb DESC;
```

Résultat :

| payment_type | nb | montant_moyen |
|---|---|---|
| credit_card | 2 278 | 167,33 |
| boleto | 634 | 143,77 |
| voucher | 154 | 64,45 |
| debit_card | 49 | 111,12 |

Trois lectures immédiates : la carte domine largement ; le *boleto* (titre de paiement bancaire brésilien) reste très utilisé ; les bons d'achat portent des montants deux fois et demie plus faibles, ce qui est cohérent avec leur usage de complément.

Notez que le total des lignes (3 115) dépasse le nombre de commandes (3 000) : une commande peut être réglée en plusieurs paiements. Encore le même piège.

---

## 3. Joindre

### 3.1 Le principe

Une jointure recolle deux tables sur la correspondance entre une clé étrangère et une clé primaire.

```sql
SELECT <colonnes>
FROM table_a AS a
JOIN table_b AS b ON a.cle = b.cle;
```

`AS a` donne un **alias** à la table, ce qui évite de réécrire son nom entier. Devant chaque colonne, préfixez par l'alias : `a.order_id`. Ce n'est obligatoire que si le nom existe dans les deux tables, mais prenez l'habitude de toujours le faire — cela rend la requête lisible.

### 3.2 Deux tables : le montant de chaque commande

```sql
SELECT o.order_id,
       ROUND(SUM(i.price + i.freight_value), 2) AS montant_total
FROM orders AS o
JOIN order_items AS i ON o.order_id = i.order_id
GROUP BY o.order_id
ORDER BY montant_total DESC
LIMIT 5;
```

Résultat :

| order_id | montant_total |
|---|---|
| df85c824523500d4066a0dbdb5d95bed | 3 602,47 |
| 586992f50ed97898737b07970376d19c | 3 016,01 |
| abb71d0110c4be1a1a0c52f929cab4a3 | 2 838,22 |
| 3745e06e7f01e1652cfd4b9d31651820 | 2 734,66 |
| acf01c9262ddb5d9adae8daa34e31568 | 2 416,00 |

La plus grosse commande vaut 3 602,47 BRL.

**Ce chiffre est important pour la suite de la formation.** C'est exactement la valeur maximale que vous retrouverez demain dans le fichier tableur. Même donnée, deux outils, même résultat : c'est la vérification que la chaîne est cohérente.

### 3.3 Trois tables : suivre le chemin des clés

Je veux le chiffre d'affaires par état du client. Trois tables : `order_items` pour les montants, `orders` pour faire le lien, `customers` pour l'état.

```sql
SELECT c.customer_state,
       COUNT(DISTINCT o.order_id)               AS nb_commandes,
       ROUND(SUM(i.price + i.freight_value), 2) AS chiffre_affaires
FROM orders AS o
JOIN customers   AS c ON o.customer_id = c.customer_id
JOIN order_items AS i ON o.order_id    = i.order_id
GROUP BY c.customer_state
ORDER BY chiffre_affaires DESC
LIMIT 5;
```

Résultat :

| customer_state | nb_commandes | chiffre_affaires |
|---|---|---|
| SP | 1 228 | 180 110,55 |
| RJ | 402 | 72 956,16 |
| MG | 323 | 59 525,25 |
| RS | 176 | 25 903,96 |
| PR | 149 | 23 152,76 |

**Notez `COUNT(DISTINCT o.order_id)` et non `COUNT(*)`.** La jointure avec `order_items` a multiplié les lignes : un `COUNT(*)` compterait les articles, pas les commandes. C'est l'erreur numéro un, et nous y revenons en section 5.

### 3.4 La méthode pour écrire une jointure

Ne cherchez pas à écrire la requête d'un coup. Procédez ainsi, c'est la méthode à retenir :

1. **Quelle information finale je veux ?** Le chiffre d'affaires par état.
2. **Dans quelles tables vivent ces informations ?** Le montant dans `order_items`, l'état dans `customers`.
3. **Quel chemin relie ces tables ?** `order_items` → `orders` (par `order_id`) → `customers` (par `customer_id`). Il n'y a pas de lien direct entre `order_items` et `customers` : il faut passer par `orders`.
4. **J'écris d'abord le `FROM ... JOIN` seul, avec `LIMIT 5`,** et je regarde si les lignes ressemblent à ce que j'attends.
5. **J'ajoute ensuite l'agrégation.**

Cette progression par étapes est ce qui distingue quelqu'un qui écrit du SQL de quelqu'un qui le devine.

---

## 4. `WHERE` contre `HAVING`, `JOIN` contre `LEFT JOIN`

### 4.1 `WHERE` filtre des lignes, `HAVING` filtre des groupes

```sql
SELECT c.customer_state, COUNT(*) AS nb
FROM orders AS o
JOIN customers AS c ON o.customer_id = c.customer_id
GROUP BY c.customer_state
HAVING COUNT(*) >= 100
ORDER BY nb DESC;
```

Résultat : 7 états seulement dépassent 100 commandes.

| customer_state | nb |
|---|---|
| SP | 1 228 |
| RJ | 402 |
| MG | 323 |
| RS | 176 |
| PR | 149 |
| SC | 120 |
| BA | 111 |

`HAVING COUNT(*) >= 100` était impossible à écrire dans un `WHERE` : au moment où `WHERE` s'applique, les groupes n'existent pas encore.

**La règle :** `WHERE` s'applique **avant** le regroupement, sur les lignes individuelles. `HAVING` s'applique **après**, sur les groupes constitués. Si votre condition porte sur un résultat d'agrégation, c'est `HAVING`. Sinon, c'est `WHERE` — et c'est préférable, car filtrer tôt fait moins de travail.

### 4.2 `JOIN` contre `LEFT JOIN`

`JOIN` (ou `INNER JOIN`) ne garde que les lignes qui ont une correspondance des deux côtés. `LEFT JOIN` garde **toutes** les lignes de la table de gauche, et remplit avec `NULL` quand il n'y a pas de correspondance.

L'exemple parlant est celui des 36 produits sans catégorie. Comparez :

```sql
-- combien de produits ont une catégorie traduite ?
SELECT COUNT(*)
FROM products AS p
JOIN product_category_name_translation AS t
  ON p.product_category_name = t.product_category_name;
```

```sql
-- combien de produits au total, catégorie ou non ?
SELECT COUNT(*)
FROM products AS p
LEFT JOIN product_category_name_translation AS t
  ON p.product_category_name = t.product_category_name;
```

La première renvoie **2 452**, la seconde **2 488**. La différence, ce sont exactement les 36 produits sans catégorie, qui **disparaissent silencieusement** du premier résultat.

**C'est le danger réel du `JOIN` :** il ne produit pas d'erreur, il produit un résultat plus petit. Personne ne s'en aperçoit, et un rapport annonce un chiffre d'affaires amputé de ce qui n'était pas catégorisé.

**Règle de conduite :** quand vous joignez pour enrichir (ajouter un libellé, une catégorie, une région), utilisez `LEFT JOIN`. Quand vous joignez pour restreindre volontairement, utilisez `JOIN` et sachez que vous restreignez.

---

## 5. Les trois erreurs classiques

### Erreur 1 — la multiplication des lignes

```sql
SELECT COUNT(*) FROM orders;                                 -- 3 000
SELECT COUNT(*) FROM orders o
JOIN order_items i ON o.order_id = i.order_id;               -- 3 418
```

La jointure a créé 418 lignes supplémentaires, une par article au-delà du premier dans une commande. Toute somme ou moyenne calculée après cette jointure porte sur 3 418 lignes, pas sur 3 000 commandes.

Concrètement : 300 commandes de notre base contiennent plusieurs articles. Si vous calculez une « moyenne du prix des articles » après jointure, vous obtenez la moyenne par article (122,51 BRL), pas la moyenne par commande. Les deux sont justes, elles répondent à deux questions différentes, et le désastre vient de les confondre dans une phrase qui dit « le panier moyen ».

**Parade :** demandez-vous toujours *une ligne de mon résultat, c'est quoi ?* Un article ? Une commande ? Un client ? Et utilisez `COUNT(DISTINCT ...)` dès que la réponse n'est pas l'unité de la table de départ.

### Erreur 2 — compter un identifiant qui n'est pas unique

Déjà rencontré : `COUNT(customer_id)` donne 3 000, `COUNT(DISTINCT customer_unique_id)` donne 2 997.

Autre cas dans notre base : les avis. `order_reviews` contient 3 021 lignes pour 3 000 commandes, parce que **20 commandes ont reçu deux avis**. Une note moyenne calculée en joignant les avis aux commandes surpondère discrètement ces 20 commandes.

**Parade :** avant de compter, vérifiez que la colonne est bien unique dans la table.

```sql
SELECT COUNT(*), COUNT(DISTINCT order_id) FROM order_reviews;
```

Si les deux nombres diffèrent, vous savez ce qui vous attend.

### Erreur 3 — la moyenne de moyennes

Si vous calculez la note moyenne par état, puis la moyenne de ces 26 moyennes, vous **n'obtenez pas** la note moyenne globale. Un état à 3 commandes pèserait autant que São Paulo et ses 1 228.

La note moyenne globale réelle de notre base :

```sql
SELECT COUNT(*) AS nb_avis, ROUND(AVG(review_score), 2) AS note_moyenne
FROM order_reviews;
```

Résultat : **3 021 avis, note moyenne 4,12**.

**Parade :** une moyenne ne se moyenne pas. Repartez toujours des lignes élémentaires, ou pondérez explicitement par les effectifs.

### Se vérifier soi-même : trois réflexes

1. **L'ordre de grandeur.** Vous attendiez environ 3 000 lignes et vous en avez 300 000 ? Vous avez oublié une condition de jointure. Le produit cartésien est la faute la plus spectaculaire, et la plus facile à repérer.
2. **Le total de contrôle.** Vous savez que le chiffre d'affaires total vaut 487 545,87 BRL. Si vous découpez par état, la somme des morceaux doit redonner ce total. Sinon, vous avez perdu des lignes — presque toujours par un `JOIN` qui aurait dû être un `LEFT JOIN`.
3. **Le cas extrême.** Regardez la plus grande et la plus petite valeur de votre résultat. Un montant négatif, une moyenne de 0, un état nommé `NULL` : ce sont des signaux, pas des détails.

---

## 6. Un exemple complet, qui répond à une vraie question

Terminons par une requête qui relie ce module au module 1. La question métier : **l'insatisfaction client vient-elle du produit ou de la logistique ?**

```sql
SELECT CASE
         WHEN julianday(o.order_delivered_customer_date)
            > julianday(o.order_estimated_delivery_date)
         THEN 'en retard' ELSE 'a l heure'
       END                              AS livraison,
       COUNT(*)                         AS nb_avis,
       ROUND(AVG(r.review_score), 2)    AS note_moyenne
FROM orders AS o
JOIN order_reviews AS r ON o.order_id = r.order_id
GROUP BY livraison;
```

Résultat :

| livraison | nb_avis | note_moyenne |
|---|---|---|
| a l heure | 2 807 | **4,26** |
| en retard | 214 | **2,25** |

Une commande livrée en retard reçoit en moyenne **2,25 sur 5**, contre 4,26 quand la promesse est tenue. L'écart est énorme et la conclusion est directe : dans cette entreprise, l'insatisfaction est d'abord logistique, pas produit.

Voilà à quoi sert SQL. Sept lignes de code, une question métier tranchée, et un plan d'action qui change complètement selon la réponse.

Deux remarques de méthode sur cette requête :

- `CASE WHEN ... THEN ... ELSE ... END` crée une catégorie à la volée. C'est l'équivalent SQL d'un `SI` de tableur.
- `julianday()` convertit une date en nombre de jours, ce qui permet de la comparer ou de la soustraire. C'est propre à SQLite ; les autres systèmes ont leurs propres fonctions de date.

### Un avertissement sur les dates, qui vaut pour toute la formation

Calculons le délai de livraison moyen de deux façons :

```sql
SELECT ROUND(AVG(julianday(order_delivered_customer_date)
                - julianday(order_purchase_timestamp)), 2)
FROM orders;
```
Résultat : **12,54 jours**.

Demain, dans le fichier tableur, vous lirez que le délai moyen est de **12,07 jours**.

Les deux sont justes. Le SQL compte les fractions de journée ; le fichier tableur contient un nombre de jours entiers, obtenu en tronquant. « Trois jours et vingt-trois heures » devient 3 dans un cas, 3,96 dans l'autre.

Retenez la leçon plus que le chiffre : **un écart entre deux chiffres n'est pas forcément une erreur, c'est souvent une différence de définition**. C'est exactement la première des quatre questions du module 1 — demander la définition. Quand deux services annoncent deux délais moyens différents, c'est neuf fois sur dix cela qui se cache derrière, et non une faute de calcul.

---

## Ce qu'il faut retenir du module 3

1. Une requête suit toujours l'ordre `SELECT / FROM / WHERE / GROUP BY / HAVING / ORDER BY / LIMIT`.
2. `GROUP BY` transforme *n* lignes en une ligne par groupe ; toute colonne du `SELECT` doit être regroupée ou agrégée.
3. Une jointure suit le chemin des clés. Écrivez-la par étapes : le `FROM ... JOIN` avec `LIMIT` d'abord, l'agrégation ensuite.
4. `WHERE` filtre des lignes avant le regroupement, `HAVING` filtre des groupes après.
5. `JOIN` fait disparaître silencieusement ce qui n'a pas de correspondance. Pour enrichir, utilisez `LEFT JOIN`.
6. Les trois erreurs classiques : la multiplication des lignes par la jointure, le comptage d'un identifiant non unique, la moyenne de moyennes.
7. Vérifiez-vous par l'ordre de grandeur, le total de contrôle et le cas extrême. Le chiffre d'affaires total de notre base vaut 487 545,87 BRL : c'est votre total de contrôle.

---

## Vocabulaire du module 3

**Requête** : instruction adressée à la base pour lire ou modifier des données.
**Clause** : chaque partie d'une requête (`SELECT`, `FROM`, `WHERE`...).
**Alias (`AS`)** : nom court donné à une table ou à une colonne de résultat.
**Fonction d'agrégation** : `COUNT`, `SUM`, `AVG`, `MIN`, `MAX`. Réduit plusieurs lignes à une valeur.
**`GROUP BY`** : regroupement produisant une ligne de résultat par valeur distincte.
**`HAVING`** : condition portant sur les groupes, appliquée après le regroupement.
**`JOIN` / `INNER JOIN`** : jointure ne conservant que les correspondances des deux côtés.
**`LEFT JOIN`** : jointure conservant toutes les lignes de gauche, avec `NULL` à droite si pas de correspondance.
**`DISTINCT`** : ne conserve que les valeurs différentes.
**`NULL`** : absence de valeur. Se teste avec `IS NULL`, jamais avec `=`.
**Produit cartésien** : résultat d'une jointure sans condition, qui multiplie les effectifs des deux tables.
**`CASE WHEN`** : expression conditionnelle, équivalent du `SI` d'un tableur.

