---
title: ASP.NET Core と Entity Framework 6
author: rick-anderson
description: Entity Framework 6.3 以降は ASP.NET Core 3.1 以降で動作します。
ms.author: riande
ms.custom: mvc
ms.date: 7/14/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: data/entity-framework-6
ms.openlocfilehash: 64d420d7076f1da453ee423cc4a3732eeb47b221
ms.sourcegitcommit: 4df148cbbfae9ec8d377283ee71394944a284051
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88876699"
---
# <a name="aspnet-core-and-entity-framework-6"></a><span data-ttu-id="ad906-103">ASP.NET Core と Entity Framework 6</span><span class="sxs-lookup"><span data-stu-id="ad906-103">ASP.NET Core and Entity Framework 6</span></span>
::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ad906-104">作成者: [Patrick Goode](https://github.com/attrib75)</span><span class="sxs-lookup"><span data-stu-id="ad906-104">By [Patrick Goode](https://github.com/attrib75)</span></span>

## <a name="using-entity-framework-6-with-aspnet-core"></a><span data-ttu-id="ad906-105">ASP.NET Core での Entity Framework 6 の使用</span><span class="sxs-lookup"><span data-stu-id="ad906-105">Using Entity Framework 6 with ASP.NET Core</span></span>

<span data-ttu-id="ad906-106">新しい開発には、[Entity Framework Core](/ef/) を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ad906-106">[Entity Framework Core](/ef/) should be used for new development.</span></span> <span data-ttu-id="ad906-107">この[ダウンロード サンプル](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/3.xsample)では [Entity Framework 6 (EF6)](/ef/ef6) を使用します。これを使用すると、既存のアプリを ASP.NET Core に移行できます。</span><span class="sxs-lookup"><span data-stu-id="ad906-107">The [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/3.xsample) uses [Entity Framework 6 (EF6)](/ef/ef6), which can be used to migrate exiting apps to ASP.NET Core.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ad906-108">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="ad906-108">Additional resources</span></span>

* [<span data-ttu-id="ad906-109">Entity Framework - コード ベースの構成</span><span class="sxs-lookup"><span data-stu-id="ad906-109">Entity Framework - Code-Based Configuration</span></span>](/ef/ef6/fundamentals/configuring/code-based)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ad906-110">作成者: [Paweł Grudzień](https://github.com/pgrudzien12)、[Damien Pontifex](https://github.com/DamienPontifex)、[Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="ad906-110">By [Paweł Grudzień](https://github.com/pgrudzien12), [Damien Pontifex](https://github.com/DamienPontifex), and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="ad906-111">この記事では、ASP.NET Core アプリケーションで Entity Framework 6 を使用する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="ad906-111">This article shows how to use Entity Framework 6 in an ASP.NET Core application.</span></span>    

## <a name="overview"></a><span data-ttu-id="ad906-112">概要</span><span class="sxs-lookup"><span data-stu-id="ad906-112">Overview</span></span> 

<span data-ttu-id="ad906-113">Entity Framework 6 は .NET Core をサポートしていないので、Entity Framework 6 を使用するには、プロジェクトが .NET Framework に対してコンパイルする必要があります。</span><span class="sxs-lookup"><span data-stu-id="ad906-113">To use Entity Framework 6, your project has to compile against .NET Framework, as Entity Framework 6 doesn't support .NET Core.</span></span> <span data-ttu-id="ad906-114">クロスプラットフォーム機能が必要な場合は、[Entity Framework Core](/ef/) にアップグレードする必要があります。</span><span class="sxs-lookup"><span data-stu-id="ad906-114">If you need cross-platform features you will need to upgrade to [Entity Framework Core](/ef/).</span></span>  

<span data-ttu-id="ad906-115">ASP.NET Core アプリケーションで Entity Framework 6 を使用するための推奨方法は、EF6 コンテキストとモデル クラスを、.NET Framework を対象とするクラス ライブラリ プロジェクト内に配置することです。</span><span class="sxs-lookup"><span data-stu-id="ad906-115">The recommended way to use Entity Framework 6 in an ASP.NET Core application is to put the EF6 context and model classes in a class library project that targets .NET Framework.</span></span> <span data-ttu-id="ad906-116">ASP.NET Core プロジェクトから、クラス ライブラリに参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="ad906-116">Add a reference to the class library from the ASP.NET Core project.</span></span> <span data-ttu-id="ad906-117">[EF6 と ASP.NET Core プロジェクトを使用した Visual Studio ソリューション](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/)のサンプルを参照してください。</span><span class="sxs-lookup"><span data-stu-id="ad906-117">See the sample [Visual Studio solution with EF6 and ASP.NET Core projects](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/).</span></span>  

