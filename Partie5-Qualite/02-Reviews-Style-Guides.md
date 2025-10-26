# 📖 Partie V - Chapitre 2 : Reviews assistées, Style guides automatisés

## 🎯 Objectif du chapitre

Maîtriser les techniques de code review assistées par IA, l'automatisation des style guides, et l'implémentation de standards de qualité dans le processus de développement.

## 👥 Code Reviews : Processus optimisé

### 1. 🔍 Code Review assistée par IA

```
Tu es un expert code review et quality assurance.

TA TÂCHE : Effectuer une code review complète et assistée pour [PULL REQUEST]

CRITÈRES DE REVIEW :
1. **Code Quality** : Standards, best practices, patterns
2. **Security** : Vulnerabilities, OWASP compliance
3. **Performance** : Bottlenecks, optimization opportunities
4. **Testing** : Coverage, test quality, edge cases
5. **Documentation** : Comments, API docs, README
6. **Architecture** : SOLID, patterns, coupling
7. **Maintainability** : Readability, complexity, debt

CONTRAINTES :
- Framework : [REACT/LARAVEL/NODE.JS]
- Standards : [PSR-12/ESLint/CODING STANDARD]
- Testing : [JEST/PHPUNIT/TESTING FRAMEWORK]
- Security : OWASP compliance
- Performance : Core Web Vitals / benchmarks

FORMAT DE SORTIE :
1. Review summary avec score global
2. Issues par catégorie (critical/high/medium/low)
3. Code suggestions avec explications
4. Security vulnerabilities identifiées
5. Performance improvements
6. Testing recommendations
7. Best practices validation

QUALITÉ : Review complète, constructive, actionable
```

**Template de code review automatisé :**
```typescript
interface CodeReviewResult {
  score: number; // 1-10
  summary: string;
  issues: {
    critical: Issue[];
    high: Issue[];
    medium: Issue[];
    low: Issue[];
  };
  suggestions: Suggestion[];
  security: SecurityIssue[];
  performance: PerformanceIssue[];
  testing: TestingRecommendation[];
  maintainability: MaintainabilityMetric[];
}

interface Issue {
  category: string;
  title: string;
  description: string;
  line?: number;
  file?: string;
  severity: 'critical' | 'high' | 'medium' | 'low';
  rule: string;
  example: string;
  fix: string;
}

const automatedCodeReview = async (pullRequest: PullRequest): Promise<CodeReviewResult> => {
  const review: CodeReviewResult = {
    score: 0,
    summary: '',
    issues: { critical: [], high: [], medium: [], low: [] },
    suggestions: [],
    security: [],
    performance: [],
    testing: [],
    maintainability: []
  };

  // Analyze code changes
  for (const file of pullRequest.changedFiles) {
    if (file.language === 'typescript') {
      await analyzeTypeScriptFile(file, review);
    } else if (file.language === 'javascript') {
      await analyzeJavaScriptFile(file, review);
    } else if (file.language === 'php') {
      await analyzePHPFile(file, review);
    }
  }

  // Calculate overall score
  review.score = calculateReviewScore(review.issues);

  // Generate summary
  review.summary = generateReviewSummary(review);

  return review;
};

const analyzeTypeScriptFile = async (file: File, review: CodeReviewResult) => {
  // Check for any usage
  const anyTypes = file.content.match(/: any\b/g);
  if (anyTypes) {
    review.issues.medium.push({
      category: 'Type Safety',
      title: 'Avoid using `any` type',
      description: 'Using `any` defeats TypeScript benefits. Use specific types instead.',
      line: getLineNumber(file.content, anyTypes[0]),
      file: file.path,
      severity: 'medium',
      rule: 'typescript-no-any',
      example: 'Replace `data: any` with `data: UserData`',
      fix: 'Define proper interfaces or use union types'
    });
  }

  // Check for console.log statements
  const consoleLogs = file.content.match(/console\.log/g);
  if (consoleLogs && !file.path.includes('debug')) {
    review.issues.low.push({
      category: 'Code Quality',
      title: 'Remove console.log statements',
      description: 'Console.log statements should not be in production code.',
      severity: 'low',
      rule: 'no-console-logs',
      example: 'console.log("Debug info")',
      fix: 'Use proper logging framework or remove if not needed'
    });
  }

  // Check for magic numbers
  const magicNumbers = file.content.match(/\b\d{2,}\b/g);
  if (magicNumbers) {
    magicNumbers.forEach(number => {
      if (!isAcceptableMagicNumber(number)) {
        review.issues.medium.push({
          category: 'Code Quality',
          title: 'Replace magic numbers with constants',
          description: `Magic number ${number} should be replaced with a named constant.`,
          severity: 'medium',
          rule: 'no-magic-numbers',
          example: `if (status === 404)`,
          fix: `const HTTP_NOT_FOUND = 404; if (status === HTTP_NOT_FOUND)`
        });
      }
    });
  }
};

const generateReviewSummary = (review: CodeReviewResult): string => {
  const totalIssues = Object.values(review.issues).flat().length;
  const criticalCount = review.issues.critical.length;
  const highCount = review.issues.high.length;

  if (criticalCount > 0) {
    return `❌ Critical issues found (${criticalCount}). This PR requires immediate attention before merging.`;
  } else if (highCount > 3) {
    return `⚠️ Multiple high-priority issues (${highCount}). Please address before merging.`;
  } else if (totalIssues > 10) {
    return `🔄 Good foundation but needs improvements (${totalIssues} issues). Consider addressing medium/low priority items.`;
  } else {
    return `✅ Great code quality! Only minor improvements suggested (${totalIssues} issues).`;
  }
};
```

