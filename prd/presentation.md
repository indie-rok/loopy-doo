<!-- Slide number: 1 -->

<!-- Slide number: 2 -->

Notre projet
Optimiser la gestion des traitements de support en oncologie via une application agenda
Dr SAYAH Lina, Institut cancérologique de Lorraine
Oncologie médicale
l.sayah@nancy.unicancer.fr
0669330258

<!-- Slide number: 3 -->

Présentation de la problématique

La prise en charge d’un patient sous chimiothérapie repose sur une multitude de traitements de support et symptomatiques indispensables à la prévention et à la gestion des effets indésirables : antiémétiques, corticoïdes, facteurs de croissance granulocytaires, érythropoïétine, traitements antalgiques, prévention de la mucite, etc.
Ces traitements s’inscrivent dans des schémas complexes, variables selon les protocoles de chimiothérapie, les cycles et l’évolution clinique du patient.
Pour les patients, cette complexité rend l’organisation quotidienne difficile, source de stress, de mauvaise compréhension de laquelle découle des erreurs de prise et un risque de mauvaise observance.
Cela entraîne pour les soignants, une perte de visibilité sur ce qui est réellement pris, augmente le nombre d’appels non programmés et expose les patients à des complications évitables.

À ce jour, il n’existe pas d’outil simple, centralisé et spécifiquement dédié à l’organisation des traitements de support autour de la chimiothérapie.
L’absence d’une solution adaptée constitue un frein majeur à l’optimisation du parcours de soins, à la qualité de vie des patients et à l’efficience du système de santé.

<!-- Slide number: 4 -->

Notre objectif concret durant le hackathon

Etablir une application ou web-app avec :

Une interface côté médecin :
Le médecin rentre des données sur le protocole de chimiothérapie
avec 5 données primordiales
qui génèrent une liste de médicaments

Une interface côté patient :
le patient entre l’heure de sa prochaine chimio et ça génère le planning (le plus important et imposant pour dimanche soir), avec des traitements à horaire fixe
Avec des notifications
Il coche ses prises

Et il a la possibilité d’ajouter des symptômes qui déclenchent des traitements à la demande via un bouton « j’ai un symptôme » avec 3 symptômes : nausées, diarrhée, douleurs

Et tout cela avec : cloud sécurisé, compatible avec l’hébergement de données de santé

<!-- Slide number: 5 -->

L’interface médecin
C’est la première partie qui doit être remplie.

J’imagine donc un algorithme avec plusieurs éléments au fur et à mesure :

1/ le cycle de chimiothérapie
2/ le niveau émétisant (=de nausées) de la chimiothérapie
3/ le niveau aplasiant (=de baisse de globules blancs) de la chimiothérapie
4/ le risque de mucite (=aphtes) de la chimiothérapie
5/ le risque de folliculite (=éruption cutanée) de la chimiothérapie

<!-- Slide number: 6 -->

1/ Les cycles de chimiothérapie

Il y a des dizaines de cycles de chimiothérapie possibles.

Les cycles ce sont les différents types d’intervalles entre les cures de chimiothérapie

On gardera deux exemples pour notre prototype :
FOLFOX : chimiothérapie administrée toutes les 2 semaines =  J1=C1 et J15=C2
GEMCITABINE : chimiothérapie administrée toutes les semaines pendant 3 semaines puis 1 semaine de pause
C1 = J1-J8-J15 et reprise à J21=C2

EN PRATIQUE, interface médecin : cocher le cycle du protocole de chimiothérapie du patient
☐J1 J15
☐J1 J8 J15

<!-- Slide number: 7 -->

2/ Le niveau émétisant (=nausées) de la chimiothérapie

Il y en a 3 et ils sont le socle des traitements de support des chimiothérapies avec des recommandations internationales
Il faut cependant que ça reste modifiable par le médecin car on s’adapte au fur et mesure aux patients

Hautement émétisant
Modérément émétisant
Faiblement émétisant

