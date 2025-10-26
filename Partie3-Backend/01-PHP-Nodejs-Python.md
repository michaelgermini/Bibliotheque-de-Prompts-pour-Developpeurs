# 📖 Partie III - Chapitre 1 : PHP / Node.js / Python

## 🎯 Objectif du chapitre

Maîtriser les prompts pour développer des applications backend robustes avec PHP, Node.js et Python, en utilisant les meilleures pratiques modernes.

## 🐘 PHP : Laravel, Symfony, API Development

### 1. 🏗️ Architecture PHP moderne

```
Tu es un développeur PHP senior expert en Laravel et Symfony.

TA TÂCHE : Créer une architecture backend PHP moderne pour [APPLICATION]

SPÉCIFICATIONS :
- Framework : Laravel [VERSION] ou Symfony [VERSION]
- Architecture : [MVC/DDD/Hexagonal]
- Database : [MySQL/PostgreSQL]
- API : REST/GraphQL
- Authentication : JWT/OAuth2
- Testing : PHPUnit, Pest

CONTRAINTES :
- PSR-12 coding standards
- PHP 8.1+ features
- Performance optimisée
- Security best practices
- Testing coverage > 90%

FORMAT DE SORTIE :
1. 📁 Structure projet optimisée
2. 🔧 Configuration Composer/PHP
3. 🏗️ Architecture (Controllers, Services, Repositories)
4. 🗄️ Models et migrations
5. 🔐 Authentication & authorization
6. 🧪 Tests complets (unit, feature, integration)
7. 📚 API documentation (OpenAPI)

QUALITÉ : Code PHP production-ready, scalable, sécurisé
```

**Structure projet Laravel recommandée :**
```
📁 app/
├── 📁 Console/           # Artisan commands
├── 📁 Events/            # Domain events
├── 📁 Exceptions/        # Custom exceptions
├── 📁 Http/              # HTTP layer
│   ├── 📁 Controllers/   # Request handlers
│   ├── 📁 Middleware/    # HTTP middleware
│   ├── 📁 Requests/      # Form requests
│   └── 📁 Resources/     # API resources
├── 📁 Models/            # Eloquent models
├── 📁 Policies/          # Authorization policies
├── 📁 Providers/         # Service providers
├── 📁 Repositories/      # Data access layer
├── 📁 Services/          # Business logic
├── 📁 Jobs/              # Queue jobs
├── 📁 Mail/              # Email templates
├── 📁 Notifications/     # Push notifications
└── 📁 Rules/             # Validation rules

📁 database/
├── 📁 migrations/        # Database migrations
├── 📁 seeders/          # Database seeders
└── 📁 factories/        # Model factories

📁 tests/
├── 📁 Feature/          # Feature tests
├── 📁 Unit/             # Unit tests
└── 📁 Browser/          # Browser tests

📁 routes/
├── api.php              # API routes
├── web.php              # Web routes
└── channels.php         # Broadcasting channels
```

### 2. 🔧 API REST avec Laravel

```
Tu es un expert Laravel API et RESTful design.

TA TÂCHE : Implémenter une API REST complète avec Laravel

ENDPOINTS À CRÉER :
1. **Authentication** : Login, register, logout, refresh
2. **Users** : CRUD operations avec policies
3. **Products** : CRUD avec validation
4. **Orders** : Complex business logic
5. **Categories** : Nested resources
6. **Search** : Full-text search avec filters

CONTRAINTES :
- RESTful conventions
- JSON API specification
- JWT authentication
- Rate limiting
- API versioning
- Comprehensive validation

FORMAT DE SORTIE :
1. API routes avec controllers
2. Request validation (Form Requests)
3. API resources (transformers)
4. Middleware (auth, rate limiting)
5. Tests API complets
6. Documentation OpenAPI/Swagger
7. Postman collection

QUALITÉ : API REST production-ready, documentée, testée
```

