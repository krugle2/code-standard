# Coding Standards and Best Practices

## Version 1.0
Last Updated: 2025-01-28

## 1. Code Quality Requirements

### 1.1 Error Handling
- **REQUIRED**: All methods must include proper error handling
- Use try-catch blocks for operations that may fail
- Never use empty catch blocks - always log errors
- Provide meaningful error messages to help debugging

**Example:**
```java
try {
    processFile(filename);
} catch (IOException e) {
    logger.error("Failed to process file: " + filename, e);
    throw new ProcessingException("Unable to process file", e);
}
```

### 1.2 Logging
- **REQUIRED**: Use appropriate logging levels
  - ERROR: System errors that require immediate attention
  - WARN: Unexpected situations that don't stop execution
  - INFO: Important business process information
  - DEBUG: Detailed information for troubleshooting
- Include context in log messages (file names, user IDs, transaction IDs)
- Never log sensitive information (passwords, credit cards, PII)

### 1.3 Input Validation
- **REQUIRED**: Validate all external input
- Check for null values before processing
- Validate data types, ranges, and formats
- Sanitize user input to prevent injection attacks
- Return clear validation error messages

## 2. Security Requirements

### 2.1 SQL Injection Prevention
- **REQUIRED**: Use parameterized queries or prepared statements
- **PROHIBITED**: String concatenation for SQL queries
- Never trust user input in database queries

**Bad:**
```java
String query = "SELECT * FROM users WHERE id = " + userId;
```

**Good:**
```java
PreparedStatement stmt = conn.prepareStatement("SELECT * FROM users WHERE id = ?");
stmt.setInt(1, userId);
```

### 2.2 XSS Prevention
- Escape all user-supplied data before rendering in HTML
- Use framework-provided escaping functions
- Validate and sanitize input on both client and server side

### 2.3 Authentication & Authorization
- Always verify user authentication before processing requests
- Check authorization for each protected resource access
- Use secure session management
- Implement proper password hashing (bcrypt, Argon2)

### 2.4 Sensitive Data
- **PROHIBITED**: Hardcoding credentials, API keys, or secrets
- Use environment variables or secure configuration management
- Encrypt sensitive data at rest and in transit
- Never log passwords or authentication tokens

## 3. Code Organization

### 3.1 Naming Conventions
- **Classes**: PascalCase (e.g., `UserAccountManager`)
- **Methods**: camelCase (e.g., `calculateTotalAmount`)
- **Constants**: UPPER_SNAKE_CASE (e.g., `MAX_RETRY_COUNT`)
- **Variables**: camelCase, descriptive names (e.g., `customerEmail`)

### 3.2 Method Design
- **Single Responsibility**: Each method should do one thing
- **Keep methods short**: Ideally under 50 lines
- **Meaningful names**: Method name should clearly indicate its purpose
- **Limit parameters**: Maximum 4-5 parameters, use objects for more

### 3.3 Comments
- Write self-documenting code with clear variable and method names
- Add comments for complex algorithms or non-obvious logic
- Keep comments up-to-date with code changes
- Use JavaDoc/JSDoc for public APIs

**Example:**
```java
/**
 * Calculates the total price including taxes and discounts.
 *
 * @param basePrice the original price before any modifications
 * @param taxRate the applicable tax rate (e.g., 0.08 for 8%)
 * @param discountPercent the discount percentage (0-100)
 * @return the final calculated price
 * @throws IllegalArgumentException if any parameter is negative
 */
public double calculateFinalPrice(double basePrice, double taxRate, double discountPercent) {
    // Implementation
}
```

## 4. Performance Guidelines

### 4.1 Database Access
- Use connection pooling
- Avoid N+1 query problems - use batch queries or joins
- Create appropriate indexes for frequently queried columns
- Close database resources in finally blocks or use try-with-resources

### 4.2 Resource Management
- Always close streams, connections, and file handles
- Use try-with-resources for AutoCloseable resources
- Avoid memory leaks by clearing collections when done

