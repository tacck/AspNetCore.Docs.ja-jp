---
title: :::no-loc(Identity):::ASP.NET Core プロジェクトでのスキャフォールディング
author: rick-anderson
description: 'ASP.NET Core のプロジェクトでスキャフォールディングする方法について説明 :::no-loc(Identity)::: します。'
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/1/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/authentication/scaffold-identity
ms.openlocfilehash: 813dd7837c265c78c584d66dd51bc23399d12fbe
ms.sourcegitcommit: 5156eab2118584405eb663e1fcd82f8bd7764504
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/31/2020
ms.locfileid: "93141496"
---
# <a name="scaffold-no-locidentity-in-aspnet-core-projects"></a><span data-ttu-id="9e659-103">:::no-loc(Identity):::ASP.NET Core プロジェクトでのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="9e659-103">Scaffold :::no-loc(Identity)::: in ASP.NET Core projects</span></span>

<span data-ttu-id="9e659-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="9e659-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9e659-105">ASP.NET Core は [:::no-loc(ASP.NET Core Identity):::](xref:security/authentication/identity) [ :::no-loc(Razor)::: クラスライブラリ](xref:razor-pages/ui-class)としてを提供します。</span><span class="sxs-lookup"><span data-stu-id="9e659-105">ASP.NET Core provides [:::no-loc(ASP.NET Core Identity):::](xref:security/authentication/identity) as a [:::no-loc(Razor)::: Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="9e659-106">を含むアプリケーション :::no-loc(Identity)::: では、scaffolder を適用して、 :::no-loc(Identity)::: :::no-loc(Razor)::: クラスライブラリ (rcl) に含まれているソースコードを選択的に追加できます。</span><span class="sxs-lookup"><span data-stu-id="9e659-106">Applications that include :::no-loc(Identity)::: can apply the scaffolder to selectively add the source code contained in the :::no-loc(Identity)::: :::no-loc(Razor)::: Class Library (RCL).</span></span> <span data-ttu-id="9e659-107">コードを変更して動作を変更できるように、ソース コードを生成できます。</span><span class="sxs-lookup"><span data-stu-id="9e659-107">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="9e659-108">たとえば、登録で使用するコードを生成するようにスキャフォルダーに指示できます。</span><span class="sxs-lookup"><span data-stu-id="9e659-108">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="9e659-109">生成されたコードは、:::no-loc(Identity)::: RCL の同じコードよりも優先されます。</span><span class="sxs-lookup"><span data-stu-id="9e659-109">Generated code takes precedence over the same code in the :::no-loc(Identity)::: RCL.</span></span> <span data-ttu-id="9e659-110">UI を完全に制御し、既定の RCL を使用しないようにするには、「 [完全な :::no-loc(Identity)::: ui ソースの作成](#full)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="9e659-110">To gain full control of the UI and not use the default RCL, see the section [Create full :::no-loc(Identity)::: UI source](#full).</span></span>

<span data-ttu-id="9e659-111">認証を含ま **ない** アプリケーションでは、scaffolder を適用して rcl パッケージを追加でき :::no-loc(Identity)::: ます。</span><span class="sxs-lookup"><span data-stu-id="9e659-111">Applications that do **not** include authentication can apply the scaffolder to add the RCL :::no-loc(Identity)::: package.</span></span> <span data-ttu-id="9e659-112">生成される :::no-loc(Identity)::: コードの選択オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="9e659-112">You have the option of selecting :::no-loc(Identity)::: code to be generated.</span></span>

<span data-ttu-id="9e659-113">Scaffolder は、必要なコードの大部分を生成しますが、プロセスを完了するにはプロジェクトを更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="9e659-113">Although the scaffolder generates most of the necessary code, you need to update your project to complete the process.</span></span> <span data-ttu-id="9e659-114">このドキュメントでは、スキャフォールディングの更新を完了するために必要な手順について説明し :::no-loc(Identity)::: ます。</span><span class="sxs-lookup"><span data-stu-id="9e659-114">This document explains the steps needed to complete an :::no-loc(Identity)::: scaffolding update.</span></span>

<span data-ttu-id="9e659-115">ファイルの違いを示すソース管理システムを使用して、変更を元に戻すことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="9e659-115">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="9e659-116">Scaffolder を実行した後に変更を確認し :::no-loc(Identity)::: ます。</span><span class="sxs-lookup"><span data-stu-id="9e659-116">Inspect the changes after running the :::no-loc(Identity)::: scaffolder.</span></span>

<span data-ttu-id="9e659-117">サービスは、 [2 要素認証](xref:security/authentication/identity-enable-qrcodes)、アカウントの [確認とパスワードの回復](xref:security/authentication/accconfirm)、およびの他のセキュリティ機能を使用する場合に必要です :::no-loc(Identity)::: 。</span><span class="sxs-lookup"><span data-stu-id="9e659-117">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with :::no-loc(Identity):::.</span></span> <span data-ttu-id="9e659-118">サービスまたはサービススタブは、スキャフォールディング時に生成されません :::no-loc(Identity)::: 。</span><span class="sxs-lookup"><span data-stu-id="9e659-118">Services or service stubs aren't generated when scaffolding :::no-loc(Identity):::.</span></span> <span data-ttu-id="9e659-119">これらの機能を有効にするサービスは、手動で追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="9e659-119">Services to enable these features must be added manually.</span></span> <span data-ttu-id="9e659-120">たとえば、「 [電子メールの確認を要求する](xref:security/authentication/accconfirm#require-email-confirmation)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9e659-120">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

<span data-ttu-id="9e659-121">:::no-loc(Identity):::新しいデータコンテキストを使用して、既存の個別のアカウントを持つプロジェクトにスキャフォールディングする場合:</span><span class="sxs-lookup"><span data-stu-id="9e659-121">When scaffolding :::no-loc(Identity)::: with a new data context into a project with existing individual accounts:</span></span>

* <span data-ttu-id="9e659-122">で `Startup.ConfigureServices` 、の呼び出しを削除します。</span><span class="sxs-lookup"><span data-stu-id="9e659-122">In `Startup.ConfigureServices`, remove the calls to:</span></span>
  * `AddDbContext`
  * `AddDefault:::no-loc(Identity):::`

<span data-ttu-id="9e659-123">たとえば、 `AddDbContext` と `AddDefault:::no-loc(Identity):::` は次のコードでコメントアウトされます。</span><span class="sxs-lookup"><span data-stu-id="9e659-123">For example, `AddDbContext` and `AddDefault:::no-loc(Identity):::` are commented out in the following code:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRemove.cs?name=snippet)]

<span data-ttu-id="9e659-124">上記のコードでは、 *区分/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs* に複製されたコードをコメントアウトします。</span><span class="sxs-lookup"><span data-stu-id="9e659-124">The preceeding code comments out the code that is duplicated in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs*</span></span>

<span data-ttu-id="9e659-125">通常、個別のアカウントで作成されたアプリは、新しいデータコンテキストを作成し **ない** ようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="9e659-125">Typically, apps that were created with individual accounts should \* **not** _ create a new data context.</span></span>

