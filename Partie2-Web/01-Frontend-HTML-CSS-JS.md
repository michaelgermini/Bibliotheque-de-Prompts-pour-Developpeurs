# 📖 Partie II - Chapitre 1 : Front-end HTML / CSS / JavaScript

## 🎯 Objectif du chapitre

Maîtriser les prompts pour créer du code frontend moderne, performant et accessible avec HTML5, CSS3 et JavaScript ES6+.

## 🏗️ Structure HTML moderne

### 1. 📄 HTML5 sémantique et accessible

```
Tu es un développeur frontend senior expert en HTML5 et accessibilité.

TA TÂCHE : Créer la structure HTML5 d'une landing page e-commerce moderne

SPÉCIFICATIONS :
- Header avec navigation responsive
- Hero section avec CTA
- Sections produits avec grid layout
- Footer avec liens et infos légales
- Formulaire de contact accessible
- Balises sémantiques (header, nav, main, section, article, aside, footer)

CONTRAINTES :
- HTML5 sémantique strict
- ARIA labels et rôles appropriés
- SEO optimisé (meta tags, structured data)
- Performance (lazy loading images)
- Mobile-first responsive

FORMAT DE SORTIE :
1. Code HTML5 complet et validé
2. Schema.org structured data
3. Meta tags SEO complets
4. Tests d'accessibilité (screen reader, keyboard navigation)
5. Checklist WCAG 2.1 compliance

QUALITÉ : HTML production-ready, accessible, SEO-friendly
```

**Exemple de résultat attendu :**
```html
<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta name="description" content="Découvrez notre collection de produits exclusifs">
    <title>MonShop - Produits Premium</title>

    <!-- SEO et Performance -->
    <meta name="robots" content="index, follow">
    <link rel="canonical" href="https://monshop.com/">
    <meta property="og:title" content="MonShop - Produits Premium">
    <meta property="og:description" content="Découvrez notre collection">

    <!-- Structured Data -->
    <script type="application/ld+json">
    {
        "@context": "https://schema.org",
        "@type": "Organization",
        "name": "MonShop",
        "url": "https://monshop.com"
    }
    </script>
</head>
<body>
    <!-- Skip link for accessibility -->
    <a href="#main" class="skip-link">Aller au contenu principal</a>

    <header role="banner">
        <nav role="navigation" aria-label="Navigation principale">
            <!-- Navigation responsive -->
        </nav>
    </header>

    <main id="main" role="main">
        <section aria-labelledby="hero-title">
            <h1 id="hero-title">Bienvenue sur MonShop</h1>
            <!-- Hero content -->
        </section>

        <section aria-labelledby="products-title">
            <h2 id="products-title">Nos Produits</h2>
            <!-- Products grid -->
        </section>
    </main>

    <footer role="contentinfo">
        <!-- Footer content -->
    </footer>
</body>
</html>
```

### 2. 🎨 CSS3 moderne et responsive

```
Tu es un CSS architect expert en design systems et responsive design.

TA TÂCHE : Créer un système de styles CSS3 complet pour une application web moderne

SPÉCIFICATIONS :
- Design system avec variables CSS
- Grid et Flexbox layouts
- Animations et transitions smooth
- Dark/Light theme support
- Mobile-first responsive design
- CSS custom properties
- Modern features (container queries, logical properties)

CONTRAINTES :
- CSS3 vanilla (pas de framework)
- Performance optimisée (critical CSS)
- Accessibilité (focus states, color contrast)
- Cross-browser compatibility (90%+ coverage)
- Bundle size minimisé

FORMAT DE SORTIE :
1. Architecture CSS avec méthodologie (BEM/ITCSS)
2. Variables et design tokens
3. Layouts responsive (mobile-first)
4. Composants UI réutilisables
5. Animations et micro-interactions
6. Theme system (dark/light mode)
7. Performance optimizations

QUALITÉ : CSS scalable, maintainable, performant
```