### 2. 🤖 Automated Code Review Tools

```
Tu es un expert automated code review et CI/CD integration.

TA TÂCHE : Configurer des outils de code review automatisée pour [REPOSITORY]

OUTILS DE CODE REVIEW :
1. **Linting** : ESLint, PHP CS Fixer, RuboCop
2. **Type Checking** : TypeScript, mypy, Flow
3. **Security Scanning** : SAST tools, dependency scanning
4. **Code Quality** : SonarQube, CodeClimate, Codacy
5. **Test Coverage** : Coverage reports, quality gates
6. **Performance** : Bundle analysis, runtime metrics
7. **Standards** : Prettier, Black, gofmt

CONTRAINTES :
- GitHub/GitLab integration
- CI/CD pipeline integration
- Team adoption
- Performance impact
- False positive management

FORMAT DE SORTIE :
1. Code review tools architecture
2. Configuration files
3. CI/CD pipeline integration
4. Quality gates setup
5. Team onboarding
6. Metrics dashboard
7. Maintenance procedures

QUALITÉ : Code review automatisé, intégré, efficace
```

**Configuration ESLint pour React :**
```json
// .eslintrc.js
module.exports = {
  root: true,
  env: {
    browser: true,
    es2021: true,
    node: true,
    jest: true
  },
  extends: [
    'eslint:recommended',
    '@typescript-eslint/recommended',
    '@typescript-eslint/recommended-requiring-type-checking',
    'plugin:react/recommended',
    'plugin:react-hooks/recommended',
    'plugin:jsx-a11y/recommended',
    'plugin:security/recommended',
    'plugin:react/jsx-runtime',
    'prettier'
  ],
  parser: '@typescript-eslint/parser',
  parserOptions: {
    ecmaFeatures: {
      jsx: true
    },
    ecmaVersion: 'latest',
    sourceType: 'module',
    project: './tsconfig.json'
  },
  plugins: [
    'react',
    'react-hooks',
    '@typescript-eslint',
    'jsx-a11y',
    'security',
    'import',
    'unused-imports'
  ],
  rules: {
    // TypeScript specific
    '@typescript-eslint/no-unused-vars': ['error', { argsIgnorePattern: '^_' }],
    '@typescript-eslint/no-explicit-any': 'warn',
    '@typescript-eslint/prefer-nullish-coalescing': 'error',
    '@typescript-eslint/prefer-optional-chain': 'error',
    '@typescript-eslint/no-unnecessary-type-assertion': 'error',

    // React specific
    'react/prop-types': 'off', // Using TypeScript for props
    'react/react-in-jsx-scope': 'off', // React 17+
    'react-hooks/rules-of-hooks': 'error',
    'react-hooks/exhaustive-deps': 'warn',

    // Code quality
    'no-console': 'warn',
    'no-debugger': 'error',
    'no-duplicate-imports': 'error',
    'prefer-const': 'error',
    'no-var': 'error',

    // Security
    'security/detect-buffer-noassert': 'error',
    'security/detect-child-process': 'warn',
    'security/detect-disable-mustache-escape': 'error',
    'security/detect-eval-with-expression': 'error',
    'security/detect-new-buffer': 'error',
    'security/detect-no-csrf-before-method-override': 'error',
    'security/detect-non-literal-fs-filename': 'warn',
    'security/detect-non-literal-regexp': 'warn',
    'security/detect-non-literal-require': 'warn',
    'security/detect-object-injection': 'warn',
    'security/detect-possible-timing-attacks': 'warn',
    'security/detect-pseudoRandomBytes': 'error',
    'security/detect-unsafe-regex': 'error',

    // Import organization
    'import/order': [
      'error',
      {
        groups: [
          'builtin',
          'external',
          'internal',
          'parent',
          'sibling',
          'index'
        ],
        'newlines-between': 'always',
        alphabetize: {
          order: 'asc',
          caseInsensitive: true
        }
      }
    ],
    'unused-imports/no-unused-imports': 'error',
    'unused-imports/no-unused-vars': [
      'warn',
      {
        vars: 'all',
        varsIgnorePattern: '^_',
        args: 'after-used',
        argsIgnorePattern: '^_'
      }
    ],

    // Accessibility
    'jsx-a11y/alt-text': 'error',
    'jsx-a11y/anchor-has-content': 'error',
    'jsx-a11y/anchor-is-valid': 'error',
    'jsx-a11y/img-redundant-alt': 'error',
    'jsx-a11y/no-redundant-roles': 'error',

    // Best practices
    'eqeqeq': ['error', 'always'],
    'curly': ['error', 'all'],
    'no-eval': 'error',
    'no-implied-eval': 'error',
    'no-new-func': 'error',
    'no-script-url': 'error'
  },
  settings: {
    react: {
      version: 'detect'
    },
    'import/resolver': {
      typescript: {}
    }
  },
  overrides: [
    {
      files: ['**/*.test.ts', '**/*.test.tsx', '**/*.spec.ts', '**/*.spec.tsx'],
      rules: {
        '@typescript-eslint/no-explicit-any': 'off',
        'security/detect-child-process': 'off',
        'no-console': 'off'
      }
    },
    {
      files: ['**/*.config.js', '**/*.config.ts'],
      rules: {
        '@typescript-eslint/no-var-requires': 'off',
        'no-console': 'off'
      }
    }
  ]
};
```

