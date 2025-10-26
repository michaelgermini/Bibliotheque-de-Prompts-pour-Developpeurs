# 📖 Partie VIII - Chapitre 1 : PHP MVC - Architecture et génération de code

## 🎯 Objectif du chapitre

Maîtriser l'architecture PHP MVC moderne, les patterns de conception avancés, et la génération de code assistée par IA pour des applications PHP robustes et maintenables.

## 🏗️ PHP Architecture : Architecture PHP moderne

### 1. 🏛️ MVC Architecture avancée

```
Tu es un expert PHP et architecture MVC moderne.

TA TÂCHE : Concevoir une architecture MVC avancée pour [APPLICATION]

ARCHITECTURE COMPONENTS :
1. **Model Layer** : Business logic, data validation, domain rules
2. **View Layer** : API resources, templates, data transformation
3. **Controller Layer** : Request handling, orchestration, validation
4. **Service Layer** : Business logic, use cases, domain services
5. **Repository Layer** : Data access, persistence, caching
6. **Middleware Layer** : HTTP middleware, authentication, logging

CONTRAINTES :
- Laravel/Symfony framework
- SOLID principles
- Clean Architecture
- Dependency injection
- Testing capabilities
- Performance optimization

FORMAT DE SORTIE :
1. Architecture overview document
2. Layer responsibilities
3. Code structure
4. Dependency injection setup
5. Testing strategy
6. Performance considerations
7. Migration plan

QUALITÉ : Architecture PHP scalable, maintenable, testée
```