**Exemple d'API Controller Laravel :**
```php
<?php

namespace App\Http\Controllers\Api;

use App\Http\Controllers\Controller;
use App\Http\Requests\StoreProductRequest;
use App\Http\Requests\UpdateProductRequest;
use App\Http\Resources\ProductResource;
use App\Models\Product;
use App\Services\ProductService;
use Illuminate\Http\JsonResponse;
use Illuminate\Http\Request;
use Illuminate\Http\Resources\Json\AnonymousResourceCollection;

class ProductController extends Controller
{
    public function __construct(
        private ProductService $productService
    ) {}

    /**
     * Liste des produits avec pagination et filtres
     */
    public function index(Request $request): AnonymousResourceCollection
    {
        $this->authorize('viewAny', Product::class);

        $filters = $request->validate([
            'category' => 'sometimes|exists:categories,id',
            'price_min' => 'sometimes|numeric|min:0',
            'price_max' => 'sometimes|numeric|min:0',
            'search' => 'sometimes|string|max:255',
            'sort' => 'sometimes|in:name,price,created_at',
            'order' => 'sometimes|in:asc,desc',
            'per_page' => 'sometimes|integer|min:1|max:100',
        ]);

        $products = $this->productService->getProductsWithFilters($filters);

        return ProductResource::collection($products);
    }

    /**
     * Créer un nouveau produit
     */
    public function store(StoreProductRequest $request): JsonResponse
    {
        $this->authorize('create', Product::class);

        $product = $this->productService->createProduct($request->validated());

        return response()->json([
            'message' => 'Produit créé avec succès',
            'data' => new ProductResource($product)
        ], 201);
    }

    /**
     * Afficher un produit spécifique
     */
    public function show(Product $product): ProductResource
    {
        $this->authorize('view', $product);

        return new ProductResource($product->load(['category', 'images']));
    }

    /**
     * Mettre à jour un produit
     */
    public function update(UpdateProductRequest $request, Product $product): JsonResponse
    {
        $this->authorize('update', $product);

        $updatedProduct = $this->productService->updateProduct($product, $request->validated());

        return response()->json([
            'message' => 'Produit mis à jour avec succès',
            'data' => new ProductResource($updatedProduct)
        ]);
    }

    /**
     * Supprimer un produit
     */
    public function destroy(Product $product): JsonResponse
    {
        $this->authorize('delete', $product);

        $product->delete();

        return response()->json([
            'message' => 'Produit supprimé avec succès'
        ], 204);
    }
}
```

## 🟢 Node.js : Express, Fastify, API Development

### 1. 🏗️ Architecture Node.js moderne

```
Tu es un développeur Node.js senior expert en Express.js et TypeScript.

TA TÂCHE : Créer une architecture backend Node.js scalable pour [APPLICATION]

SPÉCIFICATIONS :
- Runtime : Node.js [VERSION]
- Framework : Express.js ou Fastify
- Language : TypeScript strict
- Database : [MongoDB/PostgreSQL]
- Architecture : [Clean Architecture/Layered]
- Testing : Jest, Supertest

CONTRAINTES :
- TypeScript strict mode
- Performance optimisée (clustering)
- Security headers (helmet)
- Rate limiting et CORS
- Error handling centralisé
- Logging structuré (Winston)

FORMAT DE SORTIE :
1. 📁 Structure projet TypeScript
2. 🔧 Configuration (package.json, tsconfig)
3. 🏗️ Architecture (controllers, services, repositories)
4. 🗄️ Database models et migrations
5. 🔐 Authentication middleware
6. 🧪 Tests complets
7. 📚 API documentation (Swagger)

QUALITÉ : Code Node.js production-ready, type-safe, performant
```

**Structure projet Node.js/Express :**
```
📁 src/
├── 📁 controllers/        # Request handlers
├── 📁 middleware/         # Express middleware
├── 📁 models/            # Database models
├── 📁 repositories/      # Data access layer
├── 📁 services/          # Business logic
├── 📁 routes/            # Route definitions
├── 📁 utils/             # Helpers, constants
├── 📁 types/             # TypeScript types
├── 📁 validation/        # Request validation
├── 📁 config/            # Configuration files
├── 📁 __tests__/         # Tests
├── 📁 docs/              # API documentation
├── 📄 app.ts            # Express app setup
└── 📄 server.ts         # Server startup

📁 database/
├── 📁 migrations/        # Database migrations
├── 📁 seeders/          # Database seeders
└── 📄 connection.ts     # Database connection
```

