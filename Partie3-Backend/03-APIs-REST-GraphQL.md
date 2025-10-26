# 📖 Partie III - Chapitre 3 : APIs REST & GraphQL

## 🎯 Objectif du chapitre

Maîtriser la conception, l'implémentation et la documentation d'APIs REST et GraphQL modernes, avec des tests automatisés complets.

## 🌐 API REST : Design et Implementation

### 1. 🏗️ REST API Architecture moderne

```
Tu es un expert API REST et OpenAPI specification.

TA TÂCHE : Concevoir et implémenter une API REST complète pour [APPLICATION]

SPÉCIFICATIONS REST :
- RESTful conventions (Richardson Maturity Model Level 3)
- Resource-based URLs (/api/v1/users, /api/v1/products/{id})
- HTTP methods appropriés (GET, POST, PUT, PATCH, DELETE)
- HTTP status codes sémantiques (200, 201, 204, 400, 404, 500)
- HATEOAS (Hypermedia as the Engine of Application State)
- Content negotiation (JSON, XML)

CONTRAINTES :
- Framework : [EXPRESS/LARAVEL/FASTAPI]
- Authentication : JWT/OAuth2
- Rate limiting et throttling
- CORS configuration
- API versioning strategy
- Error handling standardisé

FORMAT DE SORTIE :
1. API design documentée (OpenAPI/Swagger)
2. Resource modeling et relationships
3. Route handlers avec validation
4. Authentication middleware
5. Error handling et responses
6. Tests API complets (unit, integration)
7. Documentation interactive (Swagger UI)

QUALITÉ : API REST mature, documentée, testée
```

**OpenAPI Specification Example :**
```yaml
openapi: 3.0.3
info:
  title: E-commerce API
  description: Modern REST API for e-commerce platform
  version: 1.0.0
  contact:
    name: API Support
    email: api@ecommerce.com

servers:
  - url: https://api.ecommerce.com/v1
    description: Production server
  - url: https://staging.api.ecommerce.com/v1
    description: Staging server

security:
  - bearerAuth: []

paths:
  /products:
    get:
      summary: Get products with filtering
      description: Retrieve products with pagination, filtering, and search
      operationId: getProducts
      parameters:
        - name: page
          in: query
          description: Page number
          required: false
          schema:
            type: integer
            minimum: 1
            default: 1
        - name: limit
          in: query
          description: Items per page
          required: false
          schema:
            type: integer
            minimum: 1
            maximum: 100
            default: 20
        - name: category
          in: query
          description: Filter by category ID
          required: false
          schema:
            type: integer
        - name: search
          in: query
          description: Search in product name and description
          required: false
          schema:
            type: string
            minLength: 1
            maxLength: 255
        - name: sort
          in: query
          description: Sort field
          required: false
          schema:
            type: string
            enum: [name, price, created_at]
        - name: order
          in: query
          description: Sort order
          required: false
          schema:
            type: string
            enum: [asc, desc]
      responses:
        '200':
          description: Successful response
          content:
            application/json:
              schema:
                type: object
                properties:
                  data:
                    type: array
                    items:
                      $ref: '#/components/schemas/Product'
                  meta:
                    $ref: '#/components/schemas/PaginationMeta'
                  links:
                    $ref: '#/components/schemas/PaginationLinks'
        '400':
          $ref: '#/components/responses/BadRequest'
        '401':
          $ref: '#/components/responses/Unauthorized'
        '500':
          $ref: '#/components/responses/InternalServerError'

  /products/{id}:
    get:
      summary: Get product by ID
      description: Retrieve a specific product by its ID
      operationId: getProduct
      parameters:
        - name: id
          in: path
          required: true
          description: Product ID
          schema:
            type: integer
      responses:
        '200':
          description: Product found
          content:
            application/json:
              schema:
                type: object
                properties:
                  data:
                    $ref: '#/components/schemas/Product'
        '404':
          description: Product not found
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Error'

    post:
      summary: Create new product
      description: Create a new product (Admin only)
      operationId: createProduct
      security:
        - bearerAuth: []
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/ProductCreate'
      responses:
        '201':
          description: Product created
          content:
            application/json:
              schema:
                type: object
                properties:
                  message:
                    type: string
                    example: "Product created successfully"
                  data:
                    $ref: '#/components/schemas/Product'
        '400':
          $ref: '#/components/responses/BadRequest'
        '401':
          $ref: '#/components/responses/Unauthorized'
        '403':
          $ref: '#/components/responses/Forbidden'

components:
  schemas:
    Product:
      type: object
      properties:
        id:
          type: integer
          description: Product ID
        name:
          type: string
          description: Product name
        description:
          type: string
          description: Product description
        price:
          type: number
          format: float
          description: Product price
        category_id:
          type: integer
          description: Category ID
        status:
          type: string
          enum: [active, inactive, draft]
        created_at:
          type: string
          format: date-time
        updated_at:
          type: string
          format: date-time

    PaginationMeta:
      type: object
      properties:
        current_page:
          type: integer
        per_page:
          type: integer
        total:
          type: integer
        last_page:
          type: integer

    Error:
      type: object
      properties:
        message:
          type: string
        errors:
          type: object
          additionalProperties: true

  responses:
    BadRequest:
      description: Bad request
      content:
        application/json:
          schema:
            $ref: '#/components/schemas/Error'

    Unauthorized:
      description: Unauthorized
      content:
        application/json:
          schema:
            $ref: '#/components/schemas/Error'

    Forbidden:
      description: Forbidden
      content:
        application/json:
          schema:
            $ref: '#/components/schemas/Error'

    InternalServerError:
      description: Internal server error
      content:
        application/json:
          schema:
            $ref: '#/components/schemas/Error'

  securitySchemes:
    bearerAuth:
      type: http
      scheme: bearer
      bearerFormat: JWT
```

