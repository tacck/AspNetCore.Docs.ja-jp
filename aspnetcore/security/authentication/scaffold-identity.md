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
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/scaffold-identity
ms.openlocfilehash: 116e5d27e7585e9168db433480c3a5e9d08379f3
ms.sourcegitcommit: 67eadd7bf28eae0b8786d85e90a7df811ffe5904
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/05/2020
ms.locfileid: "84454674"
---
# <a name="scaffold-identity-in-aspnet-core-projects"></a><span data-ttu-id="c232b-103">IdentityASP.NET Core プロジェクトでのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="c232b-103">Scaffold Identity in ASP.NET Core projects</span></span>

<span data-ttu-id="c232b-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="c232b-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c232b-105">ASP.NET Core は、 [ Razor クラスライブラリ](xref:razor-pages/ui-class)として[ASP.NET Core Identity ](xref:security/authentication/identity)を提供します。</span><span class="sxs-lookup"><span data-stu-id="c232b-105">ASP.NET Core provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="c232b-106">を含むアプリケーション Identity では、scaffolder を適用して、 Identity Razor クラスライブラリ (rcl) に含まれているソースコードを選択的に追加できます。</span><span class="sxs-lookup"><span data-stu-id="c232b-106">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="c232b-107">コードを変更して動作を変更できるように、ソース コードを生成できます。</span><span class="sxs-lookup"><span data-stu-id="c232b-107">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="c232b-108">たとえば、登録で使用するコードを生成するようにスキャフォルダーに指示できます。</span><span class="sxs-lookup"><span data-stu-id="c232b-108">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="c232b-109">生成されたコードは、Identity RCL の同じコードよりも優先されます。</span><span class="sxs-lookup"><span data-stu-id="c232b-109">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="c232b-110">UI を完全に制御し、既定の RCL を使用しないようにするには、「[完全な Identity ui ソースの作成](#full)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="c232b-110">To gain full control of the UI and not use the default RCL, see the section [Create full Identity UI source](#full).</span></span>

<span data-ttu-id="c232b-111">認証を含ま**ない**アプリケーションでは、scaffolder を適用して rcl パッケージを追加でき Identity ます。</span><span class="sxs-lookup"><span data-stu-id="c232b-111">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="c232b-112">生成される Identity コードの選択オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="c232b-112">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="c232b-113">Scaffolder は、必要なコードの大部分を生成しますが、プロセスを完了するにはプロジェクトを更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c232b-113">Although the scaffolder generates most of the necessary code, you need to update your project to complete the process.</span></span> <span data-ttu-id="c232b-114">このドキュメントでは、スキャフォールディングの更新を完了するために必要な手順について説明し Identity ます。</span><span class="sxs-lookup"><span data-stu-id="c232b-114">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="c232b-115">ファイルの違いを示すソース管理システムを使用して、変更を元に戻すことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="c232b-115">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="c232b-116">Scaffolder を実行した後に変更を確認し Identity ます。</span><span class="sxs-lookup"><span data-stu-id="c232b-116">Inspect the changes after running the Identity scaffolder.</span></span>

<span data-ttu-id="c232b-117">サービスは、 [2 要素認証](xref:security/authentication/identity-enable-qrcodes)、アカウントの[確認とパスワードの回復](xref:security/authentication/accconfirm)、およびの他のセキュリティ機能を使用する場合に必要です Identity 。</span><span class="sxs-lookup"><span data-stu-id="c232b-117">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="c232b-118">サービスまたはサービススタブは、スキャフォールディング時に生成されません Identity 。</span><span class="sxs-lookup"><span data-stu-id="c232b-118">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="c232b-119">これらの機能を有効にするサービスは、手動で追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c232b-119">Services to enable these features must be added manually.</span></span> <span data-ttu-id="c232b-120">たとえば、「[電子メールの確認を要求する](xref:security/authentication/accconfirm#require-email-confirmation)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c232b-120">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

<span data-ttu-id="c232b-121">Identity新しいデータコンテキストを使用して、既存の個別のアカウントを持つプロジェクトにスキャフォールディングする場合:</span><span class="sxs-lookup"><span data-stu-id="c232b-121">When scaffolding Identity with a new data context into a project with existing individual accounts:</span></span>

* <span data-ttu-id="c232b-122">で `Startup.ConfigureServices` 、の呼び出しを削除します。</span><span class="sxs-lookup"><span data-stu-id="c232b-122">In `Startup.ConfigureServices`, remove the calls to:</span></span>
  * `AddDbContext`
  * `AddDefaultIdentity`

<span data-ttu-id="c232b-123">たとえば、 `AddDbContext` と `AddDefaultIdentity` は次のコードでコメントアウトされます。</span><span class="sxs-lookup"><span data-stu-id="c232b-123">For example, `AddDbContext` and `AddDefaultIdentity` are commented out in the following code:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRemove.cs?name=snippet)]

<span data-ttu-id="c232b-124">上記のコードは、*区分/ Identity /IdentityHostingStartup.cs*で重複するコードをコメントアウトします。</span><span class="sxs-lookup"><span data-stu-id="c232b-124">The preceeding code comments out the code that is duplicated in *Areas/Identity/IdentityHostingStartup.cs*</span></span>

