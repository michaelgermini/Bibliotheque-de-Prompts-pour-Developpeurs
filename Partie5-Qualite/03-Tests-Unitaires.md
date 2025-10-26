# 📖 Partie V - Chapitre 3 : Génération de tests unitaires/intégration

## 🎯 Objectif du chapitre

Maîtriser la génération automatisée de tests unitaires et d'intégration, l'optimisation de la couverture de code, et les stratégies de testing avancées pour garantir la qualité logicielle.

## 🧪 Unit Testing : Génération automatisée

### 1. 🏗️ Unit Test Generation Strategy

```
Tu es un expert test generation et unit testing.

TA TÂCHE : Générer des tests unitaires complets pour [CODEBASE]

STRATÉGIE DE TEST UNITAIRE :
1. **Function Testing** : Pure functions, utilities
2. **Class Testing** : Methods, properties, inheritance
3. **Component Testing** : React/Vue/Angular components
4. **Hook Testing** : React hooks, Vue composables
5. **Service Testing** : Business logic, API calls
6. **Utility Testing** : Helpers, formatters, validators

CONTRAINTES :
- Framework : [JEST/PHPUNIT/VITEST]
- Coverage : > 90% functions, > 80% lines
- Mocking : External dependencies
- Edge cases : Error scenarios, boundary conditions
- Maintainability : Clear, readable tests

FORMAT DE SORTIE :
1. Unit testing architecture
2. Test file structure
3. Mocking strategies
4. Edge case coverage
5. Test utilities
6. Coverage optimization
7. Maintenance guidelines

QUALITÉ : Tests unitaires complets, fiables, maintenables
```

