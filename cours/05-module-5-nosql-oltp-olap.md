# Module 5 — NoSQL, OLTP et OLAP, architectures analytiques

**Jour 2 · 9 h 15 – 10 h 15 · 60 minutes**, suivi de l'atelier 4 (30 minutes).

---

## Objectifs pédagogiques

À la fin de ce module, l'étudiant est capable de :

1. Expliquer d'où vient NoSQL et ce que chaque famille abandonne pour gagner autre chose.
2. Nommer les quatre familles et donner un cas d'usage pour chacune.
3. Expliquer ce qu'est la cohérence à terme et quand elle est inacceptable.
4. Appliquer une grille de décision en quatre questions pour choisir un stockage.
5. Distinguer OLTP et OLAP par la question à laquelle chaque système répond.
6. Décrire une chaîne ETL et distinguer entrepôt, lac et lakehouse.
7. Lire un schéma en étoile et identifier faits et dimensions.

---

## Minutage interne

| Temps | Séquence |
|---|---|
| 12 min | 1. D'où vient NoSQL |
| 14 min | 2. Les quatre familles |
| 10 min | 3. Choisir : la grille en quatre questions |
| 12 min | 4. OLTP et OLAP |
| 12 min | 5. La chaîne analytique et le schéma en étoile |

---

## 1. D'où vient NoSQL

### 1.1 Le contexte

À la fin des années 2000, des acteurs du web à très grande échelle rencontrent des problèmes que le relationnel ne résout pas confortablement :

- des volumes qui ne tiennent plus sur une seule machine ;
- des écritures massives et continues, des dizaines de milliers par seconde ;
- des données dont la structure change tous les mois ;
- des besoins de réponse en quelques millisecondes, simultanément sur plusieurs continents.

« NoSQL » ne signifie pas « contre SQL ». On l'interprète aujourd'hui comme **Not Only SQL**. Ce n'est pas une technologie, c'est un ensemble de familles qui ont chacune **renoncé à quelque chose pour gagner autre chose**.

Cette phrase est la clé de tout le module. À chaque fois que vous rencontrerez une base NoSQL, la bonne question est : qu'a-t-elle abandonné, et est-ce que je peux me le permettre ?

### 1.2 Le renoncement principal : la cohérence à terme

La plupart des systèmes NoSQL distribués acceptent une **cohérence à terme**. Concrètement : juste après une écriture, une lecture exécutée sur une autre machine peut encore renvoyer l'ancienne valeur, le temps que la mise à jour se propage. Quelques millisecondes à quelques secondes.

Pourquoi accepter cela ? Parce que garantir la cohérence immédiate entre des machines réparties sur plusieurs continents coûte cher en latence. On échange une garantie contre de la vitesse et de la disponibilité.

**Quand est-ce acceptable ?** Pour un compteur de mentions « j'aime », un fil d'actualité, une recommandation de contenu : parfaitement.
**Quand est-ce inacceptable ?** Pour un solde bancaire, un stock de places de concert, une réservation de siège : absolument pas.

Voilà toute l'affaire. Le reste est du détail technique.

### 1.3 Les autres renoncements fréquents

- **Pas de jointure.** Il faut dupliquer les données à l'écriture, ou faire plusieurs requêtes et recoller dans le code.
- **Pas de schéma imposé.** La souplesse à l'écriture devient une charge à la lecture : le code doit gérer tous les cas de figure possibles, y compris les documents anciens au format périmé.
- **Transactions limitées.** L'atomicité couvre souvent un seul document, pas une opération portant sur plusieurs.

Le schéma ne disparaît pas du problème quand il disparaît de la base. **Il migre dans le code et dans la discipline de l'équipe.** Retenez cela : c'est ce qui explique pourquoi beaucoup de projets NoSQL mal cadrés deviennent ingérables au bout de deux ans.

---

## 2. Les quatre familles

### 2.1 Clé-valeur

**Principe.** La base est un immense dictionnaire : une clé, une valeur, rien de plus. On ne peut pas interroger le contenu de la valeur, seulement demander la valeur associée à une clé. En échange, c'est extrêmement rapide.

