# Atelier 6 — Tableau de bord et restitution orale

**Jour 2 · 15 h 35 – 16 h 25 (construction, 50 min) puis 16 h 25 – 16 h 50 (restitution orale, 25 min).**
**Binômes. Un ordinateur par binôme, un compte Google requis.**
**Outil : https://lookerstudio.google.com/ (gratuit).**
**Fichier : `bonheur-mondial-2019.csv` (156 pays). Variante : `transactions-ecommerce.csv`.**

---

## Contexte

C'est l'atelier de synthèse des deux journées. Il mobilise le module 1 (à quelle décision sert ce tableau de bord), le module 5 (dimensions et métriques) et les modules 6 et 7 (quel indicateur afficher, et pourquoi pas la moyenne).

Vous êtes analystes pour une organisation internationale qui prépare une note de cadrage sur les déterminants du bien-être. Votre commanditaire vous demande un tableau de bord d'**une seule page**, qu'il projettera en réunion — et il veut que vous le lui présentiez en cinq minutes.

### Le fichier `bonheur-mondial-2019.csv`

| Colonne | Signification |
|---|---|
| `pays` | nom du pays |
| `region` | région du monde |
| `rang` | rang mondial du pays (1 = meilleur score) |
| `score_bonheur` | score de bien-être déclaré, de 0 à 10 |
| `pib_hab` | contribution du PIB par habitant au score |
| `soutien_social` | contribution du soutien social |
| `esperance_vie` | contribution de l'espérance de vie en bonne santé |
| `liberte` | contribution de la liberté de faire ses choix de vie |
| `perception_corruption` | contribution de la confiance dans les institutions |
| `generosite` | contribution de la générosité |

**Point important à comprendre avant de commencer.** Les six dernières colonnes ne sont pas des mesures brutes de richesse ou de santé : ce sont les **contributions estimées** de chaque facteur au score, calculées par un modèle. Elles ne s'additionnent pas exactement au score total. Tenez-en compte dans votre façon de les présenter, et mentionnez-le dans votre note de lecture.

---

## Étape 1 — Cadrer avant de cliquer (8 minutes)

**Ne touchez pas à l'outil.** Sur papier, écrivez :

1. **La question unique** à laquelle votre tableau de bord répond. Une seule phrase, se terminant par un point d'interrogation.
2. **La décision** que votre commanditaire prendra différemment selon la réponse.
3. **Les quatre indicateurs ou visualisations** que vous allez afficher, et pour chacun la raison en une ligne.
4. Pour chaque visualisation : quelle colonne joue le rôle de **dimension** et laquelle joue le rôle de **métrique**.

Ce cadrage est un livrable noté. Un tableau de bord construit sans cadrage préalable produit invariablement une collection de graphiques sans propos.

---

## Étape 2 — Importer les données (8 minutes)

### Chemin A — Connecteur d'importation de fichier (recommandé)

1. https://lookerstudio.google.com/, connexion avec un compte Google.
2. **Créer**, puis **Source de données**.
3. Connecteur **Importation de fichier** (*File Upload*).
4. Déposez `bonheur-mondial-2019.csv`, puis **Associer**.
5. Les **dimensions** apparaissent en vert, les **métriques** en bleu.

### Chemin B — Via Google Sheets (repli)

Importez le CSV dans Google Sheets (séparateur : la virgule), puis dans Looker Studio : **Créer**, **Source de données**, connecteur **Google Sheets**.

### Contrôle de cohérence obligatoire

- [ ] Le nombre de lignes est bien de **156**. Si vous en avez 157, la ligne d'en-tête a été comptée comme une donnée.
- [ ] `pays` et `region` sont reconnus comme des **dimensions** de type texte.
- [ ] `score_bonheur` est une **métrique** numérique avec décimales.
- [ ] `rang` est numérique — mais demandez-vous s'il doit être agrégé. **Une somme de rangs n'a aucun sens.** Corrigez son agrégation si l'outil l'a mise sur `Somme`.
- [ ] Aucune colonne numérique n'a été interprétée comme du texte à cause du séparateur décimal.

