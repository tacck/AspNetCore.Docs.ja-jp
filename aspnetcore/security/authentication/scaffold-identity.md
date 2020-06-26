---
title: IdentityASP.NET Core プロジェクトでのスキャフォールディング
author: rick-anderson
description: ASP.NET Core のプロジェクトでスキャフォールディングする方法について説明 Identity します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/1/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/scaffold-identity
ms.openlocfilehash: 155bdfbeea06022d35bbb551d5b2d0ee5a51a093
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/26/2020
ms.locfileid: "85400818"
---
# <a name="scaffold-identity-in-aspnet-core-projects"></a><span data-ttu-id="38f6d-103">IdentityASP.NET Core プロジェクトでのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="38f6d-103">Scaffold Identity in ASP.NET Core projects</span></span>

<span data-ttu-id="38f6d-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="38f6d-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="38f6d-105">ASP.NET Core は、 [ Razor クラスライブラリ](xref:razor-pages/ui-class)として[ASP.NET Core Identity ](xref:security/authentication/identity)を提供します。</span><span class="sxs-lookup"><span data-stu-id="38f6d-105">ASP.NET Core provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="38f6d-106">を含むアプリケーション Identity では、scaffolder を適用して、 Identity Razor クラスライブラリ (rcl) に含まれているソースコードを選択的に追加できます。</span><span class="sxs-lookup"><span data-stu-id="38f6d-106">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="38f6d-107">コードを変更して動作を変更できるように、ソース コードを生成できます。</span><span class="sxs-lookup"><span data-stu-id="38f6d-107">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="38f6d-108">たとえば、登録で使用するコードを生成するようにスキャフォルダーに指示できます。</span><span class="sxs-lookup"><span data-stu-id="38f6d-108">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="38f6d-109">生成されたコードは、Identity RCL の同じコードよりも優先されます。</span><span class="sxs-lookup"><span data-stu-id="38f6d-109">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="38f6d-110">UI を完全に制御し、既定の RCL を使用しないようにするには、「[完全な Identity ui ソースの作成](#full)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="38f6d-110">To gain full control of the UI and not use the default RCL, see the section [Create full Identity UI source](#full).</span></span>

<span data-ttu-id="38f6d-111">認証を含ま**ない**アプリケーションでは、scaffolder を適用して rcl パッケージを追加でき Identity ます。</span><span class="sxs-lookup"><span data-stu-id="38f6d-111">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="38f6d-112">生成される Identity コードの選択オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="38f6d-112">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="38f6d-113">Scaffolder は、必要なコードの大部分を生成しますが、プロセスを完了するにはプロジェクトを更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="38f6d-113">Although the scaffolder generates most of the necessary code, you need to update your project to complete the process.</span></span> <span data-ttu-id="38f6d-114">このドキュメントでは、スキャフォールディングの更新を完了するために必要な手順について説明し Identity ます。</span><span class="sxs-lookup"><span data-stu-id="38f6d-114">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="38f6d-115">ファイルの違いを示すソース管理システムを使用して、変更を元に戻すことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="38f6d-115">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="38f6d-116">Scaffolder を実行した後に変更を確認し Identity ます。</span><span class="sxs-lookup"><span data-stu-id="38f6d-116">Inspect the changes after running the Identity scaffolder.</span></span>

<span data-ttu-id="38f6d-117">サービスは、 [2 要素認証](xref:security/authentication/identity-enable-qrcodes)、アカウントの[確認とパスワードの回復](xref:security/authentication/accconfirm)、およびの他のセキュリティ機能を使用する場合に必要です Identity 。</span><span class="sxs-lookup"><span data-stu-id="38f6d-117">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="38f6d-118">サービスまたはサービススタブは、スキャフォールディング時に生成されません Identity 。</span><span class="sxs-lookup"><span data-stu-id="38f6d-118">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="38f6d-119">これらの機能を有効にするサービスは、手動で追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="38f6d-119">Services to enable these features must be added manually.</span></span> <span data-ttu-id="38f6d-120">たとえば、「[電子メールの確認を要求する](xref:security/authentication/accconfirm#require-email-confirmation)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="38f6d-120">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

<span data-ttu-id="38f6d-121">Identity新しいデータコンテキストを使用して、既存の個別のアカウントを持つプロジェクトにスキャフォールディングする場合:</span><span class="sxs-lookup"><span data-stu-id="38f6d-121">When scaffolding Identity with a new data context into a project with existing individual accounts:</span></span>

* <span data-ttu-id="38f6d-122">で `Startup.ConfigureServices` 、の呼び出しを削除します。</span><span class="sxs-lookup"><span data-stu-id="38f6d-122">In `Startup.ConfigureServices`, remove the calls to:</span></span>
  * `AddDbContext`
  * `AddDefaultIdentity`

<span data-ttu-id="38f6d-123">たとえば、 `AddDbContext` と `AddDefaultIdentity` は次のコードでコメントアウトされます。</span><span class="sxs-lookup"><span data-stu-id="38f6d-123">For example, `AddDbContext` and `AddDefaultIdentity` are commented out in the following code:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRemove.cs?name=snippet)]

<span data-ttu-id="38f6d-124">上記のコードは、*区分/ Identity /IdentityHostingStartup.cs*で重複するコードをコメントアウトします。</span><span class="sxs-lookup"><span data-stu-id="38f6d-124">The preceeding code comments out the code that is duplicated in *Areas/Identity/IdentityHostingStartup.cs*</span></span>

