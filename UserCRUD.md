# User Controller Documentation
This document provides a detailed guide on implementing and using the UserController for managing users in an ASP.NET Core application with Identity. The controller includes endpoints for creating, retrieving, updating, and deleting user profiles.
## Step 1:  Create UserApiController

Create a new controller named UserController.cs in the Controllers directory.

```csharp
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Identity;
using Microsoft.EntityFrameworkCore;
using IdentityPOC.DTOs;
using IdentityPOC.Models; // Ensure you have a namespace for ApplicationUser model

namespace IdentityPOC.Controllers
{
    [Route("api/[controller]")]
    [ApiController]
    public class UserController : ControllerBase
    {
        private readonly UserManager<ApplicationUser> _userManager;

        public UserController(UserManager<ApplicationUser> userManager)
        {
            _userManager = userManager;
        }

        [HttpPost("Create")]
        public async Task<IActionResult> Create(UserDTOs model)
        {
            var user = new ApplicationUser()
            {
                Email = model.Email,
                FirstName = model.FirstName,
                LastName = model.LastName,
                UserName = model.UserName
            };
            var result = await _userManager.CreateAsync(user, model.Password);
            if (result.Succeeded)
            {
                return Ok();
            }
            return BadRequest(result.Errors);
        }

        [HttpGet("GetAll")]
        public async Task<IActionResult> GetAllUserAsync()
        {
            var users = await _userManager.Users.ToListAsync();
            return Ok(users);
        }

        [HttpGet("GetUserDetails/{userId}")]
        public async Task<IActionResult> GetUserById(string userId)
        {
            var user = await _userManager.Users.FirstOrDefaultAsync(x => x.Id == userId);
            if (user == null)
            {
                return NotFound();
            }
            return Ok(user);
        }

        [HttpPut("EditUserProfile/{id}")]
        public async Task<ActionResult> EditUserProfile(string id, UserDTOs model)
        {
            var user = await _userManager.FindByIdAsync(id);
            if (user == null)
            {
                return NotFound();
            }
            user.FirstName = model.FirstName;
            user.LastName = model.LastName;
            user.Email = model.Email;
            var result = await _userManager.UpdateAsync(user);
            if (result.Succeeded)
            {
                return Ok();
            }
            return BadRequest(result.Errors);
        }

        [HttpDelete("Delete/{userId}")]
        public async Task<IActionResult> DeleteUser(string userId)
        {
            var user = await _userManager.Users.FirstOrDefaultAsync(x => x.Id == userId);
            if (user == null)
            {
                return NotFound();
            }
            var result = await _userManager.DeleteAsync(user);
            if (result.Succeeded)
            {
                return Ok();
            }
            return BadRequest(result.Errors);
        }

        [HttpGet("GetUserDetailsByUserName/{userName}")]
        public async Task<IActionResult> GetUserDetailsByUserName(string userName)
        {
            var user = await _userManager.Users.FirstOrDefaultAsync(x => x.UserName == userName);
            if (user == null)
            {
                return NotFound();
            }
            return Ok(user);
        }
    }
}

```


## Step 2:  Create User DTO

Create a new file named UserDTOs.cs in the DTOs directory.

```csharp
namespace IdentityPOC.DTOs
{
    public class UserDTOs
    {
        public string UserName { get; set; }
        public string Email { get; set; }
        public string FirstName { get; set; }
        public string LastName { get; set; }
        public string Password { get; set; }
        public string ConfirmationPassword { get; set; }
        // Additional properties if needed
    }
}


```


## Step 3:  Create User (Register User)

```csharp
[HttpPost("Create")]
public async Task<IActionResult> Create(UserDTOs model)
{
    var user = new ApplicationUser()
    {
        Email = model.Email,
        FirstName = model.FirstName,
        LastName = model.LastName,
        UserName = model.UserName
    };
    var result = await _userManager.CreateAsync(user, model.Password);
    if (result.Succeeded)
    {
        return Ok();
    }
    return BadRequest(result.Errors);
}

```

## Step 4: Get All Users

```csharp
[HttpGet("GetAll")]
public async Task<IActionResult> GetAllUserAsync()
{
    var users = await _userManager.Users.ToListAsync();
    return Ok(users);
}
```

## Step 5: Get User by ID (User Details)

```csharp
[HttpGet("GetUserDetails/{userId}")]
public async Task<IActionResult> GetUserById(string userId)
{
    var user = await _userManager.Users.FirstOrDefaultAsync(x => x.Id == userId);
    if (user == null)
    {
        return NotFound();
    }
    return Ok(user);
}
```


## Step 6: Edit User Profile

```csharp
[HttpPut("EditUserProfile/{id}")]
public async Task<ActionResult> EditUserProfile(string id, UserDTOs model)
{
    var user = await _userManager.FindByIdAsync(id);
    if (user == null)
    {
        return NotFound();
    }
    user.FirstName = model.FirstName;
    user.LastName = model.LastName;
    user.Email = model.Email;
    var result = await _userManager.UpdateAsync(user);
    if (result.Succeeded)
    {
        return Ok();
    }
    return BadRequest(result.Errors);
}

```
## Step 7: Delete User Profile

```csharp
[HttpDelete("Delete/{userId}")]
public async Task<IActionResult> DeleteUser(string userId)
{
    var user = await _userManager.Users.FirstOrDefaultAsync(x => x.Id == userId);
    if (user == null)
    {
        return NotFound();
    }
    var result = await _userManager.DeleteAsync(user);
    if (result.Succeeded)
    {
        return Ok();
    }
    return BadRequest(result.Errors);
}


```

## Step 8: Get User Details by UserName

```csharp
[HttpGet("GetUserDetailsByUserName/{userName}")]
public async Task<IActionResult> GetUserDetailsByUserName(string userName)
{
    var user = await _userManager.Users.FirstOrDefaultAsync(x => x.UserName == userName);
    if (user == null)
    {
        return NotFound();
    }
    return Ok(user);
}
```