## <a name="scaffold-no-locidentity-into-an-empty-project"></a><span data-ttu-id="9e659-126">:::no-loc(Identity):::空のプロジェクトにスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="9e659-126">Scaffold :::no-loc(Identity)::: into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="9e659-127">`Startup`次のようなコードでクラスを更新します。</span><span class="sxs-lookup"><span data-stu-id="9e659-127">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-without-existing-authorization"></a><span data-ttu-id="9e659-128">:::no-loc(Identity)::: :::no-loc(Razor)::: 既存の承認なしでプロジェクトにスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="9e659-128">Scaffold :::no-loc(Identity)::: into a :::no-loc(Razor)::: project without existing authorization</span></span>

<!--  Updated for 3.0
set projNam=RPnoAuth
set projType=webapp

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.:::no-loc(Identity):::.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef database drop
dotnet ef migrations add Create:::no-loc(Identity):::Schema0
dotnet ef database update
-->

<!-- ERROR
There is already an object named 'AspNetRoles' in the database.

Fixed via dotnet ef database drop
before dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="9e659-129">:::no-loc(Identity):::は _Areas/HostingStartup.cs \* で構成され :::no-loc(Identity)::: / :::no-loc(Identity)::: ます。</span><span class="sxs-lookup"><span data-stu-id="9e659-129">:::no-loc(Identity)::: is configured in _Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs\*.</span></span> <span data-ttu-id="9e659-130">詳細については、「 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9e659-130">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="9e659-131">移行、UseAuthentication、およびレイアウト</span><span class="sxs-lookup"><span data-stu-id="9e659-131">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="9e659-132">認証を有効にする</span><span class="sxs-lookup"><span data-stu-id="9e659-132">Enable authentication</span></span>

<span data-ttu-id="9e659-133">`Startup`次のようなコードでクラスを更新します。</span><span class="sxs-lookup"><span data-stu-id="9e659-133">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="9e659-134">レイアウトの変更</span><span class="sxs-lookup"><span data-stu-id="9e659-134">Layout changes</span></span>

