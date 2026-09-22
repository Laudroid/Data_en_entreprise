# Atelier 1 — Attaquer une décision chiffrée

**Jour 1 · 11 h 05 – 11 h 50 · 45 minutes. Groupes de 3 ou 4. Aucun ordinateur.**
**Module de rattachement : module 1.**

---

## Contexte

Vous êtes analystes junior dans une entreprise de commerce en ligne. Quatre notes internes circulent. Chacune s'appuie sur des chiffres réels et propose une décision. Chacune contient au moins une faille de raisonnement.

Votre rôle n'est pas de dire si la décision est bonne ou mauvaise. Il est de dire **ce que les chiffres présentés ne permettent pas de conclure**, et **quelle information vous réclamez avant d'accepter**.

Vous n'avez pas accès aux données pendant cet atelier. C'est volontaire : l'exercice porte sur le raisonnement, pas sur le calcul.

---

## Note interne n° 1 — Direction marketing

> « Nous avons analysé nos 3 000 dernières commandes livrées. Le panier moyen s'élève à 162,52 BRL. Nous proposons donc de fixer le seuil de livraison gratuite à 160 BRL : ce seuil correspond exactement au comportement de notre client moyen, et incitera la majorité de nos clients à compléter légèrement leur panier pour en bénéficier. »

## Note interne n° 2 — Direction des opérations

> « Notre délai de livraison moyen est de 12 jours. Nos concurrents annoncent 10 jours. Nous lançons donc un plan d'amélioration de la logistique visant à réduire le délai moyen de 2 jours, en renégociant les tarifs avec nos transporteurs sur l'ensemble de nos expéditions. »

## Note interne n° 3 — Direction générale

> « Une étude portant sur 156 pays montre une corrélation de 0,79 entre le niveau de richesse par habitant et le score de bien-être déclaré. La conclusion est claire : la richesse produit le bien-être. Nous en déduisons que notre politique de rémunération est le principal levier de satisfaction de nos collaborateurs, et nous concentrerons nos efforts sur ce seul levier. »

## Note interne n° 4 — Direction de la qualité

> « Sur les 3 021 avis clients recueillis, 78 % attribuent 4 ou 5 étoiles sur 5, et la note moyenne s'établit à 4,12. La satisfaction client est donc solide et ne constitue pas une priorité. Nous proposons de réduire de 30 % le budget du service client l'an prochain. »

---

## Travail demandé

### 1. Analyse critique (25 minutes)

Pour **chacune des quatre notes**, remplissez la grille suivante. Une grille vierge à recopier figure en annexe.

| Rubrique | Ce que vous devez écrire |
|---|---|
| Chiffre invoqué | quel indicateur justifie la décision |
| Faille de raisonnement | en une phrase, ce qui ne tient pas |
| Nom du piège | rattachez-la à un piège vu en cours, ou nommez-la vous-même |
| Question à poser | une question précise, à laquelle un calcul ou une donnée peut répondre |
| Ce qui changerait | si la réponse est celle que vous soupçonnez, en quoi la décision devrait-elle changer |

### 2. Hiérarchisation (5 minutes)

Les quatre notes ne sont pas également dangereuses. **Classez-les de la plus risquée à la moins risquée** pour l'entreprise, et justifiez votre premier choix en deux phrases.

Un critère vous est demandé explicitement : le risque, c'est la gravité de la conséquence multipliée par la probabilité que personne ne s'en aperçoive.

### 3. Restitution (15 minutes)

Chaque groupe présente **une note** à l'oral, en **deux minutes maximum**, dans cet ordre :

1. la faille, en une phrase ;
2. la question qu'il faut poser ;
3. la décision alternative qu'il proposerait.

Le formateur attribue la note à traiter au début de la restitution. Préparez donc les quatre.

---

## Contraintes

- Une critique du type « il faudrait plus de données » est **irrecevable**. Soyez précis : quelle donnée, pour répondre à quelle question.
- Vous ne pouvez pas invoquer un chiffre que vous inventez. Vous pouvez formuler une hypothèse, à condition de l'annoncer comme telle.
- Une critique de forme (« la note est mal rédigée ») ne compte pas. On attaque le raisonnement.

---

## Livrables

À déposer en fin d'atelier, un dossier au nom de votre groupe contenant :

1. **`atelier1-grilles.md`** (ou les grilles papier photographiées) : les quatre grilles complétées.
2. **`atelier1-classement.md`** : le classement des quatre notes par niveau de risque, avec la justification du premier choix.
3. **`USAGE-IA.md`** : voir l'annexe. Obligatoire même si vous n'avez pas utilisé d'IA, auquel cas vous l'indiquez explicitement.

---

## Critères de réussite

Un observateur extérieur doit pouvoir vérifier, sans vous interroger, que :

- [ ] Les quatre notes ont chacune au moins une faille identifiée et formulée en une phrase compréhensible.
- [ ] Chaque faille désigne un **raisonnement**, et non un manque de moyens ou un défaut de forme.
- [ ] Les quatre questions posées sont **calculables** : on peut dire quel chiffre y répondrait.
- [ ] Au moins une faille porte sur la différence entre un indicateur de position et la dispersion réelle.
- [ ] Au moins une faille porte sur le lien entre corrélation et causalité.
- [ ] Au moins une faille porte sur ce qui est **absent** des données.
- [ ] La colonne « ce qui changerait » propose une décision alternative concrète, pas une invitation à réfléchir davantage.
- [ ] Le classement par risque est argumenté sur les conséquences, pas sur l'ampleur de l'erreur de calcul.
- [ ] Le fichier `USAGE-IA.md` est présent et renseigné.

---

## Annexe 1 — Grille vierge à recopier

```
NOTE N° ___

Chiffre invoqué :

Faille de raisonnement (une phrase) :

Nom du piège :

Question à poser (calculable) :

Ce qui changerait si notre soupçon est confirmé :
```

---

## Annexe 2 — Fichier `USAGE-IA.md` (obligatoire)

```markdown
# Usage de l'IA — Atelier 1
Groupe : [noms]

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

Ce fichier est évalué sur sa **sincérité et sa précision**, jamais sur la quantité d'IA utilisée. Un groupe n'ayant pas utilisé d'IA et l'indiquant clairement est pleinement conforme et obtient tous les points.