## 📋 Style Guides : Standards automatisés

### 1. 🎨 Automated Style Enforcement

```
Tu es un expert coding standards et style guides.

TA TÂCHE : Implémenter des style guides automatisés pour [LANGUAGE]

STYLE GUIDES À IMPLÉMENTER :
1. **Formatting** : Prettier, Black, gofmt
2. **Naming Conventions** : camelCase, snake_case, PascalCase
3. **Import Organization** : Grouped, alphabetical
4. **Comment Standards** : JSDoc, PHPDoc, docstrings
5. **File Structure** : Organization, naming
6. **Code Organization** : Functions, classes, modules

CONTRAINTES :
- Team consistency
- IDE integration
- Git hooks integration
- CI/CD enforcement
- Performance impact

FORMAT DE SORTIE :
1. Style guide documentation
2. Configuration files
3. IDE integration
4. Git hooks setup
5. CI/CD integration
6. Team adoption guide
7. Enforcement policies

QUALITÉ : Style guides cohérents, automatisés, adoptés
```

**Configuration Prettier pour TypeScript/React :**
```json
// .prettierrc
{
  "semi": true,
  "trailingComma": "es5",
  "singleQuote": true,
  "printWidth": 100,
  "tabWidth": 2,
  "useTabs": false,
  "bracketSpacing": true,
  "bracketSameLine": false,
  "arrowParens": "avoid",
  "endOfLine": "lf",
  "quoteProps": "as-needed",
  "jsxSingleQuote": true,
  "overrides": [
    {
      "files": "*.md",
      "options": {
        "printWidth": 80,
        "proseWrap": "always"
      }
    },
    {
      "files": "*.json",
      "options": {
        "printWidth": 200
      }
    }
  ]
}

// .prettierignore
node_modules
dist
build
coverage
.git
*.min.js
*.min.css
package-lock.json
yarn.lock
pnpm-lock.yaml
CHANGELOG.md
```