**Exemple de génération de tests unitaires TypeScript :**
```typescript
// Generated unit tests for utility functions
import {
  formatCurrency,
  validateEmail,
  calculateDiscount,
  generateSlug,
  parseDate,
  sanitizeInput
} from '../utils/formatters';

describe('Formatters Utils', () => {
  describe('formatCurrency', () => {
    it('should format currency correctly for different locales', () => {
      expect(formatCurrency(1234.56, 'en-US')).toBe('$1,234.56');
      expect(formatCurrency(1234.56, 'fr-FR')).toBe('1 234,56 €');
      expect(formatCurrency(1234.56, 'de-DE')).toBe('1.234,56 €');
    });

    it('should handle edge cases', () => {
      expect(formatCurrency(0, 'en-US')).toBe('$0.00');
      expect(formatCurrency(-100, 'en-US')).toBe('-$100.00');
      expect(formatCurrency(1000000, 'en-US')).toBe('$1,000,000.00');
    });

    it('should throw error for invalid currency', () => {
      expect(() => formatCurrency(100, 'invalid')).toThrow('Invalid currency code');
    });

    it('should handle null and undefined values', () => {
      expect(formatCurrency(null as any, 'en-US')).toBe('$0.00');
      expect(formatCurrency(undefined as any, 'en-US')).toBe('$0.00');
    });
  });

  describe('validateEmail', () => {
    const validEmails = [
      'test@example.com',
      'user.name@domain.co.uk',
      'user+tag@example.com',
      '123@example.com'
    ];

    const invalidEmails = [
      'invalid-email',
      '@example.com',
      'user@',
      'user..name@example.com',
      'user@.com',
      '',
      null,
      undefined
    ];

    it('should validate correct email addresses', () => {
      validEmails.forEach(email => {
        expect(validateEmail(email)).toBe(true);
      });
    });

    it('should reject invalid email addresses', () => {
      invalidEmails.forEach(email => {
        expect(validateEmail(email)).toBe(false);
      });
    });

    it('should handle strict validation mode', () => {
      expect(validateEmail('user@localhost', true)).toBe(false);
      expect(validateEmail('user@example.com', true)).toBe(true);
    });
  });

  describe('calculateDiscount', () => {
    it('should calculate percentage discount correctly', () => {
      expect(calculateDiscount(100, 10)).toBe(90);
      expect(calculateDiscount(50, 50)).toBe(25);
      expect(calculateDiscount(200, 25)).toBe(150);
    });

    it('should handle fixed amount discount', () => {
      expect(calculateDiscount(100, 10, 'fixed')).toBe(90);
      expect(calculateDiscount(50, 10, 'fixed')).toBe(40);
    });

    it('should not go below zero', () => {
      expect(calculateDiscount(10, 50)).toBe(0);
      expect(calculateDiscount(5, 10, 'fixed')).toBe(0);
    });

    it('should handle edge cases', () => {
      expect(calculateDiscount(0, 10)).toBe(0);
      expect(calculateDiscount(100, 0)).toBe(100);
      expect(calculateDiscount(100, 100)).toBe(0);
    });

    it('should throw error for invalid discount type', () => {
      expect(() => calculateDiscount(100, 10, 'invalid' as any)).toThrow('Invalid discount type');
    });
  });

  describe('generateSlug', () => {
    it('should generate URL-friendly slug', () => {
      expect(generateSlug('Hello World!')).toBe('hello-world');
      expect(generateSlug('Product Name 123')).toBe('product-name-123');
      expect(generateSlug('Café & Restaurant')).toBe('cafe-restaurant');
    });

    it('should handle special characters and accents', () => {
      expect(generateSlug('naïve résumé')).toBe('naive-resume');
      expect(generateSlug('Übermensch’s Café')).toBe('ubermenschs-cafe');
    });

    it('should handle empty and whitespace strings', () => {
      expect(generateSlug('')).toBe('');
      expect(generateSlug('   ')).toBe('');
      expect(generateSlug('  Hello  World  ')).toBe('hello-world');
    });

    it('should limit slug length', () => {
      const longTitle = 'A'.repeat(100);
      expect(generateSlug(longTitle).length).toBeLessThanOrEqual(50);
    });
  });

  describe('parseDate', () => {
    it('should parse ISO date strings', () => {
      const date = parseDate('2024-01-15T10:30:00Z');
      expect(date).toEqual(new Date('2024-01-15T10:30:00Z'));
    });

    it('should parse common date formats', () => {
      expect(parseDate('2024-01-15')).toEqual(new Date('2024-01-15'));
      expect(parseDate('01/15/2024')).toEqual(new Date('2024-01-15'));
      expect(parseDate('15 Jan 2024')).toEqual(new Date('2024-01-15'));
    });

    it('should handle invalid dates', () => {
      expect(parseDate('invalid')).toBeNull();
      expect(parseDate('')).toBeNull();
      expect(parseDate(null)).toBeNull();
    });

    it('should handle timezone conversion', () => {
      const date = parseDate('2024-01-15T10:30:00Z', 'America/New_York');
      expect(date.getTimezoneOffset()).toBeDefined();
    });
  });

  describe('sanitizeInput', () => {
    it('should remove HTML tags', () => {
      expect(sanitizeInput('<script>alert("xss")</script>')).toBe('');
      expect(sanitizeInput('<p>Hello <strong>World</strong></p>')).toBe('Hello World');
    });

    it('should escape special characters', () => {
      expect(sanitizeInput('Hello & World')).toBe('Hello & World');
      expect(sanitizeInput('Quote "test"')).toBe('Quote "test"');
    });

    it('should handle various input types', () => {
      expect(sanitizeInput(123)).toBe('123');
      expect(sanitizeInput(true)).toBe('true');
      expect(sanitizeInput(null)).toBe('');
      expect(sanitizeInput(undefined)).toBe('');
    });

    it('should preserve safe content', () => {
      expect(sanitizeInput('Normal text')).toBe('Normal text');
      expect(sanitizeInput('Numbers 123')).toBe('Numbers 123');
    });
  });
});
```

### 2. 🏭 Test Data Generation