### 4.3 Caching
- Cache frequently accessed, rarely changing data
- Implement cache expiration strategies
- Consider cache invalidation on data updates

## 5. Testing Requirements

### 5.1 Unit Tests
- **REQUIRED**: Write unit tests for all business logic
- Aim for at least 80% code coverage
- Test edge cases and error conditions
- Use meaningful test names that describe the scenario

**Example:**
```java
@Test
public void shouldThrowExceptionWhenUserNotFound() {
    // Given
    String nonExistentUserId = "invalid-123";

    // When & Then
    assertThrows(UserNotFoundException.class, () -> {
        userService.getUser(nonExistentUserId);
    });
}
```

### 5.2 Integration Tests
- Test integration points between components
- Use test databases or mocks for external dependencies
- Clean up test data after each test

### 5.3 Test Data
- Use realistic but anonymized test data
- Never use production data in tests
- Create reusable test fixtures

## 6. Version Control

### 6.1 Commit Messages
- Use clear, descriptive commit messages
- Start with a verb (Add, Fix, Update, Remove)
- Include ticket/issue number if applicable

**Format:**
```
[TICKET-123] Add user email validation

- Implemented email format validation
- Added unit tests for edge cases
- Updated error messages
```

### 6.2 Branching
- Create feature branches from main/master
- Use descriptive branch names: `feature/user-authentication`, `bugfix/login-error`
- Keep branches focused on single features or fixes

### 6.3 Code Review
- All code must be reviewed before merging
- Address all review comments
- Test the code before requesting review

## 7. Documentation

### 7.1 README Files
- Every module should have a README explaining its purpose
- Include setup instructions and dependencies
- Document environment variables and configuration

### 7.2 API Documentation
- Document all public APIs with parameters and return values
- Include example requests and responses
- Keep documentation in sync with code changes

### 7.3 Architecture Documentation
- Maintain high-level architecture diagrams
- Document major design decisions
- Explain integration points and dependencies

## 8. Code Review Checklist

Before submitting code for review, verify:

- [ ] All tests pass
- [ ] Code follows naming conventions
- [ ] Error handling is implemented
- [ ] Input validation is present
- [ ] No hardcoded credentials or secrets
- [ ] Logging is appropriate and doesn't expose sensitive data
- [ ] Code is properly commented where necessary
- [ ] Database resources are properly closed
- [ ] Security best practices are followed
- [ ] Unit tests are written and passing
- [ ] Code coverage meets requirements
- [ ] Documentation is updated

## 9. Common Anti-Patterns to Avoid

### 9.1 God Objects
- Avoid classes that do too many things
- Break large classes into smaller, focused components

### 9.2 Magic Numbers
- Don't use unexplained numeric literals
- Define constants with meaningful names

**Bad:**
```java
if (status == 3) { ... }
```

**Good:**
```java
private static final int STATUS_ACTIVE = 3;
if (status == STATUS_ACTIVE) { ... }
```

### 9.3 Deep Nesting
- Avoid deeply nested if statements (max 3 levels)
- Use early returns or guard clauses
- Extract complex conditions into well-named methods

### 9.4 Duplicate Code
- Follow DRY principle (Don't Repeat Yourself)
- Extract common code into reusable methods
- Use inheritance or composition appropriately

## 10. Language-Specific Guidelines

### 10.1 Java
- Use Java 11+ features (var, try-with-resources, Optional)
- Prefer immutable objects where possible
- Use appropriate collection types (List, Set, Map)
- Handle null with Optional or null checks

### 10.2 Python
- Follow PEP 8 style guide
- Use type hints for function parameters and return values
- Use virtual environments for dependencies
- Write docstrings for all public functions

### 10.3 JavaScript/TypeScript
- Use strict mode
- Prefer const/let over var
- Use async/await for asynchronous code
- Implement proper error boundaries in React

## Enforcement

These standards are mandatory for all code contributions. Code that doesn't meet these standards will not be merged until issues are resolved.

For questions or clarifications, contact the Architecture Team.

---
*This document is a living document and will be updated as our practices evolve.*
