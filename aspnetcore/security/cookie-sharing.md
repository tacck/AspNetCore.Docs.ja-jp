---
title: 'ASP.NET アプリ間での認証の共有 cookie'
author: rick-anderson
description: cookieASP.NET 4.x および ASP.NET Core アプリ間で認証を共有する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/05/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: security/cookie-sharing
ms.openlocfilehash: 8f54f2e4894328f8471d5f80c8184839ce47add6
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059690"
---
# <a name="share-authentication-no-loccookies-among-aspnet-apps"></a><span data-ttu-id="afadc-103">ASP.NET アプリ間での認証の共有 cookie</span><span class="sxs-lookup"><span data-stu-id="afadc-103">Share authentication cookies among ASP.NET apps</span></span>

<span data-ttu-id="afadc-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="afadc-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="afadc-105">多くの場合、web サイトは、連携して動作する個々の web アプリで構成されます。</span><span class="sxs-lookup"><span data-stu-id="afadc-105">Websites often consist of individual web apps working together.</span></span> <span data-ttu-id="afadc-106">シングルサインオン (SSO) エクスペリエンスを提供するには、サイト内の web アプリで認証を共有する必要があり cookie ます。</span><span class="sxs-lookup"><span data-stu-id="afadc-106">To provide a single sign-on (SSO) experience, web apps within a site must share authentication cookies.</span></span> <span data-ttu-id="afadc-107">このシナリオをサポートするために、データ保護スタックは Katana cookie 認証と ASP.NET Core 認証チケットを共有できるようにし cookie ます。</span><span class="sxs-lookup"><span data-stu-id="afadc-107">To support this scenario, the data protection stack allows sharing Katana cookie authentication and ASP.NET Core cookie authentication tickets.</span></span>

<span data-ttu-id="afadc-108">次の例を参照してください。</span><span class="sxs-lookup"><span data-stu-id="afadc-108">In the examples that follow:</span></span>

* <span data-ttu-id="afadc-109">認証 cookie 名は、共通の値に設定され `.AspNet.SharedCookie` ます。</span><span class="sxs-lookup"><span data-stu-id="afadc-109">The authentication cookie name is set to a common value of `.AspNet.SharedCookie`.</span></span>
* <span data-ttu-id="afadc-110">`AuthenticationType`は、 `Identity.Application` 明示的にまたは既定でに設定されます。</span><span class="sxs-lookup"><span data-stu-id="afadc-110">The `AuthenticationType` is set to `Identity.Application` either explicitly or by default.</span></span>
* <span data-ttu-id="afadc-111">共通のアプリ名は、データ保護システムがデータ保護キー () を共有できるようにするために使用され `SharedCookieApp` ます。</span><span class="sxs-lookup"><span data-stu-id="afadc-111">A common app name is used to enable the data protection system to share data protection keys (`SharedCookieApp`).</span></span>
* <span data-ttu-id="afadc-112">`Identity.Application` は、認証スキームとして使用されます。</span><span class="sxs-lookup"><span data-stu-id="afadc-112">`Identity.Application` is used as the authentication scheme.</span></span> <span data-ttu-id="afadc-113">どのようなスキームを使用する場合でも、既定のスキームとして、または明示的に設定することによって、共有アプリ *内および* 共有アプリ全体で一貫して使用する必要があり cookie ます。</span><span class="sxs-lookup"><span data-stu-id="afadc-113">Whatever scheme is used, it must be used consistently *within and across* the shared cookie apps either as the default scheme or by explicitly setting it.</span></span> <span data-ttu-id="afadc-114">スキームは、の暗号化と復号化に使用される cookie ため、一貫したスキームをアプリ間で使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="afadc-114">The scheme is used when encrypting and decrypting cookies, so a consistent scheme must be used across apps.</span></span>
* <span data-ttu-id="afadc-115">一般的な [データ保護キー](xref:security/data-protection/implementation/key-management) の保存場所が使用されます。</span><span class="sxs-lookup"><span data-stu-id="afadc-115">A common [data protection key](xref:security/data-protection/implementation/key-management) storage location is used.</span></span>
  * <span data-ttu-id="afadc-116">ASP.NET Core アプリでは、を <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> 使用してキーの格納場所を設定します。</span><span class="sxs-lookup"><span data-stu-id="afadc-116">In ASP.NET Core apps, <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> is used to set the key storage location.</span></span>
  * <span data-ttu-id="afadc-117">.NET Framework アプリでは、 Cookie 認証ミドルウェアはの実装を使用 <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider> します。</span><span class="sxs-lookup"><span data-stu-id="afadc-117">In .NET Framework apps, Cookie Authentication Middleware uses an implementation of <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider>.</span></span> <span data-ttu-id="afadc-118">`DataProtectionProvider` には、認証ペイロードデータの暗号化と復号化を行うためのデータ保護サービスが用意されて cookie います。</span><span class="sxs-lookup"><span data-stu-id="afadc-118">`DataProtectionProvider` provides data protection services for the encryption and decryption of authentication cookie payload data.</span></span> <span data-ttu-id="afadc-119">インスタンスは、 `DataProtectionProvider` アプリの他の部分で使用されるデータ保護システムから分離されています。</span><span class="sxs-lookup"><span data-stu-id="afadc-119">The `DataProtectionProvider` instance is isolated from the data protection system used by other parts of the app.</span></span> <span data-ttu-id="afadc-120">[Dataprotectionprovider. Create (DirectoryInfo, Action \<IDataProtectionBuilder> )](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) は、 <xref:System.IO.DirectoryInfo> データ保護キーの保存場所を指定するを受け入れます。</span><span class="sxs-lookup"><span data-stu-id="afadc-120">[DataProtectionProvider.Create(System.IO.DirectoryInfo, Action\<IDataProtectionBuilder>)](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) accepts a <xref:System.IO.DirectoryInfo> to specify the location for data protection key storage.</span></span>
