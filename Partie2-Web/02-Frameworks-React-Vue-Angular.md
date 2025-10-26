# 📖 Partie II - Chapitre 2 : Frameworks React, Vue, Angular

## 🎯 Objectif du chapitre

Maîtriser les prompts pour développer avec les frameworks frontend modernes : React, Vue.js et Angular, avec les meilleures pratiques et patterns avancés.

## ⚛️ React : Components, Hooks, State Management

### 1. 🏗️ Architecture React moderne

```
Tu es un développeur React senior expert en React 18 et TypeScript.

TA TÂCHE : Créer une architecture React complète pour une application [TYPE]

SPÉCIFICATIONS :
- React 18 avec concurrent features
- TypeScript strict mode
- Architecture scalable (atomic design)
- State management moderne
- Performance optimisée
- Testing strategy complète

CONTRAINTES :
- Bundle size < 500KB gzipped
- Core Web Vitals optimisés
- TypeScript strict
- Testing coverage > 90%
- Accessibility WCAG 2.1 AA

FORMAT DE SORTIE :
1. 📁 Structure projet optimisée
2. 🔧 Configuration TypeScript/Webpack
3. 🏗️ Architecture components (atomic design)
4. 📊 State management (Redux Toolkit/Zustand)
5. 🚀 Performance optimizations
6. 🧪 Testing strategy (Jest, RTL, Cypress)
7. 📚 Documentation développeur

QUALITÉ : Architecture enterprise-ready, scalable, maintainable
```

**Structure projet React recommandée :**
```
📁 src/
├── 📁 components/           # Atomic design
│   ├── 📁 atoms/           # Boutons, inputs, icons
│   ├── 📁 molecules/       # Formulaires, cards
│   ├── 📁 organisms/       # Sections, layouts
│   ├── 📁 templates/       # Page layouts
│   └── 📁 pages/          # Route components
├── 📁 hooks/              # Custom hooks
├── 📁 services/           # API, utilities
├── 📁 store/              # State management
├── 📁 styles/             # Global styles, design system
├── 📁 types/              # TypeScript types
├── 📁 utils/              # Helpers, constants
├── 📁 __tests__/          # Tests unitaires
└── 📄 index.tsx          # App entry point
```

### 2. 🎣 Hooks avancés et patterns

```
Tu es un expert React Hooks et performance optimization.

TA TÂCHE : Implémenter des hooks personnalisés pour [FONCTIONNALITÉ]

HOOKS À CRÉER :
1. useAsyncData - Gestion asynchrone avec cache
2. useLocalStorage - Persistance locale typée
3. useDebounce - Debouncing optimisé
4. useIntersectionObserver - Lazy loading
5. useWindowSize - Responsive utilities

CONTRAINTES :
- TypeScript générique
- Performance optimisée (no unnecessary re-renders)
- Memory leaks prevention
- Error boundaries incluses
- Testing utilities

FORMAT DE SORTIE :
1. Code des hooks personnalisés
2. JSDoc documentation complète
3. Usage examples avec TypeScript
4. Tests unitaires pour chaque hook
5. Performance benchmarks
6. Common pitfalls et solutions

QUALITÉ : Hooks reusable, performant, well-tested
```

**Exemple de hook avancé :**
```typescript
/**
 * Hook personnalisé pour la gestion asynchrone avec cache
 * @template T - Type des données
 */
function useAsyncData<T>(
  asyncFunction: () => Promise<T>,
  dependencies: React.DependencyList = []
): {
  data: T | null;
  loading: boolean;
  error: Error | null;
  refetch: () => Promise<void>;
} {
  const [state, setState] = useState<{
    data: T | null;
    loading: boolean;
    error: Error | null;
  }>({
    data: null,
    loading: true,
    error: null
  });

  const [cache, setCache] = useState<Map<string, T>>(new Map());

  const cacheKey = useMemo(
    () => JSON.stringify(dependencies),
    dependencies
  );

  const fetchData = useCallback(async () => {
    // Check cache first
    if (cache.has(cacheKey)) {
      setState({
        data: cache.get(cacheKey)!,
        loading: false,
        error: null
      });
      return;
    }

    setState(prev => ({ ...prev, loading: true, error: null }));

    try {
      const result = await asyncFunction();

      // Update cache
      setCache(prev => new Map(prev.set(cacheKey, result)));

      setState({
        data: result,
        loading: false,
        error: null
      });
    } catch (error) {
      setState({
        data: null,
        loading: false,
        error: error as Error
      });
    }
  }, [asyncFunction, cacheKey]);

  useEffect(() => {
    fetchData();
  }, dependencies);

  return {
    ...state,
    refetch: fetchData
  };
}

// Usage example
const ProductList = ({ categoryId }: { categoryId: string }) => {
  const { data: products, loading, error, refetch } = useAsyncData(
    () => api.getProducts(categoryId),
    [categoryId]
  );

  if (loading) return <LoadingSpinner />;
  if (error) return <ErrorMessage error={error} onRetry={refetch} />;
  if (!products) return <EmptyState />;

  return (
    <ProductGrid products={products} />
  );
};
```