Plus c’est émétisant (=source de nausées) plus il y a de médicaments et pendant plus de jours

<!-- Slide number: 8 -->

2/ Le niveau émétisant (=nausées) de la chimiothérapie

1/ HAUTEMENT EMETISANT
Aprépitant comprimé : 125 mg à J1 (2h avant la chimio le jour de la chimio), 80 mg à J2 matin, 80 mg à J3 matin
Ondansétron comprimé : 8 mg 2h avant la chimio (J1)
Solupred comprimé 20 mg : 2 comprimés à J2 J3 le matin, 1 comprimé à  J3 J4 matin
Olanzapine 5 mg par voie orale le soir J2, J3, J4

2/ MODEREMENT EMETISANT
Ondansétron comprimé : 8 mg 2h avant la chimio (J1)
Solupred comprimé : 2 comprimés à J2 J3 le matin, 1 comprimé à  J3 J4 matin

3/ FAIBLEMENT EMETISANT
Aucun traitement systématique

-> si compliqué pour dimanche, on ne met pas le nombre de milligrammes, juste le nombre de comprimés

<!-- Slide number: 9 -->

<!-- Slide number: 10 -->

2/ Le niveau émétisant (=nausées) de la chimiothérapie

EN PRATIQUE, interface médecin : cocher le niveau émétisant du protocole de chimiothérapie du patient
☐ hautement émétisant
☐ moyennement émétisant
☐ faiblement émétisant

<!-- Slide number: 11 -->

3/ Le niveau aplasiant de la chimiothérapie
(= baisse des globules blancs)

Il y a deux traitements possibles seulement si le traitement est aplasiant

Pour choisir, c’est le cycle du traitement qui est pris en compte :

Si l’intervalle de chimio est de 14 jours au moins
Pegfilgrastim une seule injection 48h après la fin de la chimiothérapie

Si l’intervalle de chimio est de moins de 14 jours
Filgrastim à débuter aussi 48h après la fin de la chimiothérapie mais le nombre de jours d’administration doit être rentré par le médecin (3 ou 5 jours généralement)

<!-- Slide number: 12 -->

3/ Le niveau aplasiant de la chimiothérapie
(= baisse des globules blancs)

EN PRATIQUE

Si c’est faisable de prendre en compte le cycle
Interface médecin : cocher le niveau aplasiant du protocole de chimiothérapie du patient
☐ aplasiant
☐ non aplasiant

Si non faisable de prendre en compte le cycle dans un premier temps :

Interface médecin : avez vous prescrit un traitement aplasiant ?
☐ oui
☐ non

Et si oui :
☐ pegfilgrastim 6mg
☐ filgrastim 30 MUI 3 jours
☐ filgrastim 30 MUI 5 jours

<!-- Slide number: 13 -->

4/ Le risque de mucite (=aphtes)

Le risque de mucite est très fréquent
La prescription de bains de bouche au bicarbonate est quasi systématique.

On peut donc simplement imaginer une interface médecin avec :
 Avez-vous prescrit des bains de bouche au bicarbonate ?
☐ oui
☐ non

Et les inclure dans le planning

Posologie : 1 bain de bouche avant et après chaque repas

(plus tard, on pourra évoluer avec l’inclusion de bains de bouche plus spéciaux en fonction du niveau de gravité de la mucite, d’où l’intérêt de garder cette question)

<!-- Slide number: 14 -->

5/ Le risque de folliculite (=eruption cutanée)

En cas de folliculite, le patient prend en prévention un traitement antibiotique le soir.
On pourra imaginer ensuite que si il y a une folliculite sévère, on ajoutera des propositions supplémentaires.

Interface médecin :
Y a-t-il un risque de folliculite indiquant une prévention primaire ?
☐ oui
☐ non

Si oui : doxycycline 100 mg le soir au moins 30 minutes avant toute position allongée

<!-- Slide number: 15 -->

Notes sur l’interface médecin

