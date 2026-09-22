# Atelier 3 — Modéliser un schéma de données

**Jour 1 · 16 h 30 – 17 h 00 · 30 minutes. Binômes. Un ordinateur.**
**Module de rattachement : module 4.**
**Outil : https://dbdiagram.io/home (utilisable sans compte pour dessiner).**

---

## Contexte

Une association de producteurs locaux vous demande de concevoir la base de données de sa future plateforme de vente. Voici ce qu'elle vous décrit, en ses propres mots, lors du premier rendez-vous.

> « Nous avons une trentaine de producteurs. Chacun propose plusieurs produits. Nos clients passent des commandes, et une commande peut contenir des produits de plusieurs producteurs à la fois. Pour chaque produit d'une commande, on doit connaître la quantité commandée et le prix pratiqué ce jour-là, parce que nos prix changent avec les saisons. Les clients retirent leur commande dans l'un de nos quatre points de retrait, et on doit savoir lequel, avec le créneau horaire choisi. Après le retrait, le client peut noter chaque producteur de sa commande. »

Aucune autre information ne vous sera donnée. Les manques éventuels font partie de l'exercice : vous devrez les signaler.

---

## Étape 1 — Identifier, sur papier (8 minutes)

**Ne touchez pas à l'outil.** Listez :

- les **entités** que vous repérez dans le texte ;
- pour chacune, son **identifiant** ;
- les **associations** entre entités, avec leur **cardinalité** (1-1, 1-N ou N-N).

Méthode vue en cours : les noms communs répétés sont des entités candidates, les verbes qui les relient sont des associations.

Attention : le texte contient au moins une relation **plusieurs-à-plusieurs**. Vous savez ce qu'elle impose.

---

## Étape 2 — Dessiner (17 minutes)

Ouvrez https://dbdiagram.io/home et construisez le schéma en DBML.

Le squelette ci-dessous vous donne la syntaxe et **une seule table à titre d'exemple**. Il est volontairement incomplet : ni les autres tables, ni les liens ne vous sont fournis.

```
// Squelette de depart — a completer
// [pk] marque la cle primaire
// [ref: > autre_table.colonne] cree un lien "plusieurs vers un"

Table producteur {
  id_producteur   integer [pk]
  nom             varchar
  ville           varchar
}

// A vous : ajoutez les autres tables et les liens.
// Pour une cle primaire composee de deux colonnes :
//   indexes { (colonne_a, colonne_b) [pk] }
```

### Exigences sur le schéma

Votre schéma doit satisfaire les points suivants, qui sont tous vérifiables à l'oeil :

- Chaque table possède une clé primaire explicitement marquée.
- Chaque relation plusieurs-à-plusieurs est résolue par une **table intermédiaire**, et cette table porte les attributs qui appartiennent à la rencontre des deux entités.
- **Le prix pratiqué lors de la commande est stocké de telle façon qu'une modification ultérieure du prix du produit ne change pas le montant des commandes déjà passées.** Ce point est le coeur de l'exercice : réfléchissez à l'endroit où ce prix doit vivre.
- Un point de retrait peut exister sans qu'aucune commande n'y soit encore rattachée.
- La notation d'un producteur est rattachée à une commande précise, et non au client seul.
- Aucune information n'est stockée deux fois à deux endroits différents.
- Aucune case ne peut contenir plusieurs valeurs.

---

## Étape 3 — Livrer (5 minutes)

L'enregistrement sur dbdiagram.io demande un compte : **ne perdez pas de temps à en créer un.** Livrez de deux façons :

1. le **code DBML** copié dans un fichier `schema.dbml` ;
2. une **capture d'écran** du diagramme dessiné, nommée `schema.png`.

Ajoutez un fichier `manques.md` listant les informations que le client ne vous a **pas** données et dont vous auriez eu besoin. Au moins deux.

---

## Livrables

Un dossier au nom de votre binôme contenant :

1. **`schema.dbml`**
2. **`schema.png`**
3. **`manques.md`**
4. **`USAGE-IA.md`** — modèle en annexe

---

## Critères de réussite

- [ ] Le diagramme se charge sans erreur de syntaxe sur dbdiagram.io.
- [ ] Toutes les tables ont une clé primaire marquée.
- [ ] Il existe au moins une table intermédiaire résolvant une relation plusieurs-à-plusieurs.
- [ ] **Le prix pratiqué est stocké à un emplacement qui le rend insensible aux changements de prix ultérieurs.**
- [ ] Le point de retrait et le créneau horaire sont représentés, et un point de retrait peut exister sans commande.
- [ ] La notation est rattachée à une commande précise.
- [ ] Aucune colonne ne contient une liste de valeurs.
- [ ] Le fichier `manques.md` contient au moins deux informations réellement absentes du cahier des charges.

---

## Annexe — Fichier `USAGE-IA.md` (obligatoire)

```markdown
# Usage de l'IA — Atelier 3
Binôme : [noms]

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

Si vous demandez à une IA de produire le schéma DBML, consignez précisément ce qu'elle a proposé et ce que vous avez corrigé. Un schéma généré et livré tel quel, sans trace de vérification, sera traité comme un travail non fait.

Le signe le plus visible : un schéma contenant des tables dont l'énoncé ne parle jamais — une gestion de stock complète, une table `categorie`, un champ `sku`. Une IA produit le schéma canonique d'un site marchand, pas celui de **ce** cahier des charges.

---

## Ressources

- dbdiagram.io : https://dbdiagram.io/home
- Documentation de la syntaxe DBML : https://dbml.dbdiagram.io/docs/
