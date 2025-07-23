# BaseRepository

The `BaseRepository<TEntity, TKey, TContext>` is a generic implementation of the repository pattern that abstracts EF Core logic for CRUD operations. It is designed to be inherited by entity-specific repositories like `PersonsRepository`.

## Responsibilities

- Centralize data access logic using EF Core
- Provide extensible and overrideable methods
- Handle transactions, retries, and EF lifecycle management
- Support includes, pagination, and filtering

## Key Features

- Generic type parameters for reusability
- Retry logic via Polly
- Pagination support via `pageIndex` and `pageSize`
- Includes support for eager loading via `Expression<Func<TEntity, object>>[]`
- Overridable virtual methods such as `CreateAsync`, `UpdateAsync`, `DeleteAsync`, etc.

## Typical Usage

```csharp
public class PersonsRepository : BaseRepository<Persons, int, MindWorxDbContext>, IPersonsRepository
{
    public PersonsRepository(IDbContextFactory<MindWorxDbContext> factory, ILogger<PersonsRepository> logger)
        : base(factory, logger) { }
}
```

## Methods (Selected)
- FindAsync(id) – Lookup by primary key
- FindWithDetailsAsync(id, "Id", x => x.Relation1) – Lookup with includes
- GetAsync(...) – Supports pagination, includes, filtering
- CreateAsync(entity) – Persists a new entity
- UpdateAsync(entity) – Updates existing entity
- DeleteAsync(id) – Deletes by primary key

## Notes
By centralizing CRUD logic, it ensures consistency and reduces duplication
Concrete repositories can override any method if they need custom behavior