```
Tu es un expert test data management et fixtures.

TA TÂCHE : Générer des données de test et fixtures pour [APPLICATION]

DONNÉES DE TEST À GÉNÉRER :
1. **User Data** : Realistic user profiles, roles, permissions
2. **Product Data** : Catalog, variants, categories
3. **Order Data** : Shopping carts, orders, payments
4. **Content Data** : Articles, comments, media
5. **Configuration Data** : Settings, preferences
6. **Edge Cases** : Boundary values, error scenarios

CONTRAINTES :
- Realistic data generation
- GDPR compliance (fake personal data)
- Performance optimization
- Database seeding
- Test isolation

FORMAT DE SORTIE :
1. Test data architecture
2. Factory patterns
3. Faker integration
4. Database seeders
5. Fixture files
6. Data cleanup
7. Performance optimization

QUALITÉ : Données de test réalistes, complètes, performantes
```

**Exemple de factories PHP :**
```php
<?php

// tests/Factories/UserFactory.php
class UserFactory
{
    private Faker\Generator $faker;

    public function __construct()
    {
        $this->faker = Faker\Factory::create();
    }

    public function create(array $attributes = []): User
    {
        return User::create(array_merge([
            'email' => $this->faker->unique()->safeEmail(),
            'first_name' => $this->faker->firstName(),
            'last_name' => $this->faker->lastName(),
            'phone' => $this->faker->phoneNumber(),
            'password' => bcrypt('password'),
            'is_active' => true,
            'email_verified_at' => now(),
            'created_at' => now(),
            'updated_at' => now(),
        ], $attributes));
    }

    public function createAdmin(array $attributes = []): User
    {
        return $this->create(array_merge([
            'email' => 'admin@' . $this->faker->domainName(),
            'role' => 'admin',
            'permissions' => ['*'],
        ], $attributes));
    }

    public function createInactive(array $attributes = []): User
    {
        return $this->create(array_merge([
            'is_active' => false,
            'email_verified_at' => null,
        ], $attributes));
    }

    public function createWithOrders(int $orderCount = 3): User
    {
        $user = $this->create();

        // Create associated orders
        $orderFactory = new OrderFactory();
        $orderFactory->createMany($orderCount, ['user_id' => $user->id]);

        return $user;
    }

    public function createMany(int $count, array $attributes = []): Collection
    {
        $users = collect();

        for ($i = 0; $i < $count; $i++) {
            $users->push($this->create($attributes));
        }

        return $users;
    }
}

// tests/Factories/ProductFactory.php
class ProductFactory
{
    private Faker\Generator $faker;
    private CategoryFactory $categoryFactory;

    public function __construct()
    {
        $this->faker = Faker\Factory::create();
        $this->categoryFactory = new CategoryFactory();
    }

    public function create(array $attributes = []): Product
    {
        // Ensure category exists
        if (!isset($attributes['category_id'])) {
            $category = $this->categoryFactory->create();
            $attributes['category_id'] = $category->id;
        }

        return Product::create(array_merge([
            'sku' => $this->generateSku(),
            'name' => $this->faker->productName(),
            'description' => $this->faker->paragraph(3),
            'short_description' => $this->faker->sentence(10),
            'price' => $this->faker->randomFloat(2, 10, 1000),
            'compare_at_price' => $this->faker->optional(0.3)->randomFloat(2, 100, 2000),
            'cost_price' => $this->faker->randomFloat(2, 5, 500),
            'track_quantity' => $this->faker->boolean(80),
            'quantity' => $this->faker->numberBetween(0, 1000),
            'low_stock_threshold' => $this->faker->numberBetween(5, 50),
            'brand' => $this->faker->optional(0.7)->company(),
            'status' => $this->faker->randomElement(['active', 'inactive', 'draft']),
            'weight' => $this->faker->optional(0.6)->randomFloat(2, 0.1, 50),
            'created_at' => now(),
            'updated_at' => now(),
        ], $attributes));
    }

    public function createWithVariants(int $variantCount = 3): Product
    {
        $product = $this->create(['track_quantity' => false]);

        // Create variants
        $variantFactory = new ProductVariantFactory();
        $variantFactory->createMany($variantCount, ['product_id' => $product->id]);

        return $product;
    }

    public function createOutOfStock(): Product
    {
        return $this->create([
            'track_quantity' => true,
            'quantity' => 0,
            'status' => 'active'
        ]);
    }

    private function generateSku(): string
    {
        return strtoupper(
            $this->faker->randomElement(['PRD', 'ITEM', 'PROD']) . '-' .
            $this->faker->numberBetween(1000, 9999)
        );
    }
}
```