**Exemples :** Redis, Amazon DynamoDB, Memcached.

**Cas d'usage.** Les sessions utilisateur d'un site. La clé est l'identifiant de session, la valeur est le panier en cours. On n'a jamais besoin de chercher « toutes les sessions dont le panier dépasse 100 euros ».

**Le plus qui compte :** l'expiration automatique. On peut dire à la base « oublie cette clé dans 48 heures », et elle le fait. En relationnel, il faudrait une tâche de nettoyage périodique.

### 2.2 Document

**Principe.** Chaque enregistrement est un document autonome, généralement en JSON, avec sa propre structure. Deux documents de la même collection peuvent avoir des champs différents. On peut interroger l'intérieur des documents.

**Exemples :** MongoDB, Couchbase, Firestore.

**Cas d'usage.** Un catalogue de produits hétérogènes. Un téléviseur a une diagonale et une résolution, un livre a un auteur et un nombre de pages, un vin a un millésime et un cépage. En relationnel, il faudrait soit une table à cent colonnes majoritairement vides, soit un montage attribut-valeur pénible à interroger. En document, chaque produit porte ses propres attributs.

Voici à quoi ressemble un document :

```json
{
  "id": "prod-4471",
  "nom": "Comté 24 mois",
  "categorie": "fromage",
  "lait": "vache",
  "affinage_mois": 24,
  "producteur": { "nom": "Ferme du Haut", "ville": "Poligny" }
}
```

Notez la structure imbriquée : le producteur est un objet dans l'objet. C'est impossible dans une case de table relationnelle, et c'est le principal attrait de cette famille.

### 2.3 Colonnes larges

**Principe.** Les données sont organisées par colonnes et distribuées sur de nombreuses machines, conçues pour absorber un flux d'écriture massif et continu.

**Exemples :** Apache Cassandra, HBase, ScyllaDB.

**Cas d'usage.** Les relevés de millions de capteurs, les journaux techniques d'une plateforme, l'historique de positions d'une flotte de véhicules. On écrit énormément, on lit par plages de temps.

### 2.4 Graphe

**Principe.** On stocke des noeuds et les liens entre eux, et on interroge les **chemins**. La force est de répondre à des questions de proximité que le relationnel exprime très mal.

**Exemples :** Neo4j, Amazon Neptune.

**Cas d'usage.** Un réseau social : « les amis de mes amis qui travaillent dans mon secteur ». La détection de fraude en anneau : « ces quinze comptes partagent deux à deux un numéro de téléphone ou une adresse ».

**Pourquoi le relationnel s'y prend mal.** Chaque niveau de profondeur exige une jointure supplémentaire. Trouver « les amis d'amis d'amis » demande trois jointures ; à six niveaux, la requête devient inécrivable et ingérable. Une base graphe parcourt le chemin sans multiplier les jointures.

### 2.5 Tableau de synthèse

| Famille | On interroge | Renoncement principal | Cas d'usage type |
|---|---|---|---|
| Clé-valeur | par la clé uniquement | aucune recherche sur le contenu | sessions, cache |
| Document | l'intérieur du document | pas de jointure, schéma dans le code | catalogue hétérogène |
| Colonnes larges | par plage de clés | modèle de requête figé dès la conception | capteurs, journaux |
| Graphe | des chemins | montée en charge plus délicate | réseaux, fraude |

---

## 3. Choisir : la grille en quatre questions

### 3.1 Le tableau de comparaison

| Critère | Relationnel (SQL) | NoSQL |
|---|---|---|
| Structure | schéma fixe, défini avant d'écrire | schéma souple, porté par la donnée |
| Modifier la structure | opération à planifier | ajouter un champ ne casse rien |
| Langage | SQL, normalisé et commun | propre à chaque système |
| Relations entre données | jointures natives | à gérer dans le code ou par duplication |
| Cohérence | forte, immédiate (ACID) | souvent à terme |
| Montée en charge | d'abord une machine plus puissante | ajouter des machines |
| Compétences disponibles | très nombreuses | plus rares et plus spécialisées |
| Outillage BI | excellent, tout s'y connecte | inégal |

### 3.2 La grille de décision

