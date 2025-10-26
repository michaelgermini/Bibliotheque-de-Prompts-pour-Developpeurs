# 📖 Partie V - Chapitre 1 : Refactoring, Tech Debt, Patterns, SOLID

## 🎯 Objectif du chapitre

Maîtriser les techniques de refactoring de code, la gestion de la dette technique, l'application des design patterns, et les principes SOLID pour du code maintenable et scalable.

## 🔄 Refactoring : Amélioration continue du code

### 1. 🏗️ Refactoring systématique

```
Tu es un expert refactoring et clean code.

TA TÂCHE : Refactorer du code legacy vers les standards modernes pour [APPLICATION]

TECHNIQUES DE REFACTORING :
1. **Extract Method** : Décomposer les fonctions longues
2. **Rename Variables** : Noms explicites et cohérents
3. **Move Method** : Regrouper les méthodes liées
4. **Replace Magic Numbers** : Constantes nommées
5. **Extract Class** : Séparer les responsabilités
6. **Remove Duplication** : DRY principle
7. **Simplify Conditionals** : Logique claire

CONTRAINTES :
- Backward compatibility
- Testing de non-régression
- Performance preservation
- Team collaboration
- Documentation des changements

FORMAT DE SORTIE :
1. Code analysis et issues identifiés
2. Refactoring plan step-by-step
3. Code refactoré avec explications
4. Tests de validation
5. Performance benchmarks
6. Migration guide
7. Best practices documentation

QUALITÉ : Code refactoré maintenable, performant, testé
```

**Exemple de refactoring Laravel :**
```php
<?php

// ❌ BEFORE: Code legacy avec problèmes
class OrderController extends Controller
{
    public function store(Request $request)
    {
        // Validation basique
        if (!$request->has('items') || empty($request->items)) {
            return response()->json(['error' => 'Items required'], 400);
        }

        // Logique métier mélangée avec controller
        $total = 0;
        $user = auth()->user();

        foreach ($request->items as $item) {
            $product = Product::find($item['product_id']);
            if (!$product) {
                return response()->json(['error' => 'Product not found'], 404);
            }

            // Calcul de prix hardcoded
            $price = $product->price;
            if ($product->discount) {
                $price = $price * (1 - $product->discount / 100);
            }

            $total += $price * $item['quantity'];
        }

        // Création order avec logique inline
        $order = new Order();
        $order->user_id = $user->id;
        $order->total = $total;
        $order->status = 'pending';
        $order->save();

        // Items avec logique répétée
        foreach ($request->items as $item) {
            OrderItem::create([
                'order_id' => $order->id,
                'product_id' => $item['product_id'],
                'quantity' => $item['quantity'],
                'price' => $product->price // Bug: $product n'est pas défini ici
            ]);
        }

        return response()->json($order, 201);
    }
}

// ✅ AFTER: Code refactoré avec clean architecture
class OrderController extends Controller
{
    public function __construct(
        private OrderService $orderService,
        private ProductRepository $productRepository
    ) {}

    public function store(StoreOrderRequest $request): JsonResponse
    {
        // Validation via Form Request
        $validated = $request->validated();

        // Delegation to service layer
        $order = $this->orderService->createOrder(
            $validated['items'],
            auth()->id()
        );

        return response()->json([
            'message' => 'Order created successfully',
            'data' => new OrderResource($order)
        ], 201);
    }
}

class OrderService
{
    public function __construct(
        private OrderRepository $orderRepository,
        private ProductRepository $productRepository,
        private PriceCalculator $priceCalculator
    ) {}

    public function createOrder(array $items, int $userId): Order
    {
        // Validation métier
        $this->validateOrderItems($items);

        // Calcul du total avec service dédié
        $totalAmount = $this->calculateOrderTotal($items);

        // Création de l'order
        $order = new Order(
            orderNumber: $this->generateOrderNumber(),
            customerId: $userId,
            totalAmount: $totalAmount,
            status: OrderStatus::PENDING
        );

        // Sauvegarde
        $this->orderRepository->save($order);

        // Création des items
        $this->createOrderItems($order, $items);

        // Domain event
        event(new OrderCreated($order));

        return $order;
    }

    private function validateOrderItems(array $items): void
    {
        if (empty($items)) {
            throw new ValidationException('Order must have at least one item');
        }

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
        }
    }

    private function calculateOrderTotal(array $items): Money
    {
        $total = Money::zero();

        foreach ($items as $item) {
            $product = $this->productRepository->findById($item['product_id']);
            $unitPrice = $this->priceCalculator->calculateProductPrice($product, $item['options'] ?? []);
            $lineTotal = $unitPrice->multiply($item['quantity']);
            $total = $total->add($lineTotal);
        }

        return $total;
    }

    private function createOrderItems(Order $order, array $items): void
    {
        foreach ($items as $item) {
            $product = $this->productRepository->findById($item['product_id']);
            $unitPrice = $this->priceCalculator->calculateProductPrice($product, $item['options'] ?? []);

            OrderItem::create([
                'order_id' => $order->getId(),
                'product_id' => $product->getId(),
                'product_name' => $product->getName(), // Snapshot for historical records
                'product_sku' => $product->getSku(),
                'quantity' => $item['quantity'],
                'unit_price' => $unitPrice->getAmount(),
                'total_price' => $unitPrice->multiply($item['quantity'])->getAmount(),
                'options' => $item['options'] ?? null
            ]);
        }
    }
}
```

