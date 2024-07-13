# Role Controller Documentation
This document provides a detailed guide on implementing and using the RoleController for managing roles in an ASP.NET Core application with Identity. The controller includes endpoints for creating, retrieving, updating, and deleting roles.


## Step 1: Create RoleApiController

Create a new controller named RoleController.cs in the Controllers directory.

```csharp
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Identity;
using System.Linq;
using System.Threading.Tasks;

namespace IdentityPOC.Controllers
{
    [Route("api/[controller]")]
    [ApiController]
    public class RoleController : ControllerBase
    {
        private readonly RoleManager<IdentityRole> _roleManager;

        public RoleController(RoleManager<IdentityRole> roleManager)
        {
            _roleManager = roleManager;
        }

        [HttpPost("Create")]
        public async Task<ActionResult> CreateRoleAsync(RoleDTOs model)
        {
            var result = await _roleManager.CreateAsync(new IdentityRole(model.RoleName));
            if (result.Succeeded)
            {
                return Ok(result);
            }
            return BadRequest(result.Errors);
        }

        [HttpGet("GetAllRoles")]
        public async Task<IActionResult> GetAllRolesAsync()
        {
            var roles = await _roleManager.Roles.Select(x => new
            {
                x.Id,
                x.Name
            }).ToListAsync();

            return Ok(roles);
        }

        [HttpGet("{id}")]
        public async Task<IActionResult> GetRoleByIdAsync(string id)
        {
            var role = await _roleManager.FindByIdAsync(id);
            if (role == null)
            {
                return NotFound();
            }
            return Ok(role);
        }

        [HttpDelete("Delete/{id}")]
        public async Task<IActionResult> DeleteRoleAsync(string id)
        {
            var role = await _roleManager.FindByIdAsync(id);
            if (role == null)
            {
                return NotFound();
            }
            var result = await _roleManager.DeleteAsync(role);
            if (result.Succeeded)
            {
                return Ok(result);
            }
            return BadRequest(result.Errors);
        }

        [HttpPut("Edit/{id}")]
        public async Task<ActionResult> EditRole(string id, RoleDTOs models)
        {
            var role = await _roleManager.FindByIdAsync(id);
            if (role == null)
            {
                return NotFound();
            }
            role.Name = models.RoleName;
            var result = await _roleManager.UpdateAsync(role);
            if (result.Succeeded)
            {
                return Ok(result);
            }
            return BadRequest(result.Errors);
        }
    }
}

```

## Step 2: Create Role DTOs

Create a new file named RoleDTOs.cs in the DTOs directory.

```csharp

namespace IdentityPOC.DTOs
{
    public class RoleDTOs
    {
        public string RoleName { get; set; }
    }
}

```


## Step 3: Create Role


```csharp

[HttpPost("Create")]
public async Task<ActionResult> CreateRoleAsync(RoleDTOs model)
{
    var result = await _roleManager.CreateAsync(new IdentityRole(model.RoleName));
    if (result.Succeeded)
    {
        return Ok(result);
    }
    return BadRequest(result.Errors);
}


```

## Step 4: Get All Roles


```csharp

[HttpGet("GetAllRoles")]
public async Task<IActionResult> GetAllRolesAsync()
{
    var roles = await _roleManager.Roles.Select(x => new
    {
        x.Id,
        x.Name
    }).ToListAsync();

    return Ok(roles);
}

```

## Step 5:  Get Role by ID


```csharp

[HttpGet("{id}")]
public async Task<IActionResult> GetRoleByIdAsync(string id)
{
    var role = await _roleManager.FindByIdAsync(id);
    if (role == null)
    {
        return NotFound();
    }
    return Ok(role);
}


```


## Step 6:  Delete Role


```csharp

[HttpDelete("Delete/{id}")]
public async Task<IActionResult> DeleteRoleAsync(string id)
{
    var role = await _roleManager.FindByIdAsync(id);
    if (role == null)
    {
        return NotFound();
    }
    var result = await _roleManager.DeleteAsync(role);
    if (result.Succeeded)
    {
        return Ok(result);
    }
    return BadRequest(result.Errors);
}



```

## Step 7:  Update Role


```csharp

[HttpPut("Edit/{id}")]
public async Task<ActionResult> EditRole(string id, RoleDTOs models)
{
    var role = await _roleManager.FindByIdAsync(id);
    if (role == null)
    {
        return NotFound();
    }
    role.Name = models.RoleName;
    var result = await _roleManager.UpdateAsync(role);
    if (result.Succeeded)
    {
        return Ok(result);
    }
    return BadRequest(result.Errors);
}
```
