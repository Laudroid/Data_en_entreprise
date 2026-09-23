# Quiz flash — reprise du jour 1

**Jour 2 · 9 h 00 – 9 h 10 · 5 minutes de réponse, 5 minutes de correction commentée.**
**Sans support, sans ordinateur. 8 questions.**

Répondez directement sur cette feuille.

---

**1.** Une donnée est :
- a) un fait qui se produit dans l'entreprise
- b) la trace enregistrée d'un fait, dans un format traitable
- c) une information interprétée par un analyste

**2.** À quelle étape du cycle de vie une erreur est-elle la plus difficile à réparer ensuite ?
- a) l'analyse
- b) la diffusion
- c) la collecte

**3.** Dans une relation « un client passe plusieurs commandes », la clé étrangère se place :
- a) dans la table `client`
- b) dans la table `commande`
- c) dans une table intermédiaire

**4.** Notre base contient 3 000 commandes et 3 418 lignes dans `order_items`. Que renvoie cette requête ?

```sql
SELECT COUNT(*) FROM orders o JOIN order_items i ON o.order_id = i.order_id;
```
- a) 3 000
- b) 3 418
- c) 10 254 000

**5.** `WHERE` et `HAVING` : lequel s'applique **après** le regroupement ?
- a) `WHERE`
- b) `HAVING`
- c) les deux, dans cet ordre

**6.** Vous joignez la table des produits à celle des libellés de catégorie pour enrichir un rapport. 36 produits n'ont pas de catégorie. Avec un `JOIN` simple, que se passe-t-il ?
- a) une erreur est levée
- b) les 36 produits apparaissent avec une catégorie vide
- c) les 36 produits disparaissent silencieusement du résultat

**7.** Une relation plusieurs-à-plusieurs se traduit en tables par :
- a) une clé étrangère dans chacune des deux tables
- b) une colonne contenant une liste de valeurs
- c) une table intermédiaire

**8.** Le prix facturé lors d'une commande doit être stocké :
- a) uniquement dans la fiche produit
- b) dans la ligne de commande, figé au moment de l'achat
- c) recalculé à chaque affichage à partir du tarif courant

---

## Question ouverte (facultative, 1 minute)

**9.** En une phrase : complétez la règle vue hier. « On normalise pour \_\_\_\_\_, on dénormalise pour \_\_\_\_\_. »

_______________________________________________
