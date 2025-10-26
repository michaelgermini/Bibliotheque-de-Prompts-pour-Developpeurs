# 📖 Partie III - Chapitre 4 : SQL & NoSQL

## 🎯 Objectif du chapitre

Maîtriser la conception, l'optimisation et la gestion des bases de données SQL et NoSQL, incluant les requêtes complexes, les migrations et les stratégies de performance.

## 🗄️ SQL : PostgreSQL, MySQL, Optimisation

### 1. 🏗️ Database Design et Schema

```
Tu es un expert database design et SQL optimization.

TA TÂCHE : Concevoir et optimiser un schema de base de données SQL pour [APPLICATION]

SPÉCIFICATIONS :
- Database : PostgreSQL ou MySQL
- Schema design normalisé (3NF)
- Relations et contraintes
- Index strategy optimisée
- Migrations avec rollback
- Seeders pour données de test

CONTRAINTES :
- ACID compliance
- Performance requirements
- Scalability considerations
- Backup et recovery strategy
- Security best practices

FORMAT DE SORTIE :
1. ERD (Entity Relationship Diagram)
2. Tables avec types et contraintes
3. Index strategy documentée
4. Migrations SQL complètes
5. Seeders et factories
6. Performance analysis
7. Backup strategy

QUALITÉ : Schema SQL optimisé, scalable, sécurisé
```