<span data-ttu-id="9e659-135">省略可能: 次のように、ログイン部分 ( `_LoginPartial` ) をレイアウトファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="9e659-135">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-cshtml[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-with-authorization"></a><span data-ttu-id="9e659-136">:::no-loc(Identity)::: :::no-loc(Razor)::: 承認を使用してプロジェクトにスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="9e659-136">Scaffold :::no-loc(Identity)::: into a :::no-loc(Razor)::: project with authorization</span></span>

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

<span data-ttu-id="9e659-137">一部 :::no-loc(Identity)::: のオプションは、 *Areas/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs* で構成されています。</span><span class="sxs-lookup"><span data-stu-id="9e659-137">Some :::no-loc(Identity)::: options are configured in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs* .</span></span> <span data-ttu-id="9e659-138">詳細については、「 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9e659-138">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-no-locidentity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="9e659-139">:::no-loc(Identity):::既存の承認なしで MVC プロジェクトにスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="9e659-139">Scaffold :::no-loc(Identity)::: into an MVC project without existing authorization</span></span>

<!--
set projNam=MvcNoAuth
set projType=mvc
set version=2.1.0

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v %version%
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add Create:::no-loc(Identity):::Schema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="9e659-140">省略可能: `_LoginPartial` *Views/Shared/_Layout cshtml* ファイルにログイン部分 () を追加します。</span><span class="sxs-lookup"><span data-stu-id="9e659-140">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-cshtml[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* <span data-ttu-id="9e659-141">*Pages/shared/_LoginPartial cshtml* ファイルを *Views/shared/_LoginPartial* に移動します。</span><span class="sxs-lookup"><span data-stu-id="9e659-141">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="9e659-142">:::no-loc(Identity):::は、 *Areas/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs* で構成されます。</span><span class="sxs-lookup"><span data-stu-id="9e659-142">:::no-loc(Identity)::: is configured in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs* .</span></span> <span data-ttu-id="9e659-143">詳細については、「IHostingStartup」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9e659-143">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="9e659-144">`Startup`次のようなコードでクラスを更新します。</span><span class="sxs-lookup"><span data-stu-id="9e659-144">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-no-locidentity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="9e659-145">:::no-loc(Identity):::承認を使用した MVC プロジェクトへのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="9e659-145">Scaffold :::no-loc(Identity)::: into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

## <a name="scaffold-no-locidentity-into-a-no-locblazor-server-project-without-existing-authorization"></a><span data-ttu-id="9e659-146">:::no-loc(Identity)::: :::no-loc(Blazor Server)::: 既存の承認なしでプロジェクトにスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="9e659-146">Scaffold :::no-loc(Identity)::: into a :::no-loc(Blazor Server)::: project without existing authorization</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="9e659-147">:::no-loc(Identity):::は、 *Areas/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs* で構成されます。</span><span class="sxs-lookup"><span data-stu-id="9e659-147">:::no-loc(Identity)::: is configured in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs* .</span></span> <span data-ttu-id="9e659-148">詳細については、「 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9e659-148">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

### <a name="migrations"></a><span data-ttu-id="9e659-149">移行</span><span class="sxs-lookup"><span data-stu-id="9e659-149">Migrations</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

### <a name="pass-an-xsrf-token-to-the-app"></a><span data-ttu-id="9e659-150">XSRF トークンをアプリに渡す</span><span class="sxs-lookup"><span data-stu-id="9e659-150">Pass an XSRF token to the app</span></span>

<span data-ttu-id="9e659-151">トークンは、次のようにコンポーネントに渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="9e659-151">Tokens can be passed to components:</span></span>

* <span data-ttu-id="9e659-152">認証トークンがプロビジョニングされ、認証に保存されると :::no-loc(cookie)::: 、コンポーネントに渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="9e659-152">When authentication tokens are provisioned and saved to the authentication :::no-loc(cookie):::, they can be passed to components.</span></span>
* <span data-ttu-id="9e659-153">:::no-loc(Razor)::: コンポーネントでを直接使用することはできない `HttpContext` ため、のログアウトエンドポイントをに送信するための [要求防止偽造 (XSRF) トークン](xref:security/anti-request-forgery) を取得する方法はありません :::no-loc(Identity)::: `/:::no-loc(Identity):::/Account/Logout` 。</span><span class="sxs-lookup"><span data-stu-id="9e659-153">:::no-loc(Razor)::: components can't use `HttpContext` directly, so there's no way to obtain an [anti-request forgery (XSRF) token](xref:security/anti-request-forgery) to POST to :::no-loc(Identity):::'s logout endpoint at `/:::no-loc(Identity):::/Account/Logout`.</span></span> <span data-ttu-id="9e659-154">XSRF トークンは、コンポーネントに渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="9e659-154">An XSRF token can be passed to components.</span></span>

<span data-ttu-id="9e659-155">詳細については、「<xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9e659-155">For more information, see <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.</span></span>

<span data-ttu-id="9e659-156">*Pages/_Host cshtml* ファイルで、クラスおよびクラスに追加した後、トークンを設定し `InitialApplicationState` `TokenProvider` ます。</span><span class="sxs-lookup"><span data-stu-id="9e659-156">In the *Pages/_Host.cshtml* file, establish the token after adding it to the `InitialApplicationState` and `TokenProvider` classes:</span></span>

```csharp
@inject Microsoft.AspNetCore.Antiforgery.IAntiforgery Xsrf

...

var tokens = new InitialApplicationState
{
    ...

    XsrfToken = Xsrf.GetAndStoreTokens(HttpContext).RequestToken
};
```

<span data-ttu-id="9e659-157">`App`コンポーネント ( *app.xaml* ) を更新して、次の `InitialState.XsrfToken` ものを割り当てます。</span><span class="sxs-lookup"><span data-stu-id="9e659-157">Update the `App` component ( *App.razor* ) to assign the `InitialState.XsrfToken`:</span></span>

```csharp
@inject TokenProvider TokenProvider

...

TokenProvider.XsrfToken = InitialState.XsrfToken;
```

<span data-ttu-id="9e659-158">トピック「」で説明されているサービスは、 `TokenProvider` `LoginDisplay` 次の [レイアウトと認証フローの変更](#layout-and-authentication-flow-changes) セクションのコンポーネントで使用されます。</span><span class="sxs-lookup"><span data-stu-id="9e659-158">The `TokenProvider` service demonstrated in the topic is used in the `LoginDisplay` component in the following [Layout and authentication flow changes](#layout-and-authentication-flow-changes) section.</span></span>

### <a name="enable-authentication"></a><span data-ttu-id="9e659-159">認証を有効にする</span><span class="sxs-lookup"><span data-stu-id="9e659-159">Enable authentication</span></span>

<span data-ttu-id="9e659-160">クラスの場合 `Startup` :</span><span class="sxs-lookup"><span data-stu-id="9e659-160">In the `Startup` class:</span></span>

* <span data-ttu-id="9e659-161">ページサービスがに追加されていることを確認 :::no-loc(Razor)::: `Startup.ConfigureServices` します。</span><span class="sxs-lookup"><span data-stu-id="9e659-161">Confirm that :::no-loc(Razor)::: Pages services are added in `Startup.ConfigureServices`.</span></span>
* <span data-ttu-id="9e659-162">[TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app)を使用する場合は、サービスを登録します。</span><span class="sxs-lookup"><span data-stu-id="9e659-162">If using the [TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app), register the service.</span></span>
* <span data-ttu-id="9e659-163">`UseDatabaseErrorPage`開発環境用のアプリケーションビルダーでを呼び出し `Startup.Configure` ます。</span><span class="sxs-lookup"><span data-stu-id="9e659-163">Call `UseDatabaseErrorPage` on the application builder in `Startup.Configure` for the Development environment.</span></span>
* <span data-ttu-id="9e659-164">`UseAuthentication`との後にを呼び出し `UseAuthorization` `UseRouting` ます。</span><span class="sxs-lookup"><span data-stu-id="9e659-164">Call `UseAuthentication` and `UseAuthorization` after `UseRouting`.</span></span>
* <span data-ttu-id="9e659-165">ページのエンドポイントを追加 :::no-loc(Razor)::: します。</span><span class="sxs-lookup"><span data-stu-id="9e659-165">Add an endpoint for :::no-loc(Razor)::: Pages.</span></span>

[!code-csharp[](scaffold-identity/3.1sample/Startup:::no-loc(Blazor):::.cs?highlight=3,6,14,27-28,32)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-and-authentication-flow-changes"></a><span data-ttu-id="9e659-166">レイアウトと認証のフローの変更</span><span class="sxs-lookup"><span data-stu-id="9e659-166">Layout and authentication flow changes</span></span>

<span data-ttu-id="9e659-167">`RedirectToLogin`プロジェクトルートのアプリの *共有* フォルダーにコンポーネント ( *redirecttologin. razor* ) を追加します。</span><span class="sxs-lookup"><span data-stu-id="9e659-167">Add a `RedirectToLogin` component ( *RedirectToLogin.razor* ) to the app's *Shared* folder in the project root:</span></span>

```razor
@inject NavigationManager Navigation
@code {
    protected override void OnInitialized()
    {
        Navigation.NavigateTo(":::no-loc(Identity):::/Account/Login?returnUrl=" +
            Uri.EscapeDataString(Navigation.Uri), true);
    }
}
```

`LoginDisplay`アプリの *共有* フォルダーにコンポーネント ( *logindisplay. razor* ) を追加します。 <span data-ttu-id="9e659-169">[TokenProvider サービス](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app)は、ログアウトエンドポイントにポストする HTML フォームの XSRF トークンを提供し :::no-loc(Identity)::: ます。</span><span class="sxs-lookup"><span data-stu-id="9e659-169">The [TokenProvider service](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app) provides the XSRF token for the HTML form that POSTs to :::no-loc(Identity):::'s logout endpoint:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@inject NavigationManager Navigation
@inject TokenProvider TokenProvider

<AuthorizeView>
    <Authorized>
        <a href=":::no-loc(Identity):::/Account/Manage/Index">
            Hello, @context.User.:::no-loc(Identity):::.Name!
        </a>
        <form action="/:::no-loc(Identity):::/Account/Logout?returnUrl=%2F" method="post">
            <button class="nav-link btn btn-link" type="submit">Logout</button>
            <input name="__RequestVerificationToken" type="hidden" 
                value="@TokenProvider.XsrfToken">
        </form>
    </Authorized>
    <NotAuthorized>
        <a href=":::no-loc(Identity):::/Account/Register">Register</a>
        <a href=":::no-loc(Identity):::/Account/Login">Login</a>
    </NotAuthorized>
</AuthorizeView>
```

<span data-ttu-id="9e659-170">`MainLayout`コンポーネント ( *Shared/mainlayout. razor* ) で、 `LoginDisplay` 最上位の要素のコンテンツにコンポーネントを追加し `<div>` ます。</span><span class="sxs-lookup"><span data-stu-id="9e659-170">In the `MainLayout` component ( *Shared/MainLayout.razor* ), add the `LoginDisplay` component to the top-row `<div>` element's content:</span></span>

```razor
<div class="top-row px-4 auth">
    <LoginDisplay />
    <a href="https://docs.microsoft.com/aspnet/" target="_blank">About</a>
</div>
```

### <a name="style-authentication-endpoints"></a><span data-ttu-id="9e659-171">認証エンドポイントのスタイルを適用する</span><span class="sxs-lookup"><span data-stu-id="9e659-171">Style authentication endpoints</span></span>

<span data-ttu-id="9e659-172">ではページページが使用されるため、 :::no-loc(Blazor Server)::: :::no-loc(Razor)::: :::no-loc(Identity)::: ビジターがページとコンポーネントの間を移動すると、UI のスタイルが変更され :::no-loc(Identity)::: ます。</span><span class="sxs-lookup"><span data-stu-id="9e659-172">Because :::no-loc(Blazor Server)::: uses :::no-loc(Razor)::: Pages :::no-loc(Identity)::: pages, the styling of the UI changes when a visitor navigates between :::no-loc(Identity)::: pages and components.</span></span> <span data-ttu-id="9e659-173">Incongruous スタイルに対処するには、次の2つのオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="9e659-173">You have two options to address the incongruous styles:</span></span>

#### <a name="build-no-locidentity-components"></a><span data-ttu-id="9e659-174">ビルド :::no-loc(Identity)::: コンポーネント</span><span class="sxs-lookup"><span data-stu-id="9e659-174">Build :::no-loc(Identity)::: components</span></span>

<span data-ttu-id="9e659-175">ページではなくコンポーネントを使用する方法 :::no-loc(Identity)::: は、コンポーネントを構築することです :::no-loc(Identity)::: 。</span><span class="sxs-lookup"><span data-stu-id="9e659-175">An approach to using components for :::no-loc(Identity)::: instead of pages is to build :::no-loc(Identity)::: components.</span></span> <span data-ttu-id="9e659-176">`SignInManager`とは `UserManager` コンポーネントでサポートされていないため :::no-loc(Razor)::: 、アプリの API エンドポイントを使用して、 :::no-loc(Blazor Server)::: ユーザーアカウントの操作を処理します。</span><span class="sxs-lookup"><span data-stu-id="9e659-176">Because `SignInManager` and `UserManager` aren't supported in :::no-loc(Razor)::: components, use API endpoints in the :::no-loc(Blazor Server)::: app to process user account actions.</span></span>

#### <a name="use-a-custom-layout-with-no-locblazor-app-styles"></a><span data-ttu-id="9e659-177">アプリスタイルでカスタムレイアウトを使用する :::no-loc(Blazor):::</span><span class="sxs-lookup"><span data-stu-id="9e659-177">Use a custom layout with :::no-loc(Blazor)::: app styles</span></span>

<span data-ttu-id="9e659-178">:::no-loc(Identity):::ページのレイアウトとスタイルを変更して、既定のテーマを使用するページを生成することができ :::no-loc(Blazor)::: ます。</span><span class="sxs-lookup"><span data-stu-id="9e659-178">The :::no-loc(Identity)::: pages layout and styles can be modified to produce pages that use the default :::no-loc(Blazor)::: theme.</span></span>

> [!NOTE]
> <span data-ttu-id="9e659-179">このセクションの例は、カスタマイズの開始点にすぎません。</span><span class="sxs-lookup"><span data-stu-id="9e659-179">The example in this section is merely a starting point for customization.</span></span> <span data-ttu-id="9e659-180">最適なユーザーエクスペリエンスを実現するには、追加の作業が必要になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="9e659-180">Additional work is likely required for the best user experience.</span></span>

<span data-ttu-id="9e659-181">新しい `NavMenu_:::no-loc(Identity):::Layout` コンポーネント ( *Shared/NavMenu_ :::no-loc(Identity)::: Layout* ) を作成します。</span><span class="sxs-lookup"><span data-stu-id="9e659-181">Create a new `NavMenu_:::no-loc(Identity):::Layout` component ( *Shared/NavMenu_:::no-loc(Identity):::Layout.razor* ).</span></span> <span data-ttu-id="9e659-182">コンポーネントのマークアップとコードについては、アプリのコンポーネントの同じコンテンツを使用し `NavMenu` ます ( *共有/ナビゲーションメニュー。 razor* )。</span><span class="sxs-lookup"><span data-stu-id="9e659-182">For the markup and code of the component, use the same content of the app's `NavMenu` component ( *Shared/NavMenu.razor* ).</span></span> <span data-ttu-id="9e659-183">コンポーネントの `NavLink` 自動リダイレクトが `RedirectToLogin` 認証または承認を必要とするコンポーネントに対して失敗するため、匿名にアクセスできないコンポーネントに対してを除去します。</span><span class="sxs-lookup"><span data-stu-id="9e659-183">Strip out any `NavLink`s to components that can't be reached anonymously because automatic redirects in the `RedirectToLogin` component fail for components requiring authentication or authorization.</span></span>

<span data-ttu-id="9e659-184">*Pages/Shared/Layout. cshtml* ファイルで、次の変更を行います。</span><span class="sxs-lookup"><span data-stu-id="9e659-184">In the *Pages/Shared/Layout.cshtml* file, make the following changes:</span></span>

* <span data-ttu-id="9e659-185">:::no-loc(Razor):::タグヘルパーと *共有* フォルダー内のアプリのコンポーネントを使用するために、ファイルの先頭にディレクティブを追加します。</span><span class="sxs-lookup"><span data-stu-id="9e659-185">Add :::no-loc(Razor)::: directives to the top of the file to use Tag Helpers and the app's components in the *Shared* folder:</span></span>

  ```cshtml
  @addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
  @using {APPLICATION ASSEMBLY}.Shared
  ```

  <span data-ttu-id="9e659-186">`{APPLICATION ASSEMBLY}`をアプリのアセンブリ名に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="9e659-186">Replace `{APPLICATION ASSEMBLY}` with the app's assembly name.</span></span>

* <span data-ttu-id="9e659-187">`<base>`コンテンツにタグと :::no-loc(Blazor)::: スタイルシートを追加し `<link>` `<head>` ます。</span><span class="sxs-lookup"><span data-stu-id="9e659-187">Add a `<base>` tag and :::no-loc(Blazor)::: stylesheet `<link>` to the `<head>` content:</span></span>

  ```cshtml
  <base href="~/" />
  <link rel="stylesheet" href="~/css/site.css" />
  ```

* <span data-ttu-id="9e659-188">タグの内容 `<body>` を次のように変更します。</span><span class="sxs-lookup"><span data-stu-id="9e659-188">Change the content of the `<body>` tag to the following:</span></span>

  ```cshtml
  <div class="sidebar" style="float:left">
      <component type="typeof(NavMenu_:::no-loc(Identity):::Layout)" 
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
              throw new InvalidOperationException("The default :::no-loc(Identity)::: UI " +
                  "layout requires a partial view '_LoginPartial'.");
          }
          <a href="https://docs.microsoft.com/aspnet/" target="_blank">About</a>
      </div>

      <div class="content px-4">
          @RenderBody()
      </div>
  </div>

  <script src="~/:::no-loc(Identity):::/lib/jquery/dist/jquery.min.js"></script>
  <script src="~/:::no-loc(Identity):::/lib/bootstrap/dist/js/bootstrap.bundle.min.js"></script>
  <script src="~/:::no-loc(Identity):::/js/site.js" asp-append-version="true"></script>
  @RenderSection("Scripts", required: false)
  <script src="_framework/blazor.server.js"></script>
  ```

## <a name="scaffold-no-locidentity-into-a-no-locblazor-server-project-with-authorization"></a><span data-ttu-id="9e659-189">:::no-loc(Identity)::: :::no-loc(Blazor Server)::: 承認を使用してプロジェクトにスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="9e659-189">Scaffold :::no-loc(Identity)::: into a :::no-loc(Blazor Server)::: project with authorization</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="9e659-190">一部 :::no-loc(Identity)::: のオプションは、 *Areas/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs* で構成されています。</span><span class="sxs-lookup"><span data-stu-id="9e659-190">Some :::no-loc(Identity)::: options are configured in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs* .</span></span> <span data-ttu-id="9e659-191">詳細については、「 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9e659-191">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="standalone-or-hosted-no-locblazor-webassembly-apps"></a><span data-ttu-id="9e659-192">スタンドアロンまたはホストされた :::no-loc(Blazor WebAssembly)::: アプリ</span><span class="sxs-lookup"><span data-stu-id="9e659-192">Standalone or hosted :::no-loc(Blazor WebAssembly)::: apps</span></span>

<span data-ttu-id="9e659-193">クライアント側 :::no-loc(Blazor WebAssembly)::: アプリは独自の :::no-loc(Identity)::: UI アプローチを使用し、スキャフォールディングを使用することはできません :::no-loc(ASP.NET Core Identity)::: 。</span><span class="sxs-lookup"><span data-stu-id="9e659-193">Client-side :::no-loc(Blazor WebAssembly)::: apps use their own :::no-loc(Identity)::: UI approaches and can't use :::no-loc(ASP.NET Core Identity)::: scaffolding.</span></span> <span data-ttu-id="9e659-194">ホストされたソリューションのサーバー側 ASP.NET Core アプリ :::no-loc(Blazor)::: は、 :::no-loc(Razor)::: この記事のページ/MVC ガイダンスに従うことができ、をサポートする他の種類の ASP.NET Core アプリと同様に構成され :::no-loc(Identity)::: ます。</span><span class="sxs-lookup"><span data-stu-id="9e659-194">Server-side ASP.NET Core apps of hosted :::no-loc(Blazor)::: solutions can follow the :::no-loc(Razor)::: Pages/MVC guidance in this article and are configured just like any other type of ASP.NET Core app that supports :::no-loc(Identity):::.</span></span>

<span data-ttu-id="9e659-195">フレームワークには、 :::no-loc(Blazor)::: :::no-loc(Razor)::: コンポーネントバージョンの :::no-loc(Identity)::: UI ページは含まれません。</span><span class="sxs-lookup"><span data-stu-id="9e659-195">The :::no-loc(Blazor)::: framework doesn't include :::no-loc(Razor)::: component versions of :::no-loc(Identity)::: UI pages.</span></span> <span data-ttu-id="9e659-196">:::no-loc(Identity)::: UI :::no-loc(Razor)::: コンポーネントは、サポートされていないサードパーティソースから作成または取得できます。</span><span class="sxs-lookup"><span data-stu-id="9e659-196">:::no-loc(Identity)::: UI :::no-loc(Razor)::: components can be custom built or obtained from unsupported third-party sources.</span></span>

<span data-ttu-id="9e659-197">詳細については、「 [ :::no-loc(Blazor)::: セキュリティと :::no-loc(Identity)::: 記事](xref:blazor/security/index)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9e659-197">For more information, see the [:::no-loc(Blazor)::: Security and :::no-loc(Identity)::: articles](xref:blazor/security/index).</span></span>

<a name="full"></a>

## <a name="create-full-no-locidentity-ui-source"></a><span data-ttu-id="9e659-198">完全な :::no-loc(Identity)::: UI ソースの作成</span><span class="sxs-lookup"><span data-stu-id="9e659-198">Create full :::no-loc(Identity)::: UI source</span></span>

<span data-ttu-id="9e659-199">UI の完全な制御を維持するには、scaffolder を実行し、 :::no-loc(Identity)::: :::no-loc(Identity)::: [ **すべてのファイルを上書き** する] を選択します。</span><span class="sxs-lookup"><span data-stu-id="9e659-199">To maintain full control of the :::no-loc(Identity)::: UI, run the :::no-loc(Identity)::: scaffolder and select **Override all files** .</span></span>

<span data-ttu-id="9e659-200">次の強調表示されたコードは、 :::no-loc(Identity)::: ASP.NET Core 2.1 web アプリで既定の UI をに置き換える変更を示して :::no-loc(Identity)::: います。</span><span class="sxs-lookup"><span data-stu-id="9e659-200">The following highlighted code shows the changes to replace the default :::no-loc(Identity)::: UI with :::no-loc(Identity)::: in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="9e659-201">この操作を行うと、UI を完全に制御でき :::no-loc(Identity)::: ます。</span><span class="sxs-lookup"><span data-stu-id="9e659-201">You might want to do this to have full control of the :::no-loc(Identity)::: UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="9e659-202">既定値 :::no-loc(Identity)::: は、次のコードで置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="9e659-202">The default :::no-loc(Identity)::: is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="9e659-203">次のコードでは、 [Loginpath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.loginpath)、 [logoutpath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.logoutpath)、および [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.accessdeniedpath)を設定しています。</span><span class="sxs-lookup"><span data-stu-id="9e659-203">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="9e659-204">`IEmailSender`の実装を登録します。次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="9e659-204">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->

## <a name="password-configuration"></a><span data-ttu-id="9e659-205">パスワードの構成</span><span class="sxs-lookup"><span data-stu-id="9e659-205">Password configuration</span></span>

<span data-ttu-id="9e659-206">がで構成されている場合は <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PasswordOptions> `Startup.ConfigureServices` 、スキャフォールディング pages のプロパティに[ `[StringLength]` 属性](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute)の構成が必要になることがあり `Password` :::no-loc(Identity)::: ます。</span><span class="sxs-lookup"><span data-stu-id="9e659-206">If <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PasswordOptions> are configured in `Startup.ConfigureServices`, [`[StringLength]` attribute](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) configuration might be required for the `Password` property in scaffolded :::no-loc(Identity)::: pages.</span></span> <span data-ttu-id="9e659-207">`InputModel``Password`プロパティは次のファイルにあります。</span><span class="sxs-lookup"><span data-stu-id="9e659-207">`InputModel` `Password` properties are found in the following files:</span></span>

* `Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs`
* `Areas/:::no-loc(Identity):::/Pages/Account/ResetPassword.cshtml.cs`

## <a name="disable-a-page"></a><span data-ttu-id="9e659-208">ページを無効にする</span><span class="sxs-lookup"><span data-stu-id="9e659-208">Disable a page</span></span>

<span data-ttu-id="9e659-209">このセクションでは、登録ページを無効にする方法について説明しますが、この方法を使用して任意のページを無効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="9e659-209">This sections show how to disable the register page but the approach can be used to disable any page.</span></span>

<span data-ttu-id="9e659-210">ユーザー登録を無効にするには:</span><span class="sxs-lookup"><span data-stu-id="9e659-210">To disable user registration:</span></span>

* <span data-ttu-id="9e659-211">スキャフォールディング :::no-loc(Identity)::: 。</span><span class="sxs-lookup"><span data-stu-id="9e659-211">Scaffold :::no-loc(Identity):::.</span></span> <span data-ttu-id="9e659-212">Account、Account. Login、および Account. RegisterConfirmation を含めます。</span><span class="sxs-lookup"><span data-stu-id="9e659-212">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="9e659-213">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="9e659-213">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="9e659-214">ユーザーがこのエンドポイントから登録できないように、 *区分/ :::no-loc(Identity)::: /Pages/Account/Register.cshtml.cs* を更新します。</span><span class="sxs-lookup"><span data-stu-id="9e659-214">Update *Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="9e659-215">次の変更との整合性を保つために、 *区分/////また :::no-loc(Identity):::* はを更新します。</span><span class="sxs-lookup"><span data-stu-id="9e659-215">Update *Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="9e659-216">*区分///////// :::no-loc(Identity)::: ////* から登録リンクをコメントアウトまたは削除します。</span><span class="sxs-lookup"><span data-stu-id="9e659-216">Comment out or remove the registration link from *Areas/:::no-loc(Identity):::/Pages/Account/Login.cshtml*</span></span>

  ```cshtml
  @*
  <p>
      <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
  </p>
  *@
  ```

* <span data-ttu-id="9e659-217">[ *区分/ページ/ :::no-loc(Identity)::: アカウント/* 登録の確認] ページを更新します。</span><span class="sxs-lookup"><span data-stu-id="9e659-217">Update the *Areas/:::no-loc(Identity):::/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="9e659-218">コードとリンクを cshtml ファイルから削除します。</span><span class="sxs-lookup"><span data-stu-id="9e659-218">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="9e659-219">から確認コードを削除し `PageModel` ます。</span><span class="sxs-lookup"><span data-stu-id="9e659-219">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="9e659-220">別のアプリを使用してユーザーを追加する</span><span class="sxs-lookup"><span data-stu-id="9e659-220">Use another app to add users</span></span>

<span data-ttu-id="9e659-221">Web アプリの外部にユーザーを追加するためのメカニズムを提供します。</span><span class="sxs-lookup"><span data-stu-id="9e659-221">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="9e659-222">ユーザーを追加するためのオプションは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="9e659-222">Options to add users include:</span></span>

* <span data-ttu-id="9e659-223">専用管理者 web アプリ。</span><span class="sxs-lookup"><span data-stu-id="9e659-223">A dedicated admin web app.</span></span>
* <span data-ttu-id="9e659-224">コンソールアプリ。</span><span class="sxs-lookup"><span data-stu-id="9e659-224">A console app.</span></span>

<span data-ttu-id="9e659-225">次のコードは、ユーザーを追加する方法の概要を示しています。</span><span class="sxs-lookup"><span data-stu-id="9e659-225">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="9e659-226">ユーザーの一覧がメモリに読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="9e659-226">A list of users is read into memory.</span></span>
* <span data-ttu-id="9e659-227">ユーザーごとに一意の強力なパスワードが生成されます。</span><span class="sxs-lookup"><span data-stu-id="9e659-227">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="9e659-228">ユーザーがデータベースに追加され :::no-loc(Identity)::: ます。</span><span class="sxs-lookup"><span data-stu-id="9e659-228">The user is added to the :::no-loc(Identity)::: database.</span></span>
* <span data-ttu-id="9e659-229">ユーザーに通知され、パスワードを変更するように指示されます。</span><span class="sxs-lookup"><span data-stu-id="9e659-229">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="9e659-230">次のコードは、ユーザーの追加の概要を示しています。</span><span class="sxs-lookup"><span data-stu-id="9e659-230">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="9e659-231">同様の方法で、運用環境のシナリオにも対応できます。</span><span class="sxs-lookup"><span data-stu-id="9e659-231">A similar approach can be followed for production scenarios.</span></span>

## <a name="prevent-publish-of-static-no-locidentity-assets"></a><span data-ttu-id="9e659-232">静的アセットの発行を禁止する :::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="9e659-232">Prevent publish of static :::no-loc(Identity)::: assets</span></span>

<span data-ttu-id="9e659-233">静的なアセットが web ルートに発行されないようにするに :::no-loc(Identity)::: は、「」を参照してください <xref:security/authentication/identity#prevent-publish-of-static-identity-assets> 。</span><span class="sxs-lookup"><span data-stu-id="9e659-233">To prevent publishing static :::no-loc(Identity)::: assets to the web root, see <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9e659-234">その他の資料</span><span class="sxs-lookup"><span data-stu-id="9e659-234">Additional resources</span></span>

* [<span data-ttu-id="9e659-235">ASP.NET Core 2.1 以降に認証コードが変更された</span><span class="sxs-lookup"><span data-stu-id="9e659-235">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9e659-236">ASP.NET Core 2.1 以降は、 [:::no-loc(ASP.NET Core Identity):::](xref:security/authentication/identity) [ :::no-loc(Razor)::: クラスライブラリ](xref:razor-pages/ui-class)としてを提供します。</span><span class="sxs-lookup"><span data-stu-id="9e659-236">ASP.NET Core 2.1 and later provides [:::no-loc(ASP.NET Core Identity):::](xref:security/authentication/identity) as a [:::no-loc(Razor)::: Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="9e659-237">を含むアプリケーション :::no-loc(Identity)::: では、scaffolder を適用して、 :::no-loc(Identity)::: :::no-loc(Razor)::: クラスライブラリ (rcl) に含まれているソースコードを選択的に追加できます。</span><span class="sxs-lookup"><span data-stu-id="9e659-237">Applications that include :::no-loc(Identity)::: can apply the scaffolder to selectively add the source code contained in the :::no-loc(Identity)::: :::no-loc(Razor)::: Class Library (RCL).</span></span> <span data-ttu-id="9e659-238">コードを変更して動作を変更できるように、ソース コードを生成できます。</span><span class="sxs-lookup"><span data-stu-id="9e659-238">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="9e659-239">たとえば、登録で使用するコードを生成するようにスキャフォルダーに指示できます。</span><span class="sxs-lookup"><span data-stu-id="9e659-239">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="9e659-240">生成されたコードは、:::no-loc(Identity)::: RCL の同じコードよりも優先されます。</span><span class="sxs-lookup"><span data-stu-id="9e659-240">Generated code takes precedence over the same code in the :::no-loc(Identity)::: RCL.</span></span> <span data-ttu-id="9e659-241">UI を完全に制御し、既定の RCL を使用しないようにするには、「 [完全な ID UI ソースの作成](#full)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="9e659-241">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="9e659-242">認証を含ま **ない** アプリケーションでは、scaffolder を適用して rcl パッケージを追加でき :::no-loc(Identity)::: ます。</span><span class="sxs-lookup"><span data-stu-id="9e659-242">Applications that do **not** include authentication can apply the scaffolder to add the RCL :::no-loc(Identity)::: package.</span></span> <span data-ttu-id="9e659-243">生成される :::no-loc(Identity)::: コードの選択オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="9e659-243">You have the option of selecting :::no-loc(Identity)::: code to be generated.</span></span>

<span data-ttu-id="9e659-244">Scaffolder は、必要なコードの大部分を生成しますが、プロセスを完了するにはプロジェクトを更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="9e659-244">Although the scaffolder generates most of the necessary code, you'll have to update your project to complete the process.</span></span> <span data-ttu-id="9e659-245">このドキュメントでは、スキャフォールディングの更新を完了するために必要な手順について説明し :::no-loc(Identity)::: ます。</span><span class="sxs-lookup"><span data-stu-id="9e659-245">This document explains the steps needed to complete an :::no-loc(Identity)::: scaffolding update.</span></span>

<span data-ttu-id="9e659-246">Scaffolder を :::no-loc(Identity)::: 実行すると、プロジェクトディレクトリに *ScaffoldingReadme.txt* ファイルが作成されます。</span><span class="sxs-lookup"><span data-stu-id="9e659-246">When the :::no-loc(Identity)::: scaffolder is run, a *ScaffoldingReadme.txt* file is created in the project directory.</span></span> <span data-ttu-id="9e659-247">*ScaffoldingReadme.txt* ファイルには、スキャフォールディングの更新を完了するために必要な手順に関する一般的な指示が含まれてい :::no-loc(Identity)::: ます。</span><span class="sxs-lookup"><span data-stu-id="9e659-247">The *ScaffoldingReadme.txt* file contains general instructions on what's needed to complete the :::no-loc(Identity)::: scaffolding update.</span></span> <span data-ttu-id="9e659-248">このドキュメントには、 *ScaffoldingReadme.txt* ファイルよりも完全な手順が含まれています。</span><span class="sxs-lookup"><span data-stu-id="9e659-248">This document contains more complete instructions than the *ScaffoldingReadme.txt* file.</span></span>

<span data-ttu-id="9e659-249">ファイルの違いを示すソース管理システムを使用して、変更を元に戻すことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="9e659-249">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="9e659-250">Scaffolder を実行した後に変更を確認し :::no-loc(Identity)::: ます。</span><span class="sxs-lookup"><span data-stu-id="9e659-250">Inspect the changes after running the :::no-loc(Identity)::: scaffolder.</span></span>

> [!NOTE]
> <span data-ttu-id="9e659-251">サービスは、 [2 要素認証](xref:security/authentication/identity-enable-qrcodes)、アカウントの [確認とパスワードの回復](xref:security/authentication/accconfirm)、およびの他のセキュリティ機能を使用する場合に必要です :::no-loc(Identity)::: 。</span><span class="sxs-lookup"><span data-stu-id="9e659-251">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with :::no-loc(Identity):::.</span></span> <span data-ttu-id="9e659-252">サービスまたはサービススタブは、スキャフォールディング時に生成されません :::no-loc(Identity)::: 。</span><span class="sxs-lookup"><span data-stu-id="9e659-252">Services or service stubs aren't generated when scaffolding :::no-loc(Identity):::.</span></span> <span data-ttu-id="9e659-253">これらの機能を有効にするサービスは、手動で追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="9e659-253">Services to enable these features must be added manually.</span></span> <span data-ttu-id="9e659-254">たとえば、「 [電子メールの確認を要求する](xref:security/authentication/accconfirm#require-email-confirmation)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9e659-254">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

## <a name="scaffold-no-locidentity-into-an-empty-project"></a><span data-ttu-id="9e659-255">:::no-loc(Identity):::空のプロジェクトにスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="9e659-255">Scaffold :::no-loc(Identity)::: into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="9e659-256">次の強調表示された呼び出しをクラスに追加し `Startup` ます。</span><span class="sxs-lookup"><span data-stu-id="9e659-256">Add the following highlighted calls to the `Startup` class:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-without-existing-authorization"></a><span data-ttu-id="9e659-257">:::no-loc(Identity)::: :::no-loc(Razor)::: 既存の承認なしでプロジェクトにスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="9e659-257">Scaffold :::no-loc(Identity)::: into a :::no-loc(Razor)::: project without existing authorization</span></span>

<!--  Updated for 3.0
set projNam=RPnoAuth
set projType=webapp

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.:::no-loc(Identity):::.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add Create:::no-loc(Identity):::Schema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="9e659-258">:::no-loc(Identity):::は、 *Areas/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs* で構成されます。</span><span class="sxs-lookup"><span data-stu-id="9e659-258">:::no-loc(Identity)::: is configured in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs* .</span></span> <span data-ttu-id="9e659-259">詳細については、「 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9e659-259">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="9e659-260">移行、UseAuthentication、およびレイアウト</span><span class="sxs-lookup"><span data-stu-id="9e659-260">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="9e659-261">認証を有効にする</span><span class="sxs-lookup"><span data-stu-id="9e659-261">Enable authentication</span></span>

<span data-ttu-id="9e659-262">`Configure`クラスのメソッドで `Startup` 、の後にを呼び出し <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> `UseStaticFiles` ます。</span><span class="sxs-lookup"><span data-stu-id="9e659-262">In the `Configure` method of the `Startup` class, call <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="9e659-263">レイアウトの変更</span><span class="sxs-lookup"><span data-stu-id="9e659-263">Layout changes</span></span>

<span data-ttu-id="9e659-264">省略可能: 次のように、ログイン部分 ( `_LoginPartial` ) をレイアウトファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="9e659-264">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-cshtml[](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-with-authorization"></a><span data-ttu-id="9e659-265">:::no-loc(Identity)::: :::no-loc(Razor)::: 承認を使用してプロジェクトにスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="9e659-265">Scaffold :::no-loc(Identity)::: into a :::no-loc(Razor)::: project with authorization</span></span>

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

<span data-ttu-id="9e659-266">一部 :::no-loc(Identity)::: のオプションは、 *Areas/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs* で構成されています。</span><span class="sxs-lookup"><span data-stu-id="9e659-266">Some :::no-loc(Identity)::: options are configured in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs* .</span></span> <span data-ttu-id="9e659-267">詳細については、「 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9e659-267">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-no-locidentity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="9e659-268">:::no-loc(Identity):::既存の承認なしで MVC プロジェクトにスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="9e659-268">Scaffold :::no-loc(Identity)::: into an MVC project without existing authorization</span></span>

<!--
set projNam=MvcNoAuth
set projType=mvc
set version=2.1.0

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v %version%
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add Create:::no-loc(Identity):::Schema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="9e659-269">省略可能: `_LoginPartial` *Views/Shared/_Layout cshtml* ファイルにログイン部分 () を追加します。</span><span class="sxs-lookup"><span data-stu-id="9e659-269">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-cshtml[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* <span data-ttu-id="9e659-270">*Pages/shared/_LoginPartial cshtml* ファイルを *Views/shared/_LoginPartial* に移動します。</span><span class="sxs-lookup"><span data-stu-id="9e659-270">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="9e659-271">:::no-loc(Identity):::は、 *Areas/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs* で構成されます。</span><span class="sxs-lookup"><span data-stu-id="9e659-271">:::no-loc(Identity)::: is configured in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs* .</span></span> <span data-ttu-id="9e659-272">詳細については、「IHostingStartup」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9e659-272">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="9e659-273">呼び出しの <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> 後 `UseStaticFiles` :</span><span class="sxs-lookup"><span data-stu-id="9e659-273">Call <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-no-locidentity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="9e659-274">:::no-loc(Identity):::承認を使用した MVC プロジェクトへのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="9e659-274">Scaffold :::no-loc(Identity)::: into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="9e659-275">*ページ/共有* フォルダーとそのフォルダー内のファイルを削除します。</span><span class="sxs-lookup"><span data-stu-id="9e659-275">Delete the *Pages/Shared* folder and the files in that folder.</span></span>

<a name="full"></a>

## <a name="create-full-no-locidentity-ui-source"></a><span data-ttu-id="9e659-276">完全な :::no-loc(Identity)::: UI ソースの作成</span><span class="sxs-lookup"><span data-stu-id="9e659-276">Create full :::no-loc(Identity)::: UI source</span></span>

<span data-ttu-id="9e659-277">UI の完全な制御を維持するには、scaffolder を実行し、 :::no-loc(Identity)::: :::no-loc(Identity)::: [ **すべてのファイルを上書き** する] を選択します。</span><span class="sxs-lookup"><span data-stu-id="9e659-277">To maintain full control of the :::no-loc(Identity)::: UI, run the :::no-loc(Identity)::: scaffolder and select **Override all files** .</span></span>

<span data-ttu-id="9e659-278">次の強調表示されたコードは、 :::no-loc(Identity)::: ASP.NET Core 2.1 web アプリで既定の UI をに置き換える変更を示して :::no-loc(Identity)::: います。</span><span class="sxs-lookup"><span data-stu-id="9e659-278">The following highlighted code shows the changes to replace the default :::no-loc(Identity)::: UI with :::no-loc(Identity)::: in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="9e659-279">この操作を行うと、UI を完全に制御でき :::no-loc(Identity)::: ます。</span><span class="sxs-lookup"><span data-stu-id="9e659-279">You might want to do this to have full control of the :::no-loc(Identity)::: UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="9e659-280">既定値 :::no-loc(Identity)::: は、次のコードで置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="9e659-280">The default :::no-loc(Identity)::: is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="9e659-281">次のコードでは、 [Loginpath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.loginpath)、 [logoutpath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.logoutpath)、および [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.accessdeniedpath)を設定しています。</span><span class="sxs-lookup"><span data-stu-id="9e659-281">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="9e659-282">`IEmailSender`の実装を登録します。次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="9e659-282">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->

## <a name="password-configuration"></a><span data-ttu-id="9e659-283">パスワードの構成</span><span class="sxs-lookup"><span data-stu-id="9e659-283">Password configuration</span></span>

<span data-ttu-id="9e659-284">がで構成されている場合は <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PasswordOptions> `Startup.ConfigureServices` 、スキャフォールディング pages のプロパティに[ `[StringLength]` 属性](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute)の構成が必要になることがあり `Password` :::no-loc(Identity)::: ます。</span><span class="sxs-lookup"><span data-stu-id="9e659-284">If <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PasswordOptions> are configured in `Startup.ConfigureServices`, [`[StringLength]` attribute](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) configuration might be required for the `Password` property in scaffolded :::no-loc(Identity)::: pages.</span></span> <span data-ttu-id="9e659-285">`InputModel``Password`プロパティは次のファイルにあります。</span><span class="sxs-lookup"><span data-stu-id="9e659-285">`InputModel` `Password` properties are found in the following files:</span></span>

* `Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs`
* `Areas/:::no-loc(Identity):::/Pages/Account/ResetPassword.cshtml.cs`

## <a name="disable-register-page"></a><span data-ttu-id="9e659-286">登録の無効化ページ</span><span class="sxs-lookup"><span data-stu-id="9e659-286">Disable register page</span></span>

<span data-ttu-id="9e659-287">ユーザー登録を無効にするには:</span><span class="sxs-lookup"><span data-stu-id="9e659-287">To disable user registration:</span></span>

* <span data-ttu-id="9e659-288">スキャフォールディング :::no-loc(Identity)::: 。</span><span class="sxs-lookup"><span data-stu-id="9e659-288">Scaffold :::no-loc(Identity):::.</span></span> <span data-ttu-id="9e659-289">Account、Account. Login、および Account. RegisterConfirmation を含めます。</span><span class="sxs-lookup"><span data-stu-id="9e659-289">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="9e659-290">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="9e659-290">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="9e659-291">ユーザーがこのエンドポイントから登録できないように、 *区分/ :::no-loc(Identity)::: /Pages/Account/Register.cshtml.cs* を更新します。</span><span class="sxs-lookup"><span data-stu-id="9e659-291">Update *Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="9e659-292">次の変更との整合性を保つために、 *区分/////また :::no-loc(Identity):::* はを更新します。</span><span class="sxs-lookup"><span data-stu-id="9e659-292">Update *Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="9e659-293">*区分///////// :::no-loc(Identity)::: ////* から登録リンクをコメントアウトまたは削除します。</span><span class="sxs-lookup"><span data-stu-id="9e659-293">Comment out or remove the registration link from *Areas/:::no-loc(Identity):::/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="9e659-294">[ *区分/ページ/ :::no-loc(Identity)::: アカウント/* 登録の確認] ページを更新します。</span><span class="sxs-lookup"><span data-stu-id="9e659-294">Update the *Areas/:::no-loc(Identity):::/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="9e659-295">コードとリンクを cshtml ファイルから削除します。</span><span class="sxs-lookup"><span data-stu-id="9e659-295">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="9e659-296">から確認コードを削除し `PageModel` ます。</span><span class="sxs-lookup"><span data-stu-id="9e659-296">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="9e659-297">別のアプリを使用してユーザーを追加する</span><span class="sxs-lookup"><span data-stu-id="9e659-297">Use another app to add users</span></span>

<span data-ttu-id="9e659-298">Web アプリの外部にユーザーを追加するためのメカニズムを提供します。</span><span class="sxs-lookup"><span data-stu-id="9e659-298">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="9e659-299">ユーザーを追加するためのオプションは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="9e659-299">Options to add users include:</span></span>

* <span data-ttu-id="9e659-300">専用管理者 web アプリ。</span><span class="sxs-lookup"><span data-stu-id="9e659-300">A dedicated admin web app.</span></span>
* <span data-ttu-id="9e659-301">コンソールアプリ。</span><span class="sxs-lookup"><span data-stu-id="9e659-301">A console app.</span></span>

<span data-ttu-id="9e659-302">次のコードは、ユーザーを追加する方法の概要を示しています。</span><span class="sxs-lookup"><span data-stu-id="9e659-302">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="9e659-303">ユーザーの一覧がメモリに読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="9e659-303">A list of users is read into memory.</span></span>
* <span data-ttu-id="9e659-304">ユーザーごとに一意の強力なパスワードが生成されます。</span><span class="sxs-lookup"><span data-stu-id="9e659-304">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="9e659-305">ユーザーがデータベースに追加され :::no-loc(Identity)::: ます。</span><span class="sxs-lookup"><span data-stu-id="9e659-305">The user is added to the :::no-loc(Identity)::: database.</span></span>
* <span data-ttu-id="9e659-306">ユーザーに通知され、パスワードを変更するように指示されます。</span><span class="sxs-lookup"><span data-stu-id="9e659-306">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="9e659-307">次のコードは、ユーザーの追加の概要を示しています。</span><span class="sxs-lookup"><span data-stu-id="9e659-307">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="9e659-308">同様の方法で、運用環境のシナリオにも対応できます。</span><span class="sxs-lookup"><span data-stu-id="9e659-308">A similar approach can be followed for production scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9e659-309">その他の資料</span><span class="sxs-lookup"><span data-stu-id="9e659-309">Additional resources</span></span>

* [<span data-ttu-id="9e659-310">ASP.NET Core 2.1 以降に認証コードが変更された</span><span class="sxs-lookup"><span data-stu-id="9e659-310">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end