<span data-ttu-id="38f6d-125">通常、個別のアカウントで作成されたアプリは、新しいデータコンテキストを作成し***ない***ようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="38f6d-125">Typically, apps that were created with individual accounts should ***not*** create a new data context.</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="38f6d-126">Identity空のプロジェクトにスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="38f6d-126">Scaffold Identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="38f6d-127">`Startup`次のようなコードでクラスを更新します。</span><span class="sxs-lookup"><span data-stu-id="38f6d-127">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="38f6d-128">Identity Razor 既存の承認なしでプロジェクトにスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="38f6d-128">Scaffold Identity into a Razor project without existing authorization</span></span>

<!--  Updated for 3.0
set projNam=RPnoAuth
set projType=webapp

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef database drop
dotnet ef migrations add CreateIdentitySchema0
dotnet ef database update
-->

<!-- ERROR
There is already an object named 'AspNetRoles' in the database.

Fixed via dotnet ef database drop
before dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Identity<span data-ttu-id="38f6d-129">は、 *Areas/ Identity /IdentityHostingStartup.cs*で構成されます。</span><span class="sxs-lookup"><span data-stu-id="38f6d-129"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="38f6d-130">詳細については、「 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="38f6d-130">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="38f6d-131">移行、UseAuthentication、およびレイアウト</span><span class="sxs-lookup"><span data-stu-id="38f6d-131">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="38f6d-132">認証を有効にする</span><span class="sxs-lookup"><span data-stu-id="38f6d-132">Enable authentication</span></span>

<span data-ttu-id="38f6d-133">`Startup`次のようなコードでクラスを更新します。</span><span class="sxs-lookup"><span data-stu-id="38f6d-133">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="38f6d-134">レイアウトの変更</span><span class="sxs-lookup"><span data-stu-id="38f6d-134">Layout changes</span></span>

<span data-ttu-id="38f6d-135">省略可能: 次のように、ログイン部分 ( `_LoginPartial` ) をレイアウトファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="38f6d-135">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="38f6d-136">Identity Razor 承認を使用してプロジェクトにスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="38f6d-136">Scaffold Identity into a Razor project with authorization</span></span>

<!--
Use >=2.1: dotnet new webapp -au Individual -o RPauth
Use = 2.0: dotnet new razor -au Individual -o RPauth
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="38f6d-137">一部 Identity のオプションは、 *Areas/ Identity /IdentityHostingStartup.cs*で構成されています。</span><span class="sxs-lookup"><span data-stu-id="38f6d-137">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="38f6d-138">詳細については、「 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="38f6d-138">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="38f6d-139">Identity既存の承認なしで MVC プロジェクトにスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="38f6d-139">Scaffold Identity into an MVC project without existing authorization</span></span>

<!--
set projNam=MvcNoAuth
set projType=mvc
set version=2.1.0

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v %version%
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="38f6d-140">省略可能: `_LoginPartial` *Views/Shared/_Layout cshtml*ファイルにログイン部分 () を追加します。</span><span class="sxs-lookup"><span data-stu-id="38f6d-140">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* <span data-ttu-id="38f6d-141">*Pages/shared/_LoginPartial cshtml*ファイルを*Views/shared/_LoginPartial*に移動します。</span><span class="sxs-lookup"><span data-stu-id="38f6d-141">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

Identity<span data-ttu-id="38f6d-142">は、 *Areas/ Identity /IdentityHostingStartup.cs*で構成されます。</span><span class="sxs-lookup"><span data-stu-id="38f6d-142"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="38f6d-143">詳細については、「IHostingStartup」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="38f6d-143">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="38f6d-144">`Startup`次のようなコードでクラスを更新します。</span><span class="sxs-lookup"><span data-stu-id="38f6d-144">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="38f6d-145">Identity承認を使用した MVC プロジェクトへのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="38f6d-145">Scaffold Identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

## <a name="scaffold-identity-into-a-blazor-server-project-without-existing-authorization"></a><span data-ttu-id="38f6d-146">Identity Blazor Server 既存の承認なしでプロジェクトにスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="38f6d-146">Scaffold Identity into a Blazor Server project without existing authorization</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Identity<span data-ttu-id="38f6d-147">は、 *Areas/ Identity /IdentityHostingStartup.cs*で構成されます。</span><span class="sxs-lookup"><span data-stu-id="38f6d-147"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="38f6d-148">詳細については、「 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="38f6d-148">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

### <a name="migrations"></a><span data-ttu-id="38f6d-149">移行</span><span class="sxs-lookup"><span data-stu-id="38f6d-149">Migrations</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

### <a name="pass-an-xsrf-token-to-the-app"></a><span data-ttu-id="38f6d-150">XSRF トークンをアプリに渡す</span><span class="sxs-lookup"><span data-stu-id="38f6d-150">Pass an XSRF token to the app</span></span>

<span data-ttu-id="38f6d-151">トークンは、次のようにコンポーネントに渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="38f6d-151">Tokens can be passed to components:</span></span>

* <span data-ttu-id="38f6d-152">認証トークンがプロビジョニングされ、認証 cookie に保存されると、コンポーネントに渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="38f6d-152">When authentication tokens are provisioned and saved to the authentication cookie, they can be passed to components.</span></span>
* Razor<span data-ttu-id="38f6d-153">コンポーネントでを直接使用することはできない `HttpContext` ため、のログアウトエンドポイントをに送信するための[要求防止偽造 (XSRF) トークン](xref:security/anti-request-forgery)を取得する方法はありません Identity `/Identity/Account/Logout` 。</span><span class="sxs-lookup"><span data-stu-id="38f6d-153"> components can't use `HttpContext` directly, so there's no way to obtain an [anti-request forgery (XSRF) token](xref:security/anti-request-forgery) to POST to Identity's logout endpoint at `/Identity/Account/Logout`.</span></span> <span data-ttu-id="38f6d-154">XSRF トークンは、コンポーネントに渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="38f6d-154">An XSRF token can be passed to components.</span></span>

