# 📖 Partie II - Chapitre 3 : UI/UX Design Systems & Accessibilité

## 🎯 Objectif du chapitre

Maîtriser la création de design systems modernes, l'optimisation UX, et l'implémentation de l'accessibilité selon les standards WCAG 2.1.

## 🎨 Design Systems : Architecture et composants

### 1. 🏗️ Architecture Design System

```
Tu es un design systems architect expert en atomic design et tokens.

TA TÂCHE : Créer un design system complet pour [APPLICATION/BRAND]

SPÉCIFICATIONS :
- Design tokens (colors, typography, spacing)
- Component library (atoms, molecules, organisms)
- Design principles et guidelines
- Documentation Storybook
- Theme system (light/dark)
- Responsive utilities

CONTRAINTES :
- Framework agnostic (CSS-in-JS ou CSS)
- Scalable architecture (atomic design)
- Performance optimisée
- Accessibility WCAG 2.1 AA
- Developer experience (DX)

FORMAT DE SORTIE :
1. 📐 Design tokens et variables
2. 🎨 Color palette et contrast ratios
3. 📝 Typography scale et hierarchy
4. 📏 Spacing et layout systems
5. 🧩 Component library complète
6. 📚 Storybook documentation
7. 🎭 Theme system implementation
8. ♿ Accessibility guidelines

QUALITÉ : Design system scalable, accessible, developer-friendly
```

**Design Tokens Structure :**
```css
/* Design Tokens - Figma to Code */
:root {
  /* Colors - Semantic naming */
  --color-primary-50: #eff6ff;
  --color-primary-100: #dbeafe;
  --color-primary-500: #3b82f6;
  --color-primary-900: #1e3a8a;

  --color-neutral-0: #ffffff;
  --color-neutral-50: #f9fafb;
  --color-neutral-100: #f3f4f6;
  --color-neutral-900: #111827;

  /* Typography */
  --font-family-sans: 'Inter', system-ui, sans-serif;
  --font-family-mono: 'Fira Code', monospace;

  --font-size-xs: 0.75rem;    /* 12px */
  --font-size-sm: 0.875rem;   /* 14px */
  --font-size-base: 1rem;     /* 16px */
  --font-size-lg: 1.125rem;   /* 18px */
  --font-size-xl: 1.25rem;    /* 20px */
  --font-size-2xl: 1.5rem;    /* 24px */
  --font-size-3xl: 1.875rem;  /* 30px */

  --font-weight-light: 300;
  --font-weight-normal: 400;
  --font-weight-medium: 500;
  --font-weight-semibold: 600;
  --font-weight-bold: 700;

  --line-height-tight: 1.25;
  --line-height-normal: 1.5;
  --line-height-relaxed: 1.75;

  /* Spacing - 8px base scale */
  --space-px: 1px;
  --space-0: 0;
  --space-1: 0.25rem;  /* 4px */
  --space-2: 0.5rem;   /* 8px */
  --space-3: 0.75rem;  /* 12px */
  --space-4: 1rem;     /* 16px */
  --space-5: 1.25rem;  /* 20px */
  --space-6: 1.5rem;   /* 24px */
  --space-8: 2rem;     /* 32px */
  --space-10: 2.5rem;  /* 40px */
  --space-12: 3rem;    /* 48px */
  --space-16: 4rem;    /* 64px */
  --space-20: 5rem;    /* 80px */

  /* Shadows */
  --shadow-sm: 0 1px 2px 0 rgba(0, 0, 0, 0.05);
  --shadow-md: 0 4px 6px -1px rgba(0, 0, 0, 0.1);
  --shadow-lg: 0 10px 15px -3px rgba(0, 0, 0, 0.1);
  --shadow-xl: 0 20px 25px -5px rgba(0, 0, 0, 0.1);

  /* Border radius */
  --radius-none: 0;
  --radius-sm: 0.125rem;
  --radius-base: 0.25rem;
  --radius-md: 0.375rem;
  --radius-lg: 0.5rem;
  --radius-xl: 0.75rem;
  --radius-full: 9999px;

  /* Breakpoints */
  --breakpoint-sm: 640px;
  --breakpoint-md: 768px;
  --breakpoint-lg: 1024px;
  --breakpoint-xl: 1280px;
  --breakpoint-2xl: 1536px;

  /* Transitions */
  --transition-fast: 150ms cubic-bezier(0.4, 0, 0.2, 1);
  --transition-base: 300ms cubic-bezier(0.4, 0, 0.2, 1);
  --transition-slow: 500ms cubic-bezier(0.4, 0, 0.2, 1);
}
```