### 2. 📊 Tech Debt Management

```
Tu es un expert technical debt management et code quality.

TA TÂCHE : Identifier et gérer la dette technique dans [CODEBASE]

ANALYSE DE LA DETTE TECHNIQUE :
1. **Code Smells** : Detection automatique
2. **Complexity Metrics** : Cyclomatic complexity, nesting
3. **Duplication** : Copy-paste detection
4. **Test Coverage** : Gaps identification
5. **Security Issues** : Vulnerabilities assessment
6. **Performance Issues** : Bottlenecks detection

CONTRAINTES :
- Business impact assessment
- Prioritization matrix
- Team capacity
- Risk management
- Continuous improvement

FORMAT DE SORTIE :
1. Tech debt assessment report
2. Prioritization matrix
3. Refactoring roadmap
4. Code quality metrics
5. Risk analysis
6. Improvement tracking
7. Team guidelines

QUALITÉ : Tech debt gérée, améliorations mesurables
```

## 🎯 Design Patterns : Solutions éprouvées

### 1. 🏗️ Creational Patterns

```
Tu es un expert design patterns et software architecture.

TA TÂCHE : Implémenter des design patterns pour [PROBLÈME]

CREATIONAL PATTERNS :
1. **Singleton** : Single instance guarantee
2. **Factory** : Object creation abstraction
3. **Builder** : Complex object construction
4. **Dependency Injection** : Loose coupling
5. **Service Locator** : Service discovery

CONTRAINTES :
- SOLID principles respectés
- Testability maximale
- Performance optimisée
- Framework integration
- Team patterns

FORMAT DE SORTIE :
1. Pattern selection rationale
2. Implementation examples
3. Testing strategies
4. Performance analysis
5. Best practices
6. Anti-patterns avoidance
7. Migration guide

QUALITÉ : Patterns implémentés correctement, testés, documentés
```

**Exemple de Factory Pattern :**
```typescript
// Factory Pattern for Payment Processing
interface PaymentProcessor {
  process(amount: Money, details: PaymentDetails): Promise<PaymentResult>;
  refund(paymentId: string, amount: Money): Promise<RefundResult>;
}

class PaymentFactory {
  static createProcessor(type: PaymentType): PaymentProcessor {
    switch (type) {
      case PaymentType.STRIPE:
        return new StripePaymentProcessor();
      case PaymentType.PAYPAL:
        return new PayPalPaymentProcessor();
      case PaymentType.BANK_TRANSFER:
        return new BankTransferProcessor();
      default:
        throw new UnsupportedPaymentTypeError(`Payment type ${type} not supported`);
    }
  }

  static createFromConfig(config: PaymentConfig): PaymentProcessor {
    const processor = this.createProcessor(config.type);

    // Configure processor with specific settings
    if (config.testMode) {
      processor.setTestMode(true);
    }

    if (config.webhookUrl) {
      processor.setWebhookUrl(config.webhookUrl);
    }

    return processor;
  }
}

// Usage
const config = {
  type: PaymentType.STRIPE,
  testMode: process.env.NODE_ENV === 'development',
  webhookUrl: process.env.STRIPE_WEBHOOK_URL
};

const paymentProcessor = PaymentFactory.createFromConfig(config);

// Process payment
const result = await paymentProcessor.process(
  new Money(99.99, 'USD'),
  {
    customerId: 'customer_123',
    description: 'Product purchase',
    metadata: { orderId: 'order_456' }
  }
);
```

### 2. 🏛️ Structural Patterns