<span data-ttu-id="ad906-118">.NET Core プロジェクトは、*Enable-Migrations* などの EF6 コマンドが必要とするすべての機能をサポートしていないため、EF6 コンテキストを ASP.NET Core プロジェクトに配置することはできません。</span><span class="sxs-lookup"><span data-stu-id="ad906-118">You can't put an EF6 context in an ASP.NET Core project because .NET Core projects don't support all of the functionality that EF6 commands such as *Enable-Migrations* require.</span></span>    

<span data-ttu-id="ad906-119">EF6 コンテキストを検索するプロジェクトの種類に関係なく、EF6 コマンドライン ツールのみが EF6 コンテキストを使用します。</span><span class="sxs-lookup"><span data-stu-id="ad906-119">Regardless of project type in which you locate your EF6 context, only EF6 command-line tools work with an EF6 context.</span></span> <span data-ttu-id="ad906-120">たとえば、`Scaffold-DbContext` は、Entity Framework Core でのみ使用できます。</span><span class="sxs-lookup"><span data-stu-id="ad906-120">For example, `Scaffold-DbContext` is only available in Entity Framework Core.</span></span> <span data-ttu-id="ad906-121">データベースを EF6 モデルにリバース エンジニアリングする必要がある場合は、「<https://docs.microsoft.com/ef/ef6/modeling/code-first/workflows/existing-database>」 (既存のデータベースに対する Entity Framework Code First) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ad906-121">If you need to do reverse engineering of a database into an EF6 model, see <https://docs.microsoft.com/ef/ef6/modeling/code-first/workflows/existing-database>.</span></span>    

## <a name="reference-full-framework-and-ef6-in-the-aspnet-core-project"></a><span data-ttu-id="ad906-122">ASP.NET Core プロジェクトで完全なフレームワークと EF6 を参照する</span><span class="sxs-lookup"><span data-stu-id="ad906-122">Reference full framework and EF6 in the ASP.NET Core project</span></span> 

<span data-ttu-id="ad906-123">ASP.NET Core プロジェクトは、.NET Framework を対象とし、EF6 を参照する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ad906-123">Your ASP.NET Core project needs to target .NET Framework and reference EF6.</span></span> <span data-ttu-id="ad906-124">たとえば、ASP.NET Core プロジェクトの *.csproj* ファイルは次の例のようになります (ファイルの関連する部分のみが表示されています)。</span><span class="sxs-lookup"><span data-stu-id="ad906-124">For example, the *.csproj* file of your ASP.NET Core project will look similar to the following example (only relevant parts of the file are shown).</span></span>    

[!code-xml[](entity-framework-6/sample/MVCCore/MVCCore.csproj?range=3-9&highlight=2)]   

<span data-ttu-id="ad906-125">新しいプロジェクトを作成する場合は、**ASP.NET Core Web アプリケーション (.NET Framework)** テンプレートを使用します。</span><span class="sxs-lookup"><span data-stu-id="ad906-125">When creating a new project, use the **ASP.NET Core Web Application (.NET Framework)** template.</span></span>    

## <a name="handle-connection-strings"></a><span data-ttu-id="ad906-126">接続文字列を処理する</span><span class="sxs-lookup"><span data-stu-id="ad906-126">Handle connection strings</span></span>    

<span data-ttu-id="ad906-127">EF6 クラス ライブラリ プロジェクトで使用する EF6 コマンドライン ツールには、コンテキストをインスタンス化できるように、既定のコンストラクターが必要です。</span><span class="sxs-lookup"><span data-stu-id="ad906-127">The EF6 command-line tools that you'll use in the EF6 class library project require a default constructor so they can instantiate the context.</span></span> <span data-ttu-id="ad906-128">しかし、ASP.NET Core プロジェクトで使用するために接続文字列を指定する場合は、コンテキスト コンストラクターは接続文字列で渡すことができるパラメーターを持つ必要があります。</span><span class="sxs-lookup"><span data-stu-id="ad906-128">But you'll probably want to specify the connection string to use in the ASP.NET Core project, in which case your context constructor must have a parameter that lets you pass in the connection string.</span></span> <span data-ttu-id="ad906-129">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="ad906-129">Here's an example.</span></span>   

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContext.cs?name=snippet_Constructor)]   