**Exemple d'architecture MVC Laravel avancée :**
```php
<?php

// Domain Layer - Entities and Value Objects
namespace App\Domain\Order;

use App\Domain\Shared\ValueObject\Money;
use App\Domain\Shared\Entity\AggregateRoot;
use App\Domain\Order\Events\OrderCreated;
use Carbon\Carbon;

class Order extends AggregateRoot
{
    private OrderId $id;
    private Customer $customer;
    private OrderStatus $status;
    private Money $totalAmount;
    private ShippingAddress $shippingAddress;
    private BillingAddress $billingAddress;
    private PaymentMethod $paymentMethod;
    private array $items = [];
    private ?Carbon $createdAt;
    private ?Carbon $updatedAt;

    public function __construct(
        OrderId $id,
        Customer $customer,
        ShippingAddress $shippingAddress,
        BillingAddress $billingAddress,
        PaymentMethod $paymentMethod
    ) {
        $this->id = $id;
        $this->customer = $customer;
        $this->status = OrderStatus::pending();
        $this->shippingAddress = $shippingAddress;
        $this->billingAddress = $billingAddress;
        $this->paymentMethod = $paymentMethod;
        $this->totalAmount = Money::zero();
        $this->createdAt = Carbon::now();
        $this->updatedAt = Carbon::now();

        $this->addDomainEvent(new OrderCreated($this));
    }

    // Business methods
    public function addItem(Product $product, int $quantity): void
    {
        $this->ensureOrderIsModifiable();

        $item = new OrderItem(
            OrderItemId::generate(),
            $product,
            $quantity,
            $product->getPrice()
        );

        $this->items[] = $item;
        $this->recalculateTotal();
        $this->updatedAt = Carbon::now();
    }

    public function confirm(): void
    {
        $this->ensureOrderIsModifiable();
        $this->ensureOrderHasItems();

        $this->status = OrderStatus::confirmed();
        $this->updatedAt = Carbon::now();

        $this->addDomainEvent(new OrderStatusChanged($this, OrderStatus::pending(), $this->status));
    }

    public function cancel(string $reason): void
    {
        $this->status = OrderStatus::cancelled($reason);
        $this->updatedAt = Carbon::now();

        $this->addDomainEvent(new OrderCancelled($this, $reason));
    }

    // Private business logic methods
    private function ensureOrderIsModifiable(): void
    {
        if (!$this->status->isModifiable()) {
            throw new OrderNotModifiableException('Order cannot be modified in current status');
        }
    }

    private function ensureOrderHasItems(): void
    {
        if (empty($this->items)) {
            throw new OrderEmptyException('Cannot confirm empty order');
        }
    }

    private function recalculateTotal(): void
    {
        $this->totalAmount = array_reduce(
            $this->items,
            fn(Money $total, OrderItem $item) => $total->add($item->getSubtotal()),
            Money::zero()
        );
    }

    // Getters
    public function getId(): OrderId { return $this->id; }
    public function getCustomer(): Customer { return $this->customer; }
    public function getStatus(): OrderStatus { return $this->status; }
    public function getTotalAmount(): Money { return $this->totalAmount; }
    public function getItems(): array { return $this->items; }
    public function getCreatedAt(): ?Carbon { return $this->createdAt; }
    public function getUpdatedAt(): ?Carbon { return $this->updatedAt; }
}

// Application Layer - Use Cases
namespace App\Application\Order;

use App\Domain\Order\Order;
use App\Domain\Order\OrderRepository;
use App\Domain\Order\Events\OrderCreated;
use App\Application\Shared\UseCaseInterface;
use App\Application\Shared\CommandInterface;
use App\Application\Shared\HandlerInterface;

class CreateOrderCommand implements CommandInterface
{
    public function __construct(
        public readonly int $customerId,
        public readonly array $items,
        public readonly array $shippingAddress,
        public readonly array $billingAddress,
        public readonly string $paymentMethod
    ) {}
}

class CreateOrderHandler implements HandlerInterface
{
    public function __construct(
        private OrderRepository $orderRepository,
        private CustomerRepository $customerRepository,
        private ProductRepository $productRepository,
        private EventDispatcher $eventDispatcher
    ) {}

    public function handle(CreateOrderCommand $command): Order
    {
        // Get customer
        $customer = $this->customerRepository->findById($command->customerId);
        if (!$customer) {
            throw new CustomerNotFoundException('Customer not found');
        }

        // Validate and create order items
        $orderItems = $this->validateAndCreateOrderItems($command->items);

        // Create order
        $order = new Order(
            OrderId::generate(),
            $customer,
            new ShippingAddress($command->shippingAddress),
            new BillingAddress($command->billingAddress),
            new PaymentMethod($command->paymentMethod)
        );

        // Add items to order
        foreach ($orderItems as $item) {
            $order->addItem($item['product'], $item['quantity']);
        }

        // Save order
        $this->orderRepository->save($order);

        // Dispatch domain events
        $this->eventDispatcher->dispatch($order->getDomainEvents());

        return $order;
    }

    private function validateAndCreateOrderItems(array $items): array
    {
        $orderItems = [];

        foreach ($items as $item) {
            $product = $this->productRepository->findById($item['product_id']);

            if (!$product) {
                throw new ProductNotFoundException("Product {$item['product_id']} not found");
            }

            if (!$product->isAvailable()) {
                throw new ProductNotAvailableException("Product {$product->getName()} is not available");
            }

            if ($item['quantity'] <= 0 || $item['quantity'] > $product->getStock()) {
                throw new InvalidQuantityException("Invalid quantity for product {$product->getName()}");
            }

            $orderItems[] = [
                'product' => $product,
                'quantity' => $item['quantity']
            ];
        }

        return $orderItems;
    }
}
```

### 2. 🎯 Service Layer Architecture

```
Tu es un expert PHP service layer et business logic.

TA TÂCHE : Implémenter une couche service robuste pour [DOMAIN]

SERVICE LAYER COMPONENTS :
1. **Application Services** : Use cases, orchestration
2. **Domain Services** : Business logic, domain rules
3. **Infrastructure Services** : External APIs, caching, logging
4. **Event Services** : Event dispatching, handling
5. **Notification Services** : Email, SMS, push notifications
6. **Security Services** : Authentication, authorization, encryption

CONTRAINTES :
- SOLID principles
- Dependency injection
- Interface segregation
- Error handling
- Performance optimization

FORMAT DE SORTIE :
1. Service layer architecture
2. Service implementations
3. Interface definitions
4. Dependency injection
5. Error handling
6. Performance optimization
7. Testing strategies

QUALITÉ : Services robustes, testables, performants
```

