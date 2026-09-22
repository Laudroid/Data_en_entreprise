# Module 6 — Statistiques descriptives : population, variables, position, dispersion

**Jour 2 · 10 h 55 – 12 h 30 · 95 minutes**, suivi de l'atelier 5 (60 minutes).
**Aucune statistique inférentielle, aucun test d'hypothèse, aucune probabilité.**

> **Toutes les valeurs de ce module portent sur `transactions-ecommerce.csv`**, l'extrait de 3 000 commandes que vous avez entre les mains. Elles ont été calculées sur ce fichier exact. Vous devez retrouver les mêmes.

---

## Objectifs pédagogiques

À la fin de ce module, l'étudiant est capable de :

1. Distinguer population et échantillon, et dire de quoi il généralise quand il calcule une moyenne.
2. Identifier le type d'une variable et en déduire quels calculs sont licites.
3. Construire et lire un tableau de fréquences.
4. Calculer moyenne, médiane et mode, et expliquer pourquoi elles diffèrent.
5. Calculer une moyenne pondérée et expliquer pourquoi une moyenne de moyennes est fausse.
6. Calculer variance, écart-type et coefficient de variation, et les interpréter dans l'unité des données.
7. Énoncer la règle des écarts-types et dire quand elle ne s'applique pas.

---

## 1. Population, échantillon et variables

### 1.1 Population

La **population** est l'ensemble complet de tous les individus sur lesquels porte la question. Le mot « individu » est technique : ce peut être une personne, mais aussi une commande, un produit, une journée, un capteur.

Le piège est que la population n'est pas donnée par les données : **elle est définie par la question.** Trois questions, trois populations différentes sur le même jeu :

| Question | Population | Individu |
|---|---|---|
| Combien dépense une commande ? | toutes les commandes | une commande |
| Combien dépense un client ? | tous les clients | un client |
| Quel état achète le plus ? | tous les états brésiliens | un état |

Erreur classique et coûteuse : calculer la moyenne des commandes et l'appeler « dépense moyenne par client ». Un client ayant passé trois commandes compte trois fois dans le premier calcul, une seule fois dans le second.

Vous avez déjà rencontré ce piège hier, en SQL : `COUNT(customer_id)` donnait 3 000 et `COUNT(DISTINCT customer_unique_id)` donnait 2 997. Ce sont deux nombres différents qui portent deux noms différents, et les confondre fait dire n'importe quoi à une réunion.

### 1.2 Échantillon

Un **échantillon** est un sous-ensemble de la population, sur lequel on calcule parce qu'on n'a pas accès au tout, ou parce que le tout coûte trop cher à traiter.

Le fichier que vous avez est un échantillon, et il faut le dire précisément : **3 000 commandes tirées au hasard parmi les 96 478 commandes livrées** du jeu Olist, restreintes aux années 2017 et 2018. Le tirage a été fait avec une graine aléatoire fixée, ce qui le rend reproductible à l'identique.

### 1.3 La représentativité, vérifiée

Un échantillon est **représentatif** s'il ressemble à la population sur les caractéristiques qui importent. Vérifions-le honnêtement.

| Indicateur sur le montant | Population (96 478) | Échantillon (3 000) | Écart |
|---|---|---|---|
| Moyenne | 159,83 BRL | 162,52 BRL | +1,7 % |
| Médiane | 105,28 BRL | 107,95 BRL | +2,5 % |
| Écart-type | 218,79 BRL | 215,65 BRL | −1,4 % |
| Q1 | 61,85 BRL | 62,82 BRL | +1,6 % |
| Q3 | 176,26 BRL | 182,19 BRL | +3,4 % |

Les écarts sont de l'ordre de 1 à 3 %. L'échantillon est fidèle. Ce n'est pas un miracle : c'est ce que produit un tirage vraiment aléatoire de taille suffisante.

### 1.4 Le point à retenir plus que tous les autres

**Le danger ne vient presque jamais de la taille de l'échantillon, il vient de la manière dont il a été constitué.**

