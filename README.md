# ContactManager.Common

**Inhalt:**

- Packages
- Schnittstellen
  - `IIdentifiable`
  - `IContact`
  - `ISettings`
- Modules
  - Configuration
    - `AppSettings`

## Packages

```csharp
<Project Sdk="Microsoft.NET.Sdk">

	<PropertyGroup>
		<TargetFramework>net8.0</TargetFramework>
		<ImplicitUsings>enable</ImplicitUsings>
		<Nullable>enable</Nullable>
	</PropertyGroup>

	<ItemGroup>
		<PackageReference Include="Microsoft.Extensions.Configuration" Version="8.0.0" />
		<PackageReference Include="Microsoft.Extensions.Configuration.Json" Version="8.0.1" />
		<PackageReference Include="Microsoft.Extensions.Configuration.EnvironmentVariables" Version="8.0.0" />
	</ItemGroup>

</Project>
```

## Schnittstellen

### `IIdentifiable`

```csharp
namespace ContactManager.Common.Contracts
{
    /// <summary>
    /// Represents an identifiable in the company manager.
    /// </summary>
    public interface IIdentifiable
    {
        #region Properties
        /// <summary>
        /// Gets the unique identifier for the entity.
        /// </summary>
        int Id { get; protected set; }
        #endregion Properties
    }
}
```

### `IContact`

```csharp
namespace ContactManager.Common.Contracts
{
    /// <summary>
    /// Represents an contact in the company manager.
    /// </summary>
    public interface IContact : IIdentifiable
    {
        #region Properties
        /// <summary>
        /// Gets or sets the first name of the contact.
        /// </summary>
        string FirstName { get; set; }
        /// <summary>
        /// Gets or sets the last name of the contact.
        /// </summary>
        string LastName { get; set; }
        /// <summary>
        /// Gets or sets the company name of the contact.
        /// </summary>
        string Company { get; set; }
        /// <summary>
        /// Gets or sets email of the contact.
        /// </summary>
        string Email { get; set; }
        /// <summary>
        /// Gets or sets phone number of the contact.
        /// </summary>
        string PhoneNumber { get; set; }
        /// <summary>
        /// Gets or sets address of the contact.
        /// </summary>
        string Address { get; set; }
        /// <summary>
        /// Gets or sets note of the contact.
        /// </summary>
        string Note { get; set; }
        #endregion Properties

        #region Methods
        /// <summary>
        /// Copies the properties of the specified contact to this contact.
        /// </summary>
        /// <param name="other">The contact object that is copied.</param>
        void CopyProperties(IContact other)
        {
            if (other == null) throw new ArgumentNullException(nameof(other));

            Id = other.Id;
            FirstName = other.FirstName;
            LastName = other.LastName;
            Company = other.Company;

            Email = other.Email;
            PhoneNumber = other.PhoneNumber;
            Address = other.Address;
            Note = other.Note;
        }
        #endregion Methods
    }
}
```

### `ISettings`

```csharp
namespace ContactManager.Common.Contracts
{
    public interface ISettings
    {
        string? this[string key] { get; }
    }
}
```

## Modules

### Configuration

#### `AppSettings`

```csharp
using Microsoft.Extensions.Configuration;

namespace ContactManager.Common.Modules.Configuration
{
    /// <summary>
    /// Singleton class to manage application settings.
    /// </summary>
    public sealed class AppSettings : Contracts.ISettings
    {
        #region fields
        private static AppSettings _instance = new AppSettings();
        private static IConfigurationRoot _configurationRoot;
        #endregion fields

        /// <summary>
        /// Static constructor to initialize the configuration.
        /// </summary>
        static AppSettings()
        {
            var environmentName = Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT");
            var builder = new ConfigurationBuilder()
                    .AddJsonFile("appsettings.json", optional: false, reloadOnChange: true)
                    .AddJsonFile($"appsettings.{environmentName ?? "Development"}.json", optional: true)
                    .AddEnvironmentVariables();

            _configurationRoot = builder.Build();
        }

        #region properties
        /// <summary>
        /// Gets the singleton instance of the AppSettings class.
        /// </summary>
        public static AppSettings Instance => _instance;
        #endregion properties

        /// <summary>
        /// Private constructor to prevent instantiation.
        /// </summary>
        private AppSettings()
        {
        }

        /// <summary>
        /// Indexer to get the configuration value by key.
        /// </summary>
        /// <param name="key">The configuration key.</param>
        /// <returns>The configuration value.</returns>
        public string? this[string key] => _configurationRoot[key];
    }
}
```