**Exemple de schema e-commerce :**
```sql
-- Users table
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    email VARCHAR(255) UNIQUE NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    first_name VARCHAR(100) NOT NULL,
    last_name VARCHAR(100) NOT NULL,
    phone VARCHAR(20),
    is_active BOOLEAN DEFAULT true,
    email_verified_at TIMESTAMP NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,

    INDEX idx_users_email (email),
    INDEX idx_users_active (is_active),
    INDEX idx_users_created_at (created_at)
);

-- Categories table
CREATE TABLE categories (
    id SERIAL PRIMARY KEY,
    name VARCHAR(255) UNIQUE NOT NULL,
    slug VARCHAR(255) UNIQUE NOT NULL,
    description TEXT,
    parent_id INT NULL,
    is_active BOOLEAN DEFAULT true,
    sort_order INT DEFAULT 0,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,

    FOREIGN KEY (parent_id) REFERENCES categories(id) ON DELETE CASCADE,
    INDEX idx_categories_parent (parent_id),
    INDEX idx_categories_active (is_active),
    INDEX idx_categories_slug (slug)
);

-- Products table
CREATE TABLE products (
    id SERIAL PRIMARY KEY,
    sku VARCHAR(100) UNIQUE NOT NULL,
    name VARCHAR(255) NOT NULL,
    description TEXT,
    short_description VARCHAR(500),
    price DECIMAL(10,2) NOT NULL CHECK (price >= 0),
    compare_at_price DECIMAL(10,2) NULL CHECK (compare_at_price >= price),
    cost_price DECIMAL(10,2) NULL CHECK (cost_price >= 0),
    track_quantity BOOLEAN DEFAULT false,
    quantity INT DEFAULT 0 CHECK (quantity >= 0),
    low_stock_threshold INT DEFAULT 10,
    category_id INT NOT NULL,
    brand VARCHAR(255),
    status ENUM('draft', 'active', 'inactive', 'archived') DEFAULT 'draft',
    weight DECIMAL(8,3) NULL,
    dimensions JSON NULL, -- {"length": 10, "width": 5, "height": 2}
    meta_data JSON NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,

    FOREIGN KEY (category_id) REFERENCES categories(id),
    INDEX idx_products_sku (sku),
    INDEX idx_products_category (category_id),
    INDEX idx_products_status (status),
    INDEX idx_products_price (price),
    INDEX idx_products_name (name),
    INDEX idx_products_created_at (created_at),
    FULLTEXT idx_products_search (name, description, short_description)
);

-- Product variants (size, color, etc.)
CREATE TABLE product_variants (
    id SERIAL PRIMARY KEY,
    product_id INT NOT NULL,
    sku VARCHAR(100) UNIQUE NOT NULL,
    name VARCHAR(255) NOT NULL, -- "Small Red T-Shirt"
    price_modifier DECIMAL(10,2) DEFAULT 0, -- Additional cost
    quantity INT DEFAULT 0 CHECK (quantity >= 0),
    is_active BOOLEAN DEFAULT true,
    attributes JSON NOT NULL, -- {"size": "S", "color": "red"}
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,

    FOREIGN KEY (product_id) REFERENCES products(id) ON DELETE CASCADE,
    INDEX idx_variants_product (product_id),
    INDEX idx_variants_sku (sku),
    INDEX idx_variants_active (is_active)
);

-- Orders table
CREATE TABLE orders (
    id SERIAL PRIMARY KEY,
    order_number VARCHAR(50) UNIQUE NOT NULL,
    user_id INT NOT NULL,
    status ENUM('pending', 'confirmed', 'processing', 'shipped', 'delivered', 'cancelled') DEFAULT 'pending',
    subtotal DECIMAL(10,2) NOT NULL CHECK (subtotal >= 0),
    tax_amount DECIMAL(10,2) DEFAULT 0 CHECK (tax_amount >= 0),
    shipping_amount DECIMAL(10,2) DEFAULT 0 CHECK (shipping_amount >= 0),
    discount_amount DECIMAL(10,2) DEFAULT 0 CHECK (discount_amount >= 0),
    total_amount DECIMAL(10,2) NOT NULL CHECK (total_amount >= 0),
    currency VARCHAR(3) DEFAULT 'USD',
    shipping_address JSON NOT NULL,
    billing_address JSON NOT NULL,
    payment_method VARCHAR(50),
    payment_status ENUM('pending', 'paid', 'failed', 'refunded') DEFAULT 'pending',
    notes TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,

    FOREIGN KEY (user_id) REFERENCES users(id),
    INDEX idx_orders_user (user_id),
    INDEX idx_orders_status (status),
    INDEX idx_orders_number (order_number),
    INDEX idx_orders_created_at (created_at),
    INDEX idx_orders_payment_status (payment_status)
);

-- Order items
CREATE TABLE order_items (
    id SERIAL PRIMARY KEY,
    order_id INT NOT NULL,
    product_id INT NOT NULL,
    product_variant_id INT NULL,
    product_name VARCHAR(255) NOT NULL, -- Snapshot for historical records
    product_sku VARCHAR(100) NOT NULL,
    quantity INT NOT NULL CHECK (quantity > 0),
    unit_price DECIMAL(10,2) NOT NULL CHECK (unit_price >= 0),
    total_price DECIMAL(10,2) NOT NULL CHECK (total_price >= 0),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    FOREIGN KEY (order_id) REFERENCES orders(id) ON DELETE CASCADE,
    FOREIGN KEY (product_id) REFERENCES products(id),
    FOREIGN KEY (product_variant_id) REFERENCES product_variants(id),
    INDEX idx_order_items_order (order_id),
    INDEX idx_order_items_product (product_id)
);

-- Product reviews
CREATE TABLE product_reviews (
    id SERIAL PRIMARY KEY,
    product_id INT NOT NULL,
    user_id INT NOT NULL,
    order_id INT NULL, -- Verified purchase
    rating TINYINT NOT NULL CHECK (rating >= 1 AND rating <= 5),
    title VARCHAR(255),
    comment TEXT,
    is_verified BOOLEAN DEFAULT false,
    is_approved BOOLEAN DEFAULT true,
    helpful_count INT DEFAULT 0,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,

    FOREIGN KEY (product_id) REFERENCES products(id) ON DELETE CASCADE,
    FOREIGN KEY (user_id) REFERENCES users(id),
    FOREIGN KEY (order_id) REFERENCES orders(id),
    UNIQUE KEY unique_user_product_review (user_id, product_id),
    INDEX idx_reviews_product (product_id),
    INDEX idx_reviews_user (user_id),
    INDEX idx_reviews_rating (rating),
    INDEX idx_reviews_approved (is_approved)
);

-- Shopping cart (temporary)
CREATE TABLE cart_items (
    id SERIAL PRIMARY KEY,
    session_id VARCHAR(255) NOT NULL, -- For guest users
    user_id INT NULL, -- For authenticated users
    product_id INT NOT NULL,
    product_variant_id INT NULL,
    quantity INT NOT NULL CHECK (quantity > 0),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,

    FOREIGN KEY (user_id) REFERENCES users(id),
    FOREIGN KEY (product_id) REFERENCES products(id),
    FOREIGN KEY (product_variant_id) REFERENCES product_variants(id),
    INDEX idx_cart_session (session_id),
    INDEX idx_cart_user (user_id),
    INDEX idx_cart_product (product_id)
);

-- Audit log
CREATE TABLE audit_logs (
    id SERIAL PRIMARY KEY,
    table_name VARCHAR(100) NOT NULL,
    record_id INT NOT NULL,
    action ENUM('INSERT', 'UPDATE', 'DELETE') NOT NULL,
    user_id INT NULL,
    old_values JSON NULL,
    new_values JSON NULL,
    ip_address VARCHAR(45),
    user_agent TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    INDEX idx_audit_table_record (table_name, record_id),
    INDEX idx_audit_user (user_id),
    INDEX idx_audit_created_at (created_at)
);
```