### 2. 🚀 Implementation REST API

```
Tu es un expert implémentation API REST avec [FRAMEWORK].

TA TÂCHE : Implémenter les endpoints REST avec validation et tests

ENDPOINTS À IMPLÉMENTER :
1. **CRUD Operations** : Create, Read, Update, Delete
2. **Filtering & Search** : Query parameters, full-text search
3. **Pagination** : Offset/limit, cursor-based
4. **Sorting** : Multiple fields, directions
5. **Validation** : Input sanitization, business rules
6. **Error Handling** : Consistent error responses

CONTRAINTES :
- RESTful URL design
- HTTP status codes appropriés
- JSON response format
- Input validation robuste
- Rate limiting
- CORS headers

FORMAT DE SORTIE :
1. Route handlers implementation
2. Request validation schemas
3. Response transformers
4. Error handling middleware
5. Tests API complets
6. Postman collection
7. API documentation

QUALITÉ : API REST robuste, validée, documentée
```

**Exemple d'implémentation Express :**
```typescript
// src/routes/products.ts
import express, { Request, Response, NextFunction } from 'express';
import { body, param, query, validationResult } from 'express-validator';
import { ProductService } from '../services/ProductService';
import { authenticateToken } from '../middleware/auth';
import { authorizeRoles } from '../middleware/authorization';
import { rateLimit } from '../middleware/rateLimit';
import { asyncHandler } from '../utils/asyncHandler';

const router = express.Router();
const productService = new ProductService();

// Validation middleware
const validateProduct = [
  body('name').trim().isLength({ min: 1, max: 255 }).withMessage('Product name is required'),
  body('description').trim().isLength({ max: 2000 }).withMessage('Description too long'),
  body('price').isFloat({ min: 0 }).withMessage('Price must be positive'),
  body('categoryId').isInt({ min: 1 }).withMessage('Valid category ID required'),
  body('status').optional().isIn(['active', 'inactive', 'draft']),
];

const validateProductId = [
  param('id').isInt({ min: 1 }).withMessage('Valid product ID required'),
];

// GET /api/products - Get all products with filtering
router.get('/', [
  query('page').optional().isInt({ min: 1 }),
  query('limit').optional().isInt({ min: 1, max: 100 }),
  query('category').optional().isInt({ min: 1 }),
  query('search').optional().isLength({ min: 1, max: 255 }),
  query('sort').optional().isIn(['name', 'price', 'created_at']),
  query('order').optional().isIn(['asc', 'desc']),
], asyncHandler(async (req: Request, res: Response) => {
  const errors = validationResult(req);
  if (!errors.isEmpty()) {
    return res.status(400).json({
      success: false,
      message: 'Validation failed',
      errors: errors.array()
    });
  }

  const {
    page = 1,
    limit = 20,
    category,
    search,
    sort = 'created_at',
    order = 'desc'
  } = req.query;

  const filters = {
    category: category ? parseInt(category as string) : undefined,
    search: search as string,
  };

  const result = await productService.getProducts({
    page: parseInt(page as string),
    limit: parseInt(limit as string),
    filters,
    sort: sort as string,
    order: order as string
  });

  res.json({
    success: true,
    data: result.products,
    meta: {
      pagination: result.pagination,
      filters: result.filters
    }
  });
}));

// GET /api/products/:id - Get single product
router.get('/:id', validateProductId, asyncHandler(async (req: Request, res: Response) => {
  const errors = validationResult(req);
  if (!errors.isEmpty()) {
    return res.status(400).json({
      success: false,
      message: 'Validation failed',
      errors: errors.array()
    });
  }

  const product = await productService.getProductById(parseInt(req.params.id));

  if (!product) {
    return res.status(404).json({
      success: false,
      message: 'Product not found'
    });
  }

  res.json({
    success: true,
    data: product
  });
}));

// POST /api/products - Create new product (Admin only)
router.post('/', [
  authenticateToken,
  authorizeRoles(['admin']),
  rateLimit({ windowMs: 15 * 60 * 1000, max: 10 }),
  ...validateProduct,
], asyncHandler(async (req: Request, res: Response) => {
  const errors = validationResult(req);
  if (!errors.isEmpty()) {
    return res.status(400).json({
      success: false,
      message: 'Validation failed',
      errors: errors.array()
    });
  }

  const productData = req.body;
  const product = await productService.createProduct(productData);

  res.status(201).json({
    success: true,
    message: 'Product created successfully',
    data: product
  });
}));

// PUT /api/products/:id - Update product (Admin only)
router.put('/:id', [
  authenticateToken,
  authorizeRoles(['admin']),
  ...validateProductId,
  ...validateProduct,
], asyncHandler(async (req: Request, res: Response) => {
  const errors = validationResult(req);
  if (!errors.isEmpty()) {
    return res.status(400).json({
      success: false,
      message: 'Validation failed',
      errors: errors.array()
    });
  }

  const productId = parseInt(req.params.id);
  const productData = req.body;

  const updatedProduct = await productService.updateProduct(productId, productData);

  if (!updatedProduct) {
    return res.status(404).json({
      success: false,
      message: 'Product not found'
    });
  }

  res.json({
    success: true,
    message: 'Product updated successfully',
    data: updatedProduct
  });
}));

// DELETE /api/products/:id - Delete product (Admin only)
router.delete('/:id', [
  authenticateToken,
  authorizeRoles(['admin']),
  ...validateProductId,
], asyncHandler(async (req: Request, res: Response) => {
  const errors = validationResult(req);
  if (!errors.isEmpty()) {
    return res.status(400).json({
      success: false,
      message: 'Validation failed',
      errors: errors.array()
    });
  }

  const deleted = await productService.deleteProduct(parseInt(req.params.id));

  if (!deleted) {
    return res.status(404).json({
      success: false,
      message: 'Product not found'
    });
  }

  res.status(204).send();
}));

export default router;
```

