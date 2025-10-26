# 📖 Partie I - Chapitre 3 : Structurer un prompt efficace

## 🎯 Objectif du chapitre

Maîtriser l'art de la structuration des prompts pour obtenir des résultats précis, cohérents et de haute qualité.

## 🏗️ Les 6 éléments essentiels d'un prompt structuré

### 1. 🎭 Rôle (Persona)
**Pourquoi :** Définit le comportement et l'expertise du modèle

**Structure :**
```
Tu es un [RÔLE] expérimenté avec [ANNÉES] d'expérience en [DOMAINE].
Tu as travaillé sur [TYPE DE PROJETS] pour [TYPE D'ENTREPRISES].
Tu maîtrises [TECHNOLOGIES] et les [MÉTHODOLOGIES].
```

**Exemples :**
```
Tu es un développeur PHP senior avec 10 ans d'expérience en e-commerce.
Tu es un DevOps engineer certifié AWS spécialisé dans les microservices.
Tu es un QA engineer expert en tests d'automatisation mobile.
```

### 2. 🎯 Tâche (Task)
**Pourquoi :** Définit clairement ce que vous attendez

**Structure :**
```
TA TÂCHE : [VERBE D'ACTION] [OBJET] [SPÉCIFICATIONS]
```

**Exemples :**
```
TA TÂCHE : Créer une API REST pour la gestion des utilisateurs
TA TÂCHE : Optimiser les performances d'une requête SQL complexe
TA TÂCHE : Générer des tests unitaires pour un service de paiement
```

### 3. 📋 Contexte (Context)
**Pourquoi :** Fournit les informations nécessaires pour bien comprendre

**Structure :**
```
CONTEXTE :
- [INFORMATION 1]
- [INFORMATION 2]
- [INFORMATION 3]
```

**Exemple :**
```
CONTEXTE :
- Application React avec TypeScript et Redux
- Base de données PostgreSQL avec Prisma ORM
- API REST documentée avec Swagger
- Tests avec Jest et React Testing Library
```

### 4. ⚙️ Contraintes (Constraints)
**Pourquoi :** Définit les limites et exigences techniques

**Structure :**
```
CONTRAINTES :
- [CONTRAINTE TECHNIQUE]
- [CONTRAINTE TEMPORELLE]
- [CONTRAINTE QUALITÉ]
```

**Exemple :**
```
CONTRAINTES :
- PHP 8.1 minimum, PSR-12 coding standards
- Compatible mobile-first responsive design
- Performance : temps de réponse < 200ms
- Sécurité : validation et sanitisation complètes
```

### 5. 📄 Format de sortie (Output Format)
**Pourquoi :** Spécifie exactement comment vous voulez le résultat

**Structure :**
```
FORMAT DE SORTIE :
1. [SECTION 1] : [DESCRIPTION]
2. [SECTION 2] : [DESCRIPTION]
3. [SECTION 3] : [DESCRIPTION]
```

**Exemple :**
```
FORMAT DE SORTIE :
1. Code complet du composant avec TypeScript
2. Exemple d'utilisation avec props
3. Tests unitaires avec Jest
4. Documentation JSDoc
5. Explications des choix techniques
```

### 6. ✅ Critères de qualité (Quality Criteria)
**Pourquoi :** Définit le niveau d'excellence attendu

**Structure :**
```
CRITÈRES DE QUALITÉ :
- [CRITÈRE 1]
- [CRITÈRE 2]
- [CRITÈRE 3]
```

**Exemple :**
```
CRITÈRES DE QUALITÉ :
- Code production-ready et sécurisé
- Performance optimisée
- Tests de validation inclus
- Documentation complète
- Respect des best practices
```

## 🚀 Template maître : Structure universelle

