# 📖 Partie I - Chapitre 4 : Modèles de structure universels

## 🎯 Objectif du chapitre

Découvrir les modèles de prompts éprouvés qui fonctionnent pour tous types de tâches de développement, avec des templates prêts à l'emploi.

## 🏗️ Les 7 modèles universels

### 1. 🎯 Modèle CRÉATION (Génération de code)
**Quand l'utiliser :** Créer du nouveau code, composants, classes

**Structure :**
```
Tu es un [RÔLE] expérimenté.

TA TÂCHE : Créer [TYPE D'ÉLÉMENT] qui [FONCTIONNALITÉ]

SPÉCIFICATIONS :
- [FONCTION 1]
- [FONCTION 2]
- [FONCTION 3]

CONTRAINTES :
- [LANGAGE/VERSION]
- [FRAMEWORK]
- [STANDARDS]

FORMAT :
1. Code complet et fonctionnel
2. Exemple d'utilisation
3. Tests de validation
4. Documentation

QUALITÉ : Production-ready, testé, documenté
```

**Exemple - Composant React :**
```
Tu es un développeur React senior avec 8 ans d'expérience.

TA TÂCHE : Créer un composant Modal réutilisable qui :
- S'ouvre/ferme avec animation
- Supporte le focus trap
- Gère l'escape key
- Bloque le scroll du body

SPÉCIFICATIONS :
- React 18 + TypeScript
- Hook useReducer pour l'état
- Animations CSS-in-JS (emotion/styled-components)
- Accessibilité WCAG 2.1 AA
- Props : isOpen, onClose, title, children, size

CONTRAINTES :
- Mobile-first responsive
- Performance optimisée (lazy loading)
- Tests Jest + React Testing Library
- Storybook stories incluses

FORMAT :
1. Code TypeScript complet du composant
2. Styles CSS-in-JS optimisés
3. Exemple d'utilisation avec différents cas
4. Suite de tests complète (unit + integration)
5. Stories Storybook
6. README avec API documentation

QUALITÉ : Composant enterprise-ready, accessible, performant
```

### 2. 🔧 Modèle RÉPARATION (Debug/Fix)
**Quand l'utiliser :** Corriger des bugs, optimiser du code existant

**Structure :**
```
Tu es un [RÔLE] expert en debugging.

TA TÂCHE : Analyser et corriger [PROBLÈME] dans ce code

CODE À ANALYSER :
[CODE]

PROBLÈMES SIGNALÉS :
- [PROBLÈME 1]
- [PROBLÈME 2]

FORMAT :
1. Diagnostic détaillé des problèmes
2. Code corrigé avec explications
3. Tests de validation
4. Prévention pour l'avenir

QUALITÉ : Solution robuste et documentée
```

**Exemple - Debug SQL :**
```
Tu es un DBA PostgreSQL senior avec 10 ans d'expérience.

TA TÂCHE : Optimiser cette requête lente et corriger les problèmes

REQUÊTE ACTUELLE :
```sql
SELECT * FROM users u
JOIN orders o ON u.id = o.user_id
WHERE u.created_at > '2023-01-01'
ORDER BY u.created_at DESC
```

PROBLÈMES SIGNALÉS :
- Requête lente sur 1M+ d'enregistrements
- Index manquants probables
- SELECT * non optimisé
- Pas de pagination

FORMAT :
1. Analyse des problèmes de performance
2. Explication des goulots d'étranglement
3. Requête optimisée avec index suggérés
4. Plan d'exécution avant/après
5. Tests de performance
6. Recommandations de monitoring

QUALITÉ : Requête optimisée < 100ms, solution scalable
```

### 3. 📚 Modèle DOCUMENTATION (Docs/Comments)
**Quand l'utiliser :** Documenter du code, APIs, processus

**Structure :**
```
Tu es un technical writer spécialisé en [DOMAINE].

TA TÂCHE : Documenter [ÉLÉMENT] de manière complète et claire

ÉLÉMENT À DOCUMENTER :
[CODE/API/PROCESS]

FORMAT :
1. Vue d'ensemble et objectifs
2. Architecture et composants
3. Guide d'utilisation détaillé
4. Exemples pratiques
5. FAQ et troubleshooting

QUALITÉ : Documentation claire, complète, à jour
```