Le champ `rang` est un piège volontaire. Consignez ce que vous avez fait.

---

## Étape 3 — Construire (26 minutes)

Un rapport d'**une seule page** contenant au minimum les six éléments suivants.

### 3.1 Titre et sous-titre

Le titre énonce le sujet. Le sous-titre indique la **source des données, l'année et l'effectif**. C'est une obligation professionnelle, pas une décoration : un graphique dont on ne peut pas retrouver la source est inutilisable dans une note officielle. C'est la métadonnée minimale vue au module 1.

### 3.2 Deux indicateurs de synthèse

Deux cartes de type « chiffre clé ». **Au moins l'une des deux doit afficher une médiane**, et non une moyenne.

Difficulté technique de l'atelier : la médiane **ne figure pas** dans la liste déroulante des agrégations, qui ne propose que somme, moyenne, comptage, minimum et maximum. Elle existe en revanche comme **fonction de champ calculé**. Créez donc un champ calculé ; la liste officielle des fonctions vous en donnera le nom exact.

Deux contraintes à connaître : cette fonction ne s'applique qu'à un champ **non agrégé**, et notre source est une table de lignes brutes, une par pays — vous êtes dans le cas favorable.

Consignez dans vos notes de lecture **la formule exacte** que vous avez utilisée.

### 3.3 Une comparaison par catégorie

Un graphique comparant les régions du monde sur le score de bien-être.

**Contrainte :** votre graphique doit permettre de voir non seulement le niveau de chaque région, mais aussi le fait que **les régions n'ont pas le même nombre de pays**. Trouvez un moyen de faire apparaître cet effectif, faute de quoi votre comparaison est trompeuse.

### 3.4 Un classement

Un tableau ou un graphique à barres présentant les **dix premiers et les dix derniers** pays. Les deux extrémités : un tableau de bord qui ne montre que les meilleurs est un outil de communication, pas d'analyse.

### 3.5 Une relation entre deux variables

Un nuage de points croisant `pib_hab` (abscisse) et `score_bonheur` (ordonnée), un point par pays, la couleur portant la région.

**Puis, obligatoirement, une zone de texte de deux phrases** à côté du graphique, qui énonce :

- ce que la relation observée suggère ;
- ce qu'elle ne permet **pas** de conclure.

Cette zone de texte est l'élément le plus important de l'atelier. Un nuage de points sans avertissement sur la causalité est précisément la faute que vous avez critiquée hier matin dans la note interne n° 3.

### 3.6 Un filtre

Un contrôle de filtre sur `region`, pour que votre commanditaire puisse isoler une région pendant la réunion. Vérifiez qu'il agit bien sur **tous** vos graphiques.

---

## Étape 4 — Préparer la restitution (8 minutes)

Vous présenterez votre tableau de bord en **cinq minutes maximum**, en suivant ce plan imposé :

| Temps | Contenu |
|---|---|
| 45 s | La question et la décision qu'elle éclaire |
| 2 min | Ce que montrent les données : trois constats chiffrés, pas plus |
| 1 min | Les limites : ce que ce tableau de bord ne dit pas |
| 1 min | Votre recommandation, et la donnée que vous réclameriez ensuite |
| 15 s | Conclusion en une phrase |

**Préparez une note d'une page**, pas un texte à lire. Vous serez interrompus à cinq minutes, même en milieu de phrase.

Les deux membres du binôme parlent, en se partageant les parties.

---

## Étape 5 — Livrer

1. Nommez votre rapport : `TB-bonheur-[vos noms]`.
2. Exportez-le en PDF, ou capturez la page entière.
3. Partagez le lien de consultation si votre compte le permet ; sinon le PDF suffit, signalez-le simplement.

---

## Variante avancée (si vous avez terminé en avance)

Reprenez `transactions-ecommerce.csv` et construisez une seconde page répondant à cette question : **quels états brésiliens méritent un effort logistique prioritaire ?**

Contraintes : au moins une métrique de **dispersion** doit apparaître, et l'effectif de commandes par état doit être visible partout où vous affichez une moyenne ou une médiane.

Si vous avez fait l'atelier SQL du jour 1, vous disposez déjà d'éléments de réponse. Citez-les.

