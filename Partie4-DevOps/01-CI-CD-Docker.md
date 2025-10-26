# 📖 Partie IV - Chapitre 1 : CI/CD + Docker + Automatisation

## 🎯 Objectif du chapitre

Maîtriser les outils et pratiques DevOps modernes : CI/CD pipelines, containerisation avec Docker, et automatisation complète du développement au déploiement.

## 🔄 CI/CD : Pipelines d'intégration continue

### 1. 🏗️ GitHub Actions : CI/CD moderne

```
Tu es un expert DevOps et GitHub Actions.

TA TÂCHE : Configurer un pipeline CI/CD complet avec GitHub Actions pour [APPLICATION]

PIPELINE À IMPLÉMENTER :
1. **Build** : Compilation, tests, linting
2. **Test** : Unit, integration, e2e tests
3. **Security** : SAST, DAST, dependency scanning
4. **Deploy** : Staging et production deployments
5. **Monitoring** : Performance, errors, analytics

CONTRAINTES :
- Framework : [REACT/LARAVEL/NODE.JS]
- Container : Docker
- Cloud : [AWS/AZURE/GCP]
- Database : [SQL/NoSQL]
- Security : OWASP compliance

FORMAT DE SORTIE :
1. GitHub Actions workflows
2. Docker configuration
3. Environment setup
4. Deployment scripts
5. Testing pipeline
6. Security scanning
7. Monitoring integration

QUALITÉ : Pipeline CI/CD robuste, sécurisé, automatisé
```

**Exemple de workflow GitHub Actions :**
```yaml
# .github/workflows/ci.yml
name: CI Pipeline

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main, develop ]

env:
  NODE_VERSION: 18.x
  PHP_VERSION: 8.1
  COMPOSER_NO_INTERACTION: 1

jobs:
  # Frontend CI
  frontend:
    runs-on: ubuntu-latest
    defaults:
      run:
        working-directory: frontend

    steps:
    - uses: actions/checkout@v3

    - name: Setup Node.js
      uses: actions/setup-node@v3
      with:
        node-version: ${{ env.NODE_VERSION }}
        cache: 'npm'
        cache-dependency-path: frontend/package-lock.json

    - name: Install dependencies
      run: npm ci

    - name: Lint code
      run: npm run lint

    - name: Type checking
      run: npm run type-check

    - name: Run tests
      run: npm run test:ci
      env:
        CI: true

    - name: Build application
      run: npm run build

    - name: Upload build artifacts
      uses: actions/upload-artifact@v3
      with:
        name: frontend-build
        path: frontend/dist/

  # Backend CI
  backend:
    runs-on: ubuntu-latest
    defaults:
      run:
        working-directory: backend

    services:
      mysql:
        image: mysql:8.0
        env:
          MYSQL_ROOT_PASSWORD: root
          MYSQL_DATABASE: testdb
        ports:
          - 3306:3306
        options: --health-cmd="mysqladmin ping" --health-interval=10s --health-timeout=5s --health-retries=3

      redis:
        image: redis:7-alpine
        ports:
          - 6379:6379

    steps:
    - uses: actions/checkout@v3

    - name: Setup PHP
      uses: shivammathur/setup-php@v2
      with:
        php-version: ${{ env.PHP_VERSION }}
        extensions: mbstring, xml, ctype, iconv, intl, pdo, pdo_mysql, redis
        coverage: xdebug

    - name: Cache Composer packages
      uses: actions/cache@v3
      with:
        path: ~/.composer/cache
        key: ${{ runner.os }}-composer-${{ hashFiles('**/composer.lock') }}
        restore-keys: ${{ runner.os }}-composer-

    - name: Install dependencies
      run: composer install --prefer-dist --no-progress

    - name: Run PHP CS Fixer
      run: vendor/bin/php-cs-fixer fix --dry-run --diff

    - name: Run PHPStan
      run: vendor/bin/phpstan analyse --error-format=github

    - name: Run PHPUnit
      run: vendor/bin/phpunit --coverage-clover=coverage.xml
      env:
        DB_HOST: 127.0.0.1
        DB_DATABASE: testdb
        DB_USERNAME: root
        DB_PASSWORD: root

    - name: Upload coverage to Codecov
      uses: codecov/codecov-action@v3
      with:
        file: backend/coverage.xml
        flags: backend
        fail_ci_if_error: true

  # Security scanning
  security:
    runs-on: ubuntu-latest
    permissions:
      actions: read
      contents: read
      security-events: write

    steps:
    - uses: actions/checkout@v3

    - name: Run Trivy vulnerability scanner
      uses: aquasecurity/trivy-action@master
      with:
        scan-type: 'fs'
        scan-ref: '.'
        format: 'sarif'
        output: 'trivy-results.sarif'

    - name: Upload Trivy scan results to GitHub Security tab
      uses: github/codeql-action/upload-sarif@v2
      if: always()
      with:
        sarif_file: 'trivy-results.sarif'

    - name: Run npm audit
      run: |
        cd frontend && npm audit --audit-level high

    - name: Run security scan
      uses: securecodewarrior/github-action-gosec@master
      with:
        args: ./backend

  # Docker build
  docker:
    runs-on: ubuntu-latest
    needs: [frontend, backend, security]

    steps:
    - uses: actions/checkout@v3

    - name: Set up Docker Buildx
      uses: docker/setup-buildx-action@v2

    - name: Build and push Docker image
      uses: docker/build-push-action@v4
      with:
        context: .
        platforms: linux/amd64,linux/arm64
        push: false
        tags: |
          myapp:${{ github.sha }}
          myapp:latest
        cache-from: type=gha
        cache-to: type=gha,mode=max
        build-args: |
          NODE_VERSION=${{ env.NODE_VERSION }}
          PHP_VERSION=${{ env.PHP_VERSION }}
```