**Exemple - API Documentation :**
```
Tu es un technical writer API spécialisé en REST APIs.

TA TÂCHE : Créer la documentation complète pour cette API endpoint

ENDPOINT : POST /api/users/{id}/profile
DESCRIPTION : Met à jour le profil utilisateur avec validation

CODE :
[CODE DE L'ENDPOINT]

FORMAT :
1. Description générale de l'endpoint
2. Spécification OpenAPI/Swagger complète
3. Guide d'authentification et autorisation
4. Exemples de requêtes/réponses
5. Gestion d'erreurs et codes HTTP
6. Rate limiting et quotas
7. Tests Postman/cURL
8. Migration guide si applicable

QUALITÉ : Documentation developer-friendly, complète, testée
```

### 4. 🧪 Modèle TEST (Tests unitaires/intégration)
**Quand l'utiliser :** Générer des tests pour du code existant

**Structure :**
```
Tu es un QA engineer expert en [FRAMEWORK DE TEST].

TA TÂCHE : Créer une suite de tests complète pour [CODE]

CODE À TESTER :
[CODE]

COUVERTURE ATTENDUE :
- [FONCTION 1] : [SCÉNARIOS]
- [FONCTION 2] : [SCÉNARIOS]

FORMAT :
1. Tests unitaires isolés
2. Tests d'intégration
3. Tests edge cases
4. Mock et fixtures
5. Configuration CI/CD

QUALITÉ : Coverage > 90%, tests fiables
```

**Exemple - Tests React :**
```
Tu es un QA engineer expert en tests React avec Jest et RTL.

TA TÂCHE : Créer une suite de tests complète pour ce composant

COMPOSANT :
```tsx
const ProductCard = ({ product, onAddToCart }) => {
  return (
    <div className="product-card">
      <img src={product.image} alt={product.name} />
      <h3>{product.name}</h3>
      <p>{product.price}€</p>
      <button onClick={() => onAddToCart(product)}>
        Ajouter au panier
      </button>
    </div>
  );
};
```

COUVERTURE ATTENDUE :
- Rendu correct avec props
- Gestion des props manquantes
- Clic sur le bouton
- Accessibilité (alt text, keyboard)
- Responsive design
- Edge cases (prix négatif, image manquante)

FORMAT :
1. Tests unitaires avec Jest
2. Tests d'intégration avec React Testing Library
3. Tests d'accessibilité avec axe-core
4. Tests responsive avec different viewports
5. Mock des fonctions externes
6. Configuration Jest complète

QUALITÉ : Tests maintenables, coverage > 95%
```

### 5. 🔄 Modèle RÉFACTORING (Amélioration de code)
**Quand l'utiliser :** Améliorer du code existant, appliquer des patterns

**Structure :**
```
Tu es un [RÔLE] expert en [PATTERNS/MÉTHODOLOGIES].

TA TÂCHE : Refactorer ce code selon [PRINCIPE/PATTERN]

CODE ACTUEL :
[CODE]

AMÉLIORATIONS ATTENDUES :
- [AMÉLIORATION 1]
- [AMÉLIORATION 2]
- [AMÉLIORATION 3]

FORMAT :
1. Analyse du code existant
2. Problèmes identifiés
3. Code refactoré
4. Explications des changements
5. Tests de non-régression

QUALITÉ : Code plus maintenable et performant
```

**Exemple - Refactoring Legacy :**
```
Tu es un développeur PHP senior expert en Clean Code.

TA TÂCHE : Refactorer cette classe legacy vers les standards modernes

CLASSE ACTUELLE :
```php
class UserManager {
    public function doSomething() {
        // 200 lignes de code spaghetti
        // variables globales, SQL inline, pas de validation
    }
}
```

AMÉLIORATIONS ATTENDUES :
- Séparation des responsabilités (SRP)
- Injection de dépendances
- Validation des données
- Prepared statements
- Tests unitaires
- Documentation

FORMAT :
1. Analyse des problèmes SOLID
2. Diagramme des classes après refactoring
3. Code refactoré avec patterns
4. Explications détaillées des changements
5. Tests de migration
6. Guide de déploiement

QUALITÉ : Code maintenable, testable, scalable
```

### 6. 🎨 Modèle DESIGN (Architecture/Patterns)
**Quand l'utiliser :** Concevoir l'architecture, choisir des patterns

