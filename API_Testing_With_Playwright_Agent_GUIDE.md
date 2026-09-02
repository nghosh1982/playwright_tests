# API Testing Using Playwright Test Planner, Generator, and Healer Agents

## Overview

This guide provides comprehensive information on how to create and maintain API tests using three specialized Playwright agents:
- **Test Planner Agent** - Plans and strategizes API test scenarios
- **Test Generator Agent** - Generates API test code automatically
- **Test Healer Agent** - Repairs and maintains failing tests

---

## Table of Contents

1. [Introduction](#introduction)
2. [Prerequisites](#prerequisites)
3. [Test Planner Agent](#test-planner-agent)
4. [Test Generator Agent](#test-generator-agent)
5. [Test Healer Agent](#test-healer-agent)
6. [Complete Workflow](#complete-workflow)
7. [Best Practices](#best-practices)
8. [Troubleshooting](#troubleshooting)
9. [Examples](#examples)

---

## Introduction

### What Are These Agents?

The Playwright test agents are AI-powered tools designed to accelerate API testing:

| Agent | Purpose | Use Case |
|-------|---------|----------|
| **Test Planner** | Analyzes API specifications and creates test plans | Initial test strategy and coverage analysis |
| **Test Generator** | Generates executable Playwright test code | Creating and scaffolding test files |
| **Test Healer** | Identifies and fixes broken tests | Maintenance and repair of flaky tests |

### Why Use These Agents?

- **Speed**: Rapidly create comprehensive test suites
- **Consistency**: Maintain uniform test structure and patterns
- **Reliability**: Automatic detection and repair of failing tests
- **Scalability**: Generate tests for multiple API endpoints
- **Maintainability**: Clear, well-organized test code

---

## Prerequisites

### System Requirements

```bash
Node.js >= 16.0.0
npm >= 8.0.0
Playwright >= 1.40.0
```

### Installation Steps

1. **Initialize Playwright Project**
```bash
npm init playwright@latest
```

2. **Install Dependencies**
```bash
npm install @playwright/test
npm install -D dotenv
npm install -D @types/node
```

3. **Create Project Structure**
```
playwright_tests/
├── tests/
│   ├── api/
│   │   ├── users.spec.ts
│   │   ├── products.spec.ts
│   │   └── orders.spec.ts
│   └── fixtures/
│       └── api-client.ts
├── utils/
│   ├── request-helpers.ts
│   ├── auth-helpers.ts
│   └── data-validators.ts
├── data/
│   ├── test-data.json
│   └── fixtures.json
├── playwright.config.ts
├── .env.example
└── README.md
```

---

## Test Planner Agent

### What Does It Do?

The Test Planner Agent analyzes your API and creates a comprehensive test plan by:
- Reviewing API documentation/specifications
- Identifying test scenarios and edge cases
- Prioritizing tests by importance
- Defining test coverage goals
- Mapping dependencies between tests

### How to Use It

#### Step 1: Prepare API Specification

Create a specification file or use your API documentation:

```yaml
# api-spec.yaml
endpoints:
  - name: Get User
    method: GET
    path: /api/users/{id}
    params: [id]
    responses:
      - status: 200
        schema: User
      - status: 404
        message: "User not found"
  
  - name: Create User
    method: POST
    path: /api/users
    body:
      required: [name, email]
      optional: [phone, avatar]
    responses:
      - status: 201
        schema: User
      - status: 400
        message: "Validation error"
```

#### Step 2: Invoke the Test Planner

**Via CLI**:
```bash
playwright test-planner --spec api-spec.yaml --output test-plan.json
```

**Via Code**:
```typescript
import { TestPlanner } from '@playwright/test-agents';

const planner = new TestPlanner();
const plan = await planner.analyze({
  specFile: 'api-spec.yaml',
  baseUrl: 'https://api.example.com',
  coverage: 'high' // low | medium | high
});

console.log(plan);
```

#### Step 3: Review and Customize Plan

Example output from Test Planner:

```json
{
  "title": "User API Test Plan",
  "baseUrl": "https://api.example.com",
  "scenarios": [
    {
      "id": "GET_USER_001",
      "endpoint": "GET /api/users/{id}",
      "priority": "high",
      "tests": [
        {
          "name": "Get existing user by ID",
          "steps": [
            "Send GET request to /api/users/123",
            "Assert status code 200",
            "Assert response contains user object",
            "Assert id matches request parameter"
          ]
        },
        {
          "name": "Get non-existent user returns 404",
          "steps": [
            "Send GET request to /api/users/99999",
            "Assert status code 404"
          ]
        }
      ]
    },
    {
      "id": "POST_USER_001",
      "endpoint": "POST /api/users",
      "priority": "high",
      "tests": [
        {
          "name": "Create user with valid data",
          "steps": [
            "Send POST request with valid user data",
            "Assert status code 201",
            "Assert response contains user object with ID"
          ]
        },
        {
          "name": "Create user with missing required fields",
          "steps": [
            "Send POST request without 'email' field",
            "Assert status code 400",
            "Assert error message about missing field"
          ]
        }
      ]
    }
  ],
  "coverage": {
    "endpoints": 3,
    "scenarios": 5,
    "estimatedTests": 12,
    "coverage_percentage": 85
  }
}
```

### Test Planner Configuration

```typescript
// test-planner.config.ts
export const plannerConfig = {
  // API Configuration
  baseUrl: 'https://api.example.com',
  apiVersion: 'v1',
  
  // Coverage Options
  coverage: 'high', // low, medium, high, comprehensive
  
  // Test Scope
  includeAuthentication: true,
  includeErrorScenarios: true,
  includeEdgeCases: true,
  includePerformanceTests: false,
  
  // Priorities
  focusOn: ['users', 'products'], // Focus on specific endpoints
  
  // Output
  outputFormat: 'json', // json, yaml, markdown
  outputPath: './test-plans/',
};
```

---

## Test Generator Agent

### What Does It Do?

The Test Generator Agent creates executable Playwright test code based on:
- Test plans from the Test Planner
- API specifications
- Custom test templates
- Best practices and patterns

### How to Use It

#### Step 1: Configure Generator

```typescript
// test-generator.config.ts
export const generatorConfig = {
  // Template options
  template: 'standard', // standard, bdd, advanced
  
  // Language & Framework
  language: 'typescript',
  framework: 'playwright',
  
  // Features to generate
  features: {
    fixtures: true,
    fixtures: true,
    helpers: true,
    errorHandling: true,
    logging: true,
    retries: true,
    authentication: true,
  },
  
  // Code style
  style: {
    usePageObjectModel: false,
    useFixtures: true,
    useTestDataBuilder: true,
  },
  
  // Output
  outputDirectory: './tests/api/',
  overwrite: false,
};
```

#### Step 2: Generate Tests

**Via CLI**:
```bash
playwright test-generator \
  --plan test-plan.json \
  --config test-generator.config.ts \
  --output ./tests/api/
```

**Via Code**:
```typescript
import { TestGenerator } from '@playwright/test-agents';

const generator = new TestGenerator();
const tests = await generator.generate({
  plan: testPlan,
  config: generatorConfig,
  templates: customTemplates
});

console.log(`Generated ${tests.length} test files`);
```

#### Step 3: Review Generated Tests

Example generated test file:

```typescript
// tests/api/users.spec.ts
import { test, expect } from '@playwright/test';
import { createRequestContext, createTestUser } from '../fixtures/api-client';

test.describe('User API Tests', () => {
  let apiContext;

  test.beforeAll(async ({ playwright }) => {
    apiContext = await createRequestContext('https://api.example.com');
  });

  test.afterAll(async () => {
    await apiContext.dispose();
  });

  test.describe('GET /api/users/{id}', () => {
    test('GET_USER_001: Should retrieve existing user by ID', async () => {
      // Arrange
      const userId = 123;

      // Act
      const response = await apiContext.get(`/api/users/${userId}`);

      // Assert
      expect(response.status()).toBe(200);
      
      const body = await response.json();
      expect(body).toHaveProperty('id', userId);
      expect(body).toHaveProperty('name');
      expect(body).toHaveProperty('email');
    });

    test('GET_USER_002: Should return 404 for non-existent user', async () => {
      // Arrange
      const userId = 99999;

      // Act
      const response = await apiContext.get(`/api/users/${userId}`);

      // Assert
      expect(response.status()).toBe(404);
    });
  });

  test.describe('POST /api/users', () => {
    test('POST_USER_001: Should create user with valid data', async () => {
      // Arrange
      const userData = {
        name: 'John Doe',
        email: 'john@example.com',
        phone: '+1234567890'
      };

      // Act
      const response = await apiContext.post('/api/users', {
        data: userData
      });

      // Assert
      expect(response.status()).toBe(201);
      
      const body = await response.json();
      expect(body).toHaveProperty('id');
      expect(body.name).toBe(userData.name);
      expect(body.email).toBe(userData.email);
    });

    test('POST_USER_002: Should return 400 for missing required fields', async () => {
      // Arrange
      const invalidData = {
        name: 'John Doe'
        // Missing email
      };

      // Act
      const response = await apiContext.post('/api/users', {
        data: invalidData
      });

      // Assert
      expect(response.status()).toBe(400);
      
      const body = await response.json();
      expect(body).toHaveProperty('errors');
      expect(body.errors).toContain('email');
    });
  });
});
```

### Generator Output Structure

The Test Generator creates the following files:

```
tests/api/
├── generated/
│   ├── users.spec.ts
│   ├── products.spec.ts
│   ├── orders.spec.ts
│   └── README.md
├── fixtures/
│   └── api-client.ts
└── data/
    └── test-data.json
```

### Customizing Generated Tests

#### Create Custom Test Templates

```typescript
// templates/custom-api-test.hbs
import { test, expect } from '@playwright/test';
import { {{import}} } from '{{importPath}}';

test.describe('{{endpoint}}', () => {
  {{#each tests}}
  test('{{this.id}}: {{this.name}}', async ({{ context }}) => {
    // Arrange
    {{#each this.arrange}}
    {{this}}
    {{/each}}

    // Act
    {{#each this.act}}
    {{this}}
    {{/each}}

    // Assert
    {{#each this.assert}}
    {{this}}
    {{/each}}
  });
  {{/each}}
});
```

#### Use Custom Template

```typescript
const tests = await generator.generate({
  plan: testPlan,
  customTemplate: './templates/custom-api-test.hbs',
});
```

---

## Test Healer Agent

### What Does It Do?

The Test Healer Agent automatically:
- Identifies failing tests
- Analyzes failure root causes
- Suggests fixes
- Applies automated repairs
- Maintains test health over time

### How to Use It

#### Step 1: Configure Healer

```typescript
// test-healer.config.ts
export const healerConfig = {
  // Detection
  watchMode: true,
  autoRun: true,
  
  // Analysis
  analyzeFailurePatterns: true,
  trackFlakiness: true,
  
  // Repair Strategy
  strategy: 'aggressive', // conservative | balanced | aggressive
  
  // Actions
  autoRepair: true,
  createIssues: true,
  notifyTeam: true,
  
  // Logging
  logLevel: 'info',
  outputPath: './healer-reports/',
};
```

#### Step 2: Run Healer

**Via CLI**:
```bash
playwright test-healer --watch --auto-repair
```

**Via Code**:
```typescript
import { TestHealer } from '@playwright/test-agents';

const healer = new TestHealer(healerConfig);

// Start monitoring
healer.onTestFailure(async (failure) => {
  console.log(`Test failed: ${failure.testName}`);
  
  const analysis = await healer.analyze(failure);
  console.log(`Root cause: ${analysis.rootCause}`);
  
  if (analysis.canAutoRepair) {
    const repair = await healer.repair(failure, analysis);
    console.log(`Auto-repaired: ${repair.success}`);
  }
});

await healer.start();
```

#### Step 3: Review Healer Reports

Example healer analysis output:

```json
{
  "testId": "GET_USER_001",
  "testName": "Should retrieve existing user by ID",
  "status": "FAILED",
  "error": "Expected status 200, got 503",
  "timestamp": "2024-01-15T10:30:00Z",
  "analysis": {
    "rootCause": "API server temporarily unavailable",
    "category": "FLAKY_TEST",
    "severity": "MEDIUM",
    "confidence": 0.92,
    "affectedTests": 5,
    "pattern": "Service restart during test execution"
  },
  "suggestions": [
    {
      "type": "RETRY_WITH_BACKOFF",
      "confidence": 0.95,
      "code": "expect.poll(async () => {\n  const response = await apiContext.get('/api/users/123');\n  return response.status();\n}, { timeout: 10000 }).toBe(200);"
    },
    {
      "type": "WAIT_FOR_SERVICE",
      "confidence": 0.87,
      "code": "await page.waitForURL('/api/health', { waitUntil: 'commit' });"
    },
    {
      "type": "INCREASE_TIMEOUT",
      "confidence": 0.78,
      "code": "test.setTimeout(30000);"
    }
  ],
  "autoRepairApplied": true,
  "repairDetails": {
    "type": "RETRY_WITH_BACKOFF",
    "timestamp": "2024-01-15T10:30:45Z",
    "status": "SUCCESS"
  }
}
```

### Healer Features

#### Real-Time Monitoring

```typescript
// Enable real-time monitoring
const healer = new TestHealer(healerConfig);

healer.onTestStart((test) => {
  console.log(`Starting: ${test.name}`);
});

healer.onTestPass((test) => {
  console.log(`Passed: ${test.name}`);
});

healer.onTestFail((failure) => {
  console.log(`Failed: ${failure.testName}`);
  // Auto-analyze and repair
  healer.analyzeAndRepair(failure);
});

healer.onTestSkip((test) => {
  console.log(`Skipped: ${test.name}`);
});
```

#### Flakiness Detection

```typescript
// Track flaky tests
const flakies = await healer.detectFlakiness({
  minRuns: 10,
  failureThreshold: 0.5, // 50% failure rate
  timeWindow: '7d'
});

for (const flaky of flakies) {
  console.log(`Flaky test: ${flaky.testName}`);
  console.log(`Failure rate: ${flaky.failureRate}%`);
  console.log(`Suggested fix: ${flaky.suggestion}`);
}
```

#### Health Dashboard

```typescript
// Generate health report
const health = await healer.generateHealthReport();

console.log(`Total tests: ${health.totalTests}`);
console.log(`Passing: ${health.passing} (${health.passingRate}%)`);
console.log(`Failing: ${health.failing} (${health.failingRate}%)`);
console.log(`Flaky: ${health.flaky} (${health.flakyRate}%)`);
console.log(`Repaired this week: ${health.repairedThisWeek}`);
```

---

## Complete Workflow

### End-to-End Process

```
┌─────────────────────────────────────────────────────────────┐
│                  API Testing Workflow                        │
└─────────────────────────────────────────────────────────────┘

Step 1: PLANNING
├─ Prepare API specification
├─ Invoke Test Planner Agent
├─ Review test plan
└─ Adjust coverage if needed

Step 2: GENERATION
├─ Configure Test Generator
├─ Run generator against test plan
├─ Review generated tests
└─ Customize if needed

Step 3: EXECUTION
├─ Run tests locally
├─ Validate test results
└─ Commit to repository

Step 4: MAINTENANCE
├─ Deploy Test Healer Agent
├─ Monitor test health
├─ Auto-repair failures
└─ Track improvements
```

### Detailed Workflow Example

#### Phase 1: Planning (Day 1)

```bash
# Step 1: Prepare specification
cat > api-spec.yaml << 'EOF'
endpoints:
  - name: Get User
    method: GET
    path: /api/users/{id}
    responses:
      - status: 200
      - status: 404
EOF

# Step 2: Run Test Planner
npx playwright test-planner \
  --spec api-spec.yaml \
  --coverage high \
  --output test-plan.json

# Step 3: Review output
cat test-plan.json
```

#### Phase 2: Generation (Day 2)

```bash
# Step 1: Generate tests
npx playwright test-generator \
  --plan test-plan.json \
  --output ./tests/api/

# Step 2: Review generated code
ls -la ./tests/api/

# Step 3: Customize if needed
# Edit test files as desired
```

#### Phase 3: Execution (Day 3)

```bash
# Step 1: Run tests
npx playwright test tests/api/

# Step 2: View results
npx playwright show-report

# Step 3: Commit to repository
git add tests/api/
git commit -m "Generated API tests from test planner"
```

#### Phase 4: Maintenance (Ongoing)

```bash
# Step 1: Deploy Test Healer
npx playwright test-healer --watch --auto-repair

# Step 2: Monitor dashboard
# Visit http://localhost:3000/healer/dashboard

# Step 3: Review reports weekly
ls -la ./healer-reports/
```

---

## Best Practices

### 1. Test Organization

✅ **DO**:
```typescript
// Group related tests
test.describe('User API', () => {
  test.describe('GET /users', () => {
    // GET tests
  });
  
  test.describe('POST /users', () => {
    // POST tests
  });
});
```

❌ **DON'T**:
```typescript
// Mix unrelated endpoints
test('get user', async () => {});
test('create product', async () => {});
test('delete order', async () => {});
```

### 2. Data Management

✅ **DO**:
```typescript
// Use test data builders
const user = new UserBuilder()
  .withName('John Doe')
  .withEmail('john@example.com')
  .build();
```

❌ **DON'T**:
```typescript
// Hard-code data everywhere
const user = {
  name: 'John Doe',
  email: 'john@example.com'
};
```

### 3. Error Handling

✅ **DO**:
```typescript
// Handle different error scenarios
if (response.status() === 400) {
  expect(body).toHaveProperty('errors');
} else if (response.status() === 500) {
  // Handle server error
}
```

❌ **DON'T**:
```typescript
// Only check status code
expect(response.status()).toBe(200);
```

### 4. Assertions

✅ **DO**:
```typescript
// Use specific assertions
expect(user.id).toBeDefined();
expect(user.email).toMatch(/@example\.com$/);
expect(users).toHaveLength(10);
```

❌ **DON'T**:
```typescript
// Vague assertions
expect(user).toBeTruthy();
expect(response).toBeTruthy();
```

### 5. Test Independence

✅ **DO**:
```typescript
// Each test is independent
test('user A', async () => {
  // Setup own data
  // Run own test
  // Cleanup own data
});
```

❌ **DON'T**:
```typescript
// Tests depend on each other
test('create user', () => { /* setup */ });
test('get user', () => { /* uses data from previous test */ });
```

### 6. Performance

✅ **DO**:
```typescript
// Reuse connections
test.beforeAll(async () => {
  apiContext = await createRequestContext();
});

test.afterAll(async () => {
  await apiContext.dispose();
});
```

❌ **DON'T**:
```typescript
// Create new connection for each test
test('test 1', async () => {
  const response = await fetch(...);
});

test('test 2', async () => {
  const response = await fetch(...);
});
```

### 7. Maintainability

✅ **DO**:
```typescript
// Use meaningful test names
test('should create user with valid email format', async () => {});
```

❌ **DON'T**:
```typescript
// Vague test names
test('test 1', async () => {});
test('user test', async () => {});
```

---

## Troubleshooting

### Common Issues and Solutions

#### Issue 1: Test Planner Fails to Analyze Spec

**Error**: `Failed to parse specification file`

**Solution**:
```bash
# Validate YAML/JSON syntax
npm install -g yaml-validator
yaml-validator api-spec.yaml

# Check file encoding
file -i api-spec.yaml # Should be UTF-8

# Verify required fields
# Ensure spec has: endpoints[], method, path, responses[]
```

#### Issue 2: Generated Tests Have Syntax Errors

**Error**: `SyntaxError: Unexpected token`

**Solution**:
```bash
# Check TypeScript compilation
npx tsc --noEmit tests/api/

# Regenerate with correct template
npx playwright test-generator --template standard --overwrite

# Lint generated code
npx eslint tests/api/ --fix
```

#### Issue 3: Test Healer Doesn't Detect Failures

**Error**: `No failures detected`

**Solution**:
```typescript
// Ensure healer is watching correct directory
const healer = new TestHealer({
  testDir: './tests/api',
  watchMode: true,
  autoRun: true
});

// Check healer logs
healer.setLogLevel('debug');
```

#### Issue 4: API Context Connection Issues

**Error**: `Failed to connect to API server`

**Solution**:
```typescript
// Verify base URL
console.log(`Connecting to: ${apiContext.baseUrl}`);

// Check network connectivity
await apiContext.get('/health');

// Add retry logic
const response = await apiContext.get('/api/users', {
  timeout: 30000,
  retries: 3
});
```

#### Issue 5: Flaky Tests Not Being Detected

**Error**: `No flaky tests found`

**Solution**:
```typescript
// Run tests multiple times
npx playwright test tests/api/ --repeat=10

// Use healer flakiness detection
const flakies = await healer.detectFlakiness({
  minRuns: 20,
  failureThreshold: 0.3
});

// Analyze specific test
const analysis = await healer.analyzeTestFlakiness('GET_USER_001');
```

---

## Examples

### Example 1: Testing User API

#### API Specification

```yaml
# api-spec.yaml
endpoints:
  - name: Get User
    method: GET
    path: /api/users/{id}
    params:
      - name: id
        required: true
        type: string
    responses:
      - status: 200
        schema:
          id: string
          name: string
          email: string
      - status: 404
        
  - name: Create User
    method: POST
    path: /api/users
    body:
      required: [name, email]
    responses:
      - status: 201
      - status: 400
```

#### Generated Test

```typescript
// tests/api/users.spec.ts
import { test, expect } from '@playwright/test';
import { createRequestContext } from '../fixtures/api-client';

test.describe('User API', () => {
  let apiContext;

  test.beforeAll(async ({ playwright }) => {
    apiContext = await createRequestContext(
      'https://jsonplaceholder.typicode.com'
    );
  });

  test.describe('GET /users/{id}', () => {
    test('should get existing user', async () => {
      const response = await apiContext.get('/users/1');
      
      expect(response.status()).toBe(200);
      const user = await response.json();
      expect(user.id).toBe(1);
      expect(user.name).toBeDefined();
      expect(user.email).toBeDefined();
    });

    test('should return 404 for non-existent user', async () => {
      const response = await apiContext.get('/users/99999');
      expect(response.status()).toBe(404);
    });
  });

  test.describe('POST /users', () => {
    test('should create user with valid data', async () => {
      const newUser = {
        name: 'John Doe',
        email: 'john@example.com',
        phone: '1234567890'
      };

      const response = await apiContext.post('/users', {
        data: newUser
      });

      expect(response.status()).toBe(201);
      const created = await response.json();
      expect(created.name).toBe(newUser.name);
      expect(created.email).toBe(newUser.email);
    });

    test('should return 400 for missing email', async () => {
      const invalidUser = { name: 'John Doe' };

      const response = await apiContext.post('/users', {
        data: invalidUser
      });

      expect(response.status()).toBe(400);
    });
  });

  test.afterAll(async () => {
    await apiContext.dispose();
  });
});
```

#### Running Tests

```bash
# Run all user API tests
npx playwright test tests/api/users.spec.ts

# Run specific test
npx playwright test -g "should get existing user"

# Run with UI
npx playwright test --ui

# View report
npx playwright show-report
```

### Example 2: Product API with Authentication

#### Configuration

```typescript
// tests/fixtures/api-client.ts
import { APIRequestContext } from '@playwright/test';

export interface ApiClientOptions {
  baseUrl: string;
  token?: string;
}

export async function createRequestContext(options: ApiClientOptions) {
  const headers: Record<string, string> = {
    'Content-Type': 'application/json'
  };

  if (options.token) {
    headers['Authorization'] = `Bearer ${options.token}`;
  }

  return {
    async get(path: string) {
      return fetch(`${options.baseUrl}${path}`, {
        method: 'GET',
        headers
      });
    },

    async post(path: string, data: any) {
      return fetch(`${options.baseUrl}${path}`, {
        method: 'POST',
        headers,
        body: JSON.stringify(data)
      });
    },

    async put(path: string, data: any) {
      return fetch(`${options.baseUrl}${path}`, {
        method: 'PUT',
        headers,
        body: JSON.stringify(data)
      });
    },

    async delete(path: string) {
      return fetch(`${options.baseUrl}${path}`, {
        method: 'DELETE',
        headers
      });
    }
  };
}
```

#### Product Tests

```typescript
// tests/api/products.spec.ts
import { test, expect } from '@playwright/test';
import { createRequestContext } from '../fixtures/api-client';

test.describe('Product API', () => {
  let apiContext;
  const token = process.env.API_TOKEN || 'test-token';

  test.beforeAll(async () => {
    apiContext = await createRequestContext({
      baseUrl: 'https://api.example.com/v1',
      token
    });
  });

  test.describe('GET /products', () => {
    test('should list all products', async () => {
      const response = await apiContext.get('/products');
      
      expect(response.status()).toBe(200);
      const products = await response.json();
      expect(Array.isArray(products)).toBe(true);
    });

    test('should filter products by category', async () => {
      const response = await apiContext.get('/products?category=electronics');
      
      expect(response.status()).toBe(200);
      const products = await response.json();
      products.forEach(p => {
        expect(p.category).toBe('electronics');
      });
    });
  });

  test.describe('POST /products', () => {
    test('should create product', async () => {
      const product = {
        name: 'New Product',
        price: 99.99,
        category: 'electronics'
      };

      const response = await apiContext.post('/products', product);
      
      expect(response.status()).toBe(201);
      const created = await response.json();
      expect(created.id).toBeDefined();
    });
  });

  test.afterAll(async () => {
    await apiContext.dispose();
  });
});
```

### Example 3: Using Test Healer for Maintenance

```typescript
// healer-example.ts
import { TestHealer } from '@playwright/test-agents';

const healer = new TestHealer({
  testDir: './tests/api',
  watchMode: true,
  autoRepair: true,
  outputPath: './healer-reports/'
});

// Monitor failures
healer.onTestFail(async (failure) => {
  console.log(`❌ Test Failed: ${failure.testName}`);
  
  const analysis = await healer.analyze(failure);
  console.log(`📊 Analysis: ${analysis.rootCause}`);
  
  if (analysis.canAutoRepair) {
    const repair = await healer.repair(failure, analysis);
    console.log(`✅ Auto-repaired: ${repair.success}`);
  } else {
    console.log(`⚠️  Requires manual review`);
  }
});

// Weekly health report
setInterval(async () => {
  const health = await healer.generateHealthReport();
  console.log(`\n📈 Weekly Health Report:`);
  console.log(`Total: ${health.totalTests}`);
  console.log(`Passing: ${health.passingRate}%`);
  console.log(`Flaky: ${health.flakyCount}`);
}, 7 * 24 * 60 * 60 * 1000);

healer.start();
```

---

## Advanced Topics

### Custom Validators

```typescript
// utils/validators.ts
export const validators = {
  isValidEmail: (email: string) => /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email),
  isValidPhone: (phone: string) => /^\d{10}$/.test(phone),
  isValidUUID: (uuid: string) => /^[0-9a-f]{8}-[0-9a-f]{4}-4[0-9a-f]{3}-[89ab][0-9a-f]{3}-[0-9a-f]{12}$/i.test(uuid),
};

// In tests
const body = await response.json();
expect(validators.isValidEmail(body.email)).toBe(true);
```

### Performance Testing

```typescript
// tests/api/performance.spec.ts
test('GET /users should respond within 500ms', async () => {
  const start = Date.now();
  const response = await apiContext.get('/users');
  const duration = Date.now() - start;
  
  expect(response.status()).toBe(200);
  expect(duration).toBeLessThan(500);
});
```

### Parallel Execution

```bash
# Run tests in parallel (default)
npx playwright test

# Run tests sequentially
npx playwright test --workers=1

# Run with specific worker count
npx playwright test --workers=4
```

---

## Resources

- [Playwright Documentation](https://playwright.dev)
- [API Testing Best Practices](https://testingcup.com)
- [Test Agents Documentation](https://github.com/microsoft/playwright)
- [Continuous Integration Guides](https://playwright.dev/docs/ci)

---

## Summary

Using Playwright Test Planner, Generator, and Healer agents provides:

| Agent | Benefit | Time Saved |
|-------|---------|-----------|
| **Planner** | Comprehensive test coverage | 70% faster planning |
| **Generator** | Consistent, maintainable tests | 80% faster generation |
| **Healer** | Self-healing tests | 60% less maintenance |

**Total Impact**: ~95% reduction in API test creation and maintenance time.

---

## Document Version

- **Version**: 1.00
- **Last Updated**: 2026-09-01
- **Author**: Playwright Test Agents Guide
- **License**: GPL-3.0

---

*For questions or updates, please refer to the [Playwright documentation](https://playwright.dev) and [GitHub discussions](https://github.com/microsoft/playwright).*
