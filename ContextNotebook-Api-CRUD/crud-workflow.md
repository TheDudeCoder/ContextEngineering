# CRUD Workflow

This document outlines the complete workflow for a standard Create, Read, Update, and Delete (CRUD) operation within the MindWorx API architecture using the `Persons` entity as an example.

## Workflow Layers

Each API request moves through the following layers:

### 1. Controller (API Layer)

- Accepts HTTP requests
- Performs parameter validation (e.g., route ID match)
- Delegates execution to the business service
- Returns appropriate HTTP responses

### 2. Service (Business Layer)

- Contains core business logic
- Applies logging using `ILogger<T>`
- Calls FluentValidation (if integrated)
- Uses mapping to convert DTO → Entity and Entity → DTO
- Delegates persistence to repository layer

### 3. Repository (Data Layer)

- Inherits from `BaseRepository<TEntity, TKey, TContext>`
- Executes actual EF Core data operations
- Optional: extends with custom data access logic

### 4. DbContext (EF Core Layer)

- Implements `DbSet<TEntity>` for each entity
- Configures table and relationship mapping using Fluent API in `OnModelCreating`

---

## Example Flow – Creating a Person

### Step 1: HTTP POST `/api/persons`

- **Input:** JSON body mapped to `PersonsDto`
- **Validation:** `PersonsValidator` ensures required fields are populated

### Step 2: `PersonsController.CreateAsync(dto)`

- Validates request
- Calls `_personsService.CreateAsync(dto)`

### Step 3: `PersonsService.CreateAsync(dto)`

- Logs the intent
- Maps `PersonsDto` → `Persons` entity
- Calls `_personsRepository.CreateAsync(entity)`

### Step 4: `PersonsRepository.CreateAsync(entity)`

- Calls `BaseRepository.CreateAsync(entity)` which:
  - Adds entity to `DbSet`
  - Saves changes using EF Core

### Step 5: Response

- The newly generated `Id` is returned to the controller
- The controller returns `200 OK` with `{ Id = <newId> }`

---

## Summary

This workflow cleanly separates responsibilities:

- Controllers = HTTP logic
- Services = Application logic
- Repositories = Data logic
- Mappers/Validators = Transformation & enforcement

This pattern promotes reusability, testability, and consistency across your API.