## 🔗 Integration Testing : API et Services

### 1. 🏗️ Integration Test Architecture

```
Tu es un expert integration testing et API testing.

TA TÂCHE : Créer des tests d'intégration complets pour [APPLICATION]

TESTS D'INTÉGRATION :
1. **API Endpoints** : HTTP requests, responses, status codes
2. **Database Integration** : CRUD operations, transactions
3. **External Services** : Third-party APIs, webhooks
4. **Authentication** : JWT, OAuth, sessions
5. **File Operations** : Upload, download, processing
6. **Background Jobs** : Queue processing, async tasks

CONTRAINTES :
- Database transactions
- Test isolation
- Mock external services
- Performance optimization
- CI/CD compatibility

FORMAT DE SORTIE :
1. Integration testing strategy
2. API endpoint tests
3. Database integration tests
4. Service integration tests
5. Mock strategies
6. Test data management
7. Performance considerations

QUALITÉ : Tests d'intégration fiables, isolés, performants
```

**Exemple de tests d'intégration API :**
```typescript
// tests/integration/api/products.test.ts
import request from 'supertest';
import { app } from '../../src/app';
import { setupTestDatabase, teardownTestDatabase } from '../utils/testDatabase';
import { createTestUser, createTestCategory } from '../factories';
import { User } from '../../src/models/User';
import { Category } from '../../src/models/Category';
import { Product } from '../../src/models/Product';

describe('Product API Integration', () => {
  let testUser: User;
  let adminUser: User;
  let testCategory: Category;
  let authToken: string;
  let adminToken: string;

  beforeAll(async () => {
    await setupTestDatabase();

    // Create test users
    testUser = await createTestUser({ role: 'user' });
    adminUser = await createTestUser({ role: 'admin' });
    testCategory = await createTestCategory();

    // Generate auth tokens
    authToken = generateJWT(testUser);
    adminToken = generateJWT(adminUser);
  });

  afterAll(async () => {
    await teardownTestDatabase();
  });

  beforeEach(async () => {
    // Clean up products before each test
    await Product.destroy({ where: {} });
  });

  describe('GET /api/products', () => {
    beforeEach(async () => {
      // Create test products
      await Product.bulkCreate([
        {
          sku: 'TEST-001',
          name: 'Test Product 1',
          description: 'Test Description 1',
          price: 99.99,
          categoryId: testCategory.id,
          status: 'active'
        },
        {
          sku: 'TEST-002',
          name: 'Test Product 2',
          description: 'Test Description 2',
          price: 149.99,
          categoryId: testCategory.id,
          status: 'active'
        }
      ]);
    });

    it('should return products with pagination', async () => {
      const response = await request(app)
        .get('/api/products')
        .expect(200);

      expect(response.body.success).toBe(true);
      expect(response.body.data).toHaveLength(2);
      expect(response.body.meta.pagination).toBeDefined();
      expect(response.body.data[0]).toHaveProperty('id');
      expect(response.body.data[0]).toHaveProperty('name');
      expect(response.body.data[0]).toHaveProperty('price');
    });

    it('should filter products by category', async () => {
      const response = await request(app)
        .get(`/api/products?category=${testCategory.id}`)
        .expect(200);

      expect(response.body.success).toBe(true);
      expect(response.body.data).toHaveLength(2);
      response.body.data.forEach((product: any) => {
        expect(product.categoryId).toBe(testCategory.id);
      });
    });

    it('should search products by name', async () => {
      const response = await request(app)
        .get('/api/products?search=Product 1')
        .expect(200);

      expect(response.body.success).toBe(true);
      expect(response.body.data).toHaveLength(1);
      expect(response.body.data[0].name).toBe('Test Product 1');
    });

    it('should sort products by price', async () => {
      const response = await request(app)
        .get('/api/products?sort=price&order=asc')
        .expect(200);

      expect(response.body.success).toBe(true);
      expect(response.body.data).toHaveLength(2);
      expect(response.body.data[0].price).toBeLessThanOrEqual(response.body.data[1].price);
    });
  });

  describe('POST /api/products', () => {
    it('should create product for admin users', async () => {
      const productData = {
        sku: 'NEW-PRODUCT',
        name: 'New Test Product',
        description: 'New test description',
        price: 199.99,
        categoryId: testCategory.id,
        status: 'active'
      };

      const response = await request(app)
        .post('/api/products')
        .set('Authorization', `Bearer ${adminToken}`)
        .send(productData)
        .expect(201);

      expect(response.body.success).toBe(true);
      expect(response.body.data.name).toBe(productData.name);
      expect(response.body.data.sku).toBe(productData.sku);

      // Verify in database
      const createdProduct = await Product.findByPk(response.body.data.id);
      expect(createdProduct).toBeTruthy();
      expect(createdProduct?.sku).toBe(productData.sku);
    });

    it('should reject product creation for regular users', async () => {
      const productData = {
        sku: 'UNAUTHORIZED',
        name: 'Unauthorized Product',
        description: 'Should fail',
        price: 99.99,
        categoryId: testCategory.id
      };

      await request(app)
        .post('/api/products')
        .set('Authorization', `Bearer ${authToken}`)
        .send(productData)
        .expect(403);
    });

    it('should validate required fields', async () => {
      const invalidData = {
        description: 'Missing required fields'
        // Missing sku, name, price, categoryId
      };

      const response = await request(app)
        .post('/api/products')
        .set('Authorization', `Bearer ${adminToken}`)
        .send(invalidData)
        .expect(400);

      expect(response.body.success).toBe(false);
      expect(response.body.errors).toBeDefined();
      expect(response.body.errors.length).toBeGreaterThan(0);
    });

    it('should reject duplicate SKU', async () => {
      const existingProduct = await Product.create({
        sku: 'DUPLICATE-SKU',
        name: 'Existing Product',
        price: 100,
        categoryId: testCategory.id,
        status: 'active'
      });

      const duplicateData = {
        sku: 'DUPLICATE-SKU',
        name: 'Duplicate Product',
        price: 150,
        categoryId: testCategory.id
      };

      const response = await request(app)
        .post('/api/products')
        .set('Authorization', `Bearer ${adminToken}`)
        .send(duplicateData)
        .expect(400);

      expect(response.body.success).toBe(false);
      expect(response.body.message).toContain('unique');
    });
  });

  describe('PUT /api/products/:id', () => {
    let testProduct: Product;

    beforeEach(async () => {
      testProduct = await Product.create({
        sku: 'UPDATE-TEST',
        name: 'Original Product',
        description: 'Original description',
        price: 100,
        categoryId: testCategory.id,
        status: 'active'
      });
    });

    it('should update product for admin users', async () => {
      const updateData = {
        name: 'Updated Product',
        price: 150,
        status: 'active'
      };

      const response = await request(app)
        .put(`/api/products/${testProduct.id}`)
        .set('Authorization', `Bearer ${adminToken}`)
        .send(updateData)
        .expect(200);

      expect(response.body.success).toBe(true);
      expect(response.body.data.name).toBe(updateData.name);
      expect(response.body.data.price).toBe(updateData.price);

      // Verify in database
      const updatedProduct = await Product.findByPk(testProduct.id);
      expect(updatedProduct?.name).toBe(updateData.name);
      expect(updatedProduct?.price).toBe(updateData.price);
    });

    it('should return 404 for non-existent product', async () => {
      await request(app)
        .put('/api/products/99999')
        .set('Authorization', `Bearer ${adminToken}`)
        .send({ name: 'Updated' })
        .expect(404);
    });

    it('should reject update for regular users', async () => {
      await request(app)
        .put(`/api/products/${testProduct.id}`)
        .set('Authorization', `Bearer ${authToken}`)
        .send({ name: 'Unauthorized Update' })
        .expect(403);
    });
  });

  describe('DELETE /api/products/:id', () => {
    let testProduct: Product;

    beforeEach(async () => {
      testProduct = await Product.create({
        sku: 'DELETE-TEST',
        name: 'Product to Delete',
        price: 50,
        categoryId: testCategory.id,
        status: 'active'
      });
    });

    it('should delete product for admin users', async () => {
      await request(app)
        .delete(`/api/products/${testProduct.id}`)
        .set('Authorization', `Bearer ${adminToken}`)
        .expect(204);

      // Verify deletion
      const deletedProduct = await Product.findByPk(testProduct.id);
      expect(deletedProduct).toBeNull();
    });

    it('should return 404 for non-existent product', async () => {
      await request(app)
        .delete('/api/products/99999')
        .set('Authorization', `Bearer ${adminToken}`)
        .expect(404);
    });

    it('should reject delete for regular users', async () => {
      await request(app)
        .delete(`/api/products/${testProduct.id}`)
        .set('Authorization', `Bearer ${authToken}`)
        .expect(403);
    });
  });

  describe('Authentication and Authorization', () => {
    it('should require authentication for protected endpoints', async () => {
      await request(app)
        .post('/api/products')
        .expect(401);
    });

    it('should require admin role for write operations', async () => {
      const productData = {
        sku: 'AUTH-TEST',
        name: 'Auth Test Product',
        price: 100,
        categoryId: testCategory.id
      };

      await request(app)
        .post('/api/products')
        .set('Authorization', `Bearer ${authToken}`)
        .send(productData)
        .expect(403);
    });

    it('should allow read operations for authenticated users', async () => {
      await request(app)
        .get('/api/products')
        .set('Authorization', `Bearer ${authToken}`)
        .expect(200);
    });
  });

  describe('Error Handling', () => {
    it('should handle database connection errors gracefully', async () => {
      // Simulate database disconnection
      await teardownTestDatabase();

      const response = await request(app)
        .get('/api/products')
        .set('Authorization', `Bearer ${adminToken}`)
        .expect(500);

      expect(response.body.success).toBe(false);
      expect(response.body.message).toContain('database');

      // Restore database
      await setupTestDatabase();
    });

    it('should handle malformed JSON', async () => {
      const response = await request(app)
        .post('/api/products')
        .set('Authorization', `Bearer ${adminToken}`)
        .set('Content-Type', 'application/json')
        .send('{ invalid json }')
        .expect(400);

      expect(response.body.success).toBe(false);
      expect(response.body.message).toContain('json');
    });

    it('should handle rate limiting', async () => {
      const productData = {
        sku: 'RATE-TEST',
        name: 'Rate Test Product',
        price: 100,
        categoryId: testCategory.id
      };

      // Make multiple requests quickly
      const requests = Array(10).fill(null).map(() =>
        request(app)
          .post('/api/products')
          .set('Authorization', `Bearer ${adminToken}`)
          .send(productData)
      );

      const responses = await Promise.all(requests);

      // At least one should be rate limited
      const rateLimitedResponse = responses.find(r => r.status === 429);
      expect(rateLimitedResponse).toBeDefined();
    });
  });
});
```

