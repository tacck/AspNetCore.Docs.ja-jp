---
title: ASP.NET Core Identityプロジェクトでのスキャフォールディング
author: rick-anderson
description: ASP.NET Core のプロジェクトでIdentityスキャフォールディングする方法について説明します。
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
ms.openlocfilehash: 6f1ff69863e14c73e90496ea61188387f5267b19
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82768391"
---
# <a name="scaffold-identity-in-aspnet-core-projects"></a><span data-ttu-id="2df2f-103">ASP.NET Core Identityプロジェクトでのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="2df2f-103">Scaffold Identity in ASP.NET Core projects</span></span>

<span data-ttu-id="2df2f-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="2df2f-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2df2f-105">ASP.NET Core は、 [ Razorクラスライブラリ](xref:razor-pages/ui-class)として[ASP.NET Core Identity ](xref:security/authentication/identity)を提供します。</span><span class="sxs-lookup"><span data-stu-id="2df2f-105">ASP.NET Core provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="2df2f-106">を含むIdentityアプリケーションでは、scaffolder を適用して、 Identity Razorクラスライブラリ (rcl) に含まれているソースコードを選択的に追加できます。</span><span class="sxs-lookup"><span data-stu-id="2df2f-106">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="2df2f-107">コードを変更して動作を変更できるように、ソース コードを生成できます。</span><span class="sxs-lookup"><span data-stu-id="2df2f-107">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="2df2f-108">たとえば、登録で使用するコードを生成するようにスキャフォルダーに指示できます。</span><span class="sxs-lookup"><span data-stu-id="2df2f-108">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="2df2f-109">生成されたコードは、 Identity rcl の同じコードよりも優先されます。</span><span class="sxs-lookup"><span data-stu-id="2df2f-109">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="2df2f-110">UI を完全に制御し、既定の RCL を使用しないようにするには、「[完全な ID UI ソースの作成](#full)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="2df2f-110">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="2df2f-111">認証を含ま**ない**アプリケーションでは、scaffolder を適用して rcl Identityパッケージを追加できます。</span><span class="sxs-lookup"><span data-stu-id="2df2f-111">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="2df2f-112">生成するコードを選択Identityすることもできます。</span><span class="sxs-lookup"><span data-stu-id="2df2f-112">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="2df2f-113">Scaffolder は、必要なコードの大部分を生成しますが、プロセスを完了するにはプロジェクトを更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="2df2f-113">Although the scaffolder generates most of the necessary code, you need to update your project to complete the process.</span></span> <span data-ttu-id="2df2f-114">このドキュメントでは、 Identityスキャフォールディングの更新を完了するために必要な手順について説明します。</span><span class="sxs-lookup"><span data-stu-id="2df2f-114">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="2df2f-115">ファイルの違いを示すソース管理システムを使用して、変更を元に戻すことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="2df2f-115">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="2df2f-116">Scaffolder をIdentity実行した後に変更を確認します。</span><span class="sxs-lookup"><span data-stu-id="2df2f-116">Inspect the changes after running the Identity scaffolder.</span></span>

