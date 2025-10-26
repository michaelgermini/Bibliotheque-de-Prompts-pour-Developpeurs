# 📖 Partie I - Chapitre 5 : Contexte, rôles et formats de sortie

## 🎯 Objectif du chapitre

Maîtriser l'utilisation du contexte, des rôles et des formats de sortie pour créer des prompts ultra-efficaces et obtenir exactement ce que vous voulez.

## 🎭 Rôles avancés : Personas expertes

### 1. 👨‍💻 Développeur par spécialité

#### Full-Stack Developer
```
Tu es un développeur full-stack senior avec 10 ans d'expérience.
Tu maîtrises le développement frontend (React, Vue, Angular) et backend (Node.js, PHP, Python).
Tu es expert en bases de données (SQL/NoSQL) et DevOps (Docker, AWS).
Tu conçois des architectures scalables et des APIs REST/GraphQL.
```

#### Frontend Specialist
```
Tu es un développeur frontend expert avec 8 ans d'expérience en React/Next.js.
Tu es spécialisé dans les performances Web (Core Web Vitals) et l'accessibilité (WCAG 2.1).
Tu maîtrises TypeScript, CSS-in-JS, animations (Framer Motion), et les tests (Jest, RTL).
Tu optimises pour SEO et l'expérience utilisateur mobile-first.
```

#### Backend Architect
```
Tu es un software architect backend avec 12 ans d'expérience.
Tu conçois des systèmes distribués, des microservices, et des APIs enterprise.
Tu es expert en patterns (DDD, CQRS, Event Sourcing) et bases de données (PostgreSQL, MongoDB, Redis).
Tu optimises pour la performance, la sécurité, et la scalabilité cloud (AWS/Azure/GCP).
```

### 2. 🧪 Experts QA et Testing

#### QA Automation Engineer
```
Tu es un QA engineer senior spécialisé dans l'automatisation des tests.
Tu maîtrises Selenium, Cypress, Playwright pour les tests end-to-end.
Tu écris des tests unitaires (Jest, PHPUnit) et d'intégration (Postman, Supertest).
Tu es expert en CI/CD (GitHub Actions, Jenkins) et en qualité logicielle.
```

#### Security Tester
```
Tu es un security tester certifié OSCP/CEH avec 8 ans d'expérience.
Tu effectues des tests de pénétration, des audits de sécurité, et des analyses de vulnérabilités.
Tu connais OWASP Top 10, les techniques d'injection, et les standards de sécurité.
Tu utilises Burp Suite, OWASP ZAP, et des frameworks de fuzzing.
```

### 3. 🏗️ Rôles d'architecture et DevOps

#### DevOps Engineer
```
Tu es un DevOps engineer certifié AWS/Kubernetes avec 10 ans d'expérience.
Tu conçois et maintiens des pipelines CI/CD, des infrastructures cloud, et des déploiements automatisés.
Tu es expert en Docker, Kubernetes, Terraform, et en monitoring (Prometheus, Grafana).
Tu optimises les coûts cloud et assures la sécurité des infrastructures.
```

#### Database Administrator
```
Tu es un DBA expert PostgreSQL/MySQL avec 12 ans d'expérience.
Tu optimises les requêtes, conçois les schémas, et gères les migrations.
Tu es spécialisé dans la performance, la réplication, et la sauvegarde.
Tu connais les patterns de sharding et les techniques de partitionnement.
```

## 📚 Formats de sortie avancés

### 1. 💻 Code et fichiers

#### Code formaté avec syntax highlighting
```
FORMAT :
1. Code complet dans le langage demandé
2. Structure de fichiers avec arborescence
3. Configuration (package.json, composer.json, etc.)
4. Scripts de build/déploiement

QUALITÉ : Code prêt pour git commit
```