### 2. 🐳 Docker : Containerisation moderne

```
Tu es un expert Docker et container orchestration.

TA TÂCHE : Créer une configuration Docker complète pour [APPLICATION]

COMPOSANTS DOCKER :
1. **Dockerfile** : Multi-stage builds
2. **docker-compose.yml** : Development environment
3. **Dockerfile.production** : Optimized production image
4. **Kubernetes manifests** : Production deployment
5. **Docker registry** : Image management

CONTRAINTES :
- Multi-stage builds
- Security best practices
- Performance optimisée
- Development vs production
- Health checks
- Logging configuration

FORMAT DE SORTIE :
1. Dockerfile multi-stage
2. Docker Compose setup
3. Production configuration
4. Kubernetes manifests
5. CI/CD integration
6. Security hardening
7. Performance optimization

QUALITÉ : Configuration Docker production-ready, sécurisée
```

**Dockerfile multi-stage optimisé :**
```dockerfile
# Multi-stage build for Node.js/React application
FROM node:18-alpine AS base

# Install dependencies only when needed
FROM base AS deps
RUN apk add --no-cache libc6-compat
WORKDIR /app

# Copy package files
COPY frontend/package.json frontend/package-lock.json* ./
RUN npm ci --only=production && npm cache clean --force

# Build stage
FROM base AS builder
WORKDIR /app
COPY frontend/ ./
COPY --from=deps /app/node_modules ./node_modules

# Set environment variables
ARG NODE_ENV=production
ARG NEXT_PUBLIC_API_URL
ENV NODE_ENV=${NODE_ENV}
ENV NEXT_PUBLIC_API_URL=${NEXT_PUBLIC_API_URL}

# Build application
RUN npm run build

# Production stage
FROM base AS runner
WORKDIR /app

# Create non-root user
RUN addgroup --system --gid 1001 nodejs
RUN adduser --system --uid 1001 nextjs

# Copy built application
COPY --from=builder /app/public ./public
COPY --from=builder --chown=nextjs:nodejs /app/.next/standalone ./
COPY --from=builder --chown=nextjs:nodejs /app/.next/static ./.next/static

# Switch to non-root user
USER nextjs

# Expose port
EXPOSE 3000

# Health check
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD node healthcheck.js

# Start application
CMD ["node", "server.js"]
```

