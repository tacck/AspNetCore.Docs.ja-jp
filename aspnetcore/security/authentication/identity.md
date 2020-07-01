---
title: IdentityASP.NET Core の概要
author: rick-anderson
description: IdentityASP.NET Core アプリで使用します。 パスワードの要件 (RequireDigit、RequiredLength、RequiredUniqueChars など) を設定する方法について説明します。
ms.author: riande
ms.date: 01/15/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/identity
ms.openlocfilehash: 31970bd2b52ad83c116067d258aa9dca2d9b3b66
ms.sourcegitcommit: 895e952aec11c91d703fbdd3640a979307b8cc67
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2020
ms.locfileid: "85793565"
---
# <a name="introduction-to-identity-on-aspnet-core"></a><span data-ttu-id="f742a-104">IdentityASP.NET Core の概要</span><span class="sxs-lookup"><span data-stu-id="f742a-104">Introduction to Identity on ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f742a-105">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f742a-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="f742a-106">ASP.NET Core Identity:</span><span class="sxs-lookup"><span data-stu-id="f742a-106">ASP.NET Core Identity:</span></span>

* <span data-ttu-id="f742a-107">は、ユーザーインターフェイス (UI) のログイン機能をサポートする API です。</span><span class="sxs-lookup"><span data-stu-id="f742a-107">Is an API that supports user interface (UI) login functionality.</span></span>
* <span data-ttu-id="f742a-108">ユーザー、パスワード、プロファイルデータ、ロール、要求、トークン、電子メールの確認などを管理します。</span><span class="sxs-lookup"><span data-stu-id="f742a-108">Manages users, passwords, profile data, roles, claims, tokens, email confirmation, and more.</span></span>