```
Tu es un expert structural patterns et system design.

TA TÂCHE : Implémenter des structural patterns pour [ARCHITECTURE]

STRUCTURAL PATTERNS :
1. **Adapter** : Interface compatibility
2. **Decorator** : Functionality extension
3. **Facade** : Complex system simplification
4. **Proxy** : Access control et optimization
5. **Composite** : Tree structure management

CONTRAINTES :
- Interface segregation
- Composition over inheritance
- Performance considerations
- Testing isolation
- Documentation clarity

FORMAT DE SORTIE :
1. Pattern architecture design
2. Implementation examples
3. Interface definitions
4. Testing strategies
5. Performance analysis
6. Usage documentation
7. Evolution guidelines

QUALITÉ : Patterns structuraux solides, flexibles, maintenables
```

**Exemple de Decorator Pattern :**
```typescript
// Decorator Pattern for API Response Enhancement
interface ApiResponse {
  getData(): any;
  getStatus(): number;
  getHeaders(): Record<string, string>;
}

class BaseApiResponse implements ApiResponse {
  constructor(
    private data: any,
    private status: number = 200,
    private headers: Record<string, string> = {}
  ) {}

  getData(): any {
    return this.data;
  }

  getStatus(): number {
    return this.status;
  }

  getHeaders(): Record<string, string> {
    return this.headers;
  }
}

// Decorator for caching
class CachedApiResponse implements ApiResponse {
  private cachedResponse?: ApiResponse;

  constructor(private baseResponse: ApiResponse) {}

  getData(): any {
    if (!this.cachedResponse) {
      this.cachedResponse = this.baseResponse;
    }
    return this.cachedResponse.getData();
  }

  getStatus(): number {
    return this.baseResponse.getStatus();
  }

  getHeaders(): Record<string, string> {
    return {
      ...this.baseResponse.getHeaders(),
      'X-Cache': 'HIT'
    };
  }
}

// Decorator for compression
class CompressedApiResponse implements ApiResponse {
  private compressedData?: string;

  constructor(private baseResponse: ApiResponse) {}

  getData(): any {
    if (!this.compressedData) {
      const data = this.baseResponse.getData();
      this.compressedData = JSON.stringify(data);
    }
    return this.compressedData;
  }

  getStatus(): number {
    return this.baseResponse.getStatus();
  }

  getHeaders(): Record<string, string> {
    return {
      ...this.baseResponse.getHeaders(),
      'Content-Encoding': 'gzip',
      'Content-Type': 'application/json'
    };
  }
}

// Decorator for encryption
class EncryptedApiResponse implements ApiResponse {
  private encryptedData?: string;

  constructor(
    private baseResponse: ApiResponse,
    private encryptionKey: string
  ) {}

  getData(): any {
    if (!this.encryptedData) {
      const data = this.baseResponse.getData();
      this.encryptedData = this.encrypt(JSON.stringify(data));
    }
    return this.encryptedData;
  }

  getStatus(): number {
    return this.baseResponse.getStatus();
  }

  getHeaders(): Record<string, string> {
    return {
      ...this.baseResponse.getHeaders(),
      'X-Encrypted': 'true',
      'Content-Type': 'application/encrypted'
    };
  }

  private encrypt(data: string): string {
    // Simple encryption for example (use proper encryption in production)
    return Buffer.from(data).toString('base64');
  }
}

// Usage in API controller
class ProductController {
  async getProduct(id: string): Promise<ApiResponse> {
    // Base response
    const baseResponse = new BaseApiResponse(
      await this.productService.getProduct(id),
      200,
      { 'Content-Type': 'application/json' }
    );

    // Apply decorators based on requirements
    let response: ApiResponse = baseResponse;

    // Add caching for GET requests
    if (this.shouldCache(id)) {
      response = new CachedApiResponse(response);
    }

    // Add compression for large responses
    if (this.shouldCompress(response)) {
      response = new CompressedApiResponse(response);
    }

    // Add encryption for sensitive data
    if (this.shouldEncrypt(id)) {
      response = new EncryptedApiResponse(response, this.encryptionKey);
    }

    return response;
  }
}
```

### 3. 🎭 Behavioral Patterns

```
Tu es un expert behavioral patterns et system behavior.

TA TÂCHE : Implémenter des behavioral patterns pour [COMPORTEMENT]

BEHAVIORAL PATTERNS :
1. **Observer** : Event-driven communication
2. **Strategy** : Algorithm selection
3. **Command** : Action encapsulation
4. **Template Method** : Algorithm skeleton
5. **State** : State-dependent behavior

CONTRAINTES :
- Event-driven architecture
- Algorithm flexibility
- Command pattern for undo
- State management
- Performance optimization

FORMAT DE SORTIE :
1. Behavior design analysis
2. Pattern implementation
3. Event system setup
4. Algorithm strategies
5. State management
6. Testing scenarios
7. Documentation

QUALITÉ : Patterns comportementaux flexibles, testables, performants
```