### 2. 🚀 SQL Query Optimization

```
Tu es un expert SQL performance et query optimization.

TA TÂCHE : Optimiser des requêtes SQL complexes pour [APPLICATION]

REQUÊTES À OPTIMISER :
1. **Complex Joins** : Multi-table queries avec relations
2. **Aggregations** : COUNT, SUM, AVG avec GROUP BY
3. **Full-text Search** : LIKE patterns, FULLTEXT indexes
4. **Pagination** : OFFSET/LIMIT vs cursor-based
5. **Reporting** : Complex analytics queries
6. **Real-time** : Dashboard queries performance

CONTRAINTES :
- Execution time < 100ms
- Index coverage optimale
- EXPLAIN ANALYZE review
- Query plan optimization
- Caching strategy

FORMAT DE SORTIE :
1. Query performance analysis
2. Index recommendations
3. Optimized queries
4. EXPLAIN ANALYZE results
5. Performance benchmarks
6. Monitoring setup
7. Best practices documentation

QUALITÉ : Requêtes SQL optimisées, performance maximale
```

**Exemple d'optimisation de requête :**
```sql
-- ❌ BEFORE: Slow query with multiple issues
SELECT
    p.id,
    p.name,
    p.price,
    c.name as category_name,
    COUNT(o.id) as order_count,
    AVG(r.rating) as avg_rating,
    (SELECT COUNT(*) FROM product_images pi WHERE pi.product_id = p.id) as image_count
FROM products p
LEFT JOIN categories c ON p.category_id = c.id
LEFT JOIN orders o ON o.id IN (SELECT order_id FROM order_items WHERE product_id = p.id)
LEFT JOIN product_reviews r ON r.product_id = p.id
WHERE p.status = 'active'
  AND p.price BETWEEN 10 AND 1000
  AND c.name LIKE '%electronics%'
GROUP BY p.id, p.name, p.price, c.name
ORDER BY p.created_at DESC
LIMIT 20;

-- ✅ AFTER: Optimized query with proper indexing
-- 1. Create composite indexes
CREATE INDEX idx_products_status_price_category ON products (status, price, category_id);
CREATE INDEX idx_products_search ON products (status, name, description);
CREATE INDEX idx_categories_name ON categories (name);
CREATE INDEX idx_order_items_product ON order_items (product_id, order_id);
CREATE INDEX idx_reviews_product_rating ON product_reviews (product_id, rating);

-- 2. Optimized query with JOINs and subqueries
SELECT
    p.id,
    p.name,
    p.price,
    c.name as category_name,
    COALESCE(stats.order_count, 0) as order_count,
    COALESCE(stats.avg_rating, 0) as avg_rating,
    COALESCE(img.image_count, 0) as image_count
FROM products p
LEFT JOIN categories c ON p.category_id = c.id
LEFT JOIN (
    SELECT
        oi.product_id,
        COUNT(DISTINCT o.id) as order_count
    FROM order_items oi
    JOIN orders o ON oi.order_id = o.id AND o.status != 'cancelled'
    GROUP BY oi.product_id
) stats ON p.id = stats.product_id
LEFT JOIN (
    SELECT
        product_id,
        AVG(rating) as avg_rating
    FROM product_reviews
    WHERE is_approved = true
    GROUP BY product_id
) stats_rating ON p.id = stats_rating.product_id
LEFT JOIN (
    SELECT product_id, COUNT(*) as image_count
    FROM product_images
    GROUP BY product_id
) img ON p.id = img.product_id
WHERE p.status = 'active'
  AND p.price BETWEEN 10 AND 1000
  AND c.name LIKE '%electronics%'
ORDER BY p.created_at DESC
LIMIT 20;

-- 3. Alternative with CTE for complex logic
WITH product_stats AS (
    SELECT
        p.id,
        COUNT(DISTINCT o.id) as order_count,
        AVG(r.rating) as avg_rating,
        COUNT(pi.id) as image_count
    FROM products p
    LEFT JOIN order_items oi ON p.id = oi.product_id
    LEFT JOIN orders o ON oi.order_id = o.id AND o.status != 'cancelled'
    LEFT JOIN product_reviews r ON p.id = r.product_id AND r.is_approved = true
    LEFT JOIN product_images pi ON p.id = pi.product_id
    WHERE p.status = 'active'
    GROUP BY p.id
)
SELECT
    p.id,
    p.name,
    p.price,
    c.name as category_name,
    ps.order_count,
    ps.avg_rating,
    ps.image_count
FROM products p
LEFT JOIN categories c ON p.category_id = c.id
LEFT JOIN product_stats ps ON p.id = ps.id
WHERE p.price BETWEEN 10 AND 1000
  AND c.name LIKE '%electronics%'
ORDER BY p.created_at DESC
LIMIT 20;
```