<span data-ttu-id="ad906-130">EF6 コンテキストにはパラメーターなしのコンストラクターがないため、EF6 プロジェクトが <https://docs.microsoft.com/dotnet/api/system.data.entity.infrastructure.idbcontextfactory-1?view=entity-framework-6.2.0> の実装を提供する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ad906-130">Since your EF6 context doesn't have a parameterless constructor, your EF6 project has to provide an implementation of <https://docs.microsoft.com/dotnet/api/system.data.entity.infrastructure.idbcontextfactory-1?view=entity-framework-6.2.0>.</span></span> <span data-ttu-id="ad906-131">EF6 コマンドライン ツールはその実装を見つけて使用するため、コンテキストをインスタンス化することができます。</span><span class="sxs-lookup"><span data-stu-id="ad906-131">The EF6 command-line tools will find and use that implementation so they can instantiate the context.</span></span> <span data-ttu-id="ad906-132">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="ad906-132">Here's an example.</span></span>   

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContextFactory.cs?name=snippet_IDbContextFactory)]  

<span data-ttu-id="ad906-133">このサンプル コードでは、`IDbContextFactory` の実装がハード コーディングされた接続文字列で渡されます。</span><span class="sxs-lookup"><span data-stu-id="ad906-133">In this sample code, the `IDbContextFactory` implementation passes in a hard-coded connection string.</span></span> <span data-ttu-id="ad906-134">これがコマンドライン ツールで使用される接続文字列です。</span><span class="sxs-lookup"><span data-stu-id="ad906-134">This is the connection string that the command-line tools will use.</span></span> <span data-ttu-id="ad906-135">クラス ライブラリで呼び出し元のアプリケーションが使用するのと同じ接続文字列が確実に使用されるようにする方法を実装したい場合があります。</span><span class="sxs-lookup"><span data-stu-id="ad906-135">You'll want to implement a strategy to ensure that the class library uses the same connection string that the calling application uses.</span></span> <span data-ttu-id="ad906-136">たとえば、両方のプロジェクトで環境変数から値を取得する場合です。</span><span class="sxs-lookup"><span data-stu-id="ad906-136">For example, you could get the value from an environment variable in both projects.</span></span>   

## <a name="set-up-dependency-injection-in-the-aspnet-core-project"></a><span data-ttu-id="ad906-137">ASP.NET Core プロジェクトに依存性の注入を設定する</span><span class="sxs-lookup"><span data-stu-id="ad906-137">Set up dependency injection in the ASP.NET Core project</span></span>  

<span data-ttu-id="ad906-138">Core プロジェクトの *Startup.cs* ファイルで、`ConfigureServices` に依存性を注入 (DI) するために EF6 コンテキストを設定します。</span><span class="sxs-lookup"><span data-stu-id="ad906-138">In the Core project's *Startup.cs* file, set up the EF6 context for dependency injection (DI) in `ConfigureServices`.</span></span> <span data-ttu-id="ad906-139">EF コンテキスト オブジェクトは、要求ごとの有効期間に限定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ad906-139">EF context objects should be scoped for a per-request lifetime.</span></span>   

[!code-csharp[](entity-framework-6/sample/MVCCore/Startup.cs?name=snippet_ConfigureServices&highlight=5)]   

<span data-ttu-id="ad906-140">その後、DI を使用して、コントローラーでコンテキストのインスタンスを取得できます。</span><span class="sxs-lookup"><span data-stu-id="ad906-140">You can then get an instance of the context in your controllers by using DI.</span></span> <span data-ttu-id="ad906-141">コードは、EF Core コンテキスト用に記述したものと似ています。</span><span class="sxs-lookup"><span data-stu-id="ad906-141">The code is similar to what you'd write for an EF Core context:</span></span>    

[!code-csharp[](entity-framework-6/sample/MVCCore/Controllers/StudentsController.cs?name=snippet_ContextInController)]  

## <a name="sample-application"></a><span data-ttu-id="ad906-142">サンプル アプリケーション</span><span class="sxs-lookup"><span data-stu-id="ad906-142">Sample application</span></span>   

