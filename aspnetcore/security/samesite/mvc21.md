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
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/samesite/mvc21
ms.openlocfilehash: ce301cd7e2cbfbfc724d78bd5734dff231d0ab93
ms.sourcegitcommit: 66fca14611eba141d455fe0bd2c37803062e439c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2020
ms.locfileid: "85944737"
---
# <a name="aspnet-core-21-mvc-samesite-cookie-sample"></a>ASP.NET Core 2.1 MVC SameSite cookie のサンプル

ASP.NET Core 2.1 には[SameSite](https://www.owasp.org/index.php/SameSite)属性のサポートが組み込まれていますが、元の標準に書き込まれました。 [パッチを適用](https://github.com/dotnet/aspnetcore/issues/8212)した動作によって、の意味が変更され、 `SameSite.None` `None` 値がまったく出力されるのではなく、の値を持つ sameSite 属性が出力されます。 値を出力しない場合は、cookie のプロパティを `SameSite` -1 に設定します。

[!INCLUDE[](~/includes/SameSiteIdentity.md)]

## <a name="writing-the-samesite-attribute"></a><a name="sampleCode"></a>SameSite 属性の書き込み

次に、SameSite 属性をクッキーに書き込む方法の例を示します。

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

## <a name="setting-cookie-authentication-and-session-state-cookies"></a>Cookie 認証およびセッション状態 cookie の設定

Cookie 認証、セッション状態、および[その他のさまざまなコンポーネント](https://docs.microsoft.com/aspnet/core/security/samesite?view=aspnetcore-2.1)は、cookie オプションを使用して sameSite オプションを設定します。たとえば、

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

上記のコードでは、cookie 認証とセッション状態の両方で、sameSite 属性をに設定し `None` 、値を使用して属性を出力 `None` し、さらに Secure 属性を true に設定しています。

### <a name="run-the-sample"></a>サンプルを実行する

[サンプルプロジェクト](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC)を実行する場合は、最初のページにブラウザーデバッガーを読み込み、それを使用してサイトの cookie のコレクションを表示します。 これを行うには、Edge で [] をクリックし、タブを選択して、 `F12` セクションの `Application` オプションの下にあるサイトの URL をクリックし `Cookies` `Storage` ます。

![ブラウザーデバッガーの Cookie の一覧](BrowserDebugger.png)

上の図から、[Create SameSite Cookie] ボタンをクリックしたときにサンプルによって作成された cookie の SameSite 属性値が、 `Lax` [サンプルコード](#sampleCode)で設定されている値と一致していることがわかります。

## <a name="intercepting-cookies"></a><a name="interception"></a>Cookie の傍受

Cookie をインターセプトするには、ユーザーのブラウザーエージェントでのサポートに応じて none 値を調整するために、ミドルウェアを使用する必要があり `CookiePolicy` ます。 クッキーを書き込み、内で構成されるコンポーネントの**前に**、これを http 要求パイプラインに配置する必要があり `ConfigureServices()` ます。

パイプラインに挿入するには、 `app.UseCookiePolicy()` `Configure(IApplicationBuilder, IHostingEnvironment)` [Startup.cs](https://github.com/blowdart/AspNetSameSiteSamples/blob/master/AspNetCore21MVC/Startup.cs)のメソッドでを使用します。 次に例を示します。

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

次に、cookie `ConfigureServices(IServiceCollection services)` が追加または削除されたときにヘルパークラスを呼び出すように cookie ポリシーを構成します。 次に例を示します。

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

ヘルパー関数 `CheckSameSite(HttpContext, CookieOptions)` :

* は、要求に cookie が追加されたとき、または要求から削除されたときに呼び出されます。
* プロパティがに設定されているかどうかを確認し `SameSite` `None` ます。
* `SameSite`がに設定され `None` ていて、現在のユーザーエージェントが none 属性値をサポートしていないことがわかっている場合。 このチェックは、 [SameSiteSupport](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/samesite/sample/snippets/SameSiteSupport.cs)クラスを使用して行います。
  * `SameSite`プロパティをに設定して値を出力しないように設定します。`(SameSiteMode)(-1)`

## <a name="targeting-net-framework"></a>.NET Framework のターゲット設定

ASP.NET Core と System.web (ASP.NET Classic) には、SameSite の独立した実装があります。 .NET Framework 用の SameSite KB 修正プログラムは、ASP.NET Core を使用していない場合や、System.web SameSite minimum Framework バージョン要件 (.NET 4.7.2) が ASP.NET Core に適用されている場合は必要ありません。

.NET で ASP.NET Core するには、nuget パッケージの依存関係を更新して適切な修正プログラムを取得する必要があります。

.NET Framework の ASP.NET Core の変更を取得するには、修正プログラムが適用されたパッケージとバージョン (2.1.14 以降の2.1 バージョン) への直接参照があることを確認します。

```xml
<PackageReference Include="Microsoft.Net.Http.Headers" Version="2.1.14" />
<PackageReference Include="Microsoft.AspNetCore.CookiePolicy" Version="2.1.14" />
```

### <a name="more-information"></a>詳細情報
 
[Chrome の更新](https://www.chromium.org/updates/same-site) 
[ASP.NET Core SameSite のドキュメント](https://docs.microsoft.com/aspnet/core/security/samesite?view=aspnetcore-2.1) 
[ASP.NET Core 2.1 SameSite 変更のお知らせ](https://github.com/dotnet/aspnetcore/issues/8212)