**Docker Compose pour développement :**
```yaml
version: '3.8'

services:
  # Frontend development
  frontend:
    build:
      context: ./frontend
      dockerfile: Dockerfile.dev
    ports:
      - "3000:3000"
    volumes:
      - ./frontend:/app
      - /app/node_modules
    environment:
      - NODE_ENV=development
      - NEXT_PUBLIC_API_URL=http://localhost:8000/api
    depends_on:
      - backend
    command: npm run dev

  # Backend development
  backend:
    build:
      context: ./backend
      dockerfile: Dockerfile.dev
    ports:
      - "8000:8000"
    volumes:
      - ./backend:/app
      - /app/vendor
    environment:
      - APP_ENV=development
      - DB_HOST=mysql
      - DB_DATABASE=myapp
      - DB_USERNAME=myapp
      - DB_PASSWORD=secret
      - REDIS_HOST=redis
      - REDIS_PORT=6379
    depends_on:
      mysql:
        condition: service_healthy
      redis:
        condition: service_started
    command: php artisan serve --host=0.0.0.0

  # MySQL database
  mysql:
    image: mysql:8.0
    ports:
      - "3306:3306"
    environment:
      - MYSQL_ROOT_PASSWORD=root
      - MYSQL_DATABASE=myapp
      - MYSQL_USER=myapp
      - MYSQL_PASSWORD=secret
    volumes:
      - mysql_data:/var/lib/mysql
    healthcheck:
      test: ["CMD", "mysqladmin", "ping", "-h", "localhost"]
      timeout: 20s
      retries: 10

  # Redis cache
  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"
    volumes:
      - redis_data:/data
    healthcheck:
      test: ["CMD", "redis-cli", "ping"]
      interval: 10s
      timeout: 3s
      retries: 5

  # Elasticsearch for search
  elasticsearch:
    image: elasticsearch:8.5.0
    environment:
      - discovery.type=single-node
      - xpack.security.enabled=false
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
    ports:
      - "9200:9200"
    volumes:
      - elasticsearch_data:/usr/share/elasticsearch/data
    healthcheck:
      test: ["CMD-SHELL", "curl -f http://localhost:9200/_cluster/health || exit 1"]
      interval: 30s
      timeout: 10s
      retries: 5

  # Nginx reverse proxy
  nginx:
    image: nginx:alpine
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./docker/nginx/nginx.conf:/etc/nginx/nginx.conf
      - ./docker/nginx/conf.d:/etc/nginx/conf.d
      - ./docker/ssl:/etc/ssl
    depends_on:
      - frontend
      - backend
    restart: unless-stopped

volumes:
  mysql_data:
  redis_data:
  elasticsearch_data:

networks:
  default:
    name: myapp_network
    driver: bridge
```

## ☁️ Infrastructure as Code : Terraform, Kubernetes

### 1. 🏗️ Infrastructure as Code avec Terraform

```
Tu es un expert Infrastructure as Code et Terraform.

TA TÂCHE : Créer l'infrastructure cloud avec Terraform pour [APPLICATION]

RESSOURCES À PROVISIONNER :
1. **VPC & Networking** : VPC, subnets, security groups
2. **Compute** : EC2, ECS, Lambda
3. **Database** : RDS, ElastiCache, DocumentDB
4. **Storage** : S3, EFS
5. **CDN** : CloudFront
6. **Monitoring** : CloudWatch, X-Ray

CONTRAINTES :
- Provider : AWS/Azure/GCP
- Environment : Dev, staging, production
- Security : IAM, encryption, compliance
- Cost optimization
- High availability

FORMAT DE SORTIE :
1. Terraform modules architecture
2. Main.tf configuration
3. Variables et outputs
4. Security groups et policies
5. Monitoring et alerting
6. CI/CD integration
7. Cost optimization

QUALITÉ : Infrastructure IaC scalable, sécurisée, cost-effective
```

