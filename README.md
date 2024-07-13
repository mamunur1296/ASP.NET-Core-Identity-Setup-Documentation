# ASP.NET Core Identity Setup Documentation

## Step 1: Install Required Packages

Add the following package references to your `.csproj` file:

```csharp
<PackageReference Include="Microsoft.AspNetCore.Identity.EntityFrameworkCore" Version="6.0.32" />
<PackageReference Include="Microsoft.EntityFrameworkCore.SqlServer" Version="6.0.32" />
<PackageReference Include="Microsoft.EntityFrameworkCore.Tools" Version="6.0.32" />
```
## Step 2: Define the Database Context Class

Create the `ApplicationDbContext` class that inherits from `IdentityDbContext<ApplicationUser>`.

**File:** `DataContext/ApplicationDbContext.cs`

```csharp
using Microsoft.AspNetCore.Identity.EntityFrameworkCore;
using Microsoft.EntityFrameworkCore;

public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options) : base(options) { }
}
```


## Step 3: Define the ApplicationUser Class

Create the `ApplicationUser` model that inherits from `IdentityUser`

**File:** `Models/ApplicationUser.cs`

```csharp
using Microsoft.AspNetCore.Identity;

namespace IdentityPOC.Models
{
    public class ApplicationUser : IdentityUser
    {
        public string FirstName { get; set; }
        public string LastName { get; set; }
        //more ……..
    }
}

```

## Step 4: Configure ASP.NET Core Identity Services

Configure Identity services in the `Program.cs` file.

```csharp
builder.Services.AddIdentity<ApplicationUser, IdentityRole>()
    .AddEntityFrameworkStores<ApplicationDbContext>()
    .AddDefaultTokenProviders();

```

## Step 5: Configure Entity Framework Core
Set up the database context in the `Program.cs` file.

```csharp
var connectionString = builder.Configuration.GetConnectionString("dbcs") ?? throw new InvalidOperationException("Connection string 'dbcs' not found.");

```
```csharp
builder.Services.AddDbContext<ApplicationDbContext>(options =>
    options.UseSqlServer(connectionString));
```

## Step 6: Configure Identity Options
Customize Identity options in the `Program.cs` file.

```csharp
builder.Services.Configure<IdentityOptions>(options =>
{
    // Lockout settings: Configures the lockout options for the identity system.
    
    // The default amount of time a user is locked out for after MaxFailedAccessAttempts.
    options.Lockout.DefaultLockoutTimeSpan = TimeSpan.FromMinutes(5);
    
    // The number of failed access attempts allowed before a user is locked out.
    options.Lockout.MaxFailedAccessAttempts = 5;
    
    // Determines if a new user can be locked out. If false, new users will not be locked out.
    options.Lockout.AllowedForNewUsers = true;
    
    // Password settings: Configures the password policy for users.
    
    // Require a digit (0-9) in the password.
    options.Password.RequireDigit = false;
    
    // Require a lowercase letter (a-z) in the password.
    options.Password.RequireLowercase = true;
    
    // Require a non-alphanumeric character (e.g., @, !, #) in the password.
    options.Password.RequireNonAlphanumeric = false;
    
    // Require an uppercase letter (A-Z) in the password.
    options.Password.RequireUppercase = false;
    
    // Minimum length of the password.
    options.Password.RequiredLength = 6;
    
    // Number of unique characters in the password.
    options.Password.RequiredUniqueChars = 1;
    
    // SignIn settings: Configures the sign-in options for the identity system.
    
    // Require confirmed email to sign in.
    options.SignIn.RequireConfirmedEmail = false;
    
    // Require confirmed phone number to sign in.
    options.SignIn.RequireConfirmedPhoneNumber = false;
    
    // User settings: Configures the user options for the identity system.
    
    // Require unique email for each user.
    options.User.RequireUniqueEmail = true;
    
    // Specifies the allowed characters in the username.
    options.User.AllowedUserNameCharacters = "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789-._@+";
    
    // Token settings: Configures the token options for the identity system.
    
    // The token provider used to generate email confirmation tokens.
    options.Tokens.EmailConfirmationTokenProvider = TokenOptions.DefaultEmailProvider;
    
    // The token provider used to generate password reset tokens.
    options.Tokens.PasswordResetTokenProvider = TokenOptions.DefaultProvider;
    
    // The token provider used to generate change email tokens.
    options.Tokens.ChangeEmailTokenProvider = TokenOptions.DefaultProvider;
    
    // Store settings: Configures the store options for the identity system.
    
    // Specifies whether to protect personal data using a data protection provider.
    options.Stores.ProtectPersonalData = true;
    
    // Specifies whether to use the original user token life span for user tokens.
    options.Stores.OriginalUserTokenLifeSpan = TimeSpan.FromDays(1);
    
    // SignInManager settings: Configures options for SignInManager.
    
    // The default sign-in scheme used by the application.
    options.SignInManager.DefaultSignInScheme = IdentityConstants.ApplicationScheme;
    
    // The default authentication scheme used by the application.
    options.SignInManager.DefaultAuthenticateScheme = IdentityConstants.ApplicationScheme;
    
    // The default challenge scheme used by the application.
    options.SignInManager.DefaultChallengeScheme = IdentityConstants.ApplicationScheme;
    
    // The default external sign-in scheme used by the application.
    options.SignInManager.DefaultExternalSignInScheme = IdentityConstants.ExternalScheme;
    
    // The default two-factor remember me scheme used by the application.
    options.SignInManager.DefaultTwoFactorRememberMeScheme = IdentityConstants.TwoFactorRememberMeScheme;
    
    // The default two-factor user id scheme used by the application.
    options.SignInManager.DefaultTwoFactorUserIdScheme = IdentityConstants.TwoFactorUserIdScheme;
});


```
## Step 7: Add Migration and Update Database

### Add Migration
```csharp
Add-Migration (Name)

```
### Update Database
```csharp
Update-Database

```


