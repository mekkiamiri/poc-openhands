# ARIA - AI Risk & Impact Assessment

## Spécification Fonctionnelle MVP

**Version :** 1.0
**Date :** Janvier 2025
**Statut :** Draft

---

## Synthèse Exécutive

### Pourquoi ARIA ?

Le Groupe EDF et ses filiales déploient un nombre croissant de cas d'usage d'Intelligence Artificielle. L'entrée en vigueur de l'**AI Act européen** impose un cadre réglementaire strict avec des obligations de classification, documentation et supervision des systèmes IA.

**ARIA** (AI Risk & Impact Assessment) est un outil simple et centralisé permettant de :

| Enjeu | Solution ARIA |
|-------|---------------|
| **Conformité AI Act** | Classification automatique en 4 niveaux de risque |
| **Visibilité Groupe** | Registre centralisé de tous les cas d'usage IA |
| **Gouvernance** | Workflow de validation métier → gouvernance |
| **Traçabilité** | Historique complet et export CSV |

### Bénéfices Clés

- **Pour les métiers** : Déclarer un cas d'usage en moins de 15 minutes
- **Pour la gouvernance** : Vue consolidée et validation en 1 clic
- **Pour le Groupe** : Conformité réglementaire et maîtrise des risques

### Périmètre MVP

| Inclus | Hors périmètre (futures versions) |
|--------|-----------------------------------|
| Formulaire de criblage (9 sections) | Analyses IA automatisées |
| Classification AI Act automatique | Intégrations API externes |
| Workflow 2 profils (métier/gouvernance) | Cycle de vie post-déploiement |
| Export CSV exhaustif (104 champs) | Dashboards avancés |

### Cibles Utilisateurs

- **Filiales EDF** : Toutes entités du Groupe
- **Profil Métier** : Porteurs de projets IA
- **Profil Gouvernance** : Équipe Gouvernance IA Groupe

---

## 1. Introduction et Objectifs

### 1.1 Contexte

Le Groupe EDF déploie de nombreux cas d'usage d'Intelligence Artificielle. L'équipe Gouvernance IA Groupe a besoin d'un outil simple pour :
- **Recenser** les cas d'usage IA
- **Évaluer** leur conformité avec l'AI Act européen
- **Valider** leur déploiement selon un workflow simplifié

### 1.2 Objectifs MVP

| Objectif | Description |
|----------|-------------|
| **Centraliser** | Un point d'entrée unique pour déclarer les cas d'usage IA |
| **Classifier** | Déterminer automatiquement le niveau de risque AI Act |
| **Valider** | Workflow simple métier → gouvernance |
| **Tracer** | Historique des actions et export CSV |

### 1.3 Périmètre MVP

**Inclus :**
- Formulaire de criblage complet
- Classification automatique AI Act (4 niveaux)
- Workflow de validation à 2 profils
- Export CSV

**Hors périmètre :**
- Analyses IA automatisées
- Intégrations API externes
- Gestion du cycle de vie post-déploiement

---

## 2. Profils Utilisateurs

### 2.1 Vue d'ensemble

```
┌─────────────────────────────────────────────────────────────────┐
│                    PROFILS UTILISATEURS                         │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   ┌─────────────────┐              ┌─────────────────┐          │
│   │     MÉTIER      │              │   GOUVERNANCE   │          │
│   │    (Porteur)    │ ──────────▶  │   (Validateur)  │          │
│   └─────────────────┘   Soumet     └─────────────────┘          │
│                                                                 │
│   • Crée un cas d'usage            • Consulte les soumissions   │
│   • Remplit le formulaire          • Valide ou rejette          │
│   • Soumet pour validation         • Ajoute des commentaires    │
│   • Modifie (si brouillon)         • Exporte les données        │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### 2.2 Détail des profils

| Profil | Description | Droits |
|--------|-------------|--------|
| **Métier** | Porteur de projet IA (chef de projet, data scientist, métier) | Créer, Modifier (brouillon), Soumettre, Consulter ses cas |
| **Gouvernance** | Acteur gouvernance IA Groupe | Consulter tous, Valider, Rejeter, Commenter, Exporter CSV |

---

## 3. Workflow et Statuts

### 3.1 Diagramme de flux

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                           WORKFLOW DE VALIDATION                            │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│    ┌──────────┐      ┌──────────┐      ┌──────────┐      ┌──────────┐      │
│    │          │      │          │      │          │      │          │      │
│    │ BROUILLON│─────▶│  SOUMIS  │─────▶│  VALIDÉ  │      │  REJETÉ  │      │
│    │          │      │          │      │          │      │          │      │
│    └──────────┘      └────┬─────┘      └──────────┘      └──────────┘      │
│         │                 │                                    ▲            │
│         │                 │         ┌──────────┐               │            │
│         │                 └────────▶│COMMENTÉ  │───────────────┘            │
│         │                           │(retour)  │                            │
│         │                           └────┬─────┘                            │
│         │                                │                                  │
│         └────────────────────────────────┘                                  │
│                     Modifie et re-soumet                                    │
│                                                                             │
├─────────────────────────────────────────────────────────────────────────────┤
│  LÉGENDE :                                                                  │
│  ─────▶  Action Métier       ─────▶  Action Gouvernance                     │
└─────────────────────────────────────────────────────────────────────────────┘
```

### 3.2 Description des statuts

| Statut | Description | Actions possibles |
|--------|-------------|-------------------|
| **Brouillon** | Cas en cours de saisie | Métier : Modifier, Supprimer, Soumettre |
| **Soumis** | En attente de validation | Gouvernance : Valider, Rejeter, Commenter |
| **Validé** | Approuvé par la gouvernance | Consultation uniquement |
| **Rejeté** | Refusé (avec motif obligatoire) | Consultation uniquement |
| **Commenté** | Retour avec demande de complément | Métier : Modifier, Re-soumettre |

