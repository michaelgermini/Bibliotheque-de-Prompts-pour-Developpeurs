# 📖 Partie IX - Chapitre 2 : Fiches Pratiques par Usage

## 🎯 Objectif du chapitre

Fournir des fiches pratiques organisées par cas d'usage, avec des workflows étape par étape, des commandes utiles, et des références rapides pour maximiser l'efficacité quotidienne.

## 🚀 Fiches Pratiques Organisées par Usage

### 📚 1. Développement Frontend

#### 🎨 **React Component Creation**
```
# Commandes et Workflow

1. Générer un composant React
2. Ajouter TypeScript types
3. Implémenter les tests
4. Ajouter documentation
5. Optimiser les performances

# Template de Composant
export interface ComponentProps {
  title: string;
  onClick?: () => void;
  variant?: 'primary' | 'secondary';
}

export const Component: React.FC<ComponentProps> = ({
  title,
  onClick,
  variant = 'primary'
}) => {
  return (
    <button
      className={styles[variant]}
      onClick={onClick}
      type="button"
    >
      {title}
    </button>
  );
};
```

#### 🎯 **CSS Responsive Design**
```
# Breakpoints Standards
Mobile: 320px - 767px
Tablet: 768px - 1023px
Desktop: 1024px - 1439px
Large: 1440px+

# Media Queries
@media (max-width: 767px) { /* Mobile */ }
@media (min-width: 768px) and (max-width: 1023px) { /* Tablet */ }
@media (min-width: 1024px) { /* Desktop */ }

# Container Queries (Modern)
@container (min-width: 400px) { /* Container-based responsive */ }
```

#### ♿ **Accessibility Checklist**
```
✅ Semantic HTML (header, nav, main, section, article, aside, footer)
✅ ARIA labels et descriptions appropriées
✅ Keyboard navigation (Tab, Enter, Escape, Arrow keys)
✅ Focus management (visible focus indicators)
✅ Color contrast ratio ≥ 4.5:1 (AA) ou ≥ 3:1 (large text)
✅ Alternative text for images
✅ Form labels et error messages
✅ Screen reader testing (NVDA, JAWS, VoiceOver)
✅ WCAG 2.1 compliance validation
✅ Mobile accessibility (touch targets ≥ 44px)
```

### 🔧 2. Backend Development

#### 🐘 **Laravel API Development**
```
# Commandes Artisan Utiles
php artisan make:model Product -mcr  # Model + Migration + Controller + Resource
php artisan make:request StoreProductRequest  # Form Request validation
php artisan make:policy ProductPolicy  # Authorization policy
php artisan make:job ProcessOrder  # Queue job
php artisan make:command SyncProducts  # Artisan command

# Structure Recommandée
app/
├── Http/Controllers/     # Request handlers
├── Models/              # Eloquent models
├── Services/            # Business logic
├── Repositories/        # Data access
├── Policies/            # Authorization
├── Jobs/                # Queue jobs
└── Rules/               # Validation rules
```

#### 🟢 **Node.js Express Setup**
```
# Structure Projet TypeScript
src/
├── controllers/         # Route handlers
├── middleware/          # Express middleware
├── models/              # Database models
├── services/            # Business logic
├── routes/              # Route definitions
├── utils/               # Helpers
├── types/               # TypeScript types
└── config/              # Configuration

# Commandes npm Scripts
{
  "scripts": {
    "dev": "nodemon src/server.ts",
    "build": "tsc",
    "start": "node dist/server.js",
    "test": "jest",
    "lint": "eslint src/**/*.ts",
    "format": "prettier --write src/**/*.ts"
  }
}
```

#### 🐍 **Python FastAPI Setup**
```
# Structure Projet
app/
├── main.py              # FastAPI application
├── core/                # Configuration, security
├── models/              # Pydantic models, SQLAlchemy
├── routers/             # API endpoints
├── services/            # Business logic
├── dependencies/        # Dependency injection
└── tests/               # Test files

# Commandes Uvicorn
uvicorn app.main:app --reload --host 0.0.0.0 --port 8000
# Development avec hot reload
uvicorn app.main:app --reload --reload-dir app

# Production
uvicorn app.main:app --workers 4 --host 0.0.0.0 --port 8000
```

### 🗄️ 3. Base de Données

