# Backend Testing Anti-Patterns Guide

## Unit Testing Anti-Patterns

### 1. Testing Implementation Details
**Problem:** Tests break when refactoring internal code without changing behavior.

```java
// ❌ BAD: Testing private methods
@Test
void testPrivateCalculation() {
    var result = ReflectionUtils.invokePrivate(service, "calculateDiscount", 100);
    assertEquals(10, result);
}

// ✅ GOOD: Test public behavior
@Test
void shouldApplyDiscountToOrder() {
    Order order = new Order(100);
    Order result = service.processOrder(order);
    assertEquals(90, result.getTotal());
}
```

**Why it's bad:** Tests become brittle and coupled to implementation.

### 2. Mock Overuse (Over-Mocking)
**Problem:** Mocking everything makes tests meaningless.

```java
// ❌ BAD: Mocking everything
@Test
void processOrder_shouldWork() {
    Order order = mock(Order.class);
    when(order.getTotal()).thenReturn(100.0);
    when(order.getId()).thenReturn(1L);
    
    Payment payment = mock(Payment.class);
    when(paymentService.createPayment(any())).thenReturn(payment);
    
    orderService.processOrder(order);
    verify(paymentService).createPayment(order);
}

// ✅ GOOD: Use real objects where practical
@Test
void processOrder_shouldCreatePayment() {
    Order order = new Order(1L, 100.0);
    Payment expectedPayment = new Payment(order);
    when(paymentService.createPayment(order)).thenReturn(expectedPayment);
    
    orderService.processOrder(order);
    
    verify(paymentService).createPayment(order);
}
```

**Rule of thumb:** Mock external dependencies, use real objects for domain logic.

### 3. Testing Framework Code
**Problem:** Testing library or framework functionality instead of your code.

```java
// ❌ BAD: Testing Spring's @Autowired
@Test
void contextLoads() {
    assertNotNull(userService);
    assertNotNull(userRepository);
}

// ❌ BAD: Testing JPA basic operations
@Test
void shouldSaveUser() {
    User user = new User("John");
    userRepository.save(user);
    assertTrue(userRepository.findById(user.getId()).isPresent());
}

// ✅ GOOD: Test your business logic
@Test
void shouldNotAllowDuplicateUsernames() {
    userService.createUser("john", "john@example.com");
    
    assertThrows(DuplicateUsernameException.class, 
        () -> userService.createUser("john", "different@example.com"));
}
```

### 4. Assertion Roulette
**Problem:** Multiple assertions without clear messages make failures hard to debug.

```java
// ❌ BAD: Unclear which assertion failed
@Test
void testUser() {
    User user = userService.getUser(1L);
    assertEquals("John", user.getName());
    assertEquals("john@example.com", user.getEmail());
    assertTrue(user.isActive());
    assertNotNull(user.getCreatedAt());
}

// ✅ GOOD: Clear messages or separate tests
@Test
void shouldReturnUserWithCorrectName() {
    User user = userService.getUser(1L);
    assertEquals("John", user.getName(), "User name should be John");
}

@Test
void shouldReturnActiveUser() {
    User user = userService.getUser(1L);
    assertTrue(user.isActive(), "User should be active");
}
```

### 5. Hidden Dependencies
**Problem:** Tests depend on external state, making them flaky.

```java
// ❌ BAD: Hidden dependency on system time
@Test
void shouldCreateOrderWithCurrentDate() {
    Order order = orderService.createOrder();
    assertEquals(LocalDate.now(), order.getDate());
}

// ✅ GOOD: Inject time dependency
@Test
void shouldCreateOrderWithProvidedDate() {
    Clock clock = Clock.fixed(Instant.parse("2024-01-01T00:00:00Z"), ZoneId.of("UTC"));
    OrderService service = new OrderService(clock);
    
    Order order = service.createOrder();
    assertEquals(LocalDate.of(2024, 1, 1), order.getDate());
}
```

