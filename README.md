# IDENTITY WEB API

## Required NuGet packages
* Microsoft.AspNetCore.Identity.EntityFrameworkCore
* Microsoft.EntityFrameworkCore.Design (for database migrations)
* If using SQLite: Microsoft.EntityFrameworkCore.Sqlite
### for swagger api documentation (.NET 9 no longer bundles it in)
* Swashbuckle.AspNetCore

## Configure for Identity

### DbContext
1. Create a new class: ApplicationDbContext
2. Inherit from IdentityDbContext (I use base IdentityUser as type) and create the contructor
```
    public class ApplicationDbContext : IdentityDbContext<IdentityUser>
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options) 
            : base(options)
        {
        }
    }
```

### Configure Identity Endpoints
#### Add services to builder
1. Add DbContext (ApplicationDbContext) (UseSqlite, or whatever)
```
    builder.Services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(builder.Configuration.GetConnectionString("DefaultConnection")));
```
2. Add endpoints:
```
    builder.Services.AddIdentityApiEndpoints<IdentityUser>(options => 
    {
        options.SignIn.RequireConfirmedEmail = false;
        options.SignIn.RequireConfirmedAccount = false;
    })
    .AddEntityFrameworkStores<ApplicationDbContext>();
```
#### Configure app
1. Map the API: ```app.MapIdentityApi<IdentityUser>();```

#### Add authorization protection to Weather Forcast endpoint 
1. Add `Authorize` attribute to the controller / endpoint
```
    [ApiController]
    [Route("[controller]")]
    [Authorize]
    public class WeatherForecastController : ControllerBase
    {
    ...
```

### Optional: Configure Swagger for API testing / documentation
1.  Add Services
```
    builder.Services.AddEndpointsApiExplorer();
    builder.Services.AddSwaggerGen();
```
2. Configure App
```
    app.UseSwagger();
    app.UseSwaggerUI();
```
