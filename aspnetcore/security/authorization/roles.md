---
title: ASP.NET Core でのロールベースの承認
author: rick-anderson
description: ロールを承認属性に渡すことによって、ASP.NET Core コントローラーとアクションアクセスを制限する方法について説明します。
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/roles
ms.openlocfilehash: 01d4239377b128f711a110a821e1afea58ca14a7
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776540"
---
# <a name="role-based-authorization-in-aspnet-core"></a>ASP.NET Core でのロールベースの承認

<a name="security-authorization-role-based"></a>

Id が作成されると、1つまたは複数のロールに属することができます。 たとえば、Tracy は管理者ロールとユーザーロールに属している場合がありますが、Scott はユーザーロールのみに属している可能性があります。 これらのロールを作成および管理する方法は、承認プロセスのバッキングストアによって異なります。 ロールは、 [ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal)クラスの[IsInRole](/dotnet/api/system.security.principal.genericprincipal.isinrole)メソッドを使用して開発者に公開されます。

## <a name="adding-role-checks"></a>ロールチェックの追加

ロールベースの承認チェックは宣言&mdash;型です。開発者は、コントローラーまたはコントローラー内のアクションに対してコード内にそれらを埋め込み、要求されたリソースにアクセスするために、現在のユーザーがメンバーである必要があるロールを指定します。

たとえば、次のコードは、のアクションへのアクセスを`AdministrationController` 、 `Administrator`ロールのメンバーであるユーザーに制限します。

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

このコントローラーにアクセスできるのは、 `HRManager`ロールまたは`Finance`ロールのメンバーであるユーザーだけです。

複数の属性を適用する場合、アクセスするユーザーは、指定されたすべてのロールのメンバーである必要があります。次の例では、ユーザーが`PowerUser`と`ControlPanelUser`の両方のロールのメンバーである必要があります。

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

前のコードスニペットでは、 `Administrator`ロールのメンバー、 `PowerUser`またはロールはコントローラーと`SetTime`アクションにアクセスできますが、ロール`Administrator`のメンバーだけが`ShutDown`アクションにアクセスできます。

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

ページRazorの場合、 `AuthorizeAttribute`は次のいずれかの方法で適用できます。

* [規則](xref:razor-pages/razor-pages-conventions#page-model-action-conventions)の使用、または
* を`PageModel`インスタンス`AuthorizeAttribute`に適用します。

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
> フィルター属性 (を`AuthorizeAttribute`含む) は、PageModel にのみ適用でき、特定のページハンドラーメソッドには適用できません。
::: moniker-end

<a name="security-authorization-role-policy"></a>

## <a name="policy-based-role-checks"></a>ポリシーベースのロールチェック

ロール要件は、新しいポリシー構文を使用して表すこともできます。ここでは、開発者が承認サービス構成の一部としてスタートアップ時にポリシーを登録します。 これは通常、 `ConfigureServices()` *Startup.cs*ファイル内で発生します。

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

ポリシーは、 `AuthorizeAttribute`属性の`Policy`プロパティを使用して適用されます。

```csharp
[Authorize(Policy = "RequireAdministratorRole")]
public IActionResult Shutdown()
{
    return View();
}
```

要件に複数の許可されたロールを指定する場合は、メソッドの`RequireRole`パラメーターとして指定できます。

```csharp
options.AddPolicy("ElevatedRights", policy =>
                  policy.RequireRole("Administrator", "PowerUser", "BackupAdministrator"));
```

この例では、、、 `Administrator` `PowerUser`または`BackupAdministrator`ロールに属しているユーザーを承認します。

### <a name="add-role-services-to-identity"></a>役割サービスの追加先Identity

役割サービスを追加するには、 [Addroles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1)を追加します。

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](roles/samples/3_0/Startup.cs?name=snippet&highlight=7)]
::: moniker-end

::: moniker range="< aspnetcore-3.0"
[!code-csharp[](roles/samples/2_2/Startup.cs?name=snippet&highlight=7)]
::: moniker-end

