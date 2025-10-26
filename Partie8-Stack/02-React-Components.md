# 📖 Partie VIII - Chapitre 2 : React - Composants, Hooks, State, Accessibilité

## 🎯 Objectif du chapitre

Maîtriser la création de composants React modernes, l'utilisation avancée des hooks, la gestion d'état complexe, et l'implémentation de l'accessibilité selon les standards WCAG 2.1.

## ⚛️ React Components : Composants React modernes

### 1. 🏗️ Component Architecture : Architecture de composants

```
Tu es un expert React et component design patterns.

TA TÂCHE : Concevoir une architecture de composants React pour [APPLICATION]

COMPONENT ARCHITECTURE :
1. **Atomic Design** : Atoms, molecules, organisms, templates, pages
2. **Smart vs Dumb Components** : Container vs presentational
3. **Composition Patterns** : Compound components, render props
4. **State Management** : Local state, global state, server state
5. **Performance Patterns** : Memoization, code splitting, virtualization
6. **Accessibility Patterns** : WCAG compliance, keyboard navigation

CONTRAINTES :
- React 18+ features
- TypeScript strict mode
- Performance optimization
- Accessibility WCAG 2.1 AA
- Testing coverage
- Design system integration

FORMAT DE SORTIE :
1. Component architecture design
2. Atomic design implementation
3. Smart/dumb component separation
4. State management strategy
5. Performance optimization
6. Accessibility implementation
7. Testing strategy

QUALITÉ : Composants React modernes, performants, accessibles
```

**Exemple d'architecture de composants React :**
```typescript
// src/components/atomic/atoms/index.ts
export { default as Button } from './Button';
export { default as Input } from './Input';
export { default as Icon } from './Icon';
export { default as Badge } from './Badge';
export { default as Spinner } from './Spinner';
export { default as Avatar } from './Avatar';

// src/components/atomic/molecules/index.ts
export { default as FormField } from './FormField';
export { default as SearchBar } from './SearchBar';
export { default as Pagination } from './Pagination';
export { default as Modal } from './Modal';
export { default as Toast } from './Toast';
export { default as Card } from './Card';

// src/components/atomic/organisms/index.ts
export { default as Header } from './Header';
export { default as Footer } from './Footer';
export { default as Sidebar } from './Sidebar';
export { default as ProductGrid } from './ProductGrid';
export { default as ShoppingCart } from './ShoppingCart';
export { default as UserProfile } from './UserProfile';

// src/components/atomic/templates/index.ts
export { default as DashboardLayout } from './DashboardLayout';
export { default as AuthLayout } from './AuthLayout';
export { default as ProductLayout } from './ProductLayout';
export { default as CheckoutLayout } from './CheckoutLayout';

// src/components/atomic/pages/index.ts
export { default as HomePage } from './HomePage';
export { default as ProductPage } from './ProductPage';
export { default as CartPage } from './CartPage';
export { default as CheckoutPage } from './CheckoutPage';
export { default as ProfilePage } from './ProfilePage';
```

### 2. 🎯 Advanced React Hooks : Hooks avancés

```
Tu es un expert React hooks et state management.

TA TÂCHE : Implémenter des hooks React avancés pour [FONCTIONNALITÉS]

HOOKS À CRÉER :
1. **useAsyncData** : Async data fetching with caching
2. **useLocalStorage** : Persistent local storage with TypeScript
3. **useDebounce** : Debounced value for search/optimization
4. **useIntersectionObserver** : Lazy loading and infinite scroll
5. **useWindowSize** : Responsive design utilities
6. **useForm** : Complex form management with validation
7. **useAuth** : Authentication state management

CONTRAINTES :
- TypeScript generics
- Performance optimization
- Memory leak prevention
- Error handling
- Testing utilities

FORMAT DE SORTIE :
1. Custom hooks implementation
2. TypeScript definitions
3. Usage examples
4. Performance analysis
5. Error handling
6. Testing strategies
7. Best practices

QUALITÉ : Hooks réutilisables, performants, testés
```