### 2. 🧩 Composants UI : Atomic Design

```
Tu es un UI developer expert en component systems et accessibility.

TA TÂCHE : Créer une bibliothèque de composants UI selon atomic design

COMPOSANTS À IMPLÉMENTER :
1. **Atoms** : Button, Input, Label, Icon, Badge
2. **Molecules** : FormField, SearchBar, Pagination, Modal
3. **Organisms** : Header, Footer, Sidebar, CardGrid
4. **Templates** : DashboardLayout, AuthLayout, ProductLayout

CONTRAINTES :
- Framework : [REACT/VUE/ANGULAR]
- Design tokens based
- Accessibility WCAG 2.1 AA
- Performance optimisée
- TypeScript support
- Testing coverage > 90%

FORMAT DE SORTIE :
1. Component architecture (atomic design)
2. Code de chaque composant
3. Props API documentation
4. Usage examples
5. Tests unitaires et visuels
6. Storybook stories
7. Accessibility audit

QUALITÉ : Composants accessibles, performants, réutilisables
```

**Exemple de composant Button (Atom) :**
```tsx
interface ButtonProps extends React.ButtonHTMLAttributes<HTMLButtonElement> {
  variant?: 'primary' | 'secondary' | 'outline' | 'ghost' | 'danger';
  size?: 'sm' | 'md' | 'lg' | 'xl';
  loading?: boolean;
  leftIcon?: React.ReactNode;
  rightIcon?: React.ReactNode;
  fullWidth?: boolean;
}

const Button = React.forwardRef<HTMLButtonElement, ButtonProps>(
  ({
    className = '',
    variant = 'primary',
    size = 'md',
    loading = false,
    leftIcon,
    rightIcon,
    fullWidth = false,
    disabled,
    children,
    ...props
  }, ref) => {
    const baseClasses = 'inline-flex items-center justify-center font-medium transition-all duration-200 focus:outline-none focus:ring-2 focus:ring-offset-2 disabled:opacity-50 disabled:cursor-not-allowed';

    const variantClasses = {
      primary: 'bg-blue-600 text-white hover:bg-blue-700 focus:ring-blue-500 border border-blue-600',
      secondary: 'bg-gray-600 text-white hover:bg-gray-700 focus:ring-gray-500 border border-gray-600',
      outline: 'bg-transparent text-gray-700 border border-gray-300 hover:bg-gray-50 focus:ring-blue-500',
      ghost: 'bg-transparent text-gray-700 hover:bg-gray-100 focus:ring-gray-500 border border-transparent',
      danger: 'bg-red-600 text-white hover:bg-red-700 focus:ring-red-500 border border-red-600'
    };

    const sizeClasses = {
      sm: 'px-3 py-2 text-sm rounded-md gap-2',
      md: 'px-4 py-2 text-base rounded-lg gap-2',
      lg: 'px-6 py-3 text-lg rounded-lg gap-3',
      xl: 'px-8 py-4 text-xl rounded-lg gap-3'
    };

    const classes = cn(
      baseClasses,
      variantClasses[variant],
      sizeClasses[size],
      fullWidth && 'w-full',
      loading && 'cursor-wait',
      className
    );

    return (
      <button
        ref={ref}
        className={classes}
        disabled={disabled || loading}
        aria-disabled={disabled || loading}
        {...props}
      >
        {loading && (
          <svg className="animate-spin -ml-1 mr-2 h-4 w-4" fill="none" viewBox="0 0 24 24">
            <circle className="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" strokeWidth="4"></circle>
            <path className="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
          </svg>
        )}

        {!loading && leftIcon && leftIcon}
        {children}
        {!loading && rightIcon && rightIcon}
      </button>
    );
  }
);

Button.displayName = 'Button';
```

## ♿ Accessibilité : WCAG 2.1 et Design Inclusif

### 1. 🔍 Audit d'accessibilité automatisé

```
Tu es un accessibility expert certifié WCAG 2.1 et CPWA.

TA TÂCHE : Effectuer un audit d'accessibilité complet sur [COMPOSANT/APPLICATION]

ÉLÉMENTS À AUDITER :
- Structure HTML sémantique
- Navigation clavier
- Screen reader compatibility
- Color contrast ratios
- Focus management
- ARIA implementation
- Form accessibility
- Image alt text

CONTRAINTES :
- WCAG 2.1 Level AA compliance
- Section 508 compatibility
- Screen reader testing (NVDA, JAWS, VoiceOver)
- Keyboard-only navigation
- High contrast mode support

FORMAT DE SORTIE :
1. Audit automatisé avec axe-core
2. Test manuel checklist
3. Violations par sévérité
4. Code corrigé avec ARIA
5. Tests d'accessibilité
6. WCAG compliance report
7. User testing recommendations

QUALITÉ : Accessibilité 100% conforme, inclusive design
```