**Exemple de configuration Terraform :**
```hcl
# terraform/main.tf
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }

  backend "s3" {
    bucket = "myapp-terraform-state"
    key    = "terraform.tfstate"
    region = "us-east-1"
    encrypt = true
  }
}

# Provider configuration
provider "aws" {
  region = var.aws_region

  default_tags {
    tags = {
      Environment = var.environment
      Project     = "MyApp"
      ManagedBy   = "Terraform"
    }
  }
}

# VPC and networking
module "vpc" {
  source = "terraform-aws-modules/vpc/aws"

  name = "myapp-vpc"
  cidr = "10.0.0.0/16"

  azs             = ["us-east-1a", "us-east-1b", "us-east-1c"]
  private_subnets = ["10.0.1.0/24", "10.0.2.0/24", "10.0.3.0/24"]
  public_subnets  = ["10.0.101.0/24", "10.0.102.0/24", "10.0.103.0/24"]

  enable_nat_gateway = true
  enable_vpn_gateway = false

  tags = {
    Name = "MyApp VPC"
  }
}

# Security groups
resource "aws_security_group" "app" {
  name_prefix = "myapp-app"
  vpc_id      = module.vpc.vpc_id

  ingress {
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  ingress {
    from_port   = 443
    to_port     = 443
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  ingress {
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = [var.admin_cidr_block]
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }

  tags = {
    Name = "MyApp App Security Group"
  }
}

# RDS PostgreSQL
module "db" {
  source = "terraform-aws-modules/rds/aws"

  identifier = "myapp-db"

  engine            = "postgres"
  engine_version    = "15.3"
  instance_class    = var.db_instance_class
  allocated_storage = 20

  db_name  = var.db_name
  username = var.db_username
  password = random_password.db_password.result
  port     = 5432

  vpc_security_group_ids = [aws_security_group.db.id]
  subnet_ids            = module.vpc.private_subnets

  maintenance_window = "sun:03:00-sun:04:00"
  backup_window      = "02:00-03:00"

  backup_retention_period = var.environment == "production" ? 7 : 1
  deletion_protection    = var.environment == "production"

  enabled_cloudwatch_logs_exports = ["postgresql", "upgrade"]

  tags = {
    Name = "MyApp Database"
  }
}

# ElastiCache Redis
module "redis" {
  source = "terraform-aws-modules/elasticache/aws"

  cluster_name           = "myapp-redis"
  engine                 = "redis"
  engine_version         = "7.0"
  node_type              = var.redis_node_type
  num_cache_nodes        = var.redis_num_cache_nodes
  parameter_group_name   = "default.redis7"
  port                   = 6379

  vpc_id     = module.vpc.vpc_id
  subnet_ids = module.vpc.private_subnets

  security_group_ids = [aws_security_group.redis.id]

  tags = {
    Name = "MyApp Redis"
  }
}

# ECS Cluster for containerized applications
module "ecs" {
  source = "terraform-aws-modules/ecs/aws"

  cluster_name = "myapp-cluster"

  fargate_capacity_providers = {
    FARGATE = {
      default_capacity_provider_strategy = {
        weight = 100
      }
    }
  }

  tags = {
    Name = "MyApp ECS Cluster"
  }
}

# Application Load Balancer
resource "aws_lb" "app" {
  name               = "myapp-alb"
  internal           = false
  load_balancer_type = "application"
  security_groups    = [aws_security_group.app.id]
  subnets            = module.vpc.public_subnets

  enable_deletion_protection = var.environment == "production"

  tags = {
    Name = "MyApp Load Balancer"
  }
}

# ECS Service
resource "aws_ecs_service" "app" {
  name            = "myapp-service"
  cluster         = module.ecs.cluster_id
  task_definition = aws_ecs_task_definition.app.arn
  desired_count   = var.app_count

  load_balancer {
    target_group_arn = aws_lb_target_group.app.arn
    container_name   = "app"
    container_port   = 3000
  }

  network_configuration {
    subnets          = module.vpc.private_subnets
    security_groups  = [aws_security_group.app.id]
    assign_public_ip = false
  }

  depends_on = [aws_lb_listener.app]

  tags = {
    Name = "MyApp Service"
  }
}

# CloudWatch monitoring
resource "aws_cloudwatch_dashboard" "app" {
  dashboard_name = "MyApp-Dashboard"

  dashboard_body = jsonencode({
    widgets = [
      {
        type   = "metric"
        x      = 0
        y      = 0
        width  = 12
        height = 6

        properties = {
          metrics = [
            ["AWS/ECS", "CPUUtilization", "ServiceName", "myapp-service"]
          ]
          view    = "timeSeries"
          stacked = false
          region  = var.aws_region
          title   = "ECS CPU Utilization"
        }
      }
    ]
  })
}
```

### 2. 🚀 Kubernetes : Orchestration de containers

```
Tu es un expert Kubernetes et container orchestration.

TA TÂCHE : Déployer [APPLICATION] sur Kubernetes avec [CLOUD PROVIDER]

RESSOURCES KUBERNETES :
1. **Deployments** : Application stateless
2. **StatefulSets** : Database stateful
3. **Services** : Network connectivity
4. **Ingress** : HTTP routing
5. **ConfigMaps** : Configuration management
6. **Secrets** : Sensitive data
7. **HPA** : Horizontal Pod Autoscaling

CONTRAINTES :
- Kubernetes [VERSION]
- Helm charts
- Security contexts
- Resource limits
- Health checks
- Rolling updates

FORMAT DE SORTIE :
1. Kubernetes manifests
2. Helm charts
3. Kustomization files
4. CI/CD pipeline
5. Monitoring setup
6. Security policies
7. Performance tuning

QUALITÉ : Déploiement Kubernetes production-ready, scalable
```