**Exemple de hooks avancés :**
```typescript
// src/hooks/useAsyncData.ts
import { useState, useEffect, useCallback, useRef } from 'react';

interface UseAsyncDataOptions<T> {
  initialData?: T;
  cacheTime?: number;
  staleTime?: number;
  retry?: number;
  retryDelay?: number;
  onSuccess?: (data: T) => void;
  onError?: (error: Error) => void;
}

interface UseAsyncDataResult<T> {
  data: T | undefined;
  loading: boolean;
  error: Error | null;
  refetch: () => Promise<void>;
  isStale: boolean;
}

export function useAsyncData<T>(
  asyncFunction: () => Promise<T>,
  options: UseAsyncDataOptions<T> = {}
): UseAsyncDataResult<T> {
  const {
    initialData,
    cacheTime = 5 * 60 * 1000, // 5 minutes
    staleTime = 0,
    retry = 3,
    retryDelay = 1000,
    onSuccess,
    onError
  } = options;

  const [data, setData] = useState<T | undefined>(initialData);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState<Error | null>(null);
  const [isStale, setIsStale] = useState(false);

  const cacheRef = useRef<Map<string, { data: T; timestamp: number }>>(new Map());
  const abortControllerRef = useRef<AbortController | null>(null);
  const mountedRef = useRef(true);

  const cacheKey = JSON.stringify(asyncFunction.toString());

  const fetchData = useCallback(async (showLoading = true) => {
    // Check cache first
    const cached = cacheRef.current.get(cacheKey);
    if (cached && Date.now() - cached.timestamp < cacheTime) {
      setData(cached.data);
      setIsStale(Date.now() - cached.timestamp > staleTime);
      return;
    }

    // Cancel previous request
    if (abortControllerRef.current) {
      abortControllerRef.current.abort();
    }

    abortControllerRef.current = new AbortController();

    if (showLoading) {
      setLoading(true);
    }
    setError(null);
    setIsStale(false);

    let lastError: Error;

    for (let attempt = 1; attempt <= retry; attempt++) {
      try {
        const result = await asyncFunction();

        if (!mountedRef.current) return;

        // Update cache
        cacheRef.current.set(cacheKey, { data: result, timestamp: Date.now() });

        setData(result);
        setIsStale(false);
        setLoading(false);
        setError(null);

        onSuccess?.(result);
        return;

      } catch (err) {
        if (!mountedRef.current) return;

        lastError = err as Error;

        if (attempt === retry) {
          setError(lastError);
          setLoading(false);
          setIsStale(false);
          onError?.(lastError);
        } else {
          // Exponential backoff
          await new Promise(resolve =>
            setTimeout(resolve, retryDelay * Math.pow(2, attempt - 1))
          );
        }
      }
    }
  }, [asyncFunction, cacheTime, staleTime, retry, retryDelay, onSuccess, onError]);

  const refetch = useCallback(async () => {
    await fetchData(true);
  }, [fetchData]);

  useEffect(() => {
    fetchData();
  }, [fetchData]);

  useEffect(() => {
    return () => {
      mountedRef.current = false;
      if (abortControllerRef.current) {
        abortControllerRef.current.abort();
      }
    };
  }, []);

  return {
    data,
    loading,
    error,
    refetch,
    isStale
  };
}

// src/hooks/useForm.ts
import { useState, useCallback, useMemo } from 'react';

interface ValidationRule<T = any> {
  validate: (value: T, formData?: Record<string, any>) => string | null;
  required?: boolean;
}

interface FieldConfig<T = any> {
  defaultValue?: T;
  validation?: ValidationRule<T>[];
  transform?: (value: T) => T;
}

interface UseFormOptions<T extends Record<string, any>> {
  initialValues: T;
  onSubmit?: (values: T) => void | Promise<void>;
  validateOnChange?: boolean;
  validateOnBlur?: boolean;
}

interface UseFormResult<T extends Record<string, any>> {
  values: T;
  errors: Partial<Record<keyof T, string>>;
  touched: Partial<Record<keyof T, boolean>>;
  isValid: boolean;
  isSubmitting: boolean;
  setValue: (field: keyof T, value: T[keyof T]) => void;
  setError: (field: keyof T, error: string) => void;
  setTouched: (field: keyof T, touched?: boolean) => void;
  handleSubmit: (e?: React.FormEvent) => Promise<void>;
  handleChange: (field: keyof T) => (e: React.ChangeEvent<HTMLInputElement | HTMLTextAreaElement | HTMLSelectElement>) => void;
  handleBlur: (field: keyof T) => () => void;
  reset: () => void;
  resetField: (field: keyof T) => void;
}

export function useForm<T extends Record<string, any>>(
  options: UseFormOptions<T>
): UseFormResult<T> {
  const { initialValues, onSubmit, validateOnChange = false, validateOnBlur = true } = options;

  const [values, setValues] = useState<T>(initialValues);
  const [errors, setErrors] = useState<Partial<Record<keyof T, string>>>({});
  const [touched, setTouchedFields] = useState<Partial<Record<keyof T, boolean>>>({});
  const [isSubmitting, setIsSubmitting] = useState(false);

  const fieldConfigs = useMemo(() => {
    // This would be passed as a parameter in a real implementation
    return {} as Record<keyof T, FieldConfig>;
  }, []);

  const validateField = useCallback((field: keyof T, value: T[keyof T]): string | null => {
    const config = fieldConfigs[field];
    if (!config?.validation) return null;

    for (const rule of config.validation) {
      const error = rule.validate(value, values);
      if (error) return error;
    }

    return null;
  }, [fieldConfigs, values]);

  const validateForm = useCallback((): Partial<Record<keyof T, string>> => {
    const newErrors: Partial<Record<keyof T, string>> = {};

    Object.keys(values).forEach(field => {
      const error = validateField(field as keyof T, values[field]);
      if (error) {
        newErrors[field as keyof T] = error;
      }
    });

    return newErrors;
  }, [values, validateField]);

  const setValue = useCallback((field: keyof T, value: T[keyof T]) => {
    const config = fieldConfigs[field];
    const transformedValue = config?.transform ? config.transform(value) : value;

    setValues(prev => ({ ...prev, [field]: transformedValue }));

    if (validateOnChange) {
      const error = validateField(field, transformedValue);
      setErrors(prev => ({ ...prev, [field]: error }));
    }
  }, [fieldConfigs, validateOnChange, validateField]);

  const setError = useCallback((field: keyof T, error: string) => {
    setErrors(prev => ({ ...prev, [field]: error }));
  }, []);

  const setTouched = useCallback((field: keyof T, touched = true) => {
    setTouchedFields(prev => ({ ...prev, [field]: touched }));

    if (validateOnBlur && touched) {
      const error = validateField(field, values[field]);
      setErrors(prev => ({ ...prev, [field]: error }));
    }
  }, [validateOnBlur, validateField, values]);

  const handleSubmit = useCallback(async (e?: React.FormEvent) => {
    e?.preventDefault();

    // Validate all fields
    const formErrors = validateForm();
    setErrors(formErrors);

    // Mark all fields as touched
    const allTouched = Object.keys(values).reduce((acc, field) => {
      acc[field as keyof T] = true;
      return acc;
    }, {} as Partial<Record<keyof T, boolean>>);
    setTouchedFields(allTouched);

    // Check if form is valid
    const isValid = Object.keys(formErrors).length === 0;

    if (!isValid) {
      return;
    }

    if (!onSubmit) {
      return;
    }

    setIsSubmitting(true);

    try {
      await onSubmit(values);
    } catch (error) {
      console.error('Form submission error:', error);
    } finally {
      setIsSubmitting(false);
    }
  }, [values, validateForm, onSubmit]);

  const handleChange = useCallback((field: keyof T) => {
    return (e: React.ChangeEvent<HTMLInputElement | HTMLTextAreaElement | HTMLSelectElement>) => {
      const value = e.target.type === 'checkbox'
        ? (e.target as HTMLInputElement).checked
        : e.target.value;

      setValue(field, value as T[keyof T]);
    };
  }, [setValue]);

  const handleBlur = useCallback((field: keyof T) => {
    return () => setTouched(field, true);
  }, [setTouched]);

  const reset = useCallback(() => {
    setValues(initialValues);
    setErrors({});
    setTouchedFields({});
    setIsSubmitting(false);
  }, [initialValues]);

  const resetField = useCallback((field: keyof T) => {
    setValues(prev => ({ ...prev, [field]: initialValues[field] }));
    setErrors(prev => ({ ...prev, [field]: undefined }));
    setTouchedFields(prev => ({ ...prev, [field]: false }));
  }, [initialValues]);

  const isValid = Object.keys(errors).length === 0;

  return {
    values,
    errors,
    touched,
    isValid,
    isSubmitting,
    setValue,
    setError,
    setTouched,
    handleSubmit,
    handleChange,
    handleBlur,
    reset,
    resetField
  };
}
```