### 6. Test Interdependence
**Problem:** Tests depend on execution order or shared state.

```java
// ❌ BAD: Tests depend on each other
private static User savedUser;

@Test
void test1_createUser() {
    savedUser = userService.createUser("John");
    assertNotNull(savedUser.getId());
}

@Test
void test2_updateUser() {
    savedUser.setName("Jane");
    userService.updateUser(savedUser);
    assertEquals("Jane", savedUser.getName());
}

// ✅ GOOD: Each test is independent
@Test
void shouldCreateUser() {
    User user = userService.createUser("John");
    assertNotNull(user.getId());
}

@Test
void shouldUpdateUser() {
    User user = userService.createUser("John");
    user.setName("Jane");
    userService.updateUser(user);
    
    User updated = userService.getUser(user.getId());
    assertEquals("Jane", updated.getName());
}
```

## Integration Testing Anti-Patterns

### 7. Not Isolating Tests
**Problem:** Tests share database state causing random failures.

```java
// ❌ BAD: Shared database state
@Test
void shouldFindAllUsers() {
    List<User> users = userRepository.findAll();
    assertEquals(5, users.size()); // Fails if other tests add users
}

// ✅ GOOD: Clean state before/after
@BeforeEach
void setUp() {
    userRepository.deleteAll();
}

@Test
void shouldFindAllUsers() {
    userRepository.saveAll(List.of(
        new User("John"),
        new User("Jane")
    ));
    
    List<User> users = userRepository.findAll();
    assertEquals(2, users.size());
}
```

### 8. Testing Through the UI/API for Everything
**Problem:** Slow, brittle tests that are hard to maintain.

```java
// ❌ BAD: Testing business logic through REST API
@Test
void shouldCalculateComplexDiscount() {
    // Create user via API
    restTemplate.postForEntity("/users", userDto, User.class);
    // Create order via API
    restTemplate.postForEntity("/orders", orderDto, Order.class);
    // Apply discount via API
    ResponseEntity<Order> response = 
        restTemplate.postForEntity("/orders/1/discount", null, Order.class);
    
    assertEquals(90.0, response.getBody().getTotal());
}

// ✅ GOOD: Test business logic directly
@Test
void shouldCalculateComplexDiscount() {
    Order order = new Order(100.0);
    User user = new User("premium");
    
    Order result = discountService.applyDiscount(order, user);
    
    assertEquals(90.0, result.getTotal());
}
```

### 9. Ignoring Test Performance
**Problem:** Slow test suites discourage running tests frequently.

```java
// ❌ BAD: Full database setup for every test
@BeforeEach
void setUp() {
    // Loads 10,000 records
    databaseSeeder.seedCompleteDatabase();
}

@Test
void shouldFindUserById() {
    User user = userRepository.findById(1L).get();
    assertNotNull(user);
}

// ✅ GOOD: Minimal setup
@BeforeEach
void setUp() {
    userRepository.deleteAll();
}

@Test
void shouldFindUserById() {
    User saved = userRepository.save(new User("John"));
    User found = userRepository.findById(saved.getId()).get();
    assertNotNull(found);
}
```

### 10. Not Testing Error Paths
**Problem:** Only testing happy paths leaves bugs undiscovered.

```java
// ❌ BAD: Only happy path
@Test
void shouldTransferMoney() {
    accountService.transfer(account1, account2, 100.0);
    assertEquals(900.0, account1.getBalance());
    assertEquals(1100.0, account2.getBalance());
}

// ✅ GOOD: Test edge cases and errors
@Test
void shouldFailTransferWithInsufficientFunds() {
    Account account = new Account(50.0);
    
    assertThrows(InsufficientFundsException.class,
        () -> accountService.transfer(account, account2, 100.0));
}

@Test
void shouldFailTransferToNonexistentAccount() {
    assertThrows(AccountNotFoundException.class,
        () -> accountService.transfer(account1, null, 100.0));
}
```