### 2. 🗄️ Database Integration Testing

```
Tu es un expert database testing et transaction management.

TA TÂCHE : Créer des tests d'intégration base de données pour [APPLICATION]

TESTS BASE DE DONNÉES :
1. **CRUD Operations** : Create, read, update, delete
2. **Relationships** : Foreign keys, associations, joins
3. **Transactions** : Rollback, commit, isolation
4. **Migrations** : Schema changes, data preservation
5. **Constraints** : Validation, triggers, stored procedures
6. **Performance** : Query optimization, indexing

CONTRAINTES :
- Test isolation (transactions)
- Database cleanup
- Performance optimization
- Realistic test data
- Edge case coverage

FORMAT DE SORTIE :
1. Database testing strategy
2. Transaction management
3. Test data setup
4. CRUD operation tests
5. Relationship tests
6. Performance tests
7. Cleanup procedures

QUALITÉ : Tests base de données isolés, complets, performants
```

## 📊 Test Coverage Optimization

### 1. 🎯 Coverage Analysis et Optimization

```
Tu es un expert test coverage et quality metrics.

TA TÂCHE : Optimiser la couverture de test pour [CODEBASE]

ANALYSE DE COUVERTURE :
1. **Line Coverage** : Code lines executed
2. **Function Coverage** : Functions called
3. **Branch Coverage** : Conditional branches
4. **Statement Coverage** : Code statements
5. **Path Coverage** : Execution paths
6. **Mutation Coverage** : Code change detection

CONTRAINTES :
- Coverage thresholds
- Performance impact
- Maintenance cost
- Team standards
- Business requirements

FORMAT DE SORTIE :
1. Coverage analysis report
2. Gap identification
3. Test generation plan
4. Coverage optimization
5. Quality gates
6. Progress tracking
7. Maintenance strategy

QUALITÉ : Couverture de test optimale, mesurable, maintenable
```