### 2. 🚀 API Express avec TypeScript

```
Tu es un expert Express.js et TypeScript API development.

TA TÂCHE : Implémenter une API REST Express avec TypeScript

ENDPOINTS À CRÉER :
1. **Auth** : JWT authentication, refresh tokens
2. **Users** : CRUD avec role-based access
3. **Products** : CRUD avec validation
4. **Orders** : Complex business logic
5. **Search** : Elasticsearch integration
6. **Upload** : File upload avec Multer

CONTRAINTES :
- TypeScript strict mode
- Express.js best practices
- Joi/Yup validation
- Rate limiting (express-rate-limit)
- Security headers (helmet)
- Error handling middleware

FORMAT DE SORTIE :
1. Express app configuration
2. TypeScript interfaces et types
3. Route handlers avec validation
4. Middleware personnalisés
5. Error handling centralisé
6. Tests API avec Supertest
7. OpenAPI specification

QUALITÉ : API Express type-safe, sécurisée, performante
```

**Exemple de route Express TypeScript :**
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
  body('name').trim().isLength({ min: 1, max: 255 }).withMessage('Name is required'),
  body('description').trim().isLength({ max: 2000 }).withMessage('Description too long'),
  body('price').isFloat({ min: 0 }).withMessage('Price must be positive'),
  body('categoryId').isInt({ min: 1 }).withMessage('Valid category required'),
];

const validateProductId = [
  param('id').isInt({ min: 1 }).withMessage('Valid product ID required'),
];

// Get all products with filters
router.get('/', [
  query('page').optional().isInt({ min: 1 }),
  query('limit').optional().isInt({ min: 1, max: 100 }),
  query('category').optional().isInt({ min: 1 }),
  query('search').optional().isLength({ min: 1, max: 255 }),
], asyncHandler(async (req: Request, res: Response) => {
  const errors = validationResult(req);
  if (!errors.isEmpty()) {
    return res.status(400).json({ errors: errors.array() });
  }

  const { page = 1, limit = 10, category, search } = req.query;
  const filters = { category: category as string, search: search as string };

  const result = await productService.getProducts(
    parseInt(page as string),
    parseInt(limit as string),
    filters
  );

  res.json({
    data: result.products,
    pagination: {
      page: result.page,
      limit: result.limit,
      total: result.total,
      totalPages: Math.ceil(result.total / parseInt(limit as string))
    }
  });
}));

// Get single product
router.get('/:id', validateProductId, asyncHandler(async (req: Request, res: Response) => {
  const errors = validationResult(req);
  if (!errors.isEmpty()) {
    return res.status(400).json({ errors: errors.array() });
  }

  const product = await productService.getProductById(parseInt(req.params.id));

  if (!product) {
    return res.status(404).json({ message: 'Product not found' });
  }

  res.json({ data: product });
}));

// Create product (Admin only)
router.post('/', [
  authenticateToken,
  authorizeRoles(['admin']),
  rateLimit({ windowMs: 15 * 60 * 1000, max: 10 }), // 10 requests per 15 minutes
  ...validateProduct,
], asyncHandler(async (req: Request, res: Response) => {
  const errors = validationResult(req);
  if (!errors.isEmpty()) {
    return res.status(400).json({ errors: errors.array() });
  }

  const productData = req.body;
  const product = await productService.createProduct(productData);

  res.status(201).json({
    message: 'Product created successfully',
    data: product
  });
}));

// Update product (Admin only)
router.put('/:id', [
  authenticateToken,
  authorizeRoles(['admin']),
  ...validateProductId,
  ...validateProduct,
], asyncHandler(async (req: Request, res: Response) => {
  const errors = validationResult(req);
  if (!errors.isEmpty()) {
    return res.status(400).json({ errors: errors.array() });
  }

  const productId = parseInt(req.params.id);
  const productData = req.body;

  const updatedProduct = await productService.updateProduct(productId, productData);

  if (!updatedProduct) {
    return res.status(404).json({ message: 'Product not found' });
  }

  res.json({
    message: 'Product updated successfully',
    data: updatedProduct
  });
}));

