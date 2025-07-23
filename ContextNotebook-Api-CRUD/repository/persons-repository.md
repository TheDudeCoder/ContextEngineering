# PersonsRepository

The `PersonsRepository` class is the data access layer for the `Persons` entity. It inherits from the generic `BaseRepository<TEntity, TKey, TContext>`, which provides standard CRUD functionality using EF Core.

This repository uses the `MindWorxDbContext` and implements the `IPersonsRepository` interface.

## Responsibilities

- Inherit shared data access methods from `BaseRepository`
- Provide EF Core access for the `Persons` table
- Enable override of CRUD methods if necessary
- Log repository-level actions if custom logic is added

## Implementation

```csharp
using Microsoft.EntityFrameworkCore;
using Microsoft.Extensions.Logging;
using System.Linq.Expressions;
using MindWorx.Data.EntityFramework.Repositories;
using MindWorx.Data.EntityFramework.Repositories.Contracts;
using MindWorx.Web.DataLayer.Entities;
using MindWorx.Web.DataLayer.Repositories.Contracts;

namespace MindWorx.Web.DataLayer.Repositories
{
    public class PersonsRepository : BaseRepository<Persons, int, MindWorxDbContext>, IPersonsRepository
    {
        private readonly ILogger<PersonsRepository> _logger;

        public PersonsRepository(IDbContextFactory<MindWorxDbContext> contextFactory, ILogger<PersonsRepository> logger) 
            : base(contextFactory)
        {
            _logger = logger;
        }
    }
}
```

## Notes
The base class handles the actual database interactions.
This class may be extended to add custom queries specific to Persons.
Logging is preconfigured via ILogger<PersonsRepository>.