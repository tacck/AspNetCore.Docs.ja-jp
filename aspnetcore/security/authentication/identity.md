---
title: :::no-loc(Identity):::ASP.NET Core の概要
author: rick-anderson
description: :::no-loc(Identity):::ASP.NET Core アプリで使用します。 パスワードの要件 (RequireDigit、RequiredLength、RequiredUniqueChars など) を設定する方法について説明します。
ms.author: riande
ms.date: 7/15/2020
no-loc:
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/authentication/identity
ms.openlocfilehash: 25070e90050db9dca8b003ae782662811096526a
ms.sourcegitcommit: 1b89fc58114a251926abadfd5c69c120f1ba12d8
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2020
ms.locfileid: "87160305"
---
# <a name="introduction-to-no-locidentity-on-aspnet-core"></a><span data-ttu-id="301d1-104">:::no-loc(Identity):::ASP.NET Core の概要</span><span class="sxs-lookup"><span data-stu-id="301d1-104">Introduction to :::no-loc(Identity)::: on ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="301d1-105">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="301d1-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="301d1-106">ASP.NET Core :::no-loc(Identity)::::</span><span class="sxs-lookup"><span data-stu-id="301d1-106">ASP.NET Core :::no-loc(Identity)::::</span></span>

* <span data-ttu-id="301d1-107">は、ユーザーインターフェイス (UI) のログイン機能をサポートする API です。</span><span class="sxs-lookup"><span data-stu-id="301d1-107">Is an API that supports user interface (UI) login functionality.</span></span>
* <span data-ttu-id="301d1-108">ユーザー、パスワード、プロファイルデータ、ロール、要求、トークン、電子メールの確認などを管理します。</span><span class="sxs-lookup"><span data-stu-id="301d1-108">Manages users, passwords, profile data, roles, claims, tokens, email confirmation, and more.</span></span>

<span data-ttu-id="301d1-109">ユーザーは、に格納されているログイン情報を持つアカウントを作成することも、外部ログインプロバイダーを使用することもでき :::no-loc(Identity)::: ます。</span><span class="sxs-lookup"><span data-stu-id="301d1-109">Users can create an account with the login information stored in :::no-loc(Identity)::: or they can use an external login provider.</span></span> <span data-ttu-id="301d1-110">サポートされている外部ログインプロバイダーには、 [Facebook、Google、Microsoft アカウント、Twitter](xref:security/authentication/social/index)があります。</span><span class="sxs-lookup"><span data-stu-id="301d1-110">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

[!INCLUDE[](~/includes/requireAuth.md)]

