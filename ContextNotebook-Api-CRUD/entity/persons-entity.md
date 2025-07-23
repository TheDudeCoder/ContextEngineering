# Persons Entity

The `Persons` class is an EF Core entity representing the `Persons` table in the database. It includes navigation properties for related collections such as `Addresses` and `Contacts`.

## Responsibilities

- Serve as the database model for the `Persons` table
- Define relationships to related entities using navigation properties
- Represent persisted state in the data access layer

## Implementation

```csharp
namespace MindWorx.Web.DataLayer.Entities
{
    public class Persons
    {
        public int Id { get; set; }
        public string Name { get; set; }
        public DateTime Date { get; set; }

        // Navigation properties for referencing tables
        public virtual ICollection<Addresses>? AddressesCollection { get; set; }
        public virtual ICollection<Contacts>? ContactsCollection { get; set; }
    }
}
```

## Notes
Marked as public for EF Core to use during runtime and migrations
Virtual navigation properties allow lazy loading if configured
Collections are optional (nullable) to support use cases that do not include child entities