**Template CSS Design System :**
```css
/* Design Tokens */
:root {
  /* Colors */
  --color-primary: #2563eb;
  --color-primary-dark: #1d4ed8;
  --color-secondary: #64748b;
  --color-success: #059669;
  --color-warning: #d97706;
  --color-error: #dc2626;

  /* Typography */
  --font-family: 'Inter', system-ui, sans-serif;
  --font-size-xs: 0.75rem;
  --font-size-sm: 0.875rem;
  --font-size-base: 1rem;
  --font-size-lg: 1.125rem;
  --font-size-xl: 1.25rem;

  /* Spacing */
  --space-1: 0.25rem;
  --space-2: 0.5rem;
  --space-3: 0.75rem;
  --space-4: 1rem;
  --space-5: 1.25rem;
  --space-6: 1.5rem;

  /* Breakpoints */
  --bp-sm: 640px;
  --bp-md: 768px;
  --bp-lg: 1024px;
  --bp-xl: 1280px;
}

/* Base Styles */
*, *::before, *::after {
  box-sizing: border-box;
}

html {
  font-size: 16px;
  scroll-behavior: smooth;
}

body {
  font-family: var(--font-family);
  line-height: 1.6;
  color: var(--color-text);
  background-color: var(--color-background);
  margin: 0;
  padding: 0;
}

/* Layout Components */
.container {
  width: 100%;
  max-width: 1280px;
  margin: 0 auto;
  padding: 0 var(--space-4);
}

.grid {
  display: grid;
  gap: var(--space-4);
}

.grid-cols-1 { grid-template-columns: 1fr; }
.grid-cols-2 { grid-template-columns: repeat(2, 1fr); }
.grid-cols-3 { grid-template-columns: repeat(3, 1fr); }

@media (min-width: 768px) {
  .grid-cols-2 { grid-template-columns: repeat(2, 1fr); }
  .grid-cols-3 { grid-template-columns: repeat(3, 1fr); }
}

/* Button Component */
.btn {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  gap: var(--space-2);
  padding: var(--space-3) var(--space-6);
  border: 1px solid transparent;
  border-radius: 0.5rem;
  font-size: var(--font-size-sm);
  font-weight: 500;
  text-decoration: none;
  cursor: pointer;
  transition: all 0.2s ease;
}

.btn-primary {
  background-color: var(--color-primary);
  color: white;
  border-color: var(--color-primary);
}

.btn-primary:hover {
  background-color: var(--color-primary-dark);
  transform: translateY(-1px);
  box-shadow: 0 4px 12px rgba(37, 99, 235, 0.3);
}
```

## 🚀 JavaScript ES6+ moderne

### 1. 🏗️ Architecture JavaScript modulaire

```
Tu es un développeur JavaScript senior expert en ES6+ et design patterns.

TA TÂCHE : Créer une architecture JavaScript modulaire pour une SPA moderne

SPÉCIFICATIONS :
- Modules ES6 avec import/export
- Classes et héritage modernes
- Async/await pour les API calls
- Event-driven architecture
- State management simple
- Error handling robuste
- Performance optimizations

CONTRAINTES :
- ES6+ vanilla (pas de framework)
- Browser compatibility ES2018+
- Bundle size optimisé
- Memory leaks prevention
- Security best practices

FORMAT DE SORTIE :
1. Structure de modules (core, components, utils, services)
2. Classes principales avec TypeScript-like JSDoc
3. Gestion d'état immutable
4. API client avec retry logic
5. Event system personnalisé
6. Performance monitoring
7. Bundle configuration (rollup/webpack)

QUALITÉ : Code scalable, maintainable, performant
```