## 🍃 NoSQL : MongoDB, Document Modeling

### 1. 🏗️ Document Database Design

```
Tu es un expert MongoDB et document modeling.

TA TÂCHE : Concevoir un schema de base de données NoSQL pour [APPLICATION]

SPÉCIFICATIONS :
- Database : MongoDB
- Document modeling optimisé
- Indexes et performance
- Aggregation pipelines
- Transactions (si nécessaire)
- Backup et recovery

CONTRAINTES :
- Schema design patterns
- Embedding vs referencing
- Query performance
- Data consistency
- Scalability considerations

FORMAT DE SORTIE :
1. Document schema design
2. Collections et relationships
3. Index strategy
4. Aggregation examples
5. Performance analysis
6. Migration strategy
7. Best practices

QUALITÉ : Schema MongoDB optimisé, scalable, performant
```

**Exemple de schema MongoDB :**
```javascript
// Users Collection
{
  _id: ObjectId,
  email: String, // unique index
  profile: {
    firstName: String,
    lastName: String,
    phone: String,
    avatar: String,
    preferences: {
      language: String,
      currency: String,
      notifications: {
        email: Boolean,
        sms: Boolean,
        push: Boolean
      }
    }
  },
  addresses: [{
    type: String, // 'shipping' or 'billing'
    street: String,
    city: String,
    state: String,
    country: String,
    zipCode: String,
    isDefault: Boolean
  }],
  auth: {
    passwordHash: String,
    salt: String,
    resetToken: String,
    resetExpires: Date,
    lastLoginAt: Date,
    loginAttempts: Number,
    lockedUntil: Date
  },
  roles: [String], // ['customer', 'admin']
  status: String, // 'active', 'inactive', 'suspended'
  createdAt: Date,
  updatedAt: Date
}

// Products Collection - Embedded variant data
{
  _id: ObjectId,
  sku: String, // unique index
  name: String,
  description: String,
  shortDescription: String,
  pricing: {
    basePrice: Number,
    compareAtPrice: Number,
    costPrice: Number,
    currency: String
  },
  inventory: {
    trackQuantity: Boolean,
    quantity: Number,
    lowStockThreshold: Number,
    policy: String // 'deny', 'allow', 'backorder'
  },
  category: {
    _id: ObjectId,
    name: String,
    slug: String
  },
  brand: String,
  variants: [{
    _id: ObjectId,
    sku: String,
    name: String, // "Small Red T-Shirt"
    priceModifier: Number,
    inventory: {
      quantity: Number,
      reserved: Number
    },
    attributes: {
      size: String,
      color: String,
      material: String
    },
    images: [{
      url: String,
      alt: String,
      isPrimary: Boolean,
      order: Number
    }]
  }],
  images: [{
    url: String,
    alt: String,
    isPrimary: Boolean,
    order: Number
  }],
  attributes: {
    weight: Number,
    dimensions: {
      length: Number,
      width: Number,
      height: Number,
      unit: String
    },
    tags: [String],
    specifications: Map // key-value pairs
  },
  seo: {
    metaTitle: String,
    metaDescription: String,
    slug: String
  },
  status: String, // 'draft', 'active', 'inactive'
  publishedAt: Date,
  createdAt: Date,
  updatedAt: Date
}

// Orders Collection - Referenced relationships
{
  _id: ObjectId,
  orderNumber: String, // unique index
  customer: {
    _id: ObjectId, // reference to users
    email: String,
    name: String
  },
  status: String,
  financial: {
    subtotal: Number,
    taxAmount: Number,
    shippingAmount: Number,
    discountAmount: Number,
    total: Number,
    currency: String
  },
  shipping: {
    method: String,
    cost: Number,
    address: {
      name: String,
      street: String,
      city: String,
      state: String,
      country: String,
      zipCode: String
    },
    tracking: {
      number: String,
      carrier: String,
      url: String
    }
  },
  billing: {
    address: {
      name: String,
      street: String,
      city: String,
      state: String,
      country: String,
      zipCode: String
    }
  },
  items: [{
    productId: ObjectId,
    variantId: ObjectId,
    sku: String,
    name: String,
    price: Number,
    quantity: Number,
    total: Number,
    attributes: Object
  }],
  payments: [{
    method: String,
    amount: Number,
    status: String,
    transactionId: String,
    processedAt: Date
  }],
  fulfillment: {
    status: String,
    shippedAt: Date,
    deliveredAt: Date,
    tracking: String
  },
  timeline: [{
    status: String,
    timestamp: Date,
    note: String,
    userId: ObjectId
  }],
  notes: String,
  tags: [String],
  createdAt: Date,
  updatedAt: Date
}

// Categories Collection - Tree structure
{
  _id: ObjectId,
  name: String,
  slug: String, // unique index
  description: String,
  parentId: ObjectId, // self-reference for tree
  ancestors: [ObjectId], // path from root
  level: Number, // depth in tree
  isActive: Boolean,
  image: String,
  seo: {
    metaTitle: String,
    metaDescription: String
  },
  sortOrder: Number,
  createdAt: Date,
  updatedAt: Date
}

// Analytics Collection - Time series data
{
  _id: ObjectId,
  date: Date, // compound index with type
  type: String, // 'pageview', 'purchase', 'search'
  data: {
    // Flexible schema based on type
    pageview: {
      path: String,
      referrer: String,
      userAgent: String,
      sessionId: String
    },
    purchase: {
      orderId: ObjectId,
      amount: Number,
      currency: String,
      items: [ObjectId]
    },
    search: {
      query: String,
      results: Number,
      filters: Object
    }
  },
  metadata: {
    userId: ObjectId,
    sessionId: String,
    ip: String,
    userAgent: String
  }
}
```

