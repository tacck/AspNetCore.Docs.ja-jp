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
ms.openlocfilehash: 6bd83473e168ba9100d4f6041d5d71139762b46c
ms.sourcegitcommit: fa89d6553378529ae86b388689ac2c6f38281bb9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060112"
---
# <a name="simple-authorization-in-aspnet-core"></a><span data-ttu-id="60e35-103">ASP.NET Core での単純な承認</span><span class="sxs-lookup"><span data-stu-id="60e35-103">Simple authorization in ASP.NET Core</span></span>

<a name="security-authorization-simple"></a>

<span data-ttu-id="60e35-104">ASP.NET Core での承認は <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> 、とそのさまざまなパラメーターで制御されます。</span><span class="sxs-lookup"><span data-stu-id="60e35-104">Authorization in ASP.NET Core is controlled with <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> and its various parameters.</span></span> <span data-ttu-id="60e35-105">最も単純な形式では、 `[Authorize]` コントローラー、アクション、またはページに属性を適用する Razor ことで、そのコンポーネントへのアクセスが認証されたユーザーに制限されます。</span><span class="sxs-lookup"><span data-stu-id="60e35-105">In its simplest form, applying the `[Authorize]` attribute to a controller, action, or Razor Page, limits access to that component to any authenticated user.</span></span>

<span data-ttu-id="60e35-106">たとえば、次のコードは、へのアクセスを認証された `AccountController` ユーザーに制限します。</span><span class="sxs-lookup"><span data-stu-id="60e35-106">For example, the following code limits access to the `AccountController` to any authenticated user.</span></span>

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

<span data-ttu-id="60e35-107">コントローラーではなくアクションに承認を適用する場合は、 `AuthorizeAttribute` アクション自体に属性を適用します。</span><span class="sxs-lookup"><span data-stu-id="60e35-107">If you want to apply authorization to an action rather than the controller, apply the `AuthorizeAttribute` attribute to the action itself:</span></span>

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

<span data-ttu-id="60e35-108">これで、認証されたユーザーのみが関数にアクセスできるようになりました `Logout` 。</span><span class="sxs-lookup"><span data-stu-id="60e35-108">Now only authenticated users can access the `Logout` function.</span></span>

<span data-ttu-id="60e35-109">属性を使用して、認証され `AllowAnonymous` ていないユーザーによる個々のアクションへのアクセスを許可することもできます。</span><span class="sxs-lookup"><span data-stu-id="60e35-109">You can also use the `AllowAnonymous` attribute to allow access by non-authenticated users to individual actions.</span></span> <span data-ttu-id="60e35-110">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="60e35-110">For example:</span></span>

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

<span data-ttu-id="60e35-111">これにより、認証済みのユーザー `AccountController` または認証 `Login` されていない/匿名の状態に関係なく、すべてのユーザーがアクセスできるアクションを除き、に対して認証されたユーザーのみが許可されます。</span><span class="sxs-lookup"><span data-stu-id="60e35-111">This would allow only authenticated users to the `AccountController`, except for the `Login` action, which is accessible by everyone, regardless of their authenticated or unauthenticated / anonymous status.</span></span>

> [!WARNING]
> <span data-ttu-id="60e35-112">`[AllowAnonymous]`すべての承認ステートメントをバイパスします。</span><span class="sxs-lookup"><span data-stu-id="60e35-112">`[AllowAnonymous]` bypasses all authorization statements.</span></span> <span data-ttu-id="60e35-113">`[AllowAnonymous]`との `[Authorize]` 属性を組み合わせると、 `[Authorize]` 属性は無視されます。</span><span class="sxs-lookup"><span data-stu-id="60e35-113">If you combine `[AllowAnonymous]` and any `[Authorize]` attribute, the `[Authorize]` attributes are ignored.</span></span> <span data-ttu-id="60e35-114">たとえば `[AllowAnonymous]` 、コントローラーレベルでを適用した場合、 `[Authorize]` 同じコントローラー (またはその中のアクション) の属性はすべて無視されます。</span><span class="sxs-lookup"><span data-stu-id="60e35-114">For example if you apply `[AllowAnonymous]` at the controller level, any `[Authorize]` attributes on the same controller (or on any action within it) is ignored.</span></span>

<a name="aarp"></a>

## <a name="authorize-attribute-and-razor-pages"></a><span data-ttu-id="60e35-115">属性と Razor ページを承認する</span><span class="sxs-lookup"><span data-stu-id="60e35-115">Authorize attribute and Razor Pages</span></span>

<span data-ttu-id="60e35-116">は、 <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ページハンドラーには適用でき***ません*** Razor 。</span><span class="sxs-lookup"><span data-stu-id="60e35-116">The <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> can ***not*** be applied to Razor Page handlers.</span></span> <span data-ttu-id="60e35-117">たとえば、は、、 `[Authorize]` `OnGet` `OnPost` または他のページハンドラーには適用できません。</span><span class="sxs-lookup"><span data-stu-id="60e35-117">For example, `[Authorize]` can't be applied to `OnGet`, `OnPost`, or any other page handler.</span></span> <span data-ttu-id="60e35-118">さまざまなハンドラーに対して異なる承認要件を持つページに ASP.NET Core MVC コントローラーを使用することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="60e35-118">Consider using an ASP.NET Core MVC controller for pages with different authorization requirements for different handlers.</span></span>