// Delete product (Admin only)
router.delete('/:id', [
  authenticateToken,
  authorizeRoles(['admin']),
  ...validateProductId,
], asyncHandler(async (req: Request, res: Response) => {
  const errors = validationResult(req);
  if (!errors.isEmpty()) {
    return res.status(400).json({ errors: errors.array() });
  }

  const deleted = await productService.deleteProduct(parseInt(req.params.id));

  if (!deleted) {
    return res.status(404).json({ message: 'Product not found' });
  }

  res.status(204).send();
}));

export default router;
```

## 🐍 Python : Django, FastAPI, Flask

### 1. 🏗️ Architecture Python moderne

```
Tu es un développeur Python senior expert en Django et FastAPI.

TA TÂCHE : Créer une architecture backend Python scalable pour [APPLICATION]

SPÉCIFICATIONS :
- Framework : Django, FastAPI, ou Flask
- Architecture : [MVC/MVT/Clean Architecture]
- Database : [PostgreSQL/MongoDB]
- API : REST/GraphQL
- Authentication : JWT/OAuth2
- Testing : pytest, unittest

CONTRAINTES :
- Python 3.9+ features
- Type hints (mypy)
- Async/await support
- Security best practices
- Performance optimisée

FORMAT DE SORTIE :
1. 📁 Structure projet Python
2. 🔧 Configuration (requirements.txt, setup.py)
3. 🏗️ Architecture (views, services, models)
4. 🗄️ Models et migrations
5. 🔐 Authentication system
6. 🧪 Tests complets
7. 📚 API documentation (OpenAPI)

QUALITÉ : Code Python production-ready, type-safe, performant
```

### 2. 🚀 API FastAPI avec Python

```
Tu es un expert FastAPI et async Python development.

TA TÂCHE : Implémenter une API REST moderne avec FastAPI

ENDPOINTS À CRÉER :
1. **Authentication** : OAuth2 avec JWT
2. **Users** : CRUD avec permissions
3. **Products** : CRUD avec validation Pydantic
4. **Orders** : Business logic complexe
5. **Analytics** : Data aggregation
6. **Webhooks** : Async event handling

CONTRAINTES :
- FastAPI async features
- Pydantic models validation
- SQLAlchemy async ORM
- Dependency injection
- CORS et security middleware
- Background tasks

FORMAT DE SORTIE :
1. FastAPI application setup
2. Pydantic models et validators
3. Route handlers async
4. Dependency injection
5. Background tasks
6. Tests avec pytest-asyncio
7. OpenAPI documentation

QUALITÉ : API FastAPI moderne, async, type-safe
```

**Exemple de code FastAPI :**
```python
# app/main.py
from fastapi import FastAPI, Depends, HTTPException, status
from fastapi.security import OAuth2PasswordBearer, OAuth2PasswordRequestForm
from fastapi.middleware.cors import CORSMiddleware
from sqlalchemy.ext.asyncio import AsyncSession
from contextlib import asynccontextmanager

from app.core.config import settings
from app.core.database import get_async_session, create_tables
from app.api.v1.api import api_router
from app.services.auth import AuthService

# Lifespan context manager
@asynccontextmanager
async def lifespan(app: FastAPI):
    # Startup
    await create_tables()
    yield
    # Shutdown
    # Cleanup code here

# FastAPI app
app = FastAPI(
    title=settings.PROJECT_NAME,
    version=settings.VERSION,
    description=settings.DESCRIPTION,
    lifespan=lifespan
)

# CORS middleware
app.add_middleware(
    CORSMiddleware,
    allow_origins=settings.ALLOWED_HOSTS,
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)

# Security
oauth2_scheme = OAuth2PasswordBearer(tokenUrl="token")

# Include routers
app.include_router(api_router, prefix=settings.API_V1_STR)

# Health check endpoint
@app.get("/health")
async def health_check():
    return {"status": "healthy"}

# API routes
# ... authentication routes, product routes, etc.
```

```python
# app/api/v1/endpoints/products.py
from typing import List, Optional
from fastapi import APIRouter, Depends, HTTPException, Query
from sqlalchemy.ext.asyncio import AsyncSession
from sqlalchemy import select, and_, or_