#### 🏗️ **SQL Schema Design**
```
# Index Strategy
CREATE INDEX idx_products_category_status ON products (category_id, status);
CREATE INDEX idx_products_price ON products (price);
CREATE INDEX idx_products_search ON products (name, description);
CREATE FULLTEXT INDEX idx_products_fts ON products (name, description);

# Foreign Key Constraints
ALTER TABLE products ADD CONSTRAINT fk_products_category
FOREIGN KEY (category_id) REFERENCES categories(id) ON DELETE CASCADE;

# Check Constraints
ALTER TABLE products ADD CONSTRAINT chk_price_positive
CHECK (price >= 0);

ALTER TABLE products ADD CONSTRAINT chk_status_valid
CHECK (status IN ('draft', 'active', 'inactive', 'archived'));
```

#### 🍃 **MongoDB Document Design**
```
# Index Strategy
db.products.createIndex({ "category._id": 1, "status": 1 });
db.products.createIndex({ "pricing.basePrice": 1 });
db.products.createIndex({
  "name": "text",
  "description": "text",
  "shortDescription": "text"
}, {
  weights: { name: 10, description: 5, shortDescription: 3 }
});

# Aggregation Pipeline Template
db.products.aggregate([
  { $match: { status: "active" } },
  { $lookup: {
    from: "categories",
    localField: "category._id",
    foreignField: "_id",
    as: "category"
  }},
  { $unwind: "$category" },
  { $group: {
    _id: "$category._id",
    categoryName: { $first: "$category.name" },
    productCount: { $sum: 1 },
    averagePrice: { $avg: "$pricing.basePrice" }
  }}
]);
```

### 🛠️ 4. DevOps et Déploiement

#### 🐳 **Docker Best Practices**
```
# Multi-stage Dockerfile Template
FROM node:18-alpine AS base
WORKDIR /app

FROM base AS deps
COPY package*.json ./
RUN npm ci --only=production

FROM base AS builder
COPY . .
COPY --from=deps /app/node_modules ./node_modules
RUN npm run build

FROM base AS runner
COPY --from=builder /app/dist ./
COPY --from=builder /app/package.json ./
RUN npm ci --only=production

EXPOSE 3000
CMD ["npm", "start"]

# Docker Compose pour Développement
version: '3.8'
services:
  app:
    build: .
    ports: ["3000:3000"]
    volumes: ["./src:/app/src"]
    environment:
      - NODE_ENV=development
    depends_on:
      - db
      - redis
```

#### ☁️ **Kubernetes Deployment**
```
# Deployment Template
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp
spec:
  replicas: 3
  selector:
    matchLabels:
      app: myapp
  template:
    metadata:
      labels:
        app: myapp
    spec:
      containers:
      - name: app
        image: myapp:latest
        ports:
        - containerPort: 3000
        resources:
          requests:
            memory: "128Mi"
            cpu: "100m"
          limits:
            memory: "512Mi"
            cpu: "500m"

# Service Template
apiVersion: v1
kind: Service
metadata:
  name: myapp
spec:
  selector:
    app: myapp
  ports:
  - port: 80
    targetPort: 3000
  type: ClusterIP
```

### 🧪 5. Testing et Quality Assurance

#### 🧪 **Testing Commands**
```
# Frontend Testing
npm run test                    # Run all tests
npm run test:watch             # Watch mode
npm run test:coverage          # Coverage report
npm run test:e2e               # End-to-end tests
npm run test:a11y              # Accessibility tests

# Backend Testing (PHP)
vendor/bin/phpunit             # Run all tests
vendor/bin/phpunit --coverage  # Coverage report
vendor/bin/phpunit tests/Feature  # Feature tests only
vendor/bin/pest                # Pest testing framework

# Backend Testing (Python)
pytest                         # Run all tests
pytest --cov                   # Coverage report
pytest -v tests/               # Verbose output
pytest tests/test_api.py       # Specific test file
```

#### 📊 **Coverage Targets**
```
# Frontend Coverage (Jest)
global:
  branches: 85
  functions: 85
  lines: 85
  statements: 85

src/components/:           # Higher standards for UI
  branches: 90
  functions: 90
  lines: 90

src/utils/:               # Critical utilities
  branches: 95
  functions: 95
  lines: 95

# Backend Coverage (PHPUnit)
<directory name="src">
  <total>
    <lines>85</lines>
    <functions>85</functions>
  </total>
</directory>

<directory name="tests">
  <exclude>
    <lines>90</lines>
  </exclude>
</directory>
```

### 🔒 6. Sécurité