### 2. 🚀 MongoDB Aggregation Pipelines

```
Tu es un expert MongoDB aggregation et performance.

TA TÂCHE : Créer des aggregation pipelines complexes pour [USE CASES]

PIPELINES À IMPLÉMENTER :
1. **Reporting** : Sales analytics, product performance
2. **Search** : Full-text search avec filters
3. **Recommendations** : User behavior analysis
4. **Inventory** : Stock management et alerts
5. **Analytics** : Real-time dashboards

CONTRAINTES :
- Pipeline performance optimisée
- Index usage maximal
- Memory consumption contrôlée
- Result set pagination
- Caching strategy

FORMAT DE SORTIE :
1. Aggregation pipeline design
2. Index recommendations
3. Performance analysis
4. Pipeline examples
5. Optimization techniques
6. Testing et validation
7. Monitoring setup

QUALITÉ : Pipelines MongoDB performants, optimisés, testés
```

**Exemple d'aggregation pipeline :**
```javascript
// Sales analytics pipeline
const salesAnalyticsPipeline = [
  // Match date range
  {
    $match: {
      createdAt: {
        $gte: new Date('2024-01-01'),
        $lt: new Date('2024-02-01')
      },
      status: { $in: ['completed', 'shipped', 'delivered'] }
    }
  },

  // Unwind items for individual analysis
  { $unwind: '$items' },

  // Lookup product details
  {
    $lookup: {
      from: 'products',
      localField: 'items.productId',
      foreignField: '_id',
      as: 'product'
    }
  },

  // Unwind product details
  { $unwind: '$product' },

  // Group by product for analytics
  {
    $group: {
      _id: '$items.productId',
      productName: { $first: '$product.name' },
      category: { $first: '$product.category.name' },
      totalQuantity: { $sum: '$items.quantity' },
      totalRevenue: { $sum: { $multiply: ['$items.price', '$items.quantity'] } },
      orderCount: { $sum: 1 },
      averageOrderValue: { $avg: { $multiply: ['$items.price', '$items.quantity'] } }
    }
  },

  // Sort by revenue
  { $sort: { totalRevenue: -1 } },

  // Limit results
  { $limit: 50 }
];

// Product search pipeline with filters
const productSearchPipeline = [
  // Full-text search
  {
    $search: {
      index: 'products_search',
      text: {
        query: searchQuery,
        path: ['name', 'description', 'shortDescription'],
        fuzzy: {
          maxEdits: 1,
          prefixLength: 2
        }
      }
    }
  },

  // Apply filters
  {
    $match: {
      ...(categoryId && { 'category._id': new ObjectId(categoryId) }),
      ...(minPrice && { 'pricing.basePrice': { $gte: minPrice } }),
      ...(maxPrice && { 'pricing.basePrice': { $lte: maxPrice } }),
      ...(brands && { brand: { $in: brands } }),
      ...(status && { status }),
      ...(inStock && { 'inventory.quantity': { $gt: 0 } })
    }
  },

  // Add computed fields
  {
    $addFields: {
      discountPercentage: {
        $cond: {
          if: { $gt: ['$pricing.compareAtPrice', 0] },
          then: {
            $multiply: [
              {
                $divide: [
                  { $subtract: ['$pricing.compareAtPrice', '$pricing.basePrice'] },
                  '$pricing.compareAtPrice'
                ]
              },
              100
            ]
          },
          else: 0
        }
      },
      isOnSale: {
        $cond: {
          if: { $gt: ['$pricing.compareAtPrice', '$pricing.basePrice'] },
          then: true,
          else: false
        }
      }
    }
  },

  // Sort results
  {
    $sort: {
      ...(sortBy === 'price_asc' && { 'pricing.basePrice': 1 }),
      ...(sortBy === 'price_desc' && { 'pricing.basePrice': -1 }),
      ...(sortBy === 'name' && { name: 1 }),
      ...(sortBy === 'newest' && { createdAt: -1 }),
      ...(sortBy === 'popular' && { 'stats.orderCount': -1 }),
      ...(sortBy === 'rating' && { 'stats.avgRating': -1 })
    }
  },

  // Facet for additional data
  {
    $facet: {
      products: [
        { $skip: skip },
        { $limit: limit }
      ],
      categories: [
        {
          $group: {
            _id: '$category._id',
            name: { $first: '$category.name' },
            count: { $sum: 1 }
          }
        }
      ],
      brands: [
        {
          $match: { brand: { $exists: true } }
        },
        {
          $group: {
            _id: '$brand',
            count: { $sum: 1 }
          }
        }
      ],
      priceRange: [
        {
          $group: {
            _id: null,
            minPrice: { $min: '$pricing.basePrice' },
            maxPrice: { $max: '$pricing.basePrice' }
          }
        }
      ],
      totalCount: [
        { $count: 'count' }
      ]
    }
  }
];
```

