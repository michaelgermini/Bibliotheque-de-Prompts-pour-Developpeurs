# 📖 Partie III - Chapitre 2 : Architecture logicielle

## 🎯 Objectif du chapitre

Maîtriser les patterns d'architecture modernes : MVC, DDD (Domain Driven Design), Clean Architecture, et Hexagonal Architecture pour des applications backend maintenables et scalables.

## 🏛️ MVC (Model-View-Controller) : Architecture classique

### 1. 🏗️ MVC Architecture moderne

```
Tu es un expert architecture MVC et design patterns.

TA TÂCHE : Implémenter une architecture MVC moderne pour [APPLICATION]

COMPOSANTS MVC :
1. **Model** : Business logic, data validation, domain rules
2. **View** : API responses, templates, data transformation
3. **Controller** : Request handling, input validation, orchestration

SPÉCIFICATIONS :
- Séparation claire des responsabilités (SRP)
- Dependency injection
- Request validation
- Error handling centralisé
- Testing de chaque couche

CONTRAINTES :
- Framework : [LARAVEL/EXPRESS/DJANGO]
- SOLID principles respectés
- Testability maximale
- Performance optimisée
- Documentation des interfaces

FORMAT DE SORTIE :
1. Architecture MVC documentée
2. Models avec business logic
3. Controllers avec validation
4. Views/Resources pour API
5. Services pour orchestration
6. Tests unitaires par couche
7. Diagramme d'architecture

QUALITÉ : Architecture MVC claire, testable, maintenable
```

**Structure MVC Laravel :**
```
📁 app/
├── 📁 Http/Controllers/     # Controllers (Request/Response)
│   ├── UserController.php
│   ├── ProductController.php
│   └── OrderController.php
├── 📁 Models/              # Models (Business Logic)
│   ├── User.php
│   ├── Product.php
│   └── Order.php
├── 📁 Http/Resources/      # Views (API Resources)
│   ├── UserResource.php
│   ├── ProductResource.php
│   └── OrderResource.php
├── 📁 Services/            # Business Services
│   ├── UserService.php
│   ├── ProductService.php
│   └── OrderService.php
├── 📁 Repositories/        # Data Access
│   ├── UserRepository.php
│   ├── ProductRepository.php
│   └── OrderRepository.php
└── 📁 Rules/              # Validation Rules
    ├── UserRules.php
    └── ProductRules.php
```