<span data-ttu-id="301d1-111">[ :::no-loc(Identity)::: ソースコード](https://github.com/dotnet/AspNetCore/tree/master/src/:::no-loc(Identity):::)は GitHub で入手できます。</span><span class="sxs-lookup"><span data-stu-id="301d1-111">The [:::no-loc(Identity)::: source code](https://github.com/dotnet/AspNetCore/tree/master/src/:::no-loc(Identity):::) is available on GitHub.</span></span> <span data-ttu-id="301d1-112">[スキャフォールディング :::no-loc(Identity)::: ](xref:security/authentication/scaffold-identity)生成されたファイルを表示して、テンプレートとの対話を確認し :::no-loc(Identity)::: ます。</span><span class="sxs-lookup"><span data-stu-id="301d1-112">[Scaffold :::no-loc(Identity):::](xref:security/authentication/scaffold-identity) and view the generated files to review the template interaction with :::no-loc(Identity):::.</span></span>

<span data-ttu-id="301d1-113">:::no-loc(Identity):::は、通常、ユーザー名、パスワード、およびプロファイルデータを格納するために SQL Server データベースを使用して構成されます。</span><span class="sxs-lookup"><span data-stu-id="301d1-113">:::no-loc(Identity)::: is typically configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="301d1-114">別の永続ストアを使用することもできます (たとえば、Azure Table Storage)。</span><span class="sxs-lookup"><span data-stu-id="301d1-114">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="301d1-115">このトピックでは、を使用し :::no-loc(Identity)::: てユーザーを登録、ログイン、ログアウトする方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="301d1-115">In this topic, you learn how to use :::no-loc(Identity)::: to register, log in, and log out a user.</span></span> <span data-ttu-id="301d1-116">注: このテンプレートでは、ユーザー名と電子メールはユーザーに対して同じものとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="301d1-116">Note: the templates treat username and email as the same for users.</span></span> <span data-ttu-id="301d1-117">を使用するアプリを作成する方法の詳細について :::no-loc(Identity)::: は、「[次のステップ](#next)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="301d1-117">For more detailed instructions about creating apps that use :::no-loc(Identity):::, see [Next Steps](#next).</span></span>

<span data-ttu-id="301d1-118">[Microsoft id プラットフォーム](/azure/active-directory/develop/)は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="301d1-118">[Microsoft identity platform](/azure/active-directory/develop/) is:</span></span>

* <span data-ttu-id="301d1-119">Azure Active Directory (Azure AD) 開発プラットフォームの進化。</span><span class="sxs-lookup"><span data-stu-id="301d1-119">An evolution of the Azure Active Directory (Azure AD) developer platform.</span></span>
* <span data-ttu-id="301d1-120">ASP.NET Core に関連付けら :::no-loc(Identity)::: れていません。</span><span class="sxs-lookup"><span data-stu-id="301d1-120">Unrelated to ASP.NET Core :::no-loc(Identity):::.</span></span>

[!INCLUDE[](~/includes/:::no-loc(Identity):::Server4.md)]

<span data-ttu-id="301d1-121">サンプルコード ([ダウンロード方法)](xref:index#how-to-download-a-sample)を[表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample)します。</span><span class="sxs-lookup"><span data-stu-id="301d1-121">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="301d1-122">認証を使用して Web アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="301d1-122">Create a Web app with authentication</span></span>

<span data-ttu-id="301d1-123">個々のユーザーアカウントを使用して ASP.NET Core Web アプリケーションプロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="301d1-123">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="301d1-124">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="301d1-124">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="301d1-125">[**ファイル**] [新しいプロジェクト] を選択し > **New** > **Project**ます。</span><span class="sxs-lookup"><span data-stu-id="301d1-125">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="301d1-126">**[ASP.NET Core Web アプリケーション]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="301d1-126">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="301d1-127">プロジェクトに**WebApp1**という名前を付け、プロジェクトのダウンロードと同じ名前空間にします。</span><span class="sxs-lookup"><span data-stu-id="301d1-127">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="301d1-128">**[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="301d1-128">Click **OK**.</span></span>
* <span data-ttu-id="301d1-129">ASP.NET Core **Web アプリケーション**を選択し、[**認証の変更**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="301d1-129">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="301d1-130">**個々のユーザーアカウント**を選択し、[ **OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="301d1-130">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="301d1-131">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="301d1-131">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

<span data-ttu-id="301d1-132">上記のコマンドは、 :::no-loc(Razor)::: SQLite を使用して web アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="301d1-132">The preceding command creates a :::no-loc(Razor)::: web app using SQLite.</span></span> <span data-ttu-id="301d1-133">LocalDB を使用して web アプリを作成するには、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="301d1-133">To create the web app with LocalDB, run the following command:</span></span>

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

<span data-ttu-id="301d1-134">生成されたプロジェクトでは、 [ :::no-loc(Razor)::: クラスライブラリ](xref:razor-pages/ui-class)として[ASP.NET Core :::no-loc(Identity)::: ](xref:security/authentication/identity)が提供されます。</span><span class="sxs-lookup"><span data-stu-id="301d1-134">The generated project provides [ASP.NET Core :::no-loc(Identity):::](xref:security/authentication/identity) as a [:::no-loc(Razor)::: Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="301d1-135">:::no-loc(Identity)::: :::no-loc(Razor)::: クラスライブラリは、領域と共にエンドポイントを公開し `:::no-loc(Identity):::` ます。</span><span class="sxs-lookup"><span data-stu-id="301d1-135">The :::no-loc(Identity)::: :::no-loc(Razor)::: Class Library exposes endpoints with the `:::no-loc(Identity):::` area.</span></span> <span data-ttu-id="301d1-136">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="301d1-136">For example:</span></span>

* <span data-ttu-id="301d1-137">/:::no-loc(Identity):::/Account/Login</span><span class="sxs-lookup"><span data-stu-id="301d1-137">/:::no-loc(Identity):::/Account/Login</span></span>
* <span data-ttu-id="301d1-138">/:::no-loc(Identity):::/アカウント/ログアウト</span><span class="sxs-lookup"><span data-stu-id="301d1-138">/:::no-loc(Identity):::/Account/Logout</span></span>
* <span data-ttu-id="301d1-139">/:::no-loc(Identity):::/アカウント/管理</span><span class="sxs-lookup"><span data-stu-id="301d1-139">/:::no-loc(Identity):::/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="301d1-140">移行を適用する</span><span class="sxs-lookup"><span data-stu-id="301d1-140">Apply migrations</span></span>

<span data-ttu-id="301d1-141">移行を適用してデータベースを初期化します。</span><span class="sxs-lookup"><span data-stu-id="301d1-141">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="301d1-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="301d1-142">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="301d1-143">パッケージマネージャーコンソール (PMC) で次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="301d1-143">Run the following command in the Package Manager Console (PMC):</span></span>

`PM> Update-Database`

# <a name="net-core-cli"></a>[<span data-ttu-id="301d1-144">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="301d1-144">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="301d1-145">SQLite を使用する場合、この手順で移行する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="301d1-145">Migrations are not necessary at this step when using SQLite.</span></span>

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="301d1-146">LocalDB の場合は、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="301d1-146">For LocalDB, run the following command:</span></span>

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="301d1-147">テストレジスタとログイン</span><span class="sxs-lookup"><span data-stu-id="301d1-147">Test Register and Login</span></span>

<span data-ttu-id="301d1-148">アプリを実行し、ユーザーを登録します。</span><span class="sxs-lookup"><span data-stu-id="301d1-148">Run the app and register a user.</span></span> <span data-ttu-id="301d1-149">画面のサイズによっては、[ナビゲーション] トグルボタンを選択して、**登録**リンクと**ログイン**リンクを表示する必要がある場合があります。</span><span class="sxs-lookup"><span data-stu-id="301d1-149">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-no-locidentity-services"></a><span data-ttu-id="301d1-150">サービスの構成 :::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="301d1-150">Configure :::no-loc(Identity)::: services</span></span>

<span data-ttu-id="301d1-151">サービスはに追加されて `ConfigureServices` います。</span><span class="sxs-lookup"><span data-stu-id="301d1-151">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="301d1-152">一般的なパターンは、すべての `Add{Service}` メソッドを呼び出した後、すべての `services.Configure{Service}` メソッドを呼び出すことです。</span><span class="sxs-lookup"><span data-stu-id="301d1-152">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=11-99)]

<span data-ttu-id="301d1-153">前の強調表示されたコードは、 :::no-loc(Identity)::: 既定のオプション値を使用してを構成します。</span><span class="sxs-lookup"><span data-stu-id="301d1-153">The preceding highlighted code configures :::no-loc(Identity)::: with default option values.</span></span> <span data-ttu-id="301d1-154">サービスは、[依存関係の挿入](xref:fundamentals/dependency-injection)によってアプリで使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="301d1-154">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="301d1-155">:::no-loc(Identity):::は、を呼び出すことによって有効になり <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> ます。</span><span class="sxs-lookup"><span data-stu-id="301d1-155">:::no-loc(Identity)::: is enabled by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>.</span></span> <span data-ttu-id="301d1-156">`UseAuthentication`認証[ミドルウェア](xref:fundamentals/middleware/index)を要求パイプラインに追加します。</span><span class="sxs-lookup"><span data-stu-id="301d1-156">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

<span data-ttu-id="301d1-157">テンプレートで生成されたアプリは、[承認](xref:security/authorization/secure-data)を使用しません。</span><span class="sxs-lookup"><span data-stu-id="301d1-157">The template-generated app doesn't use [authorization](xref:security/authorization/secure-data).</span></span> <span data-ttu-id="301d1-158">`app.UseAuthorization`は、アプリが承認を追加する正しい順序で追加されるようにするために用意されています。</span><span class="sxs-lookup"><span data-stu-id="301d1-158">`app.UseAuthorization` is included to ensure it's added in the correct order should the app add authorization.</span></span> <span data-ttu-id="301d1-159">`UseRouting`、 `UseAuthentication` 、 `UseAuthorization` 、および `UseEndpoints` は、前のコードに示されている順序で呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="301d1-159">`UseRouting`, `UseAuthentication`, `UseAuthorization`, and `UseEndpoints` must be called in the order shown in the preceding code.</span></span>

<span data-ttu-id="301d1-160">およびの詳細に `:::no-loc(Identity):::Options` つい `Startup` ては、「」および「アプリケーションの起動」を参照してください <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::Options> 。 [Application Startup](xref:fundamentals/startup)</span><span class="sxs-lookup"><span data-stu-id="301d1-160">For more information on `:::no-loc(Identity):::Options` and `Startup`, see <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::Options> and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-logout-and-registerconfirmation"></a><span data-ttu-id="301d1-161">スキャフォールディング Register、Login、LogOut、および RegisterConfirmation</span><span class="sxs-lookup"><span data-stu-id="301d1-161">Scaffold Register, Login, LogOut, and RegisterConfirmation</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="301d1-162">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="301d1-162">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="301d1-163">、、、およびの各ファイルを追加し `Register` `Login` `LogOut` `RegisterConfirmation` ます。</span><span class="sxs-lookup"><span data-stu-id="301d1-163">Add the `Register`, `Login`, `LogOut`, and `RegisterConfirmation` files.</span></span> <span data-ttu-id="301d1-164">このセクションに示されているコードを生成するための[ :::no-loc(Razor)::: 承認手順を含むプロジェクトに、スキャフォールディング id](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization)に従います。</span><span class="sxs-lookup"><span data-stu-id="301d1-164">Follow the [Scaffold identity into a :::no-loc(Razor)::: project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="301d1-165">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="301d1-165">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="301d1-166">**WebApp1**という名前のプロジェクトを作成した場合は、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="301d1-166">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="301d1-167">それ以外の場合は、の正しい名前空間を使用し `ApplicationDbContext` ます。</span><span class="sxs-lookup"><span data-stu-id="301d1-167">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout;Account.RegisterConfirmation"
```

<span data-ttu-id="301d1-168">PowerShell では、コマンドの区切り記号としてセミコロンを使用します。</span><span class="sxs-lookup"><span data-stu-id="301d1-168">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="301d1-169">PowerShell を使用する場合は、前の例に示したように、ファイルリスト内のセミコロンをエスケープするか、ファイルリストを二重引用符で囲みます。</span><span class="sxs-lookup"><span data-stu-id="301d1-169">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

<span data-ttu-id="301d1-170">スキャフォールディングの詳細につい :::no-loc(Identity)::: ては、「[スキャフォールディング identity to a :::no-loc(Razor)::: project in authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="301d1-170">For more information on scaffolding :::no-loc(Identity):::, see [Scaffold identity into a :::no-loc(Razor)::: project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="301d1-171">レジスタの確認</span><span class="sxs-lookup"><span data-stu-id="301d1-171">Examine Register</span></span>

<span data-ttu-id="301d1-172">ユーザーがページの [**登録**] ボタンをクリックすると、 `Register` `RegisterModel.OnPostAsync` アクションが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="301d1-172">When a user clicks the **Register** button on the `Register` page, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="301d1-173">ユーザーは、オブジェクトに対して[Createasync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_:::no-loc(Identity):::_UserManager_1_CreateAsync__0_System_String_)によって作成され `_userManager` ます。</span><span class="sxs-lookup"><span data-stu-id="301d1-173">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_:::no-loc(Identity):::_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object:</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

<!-- .NET 5 fixes this, see
https://github.com/dotnet/aspnetcore/blob/master/src/:::no-loc(Identity):::/UI/src/Areas/:::no-loc(Identity):::/Pages/V4/Account/RegisterConfirmation.cshtml.cs#L74-L77
-->
[!INCLUDE[](~/includes/disableVer.md)]

### <a name="log-in"></a><span data-ttu-id="301d1-174">ログイン</span><span class="sxs-lookup"><span data-stu-id="301d1-174">Log in</span></span>

<span data-ttu-id="301d1-175">ログインフォームは次の場合に表示されます。</span><span class="sxs-lookup"><span data-stu-id="301d1-175">The Login form is displayed when:</span></span>

* <span data-ttu-id="301d1-176">**ログイン**リンクが選択されています。</span><span class="sxs-lookup"><span data-stu-id="301d1-176">The **Log in** link is selected.</span></span>
* <span data-ttu-id="301d1-177">ユーザーがアクセスを承認されていない、**または**システムによって認証されていない制限付きページにアクセスしようとしています。</span><span class="sxs-lookup"><span data-stu-id="301d1-177">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="301d1-178">ログインページのフォームが送信されると、 `OnPostAsync` アクションが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="301d1-178">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="301d1-179">`PasswordSignInAsync`は、オブジェクトで呼び出され `_signInManager` ます。</span><span class="sxs-lookup"><span data-stu-id="301d1-179">`PasswordSignInAsync` is called on the `_signInManager` object.</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/:::no-loc(Identity):::/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="301d1-180">承認決定を行う方法については、「」を参照してください <xref:security/authorization/introduction> 。</span><span class="sxs-lookup"><span data-stu-id="301d1-180">For information on how to make authorization decisions, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="301d1-181">ログアウト</span><span class="sxs-lookup"><span data-stu-id="301d1-181">Log out</span></span>

<span data-ttu-id="301d1-182">[**ログアウト**すると、アクションが呼び出され `LogoutModel.OnPost` ます。</span><span class="sxs-lookup"><span data-stu-id="301d1-182">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp3/Areas/:::no-loc(Identity):::/Pages/Account/Logout.cshtml.cs?highlight=36)]

<span data-ttu-id="301d1-183">前のコードでは、 `return RedirectToPage();` ブラウザーが新しい要求を実行し、ユーザーの id が更新されるように、コードをリダイレクトにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="301d1-183">In the preceding code, the code `return RedirectToPage();` needs to be a redirect so that the browser performs a new request and the identity for the user gets updated.</span></span>

<span data-ttu-id="301d1-184">[Signoutasync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_:::no-loc(Identity):::_SignInManager_1_SignOutAsync)者は、cookie に格納されているユーザーの要求をクリアします。</span><span class="sxs-lookup"><span data-stu-id="301d1-184">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_:::no-loc(Identity):::_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="301d1-185">Post は*Pages/Shared/_LoginPartial*に指定されています。</span><span class="sxs-lookup"><span data-stu-id="301d1-185">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-cshtml[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-no-locidentity"></a><span data-ttu-id="301d1-186">Test:::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="301d1-186">Test :::no-loc(Identity):::</span></span>

<span data-ttu-id="301d1-187">既定の web プロジェクトテンプレートでは、ホームページへの匿名アクセスが許可されます。</span><span class="sxs-lookup"><span data-stu-id="301d1-187">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="301d1-188">テストするには :::no-loc(Identity)::: 、次のように追加し [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) ます。</span><span class="sxs-lookup"><span data-stu-id="301d1-188">To test :::no-loc(Identity):::, add [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute):</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="301d1-189">サインインしている場合は、サインアウトします。アプリを実行し、[**プライバシー** ] リンクを選択します。</span><span class="sxs-lookup"><span data-stu-id="301d1-189">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="301d1-190">ログイン ページにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="301d1-190">You are redirected to the login page.</span></span>

### <a name="explore-no-locidentity"></a><span data-ttu-id="301d1-191">Tsm:::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="301d1-191">Explore :::no-loc(Identity):::</span></span>

<span data-ttu-id="301d1-192">詳細については、次を参照して :::no-loc(Identity)::: ください。</span><span class="sxs-lookup"><span data-stu-id="301d1-192">To explore :::no-loc(Identity)::: in more detail:</span></span>

* [<span data-ttu-id="301d1-193">完全な id UI ソースの作成</span><span class="sxs-lookup"><span data-stu-id="301d1-193">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="301d1-194">各ページのソースを確認し、デバッガーをステップ実行します。</span><span class="sxs-lookup"><span data-stu-id="301d1-194">Examine the source of each page and step through the debugger.</span></span>

## <a name="no-locidentity-components"></a><span data-ttu-id="301d1-195">:::no-loc(Identity):::コンポーネント</span><span class="sxs-lookup"><span data-stu-id="301d1-195">:::no-loc(Identity)::: Components</span></span>

<span data-ttu-id="301d1-196">すべての :::no-loc(Identity)::: 依存する NuGet パッケージは、 [ASP.NET Core 共有フレームワーク](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework)に含まれています。</span><span class="sxs-lookup"><span data-stu-id="301d1-196">All the :::no-loc(Identity):::-dependent NuGet packages are included in the [ASP.NET Core shared framework](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span></span>

<span data-ttu-id="301d1-197">のプライマリパッケージ :::no-loc(Identity)::: は[AspNetCore です :::no-loc(Identity)::: ](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(Identity):::/)。</span><span class="sxs-lookup"><span data-stu-id="301d1-197">The primary package for :::no-loc(Identity)::: is [Microsoft.AspNetCore.:::no-loc(Identity):::](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(Identity):::/).</span></span> <span data-ttu-id="301d1-198">このパッケージには ASP.NET Core のインターフェイスのコアセットが含まれており、によって含まれてい :::no-loc(Identity)::: `Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore` ます。</span><span class="sxs-lookup"><span data-stu-id="301d1-198">This package contains the core set of interfaces for ASP.NET Core :::no-loc(Identity):::, and is included by `Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-no-locidentity"></a><span data-ttu-id="301d1-199">ASP.NET Core への移行:::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="301d1-199">Migrating to ASP.NET Core :::no-loc(Identity):::</span></span>

<span data-ttu-id="301d1-200">既存のストアの移行に関する詳細とガイダンスについて :::no-loc(Identity)::: は、「[認証と :::no-loc(Identity)::: の移行](xref:migration/identity)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="301d1-200">For more information and guidance on migrating your existing :::no-loc(Identity)::: store, see [Migrate Authentication and :::no-loc(Identity):::](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="301d1-201">パスワードの強度を設定する</span><span class="sxs-lookup"><span data-stu-id="301d1-201">Setting password strength</span></span>

<span data-ttu-id="301d1-202">パスワードの最小要件を設定するサンプルについては、「[構成](#pw)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="301d1-202">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="adddefaultno-locidentity-and-addno-locidentity"></a><span data-ttu-id="301d1-203">AddDefault :::no-loc(Identity)::: と Add:::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="301d1-203">AddDefault:::no-loc(Identity)::: and Add:::no-loc(Identity):::</span></span>

<span data-ttu-id="301d1-204"><xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionUIExtensions.AddDefault:::no-loc(Identity):::*>は ASP.NET Core 2.1 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="301d1-204"><xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionUIExtensions.AddDefault:::no-loc(Identity):::*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="301d1-205">`AddDefault:::no-loc(Identity):::`の呼び出しは、次の呼び出しに似ています。</span><span class="sxs-lookup"><span data-stu-id="301d1-205">Calling `AddDefault:::no-loc(Identity):::` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionExtensions.Add:::no-loc(Identity):::*>
* <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::BuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::BuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="301d1-206">詳細については、「 [Adddefault :::no-loc(Identity)::: source](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/:::no-loc(Identity):::/UI/src/:::no-loc(Identity):::ServiceCollectionUIExtensions.cs#L47-L63) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="301d1-206">See [AddDefault:::no-loc(Identity)::: source](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/:::no-loc(Identity):::/UI/src/:::no-loc(Identity):::ServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="prevent-publish-of-static-no-locidentity-assets"></a><span data-ttu-id="301d1-207">静的アセットの発行を禁止する :::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="301d1-207">Prevent publish of static :::no-loc(Identity)::: assets</span></span>

<span data-ttu-id="301d1-208">静的 :::no-loc(Identity)::: アセット (UI 用のスタイルシートおよび JavaScript ファイル) を web ルートに発行できないようにするには :::no-loc(Identity)::: 、 `ResolveStaticWebAssetsInputsDependsOn` `Remove:::no-loc(Identity):::Assets` アプリケーションのプロジェクトファイルに次のプロパティとターゲットを追加します。</span><span class="sxs-lookup"><span data-stu-id="301d1-208">To prevent publishing static :::no-loc(Identity)::: assets (stylesheets and JavaScript files for :::no-loc(Identity)::: UI) to the web root, add the following `ResolveStaticWebAssetsInputsDependsOn` property and `Remove:::no-loc(Identity):::Assets` target to the app's project file:</span></span>

```xml
<PropertyGroup>
  <ResolveStaticWebAssetsInputsDependsOn>Remove:::no-loc(Identity):::Assets</ResolveStaticWebAssetsInputsDependsOn>
</PropertyGroup>

<Target Name="Remove:::no-loc(Identity):::Assets">
  <ItemGroup>
    <StaticWebAsset Remove="@(StaticWebAsset)" Condition="%(SourceId) == 'Microsoft.AspNetCore.:::no-loc(Identity):::.UI'" />
  </ItemGroup>
</Target>
```

<a name="next"></a>

## <a name="next-steps"></a><span data-ttu-id="301d1-209">次の手順</span><span class="sxs-lookup"><span data-stu-id="301d1-209">Next Steps</span></span>

* <span data-ttu-id="301d1-210">[ASP.NET Core :::no-loc(Identity)::: ソースコード](https://github.com/dotnet/aspnetcore/tree/master/src/:::no-loc(Identity):::)</span><span class="sxs-lookup"><span data-stu-id="301d1-210">[ASP.NET Core :::no-loc(Identity)::: source code](https://github.com/dotnet/aspnetcore/tree/master/src/:::no-loc(Identity):::)</span></span>
* <span data-ttu-id="301d1-211">SQLite を使用したの構成の詳細については、こちらの[GitHub の問題](https://github.com/dotnet/AspNetCore.Docs/issues/5131)を参照してください :::no-loc(Identity)::: 。</span><span class="sxs-lookup"><span data-stu-id="301d1-211">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring :::no-loc(Identity)::: using SQLite.</span></span>
* [<span data-ttu-id="301d1-212">:::no-loc(Identity)::: を構成する</span><span class="sxs-lookup"><span data-stu-id="301d1-212">Configure :::no-loc(Identity):::</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="301d1-213">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="301d1-213">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="301d1-214">ASP.NET Core :::no-loc(Identity)::: は ASP.NET Core アプリにログイン機能を追加するメンバーシップシステムです。</span><span class="sxs-lookup"><span data-stu-id="301d1-214">ASP.NET Core :::no-loc(Identity)::: is a membership system that adds login functionality to ASP.NET Core apps.</span></span> <span data-ttu-id="301d1-215">ユーザーは、に格納されているログイン情報を持つアカウントを作成することも、外部ログインプロバイダーを使用することもでき :::no-loc(Identity)::: ます。</span><span class="sxs-lookup"><span data-stu-id="301d1-215">Users can create an account with the login information stored in :::no-loc(Identity)::: or they can use an external login provider.</span></span> <span data-ttu-id="301d1-216">サポートされている外部ログインプロバイダーには、 [Facebook、Google、Microsoft アカウント、Twitter](xref:security/authentication/social/index)があります。</span><span class="sxs-lookup"><span data-stu-id="301d1-216">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="301d1-217">:::no-loc(Identity):::SQL Server データベースを使用して、ユーザー名、パスワード、およびプロファイルデータを格納するように構成できます。</span><span class="sxs-lookup"><span data-stu-id="301d1-217">:::no-loc(Identity)::: can be configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="301d1-218">別の永続ストアを使用することもできます (たとえば、Azure Table Storage)。</span><span class="sxs-lookup"><span data-stu-id="301d1-218">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="301d1-219">サンプルコード ([ダウンロード方法)](xref:index#how-to-download-a-sample)を[表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-:::no-loc(Identity):::DemoComplete/)します。</span><span class="sxs-lookup"><span data-stu-id="301d1-219">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-:::no-loc(Identity):::DemoComplete/) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="301d1-220">このトピックでは、を使用し :::no-loc(Identity)::: てユーザーを登録、ログイン、ログアウトする方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="301d1-220">In this topic, you learn how to use :::no-loc(Identity)::: to register, log in, and log out a user.</span></span> <span data-ttu-id="301d1-221">を使用するアプリを作成する方法の詳細については :::no-loc(Identity)::: 、この記事の最後にある「次のステップ」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="301d1-221">For more detailed instructions about creating apps that use :::no-loc(Identity):::, see the Next Steps section at the end of this article.</span></span>

<a name="adi"></a>

## <a name="adddefaultno-locidentity-and-addno-locidentity"></a><span data-ttu-id="301d1-222">AddDefault :::no-loc(Identity)::: と Add:::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="301d1-222">AddDefault:::no-loc(Identity)::: and Add:::no-loc(Identity):::</span></span>

<span data-ttu-id="301d1-223"><xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionUIExtensions.AddDefault:::no-loc(Identity):::*>は ASP.NET Core 2.1 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="301d1-223"><xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionUIExtensions.AddDefault:::no-loc(Identity):::*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="301d1-224">`AddDefault:::no-loc(Identity):::`の呼び出しは、次の呼び出しに似ています。</span><span class="sxs-lookup"><span data-stu-id="301d1-224">Calling `AddDefault:::no-loc(Identity):::` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionExtensions.Add:::no-loc(Identity):::*>
* <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::BuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::BuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="301d1-225">詳細については、「 [Adddefault :::no-loc(Identity)::: source](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/:::no-loc(Identity):::/UI/src/:::no-loc(Identity):::ServiceCollectionUIExtensions.cs#L47-L63) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="301d1-225">See [AddDefault:::no-loc(Identity)::: source](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/:::no-loc(Identity):::/UI/src/:::no-loc(Identity):::ServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="301d1-226">認証を使用して Web アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="301d1-226">Create a Web app with authentication</span></span>

<span data-ttu-id="301d1-227">個々のユーザーアカウントを使用して ASP.NET Core Web アプリケーションプロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="301d1-227">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="301d1-228">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="301d1-228">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="301d1-229">[**ファイル**] [新しいプロジェクト] を選択し > **New** > **Project**ます。</span><span class="sxs-lookup"><span data-stu-id="301d1-229">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="301d1-230">**[ASP.NET Core Web アプリケーション]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="301d1-230">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="301d1-231">プロジェクトに**WebApp1**という名前を付け、プロジェクトのダウンロードと同じ名前空間にします。</span><span class="sxs-lookup"><span data-stu-id="301d1-231">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="301d1-232">**[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="301d1-232">Click **OK**.</span></span>
* <span data-ttu-id="301d1-233">ASP.NET Core **Web アプリケーション**を選択し、[**認証の変更**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="301d1-233">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="301d1-234">**個々のユーザーアカウント**を選択し、[ **OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="301d1-234">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="301d1-235">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="301d1-235">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

<span data-ttu-id="301d1-236">生成されたプロジェクトでは、 [ :::no-loc(Razor)::: クラスライブラリ](xref:razor-pages/ui-class)として[ASP.NET Core :::no-loc(Identity)::: ](xref:security/authentication/identity)が提供されます。</span><span class="sxs-lookup"><span data-stu-id="301d1-236">The generated project provides [ASP.NET Core :::no-loc(Identity):::](xref:security/authentication/identity) as a [:::no-loc(Razor)::: Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="301d1-237">:::no-loc(Identity)::: :::no-loc(Razor)::: クラスライブラリは、領域と共にエンドポイントを公開し `:::no-loc(Identity):::` ます。</span><span class="sxs-lookup"><span data-stu-id="301d1-237">The :::no-loc(Identity)::: :::no-loc(Razor)::: Class Library exposes endpoints with the `:::no-loc(Identity):::` area.</span></span> <span data-ttu-id="301d1-238">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="301d1-238">For example:</span></span>

* <span data-ttu-id="301d1-239">/:::no-loc(Identity):::/Account/Login</span><span class="sxs-lookup"><span data-stu-id="301d1-239">/:::no-loc(Identity):::/Account/Login</span></span>
* <span data-ttu-id="301d1-240">/:::no-loc(Identity):::/アカウント/ログアウト</span><span class="sxs-lookup"><span data-stu-id="301d1-240">/:::no-loc(Identity):::/Account/Logout</span></span>
* <span data-ttu-id="301d1-241">/:::no-loc(Identity):::/アカウント/管理</span><span class="sxs-lookup"><span data-stu-id="301d1-241">/:::no-loc(Identity):::/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="301d1-242">移行を適用する</span><span class="sxs-lookup"><span data-stu-id="301d1-242">Apply migrations</span></span>

<span data-ttu-id="301d1-243">移行を適用してデータベースを初期化します。</span><span class="sxs-lookup"><span data-stu-id="301d1-243">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="301d1-244">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="301d1-244">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="301d1-245">パッケージマネージャーコンソール (PMC) で次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="301d1-245">Run the following command in the Package Manager Console (PMC):</span></span>

```powershell
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="301d1-246">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="301d1-246">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="301d1-247">テストレジスタとログイン</span><span class="sxs-lookup"><span data-stu-id="301d1-247">Test Register and Login</span></span>

<span data-ttu-id="301d1-248">アプリを実行し、ユーザーを登録します。</span><span class="sxs-lookup"><span data-stu-id="301d1-248">Run the app and register a user.</span></span> <span data-ttu-id="301d1-249">画面のサイズによっては、[ナビゲーション] トグルボタンを選択して、**登録**リンクと**ログイン**リンクを表示する必要がある場合があります。</span><span class="sxs-lookup"><span data-stu-id="301d1-249">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-no-locidentity-services"></a><span data-ttu-id="301d1-250">サービスの構成 :::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="301d1-250">Configure :::no-loc(Identity)::: services</span></span>

<span data-ttu-id="301d1-251">サービスはに追加されて `ConfigureServices` います。</span><span class="sxs-lookup"><span data-stu-id="301d1-251">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="301d1-252">一般的なパターンは、すべての `Add{Service}` メソッドを呼び出した後、すべての `services.Configure{Service}` メソッドを呼び出すことです。</span><span class="sxs-lookup"><span data-stu-id="301d1-252">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

<span data-ttu-id="301d1-253">前のコードでは、 :::no-loc(Identity)::: 既定のオプション値を使用してを構成します。</span><span class="sxs-lookup"><span data-stu-id="301d1-253">The preceding code configures :::no-loc(Identity)::: with default option values.</span></span> <span data-ttu-id="301d1-254">サービスは、[依存関係の挿入](xref:fundamentals/dependency-injection)によってアプリで使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="301d1-254">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="301d1-255">:::no-loc(Identity):::は、 [Useauthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)を呼び出すことによって有効になります。</span><span class="sxs-lookup"><span data-stu-id="301d1-255">:::no-loc(Identity)::: is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="301d1-256">`UseAuthentication`認証[ミドルウェア](xref:fundamentals/middleware/index)を要求パイプラインに追加します。</span><span class="sxs-lookup"><span data-stu-id="301d1-256">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

<span data-ttu-id="301d1-257">詳細については、「 [ :::no-loc(Identity)::: オプションクラス](/dotnet/api/microsoft.aspnetcore.identity.identityoptions)」と「[アプリケーションの起動](xref:fundamentals/startup)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="301d1-257">For more information, see the [:::no-loc(Identity):::Options Class](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="301d1-258">スキャフォールディング Register、Login、および LogOut</span><span class="sxs-lookup"><span data-stu-id="301d1-258">Scaffold Register, Login, and LogOut</span></span>

<span data-ttu-id="301d1-259">このセクションに示されているコードを生成するための[ :::no-loc(Razor)::: 承認手順を含むプロジェクトに、スキャフォールディング id](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization)に従います。</span><span class="sxs-lookup"><span data-stu-id="301d1-259">Follow the [Scaffold identity into a :::no-loc(Razor)::: project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="301d1-260">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="301d1-260">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="301d1-261">Register、Login、および LogOut の各ファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="301d1-261">Add the Register, Login, and LogOut files.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="301d1-262">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="301d1-262">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="301d1-263">**WebApp1**という名前のプロジェクトを作成した場合は、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="301d1-263">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="301d1-264">それ以外の場合は、の正しい名前空間を使用し `ApplicationDbContext` ます。</span><span class="sxs-lookup"><span data-stu-id="301d1-264">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="301d1-265">PowerShell では、コマンドの区切り記号としてセミコロンを使用します。</span><span class="sxs-lookup"><span data-stu-id="301d1-265">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="301d1-266">PowerShell を使用する場合は、前の例に示したように、ファイルリスト内のセミコロンをエスケープするか、ファイルリストを二重引用符で囲みます。</span><span class="sxs-lookup"><span data-stu-id="301d1-266">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="301d1-267">レジスタの確認</span><span class="sxs-lookup"><span data-stu-id="301d1-267">Examine Register</span></span>

<span data-ttu-id="301d1-268">ユーザーが [**登録**] リンクをクリックすると、 `RegisterModel.OnPostAsync` アクションが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="301d1-268">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="301d1-269">ユーザーは、オブジェクトに対して[Createasync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_:::no-loc(Identity):::_UserManager_1_CreateAsync__0_System_String_)によって作成され `_userManager` ます。</span><span class="sxs-lookup"><span data-stu-id="301d1-269">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_:::no-loc(Identity):::_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object:</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

<span data-ttu-id="301d1-270">ユーザーが正常に作成された場合は、の呼び出しによってユーザーがログインし `_signInManager.SignInAsync` ます。</span><span class="sxs-lookup"><span data-stu-id="301d1-270">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="301d1-271">**注:** 登録時にすぐにログインできないようにする手順については、「[アカウントの確認](xref:security/authentication/accconfirm#prevent-login-at-registration)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="301d1-271">**Note:** See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="301d1-272">ログイン</span><span class="sxs-lookup"><span data-stu-id="301d1-272">Log in</span></span>

<span data-ttu-id="301d1-273">ログインフォームは次の場合に表示されます。</span><span class="sxs-lookup"><span data-stu-id="301d1-273">The Login form is displayed when:</span></span>

* <span data-ttu-id="301d1-274">**ログイン**リンクが選択されています。</span><span class="sxs-lookup"><span data-stu-id="301d1-274">The **Log in** link is selected.</span></span>
* <span data-ttu-id="301d1-275">ユーザーがアクセスを承認されていない、**または**システムによって認証されていない制限付きページにアクセスしようとしています。</span><span class="sxs-lookup"><span data-stu-id="301d1-275">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="301d1-276">ログインページのフォームが送信されると、 `OnPostAsync` アクションが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="301d1-276">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="301d1-277">`PasswordSignInAsync`は、オブジェクトで呼び出され `_signInManager` ます。</span><span class="sxs-lookup"><span data-stu-id="301d1-277">`PasswordSignInAsync` is called on the `_signInManager` object.</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/:::no-loc(Identity):::/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="301d1-278">承認決定を行う方法については、「」を参照してください <xref:security/authorization/introduction> 。</span><span class="sxs-lookup"><span data-stu-id="301d1-278">For information on how to make authorization decisions, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="301d1-279">ログアウト</span><span class="sxs-lookup"><span data-stu-id="301d1-279">Log out</span></span>

<span data-ttu-id="301d1-280">[**ログアウト**すると、アクションが呼び出され `LogoutModel.OnPost` ます。</span><span class="sxs-lookup"><span data-stu-id="301d1-280">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp1/Areas/:::no-loc(Identity):::/Pages/Account/Logout.cshtml.cs)]

<span data-ttu-id="301d1-281">[Signoutasync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_:::no-loc(Identity):::_SignInManager_1_SignOutAsync)者は、cookie に格納されているユーザーの要求をクリアします。</span><span class="sxs-lookup"><span data-stu-id="301d1-281">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_:::no-loc(Identity):::_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="301d1-282">Post は*Pages/Shared/_LoginPartial*に指定されています。</span><span class="sxs-lookup"><span data-stu-id="301d1-282">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-cshtml[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-no-locidentity"></a><span data-ttu-id="301d1-283">Test:::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="301d1-283">Test :::no-loc(Identity):::</span></span>

<span data-ttu-id="301d1-284">既定の web プロジェクトテンプレートでは、ホームページへの匿名アクセスが許可されます。</span><span class="sxs-lookup"><span data-stu-id="301d1-284">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="301d1-285">をテストするに :::no-loc(Identity)::: は、[ [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) プライバシー] ページにを追加します。</span><span class="sxs-lookup"><span data-stu-id="301d1-285">To test :::no-loc(Identity):::, add [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) to the Privacy page.</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="301d1-286">サインインしている場合は、サインアウトします。アプリを実行し、[**プライバシー** ] リンクを選択します。</span><span class="sxs-lookup"><span data-stu-id="301d1-286">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="301d1-287">ログイン ページにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="301d1-287">You are redirected to the login page.</span></span>

### <a name="explore-no-locidentity"></a><span data-ttu-id="301d1-288">Tsm:::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="301d1-288">Explore :::no-loc(Identity):::</span></span>

<span data-ttu-id="301d1-289">詳細については、次を参照して :::no-loc(Identity)::: ください。</span><span class="sxs-lookup"><span data-stu-id="301d1-289">To explore :::no-loc(Identity)::: in more detail:</span></span>

* [<span data-ttu-id="301d1-290">完全な id UI ソースの作成</span><span class="sxs-lookup"><span data-stu-id="301d1-290">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="301d1-291">各ページのソースを確認し、デバッガーをステップ実行します。</span><span class="sxs-lookup"><span data-stu-id="301d1-291">Examine the source of each page and step through the debugger.</span></span>

## <a name="no-locidentity-components"></a><span data-ttu-id="301d1-292">:::no-loc(Identity):::コンポーネント</span><span class="sxs-lookup"><span data-stu-id="301d1-292">:::no-loc(Identity)::: Components</span></span>

<span data-ttu-id="301d1-293">すべての :::no-loc(Identity)::: 依存する NuGet パッケージは、 [AspNetCore メタパッケージ](xref:fundamentals/metapackage-app)に含まれています。</span><span class="sxs-lookup"><span data-stu-id="301d1-293">All the :::no-loc(Identity)::: dependent NuGet packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="301d1-294">のプライマリパッケージ :::no-loc(Identity)::: は[AspNetCore です :::no-loc(Identity)::: ](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(Identity):::/)。</span><span class="sxs-lookup"><span data-stu-id="301d1-294">The primary package for :::no-loc(Identity)::: is [Microsoft.AspNetCore.:::no-loc(Identity):::](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(Identity):::/).</span></span> <span data-ttu-id="301d1-295">このパッケージには ASP.NET Core のインターフェイスのコアセットが含まれており、によって含まれてい :::no-loc(Identity)::: `Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore` ます。</span><span class="sxs-lookup"><span data-stu-id="301d1-295">This package contains the core set of interfaces for ASP.NET Core :::no-loc(Identity):::, and is included by `Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-no-locidentity"></a><span data-ttu-id="301d1-296">ASP.NET Core への移行:::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="301d1-296">Migrating to ASP.NET Core :::no-loc(Identity):::</span></span>

<span data-ttu-id="301d1-297">既存のストアの移行に関する詳細とガイダンスについて :::no-loc(Identity)::: は、「[認証と :::no-loc(Identity)::: の移行](xref:migration/identity)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="301d1-297">For more information and guidance on migrating your existing :::no-loc(Identity)::: store, see [Migrate Authentication and :::no-loc(Identity):::](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="301d1-298">パスワードの強度を設定する</span><span class="sxs-lookup"><span data-stu-id="301d1-298">Setting password strength</span></span>

<span data-ttu-id="301d1-299">パスワードの最小要件を設定するサンプルについては、「[構成](#pw)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="301d1-299">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="next-steps"></a><span data-ttu-id="301d1-300">次の手順</span><span class="sxs-lookup"><span data-stu-id="301d1-300">Next Steps</span></span>

* <span data-ttu-id="301d1-301">SQLite を使用したの構成の詳細については、こちらの[GitHub の問題](https://github.com/dotnet/AspNetCore.Docs/issues/5131)を参照してください :::no-loc(Identity)::: 。</span><span class="sxs-lookup"><span data-stu-id="301d1-301">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring :::no-loc(Identity)::: using SQLite.</span></span>
* [<span data-ttu-id="301d1-302">:::no-loc(Identity)::: を構成する</span><span class="sxs-lookup"><span data-stu-id="301d1-302">Configure :::no-loc(Identity):::</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end