**Configuration coverage avancée :**
```json
// jest.config.js
module.exports = {
  // ... other config
  collectCoverage: true,
  collectCoverageFrom: [
    'src/**/*.{js,jsx,ts,tsx}',
    '!src/**/*.d.ts',
    '!src/**/__tests__/**',
    '!src/**/index.ts',
    '!src/main.tsx'
  ],
  coverageReporters: [
    'text',
    'lcov',
    'html',
    'json-summary',
    'cobertura'
  ],
  coverageThreshold: {
    global: {
      branches: 85,
      functions: 85,
      lines: 85,
      statements: 85
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
    },
    './src/services/': {
      branches: 80,
      functions: 80,
      lines: 80,
      statements: 80
    }
  },
  coverageDirectory: 'coverage',
  coverageReporters: [
    'text',
    'lcov',
    'html',
    'json-summary'
  ],
  // Ignore coverage for certain patterns
  coveragePathIgnorePatterns: [
    '/node_modules/',
    '/coverage/',
    '/dist/',
    '\\.stories\\.',
    '\\.test\\.',
    '\\.spec\\.'
  ]
};
```

### 2. 🧬 Mutation Testing

```
Tu es un expert mutation testing et test quality.

TA TÂCHE : Implémenter mutation testing pour [CODEBASE]

MUTATION TESTING :
1. **Code Mutations** : Introduce bugs artificially
2. **Test Detection** : Verify tests catch mutations
3. **Quality Score** : Mutation detection rate
4. **Gap Analysis** : Weak test identification
5. **Test Improvement** : Enhanced test strategies

CONTRAINTES :
- Stryker/PIT tools
- Performance optimization
- False positive handling
- CI/CD integration
- Team adoption

FORMAT DE SORTIE :
1. Mutation testing setup
2. Configuration files
3. Test quality analysis
4. Gap identification
5. Test improvements
6. Quality metrics
7. CI/CD integration

QUALITÉ : Tests de qualité, gaps identifiés, améliorés
```

