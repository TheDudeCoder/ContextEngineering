# PersonsController

The `PersonsController` exposes a RESTful API for managing the `Persons` entity. It defines endpoints for Create, Read, Update, and Delete (CRUD) operations and delegates business logic to the `IPersonsService`.

## Responsibilities

- Receive and route HTTP requests related to `Persons`
- Perform basic parameter validation (such as ID matching)
- Return appropriate HTTP status codes
- Delegate business operations to the `IPersonsService` interface

## Implementation

```csharp
using Microsoft.AspNetCore.Mvc;
using System.Threading.Tasks;
using MindWorx.Web.BusinessLayer.Services.Contracts;
using MindWorx.Web.Client.Models;

namespace MindWorx.Web.Api.Controllers
{
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
}
```

### Notes
Relies on dependency injection for IPersonsService
Pagination validation is handled directly in the controller
Returns descriptive error messages for invalid input or system failures
BaseController may encapsulate shared controller logic (authentication, logging, etc.)