**Exemple de Controller MVC :**
```php
<?php

namespace App\Http\Controllers;

use App\Http\Requests\StoreOrderRequest;
use App\Http\Resources\OrderResource;
use App\Models\Order;
use App\Services\OrderService;
use App\Repositories\OrderRepository;
use Illuminate\Http\JsonResponse;
use Illuminate\Http\Request;

class OrderController extends Controller
{
    public function __construct(
        private OrderService $orderService,
        private OrderRepository $orderRepository
    ) {}

    /**
     * Display a listing of orders with filtering and pagination
     */
    public function index(Request $request): JsonResponse
    {
        // Input validation
        $validated = $request->validate([
            'status' => 'sometimes|in:pending,processing,completed,cancelled',
            'user_id' => 'sometimes|exists:users,id',
            'date_from' => 'sometimes|date',
            'date_to' => 'sometimes|date|after_or_equal:date_from',
            'page' => 'sometimes|integer|min:1',
            'per_page' => 'sometimes|integer|min:1|max:100',
        ]);

        // Controller logic: orchestrate the request
        $filters = [
            'status' => $validated['status'] ?? null,
            'user_id' => $validated['user_id'] ?? null,
            'date_from' => $validated['date_from'] ?? null,
            'date_to' => $validated['date_to'] ?? null,
        ];

        $pagination = [
            'page' => $validated['page'] ?? 1,
            'per_page' => $validated['per_page'] ?? 15,
        ];

        // Delegate to service layer
        $orders = $this->orderService->getOrdersWithFilters($filters, $pagination);

        // Transform for API response (View layer)
        return response()->json([
            'data' => OrderResource::collection($orders),
            'meta' => [
                'pagination' => [
                    'current_page' => $orders->currentPage(),
                    'per_page' => $orders->perPage(),
                    'total' => $orders->total(),
                    'last_page' => $orders->lastPage(),
                ]
            ]
        ]);
    }

    /**
     * Store a newly created order
     */
    public function store(StoreOrderRequest $request): JsonResponse
    {
        // Request validation handled by Form Request

        // Controller logic: orchestrate creation
        $orderData = $request->validated();
        $orderData['user_id'] = auth()->id(); // Get authenticated user

        // Delegate to service layer
        $order = $this->orderService->createOrder($orderData);

        // Return transformed response
        return response()->json([
            'message' => 'Order created successfully',
            'data' => new OrderResource($order)
        ], 201);
    }

    /**
     * Display the specified order
     */
    public function show(Order $order): JsonResponse
    {
        // Policy authorization handled automatically by model binding

        // Load relationships
        $order->load(['user', 'items.product', 'payments']);

        // Return transformed response
        return response()->json([
            'data' => new OrderResource($order)
        ]);
    }

    /**
     * Update the specified order
     */
    public function update(Request $request, Order $order): JsonResponse
    {
        // Input validation
        $validated = $request->validate([
            'status' => 'required|in:pending,processing,completed,cancelled',
            'shipping_address' => 'sometimes|array',
            'billing_address' => 'sometimes|array',
        ]);

        // Delegate to service layer
        $updatedOrder = $this->orderService->updateOrderStatus($order, $validated);

        // Return transformed response
        return response()->json([
            'message' => 'Order updated successfully',
            'data' => new OrderResource($updatedOrder)
        ]);
    }

    /**
     * Remove the specified order
     */
    public function destroy(Order $order): JsonResponse
    {
        // Delegate to service layer
        $this->orderService->cancelOrder($order);

        return response()->json([
            'message' => 'Order cancelled successfully'
        ], 204);
    }
}
```

## 🎯 DDD (Domain Driven Design) : Focus métier

### 1. 🏗️ Domain Driven Design Architecture

```
Tu es un expert Domain Driven Design et clean architecture.

TA TÂCHE : Implémenter une architecture DDD pour [DOMAINE MÉTIER]

COMPOSANTS DDD :
1. **Domain Layer** : Entities, Value Objects, Aggregates, Domain Services
2. **Application Layer** : Application Services, DTOs, Use Cases
3. **Infrastructure Layer** : Repositories, External Services, Persistence
4. **Presentation Layer** : Controllers, Views, API Resources

CONTRAINTES DDD :
- Ubiquitous Language (langage métier)
- Bounded Contexts (limites métier)
- Domain invariants (règles métier)
- Isolation des couches
- Testability du domain

FORMAT DE SORTIE :
1. Domain model avec ubiquitous language
2. Bounded contexts identifiés
3. Entities et Value Objects
4. Aggregates et root entities
5. Domain services pour business logic
6. Application services pour use cases
7. Infrastructure repositories
8. Tests du domain model

QUALITÉ : Architecture DDD métier-centrique, testable, évolutive
```