## 🔗 GraphQL : API flexible et typée

### 1. 🏗️ GraphQL Schema Design

```
Tu es un expert GraphQL et Apollo Server.

TA TÂCHE : Concevoir et implémenter un schema GraphQL pour [APPLICATION]

SPÉCIFICATIONS GRAPHQL :
- Schema-first ou code-first approach
- Resolvers avec data loaders
- Subscriptions pour real-time
- Mutations avec validation
- Custom scalars (Date, Email, URL)
- Schema stitching (si multiple services)

CONTRAINTES :
- Framework : Apollo Server, Express-GraphQL
- Database : [SQL/NoSQL] avec optimization
- Authentication : JWT dans context
- Performance : N+1 query prevention
- Error handling : Apollo errors

FORMAT DE SORTIE :
1. GraphQL schema documenté
2. Resolvers implementation
3. Data loaders pour optimization
4. Mutations avec validation
5. Subscriptions setup
6. Tests GraphQL complets
7. GraphQL Playground/Explorer

QUALITÉ : API GraphQL performante, type-safe, scalable
```

**Exemple de schema GraphQL :**
```graphql
# Schema Definition
type Product {
  id: ID!
  name: String!
  description: String
  price: Float!
  category: Category!
  status: ProductStatus!
  images: [ProductImage!]!
  variants: [ProductVariant!]!
  reviews: [Review!]!
  createdAt: DateTime!
  updatedAt: DateTime!
}

type Category {
  id: ID!
  name: String!
  description: String
  products: [Product!]!
  parent: Category
  children: [Category!]!
}

type ProductImage {
  id: ID!
  url: URL!
  alt: String
  isPrimary: Boolean!
}

type Review {
  id: ID!
  rating: Int!
  comment: String
  user: User!
  createdAt: DateTime!
}

enum ProductStatus {
  ACTIVE
  INACTIVE
  DRAFT
}

# Queries
type Query {
  # Get products with filtering and pagination
  products(
    first: Int
    after: String
    filter: ProductFilter
    sort: ProductSort
  ): ProductConnection!

  # Get single product
  product(id: ID!): Product

  # Get product categories
  categories: [Category!]!

  # Search products
  searchProducts(query: String!, filters: ProductFilter): [Product!]!
}

# Mutations
type Mutation {
  # Create product (Admin only)
  createProduct(input: CreateProductInput!): CreateProductPayload!

  # Update product (Admin only)
  updateProduct(id: ID!, input: UpdateProductInput!): UpdateProductPayload!

  # Delete product (Admin only)
  deleteProduct(id: ID!): DeleteProductPayload!

  # Add product review
  addProductReview(productId: ID!, input: AddReviewInput!): AddReviewPayload!
}

# Input types
input ProductFilter {
  categoryId: ID
  status: ProductStatus
  priceMin: Float
  priceMax: Float
  hasReviews: Boolean
}

input ProductSort {
  field: ProductSortField!
  direction: SortDirection!
}

input CreateProductInput {
  name: String!
  description: String
  price: Float!
  categoryId: ID!
  status: ProductStatus = DRAFT
  images: [CreateProductImageInput!]!
}

input UpdateProductInput {
  name: String
  description: String
  price: Float
  categoryId: ID
  status: ProductStatus
}

input AddReviewInput {
  rating: Int!
  comment: String!
}

# Connection types for pagination
type ProductConnection {
  edges: [ProductEdge!]!
  pageInfo: PageInfo!
  totalCount: Int!
}

type ProductEdge {
  node: Product!
  cursor: String!
}

type PageInfo {
  hasNextPage: Boolean!
  hasPreviousPage: Boolean!
  startCursor: String
  endCursor: String
}

# Payload types
type CreateProductPayload {
  product: Product!
  success: Boolean!
  errors: [Error!]
}

type UpdateProductPayload {
  product: Product
  success: Boolean!
  errors: [Error!]
}

type DeleteProductPayload {
  success: Boolean!
  errors: [Error!]
}

type AddReviewPayload {
  review: Review
  success: Boolean!
  errors: [Error!]
}

# Custom scalars
scalar DateTime
scalar URL

# Enums
enum ProductSortField {
  NAME
  PRICE
  CREATED_AT
}

enum SortDirection {
  ASC
  DESC
}
```

