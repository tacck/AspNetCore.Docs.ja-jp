---
title: ASP.NET Core プロジェクト内のスキャフォールディング Id
author: rick-anderson
description: ASP.NET Core プロジェクトで Id をスキャフォールディングする方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/1/2020
uid: security/authentication/scaffold-identity
ms.openlocfilehash: ac95035b114274ddaa6ccb0b5b6e3da98885e39e
ms.sourcegitcommit: 6318d2bdd63116e178c34492a904be85ec9ac108
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/30/2020
ms.locfileid: "82604728"
---
# <a name="scaffold-identity-in-aspnet-core-projects"></a><span data-ttu-id="a5067-103">ASP.NET Core プロジェクト内のスキャフォールディング Id</span><span class="sxs-lookup"><span data-stu-id="a5067-103">Scaffold Identity in ASP.NET Core projects</span></span>

<span data-ttu-id="a5067-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="a5067-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a5067-105">ASP.NET Core は、 [ASP.NET Core id](xref:security/authentication/identity)を[Razor クラスライブラリ](xref:razor-pages/ui-class)として提供します。</span><span class="sxs-lookup"><span data-stu-id="a5067-105">ASP.NET Core provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="a5067-106">Id を含むアプリケーションでは、scaffolder を適用して、Id Razor クラスライブラリ (RCL) に含まれているソースコードを選択的に追加することができます。</span><span class="sxs-lookup"><span data-stu-id="a5067-106">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="a5067-107">コードを変更して動作を変更できるように、ソース コードを生成できます。</span><span class="sxs-lookup"><span data-stu-id="a5067-107">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="a5067-108">たとえば、登録で使用するコードを生成するようにスキャフォルダーに指示できます。</span><span class="sxs-lookup"><span data-stu-id="a5067-108">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="a5067-109">生成されたコードは、Identity RCL の同じコードよりも優先されます。</span><span class="sxs-lookup"><span data-stu-id="a5067-109">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="a5067-110">UI を完全に制御し、既定の RCL を使用しないようにするには、「[完全な ID UI ソースの作成](#full)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="a5067-110">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="a5067-111">認証を含ま**ない**アプリケーションでは、scaffolder を適用して Rcl id パッケージを追加できます。</span><span class="sxs-lookup"><span data-stu-id="a5067-111">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="a5067-112">生成される Identity コードの選択オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="a5067-112">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="a5067-113">Scaffolder は、必要なコードの大部分を生成しますが、プロセスを完了するにはプロジェクトを更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a5067-113">Although the scaffolder generates most of the necessary code, you need to update your project to complete the process.</span></span> <span data-ttu-id="a5067-114">このドキュメントでは、Id スキャフォールディングの更新を完了するために必要な手順について説明します。</span><span class="sxs-lookup"><span data-stu-id="a5067-114">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="a5067-115">ファイルの違いを示すソース管理システムを使用して、変更を元に戻すことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="a5067-115">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="a5067-116">Id scaffolder を実行した後に変更を確認します。</span><span class="sxs-lookup"><span data-stu-id="a5067-116">Inspect the changes after running the Identity scaffolder.</span></span>

<span data-ttu-id="a5067-117">サービスは、 [2 要素認証](xref:security/authentication/identity-enable-qrcodes)、アカウントの[確認とパスワードの回復](xref:security/authentication/accconfirm)、および id を持つその他のセキュリティ機能を使用する場合に必要です。</span><span class="sxs-lookup"><span data-stu-id="a5067-117">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="a5067-118">サービスまたはサービススタブは、スキャフォールディング Id では生成されません。</span><span class="sxs-lookup"><span data-stu-id="a5067-118">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="a5067-119">これらの機能を有効にするサービスは、手動で追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a5067-119">Services to enable these features must be added manually.</span></span> <span data-ttu-id="a5067-120">たとえば、「[電子メールの確認を要求する](xref:security/authentication/accconfirm#require-email-confirmation)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a5067-120">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

<span data-ttu-id="a5067-121">新しいデータコンテキストで Id をスキャフォールディングし、既存の個別のアカウントを持つプロジェクトにする場合:</span><span class="sxs-lookup"><span data-stu-id="a5067-121">When scaffolding Identity with a new data context into a project with existing individual accounts:</span></span>

* <span data-ttu-id="a5067-122">で`Startup.ConfigureServices`、の呼び出しを削除します。</span><span class="sxs-lookup"><span data-stu-id="a5067-122">In `Startup.ConfigureServices`, remove the calls to:</span></span>
  * `AddDbContext`
  * `AddDefaultIdentity`