<span data-ttu-id="ad906-143">実際に動作するサンプル アプリケーションについては、この記事に付属している[Visual Studio のサンプル ソリューション](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ad906-143">For a working sample application, see the [sample Visual Studio solution](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/) that accompanies this article.</span></span>  

<span data-ttu-id="ad906-144">このサンプルは、次の手順に従って、Visual Studio でゼロから作成することができます。</span><span class="sxs-lookup"><span data-stu-id="ad906-144">This sample can be created from scratch by the following steps in Visual Studio:</span></span>    

* <span data-ttu-id="ad906-145">ソリューションを作成します。</span><span class="sxs-lookup"><span data-stu-id="ad906-145">Create a solution.</span></span>    

* <span data-ttu-id="ad906-146">**[追加]** > **[新しいプロジェクト]** > **[Web]** > **[ASP.NET Core Web アプリケーション]**</span><span class="sxs-lookup"><span data-stu-id="ad906-146">**Add** > **New Project** > **Web** > **ASP.NET Core Web Application**</span></span>    
  * <span data-ttu-id="ad906-147">プロジェクト テンプレートの選択ダイアログで、ドロップダウンの [API] と [.NET Framework] を選択します。</span><span class="sxs-lookup"><span data-stu-id="ad906-147">In project template selection dialog, select API and .NET Framework in dropdown</span></span> 

* <span data-ttu-id="ad906-148">**[追加]** > **[新しいプロジェクト]** > **[Windows デスクトップ]** > **[クラス ライブラリ (.NET Framework)]**</span><span class="sxs-lookup"><span data-stu-id="ad906-148">**Add** > **New Project** > **Windows Desktop** > **Class Library (.NET Framework)**</span></span>  

* <span data-ttu-id="ad906-149">両方のプロジェクトの**パッケージ マネージャー コンソール** (PMC) ウィンドウで、`Install-Package Entityframework` コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="ad906-149">In **Package Manager Console** (PMC) for both projects, run the command `Install-Package Entityframework`.</span></span>    

* <span data-ttu-id="ad906-150">クラス ライブラリ プロジェクトで、データ モデル クラスとコンテキスト クラス、および `IDbContextFactory` の実装を作成します。</span><span class="sxs-lookup"><span data-stu-id="ad906-150">In the class library project, create data model classes and a context class, and an implementation of `IDbContextFactory`.</span></span>    

* <span data-ttu-id="ad906-151">クラス ライブラリ プロジェクトの PMC で、コマンド `Enable-Migrations` と `Add-Migration Initial` を実行します。</span><span class="sxs-lookup"><span data-stu-id="ad906-151">In PMC for the class library project, run the commands `Enable-Migrations` and `Add-Migration Initial`.</span></span> <span data-ttu-id="ad906-152">ASP.NET Core プロジェクトをスタートアップ プロジェクトとして設定している場合は、`-StartupProjectName EF6` をこれらのコマンドに追加します。</span><span class="sxs-lookup"><span data-stu-id="ad906-152">If you have set the ASP.NET Core project as the startup project, add `-StartupProjectName EF6` to these commands.</span></span> 

* <span data-ttu-id="ad906-153">Core プロジェクトで、プロジェクト参照をクラス ライブラリ プロジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="ad906-153">In the Core project, add a project reference to the class library project.</span></span>    

* <span data-ttu-id="ad906-154">Core プロジェクト内の *Startup.cs* で、DI のコンテキストを登録します。</span><span class="sxs-lookup"><span data-stu-id="ad906-154">In the Core project, in *Startup.cs*, register the context for DI.</span></span>    

* <span data-ttu-id="ad906-155">Core プロジェクト内の *appsettings.json* で、接続文字列を追加します。</span><span class="sxs-lookup"><span data-stu-id="ad906-155">In the Core project, in *appsettings.json*, add the connection string.</span></span>    

* <span data-ttu-id="ad906-156">Core プロジェクトで、コントローラーとビューを追加してデータの読み書きができることを確認します</span><span class="sxs-lookup"><span data-stu-id="ad906-156">In the Core project, add a controller and view(s) to verify that you can read and write data.</span></span> <span data-ttu-id="ad906-157">(ASP.NET Core MVC のスキャフォールディングは、クラス ライブラリから参照される EF6 コンテキストでは機能しないことに注意してください)。</span><span class="sxs-lookup"><span data-stu-id="ad906-157">(Note that ASP.NET Core MVC scaffolding won't work with the EF6 context referenced from the class library.)</span></span>

::: moniker-end