* <span data-ttu-id="afadc-121">`DataProtectionProvider` には、 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) NuGet パッケージが必要です。</span><span class="sxs-lookup"><span data-stu-id="afadc-121">`DataProtectionProvider` requires the [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) NuGet package:</span></span>
  * <span data-ttu-id="afadc-122">ASP.NET Core 2.x アプリで、 [AspNetCore メタパッケージ](xref:fundamentals/metapackage-app)を参照します。</span><span class="sxs-lookup"><span data-stu-id="afadc-122">In ASP.NET Core 2.x apps, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
  * <span data-ttu-id="afadc-123">.NET Framework アプリで、 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/)へのパッケージ参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="afadc-123">In .NET Framework apps, add a package reference to [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/).</span></span>
* <span data-ttu-id="afadc-124"><xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> 共通アプリ名を設定します。</span><span class="sxs-lookup"><span data-stu-id="afadc-124"><xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> sets the common app name.</span></span>

## <a name="share-authentication-no-loccookies-with-no-locaspnet-core-identity"></a><span data-ttu-id="afadc-125">認証を共有する cookieASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="afadc-125">Share authentication cookies with ASP.NET Core Identity</span></span>

<span data-ttu-id="afadc-126">ASP.NET Core Identity を使うとき:</span><span class="sxs-lookup"><span data-stu-id="afadc-126">When using ASP.NET Core Identity:</span></span>

* <span data-ttu-id="afadc-127">データ保護キーとアプリ名は、アプリ間で共有する必要があります。</span><span class="sxs-lookup"><span data-stu-id="afadc-127">Data protection keys and the app name must be shared among apps.</span></span> <span data-ttu-id="afadc-128">共通のキー格納場所は、次の例のメソッドに提供され <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> ます。</span><span class="sxs-lookup"><span data-stu-id="afadc-128">A common key storage location is provided to the <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> method in the following examples.</span></span> <span data-ttu-id="afadc-129"><xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*>共通の共有アプリ名を構成するには、を使用し `SharedCookieApp` ます (次の例を参考にしてください)。</span><span class="sxs-lookup"><span data-stu-id="afadc-129">Use <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> to configure a common shared app name (`SharedCookieApp` in the following examples).</span></span> <span data-ttu-id="afadc-130">詳細については、「<xref:security/data-protection/configuration/overview>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="afadc-130">For more information, see <xref:security/data-protection/configuration/overview>.</span></span>
* <span data-ttu-id="afadc-131">拡張メソッドを使用して、 <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*> のデータ保護サービスを設定し cookie ます。</span><span class="sxs-lookup"><span data-stu-id="afadc-131">Use the <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*> extension method to set up the data protection service for cookies.</span></span>
* <span data-ttu-id="afadc-132">既定の認証の種類はです `Identity.Application` 。</span><span class="sxs-lookup"><span data-stu-id="afadc-132">The default authentication type is `Identity.Application`.</span></span>