Un échantillon de 3 000 commandes tirées au hasard est excellent. Un échantillon de 30 000 commandes constitué uniquement des commandes de décembre serait dix fois plus gros et infiniment plus trompeur : il porterait toute la saisonnalité de Noël.

Quand on vous présente un chiffre issu d'un échantillon, la question utile n'est donc pas « combien de personnes avez-vous interrogées ? » mais **« comment les avez-vous choisies ? »**.

### 1.5 Les types de variables

Une **variable** est une caractéristique mesurée sur chaque individu. Dans un tableau, c'est une colonne. Son type détermine ce que l'on a le droit de calculer — raison pour laquelle on commence par là.

**Variables quantitatives** — des nombres sur lesquels les opérations arithmétiques ont un sens.

- **Continues** : toutes les valeurs intermédiaires sont possibles. `montant_total_brl` : 162,52 BRL a un sens.
- **Discrètes** : seules des valeurs isolées, en général entières. `nb_articles` : on ne commande pas 2,7 articles.

**Variables qualitatives** — des catégories.

- **Nominales** : sans ordre. `etat_client` (SP, RJ, MG), `categorie`. On ne peut pas dire que SP est « plus grand » que RJ.
- **Ordinales** : avec un ordre, mais sans écart mesurable. Une note de satisfaction de 1 à 5, une taille S/M/L. On sait que 4 est mieux que 3, mais rien ne garantit que l'écart 3→4 vaut l'écart 4→5.

### 1.6 Le tableau de ce qui est permis

| Calcul | Quantitative | Qualitative ordinale | Qualitative nominale |
|---|---|---|---|
| Effectif, fréquence | oui | oui | oui |
| Mode | oui | oui | oui |
| Médiane, quartiles | oui | oui | **non** |
| Moyenne, écart-type | oui | discutable | **non** |

Le cas « discutable » mérite un mot, parce qu'il est partout dans la vie professionnelle. On calcule constamment des moyennes de notes de satisfaction. « Note moyenne : 4,12 sur 5 » — le chiffre réel de notre base, calculé hier en SQL — est un énoncé fréquent, techniquement contestable, et souvent acceptable en pratique. Sachez que c'est un raccourci, et qu'un écart de 0,1 sur une telle moyenne ne signifie rien de solide.

En revanche, **la moyenne d'une variable nominale n'existe pas**. La moyenne de `etat_client` n'a aucun sens. Cette erreur se produit vraiment, quand un état est codé par un numéro : l'outil calcule sans broncher la moyenne des numéros et sort un nombre parfaitement vide de sens. Vous en verrez un cas cet après-midi, avec la colonne `rang` du jeu du bonheur.

### 1.7 Appliqué à notre fichier

| Colonne | Type | Calculs licites |
|---|---|---|
| `id_commande` | identifiant, pas une variable | aucun calcul |
| `date_achat` | date (ordinale particulière) | min, max, médiane, regroupement par mois |
| `etat_client` | qualitative nominale | effectif, mode |
| `categorie` | qualitative nominale | effectif, mode |
| `nb_articles` | quantitative discrète | tout, avec prudence sur la moyenne |
| `prix_articles_brl` | quantitative continue | tout |
| `frais_port_brl` | quantitative continue | tout |
| `montant_total_brl` | quantitative continue | tout |
| `delai_livraison_j` | quantitative discrète | tout |

---

## 2. Effectifs et fréquences

Avant tout indicateur, le geste de base : **compter**.

| Terme | Définition | Exemple |
|---|---|---|
| Effectif | nombre d'individus dans une catégorie | 2 700 commandes d'un seul article |
| Fréquence | effectif rapporté au total, en pourcentage | 90,00 % |
| Effectif cumulé | somme des effectifs jusqu'à cette catégorie | 2 932 commandes de 1 ou 2 articles |
| Fréquence cumulée | idem en pourcentage | 97,73 % |