**Exemple d'architecture :**
```javascript
// Core Module - Event System
class EventEmitter {
  constructor() {
    this.events = new Map();
  }

  on(event, callback) {
    if (!this.events.has(event)) {
      this.events.set(event, new Set());
    }
    this.events.get(event).add(callback);
    return this;
  }

  emit(event, ...args) {
    const callbacks = this.events.get(event);
    if (callbacks) {
      callbacks.forEach(callback => callback(...args));
    }
    return this;
  }

  off(event, callback) {
    const callbacks = this.events.get(event);
    if (callbacks) {
      callbacks.delete(callback);
    }
    return this;
  }
}

// State Management
class Store {
  constructor(initialState = {}) {
    this.state = { ...initialState };
    this.listeners = new Set();
    this.eventEmitter = new EventEmitter();
  }

  getState() {
    return { ...this.state };
  }

  setState(newState) {
    const prevState = { ...this.state };
    this.state = { ...this.state, ...newState };

    if (this.hasChanged(prevState, this.state)) {
      this.notifyListeners();
      this.eventEmitter.emit('stateChanged', this.state, prevState);
    }
  }

  hasChanged(prevState, newState) {
    return JSON.stringify(prevState) !== JSON.stringify(newState);
  }

  subscribe(listener) {
    this.listeners.add(listener);
    return () => this.listeners.delete(listener);
  }

  notifyListeners() {
    this.listeners.forEach(listener => listener(this.state));
  }
}

// API Client
class ApiClient {
  constructor(baseURL, options = {}) {
    this.baseURL = baseURL;
    this.defaultHeaders = {
      'Content-Type': 'application/json',
      ...options.headers
    };
    this.retryCount = options.retryCount || 3;
    this.eventEmitter = new EventEmitter();
  }

  async request(endpoint, options = {}) {
    const url = `${this.baseURL}${endpoint}`;
    const config = {
      headers: { ...this.defaultHeaders, ...options.headers },
      method: options.method || 'GET',
      body: options.body ? JSON.stringify(options.body) : undefined
    };

    let lastError;

    for (let attempt = 1; attempt <= this.retryCount; attempt++) {
      try {
        this.eventEmitter.emit('requestStart', { url, attempt });

        const response = await fetch(url, config);

        if (!response.ok) {
          throw new Error(`HTTP ${response.status}: ${response.statusText}`);
        }

        const data = await response.json();

        this.eventEmitter.emit('requestSuccess', { url, attempt, data });
        return data;

      } catch (error) {
        lastError = error;

        if (attempt === this.retryCount) {
          this.eventEmitter.emit('requestError', { url, attempt, error });
          throw error;
        }

        // Exponential backoff
        await this.delay(Math.pow(2, attempt) * 1000);
      }
    }

    throw lastError;
  }

  async delay(ms) {
    return new Promise(resolve => setTimeout(resolve, ms));
  }
}
```

### 2. 🎯 Prompts pour cas d'usage spécifiques

#### Responsive Design
```
Tu es un expert CSS responsive et mobile-first design.

TA TÂCHE : Implémenter un layout responsive complexe avec CSS Grid et Flexbox

SPÉCIFICATIONS :
- Header responsive avec hamburger menu
- Hero section full-width avec background
- Grid de produits adaptatif (1→2→3→4 colonnes)
- Sidebar qui se transforme en modal mobile
- Footer sticky avec layout complexe

CONTRAINTES :
- Mobile-first approach
- Breakpoints : 320px, 768px, 1024px, 1440px
- Performance : Critical CSS, non-blocking renders
- Accessibilité : Focus management, keyboard navigation
- SEO : Above the fold optimization

FORMAT DE SORTIE :
1. CSS responsive complet
2. Media queries optimisées
3. Layout mobile-first
4. Performance considerations
5. Accessibility features
6. Browser testing checklist

QUALITÉ : Layout pixel-perfect sur tous devices
```

#### Performance Optimization
```
Tu es un expert performance web et Core Web Vitals.

TA TÂCHE : Optimiser les performances d'une application React pour Core Web Vitals

SPÉCIFICATIONS :
- LCP < 2.5s (Largest Contentful Paint)
- FID < 100ms (First Input Delay)
- CLS < 0.1 (Cumulative Layout Shift)
- Bundle size < 200KB gzipped
- Runtime performance optimisé

CONTRAINTES :
- React 18 avec concurrent features
- Code splitting et lazy loading
- Image optimization (WebP, responsive)
- Critical CSS inlined
- Service worker pour caching

FORMAT DE SORTIE :
1. Audit performance initial
2. Optimisations LCP (images, fonts, CSS)
3. Optimisations FID (JavaScript, TBT)
4. Optimisations CLS (layout stability)
5. Bundle analysis et code splitting
6. Monitoring et métrics

QUALITÉ : Score 90+ sur tous Core Web Vitals
```

#### Accessibility (a11y)
```
Tu es un expert accessibilité WCAG 2.1 et design inclusif.

TA TÂCHE : Auditer et corriger l'accessibilité d'un composant React

COMPOSANT À AUDITER :
[CODE DU COMPOSANT]

VIOLATIONS IDENTIFIÉES :
- Images sans alt text
- Contraste insuffisant
- Navigation clavier incomplète
- Screen reader non supporté
- Focus management manquant

CONTRAINTES :
- WCAG 2.1 AA compliance
- Screen reader support (NVDA, JAWS, VoiceOver)
- Keyboard-only navigation
- High contrast mode support
- ARIA patterns appropriés

FORMAT DE SORTIE :
1. Audit a11y détaillé avec axe-core
2. Composant corrigé avec ARIA
3. Tests keyboard navigation
4. Screen reader testing
5. High contrast styles
6. WCAG compliance report

QUALITÉ : Accessibilité 100% conforme WCAG 2.1 AA
```