**Configuration PHP CS Fixer :**
```php
<?php
// .php-cs-fixer.php
$finder = PhpCsFixer\Finder::create()
    ->in(__DIR__)
    ->exclude('vendor')
    ->exclude('node_modules')
    ->exclude('storage')
    ->exclude('bootstrap/cache')
    ->notPath('server.php')
    ->notPath('webpack.mix.js');

$config = new PhpCsFixer\Config();
return $config
    ->setRules([
        '@PSR12' => true,
        '@PHP81Migration' => true,
        'array_syntax' => ['syntax' => 'short'],
        'binary_operator_spaces' => ['default' => 'single_space'],
        'blank_line_after_namespace' => true,
        'blank_line_after_opening_tag' => true,
        'blank_line_before_statement' => ['statements' => ['return', 'throw', 'break', 'continue', 'declare']],
        'cast_spaces' => true,
        'class_attributes_separation' => ['elements' => ['method' => 'one', 'property' => 'one']],
        'class_definition' => ['single_line' => true],
        'clean_namespace' => true,
        'compact_nullable_typehint' => true,
        'concat_space' => ['spacing' => 'one'],
        'constant_case' => ['case' => 'lower'],
        'declare_equal_normalize' => true,
        'declare_strict_types' => false,
        'function_typehint_space' => true,
        'general_phpdoc_tag_rename' => true,
        'global_namespace_import' => ['import_classes' => true, 'import_constants' => true, 'import_functions' => true],
        'group_import' => true,
        'include' => true,
        'increment_style' => ['style' => 'post'],
        'indentation_type' => true,
        'integer_literal_case' => true,
        'lambda_not_used_import' => true,
        'linebreak_after_opening_tag' => true,
        'list_syntax' => ['syntax' => 'short'],
        'lowercase_cast' => true,
        'lowercase_keywords' => true,
        'lowercase_static_reference' => true,
        'magic_constant_casing' => true,
        'magic_method_casing' => true,
        'method_chaining_indentation' => true,
        'multiline_comment_opening_closing' => true,
        'multiline_whitespace_before_semicolons' => true,
        'native_function_casing' => true,
        'native_function_type_declaration_casing' => true,
        'new_with_braces' => true,
        'no_alias_functions' => true,
        'no_alternative_syntax' => true,
        'no_binary_string' => true,
        'no_blank_lines_after_class_opening' => true,
        'no_blank_lines_after_phpdoc' => true,
        'no_closing_tag' => true,
        'no_empty_phpdoc' => true,
        'no_empty_statement' => true,
        'no_extra_blank_lines' => true,
        'no_homoglyph_names' => true,
        'no_leading_import_slash' => true,
        'no_leading_namespace_whitespace' => true,
        'no_mixed_echo_print' => ['use' => 'echo'],
        'no_multiline_whitespace_around_double_arrow' => true,
        'no_null_property_initialization' => true,
        'no_php4_constructor' => true,
        'no_short_bool_cast' => true,
        'no_singleline_whitespace_before_semicolons' => true,
        'no_spaces_after_function_name' => true,
        'no_spaces_around_offset' => true,
        'no_superfluous_phpdoc_tags' => true,
        'no_trailing_comma_in_list_call' => true,
        'no_trailing_comma_in_singleline' => true,
        'no_trailing_whitespace' => true,
        'no_trailing_whitespace_in_comment' => true,
        'no_unneeded_control_parentheses' => true,
        'no_unneeded_curly_braces' => true,
        'no_unneeded_final_method' => true,
        'no_unset_cast' => true,
        'no_unused_imports' => true,
        'no_useless_return' => true,
        'no_whitespace_before_comma_in_array' => true,
        'no_whitespace_in_blank_line' => true,
        'normalize_comment_indentation' => true,
        'normalize_index_brace' => true,
        'object_operator_without_whitespace' => true,
        'ordered_class_elements' => true,
        'ordered_imports' => true,
        'ordered_interfaces' => true,
        'ordered_traits' => true,
        'php_unit_fqcn_annotation' => true,
        'php_unit_method_casing' => true,
        'phpdoc_add_missing_param_annotation' => true,
        'phpdoc_align' => true,
        'phpdoc_annotation_without_dot' => true,
        'phpdoc_indent' => true,
        'phpdoc_inline_tag_normalizer' => true,
        'phpdoc_no_access' => true,
        'phpdoc_no_empty_return' => true,
        'phpdoc_no_package' => true,
        'phpdoc_no_useless_inheritdoc' => true,
        'phpdoc_order' => true,
        'phpdoc_return_self_reference' => true,
        'phpdoc_scalar' => true,
        'phpdoc_separation' => true,
        'phpdoc_single_line_var_spacing' => true,
        'phpdoc_summary' => true,
        'phpdoc_to_comment' => true,
        'phpdoc_to_param_type' => true,
        'phpdoc_to_property_type' => true,
        'phpdoc_to_return_type' => true,
        'phpdoc_trim' => true,
        'phpdoc_types' => true,
        'phpdoc_types_order' => true,
        'phpdoc_var_without_name' => true,
        'protected_to_private' => true,
        'return_assignment' => true,
        'return_type_declaration' => true,
        'self_accessor' => true,
        'self_static_accessor' => true,
        'short_scalar_cast' => true,
        'simple_to_complex_string_variable' => true,
        'simplified_null_return' => true,
        'single_blank_line_at_eof' => true,
        'single_blank_line_before_namespace' => true,
        'single_class_element_per_statement' => true,
        'single_import_per_statement' => true,
        'single_line_after_imports' => true,
        'single_line_comment_style' => true,
        'single_line_throw' => true,
        'single_quote' => true,
        'single_space_after_comma' => true,
        'single_space_after_construct' => true,
        'single_space_around_construct' => true,
        'single_trait_insert_per_statement' => true,
        'space_after_semicolon' => true,
        'standardize_comment' => true,
        'standardize_not_equals' => true,
        'static_lambda' => true,
        'strict_comparison' => true,
        'strict_param' => true,
        'string_length_to_empty' => true,
        'switch_case_semicolon_to_colon' => true,
        'switch_case_space' => true,
        'switch_continue_to_break' => true,
        'ternary_operator_spaces' => true,
        'ternary_to_elvis_operator' => true,
        'ternary_to_null_coalescing' => true,
        'trailing_comma_in_multiline' => true,
        'trim_array_spaces' => true,
        'unary_operator_spaces' => true,
        'visibility_required' => true,
        'void_return' => true,
        'whitespace_after_comma_in_array' => true,
        'yoda_style' => true,
    ])
    ->setFinder($finder)
    ->setRiskyAllowed(true)
    ->setUsingCache(true)
    ->setCacheFile(__DIR__ . '/.php-cs-fixer.cache');
```