## Database Testing Anti-Patterns

### 11. Using Production Database for Tests
**Problem:** Tests can corrupt production data or fail due to production state.

```yaml
# ❌ BAD: application-test.yml
spring:
  datasource:
    url: jdbc:postgresql://prod-db:5432/production

# ✅ GOOD: Use test database
spring:
  datasource:
    url: jdbc:h2:mem:testdb
  # Or use Testcontainers for real database
```

### 12. Not Cleaning Up Test Data
**Problem:** Tests fail randomly due to leftover data.

```java
// ❌ BAD: Leaves data behind
@Test
void shouldCreateUser() {
    User user = new User("john@example.com");
    userRepository.save(user);
    assertNotNull(user.getId());
}

// ✅ GOOD: Clean up after test
@AfterEach
void tearDown() {
    userRepository.deleteAll();
}

// ✅ BETTER: Use transactional tests
@Transactional
@Test
void shouldCreateUser() {
    User user = new User("john@example.com");
    userRepository.save(user);
    assertNotNull(user.getId());
} // Automatically rolled back
```

### 13. Hardcoded Test Data
**Problem:** Makes tests fragile and hard to understand.

```java
// ❌ BAD: Magic numbers and strings
@Test
void shouldCalculateTotal() {
    Order order = orderService.getOrder(42L);
    assertEquals(123.45, order.getTotal());
}

// ✅ GOOD: Clear test data
@Test
void shouldCalculateTotal() {
    Product product = new Product("Widget", 10.0);
    Order order = new Order();
    order.addItem(product, quantity: 5);
    
    double total = order.calculateTotal();
    
    assertEquals(50.0, total);
}
```

## Async and Concurrency Anti-Patterns

### 14. Thread.sleep() in Tests
**Problem:** Makes tests slow and still flaky.

```java
// ❌ BAD: Arbitrary sleep
@Test
void shouldProcessAsync() throws InterruptedException {
    asyncService.processOrder(order);
    Thread.sleep(5000); // Hope it's done by now
    assertTrue(order.isProcessed());
}

// ✅ GOOD: Use proper waiting mechanisms
@Test
void shouldProcessAsync() {
    asyncService.processOrder(order);
    
    await().atMost(5, SECONDS)
           .until(() -> order.isProcessed());
}
```

### 15. Not Testing Concurrent Scenarios
**Problem:** Race conditions only appear in production.

```java
// ❌ BAD: Only single-threaded tests
@Test
void shouldIncrementCounter() {
    counter.increment();
    assertEquals(1, counter.getValue());
}

// ✅ GOOD: Test concurrent access
@Test
void shouldHandleConcurrentIncrements() throws InterruptedException {
    int threadCount = 10;
    int incrementsPerThread = 100;
    CountDownLatch latch = new CountDownLatch(threadCount);
    
    ExecutorService executor = Executors.newFixedThreadPool(threadCount);
    
    for (int i = 0; i < threadCount; i++) {
        executor.submit(() -> {
            for (int j = 0; j < incrementsPerThread; j++) {
                counter.increment();
            }
            latch.countDown();
        });
    }
    
    latch.await();
    assertEquals(1000, counter.getValue());
}
```

## API/REST Testing Anti-Patterns

### 16. Not Testing HTTP Status Codes
**Problem:** Missing validation of proper API responses.

```java
// ❌ BAD: Only testing success
@Test
void shouldGetUser() {
    ResponseEntity<User> response = 
        restTemplate.getForEntity("/users/1", User.class);
    assertNotNull(response.getBody());
}

// ✅ GOOD: Verify status codes
@Test
void shouldReturn404ForNonexistentUser() {
    ResponseEntity<User> response = 
        restTemplate.getForEntity("/users/999", User.class);
    assertEquals(HttpStatus.NOT_FOUND, response.getStatusCode());
}

@Test
void shouldReturn400ForInvalidInput() {
    User invalidUser = new User(); // Missing required fields
    
    assertThrows(HttpClientErrorException.BadRequest.class,
        () -> restTemplate.postForEntity("/users", invalidUser, User.class));
}
```