**Exemple de Domain Model DDD :**
```php
<?php

namespace App\Domain\Order;

use App\Domain\User\User;
use App\Domain\Product\Product;
use App\Domain\Shared\ValueObject\Money;
use App\Domain\Shared\Entity\AggregateRoot;
use App\Domain\Order\Events\OrderCreated;
use App\Domain\Order\Events\OrderStatusChanged;
use Carbon\Carbon;

// Entity - Order Aggregate Root
class Order extends AggregateRoot
{
    private OrderId $id;
    private User $customer;
    private OrderStatus $status;
    private Money $totalAmount;
    private ShippingAddress $shippingAddress;
    private ?Carbon $createdAt;
    private ?Carbon $updatedAt;

    // Order Items Collection (private collection)
    private array $items = [];

    public function __construct(
        OrderId $id,
        User $customer,
        ShippingAddress $shippingAddress
    ) {
        $this->id = $id;
        $this->customer = $customer;
        $this->status = OrderStatus::pending();
        $this->shippingAddress = $shippingAddress;
        $this->totalAmount = Money::zero();
        $this->createdAt = Carbon::now();
        $this->updatedAt = Carbon::now();

        // Domain Event
        $this->addDomainEvent(new OrderCreated($this));
    }

    // Business Methods (Domain Logic)
    public function addItem(Product $product, int $quantity): void
    {
        // Domain invariant: quantity must be positive
        if ($quantity <= 0) {
            throw new InvalidQuantityException('Quantity must be positive');
        }

        // Domain invariant: product must be available
        if (!$product->isAvailable()) {
            throw new ProductNotAvailableException('Product is not available');
        }

        // Check if item already exists
        $existingItem = $this->findItemByProduct($product);

        if ($existingItem) {
            $existingItem->increaseQuantity($quantity);
        } else {
            $item = new OrderItem(
                OrderItemId::generate(),
                $product,
                $quantity,
                $product->getPrice()
            );
            $this->items[] = $item;
        }

        // Recalculate total
        $this->calculateTotal();

        $this->updatedAt = Carbon::now();
    }

    public function removeItem(Product $product): void
    {
        $item = $this->findItemByProduct($product);

        if (!$item) {
            throw new ItemNotFoundException('Item not found in order');
        }

        // Remove item
        $this->items = array_filter(
            $this->items,
            fn($item) => $item->getProduct()->getId()->equals($product->getId())
        );

        // Recalculate total
        $this->calculateTotal();

        $this->updatedAt = Carbon::now();
    }

    public function confirm(): void
    {
        // Domain invariant: order must have items
        if (empty($this->items)) {
            throw new OrderEmptyException('Cannot confirm empty order');
        }

        // Domain invariant: order must be in pending status
        if (!$this->status->equals(OrderStatus::pending())) {
            throw new InvalidOrderStatusException('Only pending orders can be confirmed');
        }

        // Business rule: validate shipping address
        $this->validateShippingAddress();

        // Change status
        $this->status = OrderStatus::confirmed();

        // Domain Event
        $this->addDomainEvent(new OrderStatusChanged($this, OrderStatus::pending(), $this->status));

        $this->updatedAt = Carbon::now();
    }

    public function cancel(string $reason): void
    {
        // Domain rule: cannot cancel completed orders
        if ($this->status->equals(OrderStatus::completed())) {
            throw new CannotCancelCompletedOrderException('Cannot cancel completed order');
        }

        $previousStatus = $this->status;
        $this->status = OrderStatus::cancelled($reason);

        // Domain Event
        $this->addDomainEvent(new OrderStatusChanged($this, $previousStatus, $this->status));

        $this->updatedAt = Carbon::now();
    }

    // Private methods (Domain Logic)
    private function calculateTotal(): void
    {
        $total = array_reduce(
            $this->items,
            fn($sum, $item) => $sum->add($item->getSubtotal()),
            Money::zero()
        );

        $this->totalAmount = $total;
    }

    private function findItemByProduct(Product $product): ?OrderItem
    {
        return array_find(
            $this->items,
            fn($item) => $item->getProduct()->getId()->equals($product->getId())
        );
    }

    private function validateShippingAddress(): void
    {
        // Business rule: validate shipping constraints
        if (!$this->shippingAddress->isValid()) {
            throw new InvalidShippingAddressException('Invalid shipping address');
        }
    }

    // Getters
    public function getId(): OrderId { return $this->id; }
    public function getCustomer(): User { return $this->customer; }
    public function getStatus(): OrderStatus { return $this->status; }
    public function getTotalAmount(): Money { return $this->totalAmount; }
    public function getShippingAddress(): ShippingAddress { return $this->shippingAddress; }
    public function getItems(): array { return $this->items; }
    public function getCreatedAt(): ?Carbon { return $this->createdAt; }
    public function getUpdatedAt(): ?Carbon { return $this->updatedAt; }
}
```

## 🏗️ Clean Architecture : Indépendance des frameworks

