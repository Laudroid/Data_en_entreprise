# Module 7 — Quartiles, distribution et valeurs aberrantes

**Jour 2 · 14 h 30 – 15 h 20 · 50 minutes**, suivi de l'atelier 6 (50 minutes de construction) et de la restitution orale.

> Toutes les valeurs portent sur `transactions-ecommerce.csv`, les 3 000 commandes que vous manipulez.

---

## Objectifs pédagogiques

À la fin de ce module, l'étudiant est capable de :

1. Calculer et interpréter les quartiles, et produire un résumé à cinq nombres.
2. Construire un intervalle interquartile et expliquer pourquoi il est robuste.
3. Lire une boîte à moustaches.
4. Reconnaître les trois formes de distribution et ce que chacune implique.
5. Appliquer la règle de Tukey et, surtout, décider quoi faire des valeurs qu'elle signale.
6. Rédiger une fiche d'identité de variable et le paragraphe d'interprétation qui l'accompagne.

---

## Minutage interne

| Temps | Séquence |
|---|---|
| 12 min | 1. Quartiles et résumé à cinq nombres |
| 8 min | 2. IQR et boîte à moustaches |
| 10 min | 3. Lire une distribution |
| 12 min | 4. Valeurs aberrantes : détecter, puis décider |
| 8 min | 5. La fiche d'identité et son paragraphe |

---

## 1. Quartiles et résumé à cinq nombres

### 1.1 Les quartiles

Les **quartiles** découpent la série triée en quatre parts d'effectifs égaux.

- **Q1** : 25 % des valeurs sont en dessous.
- **Q2** : 50 % en dessous — c'est la médiane.
- **Q3** : 75 % en dessous, donc 25 % au-dessus.

Sur nos 3 000 transactions :

| Indicateur | Valeur | Lecture |
|---|---|---|
| Minimum | 15,78 BRL | la plus petite commande |
| **Q1** | **62,82 BRL** | un quart des commandes sont sous 62,82 BRL |
| **Q2 (médiane)** | **107,95 BRL** | la moitié sont sous 107,95 BRL |
| **Q3** | **182,19 BRL** | trois quarts sont sous 182,19 BRL |
| Maximum | 3 602,47 BRL | la plus grosse commande |

Ces cinq nombres forment le **résumé à cinq nombres**. C'est le meilleur résumé d'une variable quantitative qui tienne sur une ligne, et il dit bien plus qu'une moyenne.

### 1.2 Ce que ce résumé raconte, sans aucune formule

Regardez les écarts.

- La **moitié centrale** des commandes tient entre 62,82 et 182,19 BRL, soit un intervalle de **119 BRL** de large.
- Le **quart supérieur** s'étale de 182,19 à 3 602,47 BRL, soit **3 420 BRL** de large.

Le dernier quart occupe **vingt-huit fois plus d'espace** que la moitié du milieu.

Voilà l'asymétrie, rendue visible par une simple soustraction. Vous n'avez eu besoin ni d'écart-type, ni de coefficient d'asymétrie : cinq nombres et deux soustractions.

### 1.3 Les déciles, quand on veut voir plus fin

Le même principe avec dix parts. Utile quand la queue de distribution est le sujet.

| Décile | Montant |
|---|---|
| D1 | 41,35 BRL |
| D2 | 55,95 |
| D3 | 69,59 |
| D4 | 87,75 |
| **D5 (médiane)** | **107,95** |
| D6 | 133,75 |
| D7 | 163,41 |
| D8 | 207,74 |
| D9 | 313,60 BRL |

Et maintenant le calcul qui intéresse un directeur commercial : **quelle part du chiffre d'affaires chaque tranche de 10 % des commandes apporte-t-elle ?**

| Tranche de commandes | Part du chiffre d'affaires |
|---|---|
| Les 10 % les plus petites | 1,98 % |
| 2e décile | 2,95 % |
| 3e décile | 3,85 % |
| 4e décile | 4,83 % |
| 5e décile | 6,03 % |
| 6e décile | 7,39 % |
| 7e décile | 9,10 % |
| 8e décile | 11,26 % |
| 9e décile | 15,31 % |
| **Les 10 % les plus grosses** | **37,30 %** |

