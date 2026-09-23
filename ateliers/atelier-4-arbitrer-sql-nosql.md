# Atelier 4 — Arbitrer entre SQL et NoSQL

**Jour 2 · 10 h 15 – 10 h 45 · 30 minutes. Binômes. Ordinateur facultatif.**
**Module de rattachement : module 5.**

---

## Contexte

Nous reprenons la plateforme de l'association de producteurs locaux, pour laquelle vous avez conçu un schéma relationnel hier. Cinq besoins nouveaux arrivent, **indépendants les uns des autres**.

Pour chacun, vous décidez du stockage : **relationnel** ou **NoSQL** — et dans ce second cas, vous précisez la famille : clé-valeur, document, colonnes larges, ou graphe.

---

## Besoin 1 — Le panier en cours

Quand un client remplit son panier sans valider, il faut conserver le contenu du panier pendant 48 heures pour qu'il le retrouve à sa prochaine visite. Des milliers de paniers en cours à tout instant, lecture et écriture très fréquentes, aucune analyse ne sera faite dessus.

## Besoin 2 — La fiche descriptive des produits

Un fromage a un lait d'origine, un temps d'affinage et un pourcentage de matière grasse. Une confiture a un fruit, un taux de sucre et une date de mise en pot. Un panier composé a une liste de ce qu'il contient. L'association ajoute de nouvelles familles de produits toutes les quelques semaines et veut pouvoir décrire librement chacune.

## Besoin 3 — La comptabilité des règlements

Il faut enregistrer les paiements, les rembourser en cas d'annulation, et produire un état mensuel des sommes dues à chaque producteur. Un écart d'un centime est inacceptable, et un contrôle fiscal peut porter sur trois ans d'historique.

## Besoin 4 — Les capteurs de température des chambres froides

Chaque point de retrait est équipé de six sondes qui relèvent la température toutes les trente secondes. Les relevés sont conservés deux ans pour prouver le respect de la chaîne du froid. On les consulte par plage de temps, presque jamais individuellement, et jamais en les croisant avec les commandes.

## Besoin 5 — Le programme de parrainage

Chaque client peut parrainer d'autres clients, qui peuvent à leur tour en parrainer. L'association veut pouvoir répondre à des questions comme : « quels sont les clients à trois niveaux de parrainage sous Madame Durand ? » et « existe-t-il des boucles de parrainage entre membres d'un même foyer ? »

---

## Travail demandé

### Partie A — Les cinq arbitrages (18 minutes)

Pour chacun des cinq besoins, écrivez **exactement quatre lignes** :

```
Besoin n : [relationnel | NoSQL — famille]
Argument 1 :
Argument 2 :
Ce que ce choix nous coûte :
```

**Contrainte sur les arguments.** Vos deux arguments doivent être pris parmi les quatre questions de la grille de décision vue en cours :

1. les données sont-elles fortement reliées entre elles ?
2. la structure est-elle stable ?
3. une lecture légèrement périmée est-elle acceptable ?
4. le volume dépasse-t-il une grosse machine ?

Un argument du type « c'est plus moderne » ou « c'est plus rapide » sans justification ne compte pas.

**Contrainte sur la quatrième ligne.** « Ce que ce choix nous coûte » n'est pas facultatif. Tout choix de stockage abandonne quelque chose. Si vous n'arrivez pas à nommer ce que vous perdez, c'est que vous n'avez pas vraiment arbitré.

### Partie B — La vue d'ensemble (7 minutes)

Vos cinq décisions, plus le schéma relationnel d'hier, décrivent maintenant le système d'information complet de l'association.

1. **Combien de systèmes de stockage différents** avez-vous au total ? Listez-les.
2. Comment appelle-t-on cette situation, vue en cours ?
3. En **trois lignes**, donnez l'argument que vous opposeriez au trésorier de l'association qui vous dirait : « tout cela est bien compliqué, mettons tout dans une seule base et n'en parlons plus. » Votre argument doit reconnaître qu'il n'a pas complètement tort.

### Partie C — Le piège (5 minutes)

Un prestataire propose à l'association de tout reconstruire sur une base NoSQL document unique, « plus moderne et plus souple ».

En **deux phrases**, énoncez la question précise que vous lui poseriez pour tester le sérieux de sa proposition. Une seule question, celle qui fait le plus mal.

---

## Livrables

Un fichier **`atelier4-arbitrages.md`** au nom de votre binôme, contenant les parties A, B et C, plus un fichier **`USAGE-IA.md`**.

---

## Critères de réussite

- [ ] Les cinq besoins reçoivent une décision tranchée, sans « ça dépend » non résolu.
- [ ] Chaque décision est appuyée sur **deux arguments issus de la grille**, nommés explicitement.
- [ ] Chaque décision nomme **ce qu'elle coûte**.
- [ ] Les cinq décisions ne sont pas identiques entre elles. Si elles le sont, vous avez appliqué une préférence et non une grille.
- [ ] Au moins trois familles NoSQL différentes apparaissent dans vos réponses.
- [ ] La partie B nomme correctement la situation et compte les systèmes.
- [ ] L'argument opposé au trésorier reconnaît explicitement la part de vérité de son objection.
- [ ] La question de la partie C porte sur un besoin réel de l'association, pas sur une généralité technique.

---

## Annexe — Fichier `USAGE-IA.md` (obligatoire)

```markdown
# Usage de l'IA — Atelier 4
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
