---
title: ASP.NET Core でのロールベースの承認
author: rick-anderson
description: ロールを承認属性に渡すことによって、ASP.NET Core コントローラーとアクションアクセスを制限する方法について説明します。
ms.author: riande
ms.date: 10/14/2016
no-loc:
- appsettings.json
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
uid: security/authorization/roles
ms.openlocfilehash: 0a2e62afebbcda9710ef82857c87cae8af0375fe
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051162"
---
# <a name="role-based-authorization-in-aspnet-core"></a>ASP.NET Core でのロールベースの承認

<a name="security-authorization-role-based"></a>

Id が作成されると、1つまたは複数のロールに属することができます。 たとえば、Tracy は管理者ロールとユーザーロールに属している場合がありますが、Scott はユーザーロールのみに属している可能性があります。 これらのロールを作成および管理する方法は、承認プロセスのバッキングストアによって異なります。 ロールは、 [ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal)クラスの[IsInRole](/dotnet/api/system.security.principal.genericprincipal.isinrole)メソッドを使用して開発者に公開されます。

## <a name="adding-role-checks"></a>ロールチェックの追加

ロールベースの承認チェックは宣言型です &mdash; 。開発者は、コントローラーまたはコントローラー内のアクションに対してコード内にそれらを埋め込み、要求されたリソースにアクセスするために、現在のユーザーがメンバーである必要があるロールを指定します。

たとえば、次のコードは、のアクションへのアクセスを、 `AdministrationController` ロールのメンバーであるユーザーに制限し `Administrator` ます。

```csharp
[Authorize(Roles = "Administrator")]
public class AdministrationController : Controller
{
}
```

複数のロールは、コンマ区切りの一覧として指定できます。

```csharp
[Authorize(Roles = "HRManager,Finance")]
public class SalaryController : Controller
{
}
```

このコントローラーにアクセスできるのは、ロールまたはロールのメンバーであるユーザーだけ `HRManager` `Finance` です。

複数の属性を適用する場合、アクセスするユーザーは、指定されたすべてのロールのメンバーである必要があります。次の例では、ユーザーがとの両方のロールのメンバーである必要があり `PowerUser` `ControlPanelUser` ます。

```csharp
[Authorize(Roles = "PowerUser")]
[Authorize(Roles = "ControlPanelUser")]
public class ControlPanelController : Controller
{
}
```

アクションレベルで追加のロール承認属性を適用することで、さらにアクセスを制限できます。

```csharp
[Authorize(Roles = "Administrator, PowerUser")]
public class ControlPanelController : Controller
{
    public ActionResult SetTime()
    {
    }

    [Authorize(Roles = "Administrator")]
    public ActionResult ShutDown()
    {
    }
}
```

前のコードスニペットでは、ロールのメンバー、 `Administrator` または `PowerUser` ロールはコントローラーとアクションにアクセスできますが、ロール `SetTime` のメンバーだけが `Administrator` アクションにアクセスでき `ShutDown` ます。

コントローラーをロックダウンすることもできますが、個々のアクションへの認証されていない匿名アクセスが許可されます。

```csharp
[Authorize]
public class ControlPanelController : Controller
{
    public ActionResult SetTime()
    {
    }

    [AllowAnonymous]
    public ActionResult Login()
    {
    }
}
```

::: moniker range=">= aspnetcore-2.0"

ページの場合 Razor 、は `AuthorizeAttribute` 次のいずれかの方法で適用できます。

* [規則](xref:razor-pages/razor-pages-conventions#page-model-action-conventions)の使用、または
* `AuthorizeAttribute`をインスタンスに適用し `PageModel` ます。

```csharp
[Authorize(Policy = "RequireAdministratorRole")]
public class UpdateModel : PageModel
{
    public ActionResult OnPost()
    {
    }
}
```

> [!IMPORTANT]
> フィルター属性 (を含む) は `AuthorizeAttribute` 、PageModel にのみ適用でき、特定のページハンドラーメソッドには適用できません。
::: moniker-end

<a name="security-authorization-role-policy"></a>

## <a name="policy-based-role-checks"></a>ポリシーベースのロールチェック

ロール要件は、新しいポリシー構文を使用して表すこともできます。ここでは、開発者が承認サービス構成の一部としてスタートアップ時にポリシーを登録します。 これは通常、 `ConfigureServices()` *Startup.cs* ファイル内で発生します。

::: moniker range=">= aspnetcore-3.0"
```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.AddRazorPages();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("RequireAdministratorRole",
             policy => policy.RequireRole("Administrator"));
    });
}
```
::: moniker-end

::: moniker range="< aspnetcore-3.0"
```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("RequireAdministratorRole",
             policy => policy.RequireRole("Administrator"));
    });
}
```
::: moniker-end

ポリシーは、属性のプロパティを使用して適用され `Policy` `AuthorizeAttribute` ます。

```csharp
[Authorize(Policy = "RequireAdministratorRole")]
public IActionResult Shutdown()
{
    return View();
}
```

要件に複数の許可されたロールを指定する場合は、メソッドのパラメーターとして指定でき `RequireRole` ます。

```csharp
options.AddPolicy("ElevatedRights", policy =>
                  policy.RequireRole("Administrator", "PowerUser", "BackupAdministrator"));
```

この例 `Administrator` では、、、またはロールに属しているユーザーを承認 `PowerUser` `BackupAdministrator` します。

### <a name="add-role-services-to-no-locidentity"></a>役割サービスの追加先 Identity

役割サービスを追加するには、 [Addroles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) を追加します。

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](roles/samples/3_0/Startup.cs?name=snippet&highlight=7)]
::: moniker-end

::: moniker range="< aspnetcore-3.0"
[!code-csharp[](roles/samples/2_2/Startup.cs?name=snippet&highlight=7)]
::: moniker-end

