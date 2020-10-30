---
title: ASP.NET MVC から ASP.NET Core MVC への移行について説明します
author: wadepickett
description: ASP.NET MVC プロジェクトの ASP.NET Core MVC への移行を開始する方法について説明します。
ms.author: wpickett
ms.date: 06/18/2020
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
uid: migration/mvc
ms.openlocfilehash: 226ac6da508378c7b3c81779d38dd2e0840f1fed
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051514"
---
# <a name="migrate-from-aspnet-mvc-to-aspnet-core-mvc"></a><span data-ttu-id="e6b1b-103">ASP.NET MVC から ASP.NET Core MVC への移行</span><span class="sxs-lookup"><span data-stu-id="e6b1b-103">Migrate from ASP.NET MVC to ASP.NET Core MVC</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e6b1b-104">この記事では、ASP.NET MVC プロジェクトの [ASP.NET CORE mvc](xref:mvc/overview)への移行を開始する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-104">This article shows how to start migrating an ASP.NET MVC project to [ASP.NET Core MVC](xref:mvc/overview).</span></span> <span data-ttu-id="e6b1b-105">プロセスでは、ASP.NET MVC からの関連する変更が強調表示されます。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-105">In the process, it highlights related changes from ASP.NET MVC.</span></span>

<span data-ttu-id="e6b1b-106">ASP.NET MVC からの移行は、複数の手順から成るプロセスです。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-106">Migrating from ASP.NET MVC is a multi-step process.</span></span> <span data-ttu-id="e6b1b-107">この記事には、次の内容が含まれます。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-107">This article covers:</span></span>

* <span data-ttu-id="e6b1b-108">初期セットアップ。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-108">Initial setup.</span></span>
* <span data-ttu-id="e6b1b-109">基本的なコントローラーとビュー。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-109">Basic controllers and views.</span></span>
* <span data-ttu-id="e6b1b-110">静的コンテンツ。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-110">Static content.</span></span>
* <span data-ttu-id="e6b1b-111">クライアント側の依存関係。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-111">Client-side dependencies.</span></span>

<span data-ttu-id="e6b1b-112">構成とコードの移行につい :::no-loc(Identity)::: ては、「 [ASP.NET Core への構成の移行](xref:migration/configuration) 」および「 [認証の移行」および「 :::no-loc(Identity)::: ASP.NET Core への移行](xref:migration/identity)」を参照してください</span><span class="sxs-lookup"><span data-stu-id="e6b1b-112">For migrating configuration and :::no-loc(Identity)::: code, see [Migrate configuration to ASP.NET Core](xref:migration/configuration) and [Migrate Authentication and :::no-loc(Identity)::: to ASP.NET Core](xref:migration/identity).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="e6b1b-113">[前提条件]</span><span class="sxs-lookup"><span data-stu-id="e6b1b-113">Prerequisites</span></span>

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs-3.1.md)]

## <a name="create-the-starter-aspnet-mvc-project"></a><span data-ttu-id="e6b1b-114">Starter ASP.NET MVC プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="e6b1b-114">Create the starter ASP.NET MVC project</span></span>

<span data-ttu-id="e6b1b-115">移行するために Visual Studio で ASP.NET MVC プロジェクトの例を作成します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-115">Create an example ASP.NET MVC project in Visual Studio to migrate:</span></span>

1. <span data-ttu-id="e6b1b-116">**[ファイル]** メニューで、 **[新規作成]** > **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-116">From the **File** menu, select **New** > **Project** .</span></span>
1. <span data-ttu-id="e6b1b-117">[ **ASP.NET Web アプリケーション (.NET Framework)** ] を選択し、[ **次へ** ] を選択します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-117">Select **ASP.NET Web Application (.NET Framework)** and then select **Next** .</span></span>
1. <span data-ttu-id="e6b1b-118">名前空間が次の手順で作成した ASP.NET Core プロジェクトと一致するように、プロジェクトに *WebApp1* という名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-118">Name the project *WebApp1* so the namespace matches the ASP.NET Core project created in the next step.</span></span> <span data-ttu-id="e6b1b-119">**［作成］** を選択します</span><span class="sxs-lookup"><span data-stu-id="e6b1b-119">Select **Create** .</span></span>
1. <span data-ttu-id="e6b1b-120">[ **MVC** ] を選択し、[ **作成** ] を選択します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-120">Select **MVC** , and then select **Create** .</span></span>

## <a name="create-the-aspnet-core-project"></a><span data-ttu-id="e6b1b-121">ASP.NET Core プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="e6b1b-121">Create the ASP.NET Core project</span></span>

<span data-ttu-id="e6b1b-122">新しい ASP.NET Core プロジェクトで移行する新しいソリューションを作成します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-122">Create a new solution with a new ASP.NET Core project to migrate to:</span></span>

1. <span data-ttu-id="e6b1b-123">Visual Studio の2番目のインスタンスを起動します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-123">Launch a second instance of Visual Studio.</span></span>
1. <span data-ttu-id="e6b1b-124">**[ファイル]** メニューで、 **[新規作成]** > **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-124">From the **File** menu, select **New** > **Project** .</span></span>
1. <span data-ttu-id="e6b1b-125">**[ASP.NET Core Web アプリケーション]** を選択し、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-125">Select **ASP.NET Core Web Application** and then select **Next** .</span></span>
1. <span data-ttu-id="e6b1b-126">[ **新しいプロジェクトの構成** ] ダイアログで、プロジェクトに *WebApp1* という名前を設定します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-126">In the **Configure your new project** dialog, Name the project *WebApp1* .</span></span>
1. <span data-ttu-id="e6b1b-127">同じプロジェクト名を使用するように、場所を前のプロジェクトとは別のディレクトリに設定します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-127">Set the location to a different directory than the previous project to use the same project name.</span></span> <span data-ttu-id="e6b1b-128">同じ名前空間を使用すると、2つのプロジェクト間でコードを簡単にコピーできるようになります。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-128">Using the same namespace makes it easier to copy code between the two projects.</span></span> <span data-ttu-id="e6b1b-129">**［作成］** を選択します</span><span class="sxs-lookup"><span data-stu-id="e6b1b-129">Select **Create** .</span></span>
1. <span data-ttu-id="e6b1b-130">**[新しい ASP.NET Core Web アプリケーションを作成する]** ダイアログで、 **[.NET Core]** と **[ASP.NET Core 3.1]** が選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-130">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="e6b1b-131">[ **Web アプリケーション (モデルビューコントローラー)** ] プロジェクトテンプレートを選択し、[ **作成** ] を選択します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-131">Select the **Web Application (Model-View-Controller)** project template, and select **Create** .</span></span>

## <a name="configure-the-aspnet-core-site-to-use-mvc"></a><span data-ttu-id="e6b1b-132">MVC を使用するように ASP.NET Core サイトを構成する</span><span class="sxs-lookup"><span data-stu-id="e6b1b-132">Configure the ASP.NET Core site to use MVC</span></span>