## 🎨 React State Management : Gestion d'état React

### 1. 📊 Global State Management : Gestion d'état global

```
Tu es un expert React state management et modern patterns.

TA TÂCHE : Implémenter la gestion d'état global pour [APPLICATION]

STATE MANAGEMENT SOLUTIONS :
1. **Context API** : Simple global state, theme, user preferences
2. **Redux Toolkit** : Complex state, async actions, middleware
3. **Zustand** : Lightweight, TypeScript-friendly, no boilerplate
4. **React Query** : Server state, caching, background updates
5. **XState** : Complex state machines, finite state logic
6. **Jotai** : Atomic state, composable, minimal

CONTRAINTES :
- TypeScript integration
- Performance optimization
- Developer experience
- Testing capabilities
- Scalability considerations

FORMAT DE SORTIE :
1. State management architecture
2. Store implementations
3. TypeScript integration
4. Performance optimization
5. Testing strategies
6. Migration guidelines
7. Best practices

QUALITÉ : State management scalable, performant, type-safe
```

**Exemple de state management avec Zustand :**
```typescript
// src/stores/authStore.ts
import { create } from 'zustand';
import { devtools, persist, subscribeWithSelector } from 'zustand/middleware';
import { immer } from 'zustand/middleware/immer';

interface User {
  id: string;
  email: string;
  name: string;
  avatar?: string;
  role: 'admin' | 'user' | 'guest';
  preferences: {
    theme: 'light' | 'dark' | 'system';
    language: string;
    notifications: boolean;
  };
}

interface AuthState {
  user: User | null;
  isAuthenticated: boolean;
  isLoading: boolean;
  error: string | null;
  lastLoginAt: Date | null;
  loginAttempts: number;
  isLocked: boolean;
  lockUntil: Date | null;
}

interface AuthActions {
  login: (email: string, password: string) => Promise<void>;
  logout: () => void;
  register: (userData: RegisterData) => Promise<void>;
  updateProfile: (data: Partial<User>) => Promise<void>;
  changePassword: (currentPassword: string, newPassword: string) => Promise<void>;
  refreshToken: () => Promise<void>;
  clearError: () => void;
  resetLoginAttempts: () => void;
}

type AuthStore = AuthState & AuthActions;

export const useAuthStore = create<AuthStore>()(
  devtools(
    persist(
      subscribeWithSelector(
        immer((set, get) => ({
          // Initial state
          user: null,
          isAuthenticated: false,
          isLoading: false,
          error: null,
          lastLoginAt: null,
          loginAttempts: 0,
          isLocked: false,
          lockUntil: null,

          // Actions
          login: async (email: string, password: string) => {
            set(state => {
              state.isLoading = true;
              state.error = null;
            });

            try {
              const response = await authApi.login(email, password);
              const user = response.user;

              set(state => {
                state.user = user;
                state.isAuthenticated = true;
                state.isLoading = false;
                state.lastLoginAt = new Date();
                state.loginAttempts = 0;
                state.isLocked = false;
                state.lockUntil = null;
              });

              // Dispatch auth success event
              window.dispatchEvent(new CustomEvent('auth:login', { detail: user }));

            } catch (error: any) {
              set(state => {
                state.isLoading = false;
                state.error = error.message;
                state.loginAttempts += 1;

                // Lock account after 5 failed attempts
                if (state.loginAttempts >= 5) {
                  state.isLocked = true;
                  state.lockUntil = new Date(Date.now() + 15 * 60 * 1000); // 15 minutes
                }
              });

              throw error;
            }
          },

          logout: () => {
            set(state => {
              state.user = null;
              state.isAuthenticated = false;
              state.error = null;
              state.lastLoginAt = null;
            });

            // Clear any persisted auth data
            localStorage.removeItem('auth_token');
            localStorage.removeItem('refresh_token');

            // Dispatch logout event
            window.dispatchEvent(new CustomEvent('auth:logout'));
          },

          register: async (userData: RegisterData) => {
            set(state => {
              state.isLoading = true;
              state.error = null;
            });

            try {
              const response = await authApi.register(userData);
              const user = response.user;

              set(state => {
                state.user = user;
                state.isAuthenticated = true;
                state.isLoading = false;
                state.lastLoginAt = new Date();
              });

              window.dispatchEvent(new CustomEvent('auth:register', { detail: user }));

            } catch (error: any) {
              set(state => {
                state.isLoading = false;
                state.error = error.message;
              });

              throw error;
            }
          },

          updateProfile: async (data: Partial<User>) => {
            const currentUser = get().user;
            if (!currentUser) throw new Error('Not authenticated');

            set(state => {
              state.isLoading = true;
              state.error = null;
            });

            try {
              const updatedUser = await userApi.updateProfile(currentUser.id, data);

              set(state => {
                state.user = { ...state.user!, ...updatedUser };
                state.isLoading = false;
              });

            } catch (error: any) {
              set(state => {
                state.isLoading = false;
                state.error = error.message;
              });

              throw error;
            }
          },

          changePassword: async (currentPassword: string, newPassword: string) => {
            const currentUser = get().user;
            if (!currentUser) throw new Error('Not authenticated');

            set(state => {
              state.isLoading = true;
              state.error = null;
            });

            try {
              await authApi.changePassword(currentUser.id, currentPassword, newPassword);

              set(state => {
                state.isLoading = false;
              });

              // Log out user for security
              get().logout();

            } catch (error: any) {
              set(state => {
                state.isLoading = false;
                state.error = error.message;
              });

              throw error;
            }
          },

          refreshToken: async () => {
            try {
              const response = await authApi.refreshToken();
              const user = response.user;

              set(state => {
                state.user = user;
                state.isAuthenticated = true;
                state.error = null;
              });

            } catch (error: any) {
              // Refresh failed, log out user
              get().logout();
              throw error;
            }
          },

          clearError: () => {
            set(state => {
              state.error = null;
            });
          },

          resetLoginAttempts: () => {
            set(state => {
              state.loginAttempts = 0;
              state.isLocked = false;
              state.lockUntil = null;
            });
          }
        })),
      ),
      {
        name: 'auth-storage',
        partialize: (state) => ({
          user: state.user,
          isAuthenticated: state.isAuthenticated,
          lastLoginAt: state.lastLoginAt
        }),
        version: 1,
        migrate: (persistedState: any, version: number) => {
          if (version === 0) {
            // Migration logic for version upgrades
            return {
              ...persistedState,
              loginAttempts: 0,
              isLocked: false,
              lockUntil: null
            };
          }
          return persistedState;
        }
      }
    ),
    {
      name: 'auth-store'
    }
  )
);

// Selectors for optimized re-renders
export const useAuthUser = () => useAuthStore(state => state.user);
export const useIsAuthenticated = () => useAuthStore(state => state.isAuthenticated);
export const useAuthLoading = () => useAuthStore(state => state.isLoading);
export const useAuthError = () => useAuthStore(state => state.error);
export const useUserPreferences = () => useAuthStore(state => state.user?.preferences);

// Actions
export const useAuthActions = () => useAuthStore(state => ({
  login: state.login,
  logout: state.logout,
  register: state.register,
  updateProfile: state.updateProfile,
  changePassword: state.changePassword,
  refreshToken: state.refreshToken,
  clearError: state.clearError,
  resetLoginAttempts: state.resetLoginAttempts
}));
```