## 📝 Templates de tests

### 🧪 Template Unit Testing
```
Tu es un expert unit testing et [FRAMEWORK].

TA TÂCHE : Générer tests unitaires pour [CODE]

COMPOSANTS :
- Functions et utilities
- Classes et methods
- Components [REACT/VUE/ANGULAR]
- Hooks et composables
- Services et business logic

CONTRAINTES :
- [FRAMEWORK] testing
- Coverage [THRESHOLDS]
- Mocking strategies
- Edge case coverage
- Test maintainability

FORMAT :
1. Testing architecture
2. Test file structure
3. Mocking strategies
4. Edge case tests
5. Test utilities
6. Coverage optimization
7. Maintenance guidelines

QUALITÉ : Tests unitaires complets, fiables, maintenables
```

### 🔗 Template Integration Testing
```
Tu es un expert integration testing et API testing.

TA TÂCHE : Créer tests d'intégration pour [APPLICATION]

TESTS :
- API endpoints
- Database operations
- External services
- Authentication flows
- File operations

CONTRAINTES :
- Test isolation
- Database transactions
- Mock strategies
- Performance optimization

FORMAT :
1. Integration strategy
2. API endpoint tests
3. Database tests
4. Service integration
5. Mock strategies
6. Performance tests
7. Cleanup procedures

QUALITÉ : Tests d'intégration fiables, isolés, performants
```