<span data-ttu-id="c232b-125">通常、個別のアカウントで作成されたアプリは、新しいデータコンテキストを作成し***ない***ようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="c232b-125">Typically, apps that were created with individual accounts should ***not*** create a new data context.</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="c232b-126">Identity空のプロジェクトにスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="c232b-126">Scaffold Identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="c232b-127">`Startup`次のようなコードでクラスを更新します。</span><span class="sxs-lookup"><span data-stu-id="c232b-127">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="c232b-128">Identity Razor 既存の承認なしでプロジェクトにスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="c232b-128">Scaffold Identity into a Razor project without existing authorization</span></span>

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

Identity<span data-ttu-id="c232b-129">は、 *Areas/ Identity /IdentityHostingStartup.cs*で構成されます。</span><span class="sxs-lookup"><span data-stu-id="c232b-129"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="c232b-130">詳細については、「 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c232b-130">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="c232b-131">移行、UseAuthentication、およびレイアウト</span><span class="sxs-lookup"><span data-stu-id="c232b-131">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="c232b-132">認証を有効にする</span><span class="sxs-lookup"><span data-stu-id="c232b-132">Enable authentication</span></span>

<span data-ttu-id="c232b-133">`Startup`次のようなコードでクラスを更新します。</span><span class="sxs-lookup"><span data-stu-id="c232b-133">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="c232b-134">レイアウトの変更</span><span class="sxs-lookup"><span data-stu-id="c232b-134">Layout changes</span></span>

<span data-ttu-id="c232b-135">省略可能: 次のように、ログイン部分 ( `_LoginPartial` ) をレイアウトファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="c232b-135">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="c232b-136">Identity Razor 承認を使用してプロジェクトにスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="c232b-136">Scaffold Identity into a Razor project with authorization</span></span>

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

<span data-ttu-id="c232b-137">一部 Identity のオプションは、 *Areas/ Identity /IdentityHostingStartup.cs*で構成されています。</span><span class="sxs-lookup"><span data-stu-id="c232b-137">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="c232b-138">詳細については、「 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c232b-138">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="c232b-139">Identity既存の承認なしで MVC プロジェクトにスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="c232b-139">Scaffold Identity into an MVC project without existing authorization</span></span>

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

<span data-ttu-id="c232b-140">省略可能: `_LoginPartial` *Views/Shared/_Layout cshtml*ファイルにログイン部分 () を追加します。</span><span class="sxs-lookup"><span data-stu-id="c232b-140">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* <span data-ttu-id="c232b-141">*Pages/shared/_LoginPartial cshtml*ファイルを*Views/shared/_LoginPartial*に移動します。</span><span class="sxs-lookup"><span data-stu-id="c232b-141">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

Identity<span data-ttu-id="c232b-142">は、 *Areas/ Identity /IdentityHostingStartup.cs*で構成されます。</span><span class="sxs-lookup"><span data-stu-id="c232b-142"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="c232b-143">詳細については、「IHostingStartup」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c232b-143">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="c232b-144">`Startup`次のようなコードでクラスを更新します。</span><span class="sxs-lookup"><span data-stu-id="c232b-144">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="c232b-145">Identity承認を使用した MVC プロジェクトへのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="c232b-145">Scaffold Identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

## <a name="scaffold-identity-into-a-blazor-server-project-without-existing-authorization"></a><span data-ttu-id="c232b-146">Identity Blazor 既存の承認のないサーバープロジェクトへのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="c232b-146">Scaffold Identity into a Blazor Server project without existing authorization</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Identity<span data-ttu-id="c232b-147">は、 *Areas/ Identity /IdentityHostingStartup.cs*で構成されます。</span><span class="sxs-lookup"><span data-stu-id="c232b-147"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="c232b-148">詳細については、「 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c232b-148">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

### <a name="migrations"></a><span data-ttu-id="c232b-149">移行</span><span class="sxs-lookup"><span data-stu-id="c232b-149">Migrations</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

### <a name="pass-an-xsrf-token-to-the-app"></a><span data-ttu-id="c232b-150">XSRF トークンをアプリに渡す</span><span class="sxs-lookup"><span data-stu-id="c232b-150">Pass an XSRF token to the app</span></span>

<span data-ttu-id="c232b-151">トークンは、次のようにコンポーネントに渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="c232b-151">Tokens can be passed to components:</span></span>

* <span data-ttu-id="c232b-152">認証トークンがプロビジョニングされ、認証 cookie に保存されると、コンポーネントに渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="c232b-152">When authentication tokens are provisioned and saved to the authentication cookie, they can be passed to components.</span></span>
* Razor<span data-ttu-id="c232b-153">コンポーネントでを直接使用することはできない `HttpContext` ため、のログアウトエンドポイントをに送信するための[要求防止偽造 (XSRF) トークン](xref:security/anti-request-forgery)を取得する方法はありません Identity `/Identity/Account/Logout` 。</span><span class="sxs-lookup"><span data-stu-id="c232b-153"> components can't use `HttpContext` directly, so there's no way to obtain an [anti-request forgery (XSRF) token](xref:security/anti-request-forgery) to POST to Identity's logout endpoint at `/Identity/Account/Logout`.</span></span> <span data-ttu-id="c232b-154">XSRF トークンは、コンポーネントに渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="c232b-154">An XSRF token can be passed to components.</span></span>