#### 🛡️ **Security Headers**
```
# HTTP Security Headers
Strict-Transport-Security: max-age=31536000; includeSubDomains
X-Content-Type-Options: nosniff
X-Frame-Options: DENY
X-XSS-Protection: 1; mode=block
Content-Security-Policy: default-src 'self'; script-src 'self' 'unsafe-inline'
Referrer-Policy: strict-origin-when-cross-origin
Permissions-Policy: camera=(), microphone=(), geolocation=()
```

#### 🔐 **Authentication Flow**
```
# JWT Token Implementation
1. Client sends credentials
2. Server validates credentials
3. Server generates JWT token
4. Token sent to client
5. Client stores token (httpOnly cookie or localStorage)
6. Client sends token with each request
7. Server validates token on each request
8. Refresh token for long sessions

# OAuth2 Flow
1. Client redirects to authorization server
2. User authenticates and authorizes
3. Authorization server redirects with code
4. Client exchanges code for access token
5. Client uses access token for API calls
6. Token refresh when expired
```

### 📋 7. Gestion de Projet

#### 📊 **Agile Metrics**
```
# Sprint Metrics
Velocity: 32 story points per sprint
Burndown: 80% completion by sprint end
Quality: <5% defect rate
Predictability: 85% stories completed as planned

# Release Metrics
Lead Time: 2 weeks from commit to production
Deployment Frequency: Daily for features, weekly for releases
Mean Time to Recovery: <1 hour for critical issues
Change Failure Rate: <5% failed deployments

# Team Metrics
Code Review Turnaround: <4 hours
PR Size: <300 lines of code
Test Coverage: ≥90% for new code
Technical Debt Ratio: <10% of total effort
```

#### 💬 **Communication Templates**
```
# Daily Standup Format
What did you do yesterday?
What will you do today?
Any blockers or help needed?

# Progress Update Template
✅ Completed: [Feature/Milestone]
🔄 In Progress: [Current Work]
⚠️ Blockers: [Issues/Dependencies]
📊 Progress: [X]% complete
🎯 Next: [Immediate Next Steps]

# Issue Report Template
🐛 Description: [Clear problem description]
📋 Steps: [Reproduction steps]
🎯 Expected: [Expected behavior]
❌ Actual: [Actual behavior]
🔍 Impact: [Business/User impact]
🛠️ Priority: [Critical/High/Medium/Low]
```

### 🎯 8. Performance Optimization

#### ⚡ **Frontend Performance**
```
# Core Web Vitals Targets
LCP (Largest Contentful Paint): <2.5s
FID (First Input Delay): <100ms
CLS (Cumulative Layout Shift): <0.1
INP (Interaction to Next Paint): <200ms
TTFB (Time to First Byte): <800ms

# Bundle Optimization
npm run build              # Production build
npm run analyze            # Bundle analyzer
npm run lighthouse         # Performance audit

# Image Optimization
WebP format for photos
SVG for icons and graphics
Lazy loading for below-fold images
Responsive images with srcset
```

#### 🖥️ **Backend Performance**
```
# Database Optimization
EXPLAIN ANALYZE SELECT * FROM products WHERE category_id = 1;
CREATE INDEX idx_products_category_price ON products (category_id, price);
ANALYZE TABLE products;    # Update statistics

# Caching Strategy
Redis for session data
Application cache for computed results
CDN for static assets
Database query cache for frequent queries

# API Optimization
Response compression (gzip)
Pagination for large datasets
Field selection (GraphQL)
Rate limiting per user
```

### 🔄 9. Workflow Automation

#### 🤖 **Git Hooks Setup**
```
# Pre-commit Hook (Python)
#!/usr/bin/env python3
import subprocess
import sys

def run_command(command):
    result = subprocess.run(command, shell=True, capture_output=True, text=True)
    return result.returncode == 0

# Check code formatting
if not run_command("black --check ."):
    print("Code formatting issues. Run 'black .' to fix.")
    sys.exit(1)

# Run linting
if not run_command("flake8 ."):
    print("Linting issues found. Please fix before committing.")
    sys.exit(1)

# Run tests
if not run_command("pytest --tb=short"):
    print("Tests failed. Please fix before committing.")
    sys.exit(1)

print("✅ All pre-commit checks passed!")
```