#### Structure de projet complète
```
FORMAT :
📁 [PROJET]/
├── 📁 src/
│   ├── 📁 components/
│   ├── 📁 services/
│   ├── 📁 utils/
│   └── 📁 types/
├── 📁 tests/
├── 📁 docs/
├── 📄 README.md
├── 📄 package.json
└── 📄 Dockerfile

LIVRABLES :
1. Code de chaque fichier
2. Configuration complète
3. Tests pour chaque module
4. Documentation d'installation
```

### 2. 📋 Documentation structurée

#### API Documentation (OpenAPI/Swagger)
```
FORMAT :
openapi: 3.0.3
info:
  title: [API_NAME]
  version: 1.0.0
paths:
  [ENDPOINT]:
    [METHOD]:
      summary: [DESCRIPTION]
      parameters:
        - name: [PARAM]
          required: true
          schema:
            type: [TYPE]
      responses:
        '200':
          description: Success
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/[SCHEMA]'

QUALITÉ : Documentation API complète et testable
```

#### README.md professionnel
```
FORMAT :
# [PROJET]

[![Build Status]([BADGE])]([URL])
[![Coverage]([BADGE])]([URL])

## 🚀 Installation

## 📖 Usage

## 🔧 Configuration

## 🧪 Tests

## 📚 API Reference

## 🤝 Contributing

## 📄 License

QUALITÉ : README complet et developer-friendly
```

### 3. 🧪 Tests et validation

#### Suite de tests complète
```
FORMAT :
📁 tests/
├── 📁 unit/
│   ├── [MODULE].test.js
│   └── [SERVICE].test.js
├── 📁 integration/
│   ├── [API].test.js
│   └── [COMPONENT].test.js
├── 📁 e2e/
│   ├── [FLOW].cy.js
│   └── [JOURNEY].playwright.js
├── 📄 setup.js
└── 📄 jest.config.js

LIVRABLES :
1. Tests unitaires avec mocks
2. Tests d'intégration avec API
3. Tests E2E avec navigation
4. Configuration CI complète
5. Coverage report
```

### 4. 📊 Données et configuration

#### JSON/YAML structuré
```
FORMAT :
{
  "configuration": {
    "database": {
      "host": "localhost",
      "port": 5432,
      "database": "myapp"
    },
    "api": {
      "version": "v1",
      "rateLimit": "1000/hour"
    }
  }
}

QUALITÉ : Configuration validée et documentée
```

## 🎯 Contextes avancés : Information maximale

### 1. 📋 Contexte technique complet
```
CONTEXTE TECHNIQUE :
- Framework : [FRAMEWORK] v[VERSION]
- Langage : [LANGAGE] [VERSION]
- Base de données : [BDD] avec [ORM/DRIVER]
- Cloud : [AWS/AZURE/GCP] avec [SERVICES]
- CI/CD : [JENKINS/GITHUB/CIRCLE]
- Monitoring : [TOOLKIT]
- Tests : [FRAMEWORK] avec [COVERAGE]%
```

### 2. 🎨 Contexte design et UX
```
CONTEXTE DESIGN :
- Design System : [MATERIAL/ANT/BOOTSTRAP]
- Responsive : Mobile-first, breakpoints [SIZES]
- Accessibilité : WCAG [LEVEL], screen reader support
- Performance : Core Web Vitals [TARGETS]
- SEO : Meta tags, structured data, sitemap
- Analytics : [GA/GTM/MIXPANEL]
```

### 3. 🏢 Contexte business et contraintes
```
CONTEXTE BUSINESS :
- Utilisateurs : [NOMBRE], [TYPE]
- Charge : [REQUETES/SEC], [PIC ATTENDU]
- Régions : [GEOGRAPHIE]
- Compliance : [GDPR/HIPAA/SOX]
- Budget : [CONTRAINTE]
- Deadline : [DATE]
```

## 📊 Formats de sortie par type de projet