Les 10 % de commandes les plus grosses apportent **plus du tiers** du chiffre d'affaires, soit dix-neuf fois la contribution des 10 % les plus petites.

C'est la même réalité que la moyenne et la médiane décrivaient déjà, mais exprimée dans la langue que comprend un décideur : pas « la distribution est asymétrique », mais « un dixième de vos commandes fait un tiers de votre argent ».

### 1.4 Une note technique sur le calcul

Il existe plusieurs conventions de calcul des quartiles, qui donnent des résultats légèrement différents sur de petits effectifs. Les tableurs et Looker Studio utilisent l'**interpolation linéaire** ; c'est la convention retenue ici. Sur 3 000 valeurs, l'écart entre conventions est négligeable.

Si un jour vos quartiles diffèrent de ceux d'un collègue sur un petit jeu de données, cherchez de ce côté avant de conclure à une erreur.

---

## 2. Intervalle interquartile et boîte à moustaches

### 2.1 L'IQR

L'**intervalle interquartile** (IQR, pour InterQuartile Range) est l'étendue de la moitié centrale :

$$IQR = Q3 - Q1 = 182{,}19 - 62{,}82 = \mathbf{119{,}37\ BRL}$$

C'est un indicateur de dispersion **robuste** : il ne dépend pas du tout des valeurs extrêmes. Vous pouvez remplacer la plus grosse commande par un million, l'IQR ne bougera pas d'un centime. L'écart-type, lui, exploserait.

**Deux mesures de dispersion, deux usages :**

| | Écart-type | IQR |
|---|---|---|
| Sensible aux extrêmes | oui | non |
| Utilise toute l'information | oui | non, seulement deux points |
| Bon quand | la distribution est symétrique | la distribution est asymétrique |
| Propriétés mathématiques | riches | limitées |

### 2.2 La boîte à moustaches

La **boîte à moustaches** (ou *boxplot*) est la représentation graphique du résumé à cinq nombres. C'est le graphique le plus dense en information par centimètre carré de toute la statistique descriptive, et il est très peu utilisé en entreprise — à tort.

Voici celui de nos montants :

```
   Q1      Q2        Q3                          valeurs signalées
15,78 62,82 107,95 182,19   361,25                          3 602,47
  |-----[===|=========]------------|    o  o o   o    o        o
  |      ^            ^            ^
  moustache basse   boîte      moustache haute
                                (Q3 + 1,5 x IQR)
```

Comment la lire :

- **la boîte** va de Q1 à Q3, elle contient la moitié centrale des données ;
- **le trait dans la boîte** est la médiane ;
- **les moustaches** s'étendent jusqu'à la valeur la plus éloignée qui reste dans les seuils de Tukey ;
- **les points isolés** au-delà sont les valeurs signalées comme à examiner.

Ce que l'oeil voit immédiatement sur notre boîte : la médiane n'est pas au centre de la boîte, elle est décalée vers la gauche, et la moustache droite est beaucoup plus longue que la gauche, avec une traîne de points isolés. **Asymétrie à droite**, diagnostic posé en une seconde.

C'est aussi le graphique idéal pour **comparer des groupes** : quatre boîtes côte à côte, une par état, et vous voyez d'un coup les différences de niveau et de dispersion. Une comparaison de moyennes ne montrerait que le niveau.

---

## 3. Lire une distribution

### 3.1 L'histogramme

La **distribution** d'une variable, c'est la façon dont ses valeurs se répartissent. On la lit sur un **histogramme** : on découpe en tranches, on compte.