<span data-ttu-id="60e35-119">ページハンドラーメソッドに承認を適用するには、次の2つの方法を使用でき Razor ます。</span><span class="sxs-lookup"><span data-stu-id="60e35-119">The following two approaches can be used to apply authorization to Razor Page handler methods:</span></span>

* <span data-ttu-id="60e35-120">異なる承認を必要とするページハンドラーには、個別のページを使用します。</span><span class="sxs-lookup"><span data-stu-id="60e35-120">Use separate pages for page handlers requiring different authorization.</span></span> <span data-ttu-id="60e35-121">共有コンテンツを1つまたは複数の[部分ビュー](xref:mvc/views/partial)に移動しました。</span><span class="sxs-lookup"><span data-stu-id="60e35-121">Moved shared content into one or more [partial views](xref:mvc/views/partial).</span></span> <span data-ttu-id="60e35-122">可能な場合は、これが推奨される方法です。</span><span class="sxs-lookup"><span data-stu-id="60e35-122">When possible, this is the recommended approach.</span></span>
* <span data-ttu-id="60e35-123">共通ページを共有する必要があるコンテンツには、 [Iasyncpagefilter. Onpageハンドラ Selectionasync](xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter.OnPageHandlerSelectionAsync%2A)の一部として承認を実行するフィルターを記述します。</span><span class="sxs-lookup"><span data-stu-id="60e35-123">For content that must share a common page, write a filter that performs authorization as part of [IAsyncPageFilter.OnPageHandlerSelectionAsync](xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter.OnPageHandlerSelectionAsync%2A).</span></span> <span data-ttu-id="60e35-124">[Pageハンドラ auth](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth) GitHub プロジェクトは、次の方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="60e35-124">The [PageHandlerAuth](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth) GitHub project demonstrates this approach:</span></span>
  * <span data-ttu-id="60e35-125">[Authorizeindexpageハンドラフィルター](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth/AuthorizeIndexPageHandlerFilter.cs)は、承認フィルターを実装します。[!code-csharp[](~/security/authorization/simple/samples/3.1/PageHandlerAuth/Pages/Index.cshtml.cs?name=snippet)]</span><span class="sxs-lookup"><span data-stu-id="60e35-125">The [AuthorizeIndexPageHandlerFilter](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth/AuthorizeIndexPageHandlerFilter.cs) implements the authorization filter: [!code-csharp[](~/security/authorization/simple/samples/3.1/PageHandlerAuth/Pages/Index.cshtml.cs?name=snippet)]</span></span>

  * <span data-ttu-id="60e35-126">[[Authorizepagehandler]](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth/Pages/Index.cshtml.cs#L16)属性がページハンドラーに適用され `OnGet` ます。[!code-csharp[](~/security/authorization/simple/samples/3.1/PageHandlerAuth/AuthorizeIndexPageHandlerFilter.cs?name=snippet)]</span><span class="sxs-lookup"><span data-stu-id="60e35-126">The [[AuthorizePageHandler]](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth/Pages/Index.cshtml.cs#L16) attribute is applied to the `OnGet` page handler: [!code-csharp[](~/security/authorization/simple/samples/3.1/PageHandlerAuth/AuthorizeIndexPageHandlerFilter.cs?name=snippet)]</span></span>

> [!WARNING]
> <span data-ttu-id="60e35-127">[Pageハンドラ auth](https://github.com/pranavkm/PageHandlerAuth)サンプルアプローチでは、次の***ことはできません***。</span><span class="sxs-lookup"><span data-stu-id="60e35-127">The [PageHandlerAuth](https://github.com/pranavkm/PageHandlerAuth) sample approach does ***not***:</span></span>
> * <span data-ttu-id="60e35-128">ページ、ページモデル、またはグローバルに適用された承認属性を使用して構成します。</span><span class="sxs-lookup"><span data-stu-id="60e35-128">Compose with authorization attributes applied to the page, page model, or globally.</span></span> <span data-ttu-id="60e35-129">承認属性を作成すると、ページに1つ以上のインスタンスが適用されている場合に、認証と承認が複数回実行 `AuthorizeAttribute` `AuthorizeFilter` されます。</span><span class="sxs-lookup"><span data-stu-id="60e35-129">Composing authorization attributes results in authentication and authorization executing multiple times when you have one more `AuthorizeAttribute` or `AuthorizeFilter` instances also applied to the page.</span></span>
> * <span data-ttu-id="60e35-130">ASP.NET Core 認証および承認システムの残りの部分と連携して作業します。</span><span class="sxs-lookup"><span data-stu-id="60e35-130">Work in conjunction with the rest of ASP.NET Core authentication and authorization system.</span></span> <span data-ttu-id="60e35-131">この方法を使用してアプリケーションに対して正しく動作することを確認する必要があります。</span><span class="sxs-lookup"><span data-stu-id="60e35-131">You must verify using this approach works correctly for your application.</span></span>

<span data-ttu-id="60e35-132">ページハンドラーでをサポートする予定はありません `AuthorizeAttribute` Razor 。</span><span class="sxs-lookup"><span data-stu-id="60e35-132">There are no plans to support the `AuthorizeAttribute` on Razor Page handlers.</span></span> 