<span data-ttu-id="c232b-155">詳細については、 <xref:security/blazor/server/index#pass-tokens-to-a-blazor-server-app> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c232b-155">For more information, see <xref:security/blazor/server/index#pass-tokens-to-a-blazor-server-app>.</span></span>

<span data-ttu-id="c232b-156">*Pages/_Host cshtml*ファイルで、クラスおよびクラスに追加した後、トークンを設定し `InitialApplicationState` `TokenProvider` ます。</span><span class="sxs-lookup"><span data-stu-id="c232b-156">In the *Pages/_Host.cshtml* file, establish the token after adding it to the `InitialApplicationState` and `TokenProvider` classes:</span></span>

```csharp
@inject Microsoft.AspNetCore.Antiforgery.IAntiforgery Xsrf

...

var tokens = new InitialApplicationState
{
    ...

    XsrfToken = Xsrf.GetAndStoreTokens(HttpContext).RequestToken
};
```

<span data-ttu-id="c232b-157">`App`コンポーネント (*app.xaml*) を更新して、次の `InitialState.XsrfToken` ものを割り当てます。</span><span class="sxs-lookup"><span data-stu-id="c232b-157">Update the `App` component (*App.razor*) to assign the `InitialState.XsrfToken`:</span></span>

```csharp
@inject TokenProvider TokenProvider

...

TokenProvider.XsrfToken = InitialState.XsrfToken;
```