<span data-ttu-id="a5067-123">たとえば、 `AddDbContext`と`AddDefaultIdentity`は次のコードでコメントアウトされます。</span><span class="sxs-lookup"><span data-stu-id="a5067-123">For example, `AddDbContext` and `AddDefaultIdentity` are commented out in the following code:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRemove.cs?name=snippet)]

<span data-ttu-id="a5067-124">上記のコードは、*区分/id/IdentityHostingStartup*で重複しているコードをコメントアウトします。</span><span class="sxs-lookup"><span data-stu-id="a5067-124">The preceeding code comments out the code that is duplicated in *Areas/Identity/IdentityHostingStartup.cs*</span></span>

<span data-ttu-id="a5067-125">通常、個別のアカウントで作成されたアプリは、新しいデータコンテキストを作成し***ない***ようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="a5067-125">Typically, apps that were created with individual accounts should ***not*** create a new data context.</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="a5067-126">Id を空のプロジェクトにスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="a5067-126">Scaffold identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="a5067-127">次の`Startup`ようなコードでクラスを更新します。</span><span class="sxs-lookup"><span data-stu-id="a5067-127">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="a5067-128">既存の承認なしでスキャフォールディング id を Razor プロジェクトに</span><span class="sxs-lookup"><span data-stu-id="a5067-128">Scaffold identity into a Razor project without existing authorization</span></span>

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

<span data-ttu-id="a5067-129">Id は、 *Areas/identity/IdentityHostingStartup*で構成されます。</span><span class="sxs-lookup"><span data-stu-id="a5067-129">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="a5067-130">詳細については、「 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a5067-130">for more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="a5067-131">移行、UseAuthentication、およびレイアウト</span><span class="sxs-lookup"><span data-stu-id="a5067-131">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="a5067-132">認証を有効にする</span><span class="sxs-lookup"><span data-stu-id="a5067-132">Enable authentication</span></span>

<span data-ttu-id="a5067-133">次の`Startup`ようなコードでクラスを更新します。</span><span class="sxs-lookup"><span data-stu-id="a5067-133">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="a5067-134">レイアウトの変更</span><span class="sxs-lookup"><span data-stu-id="a5067-134">Layout changes</span></span>