### 2. 🚀 GraphQL Resolvers Implementation

```
Tu es un expert GraphQL resolvers et data optimization.

TA TÂCHE : Implémenter les resolvers GraphQL avec optimization

RESOLVERS À IMPLÉMENTER :
1. **Query Resolvers** : Products, categories, search
2. **Mutation Resolvers** : CRUD operations
3. **Field Resolvers** : Relationships (category, images, reviews)
4. **Data Loaders** : N+1 query prevention
5. **Custom Scalars** : DateTime, URL validation
6. **Error Handling** : Apollo errors format

CONTRAINTES :
- Apollo Server best practices
- DataLoader pour batching
- Context pour authentication
- Validation avec Joi
- Performance monitoring

FORMAT DE SORTIE :
1. GraphQL resolvers complets
2. DataLoader implementations
3. Authentication context
4. Validation schemas
5. Error handling
6. Tests GraphQL
7. Performance monitoring

QUALITÉ : Resolvers GraphQL optimisés, sécurisés, testés
```

## 🧪 Tests automatisés : API Testing

### 1. 🧪 API Testing Strategy

```
Tu es un expert API testing et test automation.

TA TÂCHE : Créer une stratégie de testing API complète

STRATÉGIE DE TEST :
1. **Unit Tests** : Individual endpoints, middleware, services
2. **Integration Tests** : API endpoints avec database
3. **Contract Tests** : API schema validation
4. **Performance Tests** : Load testing, response times
5. **Security Tests** : Authentication, authorization, injection
6. **E2E Tests** : Complete user journeys

OUTILS DE TEST :
- **Unit/Integration** : Jest, Supertest, PHPUnit
- **Performance** : Artillery, k6, JMeter
- **Security** : OWASP ZAP, Postman Security
- **Contract** : Pact, Spring Cloud Contract
- **E2E** : Cypress, Playwright

CONTRAINTES :
- Coverage > 90%
- Tests fiables et maintenables
- CI/CD integration
- Fast execution (< 5 minutes)
- Mock strategies pour external services

FORMAT DE SORTIE :
1. Testing architecture et strategy
2. Unit tests (controllers, services)
3. Integration tests (API endpoints)
4. Performance tests setup
5. Security tests configuration
6. E2E tests (user flows)
7. CI/CD pipeline avec tests

QUALITÉ : Tests API complets, automatisés, fiables
```