### 2. 🎯 Server State Management : Gestion d'état serveur

```
Tu es un expert server state management et React Query.

TA TÂCHE : Implémenter la gestion d'état serveur pour [APPLICATION]

SERVER STATE SOLUTIONS :
1. **React Query** : Data fetching, caching, synchronization
2. **SWR** : Lightweight, built-in caching, revalidation
3. **Apollo Client** : GraphQL state management, caching
4. **RTK Query** : Redux Toolkit Query for REST/GraphQL
5. **Custom Hooks** : Tailored data fetching solutions

CONTRAINTES :
- TypeScript integration
- Caching optimization
- Background updates
- Error handling
- Performance monitoring

FORMAT DE SORTIE :
1. Server state architecture
2. Data fetching implementation
3. Caching strategies
4. Background updates
5. Error handling
6. Performance optimization
7. Testing strategies

QUALITÉ : Server state géré efficacement, cache optimisé, erreurs gérées
```

## ♿ React Accessibility : Accessibilité React

### 1. ♿ WCAG 2.1 Compliance : Conformité WCAG 2.1

```
Tu es un expert React accessibility et WCAG compliance.

TA TÂCHE : Implémenter l'accessibilité complète pour [COMPOSANTS]

ACCESSIBILITY REQUIREMENTS :
1. **Keyboard Navigation** : Focus management, shortcuts
2. **Screen Reader Support** : ARIA labels, descriptions
3. **Color Contrast** : WCAG AA compliance (4.5:1)
4. **Form Accessibility** : Labels, validation, error messages
5. **Image Accessibility** : Alt text, decorative images
6. **Navigation Accessibility** : Breadcrumbs, skip links
7. **Dynamic Content** : Live regions, announcements

CONTRAINTES :
- WCAG 2.1 AA compliance
- Screen reader compatibility
- Keyboard-only navigation
- High contrast mode
- Mobile accessibility

FORMAT DE SORTIE :
1. Accessibility audit
2. WCAG compliance implementation
3. Keyboard navigation
4. Screen reader optimization
5. Testing setup
6. Monitoring integration
7. User testing guidelines

QUALITÉ : Accessibilité complète, WCAG compliant, testée
```