INPUT :- emetisant_level- cycle_type- aplasia (yes/no)- mucite (yes/no)- folliculite (yes/no)PROCESS :→ rules engineOUTPUT :→ list of treatments per day

<!-- Slide number: 16 -->

Notes sur l’interface médecin

Pour le MVP (minimum viable product) -> on peut très bien imaginer rentrer :

Un seul cycle possible
Un seul risque émétisant
Un seul risque aplasiant

et c’est tout -> on va au plus simple et notre architecture permettra d’ajouter d’autres protocoles facilement

<!-- Slide number: 17 -->

Visuel imaginé de l’interface médecin

Veuillez entrer les données patient

Nom : DUPONT
Prénom : Bernard
DDN : 01/01/1965

<!-- Slide number: 18 -->

Visuel imaginé de l’interface médecin

Entrez le nom du protocole prescrit :

FOLFOX

<!-- Slide number: 19 -->

Visuel imaginé de l’interface médecin

Quel est le cycle du protocole de chimiothérapie prescrit :
X  J1 J15
☐J1 J8 J15

<!-- Slide number: 20 -->

Visuel imaginé de l’interface médecin

Quel est le niveau émétisant du protocole :
 X  hautement
☐ modérément
☐ faiblement

<!-- Slide number: 21 -->

Visuel imaginé de l’interface médecin

Le traitement est-il aplasiant ?
☐ oui
 x non

<!-- Slide number: 22 -->

Visuel imaginé de l’interface médecin

Y a-t-il un risque de mucite ?
 x oui
☐ non

<!-- Slide number: 23 -->

Visuel imaginé de l’interface médecin

Y a-t-il un risque de folliculite ?
☐ oui
 x non

<!-- Slide number: 24 -->

Visuel imaginé de l’interface médecin

Les données sont enregistrées.
Voulez vous générer l’agenda ?
 x oui
☐ non

<!-- Slide number: 25 -->

Visuel imaginé de l’interface médecin
(facultatif)

Voici l’agenda-type.
Vous pouvez y apporter des modifications.

Les dates et horaires apparaitront une fois la date et l’heure de chimiothérapie précisées.

<!-- Slide number: 26 -->

Visuel imaginé de l’interface médecin

L’identifiant du patient est 123456

Imprimez les codes d’accès à lui fournir

Option : imprimer l’agenda

<!-- Slide number: 27 -->

Visuel imaginé de l’interface médecin avec l’exemple pris
(prévoir bouton pour modifier :       )

![](Image9.jpg)
|  | J1 = jour chimio | J2 | J3 | J4 | J5 | J6 | J7 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 2h avant chimio | Aprépitant 125 mg Ondansétron 8 mg | Corticoides 2 comprimés | Corticoides 2 comprimés |  |  |  |  |
| Matin | Bains de bouche (BB) | Emend 80 mg BB | Emend 80 mg BB | BB | BB | BB | BB |
| Midi | BB | BB | BB | BB | BB | BB | BB |
| Soir | BB | Olanzapine 5 mg BB | Olanzapine 5 mg BB | Olanzapine 5 mg BB | BB | BB | BB |

<!-- Slide number: 28 -->

L’interface patient

1/ Le patient doit pouvoir rentrer date et heure de sa chimiothérapie et son poids
2/ Puis cliquer sur « générer mon planning »
3/ Et là, le planning est généré avec une vue « aujourd’hui » et agenda sur 7 jours

L’autre fonctionnalité concerne l’ajout de symptômes avec les traitements à la demande ; on imagine un bouton
« j’ai un symptôme », qu’il puisse cocher :
☐ nausée ou vomissement
☐ diarrhée
☐ douleur

Et que la conduite à tenir soit proposée au patient.
Si il coche qu’il a pris le médicament alors cela sera intégré dans l’agenda à postériori (et consultable par le
médecin)

<!-- Slide number: 29 -->

1/ Date et heure de la chimiothérapie