## 🔒 SOLID Principles : Code robuste

### 1. 📐 SOLID Implementation

```
Tu es un expert SOLID principles et clean architecture.

TA TÂCHE : Appliquer les principes SOLID pour améliorer [CODEBASE]

SOLID PRINCIPLES :
1. **S - Single Responsibility** : One class, one reason to change
2. **O - Open/Closed** : Open for extension, closed for modification
3. **L - Liskov Substitution** : Subtypes must be substitutable
4. **I - Interface Segregation** : Small, specific interfaces
5. **D - Dependency Inversion** : Depend on abstractions

CONTRAINTES :
- High cohesion, low coupling
- Interface design
- Dependency injection
- Testing isolation
- Refactoring safety

FORMAT DE SORTIE :
1. SOLID analysis report
2. Code violations identified
3. Refactoring proposals
4. Interface design
5. Dependency injection
6. Testing improvements
7. Quality metrics

QUALITÉ : Code SOLID compliant, testable, maintenable
```

**Exemple d'application SOLID :**
```php
<?php

// ❌ BEFORE: Violation des principes SOLID
class UserController extends Controller
{
    public function store(Request $request)
    {
        // SRP violation: Controller fait trop de choses
        $user = new User();
        $user->name = $request->name;
        $user->email = $request->email;
        $user->password = bcrypt($request->password); // Cryptography in controller
        $user->save();

        // OCP violation: Hard to extend for different user types
        if ($request->type === 'admin') {
            $user->role = 'admin';
            $user->permissions = ['all'];
            $user->save();
        }

        // ISP violation: Single interface for different operations
        Mail::to($user->email)->send(new WelcomeEmail($user));
        $this->logUserCreation($user);
        $this->sendSlackNotification($user);

        return response()->json($user, 201);
    }

    private function logUserCreation($user) { /* ... */ }
    private function sendSlackNotification($user) { /* ... */ }
}

// ✅ AFTER: SOLID compliant code
interface UserRepositoryInterface
{
    public function save(User $user): User;
    public function findByEmail(string $email): ?User;
    public function findById(int $id): ?User;
}

interface PasswordHasherInterface
{
    public function hash(string $password): string;
    public function verify(string $password, string $hash): bool;
}

interface EmailServiceInterface
{
    public function sendWelcomeEmail(User $user): void;
}

interface NotificationServiceInterface
{
    public function sendUserCreatedNotification(User $user): void;
}

interface AuditLogInterface
{
    public function logUserCreation(User $user): void;
}

class CreateUserUseCase
{
    public function __construct(
        private UserRepositoryInterface $userRepository,
        private PasswordHasherInterface $passwordHasher,
        private EmailServiceInterface $emailService,
        private NotificationServiceInterface $notificationService,
        private AuditLogInterface $auditLog
    ) {}

    public function execute(CreateUserRequest $request): User
    {
        // SRP: Single responsibility - create user
        $user = new User();
        $user->name = $request->name;
        $user->email = $request->email;
        $user->passwordHash = $this->passwordHasher->hash($request->password);

        // SRP: Repository handles persistence
        $this->userRepository->save($user);

        // SRP: Services handle their specific concerns
        $this->emailService->sendWelcomeEmail($user);
        $this->notificationService->sendUserCreatedNotification($user);
        $this->auditLog->logUserCreation($user);

        return $user;
    }
}

class UserController extends Controller
{
    public function __construct(
        private CreateUserUseCase $createUserUseCase
    ) {}

    public function store(CreateUserRequest $request): JsonResponse
    {
        // SRP: Controller only handles HTTP concerns
        $user = $this->createUserUseCase->execute($request->validated());

        // OCP: Easy to extend with different response formats
        return $this->respondWithSuccess(
            new UserResource($user),
            'User created successfully',
            201
        );
    }
}

// ISP: Specific interfaces for different concerns
interface AdminUserCreatorInterface
{
    public function createAdminUser(AdminUserData $data): User;
}

interface RegularUserCreatorInterface
{
    public function createRegularUser(RegularUserData $data): User;
}

class UserFactory
{
    public function __construct(
        private AdminUserCreatorInterface $adminCreator,
        private RegularUserCreatorInterface $regularCreator
    ) {}

    // OCP: Open for extension (new user types)
    public function createUser(UserCreationData $data): User
    {
        return match ($data->type) {
            UserType::ADMIN => $this->adminCreator->createAdminUser($data),
            UserType::REGULAR => $this->regularCreator->createRegularUser($data),
            default => throw new UnsupportedUserTypeException()
        };
    }
}
```