**Checklist WCAG 2.1 AA :**
```typescript
const accessibilityChecklist = {
  // Perceivable
  images: {
    decorative: 'aria-hidden="true" or empty alt',
    informative: 'alt text describes image content',
    complex: 'alt text + description or figure with caption'
  },
  color: {
    contrast: '4.5:1 for normal text, 3:1 for large text',
    information: 'not conveyed by color alone',
    links: '1.5:1 contrast ratio minimum'
  },
  audio: {
    captions: 'provided for audio content',
    controls: 'play, pause, volume controls available',
    alternatives: 'transcript or description provided'
  },

  // Operable
  keyboard: {
    navigation: 'all interactive elements accessible',
    focus: 'visible focus indicators',
    shortcuts: 'no conflicts with assistive tech'
  },
  timing: {
    adjustable: 'users can extend time limits',
    pause: 'auto-updating content can be paused',
    noSeizure: 'no content flashes more than 3 times'
  },

  // Understandable
  language: {
    page: 'lang attribute on html element',
    content: 'language changes marked',
    abbreviations: 'expanded on first use or glossary'
  },
  forms: {
    labels: 'all inputs have associated labels',
    errors: 'error messages are clear and specific',
    instructions: 'provided when needed'
  },

  // Robust
  compatibility: {
    html: 'valid HTML markup',
    aria: 'proper ARIA implementation',
    assistive: 'works with screen readers and other tools'
  }
};
```

### 2. 🎯 Prompts pour cas d'usage a11y

#### Navigation clavier complète
```
Tu es un expert en navigation clavier et focus management.

TA TÂCHE : Implémenter une navigation clavier complète pour [COMPOSANT]

SPÉCIFICATIONS :
- Tab order logique
- Focus visible et cohérent
- Skip links pour navigation rapide
- Escape key pour fermer modals/menus
- Arrow keys pour navigation dans composants
- Enter/Space pour activation

CONTRAINTES :
- WCAG 2.1 AA compliance
- Screen reader compatibility
- High contrast mode support
- No mouse dependency
- Performance optimisée

FORMAT DE SORTIE :
1. Code avec focus management
2. CSS pour focus indicators
3. Tests keyboard navigation
4. Screen reader testing
5. ARIA implementation
6. High contrast styles

QUALITÉ : Navigation 100% accessible au clavier
```

#### Screen reader optimization
```
Tu es un expert screen reader et technologies d'assistance.

TA TÂCHE : Optimiser [COMPOSANT] pour les technologies d'assistance

SPÉCIFICATIONS :
- ARIA labels et descriptions
- Live regions pour updates dynamiques
- Landmark roles (banner, navigation, main, contentinfo)
- Headings hierarchy (h1-h6)
- Form labels et descriptions
- Status announcements

CONTRAINTES :
- NVDA, JAWS, VoiceOver compatibility
- ARIA best practices
- No redundant information
- Clear and concise labels
- Progressive enhancement

FORMAT DE SORTIE :
1. Code avec ARIA complet
2. Screen reader test scripts
3. ARIA patterns documentation
4. Tests avec assistive tech
5. User testing guidelines
6. Compliance checklist

QUALITÉ : Expérience screen reader optimale
```

## 📱 UX Patterns : Performance et Interaction

### 1. 🚀 Performance UX : Core Web Vitals

```
Tu es un expert Core Web Vitals et performance web.

TA TÂCHE : Optimiser [APPLICATION] pour les Core Web Vitals

MÉTRIQUES CIBLES :
- LCP (Largest Contentful Paint) < 2.5s
- FID (First Input Delay) < 100ms
- CLS (Cumulative Layout Shift) < 0.1
- INP (Interaction to Next Paint) < 200ms
- TTFB (Time to First Byte) < 800ms

CONTRAINTES :
- Bundle size < 200KB gzipped
- Critical CSS inlined
- Images optimized (WebP, responsive)
- JavaScript non-blocking
- Caching strategy

FORMAT DE SORTIE :
1. Audit performance initial
2. Optimisations LCP (fonts, images, CSS)
3. Optimisations FID (JavaScript optimization)
4. Optimisations CLS (layout stability)
5. Bundle analysis et code splitting
6. Monitoring et alerting setup
7. Performance budget

QUALITÉ : Score 90+ sur tous Core Web Vitals
```

### 2. 🎭 Micro-interactions et animations