<span data-ttu-id="2df2f-117">サービスは、 [2 要素認証](xref:security/authentication/identity-enable-qrcodes)、アカウントの[確認とパスワードの回復](xref:security/authentication/accconfirm)、およびの他のセキュリティIdentity機能を使用する場合に必要です。</span><span class="sxs-lookup"><span data-stu-id="2df2f-117">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="2df2f-118">サービスまたはサービススタブは、スキャフォールディングIdentity時に生成されません。</span><span class="sxs-lookup"><span data-stu-id="2df2f-118">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="2df2f-119">これらの機能を有効にするサービスは、手動で追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="2df2f-119">Services to enable these features must be added manually.</span></span> <span data-ttu-id="2df2f-120">たとえば、「[電子メールの確認を要求する](xref:security/authentication/accconfirm#require-email-confirmation)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2df2f-120">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

<span data-ttu-id="2df2f-121">新しいデータIdentityコンテキストを使用して、既存の個別のアカウントを持つプロジェクトにスキャフォールディングする場合:</span><span class="sxs-lookup"><span data-stu-id="2df2f-121">When scaffolding Identity with a new data context into a project with existing individual accounts:</span></span>

* <span data-ttu-id="2df2f-122">で`Startup.ConfigureServices`、の呼び出しを削除します。</span><span class="sxs-lookup"><span data-stu-id="2df2f-122">In `Startup.ConfigureServices`, remove the calls to:</span></span>
  * `AddDbContext`
  * `AddDefaultIdentity`

<span data-ttu-id="2df2f-123">たとえば、 `AddDbContext`と`AddDefaultIdentity`は次のコードでコメントアウトされます。</span><span class="sxs-lookup"><span data-stu-id="2df2f-123">For example, `AddDbContext` and `AddDefaultIdentity` are commented out in the following code:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRemove.cs?name=snippet)]

<span data-ttu-id="2df2f-124">上記のコードは、*区分/Identity/IdentityHostingStartup.cs*で重複するコードをコメントアウトします。</span><span class="sxs-lookup"><span data-stu-id="2df2f-124">The preceeding code comments out the code that is duplicated in *Areas/Identity/IdentityHostingStartup.cs*</span></span>

<span data-ttu-id="2df2f-125">通常、個別のアカウントで作成されたアプリは、新しいデータコンテキストを作成し***ない***ようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="2df2f-125">Typically, apps that were created with individual accounts should ***not*** create a new data context.</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="2df2f-126">Id を空のプロジェクトにスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="2df2f-126">Scaffold identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="2df2f-127">次の`Startup`ようなコードでクラスを更新します。</span><span class="sxs-lookup"><span data-stu-id="2df2f-127">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="2df2f-128">既存のRazor承認なしで id をプロジェクトにスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="2df2f-128">Scaffold identity into a Razor project without existing authorization</span></span>

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

Identity<span data-ttu-id="2df2f-129">は、 *Areas/Identity/IdentityHostingStartup.cs*で構成されます。</span><span class="sxs-lookup"><span data-stu-id="2df2f-129"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="2df2f-130">詳細については、「 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2df2f-130">for more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="2df2f-131">移行、UseAuthentication、およびレイアウト</span><span class="sxs-lookup"><span data-stu-id="2df2f-131">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="2df2f-132">認証を有効にする</span><span class="sxs-lookup"><span data-stu-id="2df2f-132">Enable authentication</span></span>

<span data-ttu-id="2df2f-133">次の`Startup`ようなコードでクラスを更新します。</span><span class="sxs-lookup"><span data-stu-id="2df2f-133">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="2df2f-134">レイアウトの変更</span><span class="sxs-lookup"><span data-stu-id="2df2f-134">Layout changes</span></span>

