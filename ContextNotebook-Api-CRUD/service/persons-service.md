# PersonsService

The `PersonsService` class implements the `IPersonsService` interface and contains all business logic for CRUD operations related to the `Persons` entity. It uses `IPersonsRepository` for persistence, maps between entities and DTOs, and includes structured logging for observability.

## Responsibilities

- Log all CRUD operations with structured metadata
- Map `PersonsDto` to `Persons` entities and vice versa
- Call repository methods to retrieve and persist data
- Handle pagination
- Perform minimal checks (e.g., null results)
- Delegate input validation to FluentValidation

## Interface

```csharp
public interface IPersonsService
{
    Task<PersonsDto> FindAsync(int id);
    Task<PersonsDto> FindWithDetailsAsync(int id);
    Task<IEnumerable<PersonsDto>> GetAsync(int? pageIndex = null, int? pageSize = null);
    Task<int> CreateAsync(PersonsDto personsDto);
    Task UpdateAsync(PersonsDto personsDto);
    Task<bool> DeleteAsync(int id);
}
```

## Implementation
```csharp
public class PersonsService : IPersonsService
{
    private readonly IPersonsRepository _personsRepository;
    private readonly ILogger<PersonsService> _logger;

    public PersonsService(IPersonsRepository personsRepository, ILogger<PersonsService> logger)
    {
        _personsRepository = personsRepository;
        _logger = logger;
    }

    public async Task<PersonsDto> FindAsync(int Id)
    {
        try
        {
            _logger.LogInformation("Finding Persons by Id: {pkValue}", Id);
            var result = await _personsRepository.FindAsync(Id);
            if (result == null)
            {
                _logger.LogWarning("No Persons found with Id: {pkValue}", Id);
                return null;
            }
            return result.ToDto();
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "An error occurred in {methodName}. Parameters: Id: {pkValue}", nameof(FindAsync), Id);
            throw;
        }
    }

    public async Task<PersonsDto> FindWithDetailsAsync(int Id)
    {
        try
        {
            _logger.LogInformation("Finding Persons with details by Id: {pkValue}", Id);
            var result = await _personsRepository.FindWithDetailsAsync(Id, "Id",
                addressesCollection => addressesCollection.AddressesCollection,
                contactsCollection => contactsCollection.ContactsCollection);

            if (result == null)
            {
                _logger.LogWarning("No Persons with details found with Id: {pkValue}", Id);
                return null;
            }
            return result.ToDto();
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "An error occurred in {methodName}. Parameters: Id: {pkValue}", nameof(FindWithDetailsAsync), Id);
            throw;
        }
    }

    public async Task<IEnumerable<PersonsDto>> GetAsync(int? pageIndex = null, int? pageSize = null)
    {
        try
        {
            _logger.LogInformation("Fetching Persons list with pagination. PageIndex: {pageIndex}, PageSize: {pageSize}", pageIndex, pageSize);
            var results = await _personsRepository.GetAsync(null, null, pageIndex, pageSize);
            if (results == null || !results.Any())
            {
                _logger.LogWarning("No Persons entities found.");
                return new List<PersonsDto>();
            }
            return results.ToList().ToDto();
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "An error occurred in {methodName}. Parameters: PageIndex: {pageIndex}, PageSize: {pageSize}", nameof(GetAsync), pageIndex, pageSize);
            throw;
        }
    }

    public async Task<int> CreateAsync(PersonsDto personsDto)
    {
        try
        {
            _logger.LogInformation("Creating new Persons. Data: {@dto}", personsDto);
            var entity = personsDto.ToEntity();
            await _personsRepository.CreateAsync(entity);

            _logger.LogInformation("Persons created with Id: {pkValue}", entity.Id);
            return entity.Id;
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "An error occurred in {methodName}. Data: {@dto}", nameof(CreateAsync), personsDto);
            throw;
        }
    }

    public async Task UpdateAsync(PersonsDto personsDto)
    {
        try
        {
            _logger.LogInformation("Updating Persons. Data: {@dto}", personsDto);
            var entity = personsDto.ToEntity();
            await _personsRepository.UpdateAsync(entity);
            _logger.LogInformation("Persons updated.");
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "An error occurred in {methodName}. Data: {@dto}", nameof(UpdateAsync), personsDto);
            throw;
        }
    }

    public async Task<bool> DeleteAsync(int Id)
    {
        try
        {
            _logger.LogInformation("Deleting Persons with Id: {pkValue}", Id);
            var success = await _personsRepository.DeleteAsync(Id);
            if (success)
            {
                _logger.LogInformation("Persons with Id: {pkValue} deleted.", Id);
            }
            else
            {
                _logger.LogWarning("Failed to delete Persons with Id: {pkValue}", Id);
            }
            return success;
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "An error occurred in {methodName}. Parameters: Id: {pkValue}", nameof(DeleteAsync), Id);
            throw;
        }
    }
}
```

## Notes
Uses ILogger<T> to log at each step
Converts between DTO and Entity using mapping extensions
Makes use of optional pagination
Does not include inline validation logic; assumes FluentValidation is used elsewhere