Voici le tableau de fréquences complet de `nb_articles` :

| nb_articles | Effectif | Fréquence | Fréquence cumulée |
|---|---|---|---|
| 1 | 2 700 | 90,00 % | 90,00 % |
| 2 | 232 | 7,73 % | 97,73 % |
| 3 | 41 | 1,37 % | 99,10 % |
| 4 | 14 | 0,47 % | 99,57 % |
| 5 | 6 | 0,20 % | 99,77 % |
| 6 | 5 | 0,17 % | 99,93 % |
| 7 | 1 | 0,03 % | 99,97 % |
| 8 | 1 | 0,03 % | 100,00 % |
| **Total** | **3 000** | **100 %** | |

**Ce tableau, à lui seul, vaut une analyse.** Neuf commandes sur dix ne contiennent qu'un seul article. Toute stratégie de vente croisée dans cette entreprise part de très loin — et ce constat s'obtient par le plus simple des calculs, avant toute moyenne et tout écart-type.

Retenez cette habitude : **avant de calculer un indicateur, regardez la répartition.** Elle dit souvent l'essentiel, et elle vous évite de calculer une moyenne qui n'aurait aucun sens.

Une remarque de méthode : un pourcentage sans son effectif de référence ne veut rien dire. « 0,03 % des commandes contiennent 8 articles » sonne comme une statistique ; en réalité il s'agit d'**une seule commande**. C'est la deuxième des quatre questions du module 1 — demander l'effectif.

---

## 3. Moyenne, médiane, mode

On appelle **indicateur de position** — ou de tendance centrale — un nombre unique censé représenter « le milieu » d'une série. Il y en a trois, ils ne disent pas la même chose, et l'essentiel de ce module est de comprendre pourquoi.

### 3.1 La moyenne arithmétique

On additionne toutes les valeurs et on divise par l'effectif.

$$\bar{x} = \frac{1}{n}\sum_{i=1}^{n} x_i$$

En clair : somme des valeurs divisée par le nombre de valeurs.

Sur nos 3 000 transactions, la somme des montants vaut 487 545,87 BRL — le chiffre d'affaires total, que vous avez retrouvé hier en SQL. Divisée par 3 000 :

> **Moyenne = 162,52 BRL**

**Ce que la moyenne a de bien.** Elle utilise toute l'information ; elle se manipule algébriquement, ce qui est très commode en gestion — le total se retrouve en multipliant la moyenne par l'effectif ; et tout outil sait la calculer.

**Ce que la moyenne a de dangereux.** Elle est **sensible aux valeurs extrêmes**. Une seule valeur très grande la tire vers le haut.

### 3.2 La médiane

La **médiane** est la valeur qui sépare la série en deux moitiés de même effectif.

Calcul à la main, en deux étapes :

1. Trier les valeurs par ordre croissant.
2. Si l'effectif est **impair**, la médiane est la valeur du milieu, de rang (n+1)/2. Si l'effectif est **pair**, c'est la moyenne des deux valeurs centrales.

Sur le mini-jeu de 12 montants, trié :
27,99 / 35,69 / 45,62 / 48,04 / 56,78 / **58,38 / 59,33** / 65,09 / 99,90 / 139,16 / 167,74 / 194,08

L'effectif est pair, les deux valeurs centrales sont la 6e et la 7e :

> Médiane = (58,38 + 59,33) / 2 = **58,86 BRL**

alors que la moyenne de ces 12 valeurs vaut 997,80 / 12 = **83,15 BRL**. La moyenne est déjà 41 % au-dessus de la médiane sur douze valeurs seulement.

Sur les 3 000 transactions :

> **Médiane = 107,95 BRL**

### 3.3 La démonstration qu'il faut avoir vue une fois

Reprenons le mini-jeu de 12 valeurs et remplaçons la plus grande, 194,08, par une commande exceptionnelle de 20 000 BRL.