**Exemple de déploiement Kubernetes :**
```yaml
# k8s/base/deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp-frontend
  labels:
    app: myapp
    component: frontend
spec:
  replicas: 3
  selector:
    matchLabels:
      app: myapp
      component: frontend
  template:
    metadata:
      labels:
        app: myapp
        component: frontend
    spec:
      securityContext:
        runAsNonRoot: true
        runAsUser: 1001
        fsGroup: 1001
      containers:
      - name: frontend
        image: myapp/frontend:latest
        ports:
        - containerPort: 3000
          name: http
        env:
        - name: NODE_ENV
          value: "production"
        - name: API_URL
          valueFrom:
            configMapKeyRef:
              name: myapp-config
              key: api-url
        resources:
          requests:
            memory: "128Mi"
            cpu: "100m"
          limits:
            memory: "512Mi"
            cpu: "500m"
        livenessProbe:
          httpGet:
            path: /health
            port: 3000
          initialDelaySeconds: 30
          periodSeconds: 10
        readinessProbe:
          httpGet:
            path: /ready
            port: 3000
          initialDelaySeconds: 5
          periodSeconds: 5
        securityContext:
          allowPrivilegeEscalation: false
          readOnlyRootFilesystem: true
          capabilities:
            drop:
            - ALL

---
# k8s/base/service.yaml
apiVersion: v1
kind: Service
metadata:
  name: myapp-frontend
  labels:
    app: myapp
    component: frontend
spec:
  type: ClusterIP
  ports:
  - port: 80
    targetPort: 3000
    protocol: TCP
    name: http
  selector:
    app: myapp
    component: frontend

---
# k8s/base/configmap.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: myapp-config
  labels:
    app: myapp
data:
  api-url: "http://myapp-backend"
  environment: "production"
  log-level: "info"

---
# k8s/base/secret.yaml
apiVersion: v1
kind: Secret
metadata:
  name: myapp-secrets
  labels:
    app: myapp
type: Opaque
data:
  database-url: <base64-encoded-database-url>
  jwt-secret: <base64-encoded-jwt-secret>
  api-key: <base64-encoded-api-key>

---
# k8s/base/ingress.yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: myapp-ingress
  labels:
    app: myapp
  annotations:
    kubernetes.io/ingress.class: "nginx"
    cert-manager.io/cluster-issuer: "letsencrypt-prod"
    nginx.ingress.kubernetes.io/ssl-redirect: "true"
    nginx.ingress.kubernetes.io/force-ssl-redirect: "true"
spec:
  tls:
  - hosts:
    - myapp.example.com
    secretName: myapp-tls
  rules:
  - host: myapp.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: myapp-frontend
            port:
              number: 80
      - path: /api
        pathType: Prefix
        backend:
          service:
            name: myapp-backend
            port:
              number: 80

---
# k8s/base/hpa.yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: myapp-frontend-hpa
  labels:
    app: myapp
    component: frontend
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: myapp-frontend
  minReplicas: 3
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 70
  - type: Resource
    resource:
      name: memory
      target:
        type: Utilization
        averageUtilization: 80

---
# k8s/overlays/production/kustomization.yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization

metadata:
  name: myapp-production

namespace: myapp-production

images:
- name: myapp/frontend
  newTag: v1.2.3
- name: myapp/backend
  newTag: v2.1.0

configMapGenerator:
- name: myapp-config
  literals:
  - environment=production
  - log-level=warn
  - api-url=https://api.myapp.example.com

patchesStrategicMerge:
- replica-count.yaml
- resource-limits.yaml

---
# k8s/overlays/production/replica-count.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp-frontend
spec:
  replicas: 5

---
# k8s/overlays/production/resource-limits.yaml
apiVersion: v1
kind: LimitRange
metadata:
  name: myapp-limits
spec:
  limits:
  - default:
      memory: 512Mi
      cpu: 500m
    defaultRequest:
      memory: 256Mi
      cpu: 250m
    type: Container
```