<span data-ttu-id="afadc-133">`Startup.ConfigureServices`の場合:</span><span class="sxs-lookup"><span data-stu-id="afadc-133">In `Startup.ConfigureServices`:</span></span>

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
});
```

## <a name="share-authentication-no-loccookies-without-no-locaspnet-core-identity"></a><span data-ttu-id="afadc-134">認証を共有 cookie しない ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="afadc-134">Share authentication cookies without ASP.NET Core Identity</span></span>

<span data-ttu-id="afadc-135">を使用せずにを直接使用する場合 cookie ASP.NET Core Identity は、でデータ保護と認証を構成し `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="afadc-135">When using cookies directly without ASP.NET Core Identity, configure data protection and authentication in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="afadc-136">次の例では、認証の種類がに設定されてい `Identity.Application` ます。</span><span class="sxs-lookup"><span data-stu-id="afadc-136">In the following example, the authentication type is set to `Identity.Application`:</span></span>

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

## <a name="share-no-loccookies-across-different-base-paths"></a><span data-ttu-id="afadc-137">cookie異なるベースパス間で s を共有する</span><span class="sxs-lookup"><span data-stu-id="afadc-137">Share cookies across different base paths</span></span>

<span data-ttu-id="afadc-138">認証では、 cookie 既定値として[HttpRequest](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase)が使用されます[ Cookie 。パス](xref:Microsoft.AspNetCore.Http.CookieBuilder.Path)。</span><span class="sxs-lookup"><span data-stu-id="afadc-138">An authentication cookie uses the [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) as its default [Cookie.Path](xref:Microsoft.AspNetCore.Http.CookieBuilder.Path).</span></span> <span data-ttu-id="afadc-139">アプリの cookie を異なる基本パスで共有する必要がある場合は、を `Path` オーバーライドする必要があります。</span><span class="sxs-lookup"><span data-stu-id="afadc-139">If the app's cookie must be shared across different base paths, `Path` must be overridden:</span></span>

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
    options.Cookie.Path = "/";
});
```

## <a name="share-no-loccookies-across-subdomains"></a><span data-ttu-id="afadc-140">サブ cookie ドメイン間で s を共有する</span><span class="sxs-lookup"><span data-stu-id="afadc-140">Share cookies across subdomains</span></span>

<span data-ttu-id="afadc-141">をサブドメイン間で共有するアプリをホストする場合は cookie 、で共通のドメインを指定し[ Cookie ます。ドメイン](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain)プロパティ。</span><span class="sxs-lookup"><span data-stu-id="afadc-141">When hosting apps that share cookies across subdomains, specify a common domain in the [Cookie.Domain](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain) property.</span></span> <span data-ttu-id="afadc-142">やなどのでアプリ間で s を共有するには、をとし cookie `contoso.com` `first_subdomain.contoso.com` `second_subdomain.contoso.com` て指定し `Cookie.Domain` `.contoso.com` ます。</span><span class="sxs-lookup"><span data-stu-id="afadc-142">To share cookies across apps at `contoso.com`, such as `first_subdomain.contoso.com` and `second_subdomain.contoso.com`, specify the `Cookie.Domain` as `.contoso.com`:</span></span>

```csharp
options.Cookie.Domain = ".contoso.com";
```

## <a name="encrypt-data-protection-keys-at-rest"></a><span data-ttu-id="afadc-143">保存時のデータ保護キーの暗号化</span><span class="sxs-lookup"><span data-stu-id="afadc-143">Encrypt data protection keys at rest</span></span>

<span data-ttu-id="afadc-144">運用環境のデプロイの場合は、 `DataProtectionProvider` DPAPI または X509Certificate を使用して保存時のキーを暗号化するようにを構成します。</span><span class="sxs-lookup"><span data-stu-id="afadc-144">For production deployments, configure the `DataProtectionProvider` to encrypt keys at rest with DPAPI or an X509Certificate.</span></span> <span data-ttu-id="afadc-145">詳細については、「<xref:security/data-protection/implementation/key-encryption-at-rest>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="afadc-145">For more information, see <xref:security/data-protection/implementation/key-encryption-at-rest>.</span></span> <span data-ttu-id="afadc-146">次の例では、証明書の拇印がに提供されてい <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*> ます。</span><span class="sxs-lookup"><span data-stu-id="afadc-146">In the following example, a certificate thumbprint is provided to <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*>:</span></span>

```csharp
services.AddDataProtection()
    .ProtectKeysWithCertificate("{CERTIFICATE THUMBPRINT}");
