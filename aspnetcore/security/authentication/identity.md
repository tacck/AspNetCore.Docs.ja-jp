---
title: ASP.NET Core での Id の概要
author: rick-anderson
description: ASP.NET Core アプリで Id を使用します。 パスワードの要件 (RequireDigit、RequiredLength、RequiredUniqueChars など) を設定する方法について説明します。
ms.author: riande
ms.date: 01/15/2020
uid: security/authentication/identity
ms.openlocfilehash: 4bc5f206b3aee7c2d34055703acc5b6c5218f964
ms.sourcegitcommit: 56861af66bb364a5d60c3c72d133d854b4cf292d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "82205944"
---
# <a name="introduction-to-identity-on-aspnet-core"></a><span data-ttu-id="912b8-104">ASP.NET Core での Id の概要</span><span class="sxs-lookup"><span data-stu-id="912b8-104">Introduction to Identity on ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="912b8-105">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="912b8-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="912b8-106">ASP.NET Core Id:</span><span class="sxs-lookup"><span data-stu-id="912b8-106">ASP.NET Core Identity:</span></span>

* <span data-ttu-id="912b8-107">は、ユーザーインターフェイス (UI) のログイン機能をサポートする API です。</span><span class="sxs-lookup"><span data-stu-id="912b8-107">Is an API that supports user interface (UI) login functionality.</span></span>
* <span data-ttu-id="912b8-108">ユーザー、パスワード、プロファイルデータ、ロール、要求、トークン、電子メールの確認などを管理します。</span><span class="sxs-lookup"><span data-stu-id="912b8-108">Manages users, passwords, profile data, roles, claims, tokens, email confirmation, and more.</span></span>