### 17. Ignoring Response Headers
**Problem:** Missing validation of caching, content-type, etc.

```java
// ❌ BAD: Only checking body
@Test
void shouldReturnUser() {
    User user = restTemplate.getForObject("/users/1", User.class);
    assertEquals("John", user.getName());
}

// ✅ GOOD: Verify headers
@Test
void shouldReturnUserWithProperHeaders() {
    ResponseEntity<User> response = 
        restTemplate.getForEntity("/users/1", User.class);
    
    assertEquals(HttpStatus.OK, response.getStatusCode());
    assertEquals(MediaType.APPLICATION_JSON, response.getHeaders().getContentType());
    assertTrue(response.getHeaders().getCacheControl().contains("max-age"));
}
```

## General Anti-Patterns

### 18. Skipping Tests
**Problem:** Commented or @Disabled tests mask technical debt.

```java
// ❌ BAD: Disabled test
@Disabled("This test is flaky, will fix later")
@Test
void shouldProcessLargeFile() {
    // Test code
}

// ✅ GOOD: Fix or remove
@Test
void shouldProcessLargeFile() {
    // Properly implemented test
    // Or delete if no longer relevant
}
```

### 19. Not Following AAA Pattern
**Problem:** Unclear test structure makes maintenance difficult.

```java
// ❌ BAD: Mixed concerns
@Test
void testUser() {
    User user = new User("John");
    userRepository.save(user);
    User found = userRepository.findById(user.getId()).get();
    assertEquals("John", found.getName());
    user.setName("Jane");
    userRepository.save(user);
    User updated = userRepository.findById(user.getId()).get();
    assertEquals("Jane", updated.getName());
}

// ✅ GOOD: Clear Arrange-Act-Assert
@Test
void shouldUpdateUserName() {
    // Arrange
    User user = userRepository.save(new User("John"));
    
    // Act
    user.setName("Jane");
    userRepository.save(user);
    
    // Assert
    User updated = userRepository.findById(user.getId()).get();
    assertEquals("Jane", updated.getName());
}
```

### 20. Ignoring Test Code Quality
**Problem:** Treating test code as second-class code.

```java
// ❌ BAD: Duplicated setup, no helper methods
@Test
void test1() {
    User user = new User();
    user.setName("John");
    user.setEmail("john@example.com");
    user.setActive(true);
    // ... test logic
}

@Test
void test2() {
    User user = new User();
    user.setName("Jane");
    user.setEmail("jane@example.com");
    user.setActive(true);
    // ... test logic
}

// ✅ GOOD: Reusable builders/factories
@Test
void shouldCreateActiveUser() {
    User user = UserBuilder.aUser()
                    .withName("John")
                    .withEmail("john@example.com")
                    .active()
                    .build();
    // ... test logic
}
```

## Best Practices Summary

1. **Test behavior, not implementation**
2. **Keep tests independent and isolated**
3. **Use meaningful test names** (shouldDoSomethingWhenCondition)
4. **Follow the AAA pattern** (Arrange-Act-Assert)
5. **Mock external dependencies, use real objects internally**
6. **Test edge cases and error conditions**
7. **Keep tests fast** (unit tests < 100ms, integration tests < 5s)
8. **Make tests deterministic** (no random data, fixed time)
9. **Write tests as documentation** (clear and readable)
10. **Maintain test code quality** (refactor, remove duplication)

## Testing Pyramid

```
        /\
       /E2E\      Few, slow, expensive
      /------\
     /  API   \    More, medium speed
    /----------\
   / Unit Tests \  Many, fast, cheap
  /--------------\
```

**Golden Rule:** Most tests should be fast unit tests, fewer integration tests, and minimal end-to-end tests.