**Exemple de service layer :**
```php
<?php

// Application Services - Use Cases
namespace App\Application\Order;

use App\Domain\Order\Order;
use App\Domain\Order\OrderRepository;
use App\Domain\Order\Events\OrderCreated;
use App\Application\Shared\UseCaseInterface;
use App\Application\Shared\CommandInterface;
use App\Application\Shared\HandlerInterface;

class UpdateOrderStatusCommand implements CommandInterface
{
    public function __construct(
        public readonly string $orderId,
        public readonly string $newStatus,
        public readonly ?string $reason = null
    ) {}
}

class UpdateOrderStatusHandler implements HandlerInterface
{
    public function __construct(
        private OrderRepository $orderRepository,
        private OrderStatusValidator $statusValidator,
        private EventDispatcher $eventDispatcher
    ) {}

    public function handle(UpdateOrderStatusCommand $command): Order
    {
        $order = $this->orderRepository->findById($command->orderId);

        if (!$order) {
            throw new OrderNotFoundException('Order not found');
        }

        // Validate status transition
        $this->statusValidator->validateTransition(
            $order->getStatus(),
            new OrderStatus($command->newStatus)
        );

        // Update order status
        $oldStatus = $order->getStatus();
        $order->updateStatus(new OrderStatus($command->newStatus), $command->reason);

        // Save order
        $this->orderRepository->save($order);

        // Dispatch events
        $this->eventDispatcher->dispatch($order->getDomainEvents());

        return $order;
    }
}

// Domain Services - Business Logic
namespace App\Domain\Order;

class OrderStatusValidator
{
    private array $validTransitions = [
        OrderStatus::PENDING => [OrderStatus::CONFIRMED, OrderStatus::CANCELLED],
        OrderStatus::CONFIRMED => [OrderStatus::PROCESSING, OrderStatus::CANCELLED],
        OrderStatus::PROCESSING => [OrderStatus::SHIPPED, OrderStatus::CANCELLED],
        OrderStatus::SHIPPED => [OrderStatus::DELIVERED],
        OrderStatus::DELIVERED => [], // Final state
        OrderStatus::CANCELLED => [] // Final state
    ];

    public function validateTransition(OrderStatus $currentStatus, OrderStatus $newStatus): void
    {
        if ($currentStatus->equals($newStatus)) {
            throw new InvalidStatusTransitionException('Cannot transition to same status');
        }

        $allowedTransitions = $this->validTransitions[$currentStatus->getValue()] ?? [];

        if (!in_array($newStatus, $allowedTransitions)) {
            throw new InvalidStatusTransitionException(
                "Cannot transition from {$currentStatus->getValue()} to {$newStatus->getValue()}"
            );
        }
    }

    public function getAllowedTransitions(OrderStatus $currentStatus): array
    {
        return $this->validTransitions[$currentStatus->getValue()] ?? [];
    }
}

// Infrastructure Services - External Integration
namespace App\Infrastructure\Services;

class PaymentService
{
    private HttpClientInterface $httpClient;
    private PaymentConfig $config;
    private LoggerInterface $logger;

    public function __construct(
        HttpClientInterface $httpClient,
        PaymentConfig $config,
        LoggerInterface $logger
    ) {
        $this->httpClient = $httpClient;
        $this->config = $config;
        $this->logger = $logger;
    }

    public function processPayment(PaymentRequest $request): PaymentResult
    {
        try {
            $this->logger->info('Processing payment', [
                'order_id' => $request->orderId,
                'amount' => $request->amount,
                'currency' => $request->currency
            ]);

            $response = $this->httpClient->post('/payments/charge', [
                'json' => [
                    'amount' => $request->amount,
                    'currency' => $request->currency,
                    'payment_method' => $request->paymentMethod,
                    'order_id' => $request->orderId,
                    'customer_id' => $request->customerId
                ],
                'headers' => [
                    'Authorization' => 'Bearer ' . $this->config->getApiKey(),
                    'Content-Type' => 'application/json'
                ],
                'timeout' => 30
            ]);

            $data = json_decode($response->getBody(), true);

            if ($response->getStatusCode() !== 200) {
                throw new PaymentProcessingException($data['message'] ?? 'Payment failed');
            }

            $this->logger->info('Payment processed successfully', [
                'order_id' => $request->orderId,
                'transaction_id' => $data['transaction_id']
            ]);

            return new PaymentResult(
                true,
                $data['transaction_id'],
                $data['status'],
                $data['message']
            );

        } catch (Exception $e) {
            $this->logger->error('Payment processing failed', [
                'order_id' => $request->orderId,
                'error' => $e->getMessage()
            ]);

            return new PaymentResult(
                false,
                null,
                'failed',
                $e->getMessage()
            );
        }
    }

    public function refundPayment(string $transactionId, float $amount): RefundResult
    {
        try {
            $response = $this->httpClient->post('/payments/refund', [
                'json' => [
                    'transaction_id' => $transactionId,
                    'amount' => $amount
                ],
                'headers' => [
                    'Authorization' => 'Bearer ' . $this->config->getApiKey()
                ]
            ]);

            $data = json_decode($response->getBody(), true);

            return new RefundResult(
                $response->getStatusCode() === 200,
                $data['refund_id'] ?? null,
                $data['status'],
                $data['message'] ?? ''
            );

        } catch (Exception $e) {
            $this->logger->error('Payment refund failed', [
                'transaction_id' => $transactionId,
                'error' => $e->getMessage()
            ]);

            return new RefundResult(false, null, 'failed', $e->getMessage());
        }
    }
}
```