**Exemple de tests API Express :**
```typescript
// tests/integration/products.test.ts
import request from 'supertest';
import express from 'express';
import { AppDataSource } from '../src/config/database';
import { productRouter } from '../src/routes/products';
import { User } from '../src/models/User';
import { Category } from '../src/models/Category';
import { Product } from '../src/models/Product';

describe('Product API', () => {
  let app: express.Application;
  let adminToken: string;
  let userToken: string;
  let testCategory: Category;
  let testProduct: Product;

  beforeAll(async () => {
    // Setup database
    await AppDataSource.initialize();

    // Setup Express app
    app = express();
    app.use(express.json());
    app.use('/api/products', productRouter);

    // Create test data
    const adminUser = await User.create({
      email: 'admin@test.com',
      password: 'hashedpassword',
      role: 'admin'
    }).save();

    const regularUser = await User.create({
      email: 'user@test.com',
      password: 'hashedpassword',
      role: 'user'
    }).save();

    testCategory = await Category.create({
      name: 'Test Category',
      description: 'Test Description'
    }).save();

    adminToken = generateJWT(adminUser);
    userToken = generateJWT(regularUser);
  });

  afterAll(async () => {
    await AppDataSource.destroy();
  });

  describe('GET /api/products', () => {
    beforeEach(async () => {
      testProduct = await Product.create({
        name: 'Test Product',
        description: 'Test Description',
        price: 99.99,
        category: testCategory,
        status: 'active'
      }).save();
    });

    afterEach(async () => {
      await Product.delete({});
    });

    it('should return products with pagination', async () => {
      const response = await request(app)
        .get('/api/products?page=1&limit=10')
        .expect(200);

      expect(response.body.success).toBe(true);
      expect(response.body.data).toBeDefined();
      expect(response.body.meta.pagination).toBeDefined();
      expect(response.body.data).toHaveLength(1);
    });

    it('should filter products by category', async () => {
      const response = await request(app)
        .get(`/api/products?category=${testCategory.id}`)
        .expect(200);

      expect(response.body.success).toBe(true);
      expect(response.body.data).toHaveLength(1);
      expect(response.body.data[0].category.id).toBe(testCategory.id);
    });

    it('should search products by name', async () => {
      const response = await request(app)
        .get('/api/products?search=Test')
        .expect(200);

      expect(response.body.success).toBe(true);
      expect(response.body.data).toHaveLength(1);
    });
  });

  describe('POST /api/products', () => {
    it('should create product for admin users', async () => {
      const productData = {
        name: 'New Product',
        description: 'New Description',
        price: 49.99,
        categoryId: testCategory.id,
        status: 'active'
      };

      const response = await request(app)
        .post('/api/products')
        .set('Authorization', `Bearer ${adminToken}`)
        .send(productData)
        .expect(201);

      expect(response.body.success).toBe(true);
      expect(response.body.message).toBe('Product created successfully');
      expect(response.body.data.name).toBe(productData.name);
    });

    it('should reject product creation for regular users', async () => {
      const productData = {
        name: 'Unauthorized Product',
        description: 'Should fail',
        price: 29.99,
        categoryId: testCategory.id
      };

      await request(app)
        .post('/api/products')
        .set('Authorization', `Bearer ${userToken}`)
        .send(productData)
        .expect(403);
    });

    it('should validate required fields', async () => {
      const invalidData = {
        description: 'Missing required fields'
        // Missing name, price, categoryId
      };

      const response = await request(app)
        .post('/api/products')
        .set('Authorization', `Bearer ${adminToken}`)
        .send(invalidData)
        .expect(400);

      expect(response.body.success).toBe(false);
      expect(response.body.errors).toBeDefined();
    });
  });

  describe('PUT /api/products/:id', () => {
    beforeEach(async () => {
      testProduct = await Product.create({
        name: 'Original Product',
        description: 'Original Description',
        price: 79.99,
        category: testCategory,
        status: 'active'
      }).save();
    });

    it('should update product for admin users', async () => {
      const updateData = {
        name: 'Updated Product',
        price: 89.99,
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
    });

    it('should return 404 for non-existent product', async () => {
      await request(app)
        .put('/api/products/99999')
        .set('Authorization', `Bearer ${adminToken}`)
        .send({ name: 'Updated' })
        .expect(404);
    });
  });

  describe('DELETE /api/products/:id', () => {
    beforeEach(async () => {
      testProduct = await Product.create({
        name: 'Product to Delete',
        description: 'Will be deleted',
        price: 39.99,
        category: testCategory,
        status: 'active'
      }).save();
    });

    it('should delete product for admin users', async () => {
      await request(app)
        .delete(`/api/products/${testProduct.id}`)
        .set('Authorization', `Bearer ${adminToken}`)
        .expect(204);

      // Verify deletion
      const deletedProduct = await Product.findOne({
        where: { id: testProduct.id }
      });
      expect(deletedProduct).toBeNull();
    });

    it('should return 404 for non-existent product', async () => {
      await request(app)
        .delete('/api/products/99999')
        .set('Authorization', `Bearer ${adminToken}`)
        .expect(404);
    });
  });
});
```