<span data-ttu-id="a5067-135">省略可能: 次のように`_LoginPartial`、ログイン部分 () をレイアウトファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="a5067-135">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[Main](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="a5067-136">承認を使用して id を Razor プロジェクトにスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="a5067-136">Scaffold identity into a Razor project with authorization</span></span>

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
<span data-ttu-id="a5067-137">一部の Id オプションは、 *Areas/Identity/IdentityHostingStartup*で構成されています。</span><span class="sxs-lookup"><span data-stu-id="a5067-137">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="a5067-138">詳細については、「 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a5067-138">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="a5067-139">既存の承認なしでスキャフォールディング identity を MVC プロジェクトに</span><span class="sxs-lookup"><span data-stu-id="a5067-139">Scaffold identity into an MVC project without existing authorization</span></span>

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

<span data-ttu-id="a5067-140">省略可能:`_LoginPartial` *Views/Shared/_Layout cshtml*ファイルにログイン部分 () を追加します。</span><span class="sxs-lookup"><span data-stu-id="a5067-140">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[Main](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* <span data-ttu-id="a5067-141">*Pages/shared/_LoginPartial cshtml*ファイルを*Views/shared/_LoginPartial*に移動します。</span><span class="sxs-lookup"><span data-stu-id="a5067-141">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="a5067-142">Id は、 *Areas/identity/IdentityHostingStartup*で構成されます。</span><span class="sxs-lookup"><span data-stu-id="a5067-142">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="a5067-143">詳細については、「IHostingStartup」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a5067-143">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="a5067-144">次の`Startup`ようなコードでクラスを更新します。</span><span class="sxs-lookup"><span data-stu-id="a5067-144">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="a5067-145">承認を使用して id を MVC プロジェクトにスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="a5067-145">Scaffold identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="a5067-146">完全な id UI ソースの作成</span><span class="sxs-lookup"><span data-stu-id="a5067-146">Create full identity UI source</span></span>

<span data-ttu-id="a5067-147">Id UI の完全な制御を維持するには、Identity scaffolder を実行し、[**すべてのファイルを上書き**する] を選択します。</span><span class="sxs-lookup"><span data-stu-id="a5067-147">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="a5067-148">次の強調表示されたコードは、既定の Id UI を ASP.NET Core 2.1 web アプリの Id に置き換える変更を示しています。</span><span class="sxs-lookup"><span data-stu-id="a5067-148">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="a5067-149">これは、Id UI を完全に制御するために必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="a5067-149">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="a5067-150">既定の Id は、次のコードで置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="a5067-150">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="a5067-151">次のコードでは、 [Loginpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath)、 [logoutpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)、および[AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath)を設定しています。</span><span class="sxs-lookup"><span data-stu-id="a5067-151">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="a5067-152">の`IEmailSender`実装を登録します。次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="a5067-152">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a><span data-ttu-id="a5067-153">登録の無効化ページ</span><span class="sxs-lookup"><span data-stu-id="a5067-153">Disable register page</span></span>

<span data-ttu-id="a5067-154">ユーザー登録を無効にするには:</span><span class="sxs-lookup"><span data-stu-id="a5067-154">To disable user registration:</span></span>

* <span data-ttu-id="a5067-155">スキャフォールディング Id。</span><span class="sxs-lookup"><span data-stu-id="a5067-155">Scaffold Identity.</span></span> <span data-ttu-id="a5067-156">Account、Account. Login、および Account. RegisterConfirmation を含めます。</span><span class="sxs-lookup"><span data-stu-id="a5067-156">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="a5067-157">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="a5067-157">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="a5067-158">ユーザーがこのエンドポイントから登録できないように、*区分/id/ページ/アカウント/登録*を更新します。</span><span class="sxs-lookup"><span data-stu-id="a5067-158">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="a5067-159">前の変更との一貫性を保つために、*区分/id/ページ/アカウント/登録*を更新します。</span><span class="sxs-lookup"><span data-stu-id="a5067-159">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="a5067-160">*区分/id/ページ/アカウント/ログイン. cshtml*から登録リンクをコメントアウトするか削除します。</span><span class="sxs-lookup"><span data-stu-id="a5067-160">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="a5067-161">[*区分/id/ページ/アカウント/RegisterConfirmation 確認*] ページを更新します。</span><span class="sxs-lookup"><span data-stu-id="a5067-161">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="a5067-162">コードとリンクを cshtml ファイルから削除します。</span><span class="sxs-lookup"><span data-stu-id="a5067-162">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="a5067-163">から確認コードを削除し`PageModel`ます。</span><span class="sxs-lookup"><span data-stu-id="a5067-163">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="a5067-164">別のアプリを使用してユーザーを追加する</span><span class="sxs-lookup"><span data-stu-id="a5067-164">Use another app to add users</span></span>

<span data-ttu-id="a5067-165">Web アプリの外部にユーザーを追加するためのメカニズムを提供します。</span><span class="sxs-lookup"><span data-stu-id="a5067-165">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="a5067-166">ユーザーを追加するためのオプションは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="a5067-166">Options to add users include:</span></span>

* <span data-ttu-id="a5067-167">専用管理者 web アプリ。</span><span class="sxs-lookup"><span data-stu-id="a5067-167">A dedicated admin web app.</span></span>
* <span data-ttu-id="a5067-168">コンソールアプリ。</span><span class="sxs-lookup"><span data-stu-id="a5067-168">A console app.</span></span>

<span data-ttu-id="a5067-169">次のコードは、ユーザーを追加する方法の概要を示しています。</span><span class="sxs-lookup"><span data-stu-id="a5067-169">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="a5067-170">ユーザーの一覧がメモリに読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="a5067-170">A list of users is read into memory.</span></span>
* <span data-ttu-id="a5067-171">ユーザーごとに一意の強力なパスワードが生成されます。</span><span class="sxs-lookup"><span data-stu-id="a5067-171">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="a5067-172">ユーザーが Id データベースに追加されます。</span><span class="sxs-lookup"><span data-stu-id="a5067-172">The user is added to the Identity database.</span></span>
* <span data-ttu-id="a5067-173">ユーザーに通知され、パスワードを変更するように指示されます。</span><span class="sxs-lookup"><span data-stu-id="a5067-173">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="a5067-174">次のコードは、ユーザーの追加の概要を示しています。</span><span class="sxs-lookup"><span data-stu-id="a5067-174">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="a5067-175">同様の方法で、運用環境のシナリオにも対応できます。</span><span class="sxs-lookup"><span data-stu-id="a5067-175">A similar approach can be followed for production scenarios.</span></span>

## <a name="prevent-publish-of-static-identity-assets"></a><span data-ttu-id="a5067-176">静的 Id 資産の発行を禁止する</span><span class="sxs-lookup"><span data-stu-id="a5067-176">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="a5067-177">静的な ID 資産が Web ルートに公開されないようにするには、<xref:security/authentication/identity#prevent-publish-of-static-identity-assets> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a5067-177">To prevent publishing static Identity assets to the web root, see <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a5067-178">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="a5067-178">Additional resources</span></span>

* [<span data-ttu-id="a5067-179">ASP.NET Core 2.1 以降に認証コードが変更された</span><span class="sxs-lookup"><span data-stu-id="a5067-179">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a5067-180">ASP.NET Core 2.1 以降では、 [ASP.NET Core id](xref:security/authentication/identity)を[Razor クラスライブラリ](xref:razor-pages/ui-class)として提供します。</span><span class="sxs-lookup"><span data-stu-id="a5067-180">ASP.NET Core 2.1 and later provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="a5067-181">Id を含むアプリケーションでは、scaffolder を適用して、Id Razor クラスライブラリ (RCL) に含まれているソースコードを選択的に追加することができます。</span><span class="sxs-lookup"><span data-stu-id="a5067-181">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="a5067-182">コードを変更して動作を変更できるように、ソース コードを生成できます。</span><span class="sxs-lookup"><span data-stu-id="a5067-182">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="a5067-183">たとえば、登録で使用するコードを生成するようにスキャフォルダーに指示できます。</span><span class="sxs-lookup"><span data-stu-id="a5067-183">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="a5067-184">生成されたコードは、Identity RCL の同じコードよりも優先されます。</span><span class="sxs-lookup"><span data-stu-id="a5067-184">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="a5067-185">UI を完全に制御し、既定の RCL を使用しないようにするには、「[完全な ID UI ソースの作成](#full)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="a5067-185">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="a5067-186">認証を含ま**ない**アプリケーションでは、scaffolder を適用して Rcl id パッケージを追加できます。</span><span class="sxs-lookup"><span data-stu-id="a5067-186">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="a5067-187">生成される Identity コードの選択オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="a5067-187">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="a5067-188">Scaffolder は、必要なコードの大部分を生成しますが、プロセスを完了するにはプロジェクトを更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a5067-188">Although the scaffolder generates most of the necessary code, you'll have to update your project to complete the process.</span></span> <span data-ttu-id="a5067-189">このドキュメントでは、Id スキャフォールディングの更新を完了するために必要な手順について説明します。</span><span class="sxs-lookup"><span data-stu-id="a5067-189">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="a5067-190">Id scaffolder を実行すると、 *ScaffoldingReadme*ファイルがプロジェクトディレクトリに作成されます。</span><span class="sxs-lookup"><span data-stu-id="a5067-190">When the Identity scaffolder is run, a *ScaffoldingReadme.txt* file is created in the project directory.</span></span> <span data-ttu-id="a5067-191">*ScaffoldingReadme*ファイルには、id スキャフォールディングの更新を完了するために必要な手順に関する一般的な指示が含まれています。</span><span class="sxs-lookup"><span data-stu-id="a5067-191">The *ScaffoldingReadme.txt* file contains general instructions on what's needed to complete the Identity scaffolding update.</span></span> <span data-ttu-id="a5067-192">このドキュメントには、 *ScaffoldingReadme*ファイルよりも完全な手順が含まれています。</span><span class="sxs-lookup"><span data-stu-id="a5067-192">This document contains more complete instructions than the *ScaffoldingReadme.txt* file.</span></span>

<span data-ttu-id="a5067-193">ファイルの違いを示すソース管理システムを使用して、変更を元に戻すことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="a5067-193">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="a5067-194">Id scaffolder を実行した後に変更を確認します。</span><span class="sxs-lookup"><span data-stu-id="a5067-194">Inspect the changes after running the Identity scaffolder.</span></span>

> [!NOTE]
> <span data-ttu-id="a5067-195">サービスは、 [2 要素認証](xref:security/authentication/identity-enable-qrcodes)、アカウントの[確認とパスワードの回復](xref:security/authentication/accconfirm)、および id を持つその他のセキュリティ機能を使用する場合に必要です。</span><span class="sxs-lookup"><span data-stu-id="a5067-195">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="a5067-196">サービスまたはサービススタブは、スキャフォールディング Id では生成されません。</span><span class="sxs-lookup"><span data-stu-id="a5067-196">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="a5067-197">これらの機能を有効にするサービスは、手動で追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a5067-197">Services to enable these features must be added manually.</span></span> <span data-ttu-id="a5067-198">たとえば、「[電子メールの確認を要求する](xref:security/authentication/accconfirm#require-email-confirmation)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a5067-198">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="a5067-199">Id を空のプロジェクトにスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="a5067-199">Scaffold identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="a5067-200">次の強調表示された`Startup`呼び出しをクラスに追加します。</span><span class="sxs-lookup"><span data-stu-id="a5067-200">Add the following highlighted calls to the `Startup` class:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="a5067-201">既存の承認なしでスキャフォールディング id を Razor プロジェクトに</span><span class="sxs-lookup"><span data-stu-id="a5067-201">Scaffold identity into a Razor project without existing authorization</span></span>

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

<span data-ttu-id="a5067-202">Id は、 *Areas/identity/IdentityHostingStartup*で構成されます。</span><span class="sxs-lookup"><span data-stu-id="a5067-202">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="a5067-203">詳細については、「 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a5067-203">for more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="a5067-204">移行、UseAuthentication、およびレイアウト</span><span class="sxs-lookup"><span data-stu-id="a5067-204">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="a5067-205">認証を有効にする</span><span class="sxs-lookup"><span data-stu-id="a5067-205">Enable authentication</span></span>

<span data-ttu-id="a5067-206">クラスの`Configure`メソッドで、 `UseStaticFiles`次のように[useauthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)を呼び出します。 `Startup`</span><span class="sxs-lookup"><span data-stu-id="a5067-206">In the `Configure` method of the `Startup` class, call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="a5067-207">レイアウトの変更</span><span class="sxs-lookup"><span data-stu-id="a5067-207">Layout changes</span></span>

<span data-ttu-id="a5067-208">省略可能: 次のように`_LoginPartial`、ログイン部分 () をレイアウトファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="a5067-208">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[Main](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="a5067-209">承認を使用して id を Razor プロジェクトにスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="a5067-209">Scaffold identity into a Razor project with authorization</span></span>

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
<span data-ttu-id="a5067-210">一部の Id オプションは、 *Areas/Identity/IdentityHostingStartup*で構成されています。</span><span class="sxs-lookup"><span data-stu-id="a5067-210">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="a5067-211">詳細については、「 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a5067-211">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="a5067-212">既存の承認なしでスキャフォールディング identity を MVC プロジェクトに</span><span class="sxs-lookup"><span data-stu-id="a5067-212">Scaffold identity into an MVC project without existing authorization</span></span>

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

<span data-ttu-id="a5067-213">省略可能:`_LoginPartial` *Views/Shared/_Layout cshtml*ファイルにログイン部分 () を追加します。</span><span class="sxs-lookup"><span data-stu-id="a5067-213">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* <span data-ttu-id="a5067-214">*Pages/shared/_LoginPartial cshtml*ファイルを*Views/shared/_LoginPartial*に移動します。</span><span class="sxs-lookup"><span data-stu-id="a5067-214">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="a5067-215">Id は、 *Areas/identity/IdentityHostingStartup*で構成されます。</span><span class="sxs-lookup"><span data-stu-id="a5067-215">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="a5067-216">詳細については、「IHostingStartup」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a5067-216">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="a5067-217">次の後`UseStaticFiles`に[useauthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="a5067-217">Call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="a5067-218">承認を使用して id を MVC プロジェクトにスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="a5067-218">Scaffold identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="a5067-219">*ページ/共有*フォルダーとそのフォルダー内のファイルを削除します。</span><span class="sxs-lookup"><span data-stu-id="a5067-219">Delete the *Pages/Shared* folder and the files in that folder.</span></span>

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="a5067-220">完全な id UI ソースの作成</span><span class="sxs-lookup"><span data-stu-id="a5067-220">Create full identity UI source</span></span>

<span data-ttu-id="a5067-221">Id UI の完全な制御を維持するには、Identity scaffolder を実行し、[**すべてのファイルを上書き**する] を選択します。</span><span class="sxs-lookup"><span data-stu-id="a5067-221">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="a5067-222">次の強調表示されたコードは、既定の Id UI を ASP.NET Core 2.1 web アプリの Id に置き換える変更を示しています。</span><span class="sxs-lookup"><span data-stu-id="a5067-222">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="a5067-223">これは、Id UI を完全に制御するために必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="a5067-223">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="a5067-224">既定の Id は、次のコードで置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="a5067-224">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="a5067-225">次のコードでは、 [Loginpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath)、 [logoutpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)、および[AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath)を設定しています。</span><span class="sxs-lookup"><span data-stu-id="a5067-225">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="a5067-226">の`IEmailSender`実装を登録します。次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="a5067-226">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a><span data-ttu-id="a5067-227">登録の無効化ページ</span><span class="sxs-lookup"><span data-stu-id="a5067-227">Disable register page</span></span>

<span data-ttu-id="a5067-228">ユーザー登録を無効にするには:</span><span class="sxs-lookup"><span data-stu-id="a5067-228">To disable user registration:</span></span>

* <span data-ttu-id="a5067-229">スキャフォールディング Id。</span><span class="sxs-lookup"><span data-stu-id="a5067-229">Scaffold Identity.</span></span> <span data-ttu-id="a5067-230">Account、Account. Login、および Account. RegisterConfirmation を含めます。</span><span class="sxs-lookup"><span data-stu-id="a5067-230">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="a5067-231">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="a5067-231">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="a5067-232">ユーザーがこのエンドポイントから登録できないように、*区分/id/ページ/アカウント/登録*を更新します。</span><span class="sxs-lookup"><span data-stu-id="a5067-232">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="a5067-233">前の変更との一貫性を保つために、*区分/id/ページ/アカウント/登録*を更新します。</span><span class="sxs-lookup"><span data-stu-id="a5067-233">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="a5067-234">*区分/id/ページ/アカウント/ログイン. cshtml*から登録リンクをコメントアウトするか削除します。</span><span class="sxs-lookup"><span data-stu-id="a5067-234">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="a5067-235">[*区分/id/ページ/アカウント/RegisterConfirmation 確認*] ページを更新します。</span><span class="sxs-lookup"><span data-stu-id="a5067-235">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="a5067-236">コードとリンクを cshtml ファイルから削除します。</span><span class="sxs-lookup"><span data-stu-id="a5067-236">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="a5067-237">から確認コードを削除し`PageModel`ます。</span><span class="sxs-lookup"><span data-stu-id="a5067-237">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="a5067-238">別のアプリを使用してユーザーを追加する</span><span class="sxs-lookup"><span data-stu-id="a5067-238">Use another app to add users</span></span>

<span data-ttu-id="a5067-239">Web アプリの外部にユーザーを追加するためのメカニズムを提供します。</span><span class="sxs-lookup"><span data-stu-id="a5067-239">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="a5067-240">ユーザーを追加するためのオプションは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="a5067-240">Options to add users include:</span></span>

* <span data-ttu-id="a5067-241">専用管理者 web アプリ。</span><span class="sxs-lookup"><span data-stu-id="a5067-241">A dedicated admin web app.</span></span>
* <span data-ttu-id="a5067-242">コンソールアプリ。</span><span class="sxs-lookup"><span data-stu-id="a5067-242">A console app.</span></span>

<span data-ttu-id="a5067-243">次のコードは、ユーザーを追加する方法の概要を示しています。</span><span class="sxs-lookup"><span data-stu-id="a5067-243">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="a5067-244">ユーザーの一覧がメモリに読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="a5067-244">A list of users is read into memory.</span></span>
* <span data-ttu-id="a5067-245">ユーザーごとに一意の強力なパスワードが生成されます。</span><span class="sxs-lookup"><span data-stu-id="a5067-245">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="a5067-246">ユーザーが Id データベースに追加されます。</span><span class="sxs-lookup"><span data-stu-id="a5067-246">The user is added to the Identity database.</span></span>
* <span data-ttu-id="a5067-247">ユーザーに通知され、パスワードを変更するように指示されます。</span><span class="sxs-lookup"><span data-stu-id="a5067-247">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="a5067-248">次のコードは、ユーザーの追加の概要を示しています。</span><span class="sxs-lookup"><span data-stu-id="a5067-248">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="a5067-249">同様の方法で、運用環境のシナリオにも対応できます。</span><span class="sxs-lookup"><span data-stu-id="a5067-249">A similar approach can be followed for production scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a5067-250">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="a5067-250">Additional resources</span></span>

* [<span data-ttu-id="a5067-251">ASP.NET Core 2.1 以降に認証コードが変更された</span><span class="sxs-lookup"><span data-stu-id="a5067-251">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end