**Structure :**
```
Tu es un software architect certifié [CERTIFICATION].

TA TÂCHE : Concevoir [ARCHITECTURE/SOLUTION] pour [PROBLÈME]

CONTEXTE :
- [CONTRAINTE 1]
- [CONTRAINTE 2]
- [CONTRAINTE 3]

FORMAT :
1. Analyse des requirements
2. Proposition architecturale
3. Diagramme d'architecture
4. Technologies recommandées
5. Plan d'implémentation
6. Considérations de sécurité et performance

QUALITÉ : Architecture scalable et maintenable
```

**Exemple - Microservices :**
```
Tu es un software architect AWS certifié avec 12 ans d'expérience.

TA TÂCHE : Concevoir une architecture microservices pour une plateforme e-commerce

CONTEXTE :
- 10M utilisateurs actifs
- 100K commandes/jour
- Équipe distribuée (5 pays)
- Budget AWS : 50K$/mois
- Compliance PCI-DSS
- Deployment multiple regions

FORMAT :
1. Analyse des requirements business
2. Proposition d'architecture microservices
3. Diagramme AWS complet
4. Technologies par service
5. Stratégie de déploiement CI/CD
6. Plan de monitoring et observabilité
7. Considérations sécurité et compliance
8. Roadmap de migration depuis monolith

QUALITÉ : Architecture cost-effective, scalable, secure
```

### 7. 📊 Modèle ANALYSE (Audit/Review)
**Quand l'utiliser :** Auditer du code, analyser des performances, review

**Structure :**
```
Tu es un [RÔLE] expert en [TYPE D'ANALYSE].

TA TÂCHE : Analyser et auditer [ÉLÉMENT] selon [STANDARDS]

ÉLÉMENT À ANALYSER :
[CODE/PROJET/SYSTEM]

CRITÈRES D'ANALYSE :
- [CRITÈRE 1]
- [CRITÈRE 2]
- [CRITÈRE 3]

FORMAT :
1. Vue d'ensemble
2. Analyse détaillée par critère
3. Problèmes identifiés (sévérité)
4. Recommandations prioritaires
5. Plan d'action
6. Métriques d'amélioration

QUALITÈ : Analyse objective et actionable
```

**Exemple - Security Audit :**
```
Tu es un security engineer certifié OSCP avec 10 ans d'expérience.

TA TÂCHE : Audit de sécurité complet de cette application web

APPLICATION :
- Framework : Laravel 10
- Base : MySQL
- Frontend : React + API REST
- Hébergement : AWS

CODE À AUDITER :
[CODE SENSIBLE]

CRITÈRES D'ANALYSE :
- OWASP Top 10 2021
- Authentification/autorisation
- Injection SQL/XSS/CSRF
- Configuration sécurité
- Gestion des secrets
- Logging et monitoring

FORMAT :
1. Méthodologie d'audit
2. Vulnérabilités par sévérité (Critical/High/Medium/Low)
3. Code sécurisé recommandé
4. Configuration hardening
5. Tests de pénétration suggérés
6. Plan de remédiation priorisé
7. Checklist sécurité pour le futur

QUALITÉ : Audit enterprise-grade, CVSS scoring
```

## 📋 Template Maître : Modèle universel

```
[PERSONA EXPERT]
Tu es un [RÔLE] hautement qualifié avec [ANNÉES] d'expérience.

[CONTEXTE PROJET]
Contexte : [DESCRIPTION DÉTAILLÉE DU PROJET]

[TA TÂCHE PRINCIPALE]
TA TÂCHE : [ACTION PRINCIPALE] [OBJET] [SPÉCIFICATIONS]

[SPÉCIFICATIONS DÉTAILLÉES]
Fonctionnalités attendues :
- [SPEC 1]
- [SPEC 2]
- [SPEC 3]

[CONTRAINTES TECHNIQUES]
Contraintes :
- [TECH 1] : [DÉTAILS]
- [TECH 2] : [DÉTAILS]
- [BUSINESS 1] : [DÉTAILS]

[FORMAT DE LIVRAISON]
Format de sortie structuré :
1. [LIVRABLE 1] : [DESCRIPTION]
2. [LIVRABLE 2] : [DESCRIPTION]
3. [LIVRABLE 3] : [DESCRIPTION]
4. [LIVRABLE 4] : [DESCRIPTION]

[CRITÈRES D'EXCELLENCE]
Critères de qualité :
- [QUALITÉ 1]
- [QUALITÉ 2]
- [QUALITÉ 3]
- [QUALITÉ 4]

[CONTRAINTES TEMPORELLES]
Temps estimé : [DURÉE]
Deadline : [DATE]

LIVRABLE FINAL : [TYPE DE SOLUTION] production-ready, [ATTRIBUTS QUALITÉ]
```