```

## <a name="share-authentication-no-loccookies-between-aspnet-4x-and-aspnet-core-apps"></a><span data-ttu-id="afadc-147">cookieASP.NET 4.x と ASP.NET Core アプリの間で認証を共有する</span><span class="sxs-lookup"><span data-stu-id="afadc-147">Share authentication cookies between ASP.NET 4.x and ASP.NET Core apps</span></span>

<span data-ttu-id="afadc-148">Katana 認証ミドルウェアを使用する ASP.NET 4.x アプリは Cookie cookie 、ASP.NET Core 認証ミドルウェアと互換性のある認証を生成するように構成できます Cookie 。</span><span class="sxs-lookup"><span data-stu-id="afadc-148">ASP.NET 4.x apps that use Katana Cookie Authentication Middleware can be configured to generate authentication cookies that are compatible with the ASP.NET Core Cookie Authentication Middleware.</span></span> <span data-ttu-id="afadc-149">これにより、複数の手順で大規模なサイトの個々のアプリをアップグレードしながら、サイト全体でスムーズに SSO を利用することができます。</span><span class="sxs-lookup"><span data-stu-id="afadc-149">This allows upgrading a large site's individual apps in several steps while providing a smooth SSO experience across the site.</span></span>

<span data-ttu-id="afadc-150">アプリで Katana Cookie 認証ミドルウェアを使用すると、 `UseCookieAuthentication` プロジェクトの *Startup.Auth.cs* ファイルでが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="afadc-150">When an app uses Katana Cookie Authentication Middleware, it calls `UseCookieAuthentication` in the project's *Startup.Auth.cs* file.</span></span> <span data-ttu-id="afadc-151">Visual Studio 2013 以降で作成された ASP.NET 4.x web アプリプロジェクトは、既定で Katana 認証ミドルウェアを使用します Cookie 。</span><span class="sxs-lookup"><span data-stu-id="afadc-151">ASP.NET 4.x web app projects created with Visual Studio 2013 and later use the Katana Cookie Authentication Middleware by default.</span></span> <span data-ttu-id="afadc-152">`UseCookieAuthentication`は互換性のために残されており、ASP.NET Core アプリではサポートされていませんが、 `UseCookieAuthentication` Katana 認証ミドルウェアを使用する ASP.NET 4.x アプリでを呼び出すこと Cookie は有効です。</span><span class="sxs-lookup"><span data-stu-id="afadc-152">Although `UseCookieAuthentication` is obsolete and unsupported for ASP.NET Core apps, calling `UseCookieAuthentication` in an ASP.NET 4.x app that uses Katana Cookie Authentication Middleware is valid.</span></span>

<span data-ttu-id="afadc-153">ASP.NET 4.x アプリは .NET Framework 4.5.1 以降を対象にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="afadc-153">An ASP.NET 4.x app must target .NET Framework 4.5.1 or later.</span></span> <span data-ttu-id="afadc-154">そうしないと、必要な NuGet パッケージのインストールに失敗します。</span><span class="sxs-lookup"><span data-stu-id="afadc-154">Otherwise, the necessary NuGet packages fail to install.</span></span>

<span data-ttu-id="afadc-155">cookieASP.NET 4.x アプリと ASP.NET Core アプリの間で認証を共有するには、「 [ cookie ASP.NET Core アプリ間での共有認証](#share-authentication-cookies-with-aspnet-core-identity)」セクションの説明に従って ASP.NET Core アプリを構成し、次のように ASP.NET 4.x アプリを構成します。</span><span class="sxs-lookup"><span data-stu-id="afadc-155">To share authentication cookies between an ASP.NET 4.x app and an ASP.NET Core app, configure the ASP.NET Core app as stated in the [Share authentication cookies among ASP.NET Core apps](#share-authentication-cookies-with-aspnet-core-identity) section, then configure the ASP.NET 4.x app as follows.</span></span>

<span data-ttu-id="afadc-156">アプリのパッケージが最新リリースに更新されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="afadc-156">Confirm that the app's packages are updated to the latest releases.</span></span> <span data-ttu-id="afadc-157">それぞれの ASP.NET 4.x アプリに [Owin](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="afadc-157">Install the [Microsoft.Owin.Security.Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) package into each ASP.NET 4.x app.</span></span>

<span data-ttu-id="afadc-158">の呼び出しを見つけて変更し `UseCookieAuthentication` ます。</span><span class="sxs-lookup"><span data-stu-id="afadc-158">Locate and modify the call to `UseCookieAuthentication`:</span></span>

* <span data-ttu-id="afadc-159">cookieASP.NET Core 認証ミドルウェアによって使用される名前 (例では) に合わせて名前を変更 Cookie `.AspNet.SharedCookie` します。</span><span class="sxs-lookup"><span data-stu-id="afadc-159">Change the cookie name to match the name used by the ASP.NET Core Cookie Authentication Middleware (`.AspNet.SharedCookie` in the example).</span></span>
* <span data-ttu-id="afadc-160">次の例では、認証の種類がに設定されて `Identity.Application` います。</span><span class="sxs-lookup"><span data-stu-id="afadc-160">In the following example, the authentication type is set to `Identity.Application`.</span></span>
* <span data-ttu-id="afadc-161">`DataProtectionProvider`Common data protection キーの格納場所に初期化されたのインスタンスを提供します。</span><span class="sxs-lookup"><span data-stu-id="afadc-161">Provide an instance of a `DataProtectionProvider` initialized to the common data protection key storage location.</span></span>
* <span data-ttu-id="afadc-162">アプリケーション名が、認証を共有するすべてのアプリ (例では) によって使用される共通のアプリ名に設定されていることを確認し cookie `SharedCookieApp` ます。</span><span class="sxs-lookup"><span data-stu-id="afadc-162">Confirm that the app name is set to the common app name used by all apps that share authentication cookies (`SharedCookieApp` in the example).</span></span>

<span data-ttu-id="afadc-163">およびを設定していない場合は `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider` 、 <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> 一意のユーザーを識別するクレームをに設定します。</span><span class="sxs-lookup"><span data-stu-id="afadc-163">If not setting `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` and `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`, set <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> to a claim that distinguishes unique users.</span></span>

<span data-ttu-id="afadc-164">*App_Start/startup.auth.cs* :</span><span class="sxs-lookup"><span data-stu-id="afadc-164">*App_Start/Startup.Auth.cs* :</span></span>

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

<span data-ttu-id="afadc-165">ユーザー id を生成するときは、認証の種類 ( `Identity.Application` ) が `AuthenticationType` `UseCookieAuthentication` *App_Start/startup.auth.cs* ので設定されたで定義されている種類と一致している必要があります。</span><span class="sxs-lookup"><span data-stu-id="afadc-165">When generating a user identity, the authentication type (`Identity.Application`) must match the type defined in `AuthenticationType` set with `UseCookieAuthentication` in *App_Start/Startup.Auth.cs* .</span></span>

<span data-ttu-id="afadc-166">*モデル/ IdentityModels.cs* :</span><span class="sxs-lookup"><span data-stu-id="afadc-166">*Models/IdentityModels.cs* :</span></span>

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

## <a name="use-a-common-user-database"></a><span data-ttu-id="afadc-167">共通のユーザーデータベースを使用する</span><span class="sxs-lookup"><span data-stu-id="afadc-167">Use a common user database</span></span>

<span data-ttu-id="afadc-168">アプリで同じ Identity スキーマ (同じバージョンの) が使用されている場合は Identity 、 Identity 各アプリのシステムが同じユーザーデータベースを指していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="afadc-168">When apps use the same Identity schema (same version of Identity), confirm that the Identity system for each app is pointed at the same user database.</span></span> <span data-ttu-id="afadc-169">それ以外の場合、id システムは、認証の情報をデータベース内の情報と照合しようとしたときに、実行時にエラーを生成し cookie ます。</span><span class="sxs-lookup"><span data-stu-id="afadc-169">Otherwise, the identity system produces failures at runtime when it attempts to match the information in the authentication cookie against the information in its database.</span></span>

<span data-ttu-id="afadc-170">アプリ Identity 間でスキーマが異なる場合、通常はアプリで異なるバージョンが使用されているため Identity 、最新バージョンのに基づいて共通データベースを共有する Identity ことはできません。他のアプリのスキーマに再マップして列を追加する必要はありません Identity 。</span><span class="sxs-lookup"><span data-stu-id="afadc-170">When the Identity schema is different among apps, usually because apps are using different Identity versions, sharing a common database based on the latest version of Identity isn't possible without remapping and adding columns in other app's Identity schemas.</span></span> <span data-ttu-id="afadc-171">多くの場合、アプリで共通のデータベースを共有できるように、他のアプリをアップグレードして最新バージョンを使用する方が効率的です Identity 。</span><span class="sxs-lookup"><span data-stu-id="afadc-171">It's often more efficient to upgrade the other apps to use the latest Identity version so that a common database can be shared by the apps.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="afadc-172">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="afadc-172">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