### 3.3 Notifications

| Événement | Destinataire | Canal |
|-----------|--------------|-------|
| Soumission | Gouvernance | Email |
| Validation | Métier (porteur) | Email |
| Rejet | Métier (porteur) | Email |
| Commentaire | Métier (porteur) | Email |

---

## 4. Formulaire de Criblage

### 4.1 Vue d'ensemble du formulaire

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                        ARIA - FORMULAIRE DE CRIBLAGE                        │
│                                                                             │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │ A. IDENTIFICATION                                           [1/9]   │   │
│  └─────────────────────────────────────────────────────────────────────┘   │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │ B. CARACTÉRISTIQUES TECHNIQUES                              [2/9]   │   │
│  └─────────────────────────────────────────────────────────────────────┘   │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │ C. DROITS FONDAMENTAUX                                      [3/9]   │   │
│  └─────────────────────────────────────────────────────────────────────┘   │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │ D. AI ACT - BLOC INTERDIT                                   [4/9]   │   │
│  └─────────────────────────────────────────────────────────────────────┘   │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │ E. AI ACT - BLOC HAUT RISQUE                                [5/9]   │   │
│  └─────────────────────────────────────────────────────────────────────┘   │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │ F. DONNÉES ET RGPD                                          [6/9]   │   │
│  └─────────────────────────────────────────────────────────────────────┘   │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │ G. TRANSPARENCE ET SUPERVISION                              [7/9]   │   │
│  └─────────────────────────────────────────────────────────────────────┘   │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │ H. IMPACT RH / DIALOGUE SOCIAL                              [8/9]   │   │
│  └─────────────────────────────────────────────────────────────────────┘   │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │ I. COMMENTAIRES ET JUSTIFICATIONS                           [9/9]   │   │
│  └─────────────────────────────────────────────────────────────────────┘   │
│                                                                             │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │                    CLASSIFICATION AI ACT                            │   │
│  │                    ══════════════════════                           │   │
│  │                    [ RISQUE MINIMAL ]     (calculé automatiquement) │   │
│  └─────────────────────────────────────────────────────────────────────┘   │
│                                                                             │
│            [Enregistrer brouillon]        [Soumettre pour validation]       │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

### 4.2 Section A : Identification