```
[PERSONA]
Tu es [RÔLE EXPÉRIMENTÉ] avec [ANNÉES D'EXPÉRIENCE].

[CONTEXTE]
Contexte du projet : [DESCRIPTION DÉTAILLÉE].

[TA TÂCHE]
TA TÂCHE : [OBJECTIF CLAIR ET SPÉCIFIQUE].

[CONTRAINTES]
Contraintes techniques et business :
- [CONTRAINTE 1]
- [CONTRAINTE 2]
- [CONTRAINTE 3]
- [CONTRAINTE 4]

[FORMAT]
Format de sortie structuré :
1. [SECTION 1] : [DESCRIPTION]
2. [SECTION 2] : [DESCRIPTION]
3. [SECTION 3] : [DESCRIPTION]
4. [SECTION 4] : [DESCRIPTION]

[QUALITÉ]
Critères de qualité attendus :
- [CRITÈRE 1]
- [CRITÈRE 2]
- [CRITÈRE 3]
- [CRITÈRE 4]

[DÉLAI]
Temps estimé : [DURÉE RÉALISTE]

LIVRABLE : Solution complète, testée, documentée, production-ready.
```

## 📊 Niveaux de structuration

### 🔴 Niveau 1 : Basique (Débutant)
```
"Écris une fonction PHP"
```

**Résultat :** Code fonctionnel mais basique, pas optimisé, pas de tests

### 🟡 Niveau 2 : Structuré (Intermédiaire)
```
Tu es développeur PHP.
Crée une fonction de validation d'email.
Inclue des tests.
```

**Résultat :** Code correct avec quelques tests, mais manque de contexte

### 🟢 Niveau 3 : Professionnel (Expert)
```
Tu es un développeur PHP senior avec 8 ans d'expérience en sécurité web.

TA TÂCHE : Créer une classe complète de validation d'emails avec :
- Validation syntaxique RFC 5322
- Vérification DNS des MX records
- Protection contre les attaques par injection
- Tests unitaires complets avec coverage 100%
- Documentation PHPDoc

CONTRAINTES :
- PHP 8.1+, PSR-12 standards
- Performance optimisée pour high-traffic
- Sécurité enterprise-grade
- Compatible avec les frameworks Laravel/Symfony

FORMAT DE SORTIE :
1. Code de la classe complète
2. Exemples d'utilisation
3. Suite de tests PHPUnit
4. Documentation d'intégration
5. Benchmark de performance

CRITÈRES DE QUALITÉ :
- Code sécurisé et auditable
- Performance < 10ms par validation
- Tests avec 100% coverage
- Documentation complète

LIVRABLE : Solution enterprise-ready
```

**Résultat :** Solution complète, professionnelle, production-ready

## 🎯 Exemples pratiques par domaine

### 🌐 Développement Web
```
Tu es un développeur frontend React/Next.js senior.

CONTEXTE : Application e-commerce avec 100K utilisateurs
- Stack : React 18, TypeScript, Tailwind CSS
- État : Redux Toolkit, API : REST avec React Query
- Design : Atomic Design, responsive mobile-first

TA TÂCHE : Créer un composant ProductCard avec :
- Affichage produit (image, nom, prix, rating)
- Actions (ajouter au panier, favoris, partage)
- États de chargement et d'erreur
- Animations smooth avec Framer Motion
- Tests unitaires et d'intégration

CONTRAINTES :
- Performance : LCP < 2.5s, CLS < 0.1
- Accessibilité : WCAG 2.1 AA
- SEO : meta tags, structured data
- Mobile-first responsive

FORMAT DE SORTIE :
1. Code TypeScript du composant
2. Styles Tailwind optimisés
3. Tests Jest + React Testing Library
4. Storybook stories
5. Documentation des props et usage

CRITÈRES DE QUALITÉ :
- Performance Core Web Vitals optimisée
- Accessibilité complète (screen reader, keyboard)
- Tests coverage > 90%
- Code réutilisable et maintenable

LIVRABLE : Composant production-ready pour e-commerce
```

### 🔧 Backend API
```
Tu es un développeur backend Node.js senior spécialisé en APIs.

CONTEXTE : API REST pour plateforme SaaS B2B
- Framework : Express.js avec TypeScript
- Base : PostgreSQL avec Prisma ORM
- Auth : JWT + OAuth2
- Documentation : Swagger/OpenAPI
- Tests : Jest + Supertest

TA TÂCHE : Implémenter l'endpoint /api/users/{id}/profile avec :
- GET : Récupérer le profil complet
- PUT : Mettre à jour le profil
- DELETE : Supprimer le profil
- Validation complète des données
- Gestion fine des permissions (owner/admin)
- Rate limiting et caching

CONTRAINTES :
- RESTful design, HATEOAS
- Validation Joi/Yup
- Sécurité : SQL injection, XSS protection
- Performance : response time < 100ms
- Monitoring : logs structurés

FORMAT DE SORTIE :
1. Route handlers Express
2. Middleware de validation
3. Tests API complets
4. Documentation OpenAPI/Swagger
5. Postman collection

CRITÈRES DE QUALITÉ :
- Sécurité enterprise-grade
- Performance optimisée
- Tests d'intégration complets
- Documentation API exhaustive

LIVRABLE : Endpoint production-ready sécurisé
```