## 📊 Code Quality Metrics

### 1. 🔍 Code Quality Assessment

```
Tu es un expert code quality et metrics analysis.

TA TÂCHE : Évaluer et améliorer la qualité du code pour [CODEBASE]

MÉTRIQUES DE QUALITÉ :
1. **Complexity** : Cyclomatic complexity, nesting depth
2. **Coverage** : Test coverage percentage
3. **Duplication** : Code duplication percentage
4. **Maintainability** : Code maintainability index
5. **Security** : Security vulnerabilities count
6. **Performance** : Performance bottlenecks
7. **Standards** : Coding standards compliance

CONTRAINTES :
- Automated analysis
- Continuous monitoring
- Team standards
- Performance impact
- Business requirements

FORMAT DE SORTIE :
1. Code quality audit
2. Metrics dashboard
3. Improvement recommendations
4. Automated tooling setup
5. Team guidelines
6. Progress tracking
7. Quality gates

QUALITÉ : Code quality mesurable, améliorations continues
```

## 📝 Templates de refactoring

### 🔄 Template Code Refactoring
```
Tu es un expert refactoring et clean code.

TA TÂCHE : Refactorer [CODE] vers [STANDARDS]

ISSUES IDENTIFIÉS :
- [PROBLEM 1] : [DESCRIPTION]
- [PROBLEM 2] : [DESCRIPTION]
- [PROBLEM 3] : [DESCRIPTION]

CONTRAINTES :
- Backward compatibility
- Testing preservation
- Performance maintenance
- Team collaboration

FORMAT :
1. Code analysis
2. Refactoring plan
3. Code refactoré
4. Tests validation
5. Performance benchmarks
6. Migration guide
7. Best practices

QUALITÉ : Code refactoré clean, maintenable, testé
```

### 🎯 Template Design Patterns
```
Tu es un expert design patterns et architecture.

TA TÂCHE : Implémenter patterns pour [PROBLEM]

PATTERNS :
- [PATTERN 1] : [USE CASE]
- [PATTERN 2] : [USE CASE]
- [PATTERN 3] : [USE CASE]

CONTRAINTES :
- SOLID compliance
- Testability maximal
- Performance optimized
- Framework integration

FORMAT :
1. Pattern selection
2. Implementation examples
3. Testing strategies
4. Performance analysis
5. Usage documentation
6. Evolution guidelines
7. Anti-patterns

QUALITÉ : Patterns implémentés correctement, documentés
```

### 📐 Template SOLID Principles
```
Tu es un expert SOLID principles et clean architecture.

TA TÂCHE : Appliquer SOLID pour [CODEBASE]

PRINCIPLES :
- Single Responsibility
- Open/Closed
- Liskov Substitution
- Interface Segregation
- Dependency Inversion

CONTRAINTES :
- High cohesion
- Low coupling
- Interface design
- Testing isolation

FORMAT :
1. SOLID analysis
2. Code violations
3. Refactoring proposals
4. Interface design
5. Dependency injection
6. Testing improvements
7. Quality metrics

QUALITÉ : Code SOLID compliant, testable, maintenable
```

## 🎯 Points clés à retenir

1. **Refactoring** : Amélioration continue, tests de sécurité
2. **Design Patterns** : Solutions éprouvées, réutilisables
3. **SOLID** : Principes fondamentaux de clean code
4. **Tech Debt** : Gestion proactive, impact business
5. **Code Quality** : Métriques mesurables, amélioration continue
6. **Team Practices** : Collaboration, standards, reviews

## 🚀 Prochain chapitre

Maintenant que vous maîtrisez le refactoring et les patterns, découvrons les reviews assistées et les style guides automatisés.

---

## 📋 Checklist qualité

- ✅ Refactoring methodology
- ✅ Design patterns implementation
- ✅ SOLID principles application
- ✅ Tech debt management
- ✅ Code quality metrics
- ✅ Testing strategies

## 🎯 Test de compréhension

**Question :** Pourquoi le Liskov Substitution Principle (LSP) est-il important ?

**Réponse attendue :** Le LSP garantit que les sous-types sont substituables à leurs types de base sans altérer la correction du programme. Si S est un sous-type de T, alors les objets de type T peuvent être remplacés par des objets de type S sans que le programme ne se comporte différemment.

---

**Temps de lecture estimé : 100 minutes**
**Niveau : Expert**
**Prérequis : Partie I + II + III + IV**