---

## Livrables

1. **`cadrage.md`** : les quatre points de l'étape 1, rédigés **avant** la construction.
2. **`tableau-de-bord.pdf`** : l'export ou la capture.
3. **`notes-de-lecture.md`** contenant :
   - le lien de partage s'il existe ;
   - les trois constats chiffrés de votre restitution ;
   - la liste des choix techniques faits sur les champs, en particulier le traitement du champ `rang` et la façon dont vous avez obtenu une médiane ;
   - une section **limites** de trois lignes indiquant ce que ce tableau de bord ne dit pas.
4. **`USAGE-IA.md`** : selon le modèle en annexe.

---

## Critères de réussite

**Cadrage**
- [ ] La question tient en une phrase et se termine par un point d'interrogation.
- [ ] La décision associée est concrète et identifie qui la prend.
- [ ] Chaque visualisation est justifiée, et le rôle dimension ou métrique de chaque colonne est explicité.

**Construction**
- [ ] Le rapport tient sur une seule page, sans défilement.
- [ ] Le titre porte la source, l'année et l'effectif.
- [ ] Deux cartes de synthèse, dont au moins une médiane correctement obtenue.
- [ ] La comparaison régionale fait apparaître l'effectif de chaque région.
- [ ] Le classement montre les deux extrémités.
- [ ] Le nuage de points est correct : `pib_hab` en abscisse, `score_bonheur` en ordonnée, un point par pays.
- [ ] La zone de texte contient une affirmation **et** une limite explicite sur la causalité.
- [ ] Le filtre régional agit sur l'ensemble des graphiques.
- [ ] Le nombre de pays affiché est 156, pas 157.

**Restitution orale**
- [ ] La présentation tient dans les cinq minutes.
- [ ] Les deux membres du binôme parlent.
- [ ] Trois constats chiffrés maximum sont énoncés — un quatrième constat vous fait perdre des points, pas en gagner.
- [ ] Une limite est énoncée spontanément, sans qu'on ait à la demander.
- [ ] La recommandation est actionnable et nomme qui doit agir.
- [ ] Aucune phrase lue mot à mot sur une feuille.

**Notes de lecture**
- [ ] Le traitement du champ `rang` est expliqué.
- [ ] La formule exacte de la médiane est donnée.
- [ ] La section limites mentionne au moins une limite propre **au jeu de données**, et non à l'outil.

---

## Erreurs fréquentes à éviter

- Construire six graphiques qui disent la même chose. Quatre visualisations différentes valent mieux que huit redondantes.
- Empiler des indicateurs sans titre d'axe ni unité.
- Comparer des régions sans montrer les effectifs.
- Utiliser un graphique en secteurs pour comparer dix régions : au-delà de trois ou quatre parts, l'oeil ne compare plus des angles.
- Employer une échelle de couleurs qui suggère un jugement là où il n'y a qu'une catégorie.
- Laisser une moyenne là où la distribution est asymétrique, sans l'avoir vérifié.
- En restitution : commencer par décrire les graphiques au lieu de répondre à la question.

---

## Annexe — Fichier `USAGE-IA.md` (obligatoire)

```markdown
# Usage de l'IA — Atelier 6
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

**Attention particulière.** Les procédures d'interface changent souvent, et les modèles d'IA décrivent fréquemment des menus qui n'existent plus. Si une réponse vous indique un chemin de clics qui ne correspond pas à ce que vous voyez à l'écran, consignez-le dans la section « corrections ». C'est un cas d'école d'information périmée présentée avec assurance.

---

## Ressources

- Looker Studio : https://lookerstudio.google.com/
- Importer un fichier CSV : https://support.google.com/looker-studio/answer/7333350
- Créer une source de données Google Sheets : https://cloud.google.com/looker/docs/studio/tutorial-create-a-google-sheets-data-source?hl=fr
- Les champs calculés : https://support.google.com/looker-studio/answer/6299685
- Liste des fonctions : https://support.google.com/looker-studio/table/6379764
- Jeu de données d'origine : https://www.kaggle.com/datasets/unsdsn/world-happiness