<span data-ttu-id="2df2f-135">省略可能: 次のように`_LoginPartial`、ログイン部分 () をレイアウトファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="2df2f-135">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[Main](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="2df2f-136">承認を使用しRazorて id をプロジェクトにスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="2df2f-136">Scaffold identity into a Razor project with authorization</span></span>

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
<span data-ttu-id="2df2f-137">一部Identityのオプションは、 *Areas/Identity/IdentityHostingStartup.cs*で構成されています。</span><span class="sxs-lookup"><span data-stu-id="2df2f-137">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="2df2f-138">詳細については、「 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2df2f-138">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="2df2f-139">既存の承認なしでスキャフォールディング identity を MVC プロジェクトに</span><span class="sxs-lookup"><span data-stu-id="2df2f-139">Scaffold identity into an MVC project without existing authorization</span></span>

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

<span data-ttu-id="2df2f-140">省略可能:`_LoginPartial` *Views/Shared/_Layout cshtml*ファイルにログイン部分 () を追加します。</span><span class="sxs-lookup"><span data-stu-id="2df2f-140">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[Main](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* <span data-ttu-id="2df2f-141">*Pages/shared/_LoginPartial cshtml*ファイルを*Views/shared/_LoginPartial*に移動します。</span><span class="sxs-lookup"><span data-stu-id="2df2f-141">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

Identity<span data-ttu-id="2df2f-142">は、 *Areas/Identity/IdentityHostingStartup.cs*で構成されます。</span><span class="sxs-lookup"><span data-stu-id="2df2f-142"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="2df2f-143">詳細については、「IHostingStartup」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2df2f-143">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="2df2f-144">次の`Startup`ようなコードでクラスを更新します。</span><span class="sxs-lookup"><span data-stu-id="2df2f-144">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="2df2f-145">承認を使用して id を MVC プロジェクトにスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="2df2f-145">Scaffold identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="2df2f-146">完全な id UI ソースの作成</span><span class="sxs-lookup"><span data-stu-id="2df2f-146">Create full identity UI source</span></span>

<span data-ttu-id="2df2f-147">Identity UI の完全な制御を維持するにはIdentity 、scaffolder を実行し、[すべてのファイルを**上書き**する] を選択します。</span><span class="sxs-lookup"><span data-stu-id="2df2f-147">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="2df2f-148">次の強調表示されたコードは、ASP.NET Core Identity 2.1 web Identityアプリで既定の UI をに置き換える変更を示しています。</span><span class="sxs-lookup"><span data-stu-id="2df2f-148">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="2df2f-149">この操作を行うと、 Identity UI を完全に制御できます。</span><span class="sxs-lookup"><span data-stu-id="2df2f-149">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="2df2f-150">既定値Identityは、次のコードで置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="2df2f-150">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="2df2f-151">次のコードでは、 [Loginpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath)、 [logoutpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)、および[AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath)を設定しています。</span><span class="sxs-lookup"><span data-stu-id="2df2f-151">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="2df2f-152">の`IEmailSender`実装を登録します。次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="2df2f-152">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a><span data-ttu-id="2df2f-153">登録の無効化ページ</span><span class="sxs-lookup"><span data-stu-id="2df2f-153">Disable register page</span></span>

<span data-ttu-id="2df2f-154">ユーザー登録を無効にするには:</span><span class="sxs-lookup"><span data-stu-id="2df2f-154">To disable user registration:</span></span>

* <span data-ttu-id="2df2f-155">スキャフォールディングIdentity。</span><span class="sxs-lookup"><span data-stu-id="2df2f-155">Scaffold Identity.</span></span> <span data-ttu-id="2df2f-156">Account、Account. Login、および Account. RegisterConfirmation を含めます。</span><span class="sxs-lookup"><span data-stu-id="2df2f-156">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="2df2f-157">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="2df2f-157">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="2df2f-158">ユーザーがこのエンドポイントから登録できないように、*区分/Identity/Pages/Account/Register.cshtml.cs*を更新します。</span><span class="sxs-lookup"><span data-stu-id="2df2f-158">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="2df2f-159">次の変更との整合性を保つために、*区分/////またIdentity* はを更新します。</span><span class="sxs-lookup"><span data-stu-id="2df2f-159">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="2df2f-160">*区分/////////Identity////* から登録リンクをコメントアウトまたは削除します。</span><span class="sxs-lookup"><span data-stu-id="2df2f-160">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="2df2f-161">[*区分/ページIdentity/アカウント/* 登録の確認] ページを更新します。</span><span class="sxs-lookup"><span data-stu-id="2df2f-161">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="2df2f-162">コードとリンクを cshtml ファイルから削除します。</span><span class="sxs-lookup"><span data-stu-id="2df2f-162">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="2df2f-163">から確認コードを削除し`PageModel`ます。</span><span class="sxs-lookup"><span data-stu-id="2df2f-163">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="2df2f-164">別のアプリを使用してユーザーを追加する</span><span class="sxs-lookup"><span data-stu-id="2df2f-164">Use another app to add users</span></span>

<span data-ttu-id="2df2f-165">Web アプリの外部にユーザーを追加するためのメカニズムを提供します。</span><span class="sxs-lookup"><span data-stu-id="2df2f-165">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="2df2f-166">ユーザーを追加するためのオプションは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="2df2f-166">Options to add users include:</span></span>

* <span data-ttu-id="2df2f-167">専用管理者 web アプリ。</span><span class="sxs-lookup"><span data-stu-id="2df2f-167">A dedicated admin web app.</span></span>
* <span data-ttu-id="2df2f-168">コンソールアプリ。</span><span class="sxs-lookup"><span data-stu-id="2df2f-168">A console app.</span></span>

<span data-ttu-id="2df2f-169">次のコードは、ユーザーを追加する方法の概要を示しています。</span><span class="sxs-lookup"><span data-stu-id="2df2f-169">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="2df2f-170">ユーザーの一覧がメモリに読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="2df2f-170">A list of users is read into memory.</span></span>
* <span data-ttu-id="2df2f-171">ユーザーごとに一意の強力なパスワードが生成されます。</span><span class="sxs-lookup"><span data-stu-id="2df2f-171">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="2df2f-172">ユーザーがIdentityデータベースに追加されます。</span><span class="sxs-lookup"><span data-stu-id="2df2f-172">The user is added to the Identity database.</span></span>
* <span data-ttu-id="2df2f-173">ユーザーに通知され、パスワードを変更するように指示されます。</span><span class="sxs-lookup"><span data-stu-id="2df2f-173">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="2df2f-174">次のコードは、ユーザーの追加の概要を示しています。</span><span class="sxs-lookup"><span data-stu-id="2df2f-174">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="2df2f-175">同様の方法で、運用環境のシナリオにも対応できます。</span><span class="sxs-lookup"><span data-stu-id="2df2f-175">A similar approach can be followed for production scenarios.</span></span>

## <a name="prevent-publish-of-static-identity-assets"></a><span data-ttu-id="2df2f-176">静的Identityアセットの発行を禁止する</span><span class="sxs-lookup"><span data-stu-id="2df2f-176">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="2df2f-177">静的Identityなアセットが web ルートに発行され<xref:security/authentication/identity#prevent-publish-of-static-identity-assets>ないようにするには、「」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2df2f-177">To prevent publishing static Identity assets to the web root, see <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2df2f-178">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="2df2f-178">Additional resources</span></span>

* [<span data-ttu-id="2df2f-179">ASP.NET Core 2.1 以降に認証コードが変更された</span><span class="sxs-lookup"><span data-stu-id="2df2f-179">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2df2f-180">ASP.NET Core 2.1 以降では、 [ Razorクラスライブラリ](xref:razor-pages/ui-class)として[ASP.NET Core Identity ](xref:security/authentication/identity)が提供されます。</span><span class="sxs-lookup"><span data-stu-id="2df2f-180">ASP.NET Core 2.1 and later provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="2df2f-181">を含むIdentityアプリケーションでは、scaffolder を適用して、 Identity Razorクラスライブラリ (rcl) に含まれているソースコードを選択的に追加できます。</span><span class="sxs-lookup"><span data-stu-id="2df2f-181">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="2df2f-182">コードを変更して動作を変更できるように、ソース コードを生成できます。</span><span class="sxs-lookup"><span data-stu-id="2df2f-182">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="2df2f-183">たとえば、登録で使用するコードを生成するようにスキャフォルダーに指示できます。</span><span class="sxs-lookup"><span data-stu-id="2df2f-183">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="2df2f-184">生成されたコードは、 Identity rcl の同じコードよりも優先されます。</span><span class="sxs-lookup"><span data-stu-id="2df2f-184">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="2df2f-185">UI を完全に制御し、既定の RCL を使用しないようにするには、「[完全な ID UI ソースの作成](#full)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="2df2f-185">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="2df2f-186">認証を含ま**ない**アプリケーションでは、scaffolder を適用して rcl Identityパッケージを追加できます。</span><span class="sxs-lookup"><span data-stu-id="2df2f-186">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="2df2f-187">生成するコードを選択Identityすることもできます。</span><span class="sxs-lookup"><span data-stu-id="2df2f-187">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="2df2f-188">Scaffolder は、必要なコードの大部分を生成しますが、プロセスを完了するにはプロジェクトを更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="2df2f-188">Although the scaffolder generates most of the necessary code, you'll have to update your project to complete the process.</span></span> <span data-ttu-id="2df2f-189">このドキュメントでは、 Identityスキャフォールディングの更新を完了するために必要な手順について説明します。</span><span class="sxs-lookup"><span data-stu-id="2df2f-189">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="2df2f-190">Identity Scaffolder を実行すると、 *ScaffoldingReadme*ファイルがプロジェクトディレクトリに作成されます。</span><span class="sxs-lookup"><span data-stu-id="2df2f-190">When the Identity scaffolder is run, a *ScaffoldingReadme.txt* file is created in the project directory.</span></span> <span data-ttu-id="2df2f-191">*ScaffoldingReadme*ファイルには、 Identityスキャフォールディングの更新を完了するために必要な手順に関する一般的な指示が含まれています。</span><span class="sxs-lookup"><span data-stu-id="2df2f-191">The *ScaffoldingReadme.txt* file contains general instructions on what's needed to complete the Identity scaffolding update.</span></span> <span data-ttu-id="2df2f-192">このドキュメントには、 *ScaffoldingReadme*ファイルよりも完全な手順が含まれています。</span><span class="sxs-lookup"><span data-stu-id="2df2f-192">This document contains more complete instructions than the *ScaffoldingReadme.txt* file.</span></span>

<span data-ttu-id="2df2f-193">ファイルの違いを示すソース管理システムを使用して、変更を元に戻すことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="2df2f-193">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="2df2f-194">Scaffolder をIdentity実行した後に変更を確認します。</span><span class="sxs-lookup"><span data-stu-id="2df2f-194">Inspect the changes after running the Identity scaffolder.</span></span>

> [!NOTE]
> <span data-ttu-id="2df2f-195">サービスは、 [2 要素認証](xref:security/authentication/identity-enable-qrcodes)、アカウントの[確認とパスワードの回復](xref:security/authentication/accconfirm)、およびの他のセキュリティIdentity機能を使用する場合に必要です。</span><span class="sxs-lookup"><span data-stu-id="2df2f-195">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="2df2f-196">サービスまたはサービススタブは、スキャフォールディングIdentity時に生成されません。</span><span class="sxs-lookup"><span data-stu-id="2df2f-196">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="2df2f-197">これらの機能を有効にするサービスは、手動で追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="2df2f-197">Services to enable these features must be added manually.</span></span> <span data-ttu-id="2df2f-198">たとえば、「[電子メールの確認を要求する](xref:security/authentication/accconfirm#require-email-confirmation)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2df2f-198">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="2df2f-199">Id を空のプロジェクトにスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="2df2f-199">Scaffold identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="2df2f-200">次の強調表示された`Startup`呼び出しをクラスに追加します。</span><span class="sxs-lookup"><span data-stu-id="2df2f-200">Add the following highlighted calls to the `Startup` class:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="2df2f-201">既存のRazor承認なしで id をプロジェクトにスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="2df2f-201">Scaffold identity into a Razor project without existing authorization</span></span>

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

Identity<span data-ttu-id="2df2f-202">は、 *Areas/Identity/IdentityHostingStartup.cs*で構成されます。</span><span class="sxs-lookup"><span data-stu-id="2df2f-202"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="2df2f-203">詳細については、「 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2df2f-203">for more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="2df2f-204">移行、UseAuthentication、およびレイアウト</span><span class="sxs-lookup"><span data-stu-id="2df2f-204">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="2df2f-205">認証を有効にする</span><span class="sxs-lookup"><span data-stu-id="2df2f-205">Enable authentication</span></span>

<span data-ttu-id="2df2f-206">クラスの`Configure`メソッドで、 `UseStaticFiles`次のように[useauthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)を呼び出します。 `Startup`</span><span class="sxs-lookup"><span data-stu-id="2df2f-206">In the `Configure` method of the `Startup` class, call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="2df2f-207">レイアウトの変更</span><span class="sxs-lookup"><span data-stu-id="2df2f-207">Layout changes</span></span>

<span data-ttu-id="2df2f-208">省略可能: 次のように`_LoginPartial`、ログイン部分 () をレイアウトファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="2df2f-208">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[Main](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="2df2f-209">承認を使用しRazorて id をプロジェクトにスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="2df2f-209">Scaffold identity into a Razor project with authorization</span></span>

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
<span data-ttu-id="2df2f-210">一部Identityのオプションは、 *Areas/Identity/IdentityHostingStartup.cs*で構成されています。</span><span class="sxs-lookup"><span data-stu-id="2df2f-210">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="2df2f-211">詳細については、「 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2df2f-211">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="2df2f-212">既存の承認なしでスキャフォールディング identity を MVC プロジェクトに</span><span class="sxs-lookup"><span data-stu-id="2df2f-212">Scaffold identity into an MVC project without existing authorization</span></span>

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

<span data-ttu-id="2df2f-213">省略可能:`_LoginPartial` *Views/Shared/_Layout cshtml*ファイルにログイン部分 () を追加します。</span><span class="sxs-lookup"><span data-stu-id="2df2f-213">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* <span data-ttu-id="2df2f-214">*Pages/shared/_LoginPartial cshtml*ファイルを*Views/shared/_LoginPartial*に移動します。</span><span class="sxs-lookup"><span data-stu-id="2df2f-214">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

Identity<span data-ttu-id="2df2f-215">は、 *Areas/Identity/IdentityHostingStartup.cs*で構成されます。</span><span class="sxs-lookup"><span data-stu-id="2df2f-215"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="2df2f-216">詳細については、「IHostingStartup」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2df2f-216">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="2df2f-217">次の後`UseStaticFiles`に[useauthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="2df2f-217">Call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="2df2f-218">承認を使用して id を MVC プロジェクトにスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="2df2f-218">Scaffold identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="2df2f-219">*ページ/共有*フォルダーとそのフォルダー内のファイルを削除します。</span><span class="sxs-lookup"><span data-stu-id="2df2f-219">Delete the *Pages/Shared* folder and the files in that folder.</span></span>

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="2df2f-220">完全な id UI ソースの作成</span><span class="sxs-lookup"><span data-stu-id="2df2f-220">Create full identity UI source</span></span>

<span data-ttu-id="2df2f-221">Identity UI の完全な制御を維持するにはIdentity 、scaffolder を実行し、[すべてのファイルを**上書き**する] を選択します。</span><span class="sxs-lookup"><span data-stu-id="2df2f-221">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="2df2f-222">次の強調表示されたコードは、ASP.NET Core Identity 2.1 web Identityアプリで既定の UI をに置き換える変更を示しています。</span><span class="sxs-lookup"><span data-stu-id="2df2f-222">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="2df2f-223">この操作を行うと、 Identity UI を完全に制御できます。</span><span class="sxs-lookup"><span data-stu-id="2df2f-223">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="2df2f-224">既定値Identityは、次のコードで置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="2df2f-224">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="2df2f-225">次のコードでは、 [Loginpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath)、 [logoutpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)、および[AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath)を設定しています。</span><span class="sxs-lookup"><span data-stu-id="2df2f-225">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="2df2f-226">の`IEmailSender`実装を登録します。次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="2df2f-226">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a><span data-ttu-id="2df2f-227">登録の無効化ページ</span><span class="sxs-lookup"><span data-stu-id="2df2f-227">Disable register page</span></span>

<span data-ttu-id="2df2f-228">ユーザー登録を無効にするには:</span><span class="sxs-lookup"><span data-stu-id="2df2f-228">To disable user registration:</span></span>

* <span data-ttu-id="2df2f-229">スキャフォールディングIdentity。</span><span class="sxs-lookup"><span data-stu-id="2df2f-229">Scaffold Identity.</span></span> <span data-ttu-id="2df2f-230">Account、Account. Login、および Account. RegisterConfirmation を含めます。</span><span class="sxs-lookup"><span data-stu-id="2df2f-230">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="2df2f-231">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="2df2f-231">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="2df2f-232">ユーザーがこのエンドポイントから登録できないように、*区分/Identity/Pages/Account/Register.cshtml.cs*を更新します。</span><span class="sxs-lookup"><span data-stu-id="2df2f-232">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="2df2f-233">次の変更との整合性を保つために、*区分/////またIdentity* はを更新します。</span><span class="sxs-lookup"><span data-stu-id="2df2f-233">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="2df2f-234">*区分/////////Identity////* から登録リンクをコメントアウトまたは削除します。</span><span class="sxs-lookup"><span data-stu-id="2df2f-234">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="2df2f-235">[*区分/ページIdentity/アカウント/* 登録の確認] ページを更新します。</span><span class="sxs-lookup"><span data-stu-id="2df2f-235">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="2df2f-236">コードとリンクを cshtml ファイルから削除します。</span><span class="sxs-lookup"><span data-stu-id="2df2f-236">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="2df2f-237">から確認コードを削除し`PageModel`ます。</span><span class="sxs-lookup"><span data-stu-id="2df2f-237">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="2df2f-238">別のアプリを使用してユーザーを追加する</span><span class="sxs-lookup"><span data-stu-id="2df2f-238">Use another app to add users</span></span>

<span data-ttu-id="2df2f-239">Web アプリの外部にユーザーを追加するためのメカニズムを提供します。</span><span class="sxs-lookup"><span data-stu-id="2df2f-239">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="2df2f-240">ユーザーを追加するためのオプションは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="2df2f-240">Options to add users include:</span></span>

* <span data-ttu-id="2df2f-241">専用管理者 web アプリ。</span><span class="sxs-lookup"><span data-stu-id="2df2f-241">A dedicated admin web app.</span></span>
* <span data-ttu-id="2df2f-242">コンソールアプリ。</span><span class="sxs-lookup"><span data-stu-id="2df2f-242">A console app.</span></span>

<span data-ttu-id="2df2f-243">次のコードは、ユーザーを追加する方法の概要を示しています。</span><span class="sxs-lookup"><span data-stu-id="2df2f-243">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="2df2f-244">ユーザーの一覧がメモリに読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="2df2f-244">A list of users is read into memory.</span></span>
* <span data-ttu-id="2df2f-245">ユーザーごとに一意の強力なパスワードが生成されます。</span><span class="sxs-lookup"><span data-stu-id="2df2f-245">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="2df2f-246">ユーザーがIdentityデータベースに追加されます。</span><span class="sxs-lookup"><span data-stu-id="2df2f-246">The user is added to the Identity database.</span></span>
* <span data-ttu-id="2df2f-247">ユーザーに通知され、パスワードを変更するように指示されます。</span><span class="sxs-lookup"><span data-stu-id="2df2f-247">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="2df2f-248">次のコードは、ユーザーの追加の概要を示しています。</span><span class="sxs-lookup"><span data-stu-id="2df2f-248">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="2df2f-249">同様の方法で、運用環境のシナリオにも対応できます。</span><span class="sxs-lookup"><span data-stu-id="2df2f-249">A similar approach can be followed for production scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2df2f-250">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="2df2f-250">Additional resources</span></span>

* [<span data-ttu-id="2df2f-251">ASP.NET Core 2.1 以降に認証コードが変更された</span><span class="sxs-lookup"><span data-stu-id="2df2f-251">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end