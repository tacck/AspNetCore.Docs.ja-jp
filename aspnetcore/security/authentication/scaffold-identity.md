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
ms.openlocfilehash: c79dfc64d4311088c3f9ea03aad7570189000e2a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053320"
---
# <a name="scaffold-no-locidentity-in-aspnet-core-projects"></a><span data-ttu-id="cef55-103">:::no-loc(Identity):::ASP.NET Core プロジェクトでのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="cef55-103">Scaffold :::no-loc(Identity)::: in ASP.NET Core projects</span></span>

<span data-ttu-id="cef55-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="cef55-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="cef55-105">ASP.NET Core は [:::no-loc(ASP.NET Core Identity):::](xref:security/authentication/identity) [ :::no-loc(Razor)::: クラスライブラリ](xref:razor-pages/ui-class)としてを提供します。</span><span class="sxs-lookup"><span data-stu-id="cef55-105">ASP.NET Core provides [:::no-loc(ASP.NET Core Identity):::](xref:security/authentication/identity) as a [:::no-loc(Razor)::: Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="cef55-106">を含むアプリケーション :::no-loc(Identity)::: では、scaffolder を適用して、 :::no-loc(Identity)::: :::no-loc(Razor)::: クラスライブラリ (rcl) に含まれているソースコードを選択的に追加できます。</span><span class="sxs-lookup"><span data-stu-id="cef55-106">Applications that include :::no-loc(Identity)::: can apply the scaffolder to selectively add the source code contained in the :::no-loc(Identity)::: :::no-loc(Razor)::: Class Library (RCL).</span></span> <span data-ttu-id="cef55-107">コードを変更して動作を変更できるように、ソース コードを生成できます。</span><span class="sxs-lookup"><span data-stu-id="cef55-107">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="cef55-108">たとえば、登録で使用するコードを生成するようにスキャフォルダーに指示できます。</span><span class="sxs-lookup"><span data-stu-id="cef55-108">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="cef55-109">生成されたコードは、:::no-loc(Identity)::: RCL の同じコードよりも優先されます。</span><span class="sxs-lookup"><span data-stu-id="cef55-109">Generated code takes precedence over the same code in the :::no-loc(Identity)::: RCL.</span></span> <span data-ttu-id="cef55-110">UI を完全に制御し、既定の RCL を使用しないようにするには、「 [完全な :::no-loc(Identity)::: ui ソースの作成](#full)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="cef55-110">To gain full control of the UI and not use the default RCL, see the section [Create full :::no-loc(Identity)::: UI source](#full).</span></span>

<span data-ttu-id="cef55-111">認証を含ま **ない** アプリケーションでは、scaffolder を適用して rcl パッケージを追加でき :::no-loc(Identity)::: ます。</span><span class="sxs-lookup"><span data-stu-id="cef55-111">Applications that do **not** include authentication can apply the scaffolder to add the RCL :::no-loc(Identity)::: package.</span></span> <span data-ttu-id="cef55-112">生成される :::no-loc(Identity)::: コードの選択オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="cef55-112">You have the option of selecting :::no-loc(Identity)::: code to be generated.</span></span>

<span data-ttu-id="cef55-113">Scaffolder は、必要なコードの大部分を生成しますが、プロセスを完了するにはプロジェクトを更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="cef55-113">Although the scaffolder generates most of the necessary code, you need to update your project to complete the process.</span></span> <span data-ttu-id="cef55-114">このドキュメントでは、スキャフォールディングの更新を完了するために必要な手順について説明し :::no-loc(Identity)::: ます。</span><span class="sxs-lookup"><span data-stu-id="cef55-114">This document explains the steps needed to complete an :::no-loc(Identity)::: scaffolding update.</span></span>

<span data-ttu-id="cef55-115">ファイルの違いを示すソース管理システムを使用して、変更を元に戻すことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="cef55-115">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="cef55-116">Scaffolder を実行した後に変更を確認し :::no-loc(Identity)::: ます。</span><span class="sxs-lookup"><span data-stu-id="cef55-116">Inspect the changes after running the :::no-loc(Identity)::: scaffolder.</span></span>

<span data-ttu-id="cef55-117">サービスは、 [2 要素認証](xref:security/authentication/identity-enable-qrcodes)、アカウントの [確認とパスワードの回復](xref:security/authentication/accconfirm)、およびの他のセキュリティ機能を使用する場合に必要です :::no-loc(Identity)::: 。</span><span class="sxs-lookup"><span data-stu-id="cef55-117">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with :::no-loc(Identity):::.</span></span> <span data-ttu-id="cef55-118">サービスまたはサービススタブは、スキャフォールディング時に生成されません :::no-loc(Identity)::: 。</span><span class="sxs-lookup"><span data-stu-id="cef55-118">Services or service stubs aren't generated when scaffolding :::no-loc(Identity):::.</span></span> <span data-ttu-id="cef55-119">これらの機能を有効にするサービスは、手動で追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="cef55-119">Services to enable these features must be added manually.</span></span> <span data-ttu-id="cef55-120">たとえば、「 [電子メールの確認を要求する](xref:security/authentication/accconfirm#require-email-confirmation)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cef55-120">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

<span data-ttu-id="cef55-121">:::no-loc(Identity):::新しいデータコンテキストを使用して、既存の個別のアカウントを持つプロジェクトにスキャフォールディングする場合:</span><span class="sxs-lookup"><span data-stu-id="cef55-121">When scaffolding :::no-loc(Identity)::: with a new data context into a project with existing individual accounts:</span></span>

* <span data-ttu-id="cef55-122">で `Startup.ConfigureServices` 、の呼び出しを削除します。</span><span class="sxs-lookup"><span data-stu-id="cef55-122">In `Startup.ConfigureServices`, remove the calls to:</span></span>
  * `AddDbContext`
  * `AddDefault:::no-loc(Identity):::`

<span data-ttu-id="cef55-123">たとえば、 `AddDbContext` と `AddDefault:::no-loc(Identity):::` は次のコードでコメントアウトされます。</span><span class="sxs-lookup"><span data-stu-id="cef55-123">For example, `AddDbContext` and `AddDefault:::no-loc(Identity):::` are commented out in the following code:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRemove.cs?name=snippet)]

<span data-ttu-id="cef55-124">上記のコードでは、 *区分/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs* に複製されたコードをコメントアウトします。</span><span class="sxs-lookup"><span data-stu-id="cef55-124">The preceeding code comments out the code that is duplicated in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs*</span></span>