## 📝 Prompt à trous : Structure professionnelle

```
PERSONA :
Tu es un [RÔLE] expérimenté avec [ANNÉES] d'expérience en [DOMAINE].
Tu as travaillé sur [TYPE_PROJETS] pour [ENTREPRISES].
Tu maîtrises [TECHNOLOGIES] et [MÉTHODOLOGIES].

CONTEXTE :
- Stack technique : [TECHNOLOGIES]
- Architecture : [PATTERN]
- Base de données : [TYPE]
- Contraintes : [SPÉCIFICATIONS]

TA TÂCHE :
[VERBE_ACTION] [OBJET] qui [SPÉCIFICATIONS_FONCTIONNELLES]

CONTRAINTES :
- [CONTRAINTE_TECHNIQUE_1]
- [CONTRAINTE_PERFORMANCE]
- [CONTRAINTE_SÉCURITÉ]
- [CONTRAINTE_QUALITÉ]

FORMAT DE SORTIE :
1. [LIVRABLE_1] : [DESCRIPTION]
2. [LIVRABLE_2] : [DESCRIPTION]
3. [LIVRABLE_3] : [DESCRIPTION]
4. [LIVRABLE_4] : [DESCRIPTION]

CRITÈRES DE QUALITÉ :
- [CRITÈRE_1]
- [CRITÈRE_2]
- [CRITÈRE_3]
- [CRITÈRE_4]

LIVRABLE : [TYPE_SOLUTION] production-ready, [ATTRIBUTS_QUALITÉ]
```

## 📈 Optimisation itérative des prompts

### Processus d'amélioration

```
1. Test du prompt initial
   ↓
2. Analyse des résultats
   ↓
3. Identification des lacunes
   ↓
4. Amélioration de la structure
   ↓
5. Re-test et validation
   ↓
6. Documentation des améliorations
```

### Questions pour améliorer un prompt

```
❓ Le rôle est-il assez spécifique ?
❓ Le contexte est-il complet ?
❓ Les contraintes sont-elles claires ?
❓ Le format de sortie est-il détaillé ?
❓ Les critères de qualité sont-ils mesurables ?
❓ Le livrable correspond-il aux attentes ?
```

## 🎯 Points clés à retenir

1. **Structure = Succès** : Plus le prompt est structuré, meilleur le résultat
2. **Persona précise** : Définissez clairement qui vous voulez que l'IA soit
3. **Contexte complet** : Donnez tous les éléments nécessaires
4. **Contraintes claires** : Spécifiez les limites et exigences
5. **Format explicite** : Dites exactement comment vous voulez la sortie
6. **Qualité mesurable** : Définissez des critères objectifs

## 🚀 Prochain chapitre

Maintenant que vous savez structurer vos prompts, découvrons les modèles de structure universels pour différents types de tâches.

---

## 📋 Checklist qualité

- ✅ 6 éléments essentiels expliqués
- ✅ Template maître fourni
- ✅ 3 niveaux de structuration
- ✅ Exemples concrets par domaine
- ✅ Processus d'optimisation
- ✅ Questions d'amélioration

## 🎯 Test de compréhension

**Question :** Pourquoi ajouter des contraintes techniques dans un prompt améliore-t-il les résultats ?

**Réponse attendue :** Les contraintes guident l'IA vers des solutions spécifiques, évitent les réponses génériques, forcent l'optimisation et garantissent que le résultat respecte les exigences techniques et business du projet.

---

**Temps de lecture estimé : 25 minutes**
**Niveau : Intermédiaire à Expert**
**Prérequis : Chapitres 1 et 2**