## 🔄 Migrations et Schema Evolution

### 1. 🏗️ Database Migration Strategy

```
Tu es un expert database migrations et schema evolution.

TA TÂCHE : Implémenter une stratégie de migration robuste pour [DATABASE]

MIGRATIONS À CRÉER :
1. **Initial Schema** : Tables de base
2. **Feature Migrations** : Nouvelles fonctionnalités
3. **Data Migrations** : Transformation de données
4. **Index Migrations** : Performance improvements
5. **Refactoring** : Schema optimization

CONTRAINTES :
- Zero downtime deployments
- Rollback capability
- Data integrity preservation
- Performance impact minimal
- Testing et validation

FORMAT DE SORTIE :
1. Migration strategy documentée
2. Migration files organisés
3. Rollback procedures
4. Testing strategy
5. Deployment pipeline
6. Monitoring et alerting
7. Best practices guide

QUALITÉ : Migrations fiables, testées, rollback-safe
```

**Exemple de migrations Laravel :**
```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreateProductsTable extends Migration
{
    /**
     * Run the migrations.
     */
    public function up(): void
    {
        Schema::create('products', function (Blueprint $table) {
            $table->id();
            $table->string('sku', 100)->unique();
            $table->string('name', 255);
            $table->text('description')->nullable();
            $table->string('short_description', 500)->nullable();
            $table->decimal('price', 10, 2)->check('price >= 0');
            $table->decimal('compare_at_price', 10, 2)->nullable()->check('compare_at_price >= price');
            $table->decimal('cost_price', 10, 2)->nullable()->check('cost_price >= 0');
            $table->boolean('track_quantity')->default(false);
            $table->integer('quantity')->default(0)->check('quantity >= 0');
            $table->integer('low_stock_threshold')->default(10);
            $table->foreignId('category_id')->constrained('categories');
            $table->string('brand', 255)->nullable();
            $table->enum('status', ['draft', 'active', 'inactive', 'archived'])->default('draft');
            $table->decimal('weight', 8, 3)->nullable();
            $table->json('dimensions')->nullable();
            $table->json('meta_data')->nullable();
            $table->timestamps();

            // Indexes
            $table->index(['status', 'price', 'category_id']);
            $table->index(['status', 'name', 'description']);
            $table->index('created_at');
            $table->fullText(['name', 'description', 'short_description']);
        });
    }

    /**
     * Reverse the migrations.
     */
    public function down(): void
    {
        Schema::dropIfExists('products');
    }
}

class AddProductVariantsTable extends Migration
{
    public function up(): void
    {
        Schema::create('product_variants', function (Blueprint $table) {
            $table->id();
            $table->foreignId('product_id')->constrained('products')->onDelete('cascade');
            $table->string('sku', 100)->unique();
            $table->string('name', 255);
            $table->decimal('price_modifier', 10, 2)->default(0);
            $table->integer('quantity')->default(0)->check('quantity >= 0');
            $table->boolean('is_active')->default(true);
            $table->json('attributes'); // size, color, etc.
            $table->timestamps();

            $table->index(['product_id', 'is_active']);
            $table->index('sku');
        });
    }

    public function down(): void
    {
        Schema::dropIfExists('product_variants');
    }
}

class OptimizeProductQueries extends Migration
{
    public function up(): void
    {
        // Add composite indexes for common queries
        Schema::table('products', function (Blueprint $table) {
            $table->index(['status', 'category_id', 'created_at']);
            $table->index(['status', 'price']);
            $table->index(['category_id', 'status', 'price']);
        });

        Schema::table('categories', function (Blueprint $table) {
            $table->index(['parent_id', 'is_active']);
        });
    }

    public function down(): void
    {
        Schema::table('products', function (Blueprint $table) {
            $table->dropIndex(['status', 'category_id', 'created_at']);
            $table->dropIndex(['status', 'price']);
            $table->dropIndex(['category_id', 'status', 'price']);
        });

        Schema::table('categories', function (Blueprint $table) {
            $table->dropIndex(['parent_id', 'is_active']);
        });
    }
}
```

