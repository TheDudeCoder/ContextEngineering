# PersonsValidator

The `PersonsValidator` is a FluentValidation class responsible for enforcing business rules and data integrity when working with the `PersonsDto`.

## Responsibilities

- Ensure required fields are provided
- Enforce data integrity rules such as non-empty name and valid dates
- Return clear and consistent error messages

## Implementation

```csharp
using FluentValidation;
using MindWorx.Web.Client.Models;

namespace MindWorx.Web.BusinessLayer.Validation
{
    public class PersonsValidator : AbstractValidator<PersonsDto>
    {
        public PersonsValidator()
        {
            RuleFor(w => w.Name)
                .NotEmpty().WithMessage("Name is required.");

            RuleFor(w => w.Date)
                .NotEmpty().WithMessage("Date is required.");
        }
    }
}
```

## Notes
FluentValidation enables decoupling of validation logic from controllers and services
Rules can be reused across controller or API integration layers
Validators can be automatically wired into ASP.NET Core using AddFluentValidation