from app.core.database import get_async_session
from app.models.product import Product
from app.schemas.product import ProductCreate, ProductUpdate, ProductResponse
from app.services.product import ProductService
from app.api.deps import get_current_user

router = APIRouter()
product_service = ProductService()

@router.get("/", response_model=List[ProductResponse])
async def get_products(
    skip: int = Query(0, ge=0),
    limit: int = Query(100, ge=1, le=100),
    category_id: Optional[int] = Query(None, ge=1),
    search: Optional[str] = Query(None, min_length=1, max_length=255),
    min_price: Optional[float] = Query(None, ge=0),
    max_price: Optional[float] = Query(None, ge=0),
    db: AsyncSession = Depends(get_async_session),
    current_user = Depends(get_current_user)
):
    """Get products with filtering and pagination"""

    # Build filters
    filters = []
    if category_id:
        filters.append(Product.category_id == category_id)
    if min_price is not None:
        filters.append(Product.price >= min_price)
    if max_price is not None:
        filters.append(Product.price <= max_price)
    if search:
        filters.append(
            or_(
                Product.name.ilike(f"%{search}%"),
                Product.description.ilike(f"%{search}%")
            )
        )

    # Execute query
    query = select(Product).where(and_(*filters)).offset(skip).limit(limit)
    result = await db.execute(query)
    products = result.scalars().all()

    return products

@router.post("/", response_model=ProductResponse)
async def create_product(
    product_data: ProductCreate,
    db: AsyncSession = Depends(get_async_session),
    current_user = Depends(get_current_user)
):
    """Create a new product"""

    # Check permissions
    if not current_user.is_admin:
        raise HTTPException(
            status_code=status.HTTP_403_FORBIDDEN,
            detail="Not enough permissions"
        )

    # Create product
    product = await product_service.create_product(db, product_data)

    return product

@router.get("/{product_id}", response_model=ProductResponse)
async def get_product(
    product_id: int,
    db: AsyncSession = Depends(get_async_session),
    current_user = Depends(get_current_user)
):
    """Get a specific product"""

    product = await product_service.get_product_by_id(db, product_id)

    if not product:
        raise HTTPException(
            status_code=status.HTTP_404_NOT_FOUND,
            detail="Product not found"
        )

    return product

@router.put("/{product_id}", response_model=ProductResponse)
async def update_product(
    product_id: int,
    product_data: ProductUpdate,
    db: AsyncSession = Depends(get_async_session),
    current_user = Depends(get_current_user)
):
    """Update a product"""

    # Check permissions
    if not current_user.is_admin:
        raise HTTPException(
            status_code=status.HTTP_403_FORBIDDEN,
            detail="Not enough permissions"
        )

    # Update product
    updated_product = await product_service.update_product(db, product_id, product_data)

    if not updated_product:
        raise HTTPException(
            status_code=status.HTTP_404_NOT_FOUND,
            detail="Product not found"
        )

    return updated_product

@router.delete("/{product_id}")
async def delete_product(
    product_id: int,
    db: AsyncSession = Depends(get_async_session),
    current_user = Depends(get_current_user)
):
    """Delete a product"""

    # Check permissions
    if not current_user.is_admin:
        raise HTTPException(
            status_code=status.HTTP_403_FORBIDDEN,
            detail="Not enough permissions"
        )

    # Delete product
    deleted = await product_service.delete_product(db, product_id)

    if not deleted:
        raise HTTPException(
            status_code=status.HTTP_404_NOT_FOUND,
            detail="Product not found"
        )

    return {"message": "Product deleted successfully"}
```

## 📊 Comparaison et choix du backend

### 🎯 Guide de choix : PHP vs Node.js vs Python

```
Tu es un consultant backend senior expert en architectures.

TA TÂCHE : Analyser et recommander la stack backend optimale pour [PROJET]

CONTEXTE PROJET :
- Équipe : [TAILLE] développeurs
- Compétences : [EXISTANTES]
- Performance : [EXIGENCES]
- Complexité : [NIVEAU]
- Budget : [CONTRAINTE]
- Maintenance : [DURÉE]

CRITÈRES D'ANALYSE :
- Learning curve et onboarding
- Performance et scalability
- Type safety et developer experience
- Écosystème et libraries
- Deployment et hosting
- Community et support
- Cost of ownership

