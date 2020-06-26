---
title: ASP.NET アプリ間で認証 cookie を共有する
author: rick-anderson
description: ASP.NET 4.x と ASP.NET Core アプリ間で認証 cookie を共有する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/05/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/cookie-sharing
ms.openlocfilehash: b8de5ea1da47c7813aac0137719989959d4c8ce4
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/26/2020
ms.locfileid: "85407032"
---
# <a name="share-authentication-cookies-among-aspnet-apps"></a>ASP.NET アプリ間で認証 cookie を共有する

作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)

多くの場合、web サイトは、連携して動作する個々の web アプリで構成されます。 シングルサインオン (SSO) エクスペリエンスを提供するには、サイト内の web アプリで認証 cookie を共有する必要があります。 このシナリオをサポートするために、データ保護スタックは、Katana cookie 認証とクッキー認証チケットの ASP.NET Core 共有を許可します。

次の例を参照してください。

* 認証 cookie 名は、共通の値に設定され `.AspNet.SharedCookie` ます。
* `AuthenticationType`は、 `Identity.Application` 明示的にまたは既定でに設定されます。
* 共通のアプリ名は、データ保護システムがデータ保護キー () を共有できるようにするために使用され `SharedCookieApp` ます。
* `Identity.Application`は、認証スキームとして使用されます。 使用されるスキームにかかわらず、共有 cookie アプリ*内*でも、既定のスキームとしても、明示的に設定することによっても、一貫して使用する必要があります。 スキームは、cookie の暗号化と復号化に使用されるため、一貫したスキームをアプリ間で使用する必要があります。
* 一般的な[データ保護キー](xref:security/data-protection/implementation/key-management)の保存場所が使用されます。
  * ASP.NET Core アプリでは、を <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> 使用してキーの格納場所を設定します。
  * .NET Framework アプリでは、Cookie 認証ミドルウェアはの実装を使用 <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider> します。 `DataProtectionProvider`認証 cookie ペイロードデータの暗号化と復号化のためのデータ保護サービスを提供します。 インスタンスは、 `DataProtectionProvider` アプリの他の部分で使用されるデータ保護システムから分離されています。 [Dataprotectionprovider. Create (DirectoryInfo, Action \<IDataProtectionBuilder> )](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*)は、 <xref:System.IO.DirectoryInfo> データ保護キーの保存場所を指定するを受け入れます。
* `DataProtectionProvider`には、 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) NuGet パッケージが必要です。
  * ASP.NET Core 2.x アプリで、 [AspNetCore メタパッケージ](xref:fundamentals/metapackage-app)を参照します。
  * .NET Framework アプリで、 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/)へのパッケージ参照を追加します。
* <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*>共通アプリ名を設定します。

## <a name="share-authentication-cookies-with-aspnet-core-identity"></a>認証 cookie を ASP.NET Core と共有するIdentity

ASP.NET Core を使用する場合 Identity :

* データ保護キーとアプリ名は、アプリ間で共有する必要があります。 共通のキー格納場所は、次の例のメソッドに提供され <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> ます。 <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*>共通の共有アプリ名を構成するには、を使用し `SharedCookieApp` ます (次の例を参考にしてください)。 詳細については、「<xref:security/data-protection/configuration/overview>」を参照してください。
* 拡張メソッドを使用して、 <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*> cookie 用のデータ保護サービスを設定します。
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

## <a name="share-authentication-cookies-without-aspnet-core-identity"></a>ASP.NET Core なしで認証 cookie を共有するIdentity

ASP.NET Core せずに cookie を直接使用する場合 Identity は、でデータ保護と認証を構成し `Startup.ConfigureServices` ます。 次の例では、認証の種類がに設定されてい `Identity.Application` ます。

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

## <a name="share-cookies-across-different-base-paths"></a>異なるベースパス間での cookie の共有