**Exemple d'implémentation d'accessibilité :**
```typescript
// src/components/accessibility/AccessibleModal.tsx
import React, { useEffect, useRef } from 'react';
import { FocusTrap } from 'focus-trap-react';
import { useKeyboardNavigation } from '../../hooks/useKeyboardNavigation';
import { useScreenReader } from '../../hooks/useScreenReader';

interface AccessibleModalProps {
  isOpen: boolean;
  onClose: () => void;
  title: string;
  description?: string;
  children: React.ReactNode;
  size?: 'sm' | 'md' | 'lg' | 'xl';
  closeOnOverlayClick?: boolean;
  closeOnEscape?: boolean;
  initialFocus?: 'first' | 'last' | 'close' | React.RefObject<HTMLElement>;
  labelledBy?: string;
  describedBy?: string;
}

export const AccessibleModal: React.FC<AccessibleModalProps> = ({
  isOpen,
  onClose,
  title,
  description,
  children,
  size = 'md',
  closeOnOverlayClick = true,
  closeOnEscape = true,
  initialFocus = 'first',
  labelledBy,
  describedBy
}) => {
  const modalRef = useRef<HTMLDivElement>(null);
  const previousFocusRef = useRef<HTMLElement | null>(null);

  const { announceToScreenReader } = useScreenReader();

  useKeyboardNavigation({
    onEscape: closeOnEscape ? onClose : undefined,
    onTab: (event: KeyboardEvent) => {
      if (!modalRef.current) return;

      const focusableElements = modalRef.current.querySelectorAll(
        'button, [href], input, select, textarea, [tabindex]:not([tabindex="-1"])'
      );

      if (focusableElements.length === 0) return;

      const firstElement = focusableElements[0] as HTMLElement;
      const lastElement = focusableElements[focusableElements.length - 1] as HTMLElement;

      if (event.shiftKey) {
        if (document.activeElement === firstElement) {
          event.preventDefault();
          lastElement.focus();
        }
      } else {
        if (document.activeElement === lastElement) {
          event.preventDefault();
          firstElement.focus();
        }
      }
    }
  });

  useEffect(() => {
    if (isOpen) {
      // Store the currently focused element
      previousFocusRef.current = document.activeElement as HTMLElement;

      // Set initial focus
      setTimeout(() => {
        if (!modalRef.current) return;

        const focusableElements = modalRef.current.querySelectorAll(
          'button, [href], input, select, textarea, [tabindex]:not([tabindex="-1"])'
        );

        if (focusableElements.length > 0) {
          let elementToFocus: HTMLElement;

          switch (initialFocus) {
            case 'first':
              elementToFocus = focusableElements[0] as HTMLElement;
              break;
            case 'last':
              elementToFocus = focusableElements[focusableElements.length - 1] as HTMLElement;
              break;
            case 'close':
              elementToFocus = modalRef.current.querySelector('[data-testid="modal-close"]') as HTMLElement;
              break;
            default:
              elementToFocus = initialFocus.current || focusableElements[0] as HTMLElement;
          }

          elementToFocus?.focus();
        }
      }, 100);

      // Announce modal to screen readers
      announceToScreenReader(`Modal opened: ${title}`);
    } else {
      // Restore previous focus
      if (previousFocusRef.current) {
        previousFocusRef.current.focus();
        previousFocusRef.current = null;
      }

      announceToScreenReader('Modal closed');
    }

    return () => {
      if (previousFocusRef.current) {
        previousFocusRef.current.focus();
      }
    };
  }, [isOpen, title, initialFocus, announceToScreenReader]);

  const handleOverlayClick = (event: React.MouseEvent) => {
    if (closeOnOverlayClick && event.target === event.currentTarget) {
      onClose();
    }
  };

  if (!isOpen) return null;

  const modalId = labelledBy || `modal-${Math.random().toString(36).substr(2, 9)}`;
  const descriptionId = describedBy || `modal-description-${Math.random().toString(36).substr(2, 9)}`;

  return (
    <div
      className={`modal-overlay ${size}`}
      onClick={handleOverlayClick}
      role="dialog"
      aria-modal="true"
      aria-labelledby={modalId}
      aria-describedby={description || describedBy ? descriptionId : undefined}
      data-testid="modal-overlay"
    >
      <FocusTrap>
        <div
          ref={modalRef}
          className="modal-container"
          role="document"
          data-testid="modal-container"
        >
          {/* Header */}
          <header className="modal-header">
            <h2
              id={modalId}
              className="modal-title"
              data-testid="modal-title"
            >
              {title}
            </h2>

            <button
              type="button"
              className="modal-close-button"
              onClick={onClose}
              aria-label={`Close ${title} modal`}
              data-testid="modal-close"
            >
              <span aria-hidden="true">&times;</span>
            </button>
          </header>

          {/* Body */}
          <div className="modal-body">
            {description && (
              <p
                id={descriptionId}
                className="modal-description"
                data-testid="modal-description"
              >
                {description}
              </p>
            )}

            <div className="modal-content">
              {children}
            </div>
          </div>

          {/* Footer */}
          <footer className="modal-footer">
            <button
              type="button"
              className="btn btn-secondary"
              onClick={onClose}
              data-testid="modal-cancel"
            >
              Cancel
            </button>

            <button
              type="button"
              className="btn btn-primary"
              onClick={onClose}
              data-testid="modal-confirm"
            >
              Confirm
            </button>
          </footer>
        </div>
      </FocusTrap>
    </div>
  );
};
```