<span data-ttu-id="cef55-125">通常、個別のアカウントで作成されたアプリは、新しいデータコンテキストを作成し **ない** ようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="cef55-125">Typically, apps that were created with individual accounts should \* **not** _ create a new data context.</span></span>

## <a name="scaffold-no-locidentity-into-an-empty-project"></a><span data-ttu-id="cef55-126">:::no-loc(Identity):::空のプロジェクトにスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="cef55-126">Scaffold :::no-loc(Identity)::: into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="cef55-127">`Startup`次のようなコードでクラスを更新します。</span><span class="sxs-lookup"><span data-stu-id="cef55-127">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-without-existing-authorization"></a><span data-ttu-id="cef55-128">:::no-loc(Identity)::: :::no-loc(Razor)::: 既存の承認なしでプロジェクトにスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="cef55-128">Scaffold :::no-loc(Identity)::: into a :::no-loc(Razor)::: project without existing authorization</span></span>

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

<span data-ttu-id="cef55-129">:::no-loc(Identity):::は _Areas/HostingStartup.cs \* で構成され :::no-loc(Identity)::: / :::no-loc(Identity)::: ます。</span><span class="sxs-lookup"><span data-stu-id="cef55-129">:::no-loc(Identity)::: is configured in _Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs\*.</span></span> <span data-ttu-id="cef55-130">詳細については、「 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cef55-130">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="cef55-131">移行、UseAuthentication、およびレイアウト</span><span class="sxs-lookup"><span data-stu-id="cef55-131">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="cef55-132">認証を有効にする</span><span class="sxs-lookup"><span data-stu-id="cef55-132">Enable authentication</span></span>

<span data-ttu-id="cef55-133">`Startup`次のようなコードでクラスを更新します。</span><span class="sxs-lookup"><span data-stu-id="cef55-133">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="cef55-134">レイアウトの変更</span><span class="sxs-lookup"><span data-stu-id="cef55-134">Layout changes</span></span>

