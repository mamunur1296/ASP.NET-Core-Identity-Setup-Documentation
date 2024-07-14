# JWT Token Configuration Documentation
This document provides a detailed guide on implementing JWT token authentication in an ASP.NET Core application. The steps include installing necessary packages, setting up configurations, and implementing services for generating JWT tokens.
## Step 1:  Install Package

Add the following package reference to your project file (.csproj):

```csharp
<PackageReference Include="Microsoft.AspNetCore.Authentication.JwtBearer" Version="6.0.32" />
```

## Step 2:  Add Configuration to appsettings.json

Add the JWT configuration to your appsettings.json file:

```csharp
{
  "Jwt": {
    "Key": "226e4b336c6bf960ae3a01bcb5a7778660e52b4e1939ef2de1fe14d662926a6881ddd2f504dee2b3d6392e503d6fa55a2eb0ad37f7aef5b7157fbaf3b5965d9f",
    "Issuer": "jwt",
    "Audience": "jwt",
    "ExpiryMinutes": 120
  }
}

```
## Step 3:  Create Global Variables for Authentication

In Program.cs or Startup.cs, retrieve the JWT settings from the configuration:

```csharp
var _key = builder.Configuration["Jwt:Key"];
var _issuer = builder.Configuration["Jwt:Issuer"];
var _audience = builder.Configuration["Jwt:Audience"];
var _expiryMinutes = builder.Configuration["Jwt:ExpiryMinutes"];

```
## Step 4:  Configure JWT Authentication in Program.cs

Add the JWT authentication configuration in the Program.cs file:

```csharp
builder.Services.AddAuthentication(options =>
{
    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
    options.DefaultChallengeScheme = JwtBearerDefaults.AuthenticationScheme;
    options.DefaultScheme = JwtBearerDefaults.AuthenticationScheme;
})
.AddJwtBearer(options =>
{
    options.RequireHttpsMetadata = false;
    options.SaveToken = true;
    options.TokenValidationParameters = new TokenValidationParameters
    {
        ValidateIssuer = true,
        ValidateAudience = true,
        ValidateLifetime = true,
        ValidateIssuerSigningKey = true,
        ValidIssuer = _issuer,
        ValidAudience = _audience,
        IssuerSigningKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes(_key)),
        ClockSkew = TimeSpan.FromMinutes(Convert.ToDouble(_expiryMinutes))
    };
});

```
## Step 5:  Create ITokenGenerator Interface

Create an interface for the token generator in the Services/Interfaces directory:

```csharp
namespace IdentityPOC.Services.Interfaces
{
    public interface ITokenGenerator
    {
        string GenerateJWTToken((string userId, string userName, IList<string> roles) userDetails);
    }
}

```
## Step 6:  Implement TokenGenerator Class

Create the TokenGenerator class in the Services/Implement directory:

```csharp
using Microsoft.IdentityModel.Tokens;
using System;
using System.Collections.Generic;
using System.IdentityModel.Tokens.Jwt;
using System.Security.Claims;
using System.Text;
using IdentityPOC.Services.Interfaces;

namespace IdentityPOC.Services.Implement
{
    public class TokenGenerator : ITokenGenerator
    {
        private readonly string _key;
        private readonly string _issuer;
        private readonly string _audience;
        private readonly string _expiryMinutes;

        public TokenGenerator(string key, string issuer, string audience, string expiryMinutes)
        {
            _key = key;
            _issuer = issuer;
            _audience = audience;
            _expiryMinutes = expiryMinutes;
        }

        public string GenerateJWTToken((string userId, string userName, IList<string> roles) userDetails)
        {
            var securityKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes(_key));
            var signingCredentials = new SigningCredentials(securityKey, SecurityAlgorithms.HmacSha256);

            var (userId, userName, roles) = userDetails;

            var claims = new List<Claim>
            {
                new Claim(JwtRegisteredClaimNames.Sub, userName),
                new Claim(JwtRegisteredClaimNames.Jti, userId),
                new Claim(ClaimTypes.Name, userName),
                new Claim("UserId", userId)
            };
            claims.AddRange(roles.Select(role => new Claim(ClaimTypes.Role, role)));

            var token = new JwtSecurityToken(
                issuer: _issuer,
                audience: _audience,
                claims: claims,
                expires: DateTime.Now.AddMinutes(Convert.ToDouble(_expiryMinutes)),
                signingCredentials: signingCredentials
            );

            var encodedToken = new JwtSecurityTokenHandler().WriteToken(token);
            return encodedToken;
        }
    }
}

```
## Step 7: Add Dependency Injection

Register the TokenGenerator service in Program.cs:

```csharp
builder.Services.AddSingleton<ITokenGenerator>(new TokenGenerator(_key, _issuer, _audience, _expiryMinutes));

```