## 🏭 Code Generation : Génération de code PHP

### 1. 🤖 AI-Powered Code Generation

```
Tu es un expert PHP code generation et Laravel development.

TA TÂCHE : Générer du code PHP avec assistance IA pour [FONCTIONNALITÉ]

CODE À GÉNÉRER :
1. **Model Classes** : Eloquent models, relationships, scopes
2. **Controller Classes** : CRUD operations, validation, authorization
3. **Service Classes** : Business logic, use cases, domain services
4. **Repository Classes** : Data access, caching, optimization
5. **Migration Files** : Database schema, constraints, indexes
6. **Seeder Files** : Test data, factories, relationships
7. **Test Classes** : Unit tests, integration tests, feature tests

CONTRAINTES :
- Laravel framework
- PHP 8.1+ features
- PSR-12 standards
- Testing coverage
- Performance optimization

FORMAT DE SORTIE :
1. Code architecture
2. Generated classes
3. Database migrations
4. Test suites
5. Documentation
6. Performance optimization
7. Security hardening

QUALITÉ : Code PHP généré production-ready, testé, optimisé
```

**Exemple de génération de code :**
```php
<?php

// Generated Model Class
namespace App\Models;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\BelongsTo;
use Illuminate\Database\Eloquent\Relations\HasMany;
use Illuminate\Database\Eloquent\SoftDeletes;
use Illuminate\Database\Eloquent\Factories\HasFactory;
use App\Domain\Shared\Traits\HasUuid;

class Product extends Model
{
    use HasFactory, SoftDeletes, HasUuid;

    protected $fillable = [
        'uuid',
        'sku',
        'name',
        'description',
        'short_description',
        'price',
        'compare_at_price',
        'cost_price',
        'track_quantity',
        'quantity',
        'low_stock_threshold',
        'category_id',
        'brand',
        'status',
        'weight',
        'dimensions',
        'meta_data',
        'is_featured',
        'is_digital',
        'download_url',
        'download_limit',
        'download_expiry_days'
    ];

    protected $casts = [
        'price' => 'decimal:2',
        'compare_at_price' => 'decimal:2',
        'cost_price' => 'decimal:2',
        'track_quantity' => 'boolean',
        'quantity' => 'integer',
        'low_stock_threshold' => 'integer',
        'weight' => 'decimal:3',
        'dimensions' => 'array',
        'meta_data' => 'array',
        'is_featured' => 'boolean',
        'is_digital' => 'boolean',
        'download_limit' => 'integer',
        'download_expiry_days' => 'integer',
        'created_at' => 'datetime',
        'updated_at' => 'datetime',
        'deleted_at' => 'datetime'
    ];

    protected $attributes = [
        'track_quantity' => true,
        'quantity' => 0,
        'low_stock_threshold' => 10,
        'status' => 'draft',
        'is_featured' => false,
        'is_digital' => false,
        'download_limit' => null,
        'download_expiry_days' => 30
    ];

    // Relationships
    public function category(): BelongsTo
    {
        return $this->belongsTo(Category::class);
    }

    public function variants(): HasMany
    {
        return $this->hasMany(ProductVariant::class);
    }

    public function images(): HasMany
    {
        return $this->hasMany(ProductImage::class)->orderBy('sort_order');
    }

    public function reviews(): HasMany
    {
        return $this->hasMany(ProductReview::class);
    }

    public function orderItems(): HasMany
    {
        return $this->hasMany(OrderItem::class);
    }

    // Scopes
    public function scopeActive($query)
    {
        return $query->where('status', 'active');
    }

    public function scopeFeatured($query)
    {
        return $query->where('is_featured', true);
    }

    public function scopeInStock($query)
    {
        return $query->where(function ($q) {
            $q->where('track_quantity', false)
              ->orWhere('quantity', '>', 0);
        });
    }

    public function scopeByCategory($query, $categoryId)
    {
        return $query->where('category_id', $categoryId);
    }

    public function scopeSearch($query, string $search)
    {
        return $query->where(function ($q) use ($search) {
            $q->where('name', 'like', "%{$search}%")
              ->orWhere('description', 'like', "%{$search}%")
              ->orWhere('sku', 'like', "%{$search}%");
        });
    }

    public function scopePriceRange($query, float $min, float $max)
    {
        return $query->whereBetween('price', [$min, $max]);
    }

    // Business methods
    public function isAvailable(): bool
    {
        return $this->status === 'active' &&
               (!$this->track_quantity || $this->quantity > 0);
    }

    public function isLowStock(): bool
    {
        return $this->track_quantity &&
               $this->quantity > 0 &&
               $this->quantity <= $this->low_stock_threshold;
    }

    public function isOutOfStock(): bool
    {
        return $this->track_quantity && $this->quantity <= 0;
    }

    public function getDiscountedPrice(): float
    {
        if ($this->compare_at_price && $this->compare_at_price > $this->price) {
            return $this->price;
        }
        return $this->compare_at_price ?? $this->price;
    }

    public function getDiscountPercentage(): float
    {
        if ($this->compare_at_price && $this->compare_at_price > $this->price) {
            return round((($this->compare_at_price - $this->price) / $this->compare_at_price) * 100, 2);
        }
        return 0;
    }

    public function hasDiscount(): bool
    {
        return $this->compare_at_price && $this->compare_at_price > $this->price;
    }

    public function getPrimaryImage(): ?ProductImage
    {
        return $this->images()->where('is_primary', true)->first() ??
               $this->images()->first();
    }

    public function getAverageRating(): float
    {
        return $this->reviews()
            ->where('is_approved', true)
            ->avg('rating') ?? 0;
    }

    public function getReviewCount(): int
    {
        return $this->reviews()
            ->where('is_approved', true)
            ->count();
    }

    public function isDigital(): bool
    {
        return $this->is_digital;
    }

    public function canBeDownloaded(): bool
    {
        return $this->is_digital && !empty($this->download_url);
    }

    // Event handlers
    protected static function booted()
    {
        static::creating(function ($product) {
            if (empty($product->uuid)) {
                $product->uuid = (string) Str::uuid();
            }
        });

        static::updating(function ($product) {
            // Clear product cache when updated
            Cache::forget("product:{$product->uuid}");
        });

        static::deleted(function ($product) {
            // Clear product cache when deleted
            Cache::forget("product:{$product->uuid}");
        });
    }
}
```

