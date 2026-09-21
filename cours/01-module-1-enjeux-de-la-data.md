# Module 1 — Comprendre les enjeux de la data en entreprise

**Jour 1 · 9 h 20 – 10 h 50 · 90 minutes**, suivi de l'atelier 1 (45 minutes).
**Public : Bachelor 2, aucun prérequis.**

---

## Objectifs pédagogiques

À la fin de ce module, l'étudiant est capable de :

1. Définir ce qu'est une donnée et distinguer donnée, information, connaissance.
2. Nommer et ordonner les six étapes du cycle de vie de la donnée, et dire où la valeur se perd.
3. Énoncer les cinq critères de qualité d'une donnée et reconnaître une donnée personnelle.
4. Expliquer les trois apports réels de la data à une décision, et les trois pièges associés.
5. Distinguer les métiers de la data par leur livrable, et situer les compétences transversales.
6. Décrire un cas d'usage complet dans cinq secteurs, en nommant la donnée mobilisée et la décision visée.
7. Reconnaître trois symptômes d'une organisation qui se dit data-driven sans l'être.

---

## Minutage interne

| Temps | Séquence |
|---|---|
| 12 min | 1. Qu'est-ce qu'une donnée ? |
| 16 min | 2. Le cycle de vie de la donnée |
| 12 min | 3. Qualité et gouvernance |
| 16 min | 4. Le rôle stratégique dans la décision |
| 14 min | 5. Panorama des métiers |
| 16 min | 6. Cas d'usage sectoriels |
| 4 min | 7. Culture data — synthèse |

---

## 1. Qu'est-ce qu'une donnée ?

### 1.1 Une définition utilisable

Une **donnée** est la trace enregistrée d'un fait, exprimée dans un format qui permet de la conserver, de la transmettre et de la traiter automatiquement.

Trois mots comptent.

**Trace** : la donnée n'est pas le fait lui-même. Une commande passée sur un site, c'est un fait. La ligne enregistrée dans la base avec un identifiant, un horodatage et un montant, c'est la donnée. Entre le fait et sa trace, il y a toujours une perte. On ne mesure jamais tout, on mesure ce que le système a été conçu pour enregistrer.

**Enregistrée** : une donnée existe sur un support. Ce support a un propriétaire, un coût, une durée de conservation et un régime juridique.

**Format traitable** : « les ventes ont l'air bonnes ce mois-ci » n'est pas une donnée. `2018-03-14 ; commande 7f3a ; 212,40 BRL` en est une.

### 1.2 Donnée, information, connaissance

| Niveau | Exemple tiré de notre jeu fil rouge | Ce qu'il faut pour monter d'un cran |
|---|---|---|
| Donnée | `montant_total_brl = 3602,47` | un calcul, une mise en contexte |
| Information | « le montant moyen d'une commande est de 162,52 BRL, la médiane de 107,95 » | une interprétation, un référentiel |
| Connaissance | « notre chiffre d'affaires dépend d'une minorité de grosses commandes : 7 % des commandes font 32 % du chiffre d'affaires » | une décision, une action, un suivi de l'effet |

Le point à marteler : **la donnée brute ne dit rien**. Le nombre 3602,47 n'est ni bon ni mauvais, ni normal ni anormal. Il ne devient parlant qu'au regard d'un contexte. C'est précisément pour cela que les statistiques descriptives existent, et que nous y consacrerons la seconde journée.

### 1.3 Les grandes familles de données

On classe utilement les données selon leur degré de structure, parce que cela conditionne l'outil qui peut les stocker.

- **Structurées** : lignes et colonnes, chaque colonne a un type défini. Une table de commandes, un export comptable. Terrain des bases relationnelles, vues cet après-midi.
- **Semi-structurées** : une structure existe mais elle est souple et portée par le fichier lui-même. JSON, XML, journaux techniques. Chaque enregistrement peut avoir des champs différents.
- **Non structurées** : texte libre, images, son, vidéo. Un avis client rédigé en toutes lettres, une photo de produit. C'est la majorité du volume produit dans le monde, et la partie la plus difficile à exploiter.