Ne retenez pas le tableau. Retenez ces quatre questions, **dans cet ordre**.

**1. Les données sont-elles fortement reliées entre elles ?**
Si répondre aux questions métier exige de recouper en permanence commandes, clients, produits et vendeurs, le relationnel gagne. Les jointures sont faites pour cela — vous en avez écrit hier après-midi.

**2. La structure est-elle stable ?**
Si les champs sont connus et changent une fois par an, le relationnel gagne. Si chaque enregistrement peut avoir des attributs différents et que le format évolue tous les mois, le document gagne.

**3. Une lecture légèrement périmée est-elle acceptable ?**
Si non — argent, stock, réservation de place unique, dossier médical — le relationnel gagne. Si oui — compteur, fil d'actualité, recommandation — NoSQL devient possible.

**4. Le volume et le débit dépassent-ils une grosse machine ?**
Sachez que ce cas est **bien plus rare qu'on ne le croit**. Une base PostgreSQL correctement conçue traite des dizaines de millions de lignes sans difficulté. Les 112 650 articles vendus du jeu Olist complet n'impressionnent aucun système, et notre base de 3 418 lignes tourne dans un navigateur.

### 3.3 La bonne réponse est souvent « les deux »

On parle de **persistance polyglotte** : chaque usage reçoit le stockage qui lui convient, au sein du même système d'information.

Un site marchand typique utilise :

- du **relationnel** pour les commandes et les paiements ;
- du **document** pour le catalogue ;
- du **clé-valeur** pour les sessions et le cache ;
- un **moteur de recherche** spécialisé pour la recherche plein texte ;
- un **entrepôt analytique** pour les tableaux de bord.

La question de l'examen n'est donc jamais « SQL ou NoSQL », elle est **« quel stockage pour quel usage »**.

### 3.4 Les deux pièges symétriques

**Choisir NoSQL parce que c'est moderne.** L'erreur la plus fréquente et la plus coûteuse. Elle se paie six mois plus tard, quand la direction demande un tableau de bord croisant trois collections que rien ne relie, et qu'il faut réécrire dans le code ce qu'un `JOIN` aurait fait en une ligne.

**Forcer du relationnel sur des documents réellement hétérogènes.** Produit des tables de quatre-vingts colonnes vides à 90 %, que plus personne n'ose modifier.

Il n'y a pas de vainqueur. Il y a une adéquation à démontrer, et c'est ce que l'atelier 4 vous demande de faire dans trente minutes.

---

## 4. OLTP et OLAP

### 4.1 Deux métiers pour les mêmes données

Reprenons notre site marchand. Deux activités très différentes s'appuient sur les mêmes données.

**Activité 1.** Un client valide son panier. Il faut écrire une ligne dans `orders`, trois lignes dans `order_items`, une ligne dans `order_payments`, décrémenter un stock. Cela doit prendre quelques millisecondes, être absolument fiable, et se produire des milliers de fois par heure en parallèle.

**Activité 2.** Le directeur commercial veut le montant moyen des commandes par état et par mois sur deux ans, avec l'évolution d'une année sur l'autre. Cela touche des millions de lignes, ne s'exécute qu'une fois par jour, et peut se permettre de durer quelques secondes.

Ces deux activités ont des besoins **opposés**. Les traiter avec le même système, c'est mal servir les deux. D'où deux mondes.

### 4.2 OLTP — le monde de la transaction

**OLTP** : On-Line Transaction Processing. Le système qui fait tourner l'activité au quotidien.

- Beaucoup d'écritures, très fréquentes, très courtes.
- On accède à peu de lignes à la fois, mais très souvent.
- Données normalisées, toujours à jour, garanties ACID.
- Question type : « quel est l'état de la commande 40599d3d ? »
- Si le système tombe, **l'entreprise s'arrête de vendre**.

### 4.3 OLAP — le monde de l'analyse

**OLAP** : On-Line Analytical Processing. Le système qui sert à comprendre et à décider.

- Presque uniquement des lectures, sur de grands volumes.
- On agrège des millions de lignes pour produire quelques nombres.
- Données dénormalisées, souvent historisées, mises à jour par lots.
- Question type : « quelle est l'évolution du panier moyen par état sur douze mois ? »
- Si le système tombe, personne ne vend moins ce jour-là. **On décide moins bien.**