<span data-ttu-id="38f6d-155">詳細については、「<xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="38f6d-155">For more information, see <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.</span></span>

<span data-ttu-id="38f6d-156">*Pages/_Host cshtml*ファイルで、クラスおよびクラスに追加した後、トークンを設定し `InitialApplicationState` `TokenProvider` ます。</span><span class="sxs-lookup"><span data-stu-id="38f6d-156">In the *Pages/_Host.cshtml* file, establish the token after adding it to the `InitialApplicationState` and `TokenProvider` classes:</span></span>

```csharp
@inject Microsoft.AspNetCore.Antiforgery.IAntiforgery Xsrf

...

var tokens = new InitialApplicationState
{
    ...

    XsrfToken = Xsrf.GetAndStoreTokens(HttpContext).RequestToken
};
```

<span data-ttu-id="38f6d-157">`App`コンポーネント (*app.xaml*) を更新して、次の `InitialState.XsrfToken` ものを割り当てます。</span><span class="sxs-lookup"><span data-stu-id="38f6d-157">Update the `App` component (*App.razor*) to assign the `InitialState.XsrfToken`:</span></span>

```csharp
@inject TokenProvider TokenProvider

...

TokenProvider.XsrfToken = InitialState.XsrfToken;
```

<span data-ttu-id="38f6d-158">トピック「」で説明されているサービスは、 `TokenProvider` `LoginDisplay` 次の[レイアウトと認証フローの変更](#layout-and-authentication-flow-changes)セクションのコンポーネントで使用されます。</span><span class="sxs-lookup"><span data-stu-id="38f6d-158">The `TokenProvider` service demonstrated in the topic is used in the `LoginDisplay` component in the following [Layout and authentication flow changes](#layout-and-authentication-flow-changes) section.</span></span>

### <a name="enable-authentication"></a><span data-ttu-id="38f6d-159">認証を有効にする</span><span class="sxs-lookup"><span data-stu-id="38f6d-159">Enable authentication</span></span>

<span data-ttu-id="38f6d-160">クラスの場合 `Startup` :</span><span class="sxs-lookup"><span data-stu-id="38f6d-160">In the `Startup` class:</span></span>

* <span data-ttu-id="38f6d-161">ページサービスがに追加されていることを確認 Razor `Startup.ConfigureServices` します。</span><span class="sxs-lookup"><span data-stu-id="38f6d-161">Confirm that Razor Pages services are added in `Startup.ConfigureServices`.</span></span>
* <span data-ttu-id="38f6d-162">[TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app)を使用する場合は、サービスを登録します。</span><span class="sxs-lookup"><span data-stu-id="38f6d-162">If using the [TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app), register the service.</span></span>
* <span data-ttu-id="38f6d-163">`UseDatabaseErrorPage`開発環境用のアプリケーションビルダーでを呼び出し `Startup.Configure` ます。</span><span class="sxs-lookup"><span data-stu-id="38f6d-163">Call `UseDatabaseErrorPage` on the application builder in `Startup.Configure` for the Development environment.</span></span>
* <span data-ttu-id="38f6d-164">`UseAuthentication`との後にを呼び出し `UseAuthorization` `UseRouting` ます。</span><span class="sxs-lookup"><span data-stu-id="38f6d-164">Call `UseAuthentication` and `UseAuthorization` after `UseRouting`.</span></span>
* <span data-ttu-id="38f6d-165">ページのエンドポイントを追加 Razor します。</span><span class="sxs-lookup"><span data-stu-id="38f6d-165">Add an endpoint for Razor Pages.</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupBlazor.cs?highlight=3,6,14,27-28,32)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-and-authentication-flow-changes"></a><span data-ttu-id="38f6d-166">レイアウトと認証のフローの変更</span><span class="sxs-lookup"><span data-stu-id="38f6d-166">Layout and authentication flow changes</span></span>

<span data-ttu-id="38f6d-167">`RedirectToLogin`プロジェクトルートのアプリの*共有*フォルダーにコンポーネント (*redirecttologin. razor*) を追加します。</span><span class="sxs-lookup"><span data-stu-id="38f6d-167">Add a `RedirectToLogin` component (*RedirectToLogin.razor*) to the app's *Shared* folder in the project root:</span></span>

```razor
@inject NavigationManager Navigation
@code {
    protected override void OnInitialized()
    {
        Navigation.NavigateTo("Identity/Account/Login?returnUrl=" +
            Uri.EscapeDataString(Navigation.Uri), true);
    }
}
```

`LoginDisplay`アプリの*共有*フォルダーにコンポーネント (*logindisplay. razor*) を追加します。 <span data-ttu-id="38f6d-169">[TokenProvider サービス](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app)は、ログアウトエンドポイントにポストする HTML フォームの XSRF トークンを提供し Identity ます。</span><span class="sxs-lookup"><span data-stu-id="38f6d-169">The [TokenProvider service](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app) provides the XSRF token for the HTML form that POSTs to Identity's logout endpoint:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@inject NavigationManager Navigation
@inject TokenProvider TokenProvider

<AuthorizeView>
    <Authorized>
        <a href="Identity/Account/Manage/Index">
            Hello, @context.User.Identity.Name!
        </a>
        <form action="/Identity/Account/Logout?returnUrl=%2F" method="post">
            <button class="nav-link btn btn-link" type="submit">Logout</button>
            <input name="__RequestVerificationToken" type="hidden" 
                value="@TokenProvider.XsrfToken">
        </form>
    </Authorized>
    <NotAuthorized>
        <a href="Identity/Account/Register">Register</a>
        <a href="Identity/Account/Login">Login</a>
    </NotAuthorized>
