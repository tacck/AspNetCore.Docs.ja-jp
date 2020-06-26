---
title: ASP.NET Core での単純な承認
author: rick-anderson
description: 承認属性を使用して、ASP.NET Core コントローラーとアクションへのアクセスを制限する方法について説明します。
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/simple
ms.openlocfilehash: 497103a14591476f3167602631b6b011264f5086
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408332"
---
# <a name="simple-authorization-in-aspnet-core"></a>ASP.NET Core での単純な承認

<a name="security-authorization-simple"></a>

ASP.NET Core での承認は <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> 、とそのさまざまなパラメーターで制御されます。 最も単純な形式では、 `[Authorize]` コントローラー、アクション、またはページに属性を適用する Razor ことで、そのコンポーネントへのアクセスが認証されたユーザーに制限されます。

たとえば、次のコードは、へのアクセスを認証された `AccountController` ユーザーに制限します。

```csharp
[Authorize]
public class AccountController : Controller
{
    public ActionResult Login()
    {
    }

    public ActionResult Logout()
    {
    }
}
```

コントローラーではなくアクションに承認を適用する場合は、 `AuthorizeAttribute` アクション自体に属性を適用します。

```csharp
public class AccountController : Controller
{
   public ActionResult Login()
   {
   }

   [Authorize]
   public ActionResult Logout()
   {
   }
}
```

これで、認証されたユーザーのみが関数にアクセスできるようになりました `Logout` 。

属性を使用して、認証され `AllowAnonymous` ていないユーザーによる個々のアクションへのアクセスを許可することもできます。 次に例を示します。

```csharp
[Authorize]
public class AccountController : Controller
{
    [AllowAnonymous]
    public ActionResult Login()
    {
    }

    public ActionResult Logout()
    {
    }
}
```

これにより、認証済みのユーザー `AccountController` または認証 `Login` されていない/匿名の状態に関係なく、すべてのユーザーがアクセスできるアクションを除き、に対して認証されたユーザーのみが許可されます。

> [!WARNING]
> `[AllowAnonymous]`すべての承認ステートメントをバイパスします。 `[AllowAnonymous]`との `[Authorize]` 属性を組み合わせると、 `[Authorize]` 属性は無視されます。 たとえば `[AllowAnonymous]` 、コントローラーレベルでを適用した場合、 `[Authorize]` 同じコントローラー (またはその中のアクション) の属性はすべて無視されます。

<a name="aarp"></a>

## <a name="authorize-attribute-and-razor-pages"></a>属性と Razor ページを承認する

は、 <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ページハンドラーには適用でき***ません*** Razor 。 たとえば、は、、 `[Authorize]` `OnGet` `OnPost` または他のページハンドラーには適用できません。 さまざまなハンドラーに対して異なる承認要件を持つページに ASP.NET Core MVC コントローラーを使用することを検討してください。

ページハンドラーメソッドに承認を適用するには、次の2つの方法を使用でき Razor ます。

* 異なる承認を必要とするページハンドラーには、個別のページを使用します。 共有コンテンツを1つまたは複数の[部分ビュー](xref:mvc/views/partial)に移動しました。 可能な場合は、これが推奨される方法です。
* 共通ページを共有する必要があるコンテンツには、 [Iasyncpagefilter. Onpageハンドラ Selectionasync](xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter.OnPageHandlerSelectionAsync%2A)の一部として承認を実行するフィルターを記述します。 [Pageハンドラ auth](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth) GitHub プロジェクトは、次の方法を示しています。
  * [Authorizepageハンドラフィルター](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth/AuthorizePageHandlerFilter.cs)は、承認フィルターを実装します。[!code-csharp[](~/security/authorization/simple/samples/3.1/PageHandlerAuth/Pages/Index.cshtml.cs?name=snippet)]

  * [[Authorizepagehandler]](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth/Pages/Index.cshtml.cs#L16)属性がページハンドラーに適用され `OnGet` ます。[!code-csharp[](~/security/authorization/simple/samples/3.1/PageHandlerAuth/AuthorizeIndexPageHandlerFilter.cs?name=snippet)]

> [!WARNING]
> [Pageハンドラ auth](https://github.com/pranavkm/PageHandlerAuth)サンプルアプローチでは、次の***ことはできません***。
> * ページ、ページモデル、またはグローバルに適用された承認属性を使用して構成します。 承認属性を作成すると、ページに1つ以上のインスタンスが適用されている場合に、認証と承認が複数回実行 `AuthorizeAttribute` `AuthorizeFilter` されます。
> * ASP.NET Core 認証および承認システムの残りの部分と連携して作業します。 この方法を使用してアプリケーションに対して正しく動作することを確認する必要があります。

ページハンドラーでをサポートする予定はありません `AuthorizeAttribute` Razor 。 
