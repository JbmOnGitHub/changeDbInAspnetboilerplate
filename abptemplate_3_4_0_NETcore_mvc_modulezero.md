Abp Template v3.4.0 / .NET Cross Platform / mvc / modulezero
============================================================

**Unofficial HOWTO:** This instruction file guides you to change the DbProvider for a specific version of the Abp Template.
If you just download a [<i class="icon-download"></i> new Abp Template][1], be careful to compare the template version with the version pointed in this file.

> **Prerequisites**
>
> - Prepare a new database for the provider you want to use.
> - launch your Visual Studio (15.4.3+) as Administrator.

Npgsql
----------

#### <i class="icon-file"></i> Prepare the solution

##### Manage Nuget in your EntityFrameworkCore project
- Uninstall the package Microsoft.EntityFrameworkCore.SqlServer
- Add Npgsql.EntityFrameworkCore.PostgreSQL (2.0.0) & Npgsql.EntityFrameworkCore.PostgreSQL.Design (1.1.1)

##### Clean other files
- Drop ALL files in the "Migrations" directory (Project "EntityFrameworkCore/Migrations")
- Change DbContextConfigurer.cs like this:
```c#
public static void Configure(DbContextOptionsBuilder<AbpChangeDbProviderDbContext> builder, string connectionString)
{
    // builder.UseSqlServer(connectionString);
    builder.UseNpgsql(connectionString);
}

public static void Configure(DbContextOptionsBuilder<AbpChangeDbProviderDbContext> builder, DbConnection connection)
{
    // builder.UseSqlServer(connection);
    builder.UseNpgsql(connection);
}
```
- Add this method in your [YourTemplateName]DbContext.cs file like this:
```c#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    foreach (var entityType in modelBuilder.Model.GetEntityTypes())
    {
        foreach (var property in entityType.GetProperties())
        {
            // max char length value in sqlserver
            if (property.GetMaxLength() == 67108864)
                // max char length value in postgresql
                property.SetMaxLength(10485760);
        }
    }
}
```
- Adapt all appsettings.json files with your ConnectionString. It could be like below:
"Database=AbpTemplateDbName;Server=127.0.0.1;Port=5432;User id=postgres;Password=aq23zscv"
- Re-build your solution

At this point, if the solution doesn't compile, maybe you have forgotten some steps...

#### <i class="icon-file"></i> Create the database
##### Important: Set the Mvc project as your starting project and open the Visual Studio's Package Manager Console with the EntityFramework project as default project
1) Execute Add-Migration "Initial" in the PMC (26/11/2017: [see this][2])
2) Execute Update-Database in the PMC

Finished! You can launch the template!

  [1]: https://aspnetboilerplate.com/Templates
  [2]: https://github.com/aspnetboilerplate/aspnetboilerplate/issues/2485