### 🌐 Application Web Frontend
```
FORMAT DE SORTIE :
1. 📁 Structure du projet React/Next.js
2. 📄 Composants TypeScript avec props
3. 🎨 Styles CSS-in-JS optimisés
4. 🧪 Tests Jest + React Testing Library
5. 📚 Storybook pour composants
6. ♿ Tests d'accessibilité automatisés
7. 🚀 Build optimisé pour production

CONTRAINTES QUALITÉ :
- Performance : LCP < 2.5s, CLS < 0.1
- Accessibilité : WCAG 2.1 AA
- SEO : Server-side rendering
- Mobile : Responsive design
```

### 🔧 API Backend REST
```
FORMAT DE SORTIE :
1. 🏗️ Architecture API avec diagrammes
2. 📄 Endpoints Express/Fastify
3. 🛡️ Middleware de sécurité
4. 🧪 Tests API avec Supertest
5. 📚 Documentation OpenAPI/Swagger
6. 📊 Monitoring et logging
7. 🚀 Dockerfile et docker-compose

CONTRAINTES QUALITÉ :
- Performance : < 100ms response time
- Sécurité : OWASP Top 10 compliance
- Tests : > 90% coverage
- Documentation : Auto-generated
```

### 📱 Application Mobile
```
FORMAT DE SORTIE :
1. 📁 Structure projet React Native/Flutter
2. 📱 Composants UI natifs
3. 🔄 State management (Redux/Context)
4. 📡 API integration avec caching
5. 🧪 Tests unitaires et d'intégration
6. 📦 Build pour iOS/Android
7. 🔍 Tests sur devices réels

CONTRAINTES QUALITÉ :
- Performance : 60fps animations
- Offline : Sync capabilities
- Security : Secure storage
- UX : Native feel
```

### ☁️ Infrastructure DevOps
```
FORMAT DE SORTIE :
1. 🏗️ Architecture infrastructure
2. 📄 Terraform/Infrastructure as Code
3. 🐳 Docker et Kubernetes manifests
4. 🚀 Pipelines CI/CD GitHub Actions
5. 📊 Monitoring et alerting
6. 🔒 Security hardening
7. 📚 Documentation runbooks

CONTRAINTES QUALITÉ :
- Scalabilité : Auto-scaling
- Sécurité : Zero trust
- Monitoring : 99.9% uptime
- Cost : Optimisé cloud
```

## 🎯 Template Maître : Prompt contextuel complet

```
[PERSONA EXPERTISE]
Tu es un [RÔLE] senior avec [ANNÉES] d'expérience en [DOMAINES].
Tu as travaillé sur [TYPE_PROJETS] pour [ENTREPRISES].
Tu maîtrises [TECHNOLOGIES] et [MÉTHODOLOGIES].

[CONTEXTE PROJET DÉTAILLÉ]
PROJET : [NOM] - [DESCRIPTION]
STACK : [TECHNOLOGIES] v[VERSION]
ARCHITECTURE : [PATTERN]
ÉQUIPE : [TAILLE] développeurs
UTILISATEURS : [NOMBRE], [TYPE]
CONTRAINTES : [BUSINESS/TECH]

[OBJECTIF PRÉCIS]
TA TÂCHE : [ACTION] [OBJET] qui [SPÉCIFICATIONS]

[CONTRAINTES TECHNIQUES]
FRAMEWORK : [NOM] v[VERSION]
PERFORMANCE : [MÉTRIQUES]
SÉCURITÉ : [STANDARDS]
QUALITÉ : [COVERAGE/TESTS]

[FORMAT DE SORTIE STRUCTURÉ]
📁 LIVRABLES :
1. [FICHIER 1] : [DESCRIPTION]
   - Format : [TYPE]
   - Contenu : [DÉTAILS]
2. [FICHIER 2] : [DESCRIPTION]
3. [FICHIER 3] : [DESCRIPTION]

📋 DOCUMENTATION :
- README avec instructions
- API docs si applicable
- Guide de déploiement

🧪 TESTS :
- Unitaires : [FRAMEWORK]
- Intégration : [OUTILS]
- Coverage : > [POURCENTAGE]%

[CRITÈRES D'EXCELLENCE]
- [CRITÈRE 1] : [MÉTRIQUE]
- [CRITÈRE 2] : [MÉTRIQUE]
- [CRITÈRE 3] : [MÉTRIQUE]

LIVRABLE FINAL : Solution complète, testée, documentée, production-ready
```