### 2. 🔧 Git Hooks et Pre-commit

```
Tu es un expert git hooks et pre-commit automation.

TA TÂCHE : Configurer des git hooks pour enforcer la qualité du code

GIT HOOKS À IMPLÉMENTER :
1. **Pre-commit** : Code quality, linting, tests
2. **Pre-push** : Full test suite, security scan
3. **Commit-msg** : Message format validation
4. **Prepare-commit-msg** : Auto-generate commit messages
5. **Post-commit** : Documentation updates
6. **Post-merge** : Dependency updates

CONTRAINTES :
- Fast execution (< 30 seconds)
- False positive prevention
- Team adoption
- Override mechanisms
- Integration with IDEs

FORMAT DE SORTIE :
1. Git hooks configuration
2. Pre-commit setup
3. Husky integration
4. Hook scripts
5. Team guidelines
6. Override procedures
7. Maintenance documentation

QUALITÉ : Git hooks automatisés, efficaces, adoptés
```

**Configuration Husky pour Node.js :**
```json
// package.json
{
  "scripts": {
    "prepare": "husky install",
    "lint": "eslint src --ext .js,.jsx,.ts,.tsx",
    "lint:fix": "eslint src --ext .js,.jsx,.ts,.tsx --fix",
    "type-check": "tsc --noEmit",
    "test": "jest",
    "test:watch": "jest --watch",
    "test:coverage": "jest --coverage",
    "build": "next build",
    "format": "prettier --write src/**/*.{js,jsx,ts,tsx,json,css,md}",
    "format:check": "prettier --check src/**/*.{js,jsx,ts,tsx,json,css,md}",
    "security:audit": "npm audit --audit-level high",
    "security:fix": "npm audit fix"
  },
  "husky": {
    "hooks": {
      "pre-commit": "lint-staged",
      "commit-msg": "commitlint -E HUSKY_GIT_PARAMS",
      "pre-push": "npm run test:ci && npm run security:audit"
    }
  },
  "lint-staged": {
    "*.{js,jsx,ts,tsx}": [
      "eslint --fix",
      "prettier --write",
      "git add"
    ],
    "*.{json,css,md}": [
      "prettier --write",
      "git add"
    ],
    "*.{js,jsx,ts,tsx}": [
      "jest --findRelatedTests --passWithNoTests"
    ]
  }
}
```

