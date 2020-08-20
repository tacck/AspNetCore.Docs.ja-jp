---
title: 認証および Identity ASP.NET Core への移行
author: ardalis
description: ASP.NET MVC プロジェクトから ASP.NET Core MVC プロジェクトに認証と id を移行する方法について説明します。
ms.author: riande
ms.date: 3/22/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/identity
ms.openlocfilehash: 85674d7ed02f189f78da92bffdf927a3ca21357d
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88629510"
---
# <a name="migrate-authentication-and-no-locidentity-to-aspnet-core"></a>認証および Identity ASP.NET Core への移行

作成者: [Steve Smith](https://ardalis.com/)

前の記事では、 [ASP.NET mvc プロジェクトから ASP.NET CORE mvc に構成を移行](xref:migration/configuration)しています。 この記事では、登録、ログイン、およびユーザー管理機能を移行します。

## <a name="configure-no-locidentity-and-membership"></a>Identityメンバーシップを構成する

ASP.NET MVC では、認証と id 機能は、[ Identity *App_Start* ] フォルダーにある*Startup.Auth.cs*と* Identity Config.cs*の ASP.NET を使用して構成されます。 ASP.NET Core MVC では、これらの機能は *Startup.cs*で構成されています。

次の NuGet パッケージをインストールします。

* `Microsoft.AspNetCore.Identity.EntityFrameworkCore`
* `Microsoft.AspNetCore.Authentication.Cookies`
* `Microsoft.EntityFrameworkCore.SqlServer`

*Startup.cs*で、 `Startup.ConfigureServices` Entity Framework とサービスを使用するようにメソッドを更新し Identity ます。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add EF services to the services container.
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

     services.AddMvc();
}
```

この時点で、上記のコードでは、ASP.NET MVC プロジェクトからまだ移行していない2つの型が参照されています。 `ApplicationDbContext` `ApplicationUser` ASP.NET Core プロジェクトに新しい *モデル* フォルダーを作成し、これらの型に対応する2つのクラスを追加します。 これらのクラスの ASP.NET MVC バージョンは */Models/ Identity Models.cs*にありますが、移行されたプロジェクトのクラスごとに1つのファイルを使用します。これはより明確であるためです。

*ApplicationUser.cs*:

```csharp
using Microsoft.AspNetCore.Identity.EntityFrameworkCore;

namespace NewMvcProject.Models
{
  public class ApplicationUser : IdentityUser
  {
  }
}
```

*ApplicationDbContext.cs*:

```csharp
using Microsoft.AspNetCore.Identity.EntityFrameworkCore;
using Microsoft.Data.Entity;

namespace NewMvcProject.Models
{
    public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }

        protected override void OnModelCreating(ModelBuilder builder)
        {
            base.OnModelCreating(builder);
            // Customize the ASP.NET Core Identity model and override the defaults if needed.
            // For example, you can rename the ASP.NET Core Identity table names and more.
            // Add your customizations after calling base.OnModelCreating(builder);
        }
    }
}
```

ASP.NET Core MVC Starter Web プロジェクトには、ユーザーまたはの多くのカスタマイズが含まれていません `ApplicationDbContext` 。 実際のアプリを移行する場合は、アプリのユーザーとクラスのすべてのカスタムプロパティとメソッド、および `DbContext` アプリが使用するその他のモデルクラスも移行する必要があります。 たとえば、にがある場合は、 `DbContext` `DbSet<Album>` クラスを移行する必要があり `Album` ます。

これらのファイルを配置したら、ステートメントを更新して、 *Startup.cs* ファイルをコンパイルすることができ `using` ます。

```csharp
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Identity;
using Microsoft.AspNetCore.Hosting;
using Microsoft.EntityFrameworkCore;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
```

これで、アプリは認証とサービスをサポートする準備ができました Identity 。 これらの機能をユーザーに公開する必要があるだけです。

## <a name="migrate-registration-and-login-logic"></a>登録とログインのロジックを移行する

IdentityEntity Framework と SQL Server を使用して構成されたアプリとデータアクセス用に構成されたサービスを使用して、登録とアプリへのログインのサポートを追加する準備が整いました。 [移行プロセスの前半](xref:migration/mvc#migrate-the-layout-file)で、 *_Layout*の *_LoginPartial*への参照がコメントアウトされていることを思い出してください。 次に、そのコードに戻り、コメントを解除し、ログイン機能をサポートするために必要なコントローラーとビューを追加します。

_Layout の行のコメントを解除 `@Html.Partial` *します。*

```cshtml
      <li>@Html.ActionLink("Contact", "Contact", "Home")</li>
    </ul>
    @*@Html.Partial("_LoginPartial")*@
  </div>
</div>
```

次に、 Razor *_LoginPartial* という名前の新しいビューを *ビュー/共有* フォルダーに追加します。

次のコードを使用して _LoginPartial を更新 *します* (すべての内容を置き換えます)。

```cshtml
@inject SignInManager<ApplicationUser> SignInManager
@inject UserManager<ApplicationUser> UserManager

@if (SignInManager.IsSignedIn(User))
{
    <form asp-area="" asp-controller="Account" asp-action="Logout" method="post" id="logoutForm" class="navbar-right">
        <ul class="nav navbar-nav navbar-right">
            <li>
                <a asp-area="" asp-controller="Manage" asp-action="Index" title="Manage">Hello @UserManager.GetUserName(User)!</a>
            </li>
            <li>
                <button type="submit" class="btn btn-link navbar-btn navbar-link">Log out</button>
            </li>
        </ul>
    </form>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li><a asp-area="" asp-controller="Account" asp-action="Register">Register</a></li>
        <li><a asp-area="" asp-controller="Account" asp-action="Login">Log in</a></li>
    </ul>
}
```

この時点で、ブラウザーでサイトを最新の状態に更新できるようになります。

## <a name="summary"></a>まとめ

ASP.NET Core には、ASP.NET 機能の変更が導入さ Identity れています。 この記事では、ASP.NET の認証およびユーザー管理機能を ASP.NET Core に移行する方法について説明しました Identity 。