### 📊 Template Test Coverage
```
Tu es un expert test coverage et quality metrics.

TA TÂCHE : Optimiser coverage pour [CODEBASE]

MÉTRIQUES :
- Line coverage
- Function coverage
- Branch coverage
- Mutation coverage

CONTRAINTES :
- Coverage thresholds
- Performance impact
- Maintenance cost
- Quality gates

FORMAT :
1. Coverage analysis
2. Gap identification
3. Test generation
4. Coverage optimization
5. Quality gates
6. Progress tracking
7. Maintenance strategy

QUALITÉ : Couverture optimale, mesurable, maintenable
```

## 🎯 Points clés à retenir

1. **Unit Testing** : Isolation, rapidité, fiabilité
2. **Integration Testing** : Composants ensemble, API, database
3. **Test Coverage** : Mesurable, optimisée, complète
4. **Test Data** : Réaliste, complète, performante
5. **Mutation Testing** : Qualité des tests, gaps identifiés
6. **CI/CD Integration** : Automatisé, rapide, fiable

## 🚀 Conclusion de la Partie V

Vous avez maintenant toutes les compétences pour garantir la qualité du code à travers des tests complets et des processus de review optimisés. Passons maintenant à la data, l'IA et l'automatisation.

---

## 📋 Checklist qualité

- ✅ Unit testing generation
- ✅ Integration testing architecture
- ✅ Test coverage optimization
- ✅ Mutation testing setup
- ✅ Test data management
- ✅ Quality metrics

## 🎯 Test de compréhension

**Question :** Pourquoi les tests d'intégration sont-ils différents des tests unitaires ?

**Réponse attendue :** Les tests unitaires isolent les composants individuels (fonctions, classes) avec des mocks, testant leur comportement interne. Les tests d'intégration testent les composants ensemble (API, database, services externes), validant les interactions et l'intégration système, sans mocks pour les dépendances internes.

---

**Temps de lecture estimé : 110 minutes**
**Niveau : Expert**
**Prérequis : Partie I + II + III + IV + Chapitres 1, 2**

---

🎉 **Félicitations !** Vous avez terminé la Partie V - Gestion et Qualité du Code.

**Prochaines étapes :** Partie VI - Data, IA & Automatisation

---

## 📊 Synthèse des acquis

### ✅ Compétences acquises :
- Refactoring et clean code
- Design patterns (Creational, Structural, Behavioral)
- SOLID principles
- Code review assistée par IA
- Style guides automatisés
- Tests unitaires et d'intégration
- Test coverage optimization

### 🎯 Niveau atteint :
- **Code Quality** : Expert
- **Testing** : Expert
- **Architecture** : Expert
- **Clean Code** : Expert

### 🚀 Prêt pour :
- Data science et IA
- Automation et workflows
- API integrations
- Advanced development practices
- Team leadership
- System design