## 📝 Templates de prompts HTML/CSS/JS

### 🎨 Template CSS Component
```
Tu es un CSS architect expert en [FRAMEWORK CSS].

TA TÂCHE : Créer le composant [NOM] selon [DESIGN SYSTEM]

SPÉCIFICATIONS :
- [FONCTIONNALITÉ 1]
- [FONCTIONNALITÉ 2]
- [FONCTIONNALITÉ 3]

CONTRAINTES :
- [FRAMEWORK] v[VERSION]
- Responsive [BREAKPOINTS]
- Performance [MÉTRIQUES]
- Accessibilité [WCAG LEVEL]

VARIANTES :
- [VARIANTE 1] : [DESCRIPTION]
- [VARIANTE 2] : [DESCRIPTION]

FORMAT :
1. CSS complet du composant
2. Variables et customization
3. Responsive breakpoints
4. Animations et states
5. Usage examples
6. Accessibility notes

QUALITÉ : Composant reusable, performant, accessible
```

### 🚀 Template JavaScript Module
```
Tu es un développeur JavaScript senior expert en [DOMAINE].

TA TÂCHE : Implémenter [FONCTIONNALITÉ] en JavaScript vanilla ES6+

SPÉCIFICATIONS :
- [FONCTION 1] : [DÉTAILS]
- [FONCTION 2] : [DÉTAILS]
- [FONCTION 3] : [DÉTAILS]

CONTRAINTES :
- ES6+ moderne
- No dependencies externes
- Performance optimisée
- Error handling robuste
- Browser compatibility [SUPPORT]

FORMAT :
1. Code ES6+ modulaire
2. JSDoc documentation
3. Usage examples
4. Unit tests (Jest)
5. Performance benchmarks
6. Browser compatibility

QUALITÉ : Code production-ready, testé, documenté
```

### 📱 Template Responsive Layout
```
Tu es un expert responsive design et CSS Grid.

TA TÂCHE : Créer un layout [TYPE] responsive avec [CONTRAINTES]

SPÉCIFICATIONS :
- Mobile-first approach
- Breakpoints : [LISTE]
- Layout : [GRID/FLEXBOX/CONTAINER QUERIES]
- Components : [LISTE]

CONTRAINTES :
- Performance : [MÉTRIQUES]
- Accessibility : [WCAG LEVEL]
- Browser support : [COVERAGE]
- Bundle size : [LIMIT]

FORMAT :
1. CSS layout system
2. Responsive utilities
3. Component examples
4. Performance optimizations
5. Testing checklist
6. Documentation

QUALITÉ : Layout pixel-perfect, performant, accessible
```

## 🎯 Points clés à retenir

1. **HTML5 sémantique** = SEO + Accessibilité
2. **CSS moderne** = Design system + Performance
3. **JavaScript ES6+** = Modularité + Performance
4. **Mobile-first** = UX optimisée
5. **Accessibilité** = Inclusivité garantie
6. **Performance** = Core Web Vitals respectés

## 🚀 Prochain chapitre

Maintenant que vous maîtrisez les bases HTML/CSS/JS, découvrons les prompts pour les frameworks modernes : React, Vue, Angular.

---

## 📋 Checklist qualité

- ✅ HTML5 sémantique et SEO
- ✅ CSS3 moderne et responsive
- ✅ JavaScript ES6+ modulaire
- ✅ Performance et accessibilité
- ✅ Templates pour tous les cas d'usage
- ✅ Exemples de code complets

## 🎯 Test de compréhension

**Question :** Pourquoi le mobile-first est-il crucial en 2024 ?

**Réponse attendue :** 60%+ du trafic web est mobile. Le mobile-first assure une UX optimisée sur mobile, améliore les Core Web Vitals, favorise le SEO, et respecte les standards modernes de performance et accessibilité.

---

**Temps de lecture estimé : 45 minutes**
**Niveau : Intermédiaire à Expert**
**Prérequis : Partie I**
