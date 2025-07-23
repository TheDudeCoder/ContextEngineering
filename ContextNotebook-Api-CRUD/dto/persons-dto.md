# PersonsDto

`PersonsDto` is a Data Transfer Object used to transfer data between the API and client. It represents a simplified, flattened version of the `Persons` entity and includes references to related entities like `Addresses` and `Contacts`.

## Responsibilities

- Act as a contract between the API and client
- Contain only the data necessary for Create, Read, Update, and Delete operations
- Avoid exposing any database-specific or internal infrastructure details

## Implementation

```csharp
namespace MindWorx.Web.Client.Models
{
    public class PersonsDto
    {
        public int Id { get; set; }
        public string Name { get; set; }
        public DateTime Date { get; set; }

        // Navigation properties for referencing tables
        public virtual ICollection<AddressesDto>? AddressesCollection { get; set; }
        public virtual ICollection<ContactsDto>? ContactsCollection { get; set; }
    }
}
```

## Notes
- Navigation properties use DTO versions (AddressesDto, ContactsDto) for full API round-tripping
- DTOs allow mapping logic to transform between domain models and API contracts
- Nullable collections (?) support scenarios where child entities are excluded from results