FORMAT DE SORTIE :
1. Analyse comparative détaillée
2. Score par critère (1-10)
3. Recommandation justifiée
4. Migration strategy si applicable
5. Roadmap d'implémentation
6. Risk assessment et mitigation

QUALITÉ : Recommandation objective et argumentée
```

**Matrice de comparaison :**

| Critère | PHP/Laravel | Node.js/Express | Python/FastAPI |
|---------|-------------|-----------------|----------------|
| Learning Curve | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ |
| Performance | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| Type Safety | ⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| Ecosystem | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| Deployment | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐ |
| Community | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| Cost | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐ |

## 📝 Templates de prompts Backend

### 🐘 Template PHP/Laravel
```
Tu es un développeur PHP senior expert en Laravel [VERSION].

TA TÂCHE : Implémenter [FONCTIONNALITÉ] avec Laravel

SPÉCIFICATIONS :
- Laravel [VERSION] features
- PSR-12 coding standards
- PHP 8.1+ modern features
- Testing avec PHPUnit/Pest

CONTRAINTES :
- MVC architecture respectée
- Eloquent ORM optimisé
- Security best practices
- Performance optimisée

FORMAT :
1. Code PHP avec Laravel
2. Migrations de base de données
3. Tests unitaires et feature
4. API documentation
5. Performance considerations
6. Security validation

QUALITÉ : Code Laravel production-ready, sécurisé
```

### 🟢 Template Node.js/TypeScript
```
Tu es un développeur Node.js senior expert en TypeScript.

TA TÂCHE : Créer [FONCTIONNALITÉ] avec Node.js et TypeScript

SPÉCIFICATIONS :
- TypeScript strict mode
- Express.js ou Fastify
- Async/await patterns
- Error handling robuste

CONTRAINTES :
- Type safety maximale
- Performance optimisée
- Security headers (helmet)
- Testing avec Jest/Supertest

FORMAT :
1. Code TypeScript modulaire
2. Interfaces et types
3. Error handling middleware
4. Tests complets
5. API documentation
6. Performance benchmarks

QUALITÉ : Code Node.js type-safe, performant, testé
```

### 🐍 Template Python/FastAPI
```
Tu es un développeur Python senior expert en FastAPI.

TA TÂCHE : Implémenter [FONCTIONNALITÉ] avec FastAPI

SPÉCIFICATIONS :
- FastAPI async features
- Pydantic validation
- SQLAlchemy async ORM
- Dependency injection

CONTRAINTES :
- Type hints (mypy)
- Async/await patterns
- Security best practices
- Testing avec pytest-asyncio

FORMAT :
1. Code Python async avec FastAPI
2. Pydantic models
3. Database queries async
4. Tests avec pytest
5. OpenAPI documentation
6. Performance analysis

QUALITÉ : Code Python moderne, async, type-safe
```

## 🎯 Points clés à retenir

1. **Laravel** : Rapid development, ecosystem complet
2. **Express.js** : Flexibilité, performance, TypeScript
3. **FastAPI** : Performance async, type safety, auto-docs
4. **TypeScript** : Developer experience, bug prevention
5. **Testing** : Code quality, confidence in deployment
6. **Security** : OWASP compliance, best practices

## 🚀 Prochain chapitre

Maintenant que vous maîtrisez les langages backend, découvrons les patterns d'architecture logicielle modernes.

---

## 📋 Checklist qualité

- ✅ Architecture PHP/Laravel moderne
- ✅ API Express TypeScript
- ✅ FastAPI async Python
- ✅ Authentication et authorization
- ✅ Testing strategies
- ✅ Performance optimizations

## 🎯 Test de compréhension

**Question :** Pourquoi FastAPI est-il particulièrement adapté aux applications async ?

**Réponse attendue :** FastAPI est built on top de Starlette (async web framework) et utilise Python async/await nativement. Il supporte nativement les WebSockets, background tasks, et les requêtes HTTP concurrentes, offrant des performances optimales pour les I/O bound applications.

---

**Temps de lecture estimé : 65 minutes**
**Niveau : Expert**
**Prérequis : Partie I + II**