### 4.4 Le tableau

| Critère | OLTP | OLAP |
|---|---|---|
| Finalité | faire fonctionner l'activité | comprendre et décider |
| Opérations | écritures et lectures unitaires | lectures massives et agrégations |
| Volume par requête | quelques lignes | millions de lignes |
| Fraîcheur | temps réel | souvent la veille |
| Modélisation | normalisée | dénormalisée, en étoile |
| Utilisateurs | clients, applications, employés | analystes, direction |
| Historique | l'état courant | l'historique complet |
| Conséquence d'une panne | l'activité s'arrête | on décide moins bien |

### 4.5 La faute professionnelle à ne pas commettre

**On ne branche jamais un tableau de bord sur la base de production.**

Une requête d'analyse un peu lourde — exactement du genre de celles que vous avez écrites hier — peut saturer la base et ralentir les achats des clients. C'est une faute classique, et elle se produit vraiment : un analyste bien intentionné lance une agrégation sur toute l'historique un vendredi à 18 heures, et le site devient lent pendant vingt minutes.

---

## 5. La chaîne analytique et le schéma en étoile

### 5.1 ETL, ou ELT

Pour passer de l'OLTP à l'OLAP, on met en place une chaîne :

- **E**xtract : on extrait les données du système transactionnel ;
- **T**ransform : on les nettoie, on calcule, on joint, on met en forme ;
- **L**oad : on les charge dans le système analytique.

Cette chaîne tourne typiquement chaque nuit. C'est le métier du Data Engineer vu au module 1.

**ELT** est la variante moderne : on charge d'abord les données brutes dans l'entrepôt, puis on les transforme sur place. C'est possible parce que les entrepôts actuels sont assez puissants pour faire le travail de transformation eux-mêmes, et c'est plus souple — si la règle de transformation change, on la rejoue sans re-extraire.

### 5.2 Trois destinations, à ne pas confondre

| | Entrepôt de données | Lac de données | Lakehouse |
|---|---|---|---|
| Nom anglais | data warehouse | data lake | lakehouse |
| Ce qu'on y met | données structurées, nettoyées, modélisées | tout, dans le format d'origine, y compris non structuré | les deux |
| Mise en forme | avant le chargement | au moment de l'usage | selon les zones |
| Coût de stockage | plus élevé | faible | intermédiaire |
| Risque | rigidité, délai pour ajouter une source | devient un marécage sans gouvernance | complexité |
| Exemples | BigQuery, Snowflake, Redshift | stockage objet + catalogue | Databricks, formats de table ouverts |

Le mot **marécage** (*data swamp*) n'est pas une plaisanterie : c'est le terme consacré pour un lac de données dans lequel on a tout déversé sans documenter, et dont plus personne ne sait ce qu'il contient. C'est le destin de la majorité des lacs mal gouvernés.

### 5.3 Le schéma en étoile

Dans un entrepôt, on n'utilise pas le schéma normalisé de l'OLTP. On adopte un **schéma en étoile** :

- une **table de faits** au centre : les événements mesurables et leurs mesures ;
- des **tables de dimensions** autour : les axes d'analyse.

```
        dim_temps                dim_client
        (année, mois,            (état, ville,
         jour, trimestre)         segment)
              \                      /
               \                    /
                +--  FAITS_COMMANDES  --+
                |   montant_total       |
               /    nb_articles          \
              /     delai_livraison       \
        dim_produit                   dim_vendeur
        (catégorie,                   (état, ville)
         sous-catégorie)
```

On analyse ensuite **une mesure « par » une dimension** : le montant total par mois, par état, par catégorie.

### 5.4 Pourquoi cela vous concerne dans une heure

Regardez le fichier `transactions-ecommerce.csv` que vous utiliserez aujourd'hui :

| Colonne | Rôle |
|---|---|
| `montant_total_brl`, `nb_articles`, `delai_livraison_j` | **mesures** |
| `date_achat`, `etat_client`, `categorie` | **dimensions** |
| `id_commande` | identifiant du fait |