### 3. 📊 State Management moderne

```
Tu es un expert state management React avec Redux Toolkit et Zustand.

TA TÂCHE : Concevoir un système de state management pour [APPLICATION]

CONTRAINTES :
- Redux Toolkit pour state complexe
- Zustand pour state local/simple
- TypeScript strict
- Performance optimisée
- DevTools support
- Persistence optionnelle

FORMAT DE SORTIE :
1. Architecture state (slices, stores)
2. Actions et reducers Redux Toolkit
3. Stores Zustand pour state local
4. Selectors optimisés (reselect)
5. Middleware (thunk, logging)
6. Tests pour state management
7. Performance monitoring

QUALITÉ : State management scalable, performant, type-safe
```

## 🌿 Vue.js : Composition API, Composables

### 1. 🏗️ Architecture Vue 3 moderne

```
Tu es un développeur Vue.js senior expert en Composition API.

TA TÂCHE : Créer une architecture Vue 3 avec Composition API pour [PROJET]

SPÉCIFICATIONS :
- Vue 3 Composition API
- TypeScript support
- Composables réutilisables
- Pinia pour state management
- Vue Router 4 avec lazy loading
- Auto-imports et plugins

CONTRAINTES :
- Bundle size optimisé
- Tree shaking enabled
- Hot reload development
- Production build optimized
- TypeScript strict mode

FORMAT DE SORTIE :
1. Structure projet Vue 3
2. Configuration Vite/TypeScript
3. Composables core (useAuth, useApi)
4. Stores Pinia avec TypeScript
5. Router configuration
6. Auto-imports setup
7. Development workflow

QUALITÉ : Architecture Vue 3 moderne, type-safe, performant
```

**Structure projet Vue 3 :**
```
📁 src/
├── 📁 composables/        # Logic réutilisable
│   ├── useAuth.ts
│   ├── useApi.ts
│   ├── useLocalStorage.ts
│   └── useDebounce.ts
├── 📁 stores/             # Pinia stores
│   ├── auth.ts
│   ├── cart.ts
│   └── ui.ts
├── 📁 views/             # Pages/routes
├── 📁 components/         # Composants UI
│   ├── 📁 ui/            # Base components
│   ├── 📁 forms/         # Form components
│   └── 📁 layout/        # Layout components
├── 📁 router/            # Routes configuration
├── 📁 types/             # TypeScript types
├── 📁 utils/             # Utilities
├── 📁 styles/            # Global styles
└── 📄 main.ts           # App entry
```

### 2. 🎯 Composables avancés

```
Tu es un expert Vue 3 Composition API et performance.

TA TÂCHE : Créer des composables avancés pour [FONCTIONNALITÉS]

COMPOSABLES À IMPLÉMENTER :
1. useAsyncState - State asynchrone avec cache
2. useEventBus - Event bus typé
3. useIntersectionObserver - Lazy loading
4. useMediaQuery - Responsive utilities
5. usePerformance - Performance monitoring

CONTRAINTES :
- Composition API patterns
- TypeScript génériques
- Performance optimisée
- Memory management
- Error handling robuste

FORMAT DE SORTIE :
1. Code des composables
2. TypeScript definitions
3. Usage examples
4. Unit tests (Vitest)
5. Performance analysis
6. Best practices

QUALITÉ : Composables reusable, performant, type-safe
```