## 📊 Performance et Monitoring

### 1. 🚀 Database Performance Optimization

```
Tu es un expert database performance et monitoring.

TA TÂCHE : Optimiser les performances de base de données pour [APPLICATION]

OPTIMISATIONS À IMPLÉMENTER :
1. **Query Optimization** : Slow queries identification et fix
2. **Index Strategy** : Coverage et maintenance
3. **Connection Pooling** : Configuration optimale
4. **Caching** : Redis, in-memory, query cache
5. **Partitioning** : Large tables management
6. **Replication** : Read replicas setup

CONTRAINTES :
- Response time < 100ms
- Throughput maximale
- Resource usage optimisé
- High availability
- Backup performance

FORMAT DE SORTIE :
1. Performance audit initial
2. Query optimization
3. Index recommendations
4. Caching strategy
5. Connection pooling
6. Monitoring setup
7. Performance benchmarks

QUALITÉ : Database performance optimale, monitoring en place
```

## 📝 Templates de bases de données

### 🗄️ Template SQL Schema
```
Tu es un expert database design et [DATABASE].

TA TÂCHE : Concevoir schema SQL pour [APPLICATION]

SPÉCIFICATIONS :
- Tables et relations
- Constraints et validations
- Index strategy
- Migrations avec rollback

CONTRAINTES :
- Normalization [1NF/2NF/3NF]
- Performance requirements
- Scalability considerations
- Security constraints

FORMAT :
1. ERD diagram
2. Table schemas
3. Index strategy
4. Migration files
5. Seeder data
6. Performance analysis
7. Backup strategy

QUALITÉ : Schema SQL optimisé, scalable, sécurisé
```

