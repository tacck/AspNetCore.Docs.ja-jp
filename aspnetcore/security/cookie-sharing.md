---
title: ASP.NET アプリ間での認証の共有 cookie
author: rick-anderson
description: cookieASP.NET 4.x および ASP.NET Core アプリ間で認証を共有する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/05/2019
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
uid: security/cookie-sharing
ms.openlocfilehash: 0d43bbbc44015aff040b12dfacb260fe50492e54
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252995"
---
# <a name="share-authentication-no-loccookies-among-aspnet-apps"></a>ASP.NET アプリ間での認証の共有 cookie

作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)

多くの場合、web サイトは、連携して動作する個々の web アプリで構成されます。 シングルサインオン (SSO) エクスペリエンスを提供するには、サイト内の web アプリで認証を共有する必要があり cookie ます。 このシナリオをサポートするために、データ保護スタックは Katana cookie 認証と ASP.NET Core 認証チケットを共有できるようにし cookie ます。

次の例を参照してください。

* 認証 cookie 名は、共通の値に設定され `.AspNet.SharedCookie` ます。
* `AuthenticationType`は、 `Identity.Application` 明示的にまたは既定でに設定されます。
* 共通のアプリ名は、データ保護システムがデータ保護キー () を共有できるようにするために使用され `SharedCookieApp` ます。
* `Identity.Application` は、認証スキームとして使用されます。 どのようなスキームを使用する場合でも、既定のスキームとして、または明示的に設定することによって、共有アプリ *内および* 共有アプリ全体で一貫して使用する必要があり cookie ます。 スキームは、の暗号化と復号化に使用される cookie ため、一貫したスキームをアプリ間で使用する必要があります。
* 一般的な [データ保護キー](xref:security/data-protection/implementation/key-management) の保存場所が使用されます。
  * ASP.NET Core アプリでは、を <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> 使用してキーの格納場所を設定します。
  * .NET Framework アプリでは、 Cookie 認証ミドルウェアはの実装を使用 <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider> します。 `DataProtectionProvider` には、認証ペイロードデータの暗号化と復号化を行うためのデータ保護サービスが用意されて cookie います。 インスタンスは、 `DataProtectionProvider` アプリの他の部分で使用されるデータ保護システムから分離されています。 [Dataprotectionprovider. Create (DirectoryInfo, Action \<IDataProtectionBuilder> )](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) は、 <xref:System.IO.DirectoryInfo> データ保護キーの保存場所を指定するを受け入れます。
* `DataProtectionProvider` には、 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) NuGet パッケージが必要です。
  * ASP.NET Core 2.x アプリで、 [AspNetCore メタパッケージ](xref:fundamentals/metapackage-app)を参照します。
  * .NET Framework アプリで、 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/)へのパッケージ参照を追加します。
* <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> 共通アプリ名を設定します。

## <a name="share-authentication-no-loccookies-with-no-locaspnet-core-identity"></a>認証を共有する cookieASP.NET Core Identity

ASP.NET Core Identity を使うとき:

* データ保護キーとアプリ名は、アプリ間で共有する必要があります。 共通のキー格納場所は、次の例のメソッドに提供され <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> ます。 <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*>共通の共有アプリ名を構成するには、を使用し `SharedCookieApp` ます (次の例を参考にしてください)。 詳細については、「<xref:security/data-protection/configuration/overview>」を参照してください。
* 拡張メソッドを使用して、 <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*> のデータ保護サービスを設定し cookie ます。
* 既定の認証の種類はです `Identity.Application` 。

`Startup.ConfigureServices`の場合:

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
});
```

**注:** 前の手順では、 `ITicketStore` () は使用できません `CookieAuthenticationOptions.SessionStore` 。  詳細については、次を参照してください。[この GitHub の問題](https://github.com/dotnet/AspNetCore.Docs/issues/21163)します。

## <a name="share-authentication-no-loccookies-without-no-locaspnet-core-identity"></a>認証を共有 cookie しない ASP.NET Core Identity

を使用せずにを直接使用する場合 cookie ASP.NET Core Identity は、でデータ保護と認証を構成し `Startup.ConfigureServices` ます。 次の例では、認証の種類がに設定されてい `Identity.Application` ます。

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.AddAuthentication("Identity.Application")
    .AddCookie("Identity.Application", options =>
    {
        options.Cookie.Name = ".AspNet.SharedCookie";
    });
```

## <a name="share-no-loccookies-across-different-base-paths"></a>cookie異なるベースパス間で s を共有する

認証では、 cookie 既定値として[HttpRequest](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase)が使用されます[ Cookie 。パス](xref:Microsoft.AspNetCore.Http.CookieBuilder.Path)。 アプリの cookie を異なる基本パスで共有する必要がある場合は、を `Path` オーバーライドする必要があります。

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
    options.Cookie.Path = "/";
});
```

## <a name="share-no-loccookies-across-subdomains"></a>サブ cookie ドメイン間で s を共有する

をサブドメイン間で共有するアプリをホストする場合は cookie 、で共通のドメインを指定し[ Cookie ます。ドメイン](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain)プロパティ。 やなどのでアプリ間で s を共有するには、をとし cookie `contoso.com` `first_subdomain.contoso.com` `second_subdomain.contoso.com` て指定し `Cookie.Domain` `.contoso.com` ます。

```csharp
options.Cookie.Domain = ".contoso.com";
```

## <a name="encrypt-data-protection-keys-at-rest"></a>保存時のデータ保護キーの暗号化

運用環境のデプロイの場合は、 `DataProtectionProvider` DPAPI または X509Certificate を使用して保存時のキーを暗号化するようにを構成します。 詳細については、「<xref:security/data-protection/implementation/key-encryption-at-rest>」を参照してください。 次の例では、証明書の拇印がに提供されてい <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*> ます。

```csharp
services.AddDataProtection()
    .ProtectKeysWithCertificate("{CERTIFICATE THUMBPRINT}");
