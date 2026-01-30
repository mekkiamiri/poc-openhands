# ARIA - Product Requirements Document Technique

---

## Table des Matieres

1. [Vue d'ensemble](#1-vue-densemble)
2. [Stack Technique](#2-stack-technique)
3. [Architecture Backend](#3-architecture-backend)
4. [Architecture Frontend](#4-architecture-frontend)
5. [UX/UI Design](#5-uxui-design)
6. [Schema de Base de Donnees](#6-schema-de-base-de-donnees)
7. [API et Endpoints](#7-api-et-endpoints)
8. [Authentification et Securite](#8-authentification-et-securite)
9. [Configuration](#9-configuration)
10. [Deploiement](#10-deploiement)
11. [Tests](#11-tests)
12. [Patterns de Code](#12-patterns-de-code)
13. [Annexes](#13-annexes)

---

## 1. Vue d'ensemble

### 1.1 Objectif du Document

Ce PRD technique definit l'architecture, les choix technologiques et les patterns de developpement pour **ARIA** (AI Risk & Impact Assessment). Il sert de reference pour le developpement autonome de l'application.

### 1.2 Principes Directeurs

| Principe                  | Description                                                     |
| ------------------------- | --------------------------------------------------------------- |
| **Stack Eprouvee**        | Stack technique moderne et eprouvee (Spring Boot, Next Js, MUI) |
| **Database Architecture** | PostgreSQL                                                      |
| **Clean Architecture**    | Separation stricte Routers → Services → Database                |
| **Security First**        | OWASP Top 10, CSRF, injection prevention                        |

### 1.3 Perimetre Technique MVP

```
ARIA MVP
├── Backend Spring Boot ( 3.x)
│   ├── API REST JSON
│   ├── Authentification LDAP + Local
│   ├── Export CSV
│   └── Notifications Email (SMTP)
├── Frontend NextJs(TypeScript)
│   ├── Interface formulaire multi-sections
│   ├── Dashboard Metier / Gouvernance
│   └── Classification AI Act automatique
├── Base de donnees
│   └── PostgreSQL 15+
└── Deploiement
    ├── Docker Compose (dev)
    └── OpenShift/Kubernetes (prod)
```

---

## 2. Stack Technique

### 2.1 Backend

| Composant               | Technologie   | Notes               |
| ----------------------- | ------------- | ------------------- |
| **Framework**           | SpringBoot    | Async, OpenAPI auto |
| **ORM**                 | Hibernate     | Async support       |
| **Database Migrations** | Liquibase     | Schema versioning   |
| **Server**              | Apache tomcat | Web server          |

**Drivers Database:**
| Base | Driver |
|------|--------|
| PostgreSQL | ar2dbcsyncpg |

### 2.2 Frontend

| Composant      | Technologie       | Version | Notes                                  |
| -------------- | ----------------- | ------- | -------------------------------------- |
| **Framework**  | NextJs            | 15.2.8  | Hooks, Suspense, Client Side Rendering |
| **UI Library** | MUI (Material-UI) | v7.1.2  | Breaking changes vs v5                 |
| **State**      | Zustand           | 5.0.6   | Stores legers                          |
| **Routing**    | React Router      | 7.7.0   | Requires Node 20+                      |
| **HTTP**       | Axios             | 1.8.4   | Interceptors JWT/CSRF                  |
| **Forms**      | React Hook Form   | Latest  | Validation formulaire                  |
| **Build**      | Vite              | 6.0+    | ESBuild + Rollup, fast HMR             |
| **Langage**    | JavaScript        | ES2022+ | TypeScript                             |

### 2.3 Infrastructure

| Composant         | Technologie          | Version |
| ----------------- | -------------------- | ------- |
| **Container**     | Docker               | 24+     |
| **Orchestration** | OpenShift/Kubernetes | 4.x     |
| **Reverse Proxy** | Nginx                | Alpine  |
| **Load Balancer** | HAProxy              | 2.8.2   |
| **Base Prod**     | PostgreSQL           | 15+     |

### 2.4 Outils Developpement

| Outil        | Usage              |
| ------------ | ------------------ |
| **Maven**    | Tests backend      |
| **Vitest**   | Tests frontend     |
| **Cucumber** | Tests E2E          |
| **ESLint**   | Linting Typescript |

---

## 3. Architecture Backend

```

### 3.2 Pattern 3-Tiers

```

┌─────────────────────────────────────────────────────────────────┐
│ ROUTERS (HTTP Layer) │
│ - Dependency Injection (get_db, get_current_user) │
│ - HTTPException uniquement ici │
│ - NO business logic, NO db.execute() │
└─────────────────────────────────────────────────────────────────┘
│
▼
┌─────────────────────────────────────────────────────────────────┐
│ SERVICES (Business Layer) │
│ - Logique metier complete │
│ - Requetes DB via SQLAlchemy ORM │
│ - NO HTTPException (raise custom exceptions) │
│ - Retourne des objets/dicts, pas des Response │
└─────────────────────────────────────────────────────────────────┘
│
▼
┌─────────────────────────────────────────────────────────────────┐
│ DATABASE (Data Layer) │
│ - Requetes parametrees uniquement │
│ - AsyncSession pour toutes les operations │
└─────────────────────────────────────────────────────────────────┘

---

## 4. Architecture Frontend

### 4.2 State Management - Zustand

```javascript
// stores/useCaseStore.js
import { create } from "zustand";
import apiClient from "../api/apiClient";

const useUseCaseStore = create((set, get) => ({
  // State
  useCases: [],
  currentUseCase: null,
  loading: false,
  error: null,
  filters: {
    statut: null,
    direction: null,
    classification: null,
  },

  // Actions
  fetchUseCases: async () => {
    set({ loading: true, error: null });
    try {
      const params = new URLSearchParams();
      const filters = get().filters;
      if (filters.statut) params.append("statut", filters.statut);
      if (filters.direction) params.append("direction_id", filters.direction);
      if (filters.classification)
        params.append("classification", filters.classification);

      const response = await apiClient.get(`/api/use-cases?${params}`);
      set({ useCases: response.data, loading: false });
    } catch (error) {
      set({ error: error.message, loading: false });
    }
  },

  fetchUseCase: async (id) => {
    set({ loading: true, error: null });
    try {
      const response = await apiClient.get(`/api/use-cases/${id}`);
      set({ currentUseCase: response.data, loading: false });
    } catch (error) {
      set({ error: error.message, loading: false });
    }
  },

  createUseCase: async (data) => {
    set({ loading: true, error: null });
    try {
      const response = await apiClient.post("/api/use-cases", data);
      set((state) => ({
        useCases: [response.data, ...state.useCases],
        currentUseCase: response.data,
        loading: false,
      }));
      return response.data;
    } catch (error) {
      set({ error: error.message, loading: false });
      throw error;
    }
  },

  updateUseCase: async (id, data) => {
    set({ loading: true, error: null });
    try {
      const response = await apiClient.put(`/api/use-cases/${id}`, data);
      set((state) => ({
        useCases: state.useCases.map((uc) =>
          uc.id === id ? response.data : uc,
        ),
        currentUseCase: response.data,
        loading: false,
      }));
      return response.data;
    } catch (error) {
      set({ error: error.message, loading: false });
      throw error;
    }
  },

  submitUseCase: async (id) => {
    return get().updateUseCase(id, { statut: "SOUMIS" });
  },

  setFilters: (filters) => {
    set({ filters: { ...get().filters, ...filters } });
    get().fetchUseCases();
  },

  clearCurrentUseCase: () => set({ currentUseCase: null }),
}));

export default useUseCaseStore;
```

### 4.3 Composant Formulaire - Exemple Section

```javascript
// components/form/SectionD_Interdit.js
import React from "react";
import {
  Box,
  Typography,
  FormControl,
  FormLabel,
  RadioGroup,
  FormControlLabel,
  Radio,
  TextField,
  Alert,
} from "@mui/material";
import WarningIcon from "@mui/icons-material/Warning";

const SectionD_Interdit = ({ values, onChange, errors }) => {
  const handleChange = (field) => (event) => {
    const value =
      event.target.type === "radio"
        ? event.target.value === "true"
        : event.target.value;
    onChange({ ...values, [field]: value });
  };

  return (
    <Box>
      <Typography variant="h6" gutterBottom>
        D. Conformite AI Act - Bloc Interdit
      </Typography>

      <Alert severity="warning" icon={<WarningIcon />} sx={{ mb: 3 }}>
        <strong>ATTENTION :</strong> Si vous repondez OUI, le systeme sera
        classe <strong>INTERDIT</strong> selon l'AI Act europeen.
      </Alert>

      <FormControl component="fieldset" error={!!errors?.d_usage_interdit}>
        <FormLabel component="legend">
          Votre IA est-elle susceptible d'etre utilisee pour l'un des usages
          suivants ? *
        </FormLabel>
        <Typography variant="body2" color="textSecondary" sx={{ mb: 1 }}>
          - Manipulation comportementale (techniques subliminales)
          <br />
          - Exploitation de vulnerabilites (age, handicap, situation socio-eco)
          <br />
          - Notation sociale (social scoring)
          <br />
          - Profilage criminel (prediction risque criminel)
          <br />
          - Biometrie abusive (collecte massive reconnaissance faciale)
          <br />
          - Reconnaissance emotionnelle au travail
          <br />- Categorisation biometrique sensible (race, religion,
          orientation...)
        </Typography>
        <RadioGroup
          value={values.d_usage_interdit?.toString() || "false"}
          onChange={handleChange("d_usage_interdit")}
        >
          <FormControlLabel value="true" control={<Radio />} label="Oui" />
          <FormControlLabel value="false" control={<Radio />} label="Non" />
        </RadioGroup>
      </FormControl>

      {values.d_usage_interdit && (
        <TextField
          fullWidth
          multiline
          rows={3}
          label="Si oui, precisez"
          value={values.d_usage_interdit_precision || ""}
          onChange={handleChange("d_usage_interdit_precision")}
          sx={{ mt: 2 }}
        />
      )}
    </Box>
  );
};

export default SectionD_Interdit;
```

### 4.4 Hook Auto-Save

```javascript
// hooks/useAutoSave.js
import { useEffect, useRef, useCallback } from "react";
import { useDebounce } from "./useDebounce";

const useAutoSave = (data, onSave, delay = 30000) => {
  const lastSavedRef = useRef(JSON.stringify(data));
  const debouncedData = useDebounce(data, delay);

  const saveNow = useCallback(async () => {
    const currentData = JSON.stringify(data);
    if (currentData !== lastSavedRef.current) {
      try {
        await onSave(data);
        lastSavedRef.current = currentData;
        return true;
      } catch (error) {
        console.error("Auto-save failed:", error);
        return false;
      }
    }
    return false;
  }, [data, onSave]);

  // Auto-save on debounced data change
  useEffect(() => {
    const currentData = JSON.stringify(debouncedData);
    if (currentData !== lastSavedRef.current) {
      saveNow();
    }
  }, [debouncedData, saveNow]);

  // Save on page unload
  useEffect(() => {
    const handleBeforeUnload = (e) => {
      const currentData = JSON.stringify(data);
      if (currentData !== lastSavedRef.current) {
        e.preventDefault();
        e.returnValue = "";
      }
    };
    window.addEventListener("beforeunload", handleBeforeUnload);
    return () => window.removeEventListener("beforeunload", handleBeforeUnload);
  }, [data]);

  return { saveNow, lastSaved: lastSavedRef.current };
};

export default useAutoSave;
```

---

## 5. UX/UI Design

### 5.1 Principes de Design

| Principe          | Description                                           |
| ----------------- | ----------------------------------------------------- |
| **Simplicite**    | Layout epure, pas de sidebar, focus sur le contenu    |
| **Patterns MUI**  | Composants MUI v7, theming standard, patterns etablis |
| **Accessibilite** | WCAG 2.1 AA, aria-labels, navigation clavier          |
| **Responsive**    | Mobile-first, breakpoints MUI standards               |

### 5.2 Layout Global

```
┌─────────────────────────────────────────────────────────────────┐
│  Header                                                          │
│  ┌──────┐  ┌──────────────────────────────┐  ┌────────────────┐ │
│  │ Logo │  │ Navigation (Tabs)            │  │ User Menu      │ │
│  └──────┘  └──────────────────────────────┘  └────────────────┘ │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│                     Contenu Principal                            │
│                     (pleine largeur)                             │
│                                                                  │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

**Composants Header:**

- Logo ARIA (gauche)
- Navigation par tabs (centre)
- User menu dropdown (droite) : Profil, Deconnexion

**Navigation par role:**

| Role            | Tabs visibles                                 |
| --------------- | --------------------------------------------- |
| **metier**      | Mes cas d'usage, Nouveau cas                  |
| **gouvernance** | Tous les cas, Mes cas, Export                 |
| **admin**       | Tous les cas, Mes cas, Export, Administration |

### 5.3 Pages et Ecrans

#### 5.3.1 Page Login

```
┌─────────────────────────────────────────────┐
│                                             │
│              Logo ARIA                      │
│         AI Risk & Impact Assessment         │
│                                             │
│  ┌───────────────────────────────────────┐  │
│  │  Email/Identifiant                    │  │
│  └───────────────────────────────────────┘  │
│  ┌───────────────────────────────────────┐  │
│  │  Mot de passe                         │  │
│  └───────────────────────────────────────┘  │
│                                             │
│  ┌───────────────────────────────────────┐  │
│  │            Se connecter               │  │
│  └───────────────────────────────────────┘  │
│                                             │
│         Connexion via LDAP EDF              │
│                                             │
└─────────────────────────────────────────────┘
```

#### 5.3.2 Page Dashboard (Liste des cas)

```
┌─────────────────────────────────────────────────────────────────┐
│  Header avec navigation                                          │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  ┌─────────────────────────────────────────────────────────────┐│
│  │ Filtres:  [Statut ▼] [Classification ▼] [Direction ▼] 🔍   ││
│  └─────────────────────────────────────────────────────────────┘│
│                                                                  │
│  ┌─────────────────────────────────────────────────────────────┐│
│  │ Nom          │ Direction │ Statut  │ Classification │ Date  ││
│  ├──────────────┼───────────┼─────────┼────────────────┼───────┤│
│  │ Assistant IA │ DSIG      │ SOUMIS  │ 🟠 Haut Risque │ 08/01 ││
│  │ Chatbot RH   │ DRH       │ VALIDE  │ 🟢 Minimal     │ 07/01 ││
│  │ Analyse CV   │ DRH       │ BROUILL │ 🔴 Interdit    │ 06/01 ││
│  └─────────────────────────────────────────────────────────────┘│
│                                                                  │
│  < 1 2 3 ... 10 >                              25 par page ▼    │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

**Colonnes DataGrid:**

| Colonne           | Type    | Tri | Notes                     |
| ----------------- | ------- | --- | ------------------------- |
| Nom du cas        | String  | Oui | Lien vers detail          |
| Direction         | String  | Oui | Code direction            |
| Porteur           | String  | Oui | Nom du porteur            |
| Statut            | Badge   | Oui | Couleur par statut        |
| Classification    | Badge   | Oui | Couleur + icone           |
| Date modification | Date    | Oui | Format DD/MM/YYYY         |
| Actions           | Buttons | Non | Voir, Modifier, Supprimer |

**Filtres:**

| Filtre         | Type      | Options                                                    |
| -------------- | --------- | ---------------------------------------------------------- |
| Statut         | Select    | Tous, Brouillon, Soumis, Commente, Valide, Rejete          |
| Classification | Select    | Tous, Interdit, Haut Risque, Risque Limite, Risque Minimal |
| Direction      | Select    | Liste des directions                                       |
| Recherche      | TextField | Recherche texte (nom, description)                         |

#### 5.3.3 Page Formulaire (Stepper Vertical)

```
┌─────────────────────────────────────────────────────────────────┐
│  Header    [Classification: 🟢 RISQUE MINIMAL]  [Auto-save ✓]   │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  ┌────────────────┐  ┌──────────────────────────────────────────┐│
│  │                │  │                                          ││
│  │ ● A. Identif.  │  │  Section A : Identification              ││
│  │ ○ B. Technique │  │                                          ││
│  │ ○ C. Droits    │  │  Nom du cas d'usage *                    ││
│  │ ○ D. Interdit  │  │  ┌────────────────────────────────────┐  ││
│  │ ○ E. Haut Risq │  │  │                                    │  ││
│  │ ○ F. RGPD      │  │  └────────────────────────────────────┘  ││
│  │ ○ G. Transp.   │  │                                          ││
│  │ ○ H. Impact RH │  │  Description / Besoin *                  ││
│  │ ○ I. Comment.  │  │  ┌────────────────────────────────────┐  ││
│  │                │  │  │                                    │  ││
│  │                │  │  │                                    │  ││
│  │                │  │  └────────────────────────────────────┘  ││
│  │                │  │                                          ││
│  │                │  │  Direction *         [DSIG ▼]            ││
│  │                │  │                                          ││
│  │                │  │  ┌──────────────┐  ┌──────────────────┐  ││
│  │                │  │  │  Precedent   │  │  Suivant →       │  ││
│  │                │  │  └──────────────┘  └──────────────────┘  ││
│  │                │  │                                          ││
│  └────────────────┘  └──────────────────────────────────────────┘│
│                                                                  │
│  ┌──────────────────┐  ┌──────────────────┐                     │
│  │ Enregistrer      │  │ Soumettre        │  (si complet)       │
│  └──────────────────┘  └──────────────────┘                     │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

**Stepper - Etats des sections:**

| Etat     | Icone | Couleur | Description      |
| -------- | ----- | ------- | ---------------- |
| Complete | ✓     | Vert    | Section validee  |
| En cours | ●     | Bleu    | Section active   |
| A faire  | ○     | Gris    | Non visitee      |
| Erreur   | !     | Rouge   | Champs invalides |

**Actions disponibles par statut:**

| Statut    | Actions                           |
| --------- | --------------------------------- |
| BROUILLON | Enregistrer, Soumettre, Supprimer |
| SOUMIS    | (lecture seule pour metier)       |
| COMMENTE  | Modifier, Soumettre               |
| VALIDE    | (lecture seule)                   |
| REJETE    | (lecture seule)                   |

#### 5.3.4 Page Validation (Gouvernance)

```
┌─────────────────────────────────────────────────────────────────┐
│  Header                                                          │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  ┌──────────────────────────────────────────────────────────────┐│
│  │  Cas d'usage: Assistant IA pour analyse contrats             ││
│  │  Statut: SOUMIS    Classification: 🟠 HAUT RISQUE            ││
│  │  Porteur: Jean Dupont (DSIG)    Soumis le: 08/01/2026        ││
│  └──────────────────────────────────────────────────────────────┘│
│                                                                  │
│  ┌──────────────────────────────────────────────────────────────┐│
│  │  [Tabs: Resume | Details | Historique]                       ││
│  │                                                               ││
│  │  Section A : Identification                                   ││
│  │  - Direction: DSIG                                            ││
│  │  - BPDO: Jean Dupont                                          ││
│  │  ...                                                          ││
│  │                                                               ││
│  │  Section E : Haut Risque (2 reponses OUI)                    ││
│  │  ⚠ E8: Recrutement/Selection: OUI                            ││
│  │  ⚠ E9: Decisions cadre professionnel: OUI                    ││
│  │                                                               ││
│  └──────────────────────────────────────────────────────────────┘│
│                                                                  │
│  ┌────────────────────────────────────────────────────────────┐  │
│  │ Commentaire (obligatoire si rejet):                        │  │
│  │ ┌────────────────────────────────────────────────────────┐ │  │
│  │ │                                                        │ │  │
│  │ └────────────────────────────────────────────────────────┘ │  │
│  └────────────────────────────────────────────────────────────┘  │
│                                                                  │
│  ┌────────────┐  ┌────────────┐  ┌────────────┐                 │
│  │  Valider   │  │ Commenter  │  │  Rejeter   │                 │
│  │    ✓       │  │     💬     │  │     ✗      │                 │
│  └────────────┘  └────────────┘  └────────────┘                 │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### 5.4 Composants UI

#### 5.4.1 Classification Badge

```javascript
// Couleurs par classification
const CLASSIFICATION_COLORS = {
  INTERDIT: { bg: "#ffebee", color: "#c62828", icon: "🔴" },
  HAUT_RISQUE: { bg: "#fff3e0", color: "#ef6c00", icon: "🟠" },
  RISQUE_LIMITE: { bg: "#fff8e1", color: "#f9a825", icon: "🟡" },
  RISQUE_MINIMAL: { bg: "#e8f5e9", color: "#2e7d32", icon: "🟢" },
};
```

**Affichage:**

- Badge Chip avec icone couleur
- Tooltip avec description du niveau
- Animation pulse si INTERDIT (attire l'attention)

#### 5.4.2 Status Badge

```javascript
const STATUS_COLORS = {
  BROUILLON: { bg: "#e3f2fd", color: "#1565c0" },
  SOUMIS: { bg: "#fff3e0", color: "#ef6c00" },
  COMMENTE: { bg: "#fce4ec", color: "#c2185b" },
  VALIDE: { bg: "#e8f5e9", color: "#2e7d32" },
  REJETE: { bg: "#ffebee", color: "#c62828" },
};
```

#### 5.4.3 Auto-Save Indicator

```
┌─────────────────────────────────┐
│ 💾 Sauvegarde...                │  (pendant sauvegarde)
│ ✓ Sauvegarde automatique       │  (apres sauvegarde)
│ ⚠ Modifications non enregistrees│ (si erreur)
└─────────────────────────────────┘
```

- Position: Header du formulaire (droite)
- Animation: Fade in/out
- Timing: Affiche pendant 3s apres sauvegarde

#### 5.4.4 Dialog Confirmation

```
┌─────────────────────────────────────────┐
│  ⚠ Confirmer le rejet                   │
├─────────────────────────────────────────┤
│                                         │
│  Etes-vous sur de vouloir rejeter ce    │
│  cas d'usage ?                          │
│                                         │
│  Cette action notifiera le porteur      │
│  par email.                             │
│                                         │
├─────────────────────────────────────────┤
│              [Annuler]  [Rejeter]       │
└─────────────────────────────────────────┘
```

- Bouton danger en rouge
- Focus sur bouton Annuler par defaut
- Escape pour fermer

### 5.5 Theming MUI v7

#### 5.5.1 Palette de Couleurs

```javascript
const theme = createTheme({
  palette: {
    primary: {
      main: "#1976d2", // Bleu EDF
      light: "#42a5f5",
      dark: "#1565c0",
    },
    secondary: {
      main: "#ff6f00", // Orange accent
    },
    error: {
      main: "#d32f2f",
    },
    warning: {
      main: "#ed6c02",
    },
    success: {
      main: "#2e7d32",
    },
    background: {
      default: "#f5f5f5",
      paper: "#ffffff",
    },
  },
});
```

#### 5.5.2 Typography

```javascript
typography: {
  fontFamily: '"Roboto", "Helvetica", "Arial", sans-serif',
  h4: { fontWeight: 600 },
  h5: { fontWeight: 600 },
  h6: { fontWeight: 500 },
  body1: { fontSize: '1rem' },
  body2: { fontSize: '0.875rem' },
  caption: { fontSize: '0.75rem', color: 'text.secondary' },
}
```

#### 5.5.3 Spacing

- Base unit: 8px
- Standard spacing: `theme.spacing(1)` = 8px, `theme.spacing(2)` = 16px
- Margins sections: 24px (3 units)
- Padding cards: 16px (2 units)

### 5.6 Responsive Design

#### 5.6.1 Breakpoints

| Breakpoint | Largeur     | Usage            |
| ---------- | ----------- | ---------------- |
| xs         | 0-599px     | Mobile           |
| sm         | 600-899px   | Tablet portrait  |
| md         | 900-1199px  | Tablet landscape |
| lg         | 1200-1535px | Desktop          |
| xl         | 1536px+     | Large desktop    |

#### 5.6.2 Adaptations Mobile

**Formulaire:**

- Stepper: Passe en horizontal compact sur mobile
- Sections: Pleine largeur
- Boutons: Stack vertical

**DataGrid:**

- Colonnes cachees: Direction, Date sur mobile
- Actions: Menu dropdown au lieu de boutons

**Header:**

- Navigation: Menu hamburger sur mobile
- User menu: Icone seulement

### 5.7 Feedback Utilisateur

#### 5.7.1 Toasts (Snackbars)

| Type    | Couleur | Duree | Usage                   |
| ------- | ------- | ----- | ----------------------- |
| Success | Vert    | 3s    | Sauvegarde, validation  |
| Error   | Rouge   | 5s    | Erreurs API, validation |
| Warning | Orange  | 4s    | Avertissements          |
| Info    | Bleu    | 3s    | Informations            |

**Position:** Bas-centre de l'ecran

#### 5.7.2 Loading States

| Contexte     | Composant                         |
| ------------ | --------------------------------- |
| Page entiere | CircularProgress centre + overlay |
| Table        | Skeleton rows                     |
| Bouton       | CircularProgress dans le bouton   |
| Sauvegarde   | Indicator dans header             |

#### 5.7.3 Etats Vides

```
┌─────────────────────────────────────────┐
│                                         │
│           📋                            │
│                                         │
│    Aucun cas d'usage                    │
│                                         │
│    Creez votre premier cas d'usage      │
│    pour commencer le criblage AI Act    │
│                                         │
│    [+ Nouveau cas d'usage]              │
│                                         │
└─────────────────────────────────────────┘
```

### 5.8 Accessibilite

#### 5.8.1 Standards

- **WCAG 2.1 AA** compliance
- Contrast ratio minimum: 4.5:1
- Focus visible sur tous les elements interactifs
- Navigation clavier complete

#### 5.8.2 Implementation

```javascript
// Aria labels
<IconButton aria-label="Supprimer le cas d'usage">
  <DeleteIcon />
</IconButton>

// Skip links
<a href="#main-content" className="skip-link">
  Aller au contenu principal
</a>

// Form labels
<TextField
  id="nom-cas-usage"
  label="Nom du cas d'usage"
  aria-describedby="nom-cas-usage-helper"
/>
<FormHelperText id="nom-cas-usage-helper">
  Donnez un nom explicite a votre cas d'usage
</FormHelperText>
```

#### 5.8.3 Data-testid Strategy

```javascript
// Convention de nommage
data-testid="page-dashboard"
data-testid="form-section-a"
data-testid="input-nom-cas-usage"
data-testid="button-submit"
data-testid="dialog-confirm-delete"
data-testid="badge-classification-haut-risque"
data-testid="table-use-cases"
data-testid="filter-statut"
```

---

## 6. Schema de Base de Donnees

### 6.1 Diagramme Entite-Relation

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                              SCHEMA ARIA                                     │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  ┌─────────────┐       ┌─────────────────┐       ┌─────────────────┐       │
│  │   users     │       │   directions    │       │  use_cases      │       │
│  ├─────────────┤       ├─────────────────┤       ├─────────────────┤       │
│  │ id (PK)     │       │ id (PK)         │       │ id (PK)         │       │
│  │ username    │       │ nom             │       │ nom_cas_usage   │       │
│  │ email       │       │ code            │       │ description     │       │
│  │ password    │◄──────│ active          │◄──────│ direction_id(FK)│       │
│  │ role        │       └─────────────────┘       │ porteur_id (FK) │───────┤
│  │ uid_ldap    │                                 │ statut          │       │
│  │ created_at  │                                 │ classification  │       │
│  └─────────────┘                                 │ [104 champs...] │       │
│        │                                         │ created_at      │       │
│        │                                         │ updated_at      │       │
│        │                                         └─────────────────┘       │
│        │                                                  │                 │
│        │                                                  │                 │
│        │         ┌─────────────────┐                     │                 │
│        │         │   audit_logs    │                     │                 │
│        │         ├─────────────────┤                     │                 │
│        └────────►│ id (PK)         │◄────────────────────┘                 │
│                  │ use_case_id(FK) │                                       │
│                  │ user_id (FK)    │                                       │
│                  │ action          │                                       │
│                  │ details (JSON)  │                                       │
│                  │ created_at      │                                       │
│                  └─────────────────┘                                       │
│                                                                             │
│  ┌─────────────────┐                      ┌─────────────────┐              │
│  │  user_api_keys  │                      │  notifications  │              │
│  ├─────────────────┤                      ├─────────────────┤              │
│  │ id (PK)         │                      │ id (PK)         │              │
│  │ user_id (FK)    │                      │ use_case_id(FK) │              │
│  │ token_encrypted │                      │ type            │              │
│  │ name            │                      │ recipient_email │              │
│  │ last_used       │                      │ sent_at         │              │
│  │ created_at      │                      │ status          │              │
│  └─────────────────┘                      └─────────────────┘              │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 7. API et Endpoints

### 13.1 Vue d'ensemble des Routes

| Methode          | Endpoint                       | Description              | Auth    | Role        |
| ---------------- | ------------------------------ | ------------------------ | ------- | ----------- |
| **Auth**         |
| POST             | `/api/auth/login`              | Connexion (retourne JWT) | -       | -           |
| POST             | `/api/auth/login/cookie`       | Connexion cookie-based   | -       | -           |
| POST             | `/api/auth/refresh`            | Rafraichir token         | JWT     | \*          |
| POST             | `/api/auth/logout`             | Deconnexion              | JWT     | \*          |
| **Use Cases**    |
| GET              | `/api/use-cases`               | Lister (filtre par role) | JWT     | \*          |
| POST             | `/api/use-cases`               | Creer                    | JWT     | metier      |
| GET              | `/api/use-cases/{id}`          | Detail                   | JWT     | \*          |
| PUT              | `/api/use-cases/{id}`          | Modifier                 | JWT     | metier      |
| DELETE           | `/api/use-cases/{id}`          | Supprimer (brouillon)    | JWT     | metier      |
| POST             | `/api/use-cases/{id}/submit`   | Soumettre                | JWT     | metier      |
| POST             | `/api/use-cases/{id}/validate` | Valider                  | JWT     | gouvernance |
| POST             | `/api/use-cases/{id}/reject`   | Rejeter                  | JWT     | gouvernance |
| POST             | `/api/use-cases/{id}/comment`  | Commenter                | JWT     | gouvernance |
| GET              | `/api/use-cases/{id}/history`  | Historique audit         | JWT     | \*          |
| **Directions**   |
| GET              | `/api/directions`              | Lister                   | JWT     | \*          |
| **Users**        |
| GET              | `/api/users/me`                | Profil courant           | JWT     | \*          |
| PUT              | `/api/users/me`                | Modifier profil          | JWT     | \*          |
| **Admin**        |
| GET              | `/api/admin/users`             | Lister utilisateurs      | JWT     | admin       |
| POST             | `/api/admin/users`             | Creer utilisateur        | JWT     | admin       |
| PUT              | `/api/admin/users/{id}`        | Modifier utilisateur     | JWT     | admin       |
| DELETE           | `/api/admin/users/{id}`        | Supprimer utilisateur    | JWT     | admin       |
| POST             | `/api/admin/ldap/sync`         | Sync LDAP                | JWT     | admin       |
| GET              | `/api/admin/stats`             | Statistiques             | JWT     | admin/gouv  |
| **Export**       |
| GET              | `/api/export/csv`              | Export CSV               | JWT     | gouvernance |
| **External API** |
| GET              | `/api/external/use-cases`      | Liste (API Key)          | API Key | -           |
| GET              | `/api/external/use-cases/{id}` | Detail (API Key)         | API Key | -           |
| **Health**       |
| GET              | `/api/health`                  | Health check             | -       | -           |

---

## 8. Authentification et Securite

### 13.1 Flux d'Authentification

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         FLUX AUTHENTIFICATION                                │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  ┌──────────┐     ┌──────────────┐     ┌──────────────┐     ┌──────────┐  │
│  │  Client  │────►│   Backend    │────►│  Local DB    │────►│ Success  │  │
│  │  Login   │     │   (FastAPI)  │     │  (password)  │     │  (JWT)   │  │
│  └──────────┘     └──────┬───────┘     └──────────────┘     └──────────┘  │
│                          │                                        ▲         │
│                          │ Si echec local                         │         │
│                          ▼                                        │         │
│                   ┌──────────────┐     ┌──────────────┐          │         │
│                   │  LDAP Auth   │────►│   Success    │──────────┘         │
│                   │  (Fallback)  │     │  (Sync user) │                    │
│                   └──────────────┘     └──────────────┘                    │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 8 Nginx Configuration

```nginx
# frontend/nginx.conf
server {
    listen 3000;
    server_name localhost;
    root /usr/share/nginx/html;
    index index.html;

    # Gzip compression
    gzip on;
    gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;

    # Security headers
    add_header X-Frame-Options "SAMEORIGIN" always;
    add_header X-Content-Type-Options "nosniff" always;
    add_header X-XSS-Protection "1; mode=block" always;

    # React Router support
    location / {
        try_files $uri $uri/ /index.html;
    }

    # API proxy
    location /api {
        proxy_pass http://backend:8001;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    # Static files caching
    location ~* \.(js|css|png|jpg|jpeg|gif|ico|svg|woff|woff2|ttf|eot)$ {
        expires 1y;
        add_header Cache-Control "public, immutable";
    }
}
```

### 13.5 OpenShift Deployment Template

```yaml
# deploy/openshift/deployment-template.yaml
kind: Deployment
apiVersion: apps/v1
metadata:
  name: app-aria
  namespace: aria-namespace
  labels:
    app: app-aria
spec:
  replicas: 1
  selector:
    matchLabels:
      app: app-aria
  template:
    metadata:
      labels:
        app: app-aria
    spec:
      restartPolicy: Always
      serviceAccountName: aria-service-account
      terminationGracePeriodSeconds: 30
      securityContext: {}
      containers:
        # Frontend (Nginx)
        - name: app-aria-frontend
          image: "image-registry/aria-frontend:latest"
          resources:
            limits:
              cpu: "1"
              memory: 2Gi
            requests:
              cpu: 500m
              memory: 1Gi
          ports:
            - containerPort: 8080
              protocol: TCP
          imagePullPolicy: Always
          volumeMounts:
            - name: nginx-config-volume
              mountPath: /etc/nginx/conf.d/
          envFrom:
            - secretRef:
                name: aria-env
          securityContext:
            readOnlyRootFilesystem: false

        # Backend
        - name: app-aria-backend
          image: "image-registry/aria-backend:latest"
          command:
            - "-m"
          args:
            - uvicorn
            - "server:app"
            - "--host"
            - "0.0.0.0"
            - "--port"
            - "8001"
          resources:
            limits:
              cpu: "1"
              memory: 3Gi
            requests:
              cpu: 500m
              memory: 2Gi
          ports:
            - containerPort: 8001
              protocol: TCP
          imagePullPolicy: Always
          volumeMounts:
            - name: aria-config-volume
              mountPath: /.env
              subPath: .env
          envFrom:
            - secretRef:
                name: aria-env
          securityContext:
            capabilities:
              drop:
                - ALL
            runAsNonRoot: true
            readOnlyRootFilesystem: false
            allowPrivilegeEscalation: false

        # HAProxy (Load Balancer)
        - name: haproxy-container
          image: "image-registry/haproxy:2.8.2"
          command:
            - haproxy
            - "-f"
            - /etc/haproxy/haproxy.cfg
          ports:
            - containerPort: 5433
              protocol: TCP
          volumeMounts:
            - name: haproxy-secret-volume
              readOnly: true
              mountPath: /etc/haproxy/

      volumes:
        - name: aria-config-volume
          configMap:
            name: aria-config
            items:
              - key: .env
                path: .env
        - name: nginx-config-volume
          configMap:
            name: aria-nginx-conf
        - name: haproxy-secret-volume
          secret:
            secretName: aria-haproxy
  strategy:
    type: Recreate
  revisionHistoryLimit: 3
  progressDeadlineSeconds: 600
```

---

---

## 12. Patterns de Code

### 13.1 Regles Strictes

| Categorie    | Regle                                           |
| ------------ | ----------------------------------------------- |
| **Routers**  | HTTP only, NO db.execute(), NO business logic   |
| **Services** | All business logic, NO HTTPException            |
| **Database** | SQLAlchemy ORM only, parameterized queries      |
| **Typing**   | Tous les parametres et retours types            |
| **Security** | JAMAIS hardcoder de secrets, JAMAIS os.getenv() |

---

## 13. Annexes

### 13.1 Checklist Developpement

**Avant chaque commit:**

- [ ] Tests backend passent: `cd backend & mvn test`
- [ ] Tests frontend passent: `cd frontend && npm run test --watchAll=false`
- [ ] Pas de secrets hardcodes
- [ ] Routers sans logique metier
- [ ] Commit message conventionnel

**Avant chaque PR:**

- [ ] Tests E2E passent
- [ ] Pas de regressions classification AI Act
- [ ] Export CSV valide (104 champs)
- [ ] Notifications email fonctionnelles
- [ ] Documentation mise a jour

### 13.2 Commandes Utiles

```bash
# Backend
cd backend
mvn test                   # Tests
mvn spring-boot:run     # Dev server

# Frontend
cd frontend
npm run dev                    # Dev server
npm run build                    # Production build
npm run test --watchAll=false    # Tests
npm run lint                     # ESLint



# Docker
docker-compose up -d          # Demarrer
docker-compose down           # Arreter
docker-compose logs -f        # Logs
```

---