| Tranche (BRL) | Effectif | Part | |
|---|---|---|---|
| 0 à 50 | 500 | 16,7 % | `████████` |
| 50 à 100 | 882 | 29,4 % | `███████████████` |
| 100 à 150 | 599 | 20,0 % | `██████████` |
| 150 à 200 | 382 | 12,7 % | `██████` |
| 200 à 300 | 313 | 10,4 % | `█████` |
| 300 à 500 | 204 | 6,8 % | `███` |
| 500 à 1 000 | 88 | 2,9 % | `█` |
| 1 000 et plus | 32 | 1,1 % | `▌` |

La forme est caractéristique : un pic rapide entre 50 et 100 BRL, puis une décroissance qui n'en finit pas. On appelle cela une **queue longue** (*long tail*). Elle s'étend jusqu'à 3 602 BRL avec très peu d'individus, mais ces individus pèsent lourd en valeur — 37 % du chiffre d'affaires pour le dernier décile, on vient de le voir.

### 3.2 Attention au découpage

Le même jeu de données, découpé en tranches de 500 BRL, donnerait un histogramme où 97 % des commandes tomberaient dans la première barre. Le même, découpé en tranches de 10 BRL, donnerait un graphique illisible.

**Le découpage n'est pas neutre : il fabrique ce que vous voyez.** Choisissez des tranches qui ont un sens métier — ici, des paliers de 50 BRL dans la zone dense, puis des tranches plus larges dans la queue — et indiquez toujours le découpage retenu.

### 3.3 Les trois formes à savoir reconnaître

**Symétrique en cloche.** Moyenne ≈ médiane. La règle des écarts-types s'applique. Exemple : les tailles humaines adultes d'un même sexe.

**Asymétrique à droite.** Moyenne > médiane, queue longue à droite. Exemple : montants d'achat, salaires, temps d'attente, trafic web. **C'est notre cas, et de très loin le plus fréquent en entreprise.**

**Bimodale — deux pics.** Signe presque certain que **deux populations différentes sont mélangées** dans la même table.

La distribution bimodale mérite un arrêt, parce qu'elle est le piège le plus vicieux. Si vous mélangez dans un même fichier des commandes de particuliers et des commandes de professionnels, vous verrez deux pics. La moyenne se placera entre les deux, **dans le creux, là où il n'y a presque personne**. Vous obtiendrez un chiffre qui ne décrit aucun client réel.

**Le premier réflexe devant tout histogramme est donc de compter les pics.** Un seul : vous pouvez continuer. Deux : arrêtez tout et cherchez la variable qui sépare les deux populations.

---

## 4. Valeurs aberrantes : détecter, puis décider

### 4.1 La règle de Tukey

Une **valeur aberrante** (*outlier*) est une valeur suffisamment éloignée du reste pour mériter un examen.

La règle la plus utilisée, due à John Tukey, définit deux seuils :

$$\text{seuil bas} = Q1 - 1{,}5 \times IQR \qquad \text{seuil haut} = Q3 + 1{,}5 \times IQR$$

Appliquée à nos données :

- seuil bas = 62,82 − 1,5 × 119,37 = **−116,24 BRL**
- seuil haut = 182,19 + 1,5 × 119,37 = **361,25 BRL**

Le seuil bas est négatif, donc **aucune transaction n'est aberrante par le bas**. C'est logique : un montant ne peut pas être négatif. Ce résultat, en apparence décevant, est une information : il n'y a pas de micro-transactions anormales dans ce jeu.

Le seuil haut donne :

> **218 transactions dépassent 361,25 BRL, soit 7,27 % des commandes.**

**Pourquoi 1,5 ?** C'est une convention, pas une loi de la nature. Elle produit, sur une distribution en cloche, environ 0,7 % de valeurs signalées — suffisamment peu pour être examinables à la main. Avec un coefficient de 3, on ne signale que les valeurs extrêmes. Le choix vous appartient, à condition de l'annoncer.

### 4.2 Et maintenant, la vraie question : qu'en fait-on ?

C'est ici que l'on sépare le calcul de l'analyse. Le réflexe qu'on voit trop souvent est « la règle dit que ce sont des aberrations, donc je les supprime ». Regardons ce que cela ferait.