### 1. 🏛️ Clean Architecture Implementation

```
Tu es un expert Clean Architecture et SOLID principles.

TA TÂCHE : Implémenter Clean Architecture pour [APPLICATION]

COUCHES CLEAN ARCHITECTURE :
1. **Entities** : Business objects du domaine
2. **Use Cases** : Application business logic
3. **Interface Adapters** : Controllers, gateways, presenters
4. **Frameworks** : Web frameworks, databases, external services

CONTRAINTES CLEAN ARCHITECTURE :
- Dependency inversion (interfaces)
- SOLID principles stricts
- Framework independence
- High testability
- Business logic isolation

FORMAT DE SORTIE :
1. Architecture Clean documentée
2. Entities du domaine
3. Use cases d'application
4. Interface adapters (controllers, repositories)
5. Framework implementations
6. Tests d'isolation
7. Dependency injection setup

QUALITÉ : Architecture Clean testable, framework-agnostic
```

**Structure Clean Architecture :**
```
📁 src/
├── 📁 Domain/              # Business rules (Entities, Use Cases)
│   ├── 📁 Entities/        # Business objects
│   ├── 📁 UseCases/        # Application logic
│   ├── 📁 Services/        # Domain services
│   └── 📁 Repositories/    # Repository interfaces
├── 📁 Application/         # Application layer
│   ├── 📁 DTOs/           # Data Transfer Objects
│   ├── 📁 Services/       # Application services
│   └── 📁 Mappers/        # Object mappers
├── 📁 Infrastructure/      # External concerns
│   ├── 📁 Controllers/    # Web controllers
│   ├── 📁 Repositories/   # Repository implementations
│   ├── 📁 Services/       # External services
│   └── 📁 Database/       # Database setup
└── 📁 Shared/             # Shared kernel
    ├── 📁 Kernel/         # Domain events, etc.
    └── 📁 Utils/          # Utilities
```

**Exemple Clean Architecture :**
```typescript
// Domain Layer - Entity
export class Product {
  constructor(
    public readonly id: ProductId,
    public readonly name: ProductName,
    public readonly price: Money,
    public readonly category: Category,
    public readonly status: ProductStatus,
    public readonly createdAt: Date,
    public readonly updatedAt: Date
  ) {}

  // Business method
  public updatePrice(newPrice: Money): Product {
    // Domain invariant: price must be positive
    if (newPrice.isNegative()) {
      throw new DomainError('Price must be positive');
    }

    return new Product(
      this.id,
      this.name,
      newPrice,
      this.category,
      this.status,
      this.createdAt,
      new Date()
    );
  }

  // Business method
  public markAsAvailable(): Product {
    if (!this.status.canTransitionTo(ProductStatus.AVAILABLE)) {
      throw new DomainError('Cannot mark product as available');
    }

    return new Product(
      this.id,
      this.name,
      this.price,
      this.category,
      ProductStatus.AVAILABLE,
      this.createdAt,
      new Date()
    );
  }
}

// Domain Layer - Repository Interface
export interface ProductRepository {
  findById(id: ProductId): Promise<Product | null>;
  findByCategory(category: Category): Promise<Product[]>;
  save(product: Product): Promise<void>;
  delete(id: ProductId): Promise<void>;
  findAll(): Promise<Product[]>;
}

// Application Layer - Use Case
export class UpdateProductPriceUseCase {
  constructor(private productRepository: ProductRepository) {}

  async execute(
    productId: ProductId,
    newPrice: Money
  ): Promise<Product> {
    // Get product
    const product = await this.productRepository.findById(productId);

    if (!product) {
      throw new NotFoundError('Product not found');
    }

    // Business logic
    const updatedProduct = product.updatePrice(newPrice);

    // Save
    await this.productRepository.save(updatedProduct);

    return updatedProduct;
  }
}

// Infrastructure Layer - Repository Implementation
export class PostgresProductRepository implements ProductRepository {
  constructor(private db: DatabaseConnection) {}

  async findById(id: ProductId): Promise<Product | null> {
    const result = await this.db.query(
      'SELECT * FROM products WHERE id = $1',
      [id.value]
    );

    if (result.rows.length === 0) {
      return null;
    }

    return this.mapToDomain(result.rows[0]);
  }

  async save(product: Product): Promise<void> {
    await this.db.query(
      `UPDATE products SET
        name = $1, price = $2, category_id = $3, status = $4, updated_at = $5
       WHERE id = $6`,
      [
        product.name.value,
        product.price.amount,
        product.category.id,
        product.status.value,
        product.updatedAt,
        product.id.value
      ]
    );
  }

  private mapToDomain(row: any): Product {
    return new Product(
      new ProductId(row.id),
      new ProductName(row.name),
      new Money(row.price, row.currency),
      new Category(new CategoryId(row.category_id), row.category_name),
      ProductStatus.fromValue(row.status),
      new Date(row.created_at),
      new Date(row.updated_at)
    );
  }
}

// Interface Adapter - Controller
export class ProductController {
  constructor(
    private updateProductPriceUseCase: UpdateProductPriceUseCase,
    private productPresenter: ProductPresenter
  ) {}

  async updatePrice(req: Request, res: Response): Promise<void> {
    try {
      const { productId, price } = req.body;

      const updatedProduct = await this.updateProductPriceUseCase.execute(
        new ProductId(productId),
        new Money(price.amount, price.currency)
      );

      const response = this.productPresenter.present(updatedProduct);

      res.status(200).json({
        success: true,
        data: response
      });
    } catch (error) {
      // Error handling
      res.status(400).json({
        success: false,
        error: error.message
      });
    }
  }
}
```

