# PersonsMapping

`PersonsMapping` provides extension methods for mapping between the `Persons` entity and `PersonsDto`. This ensures that mapping logic is centralized and reusable.

## Responsibilities

- Convert `Persons` entity to `PersonsDto` and vice versa
- Handle nested collections for `Addresses` and `Contacts`
- Provide bulk mapping for `ICollection<T>` types

## Implementation

```csharp
using System.Collections.Generic;
using System.Linq;
using MindWorx.Web.Client.Models;
using MindWorx.Web.DataLayer.Entities;

namespace MindWorx.Web.BusinessLayer.Mapping
{
    public static class PersonsMapping
    {
        public static List<PersonsDto>? ToDto(this ICollection<Persons> entities)
        {
            return entities?.Select(e => e.ToDto()).ToList();
        }

        public static PersonsDto ToDto(this Persons entity)
        {
            if (entity == null)
            {
                return null;
            }

            var dto = new PersonsDto();
            dto.Id = entity.Id;
            dto.Name = entity.Name;
            dto.Date = entity.Date;

            if (entity.AddressesCollection != null)
            {
                dto.AddressesCollection = entity.AddressesCollection.ToDto();
            }

            if (entity.ContactsCollection != null)
            {
                dto.ContactsCollection = entity.ContactsCollection.ToDto();
            }

            return dto;
        }

        public static List<Persons>? ToEntity(this ICollection<PersonsDto> dtos)
        {
            return dtos?.Select(d => d.ToEntity()).ToList();
        }

        public static Persons ToEntity(this PersonsDto dto)
        {
            if (dto == null)
            {
                return null;
            }

            var entity = new Persons();
            entity.Id = dto.Id;
            entity.Name = dto.Name;
            entity.Date = dto.Date;

            if (dto.AddressesCollection != null)
            {
                entity.AddressesCollection = dto.AddressesCollection.ToEntity();
            }

            if (dto.ContactsCollection != null)
            {
                entity.ContactsCollection = dto.ContactsCollection.ToEntity();
            }

            return entity;
        }
    }
}
```

## Notes
Keeps mapping logic decoupled from services and controllers
Easily testable in isolation
Supports recursive mapping for related collections (Addresses, Contacts)