## 🎯 Prompt à trous : Modèles personnalisables

### 🎯 Template CRÉATION
```
Tu es un [RÔLE] expérimenté en [TECHNOLOGIE].

TA TÂCHE : Créer [TYPE_COMPOSANT] qui [FONCTIONNALITÉ]

SPÉCIFICATIONS :
- [Fonctionnalité 1]
- [Fonctionnalité 2]
- [Fonctionnalité 3]

CONTRAINTES :
- [Version/Standard 1]
- [Performance 1]
- [Sécurité 1]

FORMAT :
1. Code [LANGAGE] complet
2. Exemples d'utilisation
3. Tests [FRAMEWORK_TEST]
4. Documentation [FORMAT]

QUALITÉ : [NIVEAU] production-ready
```

### 🔧 Template RÉPARATION
```
Tu es un expert [DOMAINE] en debugging.

TA TÂCHE : Analyser et corriger [PROBLÈME]

CODE :
[CODE_PROBLÉMATIQUE]

PROBLÈMES :
- [Problème 1]
- [Problème 2]

FORMAT :
1. Diagnostic [DÉTAILLÉ/STRUCTURÉ]
2. Code corrigé avec [EXPLICATIONS/VALIDATION]
3. Tests de [NON-RÉGRESSION/VALIDATION]
4. [PRÉVENTION/CONSULTATION]

QUALITÉ : Solution [ROBUSTE/PERFORMANTE]
```

### 📚 Template DOCUMENTATION
```
Tu es un technical writer [SPÉCIALISÉ] en [DOMAINE].

TA TÂCHE : Documenter [ÉLÉMENT] de manière [COMPLÈTE/CLAIRE]

ÉLÉMENT :
[CODE/API/PROCESS]

FORMAT :
1. Vue d'ensemble [FONCTIONNELLE/TECHNIQUE]
2. Guide [UTILISATEUR/DÉVELOPPEUR]
3. [EXEMPLES/RÉFÉRENCES]
4. [FAQ/TROUBLESHOOTING]

QUALITÉ : Documentation [COMPLETE/À_JOUR]
```

## 📈 Matrice d'utilisation des modèles

| Modèle | Code | Debug | Docs | Tests | Refactor | Architecture | Audit |
|--------|------|-------|------|-------|-----------|--------------|-------|
| CRÉATION | ✅✅✅ | ❌ | ✅ | ✅ | ✅ | ✅ | ❌ |
| RÉPARATION | ❌ | ✅✅✅ | ❌ | ✅ | ✅ | ❌ | ✅ |
| DOCUMENTATION | ✅ | ✅ | ✅✅✅ | ✅ | ✅ | ✅ | ✅ |
| TEST | ✅ | ✅ | ✅ | ✅✅✅ | ✅ | ✅ | ✅ |
| RÉFACTORING | ❌ | ✅ | ✅ | ✅ | ✅✅✅ | ✅ | ✅ |
| DESIGN | ✅ | ✅ | ✅ | ✅ | ✅ | ✅✅✅ | ✅ |
| ANALYSE | ❌ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅✅✅ |

## 🎯 Points clés à retenir

1. **Un modèle par objectif** : Choisissez le bon template selon votre besoin
2. **Personnalisation essentielle** : Adaptez les templates à votre contexte
3. **Détails = qualité** : Plus vous êtes spécifique, meilleur le résultat
4. **Itération recommandée** : Testez et améliorez vos templates
5. **Documentation des succès** : Gardez trace des templates qui marchent

## 🚀 Prochain chapitre

Maintenant que vous maîtrisez les modèles universels, apprenons à utiliser le contexte, les rôles et les formats de sortie pour maximiser l'efficacité.

---

## 📋 Checklist qualité

- ✅ 7 modèles universels détaillés
- ✅ Template maître fourni
- ✅ Exemples concrets pour chaque modèle
- ✅ Templates à trous personnalisables
- ✅ Matrice d'utilisation incluse

## 🎯 Test de compréhension

**Question :** Quel modèle utiliseriez-vous pour améliorer les performances d'une API existante ?

**Réponse attendue :** Modèle RÉFACTORING - car il faut analyser le code existant, identifier les problèmes de performance, proposer des améliorations, et fournir des tests de non-régression.

---

**Temps de lecture estimé : 30 minutes**
**Niveau : Expert**
**Prérequis : Chapitres 1, 2, 3**