| | Avant | Après |
|---|---|---|
| Moyenne | 83,15 | **1 733,64** |
| Médiane | 58,86 | **58,86** |

La moyenne est multipliée par vingt. La médiane ne bouge pas.

C'est toute la différence entre un indicateur **sensible** et un indicateur **robuste**. Une seule valeur, sur douze, a suffi à rendre la moyenne absurde.

### 3.4 Le constat central des deux journées

Mettons les deux nombres côte à côte, sur les 3 000 transactions.

| Indicateur | Valeur |
|---|---|
| Moyenne | 162,52 BRL |
| Médiane | 107,95 BRL |
| Écart | la moyenne est supérieure de **50,6 %** à la médiane |

Et le chiffre qui doit rester :

> **69,5 % des transactions — 2 086 sur 3 000 — ont un montant inférieur à la moyenne.**

Lisez cette phrase deux fois. La « commande moyenne » de 162,52 BRL est plus grosse que sept commandes sur dix.

Ce n'est pas un cas pathologique construit pour le cours. C'est le comportement normal de presque toutes les données d'argent, de temps d'attente, de trafic web, de salaires et de tailles de fichiers.

**Pourquoi ?** Parce que ces grandeurs ont un **plancher** — une commande ne peut pas être négative, la plus petite ici vaut 15,78 BRL — **mais pas de plafond** : la plus grosse vaut 3 602,47 BRL. L'espace disponible n'est pas symétrique : beaucoup de place à droite, presque pas à gauche. La distribution est **asymétrique à droite**, et dans ce cas la moyenne est toujours tirée au-dessus de la médiane.

### 3.5 La règle pratique

Comparez systématiquement la moyenne et la médiane. C'est gratuit, et cela vous dit immédiatement à quoi vous avez affaire.