## 📊 Monitoring et Observabilité

### 1. 📈 Monitoring Stack moderne

```
Tu es un expert monitoring et observabilité.

TA TÂCHE : Implémenter un stack de monitoring complet pour [APPLICATION]

STACK DE MONITORING :
1. **Metrics** : Prometheus, Grafana
2. **Logs** : ELK Stack, Fluentd
3. **Traces** : Jaeger, AWS X-Ray
4. **Alerts** : AlertManager, PagerDuty
5. **Dashboards** : Performance, business metrics
6. **APM** : Application Performance Monitoring

CONTRAINTES :
- Real-time monitoring
- Alert fatigue prevention
- Performance impact minimal
- Cost optimization
- Compliance requirements

FORMAT DE SORTIE :
1. Monitoring architecture
2. Prometheus configuration
3. Grafana dashboards
4. Alert rules
5. Log aggregation setup
6. APM integration
7. Cost optimization

QUALITÉ : Monitoring complet, actionable, cost-effective
```

## 📝 Templates DevOps

### 🔄 Template CI/CD Pipeline
```
Tu es un expert CI/CD et [PLATFORM].

TA TÂCHE : Configurer pipeline CI/CD pour [APPLICATION]

STAGES :
- Build et compilation
- Tests automatisés
- Security scanning
- Deploy staging/production

CONTRAINTES :
- [FRAMEWORK] specific
- [CLOUD] deployment
- Security compliance
- Performance requirements

FORMAT :
1. Pipeline configuration
2. Build scripts
3. Test automation
4. Deploy scripts
5. Security integration
6. Monitoring setup
7. Rollback strategy

QUALITÉ : Pipeline CI/CD robuste, sécurisé, automatisé
```

### 🐳 Template Docker Configuration
```
Tu es un expert Docker et containerization.

TA TÂCHE : Créer configuration Docker pour [APPLICATION]

COMPOSANTS :
- Dockerfile multi-stage
- Docker Compose dev
- Production configuration
- Health checks

CONTRAINTES :
- Security best practices
- Performance optimisée
- Multi-environment support
- Resource constraints

FORMAT :
1. Dockerfile optimized
2. Docker Compose setup
3. Production config
4. Health checks
5. Security hardening
6. Performance tuning
7. Documentation

QUALITÉ : Configuration Docker production-ready, sécurisée
```

### ☁️ Template Infrastructure as Code
```
Tu es un expert IaC et [CLOUD PROVIDER].

TA TÂCHE : Provisionner infrastructure avec [TOOL]

RESSOURCES :
- VPC et networking
- Compute resources
- Database setup
- Monitoring stack

CONTRAINTES :
- Security compliance
- Cost optimization
- High availability
- Scalability requirements

FORMAT :
1. IaC architecture
2. Module configuration
3. Security policies
4. Monitoring setup
5. Cost optimization
6. Deployment pipeline
7. Documentation

QUALITÉ : Infrastructure IaC scalable, sécurisée, cost-effective
```

## 🎯 Points clés à retenir

1. **GitHub Actions** : CI/CD moderne, intégré, flexible
2. **Docker** : Containerisation, isolation, portability
3. **Kubernetes** : Orchestration, scalability, resilience
4. **Terraform** : Infrastructure as Code, versioning, automation
5. **Monitoring** : Observabilité, alerting, performance
6. **Security** : DevSecOps, compliance, best practices

## 🚀 Prochain chapitre

Maintenant que vous maîtrisez DevOps, découvrons les prompts pour les audits de sécurité et le pentest assisté.

---

## 📋 Checklist qualité

- ✅ CI/CD pipeline complet
- ✅ Docker configuration
- ✅ Kubernetes deployment
- ✅ Terraform infrastructure
- ✅ Monitoring stack
- ✅ Security best practices

## 🎯 Test de compréhension

**Question :** Pourquoi utiliser Kubernetes plutôt que Docker Compose en production ?

**Réponse attendue :** Kubernetes offre : orchestration automatique, scalability horizontale, auto-healing, rolling updates, resource management, service discovery, load balancing, secrets management, multi-environment support, et high availability. Docker Compose est limité à un seul host.

---

**Temps de lecture estimé : 85 minutes**
**Niveau : Expert**
**Prérequis : Partie I + II + III**