</AuthorizeView>
```

<span data-ttu-id="38f6d-170">`MainLayout`コンポーネント (*Shared/mainlayout. razor*) で、 `LoginDisplay` 最上位の要素のコンテンツにコンポーネントを追加し `<div>` ます。</span><span class="sxs-lookup"><span data-stu-id="38f6d-170">In the `MainLayout` component (*Shared/MainLayout.razor*), add the `LoginDisplay` component to the top-row `<div>` element's content:</span></span>

```razor
<div class="top-row px-4 auth">
    <LoginDisplay />
    <a href="https://docs.microsoft.com/aspnet/" target="_blank">About</a>
</div>
```

### <a name="style-authentication-endpoints"></a><span data-ttu-id="38f6d-171">認証エンドポイントのスタイルを適用する</span><span class="sxs-lookup"><span data-stu-id="38f6d-171">Style authentication endpoints</span></span>

<span data-ttu-id="38f6d-172">ではページページが使用されるため、 Blazor Server Razor Identity ビジターがページとコンポーネントの間を移動すると、UI のスタイルが変更され Identity ます。</span><span class="sxs-lookup"><span data-stu-id="38f6d-172">Because Blazor Server uses Razor Pages Identity pages, the styling of the UI changes when a visitor navigates between Identity pages and components.</span></span> <span data-ttu-id="38f6d-173">Incongruous スタイルに対処するには、次の2つのオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="38f6d-173">You have two options to address the incongruous styles:</span></span>

#### <a name="build-identity-components"></a><span data-ttu-id="38f6d-174">ビルド Identity コンポーネント</span><span class="sxs-lookup"><span data-stu-id="38f6d-174">Build Identity components</span></span>

<span data-ttu-id="38f6d-175">ページではなくコンポーネントを使用する方法 Identity は、コンポーネントを構築することです Identity 。</span><span class="sxs-lookup"><span data-stu-id="38f6d-175">An approach to using components for Identity instead of pages is to build Identity components.</span></span> <span data-ttu-id="38f6d-176">`SignInManager`とは `UserManager` コンポーネントでサポートされていないため Razor 、アプリの API エンドポイントを使用して、 Blazor Server ユーザーアカウントの操作を処理します。</span><span class="sxs-lookup"><span data-stu-id="38f6d-176">Because `SignInManager` and `UserManager` aren't supported in Razor components, use API endpoints in the Blazor Server app to process user account actions.</span></span>

#### <a name="use-a-custom-layout-with-blazor-app-styles"></a><span data-ttu-id="38f6d-177">アプリスタイルでカスタムレイアウトを使用する Blazor</span><span class="sxs-lookup"><span data-stu-id="38f6d-177">Use a custom layout with Blazor app styles</span></span>

<span data-ttu-id="38f6d-178">Identityページのレイアウトとスタイルを変更して、既定のテーマを使用するページを生成することができ Blazor ます。</span><span class="sxs-lookup"><span data-stu-id="38f6d-178">The Identity pages layout and styles can be modified to produce pages that use the default Blazor theme.</span></span>

> [!NOTE]
> <span data-ttu-id="38f6d-179">このセクションの例は、カスタマイズの開始点にすぎません。</span><span class="sxs-lookup"><span data-stu-id="38f6d-179">The example in this section is merely a starting point for customization.</span></span> <span data-ttu-id="38f6d-180">最適なユーザーエクスペリエンスを実現するには、追加の作業が必要になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="38f6d-180">Additional work is likely required for the best user experience.</span></span>

<span data-ttu-id="38f6d-181">新しい `NavMenu_IdentityLayout` コンポーネント (*Shared/NavMenu_IdentityLayout razor*) を作成します。</span><span class="sxs-lookup"><span data-stu-id="38f6d-181">Create a new `NavMenu_IdentityLayout` component (*Shared/NavMenu_IdentityLayout.razor*).</span></span> <span data-ttu-id="38f6d-182">コンポーネントのマークアップとコードについては、アプリのコンポーネントの同じコンテンツを使用し `NavMenu` ます (*共有/ナビゲーションメニュー。 razor*)。</span><span class="sxs-lookup"><span data-stu-id="38f6d-182">For the markup and code of the component, use the same content of the app's `NavMenu` component (*Shared/NavMenu.razor*).</span></span> <span data-ttu-id="38f6d-183">コンポーネントの `NavLink` 自動リダイレクトが `RedirectToLogin` 認証または承認を必要とするコンポーネントに対して失敗するため、匿名にアクセスできないコンポーネントに対してを除去します。</span><span class="sxs-lookup"><span data-stu-id="38f6d-183">Strip out any `NavLink`s to components that can't be reached anonymously because automatic redirects in the `RedirectToLogin` component fail for components requiring authentication or authorization.</span></span>

<span data-ttu-id="38f6d-184">*Pages/Shared/Layout. cshtml*ファイルで、次の変更を行います。</span><span class="sxs-lookup"><span data-stu-id="38f6d-184">In the *Pages/Shared/Layout.cshtml* file, make the following changes:</span></span>

* <span data-ttu-id="38f6d-185">Razorタグヘルパーと*共有*フォルダー内のアプリのコンポーネントを使用するために、ファイルの先頭にディレクティブを追加します。</span><span class="sxs-lookup"><span data-stu-id="38f6d-185">Add Razor directives to the top of the file to use Tag Helpers and the app's components in the *Shared* folder:</span></span>

  ```cshtml
  @addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
  @using {APPLICATION ASSEMBLY}.Shared
  ```

  <span data-ttu-id="38f6d-186">`{APPLICATION ASSEMBLY}`をアプリのアセンブリ名に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="38f6d-186">Replace `{APPLICATION ASSEMBLY}` with the app's assembly name.</span></span>

* <span data-ttu-id="38f6d-187">`<base>`コンテンツにタグと Blazor スタイルシートを追加し `<link>` `<head>` ます。</span><span class="sxs-lookup"><span data-stu-id="38f6d-187">Add a `<base>` tag and Blazor stylesheet `<link>` to the `<head>` content:</span></span>

  ```cshtml
  <base href="~/" />
  <link rel="stylesheet" href="~/css/site.css" />
  ```

* <span data-ttu-id="38f6d-188">タグの内容 `<body>` を次のように変更します。</span><span class="sxs-lookup"><span data-stu-id="38f6d-188">Change the content of the `<body>` tag to the following:</span></span>

  ```cshtml
  <div class="sidebar" style="float:left">
      <component type="typeof(NavMenu_IdentityLayout)" 
          render-mode="ServerPrerendered" />
  </div>

  <div class="main" style="padding-left:250px">
      <div class="top-row px-4">
          @{
              var result = Engine.FindView(ViewContext, "_LoginPartial", 
                  isMainPage: false);
          }
          @if (result.Success)
          {
              await Html.RenderPartialAsync("_LoginPartial");
          }
          else
          {
              throw new InvalidOperationException("The default Identity UI " +
                  "layout requires a partial view '_LoginPartial'.");
          }
          <a href="https://docs.microsoft.com/aspnet/" target="_blank">About</a>
      </div>

      <div class="content px-4">
          @RenderBody()
      </div>
  </div>

  <script src="~/Identity/lib/jquery/dist/jquery.min.js"></script>
  <script src="~/Identity/lib/bootstrap/dist/js/bootstrap.bundle.min.js"></script>
  <script src="~/Identity/js/site.js" asp-append-version="true"></script>
  @RenderSection("Scripts", required: false)
  <script src="_framework/blazor.server.js"></script>
  ```

## <a name="scaffold-identity-into-a-blazor-server-project-with-authorization"></a><span data-ttu-id="38f6d-189">Identity Blazor Server 承認を使用してプロジェクトにスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="38f6d-189">Scaffold Identity into a Blazor Server project with authorization</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="38f6d-190">一部 Identity のオプションは、 *Areas/ Identity /IdentityHostingStartup.cs*で構成されています。</span><span class="sxs-lookup"><span data-stu-id="38f6d-190">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="38f6d-191">詳細については、「 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="38f6d-191">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="38f6d-192">完全な Identity UI ソースの作成</span><span class="sxs-lookup"><span data-stu-id="38f6d-192">Create full Identity UI source</span></span>

<span data-ttu-id="38f6d-193">UI の完全な制御を維持するには、scaffolder を実行し、 Identity Identity [**すべてのファイルを上書き**する] を選択します。</span><span class="sxs-lookup"><span data-stu-id="38f6d-193">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="38f6d-194">次の強調表示されたコードは、 Identity ASP.NET Core 2.1 web アプリで既定の UI をに置き換える変更を示して Identity います。</span><span class="sxs-lookup"><span data-stu-id="38f6d-194">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="38f6d-195">この操作を行うと、UI を完全に制御でき Identity ます。</span><span class="sxs-lookup"><span data-stu-id="38f6d-195">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="38f6d-196">既定値 Identity は、次のコードで置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="38f6d-196">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="38f6d-197">次のコードでは、 [Loginpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath)、 [logoutpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)、および[AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath)を設定しています。</span><span class="sxs-lookup"><span data-stu-id="38f6d-197">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="38f6d-198">`IEmailSender`の実装を登録します。次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="38f6d-198">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-a-page"></a><span data-ttu-id="38f6d-199">ページを無効にする</span><span class="sxs-lookup"><span data-stu-id="38f6d-199">Disable a page</span></span>

<span data-ttu-id="38f6d-200">このセクションでは、登録ページを無効にする方法について説明しますが、この方法を使用して任意のページを無効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="38f6d-200">This sections show how to disable the register page but the approach can be used to disable any page.</span></span>

<span data-ttu-id="38f6d-201">ユーザー登録を無効にするには:</span><span class="sxs-lookup"><span data-stu-id="38f6d-201">To disable user registration:</span></span>

* <span data-ttu-id="38f6d-202">スキャフォールディング Identity 。</span><span class="sxs-lookup"><span data-stu-id="38f6d-202">Scaffold Identity.</span></span> <span data-ttu-id="38f6d-203">Account、Account. Login、および Account. RegisterConfirmation を含めます。</span><span class="sxs-lookup"><span data-stu-id="38f6d-203">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="38f6d-204">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="38f6d-204">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="38f6d-205">ユーザーがこのエンドポイントから登録できないように、*区分/ Identity /Pages/Account/Register.cshtml.cs*を更新します。</span><span class="sxs-lookup"><span data-stu-id="38f6d-205">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="38f6d-206">次の変更との整合性を保つために、\*区分/////また Identity \*はを更新します。</span><span class="sxs-lookup"><span data-stu-id="38f6d-206">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="38f6d-207">*区分///////// Identity ////* から登録リンクをコメントアウトまたは削除します。</span><span class="sxs-lookup"><span data-stu-id="38f6d-207">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

  ```cshtml
  @*
  <p>
      <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
  </p>
  *@
  ```

* <span data-ttu-id="38f6d-208">[*区分/ページ/ Identity アカウント/* 登録の確認] ページを更新します。</span><span class="sxs-lookup"><span data-stu-id="38f6d-208">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="38f6d-209">コードとリンクを cshtml ファイルから削除します。</span><span class="sxs-lookup"><span data-stu-id="38f6d-209">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="38f6d-210">から確認コードを削除し `PageModel` ます。</span><span class="sxs-lookup"><span data-stu-id="38f6d-210">Remove the confirmation code from the `PageModel`:</span></span>

  ```csharp
   [AllowAnonymous]
    public class RegisterConfirmationModel : PageModel
    {
        public IActionResult OnGet()
        {  
            return Page();
        }
    }
  ```
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="38f6d-211">別のアプリを使用してユーザーを追加する</span><span class="sxs-lookup"><span data-stu-id="38f6d-211">Use another app to add users</span></span>

<span data-ttu-id="38f6d-212">Web アプリの外部にユーザーを追加するためのメカニズムを提供します。</span><span class="sxs-lookup"><span data-stu-id="38f6d-212">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="38f6d-213">ユーザーを追加するためのオプションは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="38f6d-213">Options to add users include:</span></span>

* <span data-ttu-id="38f6d-214">専用管理者 web アプリ。</span><span class="sxs-lookup"><span data-stu-id="38f6d-214">A dedicated admin web app.</span></span>
* <span data-ttu-id="38f6d-215">コンソールアプリ。</span><span class="sxs-lookup"><span data-stu-id="38f6d-215">A console app.</span></span>

<span data-ttu-id="38f6d-216">次のコードは、ユーザーを追加する方法の概要を示しています。</span><span class="sxs-lookup"><span data-stu-id="38f6d-216">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="38f6d-217">ユーザーの一覧がメモリに読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="38f6d-217">A list of users is read into memory.</span></span>
* <span data-ttu-id="38f6d-218">ユーザーごとに一意の強力なパスワードが生成されます。</span><span class="sxs-lookup"><span data-stu-id="38f6d-218">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="38f6d-219">ユーザーがデータベースに追加され Identity ます。</span><span class="sxs-lookup"><span data-stu-id="38f6d-219">The user is added to the Identity database.</span></span>
* <span data-ttu-id="38f6d-220">ユーザーに通知され、パスワードを変更するように指示されます。</span><span class="sxs-lookup"><span data-stu-id="38f6d-220">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="38f6d-221">次のコードは、ユーザーの追加の概要を示しています。</span><span class="sxs-lookup"><span data-stu-id="38f6d-221">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="38f6d-222">同様の方法で、運用環境のシナリオにも対応できます。</span><span class="sxs-lookup"><span data-stu-id="38f6d-222">A similar approach can be followed for production scenarios.</span></span>

## <a name="prevent-publish-of-static-identity-assets"></a><span data-ttu-id="38f6d-223">静的アセットの発行を禁止する Identity</span><span class="sxs-lookup"><span data-stu-id="38f6d-223">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="38f6d-224">静的なアセットが web ルートに発行されないようにするに Identity は、「」を参照してください <xref:security/authentication/identity#prevent-publish-of-static-identity-assets> 。</span><span class="sxs-lookup"><span data-stu-id="38f6d-224">To prevent publishing static Identity assets to the web root, see <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="38f6d-225">その他の資料</span><span class="sxs-lookup"><span data-stu-id="38f6d-225">Additional resources</span></span>

* [<span data-ttu-id="38f6d-226">ASP.NET Core 2.1 以降に認証コードが変更された</span><span class="sxs-lookup"><span data-stu-id="38f6d-226">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="38f6d-227">ASP.NET Core 2.1 以降では、 [ Razor クラスライブラリ](xref:razor-pages/ui-class)として[ASP.NET Core Identity ](xref:security/authentication/identity)が提供されます。</span><span class="sxs-lookup"><span data-stu-id="38f6d-227">ASP.NET Core 2.1 and later provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="38f6d-228">を含むアプリケーション Identity では、scaffolder を適用して、 Identity Razor クラスライブラリ (rcl) に含まれているソースコードを選択的に追加できます。</span><span class="sxs-lookup"><span data-stu-id="38f6d-228">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="38f6d-229">コードを変更して動作を変更できるように、ソース コードを生成できます。</span><span class="sxs-lookup"><span data-stu-id="38f6d-229">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="38f6d-230">たとえば、登録で使用するコードを生成するようにスキャフォルダーに指示できます。</span><span class="sxs-lookup"><span data-stu-id="38f6d-230">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="38f6d-231">生成されたコードは、Identity RCL の同じコードよりも優先されます。</span><span class="sxs-lookup"><span data-stu-id="38f6d-231">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="38f6d-232">UI を完全に制御し、既定の RCL を使用しないようにするには、「[完全な ID UI ソースの作成](#full)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="38f6d-232">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="38f6d-233">認証を含ま**ない**アプリケーションでは、scaffolder を適用して rcl パッケージを追加でき Identity ます。</span><span class="sxs-lookup"><span data-stu-id="38f6d-233">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="38f6d-234">生成される Identity コードの選択オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="38f6d-234">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="38f6d-235">Scaffolder は、必要なコードの大部分を生成しますが、プロセスを完了するにはプロジェクトを更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="38f6d-235">Although the scaffolder generates most of the necessary code, you'll have to update your project to complete the process.</span></span> <span data-ttu-id="38f6d-236">このドキュメントでは、スキャフォールディングの更新を完了するために必要な手順について説明し Identity ます。</span><span class="sxs-lookup"><span data-stu-id="38f6d-236">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="38f6d-237">Scaffolder を Identity 実行すると、プロジェクトディレクトリに*ScaffoldingReadme.txt*ファイルが作成されます。</span><span class="sxs-lookup"><span data-stu-id="38f6d-237">When the Identity scaffolder is run, a *ScaffoldingReadme.txt* file is created in the project directory.</span></span> <span data-ttu-id="38f6d-238">*ScaffoldingReadme.txt*ファイルには、スキャフォールディングの更新を完了するために必要な手順に関する一般的な指示が含まれてい Identity ます。</span><span class="sxs-lookup"><span data-stu-id="38f6d-238">The *ScaffoldingReadme.txt* file contains general instructions on what's needed to complete the Identity scaffolding update.</span></span> <span data-ttu-id="38f6d-239">このドキュメントには、 *ScaffoldingReadme.txt*ファイルよりも完全な手順が含まれています。</span><span class="sxs-lookup"><span data-stu-id="38f6d-239">This document contains more complete instructions than the *ScaffoldingReadme.txt* file.</span></span>

<span data-ttu-id="38f6d-240">ファイルの違いを示すソース管理システムを使用して、変更を元に戻すことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="38f6d-240">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="38f6d-241">Scaffolder を実行した後に変更を確認し Identity ます。</span><span class="sxs-lookup"><span data-stu-id="38f6d-241">Inspect the changes after running the Identity scaffolder.</span></span>

> [!NOTE]
> <span data-ttu-id="38f6d-242">サービスは、 [2 要素認証](xref:security/authentication/identity-enable-qrcodes)、アカウントの[確認とパスワードの回復](xref:security/authentication/accconfirm)、およびの他のセキュリティ機能を使用する場合に必要です Identity 。</span><span class="sxs-lookup"><span data-stu-id="38f6d-242">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="38f6d-243">サービスまたはサービススタブは、スキャフォールディング時に生成されません Identity 。</span><span class="sxs-lookup"><span data-stu-id="38f6d-243">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="38f6d-244">これらの機能を有効にするサービスは、手動で追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="38f6d-244">Services to enable these features must be added manually.</span></span> <span data-ttu-id="38f6d-245">たとえば、「[電子メールの確認を要求する](xref:security/authentication/accconfirm#require-email-confirmation)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="38f6d-245">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="38f6d-246">Identity空のプロジェクトにスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="38f6d-246">Scaffold Identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="38f6d-247">次の強調表示された呼び出しをクラスに追加し `Startup` ます。</span><span class="sxs-lookup"><span data-stu-id="38f6d-247">Add the following highlighted calls to the `Startup` class:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="38f6d-248">Identity Razor 既存の承認なしでプロジェクトにスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="38f6d-248">Scaffold Identity into a Razor project without existing authorization</span></span>

<!--  Updated for 3.0
set projNam=RPnoAuth
set projType=webapp

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Identity<span data-ttu-id="38f6d-249">は、 *Areas/ Identity /IdentityHostingStartup.cs*で構成されます。</span><span class="sxs-lookup"><span data-stu-id="38f6d-249"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="38f6d-250">詳細については、「 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="38f6d-250">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="38f6d-251">移行、UseAuthentication、およびレイアウト</span><span class="sxs-lookup"><span data-stu-id="38f6d-251">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="38f6d-252">認証を有効にする</span><span class="sxs-lookup"><span data-stu-id="38f6d-252">Enable authentication</span></span>

<span data-ttu-id="38f6d-253">`Configure`クラスのメソッドで `Startup` 、次のように[useauthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)を呼び出し `UseStaticFiles` ます。</span><span class="sxs-lookup"><span data-stu-id="38f6d-253">In the `Configure` method of the `Startup` class, call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="38f6d-254">レイアウトの変更</span><span class="sxs-lookup"><span data-stu-id="38f6d-254">Layout changes</span></span>

<span data-ttu-id="38f6d-255">省略可能: 次のように、ログイン部分 ( `_LoginPartial` ) をレイアウトファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="38f6d-255">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="38f6d-256">Identity Razor 承認を使用してプロジェクトにスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="38f6d-256">Scaffold Identity into a Razor project with authorization</span></span>

<!--
Use >=2.1: dotnet new webapp -au Individual -o RPauth
Use = 2.0: dotnet new razor -au Individual -o RPauth
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="38f6d-257">一部 Identity のオプションは、 *Areas/ Identity /IdentityHostingStartup.cs*で構成されています。</span><span class="sxs-lookup"><span data-stu-id="38f6d-257">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="38f6d-258">詳細については、「 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="38f6d-258">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="38f6d-259">Identity既存の承認なしで MVC プロジェクトにスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="38f6d-259">Scaffold Identity into an MVC project without existing authorization</span></span>

<!--
set projNam=MvcNoAuth
set projType=mvc
set version=2.1.0

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v %version%
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="38f6d-260">省略可能: `_LoginPartial` *Views/Shared/_Layout cshtml*ファイルにログイン部分 () を追加します。</span><span class="sxs-lookup"><span data-stu-id="38f6d-260">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* <span data-ttu-id="38f6d-261">*Pages/shared/_LoginPartial cshtml*ファイルを*Views/shared/_LoginPartial*に移動します。</span><span class="sxs-lookup"><span data-stu-id="38f6d-261">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

Identity<span data-ttu-id="38f6d-262">は、 *Areas/ Identity /IdentityHostingStartup.cs*で構成されます。</span><span class="sxs-lookup"><span data-stu-id="38f6d-262"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="38f6d-263">詳細については、「IHostingStartup」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="38f6d-263">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="38f6d-264">次の後に[Useauthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)を呼び出し `UseStaticFiles` ます。</span><span class="sxs-lookup"><span data-stu-id="38f6d-264">Call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="38f6d-265">Identity承認を使用した MVC プロジェクトへのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="38f6d-265">Scaffold Identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="38f6d-266">*ページ/共有*フォルダーとそのフォルダー内のファイルを削除します。</span><span class="sxs-lookup"><span data-stu-id="38f6d-266">Delete the *Pages/Shared* folder and the files in that folder.</span></span>

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="38f6d-267">完全な Identity UI ソースの作成</span><span class="sxs-lookup"><span data-stu-id="38f6d-267">Create full Identity UI source</span></span>

<span data-ttu-id="38f6d-268">UI の完全な制御を維持するには、scaffolder を実行し、 Identity Identity [**すべてのファイルを上書き**する] を選択します。</span><span class="sxs-lookup"><span data-stu-id="38f6d-268">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="38f6d-269">次の強調表示されたコードは、 Identity ASP.NET Core 2.1 web アプリで既定の UI をに置き換える変更を示して Identity います。</span><span class="sxs-lookup"><span data-stu-id="38f6d-269">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="38f6d-270">この操作を行うと、UI を完全に制御でき Identity ます。</span><span class="sxs-lookup"><span data-stu-id="38f6d-270">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="38f6d-271">既定値 Identity は、次のコードで置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="38f6d-271">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="38f6d-272">次のコードでは、 [Loginpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath)、 [logoutpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)、および[AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath)を設定しています。</span><span class="sxs-lookup"><span data-stu-id="38f6d-272">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="38f6d-273">`IEmailSender`の実装を登録します。次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="38f6d-273">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a><span data-ttu-id="38f6d-274">登録の無効化ページ</span><span class="sxs-lookup"><span data-stu-id="38f6d-274">Disable register page</span></span>

<span data-ttu-id="38f6d-275">ユーザー登録を無効にするには:</span><span class="sxs-lookup"><span data-stu-id="38f6d-275">To disable user registration:</span></span>

* <span data-ttu-id="38f6d-276">スキャフォールディング Identity 。</span><span class="sxs-lookup"><span data-stu-id="38f6d-276">Scaffold Identity.</span></span> <span data-ttu-id="38f6d-277">Account、Account. Login、および Account. RegisterConfirmation を含めます。</span><span class="sxs-lookup"><span data-stu-id="38f6d-277">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="38f6d-278">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="38f6d-278">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="38f6d-279">ユーザーがこのエンドポイントから登録できないように、*区分/ Identity /Pages/Account/Register.cshtml.cs*を更新します。</span><span class="sxs-lookup"><span data-stu-id="38f6d-279">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="38f6d-280">次の変更との整合性を保つために、\*区分/////また Identity \*はを更新します。</span><span class="sxs-lookup"><span data-stu-id="38f6d-280">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="38f6d-281">*区分///////// Identity ////* から登録リンクをコメントアウトまたは削除します。</span><span class="sxs-lookup"><span data-stu-id="38f6d-281">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="38f6d-282">[*区分/ページ/ Identity アカウント/* 登録の確認] ページを更新します。</span><span class="sxs-lookup"><span data-stu-id="38f6d-282">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="38f6d-283">コードとリンクを cshtml ファイルから削除します。</span><span class="sxs-lookup"><span data-stu-id="38f6d-283">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="38f6d-284">から確認コードを削除し `PageModel` ます。</span><span class="sxs-lookup"><span data-stu-id="38f6d-284">Remove the confirmation code from the `PageModel`:</span></span>

  ```csharp
   [AllowAnonymous]
    public class RegisterConfirmationModel : PageModel
    {
        public IActionResult OnGet()
        {  
            return Page();
        }
    }
  ```
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="38f6d-285">別のアプリを使用してユーザーを追加する</span><span class="sxs-lookup"><span data-stu-id="38f6d-285">Use another app to add users</span></span>

<span data-ttu-id="38f6d-286">Web アプリの外部にユーザーを追加するためのメカニズムを提供します。</span><span class="sxs-lookup"><span data-stu-id="38f6d-286">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="38f6d-287">ユーザーを追加するためのオプションは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="38f6d-287">Options to add users include:</span></span>

* <span data-ttu-id="38f6d-288">専用管理者 web アプリ。</span><span class="sxs-lookup"><span data-stu-id="38f6d-288">A dedicated admin web app.</span></span>
* <span data-ttu-id="38f6d-289">コンソールアプリ。</span><span class="sxs-lookup"><span data-stu-id="38f6d-289">A console app.</span></span>

<span data-ttu-id="38f6d-290">次のコードは、ユーザーを追加する方法の概要を示しています。</span><span class="sxs-lookup"><span data-stu-id="38f6d-290">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="38f6d-291">ユーザーの一覧がメモリに読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="38f6d-291">A list of users is read into memory.</span></span>
* <span data-ttu-id="38f6d-292">ユーザーごとに一意の強力なパスワードが生成されます。</span><span class="sxs-lookup"><span data-stu-id="38f6d-292">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="38f6d-293">ユーザーがデータベースに追加され Identity ます。</span><span class="sxs-lookup"><span data-stu-id="38f6d-293">The user is added to the Identity database.</span></span>
* <span data-ttu-id="38f6d-294">ユーザーに通知され、パスワードを変更するように指示されます。</span><span class="sxs-lookup"><span data-stu-id="38f6d-294">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="38f6d-295">次のコードは、ユーザーの追加の概要を示しています。</span><span class="sxs-lookup"><span data-stu-id="38f6d-295">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="38f6d-296">同様の方法で、運用環境のシナリオにも対応できます。</span><span class="sxs-lookup"><span data-stu-id="38f6d-296">A similar approach can be followed for production scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="38f6d-297">その他の資料</span><span class="sxs-lookup"><span data-stu-id="38f6d-297">Additional resources</span></span>

* [<span data-ttu-id="38f6d-298">ASP.NET Core 2.1 以降に認証コードが変更された</span><span class="sxs-lookup"><span data-stu-id="38f6d-298">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end