**Configuration Git hooks PHP :**
```bash
#!/bin/bash
# scripts/pre-commit.sh

echo "🔍 Running pre-commit hooks..."

# Check PHP syntax
echo "📝 Checking PHP syntax..."
php -l $(git diff --cached --name-only --diff-filter=ACM | grep '\.php$')
if [ $? -ne 0 ]; then
    echo "❌ PHP syntax errors found. Please fix before committing."
    exit 1
fi

# Run PHP CS Fixer
echo "🎨 Running PHP CS Fixer..."
vendor/bin/php-cs-fixer fix --dry-run --diff
if [ $? -ne 0 ]; then
    echo "❌ PHP CS Fixer found issues. Run 'vendor/bin/php-cs-fixer fix' to fix them."
    echo "Or run 'git commit --no-verify' to skip this check."
    exit 1
fi

# Run PHPStan
echo "🔍 Running PHPStan..."
vendor/bin/phpstan analyse --no-progress
if [ $? -ne 0 ]; then
    echo "❌ PHPStan found issues. Please fix before committing."
    exit 1
fi

# Run tests
echo "🧪 Running tests..."
vendor/bin/phpunit --testsuite=unit
if [ $? -ne 0 ]; then
    echo "❌ Unit tests failed. Please fix before committing."
    exit 1
fi

echo "✅ All pre-commit checks passed!"
exit 0
```

## 📊 Testing : Génération automatisée

### 1. 🧪 Test Generation assistée

```
Tu es un expert test automation et test generation.

TA TÂCHE : Générer des tests automatisés pour [CODEBASE]

TESTS À GÉNÉRER :
1. **Unit Tests** : Functions, classes, methods
2. **Integration Tests** : API endpoints, database
3. **E2E Tests** : User journeys, workflows
4. **Performance Tests** : Load testing, stress testing
5. **Security Tests** : Vulnerability testing
6. **Accessibility Tests** : WCAG compliance

CONTRAINTES :
- Framework : [JEST/PHPUNIT/CYPRESS]
- Coverage : > 90%
- Maintainability
- CI/CD integration
- Test data management

FORMAT DE SORTIE :
1. Testing strategy
2. Unit tests generated
3. Integration tests
4. E2E test scenarios
5. Performance tests
6. Security tests
7. Test maintenance

QUALITÉ : Tests complets, automatisés, maintenables
```

