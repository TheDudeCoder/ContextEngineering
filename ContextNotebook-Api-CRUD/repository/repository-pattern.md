# Repository Pattern

This document explains the usage of the Repository pattern in the MindWorx API solution. Each entity has a repository that inherits from a shared base repository to encapsulate data access concerns.

## Design Goals

- Decouple business logic from data persistence
- Abstract database access behind a consistent API
- Enable unit testing of services via mocking interfaces
- Share common logic through a base class

## Structure

| Layer | Example |
|-------|---------|
| Interface | `IPersonsRepository` |
| Concrete Class | `PersonsRepository` |
| Base Class | `BaseRepository<Persons, int, MindWorxDbContext>` |
| DbContext | `MindWorxDbContext` |

## Guidelines

- Define one repository per root entity (e.g., `Persons`, `Addresses`)
- Always use interfaces (e.g., `IPersonsRepository`) to support dependency injection
- Override methods only when default behavior from `BaseRepository` is insufficient
- Use eager loading (`Include`) only when needed to avoid performance hits

## Benefits

- Promotes separation of concerns between business and data layers
- Reduces boilerplate via `BaseRepository`
- Encourages consistent access patterns for developers
- Simplifies mocking for unit testing

## Example Usage in Service Layer

```csharp
var entity = await _personsRepository.FindAsync(id);
await _personsRepository.CreateAsync(newEntity);
var result = await _personsRepository.GetAsync(null, null, 1, 25);
```

## Summary
The repository pattern creates a clean boundary between business logic and the database. The base repository eliminates repetition, while concrete repositories allow for custom logic as needed.