## 📝 Prompt à trous : Contextes et formats

### 🎭 Template PERSONA
```
Tu es un [RÔLE] [NIVEAU] avec [ANNÉES] d'expérience.

EXPÉRIENCE :
- [DOMAINE 1] : [ANNÉES] ans
- [DOMAINE 2] : [ANNÉES] ans
- Projets : [TYPES]
- Entreprises : [SECTEUR]

EXPERTISE :
- Technologies : [TECH1, TECH2, TECH3]
- Méthodologies : [METHOD1, METHOD2]
- Certifications : [CERT1, CERT2]

STYLE :
- [ATTRIBUT1] : [DÉTAILS]
- [ATTRIBUT2] : [DÉTAILS]
```

### 📋 Template CONTEXTE
```
CONTEXTE PROJET :
- Type : [WEB/MOBILE/API/INFRA]
- Stack : [TECHNOLOGIES]
- Architecture : [PATTERN]
- Équipe : [TAILLE] personnes
- Utilisateurs : [NOMBRE/TYPE]

CONTRAINTES :
- [TECHNIQUE] : [DÉTAILS]
- [BUSINESS] : [DÉTAILS]
- [TEMPS] : [DÉLAI]
- [BUDGET] : [CONTRAINTE]

OBJECTIFS :
- [FONCTIONNEL] : [DESCRIPTION]
- [PERFORMANCE] : [MÉTRIQUES]
- [QUALITÉ] : [STANDARDS]
```

### 📄 Template FORMAT
```
FORMAT DE SORTIE :
📁 Structure : [ARBORESCENCE]

📄 Fichiers :
1. [FICHIER] : [LANGAGE]
   - Objet : [DESCRIPTION]
   - Contenu : [SPÉCIFICATIONS]

🧪 Tests :
- [TYPE] : [FRAMEWORK]
- Coverage : [POURCENTAGE]%
- [OUTILS] : [DÉTAILS]

📚 Documentation :
- [TYPE] : [FORMAT]
- Sections : [LISTE]
- [EXEMPLES/REFERENCES]

QUALITÉ : [NIVEAU] production-ready
```

## 🎯 Points clés à retenir

1. **Persona précise** = comportement cohérent
2. **Contexte complet** = solutions adaptées
3. **Format structuré** = livrables utilisables
4. **Contraintes explicites** = résultats conformes
5. **Critères mesurables** = qualité garantie

## 🚀 Prochain chapitre

Maintenant que vous maîtrisez le contexte et les formats, apprenons à optimiser l'interaction avec l'IA pour des résultats encore meilleurs.

---

## 📋 Checklist qualité

- ✅ Rôles avancés par spécialité
- ✅ Formats de sortie détaillés
- ✅ Contextes techniques complets
- ✅ Templates pour chaque type de projet
- ✅ Template maître contextuel

## 🎯 Test de compréhension

**Question :** Pourquoi le contexte business est-il aussi important que le contexte technique dans un prompt ?

**Réponse attendue :** Le contexte business (utilisateurs, contraintes, objectifs) guide l'IA vers des solutions qui répondent aux vrais besoins métier, pas seulement aux spécifications techniques. Cela évite les solutions techniquement parfaites mais inadaptées au business.

---

**Temps de lecture estimé : 35 minutes**
**Niveau : Expert**
**Prérequis : Chapitres 1, 2, 3, 4**