**Exemple de génération de tests React :**
```typescript
// Generated unit tests for React component
import React from 'react';
import { render, screen, fireEvent, waitFor } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import { ProductCard } from '../ProductCard';
import { Product } from '../../../types/Product';

const mockProduct: Product = {
  id: '1',
  name: 'Test Product',
  description: 'This is a test product',
  price: 99.99,
  currency: 'USD',
  category: { id: '1', name: 'Electronics' },
  images: [
    { id: '1', url: 'https://example.com/image.jpg', alt: 'Product image' }
  ],
  status: 'active',
  createdAt: new Date('2024-01-01'),
  updatedAt: new Date('2024-01-01')
};

const mockProps = {
  product: mockProduct,
  onAddToCart: jest.fn(),
  onAddToWishlist: jest.fn(),
  isInWishlist: false,
  loading: false
};

describe('ProductCard', () => {
  beforeEach(() => {
    jest.clearAllMocks();
  });

  describe('Rendering', () => {
    it('should render product information correctly', () => {
      render(<ProductCard {...mockProps} />);

      expect(screen.getByText('Test Product')).toBeInTheDocument();
      expect(screen.getByText('This is a test product')).toBeInTheDocument();
      expect(screen.getByText('$99.99')).toBeInTheDocument();
      expect(screen.getByText('Electronics')).toBeInTheDocument();
    });

    it('should render product image with correct alt text', () => {
      render(<ProductCard {...mockProps} />);

      const image = screen.getByAltText('Product image');
      expect(image).toBeInTheDocument();
      expect(image).toHaveAttribute('src', 'https://example.com/image.jpg');
    });

    it('should show loading state when loading is true', () => {
      render(<ProductCard {...mockProps} loading={true} />);

      expect(screen.getByRole('button', { name: /loading/i })).toBeDisabled();
    });

    it('should show wishlist button when isInWishlist is true', () => {
      render(<ProductCard {...mockProps} isInWishlist={true} />);

      expect(screen.getByRole('button', { name: /remove from wishlist/i })).toBeInTheDocument();
    });
  });

  describe('User Interactions', () => {
    it('should call onAddToCart when add to cart button is clicked', async () => {
      const user = userEvent.setup();
      render(<ProductCard {...mockProps} />);

      const addToCartButton = screen.getByRole('button', { name: /add to cart/i });
      await user.click(addToCartButton);

      expect(mockProps.onAddToCart).toHaveBeenCalledTimes(1);
      expect(mockProps.onAddToCart).toHaveBeenCalledWith(mockProduct);
    });

    it('should call onAddToWishlist when wishlist button is clicked', async () => {
      const user = userEvent.setup();
      render(<ProductCard {...mockProps} />);

      const wishlistButton = screen.getByRole('button', { name: /add to wishlist/i });
      await user.click(wishlistButton);

      expect(mockProps.onAddToWishlist).toHaveBeenCalledTimes(1);
      expect(mockProps.onAddToWishlist).toHaveBeenCalledWith(mockProduct.id);
    });

    it('should handle keyboard navigation', async () => {
      const user = userEvent.setup();
      render(<ProductCard {...mockProps} />);

      const addToCartButton = screen.getByRole('button', { name: /add to cart/i });

      // Tab to button and press Enter
      await user.tab();
      expect(addToCartButton).toHaveFocus();

      await user.keyboard('{Enter}');
      expect(mockProps.onAddToCart).toHaveBeenCalledTimes(1);
    });
  });

  describe('Error Handling', () => {
    it('should handle missing product data gracefully', () => {
      const incompleteProduct = { ...mockProduct, name: undefined };

      // Should not throw error
      expect(() => {
        render(<ProductCard {...mockProps} product={incompleteProduct as Product} />);
      }).not.toThrow();
    });

    it('should handle onAddToCart error', async () => {
      const errorProps = {
        ...mockProps,
        onAddToCart: jest.fn().mockRejectedValue(new Error('Cart error'))
      };

      const user = userEvent.setup();
      render(<ProductCard {...errorProps} />);

      const addToCartButton = screen.getByRole('button', { name: /add to cart/i });
      await user.click(addToCartButton);

      // Should handle error gracefully (no console errors)
      expect(errorProps.onAddToCart).toHaveBeenCalled();
    });
  });

  describe('Accessibility', () => {
    it('should have proper ARIA labels', () => {
      render(<ProductCard {...mockProps} />);

      expect(screen.getByRole('article')).toHaveAttribute('aria-labelledby');
      expect(screen.getByRole('button', { name: /add to cart/i })).toBeInTheDocument();
    });

    it('should support keyboard navigation', async () => {
      const user = userEvent.setup();
      render(<ProductCard {...mockProps} />);

      // All interactive elements should be focusable
      await user.tab();
      expect(screen.getByRole('button', { name: /add to cart/i })).toHaveFocus();
    });

    it('should announce price changes to screen readers', () => {
      const { rerender } = render(<ProductCard {...mockProps} />);

      const updatedProduct = { ...mockProduct, price: 89.99 };
      rerender(<ProductCard {...mockProps} product={updatedProduct} />);

      expect(screen.getByText('$89.99')).toBeInTheDocument();
    });
  });
});
```

### 2. 📈 Coverage et Quality Gates

```
Tu es un expert test coverage et quality management.

TA TÂCHE : Configurer des quality gates et coverage pour [PROJECT]

QUALITY GATES :
1. **Code Coverage** : Lines, functions, branches
2. **Code Quality** : Complexity, duplication, smells
3. **Security** : Vulnerabilities, compliance
4. **Performance** : Benchmarks, metrics
5. **Testing** : Test reliability, flakiness
6. **Documentation** : Coverage, completeness

CONTRAINTES :
- CI/CD integration
- Team standards
- Performance impact
- False positive handling
- Continuous improvement

FORMAT DE SORTIE :
1. Quality gates architecture
2. Coverage configuration
3. Quality metrics
4. CI/CD integration
5. Team dashboards
6. Improvement tracking
7. Quality policies

QUALITÉ : Quality gates automatisées, mesurables, actionnables
```

