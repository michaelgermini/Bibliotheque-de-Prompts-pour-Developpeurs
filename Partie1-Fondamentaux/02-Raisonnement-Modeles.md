# 📖 Partie I - Chapitre 2 : Comprendre comment raisonne un modèle

## 🎯 Objectif du chapitre

Maîtriser les différents modes de raisonnement des modèles d'IA et apprendre à choisir le bon type de prompt selon la tâche.

## 🧠 Les 3 modes de raisonnement

### 1. 📋 Mode Instruction
**Quand l'utiliser :** Génération de code, tâches techniques précises, documentation

**Caractéristiques :**
- Réponses directes et concises
- Moins de "bla-bla"
- Format structuré
- Précision technique

**Exemple de prompt :**
```
Tu es un développeur React senior.

CRÉE un composant Modal qui :
- Accepte des props : isOpen, onClose, title, children
- Utilise useState et useEffect
- Respecte les patterns React 18
- Inclut les tests unitaires

FORMAT : Code TypeScript complet avec commentaires JSDoc
```

### 2. 💬 Mode Conversation
**Quand l'utiliser :** Explications, brainstorming, aide au debugging

**Caractéristiques :**
- Réponses détaillées et explicatives
- Approche pédagogique
- Questions de clarification
- Conseils contextuels

**Exemple de prompt :**
```
Je suis développeur junior et j'ai du mal à comprendre les closures en JavaScript.
Peux-tu m'expliquer avec des exemples concrets ?
Montre-moi d'abord un exemple simple, puis un cas d'usage réel,
et enfin comment éviter les problèmes courants.
```

### 3. 🔧 Mode Code
**Quand l'utiliser :** Complétion de code, refactoring, patterns complexes

**Caractéristiques :**
- Focus sur la syntaxe et les conventions
- Compréhension du contexte du projet
- Suggestions de meilleures pratiques
- Respect des standards

**Exemple de prompt :**
```
Analysez ce code PHP et proposez des améliorations :

```php
class User {
    public $name;
    public function getName() {
        return $this->name;
    }
}
```

Améliorations demandées :
- Encapsulation
- Type hints
- Validation
- Documentation
```

## 🔄 Chain of Thought (CoT) : La pensée étape par étape

### Technique pour les problèmes complexes

```
[Problème complexe] → [Décomposer] → [Résoudre étape] → [Vérifier] → [Solution]
```

**Exemple de prompt CoT :**
```
Résous ce problème étape par étape :

TÂCHE : Optimiser une requête SQL complexe
CONTEXTE : Base de données e-commerce avec 1M de produits

REQUÊTE ACTUELLE :
```sql
SELECT * FROM products WHERE category = 'electronics' LIMIT 100
```

PROBLÈME : La requête est lente sur de gros volumes

RÉFLÉCHIS ÉTAPE PAR ÉTAPE :
1. Analyser la structure de la table
2. Identifier les index manquants
3. Proposer la requête optimisée
4. Expliquer les améliorations de performance

RÉSULTAT : Requête optimisée + explication des gains
```

## 🎭 Personas et rôles : L'importance du contexte

### Différents rôles pour différents résultats

#### 👨‍💻 Développeur Senior
```
Tu es un développeur senior avec 15 ans d'expérience en [TECHNOLOGIE].
Tu as travaillé sur des projets d'envergure pour [TYPE D'ENTREPRISE].
Tu maîtrises les [FRAMEWORKS] et les [PATTERNS].
```

#### 🧪 Testeur QA
```
Tu es un QA engineer expérimenté spécialisé dans les tests automatisés.
Tu connais les méthodologies TDD, BDD et les outils Selenium, Cypress.
Tu es obsédé par la qualité et la couverture de code.
```

#### 🏗️ Architecte Logiciel
```
Tu es un software architect certifié AWS/Azure.
Tu conçois des systèmes scalables, sécurisés et maintenables.
Tu maîtrises les patterns d'architecture (microservices, event-driven, etc.)
```

## 📊 Matrice de choix : Quel mode pour quelle tâche ?

| Tâche | Mode Instruction | Mode Conversation | Mode Code |
|-------|-----------------|------------------|-----------|
| Générer du code | ✅✅✅ | ❌ | ✅✅ |
| Expliquer un concept | ❌ | ✅✅✅ | ❌ |
| Debug un bug | ✅ | ✅✅ | ✅✅✅ |
| Refactorer du code | ✅✅ | ✅ | ✅✅✅ |
| Documenter une API | ✅✅✅ | ✅ | ❌ |
| Brainstorming | ❌ | ✅✅✅ | ❌ |
| Tests unitaires | ✅✅ | ✅ | ✅✅✅ |
| Architecture | ✅ | ✅✅✅ | ✅ |