### 2. 📊 Database Migration Generation

```
Tu es un expert Laravel migrations et database design.

TA TÂCHE : Générer des migrations de base de données pour [SCHEMA]

MIGRATIONS À GÉNÉRER :
1. **Table Creation** : Primary tables, relationships
2. **Index Creation** : Performance indexes, constraints
3. **Constraint Creation** : Foreign keys, check constraints
4. **Data Migration** : Data transformation, seeding
5. **Rollback Scripts** : Safe rollback procedures
6. **Performance Optimization** : Query optimization, partitioning

CONTRAINTES :
- Laravel migration syntax
- Database constraints
- Performance considerations
- Rollback safety
- Data integrity

FORMAT DE SORTIE :
1. Migration files
2. Index definitions
3. Constraint setup
4. Seeder files
5. Rollback procedures
6. Performance optimization
7. Testing validation

QUALITÉ : Migrations sûres, optimisées, testées
```

## 📋 Templates PHP MVC

### 🏗️ Template Architecture
```
Tu es un expert PHP architecture et [FRAMEWORK].

TA TÂCHE : Concevoir architecture MVC pour [APPLICATION]

COUCHES :
- Model : Business logic
- View : Data transformation
- Controller : Request handling
- Service : Business orchestration
- Repository : Data persistence

CONTRAINTES :
- SOLID principles
- Clean Architecture
- Dependency injection
- Testing capabilities

FORMAT :
1. Architecture overview
2. Layer responsibilities
3. Code structure
4. Dependency injection
5. Testing strategy
6. Performance considerations
7. Migration plan

QUALITÉ : Architecture scalable, maintenable, testée
```