## 📋 Templates React

### ⚛️ Template Component Architecture
```
Tu es un expert React component design et architecture.

TA TÂCHE : Concevoir architecture composants pour [APPLICATION]

ARCHITECTURE :
- Atomic design pattern
- Smart vs dumb components
- State management strategy
- Performance optimization
- Accessibility compliance

CONTRAINTES :
- React 18+ features
- TypeScript strict
- Performance requirements
- WCAG 2.1 AA

FORMAT :
1. Component architecture
2. Atomic design implementation
3. Smart/dumb separation
4. State management
5. Performance optimization
6. Accessibility implementation
7. Testing strategy

QUALITÉ : Composants modernes, performants, accessibles
```

### 🎣 Template React Hooks
```
Tu es un expert React hooks et state management.

TA TÂCHE : Implémenter hooks pour [FONCTIONNALITÉS]

HOOKS :
- Custom hooks
- Performance optimization
- TypeScript integration
- Error handling
- Testing utilities

CONTRAINTES :
- TypeScript generics
- Performance optimization
- Memory leak prevention
- Testing coverage

FORMAT :
1. Custom hooks implementation
2. TypeScript definitions
3. Usage examples
4. Performance analysis
5. Error handling
6. Testing strategies
7. Best practices

QUALITÉ : Hooks réutilisables, performants, testés
```