<span data-ttu-id="cef55-135">省略可能: 次のように、ログイン部分 ( `_LoginPartial` ) をレイアウトファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="cef55-135">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-cshtml[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-with-authorization"></a><span data-ttu-id="cef55-136">:::no-loc(Identity)::: :::no-loc(Razor)::: 承認を使用してプロジェクトにスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="cef55-136">Scaffold :::no-loc(Identity)::: into a :::no-loc(Razor)::: project with authorization</span></span>

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

<span data-ttu-id="cef55-137">一部 :::no-loc(Identity)::: のオプションは、 *Areas/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs* で構成されています。</span><span class="sxs-lookup"><span data-stu-id="cef55-137">Some :::no-loc(Identity)::: options are configured in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs* .</span></span> <span data-ttu-id="cef55-138">詳細については、「 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cef55-138">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-no-locidentity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="cef55-139">:::no-loc(Identity):::既存の承認なしで MVC プロジェクトにスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="cef55-139">Scaffold :::no-loc(Identity)::: into an MVC project without existing authorization</span></span>

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

<span data-ttu-id="cef55-140">省略可能: `_LoginPartial` *Views/Shared/_Layout cshtml* ファイルにログイン部分 () を追加します。</span><span class="sxs-lookup"><span data-stu-id="cef55-140">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-cshtml[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* <span data-ttu-id="cef55-141">*Pages/shared/_LoginPartial cshtml* ファイルを *Views/shared/_LoginPartial* に移動します。</span><span class="sxs-lookup"><span data-stu-id="cef55-141">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="cef55-142">:::no-loc(Identity):::は、 *Areas/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs* で構成されます。</span><span class="sxs-lookup"><span data-stu-id="cef55-142">:::no-loc(Identity)::: is configured in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs* .</span></span> <span data-ttu-id="cef55-143">詳細については、「IHostingStartup」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cef55-143">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="cef55-144">`Startup`次のようなコードでクラスを更新します。</span><span class="sxs-lookup"><span data-stu-id="cef55-144">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-no-locidentity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="cef55-145">:::no-loc(Identity):::承認を使用した MVC プロジェクトへのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="cef55-145">Scaffold :::no-loc(Identity)::: into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

## <a name="scaffold-no-locidentity-into-a-no-locblazor-server-project-without-existing-authorization"></a><span data-ttu-id="cef55-146">:::no-loc(Identity)::: :::no-loc(Blazor Server)::: 既存の承認なしでプロジェクトにスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="cef55-146">Scaffold :::no-loc(Identity)::: into a :::no-loc(Blazor Server)::: project without existing authorization</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="cef55-147">:::no-loc(Identity):::は、 *Areas/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs* で構成されます。</span><span class="sxs-lookup"><span data-stu-id="cef55-147">:::no-loc(Identity)::: is configured in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs* .</span></span> <span data-ttu-id="cef55-148">詳細については、「 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cef55-148">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

### <a name="migrations"></a><span data-ttu-id="cef55-149">移行</span><span class="sxs-lookup"><span data-stu-id="cef55-149">Migrations</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

### <a name="pass-an-xsrf-token-to-the-app"></a><span data-ttu-id="cef55-150">XSRF トークンをアプリに渡す</span><span class="sxs-lookup"><span data-stu-id="cef55-150">Pass an XSRF token to the app</span></span>

<span data-ttu-id="cef55-151">トークンは、次のようにコンポーネントに渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="cef55-151">Tokens can be passed to components:</span></span>

* <span data-ttu-id="cef55-152">認証トークンがプロビジョニングされ、認証に保存されると :::no-loc(cookie)::: 、コンポーネントに渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="cef55-152">When authentication tokens are provisioned and saved to the authentication :::no-loc(cookie):::, they can be passed to components.</span></span>
* <span data-ttu-id="cef55-153">:::no-loc(Razor)::: コンポーネントでを直接使用することはできない `HttpContext` ため、のログアウトエンドポイントをに送信するための [要求防止偽造 (XSRF) トークン](xref:security/anti-request-forgery) を取得する方法はありません :::no-loc(Identity)::: `/:::no-loc(Identity):::/Account/Logout` 。</span><span class="sxs-lookup"><span data-stu-id="cef55-153">:::no-loc(Razor)::: components can't use `HttpContext` directly, so there's no way to obtain an [anti-request forgery (XSRF) token](xref:security/anti-request-forgery) to POST to :::no-loc(Identity):::'s logout endpoint at `/:::no-loc(Identity):::/Account/Logout`.</span></span> <span data-ttu-id="cef55-154">XSRF トークンは、コンポーネントに渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="cef55-154">An XSRF token can be passed to components.</span></span>

<span data-ttu-id="cef55-155">詳細については、「<xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cef55-155">For more information, see <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.</span></span>

<span data-ttu-id="cef55-156">*Pages/_Host cshtml* ファイルで、クラスおよびクラスに追加した後、トークンを設定し `InitialApplicationState` `TokenProvider` ます。</span><span class="sxs-lookup"><span data-stu-id="cef55-156">In the *Pages/_Host.cshtml* file, establish the token after adding it to the `InitialApplicationState` and `TokenProvider` classes:</span></span>

```csharp
@inject Microsoft.AspNetCore.Antiforgery.IAntiforgery Xsrf

...

var tokens = new InitialApplicationState
{
    ...

    XsrfToken = Xsrf.GetAndStoreTokens(HttpContext).RequestToken
};
```

<span data-ttu-id="cef55-157">`App`コンポーネント ( *app.xaml* ) を更新して、次の `InitialState.XsrfToken` ものを割り当てます。</span><span class="sxs-lookup"><span data-stu-id="cef55-157">Update the `App` component ( *App.razor* ) to assign the `InitialState.XsrfToken`:</span></span>

```csharp
@inject TokenProvider TokenProvider

...

TokenProvider.XsrfToken = InitialState.XsrfToken;
```

<span data-ttu-id="cef55-158">トピック「」で説明されているサービスは、 `TokenProvider` `LoginDisplay` 次の [レイアウトと認証フローの変更](#layout-and-authentication-flow-changes) セクションのコンポーネントで使用されます。</span><span class="sxs-lookup"><span data-stu-id="cef55-158">The `TokenProvider` service demonstrated in the topic is used in the `LoginDisplay` component in the following [Layout and authentication flow changes](#layout-and-authentication-flow-changes) section.</span></span>

### <a name="enable-authentication"></a><span data-ttu-id="cef55-159">認証を有効にする</span><span class="sxs-lookup"><span data-stu-id="cef55-159">Enable authentication</span></span>

<span data-ttu-id="cef55-160">クラスの場合 `Startup` :</span><span class="sxs-lookup"><span data-stu-id="cef55-160">In the `Startup` class:</span></span>

* <span data-ttu-id="cef55-161">ページサービスがに追加されていることを確認 :::no-loc(Razor)::: `Startup.ConfigureServices` します。</span><span class="sxs-lookup"><span data-stu-id="cef55-161">Confirm that :::no-loc(Razor)::: Pages services are added in `Startup.ConfigureServices`.</span></span>
* <span data-ttu-id="cef55-162">[TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app)を使用する場合は、サービスを登録します。</span><span class="sxs-lookup"><span data-stu-id="cef55-162">If using the [TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app), register the service.</span></span>
* <span data-ttu-id="cef55-163">`UseDatabaseErrorPage`開発環境用のアプリケーションビルダーでを呼び出し `Startup.Configure` ます。</span><span class="sxs-lookup"><span data-stu-id="cef55-163">Call `UseDatabaseErrorPage` on the application builder in `Startup.Configure` for the Development environment.</span></span>
* <span data-ttu-id="cef55-164">`UseAuthentication`との後にを呼び出し `UseAuthorization` `UseRouting` ます。</span><span class="sxs-lookup"><span data-stu-id="cef55-164">Call `UseAuthentication` and `UseAuthorization` after `UseRouting`.</span></span>
* <span data-ttu-id="cef55-165">ページのエンドポイントを追加 :::no-loc(Razor)::: します。</span><span class="sxs-lookup"><span data-stu-id="cef55-165">Add an endpoint for :::no-loc(Razor)::: Pages.</span></span>

[!code-csharp[](scaffold-identity/3.1sample/Startup:::no-loc(Blazor):::.cs?highlight=3,6,14,27-28,32)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-and-authentication-flow-changes"></a><span data-ttu-id="cef55-166">レイアウトと認証のフローの変更</span><span class="sxs-lookup"><span data-stu-id="cef55-166">Layout and authentication flow changes</span></span>

<span data-ttu-id="cef55-167">`RedirectToLogin`プロジェクトルートのアプリの *共有* フォルダーにコンポーネント ( *redirecttologin. razor* ) を追加します。</span><span class="sxs-lookup"><span data-stu-id="cef55-167">Add a `RedirectToLogin` component ( *RedirectToLogin.razor* ) to the app's *Shared* folder in the project root:</span></span>

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

`LoginDisplay`アプリの *共有* フォルダーにコンポーネント ( *logindisplay. razor* ) を追加します。 <span data-ttu-id="cef55-169">[TokenProvider サービス](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app)は、ログアウトエンドポイントにポストする HTML フォームの XSRF トークンを提供し :::no-loc(Identity)::: ます。</span><span class="sxs-lookup"><span data-stu-id="cef55-169">The [TokenProvider service](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app) provides the XSRF token for the HTML form that POSTs to :::no-loc(Identity):::'s logout endpoint:</span></span>

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

<span data-ttu-id="cef55-170">`MainLayout`コンポーネント ( *Shared/mainlayout. razor* ) で、 `LoginDisplay` 最上位の要素のコンテンツにコンポーネントを追加し `<div>` ます。</span><span class="sxs-lookup"><span data-stu-id="cef55-170">In the `MainLayout` component ( *Shared/MainLayout.razor* ), add the `LoginDisplay` component to the top-row `<div>` element's content:</span></span>

```razor
<div class="top-row px-4 auth">
    <LoginDisplay />
    <a href="https://docs.microsoft.com/aspnet/" target="_blank">About</a>
</div>
```

### <a name="style-authentication-endpoints"></a><span data-ttu-id="cef55-171">認証エンドポイントのスタイルを適用する</span><span class="sxs-lookup"><span data-stu-id="cef55-171">Style authentication endpoints</span></span>

<span data-ttu-id="cef55-172">ではページページが使用されるため、 :::no-loc(Blazor Server)::: :::no-loc(Razor)::: :::no-loc(Identity)::: ビジターがページとコンポーネントの間を移動すると、UI のスタイルが変更され :::no-loc(Identity)::: ます。</span><span class="sxs-lookup"><span data-stu-id="cef55-172">Because :::no-loc(Blazor Server)::: uses :::no-loc(Razor)::: Pages :::no-loc(Identity)::: pages, the styling of the UI changes when a visitor navigates between :::no-loc(Identity)::: pages and components.</span></span> <span data-ttu-id="cef55-173">Incongruous スタイルに対処するには、次の2つのオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="cef55-173">You have two options to address the incongruous styles:</span></span>

#### <a name="build-no-locidentity-components"></a><span data-ttu-id="cef55-174">ビルド :::no-loc(Identity)::: コンポーネント</span><span class="sxs-lookup"><span data-stu-id="cef55-174">Build :::no-loc(Identity)::: components</span></span>

<span data-ttu-id="cef55-175">ページではなくコンポーネントを使用する方法 :::no-loc(Identity)::: は、コンポーネントを構築することです :::no-loc(Identity)::: 。</span><span class="sxs-lookup"><span data-stu-id="cef55-175">An approach to using components for :::no-loc(Identity)::: instead of pages is to build :::no-loc(Identity)::: components.</span></span> <span data-ttu-id="cef55-176">`SignInManager`とは `UserManager` コンポーネントでサポートされていないため :::no-loc(Razor)::: 、アプリの API エンドポイントを使用して、 :::no-loc(Blazor Server)::: ユーザーアカウントの操作を処理します。</span><span class="sxs-lookup"><span data-stu-id="cef55-176">Because `SignInManager` and `UserManager` aren't supported in :::no-loc(Razor)::: components, use API endpoints in the :::no-loc(Blazor Server)::: app to process user account actions.</span></span>

#### <a name="use-a-custom-layout-with-no-locblazor-app-styles"></a><span data-ttu-id="cef55-177">アプリスタイルでカスタムレイアウトを使用する :::no-loc(Blazor):::</span><span class="sxs-lookup"><span data-stu-id="cef55-177">Use a custom layout with :::no-loc(Blazor)::: app styles</span></span>

<span data-ttu-id="cef55-178">:::no-loc(Identity):::ページのレイアウトとスタイルを変更して、既定のテーマを使用するページを生成することができ :::no-loc(Blazor)::: ます。</span><span class="sxs-lookup"><span data-stu-id="cef55-178">The :::no-loc(Identity)::: pages layout and styles can be modified to produce pages that use the default :::no-loc(Blazor)::: theme.</span></span>

> [!NOTE]
> <span data-ttu-id="cef55-179">このセクションの例は、カスタマイズの開始点にすぎません。</span><span class="sxs-lookup"><span data-stu-id="cef55-179">The example in this section is merely a starting point for customization.</span></span> <span data-ttu-id="cef55-180">最適なユーザーエクスペリエンスを実現するには、追加の作業が必要になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="cef55-180">Additional work is likely required for the best user experience.</span></span>

<span data-ttu-id="cef55-181">新しい `NavMenu_:::no-loc(Identity):::Layout` コンポーネント ( *Shared/NavMenu_ :::no-loc(Identity)::: Layout* ) を作成します。</span><span class="sxs-lookup"><span data-stu-id="cef55-181">Create a new `NavMenu_:::no-loc(Identity):::Layout` component ( *Shared/NavMenu_:::no-loc(Identity):::Layout.razor* ).</span></span> <span data-ttu-id="cef55-182">コンポーネントのマークアップとコードについては、アプリのコンポーネントの同じコンテンツを使用し `NavMenu` ます ( *共有/ナビゲーションメニュー。 razor* )。</span><span class="sxs-lookup"><span data-stu-id="cef55-182">For the markup and code of the component, use the same content of the app's `NavMenu` component ( *Shared/NavMenu.razor* ).</span></span> <span data-ttu-id="cef55-183">コンポーネントの `NavLink` 自動リダイレクトが `RedirectToLogin` 認証または承認を必要とするコンポーネントに対して失敗するため、匿名にアクセスできないコンポーネントに対してを除去します。</span><span class="sxs-lookup"><span data-stu-id="cef55-183">Strip out any `NavLink`s to components that can't be reached anonymously because automatic redirects in the `RedirectToLogin` component fail for components requiring authentication or authorization.</span></span>

<span data-ttu-id="cef55-184">*Pages/Shared/Layout. cshtml* ファイルで、次の変更を行います。</span><span class="sxs-lookup"><span data-stu-id="cef55-184">In the *Pages/Shared/Layout.cshtml* file, make the following changes:</span></span>

* <span data-ttu-id="cef55-185">:::no-loc(Razor):::タグヘルパーと *共有* フォルダー内のアプリのコンポーネントを使用するために、ファイルの先頭にディレクティブを追加します。</span><span class="sxs-lookup"><span data-stu-id="cef55-185">Add :::no-loc(Razor)::: directives to the top of the file to use Tag Helpers and the app's components in the *Shared* folder:</span></span>

  ```cshtml
  @addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
  @using {APPLICATION ASSEMBLY}.Shared
  ```

  <span data-ttu-id="cef55-186">`{APPLICATION ASSEMBLY}`をアプリのアセンブリ名に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="cef55-186">Replace `{APPLICATION ASSEMBLY}` with the app's assembly name.</span></span>

* <span data-ttu-id="cef55-187">`<base>`コンテンツにタグと :::no-loc(Blazor)::: スタイルシートを追加し `<link>` `<head>` ます。</span><span class="sxs-lookup"><span data-stu-id="cef55-187">Add a `<base>` tag and :::no-loc(Blazor)::: stylesheet `<link>` to the `<head>` content:</span></span>

  ```cshtml
  <base href="~/" />
  <link rel="stylesheet" href="~/css/site.css" />
  ```

* <span data-ttu-id="cef55-188">タグの内容 `<body>` を次のように変更します。</span><span class="sxs-lookup"><span data-stu-id="cef55-188">Change the content of the `<body>` tag to the following:</span></span>

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

## <a name="scaffold-no-locidentity-into-a-no-locblazor-server-project-with-authorization"></a><span data-ttu-id="cef55-189">:::no-loc(Identity)::: :::no-loc(Blazor Server)::: 承認を使用してプロジェクトにスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="cef55-189">Scaffold :::no-loc(Identity)::: into a :::no-loc(Blazor Server)::: project with authorization</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="cef55-190">一部 :::no-loc(Identity)::: のオプションは、 *Areas/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs* で構成されています。</span><span class="sxs-lookup"><span data-stu-id="cef55-190">Some :::no-loc(Identity)::: options are configured in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs* .</span></span> <span data-ttu-id="cef55-191">詳細については、「 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cef55-191">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="full"></a>

## <a name="create-full-no-locidentity-ui-source"></a><span data-ttu-id="cef55-192">完全な :::no-loc(Identity)::: UI ソースの作成</span><span class="sxs-lookup"><span data-stu-id="cef55-192">Create full :::no-loc(Identity)::: UI source</span></span>

<span data-ttu-id="cef55-193">UI の完全な制御を維持するには、scaffolder を実行し、 :::no-loc(Identity)::: :::no-loc(Identity)::: [ **すべてのファイルを上書き** する] を選択します。</span><span class="sxs-lookup"><span data-stu-id="cef55-193">To maintain full control of the :::no-loc(Identity)::: UI, run the :::no-loc(Identity)::: scaffolder and select **Override all files** .</span></span>

<span data-ttu-id="cef55-194">次の強調表示されたコードは、 :::no-loc(Identity)::: ASP.NET Core 2.1 web アプリで既定の UI をに置き換える変更を示して :::no-loc(Identity)::: います。</span><span class="sxs-lookup"><span data-stu-id="cef55-194">The following highlighted code shows the changes to replace the default :::no-loc(Identity)::: UI with :::no-loc(Identity)::: in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="cef55-195">この操作を行うと、UI を完全に制御でき :::no-loc(Identity)::: ます。</span><span class="sxs-lookup"><span data-stu-id="cef55-195">You might want to do this to have full control of the :::no-loc(Identity)::: UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="cef55-196">既定値 :::no-loc(Identity)::: は、次のコードで置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="cef55-196">The default :::no-loc(Identity)::: is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="cef55-197">次のコードでは、 [Loginpath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.loginpath)、 [logoutpath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.logoutpath)、および [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.accessdeniedpath)を設定しています。</span><span class="sxs-lookup"><span data-stu-id="cef55-197">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="cef55-198">`IEmailSender`の実装を登録します。次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="cef55-198">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->

## <a name="password-configuration"></a><span data-ttu-id="cef55-199">パスワードの構成</span><span class="sxs-lookup"><span data-stu-id="cef55-199">Password configuration</span></span>

<span data-ttu-id="cef55-200">がで構成されている場合は <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PasswordOptions> `Startup.ConfigureServices` 、スキャフォールディング pages のプロパティに[ `[StringLength]` 属性](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute)の構成が必要になることがあり `Password` :::no-loc(Identity)::: ます。</span><span class="sxs-lookup"><span data-stu-id="cef55-200">If <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PasswordOptions> are configured in `Startup.ConfigureServices`, [`[StringLength]` attribute](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) configuration might be required for the `Password` property in scaffolded :::no-loc(Identity)::: pages.</span></span> <span data-ttu-id="cef55-201">`InputModel``Password`プロパティは次のファイルにあります。</span><span class="sxs-lookup"><span data-stu-id="cef55-201">`InputModel` `Password` properties are found in the following files:</span></span>

* `Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs`
* `Areas/:::no-loc(Identity):::/Pages/Account/ResetPassword.cshtml.cs`

## <a name="disable-a-page"></a><span data-ttu-id="cef55-202">ページを無効にする</span><span class="sxs-lookup"><span data-stu-id="cef55-202">Disable a page</span></span>

<span data-ttu-id="cef55-203">このセクションでは、登録ページを無効にする方法について説明しますが、この方法を使用して任意のページを無効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="cef55-203">This sections show how to disable the register page but the approach can be used to disable any page.</span></span>

<span data-ttu-id="cef55-204">ユーザー登録を無効にするには:</span><span class="sxs-lookup"><span data-stu-id="cef55-204">To disable user registration:</span></span>

* <span data-ttu-id="cef55-205">スキャフォールディング :::no-loc(Identity)::: 。</span><span class="sxs-lookup"><span data-stu-id="cef55-205">Scaffold :::no-loc(Identity):::.</span></span> <span data-ttu-id="cef55-206">Account、Account. Login、および Account. RegisterConfirmation を含めます。</span><span class="sxs-lookup"><span data-stu-id="cef55-206">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="cef55-207">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="cef55-207">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="cef55-208">ユーザーがこのエンドポイントから登録できないように、 *区分/ :::no-loc(Identity)::: /Pages/Account/Register.cshtml.cs* を更新します。</span><span class="sxs-lookup"><span data-stu-id="cef55-208">Update *Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="cef55-209">次の変更との整合性を保つために、 *区分/////また :::no-loc(Identity):::* はを更新します。</span><span class="sxs-lookup"><span data-stu-id="cef55-209">Update *Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="cef55-210">*区分///////// :::no-loc(Identity)::: ////* から登録リンクをコメントアウトまたは削除します。</span><span class="sxs-lookup"><span data-stu-id="cef55-210">Comment out or remove the registration link from *Areas/:::no-loc(Identity):::/Pages/Account/Login.cshtml*</span></span>

  ```cshtml
  @*
  <p>
      <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
  </p>
  *@
  ```

* <span data-ttu-id="cef55-211">[ *区分/ページ/ :::no-loc(Identity)::: アカウント/* 登録の確認] ページを更新します。</span><span class="sxs-lookup"><span data-stu-id="cef55-211">Update the *Areas/:::no-loc(Identity):::/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="cef55-212">コードとリンクを cshtml ファイルから削除します。</span><span class="sxs-lookup"><span data-stu-id="cef55-212">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="cef55-213">から確認コードを削除し `PageModel` ます。</span><span class="sxs-lookup"><span data-stu-id="cef55-213">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="cef55-214">別のアプリを使用してユーザーを追加する</span><span class="sxs-lookup"><span data-stu-id="cef55-214">Use another app to add users</span></span>

<span data-ttu-id="cef55-215">Web アプリの外部にユーザーを追加するためのメカニズムを提供します。</span><span class="sxs-lookup"><span data-stu-id="cef55-215">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="cef55-216">ユーザーを追加するためのオプションは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="cef55-216">Options to add users include:</span></span>

* <span data-ttu-id="cef55-217">専用管理者 web アプリ。</span><span class="sxs-lookup"><span data-stu-id="cef55-217">A dedicated admin web app.</span></span>
* <span data-ttu-id="cef55-218">コンソールアプリ。</span><span class="sxs-lookup"><span data-stu-id="cef55-218">A console app.</span></span>

<span data-ttu-id="cef55-219">次のコードは、ユーザーを追加する方法の概要を示しています。</span><span class="sxs-lookup"><span data-stu-id="cef55-219">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="cef55-220">ユーザーの一覧がメモリに読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="cef55-220">A list of users is read into memory.</span></span>
* <span data-ttu-id="cef55-221">ユーザーごとに一意の強力なパスワードが生成されます。</span><span class="sxs-lookup"><span data-stu-id="cef55-221">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="cef55-222">ユーザーがデータベースに追加され :::no-loc(Identity)::: ます。</span><span class="sxs-lookup"><span data-stu-id="cef55-222">The user is added to the :::no-loc(Identity)::: database.</span></span>
* <span data-ttu-id="cef55-223">ユーザーに通知され、パスワードを変更するように指示されます。</span><span class="sxs-lookup"><span data-stu-id="cef55-223">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="cef55-224">次のコードは、ユーザーの追加の概要を示しています。</span><span class="sxs-lookup"><span data-stu-id="cef55-224">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="cef55-225">同様の方法で、運用環境のシナリオにも対応できます。</span><span class="sxs-lookup"><span data-stu-id="cef55-225">A similar approach can be followed for production scenarios.</span></span>

## <a name="prevent-publish-of-static-no-locidentity-assets"></a><span data-ttu-id="cef55-226">静的アセットの発行を禁止する :::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="cef55-226">Prevent publish of static :::no-loc(Identity)::: assets</span></span>

<span data-ttu-id="cef55-227">静的なアセットが web ルートに発行されないようにするに :::no-loc(Identity)::: は、「」を参照してください <xref:security/authentication/identity#prevent-publish-of-static-identity-assets> 。</span><span class="sxs-lookup"><span data-stu-id="cef55-227">To prevent publishing static :::no-loc(Identity)::: assets to the web root, see <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cef55-228">その他の資料</span><span class="sxs-lookup"><span data-stu-id="cef55-228">Additional resources</span></span>

* [<span data-ttu-id="cef55-229">ASP.NET Core 2.1 以降に認証コードが変更された</span><span class="sxs-lookup"><span data-stu-id="cef55-229">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="cef55-230">ASP.NET Core 2.1 以降は、 [:::no-loc(ASP.NET Core Identity):::](xref:security/authentication/identity) [ :::no-loc(Razor)::: クラスライブラリ](xref:razor-pages/ui-class)としてを提供します。</span><span class="sxs-lookup"><span data-stu-id="cef55-230">ASP.NET Core 2.1 and later provides [:::no-loc(ASP.NET Core Identity):::](xref:security/authentication/identity) as a [:::no-loc(Razor)::: Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="cef55-231">を含むアプリケーション :::no-loc(Identity)::: では、scaffolder を適用して、 :::no-loc(Identity)::: :::no-loc(Razor)::: クラスライブラリ (rcl) に含まれているソースコードを選択的に追加できます。</span><span class="sxs-lookup"><span data-stu-id="cef55-231">Applications that include :::no-loc(Identity)::: can apply the scaffolder to selectively add the source code contained in the :::no-loc(Identity)::: :::no-loc(Razor)::: Class Library (RCL).</span></span> <span data-ttu-id="cef55-232">コードを変更して動作を変更できるように、ソース コードを生成できます。</span><span class="sxs-lookup"><span data-stu-id="cef55-232">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="cef55-233">たとえば、登録で使用するコードを生成するようにスキャフォルダーに指示できます。</span><span class="sxs-lookup"><span data-stu-id="cef55-233">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="cef55-234">生成されたコードは、:::no-loc(Identity)::: RCL の同じコードよりも優先されます。</span><span class="sxs-lookup"><span data-stu-id="cef55-234">Generated code takes precedence over the same code in the :::no-loc(Identity)::: RCL.</span></span> <span data-ttu-id="cef55-235">UI を完全に制御し、既定の RCL を使用しないようにするには、「 [完全な ID UI ソースの作成](#full)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="cef55-235">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="cef55-236">認証を含ま **ない** アプリケーションでは、scaffolder を適用して rcl パッケージを追加でき :::no-loc(Identity)::: ます。</span><span class="sxs-lookup"><span data-stu-id="cef55-236">Applications that do **not** include authentication can apply the scaffolder to add the RCL :::no-loc(Identity)::: package.</span></span> <span data-ttu-id="cef55-237">生成される :::no-loc(Identity)::: コードの選択オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="cef55-237">You have the option of selecting :::no-loc(Identity)::: code to be generated.</span></span>

<span data-ttu-id="cef55-238">Scaffolder は、必要なコードの大部分を生成しますが、プロセスを完了するにはプロジェクトを更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="cef55-238">Although the scaffolder generates most of the necessary code, you'll have to update your project to complete the process.</span></span> <span data-ttu-id="cef55-239">このドキュメントでは、スキャフォールディングの更新を完了するために必要な手順について説明し :::no-loc(Identity)::: ます。</span><span class="sxs-lookup"><span data-stu-id="cef55-239">This document explains the steps needed to complete an :::no-loc(Identity)::: scaffolding update.</span></span>

<span data-ttu-id="cef55-240">Scaffolder を :::no-loc(Identity)::: 実行すると、プロジェクトディレクトリに *ScaffoldingReadme.txt* ファイルが作成されます。</span><span class="sxs-lookup"><span data-stu-id="cef55-240">When the :::no-loc(Identity)::: scaffolder is run, a *ScaffoldingReadme.txt* file is created in the project directory.</span></span> <span data-ttu-id="cef55-241">*ScaffoldingReadme.txt* ファイルには、スキャフォールディングの更新を完了するために必要な手順に関する一般的な指示が含まれてい :::no-loc(Identity)::: ます。</span><span class="sxs-lookup"><span data-stu-id="cef55-241">The *ScaffoldingReadme.txt* file contains general instructions on what's needed to complete the :::no-loc(Identity)::: scaffolding update.</span></span> <span data-ttu-id="cef55-242">このドキュメントには、 *ScaffoldingReadme.txt* ファイルよりも完全な手順が含まれています。</span><span class="sxs-lookup"><span data-stu-id="cef55-242">This document contains more complete instructions than the *ScaffoldingReadme.txt* file.</span></span>

<span data-ttu-id="cef55-243">ファイルの違いを示すソース管理システムを使用して、変更を元に戻すことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="cef55-243">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="cef55-244">Scaffolder を実行した後に変更を確認し :::no-loc(Identity)::: ます。</span><span class="sxs-lookup"><span data-stu-id="cef55-244">Inspect the changes after running the :::no-loc(Identity)::: scaffolder.</span></span>

> [!NOTE]
> <span data-ttu-id="cef55-245">サービスは、 [2 要素認証](xref:security/authentication/identity-enable-qrcodes)、アカウントの [確認とパスワードの回復](xref:security/authentication/accconfirm)、およびの他のセキュリティ機能を使用する場合に必要です :::no-loc(Identity)::: 。</span><span class="sxs-lookup"><span data-stu-id="cef55-245">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with :::no-loc(Identity):::.</span></span> <span data-ttu-id="cef55-246">サービスまたはサービススタブは、スキャフォールディング時に生成されません :::no-loc(Identity)::: 。</span><span class="sxs-lookup"><span data-stu-id="cef55-246">Services or service stubs aren't generated when scaffolding :::no-loc(Identity):::.</span></span> <span data-ttu-id="cef55-247">これらの機能を有効にするサービスは、手動で追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="cef55-247">Services to enable these features must be added manually.</span></span> <span data-ttu-id="cef55-248">たとえば、「 [電子メールの確認を要求する](xref:security/authentication/accconfirm#require-email-confirmation)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cef55-248">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

## <a name="scaffold-no-locidentity-into-an-empty-project"></a><span data-ttu-id="cef55-249">:::no-loc(Identity):::空のプロジェクトにスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="cef55-249">Scaffold :::no-loc(Identity)::: into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="cef55-250">次の強調表示された呼び出しをクラスに追加し `Startup` ます。</span><span class="sxs-lookup"><span data-stu-id="cef55-250">Add the following highlighted calls to the `Startup` class:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-without-existing-authorization"></a><span data-ttu-id="cef55-251">:::no-loc(Identity)::: :::no-loc(Razor)::: 既存の承認なしでプロジェクトにスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="cef55-251">Scaffold :::no-loc(Identity)::: into a :::no-loc(Razor)::: project without existing authorization</span></span>

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

<span data-ttu-id="cef55-252">:::no-loc(Identity):::は、 *Areas/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs* で構成されます。</span><span class="sxs-lookup"><span data-stu-id="cef55-252">:::no-loc(Identity)::: is configured in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs* .</span></span> <span data-ttu-id="cef55-253">詳細については、「 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cef55-253">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="cef55-254">移行、UseAuthentication、およびレイアウト</span><span class="sxs-lookup"><span data-stu-id="cef55-254">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="cef55-255">認証を有効にする</span><span class="sxs-lookup"><span data-stu-id="cef55-255">Enable authentication</span></span>

<span data-ttu-id="cef55-256">`Configure`クラスのメソッドで `Startup` 、次のように[useauthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)を呼び出し `UseStaticFiles` ます。</span><span class="sxs-lookup"><span data-stu-id="cef55-256">In the `Configure` method of the `Startup` class, call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="cef55-257">レイアウトの変更</span><span class="sxs-lookup"><span data-stu-id="cef55-257">Layout changes</span></span>

<span data-ttu-id="cef55-258">省略可能: 次のように、ログイン部分 ( `_LoginPartial` ) をレイアウトファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="cef55-258">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-cshtml[](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-with-authorization"></a><span data-ttu-id="cef55-259">:::no-loc(Identity)::: :::no-loc(Razor)::: 承認を使用してプロジェクトにスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="cef55-259">Scaffold :::no-loc(Identity)::: into a :::no-loc(Razor)::: project with authorization</span></span>

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

<span data-ttu-id="cef55-260">一部 :::no-loc(Identity)::: のオプションは、 *Areas/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs* で構成されています。</span><span class="sxs-lookup"><span data-stu-id="cef55-260">Some :::no-loc(Identity)::: options are configured in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs* .</span></span> <span data-ttu-id="cef55-261">詳細については、「 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cef55-261">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-no-locidentity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="cef55-262">:::no-loc(Identity):::既存の承認なしで MVC プロジェクトにスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="cef55-262">Scaffold :::no-loc(Identity)::: into an MVC project without existing authorization</span></span>

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

<span data-ttu-id="cef55-263">省略可能: `_LoginPartial` *Views/Shared/_Layout cshtml* ファイルにログイン部分 () を追加します。</span><span class="sxs-lookup"><span data-stu-id="cef55-263">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-cshtml[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* <span data-ttu-id="cef55-264">*Pages/shared/_LoginPartial cshtml* ファイルを *Views/shared/_LoginPartial* に移動します。</span><span class="sxs-lookup"><span data-stu-id="cef55-264">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="cef55-265">:::no-loc(Identity):::は、 *Areas/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs* で構成されます。</span><span class="sxs-lookup"><span data-stu-id="cef55-265">:::no-loc(Identity)::: is configured in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs* .</span></span> <span data-ttu-id="cef55-266">詳細については、「IHostingStartup」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cef55-266">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="cef55-267">次の後に [Useauthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) を呼び出し `UseStaticFiles` ます。</span><span class="sxs-lookup"><span data-stu-id="cef55-267">Call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-no-locidentity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="cef55-268">:::no-loc(Identity):::承認を使用した MVC プロジェクトへのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="cef55-268">Scaffold :::no-loc(Identity)::: into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="cef55-269">*ページ/共有* フォルダーとそのフォルダー内のファイルを削除します。</span><span class="sxs-lookup"><span data-stu-id="cef55-269">Delete the *Pages/Shared* folder and the files in that folder.</span></span>

<a name="full"></a>

## <a name="create-full-no-locidentity-ui-source"></a><span data-ttu-id="cef55-270">完全な :::no-loc(Identity)::: UI ソースの作成</span><span class="sxs-lookup"><span data-stu-id="cef55-270">Create full :::no-loc(Identity)::: UI source</span></span>

<span data-ttu-id="cef55-271">UI の完全な制御を維持するには、scaffolder を実行し、 :::no-loc(Identity)::: :::no-loc(Identity)::: [ **すべてのファイルを上書き** する] を選択します。</span><span class="sxs-lookup"><span data-stu-id="cef55-271">To maintain full control of the :::no-loc(Identity)::: UI, run the :::no-loc(Identity)::: scaffolder and select **Override all files** .</span></span>

<span data-ttu-id="cef55-272">次の強調表示されたコードは、 :::no-loc(Identity)::: ASP.NET Core 2.1 web アプリで既定の UI をに置き換える変更を示して :::no-loc(Identity)::: います。</span><span class="sxs-lookup"><span data-stu-id="cef55-272">The following highlighted code shows the changes to replace the default :::no-loc(Identity)::: UI with :::no-loc(Identity)::: in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="cef55-273">この操作を行うと、UI を完全に制御でき :::no-loc(Identity)::: ます。</span><span class="sxs-lookup"><span data-stu-id="cef55-273">You might want to do this to have full control of the :::no-loc(Identity)::: UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="cef55-274">既定値 :::no-loc(Identity)::: は、次のコードで置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="cef55-274">The default :::no-loc(Identity)::: is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="cef55-275">次のコードでは、 [Loginpath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.loginpath)、 [logoutpath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.logoutpath)、および [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.accessdeniedpath)を設定しています。</span><span class="sxs-lookup"><span data-stu-id="cef55-275">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="cef55-276">`IEmailSender`の実装を登録します。次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="cef55-276">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->

## <a name="password-configuration"></a><span data-ttu-id="cef55-277">パスワードの構成</span><span class="sxs-lookup"><span data-stu-id="cef55-277">Password configuration</span></span>

<span data-ttu-id="cef55-278">がで構成されている場合は <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PasswordOptions> `Startup.ConfigureServices` 、スキャフォールディング pages のプロパティに[ `[StringLength]` 属性](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute)の構成が必要になることがあり `Password` :::no-loc(Identity)::: ます。</span><span class="sxs-lookup"><span data-stu-id="cef55-278">If <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PasswordOptions> are configured in `Startup.ConfigureServices`, [`[StringLength]` attribute](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) configuration might be required for the `Password` property in scaffolded :::no-loc(Identity)::: pages.</span></span> <span data-ttu-id="cef55-279">`InputModel``Password`プロパティは次のファイルにあります。</span><span class="sxs-lookup"><span data-stu-id="cef55-279">`InputModel` `Password` properties are found in the following files:</span></span>

* `Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs`
* `Areas/:::no-loc(Identity):::/Pages/Account/ResetPassword.cshtml.cs`

## <a name="disable-register-page"></a><span data-ttu-id="cef55-280">登録の無効化ページ</span><span class="sxs-lookup"><span data-stu-id="cef55-280">Disable register page</span></span>

<span data-ttu-id="cef55-281">ユーザー登録を無効にするには:</span><span class="sxs-lookup"><span data-stu-id="cef55-281">To disable user registration:</span></span>

* <span data-ttu-id="cef55-282">スキャフォールディング :::no-loc(Identity)::: 。</span><span class="sxs-lookup"><span data-stu-id="cef55-282">Scaffold :::no-loc(Identity):::.</span></span> <span data-ttu-id="cef55-283">Account、Account. Login、および Account. RegisterConfirmation を含めます。</span><span class="sxs-lookup"><span data-stu-id="cef55-283">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="cef55-284">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="cef55-284">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="cef55-285">ユーザーがこのエンドポイントから登録できないように、 *区分/ :::no-loc(Identity)::: /Pages/Account/Register.cshtml.cs* を更新します。</span><span class="sxs-lookup"><span data-stu-id="cef55-285">Update *Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="cef55-286">次の変更との整合性を保つために、 *区分/////また :::no-loc(Identity):::* はを更新します。</span><span class="sxs-lookup"><span data-stu-id="cef55-286">Update *Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="cef55-287">*区分///////// :::no-loc(Identity)::: ////* から登録リンクをコメントアウトまたは削除します。</span><span class="sxs-lookup"><span data-stu-id="cef55-287">Comment out or remove the registration link from *Areas/:::no-loc(Identity):::/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="cef55-288">[ *区分/ページ/ :::no-loc(Identity)::: アカウント/* 登録の確認] ページを更新します。</span><span class="sxs-lookup"><span data-stu-id="cef55-288">Update the *Areas/:::no-loc(Identity):::/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="cef55-289">コードとリンクを cshtml ファイルから削除します。</span><span class="sxs-lookup"><span data-stu-id="cef55-289">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="cef55-290">から確認コードを削除し `PageModel` ます。</span><span class="sxs-lookup"><span data-stu-id="cef55-290">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="cef55-291">別のアプリを使用してユーザーを追加する</span><span class="sxs-lookup"><span data-stu-id="cef55-291">Use another app to add users</span></span>

<span data-ttu-id="cef55-292">Web アプリの外部にユーザーを追加するためのメカニズムを提供します。</span><span class="sxs-lookup"><span data-stu-id="cef55-292">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="cef55-293">ユーザーを追加するためのオプションは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="cef55-293">Options to add users include:</span></span>

* <span data-ttu-id="cef55-294">専用管理者 web アプリ。</span><span class="sxs-lookup"><span data-stu-id="cef55-294">A dedicated admin web app.</span></span>
* <span data-ttu-id="cef55-295">コンソールアプリ。</span><span class="sxs-lookup"><span data-stu-id="cef55-295">A console app.</span></span>

<span data-ttu-id="cef55-296">次のコードは、ユーザーを追加する方法の概要を示しています。</span><span class="sxs-lookup"><span data-stu-id="cef55-296">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="cef55-297">ユーザーの一覧がメモリに読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="cef55-297">A list of users is read into memory.</span></span>
* <span data-ttu-id="cef55-298">ユーザーごとに一意の強力なパスワードが生成されます。</span><span class="sxs-lookup"><span data-stu-id="cef55-298">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="cef55-299">ユーザーがデータベースに追加され :::no-loc(Identity)::: ます。</span><span class="sxs-lookup"><span data-stu-id="cef55-299">The user is added to the :::no-loc(Identity)::: database.</span></span>
* <span data-ttu-id="cef55-300">ユーザーに通知され、パスワードを変更するように指示されます。</span><span class="sxs-lookup"><span data-stu-id="cef55-300">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="cef55-301">次のコードは、ユーザーの追加の概要を示しています。</span><span class="sxs-lookup"><span data-stu-id="cef55-301">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="cef55-302">同様の方法で、運用環境のシナリオにも対応できます。</span><span class="sxs-lookup"><span data-stu-id="cef55-302">A similar approach can be followed for production scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cef55-303">その他の資料</span><span class="sxs-lookup"><span data-stu-id="cef55-303">Additional resources</span></span>

* [<span data-ttu-id="cef55-304">ASP.NET Core 2.1 以降に認証コードが変更された</span><span class="sxs-lookup"><span data-stu-id="cef55-304">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end
