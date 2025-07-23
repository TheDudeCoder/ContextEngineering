# Full CRUD Example: Persons

This file provides the complete implementation of all layers involved in the `Persons` CRUD functionality. This includes the controller, service, repository, entity, DTO, mapping, validation, and EF Core context setup.

## Summary of Components

| Layer         | Type                     | File                         |
|---------------|--------------------------|------------------------------|
| Controller    | `PersonsController`      | `PersonsController.cs`       |
| Service       | `PersonsService`         | `PersonsService.cs`          |
| Repository    | `PersonsRepository`      | `PersonsRepository.cs`       |
| DTO           | `PersonsDto`             | `PersonsDto.cs`              |
| Entity        | `Persons`                | `Persons.cs`                 |
| Mapping       | `PersonsMapping`         | `PersonsMapping.cs`          |
| Validation    | `PersonsValidator`       | `PersonsValidator.cs`        |
| DbContext     | `MindWorxDbContext`      | `MindWorxDbContext.cs`       |

## Flow

1. A request is made to `/api/persons`
2. `PersonsController` receives and forwards it to `PersonsService`
3. The service logs the action, applies mapping and validation
4. `PersonsRepository` executes database actions using `BaseRepository`
5. EF Core persists/retrieves data via `MindWorxDbContext`
6. Data is returned, mapped back to DTO, and returned to the client

## Use Case Support

- Pagination via `GetAsync`
- Eager loading via `FindWithDetailsAsync`
- Clean, testable separation between layers
- Full extensibility via base and contract patterns

Each component is modular and reusable, making it easy to copy this pattern for other entities.