<span data-ttu-id="c232b-158">トピック「」で説明されているサービスは、 `TokenProvider` `LoginDisplay` 次の[レイアウトと認証フローの変更](#layout-and-authentication-flow-changes)セクションのコンポーネントで使用されます。</span><span class="sxs-lookup"><span data-stu-id="c232b-158">The `TokenProvider` service demonstrated in the topic is used in the `LoginDisplay` component in the following [Layout and authentication flow changes](#layout-and-authentication-flow-changes) section.</span></span>

### <a name="enable-authentication"></a><span data-ttu-id="c232b-159">認証を有効にする</span><span class="sxs-lookup"><span data-stu-id="c232b-159">Enable authentication</span></span>

<span data-ttu-id="c232b-160">クラスの場合 `Startup` :</span><span class="sxs-lookup"><span data-stu-id="c232b-160">In the `Startup` class:</span></span>

* <span data-ttu-id="c232b-161">ページサービスがに追加されていることを確認 Razor `Startup.ConfigureServices` します。</span><span class="sxs-lookup"><span data-stu-id="c232b-161">Confirm that Razor Pages services are added in `Startup.ConfigureServices`.</span></span>
* <span data-ttu-id="c232b-162">[TokenProvider](xref:security/blazor/server/index#pass-tokens-to-a-blazor-server-app)を使用する場合は、サービスを登録します。</span><span class="sxs-lookup"><span data-stu-id="c232b-162">If using the [TokenProvider](xref:security/blazor/server/index#pass-tokens-to-a-blazor-server-app), register the service.</span></span>
* <span data-ttu-id="c232b-163">`UseDatabaseErrorPage`開発環境用のアプリケーションビルダーでを呼び出し `Startup.Configure` ます。</span><span class="sxs-lookup"><span data-stu-id="c232b-163">Call `UseDatabaseErrorPage` on the application builder in `Startup.Configure` for the Development environment.</span></span>
* <span data-ttu-id="c232b-164">`UseAuthentication`との後にを呼び出し `UseAuthorization` `UseRouting` ます。</span><span class="sxs-lookup"><span data-stu-id="c232b-164">Call `UseAuthentication` and `UseAuthorization` after `UseRouting`.</span></span>
* <span data-ttu-id="c232b-165">ページのエンドポイントを追加 Razor します。</span><span class="sxs-lookup"><span data-stu-id="c232b-165">Add an endpoint for Razor Pages.</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupBlazor.cs?highlight=3,6,14,27-28,32)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-and-authentication-flow-changes"></a><span data-ttu-id="c232b-166">レイアウトと認証のフローの変更</span><span class="sxs-lookup"><span data-stu-id="c232b-166">Layout and authentication flow changes</span></span>

<span data-ttu-id="c232b-167">`RedirectToLogin`プロジェクトルートのアプリの*共有*フォルダーにコンポーネント (*redirecttologin. razor*) を追加します。</span><span class="sxs-lookup"><span data-stu-id="c232b-167">Add a `RedirectToLogin` component (*RedirectToLogin.razor*) to the app's *Shared* folder in the project root:</span></span>

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

`LoginDisplay`アプリの*共有*フォルダーにコンポーネント (*logindisplay. razor*) を追加します。 <span data-ttu-id="c232b-169">[TokenProvider サービス](xref:security/blazor/server/index#pass-tokens-to-a-blazor-server-app)は、ログアウトエンドポイントにポストする HTML フォームの XSRF トークンを提供し Identity ます。</span><span class="sxs-lookup"><span data-stu-id="c232b-169">The [TokenProvider service](xref:security/blazor/server/index#pass-tokens-to-a-blazor-server-app) provides the XSRF token for the HTML form that POSTs to Identity's logout endpoint:</span></span>

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

<span data-ttu-id="c232b-170">`MainLayout`コンポーネント (*Shared/mainlayout. razor*) で、 `LoginDisplay` 最上位の要素のコンテンツにコンポーネントを追加し `<div>` ます。</span><span class="sxs-lookup"><span data-stu-id="c232b-170">In the `MainLayout` component (*Shared/MainLayout.razor*), add the `LoginDisplay` component to the top-row `<div>` element's content:</span></span>

```razor
<div class="top-row px-4 auth">
    <LoginDisplay />
    <a href="https://docs.microsoft.com/aspnet/" target="_blank">About</a>
</div>
```

### <a name="style-authentication-endpoints"></a><span data-ttu-id="c232b-171">認証エンドポイントのスタイルを適用する</span><span class="sxs-lookup"><span data-stu-id="c232b-171">Style authentication endpoints</span></span>

<span data-ttu-id="c232b-172">Blazorサーバーは Razor ページ Identity ページを使用するため、ビジターがページとコンポーネントの間を移動すると、UI のスタイルが変更され Identity ます。</span><span class="sxs-lookup"><span data-stu-id="c232b-172">Because Blazor Server uses Razor Pages Identity pages, the styling of the UI changes when a visitor navigates between Identity pages and components.</span></span> <span data-ttu-id="c232b-173">Incongruous スタイルに対処するには、次の2つのオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="c232b-173">You have two options to address the incongruous styles:</span></span>

#### <a name="build-identity-components"></a><span data-ttu-id="c232b-174">ビルド Identity コンポーネント</span><span class="sxs-lookup"><span data-stu-id="c232b-174">Build Identity components</span></span>

<span data-ttu-id="c232b-175">ページではなくコンポーネントを使用する方法 Identity は、コンポーネントを構築することです Identity 。</span><span class="sxs-lookup"><span data-stu-id="c232b-175">An approach to using components for Identity instead of pages is to build Identity components.</span></span> <span data-ttu-id="c232b-176">`SignInManager`とは `UserManager` コンポーネントでサポートされていないため Razor 、サーバーアプリの API エンドポイントを使用して、 Blazor ユーザーアカウントの操作を処理します。</span><span class="sxs-lookup"><span data-stu-id="c232b-176">Because `SignInManager` and `UserManager` aren't supported in Razor components, use API endpoints in the Blazor Server app to process user account actions.</span></span>

#### <a name="use-a-custom-layout-with-blazor-app-styles"></a><span data-ttu-id="c232b-177">アプリスタイルでカスタムレイアウトを使用する Blazor</span><span class="sxs-lookup"><span data-stu-id="c232b-177">Use a custom layout with Blazor app styles</span></span>

<span data-ttu-id="c232b-178">Identityページのレイアウトとスタイルを変更して、既定のテーマを使用するページを生成することができ Blazor ます。</span><span class="sxs-lookup"><span data-stu-id="c232b-178">The Identity pages layout and styles can be modified to produce pages that use the default Blazor theme.</span></span>

> [!NOTE]
> <span data-ttu-id="c232b-179">このセクションの例は、カスタマイズの開始点にすぎません。</span><span class="sxs-lookup"><span data-stu-id="c232b-179">The example in this section is merely a starting point for customization.</span></span> <span data-ttu-id="c232b-180">最適なユーザーエクスペリエンスを実現するには、追加の作業が必要になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="c232b-180">Additional work is likely required for the best user experience.</span></span>

<span data-ttu-id="c232b-181">新しい `NavMenu_IdentityLayout` コンポーネント (*Shared/NavMenu_IdentityLayout razor*) を作成します。</span><span class="sxs-lookup"><span data-stu-id="c232b-181">Create a new `NavMenu_IdentityLayout` component (*Shared/NavMenu_IdentityLayout.razor*).</span></span> <span data-ttu-id="c232b-182">コンポーネントのマークアップとコードについては、アプリのコンポーネントの同じコンテンツを使用し `NavMenu` ます (*共有/ナビゲーションメニュー。 razor*)。</span><span class="sxs-lookup"><span data-stu-id="c232b-182">For the markup and code of the component, use the same content of the app's `NavMenu` component (*Shared/NavMenu.razor*).</span></span> <span data-ttu-id="c232b-183">コンポーネントの `NavLink` 自動リダイレクトが `RedirectToLogin` 認証または承認を必要とするコンポーネントに対して失敗するため、匿名にアクセスできないコンポーネントに対してを除去します。</span><span class="sxs-lookup"><span data-stu-id="c232b-183">Strip out any `NavLink`s to components that can't be reached anonymously because automatic redirects in the `RedirectToLogin` component fail for components requiring authentication or authorization.</span></span>

<span data-ttu-id="c232b-184">*Pages/Shared/Layout. cshtml*ファイルで、次の変更を行います。</span><span class="sxs-lookup"><span data-stu-id="c232b-184">In the *Pages/Shared/Layout.cshtml* file, make the following changes:</span></span>

* <span data-ttu-id="c232b-185">Razorタグヘルパーと*共有*フォルダー内のアプリのコンポーネントを使用するために、ファイルの先頭にディレクティブを追加します。</span><span class="sxs-lookup"><span data-stu-id="c232b-185">Add Razor directives to the top of the file to use Tag Helpers and the app's components in the *Shared* folder:</span></span>

  ```cshtml
  @addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
  @using {APPLICATION ASSEMBLY}.Shared
  ```

  <span data-ttu-id="c232b-186">`{APPLICATION ASSEMBLY}`をアプリのアセンブリ名に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="c232b-186">Replace `{APPLICATION ASSEMBLY}` with the app's assembly name.</span></span>

* <span data-ttu-id="c232b-187">`<base>`コンテンツにタグと Blazor スタイルシートを追加し `<link>` `<head>` ます。</span><span class="sxs-lookup"><span data-stu-id="c232b-187">Add a `<base>` tag and Blazor stylesheet `<link>` to the `<head>` content:</span></span>

  ```cshtml
  <base href="~/" />
  <link rel="stylesheet" href="~/css/site.css" />
  ```

* <span data-ttu-id="c232b-188">タグの内容 `<body>` を次のように変更します。</span><span class="sxs-lookup"><span data-stu-id="c232b-188">Change the content of the `<body>` tag to the following:</span></span>

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

## <a name="scaffold-identity-into-a-blazor-server-project-with-authorization"></a><span data-ttu-id="c232b-189">Identity Blazor 承認を使用したサーバープロジェクトへのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="c232b-189">Scaffold Identity into a Blazor Server project with authorization</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="c232b-190">一部 Identity のオプションは、 *Areas/ Identity /IdentityHostingStartup.cs*で構成されています。</span><span class="sxs-lookup"><span data-stu-id="c232b-190">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="c232b-191">詳細については、「 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c232b-191">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="c232b-192">完全な Identity UI ソースの作成</span><span class="sxs-lookup"><span data-stu-id="c232b-192">Create full Identity UI source</span></span>

<span data-ttu-id="c232b-193">UI の完全な制御を維持するには、scaffolder を実行し、 Identity Identity [**すべてのファイルを上書き**する] を選択します。</span><span class="sxs-lookup"><span data-stu-id="c232b-193">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="c232b-194">次の強調表示されたコードは、 Identity ASP.NET Core 2.1 web アプリで既定の UI をに置き換える変更を示して Identity います。</span><span class="sxs-lookup"><span data-stu-id="c232b-194">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="c232b-195">この操作を行うと、UI を完全に制御でき Identity ます。</span><span class="sxs-lookup"><span data-stu-id="c232b-195">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="c232b-196">既定値 Identity は、次のコードで置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="c232b-196">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="c232b-197">次のコードでは、 [Loginpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath)、 [logoutpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)、および[AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath)を設定しています。</span><span class="sxs-lookup"><span data-stu-id="c232b-197">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="c232b-198">`IEmailSender`の実装を登録します。次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="c232b-198">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a><span data-ttu-id="c232b-199">登録の無効化ページ</span><span class="sxs-lookup"><span data-stu-id="c232b-199">Disable register page</span></span>

<span data-ttu-id="c232b-200">ユーザー登録を無効にするには:</span><span class="sxs-lookup"><span data-stu-id="c232b-200">To disable user registration:</span></span>

* <span data-ttu-id="c232b-201">スキャフォールディング Identity 。</span><span class="sxs-lookup"><span data-stu-id="c232b-201">Scaffold Identity.</span></span> <span data-ttu-id="c232b-202">Account、Account. Login、および Account. RegisterConfirmation を含めます。</span><span class="sxs-lookup"><span data-stu-id="c232b-202">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="c232b-203">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="c232b-203">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="c232b-204">ユーザーがこのエンドポイントから登録できないように、*区分/ Identity /Pages/Account/Register.cshtml.cs*を更新します。</span><span class="sxs-lookup"><span data-stu-id="c232b-204">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="c232b-205">次の変更との整合性を保つために、\*区分/////また Identity \*はを更新します。</span><span class="sxs-lookup"><span data-stu-id="c232b-205">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="c232b-206">*区分///////// Identity ////* から登録リンクをコメントアウトまたは削除します。</span><span class="sxs-lookup"><span data-stu-id="c232b-206">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="c232b-207">[*区分/ページ/ Identity アカウント/* 登録の確認] ページを更新します。</span><span class="sxs-lookup"><span data-stu-id="c232b-207">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="c232b-208">コードとリンクを cshtml ファイルから削除します。</span><span class="sxs-lookup"><span data-stu-id="c232b-208">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="c232b-209">から確認コードを削除し `PageModel` ます。</span><span class="sxs-lookup"><span data-stu-id="c232b-209">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="c232b-210">別のアプリを使用してユーザーを追加する</span><span class="sxs-lookup"><span data-stu-id="c232b-210">Use another app to add users</span></span>

<span data-ttu-id="c232b-211">Web アプリの外部にユーザーを追加するためのメカニズムを提供します。</span><span class="sxs-lookup"><span data-stu-id="c232b-211">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="c232b-212">ユーザーを追加するためのオプションは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="c232b-212">Options to add users include:</span></span>

* <span data-ttu-id="c232b-213">専用管理者 web アプリ。</span><span class="sxs-lookup"><span data-stu-id="c232b-213">A dedicated admin web app.</span></span>
* <span data-ttu-id="c232b-214">コンソールアプリ。</span><span class="sxs-lookup"><span data-stu-id="c232b-214">A console app.</span></span>

<span data-ttu-id="c232b-215">次のコードは、ユーザーを追加する方法の概要を示しています。</span><span class="sxs-lookup"><span data-stu-id="c232b-215">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="c232b-216">ユーザーの一覧がメモリに読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="c232b-216">A list of users is read into memory.</span></span>
* <span data-ttu-id="c232b-217">ユーザーごとに一意の強力なパスワードが生成されます。</span><span class="sxs-lookup"><span data-stu-id="c232b-217">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="c232b-218">ユーザーがデータベースに追加され Identity ます。</span><span class="sxs-lookup"><span data-stu-id="c232b-218">The user is added to the Identity database.</span></span>
* <span data-ttu-id="c232b-219">ユーザーに通知され、パスワードを変更するように指示されます。</span><span class="sxs-lookup"><span data-stu-id="c232b-219">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="c232b-220">次のコードは、ユーザーの追加の概要を示しています。</span><span class="sxs-lookup"><span data-stu-id="c232b-220">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="c232b-221">同様の方法で、運用環境のシナリオにも対応できます。</span><span class="sxs-lookup"><span data-stu-id="c232b-221">A similar approach can be followed for production scenarios.</span></span>

## <a name="prevent-publish-of-static-identity-assets"></a><span data-ttu-id="c232b-222">静的アセットの発行を禁止する Identity</span><span class="sxs-lookup"><span data-stu-id="c232b-222">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="c232b-223">静的なアセットが web ルートに発行されないようにするに Identity は、「」を参照してください <xref:security/authentication/identity#prevent-publish-of-static-identity-assets> 。</span><span class="sxs-lookup"><span data-stu-id="c232b-223">To prevent publishing static Identity assets to the web root, see <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c232b-224">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="c232b-224">Additional resources</span></span>

* [<span data-ttu-id="c232b-225">ASP.NET Core 2.1 以降に認証コードが変更された</span><span class="sxs-lookup"><span data-stu-id="c232b-225">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c232b-226">ASP.NET Core 2.1 以降では、 [ Razor クラスライブラリ](xref:razor-pages/ui-class)として[ASP.NET Core Identity ](xref:security/authentication/identity)が提供されます。</span><span class="sxs-lookup"><span data-stu-id="c232b-226">ASP.NET Core 2.1 and later provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="c232b-227">を含むアプリケーション Identity では、scaffolder を適用して、 Identity Razor クラスライブラリ (rcl) に含まれているソースコードを選択的に追加できます。</span><span class="sxs-lookup"><span data-stu-id="c232b-227">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="c232b-228">コードを変更して動作を変更できるように、ソース コードを生成できます。</span><span class="sxs-lookup"><span data-stu-id="c232b-228">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="c232b-229">たとえば、登録で使用するコードを生成するようにスキャフォルダーに指示できます。</span><span class="sxs-lookup"><span data-stu-id="c232b-229">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="c232b-230">生成されたコードは、Identity RCL の同じコードよりも優先されます。</span><span class="sxs-lookup"><span data-stu-id="c232b-230">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="c232b-231">UI を完全に制御し、既定の RCL を使用しないようにするには、「[完全な ID UI ソースの作成](#full)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="c232b-231">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="c232b-232">認証を含ま**ない**アプリケーションでは、scaffolder を適用して rcl パッケージを追加でき Identity ます。</span><span class="sxs-lookup"><span data-stu-id="c232b-232">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="c232b-233">生成される Identity コードの選択オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="c232b-233">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="c232b-234">Scaffolder は、必要なコードの大部分を生成しますが、プロセスを完了するにはプロジェクトを更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c232b-234">Although the scaffolder generates most of the necessary code, you'll have to update your project to complete the process.</span></span> <span data-ttu-id="c232b-235">このドキュメントでは、スキャフォールディングの更新を完了するために必要な手順について説明し Identity ます。</span><span class="sxs-lookup"><span data-stu-id="c232b-235">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="c232b-236">Scaffolder を Identity 実行すると、 *ScaffoldingReadme*ファイルがプロジェクトディレクトリに作成されます。</span><span class="sxs-lookup"><span data-stu-id="c232b-236">When the Identity scaffolder is run, a *ScaffoldingReadme.txt* file is created in the project directory.</span></span> <span data-ttu-id="c232b-237">*ScaffoldingReadme*ファイルには、スキャフォールディングの更新を完了するために必要な手順に関する一般的な指示が含まれてい Identity ます。</span><span class="sxs-lookup"><span data-stu-id="c232b-237">The *ScaffoldingReadme.txt* file contains general instructions on what's needed to complete the Identity scaffolding update.</span></span> <span data-ttu-id="c232b-238">このドキュメントには、 *ScaffoldingReadme*ファイルよりも完全な手順が含まれています。</span><span class="sxs-lookup"><span data-stu-id="c232b-238">This document contains more complete instructions than the *ScaffoldingReadme.txt* file.</span></span>

<span data-ttu-id="c232b-239">ファイルの違いを示すソース管理システムを使用して、変更を元に戻すことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="c232b-239">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="c232b-240">Scaffolder を実行した後に変更を確認し Identity ます。</span><span class="sxs-lookup"><span data-stu-id="c232b-240">Inspect the changes after running the Identity scaffolder.</span></span>

> [!NOTE]
> <span data-ttu-id="c232b-241">サービスは、 [2 要素認証](xref:security/authentication/identity-enable-qrcodes)、アカウントの[確認とパスワードの回復](xref:security/authentication/accconfirm)、およびの他のセキュリティ機能を使用する場合に必要です Identity 。</span><span class="sxs-lookup"><span data-stu-id="c232b-241">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="c232b-242">サービスまたはサービススタブは、スキャフォールディング時に生成されません Identity 。</span><span class="sxs-lookup"><span data-stu-id="c232b-242">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="c232b-243">これらの機能を有効にするサービスは、手動で追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c232b-243">Services to enable these features must be added manually.</span></span> <span data-ttu-id="c232b-244">たとえば、「[電子メールの確認を要求する](xref:security/authentication/accconfirm#require-email-confirmation)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c232b-244">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="c232b-245">Identity空のプロジェクトにスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="c232b-245">Scaffold Identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="c232b-246">次の強調表示された呼び出しをクラスに追加し `Startup` ます。</span><span class="sxs-lookup"><span data-stu-id="c232b-246">Add the following highlighted calls to the `Startup` class:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="c232b-247">Identity Razor 既存の承認なしでプロジェクトにスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="c232b-247">Scaffold Identity into a Razor project without existing authorization</span></span>

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

Identity<span data-ttu-id="c232b-248">は、 *Areas/ Identity /IdentityHostingStartup.cs*で構成されます。</span><span class="sxs-lookup"><span data-stu-id="c232b-248"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="c232b-249">詳細については、「 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c232b-249">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="c232b-250">移行、UseAuthentication、およびレイアウト</span><span class="sxs-lookup"><span data-stu-id="c232b-250">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="c232b-251">認証を有効にする</span><span class="sxs-lookup"><span data-stu-id="c232b-251">Enable authentication</span></span>

<span data-ttu-id="c232b-252">`Configure`クラスのメソッドで `Startup` 、次のように[useauthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)を呼び出し `UseStaticFiles` ます。</span><span class="sxs-lookup"><span data-stu-id="c232b-252">In the `Configure` method of the `Startup` class, call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="c232b-253">レイアウトの変更</span><span class="sxs-lookup"><span data-stu-id="c232b-253">Layout changes</span></span>

<span data-ttu-id="c232b-254">省略可能: 次のように、ログイン部分 ( `_LoginPartial` ) をレイアウトファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="c232b-254">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="c232b-255">Identity Razor 承認を使用してプロジェクトにスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="c232b-255">Scaffold Identity into a Razor project with authorization</span></span>

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

<span data-ttu-id="c232b-256">一部 Identity のオプションは、 *Areas/ Identity /IdentityHostingStartup.cs*で構成されています。</span><span class="sxs-lookup"><span data-stu-id="c232b-256">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="c232b-257">詳細については、「 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c232b-257">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="c232b-258">Identity既存の承認なしで MVC プロジェクトにスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="c232b-258">Scaffold Identity into an MVC project without existing authorization</span></span>

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

<span data-ttu-id="c232b-259">省略可能: `_LoginPartial` *Views/Shared/_Layout cshtml*ファイルにログイン部分 () を追加します。</span><span class="sxs-lookup"><span data-stu-id="c232b-259">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* <span data-ttu-id="c232b-260">*Pages/shared/_LoginPartial cshtml*ファイルを*Views/shared/_LoginPartial*に移動します。</span><span class="sxs-lookup"><span data-stu-id="c232b-260">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

Identity<span data-ttu-id="c232b-261">は、 *Areas/ Identity /IdentityHostingStartup.cs*で構成されます。</span><span class="sxs-lookup"><span data-stu-id="c232b-261"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="c232b-262">詳細については、「IHostingStartup」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c232b-262">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="c232b-263">次の後に[Useauthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)を呼び出し `UseStaticFiles` ます。</span><span class="sxs-lookup"><span data-stu-id="c232b-263">Call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="c232b-264">Identity承認を使用した MVC プロジェクトへのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="c232b-264">Scaffold Identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="c232b-265">*ページ/共有*フォルダーとそのフォルダー内のファイルを削除します。</span><span class="sxs-lookup"><span data-stu-id="c232b-265">Delete the *Pages/Shared* folder and the files in that folder.</span></span>

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="c232b-266">完全な Identity UI ソースの作成</span><span class="sxs-lookup"><span data-stu-id="c232b-266">Create full Identity UI source</span></span>

<span data-ttu-id="c232b-267">UI の完全な制御を維持するには、scaffolder を実行し、 Identity Identity [**すべてのファイルを上書き**する] を選択します。</span><span class="sxs-lookup"><span data-stu-id="c232b-267">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="c232b-268">次の強調表示されたコードは、 Identity ASP.NET Core 2.1 web アプリで既定の UI をに置き換える変更を示して Identity います。</span><span class="sxs-lookup"><span data-stu-id="c232b-268">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="c232b-269">この操作を行うと、UI を完全に制御でき Identity ます。</span><span class="sxs-lookup"><span data-stu-id="c232b-269">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="c232b-270">既定値 Identity は、次のコードで置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="c232b-270">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="c232b-271">次のコードでは、 [Loginpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath)、 [logoutpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)、および[AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath)を設定しています。</span><span class="sxs-lookup"><span data-stu-id="c232b-271">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="c232b-272">`IEmailSender`の実装を登録します。次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="c232b-272">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a><span data-ttu-id="c232b-273">登録の無効化ページ</span><span class="sxs-lookup"><span data-stu-id="c232b-273">Disable register page</span></span>

<span data-ttu-id="c232b-274">ユーザー登録を無効にするには:</span><span class="sxs-lookup"><span data-stu-id="c232b-274">To disable user registration:</span></span>

* <span data-ttu-id="c232b-275">スキャフォールディング Identity 。</span><span class="sxs-lookup"><span data-stu-id="c232b-275">Scaffold Identity.</span></span> <span data-ttu-id="c232b-276">Account、Account. Login、および Account. RegisterConfirmation を含めます。</span><span class="sxs-lookup"><span data-stu-id="c232b-276">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="c232b-277">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="c232b-277">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="c232b-278">ユーザーがこのエンドポイントから登録できないように、*区分/ Identity /Pages/Account/Register.cshtml.cs*を更新します。</span><span class="sxs-lookup"><span data-stu-id="c232b-278">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="c232b-279">次の変更との整合性を保つために、\*区分/////また Identity \*はを更新します。</span><span class="sxs-lookup"><span data-stu-id="c232b-279">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="c232b-280">*区分///////// Identity ////* から登録リンクをコメントアウトまたは削除します。</span><span class="sxs-lookup"><span data-stu-id="c232b-280">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="c232b-281">[*区分/ページ/ Identity アカウント/* 登録の確認] ページを更新します。</span><span class="sxs-lookup"><span data-stu-id="c232b-281">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="c232b-282">コードとリンクを cshtml ファイルから削除します。</span><span class="sxs-lookup"><span data-stu-id="c232b-282">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="c232b-283">から確認コードを削除し `PageModel` ます。</span><span class="sxs-lookup"><span data-stu-id="c232b-283">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="c232b-284">別のアプリを使用してユーザーを追加する</span><span class="sxs-lookup"><span data-stu-id="c232b-284">Use another app to add users</span></span>

<span data-ttu-id="c232b-285">Web アプリの外部にユーザーを追加するためのメカニズムを提供します。</span><span class="sxs-lookup"><span data-stu-id="c232b-285">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="c232b-286">ユーザーを追加するためのオプションは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="c232b-286">Options to add users include:</span></span>

* <span data-ttu-id="c232b-287">専用管理者 web アプリ。</span><span class="sxs-lookup"><span data-stu-id="c232b-287">A dedicated admin web app.</span></span>
* <span data-ttu-id="c232b-288">コンソールアプリ。</span><span class="sxs-lookup"><span data-stu-id="c232b-288">A console app.</span></span>

<span data-ttu-id="c232b-289">次のコードは、ユーザーを追加する方法の概要を示しています。</span><span class="sxs-lookup"><span data-stu-id="c232b-289">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="c232b-290">ユーザーの一覧がメモリに読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="c232b-290">A list of users is read into memory.</span></span>
* <span data-ttu-id="c232b-291">ユーザーごとに一意の強力なパスワードが生成されます。</span><span class="sxs-lookup"><span data-stu-id="c232b-291">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="c232b-292">ユーザーがデータベースに追加され Identity ます。</span><span class="sxs-lookup"><span data-stu-id="c232b-292">The user is added to the Identity database.</span></span>
* <span data-ttu-id="c232b-293">ユーザーに通知され、パスワードを変更するように指示されます。</span><span class="sxs-lookup"><span data-stu-id="c232b-293">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="c232b-294">次のコードは、ユーザーの追加の概要を示しています。</span><span class="sxs-lookup"><span data-stu-id="c232b-294">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="c232b-295">同様の方法で、運用環境のシナリオにも対応できます。</span><span class="sxs-lookup"><span data-stu-id="c232b-295">A similar approach can be followed for production scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c232b-296">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="c232b-296">Additional resources</span></span>

* [<span data-ttu-id="c232b-297">ASP.NET Core 2.1 以降に認証コードが変更された</span><span class="sxs-lookup"><span data-stu-id="c232b-297">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end
