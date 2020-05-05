---
title: ASP.NET Core 2.1 MVC SameSite cookie のサンプル
author: rick-anderson
description: ASP.NET Core 2.1 MVC SameSite cookie のサンプル
monikerRange: '>= aspnetcore-2.1 < aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/03/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/samesite/mvc21
ms.openlocfilehash: 6a53c0d3c0a314c4137f071cf50062182b654658
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777307"
---
# <a name="aspnet-core-21-mvc-samesite-cookie-sample"></a><span data-ttu-id="7a0aa-103">ASP.NET Core 2.1 MVC SameSite cookie のサンプル</span><span class="sxs-lookup"><span data-stu-id="7a0aa-103">ASP.NET Core 2.1 MVC SameSite cookie sample</span></span>

<span data-ttu-id="7a0aa-104">ASP.NET Core 2.1 には[SameSite](https://www.owasp.org/index.php/SameSite)属性のサポートが組み込まれていますが、元の標準に書き込まれました。</span><span class="sxs-lookup"><span data-stu-id="7a0aa-104">ASP.NET Core 2.1 has built-in support for the [SameSite](https://www.owasp.org/index.php/SameSite) attribute, but it was written to the original standard.</span></span> <span data-ttu-id="7a0aa-105">[パッチを適用](https://github.com/dotnet/aspnetcore/issues/8212)した動作に`SameSite.None`よって、の意味が変更され`None`、値がまったく出力されるのではなく、の値を持つ sameSite 属性が出力されます。</span><span class="sxs-lookup"><span data-stu-id="7a0aa-105">The [patched behavior](https://github.com/dotnet/aspnetcore/issues/8212) changed the meaning of `SameSite.None` to emit the sameSite attribute with a value of `None`, rather than not emit the value at all.</span></span> <span data-ttu-id="7a0aa-106">値を出力しない場合は、cookie の`SameSite`プロパティを-1 に設定します。</span><span class="sxs-lookup"><span data-stu-id="7a0aa-106">If you want to not emit the value you can set the `SameSite` property on a cookie to -1.</span></span>

## <a name="writing-the-samesite-attribute"></a><a name="sampleCode"></a><span data-ttu-id="7a0aa-107">SameSite 属性の書き込み</span><span class="sxs-lookup"><span data-stu-id="7a0aa-107">Writing the SameSite attribute</span></span>

<span data-ttu-id="7a0aa-108">次に、SameSite 属性をクッキーに書き込む方法の例を示します。</span><span class="sxs-lookup"><span data-stu-id="7a0aa-108">Following is an example of how to write a SameSite attribute on a cookie:</span></span>

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

## <a name="setting-cookie-authentication-and-session-state-cookies"></a><span data-ttu-id="7a0aa-109">Cookie 認証およびセッション状態 cookie の設定</span><span class="sxs-lookup"><span data-stu-id="7a0aa-109">Setting Cookie Authentication and Session State cookies</span></span>

<span data-ttu-id="7a0aa-110">Cookie 認証、セッション状態、および[その他のさまざまなコンポーネント](https://docs.microsoft.com/aspnet/core/security/samesite?view=aspnetcore-2.1)は、cookie オプションを使用して sameSite オプションを設定します。たとえば、</span><span class="sxs-lookup"><span data-stu-id="7a0aa-110">Cookie authentication, session state and [various other components](https://docs.microsoft.com/aspnet/core/security/samesite?view=aspnetcore-2.1) set their sameSite options via Cookie options, for example</span></span>

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

<span data-ttu-id="7a0aa-111">上記のコードでは、cookie 認証とセッション状態の両方で、sameSite `None`属性をに設定し、 `None`値を使用して属性を出力し、さらに Secure 属性を true に設定しています。</span><span class="sxs-lookup"><span data-stu-id="7a0aa-111">In the preceding code, both cookie authentication and session state set their sameSite attribute to `None`, emitting the attribute with a `None` value, and also set the Secure attribute to true.</span></span>

### <a name="run-the-sample"></a><span data-ttu-id="7a0aa-112">サンプルを実行する</span><span class="sxs-lookup"><span data-stu-id="7a0aa-112">Run the sample</span></span>

<span data-ttu-id="7a0aa-113">[サンプルプロジェクト](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC)を実行する場合は、最初のページにブラウザーデバッガーを読み込み、それを使用してサイトの cookie のコレクションを表示します。</span><span class="sxs-lookup"><span data-stu-id="7a0aa-113">If you run the [sample project](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC), load your browser debugger on the initial page and use it to view the cookie collection for the site.</span></span> <span data-ttu-id="7a0aa-114">これを行うには、Edge で`F12` [] を`Application`クリックし、タブを選択して`Cookies` 、 `Storage`セクションのオプションの下にあるサイトの URL をクリックします。</span><span class="sxs-lookup"><span data-stu-id="7a0aa-114">To do so in Edge and Chrome press `F12` then select the `Application` tab and click the site URL under the `Cookies` option in the `Storage` section.</span></span>

![ブラウザーデバッガーの Cookie の一覧](BrowserDebugger.png)

<span data-ttu-id="7a0aa-116">上の図から、[Create SameSite Cookie] ボタンをクリックしたときにサンプルによって作成された cookie の SameSite 属性値`Lax`が、[サンプルコード](#sampleCode)で設定されている値と一致していることがわかります。</span><span class="sxs-lookup"><span data-stu-id="7a0aa-116">You can see from the image above that the cookie created by the sample when you click the "Create SameSite Cookie" button has a SameSite attribute value of `Lax`, matching the value set in the [sample code](#sampleCode).</span></span>

## <a name="intercepting-cookies"></a><a name="interception"></a><span data-ttu-id="7a0aa-117">Cookie の傍受</span><span class="sxs-lookup"><span data-stu-id="7a0aa-117">Intercepting cookies</span></span>

<span data-ttu-id="7a0aa-118">Cookie をインターセプトするには、ユーザーのブラウザーエージェントでのサポートに応じて none 値を調整するために、 `CookiePolicy`ミドルウェアを使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7a0aa-118">In order to intercept cookies, to adjust the none value according to its support in the user's browser agent you must use the `CookiePolicy` middleware.</span></span> <span data-ttu-id="7a0aa-119">クッキーを書き込み、内で`ConfigureServices()`構成されるコンポーネントの**前に**、これを http 要求パイプラインに配置する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7a0aa-119">This must be placed into the http request pipeline **before** any components that write cookies and configured within `ConfigureServices()`.</span></span>

<span data-ttu-id="7a0aa-120">パイプラインに挿入するには、 `app.UseCookiePolicy()` Startup.cs の`Configure(IApplicationBuilder, IHostingEnvironment)`メソッドで[Startup.cs](https://github.com/blowdart/AspNetSameSiteSamples/blob/master/AspNetCore21MVC/Startup.cs)を使用します。</span><span class="sxs-lookup"><span data-stu-id="7a0aa-120">To insert it into the pipeline use `app.UseCookiePolicy()` in the `Configure(IApplicationBuilder, IHostingEnvironment)` method in [Startup.cs](https://github.com/blowdart/AspNetSameSiteSamples/blob/master/AspNetCore21MVC/Startup.cs).</span></span> <span data-ttu-id="7a0aa-121">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="7a0aa-121">For example:</span></span>

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

<span data-ttu-id="7a0aa-122">次に、 `ConfigureServices(IServiceCollection services)` cookie が追加または削除されたときにヘルパークラスを呼び出すように cookie ポリシーを構成します。</span><span class="sxs-lookup"><span data-stu-id="7a0aa-122">Then in the `ConfigureServices(IServiceCollection services)` configure the cookie policy to call out to a helper class when cookies are appended or deleted.</span></span> <span data-ttu-id="7a0aa-123">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="7a0aa-123">For example:</span></span>

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

<span data-ttu-id="7a0aa-124">ヘルパー関数`CheckSameSite(HttpContext, CookieOptions)`:</span><span class="sxs-lookup"><span data-stu-id="7a0aa-124">The helper function `CheckSameSite(HttpContext, CookieOptions)`:</span></span>

* <span data-ttu-id="7a0aa-125">は、要求に cookie が追加されたとき、または要求から削除されたときに呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="7a0aa-125">Is called when cookies are appended to the request or deleted from the request.</span></span>
* <span data-ttu-id="7a0aa-126">`SameSite`プロパティがに`None`設定されているかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="7a0aa-126">Checks to see if the `SameSite` property is set to `None`.</span></span>
* <span data-ttu-id="7a0aa-127">が`SameSite`に`None`設定されていて、現在のユーザーエージェントが none 属性値をサポートしていないことがわかっている場合。</span><span class="sxs-lookup"><span data-stu-id="7a0aa-127">If `SameSite` is set to `None` and the current user agent is known to not support the none attribute value.</span></span> <span data-ttu-id="7a0aa-128">このチェックは、 [SameSiteSupport](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/samesite/sample/snippets/SameSiteSupport.cs)クラスを使用して行います。</span><span class="sxs-lookup"><span data-stu-id="7a0aa-128">The check is done using the [SameSiteSupport](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/samesite/sample/snippets/SameSiteSupport.cs) class:</span></span>
  * <span data-ttu-id="7a0aa-129">プロパティ`SameSite`をに設定して値を出力しないように設定します。`(SameSiteMode)(-1)`</span><span class="sxs-lookup"><span data-stu-id="7a0aa-129">Sets `SameSite` to not emit the value by setting the property to `(SameSiteMode)(-1)`</span></span>

## <a name="targeting-net-framework"></a><span data-ttu-id="7a0aa-130">.NET Framework のターゲット設定</span><span class="sxs-lookup"><span data-stu-id="7a0aa-130">Targeting .NET Framework</span></span>

<span data-ttu-id="7a0aa-131">ASP.NET Core と System.web (ASP.NET Classic) には、SameSite の独立した実装があります。</span><span class="sxs-lookup"><span data-stu-id="7a0aa-131">ASP.NET Core and System.Web (ASP.NET Classic) have independent implementations of SameSite.</span></span> <span data-ttu-id="7a0aa-132">.NET Framework 用の SameSite KB 修正プログラムは、ASP.NET Core を使用していない場合や、System.web SameSite minimum Framework バージョン要件 (.NET 4.7.2) が ASP.NET Core に適用されている場合は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="7a0aa-132">The SameSite KB patches for .NET Framework are not required if using ASP.NET Core nor does the System.Web SameSite minimum framework version requirement (.NET 4.7.2) apply to ASP.NET Core.</span></span>

<span data-ttu-id="7a0aa-133">.NET で ASP.NET Core するには、nuget パッケージの依存関係を更新して適切な修正プログラムを取得する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7a0aa-133">ASP.NET Core on .NET requires updating nuget package dependencies to get the appropriate fixes.</span></span>

<span data-ttu-id="7a0aa-134">.NET Framework の ASP.NET Core の変更を取得するには、修正プログラムが適用されたパッケージとバージョン (2.1.14 以降の2.1 バージョン) への直接参照があることを確認します。</span><span class="sxs-lookup"><span data-stu-id="7a0aa-134">To get the ASP.NET Core changes for .NET Framework ensure that you have a direct reference to the patched packages and versions (2.1.14 or later 2.1 versions).</span></span>

```xml
<PackageReference Include="Microsoft.Net.Http.Headers" Version="2.1.14" />
<PackageReference Include="Microsoft.AspNetCore.CookiePolicy" Version="2.1.14" />
```

### <a name="more-information"></a><span data-ttu-id="7a0aa-135">詳細情報</span><span class="sxs-lookup"><span data-stu-id="7a0aa-135">More Information</span></span>
 
<span data-ttu-id="7a0aa-136">[Chrome 更新プログラム](https://www.chromium.org/updates/same-site)
[ASP.NET Core SameSite ドキュメント](https://docs.microsoft.com/aspnet/core/security/samesite?view=aspnetcore-2.1)
[ASP.NET Core 2.1 SameSite 変更アナウンス](https://github.com/dotnet/aspnetcore/issues/8212)</span><span class="sxs-lookup"><span data-stu-id="7a0aa-136">[Chrome Updates](https://www.chromium.org/updates/same-site)
[ASP.NET Core SameSite Documentation](https://docs.microsoft.com/aspnet/core/security/samesite?view=aspnetcore-2.1)
[ASP.NET Core 2.1 SameSite Change Announcement](https://github.com/dotnet/aspnetcore/issues/8212)</span></span>