**Exemple de composable avancé :**
```typescript
/**
 * Composable pour la gestion d'état asynchrone avec cache
 */
export function useAsyncState<T>(
  asyncFunction: () => Promise<T>,
  options: {
    immediate?: boolean;
    cacheTime?: number;
    retry?: number;
  } = {}
) {
  const {
    immediate = true,
    cacheTime = 5 * 60 * 1000, // 5 minutes
    retry = 3
  } = options;

  const state = ref<{
    data: T | null;
    loading: boolean;
    error: Error | null;
  }>({
    data: null,
    loading: false,
    error: null
  });

  const cache = new Map<string, { data: T; timestamp: number }>();
  let abortController: AbortController | null = null;

  const generateCacheKey = () => {
    // Generate unique cache key based on function
    return asyncFunction.toString();
  };

  const fetchData = async (showLoading = true) => {
    const cacheKey = generateCacheKey();

    // Check cache first
    const cached = cache.get(cacheKey);
    if (cached && Date.now() - cached.timestamp < cacheTime) {
      state.value = {
        data: cached.data,
        loading: false,
        error: null
      };
      return;
    }

    // Cancel previous request
    if (abortController) {
      abortController.abort();
    }

    abortController = new AbortController();

    if (showLoading) {
      state.value.loading = true;
    }
    state.value.error = null;

    let lastError: Error;

    for (let attempt = 1; attempt <= retry; attempt++) {
      try {
        const result = await asyncFunction();

        // Update cache
        cache.set(cacheKey, { data: result, timestamp: Date.now() });

        state.value = {
          data: result,
          loading: false,
          error: null
        };

        return;
      } catch (error) {
        lastError = error as Error;

        if (attempt === retry) {
          state.value = {
            data: null,
            loading: false,
            error: lastError
          };
        } else {
          // Exponential backoff
          await new Promise(resolve =>
            setTimeout(resolve, Math.pow(2, attempt) * 1000)
          );
        }
      }
    }
  };

  const refresh = () => fetchData(true);
  const invalidateCache = () => {
    const cacheKey = generateCacheKey();
    cache.delete(cacheKey);
  };

  if (immediate) {
    fetchData();
  }

  // Cleanup on unmount
  onUnmounted(() => {
    if (abortController) {
      abortController.abort();
    }
  });

  return {
    ...toRefs(state),
    refresh,
    invalidateCache
  };
}
```

## 🅰️ Angular : Components, Services, RxJS

### 1. 🏗️ Architecture Angular moderne

```
Tu es un développeur Angular senior expert en Angular 17 et standalone components.

TA TÂCHE : Créer une architecture Angular moderne pour [APPLICATION]

SPÉCIFICATIONS :
- Angular 17+ avec standalone components
- TypeScript strict mode
- RxJS pour state management
- Angular Signals preview
- SCAM architecture
- Performance optimisée

CONTRAINTES :
- Bundle size < 400KB gzipped
- Core Web Vitals optimisés
- TypeScript strict
- Testing coverage > 90%
- Accessibility WCAG 2.1 AA

FORMAT DE SORTIE :
1. Structure projet Angular 17
2. Standalone components setup
3. Services avec RxJS
4. State management (NgRx/Signals)
5. Routing configuration
6. Testing strategy
7. Build optimizations

QUALITÉ : Architecture Angular moderne, scalable, performant
```

### 2. 🚀 Services et RxJS patterns

```
Tu es un expert Angular et RxJS patterns avancés.

TA TÂCHE : Implémenter des services Angular avec RxJS pour [FONCTIONNALITÉS]

SERVICES À CRÉER :
1. DataService - CRUD avec cache HTTP
2. AuthService - Authentication JWT
3. NotificationService - Toast messages
4. WebSocketService - Real-time updates
5. FileUploadService - Upload progressif

CONTRAINTES :
- RxJS operators avancés
- Error handling robuste
- TypeScript strict
- Performance optimisée
- Testing avec marbles

FORMAT DE SORTIE :
1. Services Angular avec RxJS
2. TypeScript interfaces
3. Error handling strategies
4. Unit tests avec TestBed
5. RxJS marble diagrams
6. Performance considerations

QUALITÉ : Services robustes, performant, well-tested
```

## 📊 Comparaison et choix du framework

### 🎯 Guide de choix : React vs Vue vs Angular

