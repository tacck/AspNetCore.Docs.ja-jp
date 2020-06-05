## <a name="usermanager-and-signinmanager"></a><span data-ttu-id="424db-101">UserManager と SignInManager</span><span class="sxs-lookup"><span data-stu-id="424db-101">UserManager and SignInManager</span></span>

<span data-ttu-id="424db-102">サーバーアプリが必要とするユーザー id 要求の種類を設定します。</span><span class="sxs-lookup"><span data-stu-id="424db-102">Set the user identifier claim type when a Server app requires:</span></span>

* <span data-ttu-id="424db-103"><xref:Microsoft.AspNetCore.Identity.UserManager%601><xref:Microsoft.AspNetCore.Identity.SignInManager%601>API エンドポイント内。</span><span class="sxs-lookup"><span data-stu-id="424db-103"><xref:Microsoft.AspNetCore.Identity.UserManager%601> or <xref:Microsoft.AspNetCore.Identity.SignInManager%601> in an API endpoint.</span></span>
* <span data-ttu-id="424db-104"><xref:Microsoft.AspNetCore.Identity.IdentityUser>ユーザーの名前、電子メールアドレス、またはロックアウトの終了時刻などの詳細。</span><span class="sxs-lookup"><span data-stu-id="424db-104"><xref:Microsoft.AspNetCore.Identity.IdentityUser> details, such as the user's name, email address, or lockout end time.</span></span>

<span data-ttu-id="424db-105">`Startup.ConfigureServices`の場合:</span><span class="sxs-lookup"><span data-stu-id="424db-105">In `Startup.ConfigureServices`:</span></span>

```csharp
using System.Security.Claims;

...

services.Configure<IdentityOptions>(options => 
    options.ClaimsIdentity.UserIdClaimType = ClaimTypes.NameIdentifier);
```

<span data-ttu-id="424db-106">`WeatherForecastController` <xref:Microsoft.AspNetCore.Identity.IdentityUser%601.UserName> メソッドが呼び出されると、次のログがに記録 `Get` されます。</span><span class="sxs-lookup"><span data-stu-id="424db-106">The following `WeatherForecastController` logs the <xref:Microsoft.AspNetCore.Identity.IdentityUser%601.UserName> when the `Get` method is called:</span></span>

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Identity;
using Microsoft.Extensions.Logging;
using {APP NAMESPACE}.Server.Models;
using {APP NAMESPACE}.Shared;

namespace {APP NAMESPACE}.Server.Controllers
{
    [Authorize]
    [ApiController]
    [Route("[controller]")]
    public class WeatherForecastController : ControllerBase
    {
        private readonly UserManager<ApplicationUser> userManager;

        private static readonly string[] Summaries = new[]
        {
            "Freezing", "Bracing", "Chilly", "Cool", "Mild", "Warm", 
            "Balmy", "Hot", "Sweltering", "Scorching"
        };

        private readonly ILogger<WeatherForecastController> logger;

        public WeatherForecastController(ILogger<WeatherForecastController> logger, 
            UserManager<ApplicationUser> userManager)
        {
            this.logger = logger;
            this.userManager = userManager;
        }

        [HttpGet]
        public async Task<IEnumerable<WeatherForecast>> Get()
        {
            var rng = new Random();

            var user = await userManager.GetUserAsync(User);

            if (user != null)
            {
                logger.LogInformation($"User.Identity.Name: {user.UserName}");
            }

            return Enumerable.Range(1, 5).Select(index => new WeatherForecast
            {
                Date = DateTime.Now.AddDays(index),
                TemperatureC = rng.Next(-20, 55),
                Summary = Summaries[rng.Next(Summaries.Length)]
            })
            .ToArray();
        }
    }
}
```