### 📊 Template State Management
```
Tu es un expert React state management et modern patterns.

TA TÂCHE : Implémenter state management pour [APPLICATION]

SOLUTIONS :
- Context API
- Redux Toolkit
- Zustand
- React Query
- XState

CONTRAINTES :
- TypeScript integration
- Performance optimization
- Developer experience
- Testing capabilities

FORMAT :
1. State management architecture
2. Store implementations
3. TypeScript integration
4. Performance optimization
5. Testing strategies
6. Migration guidelines
7. Best practices

QUALITÉ : State management scalable, performant, type-safe
```

## 🎯 Points clés à retenir

1. **Atomic Design** : Composants réutilisables, maintenables
2. **React Hooks** : Logique réutilisable, performance optimisée
3. **State Management** : Global vs local, server vs client state
4. **Performance** : Memoization, lazy loading, code splitting
5. **Accessibility** : WCAG compliance, keyboard navigation, screen readers
6. **TypeScript** : Type safety, developer experience, error prevention

## 🚀 Prochain chapitre

Maintenant que vous maîtrisez React, découvrons les prompts PyQt5 pour interfaces VJ, effets vidéo et threading.

---

## 📋 Checklist qualité

- ✅ React component architecture
- ✅ Advanced hooks implementation
- ✅ State management patterns
- ✅ Accessibility WCAG 2.1
- ✅ Performance optimization
- ✅ Testing strategies

## 🎯 Test de compréhension

**Question :** Pourquoi React.memo est-il important pour les performances ?

**Réponse attendue :** React.memo empêche les re-renders inutiles des composants fonctionnels en comparant les props (shallow comparison). Sans memo, les composants se re-rendent même si leurs props n'ont pas changé, causant des performances dégradées, surtout dans les grandes applications avec de nombreux composants.

---

**Temps de lecture estimé : 150 minutes**
**Niveau : Expert**
**Prérequis : Partie I + II + III + IV + V + VI + VII + Chapitre 1**
