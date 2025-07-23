# Controller Pattern

This document describes the standard pattern for implementing controllers in the `MindWorx` API solution using the `PersonsController` as the example.

## Purpose

Controllers are responsible for:
- Routing HTTP requests to appropriate actions
- Validating route and query parameters
- Returning HTTP-compliant responses (status codes and payloads)
- Delegating logic to services (no business logic in controllers)

## Summary
Controllers are responsible for HTTP request handling, while business logic resides in the service layer. This separation ensures modular, testable, and reusable application components.

## Characteristics

- **Thin Controllers**: Controllers do not contain business logic. All logic is offloaded to services.
- **Structured Responses**: Returns HTTP 200, 204, 400, 404, or 500 based on context.
- **Validation**: Controllers perform only light validation (e.g., ID matching, paging rules). DTO-level validation is handled via FluentValidation in the service layer.
- **Dependency Injection**: All services are injected via constructor.

## Guidelines

- Each controller should represent a distinct entity or bounded context
- Use `[ApiController]` and `[Route("api/[controller]")]` for routing
- Inject services via constructor injection
- Avoid complex logic; all business processing should occur in the service layer
- Return appropriate HTTP status codes:
  - `200 OK` for success
  - `204 No Content` for successful updates/deletes with no body
  - `400 Bad Request` for client input errors
  - `404 Not Found` for missing resources
  - `500 Internal Server Error` for unhandled failures
  
## Implementation Pattern

Controllers should:

- Inherit from `BaseController` for shared cross-cutting concerns
- Define route using `[Route("api/[controller]")]`
- Use `[HttpGet]`, `[HttpPost]`, `[HttpPut]`, and `[HttpDelete]` to mark actions
- Handle input validation and error handling where appropriate
- Return appropriate `IActionResult` responses

## Sample Template from PersonsController

```csharp
public class PersonsController : BaseController
{
	private readonly IPersonsService _personsService;

	public PersonsController(IPersonsService personsService)
	{
		_personsService = personsService;
	}

	[HttpGet("{Id}")]
	public async Task<IActionResult> FindAsync([FromRoute] int Id)
	{
		var result = await _personsService.FindAsync(Id);
		if (result == null)
		{
			return NotFound();
		}
		return Ok(result);
	}

	[HttpGet("{Id}/details")]
	public async Task<IActionResult> FindWithDetailsAsync([FromRoute] int Id)
	{
		var result = await _personsService.FindWithDetailsAsync(Id);
		if (result == null)
		{
			return NotFound();
		}
		return Ok(result);
	}

	[HttpGet]
	public async Task<IActionResult> GetAsync([FromQuery] int? pageIndex = null, [FromQuery] int? pageSize = null)
	{
		if (pageIndex < 0 || pageSize <= 0) 
		{
			return BadRequest("Invalid pagination parameters. PageIndex must be 0 or greater, and PageSize must be greater than 0.");
		}

		var results = await _personsService.GetAsync(pageIndex, pageSize);
		return Ok(results);
	}

	[HttpPost]
	public async Task<IActionResult> CreateAsync([FromBody] PersonsDto persons)
	{
		var primaryKeyValue = await _personsService.CreateAsync(persons);
		if (primaryKeyValue == null || primaryKeyValue.Equals(default(int)))
		{
			return StatusCode(500, "An error occurred while creating the entity.");
		}

		return Ok(new { Id = primaryKeyValue });
	}

	[HttpPut("{Id}")]
	public async Task<IActionResult> UpdateAsync([FromRoute] int Id, [FromBody] PersonsDto persons)
	{
		if (Id != persons.Id)
		{
			return BadRequest($"Id is required and must equal the object Id.");
		}

		await _personsService.UpdateAsync(persons);
		return NoContent();
	}

	[HttpDelete("{Id}")]
	public async Task<IActionResult> DeleteAsync([FromRoute] int Id)
	{
		var success = await _personsService.DeleteAsync(Id);
		if (!success)
		{
			return NotFound();
		}
		return NoContent();
	}
}
```