```

## <a name="share-authentication-no-loccookies-between-aspnet-4x-and-aspnet-core-apps"></a>cookieASP.NET 4.x と ASP.NET Core アプリの間で認証を共有する

Katana 認証ミドルウェアを使用する ASP.NET 4.x アプリは Cookie cookie 、ASP.NET Core 認証ミドルウェアと互換性のある認証を生成するように構成できます Cookie 。 これにより、複数の手順で大規模なサイトの個々のアプリをアップグレードしながら、サイト全体でスムーズに SSO を利用することができます。

アプリで Katana Cookie 認証ミドルウェアを使用すると、 `UseCookieAuthentication` プロジェクトの *Startup.Auth.cs* ファイルでが呼び出されます。 Visual Studio 2013 以降で作成された ASP.NET 4.x web アプリプロジェクトは、既定で Katana 認証ミドルウェアを使用します Cookie 。 `UseCookieAuthentication`は互換性のために残されており、ASP.NET Core アプリではサポートされていませんが、 `UseCookieAuthentication` Katana 認証ミドルウェアを使用する ASP.NET 4.x アプリでを呼び出すこと Cookie は有効です。

ASP.NET 4.x アプリは .NET Framework 4.5.1 以降を対象にする必要があります。 そうしないと、必要な NuGet パッケージのインストールに失敗します。

cookieASP.NET 4.x アプリと ASP.NET Core アプリの間で認証を共有するには、「 [ cookie ASP.NET Core アプリ間での共有認証](#share-authentication-cookies-with-aspnet-core-identity)」セクションの説明に従って ASP.NET Core アプリを構成し、次のように ASP.NET 4.x アプリを構成します。

アプリのパッケージが最新リリースに更新されていることを確認します。 それぞれの ASP.NET 4.x アプリに [Owin](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) パッケージをインストールします。

の呼び出しを見つけて変更し `UseCookieAuthentication` ます。

* cookieASP.NET Core 認証ミドルウェアによって使用される名前 (例では) に合わせて名前を変更 Cookie `.AspNet.SharedCookie` します。
* 次の例では、認証の種類がに設定されて `Identity.Application` います。
* `DataProtectionProvider`Common data protection キーの格納場所に初期化されたのインスタンスを提供します。
* アプリケーション名が、認証を共有するすべてのアプリ (例では) によって使用される共通のアプリ名に設定されていることを確認し cookie `SharedCookieApp` ます。

およびを設定していない場合は `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider` 、 <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> 一意のユーザーを識別するクレームをに設定します。

*App_Start/startup.auth.cs*:

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions
{
    AuthenticationType = "Identity.Application",
    CookieName = ".AspNet.SharedCookie",
    LoginPath = new PathString("/Account/Login"),
    Provider = new CookieAuthenticationProvider
    {
        OnValidateIdentity =
            SecurityStampValidator
                .OnValidateIdentity<ApplicationUserManager, ApplicationUser>(
                    validateInterval: TimeSpan.FromMinutes(30),
                    regenerateIdentity: (manager, user) =>
                        user.GenerateUserIdentityAsync(manager))
    },
    TicketDataFormat = new AspNetTicketDataFormat(
        new DataProtectorShim(
            DataProtectionProvider.Create("{PATH TO COMMON KEY RING FOLDER}",
                (builder) => { builder.SetApplicationName("SharedCookieApp"); })
            .CreateProtector(
                "Microsoft.AspNetCore.Authentication.Cookies." +
                    "CookieAuthenticationMiddleware",
                "Identity.Application",
                "v2"))),
    CookieManager = new ChunkingCookieManager()
});

System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier =
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name";
```

ユーザー id を生成するときは、認証の種類 ( `Identity.Application` ) が `AuthenticationType` `UseCookieAuthentication` *App_Start/startup.auth.cs* ので設定されたで定義されている種類と一致している必要があります。

*モデル/ IdentityModels.cs*:

```csharp
public class ApplicationUser : IdentityUser
{
    public async Task<ClaimsIdentity> GenerateUserIdentityAsync(
        UserManager<ApplicationUser> manager)
    {
        // The authenticationType must match the one defined in 
        // CookieAuthenticationOptions.AuthenticationType
        var userIdentity = 
            await manager.CreateIdentityAsync(this, "Identity.Application");

        // Add custom user claims here

        return userIdentity;
    }
}
```

## <a name="use-a-common-user-database"></a>共通のユーザーデータベースを使用する

アプリで同じ Identity スキーマ (同じバージョンの) が使用されている場合は Identity 、 Identity 各アプリのシステムが同じユーザーデータベースを指していることを確認します。 それ以外の場合、id システムは、認証の情報をデータベース内の情報と照合しようとしたときに、実行時にエラーを生成し cookie ます。

アプリ Identity 間でスキーマが異なる場合、通常はアプリで異なるバージョンが使用されているため Identity 、最新バージョンのに基づいて共通データベースを共有する Identity ことはできません。他のアプリのスキーマに再マップして列を追加する必要はありません Identity 。 多くの場合、アプリで共通のデータベースを共有できるように、他のアプリをアップグレードして最新バージョンを使用する方が効率的です Identity 。

## <a name="additional-resources"></a>その他のリソース

* <xref:host-and-deploy/web-farm>