| Indicateur | Toutes les commandes | Sans les 218 « aberrantes » |
|---|---|---|
| Effectif | 3 000 | 2 782 |
| Moyenne | 162,52 BRL | 119,75 BRL |
| Médiane | 107,95 BRL | 101,14 BRL |

Retirer 7,27 % des lignes fait chuter la moyenne de 26 % — et la médiane de seulement 6 %. Vous reconnaissez la robustesse de la médiane, mesurée cette fois sur les vraies données.

Et surtout :

> **Ces 218 commandes représentent 154 405,48 BRL, soit 31,67 % du chiffre d'affaires total (487 545,87 BRL).**

Supprimer ces lignes, c'est **effacer près d'un tiers du chiffre d'affaires de l'entreprise**. Ce ne sont pas des erreurs, ce sont ses meilleurs clients.

La liste des dix plus grosses commandes le confirme : un ordinateur à 3 602 BRL, une montre à 3 016 BRL, un produit de beauté à 2 838 BRL, un instrument de musique à 2 416 BRL. Toutes sont des commandes d'**un seul article cher**. Rien d'anormal, juste du haut de gamme.

### 4.3 La leçon, qui est le coeur du module

**Une valeur aberrante au sens statistique n'est pas une erreur.** La règle de Tukey ne détecte pas des fautes, elle **signale des valeurs à examiner**. Trois issues possibles après examen :

**1. C'est une erreur.** Montant négatif, date impossible, quantité de 9 999, doublon de saisie. On corrige ou on exclut, **en documentant ce qu'on a fait**.

**2. C'est réel et hors sujet pour la question posée.** Exemple légitime : pour calibrer un emballage standard, on écarte les commandes de gros volume, qui relèvent d'un autre processus. On exclut **en le disant**.

**3. C'est réel et central.** C'est notre cas. 7 % des commandes qui portent 32 % du chiffre d'affaires méritent leur propre analyse, leur propre traitement commercial, peut-être leur propre logistique. **On n'exclut pas : on segmente.**

### 4.4 Ce qu'aucune règle ne détectera jamais

Point important, et rarement enseigné.

La règle de Tukey ne repère que les valeurs **extrêmes**. Elle est aveugle à tout ce qui est anormal sans être extrême :

- un montant de 0,00 dans une colonne où il ne devrait pas y en avoir ;
- une date de livraison **antérieure** à la date d'achat ;
- un état client qui n'existe pas dans la nomenclature ;
- trois cents commandes identiques passées à la même seconde.

Toutes ces anomalies se situent à l'intérieur de la plage de valeurs plausibles. **Aucun seuil statistique ne les signalera.** Seule la connaissance du métier les repère.

Retenez la formule : **une valeur peut être anormale sans être extrême.** La détection par les seuils est un filet à grosses mailles, pas un contrôle de qualité.

### 4.5 La règle de conduite

**Toute exclusion de données se documente** : combien de lignes, sur quel critère, avec quel effet sur les résultats.

Une analyse dont on ne peut pas reconstituer le périmètre n'est pas vérifiable, donc elle ne vaut rien. Cette phrase vaut pour votre rapport de stage comme pour un rapport d'audit.

---

## 5. La fiche d'identité d'une variable

### 5.1 Le protocole en sept lignes

Voici ce qu'il faut produire devant toute nouvelle variable quantitative, avant toute analyse. Sept lignes, dix minutes, et vous savez à qui vous avez affaire.

| Ligne | Indicateur | `montant_total_brl` |
|---|---|---|
| 1 | Effectif et valeurs manquantes | 3 000, aucune manquante |
| 2 | Minimum et maximum | 15,78 et 3 602,47 BRL |
| 3 | Moyenne | 162,52 BRL |
| 4 | Médiane | 107,95 BRL |
| 5 | Écart-type et CV | 215,65 BRL, soit 132,7 % |
| 6 | Q1, Q3, IQR | 62,82 / 182,19 / 119,37 |
| 7 | Valeurs hors seuils de Tukey | 218, soit 7,27 % |

