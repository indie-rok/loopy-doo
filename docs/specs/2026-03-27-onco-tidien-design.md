# Onco-tidien — Product Specification

**Version**: 1.0
**Date**: 2026-03-27
**Author**: Dr. SAYAH Lina / Équipe Hackathon ICL
**Status**: Draft — En attente de validation PO
**Mockups**: [Doctor](https://indie-rok.github.io/loopy-doo/doctor.html) | [Patient](https://indie-rok.github.io/loopy-doo/patient.html)

---

## 1. Vision produit

Onco-tidien transforme une ordonnance complexe de chimiothérapie en un agenda clair, personnalisé et interactif. Le médecin configure un protocole en 5 paramètres ; le patient reçoit un planning quotidien avec rappels, confirmation de prises, et un assistant IA pour gérer les symptômes.

**Problème** : Les patients sous chimiothérapie reçoivent des traitements de support complexes (antiémétiques, corticoïdes, facteurs de croissance, bains de bouche, etc.) avec des schémas variables selon les protocoles et cycles. Cette complexité engendre stress, erreurs de prise, et mauvaise observance.

**Solution** : Une application mobile + web avec deux interfaces (médecin / patient) qui automatise la génération du planning médicamenteux et accompagne le patient au quotidien.

---

## 2. Utilisateurs

| Rôle | Profil | Objectif principal |
|---|---|---|
| **Médecin** | Oncologue hospitalier | Configurer un protocole, suivre l'observance, voir les symptômes signalés |
| **Patient** | Patient sous chimiothérapie | Savoir quoi prendre et quand, confirmer ses prises, signaler des symptômes |

---

## 3. Périmètre fonctionnel

### 3.1 Interface Médecin

#### 3.1.1 Création de patient

Le médecin saisit :
- **Nom**
- **Prénom**
- **Date de naissance**

Le système génère automatiquement un **code d'accès à 6 chiffres** unique. Ce code est le seul moyen d'authentification du patient (pas de login/mot de passe traditionnel).

#### 3.1.2 Configuration du protocole

Le médecin renseigne 5 paramètres pour chaque patient :

| # | Paramètre | Options | Impact sur le planning |
|---|---|---|---|
| 1 | **Cycle de chimiothérapie** | `J1-J15` (FOLFOX) / `J1-J8-J15` (Gemcitabine) | Détermine l'intervalle entre cures et le type de G-CSF |
| 2 | **Niveau émétisant** | Hautement / Modérément / Faiblement | Détermine les antiémétiques prescrits (voir §4.1) |
| 3 | **Traitement aplasiant** | Oui/Non → si oui : Pegfilgrastim / Filgrastim 3j / Filgrastim 5j | Ajoute G-CSF au planning |
| 4 | **Risque de mucite** | Oui/Non | Ajoute bains de bouche bicarbonate |
| 5 | **Risque de folliculite** | Oui/Non | Ajoute Doxycycline 100mg le soir |

**Nom du protocole** : champ texte libre (ex : "FOLFOX", "EC", "GEMCITABINE").

#### 3.1.3 Génération et édition de l'agenda

Le moteur de règles (§4.1) génère un planning template sur 7 jours (J1 à J7) avec 4 créneaux horaires :
- **2h avant chimio**
- **Matin**
- **Midi**
- **Soir**

Le médecin peut **modifier le planning généré** avant de le valider :
- Ajouter / supprimer un médicament dans une case
- Modifier la posologie
- Les modifications sont enregistrées comme surcharges du template généré

Après validation → le code patient est affiché et imprimable.

#### 3.1.4 Dashboard patient (suivi)

Pour chaque patient, le médecin voit :
- **Taux d'adhésion** : % de prises confirmées vs prises prévues
- **Symptômes signalés** : type, date/heure, traitement recommandé
- **Médicaments ajoutés via symptômes** : médicaments à la demande intégrés au planning
- **Historique chatbot** : conversations entre le patient et l'assistant IA
- **Streak d'observance** : nombre de jours consécutifs à 100% (voir §3.3)

#### 3.1.5 Liste des patients

Vue d'ensemble de tous les patients avec :
- Nom, protocole, jour courant du cycle (ex : J3)
- Taux d'adhésion (barre de progression)
- Alertes : symptômes récents, prises manquées

---

### 3.2 Interface Patient

#### 3.2.1 Onboarding (première connexion)

1. Le patient saisit son **code à 6 chiffres**
2. Il renseigne :
   - **Date et heure de la prochaine chimiothérapie** (format JJ/MM/AAAA + HH:MM 24h)
   - **Poids** (en kg — utilisé pour le dosage du Paracétamol)
3. Le système hydrate le template (jours relatifs → dates réelles) :
   - J1 = date saisie
   - "2h avant chimio" = heure saisie - 2h
   - Matin = 08h00, Midi = 12h00, Soir = 20h00

#### 3.2.2 Vue Aujourd'hui (écran principal)

**Header** : Nom du patient + jour relatif + date réelle (ex : "J3 — Samedi 17 Oct.")

**Timeline verticale** organisée par créneau horaire (Matin — 08h00, Midi — 12h00, Soir — 20h00). Chaque médicament est affiché dans une carte contenant :

- **Icône du type** : 💊 comprimé, 💉 injection, 🫗 liquide (bain de bouche)
- **Bande de couleur latérale** selon la catégorie :
  - 🟢 Vert (`#10B981`) : antiémétiques
  - 🔴 Rouge (`#EF4444`) : traitement aplasiant
  - 🔵 Bleu (`#3B82F6`) : bains de bouche
  - ⚪ Gris (`#6B7280`) : prévention folliculite
  - 🟠 Orange (`#F97316`) : médicaments ajoutés suite à un symptôme
- **Nom + posologie**
- **"ℹ️ À quoi ça sert ?"** : lien cliquable qui déploie une explication en langage patient (pas de jargon médical)
- **Case de confirmation** : le patient coche "Pris ✓" → enregistre l'heure exacte de confirmation

**États visuels** :
- Non pris (à venir) : carte normale
- Confirmé : carte grisée + checkmark bleu
- Manqué (créneau passé, non confirmé) : surbrillance rouge subtile

#### 3.2.3 Chatbot symptômes (Assistant IA)

**Accès** : onglet "Symptôme" dans la navigation en bas.

**Périmètre strict de l'IA** :
- L'IA connaît UNIQUEMENT : le protocole du patient, son planning actuel, les médicaments prescrits, et son poids
- L'IA ne peut PAS : inventer des médicaments hors de la liste prédéfinie (§4.2), donner des conseils médicaux généraux, diagnostiquer

**Interaction** :
1. Le patient peut utiliser les **boutons rapides** (Nausée, Diarrhée, Douleur) ou **écrire en texte libre**
2. L'IA détecte le symptôme (par mots-clés pour le MVP, classification NLP ensuite)
3. L'IA recommande le traitement à la demande correspondant (§4.2) avec posologie
4. Le patient confirme → **le médicament est injecté dans son planning** :
   - Ajouté au créneau horaire approprié
   - Affiché en orange dans la vue Aujourd'hui et l'Agenda
   - Visible par le médecin dans le dashboard
5. Chaque réponse inclut un conseil hygiéno-diététique + message de sécurité ("Consultez les urgences si...")

**Détection de mots-clés** (MVP) :

| Symptôme | Mots-clés détectés |
|---|---|
| Nausée | nausée, nausées, vomir, vomissement, vomissements |
| Diarrhée | diarrhée, selles liquides, colique |
| Douleur | douleur, j'ai mal, mal |

Messages hors périmètre → "Je ne peux répondre qu'aux symptômes liés à votre traitement. Contactez votre équipe soignante."

#### 3.2.4 Vue Agenda (7 jours)

Tableau 7 colonnes × 4 lignes :
- **Colonnes** : J1 — Lun 15/10, J2 — Mar 16/10, etc. (dates réelles)
- **Lignes** : 2h Avant (07h), Matin (08h), Midi (12h), Soir (20h)
- Chaque cellule : pastilles colorées avec nom du médicament + icône
- Les médicaments ajoutés via symptômes apparaissent en orange
- Vue en lecture seule (pas de modification par le patient)

**Légende** affichée sous le tableau.

---

### 3.3 Gamification — Streak d'observance

Système de motivation inspiré de Duolingo pour encourager l'adhésion au traitement.

#### Règles du streak

- **Un jour compte comme "complété"** si et seulement si **100% des médicaments prévus** ce jour sont confirmés comme pris
- Le streak s'incrémente de +1 pour chaque jour consécutif complété
- Si un jour est manqué (même 1 seul médicament non confirmé) → **le streak revient à 0**
- Le streak n'est actif que les jours où des médicaments sont prévus (les jours sans traitement ne cassent pas le streak)

#### Affichage

**Patient** :
- Widget en haut de la vue Aujourd'hui : 🔥 icône flamme + nombre de jours (ex : "🔥 5 jours")
- Animation de célébration quand le patient complète tous les médicaments du jour
- Paliers visuels : 3 jours → flamme bronze, 7 jours → argent, 14 jours → or
- Message d'encouragement contextuel :
  - "Bravo, 5 jours d'affilée ! Votre corps vous remercie."
  - "Continuez comme ça, vous êtes sur la bonne voie !"

**Médecin** :
- Streak affiché sur la fiche patient dans le dashboard
- Historique : streak maximum atteint, nombre de ruptures

---

### 3.4 Notifications

Système de rappels push personnalisés selon le planning du patient.

#### Règles de notification

| Règle | Déclencheur | Contenu | Timing |
|---|---|---|---|
| **Rappel médicament** | Chaque prise prévue au planning | "Il est l'heure de prendre [Nom] [Posologie]" | À l'heure exacte de la prise |
| **Prise manquée** | Médicament non confirmé après 30 min | "Avez-vous pris votre [Nom] ? N'oubliez pas de confirmer." | +30 min après l'heure prévue |
| **J-2 : Prise de sang** | 2 jours avant la prochaine chimiothérapie | "Rappel : pensez à faire votre prise de sang avant votre prochaine séance de chimiothérapie." | J-2, 09h00 |
| **J-1 : Rappel chimio** | Veille de la chimiothérapie | "Votre séance de chimiothérapie est demain à [heure]. Pensez à prendre vos médicaments préparatoires." | J-1, 18h00 |

#### Comportement technique

- **MVP** : notifications push locales (pas de serveur requis)
- Le patient peut **désactiver** les notifications dans les paramètres
- Cliquer sur une notification ouvre l'app sur la vue Aujourd'hui avec le médicament concerné mis en surbrillance
- Les notifications respectent les horaires : pas de notification entre 22h et 07h (mode nuit)

---

### 3.5 Export PDF du calendrier

Le patient ou le médecin peut exporter le planning complet sous forme de PDF imprimable.

#### Contenu du PDF

- En-tête : nom du patient, protocole, dates du cycle
- Tableau 7 jours × 4 créneaux avec tous les médicaments
- Code couleur préservé (catégories de médicaments)
- Légende des couleurs
- Pied de page : "Généré par Onco-tidien — [date]"

#### Points d'accès

- **Médecin** : bouton "Imprimer la fiche" sur l'écran du code patient + sur le dashboard
- **Patient** : bouton dans la vue Agenda

---

## 4. Moteur de règles

### 4.1 Règles de génération du planning

Les règles ci-dessous transforment les 5 paramètres du médecin en un planning médicamenteux.

#### Antiémétiques (selon le niveau émétisant)

**Hautement émétisant** :

| Médicament | J1 | J2 | J3 | J4 |
|---|---|---|---|---|
| Aprépitant 125mg | 2h avant chimio | — | — | — |
| Aprépitant 80mg | — | Matin | Matin | — |
| Ondansétron 8mg | 2h avant chimio | — | — | — |
| Solupred 20mg (2cp) | — | Matin | Matin | — |
| Solupred 20mg (1cp) | — | — | — | Matin |
| Olanzapine 5mg | — | Soir | Soir | Soir |

**Modérément émétisant** :

| Médicament | J1 | J2 | J3 | J4 |
|---|---|---|---|---|
| Ondansétron 8mg | 2h avant chimio | — | — | — |
| Solupred 20mg (2cp) | — | Matin | Matin | — |
| Solupred 20mg (1cp) | — | — | — | Matin |

**Faiblement émétisant** : aucun traitement systématique.

#### Traitement aplasiant (G-CSF)

| Condition | Médicament | Planning |
|---|---|---|
| Cycle ≥ 14 jours (J1-J15) | Pegfilgrastim 6mg | 1 injection unique à J3 (48h post-chimio) |
| Cycle < 14 jours + 3 jours | Filgrastim 30 MUI | J3, J4, J5 |
| Cycle < 14 jours + 5 jours | Filgrastim 30 MUI | J3, J4, J5, J6, J7 |

#### Mucite (bains de bouche)

Si activé : **Bains de bouche bicarbonate** — 1 avant et 1 après chaque repas → Matin + Midi + Soir, tous les jours du cycle (J1 à J7+).

#### Folliculite (Doxycycline)

Si activé : **Doxycycline 100mg** — 1 comprimé le soir, au moins 30 min avant toute position allongée, tous les jours du cycle.

### 4.2 Traitements à la demande (symptômes)

| Symptôme | Médicament | Posologie | Règle de timing | Conseil associé |
|---|---|---|---|---|
| **Nausée / vomissement** | Métoclopramide cp | 1 comprimé | 30 min avant repas, renouvelable toutes les 8h | Bien s'hydrater régulièrement |
| **Diarrhée** | Diosmectite sachet | 1 sachet | Max 3x/jour, **2h d'écart** avec les autres médicaments | Éviter fruits, fibres, laitages, viandes |
| **Douleur** | Paracétamol gélule | <50kg : 500mg / ≥50kg : 1000mg | Renouvelable toutes les 6h, max 4x/jour | — |

**Chaque traitement à la demande inclut le message** : "Si les symptômes persistent ou s'aggravent, consultez les urgences (15)."

### 4.3 Explications patient (langage simple)

| Médicament | Explication affichée |
|---|---|
| Aprépitant | Empêche les nausées les jours suivant la chimiothérapie |
| Ondansétron | Médicament anti-nausée à prendre avant la chimiothérapie |
| Solupred | Aide à prévenir les nausées qui peuvent apparaître après votre chimiothérapie |
| Olanzapine | Aide à prévenir les nausées tardives, à prendre le soir avant de dormir |
| Pegfilgrastim | Aide votre corps à fabriquer des globules blancs pour vous protéger des infections |
| Filgrastim | Stimule la production de globules blancs pendant plusieurs jours après la chimio |
| Bains de bouche | Protège votre bouche contre les aphtes causés par le traitement |
| Doxycycline | Prévient les éruptions cutanées liées au traitement |
| Métoclopramide | Aide à calmer les nausées, à prendre 30 minutes avant le repas |
| Diosmectite | Protège votre intestin et aide à réduire la diarrhée |
| Paracétamol | Soulage la douleur, ne dépassez pas 4 prises par jour |

---

## 5. Authentification

**Pas de login/mot de passe traditionnel.**

- Le médecin accède à son interface sans authentification (interface unique, pas de multi-tenant pour le MVP)
- Le patient s'authentifie avec un **code à 6 chiffres** généré par le système lors de la création du patient
- Le code crée une session persistante sur l'appareil du patient
- Pas de récupération de code : si perdu, le médecin peut régénérer un nouveau code

---

## 6. Design & UX

### Principes

- **Mobile-first** : optimisé pour smartphone (max-width 430px), utilisable sur desktop
- **Langue** : tout en français
- **Accessibilité** : texte lisible (16px minimum), contrastes suffisants, zones de tap larges (48px)
- **Ton** : professionnel mais bienveillant — pas clinique, pas infantilisant

### Palette de couleurs

| Usage | Couleur | Hex |
|---|---|---|
| Primaire (actions, liens) | Bleu | `#2563EB` |
| Antiémétiques | Vert | `#10B981` |
| Aplasie (G-CSF) | Rouge | `#EF4444` |
| Bains de bouche | Bleu | `#3B82F6` |
| Folliculite | Gris | `#6B7280` |
| Symptômes (à la demande) | Orange | `#F97316` |
| Fond app | Gris clair | `#F8FAFC` |
| Texte principal | Noir profond | `#0F172A` |

### Icônes médicaments

| Type | Icône |
|---|---|
| Comprimé / gélule | 💊 |
| Injection | 💉 |
| Bain de bouche / liquide | 🫗 |

---

## 7. Stack technique

| Composant | Technologie |
|---|---|
| App mobile (patient) | Expo (React Native) |
| Interface web (médecin) | Next.js |
| Base de données | SQLite (locale pour MVP) |
| Notifications | Expo Notifications (push locales) |
| Chatbot IA | LLM avec prompt contraint + contexte patient |
| Export PDF | Bibliothèque de génération PDF côté client |

---

## 8. Données & modèle

### Entités principales

```
Patient {
  id: string (UUID)
  access_code: string (6 chiffres)
  nom: string
  prenom: string
  date_naissance: date
  poids: number (kg)
  chemo_datetime: datetime
  created_at: datetime
}

ProtocolConfig {
  id: string
  patient_id: FK → Patient
  protocol_name: string
  cycle_type: "J1-J15" | "J1-J8-J15"
  emetic_level: "haut" | "mod" | "faible"
  aplastic_enabled: boolean
  aplastic_type: "Pegfilgrastim" | "Filgrastim 3j" | "Filgrastim 5j" | null
  mucositis_enabled: boolean
  folliculitis_enabled: boolean
}

ScheduleEntry {
  id: string
  patient_id: FK → Patient
  medication_name: string
  dosage: string
  relative_day: number (1-7+)
  time_slot: "2H_AVANT" | "MATIN" | "MIDI" | "SOIR"
  category: "anti_emetic" | "aplastic" | "mouthwash" | "folliculitis" | "symptom"
  source: "generated" | "doctor_edit" | "symptom_chatbot"
  actual_datetime: datetime | null (hydrated after patient enters chemo date)
  confirmed_at: datetime | null
  is_taken: boolean
}

SymptomReport {
  id: string
  patient_id: FK → Patient
  symptom_type: "nausea" | "diarrhea" | "pain"
  reported_at: datetime
  medication_added: string | null
  chatbot_conversation: JSON
}

Streak {
  patient_id: FK → Patient
  current_streak: number
  max_streak: number
  last_completed_date: date
}
```

---

## 9. Priorisation MVP

### P0 — Essentiel pour la démo

- [ ] Création patient (nom, prénom, DDN → code)
- [ ] Configuration protocole (5 paramètres)
- [ ] Moteur de règles → génération du planning
- [ ] Édition du planning par le médecin
- [ ] Onboarding patient (code + date chimio + poids)
- [ ] Vue Aujourd'hui avec confirmation des prises
- [ ] Chatbot symptômes (3 symptômes) avec injection dans le planning
- [ ] Vue Agenda 7 jours avec dates réelles

### P1 — Important pour convaincre

- [ ] Notifications push (rappels médicaments + prise manquée)
- [ ] J-2 prise de sang + J-1 rappel chimio
- [ ] Dashboard médecin (adhésion, symptômes, historique)
- [ ] Gamification streak (flamme + animation)

### P2 — Bonus

- [ ] Export PDF du calendrier
- [ ] Explications patient (ℹ️ À quoi ça sert ?)
- [ ] Icônes type médicament (💊💉🫗)
- [ ] Paliers streak (bronze/argent/or)

---

## 10. Questions ouvertes

| # | Question | Impact |
|---|---|---|
| 1 | Le planning doit-il s'étendre au-delà de 7 jours (cycle J1-J15) ? | Architecture du moteur de règles |
| 2 | Faut-il gérer plusieurs cycles consécutifs (C1, C2, C3...) ? | Complexité du modèle de données |
| 3 | Le chatbot doit-il garder un historique persistant entre sessions ? | Stockage et UX |
| 4 | Hébergement HDS (Hébergeur de Données de Santé) requis pour la prod ? | Choix d'infrastructure |
| 5 | Le médecin doit-il pouvoir modifier le planning après que le patient a commencé ? | Synchronisation temps réel |

---

## Annexe A — Prototypes interactifs

- **Doctor flow** : https://indie-rok.github.io/loopy-doo/doctor.html
- **Patient flow** : https://indie-rok.github.io/loopy-doo/patient.html