Notre jeu Olist contient les trois : des tables de commandes, des horodatages techniques, et des commentaires d'avis rédigés librement.

### 1.4 Métadonnées

Une **métadonnée** est une donnée qui décrit une autre donnée : sa date de création, sa source, son unité, son propriétaire, sa fréquence de mise à jour.

On les néglige systématiquement, et c'est une erreur coûteuse. Un fichier `ventes_final_v3.xlsx` sans métadonnées pose des questions sans réponse : ventes de quoi, sur quelle période, en quelle devise, hors taxe ou toutes taxes, arrêtées à quelle date ? Un tableau de chiffres dont on ignore l'unité n'est pas exploitable, il est seulement décoratif.

C'est pour cette raison que l'atelier tableau de bord de demain exige que le titre porte la source, l'année et l'effectif. Ce n'est pas de la mise en forme, c'est la métadonnée minimale sans laquelle le graphique ne vaut rien.

### 1.5 Les trois V, et pourquoi s'en méfier un peu

Le vocabulaire du « big data » a popularisé trois dimensions : **volume**, **vélocité** (vitesse d'arrivée), **variété** (diversité des formats). On y ajoute souvent la **véracité** et la **valeur**.

Ce cadre pose du vocabulaire utile. Il devient trompeur s'il laisse croire que le volume est le critère principal. Dans la vie réelle d'une entreprise, une table de 3 000 lignes propre et bien comprise crée plus de valeur qu'un entrepôt de dix téraoctets que personne ne sait interpréter. Retenez l'ordre inverse de celui qu'on entend : d'abord la véracité, ensuite la valeur, le volume en dernier.

---

## 2. Le cycle de vie de la donnée

Une donnée n'est pas un objet inerte posé quelque part. Elle traverse des étapes, et à chacune elle peut se dégrader, se perdre, ou devenir illégale à conserver.

### 2.1 Les six étapes

**1. Collecte.** La donnée entre dans le système. Trois voies : la saisie humaine (formulaire, ticket de caisse), la captation automatique (capteur, clic, transaction), l'acquisition externe (fichier acheté, API partenaire, open data).

C'est l'étape la plus décisive et la plus négligée. Une erreur commise ici ne se corrige jamais complètement en aval. Si le formulaire d'inscription ne rend pas le pays obligatoire, aucun traitement ultérieur ne fera apparaître les pays manquants. On peut imputer, estimer, corriger — on ne peut pas inventer une information qui n'a jamais été saisie.

**2. Stockage.** La donnée est déposée dans un système qui la conserve : base de données, fichier, entrepôt, lac. Choisir le stockage, c'est choisir ce qu'on pourra faire ensuite, et à quel coût. C'est l'objet des modules 2, 4 et 5.

**3. Préparation.** Nettoyage, dédoublonnage, correction des formats, gestion des valeurs manquantes, jointure entre sources. Dans un projet réel, cette étape consomme régulièrement la majeure partie du temps de travail. Ce n'est pas un signe de mauvaise organisation : c'est la nature du métier.

**4. Analyse.** Calculs, agrégations, statistiques, modèles. C'est l'étape visible, celle qu'on imagine quand on pense « data ». Elle est courte par rapport aux trois précédentes.

**5. Diffusion.** Restitution : tableau de bord, rapport, alerte, API, ou fonctionnalité intégrée au produit. Une analyse que personne ne lit n'a produit aucune valeur.

**6. Archivage ou suppression.** La donnée sort du circuit actif : conservation à froid pour raisons légales ou historiques, ou destruction. Cette étape existe pour des raisons de coût, mais surtout de droit.

### 2.2 Pourquoi un cycle, et non une chaîne

Parce que la sortie alimente l'entrée. Le tableau de bord diffusé fait apparaître un besoin non couvert, qui déclenche une nouvelle collecte. Et parce que l'analyse produit elle-même de nouvelles données : la moyenne calculée, le segment identifié, le score attribué sont des données à leur tour, à stocker, à documenter et à faire vivre.

### 2.3 Où la valeur se perd

Représentez-vous le cycle comme un entonnoir. Sur cent unités de valeur potentielle :

- la **collecte** en perd par ce qu'elle n'enregistre pas, ou enregistre mal ;
- la **préparation** en perd par les lignes qu'il faut écarter faute de pouvoir les corriger ;
- l'**analyse** en perd très peu, car c'est l'étape la mieux outillée ;
- la **diffusion** en perd énormément, par les analyses justes que personne ne lit ou ne comprend.

La conséquence pratique est contre-intuitive : investir sur la qualité de la collecte et sur la clarté de la restitution rapporte davantage que raffiner la méthode d'analyse. C'est un argument que vous aurez à tenir en entreprise face à des gens qui veulent un modèle sophistiqué sur des données douteuses.

---

## 3. Qualité et gouvernance des données

### 3.1 Les cinq critères de qualité

Une donnée est de qualité si elle est :

| Critère | Question de vérification | Exemple de défaut |
|---|---|---|
| **Exacte** | correspond-elle au fait réel ? | une adresse périmée |
| **Complète** | tous les champs attendus sont-ils remplis ? | pays manquant pour 12 % des clients |
| **Cohérente** | les sources concordent-elles ? | le CRM dit 1 200 clients, la facturation 1 143 |
| **Fraîche** | la mise à jour est-elle assez récente pour l'usage ? | un stock actualisé une fois par semaine |
| **Unique** | y a-t-il des doublons ? | le même client saisi deux fois avec deux orthographes |

Ces cinq critères se vérifient. Un projet sérieux les vérifie avant de conclure quoi que ce soit, et sait dire lequel pose problème.

À noter : la fraîcheur se juge par rapport à l'usage, jamais dans l'absolu. Un stock actualisé chaque semaine est catastrophique pour une place de marché et parfaitement suffisant pour un inventaire annuel.

### 3.2 La donnée personnelle

Dès qu'une donnée permet d'identifier une personne, directement ou indirectement, le **RGPD** s'applique en Europe. Quatre principes suffisent à ce niveau :

- **Finalité** : on collecte pour un usage annoncé, pas « au cas où ».
- **Minimisation** : on ne collecte que le nécessaire à cette finalité.
- **Limitation de conservation** : on ne garde pas indéfiniment.
- **Droits de la personne** : accès, rectification, effacement, portabilité.

Deux termes à ne pas confondre :

- **Pseudonymisation** : les identifiants directs sont remplacés par des codes. La réidentification reste possible, indirectement. La donnée reste une donnée personnelle.
- **Anonymisation** : transformation irréversible, la réidentification est impossible. La donnée sort du champ du RGPD.

Observation utile pour les deux jours : notre jeu Olist est publié avec des identifiants remplacés par des codes aléatoires, et les noms de vendeurs remplacés par des noms de fiction. C'est cette **pseudonymisation** qui a rendu sa publication possible. Regardez le fichier : `40599d3d` au lieu d'un nom de client, ce n'est pas une négligence, c'est une décision de conformité.

### 3.3 Qui décide de quoi

Trois rôles à connaître, qu'on retrouve dans toute organisation un peu structurée :

- le **propriétaire de la donnée** (data owner), côté métier : il décide de ce qu'elle signifie et de qui peut y accéder ;
- le **gardien** (data steward) : il veille aux définitions, au catalogue, à la qualité ;
- le **responsable technique** : il assure le stockage, la sécurité, la disponibilité.

Quand ces trois rôles ne sont identifiés nulle part, la donnée n'appartient à personne, et c'est exactement à ce moment-là que deux services produisent deux chiffres différents sur la même question.

---

## 4. Le rôle stratégique de la data dans la prise de décision

### 4.1 Le vrai apport n'est pas la certitude

Une erreur fréquente consiste à présenter la data comme ce qui remplace le jugement par la vérité. C'est faux, et pédagogiquement dangereux.

La donnée ne supprime pas l'incertitude. Elle fait trois choses, plus modestes et plus puissantes :

1. Elle **rend l'incertitude visible et mesurable**. Sans données, on ignore qu'on ne sait pas. Avec des données, on peut dire : « la commande moyenne est à 162 BRL, mais la dispersion est énorme, donc cette moyenne seule ne suffit pas à décider ».
2. Elle **rend la décision discutable**. Un raisonnement appuyé sur des chiffres explicites peut être contredit, vérifié, reproduit. Une intuition ne peut être qu'acceptée ou refusée, selon l'autorité de celui qui l'énonce.
3. Elle **permet de mesurer l'effet** de la décision. C'est le point le plus souvent oublié. Décider avec des données sans mesurer ensuite le résultat, c'est retomber dans l'intuition avec une étape de plus.

### 4.2 Les quatre niveaux d'analyse

| Niveau | Question | Exemple sur nos données | Traité |
|---|---|---|---|
| Descriptif | Que s'est-il passé ? | quel est le montant moyen des commandes de 2018 ? | entièrement, jour 2 |
| Diagnostique | Pourquoi ? | pourquoi le montant moyen de SP diffère-t-il de celui du RS ? | en partie, atelier 5 |
| Prédictif | Que va-t-il se passer ? | quelles commandes risquent d'être livrées en retard ? | cité, non pratiqué |
| Prescriptif | Que faut-il faire ? | lesquelles traiter en priorité, et comment ? | cité, non pratiqué |

Ces deux journées couvrent entièrement le descriptif et ouvrent le diagnostique. C'est volontaire : le descriptif bien fait règle la majorité des questions réelles d'une entreprise, et personne ne réussit les niveaux supérieurs sans lui.

### 4.3 Décider avec des données : un exemple complet

Prenons une décision réelle, de bout en bout, pour montrer l'enchaînement.

**La question métier.** Faut-il offrir la livraison au-delà d'un certain montant d'achat ?

**La décision à prendre.** Fixer un seuil, ou renoncer au dispositif.

**Les données mobilisées.** La distribution des montants de commande, le coût réel du transport, la marge par commande.

**Ce que l'analyse descriptive apporte.** La répartition des montants, la proportion de clients situés juste sous un seuil candidat, le coût du dispositif à volume constant.

**Ce qu'elle n'apporte pas.** L'effet du seuil sur le comportement. Pour le savoir, il faut le tester sur une partie du trafic et mesurer.

**Le piège.** Calibrer le seuil sur la moyenne. Nous verrons demain, chiffres en main, pourquoi c'est une faute — et l'atelier de ce matin vous le fera trouver avant même de disposer des chiffres.

### 4.4 Les trois pièges de la décision par les données

**Le piège de la corrélation.** Deux grandeurs qui varient ensemble ne sont pas liées par une cause. Dans notre jeu World Happiness, la corrélation entre score de bonheur et PIB par habitant vaut 0,79, ce qui est fort. Cela ne dit pas que l'argent produit le bonheur : les deux peuvent dépendre d'un troisième facteur, et la causalité peut aller dans l'autre sens. La corrélation pose une question, elle ne fournit pas la réponse.

**Le piège de la moyenne.** Une moyenne écrase la réalité qu'elle résume. Sur nos 3 000 transactions, la moyenne est de 162,52 BRL alors que la médiane est de 107,95. La moyenne est supérieure de moitié à la valeur qui sépare la clientèle en deux.

**Le piège du survivant.** On analyse les données disponibles, qui sont rarement les données pertinentes. Le jeu Olist contient les commandes passées. Il ne contient pas les paniers abandonnés, ni les clients partis chez un concurrent, ni les recherches qui n'ont rien trouvé. Toute conclusion tirée de ce jeu porte sur ceux qui ont acheté, pas sur le marché. Demandez-vous systématiquement : **qui est absent de cette table ?**

---

## 5. Panorama des métiers de la data

### 5.1 Lire les métiers par le livrable

Les intitulés de poste varient d'une entreprise à l'autre au point d'être peu fiables. Une méthode robuste consiste à identifier le métier par ce qu'il produit et par qui utilise sa production.

| Métier | Livrable concret | Client interne | Outils typiques |
|---|---|---|---|
| **Data Analyst** | un tableau de bord, une analyse chiffrée qui répond à une question métier | direction, marketing, opérations | SQL, tableur, Looker Studio, Power BI |
| **Data Engineer** | un pipeline qui alimente des données fiables, à l'heure, au bon endroit | les autres métiers de la data | SQL, Python, orchestrateurs, entrepôts cloud |
| **Data Scientist** | un modèle qui prédit ou classe, et sa mesure de performance | produit, métier | Python, R, bibliothèques de machine learning |
| **Analytics Engineer** | des tables propres et documentées, prêtes à être analysées | analystes | SQL, dbt, entrepôt |
| **Data Steward / Manager** | des définitions partagées, un catalogue, des règles de qualité | toute l'entreprise | catalogues, documentation |
| **ML Engineer** | un modèle mis en production et supervisé | produit, technique | Python, conteneurs, plateformes |
| **Chief Data Officer** | une stratégie, des priorités, des arbitrages budgétaires | comité de direction | ne code pas |

### 5.2 Les trois métiers coeur, en une phrase

- Le **Data Engineer** fait en sorte que la donnée soit là, propre, à l'heure. Sans lui, les autres travaillent sur du sable.
- Le **Data Analyst** transforme une question floue posée par un humain en un chiffre défendable et en une recommandation.
- Le **Data Scientist** construit un objet qui généralise à des cas qu'il n'a jamais vus.

### 5.3 Une journée type de Data Analyst

Pour rendre le métier concret, voici à quoi ressemble réellement une journée. La proportion surprend toujours.

| Part du temps | Activité |
|---|---|
| ~30 % | comprendre la demande : reformuler la question, négocier le périmètre, obtenir les définitions |
| ~35 % | récupérer et préparer les données : requêtes SQL, jointures, contrôles de cohérence |
| ~15 % | analyser : calculs, agrégations, comparaisons |
| ~20 % | restituer : construire le support, écrire la recommandation, la défendre en réunion |

Une seule ligne sur quatre relève de l'analyse au sens strict. C'est pour cela que ces deux journées consacrent une matinée au SQL et une séquence à la restitution orale : ce sont les compétences qui occupent réellement le poste.

### 5.4 Les compétences transversales

Trois compétences se retrouvent dans les trois métiers, et ce sont les seules qui valent un investissement inconditionnel à votre niveau.

1. **SQL.** Le langage commun. Quarante ans d'âge et toujours incontournable. Vous l'écrirez cet après-midi.
2. **La compréhension du métier.** Un analyste qui ne comprend pas le commerce produit des chiffres justes et inutiles.
3. **La communication.** Savoir dire en trois phrases ce qu'un calcul implique pour une décision. C'est ce que la restitution orale de demain évaluera.

### 5.5 Une remarque honnête sur les titres

Le titre « Data Scientist » a été massivement utilisé entre 2015 et 2020 pour des postes qui étaient en réalité des postes d'analyste. À l'inverse, beaucoup de travail d'ingénierie de données est fait aujourd'hui par des profils qui ne portent pas ce titre. Quand vous lirez une offre d'emploi, lisez les missions et les outils, jamais l'intitulé.

---

## 6. Cas d'usage de la data dans différents secteurs

Cinq secteurs développés, chacun selon la même grille : la décision visée, la donnée mobilisée, la difficulté propre.

### 6.1 Commerce en ligne

C'est le secteur de notre jeu fil rouge.

**Cas d'usage 1 — la tenue du délai de livraison.** Olist enregistre pour chaque commande la date d'achat, la date de livraison réelle et la date estimée annoncée au client. La comparaison des deux dernières mesure directement la promesse tenue ou non. Sur notre extrait : délai médian de 10 jours, moyenne de 12,07, écart-type de 8,93, et une commande à 85 jours.

La décision qui en découle n'est pas « améliorons la moyenne ». C'est : identifier les commandes de la queue longue, comprendre ce qu'elles ont en commun, agir sur cette cause. **La dispersion est l'information, pas la moyenne.**

**Cas d'usage 2 — la concentration du chiffre d'affaires.** 7,27 % des commandes apportent 31,67 % du chiffre d'affaires. Cette phrase réorganise des priorités commerciales : un client à 500 BRL ne mérite pas le même traitement opérationnel qu'un client à 60 BRL, et pourtant la plupart des processus les traitent à l'identique.

**Cas d'usage 3 — l'exploitation des avis.** Le jeu contient des notes de 1 à 5. Croiser la note avec le retard de livraison permet de savoir si l'insatisfaction porte sur le produit ou sur la logistique. Deux plans d'action entièrement différents, et sans données on les confond.

**La difficulté propre au secteur :** l'attribution. Une vente suit un parcours passant par une publicité, une recherche, un avis, un e-mail. Décider quelle étape mérite le budget est une question qu'aucune donnée ne tranche seule.

### 6.2 Santé

**Cas d'usage — le suivi des parcours de soin.** En reliant actes, prescriptions et hospitalisations, on identifie des ruptures de parcours : un patient sorti d'hospitalisation sans consultation de suivi dans les trente jours présente un risque de réhospitalisation nettement plus élevé. La donnée permet de déclencher un rappel.

**La difficulté propre, qui est le vrai sujet.** Ces données sont des données de santé, catégorie particulière au sens du RGPD, avec un régime renforcé. L'hébergement exige un agrément spécifique. Les données ne peuvent pas être réutilisées librement pour une autre finalité. Et une erreur n'a pas le même coût qu'ailleurs : un faux positif dans une recommandation de produit coûte un clic, un faux négatif dans un dépistage coûte une vie.

À retenir : dans la data, « peut-on techniquement le faire » et « a-t-on le droit et la légitimité de le faire » sont deux questions distinctes, et la seconde arrive en premier.

### 6.3 Banque et assurance

**Cas d'usage — la détection de fraude à la carte.** Chaque transaction est comparée en temps réel au comportement habituel du porteur : montant inhabituel, pays inhabituel, succession rapide d'opérations. Un score de risque déclenche un blocage ou une demande de confirmation.

**La difficulté propre : l'arbitrage entre deux erreurs.** Bloquer une transaction légitime agace un client fidèle. Laisser passer une fraude coûte de l'argent. Aucun modèle ne supprime le compromis, il ne fait que le déplacer. Décider où placer le curseur est une décision d'entreprise, pas une décision technique — et c'est l'analyste qui doit présenter le compromis clairement pour que quelqu'un puisse trancher.

Second point : les fraudes sont rares, souvent moins d'une transaction sur mille. Un modèle qui prédit « jamais de fraude » a plus de 99,9 % de réussite et ne sert à rien. Se méfier d'un taux de réussite élevé sur un événement rare est un réflexe utile bien au-delà de ce secteur.

### 6.4 Industrie

**Cas d'usage — la maintenance prédictive.** Des capteurs de vibration, de température et de consommation équipent une machine. En rapprochant leurs relevés de l'historique des pannes, on détecte les signatures qui précèdent une défaillance, et on intervient avant l'arrêt.

Le gain ne vient pas de la réparation, qui aurait eu lieu de toute façon : il vient de l'arrêt planifié plutôt que subi. Un arrêt de chaîne non planifié coûte plusieurs fois le prix de la pièce.

**La difficulté propre :** il faut des pannes pour apprendre à les prévoir. Une machine fiable fournit peu d'exemples. Beaucoup de projets échouent ici, non par manque de données, mais par manque de données du cas intéressant.

### 6.5 Secteur public et transport

**Cas d'usage — la prévision de charge d'un réseau de transport.** L'historique de validations, croisé au calendrier scolaire, à la météo et aux événements, permet d'anticiper l'affluence et d'ajuster l'offre.

**La difficulté propre :** la donnée publique engage. Une erreur de prévision se voit, se commente, et la décision doit pouvoir s'expliquer à un citoyen. L'exigence de transparence y est plus forte que dans le privé, ce qui interdit en pratique certaines méthodes difficiles à justifier.

### 6.6 En survol

| Secteur | Cas d'usage | Donnée mobilisée |
|---|---|---|
| Distribution physique | prévision de stock par magasin | ventes par référence, météo, calendrier |
| Énergie | prévision de consommation, équilibrage | compteurs communicants, météo |
| Médias | recommandation, mesure d'audience | historique de consultation, temps passé |
| Sport | prévention des blessures, recrutement | charge d'entraînement, GPS, données de match |
| Agriculture | pilotage de l'irrigation | humidité des sols, imagerie satellite |

### 6.7 Le point commun

Aucun de ces cas ne consiste à « faire de la data ». Chacun part d'une décision précise que quelqu'un doit prendre de façon répétée, et cherche à l'améliorer.

C'est le test à appliquer à tout projet data qu'on vous présentera : **quelle décision sera prise différemment, par qui, à quelle fréquence ?** Si la réponse n'existe pas, le projet produira un rapport et rien d'autre.

---

## 7. Culture data et data-driven decision making

### 7.1 Définition

Une organisation est **data-driven** lorsque, face à un désaccord, elle règle la question en regardant les données plutôt qu'en comptant les galons.

Cette définition par le conflit est plus opérante que les définitions habituelles, parce qu'elle est observable. Une entreprise qui produit beaucoup de tableaux de bord mais où l'avis du directeur l'emporte systématiquement n'est pas data-driven : elle est data-décorée.

### 7.2 Les quatre conditions

1. **Accès.** Les données sont accessibles à ceux qui décident, sans trois semaines d'attente pour une extraction.
2. **Compétence.** Les décideurs savent lire un chiffre, y compris ses limites. C'est l'objet de ces deux journées : la culture data n'est pas la compétence des spécialistes, c'est celle de tous les autres.
3. **Définitions partagées.** Tout le monde calcule « un client actif » de la même manière. Sans cela, deux services arrivent à deux chiffres différents et la réunion porte sur les chiffres au lieu de porter sur la décision.
4. **Droit de contredire.** Un junior peut montrer un graphique qui contredit son directeur sans que cela lui coûte. La condition la plus difficile, et la seule qui ne s'achète pas.

### 7.3 Trois symptômes d'une fausse culture data

**Le tableau de bord que personne n'ouvre.** Un besoin jamais formulé : on a construit un outil avant d'avoir une question.

**Le chiffre choisi après la conclusion.** On sait ce qu'on veut démontrer, on cherche l'indicateur et la période qui le démontrent. La plus courante des malhonnêtetés analytiques, et la plus facile à commettre de bonne foi.

**La métrique devenue objectif.** Dès qu'un indicateur sert à évaluer les gens, il cesse de mesurer la réalité et commence à mesurer l'effort pour le faire monter. Un service client noté sur le temps de traitement raccourcira les appels sans résoudre les problèmes. La mesure a détruit ce qu'elle mesurait.

### 7.4 Ce qu'on attend de vous : quatre questions

Vous ne serez pas tous analystes. Mais vous serez tous, très vite, des gens à qui l'on présentera un graphique pour obtenir une décision.

1. **Demander la définition.** « Quand vous dites client actif, vous comptez quoi exactement ? »
2. **Demander l'effectif.** Un pourcentage sans dénominateur ne veut rien dire. « 40 % de hausse » sur 5 commandes, c'est 2 commandes de plus.
3. **Demander la dispersion.** « C'est la moyenne. Quelle est la médiane ? Quel est l'étalement ? »
4. **Demander qui manque.** « Cette analyse porte sur les clients qui ont acheté. Que sait-on des autres ? »

Ces quatre questions ne demandent aucune compétence technique. Elles suffisent à éliminer la majorité des mauvaises décisions prises au nom de la data. Et elles vont faire tomber, dans quinze minutes, les trois notes internes de l'atelier 1.

---

## Ce qu'il faut retenir du module 1

1. Une donnée est la trace enregistrée d'un fait : il y a toujours un écart entre le fait et sa trace, et cet écart est votre premier sujet de vigilance.
2. Le cycle compte six étapes, et la valeur se perd surtout à la collecte et à la diffusion, pas à l'analyse.
3. La qualité se mesure sur cinq critères : exactitude, complétude, cohérence, fraîcheur, unicité.
4. La data n'apporte pas la certitude : elle rend l'incertitude mesurable, la décision discutable et l'effet vérifiable.
5. On identifie un métier de la data par son livrable et son client interne, jamais par son intitulé. Un analyste passe 30 % de son temps à comprendre la demande et 20 % à restituer.
6. Un projet data commence par une décision à prendre, pas par une donnée disponible.
7. Une organisation est data-driven si les données tranchent les désaccords. Sinon, elle est data-décorée.

---

## Vocabulaire du module 1

**Donnée** : trace enregistrée d'un fait, dans un format traitable.
**Métadonnée** : donnée décrivant une autre donnée (source, unité, date, propriétaire).
**Donnée structurée / semi-structurée / non structurée** : selon que l'organisation en lignes et colonnes est stricte, souple ou absente.
**Cycle de vie** : collecte, stockage, préparation, analyse, diffusion, archivage.
**Critères de qualité** : exactitude, complétude, cohérence, fraîcheur, unicité.
**Pseudonymisation** : remplacement des identifiants directs par des codes, réidentification indirecte possible.
**Anonymisation** : transformation irréversible rendant la réidentification impossible.
**RGPD** : règlement européen sur la protection des données personnelles.
**Data owner, data steward** : responsable métier de la donnée, gardien de sa qualité et de ses définitions.
**Analyse descriptive / diagnostique / prédictive / prescriptive** : les quatre niveaux, du constat à la recommandation d'action.
**Corrélation** : tendance de deux grandeurs à varier ensemble. N'implique pas la causalité.
**Biais du survivant** : erreur consistant à conclure à partir des seuls cas présents dans les données.
**Data-driven** : qualifie une organisation dont les décisions sont arbitrées par les données.

---

## Notes pour le formateur

**Ouverture recommandée (sur les 20 minutes d'accueil).** Deux questions à main levée : « qui pense que sa future entreprise prend ses décisions à partir de données ? » puis « qui a déjà vu quelqu'un présenter un graphique pour gagner un argument ? ». L'écart entre les deux réponses installe tout le module.

**Le format 90 minutes est long pour un public sans prérequis.** Prévoyez deux respirations : une question à la salle à la fin de la section 2 (« à votre avis, à quelle étape perd-on le plus de valeur ? »), et le tableau de la journée type en section 5.3, qui réveille toujours la salle parce que la proportion surprend.

**Point de vigilance sur la section 4.** La tentation est de survendre la data. Résistez-y. Ce public est déjà largement exposé à un discours enthousiaste sur l'intelligence artificielle. L'apport différenciant de ce cours est la lucidité méthodologique, pas l'enthousiasme.

**La section 6 est modulable.** Si vous êtes en retard, traitez trois secteurs sur cinq en profondeur et distribuez le reste à lire. Ne traitez jamais les cinq en survol : mieux vaut comprendre trois mécanismes complets que collectionner cinq exemples.

**Transition vers l'atelier 1.** « On vient de dire que la donnée rend la décision discutable. On va le vérifier : je vous donne trois décisions d'entreprise argumentées avec des chiffres, et vous allez les attaquer. Sans ordinateur, et sans les données. »