## 🚀 Exemples avancés de prompts

### 🎯 Prompt multi-étapes
```
ÉTAPE 1 - ANALYSE
Examine ce code React et identifie les problèmes :
[CODE]

ÉTAPE 2 - DIAGNOSTIC
Pour chaque problème, explique :
- Le problème spécifique
- L'impact sur l'application
- La sévérité (critique/majeure/mineure)

ÉTAPE 3 - SOLUTIONS
Propose des corrections avec :
- Code corrigé
- Explication du fix
- Tests pour valider

ÉTAPE 4 - PRÉVENTION
Suggère comment éviter ces problèmes à l'avenir
```

### 🔄 Prompt itératif
```
VERSION 1 : Crée une fonction de validation d'email en PHP
[PREMIER RÉSULTAT]

VERSION 2 : Améliore la version 1 avec :
- Support des emails internationaux
- Validation DNS
- Performance optimisée

VERSION 3 : Ajoute des tests unitaires complets
```

## 📝 Template : Prompt universel

```
[PERSONA]
Tu es [RÔLE] avec [EXPÉRIENCE] d'expérience.

[CONTEXTE]
Contexte du projet : [DESCRIPTION]

[TA TÂCHE]
Ta mission : [OBJECTIF CLAIR]

[CONTRAINTES]
Contraintes techniques :
- [CONTRAINTE 1]
- [CONTRAINTE 2]
- [CONTRAINTE 3]

[FORMAT]
Format de sortie attendu :
1. [SECTION 1]
2. [SECTION 2]
3. [SECTION 3]

[QUALITÉ]
Critères de qualité :
- [CRITÈRE 1]
- [CRITÈRE 2]
- [CRITÈRE 3]

[DÉLAI]
Temps estimé : [DURÉE]
```

## 🎯 Prompt à trous : Raisonnement avancé

```
MODE : [INSTRUCTION/CONVERSATION/CODE]

PERSONA : Tu es un [RÔLE EXPÉRIMENTÉ] spécialisé en [DOMAINE].

PROBLÈME : [DESCRIPTION DÉTAILLÉE DU PROBLÈME]

APPROCHE :
1. [ÉTAPE 1 : ANALYSE]
2. [ÉTAPE 2 : DIAGNOSTIC]
3. [ÉTAPE 3 : SOLUTION]
4. [ÉTAPE 4 : VALIDATION]

CONTRAINTES :
- Respecter [STANDARD/CONVENTION]
- Optimiser pour [PERFORMANCE/SCALABILITÉ/SÉCURITÉ]
- Compatible avec [VERSION/FRAMEWORK]

SORTIE :
- Code/explication structuré
- Tests de validation
- Documentation des choix

QUALITÉ : Solution production-ready, documentée, testée
```

## 📈 Mesurer l'efficacité de vos prompts

### Métriques quantitatives
```
Taux de succès = (Prompts réussis / Total prompts) × 100

Temps de développement :
- Sans IA : X minutes
- Avec IA optimisée : Y minutes
- Gain : ((X-Y)/X) × 100%
```

### Métriques qualitatives
```
- Clarté du code généré
- Respect des conventions
- Couverture fonctionnelle
- Documentation incluse
- Tests fournis
```

## 🎯 Points clés à retenir

1. **Choisissez le bon mode** selon la tâche
2. **Utilisez les personas** pour contextualiser
3. **Structurez vos prompts** avec des étapes claires
4. **Itérez et améliorez** vos prompts
5. **Mesurez les résultats** pour progresser

## 🚀 Prochain chapitre

Maintenant que vous comprenez comment les modèles raisonnent, apprenons à structurer des prompts vraiment efficaces.

---

## 📋 Checklist qualité

- ✅ 3 modes de raisonnement expliqués
- ✅ Exemples concrets pour chaque mode
- ✅ Template de prompt universel
- ✅ Matrice de choix incluse
- ✅ Métriques de succès définies

## 🎯 Test de compréhension

**Question :** Quel mode utiliseriez-vous pour expliquer un concept complexe à un développeur junior ?

**Réponse attendue :** Mode Conversation - car il faut des explications détaillées, pédagogiques, avec des exemples progressifs et la possibilité de poser des questions.

---

**Temps de lecture estimé : 20 minutes**
**Niveau : Intermédiaire**
**Prérequis : Chapitre 1**