```
┌─────────────────────────────────────────────────────────────────────────────┐
│  A. IDENTIFICATION DU CAS D'USAGE                                          │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  Nom du cas d'usage *                                                       │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │                                                                     │   │
│  └─────────────────────────────────────────────────────────────────────┘   │
│                                                                             │
│  Description / Besoin *                                                     │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │                                                                     │   │
│  │                                                                     │   │
│  │                                                                     │   │
│  └─────────────────────────────────────────────────────────────────────┘   │
│                                                                             │
│  Direction *                          Sous-direction                        │
│  ┌─────────────────────┐              ┌─────────────────────┐              │
│  │ [Sélectionner ▼]    │              │                     │              │
│  └─────────────────────┘              └─────────────────────┘              │
│                                                                             │
│  BPDO responsable *                   Porteur (email) *                     │
│  ┌─────────────────────┐              ┌─────────────────────┐              │
│  │                     │              │                     │              │
│  └─────────────────────┘              └─────────────────────┘              │
│                                                                             │
│  Date de lancement prévue             Nombre d'utilisateurs cible           │
│  ┌─────────────────────┐              ┌─────────────────────┐              │
│  │ [JJ/MM/AAAA]        │              │                     │              │
│  └─────────────────────┘              └─────────────────────┘              │
│                                                                             │
│  * Champs obligatoires                                                      │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

**Spécification des champs :**

| Champ | Type | Obligatoire | Validation |
|-------|------|-------------|------------|
| Nom du cas d'usage | Texte (255 car.) | Oui | Non vide |
| Description / Besoin | Textarea (2000 car.) | Oui | Min 50 caractères |
| Direction | Liste déroulante | Oui | Liste prédéfinie |
| Sous-direction | Texte (100 car.) | Non | - |
| BPDO responsable | Texte (100 car.) | Oui | Non vide |
| Porteur (email) | Email | Oui | Format email valide |
| Date de lancement | Date | Non | Date ≥ aujourd'hui |
| Nombre d'utilisateurs | Nombre entier | Non | ≥ 0 |

---

### 4.3 Section B : Caractéristiques Techniques

```
┌─────────────────────────────────────────────────────────────────────────────┐
│  B. CARACTÉRISTIQUES TECHNIQUES                                            │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  Techniques IA utilisées (plusieurs choix possibles) *                      │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │ ☐ IA générative                    ☐ Agents                         │   │
│  │ ☐ Machine learning                 ☐ Algorithmie avancée            │   │
│  │ ☐ Traitement automatique langues   ☐ Traitement de l'image          │   │
│  │ ☐ Traitement du son                ☐ Autre : _______________        │   │
│  └─────────────────────────────────────────────────────────────────────┘   │
│                                                                             │
│  Type de modèle (si pertinent)                                              │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │ ○ Open-source                                                       │   │
│  │ ○ Propriétaire                                                      │   │
│  │ ○ Développé en interne                                              │   │
│  │ ○ Non applicable                                                    │   │
│  └─────────────────────────────────────────────────────────────────────┘   │
│                                                                             │
│  Source des données (plusieurs choix possibles)                             │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │ ☐ Interne                          ☐ Externe                        │   │
│  └─────────────────────────────────────────────────────────────────────┘   │
│                                                                             │
│  Environnement d'exécution (plusieurs choix possibles)                      │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │ ☐ On-premise                       ☐ Portail IAG - Interne          │   │
│  │ ☐ Portail IAG - Cloud              ☐ Cloud                          │   │
│  │ ☐ Autre (via outil d'entreprise)                                    │   │
│  └─────────────────────────────────────────────────────────────────────┘   │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

**Spécification des champs :**

| Champ | Type | Obligatoire | Valeurs possibles |
|-------|------|-------------|-------------------|
| Techniques IA | Checkboxes | Oui (min 1) | IA générative, Agents, Machine learning, Algorithmie avancée, TAL, Image, Son, Autre |
| Type de modèle | Radio | Non | Open-source, Propriétaire, Interne, N/A |
| Source données | Checkboxes | Non | Interne, Externe |
| Environnement | Checkboxes | Non | On-premise, Portail IAG Interne, Portail IAG Cloud, Cloud, Autre |

---

### 4.4 Section C : Droits Fondamentaux

```
┌─────────────────────────────────────────────────────────────────────────────┐
│  C. IMPACT SUR LES DROITS FONDAMENTAUX                                     │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  L'IA a-t-elle un impact sur un ou plusieurs droits fondamentaux ?         │
│                                                                             │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │ ☐ Dignité humaine                  ☐ Droit à la liberté/sécurité    │   │
│  │ ☐ Respect vie privée/familiale     ☐ Liberté d'expression/info      │   │
│  │ ☐ Liberté de réunion/association   ☐ Non-discrimination             │   │
│  │ ☐ Protection des consommateurs     ☐ Droit des travailleurs         │   │
│  │ ☐ Droit personnes handicapées      ☐ Égalité de genre               │   │
│  │ ☐ Propriété intellectuelle         ☐ Autre                          │   │
│  │ ☐ Aucun impact identifié                                            │   │
│  └─────────────────────────────────────────────────────────────────────┘   │
│                                                                             │
│  Si impact identifié, précisez :                                            │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │                                                                     │   │
│  │                                                                     │   │
│  └─────────────────────────────────────────────────────────────────────┘   │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

### 4.5 Section D : AI Act - Bloc Interdit

```
┌─────────────────────────────────────────────────────────────────────────────┐
│  D. CONFORMITÉ AI ACT - BLOC INTERDIT                                      │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  ⚠️  ATTENTION : Si vous répondez OUI, le système sera classé INTERDIT     │
│      selon l'AI Act européen.                                               │
│                                                                             │
│  ─────────────────────────────────────────────────────────────────────────  │
│                                                                             │
│  Votre IA est-elle susceptible d'être utilisée pour l'un des usages        │
│  suivants ? *                                                               │
│                                                                             │
│  • Manipulation comportementale (techniques subliminales)                   │
│  • Exploitation de vulnérabilités (âge, handicap, situation socio-éco)     │
│  • Notation sociale (social scoring)                                        │
│  • Profilage criminel (prédiction risque criminel)                         │
│  • Biométrie abusive (collecte massive reconnaissance faciale)             │
│  • Reconnaissance émotionnelle au travail                                   │
│  • Catégorisation biométrique sensible (race, religion, orientation...)    │
│                                                                             │
│     ○ Oui    ○ Non                                                          │
│                                                                             │
│  Si oui, précisez :                                                         │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │                                                                     │   │
│  │                                                                     │   │
│  └─────────────────────────────────────────────────────────────────────┘   │
│                                                                             │
│  ─────────────────────────────────────────────────────────────────────────  │
│  Résultat section D : [ NON ] → ✅ Pas de blocage                           │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

**Règle de classification :** Si "Oui" → **SYSTÈME INTERDIT**

---

### 4.6 Section E : AI Act - Bloc Haut Risque

```
┌─────────────────────────────────────────────────────────────────────────────┐
│  E. CONFORMITÉ AI ACT - BLOC HAUT RISQUE                                   │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  ℹ️  Si vous répondez OUI à l'une de ces questions, le système sera        │
│      classé HAUT RISQUE et nécessitera un dialogue social spécifique.      │
│                                                                             │
│  ─────────────────────────────────────────────────────────────────────────  │
│                                                                             │
│  1. Identification biométrique à distance                                   │
│     L'IA identifie-t-elle des personnes à partir de caractéristiques       │
│     physiques (reconnaissance faciale, caméra augmentée) ?                  │
│     ○ Oui    ○ Non                                                          │
│     Précisions (optionnel) : [____________________________________]         │
│                                                                             │
│  2. Évaluation biométrique sensible                                         │
│     L'IA détermine-t-elle des attributs protégés (religion, orientation    │
│     sexuelle, origine ethnique) à partir de capteurs ?                      │
│     ○ Oui    ○ Non                                                          │
│     Précisions (optionnel) : [____________________________________]         │
│                                                                             │
│  3. Reconnaissance ou déduction des émotions                                │
│     L'IA analyse-t-elle images, vidéos ou voix pour détecter l'état        │
│     émotionnel (hors contexte médical) ?                                    │
│     ○ Oui    ○ Non                                                          │
│     Précisions (optionnel) : [____________________________________]         │
│                                                                             │
│  4. Sécurisation d'infrastructures critiques                                │
│     L'IA est-elle utilisée pour gérer ou exploiter des infrastructures     │
│     essentielles (énergie, électricité, eau, transport) ?                   │
│     ○ Oui    ○ Non                                                          │
│     Précisions (optionnel) : [____________________________________]         │
│                                                                             │
│  5. Accès à la formation ou à l'éducation                                   │
│     L'IA décide-t-elle des conditions d'accès à un cursus ou une           │
│     formation professionnelle ?                                             │
│     ○ Oui    ○ Non                                                          │
│     Précisions (optionnel) : [____________________________________]         │
│                                                                             │
│  6. Évaluation des acquis et du parcours d'apprentissage                    │
│     L'IA corrige-t-elle, note-t-elle ou oriente-t-elle le processus        │
│     d'apprentissage ?                                                       │
│     ○ Oui    ○ Non                                                          │
│     Précisions (optionnel) : [____________________________________]         │
│                                                                             │
│  7. Surveillance et détection de comportements interdits                    │
│     L'IA détecte-t-elle la triche ou des comportements non conformes ?     │
│     ○ Oui    ○ Non                                                          │
│     Précisions (optionnel) : [____________________________________]         │
│                                                                             │
│  8. Recrutement et sélection de candidats                                   │
│     L'IA filtre-t-elle des CV, analyse-t-elle des candidatures ou          │
│     évalue-t-elle des candidats ?                                           │
│     ○ Oui    ○ Non                                                          │
│     Précisions (optionnel) : [____________________________________]         │
│                                                                             │
│  9. Décisions dans le cadre professionnel                                   │
│     L'IA influence-t-elle la promotion, la sanction, l'attribution de      │
│     tâches ou l'évaluation des performances/comportements ?                 │
│     ○ Oui    ○ Non                                                          │
│     Précisions (optionnel) : [____________________________________]         │
│                                                                             │
│  ─────────────────────────────────────────────────────────────────────────  │
│  Résultat section E : [ 0 OUI / 9 ] → ✅ Pas de haut risque                 │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

**Règle de classification :** Si ≥ 1 réponse "Oui" → **SYSTÈME HAUT RISQUE**

---

### 4.7 Section F : Données et RGPD

```
┌─────────────────────────────────────────────────────────────────────────────┐
│  F. DONNÉES ET RGPD                                                        │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  1. L'IA implique-t-elle l'utilisation de données à caractère personnel ? *│
│     ○ Oui    ○ Non                                                          │
│     Précisions (optionnel) : [____________________________________]         │
│                                                                             │
│  ─────────────────────────────────────────────────────────────────────────  │
│  (Si Oui à la question 1, répondre aux questions suivantes)                 │
│                                                                             │
│  2. L'IA implique-t-elle des données sensibles ?                            │
│     ○ Oui    ○ Non                                                          │
│                                                                             │
│     Si oui, lesquelles ? (plusieurs choix possibles)                        │
│     ┌─────────────────────────────────────────────────────────────────┐    │
│     │ ☐ Appartenance syndicale          ☐ Opinions politiques         │    │
│     │ ☐ Données religieuses             ☐ Données biométriques        │    │
│     │ ☐ Données de santé                ☐ Données génétiques          │    │
│     │ ☐ Origine ethnique                ☐ Orientation sexuelle        │    │
│     └─────────────────────────────────────────────────────────────────┘    │
│     Précisions (optionnel) : [____________________________________]         │
│                                                                             │
│  ─────────────────────────────────────────────────────────────────────────  │
│                                                                             │
│  3. L'IA est-elle intégrée à un SI majeur / à risque pour EDF ?            │
│     (soumis à réglementation particulière, SI identifié comme critique)     │
│     ○ Oui    ○ Non                                                          │
│     Précisions (optionnel) : [____________________________________]         │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

### 4.8 Section G : Transparence et Supervision

```
┌─────────────────────────────────────────────────────────────────────────────┐
│  G. TRANSPARENCE ET SUPERVISION                                            │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  Ces questions permettent d'identifier un risque limité selon l'AI Act.    │
│                                                                             │
│  ─────────────────────────────────────────────────────────────────────────  │
│                                                                             │
│  1. L'utilisateur final est-il informé de l'utilisation de l'IA ? *        │
│     ○ Oui    ○ Non    ○ Non applicable                                      │
│     Précisions (optionnel) : [____________________________________]         │
│                                                                             │
│  2. L'IA fournit-elle des recommandations pouvant influencer un choix      │
│     (mais sans impact majeur) ?                                             │
│     ○ Oui    ○ Non                                                          │
│     Précisions (optionnel) : [____________________________________]         │
│                                                                             │
│  3. Les résultats de l'IA font-ils l'objet d'une supervision ou d'un       │
│     contrôle par un humain ? *                                              │
│     ○ Oui    ○ Non                                                          │
│     Précisions (optionnel) : [____________________________________]         │
│                                                                             │
│  ─────────────────────────────────────────────────────────────────────────  │
│                                                                             │
│  4. L'IA est-elle réservée à un usage interne, sans effet sur des          │
│     personnes extérieures ?                                                 │
│     ○ Oui    ○ Non                                                          │
│     Précisions (optionnel) : [____________________________________]         │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

**Règle de classification :** Si transparence (Q1) = Non → **RISQUE LIMITÉ**

---

### 4.9 Section H : Impact RH / Dialogue Social

```
┌─────────────────────────────────────────────────────────────────────────────┐
│  H. IMPACT RH / DIALOGUE SOCIAL                                            │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  1. Le projet concerne-t-il un grand nombre de salariés qui seront         │
│     amenés à utiliser le SIA ?                                              │
│     ○ Oui    ○ Non                                                          │
│                                                                             │
│     Si oui, estimation du nombre de personnes :                             │
│     ┌──────────────────┐    ┌──────────────────┐                           │
│     │ POC :            │    │ Cible :          │                           │
│     └──────────────────┘    └──────────────────┘                           │
│     Précisions (optionnel) : [____________________________________]         │
│                                                                             │
│  ─────────────────────────────────────────────────────────────────────────  │
│                                                                             │
│  2. La mise en œuvre risque-t-elle d'avoir un impact significatif sur      │
│     l'organisation du travail ?                                             │
│     (organigramme, rattachement hiérarchique, répartition des rôles et     │
│     responsabilités, type de management, circuit de décision et            │
│     d'information, process, interdépendances fonctionnelles)               │
│     ○ Oui    ○ Non                                                          │
│     Précisions (optionnel) : [____________________________________]         │
│                                                                             │
│  ─────────────────────────────────────────────────────────────────────────  │
│                                                                             │
│  3. Les activités et missions confiées dans les emplois concernés          │
│     vont-elles évoluer significativement du fait de l'introduction         │
│     de l'IA ?                                                               │
│     ○ Oui    ○ Non                                                          │
│     Précisions (optionnel) : [____________________________________]         │
│                                                                             │
│  4. Les compétences requises pour occuper les emplois devront-elles        │
│     évoluer significativement suite à l'introduction de l'IA ?             │
│     ○ Oui    ○ Non                                                          │
│     Précisions (optionnel) : [____________________________________]         │
│                                                                             │
│  ─────────────────────────────────────────────────────────────────────────  │
│  ⚠️  Si plusieurs OUI : dialogue social recommandé avec appui filière RH   │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

### 4.10 Section I : Commentaires et Justifications

```
┌─────────────────────────────────────────────────────────────────────────────┐
│  I. COMMENTAIRES ET JUSTIFICATIONS                                         │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  Mesures de mitigation des risques prévues                                  │
│  (information des personnes, explicabilité, gestion des plaintes...)       │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │                                                                     │   │
│  │                                                                     │   │
│  │                                                                     │   │
│  │                                                                     │   │
│  └─────────────────────────────────────────────────────────────────────┘   │
│                                                                             │
│  Commentaires additionnels                                                  │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │                                                                     │   │
│  │                                                                     │   │
│  │                                                                     │   │
│  │                                                                     │   │
│  └─────────────────────────────────────────────────────────────────────┘   │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 5. Règles de Classification AI Act

### 5.1 Algorithme de classification

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    ALGORITHME DE CLASSIFICATION AI ACT                      │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│                        ┌─────────────────┐                                  │
│                        │ Section D       │                                  │
│                        │ (Bloc Interdit) │                                  │
│                        └────────┬────────┘                                  │
│                                 │                                           │
│                          ≥1 OUI ?                                           │
│                           /    \                                            │
│                         OUI    NON                                          │
│                          │      │                                           │
│                          ▼      ▼                                           │
│                   ┌──────────┐  ┌─────────────────┐                        │
│                   │ INTERDIT │  │ Section E       │                        │
│                   │    🔴    │  │ (Bloc Haut      │                        │
│                   └──────────┘  │  Risque)        │                        │
│                                 └────────┬────────┘                        │
│                                          │                                  │
│                                   ≥1 OUI ?                                  │
│                                    /    \                                   │
│                                  OUI    NON                                 │
│                                   │      │                                  │
│                                   ▼      ▼                                  │
│                         ┌────────────┐  ┌─────────────────┐                │
│                         │HAUT RISQUE │  │ Section G       │                │
│                         │     🟠     │  │ (Transparence)  │                │
│                         └────────────┘  └────────┬────────┘                │
│                                                  │                          │
│                                      Utilisateur informé ?                  │
│                                           /    \                            │
│                                         NON    OUI                          │
│                                          │      │                           │
│                                          ▼      ▼                           │
│                               ┌──────────────┐  ┌──────────────┐           │
│                               │RISQUE LIMITÉ │  │RISQUE MINIMAL│           │
│                               │      🟡      │  │      🟢      │           │
│                               └──────────────┘  └──────────────┘           │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

### 5.2 Tableau récapitulatif

| Classification | Condition | Couleur | Action |
|----------------|-----------|---------|--------|
| **INTERDIT** | Section D ≥ 1 OUI | 🔴 Rouge | Rejet automatique |
| **HAUT RISQUE** | Section E ≥ 1 OUI | 🟠 Orange | Validation + dialogue social |
| **RISQUE LIMITÉ** | Section G Transparence = NON | 🟡 Jaune | Validation standard |
| **RISQUE MINIMAL** | Aucun critère ci-dessus | 🟢 Vert | Validation simplifiée |

---

## 6. Tableau de Bord

### 6.1 Vue Métier

```
┌─────────────────────────────────────────────────────────────────────────────┐
│  ARIA                                           [Métier] user@edf.fr  [⚙️] │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  MES CAS D'USAGE                                    [+ Nouveau cas d'usage] │
│                                                                             │
│  ┌──────────┬────────────────────────┬──────────┬────────────┬──────────┐  │
│  │ Statut   │ Nom                    │ Direction│ Classif.   │ Date     │  │
│  ├──────────┼────────────────────────┼──────────┼────────────┼──────────┤  │
│  │ 📝       │ Assistant rédaction    │ DSI      │ 🟢 Minimal │ 15/01/25 │  │
│  │ Brouillon│ contrats               │          │            │          │  │
│  ├──────────┼────────────────────────┼──────────┼────────────┼──────────┤  │
│  │ ⏳       │ Analyse CV candidats   │ DRH      │ 🟠 Haut    │ 10/01/25 │  │
│  │ Soumis   │                        │          │            │          │  │
│  ├──────────┼────────────────────────┼──────────┼────────────┼──────────┤  │
│  │ ✅       │ Chatbot FAQ interne    │ DCOM     │ 🟡 Limité  │ 05/01/25 │  │
│  │ Validé   │                        │          │            │          │  │
│  └──────────┴────────────────────────┴──────────┴────────────┴──────────┘  │
│                                                                             │
│  Afficher : [Tous ▼]  Direction : [Toutes ▼]  Rechercher : [          🔍]  │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

### 6.2 Vue Gouvernance

```
┌─────────────────────────────────────────────────────────────────────────────┐
│  ARIA                                       [Gouvernance] admin@edf.fr [⚙️]│
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  TABLEAU DE BORD                                              [📥 Export]  │
│                                                                             │
│  ┌───────────────┬───────────────┬───────────────┬───────────────┐         │
│  │   EN ATTENTE  │    VALIDÉS    │    REJETÉS    │     TOTAL     │         │
│  │      12       │      45       │       3       │      60       │         │
│  │   ⏳          │   ✅          │   ❌          │               │         │
│  └───────────────┴───────────────┴───────────────┴───────────────┘         │
│                                                                             │
│  CLASSIFICATION AI ACT                                                      │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │ 🟢 Minimal: 35 (58%)  ████████████████████░░░░░░░░░░░░░░░░░░░░░░░░  │   │
│  │ 🟡 Limité:  15 (25%)  ██████████░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░  │   │
│  │ 🟠 Haut:     8 (13%)  █████░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░  │   │
│  │ 🔴 Interdit: 2 (3%)   █░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░  │   │
│  └─────────────────────────────────────────────────────────────────────┘   │
│                                                                             │
│  CAS À VALIDER                                                              │
│  ┌──────────┬────────────────────────┬──────────┬────────────┬──────────┐  │
│  │ Classif. │ Nom                    │ Direction│ Porteur    │ Actions  │  │
│  ├──────────┼────────────────────────┼──────────┼────────────┼──────────┤  │
│  │ 🟠 Haut  │ Analyse CV candidats   │ DRH      │ m.dupont   │[👁️][✅][❌]│  │
│  ├──────────┼────────────────────────┼──────────┼────────────┼──────────┤  │
│  │ 🟢 Min.  │ Traduction documents   │ DCOM     │ p.martin   │[👁️][✅][❌]│  │
│  └──────────┴────────────────────────┴──────────┴────────────┴──────────┘  │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

### 6.3 Écran de validation

```
┌─────────────────────────────────────────────────────────────────────────────┐
│  VALIDATION DU CAS D'USAGE                                        [Fermer] │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │  Analyse CV candidats                              🟠 HAUT RISQUE   │   │
│  │  Direction: DRH | Porteur: m.dupont@edf.fr | Soumis le: 10/01/2025  │   │
│  └─────────────────────────────────────────────────────────────────────┘   │
│                                                                             │
│  RÉSUMÉ DU CRIBLAGE                                                         │
│  ─────────────────────────────────────────────────────────────────────────  │
│  • Bloc Interdit : 0/7 ✅                                                   │
│  • Bloc Haut Risque : 1/9 ⚠️ (Recrutement et sélection)                    │
│  • Données personnelles : Oui                                               │
│  • Supervision humaine : Oui                                                │
│  • Impact RH : Fort                                                         │
│                                                                             │
│  [📄 Voir le formulaire complet]                                           │
│                                                                             │
│  HISTORIQUE                                                                 │
│  ─────────────────────────────────────────────────────────────────────────  │
│  10/01/2025 10:30 | Soumis par m.dupont@edf.fr                             │
│  08/01/2025 15:45 | Modifié par m.dupont@edf.fr                            │
│  05/01/2025 09:00 | Créé par m.dupont@edf.fr                               │
│                                                                             │
│  DÉCISION                                                                   │
│  ─────────────────────────────────────────────────────────────────────────  │
│  Commentaire (obligatoire si rejet) :                                       │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │                                                                     │   │
│  │                                                                     │   │
│  └─────────────────────────────────────────────────────────────────────┘   │
│                                                                             │
│         [💬 Demander complément]    [❌ Rejeter]    [✅ Valider]           │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 7. Export CSV

### 7.1 Liste exhaustive des champs exportés

#### Métadonnées système

| # | Nom du champ | Type | Description |
|---|--------------|------|-------------|
| 1 | id | Texte | Identifiant unique du cas d'usage |
| 2 | statut | Texte | BROUILLON / SOUMIS / COMMENTE / VALIDE / REJETE |
| 3 | classification_ia_act | Texte | INTERDIT / HAUT_RISQUE / LIMITE / MINIMAL |
| 4 | date_creation | Date | Date de création du dossier |
| 5 | date_modification | Date | Date de dernière modification |
| 6 | date_soumission | Date | Date de soumission pour validation |
| 7 | date_decision | Date | Date de validation ou rejet |
| 8 | validateur_email | Texte | Email du validateur gouvernance |
| 9 | commentaire_validation | Texte | Commentaire de validation/rejet/demande complément |

#### Section A : Identification

| # | Nom du champ | Type | Description |
|---|--------------|------|-------------|
| 10 | nom_cas_usage | Texte | Nom du cas d'usage |
| 11 | description_besoin | Texte | Description / Besoin |
| 12 | direction | Texte | Direction |
| 13 | sous_direction | Texte | Sous-direction |
| 14 | bpdo_responsable | Texte | BPDO responsable |
| 15 | porteur_email | Texte | Email du porteur |
| 16 | date_lancement_prevue | Date | Date de lancement prévue |
| 17 | nb_utilisateurs_cible | Nombre | Nombre d'utilisateurs cible |

#### Section B : Caractéristiques Techniques

| # | Nom du champ | Type | Description |
|---|--------------|------|-------------|
| 18 | tech_ia_generative | Booléen | Technique : IA générative |
| 19 | tech_agents | Booléen | Technique : Agents |
| 20 | tech_machine_learning | Booléen | Technique : Machine learning |
| 21 | tech_algorithmie_avancee | Booléen | Technique : Algorithmie avancée |
| 22 | tech_tal | Booléen | Technique : Traitement automatique des langues |
| 23 | tech_image | Booléen | Technique : Traitement de l'image |
| 24 | tech_son | Booléen | Technique : Traitement du son |
| 25 | tech_autre | Booléen | Technique : Autre |
| 26 | tech_autre_precision | Texte | Précision si autre technique |
| 27 | type_modele | Texte | OPEN_SOURCE / PROPRIETAIRE / INTERNE / NA |
| 28 | source_donnees_interne | Booléen | Source : Données internes |
| 29 | source_donnees_externe | Booléen | Source : Données externes |
| 30 | env_on_premise | Booléen | Environnement : On-premise |
| 31 | env_portail_iag_interne | Booléen | Environnement : Portail IAG Interne |
| 32 | env_portail_iag_cloud | Booléen | Environnement : Portail IAG Cloud |
| 33 | env_cloud | Booléen | Environnement : Cloud |
| 34 | env_autre | Booléen | Environnement : Autre (outil entreprise) |

#### Section C : Droits Fondamentaux

| # | Nom du champ | Type | Description |
|---|--------------|------|-------------|
| 35 | c_droit_dignite_humaine | Booléen | Impact : Dignité humaine |
| 36 | c_droit_liberte_securite | Booléen | Impact : Droit à la liberté et à la sécurité |
| 37 | c_droit_vie_privee_familiale | Booléen | Impact : Respect de la vie privée et familiale |
| 38 | c_droit_expression_information | Booléen | Impact : Liberté d'expression et d'information |
| 39 | c_droit_reunion_association | Booléen | Impact : Liberté de réunion et d'association |
| 40 | c_droit_non_discrimination | Booléen | Impact : Non-discrimination |
| 41 | c_droit_protection_consommateurs | Booléen | Impact : Protection des consommateurs |
| 42 | c_droit_travailleurs | Booléen | Impact : Droit des travailleurs |
| 43 | c_droit_personnes_handicapees | Booléen | Impact : Droit des personnes handicapées |
| 44 | c_droit_egalite_genre | Booléen | Impact : Égalité de genre |
| 45 | c_droit_propriete_intellectuelle | Booléen | Impact : Propriété intellectuelle |
| 46 | c_droit_autre | Booléen | Impact : Autre droit fondamental |
| 47 | c_droit_aucun_impact | Booléen | Aucun impact identifié sur les droits fondamentaux |
| 48 | c_droit_precision | Texte | Précision sur les impacts droits fondamentaux |

#### Section D : AI Act - Bloc Interdit

| # | Nom du champ | Type | Description |
|---|--------------|------|-------------|
| 49 | d_usage_interdit | Booléen | Usage interdit selon AI Act (question globale) |
| 50 | d_usage_interdit_precision | Texte | Précisions sur l'usage interdit |

#### Section E : AI Act - Bloc Haut Risque

| # | Nom du champ | Type | Description |
|---|--------------|------|-------------|
| 51 | e1_identification_biometrique_distance | Booléen | Identification biométrique à distance |
| 52 | e1_precision | Texte | Précisions Q1 |
| 53 | e2_evaluation_biometrique_sensible | Booléen | Évaluation biométrique sensible (attributs protégés) |
| 54 | e2_precision | Texte | Précisions Q2 |
| 55 | e3_reconnaissance_emotions | Booléen | Reconnaissance ou déduction des émotions |
| 56 | e3_precision | Texte | Précisions Q3 |
| 57 | e4_infrastructures_critiques | Booléen | Sécurisation d'infrastructures critiques |
| 58 | e4_precision | Texte | Précisions Q4 |
| 59 | e5_acces_formation_education | Booléen | Accès à la formation ou à l'éducation |
| 60 | e5_precision | Texte | Précisions Q5 |
| 61 | e6_evaluation_acquis_apprentissage | Booléen | Évaluation des acquis et du parcours d'apprentissage |
| 62 | e6_precision | Texte | Précisions Q6 |
| 63 | e7_surveillance_comportements | Booléen | Surveillance et détection de comportements interdits |
| 64 | e7_precision | Texte | Précisions Q7 |
| 65 | e8_recrutement_selection | Booléen | Recrutement et sélection de candidats |
| 66 | e8_precision | Texte | Précisions Q8 |
| 67 | e9_decisions_cadre_professionnel | Booléen | Décisions dans le cadre professionnel |
| 68 | e9_precision | Texte | Précisions Q9 |
| 69 | e_nb_oui | Nombre | Nombre total de OUI section E (calculé) |

#### Section F : Données et RGPD

| # | Nom du champ | Type | Description |
|---|--------------|------|-------------|
| 70 | f1_donnees_personnelles | Booléen | Utilisation de données à caractère personnel |
| 71 | f1_precision | Texte | Précisions Q1 |
| 72 | f2_donnees_sensibles | Booléen | Utilisation de données sensibles |
| 73 | f2_donnees_sensibles_syndicale | Booléen | Données sensibles : Appartenance syndicale |
| 74 | f2_donnees_sensibles_politique | Booléen | Données sensibles : Opinions politiques |
| 75 | f2_donnees_sensibles_religieuse | Booléen | Données sensibles : Données religieuses |
| 76 | f2_donnees_sensibles_biometrique | Booléen | Données sensibles : Données biométriques |
| 77 | f2_donnees_sensibles_sante | Booléen | Données sensibles : Données de santé |
| 78 | f2_donnees_sensibles_genetique | Booléen | Données sensibles : Données génétiques |
| 79 | f2_donnees_sensibles_ethnique | Booléen | Données sensibles : Origine ethnique |
| 80 | f2_donnees_sensibles_orientation | Booléen | Données sensibles : Orientation sexuelle |
| 81 | f2_precision | Texte | Précisions Q2 |
| 82 | f3_si_majeur_risque | Booléen | Intégré à un SI majeur / à risque |
| 83 | f3_precision | Texte | Précisions Q3 |

#### Section G : Transparence et Supervision

| # | Nom du champ | Type | Description |
|---|--------------|------|-------------|
| 84 | g1_utilisateur_informe | Texte | Utilisateur informé de l'IA : OUI / NON / NA |
| 85 | g1_precision | Texte | Précisions Q1 |
| 86 | g2_recommandations_influence_choix | Booléen | IA fournit des recommandations influençant un choix |
| 87 | g2_precision | Texte | Précisions Q2 |
| 88 | g3_supervision_humaine | Booléen | Résultats supervisés/contrôlés par un humain |
| 89 | g3_precision | Texte | Précisions Q3 |
| 90 | g4_usage_interne_uniquement | Booléen | Usage interne sans effet sur personnes extérieures |
| 91 | g4_precision | Texte | Précisions Q4 |

#### Section H : Impact RH / Dialogue Social

| # | Nom du champ | Type | Description |
|---|--------------|------|-------------|
| 92 | h1_grand_nombre_salaries | Booléen | Concerne un grand nombre de salariés |
| 93 | h1_nb_salaries_poc | Nombre | Estimation nombre de personnes (POC) |
| 94 | h1_nb_salaries_cible | Nombre | Estimation nombre de personnes (Cible) |
| 95 | h1_precision | Texte | Précisions Q1 |
| 96 | h2_impact_organisation_travail | Booléen | Impact significatif sur l'organisation du travail |
| 97 | h2_precision | Texte | Précisions Q2 |
| 98 | h3_evolution_activites | Booléen | Évolution significative des activités/missions |
| 99 | h3_precision | Texte | Précisions Q3 |
| 100 | h4_evolution_competences | Booléen | Évolution significative des compétences requises |
| 101 | h4_precision | Texte | Précisions Q4 |
| 102 | h_nb_oui | Nombre | Nombre total de OUI section H (calculé) |

#### Section I : Commentaires et Justifications

| # | Nom du champ | Type | Description |
|---|--------------|------|-------------|
| 103 | i_mesures_mitigation | Texte | Mesures de mitigation des risques prévues |
| 104 | i_commentaires_additionnels | Texte | Commentaires additionnels |

---

### 7.2 Format du fichier CSV

- **Encodage** : UTF-8 avec BOM
- **Séparateur** : Point-virgule (;) pour compatibilité Excel FR
- **Délimiteur texte** : Guillemets doubles (")
- **Format booléen** : OUI / NON
- **Format date** : JJ/MM/AAAA
- **Valeurs nulles** : Cellule vide

### 7.3 Exemple d'en-tête CSV

```csv
id;statut;classification_ia_act;date_creation;date_modification;date_soumission;date_decision;validateur_email;commentaire_validation;nom_cas_usage;description_besoin;direction;sous_direction;bpdo_responsable;porteur_email;date_lancement_prevue;nb_utilisateurs_cible;tech_ia_generative;tech_agents;tech_machine_learning;tech_algorithmie_avancee;tech_tal;tech_image;tech_son;tech_autre;tech_autre_precision;type_modele;source_donnees_interne;source_donnees_externe;env_on_premise;env_portail_iag_interne;env_portail_iag_cloud;env_cloud;env_autre;c_droit_dignite_humaine;c_droit_liberte_securite;c_droit_vie_privee_familiale;c_droit_expression_information;c_droit_reunion_association;c_droit_non_discrimination;c_droit_protection_consommateurs;c_droit_travailleurs;c_droit_personnes_handicapees;c_droit_egalite_genre;c_droit_propriete_intellectuelle;c_droit_autre;c_droit_aucun_impact;c_droit_precision;d_usage_interdit;d_usage_interdit_precision;e1_identification_biometrique_distance;e1_precision;e2_evaluation_biometrique_sensible;e2_precision;e3_reconnaissance_emotions;e3_precision;e4_infrastructures_critiques;e4_precision;e5_acces_formation_education;e5_precision;e6_evaluation_acquis_apprentissage;e6_precision;e7_surveillance_comportements;e7_precision;e8_recrutement_selection;e8_precision;e9_decisions_cadre_professionnel;e9_precision;e_nb_oui;f1_donnees_personnelles;f1_precision;f2_donnees_sensibles;f2_donnees_sensibles_syndicale;f2_donnees_sensibles_politique;f2_donnees_sensibles_religieuse;f2_donnees_sensibles_biometrique;f2_donnees_sensibles_sante;f2_donnees_sensibles_genetique;f2_donnees_sensibles_ethnique;f2_donnees_sensibles_orientation;f2_precision;f3_si_majeur_risque;f3_precision;g1_utilisateur_informe;g1_precision;g2_recommandations_influence_choix;g2_precision;g3_supervision_humaine;g3_precision;g4_usage_interne_uniquement;g4_precision;h1_grand_nombre_salaries;h1_nb_salaries_poc;h1_nb_salaries_cible;h1_precision;h2_impact_organisation_travail;h2_precision;h3_evolution_activites;h3_precision;h4_evolution_competences;h4_precision;h_nb_oui;i_mesures_mitigation;i_commentaires_additionnels
```

**Total : 104 champs exportés**

---

## 8. Évolutions Futures (Hors MVP)

### Phase 2
- Intégration avec le référentiel RH (liste des directions)
- Notifications Teams
- Analyses RGPD automatisées
- Workflow "Ajourné" (demande d'avis externe)

### Phase 3
- Dashboards avancés et analytics
- API externe pour integrations tierces
- Modules métiers spécialisés (Achats, Trading, Production)
- Gestion du cycle de vie post-déploiement

### Phase 4
- Assistant IA pour aide au remplissage
- Détection automatique des biais
- Calcul d'impact environnemental
- Préparation certification ISO 42001

---

## Annexe : Principes IA Responsable EDF

L'outil s'inscrit dans les 5 piliers de l'IA Responsable du Groupe EDF :

| Pilier | Description |
|--------|-------------|
| **En appui aux utilisateurs** | L'IA aide les salariés dans leur quotidien |
| **Non discriminatoire** | Équitable et respectueuse de la diversité |
| **Écoresponsable** | Minimise les impacts environnementaux |
| **De confiance** | Transparente et supervisée par l'humain |
| **Sécurisée** | Fiable, résiliente aux cyber-attaques |

---

*Document généré le 6 janvier 2025*