### 🔧 Template Code Generation
```
Tu es un expert PHP code generation et [FRAMEWORK].

TA TÂCHE : Générer code PHP pour [FONCTIONNALITÉ]

CODE :
- Models avec relationships
- Controllers avec validation
- Services avec business logic
- Migrations avec constraints
- Tests avec coverage

CONTRAINTES :
- PHP 8.1+ features
- PSR-12 standards
- Testing coverage
- Performance optimization

FORMAT :
1. Code architecture
2. Generated classes
3. Database migrations
4. Test suites
5. Documentation
6. Performance optimization
7. Security hardening

QUALITÉ : Code production-ready, testé, optimisé
```

### 🗄️ Template Database Migration
```
Tu es un expert Laravel migrations et database design.

TA TÂCHE : Générer migrations pour [SCHEMA]

MIGRATIONS :
- Table creation
- Index optimization
- Constraint setup
- Data seeding
- Rollback procedures

CONTRAINTES :
- Laravel syntax
- Performance indexes
- Data integrity
- Rollback safety

FORMAT :
1. Migration files
2. Index definitions
3. Constraint setup
4. Seeder files
5. Rollback procedures
6. Performance optimization
7. Testing validation

QUALITÉ : Migrations sûres, optimisées, testées
```

## 🎯 Points clés à retenir

1. **Clean Architecture** : Separation of concerns, dependency inversion
2. **Domain-Driven Design** : Business focus, ubiquitous language
3. **Service Layer** : Business logic encapsulation, reusability
4. **Repository Pattern** : Data access abstraction, testability
5. **Event-Driven** : Loose coupling, scalability, audit trail
6. **SOLID Principles** : Maintainable, extensible, testable code

## 🚀 Prochain chapitre

Maintenant que vous maîtrisez PHP MVC, découvrons les prompts React pour composants, hooks, state et accessibilité.

---

## 📋 Checklist qualité

- ✅ PHP architecture design
- ✅ Service layer implementation
- ✅ Code generation patterns
- ✅ Database migration strategy
- ✅ Testing integration
- ✅ Performance optimization

## 🎯 Test de compréhension

**Question :** Quelle est la principale différence entre un service d'application et un service de domaine ?

**Réponse attendue :** Un service d'application orchestre les opérations et coordonne entre plusieurs domaines (use cases), tandis qu'un service de domaine contient la logique métier pure d'un domaine spécifique et est indépendant de l'infrastructure.

---

**Temps de lecture estimé : 145 minutes**
**Niveau : Expert**
**Prérequis : Partie I + II + III + IV + V + VI + VII**