C'est un schéma en étoile aplati en une seule table. Et quand vous construirez votre tableau de bord cet après-midi, Looker Studio vous demandera littéralement de désigner des **dimensions** et des **métriques**.

Vous saurez pourquoi ces deux mots, d'où ils viennent, et ce qu'ils recouvrent. C'est exactement le genre de chose qui distingue quelqu'un qui utilise un outil de quelqu'un qui le comprend.

---

## Ce qu'il faut retenir du module 5

1. NoSQL n'est pas plus moderne que SQL : chaque famille a renoncé à une garantie pour gagner une capacité.
2. Le renoncement le plus fréquent est la cohérence à terme : acceptable pour un compteur, inacceptable pour un solde bancaire.
3. Quatre familles : clé-valeur, document, colonnes larges, graphe.
4. Quatre questions pour choisir : les données sont-elles reliées, la structure est-elle stable, une lecture périmée est-elle acceptable, le volume dépasse-t-il une machine.
5. La bonne réponse en entreprise est souvent « les deux » : persistance polyglotte, un stockage par usage.
6. OLTP fait tourner l'activité, OLAP sert à décider. On ne branche jamais un tableau de bord sur la base de production.
7. Dans un entrepôt, on modélise en étoile : une table de faits avec les mesures, des dimensions autour. C'est ce que l'outil de BI appellera métriques et dimensions.

---

## Vocabulaire du module 5

**NoSQL** : ensemble de systèmes non relationnels, regroupés en quatre familles.
**Cohérence à terme** : garantie affaiblie selon laquelle les copies finissent par converger, sans immédiateté.
**Persistance polyglotte** : usage de plusieurs types de stockage dans un même système d'information.
**OLTP** : système transactionnel, fait tourner l'activité.
**OLAP** : système analytique, sert à décider.
**ETL / ELT** : chaîne d'extraction, transformation et chargement vers l'analytique.
**Entrepôt de données** : stockage analytique structuré et modélisé.
**Lac de données** : stockage de données brutes dans leur format d'origine.
**Marécage de données** : lac non gouverné, devenu inexploitable.
**Lakehouse** : architecture combinant souplesse du lac et garanties de l'entrepôt.
**Schéma en étoile** : table de faits centrale entourée de tables de dimensions.
**Fait, mesure, dimension** : l'événement enregistré, ce qu'on calcule, l'axe selon lequel on analyse.

---

## Notes pour le formateur

**Ce module ouvre la seconde journée : soignez la reconnexion.** Les étudiants reviennent avec une nuit de décalage. Les dix minutes de reprise qui précèdent ce module servent à cela ; enchaînez en rappelant qu'ils ont passé l'après-midi d'hier dans un système relationnel, et que la question du jour est « et quand ce n'est pas le bon outil ? ».

**Point de vigilance sur NoSQL.** Le public arrive souvent avec l'idée que NoSQL est l'avenir et SQL le passé. Le contre-argument le plus efficace est l'ancienneté de SQL retournée en atout : un langage normalisé en 1986, encore massivement utilisé en 2026, n'est pas un vestige — c'est une norme qui a survécu à tous ses concurrents. Et ils viennent d'écrire leurs premières requêtes, l'argument porte bien mieux la veille au soir passée.

**Faites voter la salle sur les cas d'usage** avant de donner la réponse, famille par famille. Les désaccords sont productifs, en particulier sur le document contre le relationnel.

**La section 5.4 est la charnière de la journée.** Elle prépare directement l'atelier tableau de bord de l'après-midi et évite vingt minutes de flottement devant l'interface de Looker Studio. Ne la coupez sous aucun prétexte : c'est le moment où « dimension » et « métrique » cessent d'être du jargon d'outil.

**Si vous êtes en retard**, comprimez la section 5.2 (entrepôt, lac, lakehouse) à trois phrases et gardez 5.3 et 5.4 en entier.

**Transition vers l'atelier 4.** « Trente minutes, trois besoins concrets, trois décisions à trancher avec deux arguments chacune. Et un avertissement : si vos trois réponses sont identiques, vous avez appliqué une préférence, pas une grille. »
