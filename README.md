# Easy-Budget 💰

Une application de gestion budgétaire moderne et intuitive pour tracker vos dépenses et revenus en temps réel.

## 📋 Table des matières

- [Stack technologique](#stack-technologique)
- [Architecture](#architecture)
- [Installation](#installation)
- [Structure du projet](#structure-du-projet)
- [Démarrage rapide](#démarrage-rapide)
- [API Documentation](#api-documentation)
- [Contributing](#contributing)
- [Roadmap](#roadmap)

---

## 🛠 Stack technologique

| Layer | Technology |
|-------|-----------|
| **Frontend** | Angular 17+ / TypeScript |
| **Backend** | NestJS / TypeScript / Node.js |
| **Database** | PostgreSQL 15+ |
| **Authentication** | JWT (JSON Web Tokens) |
| **Validation** | class-validator / class-transformer |
| **ORM** | TypeORM |
| **Security** | bcrypt / Passport.js |

---

## 🏗 Architecture

```
easy-budget/
├── apps/
│   ├── api/                  # Backend NestJS
│   │   ├── src/
│   │   │   ├── auth/         # Module authentification
│   │   │   ├── transactions/ # Module transactions
│   │   │   ├── categories/   # Module catégories
│   │   │   ├── budgets/      # Module budgets
│   │   │   ├── common/       # Guards, decorators, utils
│   │   │   └── main.ts
│   │   ├── test/
│   │   ├── .env.example
│   │   └── package.json
│   │
│   └── web/                  # Frontend Angular
│       ├── src/
│       │   ├── app/
│       │   │   ├── core/     # Services, guards, interceptors
│       │   │   ├── features/ # Pages/components métier
│       │   │   ├── shared/   # Composants partagés
│       │   │   └── app.module.ts
│       │   └── main.ts
│       ├── angular.json
│       └── package.json
│
├── docker-compose.yml        # Services (PostgreSQL, pgAdmin)
├── .gitignore
├── README.md
└── package.json (root)
```

### Schéma PostgreSQL

```
users (1) ─── (N) categories
  │                    │
  │                    └─ (N) transactions
  └───────────────────────────┘
  
users (1) ─── (N) budgets ─── (N) categories
```

---

## 🚀 Installation

### Prérequis

- Node.js >= 18.0.0
- Docker & Docker Compose
- Git

### Étapes

**1. Cloner le repository**
```bash
git clone https://github.com/yourusername/easy-budget.git
cd easy-budget
```

**2. Installation des dépendances**
```bash
# Backend
cd apps/api
npm install

# Frontend (dans un autre terminal)
cd apps/web
npm install
```

**3. Configuration environnement**

Créer `.env` dans `apps/api/` :
```env
# Database
DATABASE_HOST=localhost
DATABASE_PORT=5432
DATABASE_USER=postgres
DATABASE_PASSWORD=postgres
DATABASE_NAME=easy_budget

# JWT
JWT_SECRET=your-super-secret-key-change-in-production
JWT_EXPIRATION=86400

# Environment
NODE_ENV=development
API_PORT=3000
```

**4. Démarrer PostgreSQL**
```bash
docker-compose up -d
```

Vérifier que PostgreSQL est actif :
```bash
docker-compose ps
# pgAdmin : http://localhost:5050 (admin@example.com / admin)
```

---

## 🎯 Démarrage rapide

### Backend (Terminal 1)
```bash
cd apps/api
npm run start:dev
# API disponible sur http://localhost:3000
```

### Frontend (Terminal 2)
```bash
cd apps/web
ng serve
# App disponible sur http://localhost:4200
```

### Accès

| Service | URL | Notes |
|---------|-----|-------|
| Frontend | http://localhost:4200 | Angular app |
| Backend API | http://localhost:3000 | NestJS API |
| Swagger Docs | http://localhost:3000/api/docs | (à configurer) |
| pgAdmin | http://localhost:5050 | DB management |

---

## 📦 Structure du projet

### Backend (NestJS)

**Modules principaux :**

#### `auth/`
- Gestion inscription/connexion
- JWT token generation
- Password hashing (bcrypt)
- Passport JWT strategy

#### `transactions/`
- CRUD transactions (dépenses/revenus)
- Filtrage par date/catégorie
- Agrégations (sommes, moyennes)
- Validation DECIMAL pour montants

#### `categories/`
- Gestion catégories utilisateur
- Catégories pré-créées
- Association transactions

#### `budgets/`
- Fixation limites par catégorie/mois
- Vérification dépassement
- Alertes

#### `common/`
- JWT Auth Guard
- Decorateurs (@CurrentUser)
- Filters (erreurs globales)
- Pipes (validation)

**Patterns utilisés :**
- Dependency Injection (modules)
- Data Transfer Objects (DTOs)
- Entity-Repository pattern (TypeORM)
- JWT Strategy (Passport)

### Frontend (Angular)

**Structure :**

```
core/
  ├── auth/
  │   ├── auth.service.ts
  │   ├── auth.guard.ts
  │   └── auth.interceptor.ts
  └── services/
      └── api.service.ts

features/
  ├── auth/
  │   ├── login/
  │   └── register/
  ├── transactions/
  │   ├── transaction-list/
  │   ├── transaction-form/
  │   └── transaction-detail/
  ├── dashboard/
  └── categories/

shared/
  ├── components/
  │   ├── header/
  │   ├── navbar/
  │   └── loading-spinner/
  └── models/
      ├── transaction.model.ts
      ├── category.model.ts
      └── user.model.ts
```

**Patterns utilisés :**
- Smart & Dumb components
- RxJS Observables
- FormGroup (Reactive Forms)
- HttpInterceptor (JWT token)
- Route guards

---

## 🔌 API Documentation

### Base URL
```
http://localhost:3000/api
```

---

## 🔐 Authentification

### JWT Flow

```
1. User Register/Login
   ↓
2. Server génère JWT token (eyJhbGc...)
   ↓
3. Frontend stocke en localStorage
   ↓
4. Chaque requête inclut : Authorization: Bearer {token}
   ↓
5. Backend valide token via Passport JWT Strategy
   ↓
6. Token expire après 24h (configurable)
```

### Secured Routes

Toutes les routes sauf `/auth/register` et `/auth/login` nécessitent un token valide.

Exemple Angular Guard :
```typescript
canActivate(): boolean {
  return this.authService.isAuthenticated();
}
```

---

## 📝 Convention de nommage

### Branches
```
feature/US-1.1-signup
bugfix/transaction-validation
refactor/database-queries
docs/api-endpoints
```

### Commits
```
feat(auth): implement JWT token generation
fix(transactions): correct decimal validation
refactor(categories): simplify service logic
docs(readme): update setup instructions
```

### Issues GitHub
```
[TIER-1] US-1.1 : Inscription utilisateur
[TIER-2] US-2.3 : Rechercher transactions
[BUG] Montant affiché incorrectement
```

---

## 🧪 Testing

(À implémenter)

```bash
# Backend
cd apps/api
npm run test
npm run test:e2e

# Frontend
cd apps/web
ng test
```

---

## 📊 Roadmap

### Phase 1 : MVP (v0.1) - Juin 2026
- ✅ Auth (register/login)
- ✅ CRUD transactions
- ✅ Catégories de base
- ✅ Dashboard simple
- **Status** : En développement (TIER 1)

### Phase 2 : Core Features (v0.2) - Juillet 2026
- Budget management
- Filtres avancés
- Export CSV
- Stats mensuelles
- **Status** : À planifier (TIER 2)

### Phase 3 : Advanced (v0.3+) - Q3 2026
- Transactions récurrentes
- Partage familial
- App mobile (Ionic)
- Scan reçus
- **Status** : Backlog (TIER 3)

---

## 🚨 Problèmes connus

- [ ] Import CSV pas encore implémenté
- [ ] Notifications push (mobile) à développer
- [ ] Performance avec >10k transactions/an

---

## 📚 Resources

- [NestJS Docs](https://docs.nestjs.com)
- [Angular Docs](https://angular.io/docs)
- [TypeORM Docs](https://typeorm.io)
- [PostgreSQL Docs](https://www.postgresql.org/docs)
- [JWT.io](https://jwt.io)

---

## 👥 Contributing

Les contributions sont bienvenues ! 

### Process
1. Fork le repo
2. Créer une branch (`git checkout -b feature/amazing-feature`)
3. Commit (`git commit -m 'feat: add amazing feature'`)
4. Push (`git push origin feature/amazing-feature`)
5. Ouvrir une Pull Request

### Guidelines
- Code coverage minimum : 80%
- Respecter la convention de nommage
- Documenter les changements majeurs
- Tester avant de PR

---

## 📄 License

Pas encore - MIT maybe (?)

---

## 👨‍💻 Author

Créé par [Kevyn SOLA](https://github.com/kevyn-sola)

---

## 📞 Support

Pour les questions ou bugs :
- 🐛 [Issues GitHub](https://github.com/kevyn-sola/easy-budget/issues)
- 💬 [Discussions GitHub](https://github.com/kevyn-sola/easy-budget/discussions)

---

**Dernière mise à jour :** Juin 2026