| Observation | Interprétation |
|---|---|
| moyenne ≈ médiane | distribution à peu près symétrique, la moyenne est un bon résumé |
| moyenne > médiane | asymétrie à droite, quelques grandes valeurs tirent la moyenne, préférez la médiane |
| moyenne < médiane | asymétrie à gauche, plus rare (exemple : l'âge au décès) |

### 3.6 Le mode

Le **mode** est la valeur la plus fréquente.

Sur `montant_total_brl`, arrondi à l'unité, le mode est **64 BRL**, observé 38 fois. Mais 38 occurrences sur 3 000, c'est 1,3 %. Et sans arrondi, la valeur exacte 64,00 n'apparaît que 6 fois.

Le mode est donc **quasiment inutilisable sur une variable continue**, où chaque valeur est presque unique. Il ne devient interprétable qu'après regroupement en tranches, et il dépend alors entièrement du découpage choisi.

Où est-il vraiment utile ? Sur les variables **qualitatives**, où il est même le seul indicateur de position disponible.

| Variable | Mode | Effectif |
|---|---|---|
| `etat_client` | **SP** (São Paulo) | 1 228 sur 3 000, soit 40,9 % |
| `categorie` | **cama_mesa_banho** (linge de maison) | 288 |
| `nb_articles` | **1 article** | 2 700, soit 90 % |

### 3.7 Le tableau de choix

| Indicateur | À utiliser quand | À éviter quand |
|---|---|---|
| Moyenne | la distribution est symétrique, ou vous avez besoin du total | il y a des valeurs extrêmes |
| Médiane | il y a des valeurs extrêmes, ou vous décrivez un cas « typique » | vous devez reconstituer un total |
| Mode | la variable est qualitative, ou vous cherchez le cas le plus fréquent | la variable est continue sans regroupement |

Et la règle de conduite professionnelle : **ne présentez jamais une moyenne seule.** Donnez toujours au minimum la médiane et l'écart-type à côté. Une colonne de plus, et vous évitez des décisions fausses.

---

## 4. La moyenne pondérée, et le piège de la moyenne de moyennes

### 4.1 La moyenne pondérée

Quand les valeurs à moyenner n'ont pas le même poids, on affecte à chacune un coefficient :

$$\bar{x}_{p} = \frac{\sum w_i x_i}{\sum w_i}$$

où $w_i$ est le poids de la valeur $x_i$.

C'est exactement ce que fait une moyenne ordinaire dont les poids sont les effectifs. Reprenons le tableau de fréquences de `nb_articles` : le nombre moyen d'articles par commande se calcule en pondérant chaque valeur par son effectif, et non en faisant la moyenne de 1, 2, 3, 4, 5, 6, 7, 8.

### 4.2 Le piège, démontré sur nos données

Calculons le montant moyen par état, puis faisons la moyenne de ces moyennes.

| État | Effectif | Montant moyen |
|---|---|---|
| SP | 1 228 | 146,67 BRL |
| RJ | 402 | 181,48 BRL |
| MG | 323 | 184,29 BRL |
| RS | 176 | 147,18 BRL |
| ... | ... | ... |

Il y a 26 états.

| Méthode | Résultat |
|---|---|
| Moyenne **simple** des 26 moyennes d'état | **181,90 BRL** |
| Moyenne **pondérée** par les effectifs, c'est-à-dire la vraie moyenne globale | **162,52 BRL** |

**Écart : 19,38 BRL, soit près de 12 %.**

Pourquoi ? Parce que la moyenne simple donne le même poids à un état de 5 commandes qu'à São Paulo et ses 1 228 commandes. Or São Paulo, qui pèse 41 % de l'activité, a un panier plus faible que la moyenne. La moyenne simple efface ce poids et remonte artificiellement le résultat.

### 4.3 La règle

> **Une moyenne ne se moyenne pas.**

Repartez toujours des lignes élémentaires, ou pondérez explicitement par les effectifs.

Vous avez rencontré cette erreur hier, énoncée en SQL. La voici chiffrée. C'est la même faute, dans deux outils différents — et c'est l'une des trois ou quatre erreurs que vous verrez le plus souvent en entreprise, y compris chez des gens expérimentés.

---

## 5. La dispersion

### 5.1 Pourquoi la position ne suffit pas

Deux séries de cinq commandes :

- Série A : 100, 105, 110, 115, 120 → moyenne 110, médiane 110
- Série B : 10, 20, 110, 200, 210 → moyenne 110, médiane 110

Même moyenne, même médiane. Et pourtant ces deux réalités commerciales n'ont rien en commun. Dans la première, tous les clients se ressemblent. Dans la seconde, il y a visiblement deux clientèles.

Il faut donc un second nombre, qui mesure **l'étalement** des valeurs autour du centre.

### 5.2 L'étendue, et sa faiblesse

L'**étendue** est la différence entre le maximum et le minimum.

Sur nos transactions : 3 602,47 − 15,78 = **3 586,69 BRL**.

Simple à calculer, immédiatement compréhensible, et presque sans valeur : elle ne dépend que de deux valeurs sur 3 000, justement les deux plus susceptibles d'être des anomalies. On la donne, on ne s'appuie pas dessus.

### 5.3 La variance

L'idée est de mesurer l'écart moyen à la moyenne. Une difficulté surgit aussitôt : **la somme des écarts à la moyenne vaut toujours exactement zéro**, les écarts positifs compensant les négatifs. C'est une propriété mathématique de la moyenne, pas un accident.

On contourne l'obstacle en élevant chaque écart au carré, ce qui supprime les signes. La **variance** est la moyenne des carrés des écarts à la moyenne.

**Variance de population** — quand vos données sont la population entière :

$$\sigma^2 = \frac{1}{n}\sum_{i=1}^{n}(x_i - \bar{x})^2$$

**Variance d'échantillon** — quand vos données sont un échantillon et que vous voulez estimer la variance de la population :

$$s^2 = \frac{1}{n-1}\sum_{i=1}^{n}(x_i - \bar{x})^2$$

**Pourquoi n − 1 ?** Parce qu'en calculant les écarts à la moyenne de l'échantillon plutôt qu'à la vraie moyenne de la population — qu'on ignore — on sous-estime systématiquement la dispersion. Diviser par n − 1 corrige ce biais. Retenez la conclusion, la démonstration n'est pas de ce niveau.

Sur nos 3 000 transactions :

| Formule | Variance | Écart-type |
|---|---|---|
| Échantillon (n − 1) | 46 504,82 | 215,65 BRL |
| Population (n) | 46 489,31 | 215,61 BRL |

L'écart entre les deux est de **0,02 %**. Voilà le vrai enseignement pratique : **dès que l'effectif dépasse quelques centaines, le choix de la formule ne change rien.** Il devient sensible sur de petits effectifs, et c'est là qu'il faut y faire attention.

**Convention des deux journées, à tenir :** nous utilisons partout la variance et l'écart-type **d'échantillon** (diviseur n − 1). C'est ce que font par défaut les fonctions `VAR` et `ECARTTYPE` des tableurs, ainsi que Looker Studio. Les corrigés sont calculés avec cette convention.

### 5.4 L'écart-type

Le problème de la variance est son unité. Comme on a élevé au carré, la variance de nos montants s'exprime en « BRL au carré », ce qui ne veut rien dire. 46 504,82 est un nombre inutilisable pour parler à un décideur.

On prend donc la racine carrée. L'**écart-type** revient à l'unité des données :

$$s = \sqrt{s^2}$$

> **Écart-type = 215,65 BRL**

Voilà un nombre qui se dit et s'interprète : autour d'une moyenne de 162,52 BRL, les commandes s'écartent typiquement de 215 BRL.

Et cette phrase doit vous alerter immédiatement : **l'écart-type est plus grand que la moyenne**. La dispersion dépasse le niveau moyen. La moyenne ne décrit rien du tout. Un intervalle « moyenne plus ou moins un écart-type » descendrait à −53 BRL, alors qu'aucune commande ne peut être négative. C'est le signal mathématique clair d'une distribution très asymétrique.

### 5.5 Calculer un écart-type à la main, une fois

Pour que la formule ne reste pas une incantation, faites-le une fois sur cinq valeurs : 10, 20, 30, 40, 50.

| Valeur | Écart à la moyenne (30) | Écart au carré |
|---|---|---|
| 10 | −20 | 400 |
| 20 | −10 | 100 |
| 30 | 0 | 0 |
| 40 | +10 | 100 |
| 50 | +20 | 400 |
| **Somme** | **0** | **1 000** |

Remarquez la somme des écarts : exactement 0. C'est la propriété annoncée.

- Variance d'échantillon : 1 000 / (5 − 1) = **250**
- Écart-type : √250 ≈ **15,81**
- Variance de population : 1 000 / 5 = 200, écart-type ≈ 14,14

Ici, avec 5 valeurs seulement, l'écart entre les deux conventions atteint 12 %. Comparez avec les 0,02 % obtenus sur 3 000 valeurs : c'est la démonstration concrète de ce qui a été dit plus haut.

---

## 6. Coefficient de variation et règle des écarts-types

### 6.1 Le coefficient de variation

Pour comparer la dispersion entre deux grandeurs d'unités différentes, on rapporte l'écart-type à la moyenne :

$$CV = \frac{s}{\bar{x}} \times 100$$

Ordres de grandeur à retenir :

| CV | Lecture |
|---|---|
| moins de 15 % | série homogène |
| 15 à 30 % | dispersion modérée |
| plus de 100 % | la moyenne ne représente plus rien |

Comparons trois colonnes du même fichier :

| Variable | Moyenne | Écart-type | CV |
|---|---|---|---|
| `montant_total_brl` | 162,52 BRL | 215,65 BRL | **132,7 %** |
| `delai_livraison_j` | 12,07 j | 8,93 j | **74,0 %** |
| `nb_articles` | 1,14 | 0,51 | **44,5 %** |

Conclusion directement exploitable : les montants sont bien plus dispersés que les délais. Cette entreprise **livre de façon relativement régulière mais vend de façon très irrégulière**. Ce sont deux problèmes de nature différente, et c'est le coefficient de variation qui permet de le dire, parce qu'il compare des jours et des réaux.

### 6.2 La règle 68-95-99,7, et sa limite

Pour une distribution en cloche — dite normale — environ 68 % des valeurs se situent à moins d'un écart-type de la moyenne, 95 % à moins de deux, 99,7 % à moins de trois.

Vérifions sur nos données, c'est instructif. Le seuil « moyenne + 2 écarts-types » vaut 162,52 + 2 × 215,65 = **593,81 BRL**, dépassé par 90 commandes, soit 3 %.

La théorie annonçait 2,5 % au-dessus du seuil supérieur. On en trouve 3 %. L'ordre de grandeur tient. Mais le seuil bas, lui, est absurde : 162,52 − 2 × 215,65 = **−268,78 BRL**, une valeur que rien ne peut atteindre.

**Retenez la limite en même temps que la règle : elle suppose une distribution symétrique.** Sur des données asymétriques, elle ne tient pas, et il faut une autre méthode pour détecter les anomalies.

C'est exactement ce que le module 7 va apporter, juste après l'atelier.

---

## Ce qu'il faut retenir du module 6

1. La population est définie par la question, pas par les données disponibles. La qualité d'un échantillon dépend de son mode de constitution, pas de sa taille.
2. Le type d'une variable détermine les calculs licites : pas de moyenne sur du qualitatif nominal.
3. Avant tout indicateur, comptez : un tableau de fréquences dit souvent l'essentiel. Ici, 90 % des commandes ne contiennent qu'un article.
4. Comparez toujours moyenne et médiane. Ici : 162,52 contre 107,95, et 69,5 % des commandes sous la moyenne.
5. La médiane est robuste, la moyenne est sensible : remplacer une valeur sur douze fait passer la moyenne de 83 à 1 734 sans bouger la médiane.
6. Une moyenne ne se moyenne pas : 181,90 BRL par moyenne simple contre 162,52 en réalité, soit 12 % d'écart.
7. L'écart-type ramène la dispersion à l'unité des données. Ici, 215,65 BRL — supérieur à la moyenne, donc la moyenne ne représente rien.
8. La règle des écarts-types suppose une distribution symétrique. La nôtre ne l'est pas.

---

## Vocabulaire du module 6

**Population** : ensemble complet des individus concernés par la question.
**Échantillon** : sous-ensemble sur lequel on calcule.
**Représentativité** : ressemblance de l'échantillon à la population sur les caractéristiques pertinentes.
**Individu** : unité observée (une personne, une commande, un jour).
**Variable** : caractéristique mesurée sur chaque individu, une colonne du tableau.
**Quantitative continue / discrète** : nombre pouvant prendre toute valeur / seulement des valeurs isolées.
**Qualitative nominale / ordinale** : catégorie sans ordre / avec ordre.
**Effectif, fréquence, cumulé** : comptage brut, en proportion, additionné de proche en proche.
**Moyenne arithmétique** : somme des valeurs divisée par l'effectif.
**Moyenne pondérée** : moyenne où chaque valeur reçoit un poids.
**Médiane** : valeur partageant la série triée en deux moitiés d'effectifs égaux.
**Mode** : valeur la plus fréquente.
**Indicateur robuste / sensible** : insensible / sensible aux valeurs extrêmes.
**Asymétrie** : caractère non symétrique d'une distribution.
**Étendue** : maximum moins minimum.
**Variance** : moyenne des carrés des écarts à la moyenne. Diviseur n pour la population, n − 1 pour l'échantillon.
**Écart-type** : racine carrée de la variance, dans l'unité des données.
**Coefficient de variation** : écart-type rapporté à la moyenne, en pourcentage.
**Règle 68-95-99,7** : répartition des valeurs autour de la moyenne pour une distribution en cloche.