### 5.2 Le paragraphe d'interprétation

Et voici ce que ces sept lignes permettent d'écrire, en quatre phrases qu'un directeur commercial peut lire :

> Sur les 3 000 commandes livrées de l'échantillon, le montant médian est de 107,95 BRL, tandis que la moyenne s'établit à 162,52 BRL : sept commandes sur dix sont inférieures à cette moyenne, qui n'est donc pas représentative d'une commande typique. La dispersion est très élevée — écart-type de 215,65 BRL, supérieur à la moyenne elle-même — signe d'une clientèle hétérogène. Un groupe de 218 commandes dépassant 361 BRL, soit 7,3 % du total, concentre à lui seul 31,7 % du chiffre d'affaires. Nous recommandons de piloter l'activité sur la médiane et de traiter ce segment à forte valeur comme une population distincte, avec son propre suivi.

Comparez à la phrase qu'on aurait écrite en s'arrêtant à la moyenne : « le panier moyen est de 162,52 BRL ».

Même jeu de données, mêmes dix minutes de calcul, et une conclusion qui n'a rien à voir. C'est tout l'enjeu de ces deux journées — et c'est exactement ce que la restitution orale de tout à l'heure évaluera.

### 5.3 Les sept erreurs à ne plus commettre

1. Présenter une moyenne sans sa médiane ni sa dispersion.
2. Supprimer les valeurs aberrantes sans les avoir examinées, et sans le documenter.
3. Calculer une moyenne sur une variable qualitative codée par des numéros.
4. Confondre la population des commandes et celle des clients.
5. Appliquer la règle des écarts-types à une distribution asymétrique.
6. Conclure sur un histogramme bimodal sans avoir séparé les deux populations.
7. Donner un pourcentage sans son effectif de référence.

---

## Ce qu'il faut retenir du module 7

1. Le résumé à cinq nombres — min, Q1, médiane, Q3, max — dit bien plus qu'une moyenne, et l'asymétrie s'y lit par simple soustraction.
2. Les déciles traduisent la distribution dans la langue du décideur : 10 % des commandes font 37 % du chiffre d'affaires.
3. L'IQR est robuste : il ne dépend pas des valeurs extrêmes. La boîte à moustaches en est la représentation.
4. Le découpage d'un histogramme fabrique ce que vous voyez : annoncez-le toujours.
5. Deux pics dans un histogramme signifient deux populations mélangées. Arrêtez et séparez.
6. La règle de Tukey signale des valeurs à examiner, elle ne détecte pas des erreurs. Ici, 7,27 % des commandes font 31,67 % du chiffre d'affaires.
7. Une valeur peut être anormale sans être extrême : aucun seuil ne remplace la connaissance du métier.
8. Toute exclusion se documente : combien, sur quel critère, avec quel effet.

---

## Vocabulaire du module 7

**Quartiles (Q1, Q2, Q3)** : valeurs découpant la série en quatre parts d'effectifs égaux.
**Déciles** : même principe avec dix parts.
**Résumé à cinq nombres** : minimum, Q1, médiane, Q3, maximum.
**Intervalle interquartile (IQR)** : Q3 − Q1, dispersion de la moitié centrale.
**Boîte à moustaches** : représentation graphique du résumé à cinq nombres.
**Distribution** : répartition des valeurs d'une variable.
**Histogramme** : graphique de la distribution par tranches.
**Queue longue** : partie étalée d'une distribution asymétrique, peu d'individus mais poids important.
**Distribution bimodale** : distribution à deux pics, signe d'un mélange de deux populations.
**Règle de Tukey** : seuils Q1 − 1,5 IQR et Q3 + 1,5 IQR délimitant les valeurs à examiner.
**Valeur aberrante (outlier)** : valeur éloignée du reste, à examiner et non à supprimer par principe.
**Indicateur robuste** : indicateur insensible aux valeurs extrêmes (médiane, IQR).