#### 🚀 **CI/CD Pipeline**
```
# GitHub Actions Workflow
name: CI/CD Pipeline
on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main ]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v3
    - uses: actions/setup-node@v3
      with:
        node-version: 18
        cache: 'npm'
    - run: npm ci
    - run: npm run lint
    - run: npm run type-check
    - run: npm run test:ci
    - run: npm run build

  security:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v3
    - uses: github/super-linter@v4
      env:
        GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
    - uses: github/codeql-action/init@v2
    - uses: github/codeql-action/analyze@v2
```

### 📚 10. Références et Ressources

#### 📖 **Documentation Links**
```
# Frontend
React Docs: https://react.dev
TypeScript: https://typescriptlang.org/docs
CSS Modules: https://github.com/css-modules/css-modules
Accessibility: https://webaim.org

# Backend
Laravel Docs: https://laravel.com/docs
Express.js: https://expressjs.com
FastAPI: https://fastapi.tiangolo.com
PostgreSQL: https://postgresql.org/docs

# DevOps
Docker Docs: https://docs.docker.com
Kubernetes: https://kubernetes.io/docs
Terraform: https://developer.hashicorp.com/terraform/docs
GitHub Actions: https://docs.github.com/en/actions

# Testing
Jest: https://jestjs.io/docs/getting-started
Cypress: https://docs.cypress.io
PHPUnit: https://phpunit.readthedocs.io
Postman: https://learning.postman.com/docs
```

#### 🛠️ **Commandes Utiles**
```
# Git
git checkout -b feature/new-feature    # New branch
git rebase -i HEAD~5                   # Interactive rebase
git stash                              # Stash changes
git cherry-pick <commit>               # Apply specific commit

# Docker
docker build -t myapp .                # Build image
docker-compose up -d                   # Start services
docker system prune                    # Clean up

# Database
psql -h localhost -d mydb -U user     # Connect to PostgreSQL
mysql -h localhost -u user -p mydb    # Connect to MySQL
mongosh                                # Connect to MongoDB

# Testing
npm test                               # Run tests
npm run test:watch                     # Watch mode
npm run coverage                       # Coverage report
```

## 📋 Workflow Rapides

### 🚀 **Setup Nouveau Projet**
```bash
# Frontend React
npx create-react-app my-app --template typescript
cd my-app
npm install @types/node @types/react @types/react-dom
npm install eslint @typescript-eslint/parser @typescript-eslint/eslint-plugin
npm install prettier eslint-config-prettier eslint-plugin-prettier

# Backend Laravel
composer create-project laravel/laravel my-app
cd my-app
composer require predis/predis       # Redis
composer require spatie/laravel-permission  # Roles
php artisan make:install

# Database PostgreSQL
createdb myapp
psql -d myapp -c "CREATE EXTENSION IF NOT EXISTS \"uuid-ossp\";"
```

### 🔧 **Development Setup**
```bash
# Frontend
npm install                           # Dependencies
npm run dev                           # Development server
npm run lint                          # Code linting
npm run test                          # Run tests

# Backend
composer install                      # Dependencies
php artisan migrate                   # Database setup
php artisan db:seed                   # Seed data
php artisan serve                     # Development server

# Desktop (PyQt5)
pip install -r requirements.txt       # Dependencies
python -m py_compile src/             # Syntax check
python src/main.py                    # Run application
```

### 🚀 **Deployment**
```bash
# Build for production
npm run build                         # Frontend build
composer install --optimize-autoloader --no-dev  # Backend optimize
docker build -t myapp .              # Docker build

# Deploy to production
docker-compose -f docker-compose.prod.yml up -d
kubectl apply -f k8s/production/     # Kubernetes deploy
terraform apply                       # Infrastructure deploy

# Verify deployment
curl https://myapp.com/health         # Health check
kubectl logs -f deployment/myapp     # Application logs
```

## 📊 Checklists de Qualité