認証クッキーは、既定の[cookie のパス](xref:Microsoft.AspNetCore.Http.CookieBuilder.Path)として[HttpRequest](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase)を使用します。 アプリの cookie を異なるベースパス間で共有する必要がある場合は、を `Path` オーバーライドする必要があります。

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
    options.Cookie.Path = "/";
});
```

## <a name="share-cookies-across-subdomains"></a>サブドメイン間での cookie の共有

サブドメイン間で cookie を共有するアプリをホストする場合は、" [cookie. ドメイン](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain)" プロパティで共通のドメインを指定します。 やなどのアプリ間で cookie を共有するには、をとし `contoso.com` `first_subdomain.contoso.com` `second_subdomain.contoso.com` て指定し `Cookie.Domain` `.contoso.com` ます。

```csharp
options.Cookie.Domain = ".contoso.com";
```

## <a name="encrypt-data-protection-keys-at-rest"></a>保存時のデータ保護キーの暗号化

運用環境のデプロイの場合は、 `DataProtectionProvider` DPAPI または X509Certificate を使用して保存時のキーを暗号化するようにを構成します。 詳細については、「<xref:security/data-protection/implementation/key-encryption-at-rest>」を参照してください。 次の例では、証明書の拇印がに提供されてい <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*> ます。

```csharp
services.AddDataProtection()
    .ProtectKeysWithCertificate("{CERTIFICATE THUMBPRINT}");
```

## <a name="share-authentication-cookies-between-aspnet-4x-and-aspnet-core-apps"></a>ASP.NET 4.x と ASP.NET Core アプリ間で認証 cookie を共有する

Katana Cookie 認証ミドルウェアを使用する ASP.NET 4.x アプリは、ASP.NET Core Cookie 認証ミドルウェアと互換性のある認証 cookie を生成するように構成できます。 これにより、複数の手順で大規模なサイトの個々のアプリをアップグレードしながら、サイト全体でスムーズに SSO を利用することができます。

アプリで Katana Cookie 認証ミドルウェアを使用すると、 `UseCookieAuthentication` プロジェクトの*Startup.Auth.cs*ファイルでが呼び出されます。 Visual Studio 2013 以降で作成された ASP.NET 4.x web アプリプロジェクトは、既定で Katana Cookie 認証ミドルウェアを使用します。 `UseCookieAuthentication`は互換性のために残されており、ASP.NET Core アプリではサポートされていませんが、 `UseCookieAuthentication` Katana Cookie 認証ミドルウェアを使用する ASP.NET 4.x アプリでを呼び出すことは有効です。

ASP.NET 4.x アプリは .NET Framework 4.5.1 以降を対象にする必要があります。 そうしないと、必要な NuGet パッケージのインストールに失敗します。

ASP.NET 4.x アプリと ASP.NET Core アプリの間で認証 cookie を共有するには、「 [ASP.NET Core アプリ間で認証 cookie を共有](#share-authentication-cookies-with-aspnet-core-identity)する」セクションの説明に従って ASP.NET Core アプリを構成し、次のように ASP.NET 4.x アプリを構成します。

アプリのパッケージが最新リリースに更新されていることを確認します。 それぞれの ASP.NET 4.x アプリに[Owin](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/)パッケージをインストールします。

の呼び出しを見つけて変更し `UseCookieAuthentication` ます。

* Cookie 名を、ASP.NET Core Cookie 認証ミドルウェア (例では) で使用されている名前と一致するように変更 `.AspNet.SharedCookie` します。
* 次の例では、認証の種類がに設定されて `Identity.Application` います。
* `DataProtectionProvider`Common data protection キーの格納場所に初期化されたのインスタンスを提供します。
* アプリケーション名が、認証 cookie を共有するすべてのアプリ (例では) によって使用される共通アプリ名に設定されていることを確認し `SharedCookieApp` ます。

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

ユーザー id を生成するときは、認証の種類 ( `Identity.Application` ) が `AuthenticationType` `UseCookieAuthentication` *App_Start/startup.auth.cs*ので設定されたで定義されている種類と一致している必要があります。

*モデル/* モジュール:

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

アプリで同じ Identity スキーマ (同じバージョンの) が使用されている場合は Identity 、 Identity 各アプリのシステムが同じユーザーデータベースを指していることを確認します。 それ以外の場合、id システムは、認証クッキーの情報とデータベース内の情報を照合しようとしたときに、実行時にエラーを生成します。

アプリ Identity 間でスキーマが異なる場合、通常はアプリで異なるバージョンが使用されているため Identity 、最新バージョンのに基づいて共通データベースを共有する Identity ことはできません。他のアプリのスキーマに再マップして列を追加する必要はありません Identity 。 多くの場合、アプリで共通のデータベースを共有できるように、他のアプリをアップグレードして最新バージョンを使用する方が効率的です Identity 。

## <a name="additional-resources"></a>その他の技術情報

* <xref:host-and-deploy/web-farm>