## 🌀 Hexagonal Architecture : Ports & Adapters

### 1. 🏗️ Hexagonal Architecture Implementation

```
Tu es un expert Hexagonal Architecture et ports & adapters.

TA TÂCHE : Implémenter une architecture hexagonale pour [APPLICATION]

COMPOSANTS HEXAGONAL :
1. **Domain** : Core business logic (indépendant)
2. **Ports** : Interfaces (abstractions)
3. **Adapters** : Implementations (infrastructure)
4. **Application** : Use cases et orchestration

CONTRAINTES HEXAGONAL :
- Framework independence
- Testability maximale
- Business logic isolation
- Easy to change infrastructure
- Clear separation of concerns

FORMAT DE SORTIE :
1. Domain core business logic
2. Port interfaces (abstractions)
3. Adapter implementations
4. Application services
5. Dependency injection setup
6. Tests d'isolation
7. Migration strategy

QUALITÉ : Architecture hexagonale testable, framework-agnostic
```

## 📊 Patterns d'architecture avancés

### 1. 🎯 CQRS (Command Query Responsibility Segregation)

```
Tu es un expert CQRS et event sourcing patterns.

TA TÂCHE : Implémenter CQRS pour [DOMAINE] avec séparation reads/writes

COMPOSANTS CQRS :
1. **Commands** : Write operations (Create, Update, Delete)
2. **Queries** : Read operations (Get, List, Search)
3. **Command Handlers** : Business logic for writes
4. **Query Handlers** : Optimized reads
5. **Event Store** : Event sourcing optionnel

CONTRAINTES CQRS :
- Command model ≠ Query model
- Eventual consistency acceptance
- Performance optimization per use case
- Complexité de synchronisation

FORMAT DE SORTIE :
1. Command/Query separation
2. Command handlers implementation
3. Query handlers optimized
4. Event store setup
5. Synchronization mechanisms
6. Performance testing
7. Consistency guarantees

QUALITÉ : CQRS performant, scalable, consistent
```

### 2. 🎭 Event-Driven Architecture