### ✅ **Code Review Checklist**
```
🔍 **Functionality**
- [ ] Code does what it's supposed to do
- [ ] Edge cases handled properly
- [ ] Error handling implemented
- [ ] Input validation complete

🎨 **Code Quality**
- [ ] Follows coding standards (PSR-12, ESLint)
- [ ] Consistent naming conventions
- [ ] No magic numbers or strings
- [ ] Proper comments and documentation

🏗️ **Architecture**
- [ ] SOLID principles followed
- [ ] Design patterns used appropriately
- [ ] Dependencies properly injected
- [ ] Separation of concerns maintained

🧪 **Testing**
- [ ] Unit tests written (≥90% coverage)
- [ ] Integration tests included
- [ ] Edge cases tested
- [ ] Mock data appropriate

🔒 **Security**
- [ ] Input sanitized and validated
- [ ] SQL injection prevention
- [ ] XSS protection implemented
- [ ] Authentication/authorization correct

⚡ **Performance**
- [ ] No unnecessary database queries
- [ ] Caching implemented where appropriate
- [ ] Code optimized for performance
- [ ] Bundle size minimized

♿ **Accessibility**
- [ ] WCAG 2.1 AA compliance
- [ ] Keyboard navigation works
- [ ] Screen reader compatible
- [ ] Color contrast adequate
```

### ✅ **Deployment Checklist**
```
🐳 **Docker**
- [ ] Dockerfile optimized (multi-stage)
- [ ] .dockerignore configured
- [ ] Security scanning completed
- [ ] Image size minimized

☁️ **Infrastructure**
- [ ] Terraform code reviewed
- [ ] Security groups configured
- [ ] Monitoring and logging setup
- [ ] Backup strategy implemented

🚀 **Application**
- [ ] Environment variables configured
- [ ] Database migrations run
- [ ] Cache warmed up
- [ ] Health checks implemented

🧪 **Testing**
- [ ] All tests passing
- [ ] Load testing completed
- [ ] Security testing done
- [ ] User acceptance testing

📊 **Monitoring**
- [ ] Application metrics configured
- [ ] Error tracking setup
- [ ] Performance monitoring active
- [ ] Alerting rules defined

📚 **Documentation**
- [ ] API documentation updated
- [ ] Deployment guide updated
- [ ] Runbooks available
- [ ] Rollback procedure documented
```

## 🎯 Références Rapides

### 📋 **Commandes Git**
```bash
git status                 # Show changes
git add .                  # Stage all changes
git commit -m "Message"    # Commit changes
git push origin main       # Push to remote
git pull origin main       # Pull changes
git merge develop          # Merge branch
git rebase -i HEAD~5       # Interactive rebase
git stash                  # Stash changes
```

### 🐳 **Commandes Docker**
```bash
docker build -t myapp .    # Build image
docker run -p 3000:3000 myapp  # Run container
docker-compose up -d       # Start services
docker-compose down        # Stop services
docker system prune        # Clean up
docker logs myapp          # View logs
docker exec -it myapp bash # Access container
```

### 🧪 **Commandes Testing**
```bash
npm test                   # Run all tests
npm run test:coverage      # Coverage report
npm run test:watch         # Watch mode
composer test              # PHP tests
pytest                     # Python tests
docker-compose test        # Containerized tests
```

### 📊 **Performance Commands**
```bash
# Frontend
npm run lighthouse         # Performance audit
npm run build              # Production build
npm run analyze            # Bundle analysis

# Backend
php artisan optimize       # Laravel optimization
composer dump-autoload     # Autoloader optimization
ab -n 1000 -c 10 http://localhost/api/test  # Load testing

# Database
EXPLAIN ANALYZE SELECT ... # Query analysis
pg_stat_statements         # PostgreSQL query stats
SHOW PROCESSLIST           # MySQL connections
```

## 📚 Ressources Supplémentaires

### 📖 **Livres Recommandés**
- "Clean Code" - Robert C. Martin
- "The Pragmatic Programmer" - Hunt & Thomas
- "Designing Data-Intensive Applications" - Martin Kleppmann
- "Web Application Security" - Andrew Hoffman
- "Continuous Delivery" - Jez Humble & David Farley

### 🎥 **Chaînes YouTube**
- Traversy Media (Web Development)
- freeCodeCamp (Full Tutorials)
- Fireship (Quick Tips)
- Dev Ed (Modern Development)
- Academind (In-depth Tutorials)

### 📱 **Communautés**
- Stack Overflow (Q&A)
- Reddit r/webdev, r/reactjs, r/laravel
- Discord Developer Communities
- GitHub Discussions
- Dev.to (Articles and Tutorials)

## 🎯 Conclusion

Ces fiches pratiques vous donnent tous les outils nécessaires pour développer efficacement avec votre stack. Utilisez-les comme référence quotidienne et adaptez-les selon vos besoins spécifiques.

---

**Temps de lecture estimé : 170 minutes**
**Niveau : Expert**
**Prérequis : Livre complet**

---

🎉 **Fiches Pratiques Terminées !**