### 🍃 Template MongoDB Schema
```
Tu es un expert MongoDB et document modeling.

TA TÂCHE : Concevoir schema NoSQL pour [APPLICATION]

SPÉCIFICATIONS :
- Collections design
- Embedding vs referencing
- Index strategy
- Aggregation pipelines

CONTRAINTES :
- Query performance
- Data consistency
- Scalability patterns
- Schema evolution

FORMAT :
1. Document schema design
2. Collection relationships
3. Index recommendations
4. Aggregation examples
5. Performance analysis
6. Migration strategy
7. Best practices

QUALITÉ : Schema MongoDB optimisé, scalable, performant
```

### 🚀 Template Query Optimization
```
Tu es un expert SQL/NoSQL query optimization.

TA TÂCHE : Optimiser requêtes pour [DATABASE]

REQUÊTES À OPTIMISER :
- [COMPLEX QUERY 1]
- [AGGREGATION 1]
- [SEARCH QUERY]
- [REPORTING QUERY]

CONTRAINTES :
- Execution time < [TARGET]
- Resource usage minimal
- Index coverage maximale
- Caching strategy

FORMAT :
1. Query analysis
2. Index recommendations
3. Optimized queries
4. Performance benchmarks
5. EXPLAIN results
6. Monitoring setup
7. Best practices

QUALITÉ : Requêtes optimisées, performance maximale
```

## 🎯 Points clés à retenir

1. **SQL** : Relations, ACID, normalization, complex queries
2. **NoSQL** : Flexibility, scalability, aggregation, document model
3. **Indexing** : Performance critical, coverage optimization
4. **Migrations** : Version control, rollback capability, testing
5. **Performance** : Query optimization, caching, monitoring
6. **Scalability** : Partitioning, replication, load balancing

## 🚀 Conclusion de la Partie III

Vous avez maintenant toutes les compétences pour concevoir et optimiser des architectures backend complètes. Passons maintenant aux DevOps et à la sécurité.

---

## 📋 Checklist qualité

- ✅ SQL schema design et optimization
- ✅ MongoDB document modeling
- ✅ Query performance optimization
- ✅ Migration strategies
- ✅ Index strategies
- ✅ Performance monitoring

## 🎯 Test de compréhension

**Question :** Quand faut-il choisir NoSQL plutôt que SQL ?

**Réponse attendue :** NoSQL est préférable quand : données non-structurées/flexibles, scalability horizontale nécessaire, performance en écriture critique, développement rapide (schema-less), big data/analytics, géo-spatial queries. SQL est meilleur pour : relations complexes, ACID transactions, reporting/analytics complexes, data integrity critique.

---

**Temps de lecture estimé : 80 minutes**
**Niveau : Expert**
**Prérequis : Partie I + II + Chapitres 1, 2, 3**

---

🎉 **Félicitations !** Vous avez terminé la Partie III - Backend & Bases de données.

**Prochaines étapes :** Partie IV - DevOps et Sécurité

---

## 📊 Synthèse des acquis

### ✅ Compétences acquises :
- PHP/Laravel, Node.js/Express, Python/FastAPI
- Architecture patterns (MVC, DDD, Clean Architecture)
- APIs REST et GraphQL
- SQL et NoSQL optimization
- Database design et migrations
- Performance et monitoring

### 🎯 Niveau atteint :
- **Backend Development** : Expert
- **Database Design** : Expert
- **API Development** : Expert
- **Architecture** : Expert

### 🚀 Prêt pour :
- DevOps et automatisation
- Security et pentesting
- Full-stack applications
- Microservices architecture
- Cloud deployment