Format classique d’heure (24h) et de date (JJ/MM/AAAA)

Puis bouton « générer mon planning »

Qui génère l’agenda

<!-- Slide number: 30 -->

2/ Agenda

Heure par heure

Différentes couleurs en fonction du rôle du médicament
Vert pour les anti émétiques
Rouge pour les traitements contre l’aplasie
Bleu pour les bains de bouche
Gris pour les éruptions cutanées

Le patient coche si il a bien pris le traitement.
Il peut y avoir plusieurs traitements à la même heure.

Il ne peut pas décaler l’heure de prise.

<!-- Slide number: 31 -->

3/ J’ai un symptôme

Fonctionnalité pour les traitements à la demande

Soit sous formes de cases à cocher
☐ nausée ou vomissement
☐ diarrhée
☐ douleur

Soit faux chatbot sans IA où les mots clés sont détectés
☐ vomissements, nausées, vomir
☐ selles liquides, diarrhée, colique
☐ j’ai mal, douleur

<!-- Slide number: 32 -->

3/ J’ai un symptôme

Pour les nausées et vomissements :
Propose METOCLOPRAMIDE comprimé : 30 minutes avant les repas, pouvant être renouvelé toutes les 8h
Consigne d’hydratation
consulter aux urgences si besoin

Pour la diarrhée :
Propose DIOSMECTITE :  1 sachet jusque 3 fois par jour
Un délai d'au moins 2 heures avec la prise des autres traitements doit être respecté
Consigne d’éviter les fruits, fibres, laitages et viandes jusque résolution des symptômes
consulter aux urgences si besoin

Pour la douleur :
Propose PARACETAMOL gélule, dose selon poids
<50 kg : 500 mg pouvant être renouvelé toutes les 6h
>50 kg : 1000 mg pouvant être renouvelé toutes les 6h
consulter aux urgences si besoin

<!-- Slide number: 33 -->

Notes sur  “j’ai un symptome”

Pour le MVP (minimum viable product) :
On peut imaginer :
☐ nausée ou vomissement
☐ diarrhée
☐ douleur

Et ne rentrer les données générées que pour un seul symptôme -> on va au plus simple 

<!-- Slide number: 34 -->

Notes sur l’interface patient

Pour le MVP :

1)
Quelle est la date/heure de la prochaine chimiothérapie ?
Quel est votre poids ?

2)
Générer l’agenda
Imager une notification

3)
J’ai un symptôme, 3 propositions et 1 seule détaillée

Petit + : lorsque qu’on appuie un traitement ça dit en langage patient le rôle du traitement

<!-- Slide number: 35 -->

Visuel imaginé de l’interface patient

Rentrez vos identifiants patients fournis par le médecin

Identifiant : 123456
Mot de passe

<!-- Slide number: 36 -->

Visuel imaginé de l’interface patient

Quel et le jour et l’heure de votre prochaine chimiothérapie ?

Date : 12/10/2026
Heure : 08:30

<!-- Slide number: 37 -->

Visuel imaginé de l’interface patient

Quel est votre poids?

65 kg

<!-- Slide number: 38 -->

Visuel imaginé de l’interface patient

Générer l’agenda ?

 x oui
☐ non

<!-- Slide number: 39 -->

Visuel imaginé de l’interface patient

![](Image8.jpg)

<!-- Slide number: 40 -->

Pour moi à faire

Imprimer le PPT pour chacun

Imprimer les ordonnances de niveau émétisant, les ordonnances pegfilgrastim, filgrastim, bains de bouche,
Doxycyline

Phrase de fin de pitch dimanche, après la vidéo et avant la démo :
« Aujourd’hui, un patient sort avec une ordonnance complexe.Nous, on transforme cette ordonnance en un agenda clair, personnalisé et sécurisé
Nous présentons une première version, mais cette solution a vocation à devenir une plateforme complète»

Puis démo via enregistreur d’écran et nom d’appli
