# 🧪 Testing Guide for Software Engineers

> Comprehensive guide covering unit testing, integration testing, E2E testing, TDD, and best practices.

---

## 📚 Table of Contents

1. [Why Testing Matters](#-why-testing-matters)
2. [Types of Testing](#-types-of-testing)
3. [Test-Driven Development (TDD)](#-test-driven-development-tdd)
4. [Unit Testing](#-unit-testing)
5. [Integration Testing](#-integration-testing)
6. [End-to-End (E2E) Testing](#-end-to-end-e2e-testing)
7. [Test Coverage](#-test-coverage)
8. [Mocking and Stubbing](#-mocking-and-stubbing)
9. [Testing Best Practices](#-testing-best-practices)
10. [Testing Tools](#-testing-tools)

---

## 🎯 Why Testing Matters

### Benefits of Testing

| Benefit | Description |
|---------|-------------|
| **Bug Prevention** | Catch bugs before production |
| **Confidence** | Make changes without fear of breaking things |
| **Documentation** | Tests document expected behavior |
| **Design Feedback** | Difficult-to-test code often means poor design |
| **Regression Prevention** | Ensure old bugs don't resurface |
| **Faster Development** | Long-term time savings despite initial investment |

---

## 🔍 Types of Testing

### Testing Pyramid

```
         / \
        /E2E\
       /------\
      /  API   \
     /----------\
    /    Unit    \
   /--------------\
```

**Philosophy**: More unit tests, fewer E2E tests.

---

### 1. **Unit Testing**

Testing individual functions/methods in isolation.

**Characteristics:**
- Fast execution
- No external dependencies
- Tests single functionality
- Should be independent

**Example:**
```php
public function test_calculate_discount()
{
    $price = 100;
    $discount = 10; // 10%
    
    $result = calculateDiscount($price, $discount);
    
    $this->assertEquals(90, $result);
}
```

---

### 2. **Integration Testing**

Testing how components work together.

**Tests:**
- Database interactions
- API calls
- Service integrations
- Multiple modules together

**Example:**
```php
public function test_user_can_create_post()
{
    $user = User::factory()->create();
    
    $response = $this->actingAs($user)->post('/posts', [
        'title' => 'Test Post',
        'content' => 'Test Content'
    ]);
    
    $response->assertStatus(201);
    $this->assertDatabaseHas('posts', ['title' => 'Test Post']);
}
```

---

### 3. **End-to-End (E2E) Testing**

Testing complete user workflows through the UI.

**Tools:**
- Selenium
- Cypress
- Playwright
- Laravel Dusk

**Example:**
```javascript
// Cypress
describe('Login Flow', () => {
  it('should login successfully', () => {
    cy.visit('/login')
    cy.get('[name=email]').type('user@example.com')
    cy.get('[name=password]').type('password123')
    cy.get('button[type=submit]').click()
    cy.url().should('include', '/dashboard')
  })
})
```

---

### 4. **Acceptance Testing**

Verifying that the system meets business requirements.

**Focus:** User perspective, business logic.

---

### 5. **Performance Testing**

| Type | Purpose |
|------|---------|
| **Load Testing** | How system performs under expected load |
| **Stress Testing** | Find breaking point |
| **Spike Testing** | Sudden traffic increase |
| **Endurance Testing** | Sustained load over time |

**Tools:** JMeter, Gatling, Artillery, k6

---

### 6. **Security Testing**

- Penetration testing
- Vulnerability scanning
- Authentication/authorization tests
- Input validation tests

---

## 🔴 Test-Driven Development (TDD)

### The TDD Cycle: Red-Green-Refactor

```
1. 🔴 Red: Write a failing test
2. 🟢 Green: Write minimal code to pass
3. 🔵 Refactor: Improve code while keeping tests passing
```

---

### TDD Example

#### Step 1: Write Failing Test (Red)

```php
class CalculatorTest extends TestCase
{
    public function test_can_add_two_numbers()
    {
        $calculator = new Calculator();
        $result = $calculator->add(2, 3);
        $this->assertEquals(5, $result);
    }
}

// Test fails - Calculator class doesn't exist yet
```

#### Step 2: Make It Pass (Green)

```php
class Calculator
{
    public function add($a, $b)
    {
        return $a + $b;
    }
}

// Test passes
```

#### Step 3: Refactor (Blue)

```php
class Calculator
{
    public function add(int $a, int $b): int
    {
        return $a + $b;
    }
}

// Improved with type hints, tests still pass
```

---

### Benefits of TDD

- Forces you to think about design first
- Better code coverage
- Fewer bugs
- Easier refactoring
- Living documentation

---

## 🧩 Unit Testing

### Anatomy of a Good Unit Test

```php
public function test_<what_is_being_tested>()
{
    // Arrange (Given)
    $user = new User('John');
    
    // Act (When)
    $greeting = $user->greet();
    
    // Assert (Then)
    $this->assertEquals('Hello, John!', $greeting);
}
```

**AAA Pattern:** Arrange, Act, Assert

---

### PHPUnit Assertions

```php
// Equality
$this->assertEquals($expected, $actual);
$this->assertSame($expected, $actual); // Strict comparison

// Boolean
$this->assertTrue($condition);
$this->assertFalse($condition);
$this->assertNull($value);
$this->assertNotNull($value);

// Strings
$this->assertStringContainsString('needle', 'haystack');
$this->assertStringStartsWith('prefix', 'prefixSuffix');

// Arrays
$this->assertCount(3, $array);
$this->assertContains('value', $array);
$this->assertArrayHasKey('key', $array);

// Exceptions
$this->expectException(InvalidArgumentException::class);
$this->expectExceptionMessage('Invalid input');

// Files
$this->assertFileExists('/path/to/file');
```

---

### Testing Private Methods

**Best Practice:** Don't test private methods directly.

❌ **Bad Approach:**
```php
// Using reflection to test private method
```

✅ **Good Approach:**
```php
// Test through public interface
public function test_public_method_that_uses_private_method()
{
    $result = $object->publicMethod();
    $this->assertEquals($expected, $result);
}
```

**Reasoning:** If private method is important enough to test, it might need to be public or extracted to a separate class.

---

## 🔗 Integration Testing

### Database Testing

#### Using Transactions (Laravel)

```php
use Illuminate\Foundation\Testing\RefreshDatabase;

class UserTest extends TestCase
{
    use RefreshDatabase; // Rollback after each test
    
    public function test_user_creation()
    {
        $user = User::create([
            'name' => 'John',
            'email' => 'john@example.com'
        ]);
        
        $this->assertDatabaseHas('users', [
            'email' => 'john@example.com'
        ]);
    }
}
```

---

### Factory Pattern for Test Data

```php
// Database/Factories/UserFactory.php
class UserFactory extends Factory
{
    public function definition()
    {
        return [
            'name' => $this->faker->name(),
            'email' => $this->faker->unique()->safeEmail(),
            'password' => bcrypt('password')
        ];
    }
}

// In tests
$user = User::factory()->create();
$users = User::factory()->count(10)->create();

// With specific attributes
$admin = User::factory()->create(['role' => 'admin']);
```

---

### API Testing

```php
public function test_can_fetch_users()
{
    User::factory()->count(5)->create();
    
    $response = $this->getJson('/api/users');
    
    $response->assertStatus(200)
             ->assertJsonCount(5, 'data')
             ->assertJsonStructure([
                 'data' => [
                     '*' => ['id', 'name', 'email']
                 ]
             ]);
}

public function test_can_create_user()
{
    $payload = [
        'name' => 'John Doe',
        'email' => 'john@example.com',
        'password' => 'password123'
    ];
    
    $response = $this->postJson('/api/users', $payload);
    
    $response->assertStatus(201)
             ->assertJson(['name' => 'John Doe']);
    
    $this->assertDatabaseHas('users', ['email' => 'john@example.com']);
}
```

---

## 🌐 End-to-End (E2E) Testing

### Laravel Dusk Example

```php
public function test_user_can_login()
{
    $user = User::factory()->create([
        'email' => 'test@example.com',
        'password' => bcrypt('password')
    ]);
    
    $this->browse(function (Browser $browser) use ($user) {
        $browser->visit('/login')
                ->type('email', 'test@example.com')
                ->type('password', 'password')
                ->press('Login')
                ->assertPathIs('/dashboard')
                ->assertSee('Welcome');
    });
}
```

---

### Cypress Example

```javascript
describe('Todo App', () => {
  beforeEach(() => {
    cy.visit('/')
  })

  it('can add a new todo', () => {
    cy.get('[data-cy=new-todo]')
      .type('Buy groceries{enter}')
    
    cy.get('[data-cy=todo-list]')
      .should('contain', 'Buy groceries')
  })

  it('can mark todo as complete', () => {
    cy.get('[data-cy=todo-item]').first()
      .find('[data-cy=checkbox]')
      .check()
    
    cy.get('[data-cy=todo-item]').first()
      .should('have.class', 'completed')
  })
})
```

---

## 📊 Test Coverage

### What is Code Coverage?

Percentage of code executed during tests.

```bash
# PHPUnit with coverage
./vendor/bin/phpunit --coverage-html coverage

# Jest with coverage
npm test -- --coverage
```

---

### Coverage Metrics

| Metric | Description |
|--------|-------------|
| **Line Coverage** | % of code lines executed |
| **Function Coverage** | % of functions called |
| **Branch Coverage** | % of decision branches tested |
| **Statement Coverage** | % of statements executed |

---

### Coverage Goals

- **Not a goal**: 100% coverage
- **Realistic goal**: 70-80% coverage
- **Focus on**: Critical business logic

**Remember:** 100% coverage ≠ Bug-free code

---

## 🎭 Mocking and Stubbing

### What is Mocking?

**Mock**: Fake object that records how it was used.  
**Stub**: Fake object that returns predefined responses.  
**Spy**: Real object that records calls.

---

### Mocking External Services

```php
use Mockery;

public function test_sends_email_on_signup()
{
    // Mock email service
    $mailer = Mockery::mock(MailerInterface::class);
    $mailer->shouldReceive('send')
           ->once()
           ->with(Mockery::type('string'));
    
    $this->app->instance(MailerInterface::class, $mailer);
    
    // Test user signup
    $this->post('/register', [
        'email' => 'test@example.com',
        'password' => 'password'
    ]);
}
```

---

### PHPUnit Mocks

```php
public function test_user_repository()
{
    // Create mock
    $mock = $this->createMock(UserRepository::class);
    
    // Define behavior
    $mock->method('find')
         ->willReturn(new User(['name' => 'John']));
    
    // Use mock
    $user = $mock->find(1);
    $this->assertEquals('John', $user->name);
}
```

---

### JavaScript Mocking (Jest)

```javascript
// Mock function
const mockFn = jest.fn();
mockFn.mockReturnValue(42);

// Mock module
jest.mock('./api', () => ({
  fetchUser: jest.fn(() => Promise.resolve({ name: 'John' }))
}));

// Test
test('fetches user', async () => {
  const user = await fetchUser(1);
  expect(user.name).toBe('John');
  expect(fetchUser).toHaveBeenCalledWith(1);
});
```

---

## ✅ Testing Best Practices

### 1. **Test Behavior, Not Implementation**

❌ **Bad:**
```php
public function test_user_has_posts_array()
{
    $user = new User();
    $this->assertIsArray($user->posts); // Testing implementation
}
```

✅ **Good:**
```php
public function test_user_can_have_multiple_posts()
{
    $user = User::factory()->create();
    $posts = Post::factory()->count(3)->create(['user_id' => $user->id]);
    
    $this->assertCount(3, $user->posts); // Testing behavior
}
```

---

### 2. **One Assertion Per Test (Ideally)**

❌ **Bad:**
```php
public function test_user_operations()
{
    // Too many unrelated assertions
    $this->assertTrue($user->isActive());
    $this->assertEquals('John', $user->name);
    $this->assertCount(3, $user->posts);
}
```

✅ **Good:**
```php
public function test_user_is_active()
{
    $this->assertTrue($user->isActive());
}

public function test_user_has_correct_name()
{
    $this->assertEquals('John', $user->name);
}
```

---

### 3. **Clear Test Names**

❌ **Bad:**
```php
public function test_user() { }
public function test_1() { }
```

✅ **Good:**
```php
public function test_active_user_can_create_post() { }
public function test_guest_cannot_delete_post() { }
public function test_invalid_email_returns_validation_error() { }
```

---

### 4. **Test Edge Cases**

```php
public function test_division()
{
    // Happy path
    $this->assertEquals(2, divide(10, 5));
    
    // Edge cases
    $this->expectException(DivisionByZeroError::class);
    divide(10, 0);
}

public function test_array_operations()
{
    // Empty array
    $this->assertEmpty(filter([]));
    
    // Single item
    $this->assertCount(1, filter([1]));
    
    // Large dataset
    $this->assertCount(100, filter(range(1, 100)));
}
```

---

### 5. **Keep Tests Independent**

❌ **Bad:**
```php
public function test_a()
{
    $this->user = User::create(['name' => 'John']);
}

public function test_b()
{
    // Depends on test_a running first
    $this->assertEquals('John', $this->user->name);
}
```

✅ **Good:**
```php
public function test_a()
{
    $user = User::factory()->create(['name' => 'John']);
    // Test logic
}

public function test_b()
{
    $user = User::factory()->create(['name' => 'John']);
    // Independent test
}
```

---

### 6. **Fast Tests**

- Use in-memory databases (SQLite)
- Mock external services
- Avoid sleep() calls
- Parallelize test execution

```bash
# Run tests in parallel
./vendor/bin/phpunit --parallel
```

---

### 7. **Test Data Builders**

```php
class UserBuilder
{
    private $name = 'Default Name';
    private $email = 'default@example.com';
    private $role = 'user';
    
    public function withName($name)
    {
        $this->name = $name;
        return $this;
    }
    
    public function admin()
    {
        $this->role = 'admin';
        return $this;
    }
    
    public function build()
    {
        return User::create([
            'name' => $this->name,
            'email' => $this->email,
            'role' => $this->role
        ]);
    }
}

// Usage
$admin = (new UserBuilder())
    ->withName('John')
    ->admin()
    ->build();
```

---

## 🛠️ Testing Tools

### PHP Testing

| Tool | Purpose |
|------|---------|
| **PHPUnit** | Unit testing framework |
| **Pest** | Modern testing framework (built on PHPUnit) |
| **Mockery** | Mocking library |
| **Laravel Dusk** | Browser testing |
| **Codeception** | Full-stack testing |

---

### JavaScript Testing

| Tool | Purpose |
|------|---------|
| **Jest** | JavaScript testing framework |
| **Mocha** | Test runner |
| **Chai** | Assertion library |
| **Cypress** | E2E testing |
| **Playwright** | E2E testing |
| **Testing Library** | React/Vue component testing |

---

### API Testing

| Tool | Purpose |
|------|---------|
| **Postman** | API testing and documentation |
| **Insomnia** | REST client |
| **REST-assured** | Java API testing |
| **SuperTest** | Node.js HTTP testing |

---

### Performance Testing

| Tool | Purpose |
|------|---------|
| **JMeter** | Load testing |
| **Gatling** | Load testing |
| **k6** | Modern load testing |
| **Artillery** | Modern load testing |

---

## 📖 Testing Patterns

### Arrange-Act-Assert (AAA)

```php
public function test_user_can_update_profile()
{
    // Arrange
    $user = User::factory()->create(['name' => 'John']);
    
    // Act
    $user->update(['name' => 'Jane']);
    
    // Assert
    $this->assertEquals('Jane', $user->fresh()->name);
}
```

---

### Given-When-Then (BDD)

```php
/** @test */
public function authenticated_user_can_create_post()
{
    // Given: I am an authenticated user
    $user = User::factory()->create();
    $this->actingAs($user);
    
    // When: I create a new post
    $response = $this->post('/posts', [
        'title' => 'Test Post'
    ]);
    
    // Then: The post should be created
    $response->assertStatus(201);
    $this->assertDatabaseHas('posts', ['title' => 'Test Post']);
}
```

---

## 🎓 Advanced Topics

### Mutation Testing

Tests your tests by modifying code and seeing if tests still pass.

**Tool:** Infection (PHP)

```bash
composer require --dev infection/infection
./vendor/bin/infection
```

---

### Contract Testing

Verify that APIs meet expected contract.

**Tool:** Pact

---

### Snapshot Testing

Compare output against saved "snapshot".

```javascript
test('renders correctly', () => {
  const tree = renderer.create(<Component />).toJSON();
  expect(tree).toMatchSnapshot();
});
```

---

## 📚 Resources

### Books
- **"Test Driven Development: By Example"** by Kent Beck
- **"Growing Object-Oriented Software, Guided by Tests"** by Steve Freeman
- **"The Art of Unit Testing"** by Roy Osherove
- **"Working Effectively with Legacy Code"** by Michael Feathers

### Online Resources
- [PHPUnit Documentation](https://phpunit.de/)
- [Jest Documentation](https://jestjs.io/)
- [Laravel Testing](https://laravel.com/docs/testing)
- [Martin Fowler's Blog](https://martinfowler.com/)
- [Test Automation University](https://testautomationu.applitools.com/)

---

## ✅ Testing Checklist

- [ ] Tests are automated
- [ ] Tests run fast (< 10 minutes for full suite)
- [ ] Tests are independent
- [ ] Tests have clear names
- [ ] Edge cases are covered
- [ ] Critical paths have tests
- [ ] Tests are part of CI/CD
- [ ] Coverage is tracked (but not obsessed over)
- [ ] Tests are maintained alongside code
- [ ] Failed tests are investigated immediately

---

**Remember:** The goal of testing is confidence, not just coverage. Write tests that give you confidence to refactor and deploy!

---

## 🎯 Testing Interview Questions — Critical Scenarios

---

### 🏗️ Test Architecture & Strategy

**Q1. What is the difference between unit, integration, and end-to-end tests, and how do you balance them?**

**Answer:**

| Type | What It Tests | Speed | Confidence | Cost |
|------|--------------|-------|-----------|------|
| **Unit** | Single function/class in isolation | Very fast (ms) | Low (mocked dependencies) | Low |
| **Integration** | Interaction between components (DB, cache, services) | Medium (seconds) | Medium | Medium |
| **E2E (Functional)** | Full user flow through real system | Slow (minutes) | High | High |

**The Testing Pyramid:**
```
          /\
         /E2E\       — Few (10-20): Happy paths, critical user journeys
        /------\
       /  Integ  \   — Some (100-200): Service boundaries, DB, API contracts
      /------------\
     /     Unit     \ — Many (1000+): Business logic, edge cases, utilities
    /________________\
```

**Optimal ratio (by test count):**
- Unit: 70%, Integration: 20%, E2E: 10%

**Honeycomb pattern (microservices):**
- Inverted pyramid — focus on integration tests at service boundaries
- Few E2E tests that span services (slow, brittle)
- Unit tests for pure business logic only

**What each type catches:**
- **Unit:** Logic bugs, edge cases, regression in individual functions
- **Integration:** Wrong SQL, cache miss behavior, serialization issues
- **E2E:** Auth flows, UI state, cross-service data flows

---

**Q2. When should you mock dependencies and when should you use real ones?**

**Answer:**

**Mock when:**
- Testing a specific unit of code in isolation
- Dependency has unpredictable behavior (external API, time, randomness)
- Dependency is slow (network calls, heavy DB operations)
- Simulating error conditions (network timeout, 500 errors)

**Use real dependencies when:**
- Testing integration between layers (repository + DB)
- Testing SQL queries (mocking ORM defeats the purpose)
- Contract tests (verify your client matches provider's API)
- Performance tests

```php
// ❌ Wrong — mocking the DB for a repository test
it('finds user by email', function () {
    $db = mock(Database::class);
    $db->shouldReceive('query')->andReturn(['id' => 1, 'email' => 'a@b.com']);
    $repo = new UserRepository($db);
    // This tests nothing real — only that you call query()
});

// ✅ Right — use real SQLite or test DB
it('finds user by email', function () {
    User::factory()->create(['email' => 'a@b.com']);
    $repo = new UserRepository();
    $user = $repo->findByEmail('a@b.com');
    expect($user->email)->toBe('a@b.com');
});
```

**External API — always mock in unit/integration:**
```php
// Mock HTTP in tests, test real HTTP in contract tests
Http::fake([
    'api.stripe.com/*' => Http::response(['id' => 'ch_123', 'status' => 'succeeded'], 200),
]);
```

---

**Q3. What is contract testing and why is it better than E2E tests for microservices?**

**Answer:**

Contract testing verifies that a **consumer's expectations** match what the **provider actually delivers** — without spinning up all services.

```
Without contract testing:
  Service A and Service B integration → only tested in E2E
  E2E test is slow, brittle, requires all services running
  Breaking change in B's API → discovered only in full E2E run

With contract testing (Pact):
  Consumer (A) defines: "I expect POST /users to return {id, email, name}"
  This becomes a Pact file (contract)
  Provider (B) runs contract tests against its OWN code
  CI fails immediately when B breaks A's contract
```

**Pact workflow:**
```
1. Consumer writes tests defining expected interactions
   → Generates pact.json (the contract)
2. Consumer publishes pact.json to Pact Broker
3. Provider pulls pact.json
4. Provider's CI runs: pact verify
   → Replays all consumer interactions against real provider
   → Fails if response doesn't match contract
5. Provider publishes verification result
6. Consumer's "can-i-deploy" check before deployment
```

**Benefits over E2E:**
- Fast (runs against mocked consumer, real provider in isolation)
- Precise failure attribution (which consumer broke, which endpoint)
- Deployable independently (no full environment needed)
- Documents API expectations explicitly

---

### 🧪 Test Quality

**Q4. What causes flaky tests and how do you eliminate them?**

**Answer:**

Flaky tests pass sometimes and fail sometimes with no code change. They erode trust in the test suite.

| Root Cause | Example | Fix |
|-----------|---------|-----|
| **Timing/async** | `sleep(1)` hoping background job finishes | Use `waitForJob()`, explicit assertions |
| **Shared state** | Test A writes to DB, Test B depends on Test A's data | `DatabaseTransactions` or `RefreshDatabase` |
| **Order dependency** | Test passes only if run after another test | Each test must be fully independent (`setUp()`) |
| **External dependencies** | Test calls real API that's sometimes slow | Mock all external HTTP in unit/integration |
| **Random data** | Faker generates email with special chars, breaks validation | Seed Faker with fixed seed in tests |
| **Timezone issues** | Test passes in UTC, fails in EST | Mock time with `Carbon::setTestNow()` |
| **Race conditions** | Parallel test workers write to same file | Use unique identifiers per test worker |

```php
// ❌ Flaky — timing dependency
it('sends welcome email', function () {
    $user = User::factory()->create();
    sleep(2);  // Hoping the queue processed
    Mail::assertSent(WelcomeEmail::class);
});

// ✅ Fixed — use Queue::fake() and dispatch synchronously
it('sends welcome email', function () {
    Queue::fake();
    Mail::fake();
    $user = User::factory()->create();
    Queue::assertPushed(SendWelcomeEmail::class);

    // Or: dispatch synchronously in test env
    config(['queue.default' => 'sync']);
});

// ✅ Fix time dependency
it('token expires after 1 hour', function () {
    Carbon::setTestNow(now());
    $token = Token::create(['expires_at' => now()->addHour()]);

    Carbon::setTestNow(now()->addMinutes(61));
    expect($token->isExpired())->toBeTrue();

    Carbon::setTestNow();  // Reset
});
```

---

**Q5. How do you test high-traffic scenarios and performance-sensitive code?**

**Answer:**

**Load testing (k6, Artillery, Gatling):**
```javascript
// k6 — simulate 100 concurrent users for 5 minutes
import http from 'k6/http';
import { check, sleep } from 'k6';

export const options = {
    vus: 100,          // Virtual users
    duration: '5m',
    thresholds: {
        http_req_duration: ['p(95)<200'],  // 95th percentile < 200ms
        http_req_failed: ['rate<0.01'],    // Error rate < 1%
    },
};

export default function () {
    const res = http.get('https://api.example.com/products');
    check(res, { 'status was 200': (r) => r.status === 200 });
    sleep(1);
}
```

**PHPUnit performance assertions:**
```php
it('processes 10,000 records within 2 seconds', function () {
    $records = collect(range(1, 10000));
    $startTime = microtime(true);

    $processor->process($records);

    $elapsed = microtime(true) - $startTime;
    expect($elapsed)->toBeLessThan(2.0);
});

// Test memory usage
it('does not exceed 50MB memory for large dataset', function () {
    $memBefore = memory_get_usage();
    $result = $this->repo->getAll();  // Should use lazy loading
    $memAfter = memory_get_usage();

    expect(($memAfter - $memBefore) / 1024 / 1024)->toBeLessThan(50);
});
```

**Database query count assertions (Laravel):**
```php
it('loads users without N+1 queries', function () {
    User::factory(100)->hasPosts(5)->create();

    DB::enableQueryLog();
    $users = User::with('posts')->get();
    $queryCount = count(DB::getQueryLog());
    DB::disableQueryLog();

    expect($queryCount)->toBe(2);  // 1 for users, 1 for posts
});
```

---

**Q6. How do you structure tests for a Laravel application at scale?**

**Answer:**

**Directory structure for large apps:**
```
tests/
├── Unit/                    # Pure unit tests, no DB
│   ├── Services/
│   ├── Domain/
│   └── Helpers/
├── Integration/             # Tests with real DB/cache
│   ├── Repositories/
│   ├── Listeners/
│   └── Jobs/
├── Feature/                 # HTTP-level tests (route → response)
│   ├── Api/
│   │   ├── Auth/
│   │   ├── Products/
│   │   └── Orders/
│   └── Web/
├── Contract/                # Pact consumer contracts
└── Architecture/            # PHPStan/Deptrac rules
```

**Traits and base classes:**
```php
// Reusable test helpers
trait ActsAsAdmin {
    protected function actingAsAdmin(): self {
        $admin = User::factory()->admin()->create();
        return $this->actingAs($admin, 'sanctum');
    }
}

// Feature test base
abstract class ApiTestCase extends TestCase {
    use RefreshDatabase, ActsAsAdmin;

    protected function setUp(): void {
        parent::setUp();
        Http::preventStrayRequests();  // Fail if unmocked HTTP called
        Queue::fake();
        Mail::fake();
    }
}
```

**RefreshDatabase vs DatabaseTransactions:**
| Trait | Mechanism | Speed | Use When |
|-------|-----------|-------|---------|
| `RefreshDatabase` | Migrates + truncates all tables between tests | Slow | Schema changes, fresh data each test |
| `DatabaseTransactions` | Wraps each test in a transaction, rolls back | Fast | Tests don't change schema, no nested transactions |

---

**Q7. What is Test-Driven Development (TDD) and when does it provide ROI?**

**Answer:**

TDD cycle: **Red → Green → Refactor**

```
1. RED: Write a failing test for the desired behavior
2. GREEN: Write minimal code to make the test pass
3. REFACTOR: Clean up code while keeping tests green
```

**TDD provides ROI when:**
- Business logic is complex and rule-heavy (payment calculations, discount rules)
- Working in a domain you understand well (easy to write tests first)
- Requirements are stable and well-defined
- Long-lived code that will be maintained and refactored

**TDD is counterproductive when:**
- Exploring unknown requirements (spike/prototype phase)
- UI/UX work (hard to know the right assertion before seeing the result)
- Simple CRUD with no business logic
- Working with poorly designed legacy code (hard to test)

**BDD (Behavior-Driven Development) variant:**
```php
// Readable test descriptions as living documentation
describe('Order cancellation', function () {
    it('allows cancellation within 24 hours of placement', function () {
        $order = Order::factory()->placedAt(now()->subHours(23))->create();
        expect($order->canBeCancelled())->toBeTrue();
    });

    it('prevents cancellation after 24 hours', function () {
        $order = Order::factory()->placedAt(now()->subHours(25))->create();
        expect($order->canBeCancelled())->toBeFalse();
    });

    it('prevents cancellation of already shipped orders', function () {
        $order = Order::factory()->shipped()->create();
        expect($order->canBeCancelled())->toBeFalse();
    });
});
```