```
Tu es un expert micro-interactions et motion design.

TA TÂCHE : Implémenter des micro-interactions pour améliorer l'UX

INTERACTIONS À CRÉER :
1. Hover states avec smooth transitions
2. Loading animations (skeleton, spinner)
3. Form validation feedback
4. Success/error notifications
5. Page transitions
6. Scroll-triggered animations

CONTRAINTES :
- Performance optimisée (60fps)
- Accessibility (prefers-reduced-motion)
- Mobile-friendly (touch interactions)
- Consistent design language
- Loading states pour async actions

FORMAT DE SORTIE :
1. CSS animations et transitions
2. JavaScript pour interactions
3. Performance optimizations
4. Accessibility considerations
5. Mobile touch handling
6. Testing et validation

QUALITÉ : Interactions smooth, accessibles, performantes
```

## 📝 Templates de prompts Design System

### 🎨 Template Design Tokens
```
Tu es un design systems engineer expert en tokens.

TA TÂCHE : Créer un système de design tokens pour [BRAND/PROJET]

SPÉCIFICATIONS :
- Color palette accessible
- Typography scale harmonieuse
- Spacing system cohérent
- Shadow et elevation system
- Border radius et shape system
- Animation et transition tokens

CONTRAINTES :
- Framework : [CSS/REACT/VUE]
- Format : [CSS/JS/JSON]
- Accessibility : WCAG 2.1 AA
- Performance : Tree-shaking
- Documentation : Figma integration

FORMAT :
1. Design tokens structurés
2. Usage guidelines
3. Code implementation
4. Figma variables mapping
5. Testing et validation
6. Documentation complète

QUALITÉ : Design tokens scalable, accessible, documented
```

### 🧩 Template Component Library
```
Tu es un UI component engineer expert en [FRAMEWORK].

TA TÂCHE : Développer la bibliothèque de composants [LIBRARY]

COMPOSANTS :
- [ATOMS] : Button, Input, Icon, Badge
- [MOLECULES] : FormField, SearchBar, Modal
- [ORGANISMS] : Header, Footer, Sidebar
- [TEMPLATES] : Layouts, Page structures

CONTRAINTES :
- Atomic design methodology
- TypeScript strict
- Accessibility WCAG 2.1 AA
- Performance optimisée
- Testing coverage > 90%

FORMAT :
1. Architecture component system
2. Code de chaque composant
3. Props API documentation
4. Storybook stories
5. Tests unitaires et visuels
6. Accessibility audit
7. Usage guidelines

QUALITÉ : Component library enterprise-ready
```

### ♿ Template Accessibility
```
Tu es un accessibility expert certifié CPWA.

TA TÂCHE : Implémenter l'accessibilité complète pour [COMPOSANT/APPLICATION]

SPÉCIFICATIONS :
- WCAG 2.1 Level [A/AA/AAA]
- Screen reader optimization
- Keyboard navigation
- Color contrast compliance
- Focus management
- ARIA implementation

CONTRAINTES :
- [FRAMEWORK] compatibility
- Performance impact minimal
- User testing required
- Progressive enhancement
- Internationalization ready

FORMAT :
1. Accessibility audit initial
2. Code avec ARIA implémenté
3. Tests automatisés (axe-core)
4. Tests manuels checklist
5. Screen reader testing
6. WCAG compliance report
7. User testing guidelines

QUALITÉ : Accessibilité 100% conforme et testée
```

## 🎯 Points clés à retenir

1. **Design tokens** = Consistance et maintenabilité
2. **Atomic design** = Composants réutilisables
3. **WCAG 2.1 AA** = Accessibilité standard
4. **Core Web Vitals** = Performance mesurable
5. **Micro-interactions** = UX engagement
6. **Testing** = Qualité garantie

## 🚀 Prochain chapitre

Maintenant que vous maîtrisez l'UI/UX, découvrons les prompts pour le debugging et l'optimisation web.

---

## 📋 Checklist qualité

- ✅ Design system architecture
- ✅ Component library complète
- ✅ WCAG 2.1 compliance
- ✅ Core Web Vitals optimization
- ✅ Micro-interactions UX
- ✅ Templates pour tous les cas

## 🎯 Test de compréhension

**Question :** Pourquoi les design tokens sont-ils essentiels dans un design system ?

**Réponse attendue :** Les design tokens centralisent les valeurs de design (couleurs, typography, spacing) dans des variables réutilisables. Ils garantissent la consistance, facilitent la maintenance, permettent les themes, et accélèrent le développement en créant un langage design partagé.

---

**Temps de lecture estimé : 55 minutes**
**Niveau : Expert**
**Prérequis : Partie I + Chapitres 1, 2**