<span data-ttu-id="912b8-109">ユーザーは、Id に格納されているログイン情報を持つアカウントを作成することも、外部ログインプロバイダーを使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="912b8-109">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="912b8-110">サポートされている外部ログインプロバイダーには、 [Facebook、Google、Microsoft アカウント、Twitter](xref:security/authentication/social/index)があります。</span><span class="sxs-lookup"><span data-stu-id="912b8-110">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="912b8-111">[Id ソースコード](https://github.com/dotnet/AspNetCore/tree/master/src/Identity)は、GitHub で入手できます。</span><span class="sxs-lookup"><span data-stu-id="912b8-111">The [Identity source code](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) is available on GitHub.</span></span> <span data-ttu-id="912b8-112">[スキャフォールディング](xref:security/authentication/scaffold-identity)は、id とのテンプレートの対話を確認するために、生成されたファイルを識別して表示します。</span><span class="sxs-lookup"><span data-stu-id="912b8-112">[Scaffold Identity](xref:security/authentication/scaffold-identity) and view the generated files to review the template interaction with Identity.</span></span>

<span data-ttu-id="912b8-113">Id は、通常、ユーザー名、パスワード、およびプロファイルデータを格納するために SQL Server データベースを使用して構成されます。</span><span class="sxs-lookup"><span data-stu-id="912b8-113">Identity is typically configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="912b8-114">別の永続ストアを使用することもできます (たとえば、Azure Table Storage)。</span><span class="sxs-lookup"><span data-stu-id="912b8-114">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="912b8-115">このトピックでは、Identity を使用してユーザーを登録、ログイン、ログアウトする方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="912b8-115">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="912b8-116">注: このテンプレートでは、ユーザー名と電子メールはユーザーに対して同じものとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="912b8-116">Note: the templates treat username and email as the same for users.</span></span> <span data-ttu-id="912b8-117">Id を使用するアプリを作成する方法の詳細については、この記事の最後にある「次のステップ」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="912b8-117">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<span data-ttu-id="912b8-118">[Microsoft id プラットフォーム](/azure/active-directory/develop/)は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="912b8-118">[Microsoft identity platform](/azure/active-directory/develop/) is:</span></span>

* <span data-ttu-id="912b8-119">Azure Active Directory (Azure AD) 開発プラットフォームの進化。</span><span class="sxs-lookup"><span data-stu-id="912b8-119">An evolution of the Azure Active Directory (Azure AD) developer platform.</span></span>
* <span data-ttu-id="912b8-120">ASP.NET Core Id に関連付けられていません。</span><span class="sxs-lookup"><span data-stu-id="912b8-120">Unrelated to ASP.NET Core Identity.</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

<span data-ttu-id="912b8-121">サンプルコード ([ダウンロード方法)](xref:index#how-to-download-a-sample)を[表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample)します。</span><span class="sxs-lookup"><span data-stu-id="912b8-121">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="912b8-122">認証を使用して Web アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="912b8-122">Create a Web app with authentication</span></span>

<span data-ttu-id="912b8-123">個々のユーザーアカウントを使用して ASP.NET Core Web アプリケーションプロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="912b8-123">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="912b8-124">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="912b8-124">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="912b8-125">[**ファイル** > ] [**新しい** > **プロジェクト**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="912b8-125">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="912b8-126">**[ASP.NET Core Web アプリケーション]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="912b8-126">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="912b8-127">プロジェクトに**WebApp1**という名前を付け、プロジェクトのダウンロードと同じ名前空間にします。</span><span class="sxs-lookup"><span data-stu-id="912b8-127">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="912b8-128">**[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="912b8-128">Click **OK**.</span></span>
* <span data-ttu-id="912b8-129">ASP.NET Core **Web アプリケーション**を選択し、[**認証の変更**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="912b8-129">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="912b8-130">**個々のユーザーアカウント**を選択し、[ **OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="912b8-130">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="912b8-131">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="912b8-131">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

<span data-ttu-id="912b8-132">上記のコマンドは、SQLite を使用して Razor web アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="912b8-132">The preceding command creates a Razor web app using SQLite.</span></span> <span data-ttu-id="912b8-133">LocalDB を使用して web アプリを作成するには、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="912b8-133">To create the web app with LocalDB, run the following command:</span></span>

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

<span data-ttu-id="912b8-134">生成されたプロジェクトは、 [ASP.NET Core id](xref:security/authentication/identity)を[Razor クラスライブラリ](xref:razor-pages/ui-class)として提供します。</span><span class="sxs-lookup"><span data-stu-id="912b8-134">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="912b8-135">Identity Razor クラスライブラリは、 `Identity`領域と共にエンドポイントを公開します。</span><span class="sxs-lookup"><span data-stu-id="912b8-135">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="912b8-136">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="912b8-136">For example:</span></span>

* <span data-ttu-id="912b8-137">/アカウント/ログイン</span><span class="sxs-lookup"><span data-stu-id="912b8-137">/Identity/Account/Login</span></span>
* <span data-ttu-id="912b8-138">/アカウント/ログアウト</span><span class="sxs-lookup"><span data-stu-id="912b8-138">/Identity/Account/Logout</span></span>
* <span data-ttu-id="912b8-139">/アカウント/管理</span><span class="sxs-lookup"><span data-stu-id="912b8-139">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="912b8-140">移行を適用する</span><span class="sxs-lookup"><span data-stu-id="912b8-140">Apply migrations</span></span>

<span data-ttu-id="912b8-141">移行を適用してデータベースを初期化します。</span><span class="sxs-lookup"><span data-stu-id="912b8-141">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="912b8-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="912b8-142">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="912b8-143">パッケージマネージャーコンソール (PMC) で次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="912b8-143">Run the following command in the Package Manager Console (PMC):</span></span>

`PM> Update-Database`

# <a name="net-core-cli"></a>[<span data-ttu-id="912b8-144">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="912b8-144">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="912b8-145">SQLite を使用する場合、この手順で移行する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="912b8-145">Migrations are not necessary at this step when using SQLite.</span></span> <span data-ttu-id="912b8-146">LocalDB の場合は、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="912b8-146">For LocalDB, run the following command:</span></span>

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="912b8-147">テストレジスタとログイン</span><span class="sxs-lookup"><span data-stu-id="912b8-147">Test Register and Login</span></span>

<span data-ttu-id="912b8-148">アプリを実行し、ユーザーを登録します。</span><span class="sxs-lookup"><span data-stu-id="912b8-148">Run the app and register a user.</span></span> <span data-ttu-id="912b8-149">画面のサイズによっては、[ナビゲーション] トグルボタンを選択して、**登録**リンクと**ログイン**リンクを表示する必要がある場合があります。</span><span class="sxs-lookup"><span data-stu-id="912b8-149">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="912b8-150">Id サービスを構成する</span><span class="sxs-lookup"><span data-stu-id="912b8-150">Configure Identity services</span></span>

<span data-ttu-id="912b8-151">サービスはに`ConfigureServices`追加されています。</span><span class="sxs-lookup"><span data-stu-id="912b8-151">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="912b8-152">一般的なパターンは、すべての `Add{Service}` メソッドを呼び出した後、すべての `services.Configure{Service}` メソッドを呼び出すことです。</span><span class="sxs-lookup"><span data-stu-id="912b8-152">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=10-99)]

<span data-ttu-id="912b8-153">上の強調表示されたコードは、既定のオプション値を使用して Id を構成します。</span><span class="sxs-lookup"><span data-stu-id="912b8-153">The preceding highlighted code configures Identity with default option values.</span></span> <span data-ttu-id="912b8-154">サービスは、[依存関係の挿入](xref:fundamentals/dependency-injection)によってアプリで使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="912b8-154">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="912b8-155">Id は、を呼び<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>出すことによって有効になります。</span><span class="sxs-lookup"><span data-stu-id="912b8-155">Identity is enabled by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>.</span></span> <span data-ttu-id="912b8-156">`UseAuthentication`認証[ミドルウェア](xref:fundamentals/middleware/index)を要求パイプラインに追加します。</span><span class="sxs-lookup"><span data-stu-id="912b8-156">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

<span data-ttu-id="912b8-157">テンプレートで生成されたアプリは、[承認](xref:security/authorization/secure-data)を使用しません。</span><span class="sxs-lookup"><span data-stu-id="912b8-157">The template-generated app doesn't use [authorization](xref:security/authorization/secure-data).</span></span> <span data-ttu-id="912b8-158">`app.UseAuthorization`は、アプリが承認を追加する正しい順序で追加されるようにするために用意されています。</span><span class="sxs-lookup"><span data-stu-id="912b8-158">`app.UseAuthorization` is included to ensure it's added in the correct order should the app add authorization.</span></span> <span data-ttu-id="912b8-159">`UseRouting`、 `UseAuthentication`、 `UseAuthorization`、および`UseEndpoints`は、前のコードに示されている順序で呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="912b8-159">`UseRouting`, `UseAuthentication`, `UseAuthorization`, and `UseEndpoints` must be called in the order shown in the preceding code.</span></span>

<span data-ttu-id="912b8-160">`IdentityOptions`および`Startup`の詳細については<xref:Microsoft.AspNetCore.Identity.IdentityOptions> 、「」および「[アプリケーションの起動](xref:fundamentals/startup)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="912b8-160">For more information on `IdentityOptions` and `Startup`, see <xref:Microsoft.AspNetCore.Identity.IdentityOptions> and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="912b8-161">スキャフォールディング Register、Login、および LogOut</span><span class="sxs-lookup"><span data-stu-id="912b8-161">Scaffold Register, Login, and LogOut</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="912b8-162">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="912b8-162">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="912b8-163">Register、Login、および LogOut の各ファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="912b8-163">Add the Register, Login, and LogOut files.</span></span> <span data-ttu-id="912b8-164">スキャフォールディング id に従って、このセクションに示されているコードを生成するための[承認命令を含む Razor プロジェクトに](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization)従います。</span><span class="sxs-lookup"><span data-stu-id="912b8-164">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="912b8-165">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="912b8-165">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="912b8-166">**WebApp1**という名前のプロジェクトを作成した場合は、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="912b8-166">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="912b8-167">それ以外の場合は、 `ApplicationDbContext`の正しい名前空間を使用します。</span><span class="sxs-lookup"><span data-stu-id="912b8-167">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="912b8-168">PowerShell では、コマンドの区切り記号としてセミコロンを使用します。</span><span class="sxs-lookup"><span data-stu-id="912b8-168">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="912b8-169">PowerShell を使用する場合は、前の例に示したように、ファイルリスト内のセミコロンをエスケープするか、ファイルリストを二重引用符で囲みます。</span><span class="sxs-lookup"><span data-stu-id="912b8-169">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

<span data-ttu-id="912b8-170">スキャフォールディング Id の詳細については、「[スキャフォールディング identity to a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="912b8-170">For more information on scaffolding Identity, see [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="912b8-171">レジスタの確認</span><span class="sxs-lookup"><span data-stu-id="912b8-171">Examine Register</span></span>

<span data-ttu-id="912b8-172">ユーザーが [**登録**] リンクをクリックする`RegisterModel.OnPostAsync`と、アクションが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="912b8-172">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="912b8-173">ユーザーは、 `_userManager`オブジェクトに対して[createasync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_)によって作成されます。</span><span class="sxs-lookup"><span data-stu-id="912b8-173">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object.</span></span> <span data-ttu-id="912b8-174">`_userManager`は、依存関係の挿入によって提供されます):</span><span class="sxs-lookup"><span data-stu-id="912b8-174">`_userManager` is provided by dependency injection):</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

<span data-ttu-id="912b8-175">ユーザーが正常に作成された場合は、の呼び出しによって`_signInManager.SignInAsync`ユーザーがログインします。</span><span class="sxs-lookup"><span data-stu-id="912b8-175">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="912b8-176">登録時にすぐにログインできないようにする手順については、「[アカウントの確認](xref:security/authentication/accconfirm#prevent-login-at-registration)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="912b8-176">See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="912b8-177">ログイン</span><span class="sxs-lookup"><span data-stu-id="912b8-177">Log in</span></span>

<span data-ttu-id="912b8-178">ログインフォームは次の場合に表示されます。</span><span class="sxs-lookup"><span data-stu-id="912b8-178">The Login form is displayed when:</span></span>

* <span data-ttu-id="912b8-179">**ログイン**リンクが選択されています。</span><span class="sxs-lookup"><span data-stu-id="912b8-179">The **Log in** link is selected.</span></span>
* <span data-ttu-id="912b8-180">ユーザーがアクセスを承認されていない、**または**システムによって認証されていない制限付きページにアクセスしようとしています。</span><span class="sxs-lookup"><span data-stu-id="912b8-180">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="912b8-181">ログインページのフォームが送信されると、 `OnPostAsync`アクションが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="912b8-181">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="912b8-182">`PasswordSignInAsync`は、 `_signInManager` (依存関係の挿入によって提供される) オブジェクトで呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="912b8-182">`PasswordSignInAsync` is called on the `_signInManager` object (provided by dependency injection).</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="912b8-183">基本`Controller`クラスは、コントローラー `User`メソッドからアクセスできるプロパティを公開します。</span><span class="sxs-lookup"><span data-stu-id="912b8-183">The base `Controller` class exposes a `User` property that can be accessed from controller methods.</span></span> <span data-ttu-id="912b8-184">たとえば、列挙`User.Claims`し、承認の決定を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="912b8-184">For instance, you can enumerate `User.Claims` and make authorization decisions.</span></span> <span data-ttu-id="912b8-185">詳細については、「<xref:security/authorization/introduction>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="912b8-185">For more information, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="912b8-186">ログアウト</span><span class="sxs-lookup"><span data-stu-id="912b8-186">Log out</span></span>

<span data-ttu-id="912b8-187">[**ログアウト**すると、アクション`LogoutModel.OnPost`が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="912b8-187">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Logout.cshtml.cs?highlight=36)]

<span data-ttu-id="912b8-188">前のコードでは、ブラウザー `return RedirectToPage();`が新しい要求を実行し、ユーザーの id が更新されるように、コードをリダイレクトにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="912b8-188">In the preceding code, the code `return RedirectToPage();` needs to be a redirect so that the browser performs a new request and the identity for the user gets updated.</span></span>

<span data-ttu-id="912b8-189">[Signoutasync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync)者は、cookie に格納されているユーザーの要求をクリアします。</span><span class="sxs-lookup"><span data-stu-id="912b8-189">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="912b8-190">Post は*Pages/Shared/_LoginPartial*に指定されています。</span><span class="sxs-lookup"><span data-stu-id="912b8-190">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-identity"></a><span data-ttu-id="912b8-191">テスト Id</span><span class="sxs-lookup"><span data-stu-id="912b8-191">Test Identity</span></span>

<span data-ttu-id="912b8-192">既定の web プロジェクトテンプレートでは、ホームページへの匿名アクセスが許可されます。</span><span class="sxs-lookup"><span data-stu-id="912b8-192">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="912b8-193">Id をテストするに[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)は、次のように追加します。</span><span class="sxs-lookup"><span data-stu-id="912b8-193">To test Identity, add [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute):</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="912b8-194">サインインしている場合は、サインアウトします。アプリを実行し、[**プライバシー** ] リンクを選択します。</span><span class="sxs-lookup"><span data-stu-id="912b8-194">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="912b8-195">ログイン ページにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="912b8-195">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="912b8-196">Id の探索</span><span class="sxs-lookup"><span data-stu-id="912b8-196">Explore Identity</span></span>

<span data-ttu-id="912b8-197">Id の詳細については、以下を参照してください。</span><span class="sxs-lookup"><span data-stu-id="912b8-197">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="912b8-198">完全な id UI ソースの作成</span><span class="sxs-lookup"><span data-stu-id="912b8-198">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="912b8-199">各ページのソースを確認し、デバッガーをステップ実行します。</span><span class="sxs-lookup"><span data-stu-id="912b8-199">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a><span data-ttu-id="912b8-200">Id コンポーネント</span><span class="sxs-lookup"><span data-stu-id="912b8-200">Identity Components</span></span>

<span data-ttu-id="912b8-201">すべての Id に依存する NuGet パッケージは、 [ASP.NET Core 共有フレームワーク](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework)に含まれています。</span><span class="sxs-lookup"><span data-stu-id="912b8-201">All the Identity-dependent NuGet packages are included in the [ASP.NET Core shared framework](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span></span>

<span data-ttu-id="912b8-202">Id のプライマリパッケージは[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)です。</span><span class="sxs-lookup"><span data-stu-id="912b8-202">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="912b8-203">このパッケージには ASP.NET Core Id のインターフェイスのコアセットが含まれており`Microsoft.AspNetCore.Identity.EntityFrameworkCore`、によって含まれています。</span><span class="sxs-lookup"><span data-stu-id="912b8-203">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="912b8-204">ASP.NET Core Id への移行</span><span class="sxs-lookup"><span data-stu-id="912b8-204">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="912b8-205">既存の Id ストアを移行する方法の詳細とガイダンスについては、「[認証と id の移行](xref:migration/identity)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="912b8-205">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="912b8-206">パスワードの強度を設定する</span><span class="sxs-lookup"><span data-stu-id="912b8-206">Setting password strength</span></span>

<span data-ttu-id="912b8-207">パスワードの最小要件を設定するサンプルについては、「[構成](#pw)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="912b8-207">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="912b8-208">AddDefaultIdentity と AddIdentity</span><span class="sxs-lookup"><span data-stu-id="912b8-208">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="912b8-209"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*>は ASP.NET Core 2.1 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="912b8-209"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="912b8-210">の`AddDefaultIdentity`呼び出しは、次の呼び出しに似ています。</span><span class="sxs-lookup"><span data-stu-id="912b8-210">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="912b8-211">詳細については、「 [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="912b8-211">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="prevent-publish-of-static-identity-assets"></a><span data-ttu-id="912b8-212">静的 Id 資産の発行を禁止する</span><span class="sxs-lookup"><span data-stu-id="912b8-212">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="912b8-213">静的 Id アセット (Id UI 用のスタイルシートおよび JavaScript ファイル) が web ルートに発行されないよう`ResolveStaticWebAssetsInputsDependsOn`にする`RemoveIdentityAssets`には、次のプロパティとターゲットをアプリのプロジェクトファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="912b8-213">To prevent publishing static Identity assets (stylesheets and JavaScript files for Identity UI) to the web root, add the following `ResolveStaticWebAssetsInputsDependsOn` property and `RemoveIdentityAssets` target to the app's project file:</span></span>

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

## <a name="next-steps"></a><span data-ttu-id="912b8-214">次のステップ</span><span class="sxs-lookup"><span data-stu-id="912b8-214">Next Steps</span></span>

* <span data-ttu-id="912b8-215">SQLite を使用して Id を構成する方法については、 [GitHub の問題](https://github.com/dotnet/AspNetCore.Docs/issues/5131)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="912b8-215">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* [<span data-ttu-id="912b8-216">Identity の構成</span><span class="sxs-lookup"><span data-stu-id="912b8-216">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="912b8-217">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="912b8-217">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="912b8-218">ASP.NET Core Id は、ASP.NET Core アプリにログイン機能を追加するメンバーシップシステムです。</span><span class="sxs-lookup"><span data-stu-id="912b8-218">ASP.NET Core Identity is a membership system that adds login functionality to ASP.NET Core apps.</span></span> <span data-ttu-id="912b8-219">ユーザーは、Id に格納されているログイン情報を持つアカウントを作成することも、外部ログインプロバイダーを使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="912b8-219">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="912b8-220">サポートされている外部ログインプロバイダーには、 [Facebook、Google、Microsoft アカウント、Twitter](xref:security/authentication/social/index)があります。</span><span class="sxs-lookup"><span data-stu-id="912b8-220">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="912b8-221">Id は、SQL Server データベースを使用して、ユーザー名、パスワード、およびプロファイルデータを格納するように構成できます。</span><span class="sxs-lookup"><span data-stu-id="912b8-221">Identity can be configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="912b8-222">別の永続ストアを使用することもできます (たとえば、Azure Table Storage)。</span><span class="sxs-lookup"><span data-stu-id="912b8-222">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="912b8-223">サンプルコード ([ダウンロード方法)](xref:index#how-to-download-a-sample)を[表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/)します。</span><span class="sxs-lookup"><span data-stu-id="912b8-223">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="912b8-224">このトピックでは、Identity を使用してユーザーを登録、ログイン、ログアウトする方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="912b8-224">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="912b8-225">Id を使用するアプリを作成する方法の詳細については、この記事の最後にある「次のステップ」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="912b8-225">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<a name="adi"></a>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="912b8-226">AddDefaultIdentity と AddIdentity</span><span class="sxs-lookup"><span data-stu-id="912b8-226">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="912b8-227"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*>は ASP.NET Core 2.1 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="912b8-227"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="912b8-228">の`AddDefaultIdentity`呼び出しは、次の呼び出しに似ています。</span><span class="sxs-lookup"><span data-stu-id="912b8-228">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="912b8-229">詳細については、「 [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="912b8-229">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="912b8-230">認証を使用して Web アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="912b8-230">Create a Web app with authentication</span></span>

<span data-ttu-id="912b8-231">個々のユーザーアカウントを使用して ASP.NET Core Web アプリケーションプロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="912b8-231">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="912b8-232">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="912b8-232">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="912b8-233">[**ファイル** > ] [**新しい** > **プロジェクト**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="912b8-233">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="912b8-234">**[ASP.NET Core Web アプリケーション]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="912b8-234">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="912b8-235">プロジェクトに**WebApp1**という名前を付け、プロジェクトのダウンロードと同じ名前空間にします。</span><span class="sxs-lookup"><span data-stu-id="912b8-235">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="912b8-236">**[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="912b8-236">Click **OK**.</span></span>
* <span data-ttu-id="912b8-237">ASP.NET Core **Web アプリケーション**を選択し、[**認証の変更**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="912b8-237">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="912b8-238">**個々のユーザーアカウント**を選択し、[ **OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="912b8-238">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="912b8-239">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="912b8-239">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

<span data-ttu-id="912b8-240">生成されたプロジェクトは、 [ASP.NET Core id](xref:security/authentication/identity)を[Razor クラスライブラリ](xref:razor-pages/ui-class)として提供します。</span><span class="sxs-lookup"><span data-stu-id="912b8-240">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="912b8-241">Identity Razor クラスライブラリは、 `Identity`領域と共にエンドポイントを公開します。</span><span class="sxs-lookup"><span data-stu-id="912b8-241">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="912b8-242">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="912b8-242">For example:</span></span>

* <span data-ttu-id="912b8-243">/アカウント/ログイン</span><span class="sxs-lookup"><span data-stu-id="912b8-243">/Identity/Account/Login</span></span>
* <span data-ttu-id="912b8-244">/アカウント/ログアウト</span><span class="sxs-lookup"><span data-stu-id="912b8-244">/Identity/Account/Logout</span></span>
* <span data-ttu-id="912b8-245">/アカウント/管理</span><span class="sxs-lookup"><span data-stu-id="912b8-245">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="912b8-246">移行を適用する</span><span class="sxs-lookup"><span data-stu-id="912b8-246">Apply migrations</span></span>

<span data-ttu-id="912b8-247">移行を適用してデータベースを初期化します。</span><span class="sxs-lookup"><span data-stu-id="912b8-247">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="912b8-248">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="912b8-248">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="912b8-249">パッケージマネージャーコンソール (PMC) で次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="912b8-249">Run the following command in the Package Manager Console (PMC):</span></span>

```powershell
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="912b8-250">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="912b8-250">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="912b8-251">テストレジスタとログイン</span><span class="sxs-lookup"><span data-stu-id="912b8-251">Test Register and Login</span></span>

<span data-ttu-id="912b8-252">アプリを実行し、ユーザーを登録します。</span><span class="sxs-lookup"><span data-stu-id="912b8-252">Run the app and register a user.</span></span> <span data-ttu-id="912b8-253">画面のサイズによっては、[ナビゲーション] トグルボタンを選択して、**登録**リンクと**ログイン**リンクを表示する必要がある場合があります。</span><span class="sxs-lookup"><span data-stu-id="912b8-253">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="912b8-254">Id サービスを構成する</span><span class="sxs-lookup"><span data-stu-id="912b8-254">Configure Identity services</span></span>

<span data-ttu-id="912b8-255">サービスはに`ConfigureServices`追加されています。</span><span class="sxs-lookup"><span data-stu-id="912b8-255">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="912b8-256">一般的なパターンは、すべての `Add{Service}` メソッドを呼び出した後、すべての `services.Configure{Service}` メソッドを呼び出すことです。</span><span class="sxs-lookup"><span data-stu-id="912b8-256">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

<span data-ttu-id="912b8-257">前のコードでは、既定のオプション値を使用して Id を構成しています。</span><span class="sxs-lookup"><span data-stu-id="912b8-257">The preceding code configures Identity with default option values.</span></span> <span data-ttu-id="912b8-258">サービスは、[依存関係の挿入](xref:fundamentals/dependency-injection)によってアプリで使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="912b8-258">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="912b8-259">Id は、 [Useauthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)を呼び出すことによって有効になります。</span><span class="sxs-lookup"><span data-stu-id="912b8-259">Identity is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="912b8-260">`UseAuthentication`認証[ミドルウェア](xref:fundamentals/middleware/index)を要求パイプラインに追加します。</span><span class="sxs-lookup"><span data-stu-id="912b8-260">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

<span data-ttu-id="912b8-261">詳細については、「[クラス](/dotnet/api/microsoft.aspnetcore.identity.identityoptions)と[アプリケーションの起動](xref:fundamentals/startup)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="912b8-261">For more information, see the [IdentityOptions Class](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="912b8-262">スキャフォールディング Register、Login、および LogOut</span><span class="sxs-lookup"><span data-stu-id="912b8-262">Scaffold Register, Login, and LogOut</span></span>

<span data-ttu-id="912b8-263">スキャフォールディング id に従って、このセクションに示されているコードを生成するための[承認命令を含む Razor プロジェクトに](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization)従います。</span><span class="sxs-lookup"><span data-stu-id="912b8-263">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="912b8-264">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="912b8-264">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="912b8-265">Register、Login、および LogOut の各ファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="912b8-265">Add the Register, Login, and LogOut files.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="912b8-266">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="912b8-266">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="912b8-267">**WebApp1**という名前のプロジェクトを作成した場合は、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="912b8-267">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="912b8-268">それ以外の場合は、 `ApplicationDbContext`の正しい名前空間を使用します。</span><span class="sxs-lookup"><span data-stu-id="912b8-268">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="912b8-269">PowerShell では、コマンドの区切り記号としてセミコロンを使用します。</span><span class="sxs-lookup"><span data-stu-id="912b8-269">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="912b8-270">PowerShell を使用する場合は、前の例に示したように、ファイルリスト内のセミコロンをエスケープするか、ファイルリストを二重引用符で囲みます。</span><span class="sxs-lookup"><span data-stu-id="912b8-270">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="912b8-271">レジスタの確認</span><span class="sxs-lookup"><span data-stu-id="912b8-271">Examine Register</span></span>

<span data-ttu-id="912b8-272">ユーザーが [**登録**] リンクをクリックする`RegisterModel.OnPostAsync`と、アクションが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="912b8-272">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="912b8-273">ユーザーは、 `_userManager`オブジェクトに対して[createasync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_)によって作成されます。</span><span class="sxs-lookup"><span data-stu-id="912b8-273">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object.</span></span> <span data-ttu-id="912b8-274">`_userManager`は、依存関係の挿入によって提供されます):</span><span class="sxs-lookup"><span data-stu-id="912b8-274">`_userManager` is provided by dependency injection):</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

<span data-ttu-id="912b8-275">ユーザーが正常に作成された場合は、の呼び出しによって`_signInManager.SignInAsync`ユーザーがログインします。</span><span class="sxs-lookup"><span data-stu-id="912b8-275">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="912b8-276">**注:** 登録時にすぐにログインできないようにする手順については、「[アカウントの確認](xref:security/authentication/accconfirm#prevent-login-at-registration)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="912b8-276">**Note:** See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="912b8-277">ログイン</span><span class="sxs-lookup"><span data-stu-id="912b8-277">Log in</span></span>

<span data-ttu-id="912b8-278">ログインフォームは次の場合に表示されます。</span><span class="sxs-lookup"><span data-stu-id="912b8-278">The Login form is displayed when:</span></span>

* <span data-ttu-id="912b8-279">**ログイン**リンクが選択されています。</span><span class="sxs-lookup"><span data-stu-id="912b8-279">The **Log in** link is selected.</span></span>
* <span data-ttu-id="912b8-280">ユーザーがアクセスを承認されていない、**または**システムによって認証されていない制限付きページにアクセスしようとしています。</span><span class="sxs-lookup"><span data-stu-id="912b8-280">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="912b8-281">ログインページのフォームが送信されると、 `OnPostAsync`アクションが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="912b8-281">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="912b8-282">`PasswordSignInAsync`は、 `_signInManager` (依存関係の挿入によって提供される) オブジェクトで呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="912b8-282">`PasswordSignInAsync` is called on the `_signInManager` object (provided by dependency injection).</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="912b8-283">基本`Controller`クラスは、コントローラー `User`メソッドからアクセスできるプロパティを公開します。</span><span class="sxs-lookup"><span data-stu-id="912b8-283">The base `Controller` class exposes a `User` property that you can access from controller methods.</span></span> <span data-ttu-id="912b8-284">たとえば、列挙`User.Claims`し、承認の決定を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="912b8-284">For instance, you can enumerate `User.Claims` and make authorization decisions.</span></span> <span data-ttu-id="912b8-285">詳細については、「<xref:security/authorization/introduction>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="912b8-285">For more information, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="912b8-286">ログアウト</span><span class="sxs-lookup"><span data-stu-id="912b8-286">Log out</span></span>

<span data-ttu-id="912b8-287">[**ログアウト**すると、アクション`LogoutModel.OnPost`が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="912b8-287">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

<span data-ttu-id="912b8-288">[Signoutasync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync)者は、cookie に格納されているユーザーの要求をクリアします。</span><span class="sxs-lookup"><span data-stu-id="912b8-288">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="912b8-289">Post は*Pages/Shared/_LoginPartial*に指定されています。</span><span class="sxs-lookup"><span data-stu-id="912b8-289">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-identity"></a><span data-ttu-id="912b8-290">テスト Id</span><span class="sxs-lookup"><span data-stu-id="912b8-290">Test Identity</span></span>

<span data-ttu-id="912b8-291">既定の web プロジェクトテンプレートでは、ホームページへの匿名アクセスが許可されます。</span><span class="sxs-lookup"><span data-stu-id="912b8-291">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="912b8-292">Id をテストするに[`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute)は、[プライバシー] ページにを追加します。</span><span class="sxs-lookup"><span data-stu-id="912b8-292">To test Identity, add [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) to the Privacy page.</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="912b8-293">サインインしている場合は、サインアウトします。アプリを実行し、[**プライバシー** ] リンクを選択します。</span><span class="sxs-lookup"><span data-stu-id="912b8-293">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="912b8-294">ログイン ページにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="912b8-294">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="912b8-295">Id の探索</span><span class="sxs-lookup"><span data-stu-id="912b8-295">Explore Identity</span></span>

<span data-ttu-id="912b8-296">Id の詳細については、以下を参照してください。</span><span class="sxs-lookup"><span data-stu-id="912b8-296">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="912b8-297">完全な id UI ソースの作成</span><span class="sxs-lookup"><span data-stu-id="912b8-297">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="912b8-298">各ページのソースを確認し、デバッガーをステップ実行します。</span><span class="sxs-lookup"><span data-stu-id="912b8-298">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a><span data-ttu-id="912b8-299">Id コンポーネント</span><span class="sxs-lookup"><span data-stu-id="912b8-299">Identity Components</span></span>

<span data-ttu-id="912b8-300">すべての Id 依存 NuGet パッケージは、 [AspNetCore メタパッケージ](xref:fundamentals/metapackage-app)に含まれています。</span><span class="sxs-lookup"><span data-stu-id="912b8-300">All the Identity dependent NuGet packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="912b8-301">Id のプライマリパッケージは[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)です。</span><span class="sxs-lookup"><span data-stu-id="912b8-301">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="912b8-302">このパッケージには ASP.NET Core Id のインターフェイスのコアセットが含まれており`Microsoft.AspNetCore.Identity.EntityFrameworkCore`、によって含まれています。</span><span class="sxs-lookup"><span data-stu-id="912b8-302">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="912b8-303">ASP.NET Core Id への移行</span><span class="sxs-lookup"><span data-stu-id="912b8-303">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="912b8-304">既存の Id ストアを移行する方法の詳細とガイダンスについては、「[認証と id の移行](xref:migration/identity)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="912b8-304">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="912b8-305">パスワードの強度を設定する</span><span class="sxs-lookup"><span data-stu-id="912b8-305">Setting password strength</span></span>

<span data-ttu-id="912b8-306">パスワードの最小要件を設定するサンプルについては、「[構成](#pw)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="912b8-306">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="next-steps"></a><span data-ttu-id="912b8-307">次のステップ</span><span class="sxs-lookup"><span data-stu-id="912b8-307">Next Steps</span></span>

* <span data-ttu-id="912b8-308">SQLite を使用して Id を構成する方法については、 [GitHub の問題](https://github.com/dotnet/AspNetCore.Docs/issues/5131)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="912b8-308">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* [<span data-ttu-id="912b8-309">Identity の構成</span><span class="sxs-lookup"><span data-stu-id="912b8-309">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end
