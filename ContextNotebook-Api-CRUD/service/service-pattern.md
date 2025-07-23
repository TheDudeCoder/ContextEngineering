# Service Pattern

This document describes the pattern used for implementing service classes in the MindWorx API architecture, as demonstrated in `PersonsService`.

## Responsibilities

- Encapsulate business rules and processing logic
- Apply logging with structured metadata
- Delegate database access to the repository layer
- Coordinate mapping between DTOs and Entities
- Handle exceptions at the boundary of business logic

## Guidelines

- Services should be implemented as concrete classes with an interface (e.g., `IPersonsService`)
- Constructor inject dependencies (`ILogger<T>`, repositories)
- Use mapping extensions to convert between DTOs and entities
- Do not include infrastructure concerns such as HTTP context, request headers, or route info

## Logging

All service methods log:

- Method name
- Input parameters
- Any warnings (e.g., null results)
- Exceptions using `LogError`

## Exception Handling

Services catch and rethrow exceptions only when logging is necessary. This helps with traceability while avoiding silent failures.

## Example Usage

```csharp
_logger.LogInformation("Creating new Persons. Data: {@dto}", personsDto);
...
_logger.LogError(ex, "An error occurred in {methodName}. Data: {@dto}", nameof(CreateAsync), personsDto);
```

## Return Patterns
- FindAsync: returns null if not found
- FindWithDetailsAsync returns null if not found
- GetAsync: returns empty list if no records found
- CreateAsync: returns new primary key
- UpdateAsync: returns void on success
- DeleteAsync: returns true/false based on whether record was deleted

## Summary
Services centralize business processing logic and provide a clean interface to the controller layer. They should remain free of HTTP or infrastructure concerns and be fully testable using unit tests with mocked dependencies.