```
Tu es un expert event-driven systems et message queues.

TA TÂCHE : Implémenter une architecture event-driven pour [APPLICATION]

COMPOSANTS EVENT-DRIVEN :
1. **Events** : Domain events, integration events
2. **Event Bus** : Message routing
3. **Event Handlers** : Event processing
4. **Message Queue** : RabbitMQ, Kafka, SQS
5. **Event Store** : Event sourcing

CONTRAINTES EVENT-DRIVEN :
- Loose coupling entre services
- Eventual consistency
- Error handling et retry logic
- Monitoring et observability
- Performance impact des queues

FORMAT DE SORTIE :
1. Event model design
2. Event bus implementation
3. Event handlers
4. Message queue setup
5. Error handling strategies
6. Monitoring et alerting
7. Performance optimization

QUALITÉ : Architecture event-driven scalable, decoupled
```

## 📝 Templates d'architecture

### 🏗️ Template MVC Architecture
```
Tu es un expert architecture MVC et [FRAMEWORK].

TA TÂCHE : Implémenter architecture MVC pour [APPLICATION]

COUCHES MVC :
- Model : [BUSINESS LOGIC]
- View : [API RESOURCES/TEMPLATES]
- Controller : [REQUEST HANDLING]

CONTRAINTES :
- SOLID principles
- Dependency injection
- Request validation
- Error handling centralisé

FORMAT :
1. Structure MVC documentée
2. Models avec business logic
3. Controllers orchestration
4. Views/Resources transformation
5. Services business layer
6. Tests par couche
7. Performance optimizations

QUALITÉ : Architecture MVC claire, testable, maintenable
```

### 🎯 Template Domain Driven Design
```
Tu es un expert Domain Driven Design et clean code.

TA TÂCHE : Implémenter DDD pour [DOMAINE MÉTIER]

COMPOSANTS DDD :
- Ubiquitous Language
- Bounded Contexts
- Entities et Value Objects
- Aggregates et invariants

CONTRAINTES :
- Business focus
- Framework independence
- Domain isolation
- Testability maximale

FORMAT :
1. Domain model métier
2. Bounded contexts identifiés
3. Entities et aggregates
4. Domain services
5. Application services
6. Infrastructure layer
7. Tests du domain

QUALITÉ : Architecture DDD business-centric, testable
```

### 🏛️ Template Clean Architecture
```
Tu es un expert Clean Architecture et SOLID.

TA TÂCHE : Implémenter Clean Architecture pour [APPLICATION]

COUCHES CLEAN :
- Entities (business rules)
- Use Cases (application logic)
- Interface Adapters (controllers, repositories)
- Frameworks (infrastructure)

CONTRAINTES :
- Dependency inversion
- Framework independence
- High testability
- Business logic isolation

FORMAT :
1. Clean Architecture documentée
2. Entities du domaine
3. Use cases implementation
4. Interface adapters
5. Framework implementations
6. Dependency injection
7. Tests d'isolation

QUALITÉ : Architecture Clean testable, framework-agnostic
```

## 🎯 Points clés à retenir

1. **MVC** : Simple, framework-native, rapide à implémenter
2. **DDD** : Métier-centric, ubiquitous language, business value
3. **Clean Architecture** : Framework-independent, highly testable
4. **CQRS** : Optimized reads/writes, eventual consistency
5. **Event-Driven** : Loose coupling, scalability, async processing
6. **Testing** : Architecture impacte directement la testabilité

## 🚀 Prochain chapitre

Maintenant que vous maîtrisez les patterns d'architecture, découvrons les APIs REST et GraphQL avec leur documentation et tests automatisés.

---

## 📋 Checklist qualité

- ✅ Architecture MVC moderne
- ✅ Domain Driven Design implémenté
- ✅ Clean Architecture documentée
- ✅ CQRS et Event-Driven patterns
- ✅ Testing strategies par architecture
- ✅ Performance optimizations

## 🎯 Test de compréhension

**Question :** Quelle est la principale différence entre DDD et Clean Architecture ?

**Réponse attendue :** DDD se focus sur le domaine métier et l'ubiquitous language, créant un modèle qui reflète fidèlement le business. Clean Architecture est plus technique et structurelle, visant l'indépendance des frameworks et la testabilité. DDD peut être implémenté dans Clean Architecture.

---

**Temps de lecture estimé : 70 minutes**
**Niveau : Expert**
**Prérequis : Partie I + II + Chapitre 1**