**Configuration Jest avec coverage :**
```json
// jest.config.js
module.exports = {
  testEnvironment: 'jsdom',
  setupFilesAfterEnv: ['<rootDir>/src/setupTests.ts'],
  moduleNameMapping: {
    '\\.(css|less|scss|sass)$': 'identity-obj-proxy',
    '\\.(jpg|jpeg|png|gif|eot|otf|webp|svg|ttf|woff|woff2|mp4|webm|wav|mp3|m4a|aac|oga)$': '<rootDir>/src/__mocks__/fileMock.js'
  },
  collectCoverageFrom: [
    'src/**/*.{js,jsx,ts,tsx}',
    '!src/**/*.d.ts',
    '!src/main.tsx',
    '!src/vite-env.d.ts',
    '!src/setupTests.ts',
    '!src/**/*.stories.{js,jsx,ts,tsx}',
    '!src/**/__tests__/**',
    '!src/**/index.ts'
  ],
  coverageReporters: [
    'text',
    'lcov',
    'html',
    'json-summary'
  ],
  coverageThreshold: {
    global: {
      branches: 80,
      functions: 80,
      lines: 80,
      statements: 80
    },
    './src/components/': {
      branches: 90,
      functions: 90,
      lines: 90,
      statements: 90
    },
    './src/utils/': {
      branches: 95,
      functions: 95,
      lines: 95,
      statements: 95
    }
  },
  testMatch: [
    '<rootDir>/src/**/__tests__/**/*.{js,jsx,ts,tsx}',
    '<rootDir>/src/**/*.{test,spec}.{js,jsx,ts,tsx}'
  ],
  transform: {
    '^.+\\.(js|jsx|ts|tsx)$': 'babel-jest'
  },
  moduleFileExtensions: ['ts', 'tsx', 'js', 'jsx', 'json', 'node'],
  testTimeout: 10000,
  verbose: true,
  clearMocks: true,
  restoreMocks: true
};
```

## 📝 Templates de qualité

### 👥 Template Code Review
```
Tu es un expert code review et quality assurance.

TA TÂCHE : Review [PULL REQUEST] selon [STANDARDS]

CRITÈRES :
- Code quality [FRAMEWORK]
- Security [OWASP]
- Performance [METRICS]
- Testing [COVERAGE]
- Documentation [COMPLETENESS]

CONTRAINTES :
- [LANGUAGE] standards
- Team conventions
- Business requirements
- Performance impact

FORMAT :
1. Review summary
2. Issues by category
3. Code suggestions
4. Security findings
5. Testing recommendations
6. Performance improvements
7. Best practices

QUALITÉ : Review constructive, actionable, comprehensive
```

### 📋 Template Style Guide
```
Tu es un expert coding standards et style guides.

TA TÂCHE : Implémenter style guide pour [LANGUAGE]

STANDARDS :
- Formatting [TOOL]
- Naming conventions
- Import organization
- Comment standards
- File structure

CONTRAINTES :
- Team consistency
- IDE integration
- Git hooks integration
- CI/CD enforcement

FORMAT :
1. Style guide documentation
2. Configuration files
3. IDE integration
4. Git hooks setup
5. CI/CD integration
6. Team adoption
7. Enforcement policies

QUALITÉ : Style guide consistent, automated, adopted
```

### 🧪 Template Test Generation
```
Tu es un expert test automation et generation.

TA TÂCHE : Générer tests pour [CODE]

TEST TYPES :
- Unit tests [FUNCTIONS]
- Integration tests [APIS]
- E2E tests [WORKFLOWS]
- Performance tests [LOAD]
- Security tests [VULNERABILITIES]

CONTRAINTES :
- [FRAMEWORK] testing
- Coverage [PERCENTAGE]
- Maintainability
- CI/CD integration

FORMAT :
1. Testing strategy
2. Unit tests generated
3. Integration tests
4. E2E scenarios
5. Performance tests
6. Security tests
7. Maintenance guidelines

QUALITÉ : Tests comprehensive, automated, maintainable
```

## 🎯 Points clés à retenir

1. **Code Review** : Processus collaboratif, feedback constructif
2. **Style Guides** : Consistance, automatisation, adoption
3. **Git Hooks** : Prévention, automation, qualité
4. **Testing** : Coverage, automation, maintenance
5. **Quality Gates** : Standards, enforcement, amélioration
6. **Team Culture** : Collaboration, standards, continuous improvement

## 🚀 Prochain chapitre

Maintenant que vous maîtrisez la qualité du code, découvrons la génération de tests unitaires et d'intégration.

---

## 📋 Checklist qualité

- ✅ Code review methodology
- ✅ Style guides automation
- ✅ Git hooks configuration
- ✅ Test generation
- ✅ Quality gates setup
- ✅ Team adoption

## 🎯 Test de compréhension

**Question :** Pourquoi les git hooks sont-ils préférables aux outils de CI/CD pour certains checks ?

**Réponse attendue :** Les git hooks s'exécutent localement avant chaque commit/push, offrant un feedback immédiat au développeur, évitant les commits non conformes, et réduisant la charge sur le serveur CI/CD. Ils sont plus rapides pour les checks de base (linting, formatting) et permettent une meilleure expérience développeur.

---

**Temps de lecture estimé : 105 minutes**
**Niveau : Expert**
**Prérequis : Partie I + II + III + IV + Chapitre 1**
