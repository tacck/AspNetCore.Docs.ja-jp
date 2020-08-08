---
title: ASP.NET Core 2.1 MVC SameSite cookie サンプル
author: rick-anderson
description: ASP.NET Core 2.1 MVC SameSite cookie サンプル
monikerRange: '>= aspnetcore-2.1 < aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/03/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/samesite/mvc21
ms.openlocfilehash: 4285432d48ba11b5069d109c5667192a99fe115e
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021784"
---
# <a name="aspnet-core-21-mvc-samesite-no-loccookie-sample"></a><span data-ttu-id="4682f-103">ASP.NET Core 2.1 MVC SameSite cookie サンプル</span><span class="sxs-lookup"><span data-stu-id="4682f-103">ASP.NET Core 2.1 MVC SameSite cookie sample</span></span>

<span data-ttu-id="4682f-104">ASP.NET Core 2.1 には[SameSite](https://www.owasp.org/index.php/SameSite)属性のサポートが組み込まれていますが、元の標準に書き込まれました。</span><span class="sxs-lookup"><span data-stu-id="4682f-104">ASP.NET Core 2.1 has built-in support for the [SameSite](https://www.owasp.org/index.php/SameSite) attribute, but it was written to the original standard.</span></span> <span data-ttu-id="4682f-105">[パッチを適用](https://github.com/dotnet/aspnetcore/issues/8212)した動作によって、の意味が変更され、 `SameSite.None` `None` 値がまったく出力されるのではなく、の値を持つ sameSite 属性が出力されます。</span><span class="sxs-lookup"><span data-stu-id="4682f-105">The [patched behavior](https://github.com/dotnet/aspnetcore/issues/8212) changed the meaning of `SameSite.None` to emit the sameSite attribute with a value of `None`, rather than not emit the value at all.</span></span> <span data-ttu-id="4682f-106">値を出力しない場合は `SameSite` 、のプロパティを cookie -1 に設定します。</span><span class="sxs-lookup"><span data-stu-id="4682f-106">If you want to not emit the value you can set the `SameSite` property on a cookie to -1.</span></span>

[!INCLUDE[](~/includes/SameSiteIdentity.md)]

## <a name="writing-the-samesite-attribute"></a><a name="sampleCode"></a><span data-ttu-id="4682f-107">SameSite 属性の書き込み</span><span class="sxs-lookup"><span data-stu-id="4682f-107">Writing the SameSite attribute</span></span>

<span data-ttu-id="4682f-108">SameSite 属性をに書き込む方法の例を次に示し cookie ます。</span><span class="sxs-lookup"><span data-stu-id="4682f-108">Following is an example of how to write a SameSite attribute on a cookie:</span></span>

```c#
var cookieOptions = new CookieOptions
{
    // Set the secure flag, which Chrome's changes will require for SameSite none.
    // Note this will also require you to be running on HTTPS
    Secure = true,

    // Set the cookie to HTTP only which is good practice unless you really do need
    // to access it client side in scripts.
    HttpOnly = true,

    // Add the SameSite attribute, this will emit the attribute with a value of none.
    // To not emit the attribute at all set the SameSite property to (SameSiteMode)(-1).
    SameSite = SameSiteMode.None
};

// Add the cookie to the response cookie collection
Response.Cookies.Append(CookieName, "cookieValue", cookieOptions);
```

## <a name="setting-no-loccookie-authentication-and-session-state-no-loccookies"></a><span data-ttu-id="4682f-109">Cookie認証およびセッション状態の cookie 設定</span><span class="sxs-lookup"><span data-stu-id="4682f-109">Setting Cookie Authentication and Session State cookies</span></span>

<span data-ttu-id="4682f-110">Cookie認証、セッション状態、および[その他のさまざまなコンポーネント](https://docs.microsoft.com/aspnet/core/security/samesite?view=aspnetcore-2.1)は、オプションを使用して sameSite オプションを設定し Cookie ます。たとえば、</span><span class="sxs-lookup"><span data-stu-id="4682f-110">Cookie authentication, session state and [various other components](https://docs.microsoft.com/aspnet/core/security/samesite?view=aspnetcore-2.1) set their sameSite options via Cookie options, for example</span></span>

```c#
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.Cookie.SameSite = SameSiteMode.None;
        options.Cookie.SecurePolicy = CookieSecurePolicy.Always;
        options.Cookie.IsEssential = true;
    });

services.AddSession(options =>
{
    options.Cookie.SameSite = SameSiteMode.None;
    options.Cookie.SecurePolicy = CookieSecurePolicy.Always;
    options.Cookie.IsEssential = true;
});
```

<span data-ttu-id="4682f-111">上記のコードでは、 cookie 認証とセッション状態の両方で sameSite 属性をに設定 `None` し、値を使用して属性を出力 `None` し、さらに Secure 属性を true に設定しています。</span><span class="sxs-lookup"><span data-stu-id="4682f-111">In the preceding code, both cookie authentication and session state set their sameSite attribute to `None`, emitting the attribute with a `None` value, and also set the Secure attribute to true.</span></span>

### <a name="run-the-sample"></a><span data-ttu-id="4682f-112">サンプルを実行する</span><span class="sxs-lookup"><span data-stu-id="4682f-112">Run the sample</span></span>

<span data-ttu-id="4682f-113">[サンプルプロジェクト](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC)を実行する場合は、最初のページにブラウザーデバッガーを読み込み、それを使用して cookie サイトのコレクションを表示します。</span><span class="sxs-lookup"><span data-stu-id="4682f-113">If you run the [sample project](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC), load your browser debugger on the initial page and use it to view the cookie collection for the site.</span></span> <span data-ttu-id="4682f-114">これを行うには、Edge で [] をクリックし、タブを選択して、 `F12` セクションの `Application` オプションの下にあるサイトの URL をクリックし `Cookies` `Storage` ます。</span><span class="sxs-lookup"><span data-stu-id="4682f-114">To do so in Edge and Chrome press `F12` then select the `Application` tab and click the site URL under the `Cookies` option in the `Storage` section.</span></span>

![Browser Debugger::: no loc (Cookie)::: List](BrowserDebugger.png)

<span data-ttu-id="4682f-116">上の図から、 cookie [Create SameSite] \ (作成 \) ボタンをクリックしたときにサンプルによって作成されたが、 Cookie `Lax` [サンプルコード](#sampleCode)で設定されている値と一致する SameSite 属性値を持っていることがわかります。</span><span class="sxs-lookup"><span data-stu-id="4682f-116">You can see from the image above that the cookie created by the sample when you click the "Create SameSite Cookie" button has a SameSite attribute value of `Lax`, matching the value set in the [sample code](#sampleCode).</span></span>

## <a name="intercepting-no-loccookies"></a><a name="interception"></a><span data-ttu-id="4682f-117">をインターセプトしています cookie</span><span class="sxs-lookup"><span data-stu-id="4682f-117">Intercepting cookies</span></span>

<span data-ttu-id="4682f-118">S をインターセプトするために cookie 、ユーザーのブラウザーエージェントでのサポートに応じて none 値を調整するには、ミドルウェアを使用する必要があり `CookiePolicy` ます。</span><span class="sxs-lookup"><span data-stu-id="4682f-118">In order to intercept cookies, to adjust the none value according to its support in the user's browser agent you must use the `CookiePolicy` middleware.</span></span> <span data-ttu-id="4682f-119">これは、を書き込み、内で構成されるコンポーネントの**前に**、http 要求パイプラインに配置する必要があり cookie `ConfigureServices()` ます。</span><span class="sxs-lookup"><span data-stu-id="4682f-119">This must be placed into the http request pipeline **before** any components that write cookies and configured within `ConfigureServices()`.</span></span>

<span data-ttu-id="4682f-120">パイプラインに挿入するには、 `app.UseCookiePolicy()` `Configure(IApplicationBuilder, IHostingEnvironment)` [Startup.cs](https://github.com/blowdart/AspNetSameSiteSamples/blob/master/AspNetCore21MVC/Startup.cs)のメソッドでを使用します。</span><span class="sxs-lookup"><span data-stu-id="4682f-120">To insert it into the pipeline use `app.UseCookiePolicy()` in the `Configure(IApplicationBuilder, IHostingEnvironment)` method in [Startup.cs](https://github.com/blowdart/AspNetSameSiteSamples/blob/master/AspNetCore21MVC/Startup.cs).</span></span> <span data-ttu-id="4682f-121">例:</span><span class="sxs-lookup"><span data-stu-id="4682f-121">For example:</span></span>

```c#
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
       app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseExceptionHandler("/Home/Error");
        app.UseHsts();
    }

    app.UseHttpsRedirection();
    app.UseStaticFiles();
    app.UseCookiePolicy();
    app.UseAuthentication();
    app.UseSession();

    app.UseMvc(routes =>
    {
        routes.MapRoute(
            name: "default",
            template: "{controller=Home}/{action=Index}/{id?}");
    });
}
```

<span data-ttu-id="4682f-122">次に、が `ConfigureServices(IServiceCollection services)` cookie 追加または削除されたときにヘルパークラスを呼び出すようにポリシーを構成し cookie ます。</span><span class="sxs-lookup"><span data-stu-id="4682f-122">Then in the `ConfigureServices(IServiceCollection services)` configure the cookie policy to call out to a helper class when cookies are appended or deleted.</span></span> <span data-ttu-id="4682f-123">例:</span><span class="sxs-lookup"><span data-stu-id="4682f-123">For example:</span></span>

```c#
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<CookiePolicyOptions>(options =>
    {
        options.CheckConsentNeeded = context => true;
        options.MinimumSameSitePolicy = SameSiteMode.None;
        options.OnAppendCookie = cookieContext =>
            CheckSameSite(cookieContext.Context, cookieContext.CookieOptions);
        options.OnDeleteCookie = cookieContext =>
            CheckSameSite(cookieContext.Context, cookieContext.CookieOptions);
    });
}

private void CheckSameSite(HttpContext httpContext, CookieOptions options)
{
    if (options.SameSite == SameSiteMode.None)
    {
        var userAgent = httpContext.Request.Headers["User-Agent"].ToString();
        if (SameSite.BrowserDetection.DisallowsSameSiteNone(userAgent))
        {
            options.SameSite = (SameSiteMode)(-1);
        }
    }
}
```

<span data-ttu-id="4682f-124">ヘルパー関数 `CheckSameSite(HttpContext, CookieOptions)` :</span><span class="sxs-lookup"><span data-stu-id="4682f-124">The helper function `CheckSameSite(HttpContext, CookieOptions)`:</span></span>

* <span data-ttu-id="4682f-125">は、が要求に追加されたとき、または要求から削除されたときに呼び出され cookie ます。</span><span class="sxs-lookup"><span data-stu-id="4682f-125">Is called when cookies are appended to the request or deleted from the request.</span></span>
* <span data-ttu-id="4682f-126">プロパティがに設定されているかどうかを確認し `SameSite` `None` ます。</span><span class="sxs-lookup"><span data-stu-id="4682f-126">Checks to see if the `SameSite` property is set to `None`.</span></span>
* <span data-ttu-id="4682f-127">`SameSite`がに設定され `None` ていて、現在のユーザーエージェントが none 属性値をサポートしていないことがわかっている場合。</span><span class="sxs-lookup"><span data-stu-id="4682f-127">If `SameSite` is set to `None` and the current user agent is known to not support the none attribute value.</span></span> <span data-ttu-id="4682f-128">このチェックは、 [SameSiteSupport](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/samesite/sample/snippets/SameSiteSupport.cs)クラスを使用して行います。</span><span class="sxs-lookup"><span data-stu-id="4682f-128">The check is done using the [SameSiteSupport](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/samesite/sample/snippets/SameSiteSupport.cs) class:</span></span>
  * <span data-ttu-id="4682f-129">`SameSite`プロパティをに設定して値を出力しないように設定します。`(SameSiteMode)(-1)`</span><span class="sxs-lookup"><span data-stu-id="4682f-129">Sets `SameSite` to not emit the value by setting the property to `(SameSiteMode)(-1)`</span></span>

## <a name="targeting-net-framework"></a><span data-ttu-id="4682f-130">.NET Framework のターゲット設定</span><span class="sxs-lookup"><span data-stu-id="4682f-130">Targeting .NET Framework</span></span>

<span data-ttu-id="4682f-131">ASP.NET Core と System.web (ASP.NET Classic) には、SameSite の独立した実装があります。</span><span class="sxs-lookup"><span data-stu-id="4682f-131">ASP.NET Core and System.Web (ASP.NET Classic) have independent implementations of SameSite.</span></span> <span data-ttu-id="4682f-132">.NET Framework 用の SameSite KB 修正プログラムは、ASP.NET Core を使用していない場合や、System.web SameSite minimum Framework バージョン要件 (.NET 4.7.2) が ASP.NET Core に適用されている場合は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="4682f-132">The SameSite KB patches for .NET Framework are not required if using ASP.NET Core nor does the System.Web SameSite minimum framework version requirement (.NET 4.7.2) apply to ASP.NET Core.</span></span>

<span data-ttu-id="4682f-133">.NET で ASP.NET Core するには、nuget パッケージの依存関係を更新して適切な修正プログラムを取得する必要があります。</span><span class="sxs-lookup"><span data-stu-id="4682f-133">ASP.NET Core on .NET requires updating nuget package dependencies to get the appropriate fixes.</span></span>

<span data-ttu-id="4682f-134">.NET Framework の ASP.NET Core の変更を取得するには、修正プログラムが適用されたパッケージとバージョン (2.1.14 以降の2.1 バージョン) への直接参照があることを確認します。</span><span class="sxs-lookup"><span data-stu-id="4682f-134">To get the ASP.NET Core changes for .NET Framework ensure that you have a direct reference to the patched packages and versions (2.1.14 or later 2.1 versions).</span></span>

```xml
<PackageReference Include="Microsoft.Net.Http.Headers" Version="2.1.14" />
<PackageReference Include="Microsoft.AspNetCore.CookiePolicy" Version="2.1.14" />
```

### <a name="more-information"></a><span data-ttu-id="4682f-135">説明</span><span class="sxs-lookup"><span data-stu-id="4682f-135">More Information</span></span>
 
<span data-ttu-id="4682f-136">[Chrome の更新](https://www.chromium.org/updates/same-site) 
[ASP.NET Core SameSite のドキュメント](https://docs.microsoft.com/aspnet/core/security/samesite?view=aspnetcore-2.1) 
[ASP.NET Core 2.1 SameSite 変更のお知らせ](https://github.com/dotnet/aspnetcore/issues/8212)</span><span class="sxs-lookup"><span data-stu-id="4682f-136">[Chrome Updates](https://www.chromium.org/updates/same-site)
[ASP.NET Core SameSite Documentation](https://docs.microsoft.com/aspnet/core/security/samesite?view=aspnetcore-2.1)
[ASP.NET Core 2.1 SameSite Change Announcement](https://github.com/dotnet/aspnetcore/issues/8212)</span></span>