<span data-ttu-id="e6b1b-133">ASP.NET Core 3.0 以降のプロジェクトでは、.NET Framework はサポートされているターゲットフレームワークではなくなりました。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-133">In ASP.NET Core 3.0 and later projects, .NET Framework is no longer a supported target framework.</span></span> <span data-ttu-id="e6b1b-134">プロジェクトは .NET Core をターゲットにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-134">Your project must target .NET Core.</span></span> <span data-ttu-id="e6b1b-135">MVC を含む ASP.NET Core 共有フレームワークは、.NET Core ランタイムインストールの一部です。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-135">The ASP.NET Core shared framework, which includes MVC, is part of the .NET Core runtime installation.</span></span> <span data-ttu-id="e6b1b-136">プロジェクト ファイル内で `Microsoft.NET.Sdk.Web` SDK を使用すると、共有フレームワークが自動的に参照されます。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-136">The shared framework is automatically referenced when using the `Microsoft.NET.Sdk.Web` SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="e6b1b-137">詳細については、「 [Framework リファレンス](xref:migration/22-to-30#framework-reference)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-137">For more information, see [Framework reference](xref:migration/22-to-30#framework-reference).</span></span>

<span data-ttu-id="e6b1b-138">ASP.NET Core では、 `Startup` クラスは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-138">In ASP.NET Core, the `Startup` class:</span></span>

* <span data-ttu-id="e6b1b-139">*Global.asax* を置き換えます。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-139">Replaces *Global.asax* .</span></span>
* <span data-ttu-id="e6b1b-140">すべてのアプリスタートアップタスクを処理します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-140">Handles all app startup tasks.</span></span>

<span data-ttu-id="e6b1b-141">詳細については、「<xref:fundamentals/startup>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-141">For more information, see <xref:fundamentals/startup>.</span></span>

<span data-ttu-id="e6b1b-142">ASP.NET Core プロジェクトで、 *Startup.cs* ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-142">In the ASP.NET Core project, open the *Startup.cs* file:</span></span>

[!code-csharp[](mvc/samples/3.x/Startup.cs?highlight=13,30,32&name=snippet)]

<span data-ttu-id="e6b1b-143">ASP.NET Core アプリでは、ミドルウェアを使用してフレームワークの機能を選択する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-143">ASP.NET Core apps must opt in to framework features with middleware.</span></span> <span data-ttu-id="e6b1b-144">テンプレートで生成された以前のコードは、次のサービスとミドルウェアを追加します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-144">The previous template-generated code adds the following services and middleware:</span></span>

* <span data-ttu-id="e6b1b-145"><xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A>拡張メソッドは、コントローラー、API 関連の機能、およびビューの MVC サービスサポートを登録します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-145">The <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A> extension method registers MVC service support for controllers, API-related features, and views.</span></span> <span data-ttu-id="e6b1b-146">MVC サービスの登録オプションの詳細については、「 [mvc サービスの登録](xref:migration/22-to-30#mvc-service-registration)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-146">For more information on MVC service registration options, see [MVC service registration](xref:migration/22-to-30#mvc-service-registration)</span></span>
* <span data-ttu-id="e6b1b-147"><xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A>拡張メソッドは、静的ファイルハンドラーを追加し `Microsoft.AspNetCore.StaticFiles` ます。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-147">The <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> extension method adds the static file handler `Microsoft.AspNetCore.StaticFiles`.</span></span> <span data-ttu-id="e6b1b-148">拡張メソッドは、の `UseStaticFiles` 前に呼び出す必要があり `UseRouting` ます。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-148">The `UseStaticFiles` extension method must be called before `UseRouting`.</span></span> <span data-ttu-id="e6b1b-149">詳細については、「<xref:fundamentals/static-files>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-149">For more information, see <xref:fundamentals/static-files>.</span></span>
* <span data-ttu-id="e6b1b-150"><xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A>拡張メソッドは、ルーティングを追加します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-150">The <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> extension method adds routing.</span></span> <span data-ttu-id="e6b1b-151">詳細については、「<xref:fundamentals/routing>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-151">For more information, see <xref:fundamentals/routing>.</span></span>

<span data-ttu-id="e6b1b-152">この既存の構成には、サンプルの ASP.NET MVC プロジェクトを移行するために必要なものが含まれています。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-152">This existing configuration includes what is needed to migrate the example ASP.NET MVC project.</span></span> <span data-ttu-id="e6b1b-153">ASP.NET Core ミドルウェアオプションの詳細については、「」を参照してください <xref:fundamentals/startup> 。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-153">For more information on ASP.NET Core middleware options, see <xref:fundamentals/startup>.</span></span>

## <a name="migrate-controllers-and-views"></a><span data-ttu-id="e6b1b-154">コントローラーとビューの移行</span><span class="sxs-lookup"><span data-stu-id="e6b1b-154">Migrate controllers and views</span></span>

<span data-ttu-id="e6b1b-155">ASP.NET Core プロジェクトでは、新しい空のコントローラークラスとビュークラスが追加され、移行元の ASP.NET MVC プロジェクトのコントローラーおよびビュークラスと同じ名前を使用してプレースホルダーとして機能するようになります。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-155">In the ASP.NET Core project, a new empty controller class and view class would be added to serve as placeholders using the same names as the controller and view classes in any ASP.NET MVC project to migrate from.</span></span>

<span data-ttu-id="e6b1b-156">ASP.NET Core *WebApp1* プロジェクトには、ASP.NET MVC プロジェクトと同じ名前の最小サンプルコントローラーとビューが既に含まれています。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-156">The ASP.NET Core *WebApp1* project already includes a minimal example controller and view by the same name as the ASP.NET MVC project.</span></span> <span data-ttu-id="e6b1b-157">これらは、ASP.NET MVC *WebApp1* プロジェクトから移行する ASP.NET mvc コントローラーとビューのプレースホルダーとして機能します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-157">So those will serve as placeholders for the ASP.NET MVC controller and views to be migrated from the ASP.NET MVC *WebApp1* project.</span></span>

1. <span data-ttu-id="e6b1b-158">ASP.NET MVC からメソッドをコピーして、 `HomeController` 新しい ASP.NET Core メソッドを置き換え `HomeController` ます。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-158">Copy the methods from the ASP.NET MVC `HomeController` to replace the new ASP.NET Core `HomeController` methods.</span></span> <span data-ttu-id="e6b1b-159">アクションメソッドの戻り値の型を変更する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-159">There's no need to change the return type of the action methods.</span></span> <span data-ttu-id="e6b1b-160">ASP.NET MVC 組み込みテンプレートのコントローラーアクションメソッドの戻り値の型はです <https://docs.microsoft.com/dotnet/api/system.web.mvc.actionresult?view=aspnet-mvc-5.2> 。 ASP.NET CORE mvc では、アクションメソッドは代わりにを返し `IActionResult` ます。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-160">The ASP.NET MVC built-in template's controller action method return type is <https://docs.microsoft.com/dotnet/api/system.web.mvc.actionresult?view=aspnet-mvc-5.2>; in ASP.NET Core MVC, the action methods return `IActionResult` instead.</span></span> <span data-ttu-id="e6b1b-161">`ActionResult` は、`IActionResult` を実装します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-161">`ActionResult` implements `IActionResult`.</span></span>
1. <span data-ttu-id="e6b1b-162">ASP.NET Core プロジェクトで、 *Views/Home* ディレクトリを右クリックし、[既存項目の **追加** ] を選択し > **Existing Item** ます。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-162">In the ASP.NET Core project, right-click the *Views/Home* directory, select **Add** > **Existing Item** .</span></span>
1. <span data-ttu-id="e6b1b-163">[ **既存項目の追加** ] ダイアログで、ASP.NET MVC *WebApp1* プロジェクトの *Views/Home* ディレクトリに移動します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-163">In the **Add Existing Item** dialog, navigate to the ASP.NET MVC *WebApp1* project's *Views/Home* directory.</span></span>
1. <span data-ttu-id="e6b1b-164">[ *バージョン情報* ]、[ *連絡先.* *..* ............................... :::no-loc(Razor)::: **Add**</span><span class="sxs-lookup"><span data-stu-id="e6b1b-164">Select the *About.cshtml* , *Contact.cshtml* , and *Index.cshtml* :::no-loc(Razor)::: view files, then select **Add** , replacing the existing files.</span></span>

<span data-ttu-id="e6b1b-165">詳細については、次のトピックを参照してください。 <xref:mvc/controllers/actions> および <xref:mvc/views/overview></span><span class="sxs-lookup"><span data-stu-id="e6b1b-165">For more information, see <xref:mvc/controllers/actions> and <xref:mvc/views/overview>.</span></span>

## <a name="test-each-method"></a><span data-ttu-id="e6b1b-166">各メソッドをテストする</span><span class="sxs-lookup"><span data-stu-id="e6b1b-166">Test each method</span></span>

<span data-ttu-id="e6b1b-167">各コントローラーエンドポイントはテストできます。ただし、レイアウトとスタイルについては、ドキュメントの後半で説明します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-167">Each controller endpoint can be tested, however, layout and styles are covered later in the document.</span></span>

1. <span data-ttu-id="e6b1b-168">ASP.NET Core アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-168">Run the ASP.NET Core app.</span></span>
1. <span data-ttu-id="e6b1b-169">現在のポート番号を ASP.NET Core プロジェクトで使用されているポート番号に置き換えることによって、実行中の ASP.NET Core アプリのブラウザーから表示されたビューを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-169">Invoke the rendered views from the browser on the running ASP.NET Core app by replacing the current port number with the port number used in the ASP.NET Core project.</span></span> <span data-ttu-id="e6b1b-170">たとえば、「 `https://localhost:44375/home/about` 」のように入力します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-170">For example, `https://localhost:44375/home/about`.</span></span>

## <a name="migrate-static-content"></a><span data-ttu-id="e6b1b-171">静的コンテンツの移行</span><span class="sxs-lookup"><span data-stu-id="e6b1b-171">Migrate static content</span></span>

<span data-ttu-id="e6b1b-172">ASP.NET MVC 5 以前では、静的コンテンツは web プロジェクトのルートディレクトリからホストされており、サーバー側ファイルと混在していました。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-172">In ASP.NET MVC 5 and earlier, static content was hosted from the web project's root directory and was intermixed with server-side files.</span></span> <span data-ttu-id="e6b1b-173">ASP.NET Core では、静的ファイルはプロジェクトの [web ルート](xref:fundamentals/index#web-root) ディレクトリ内に格納されます。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-173">In ASP.NET Core, static files are stored within the project's [web root](xref:fundamentals/index#web-root) directory.</span></span> <span data-ttu-id="e6b1b-174">既定のディレクトリは *{content root}/wwwroot* ですが、変更することができます。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-174">The default directory is *{content root}/wwwroot* , but it can be changed.</span></span> <span data-ttu-id="e6b1b-175">詳細については、「[ASP.NET Core の静的ファイル](xref:fundamentals/static-files#serve-static-files)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-175">For more information, see [Static files in ASP.NET Core](xref:fundamentals/static-files#serve-static-files).</span></span>

<span data-ttu-id="e6b1b-176">ASP.NET MVC *WebApp1* プロジェクトの静的コンテンツを ASP.NET Core *WebApp1* プロジェクトの *wwwroot* ディレクトリにコピーします。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-176">Copy the static content from the ASP.NET MVC *WebApp1* project to the *wwwroot* directory in the ASP.NET Core *WebApp1* project:</span></span>

1. <span data-ttu-id="e6b1b-177">ASP.NET Core プロジェクトで、 *wwwroot* ディレクトリを右クリックし、[既存項目の **追加** ] を選択し > **Existing Item** ます。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-177">In the ASP.NET Core project, right-click the *wwwroot* directory, select **Add** > **Existing Item** .</span></span>
1. <span data-ttu-id="e6b1b-178">[ **既存項目の追加** ] ダイアログで、ASP.NET MVC *WebApp1* プロジェクトに移動します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-178">In the **Add Existing Item** dialog, navigate to the ASP.NET MVC *WebApp1* project.</span></span>
1. <span data-ttu-id="e6b1b-179">*Favicon* ファイルを選択し、[ **追加** ] を選択して、既存のファイルを置き換えます。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-179">Select the *favicon.ico* file, then select **Add** , replacing the existing file.</span></span>

## <a name="migrate-the-layout-files"></a><span data-ttu-id="e6b1b-180">レイアウトファイルを移行する</span><span class="sxs-lookup"><span data-stu-id="e6b1b-180">Migrate the layout files</span></span>

<span data-ttu-id="e6b1b-181">ASP.NET MVC プロジェクトのレイアウトファイルを ASP.NET Core プロジェクトにコピーします。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-181">Copy the ASP.NET MVC project layout files to the ASP.NET Core project:</span></span>

1. <span data-ttu-id="e6b1b-182">ASP.NET Core プロジェクトで、 *Views* ディレクトリを右クリックし、[既存項目の **追加** ] を選択し > **Existing Item** ます。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-182">In the ASP.NET Core project, right-click the *Views* directory, select **Add** > **Existing Item** .</span></span>
1. <span data-ttu-id="e6b1b-183">[ **既存項目の追加** ] ダイアログで、ASP.NET MVC *WebApp1* プロジェクトの *Views* ディレクトリに移動します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-183">In the **Add Existing Item** dialog, navigate to the ASP.NET MVC *WebApp1* project's *Views* directory.</span></span>
1. <span data-ttu-id="e6b1b-184">*_ViewStart* ファイルを選択し、[ **追加** ] を選択します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-184">Select the *_ViewStart.cshtml* file then select **Add** .</span></span>

<span data-ttu-id="e6b1b-185">ASP.NET MVC プロジェクトの共有レイアウトファイルを ASP.NET Core プロジェクトにコピーします。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-185">Copy the ASP.NET MVC project shared layout files to the ASP.NET Core project:</span></span>

1. <span data-ttu-id="e6b1b-186">ASP.NET Core プロジェクトで、 *Views/Shared* ディレクトリを右クリックし、[既存項目の **追加** ] を選択し > **Existing Item** ます。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-186">In the ASP.NET Core project, right-click the *Views/Shared* directory, select **Add** > **Existing Item** .</span></span>
1. <span data-ttu-id="e6b1b-187">[ **既存項目の追加** ] ダイアログで、ASP.NET MVC *WebApp1* プロジェクトの *Views/Shared* ディレクトリに移動します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-187">In the **Add Existing Item** dialog, navigate to the ASP.NET MVC *WebApp1* project's *Views/Shared* directory.</span></span>
1. <span data-ttu-id="e6b1b-188">_Layout の *cshtml* ファイルを選択し、[ **追加** ] を選択して既存のファイルを置き換えます。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-188">Select the *_Layout.cshtml* file, then select **Add** , replacing the existing file.</span></span>

<span data-ttu-id="e6b1b-189">ASP.NET Core プロジェクトで、 *_Layout* ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-189">In the ASP.NET Core project, open the *_Layout.cshtml* file.</span></span> <span data-ttu-id="e6b1b-190">次に示す完成したコードと一致するように、次の変更を行います。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-190">Make the following changes to match the completed code shown below:</span></span>

<span data-ttu-id="e6b1b-191">次の完成したコードに一致するように、ブートストラップ CSS インクルードを更新します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-191">Update the Bootstrap CSS inclusion to match the completed code below:</span></span>

1. <span data-ttu-id="e6b1b-192">`@Styles.Render("~/Content/css")` `<link>` *ブートストラップ* を読み込む要素で置き換えます (下記参照)。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-192">Replace `@Styles.Render("~/Content/css")` with a `<link>` element to load *bootstrap.css* (see below).</span></span>
1. <span data-ttu-id="e6b1b-193">`@Scripts.Render("~/bundles/modernizr")` を削除します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-193">Remove `@Scripts.Render("~/bundles/modernizr")`.</span></span>

<span data-ttu-id="e6b1b-194">ブートストラップ CSS インクルードの完全な置換マークアップ:</span><span class="sxs-lookup"><span data-stu-id="e6b1b-194">The completed replacement markup for Bootstrap CSS inclusion:</span></span>

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

<span data-ttu-id="e6b1b-195">以下の完成したコードに一致するように、jQuery とブートストラップの JavaScript インクルードを更新します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-195">Update the jQuery and Bootstrap JavaScript inclusion to match the completed code below:</span></span>

1. <span data-ttu-id="e6b1b-196">`@Scripts.Render("~/bundles/jquery")`要素で置き換え `<script>` ます (下記参照)。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-196">Replace `@Scripts.Render("~/bundles/jquery")` with a `<script>` element (see below).</span></span>
1. <span data-ttu-id="e6b1b-197">`@Scripts.Render("~/bundles/bootstrap")`要素で置き換え `<script>` ます (下記参照)。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-197">Replace `@Scripts.Render("~/bundles/bootstrap")` with a `<script>` element (see below).</span></span>

<span data-ttu-id="e6b1b-198">JQuery とブートストラップの JavaScript インクルードのために完了した置換マークアップ:</span><span class="sxs-lookup"><span data-stu-id="e6b1b-198">The completed replacement markup for jQuery and Bootstrap JavaScript inclusion:</span></span>

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

<span data-ttu-id="e6b1b-199">更新された *_Layout* ファイルは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-199">The updated *_Layout.cshtml* file is shown below:</span></span>

[!code-cshtml[](mvc/samples/3.x/Views/Shared/_Layout.cshtml?highlight=7-10,40-42)]

<span data-ttu-id="e6b1b-200">ブラウザーでサイトを表示します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-200">View the site in the browser.</span></span> <span data-ttu-id="e6b1b-201">必要なスタイルが設定された状態で表示されます。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-201">It should render with the expected styles in place.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="e6b1b-202">バンドルと縮小を構成する</span><span class="sxs-lookup"><span data-stu-id="e6b1b-202">Configure bundling and minification</span></span>

<span data-ttu-id="e6b1b-203">ASP.NET Core は、 [WebOptimizer](https://github.com/ligershark/WebOptimizer) やその他の類似のライブラリなど、いくつかのオープンソースバンドルおよび縮小ソリューションと互換性があります。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-203">ASP.NET Core is compatible with several open-source bundling and minification solutions such as [WebOptimizer](https://github.com/ligershark/WebOptimizer) and other similar libraries.</span></span> <span data-ttu-id="e6b1b-204">ASP.NET Core は、ネイティブのバンドルと縮小ソリューションを提供しません。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-204">ASP.NET Core doesn't provide a native bundling and minification solution.</span></span> <span data-ttu-id="e6b1b-205">バンドルと縮小の構成の詳細については、「 [バンドルと縮小](xref:client-side/bundling-and-minification)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-205">For information on configuring bundling and minification, see [Bundling and Minification](xref:client-side/bundling-and-minification).</span></span>

## <a name="solve-http-500-errors"></a><span data-ttu-id="e6b1b-206">HTTP 500 エラーを解決する</span><span class="sxs-lookup"><span data-stu-id="e6b1b-206">Solve HTTP 500 errors</span></span>

<span data-ttu-id="e6b1b-207">問題の原因としては、問題の原因に関する情報が含まれていない HTTP 500 のエラーメッセージが表示されることがあります。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-207">There are many problems that can cause an HTTP 500 error message that contains no information on the source of the problem.</span></span> <span data-ttu-id="e6b1b-208">たとえば、 *Views/_ViewImports cshtml* ファイルにプロジェクト内に存在しない名前空間が含まれている場合、HTTP 500 エラーが生成されます。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-208">For example, if the *Views/_ViewImports.cshtml* file contains a namespace that doesn't exist in the project, an HTTP 500 error is generated.</span></span> <span data-ttu-id="e6b1b-209">ASP.NET Core アプリでは、既定で `UseDeveloperExceptionPage` 、拡張機能がに追加され、 `IApplicationBuilder` 環境の *開発* 時に実行されます。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-209">By default in ASP.NET Core apps, the `UseDeveloperExceptionPage` extension is added to the `IApplicationBuilder` and executed when the environment is *Development* .</span></span> <span data-ttu-id="e6b1b-210">詳細については、次のコードを参考にしてください。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-210">This is detailed in the following code:</span></span>

[!code-csharp[](mvc/samples/3.x/Startup.cs?highlight=17-21&name=snippet)]

<span data-ttu-id="e6b1b-211">ASP.NET Core は、未処理の例外を HTTP 500 エラー応答に変換します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-211">ASP.NET Core converts unhandled exceptions into HTTP 500 error responses.</span></span> <span data-ttu-id="e6b1b-212">通常、サーバーに関する機密情報が漏えいするのを防ぐために、エラーの詳細はこれらの応答に含まれていません。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-212">Normally, error details aren't included in these responses to prevent disclosure of potentially sensitive information about the server.</span></span> <span data-ttu-id="e6b1b-213">詳細については、「 [開発者向け例外ページ](xref:fundamentals/error-handling#developer-exception-page)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-213">For more information, see [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page).</span></span>

## <a name="next-steps"></a><span data-ttu-id="e6b1b-214">次の手順</span><span class="sxs-lookup"><span data-stu-id="e6b1b-214">Next steps</span></span>

* <xref:migration/identity>

## <a name="additional-resources"></a><span data-ttu-id="e6b1b-215">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="e6b1b-215">Additional resources</span></span>

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="e6b1b-216">この記事では、ASP.NET MVC プロジェクトの [ASP.NET CORE mvc](xref:mvc/overview) 2.2 への移行を開始する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-216">This article shows how to start migrating an ASP.NET MVC project to [ASP.NET Core MVC](xref:mvc/overview) 2.2.</span></span> <span data-ttu-id="e6b1b-217">このプロセスでは、ASP.NET MVC から変更されたものの多くを強調表示します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-217">In the process, it highlights many of the things that have changed from ASP.NET MVC.</span></span> <span data-ttu-id="e6b1b-218">ASP.NET MVC からの移行は、複数の手順から成るプロセスです。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-218">Migrating from ASP.NET MVC is a multi-step process.</span></span> <span data-ttu-id="e6b1b-219">この記事には、次の内容が含まれます。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-219">This article covers:</span></span>

* <span data-ttu-id="e6b1b-220">初期セットアップ</span><span class="sxs-lookup"><span data-stu-id="e6b1b-220">Initial setup</span></span>
* <span data-ttu-id="e6b1b-221">基本的なコントローラーとビュー</span><span class="sxs-lookup"><span data-stu-id="e6b1b-221">Basic controllers and views</span></span>
* <span data-ttu-id="e6b1b-222">静的コンテンツ</span><span class="sxs-lookup"><span data-stu-id="e6b1b-222">Static content</span></span>
* <span data-ttu-id="e6b1b-223">クライアント側の依存関係。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-223">Client-side dependencies.</span></span>

<span data-ttu-id="e6b1b-224">構成とコードの移行につい :::no-loc(Identity)::: ては、「」および「」を参照してください <xref:migration/configuration> <xref:migration/identity> 。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-224">For migrating configuration and :::no-loc(Identity)::: code, see <xref:migration/configuration> and <xref:migration/identity>.</span></span>

> [!NOTE]
> <span data-ttu-id="e6b1b-225">サンプルのバージョン番号が最新ではない可能性があります。それに応じてプロジェクトを更新してください。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-225">The version numbers in the samples might not be current, update the projects accordingly.</span></span>

## <a name="create-the-starter-aspnet-mvc-project"></a><span data-ttu-id="e6b1b-226">Starter ASP.NET MVC プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="e6b1b-226">Create the starter ASP.NET MVC project</span></span>

<span data-ttu-id="e6b1b-227">アップグレードを示すために、まず ASP.NET MVC アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-227">To demonstrate the upgrade, we'll start by creating an ASP.NET MVC app.</span></span> <span data-ttu-id="e6b1b-228">名前空間が次の手順で作成した ASP.NET Core プロジェクトと一致するように、 *WebApp1* という名前を付けて作成します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-228">Create it with the name *WebApp1* so the namespace matches the ASP.NET Core project created in the next step.</span></span>

![Visual Studio の [新しいプロジェクト] ダイアログ](mvc/_static/new-project.png)

![新しい Web アプリケーションダイアログ: ASP.NET templates パネルで選択された MVC プロジェクトテンプレート](mvc/_static/new-project-select-mvc-template.png)

<span data-ttu-id="e6b1b-231">*省略可能:* ソリューションの名前を *WebApp1* から *Mvc5* に変更します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-231">*Optional:* Change the name of the Solution from *WebApp1* to *Mvc5* .</span></span> <span data-ttu-id="e6b1b-232">Visual Studio に新しいソリューション名 ( *Mvc5* ) が表示されます。これにより、次のプロジェクトからこのプロジェクトを簡単に指定できます。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-232">Visual Studio displays the new solution name ( *Mvc5* ), which makes it easier to tell this project from the next project.</span></span>

## <a name="create-the-aspnet-core-project"></a><span data-ttu-id="e6b1b-233">ASP.NET Core プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="e6b1b-233">Create the ASP.NET Core project</span></span>

<span data-ttu-id="e6b1b-234">2つのプロジェクトの名前空間が一致するように、前のプロジェクト ( *WebApp1* ) と同じ名前の新しい *空* の ASP.NET Core web アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-234">Create a new *empty* ASP.NET Core web app with the same name as the previous project ( *WebApp1* ) so the namespaces in the two projects match.</span></span> <span data-ttu-id="e6b1b-235">同じ名前空間を使用すると、2つのプロジェクト間でコードを簡単にコピーできます。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-235">Having the same namespace makes it easier to copy code between the two projects.</span></span> <span data-ttu-id="e6b1b-236">同じ名前を使用する前のプロジェクトとは別のディレクトリにこのプロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-236">Create this project in a different directory than the previous project to use the same name.</span></span>

![[新しいプロジェクト] ダイアログ](mvc/_static/new_core.png)

![New ASP.NET Web Application dialog: ASP.NET Core テンプレートパネルで空のプロジェクトテンプレートが選択されました](mvc/_static/new-project-select-empty-aspnet5-template.png)

* <span data-ttu-id="e6b1b-239">*省略可能:\*\*Web アプリケーション* プロジェクトテンプレートを使用して、新しい ASP.NET Core アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-239">*Optional:* Create a new ASP.NET Core app using the *Web Application* project template.</span></span> <span data-ttu-id="e6b1b-240">プロジェクトに *WebApp1* という名前を設定し、 **個々のユーザーアカウント** の認証オプションを選択します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-240">Name the project *WebApp1* , and select an authentication option of **Individual User Accounts** .</span></span> <span data-ttu-id="e6b1b-241">このアプリの名前を *FullAspNetCore* に変更します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-241">Rename this app to *FullAspNetCore* .</span></span> <span data-ttu-id="e6b1b-242">このプロジェクトを作成すると、変換の時間が短縮されます。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-242">Creating this project saves time in the conversion.</span></span> <span data-ttu-id="e6b1b-243">最終的な結果は、テンプレートによって生成されるコードで表示できます。また、コードを変換プロジェクトにコピーしたり、テンプレートで生成されたプロジェクトと比較したりすることができます。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-243">The end result can be viewed in the template-generated code, code can be copied to the conversion project, or compared with the template-generated project.</span></span>

## <a name="configure-the-site-to-use-mvc"></a><span data-ttu-id="e6b1b-244">MVC を使用するようにサイトを構成する</span><span class="sxs-lookup"><span data-stu-id="e6b1b-244">Configure the site to use MVC</span></span>

* <span data-ttu-id="e6b1b-245">.NET Core を対象とする場合、 [AspNetCore メタパッケージ](xref:fundamentals/metapackage-app) は既定で参照されます。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-245">When targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) is referenced by default.</span></span> <span data-ttu-id="e6b1b-246">このパッケージには、MVC アプリで一般的に使用されるパッケージが含まれています。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-246">This package contains packages commonly used by MVC apps.</span></span> <span data-ttu-id="e6b1b-247">.NET Framework を対象とする場合は、パッケージ参照をプロジェクトファイルに個別に一覧表示する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-247">If targeting .NET Framework, package references must be listed individually in the project file.</span></span>

<span data-ttu-id="e6b1b-248">`Microsoft.AspNetCore.Mvc` は ASP.NET Core MVC フレームワークです。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-248">`Microsoft.AspNetCore.Mvc` is the ASP.NET Core MVC framework.</span></span> <span data-ttu-id="e6b1b-249">`Microsoft.AspNetCore.StaticFiles` 静的ファイルハンドラーです。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-249">`Microsoft.AspNetCore.StaticFiles` is the static file handler.</span></span> <span data-ttu-id="e6b1b-250">ASP.NET Core アプリは、静的ファイルの提供など、ミドルウェアに対して明示的にオプトインされています。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-250">ASP.NET Core apps explicitly opt in for middleware, such as for serving static files.</span></span> <span data-ttu-id="e6b1b-251">詳しくは、[静的ファイル](xref:fundamentals/static-files)に関するページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-251">For more information, see [Static files](xref:fundamentals/static-files).</span></span>

* <span data-ttu-id="e6b1b-252">*Startup.cs* ファイルを開き、次のコードに一致するようにコードを変更します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-252">Open the *Startup.cs* file and change the code to match the following:</span></span>

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=7,20-25&name=snippet)]

<span data-ttu-id="e6b1b-253"><xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>拡張メソッドは、静的ファイルハンドラーを追加します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-253">The <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> extension method adds the static file handler.</span></span> <span data-ttu-id="e6b1b-254">詳細については、「 [アプリケーションの起動](xref:fundamentals/startup) と [ルーティング](xref:fundamentals/routing)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-254">For more information, see [Application Startup](xref:fundamentals/startup) and [Routing](xref:fundamentals/routing).</span></span>

## <a name="add-a-controller-and-view"></a><span data-ttu-id="e6b1b-255">コントローラーとビューを追加する</span><span class="sxs-lookup"><span data-stu-id="e6b1b-255">Add a controller and view</span></span>

<span data-ttu-id="e6b1b-256">このセクションでは、最小コントローラーとビューを追加して、次のセクションで移行する ASP.NET MVC コントローラーとビューのプレースホルダーとして機能します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-256">In this section, a minimal controller and view are added to serve as placeholders for the ASP.NET MVC controller and views migrated in the next section.</span></span>

* <span data-ttu-id="e6b1b-257">*Controllers* ディレクトリを追加します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-257">Add a *Controllers* directory.</span></span>

* <span data-ttu-id="e6b1b-258">*HomeController.cs* という名前の **コントローラークラス** を *Controllers* ディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-258">Add a **Controller Class** named *HomeController.cs* to the *Controllers* directory.</span></span>

![[新しい項目の追加] ダイアログ](mvc/_static/add_mvc_ctl.png)

* <span data-ttu-id="e6b1b-260">*Views* ディレクトリを追加します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-260">Add a *Views* directory.</span></span>

* <span data-ttu-id="e6b1b-261">*ビュー/ホーム* ディレクトリを追加します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-261">Add a *Views/Home* directory.</span></span>

* <span data-ttu-id="e6b1b-262">*Views/Home* ディレクトリに、 *Index. cshtml* という名前の **:::no-loc(Razor)::: ビュー** を追加します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-262">Add a **:::no-loc(Razor)::: View** named *Index.cshtml* to the *Views/Home* directory.</span></span>

![[新しい項目の追加] ダイアログ](mvc/_static/view.png)

<span data-ttu-id="e6b1b-264">プロジェクトの構造は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-264">The project structure is shown below:</span></span>

![WebApp1 のファイルとディレクトリを示すソリューションエクスプローラー](mvc/_static/project-structure-controller-view.png)

<span data-ttu-id="e6b1b-266">*Views/Home/Index.cshtml* ファイルの内容を次のマークアップに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-266">Replace the contents of the *Views/Home/Index.cshtml* file with the following markup:</span></span>

```html
<h1>Hello world!</h1>
```

<span data-ttu-id="e6b1b-267">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-267">Run the app.</span></span>

![Microsoft Edge で開いている Web アプリ](mvc/_static/hello-world.png)

<span data-ttu-id="e6b1b-269">詳細については、「 [コントローラー](xref:mvc/controllers/actions) と [ビュー](xref:mvc/views/overview)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-269">For more information, see [Controllers](xref:mvc/controllers/actions) and [Views](xref:mvc/views/overview).</span></span>

<span data-ttu-id="e6b1b-270">次の機能を使用するには、例の ASP.NET MVC プロジェクトから ASP.NET Core プロジェクトへの移行が必要です。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-270">The following functionality requires migration from the example ASP.NET MVC project to the ASP.NET Core project:</span></span>

* <span data-ttu-id="e6b1b-271">クライアント側コンテンツ (CSS、フォント、およびスクリプト)</span><span class="sxs-lookup"><span data-stu-id="e6b1b-271">client-side content (CSS, fonts, and scripts)</span></span>

* <span data-ttu-id="e6b1b-272">controllers</span><span class="sxs-lookup"><span data-stu-id="e6b1b-272">controllers</span></span>

* <span data-ttu-id="e6b1b-273">views</span><span class="sxs-lookup"><span data-stu-id="e6b1b-273">views</span></span>

* <span data-ttu-id="e6b1b-274">モデル</span><span class="sxs-lookup"><span data-stu-id="e6b1b-274">models</span></span>

* <span data-ttu-id="e6b1b-275">まとめる</span><span class="sxs-lookup"><span data-stu-id="e6b1b-275">bundling</span></span>

* <span data-ttu-id="e6b1b-276">filters</span><span class="sxs-lookup"><span data-stu-id="e6b1b-276">filters</span></span>

* <span data-ttu-id="e6b1b-277">ログイン/ログアウト :::no-loc(Identity)::: (これは次のチュートリアルで行います)</span><span class="sxs-lookup"><span data-stu-id="e6b1b-277">Log in/out, :::no-loc(Identity)::: (This is done in the next tutorial.)</span></span>

## <a name="controllers-and-views"></a><span data-ttu-id="e6b1b-278">コントローラーとビュー</span><span class="sxs-lookup"><span data-stu-id="e6b1b-278">Controllers and views</span></span>

* <span data-ttu-id="e6b1b-279">ASP.NET MVC の各メソッド `HomeController` を新しいにコピーし `HomeController` ます。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-279">Copy each of the methods from the ASP.NET MVC `HomeController` to the new `HomeController`.</span></span> <span data-ttu-id="e6b1b-280">ASP.NET MVC では、組み込みテンプレートのコントローラーアクションメソッドの戻り値の型はです <https://docs.microsoft.com/dotnet/api/system.web.mvc.actionresult?view=aspnet-mvc-5.2> 。 ASP.NET CORE mvc では、アクションメソッドは代わりにを返し `IActionResult` ます。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-280">In ASP.NET MVC, the built-in template's controller action method return type is <https://docs.microsoft.com/dotnet/api/system.web.mvc.actionresult?view=aspnet-mvc-5.2>; in ASP.NET Core MVC, the action methods return `IActionResult` instead.</span></span> <span data-ttu-id="e6b1b-281">`ActionResult``IActionResult`を実装するので、アクションメソッドの戻り値の型を変更する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-281">`ActionResult` implements `IActionResult`, so there's no need to change the return type of the action methods.</span></span>

* <span data-ttu-id="e6b1b-282">ASP.NET MVC プロジェクトの *About. cshtml* 、 *Contact.* cshtml、および *Index. cshtml* :::no-loc(Razor)::: view ファイルを ASP.NET Core プロジェクトにコピーします。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-282">Copy the *About.cshtml* , *Contact.cshtml* , and *Index.cshtml* :::no-loc(Razor)::: view files from the ASP.NET MVC project to the ASP.NET Core project.</span></span>

## <a name="test-each-method"></a><span data-ttu-id="e6b1b-283">各メソッドをテストする</span><span class="sxs-lookup"><span data-stu-id="e6b1b-283">Test each method</span></span>

<span data-ttu-id="e6b1b-284">レイアウトファイルとスタイルはまだ移行されていないため、表示されたビューには、ビューファイルのコンテンツのみが含まれます。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-284">The layout file and styles have not been migrated yet, so the rendered views only contain the content in the view files.</span></span> <span data-ttu-id="e6b1b-285">ビューとビューに対して生成されたレイアウトファイルは、 `About` `Contact` まだ使用できません。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-285">The layout file generated links for the `About` and `Contact` views will not be available yet.</span></span>

<span data-ttu-id="e6b1b-286">現在のポート番号を ASP.NET core プロジェクトで使用されているポート番号に置き換えることによって、実行中の ASP.NET core アプリのブラウザーから表示されたビューを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-286">Invoke the rendered views from the browser on the running ASP.NET core app by replacing the current port number with the port number used in the ASP.NET core project.</span></span> <span data-ttu-id="e6b1b-287">(例: `https://localhost:44375/home/about`)。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-287">For example: `https://localhost:44375/home/about`.</span></span>

![連絡先ページ](mvc/_static/contact-page.png)

<span data-ttu-id="e6b1b-289">スタイル設定とメニュー項目がないことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-289">Note the lack of styling and menu items.</span></span> <span data-ttu-id="e6b1b-290">スタイルは次のセクションで修正されます。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-290">The styling will be fixed in the next section.</span></span>

## <a name="static-content"></a><span data-ttu-id="e6b1b-291">静的コンテンツ</span><span class="sxs-lookup"><span data-stu-id="e6b1b-291">Static content</span></span>

<span data-ttu-id="e6b1b-292">ASP.NET MVC 5 以前では、静的コンテンツは web プロジェクトのルートからホストされており、サーバー側ファイルと混在していました。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-292">In ASP.NET MVC 5 and earlier, static content was hosted from the root of the web project and was intermixed with server-side files.</span></span> <span data-ttu-id="e6b1b-293">ASP.NET Core では、静的なコンテンツは *wwwroot* ディレクトリでホストされます。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-293">In ASP.NET Core, static content is hosted in the *wwwroot* directory.</span></span> <span data-ttu-id="e6b1b-294">ASP.NET MVC アプリの静的コンテンツを ASP.NET Core プロジェクトの *wwwroot* ディレクトリにコピーします。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-294">Copy the static content from the ASP.NET MVC app to the *wwwroot* directory in the ASP.NET Core project.</span></span> <span data-ttu-id="e6b1b-295">このサンプルの変換の例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-295">In this sample conversion:</span></span>

* <span data-ttu-id="e6b1b-296">ASP.NET MVC プロジェクトの *favicon* ファイルを ASP.NET Core プロジェクトの *wwwroot* ディレクトリにコピーします。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-296">Copy the *favicon.ico* file from the ASP.NET MVC project to the *wwwroot* directory in the ASP.NET Core project.</span></span>

<span data-ttu-id="e6b1b-297">ASP.NET MVC プロジェクトでは、そのスタイルに [ブートストラップ](https://getbootstrap.com/) を使用して、ブートストラップファイルを *Content* および *Scripts* ディレクトリに格納します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-297">The ASP.NET MVC project uses [Bootstrap](https://getbootstrap.com/) for its styling and stores the Bootstrap files in the *Content* and *Scripts* directories.</span></span> <span data-ttu-id="e6b1b-298">ASP.NET MVC プロジェクトを生成したテンプレートは、レイアウトファイル内のブートストラップ ( *Views/Shared/_Layout* ) を参照します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-298">The template, which generated the ASP.NET MVC project, references Bootstrap in the layout file ( *Views/Shared/_Layout.cshtml* ).</span></span> <span data-ttu-id="e6b1b-299">*bootstrap.js* と *ブートストラップの .css* ファイルは、ASP.NET MVC プロジェクトから新しいプロジェクトの *wwwroot* ディレクトリにコピーできます。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-299">The *bootstrap.js* and *bootstrap.css* files could be copied from the ASP.NET MVC project to the *wwwroot* directory in the new project.</span></span> <span data-ttu-id="e6b1b-300">代わりに、このドキュメントでは、次のセクションで、CDNs を使用したブートストラップ (およびその他のクライアント側ライブラリ) のサポートを追加します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-300">Instead, this document adds support for Bootstrap (and other client-side libraries) using CDNs, in the next section.</span></span>

## <a name="migrate-the-layout-file"></a><span data-ttu-id="e6b1b-301">レイアウトファイルを移行する</span><span class="sxs-lookup"><span data-stu-id="e6b1b-301">Migrate the layout file</span></span>

* <span data-ttu-id="e6b1b-302">ASP.NET MVC プロジェクトの *views* ディレクトリの *_ViewStart* ファイルを ASP.NET Core プロジェクトの *views* ディレクトリにコピーします。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-302">Copy the *_ViewStart.cshtml* file from the ASP.NET MVC project's *Views* directory into the ASP.NET Core project's *Views* directory.</span></span> <span data-ttu-id="e6b1b-303">*_ViewStart* ASP.NET Core MVC では、このファイルは変更されていません。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-303">The *_ViewStart.cshtml* file has not changed in ASP.NET Core MVC.</span></span>

* <span data-ttu-id="e6b1b-304">*ビュー/共有* ディレクトリを作成します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-304">Create a *Views/Shared* directory.</span></span>

* <span data-ttu-id="e6b1b-305">*省略可能:\*\*FullAspNetCore* MVC プロジェクトの *views* ディレクトリから、ASP.NET Core プロジェクトの *views* ディレクトリに *_ViewImports* をコピーします。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-305">*Optional:* Copy *_ViewImports.cshtml* from the *FullAspNetCore* MVC project's *Views* directory into the ASP.NET Core project's *Views* directory.</span></span> <span data-ttu-id="e6b1b-306">*_ViewImports* ファイル内の名前空間宣言を削除します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-306">Remove any namespace declaration in the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="e6b1b-307">*_ViewImports* のファイルは、すべてのビューファイルの名前空間を提供し、 [タグヘルパー](xref:mvc/views/tag-helpers/intro)を取り込みます。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-307">The *_ViewImports.cshtml* file provides namespaces for all the view files and brings in [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="e6b1b-308">タグヘルパーは、新しいレイアウトファイルで使用されます。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-308">Tag Helpers are used in the new layout file.</span></span> <span data-ttu-id="e6b1b-309">*_ViewImports* 、ASP.NET Core の新しいファイルです。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-309">The *_ViewImports.cshtml* file is new for ASP.NET Core.</span></span>

* <span data-ttu-id="e6b1b-310">ASP.NET MVC プロジェクトの *views/shared* ディレクトリにある *_Layout* ファイルを、ASP.NET Core プロジェクトの *views/shared* ディレクトリにコピーします。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-310">Copy the *_Layout.cshtml* file from the ASP.NET MVC project's *Views/Shared* directory into the ASP.NET Core project's *Views/Shared* directory.</span></span>

<span data-ttu-id="e6b1b-311">*_Layout* ファイルを開き、次のように変更します (完成したコードは次のようになります)。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-311">Open *_Layout.cshtml* file and make the following changes (the completed code is shown below):</span></span>

* <span data-ttu-id="e6b1b-312">`@Styles.Render("~/Content/css")` `<link>` *ブートストラップ* を読み込む要素で置き換えます (下記参照)。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-312">Replace `@Styles.Render("~/Content/css")` with a `<link>` element to load *bootstrap.css* (see below).</span></span>

* <span data-ttu-id="e6b1b-313">`@Scripts.Render("~/bundles/modernizr")` を削除します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-313">Remove `@Scripts.Render("~/bundles/modernizr")`.</span></span>

* <span data-ttu-id="e6b1b-314">行をコメントアウト `@Html.Partial("_LoginPartial")` します (行をで囲み `@*...*@` ます)。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-314">Comment out the `@Html.Partial("_LoginPartial")` line (surround the line with `@*...*@`).</span></span> <span data-ttu-id="e6b1b-315">詳細については、「[認証と :::no-loc(Identity)::: ASP.NET Core への移行](xref:migration/identity)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-315">For more information, see [Migrate Authentication and :::no-loc(Identity)::: to ASP.NET Core](xref:migration/identity)</span></span>

* <span data-ttu-id="e6b1b-316">`@Scripts.Render("~/bundles/jquery")`要素で置き換え `<script>` ます (下記参照)。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-316">Replace `@Scripts.Render("~/bundles/jquery")` with a `<script>` element (see below).</span></span>

* <span data-ttu-id="e6b1b-317">`@Scripts.Render("~/bundles/bootstrap")`要素で置き換え `<script>` ます (下記参照)。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-317">Replace `@Scripts.Render("~/bundles/bootstrap")` with a `<script>` element (see below).</span></span>

<span data-ttu-id="e6b1b-318">ブートストラップ CSS インクルードの置換マークアップ:</span><span class="sxs-lookup"><span data-stu-id="e6b1b-318">The replacement markup for Bootstrap CSS inclusion:</span></span>

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

<span data-ttu-id="e6b1b-319">JQuery および Bootstrap JavaScript インクルードの置換マークアップ:</span><span class="sxs-lookup"><span data-stu-id="e6b1b-319">The replacement markup for jQuery and Bootstrap JavaScript inclusion:</span></span>

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

<span data-ttu-id="e6b1b-320">更新された *_Layout* ファイルは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-320">The updated *_Layout.cshtml* file is shown below:</span></span>

[!code-cshtml[](mvc/samples/2.x/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

<span data-ttu-id="e6b1b-321">ブラウザーでサイトを表示します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-321">View the site in the browser.</span></span> <span data-ttu-id="e6b1b-322">これで、必要なスタイルが適切に読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-322">It should now load correctly, with the expected styles in place.</span></span>

* <span data-ttu-id="e6b1b-323">*省略可能:* 新しいレイアウトファイルを使用してみてください。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-323">*Optional:* Try using the new layout file.</span></span> <span data-ttu-id="e6b1b-324">*FullAspNetCore* プロジェクトからレイアウトファイルをコピーします。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-324">Copy the layout file from the *FullAspNetCore* project.</span></span> <span data-ttu-id="e6b1b-325">新しいレイアウトファイルは [タグヘルパー](xref:mvc/views/tag-helpers/intro) を使用し、その他の機能強化が行われています。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-325">The new layout file uses [Tag Helpers](xref:mvc/views/tag-helpers/intro) and has other improvements.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="e6b1b-326">バンドルと縮小を構成する</span><span class="sxs-lookup"><span data-stu-id="e6b1b-326">Configure bundling and minification</span></span>

<span data-ttu-id="e6b1b-327">バンドルと縮小を構成する方法の詳細については、「 [バンドルと縮小](xref:client-side/bundling-and-minification)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-327">For information about how to configure bundling and minification, see [Bundling and Minification](xref:client-side/bundling-and-minification).</span></span>

## <a name="solve-http-500-errors"></a><span data-ttu-id="e6b1b-328">HTTP 500 エラーを解決する</span><span class="sxs-lookup"><span data-stu-id="e6b1b-328">Solve HTTP 500 errors</span></span>

<span data-ttu-id="e6b1b-329">問題の原因として、問題の原因に関する情報が含まれていない HTTP 500 エラーメッセージが発生することがあります。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-329">There are many problems that can cause an HTTP 500 error messages that contain no information on the source of the problem.</span></span> <span data-ttu-id="e6b1b-330">たとえば、 *Views/_ViewImports cshtml* ファイルに、プロジェクトに存在しない名前空間が含まれている場合、HTTP 500 エラーが生成されます。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-330">For example, if the *Views/_ViewImports.cshtml* file contains a namespace that doesn't exist in the project, a HTTP 500 error is generated.</span></span> <span data-ttu-id="e6b1b-331">ASP.NET Core アプリでは、既定で `UseDeveloperExceptionPage` 、拡張機能がに追加され、 `IApplicationBuilder` 構成が *開発* されるときに実行されます。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-331">By default in ASP.NET Core apps, the `UseDeveloperExceptionPage` extension is added to the `IApplicationBuilder` and executed when the configuration is *Development* .</span></span> <span data-ttu-id="e6b1b-332">次のコードの例を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-332">See an example in the following code:</span></span>

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=11-15&name=snippet)]

<span data-ttu-id="e6b1b-333">ASP.NET Core は、未処理の例外を HTTP 500 エラー応答に変換します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-333">ASP.NET Core converts unhandled exceptions into HTTP 500 error responses.</span></span> <span data-ttu-id="e6b1b-334">通常、サーバーに関する機密情報が漏えいするのを防ぐために、エラーの詳細はこれらの応答に含まれていません。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-334">Normally, error details aren't included in these responses to prevent disclosure of potentially sensitive information about the server.</span></span> <span data-ttu-id="e6b1b-335">詳細については、「 [開発者向け例外ページ](xref:fundamentals/error-handling#developer-exception-page)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-335">For more information, see [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e6b1b-336">その他の資料</span><span class="sxs-lookup"><span data-stu-id="e6b1b-336">Additional resources</span></span>

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>

::: moniker-end

::: moniker range="<= aspnetcore-2.1"

<span data-ttu-id="e6b1b-337">この記事では、ASP.NET MVC プロジェクトの [ASP.NET CORE mvc](xref:mvc/overview) 2.1 への移行を開始する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-337">This article shows how to start migrating an ASP.NET MVC project to [ASP.NET Core MVC](xref:mvc/overview) 2.1.</span></span> <span data-ttu-id="e6b1b-338">このプロセスでは、ASP.NET MVC から変更されたものの多くを強調表示します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-338">In the process, it highlights many of the things that have changed from ASP.NET MVC.</span></span> <span data-ttu-id="e6b1b-339">ASP.NET MVC からの移行は、複数の手順から成るプロセスです。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-339">Migrating from ASP.NET MVC is a multi-step process.</span></span> <span data-ttu-id="e6b1b-340">この記事には、次の内容が含まれます。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-340">This article covers:</span></span>

* <span data-ttu-id="e6b1b-341">初期セットアップ</span><span class="sxs-lookup"><span data-stu-id="e6b1b-341">Initial setup</span></span>
* <span data-ttu-id="e6b1b-342">基本的なコントローラーとビュー</span><span class="sxs-lookup"><span data-stu-id="e6b1b-342">Basic controllers and views</span></span>
* <span data-ttu-id="e6b1b-343">静的コンテンツ</span><span class="sxs-lookup"><span data-stu-id="e6b1b-343">Static content</span></span>
* <span data-ttu-id="e6b1b-344">クライアント側の依存関係。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-344">Client-side dependencies.</span></span>

<span data-ttu-id="e6b1b-345">構成とコードの移行につい :::no-loc(Identity)::: ては、「 [ASP.NET Core への構成の移行](xref:migration/configuration) 」および「 [認証の移行」および「 :::no-loc(Identity)::: ASP.NET Core への移行](xref:migration/identity)」を参照してください</span><span class="sxs-lookup"><span data-stu-id="e6b1b-345">For migrating configuration and :::no-loc(Identity)::: code, see [Migrate configuration to ASP.NET Core](xref:migration/configuration) and [Migrate Authentication and :::no-loc(Identity)::: to ASP.NET Core](xref:migration/identity).</span></span>

> [!NOTE]
> <span data-ttu-id="e6b1b-346">サンプルのバージョン番号が最新ではない可能性があります。それに応じてプロジェクトを更新してください。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-346">The version numbers in the samples might not be current, update the projects accordingly.</span></span>

## <a name="create-the-starter-aspnet-mvc-project"></a><span data-ttu-id="e6b1b-347">Starter ASP.NET MVC プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="e6b1b-347">Create the starter ASP.NET MVC project</span></span>

<span data-ttu-id="e6b1b-348">アップグレードを示すために、まず ASP.NET MVC アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-348">To demonstrate the upgrade, we'll start by creating a ASP.NET MVC app.</span></span> <span data-ttu-id="e6b1b-349">名前空間が次の手順で作成した ASP.NET Core プロジェクトと一致するように、 *WebApp1* という名前を付けて作成します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-349">Create it with the name *WebApp1* so the namespace matches the ASP.NET Core project created in the next step.</span></span>

![Visual Studio の [新しいプロジェクト] ダイアログ](mvc/_static/new-project.png)

![新しい Web アプリケーションダイアログ: ASP.NET templates パネルで選択された MVC プロジェクトテンプレート](mvc/_static/new-project-select-mvc-template.png)

<span data-ttu-id="e6b1b-352">*省略可能:* ソリューションの名前を *WebApp1* から *Mvc5* に変更します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-352">*Optional:* Change the name of the Solution from *WebApp1* to *Mvc5* .</span></span> <span data-ttu-id="e6b1b-353">Visual Studio に新しいソリューション名 ( *Mvc5* ) が表示されます。これにより、次のプロジェクトからこのプロジェクトを簡単に指定できます。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-353">Visual Studio displays the new solution name ( *Mvc5* ), which makes it easier to tell this project from the next project.</span></span>

## <a name="create-the-aspnet-core-project"></a><span data-ttu-id="e6b1b-354">ASP.NET Core プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="e6b1b-354">Create the ASP.NET Core project</span></span>

<span data-ttu-id="e6b1b-355">2つのプロジェクトの名前空間が一致するように、前のプロジェクト ( *WebApp1* ) と同じ名前の新しい *空* の ASP.NET Core web アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-355">Create a new *empty* ASP.NET Core web app with the same name as the previous project ( *WebApp1* ) so the namespaces in the two projects match.</span></span> <span data-ttu-id="e6b1b-356">同じ名前空間を使用すると、2つのプロジェクト間でコードを簡単にコピーできます。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-356">Having the same namespace makes it easier to copy code between the two projects.</span></span> <span data-ttu-id="e6b1b-357">同じ名前を使用する前のプロジェクトとは別のディレクトリにこのプロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-357">Create this project in a different directory than the previous project to use the same name.</span></span>

![[新しいプロジェクト] ダイアログ](mvc/_static/new_core.png)

![New ASP.NET Web Application dialog: ASP.NET Core テンプレートパネルで空のプロジェクトテンプレートが選択されました](mvc/_static/new-project-select-empty-aspnet5-template.png)

* <span data-ttu-id="e6b1b-360">*省略可能:\*\*Web アプリケーション* プロジェクトテンプレートを使用して、新しい ASP.NET Core アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-360">*Optional:* Create a new ASP.NET Core app using the *Web Application* project template.</span></span> <span data-ttu-id="e6b1b-361">プロジェクトに *WebApp1* という名前を設定し、 **個々のユーザーアカウント** の認証オプションを選択します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-361">Name the project *WebApp1* , and select an authentication option of **Individual User Accounts** .</span></span> <span data-ttu-id="e6b1b-362">このアプリの名前を *FullAspNetCore* に変更します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-362">Rename this app to *FullAspNetCore* .</span></span> <span data-ttu-id="e6b1b-363">このプロジェクトを作成すると、変換の時間が短縮されます。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-363">Creating this project saves time in the conversion.</span></span> <span data-ttu-id="e6b1b-364">最終的な結果は、テンプレートによって生成されるコードで表示できます。また、コードを変換プロジェクトにコピーしたり、テンプレートで生成されたプロジェクトと比較したりすることができます。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-364">The end result can be viewed in the template-generated code, code can be copied to the conversion project, or compared with the template-generated project.</span></span>

## <a name="configure-the-site-to-use-mvc"></a><span data-ttu-id="e6b1b-365">MVC を使用するようにサイトを構成する</span><span class="sxs-lookup"><span data-stu-id="e6b1b-365">Configure the site to use MVC</span></span>

* <span data-ttu-id="e6b1b-366">.NET Core を対象とする場合、 [AspNetCore メタパッケージ](xref:fundamentals/metapackage-app) は既定で参照されます。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-366">When targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) is referenced by default.</span></span> <span data-ttu-id="e6b1b-367">このパッケージには、MVC アプリで一般的に使用されるパッケージが含まれています。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-367">This package contains packages commonly used by MVC apps.</span></span> <span data-ttu-id="e6b1b-368">.NET Framework を対象とする場合は、パッケージ参照をプロジェクトファイルに個別に一覧表示する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-368">If targeting .NET Framework, package references must be listed individually in the project file.</span></span>

<span data-ttu-id="e6b1b-369">`Microsoft.AspNetCore.Mvc` は ASP.NET Core MVC フレームワークです。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-369">`Microsoft.AspNetCore.Mvc` is the ASP.NET Core MVC framework.</span></span> <span data-ttu-id="e6b1b-370">`Microsoft.AspNetCore.StaticFiles` 静的ファイルハンドラーです。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-370">`Microsoft.AspNetCore.StaticFiles` is the static file handler.</span></span> <span data-ttu-id="e6b1b-371">ASP.NET Core アプリは、静的ファイルの提供など、ミドルウェアに対して明示的にオプトインされています。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-371">ASP.NET Core apps explicitly opt in for middleware, such as for serving static files.</span></span> <span data-ttu-id="e6b1b-372">詳しくは、[静的ファイル](xref:fundamentals/static-files)に関するページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-372">For more information, see [Static files](xref:fundamentals/static-files).</span></span>

* <span data-ttu-id="e6b1b-373">*Startup.cs* ファイルを開き、次のコードに一致するようにコードを変更します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-373">Open the *Startup.cs* file and change the code to match the following:</span></span>

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=7,20-25&name=snippet)]

<span data-ttu-id="e6b1b-374"><xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>拡張メソッドは、静的ファイルハンドラーを追加します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-374">The <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> extension method adds the static file handler.</span></span> <span data-ttu-id="e6b1b-375">`UseMvc`拡張メソッドは、ルーティングを追加します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-375">The `UseMvc` extension method adds routing.</span></span> <span data-ttu-id="e6b1b-376">詳細については、「 [アプリケーションの起動](xref:fundamentals/startup) と [ルーティング](xref:fundamentals/routing)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-376">For more information, see [Application Startup](xref:fundamentals/startup) and [Routing](xref:fundamentals/routing).</span></span>

## <a name="add-a-controller-and-view"></a><span data-ttu-id="e6b1b-377">コントローラーとビューを追加する</span><span class="sxs-lookup"><span data-stu-id="e6b1b-377">Add a controller and view</span></span>

<span data-ttu-id="e6b1b-378">このセクションでは、最小コントローラーとビューを追加して、次のセクションで移行する ASP.NET MVC コントローラーとビューのプレースホルダーとして機能します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-378">In this section, a minimal controller and view are added to serve as placeholders for the ASP.NET MVC controller and views migrated in the next section.</span></span>

* <span data-ttu-id="e6b1b-379">*Controllers* ディレクトリを追加します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-379">Add a *Controllers* directory.</span></span>

* <span data-ttu-id="e6b1b-380">*HomeController.cs* という名前の **コントローラークラス** を *Controllers* ディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-380">Add a **Controller Class** named *HomeController.cs* to the *Controllers* directory.</span></span>

![[新しい項目の追加] ダイアログ](mvc/_static/add_mvc_ctl.png)

* <span data-ttu-id="e6b1b-382">*Views* ディレクトリを追加します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-382">Add a *Views* directory.</span></span>

* <span data-ttu-id="e6b1b-383">*ビュー/ホーム* ディレクトリを追加します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-383">Add a *Views/Home* directory.</span></span>

* <span data-ttu-id="e6b1b-384">*Views/Home* ディレクトリに、 *Index. cshtml* という名前の **:::no-loc(Razor)::: ビュー** を追加します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-384">Add a **:::no-loc(Razor)::: View** named *Index.cshtml* to the *Views/Home* directory.</span></span>

![[新しい項目の追加] ダイアログ](mvc/_static/view.png)

<span data-ttu-id="e6b1b-386">プロジェクトの構造は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-386">The project structure is shown below:</span></span>

![WebApp1 のファイルとディレクトリを示すソリューションエクスプローラー](mvc/_static/project-structure-controller-view.png)

<span data-ttu-id="e6b1b-388">*Views/Home/Index.cshtml* ファイルの内容を次のマークアップに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-388">Replace the contents of the *Views/Home/Index.cshtml* file with the following markup:</span></span>

```html
<h1>Hello world!</h1>
```

<span data-ttu-id="e6b1b-389">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-389">Run the app.</span></span>

![Microsoft Edge で開いている Web アプリ](mvc/_static/hello-world.png)

<span data-ttu-id="e6b1b-391">詳細については、「 [コントローラー](xref:mvc/controllers/actions) と [ビュー](xref:mvc/views/overview)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-391">For more information, see [Controllers](xref:mvc/controllers/actions) and [Views](xref:mvc/views/overview).</span></span>

<span data-ttu-id="e6b1b-392">次の機能を使用するには、例の ASP.NET MVC プロジェクトから ASP.NET Core プロジェクトへの移行が必要です。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-392">The following functionality requires migration from the example ASP.NET MVC project to the ASP.NET Core project:</span></span>

* <span data-ttu-id="e6b1b-393">クライアント側コンテンツ (CSS、フォント、およびスクリプト)</span><span class="sxs-lookup"><span data-stu-id="e6b1b-393">client-side content (CSS, fonts, and scripts)</span></span>

* <span data-ttu-id="e6b1b-394">controllers</span><span class="sxs-lookup"><span data-stu-id="e6b1b-394">controllers</span></span>

* <span data-ttu-id="e6b1b-395">views</span><span class="sxs-lookup"><span data-stu-id="e6b1b-395">views</span></span>

* <span data-ttu-id="e6b1b-396">モデル</span><span class="sxs-lookup"><span data-stu-id="e6b1b-396">models</span></span>

* <span data-ttu-id="e6b1b-397">まとめる</span><span class="sxs-lookup"><span data-stu-id="e6b1b-397">bundling</span></span>

* <span data-ttu-id="e6b1b-398">filters</span><span class="sxs-lookup"><span data-stu-id="e6b1b-398">filters</span></span>

* <span data-ttu-id="e6b1b-399">ログイン/ログアウト :::no-loc(Identity)::: (これは次のチュートリアルで行います)</span><span class="sxs-lookup"><span data-stu-id="e6b1b-399">Log in/out, :::no-loc(Identity)::: (This is done in the next tutorial.)</span></span>

## <a name="controllers-and-views"></a><span data-ttu-id="e6b1b-400">コントローラーとビュー</span><span class="sxs-lookup"><span data-stu-id="e6b1b-400">Controllers and views</span></span>

* <span data-ttu-id="e6b1b-401">ASP.NET MVC の各メソッド `HomeController` を新しいにコピーし `HomeController` ます。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-401">Copy each of the methods from the ASP.NET MVC `HomeController` to the new `HomeController`.</span></span> <span data-ttu-id="e6b1b-402">ASP.NET MVC では、組み込みテンプレートのコントローラーアクションメソッドの戻り値の型はです <https://docs.microsoft.com/dotnet/api/system.web.mvc.actionresult?view=aspnet-mvc-5.2> 。 ASP.NET CORE mvc では、アクションメソッドは代わりにを返し `IActionResult` ます。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-402">In ASP.NET MVC, the built-in template's controller action method return type is <https://docs.microsoft.com/dotnet/api/system.web.mvc.actionresult?view=aspnet-mvc-5.2>; in ASP.NET Core MVC, the action methods return `IActionResult` instead.</span></span> <span data-ttu-id="e6b1b-403">`ActionResult``IActionResult`を実装するので、アクションメソッドの戻り値の型を変更する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-403">`ActionResult` implements `IActionResult`, so there's no need to change the return type of the action methods.</span></span>

* <span data-ttu-id="e6b1b-404">ASP.NET MVC プロジェクトの *About. cshtml* 、 *Contact.* cshtml、および *Index. cshtml* :::no-loc(Razor)::: view ファイルを ASP.NET Core プロジェクトにコピーします。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-404">Copy the *About.cshtml* , *Contact.cshtml* , and *Index.cshtml* :::no-loc(Razor)::: view files from the ASP.NET MVC project to the ASP.NET Core project.</span></span>

## <a name="test-each-method"></a><span data-ttu-id="e6b1b-405">各メソッドをテストする</span><span class="sxs-lookup"><span data-stu-id="e6b1b-405">Test each method</span></span>

<span data-ttu-id="e6b1b-406">レイアウトファイルとスタイルはまだ移行されていないため、表示されたビューには、ビューファイルのコンテンツのみが含まれます。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-406">The layout file and styles have not been migrated yet, so the rendered views only contain the content in the view files.</span></span> <span data-ttu-id="e6b1b-407">ビューとビューに対して生成されたレイアウトファイルは、 `About` `Contact` まだ使用できません。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-407">The layout file generated links for the `About` and `Contact` views will not be available yet.</span></span>

* <span data-ttu-id="e6b1b-408">現在のポート番号を ASP.NET core プロジェクトで使用されているポート番号に置き換えることによって、実行中の ASP.NET core アプリのブラウザーから表示されたビューを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-408">Invoke the rendered views from the browser on the running ASP.NET core app by replacing the current port number with the port number used in the ASP.NET core project.</span></span> <span data-ttu-id="e6b1b-409">(例: `https://localhost:44375/home/about`)。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-409">For example: `https://localhost:44375/home/about`.</span></span>

![連絡先ページ](mvc/_static/contact-page.png)

<span data-ttu-id="e6b1b-411">スタイル設定とメニュー項目がないことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-411">Note the lack of styling and menu items.</span></span> <span data-ttu-id="e6b1b-412">スタイルは次のセクションで修正されます。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-412">The styling will be fixed in the next section.</span></span>

## <a name="static-content"></a><span data-ttu-id="e6b1b-413">静的コンテンツ</span><span class="sxs-lookup"><span data-stu-id="e6b1b-413">Static content</span></span>

<span data-ttu-id="e6b1b-414">ASP.NET MVC 5 以前では、静的コンテンツは web プロジェクトのルートからホストされており、サーバー側ファイルと混在していました。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-414">In ASP.NET MVC 5 and earlier, static content was hosted from the root of the web project and was intermixed with server-side files.</span></span> <span data-ttu-id="e6b1b-415">ASP.NET Core では、静的なコンテンツは *wwwroot* ディレクトリでホストされます。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-415">In ASP.NET Core, static content is hosted in the *wwwroot* directory.</span></span> <span data-ttu-id="e6b1b-416">ASP.NET MVC アプリの静的コンテンツを ASP.NET Core プロジェクトの *wwwroot* ディレクトリにコピーします。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-416">Copy the static content from the ASP.NET MVC app to the *wwwroot* directory in the ASP.NET Core project.</span></span> <span data-ttu-id="e6b1b-417">このサンプルの変換の例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-417">In this sample conversion:</span></span>

* <span data-ttu-id="e6b1b-418">ASP.NET MVC プロジェクトの *favicon* ファイルを ASP.NET Core プロジェクトの *wwwroot* ディレクトリにコピーします。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-418">Copy the *favicon.ico* file from the ASP.NET MVC project to the *wwwroot* directory in the ASP.NET Core project.</span></span>

<span data-ttu-id="e6b1b-419">ASP.NET MVC プロジェクトでは、そのスタイルに [ブートストラップ](https://getbootstrap.com/) を使用して、ブートストラップファイルを *Content* および *Scripts* ディレクトリに格納します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-419">The ASP.NET MVC project uses [Bootstrap](https://getbootstrap.com/) for its styling and stores the Bootstrap files in the *Content* and *Scripts* directories.</span></span> <span data-ttu-id="e6b1b-420">ASP.NET MVC プロジェクトを生成したテンプレートは、レイアウトファイル内のブートストラップ ( *Views/Shared/_Layout* ) を参照します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-420">The template, which generated the ASP.NET MVC project, references Bootstrap in the layout file ( *Views/Shared/_Layout.cshtml* ).</span></span> <span data-ttu-id="e6b1b-421">*bootstrap.js* と *ブートストラップの .css* ファイルは、ASP.NET MVC プロジェクトから新しいプロジェクトの *wwwroot* ディレクトリにコピーできます。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-421">The *bootstrap.js* and *bootstrap.css* files could be copied from the ASP.NET MVC project to the *wwwroot* directory in the new project.</span></span> <span data-ttu-id="e6b1b-422">代わりに、このドキュメントでは、次のセクションで、CDNs を使用したブートストラップ (およびその他のクライアント側ライブラリ) のサポートを追加します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-422">Instead, this document adds support for Bootstrap (and other client-side libraries) using CDNs, in the next section.</span></span>

## <a name="migrate-the-layout-file"></a><span data-ttu-id="e6b1b-423">レイアウトファイルを移行する</span><span class="sxs-lookup"><span data-stu-id="e6b1b-423">Migrate the layout file</span></span>

* <span data-ttu-id="e6b1b-424">ASP.NET MVC プロジェクトの *views* ディレクトリの *_ViewStart* ファイルを ASP.NET Core プロジェクトの *views* ディレクトリにコピーします。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-424">Copy the *_ViewStart.cshtml* file from the ASP.NET MVC project's *Views* directory into the ASP.NET Core project's *Views* directory.</span></span> <span data-ttu-id="e6b1b-425">*_ViewStart* ASP.NET Core MVC では、このファイルは変更されていません。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-425">The *_ViewStart.cshtml* file has not changed in ASP.NET Core MVC.</span></span>

* <span data-ttu-id="e6b1b-426">*ビュー/共有* ディレクトリを作成します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-426">Create a *Views/Shared* directory.</span></span>

* <span data-ttu-id="e6b1b-427">*省略可能:\*\*FullAspNetCore* MVC プロジェクトの *views* ディレクトリから、ASP.NET Core プロジェクトの *views* ディレクトリに *_ViewImports* をコピーします。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-427">*Optional:* Copy *_ViewImports.cshtml* from the *FullAspNetCore* MVC project's *Views* directory into the ASP.NET Core project's *Views* directory.</span></span> <span data-ttu-id="e6b1b-428">*_ViewImports* ファイル内の名前空間宣言を削除します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-428">Remove any namespace declaration in the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="e6b1b-429">*_ViewImports* のファイルは、すべてのビューファイルの名前空間を提供し、 [タグヘルパー](xref:mvc/views/tag-helpers/intro)を取り込みます。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-429">The *_ViewImports.cshtml* file provides namespaces for all the view files and brings in [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="e6b1b-430">タグヘルパーは、新しいレイアウトファイルで使用されます。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-430">Tag Helpers are used in the new layout file.</span></span> <span data-ttu-id="e6b1b-431">*_ViewImports* 、ASP.NET Core の新しいファイルです。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-431">The *_ViewImports.cshtml* file is new for ASP.NET Core.</span></span>

* <span data-ttu-id="e6b1b-432">ASP.NET MVC プロジェクトの *views/shared* ディレクトリにある *_Layout* ファイルを、ASP.NET Core プロジェクトの *views/shared* ディレクトリにコピーします。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-432">Copy the *_Layout.cshtml* file from the ASP.NET MVC project's *Views/Shared* directory into the ASP.NET Core project's *Views/Shared* directory.</span></span>

<span data-ttu-id="e6b1b-433">*_Layout* ファイルを開き、次のように変更します (完成したコードは次のようになります)。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-433">Open *_Layout.cshtml* file and make the following changes (the completed code is shown below):</span></span>

* <span data-ttu-id="e6b1b-434">`@Styles.Render("~/Content/css")` `<link>` *ブートストラップ* を読み込む要素で置き換えます (下記参照)。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-434">Replace `@Styles.Render("~/Content/css")` with a `<link>` element to load *bootstrap.css* (see below).</span></span>

* <span data-ttu-id="e6b1b-435">`@Scripts.Render("~/bundles/modernizr")` を削除します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-435">Remove `@Scripts.Render("~/bundles/modernizr")`.</span></span>

* <span data-ttu-id="e6b1b-436">行をコメントアウト `@Html.Partial("_LoginPartial")` します (行をで囲み `@*...*@` ます)。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-436">Comment out the `@Html.Partial("_LoginPartial")` line (surround the line with `@*...*@`).</span></span> <span data-ttu-id="e6b1b-437">詳細については、「[認証と :::no-loc(Identity)::: ASP.NET Core への移行](xref:migration/identity)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-437">For more information, see [Migrate Authentication and :::no-loc(Identity)::: to ASP.NET Core](xref:migration/identity)</span></span>

* <span data-ttu-id="e6b1b-438">`@Scripts.Render("~/bundles/jquery")`要素で置き換え `<script>` ます (下記参照)。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-438">Replace `@Scripts.Render("~/bundles/jquery")` with a `<script>` element (see below).</span></span>

* <span data-ttu-id="e6b1b-439">`@Scripts.Render("~/bundles/bootstrap")`要素で置き換え `<script>` ます (下記参照)。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-439">Replace `@Scripts.Render("~/bundles/bootstrap")` with a `<script>` element (see below).</span></span>

<span data-ttu-id="e6b1b-440">ブートストラップ CSS インクルードの置換マークアップ:</span><span class="sxs-lookup"><span data-stu-id="e6b1b-440">The replacement markup for Bootstrap CSS inclusion:</span></span>

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

<span data-ttu-id="e6b1b-441">JQuery および Bootstrap JavaScript インクルードの置換マークアップ:</span><span class="sxs-lookup"><span data-stu-id="e6b1b-441">The replacement markup for jQuery and Bootstrap JavaScript inclusion:</span></span>

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

<span data-ttu-id="e6b1b-442">更新された *_Layout* ファイルは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-442">The updated *_Layout.cshtml* file is shown below:</span></span>

[!code-cshtml[](mvc/samples/2.x/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

<span data-ttu-id="e6b1b-443">ブラウザーでサイトを表示します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-443">View the site in the browser.</span></span> <span data-ttu-id="e6b1b-444">これで、必要なスタイルが適切に読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-444">It should now load correctly, with the expected styles in place.</span></span>

* <span data-ttu-id="e6b1b-445">*省略可能:* 新しいレイアウトファイルを使用してみてください。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-445">*Optional:* Try using the new layout file.</span></span> <span data-ttu-id="e6b1b-446">*FullAspNetCore* プロジェクトからレイアウトファイルをコピーします。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-446">Copy the layout file from the *FullAspNetCore* project.</span></span> <span data-ttu-id="e6b1b-447">新しいレイアウトファイルは [タグヘルパー](xref:mvc/views/tag-helpers/intro) を使用し、その他の機能強化が行われています。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-447">The new layout file uses [Tag Helpers](xref:mvc/views/tag-helpers/intro) and has other improvements.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="e6b1b-448">バンドルと縮小を構成する</span><span class="sxs-lookup"><span data-stu-id="e6b1b-448">Configure bundling and minification</span></span>

<span data-ttu-id="e6b1b-449">バンドルと縮小を構成する方法の詳細については、「 [バンドルと縮小](xref:client-side/bundling-and-minification)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-449">For information about how to configure bundling and minification, see [Bundling and Minification](xref:client-side/bundling-and-minification).</span></span>

## <a name="solve-http-500-errors"></a><span data-ttu-id="e6b1b-450">HTTP 500 エラーを解決する</span><span class="sxs-lookup"><span data-stu-id="e6b1b-450">Solve HTTP 500 errors</span></span>

<span data-ttu-id="e6b1b-451">問題の原因として、問題の原因に関する情報が含まれていない HTTP 500 エラーメッセージが発生することがあります。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-451">There are many problems that can cause an HTTP 500 error messages that contain no information on the source of the problem.</span></span> <span data-ttu-id="e6b1b-452">たとえば、 *Views/_ViewImports cshtml* ファイルに、プロジェクトに存在しない名前空間が含まれている場合、HTTP 500 エラーが生成されます。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-452">For example, if the *Views/_ViewImports.cshtml* file contains a namespace that doesn't exist in the project, a HTTP 500 error is generated.</span></span> <span data-ttu-id="e6b1b-453">ASP.NET Core アプリでは、既定で `UseDeveloperExceptionPage` 、拡張機能がに追加され、 `IApplicationBuilder` 構成が *開発* されるときに実行されます。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-453">By default in ASP.NET Core apps, the `UseDeveloperExceptionPage` extension is added to the `IApplicationBuilder` and executed when the configuration is *Development* .</span></span> <span data-ttu-id="e6b1b-454">次のコードの例を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-454">See an example in the following code:</span></span>

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=11-15&name=snippet)]

<span data-ttu-id="e6b1b-455">ASP.NET Core は、未処理の例外を HTTP 500 エラー応答に変換します。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-455">ASP.NET Core converts unhandled exceptions into HTTP 500 error responses.</span></span> <span data-ttu-id="e6b1b-456">通常、サーバーに関する機密情報が漏えいするのを防ぐために、エラーの詳細はこれらの応答に含まれていません。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-456">Normally, error details aren't included in these responses to prevent disclosure of potentially sensitive information about the server.</span></span> <span data-ttu-id="e6b1b-457">詳細については、「 [開発者向け例外ページ](xref:fundamentals/error-handling#developer-exception-page)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e6b1b-457">For more information, see [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e6b1b-458">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="e6b1b-458">Additional resources</span></span>

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>

::: moniker-end
