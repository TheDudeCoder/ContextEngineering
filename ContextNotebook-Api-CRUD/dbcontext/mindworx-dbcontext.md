# MindWorxDbContext

The `MindWorxDbContext` is an EF Core DbContext that configures access to all domain entities, including `Persons`, `Addresses`, and `Contacts`. It defines entity relationships and is used by repositories to persist data.

## Responsibilities

- Expose `DbSet<TEntity>` for all entity models
- Configure entity relationships using Fluent API
- Serve as the unit of work for repository operations

## Implementation

```csharp
using Microsoft.EntityFrameworkCore;
using MindWorx.Web.DataLayer.Entities;

namespace MindWorx.Web.DataLayer.Repositories
{
    public class MindWorxDbContext : DbContext
    {
        public MindWorxDbContext(DbContextOptions<MindWorxDbContext> options) : base(options) { }

        public DbSet<AddressTypes> AddressTypes { get; set; }
        public DbSet<ContactTypes> ContactTypes { get; set; }
        public DbSet<Persons> Persons { get; set; }
        public DbSet<Addresses> Addresses { get; set; }
        public DbSet<Contacts> Contacts { get; set; }
        public DbSet<ApiActivityLogs> ApiActivityLogs { get; set; }

        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder.Entity<AddressTypes>(entity =>
            {
                entity.HasKey(e => e.Id);
            });

            modelBuilder.Entity<ContactTypes>(entity =>
            {
                entity.HasKey(e => e.Id);
            });

            modelBuilder.Entity<Persons>(entity =>
            {
                entity.HasKey(e => e.Id);
            });

            modelBuilder.Entity<Addresses>(entity =>
            {
                entity.HasKey(e => e.Id);
                entity.HasOne(e => e.AddressTypes)
                      .WithMany(p => p.AddressesCollection)
                      .HasForeignKey(e => e.AddressTypeId);

                entity.HasOne(e => e.Persons)
                      .WithMany(p => p.AddressesCollection)
                      .HasForeignKey(e => e.PersonId);
            });

            modelBuilder.Entity<Contacts>(entity =>
            {
                entity.HasKey(e => e.Id);
                entity.HasOne(e => e.ContactTypes)
                      .WithMany(p => p.ContactsCollection)
                      .HasForeignKey(e => e.ContactTypeId);

                entity.HasOne(e => e.Persons)
                      .WithMany(p => p.ContactsCollection)
                      .HasForeignKey(e => e.PersonId);
            });

            modelBuilder.Entity<ApiActivityLogs>(entity =>
            {
                entity.HasNoKey();
            });
        }
    }
}
```

## Notes
DbContextOptions<T> allows injection of configuration at runtime
Relationship setup uses Fluent API rather than attributes
Foreign keys are explicitly mapped to ensure relational integrity