## 📝 Templates d'APIs

### 🌐 Template API REST
```
Tu es un expert API REST et [FRAMEWORK].

TA TÂCHE : Implémenter API REST [ENDPOINTS] avec [FONCTIONNALITÉS]

SPÉCIFICATIONS :
- RESTful conventions
- OpenAPI documentation
- Input validation
- Error handling

CONTRAINTES :
- HTTP status codes appropriés
- JSON responses
- Authentication [METHOD]
- Rate limiting

FORMAT :
1. OpenAPI specification
2. Route handlers
3. Request validation
4. Response transformers
5. Error handling
6. Tests complets
7. Postman collection

QUALITÉ : API REST mature, documentée, testée
```

### 🔗 Template GraphQL
```
Tu es un expert GraphQL et Apollo Server.

TA TÂCHE : Implémenter schema GraphQL pour [APPLICATION]

SPÉCIFICATIONS :
- Schema design
- Resolvers implementation
- Data loaders optimization
- Mutations avec validation

CONTRAINTES :
- Type safety
- Performance optimization
- Authentication context
- Error handling

FORMAT :
1. GraphQL schema
2. Resolvers implementation
3. Data loaders
4. Custom scalars
5. Tests GraphQL
6. Playground setup
7. Performance monitoring

QUALITÉ : API GraphQL performante, type-safe, scalable
```

### 🧪 Template API Testing
```
Tu es un expert API testing et automation.

TA TÂCHE : Créer tests API complets pour [ENDPOINTS]

STRATÉGIE :
- Unit tests (handlers)
- Integration tests (endpoints)
- Contract tests (schema)
- Performance tests (load)

CONTRAINTES :
- Coverage > 90%
- Tests fiables
- CI/CD integration
- Mock strategies

FORMAT :
1. Testing strategy
2. Unit tests
3. Integration tests
4. Performance tests
5. Security tests
6. E2E tests
7. CI/CD pipeline

QUALITÉ : Tests API complets, automatisés, fiables
```

## 🎯 Points clés à retenir

1. **REST** : Ressources, HTTP methods, status codes
2. **GraphQL** : Flexible queries, type safety, introspection
3. **OpenAPI** : Documentation standard, tooling ecosystem
4. **Testing** : Coverage, reliability, automation
5. **Performance** : Optimization, monitoring, caching
6. **Security** : Authentication, validation, rate limiting

## 🚀 Prochain chapitre

Maintenant que vous maîtrisez les APIs, découvrons les bases de données SQL et NoSQL avec leurs optimisations.

---

## 📋 Checklist qualité

- ✅ API REST architecture moderne
- ✅ GraphQL schema et resolvers
- ✅ OpenAPI documentation
- ✅ Tests API automatisés
- ✅ Performance optimization
- ✅ Security best practices

## 🎯 Test de compréhension

**Question :** Quelle est la principale différence entre REST et GraphQL en termes de performance ?

**Réponse attendue :** REST peut causer de l'over-fetching (trop de données) ou under-fetching (pas assez), nécessitant souvent plusieurs endpoints. GraphQL permet de demander exactement les données nécessaires, évitant l'over-fetching, mais peut causer de l'under-fetching si mal utilisé. REST est plus simple pour le caching HTTP, GraphQL nécessite des optimisations spécifiques (DataLoader, caching).

---

**Temps de lecture estimé : 75 minutes**
**Niveau : Expert**
**Prérequis : Partie I + II + Chapitres 1, 2**