```
Tu es un consultant technique senior expert en frameworks frontend.

TA TÂCHE : Analyser et recommander le framework optimal pour [PROJET]

CONTEXTE PROJET :
- Équipe : [TAILLE] développeurs
- Compétences : [EXISTANTES]
- Complexité : [NIVEAU]
- Performance : [EXIGENCES]
- Maintenance : [DURÉE]

CRITÈRES D'ANALYSE :
- Learning curve et onboarding
- Bundle size et performance
- TypeScript support
- Écosystème et libraries
- Community et support
- Enterprise features
- Mobile development

FORMAT DE SORTIE :
1. Analyse comparative détaillée
2. Score par critère (1-10)
3. Recommandation justifiée
4. Migration strategy si applicable
5. Roadmap d'implémentation
6. Risk assessment

QUALITÉ : Recommandation objective et argumentée
```

**Matrice de comparaison :**

| Critère | React | Vue.js | Angular |
|---------|-------|--------|---------|
| Learning Curve | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ |
| Bundle Size | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ |
| TypeScript | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| Performance | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐ |
| Ecosystem | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| Enterprise | ⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| Mobile | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐⭐ |

## 📝 Templates de prompts Framework

### ⚛️ Template React Component
```
Tu es un développeur React senior expert en [FEATURES].

TA TÂCHE : Créer le composant [NOM] avec [FONCTIONNALITÉS]

SPÉCIFICATIONS :
- React [VERSION] avec [HOOKS/FEATURES]
- TypeScript strict mode
- Performance [OPTIMIZATIONS]
- Testing [STRATEGY]

CONTRAINTES :
- Bundle size impact minimal
- Re-renders optimisés
- Accessibility WCAG [LEVEL]
- Mobile responsive

FORMAT :
1. Code TypeScript du composant
2. Custom hooks utilisés
3. Props interface complète
4. Tests unitaires (Jest/RTL)
5. Storybook stories
6. Performance notes

QUALITÉ : Composant reusable, performant, accessible
```

### 🌿 Template Vue Composition
```
Tu es un expert Vue 3 Composition API.

TA TÂCHE : Implémenter [FONCTIONNALITÉ] avec composables

SPÉCIFICATIONS :
- Vue 3 Composition API
- TypeScript support
- Composables réutilisables
- Performance optimisée

CONTRAINTES :
- Tree shaking enabled
- No side effects
- Memory leaks prevention
- Error handling robuste

FORMAT :
1. Code Vue 3 avec Composition API
2. Composables personnalisés
3. TypeScript interfaces
4. Tests unitaires (Vitest)
5. Usage examples
6. Performance analysis

QUALITÉ : Code Vue 3 moderne, type-safe, performant
```

### 🅰️ Template Angular Service
```
Tu es un développeur Angular senior expert en RxJS.

TA TÂCHE : Créer le service [NOM] pour [FONCTIONNALITÉ]

SPÉCIFICATIONS :
- Angular [VERSION] standalone
- RxJS patterns avancés
- TypeScript strict
- Error handling robuste

CONTRAINTES :
- Injectable optimization
- Change detection strategy
- Memory management
- Testing with marbles

FORMAT :
1. Service Angular avec RxJS
2. TypeScript interfaces
3. Error handling strategies
4. Unit tests (TestBed)
5. RxJS marble diagrams
6. Performance considerations

QUALITÉ : Service robuste, performant, well-tested
```

## 🎯 Points clés à retenir

1. **React** : Flexibilité, écosystème, performance
2. **Vue.js** : Simplicité, DX exceptionnelle, progressive
3. **Angular** : Enterprise, TypeScript natif, full framework
4. **Composition API** : Future du développement frontend
5. **TypeScript** : Qualité et maintenabilité
6. **Performance** : Optimisations critiques pour tous

## 🚀 Prochain chapitre

Maintenant que vous maîtrisez les frameworks, découvrons les prompts pour l'UI/UX, les design systems et l'accessibilité.

---

## 📋 Checklist qualité

- ✅ Architecture React moderne
- ✅ Vue 3 Composition API
- ✅ Angular standalone components
- ✅ State management patterns
- ✅ Performance optimizations
- ✅ Testing strategies

## 🎯 Test de compréhension

**Question :** Pourquoi la Composition API est-elle préférable à l'Options API dans Vue 3 ?

**Réponse attendue :** La Composition API offre une meilleure réutilisabilité du code, un TypeScript support natif, une logique plus claire et flexible, et des performances optimisées. Elle résout les limitations de l'Options API pour les applications complexes.

---

**Temps de lecture estimé : 50 minutes**
**Niveau : Expert**
**Prérequis : Partie I + Chapitre 1**