<span data-ttu-id="f742a-109">ユーザーは、に格納されているログイン情報を持つアカウントを作成することも、外部ログインプロバイダーを使用することもでき Identity ます。</span><span class="sxs-lookup"><span data-stu-id="f742a-109">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="f742a-110">サポートされている外部ログインプロバイダーには、 [Facebook、Google、Microsoft アカウント、Twitter](xref:security/authentication/social/index)があります。</span><span class="sxs-lookup"><span data-stu-id="f742a-110">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="f742a-111">[ Identity ソースコード](https://github.com/dotnet/AspNetCore/tree/master/src/Identity)は GitHub で入手できます。</span><span class="sxs-lookup"><span data-stu-id="f742a-111">The [Identity source code](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) is available on GitHub.</span></span> <span data-ttu-id="f742a-112">[スキャフォールディング Identity ](xref:security/authentication/scaffold-identity)生成されたファイルを表示して、テンプレートとの対話を確認し Identity ます。</span><span class="sxs-lookup"><span data-stu-id="f742a-112">[Scaffold Identity](xref:security/authentication/scaffold-identity) and view the generated files to review the template interaction with Identity.</span></span>

Identity<span data-ttu-id="f742a-113">は、通常、ユーザー名、パスワード、およびプロファイルデータを格納するために SQL Server データベースを使用して構成されます。</span><span class="sxs-lookup"><span data-stu-id="f742a-113"> is typically configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="f742a-114">別の永続ストアを使用することもできます (たとえば、Azure Table Storage)。</span><span class="sxs-lookup"><span data-stu-id="f742a-114">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="f742a-115">このトピックでは、を使用し Identity てユーザーを登録、ログイン、ログアウトする方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="f742a-115">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="f742a-116">注: このテンプレートでは、ユーザー名と電子メールはユーザーに対して同じものとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="f742a-116">Note: the templates treat username and email as the same for users.</span></span> <span data-ttu-id="f742a-117">を使用するアプリを作成する方法の詳細については Identity 、この記事の最後にある「次のステップ」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f742a-117">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<span data-ttu-id="f742a-118">[Microsoft id プラットフォーム](/azure/active-directory/develop/)は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="f742a-118">[Microsoft identity platform](/azure/active-directory/develop/) is:</span></span>

* <span data-ttu-id="f742a-119">Azure Active Directory (Azure AD) 開発プラットフォームの進化。</span><span class="sxs-lookup"><span data-stu-id="f742a-119">An evolution of the Azure Active Directory (Azure AD) developer platform.</span></span>
* <span data-ttu-id="f742a-120">ASP.NET Core に関連付けら Identity れていません。</span><span class="sxs-lookup"><span data-stu-id="f742a-120">Unrelated to ASP.NET Core Identity.</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

<span data-ttu-id="f742a-121">サンプルコード ([ダウンロード方法)](xref:index#how-to-download-a-sample)を[表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample)します。</span><span class="sxs-lookup"><span data-stu-id="f742a-121">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="f742a-122">認証を使用して Web アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="f742a-122">Create a Web app with authentication</span></span>

<span data-ttu-id="f742a-123">個々のユーザーアカウントを使用して ASP.NET Core Web アプリケーションプロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="f742a-123">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f742a-124">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f742a-124">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f742a-125">[**ファイル**] [新しいプロジェクト] を選択し > **New** > **Project**ます。</span><span class="sxs-lookup"><span data-stu-id="f742a-125">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="f742a-126">**[ASP.NET Core Web アプリケーション]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="f742a-126">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="f742a-127">プロジェクトに**WebApp1**という名前を付け、プロジェクトのダウンロードと同じ名前空間にします。</span><span class="sxs-lookup"><span data-stu-id="f742a-127">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="f742a-128">**[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="f742a-128">Click **OK**.</span></span>
* <span data-ttu-id="f742a-129">ASP.NET Core **Web アプリケーション**を選択し、[**認証の変更**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="f742a-129">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="f742a-130">**個々のユーザーアカウント**を選択し、[ **OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="f742a-130">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="f742a-131">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="f742a-131">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

<span data-ttu-id="f742a-132">上記のコマンドは、 Razor SQLite を使用して web アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="f742a-132">The preceding command creates a Razor web app using SQLite.</span></span> <span data-ttu-id="f742a-133">LocalDB を使用して web アプリを作成するには、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="f742a-133">To create the web app with LocalDB, run the following command:</span></span>

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

<span data-ttu-id="f742a-134">生成されたプロジェクトでは、 [ Razor クラスライブラリ](xref:razor-pages/ui-class)として[ASP.NET Core Identity ](xref:security/authentication/identity)が提供されます。</span><span class="sxs-lookup"><span data-stu-id="f742a-134">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="f742a-135">Identity Razor クラスライブラリは、領域と共にエンドポイントを公開し `Identity` ます。</span><span class="sxs-lookup"><span data-stu-id="f742a-135">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="f742a-136">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="f742a-136">For example:</span></span>

* <span data-ttu-id="f742a-137">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="f742a-137">/Identity/Account/Login</span></span>
* <span data-ttu-id="f742a-138">/Identity/アカウント/ログアウト</span><span class="sxs-lookup"><span data-stu-id="f742a-138">/Identity/Account/Logout</span></span>
* <span data-ttu-id="f742a-139">/Identity/アカウント/管理</span><span class="sxs-lookup"><span data-stu-id="f742a-139">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="f742a-140">移行を適用する</span><span class="sxs-lookup"><span data-stu-id="f742a-140">Apply migrations</span></span>

<span data-ttu-id="f742a-141">移行を適用してデータベースを初期化します。</span><span class="sxs-lookup"><span data-stu-id="f742a-141">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f742a-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f742a-142">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f742a-143">パッケージマネージャーコンソール (PMC) で次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="f742a-143">Run the following command in the Package Manager Console (PMC):</span></span>

`PM> Update-Database`

# <a name="net-core-cli"></a>[<span data-ttu-id="f742a-144">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="f742a-144">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="f742a-145">SQLite を使用する場合、この手順で移行する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="f742a-145">Migrations are not necessary at this step when using SQLite.</span></span>

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="f742a-146">LocalDB の場合は、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="f742a-146">For LocalDB, run the following command:</span></span>

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="f742a-147">テストレジスタとログイン</span><span class="sxs-lookup"><span data-stu-id="f742a-147">Test Register and Login</span></span>

<span data-ttu-id="f742a-148">アプリを実行し、ユーザーを登録します。</span><span class="sxs-lookup"><span data-stu-id="f742a-148">Run the app and register a user.</span></span> <span data-ttu-id="f742a-149">画面のサイズによっては、[ナビゲーション] トグルボタンを選択して、**登録**リンクと**ログイン**リンクを表示する必要がある場合があります。</span><span class="sxs-lookup"><span data-stu-id="f742a-149">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="f742a-150">サービスの構成 Identity</span><span class="sxs-lookup"><span data-stu-id="f742a-150">Configure Identity services</span></span>

<span data-ttu-id="f742a-151">サービスはに追加されて `ConfigureServices` います。</span><span class="sxs-lookup"><span data-stu-id="f742a-151">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="f742a-152">一般的なパターンは、すべての `Add{Service}` メソッドを呼び出した後、すべての `services.Configure{Service}` メソッドを呼び出すことです。</span><span class="sxs-lookup"><span data-stu-id="f742a-152">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=10-99)]

<span data-ttu-id="f742a-153">前の強調表示されたコードは、 Identity 既定のオプション値を使用してを構成します。</span><span class="sxs-lookup"><span data-stu-id="f742a-153">The preceding highlighted code configures Identity with default option values.</span></span> <span data-ttu-id="f742a-154">サービスは、[依存関係の挿入](xref:fundamentals/dependency-injection)によってアプリで使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="f742a-154">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

Identity<span data-ttu-id="f742a-155">は、を呼び出すことによって有効になり <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> ます。</span><span class="sxs-lookup"><span data-stu-id="f742a-155"> is enabled by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>.</span></span> <span data-ttu-id="f742a-156">`UseAuthentication`認証[ミドルウェア](xref:fundamentals/middleware/index)を要求パイプラインに追加します。</span><span class="sxs-lookup"><span data-stu-id="f742a-156">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

<span data-ttu-id="f742a-157">テンプレートで生成されたアプリは、[承認](xref:security/authorization/secure-data)を使用しません。</span><span class="sxs-lookup"><span data-stu-id="f742a-157">The template-generated app doesn't use [authorization](xref:security/authorization/secure-data).</span></span> <span data-ttu-id="f742a-158">`app.UseAuthorization`は、アプリが承認を追加する正しい順序で追加されるようにするために用意されています。</span><span class="sxs-lookup"><span data-stu-id="f742a-158">`app.UseAuthorization` is included to ensure it's added in the correct order should the app add authorization.</span></span> <span data-ttu-id="f742a-159">`UseRouting`、 `UseAuthentication` 、 `UseAuthorization` 、および `UseEndpoints` は、前のコードに示されている順序で呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="f742a-159">`UseRouting`, `UseAuthentication`, `UseAuthorization`, and `UseEndpoints` must be called in the order shown in the preceding code.</span></span>

<span data-ttu-id="f742a-160">およびの詳細に `IdentityOptions` つい `Startup` ては、「」および「アプリケーションの起動」を参照してください <xref:Microsoft.AspNetCore.Identity.IdentityOptions> 。 [Application Startup](xref:fundamentals/startup)</span><span class="sxs-lookup"><span data-stu-id="f742a-160">For more information on `IdentityOptions` and `Startup`, see <xref:Microsoft.AspNetCore.Identity.IdentityOptions> and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="f742a-161">スキャフォールディング Register、Login、および LogOut</span><span class="sxs-lookup"><span data-stu-id="f742a-161">Scaffold Register, Login, and LogOut</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f742a-162">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f742a-162">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f742a-163">Register、Login、および LogOut の各ファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="f742a-163">Add the Register, Login, and LogOut files.</span></span> <span data-ttu-id="f742a-164">このセクションに示されているコードを生成するための[ Razor 承認手順を含むプロジェクトに、スキャフォールディング id](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization)に従います。</span><span class="sxs-lookup"><span data-stu-id="f742a-164">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="f742a-165">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="f742a-165">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="f742a-166">**WebApp1**という名前のプロジェクトを作成した場合は、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="f742a-166">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="f742a-167">それ以外の場合は、の正しい名前空間を使用し `ApplicationDbContext` ます。</span><span class="sxs-lookup"><span data-stu-id="f742a-167">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="f742a-168">PowerShell では、コマンドの区切り記号としてセミコロンを使用します。</span><span class="sxs-lookup"><span data-stu-id="f742a-168">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="f742a-169">PowerShell を使用する場合は、前の例に示したように、ファイルリスト内のセミコロンをエスケープするか、ファイルリストを二重引用符で囲みます。</span><span class="sxs-lookup"><span data-stu-id="f742a-169">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

<span data-ttu-id="f742a-170">スキャフォールディングの詳細につい Identity ては、「[スキャフォールディング identity to a Razor project in authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f742a-170">For more information on scaffolding Identity, see [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="f742a-171">レジスタの確認</span><span class="sxs-lookup"><span data-stu-id="f742a-171">Examine Register</span></span>

<span data-ttu-id="f742a-172">ユーザーが [**登録**] リンクをクリックすると、 `RegisterModel.OnPostAsync` アクションが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="f742a-172">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="f742a-173">ユーザーは、オブジェクトに対して[Createasync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_)によって作成され `_userManager` ます。</span><span class="sxs-lookup"><span data-stu-id="f742a-173">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object:</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

<span data-ttu-id="f742a-174">ユーザーが正常に作成された場合は、の呼び出しによってユーザーがログインし `_signInManager.SignInAsync` ます。</span><span class="sxs-lookup"><span data-stu-id="f742a-174">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="f742a-175">登録時にすぐにログインできないようにする手順については、「[アカウントの確認](xref:security/authentication/accconfirm#prevent-login-at-registration)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="f742a-175">See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="f742a-176">ログイン</span><span class="sxs-lookup"><span data-stu-id="f742a-176">Log in</span></span>

<span data-ttu-id="f742a-177">ログインフォームは次の場合に表示されます。</span><span class="sxs-lookup"><span data-stu-id="f742a-177">The Login form is displayed when:</span></span>

* <span data-ttu-id="f742a-178">**ログイン**リンクが選択されています。</span><span class="sxs-lookup"><span data-stu-id="f742a-178">The **Log in** link is selected.</span></span>
* <span data-ttu-id="f742a-179">ユーザーがアクセスを承認されていない、**または**システムによって認証されていない制限付きページにアクセスしようとしています。</span><span class="sxs-lookup"><span data-stu-id="f742a-179">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="f742a-180">ログインページのフォームが送信されると、 `OnPostAsync` アクションが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="f742a-180">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="f742a-181">`PasswordSignInAsync`は、オブジェクトで呼び出され `_signInManager` ます。</span><span class="sxs-lookup"><span data-stu-id="f742a-181">`PasswordSignInAsync` is called on the `_signInManager` object.</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="f742a-182">承認決定を行う方法については、「」を参照してください <xref:security/authorization/introduction> 。</span><span class="sxs-lookup"><span data-stu-id="f742a-182">For information on how to make authorization decisions, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="f742a-183">ログアウト</span><span class="sxs-lookup"><span data-stu-id="f742a-183">Log out</span></span>

<span data-ttu-id="f742a-184">[**ログアウト**すると、アクションが呼び出され `LogoutModel.OnPost` ます。</span><span class="sxs-lookup"><span data-stu-id="f742a-184">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Logout.cshtml.cs?highlight=36)]

<span data-ttu-id="f742a-185">前のコードでは、 `return RedirectToPage();` ブラウザーが新しい要求を実行し、ユーザーの id が更新されるように、コードをリダイレクトにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="f742a-185">In the preceding code, the code `return RedirectToPage();` needs to be a redirect so that the browser performs a new request and the identity for the user gets updated.</span></span>

<span data-ttu-id="f742a-186">[Signoutasync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync)者は、cookie に格納されているユーザーの要求をクリアします。</span><span class="sxs-lookup"><span data-stu-id="f742a-186">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="f742a-187">Post は*Pages/Shared/_LoginPartial*に指定されています。</span><span class="sxs-lookup"><span data-stu-id="f742a-187">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-identity"></a><span data-ttu-id="f742a-188">TestIdentity</span><span class="sxs-lookup"><span data-stu-id="f742a-188">Test Identity</span></span>

<span data-ttu-id="f742a-189">既定の web プロジェクトテンプレートでは、ホームページへの匿名アクセスが許可されます。</span><span class="sxs-lookup"><span data-stu-id="f742a-189">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="f742a-190">テストするには Identity 、次のように追加し [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) ます。</span><span class="sxs-lookup"><span data-stu-id="f742a-190">To test Identity, add [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute):</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="f742a-191">サインインしている場合は、サインアウトします。アプリを実行し、[**プライバシー** ] リンクを選択します。</span><span class="sxs-lookup"><span data-stu-id="f742a-191">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="f742a-192">ログイン ページにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="f742a-192">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="f742a-193">TsmIdentity</span><span class="sxs-lookup"><span data-stu-id="f742a-193">Explore Identity</span></span>

<span data-ttu-id="f742a-194">詳細については、次を参照して Identity ください。</span><span class="sxs-lookup"><span data-stu-id="f742a-194">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="f742a-195">完全な id UI ソースの作成</span><span class="sxs-lookup"><span data-stu-id="f742a-195">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="f742a-196">各ページのソースを確認し、デバッガーをステップ実行します。</span><span class="sxs-lookup"><span data-stu-id="f742a-196">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a>Identity<span data-ttu-id="f742a-197">コンポーネント</span><span class="sxs-lookup"><span data-stu-id="f742a-197"> Components</span></span>

<span data-ttu-id="f742a-198">すべての Identity 依存する NuGet パッケージは、 [ASP.NET Core 共有フレームワーク](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework)に含まれています。</span><span class="sxs-lookup"><span data-stu-id="f742a-198">All the Identity-dependent NuGet packages are included in the [ASP.NET Core shared framework](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span></span>

<span data-ttu-id="f742a-199">のプライマリパッケージ Identity は[AspNetCore です Identity ](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)。</span><span class="sxs-lookup"><span data-stu-id="f742a-199">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="f742a-200">このパッケージには ASP.NET Core のインターフェイスのコアセットが含まれており、によって含まれてい Identity `Microsoft.AspNetCore.Identity.EntityFrameworkCore` ます。</span><span class="sxs-lookup"><span data-stu-id="f742a-200">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="f742a-201">ASP.NET Core への移行Identity</span><span class="sxs-lookup"><span data-stu-id="f742a-201">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="f742a-202">既存のストアの移行に関する詳細とガイダンスについて Identity は、「[認証と Identity の移行](xref:migration/identity)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f742a-202">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="f742a-203">パスワードの強度を設定する</span><span class="sxs-lookup"><span data-stu-id="f742a-203">Setting password strength</span></span>

<span data-ttu-id="f742a-204">パスワードの最小要件を設定するサンプルについては、「[構成](#pw)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f742a-204">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="f742a-205">AddDefaultIdentity と AddIdentity</span><span class="sxs-lookup"><span data-stu-id="f742a-205">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="f742a-206"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*>は ASP.NET Core 2.1 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="f742a-206"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="f742a-207">`AddDefaultIdentity`の呼び出しは、次の呼び出しに似ています。</span><span class="sxs-lookup"><span data-stu-id="f742a-207">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="f742a-208">詳細については、「 [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f742a-208">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="prevent-publish-of-static-identity-assets"></a><span data-ttu-id="f742a-209">静的アセットの発行を禁止する Identity</span><span class="sxs-lookup"><span data-stu-id="f742a-209">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="f742a-210">静的 Identity アセット (UI 用のスタイルシートおよび JavaScript ファイル) を web ルートに発行できないようにするには Identity 、 `ResolveStaticWebAssetsInputsDependsOn` `RemoveIdentityAssets` アプリケーションのプロジェクトファイルに次のプロパティとターゲットを追加します。</span><span class="sxs-lookup"><span data-stu-id="f742a-210">To prevent publishing static Identity assets (stylesheets and JavaScript files for Identity UI) to the web root, add the following `ResolveStaticWebAssetsInputsDependsOn` property and `RemoveIdentityAssets` target to the app's project file:</span></span>

```xml
<PropertyGroup>
  <ResolveStaticWebAssetsInputsDependsOn>RemoveIdentityAssets</ResolveStaticWebAssetsInputsDependsOn>
</PropertyGroup>

<Target Name="RemoveIdentityAssets">
  <ItemGroup>
    <StaticWebAsset Remove="@(StaticWebAsset)" Condition="%(SourceId) == 'Microsoft.AspNetCore.Identity.UI'" />
  </ItemGroup>
</Target>
```

## <a name="next-steps"></a><span data-ttu-id="f742a-211">次の手順</span><span class="sxs-lookup"><span data-stu-id="f742a-211">Next Steps</span></span>

* <span data-ttu-id="f742a-212">[ASP.NET Core Identity ソースコード](https://github.com/dotnet/aspnetcore/tree/master/src/Identity)</span><span class="sxs-lookup"><span data-stu-id="f742a-212">[ASP.NET Core Identity source code](https://github.com/dotnet/aspnetcore/tree/master/src/Identity)</span></span>
* <span data-ttu-id="f742a-213">SQLite を使用したの構成の詳細については、こちらの[GitHub の問題](https://github.com/dotnet/AspNetCore.Docs/issues/5131)を参照してください Identity 。</span><span class="sxs-lookup"><span data-stu-id="f742a-213">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* <span data-ttu-id="f742a-214">[Identity を構成する](xref:security/authentication/identity-configuration)</span><span class="sxs-lookup"><span data-stu-id="f742a-214">[Configure Identity](xref:security/authentication/identity-configuration)</span></span>
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f742a-215">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f742a-215">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="f742a-216">ASP.NET Core Identity は ASP.NET Core アプリにログイン機能を追加するメンバーシップシステムです。</span><span class="sxs-lookup"><span data-stu-id="f742a-216">ASP.NET Core Identity is a membership system that adds login functionality to ASP.NET Core apps.</span></span> <span data-ttu-id="f742a-217">ユーザーは、に格納されているログイン情報を持つアカウントを作成することも、外部ログインプロバイダーを使用することもでき Identity ます。</span><span class="sxs-lookup"><span data-stu-id="f742a-217">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="f742a-218">サポートされている外部ログインプロバイダーには、 [Facebook、Google、Microsoft アカウント、Twitter](xref:security/authentication/social/index)があります。</span><span class="sxs-lookup"><span data-stu-id="f742a-218">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

Identity<span data-ttu-id="f742a-219">SQL Server データベースを使用して、ユーザー名、パスワード、およびプロファイルデータを格納するように構成できます。</span><span class="sxs-lookup"><span data-stu-id="f742a-219"> can be configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="f742a-220">別の永続ストアを使用することもできます (たとえば、Azure Table Storage)。</span><span class="sxs-lookup"><span data-stu-id="f742a-220">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="f742a-221">サンプルコード ([ダウンロード方法)](xref:index#how-to-download-a-sample)を[表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/)します。</span><span class="sxs-lookup"><span data-stu-id="f742a-221">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="f742a-222">このトピックでは、を使用し Identity てユーザーを登録、ログイン、ログアウトする方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="f742a-222">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="f742a-223">を使用するアプリを作成する方法の詳細については Identity 、この記事の最後にある「次のステップ」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f742a-223">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<a name="adi"></a>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="f742a-224">AddDefaultIdentity と AddIdentity</span><span class="sxs-lookup"><span data-stu-id="f742a-224">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="f742a-225"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*>は ASP.NET Core 2.1 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="f742a-225"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="f742a-226">`AddDefaultIdentity`の呼び出しは、次の呼び出しに似ています。</span><span class="sxs-lookup"><span data-stu-id="f742a-226">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="f742a-227">詳細については、「 [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f742a-227">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="f742a-228">認証を使用して Web アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="f742a-228">Create a Web app with authentication</span></span>

<span data-ttu-id="f742a-229">個々のユーザーアカウントを使用して ASP.NET Core Web アプリケーションプロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="f742a-229">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f742a-230">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f742a-230">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f742a-231">[**ファイル**] [新しいプロジェクト] を選択し > **New** > **Project**ます。</span><span class="sxs-lookup"><span data-stu-id="f742a-231">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="f742a-232">**[ASP.NET Core Web アプリケーション]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="f742a-232">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="f742a-233">プロジェクトに**WebApp1**という名前を付け、プロジェクトのダウンロードと同じ名前空間にします。</span><span class="sxs-lookup"><span data-stu-id="f742a-233">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="f742a-234">**[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="f742a-234">Click **OK**.</span></span>
* <span data-ttu-id="f742a-235">ASP.NET Core **Web アプリケーション**を選択し、[**認証の変更**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="f742a-235">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="f742a-236">**個々のユーザーアカウント**を選択し、[ **OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="f742a-236">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="f742a-237">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="f742a-237">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

<span data-ttu-id="f742a-238">生成されたプロジェクトでは、 [ Razor クラスライブラリ](xref:razor-pages/ui-class)として[ASP.NET Core Identity ](xref:security/authentication/identity)が提供されます。</span><span class="sxs-lookup"><span data-stu-id="f742a-238">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="f742a-239">Identity Razor クラスライブラリは、領域と共にエンドポイントを公開し `Identity` ます。</span><span class="sxs-lookup"><span data-stu-id="f742a-239">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="f742a-240">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="f742a-240">For example:</span></span>

* <span data-ttu-id="f742a-241">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="f742a-241">/Identity/Account/Login</span></span>
* <span data-ttu-id="f742a-242">/Identity/アカウント/ログアウト</span><span class="sxs-lookup"><span data-stu-id="f742a-242">/Identity/Account/Logout</span></span>
* <span data-ttu-id="f742a-243">/Identity/アカウント/管理</span><span class="sxs-lookup"><span data-stu-id="f742a-243">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="f742a-244">移行を適用する</span><span class="sxs-lookup"><span data-stu-id="f742a-244">Apply migrations</span></span>

<span data-ttu-id="f742a-245">移行を適用してデータベースを初期化します。</span><span class="sxs-lookup"><span data-stu-id="f742a-245">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f742a-246">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f742a-246">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f742a-247">パッケージマネージャーコンソール (PMC) で次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="f742a-247">Run the following command in the Package Manager Console (PMC):</span></span>

```powershell
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="f742a-248">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="f742a-248">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="f742a-249">テストレジスタとログイン</span><span class="sxs-lookup"><span data-stu-id="f742a-249">Test Register and Login</span></span>

<span data-ttu-id="f742a-250">アプリを実行し、ユーザーを登録します。</span><span class="sxs-lookup"><span data-stu-id="f742a-250">Run the app and register a user.</span></span> <span data-ttu-id="f742a-251">画面のサイズによっては、[ナビゲーション] トグルボタンを選択して、**登録**リンクと**ログイン**リンクを表示する必要がある場合があります。</span><span class="sxs-lookup"><span data-stu-id="f742a-251">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="f742a-252">サービスの構成 Identity</span><span class="sxs-lookup"><span data-stu-id="f742a-252">Configure Identity services</span></span>

<span data-ttu-id="f742a-253">サービスはに追加されて `ConfigureServices` います。</span><span class="sxs-lookup"><span data-stu-id="f742a-253">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="f742a-254">一般的なパターンは、すべての `Add{Service}` メソッドを呼び出した後、すべての `services.Configure{Service}` メソッドを呼び出すことです。</span><span class="sxs-lookup"><span data-stu-id="f742a-254">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

<span data-ttu-id="f742a-255">前のコードでは、 Identity 既定のオプション値を使用してを構成します。</span><span class="sxs-lookup"><span data-stu-id="f742a-255">The preceding code configures Identity with default option values.</span></span> <span data-ttu-id="f742a-256">サービスは、[依存関係の挿入](xref:fundamentals/dependency-injection)によってアプリで使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="f742a-256">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

Identity<span data-ttu-id="f742a-257">は、 [Useauthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)を呼び出すことによって有効になります。</span><span class="sxs-lookup"><span data-stu-id="f742a-257"> is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="f742a-258">`UseAuthentication`認証[ミドルウェア](xref:fundamentals/middleware/index)を要求パイプラインに追加します。</span><span class="sxs-lookup"><span data-stu-id="f742a-258">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

<span data-ttu-id="f742a-259">詳細については、「[クラス](/dotnet/api/microsoft.aspnetcore.identity.identityoptions)と[アプリケーションの起動](xref:fundamentals/startup)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f742a-259">For more information, see the [IdentityOptions Class](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="f742a-260">スキャフォールディング Register、Login、および LogOut</span><span class="sxs-lookup"><span data-stu-id="f742a-260">Scaffold Register, Login, and LogOut</span></span>

<span data-ttu-id="f742a-261">このセクションに示されているコードを生成するための[ Razor 承認手順を含むプロジェクトに、スキャフォールディング id](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization)に従います。</span><span class="sxs-lookup"><span data-stu-id="f742a-261">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f742a-262">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f742a-262">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f742a-263">Register、Login、および LogOut の各ファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="f742a-263">Add the Register, Login, and LogOut files.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="f742a-264">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="f742a-264">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="f742a-265">**WebApp1**という名前のプロジェクトを作成した場合は、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="f742a-265">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="f742a-266">それ以外の場合は、の正しい名前空間を使用し `ApplicationDbContext` ます。</span><span class="sxs-lookup"><span data-stu-id="f742a-266">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="f742a-267">PowerShell では、コマンドの区切り記号としてセミコロンを使用します。</span><span class="sxs-lookup"><span data-stu-id="f742a-267">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="f742a-268">PowerShell を使用する場合は、前の例に示したように、ファイルリスト内のセミコロンをエスケープするか、ファイルリストを二重引用符で囲みます。</span><span class="sxs-lookup"><span data-stu-id="f742a-268">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="f742a-269">レジスタの確認</span><span class="sxs-lookup"><span data-stu-id="f742a-269">Examine Register</span></span>

<span data-ttu-id="f742a-270">ユーザーが [**登録**] リンクをクリックすると、 `RegisterModel.OnPostAsync` アクションが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="f742a-270">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="f742a-271">ユーザーは、オブジェクトに対して[Createasync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_)によって作成され `_userManager` ます。</span><span class="sxs-lookup"><span data-stu-id="f742a-271">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object:</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

<span data-ttu-id="f742a-272">ユーザーが正常に作成された場合は、の呼び出しによってユーザーがログインし `_signInManager.SignInAsync` ます。</span><span class="sxs-lookup"><span data-stu-id="f742a-272">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="f742a-273">**注:** 登録時にすぐにログインできないようにする手順については、「[アカウントの確認](xref:security/authentication/accconfirm#prevent-login-at-registration)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="f742a-273">**Note:** See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="f742a-274">ログイン</span><span class="sxs-lookup"><span data-stu-id="f742a-274">Log in</span></span>

<span data-ttu-id="f742a-275">ログインフォームは次の場合に表示されます。</span><span class="sxs-lookup"><span data-stu-id="f742a-275">The Login form is displayed when:</span></span>

* <span data-ttu-id="f742a-276">**ログイン**リンクが選択されています。</span><span class="sxs-lookup"><span data-stu-id="f742a-276">The **Log in** link is selected.</span></span>
* <span data-ttu-id="f742a-277">ユーザーがアクセスを承認されていない、**または**システムによって認証されていない制限付きページにアクセスしようとしています。</span><span class="sxs-lookup"><span data-stu-id="f742a-277">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="f742a-278">ログインページのフォームが送信されると、 `OnPostAsync` アクションが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="f742a-278">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="f742a-279">`PasswordSignInAsync`は、オブジェクトで呼び出され `_signInManager` ます。</span><span class="sxs-lookup"><span data-stu-id="f742a-279">`PasswordSignInAsync` is called on the `_signInManager` object.</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="f742a-280">承認決定を行う方法については、「」を参照してください <xref:security/authorization/introduction> 。</span><span class="sxs-lookup"><span data-stu-id="f742a-280">For information on how to make authorization decisions, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="f742a-281">ログアウト</span><span class="sxs-lookup"><span data-stu-id="f742a-281">Log out</span></span>

<span data-ttu-id="f742a-282">[**ログアウト**すると、アクションが呼び出され `LogoutModel.OnPost` ます。</span><span class="sxs-lookup"><span data-stu-id="f742a-282">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

<span data-ttu-id="f742a-283">[Signoutasync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync)者は、cookie に格納されているユーザーの要求をクリアします。</span><span class="sxs-lookup"><span data-stu-id="f742a-283">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="f742a-284">Post は*Pages/Shared/_LoginPartial*に指定されています。</span><span class="sxs-lookup"><span data-stu-id="f742a-284">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-identity"></a><span data-ttu-id="f742a-285">TestIdentity</span><span class="sxs-lookup"><span data-stu-id="f742a-285">Test Identity</span></span>

<span data-ttu-id="f742a-286">既定の web プロジェクトテンプレートでは、ホームページへの匿名アクセスが許可されます。</span><span class="sxs-lookup"><span data-stu-id="f742a-286">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="f742a-287">をテストするに Identity は、[ [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) プライバシー] ページにを追加します。</span><span class="sxs-lookup"><span data-stu-id="f742a-287">To test Identity, add [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) to the Privacy page.</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="f742a-288">サインインしている場合は、サインアウトします。アプリを実行し、[**プライバシー** ] リンクを選択します。</span><span class="sxs-lookup"><span data-stu-id="f742a-288">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="f742a-289">ログイン ページにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="f742a-289">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="f742a-290">TsmIdentity</span><span class="sxs-lookup"><span data-stu-id="f742a-290">Explore Identity</span></span>

<span data-ttu-id="f742a-291">詳細については、次を参照して Identity ください。</span><span class="sxs-lookup"><span data-stu-id="f742a-291">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="f742a-292">完全な id UI ソースの作成</span><span class="sxs-lookup"><span data-stu-id="f742a-292">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="f742a-293">各ページのソースを確認し、デバッガーをステップ実行します。</span><span class="sxs-lookup"><span data-stu-id="f742a-293">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a>Identity<span data-ttu-id="f742a-294">コンポーネント</span><span class="sxs-lookup"><span data-stu-id="f742a-294"> Components</span></span>

<span data-ttu-id="f742a-295">すべての Identity 依存する NuGet パッケージは、 [AspNetCore メタパッケージ](xref:fundamentals/metapackage-app)に含まれています。</span><span class="sxs-lookup"><span data-stu-id="f742a-295">All the Identity dependent NuGet packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="f742a-296">のプライマリパッケージ Identity は[AspNetCore です Identity ](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)。</span><span class="sxs-lookup"><span data-stu-id="f742a-296">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="f742a-297">このパッケージには ASP.NET Core のインターフェイスのコアセットが含まれており、によって含まれてい Identity `Microsoft.AspNetCore.Identity.EntityFrameworkCore` ます。</span><span class="sxs-lookup"><span data-stu-id="f742a-297">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="f742a-298">ASP.NET Core への移行Identity</span><span class="sxs-lookup"><span data-stu-id="f742a-298">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="f742a-299">既存のストアの移行に関する詳細とガイダンスについて Identity は、「[認証と Identity の移行](xref:migration/identity)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f742a-299">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="f742a-300">パスワードの強度を設定する</span><span class="sxs-lookup"><span data-stu-id="f742a-300">Setting password strength</span></span>

<span data-ttu-id="f742a-301">パスワードの最小要件を設定するサンプルについては、「[構成](#pw)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f742a-301">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="next-steps"></a><span data-ttu-id="f742a-302">次の手順</span><span class="sxs-lookup"><span data-stu-id="f742a-302">Next Steps</span></span>

* <span data-ttu-id="f742a-303">SQLite を使用したの構成の詳細については、こちらの[GitHub の問題](https://github.com/dotnet/AspNetCore.Docs/issues/5131)を参照してください Identity 。</span><span class="sxs-lookup"><span data-stu-id="f742a-303">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* <span data-ttu-id="f742a-304">[Identity を構成する](xref:security/authentication/identity-configuration)</span><span class="sxs-lookup"><span data-stu-id="f742a-304">[Configure Identity](xref:security/authentication/identity-configuration)</span></span>
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end
