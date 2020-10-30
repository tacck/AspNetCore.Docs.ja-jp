---
title: ASP.NET から ASP.NET Core 2.0 への移行
author: isaac2004
description: 既存の ASP.NET MVC または Web API アプリケーションを ASP.NET Core 2.0 に移行するためのガイダンスを受信します。
ms.author: scaddie
ms.custom: mvc
ms.date: 10/24/2018
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
uid: migration/mvc2
ms.openlocfilehash: cf7d2e3a94c14fb752180d9349536d17b4557e0a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051331"
---
# <a name="migrate-from-aspnet-to-aspnet-core-20"></a><span data-ttu-id="034a1-103">ASP.NET から ASP.NET Core 2.0 への移行</span><span class="sxs-lookup"><span data-stu-id="034a1-103">Migrate from ASP.NET to ASP.NET Core 2.0</span></span>

<span data-ttu-id="034a1-104">著者: [Isaac Levin](https://isaaclevin.com)</span><span class="sxs-lookup"><span data-stu-id="034a1-104">By [Isaac Levin](https://isaaclevin.com)</span></span>

<span data-ttu-id="034a1-105">この記事は、ASP.NET アプリケーションを ASP.NET Core 2.0 に移行するための参考ガイドです。</span><span class="sxs-lookup"><span data-stu-id="034a1-105">This article serves as a reference guide for migrating ASP.NET applications to ASP.NET Core 2.0.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="034a1-106">[前提条件]</span><span class="sxs-lookup"><span data-stu-id="034a1-106">Prerequisites</span></span>

<span data-ttu-id="034a1-107">.Net のダウンロードから、次の **いずれか** をインストールし [ます。 Windows](https://dotnet.microsoft.com/download):</span><span class="sxs-lookup"><span data-stu-id="034a1-107">Install **one** of the following from [.NET Downloads: Windows](https://dotnet.microsoft.com/download):</span></span>

* <span data-ttu-id="034a1-108">.NET Core SDK</span><span class="sxs-lookup"><span data-stu-id="034a1-108">.NET Core SDK</span></span>
* <span data-ttu-id="034a1-109">Windows 用 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="034a1-109">Visual Studio for Windows</span></span>
  * <span data-ttu-id="034a1-110">**ASP.NET および Web の開発** ワークロード</span><span class="sxs-lookup"><span data-stu-id="034a1-110">**ASP.NET and web development** workload</span></span>
  * <span data-ttu-id="034a1-111">**.NET Core クロスプラットフォームの開発** ワークロード</span><span class="sxs-lookup"><span data-stu-id="034a1-111">**.NET Core cross-platform development** workload</span></span>

## <a name="target-frameworks"></a><span data-ttu-id="034a1-112">ターゲット フレームワーク</span><span class="sxs-lookup"><span data-stu-id="034a1-112">Target frameworks</span></span>

<span data-ttu-id="034a1-113">ASP.NET Core 2.0 プロジェクトを使うと、開発者は、.NET Core と .NET Framework のどちらか一方または両方を対象にして柔軟に開発できます。</span><span class="sxs-lookup"><span data-stu-id="034a1-113">ASP.NET Core 2.0 projects offer developers the flexibility of targeting .NET Core, .NET Framework, or both.</span></span> <span data-ttu-id="034a1-114">最も適切なターゲット フレームワークの決定については、「[サーバー アプリ用 .NET Core と .NET Framework の選択](/dotnet/standard/choosing-core-framework-server)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="034a1-114">See [Choosing between .NET Core and .NET Framework for server apps](/dotnet/standard/choosing-core-framework-server) to determine which target framework is most appropriate.</span></span>

<span data-ttu-id="034a1-115">.NET Framework を対象にする場合は、プロジェクトで個々の NuGet パッケージを参照する必要があります。</span><span class="sxs-lookup"><span data-stu-id="034a1-115">When targeting .NET Framework, projects need to reference individual NuGet packages.</span></span>

<span data-ttu-id="034a1-116">.NET Core を対象にすると、ASP.NET Core 2.0 [メタパッケージ](xref:fundamentals/metapackage)のおかげで、さまざまな明示的パッケージ参照をしなくて済みます。</span><span class="sxs-lookup"><span data-stu-id="034a1-116">Targeting .NET Core allows you to eliminate numerous explicit package references, thanks to the ASP.NET Core 2.0 [metapackage](xref:fundamentals/metapackage).</span></span> <span data-ttu-id="034a1-117">`Microsoft.AspNetCore.All` メタパッケージをプロジェクトにインストールします。</span><span class="sxs-lookup"><span data-stu-id="034a1-117">Install the `Microsoft.AspNetCore.All` metapackage in your project:</span></span>

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.AspNetCore.All" Version="2.0.9" />
</ItemGroup>
```

<span data-ttu-id="034a1-118">メタパッケージを使うと、メタパッケージ内で参照されているパッケージはアプリでは展開されません。</span><span class="sxs-lookup"><span data-stu-id="034a1-118">When the metapackage is used, no packages referenced in the metapackage are deployed with the app.</span></span> <span data-ttu-id="034a1-119">.NET Core ランタイム ストアにはこれらのアセットが含まれており、パフォーマンス向上のためにプリコンパイルされています。</span><span class="sxs-lookup"><span data-stu-id="034a1-119">The .NET Core Runtime Store includes these assets, and they're precompiled to improve performance.</span></span> <span data-ttu-id="034a1-120">詳細については、「」を参照してください <xref:fundamentals/metapackage> 。</span><span class="sxs-lookup"><span data-stu-id="034a1-120">See <xref:fundamentals/metapackage> for more detail.</span></span>

## <a name="project-structure-differences"></a><span data-ttu-id="034a1-121">プロジェクトの構造の違い</span><span class="sxs-lookup"><span data-stu-id="034a1-121">Project structure differences</span></span>

<span data-ttu-id="034a1-122">*.csproj* ファイルの形式は、ASP.NET Core では簡素化されています。</span><span class="sxs-lookup"><span data-stu-id="034a1-122">The *.csproj* file format has been simplified in ASP.NET Core.</span></span> <span data-ttu-id="034a1-123">いくつかの重要な変更は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="034a1-123">Some notable changes include:</span></span>

* <span data-ttu-id="034a1-124">ファイルがプロジェクトの一部と見なされるためにファイルを明示的に含める必要はありません。</span><span class="sxs-lookup"><span data-stu-id="034a1-124">Explicit inclusion of files isn't necessary for them to be considered part of the project.</span></span> <span data-ttu-id="034a1-125">これにより、大規模なチームで作業する場合に XML のマージが競合するリスクが軽減されます。</span><span class="sxs-lookup"><span data-stu-id="034a1-125">This reduces the risk of XML merge conflicts when working on large teams.</span></span>
* <span data-ttu-id="034a1-126">他のプロジェクトを GUID で参照することはなくなり、ファイルの読みやすさが向上します。</span><span class="sxs-lookup"><span data-stu-id="034a1-126">There are no GUID-based references to other projects, which improves file readability.</span></span>
* <span data-ttu-id="034a1-127">Visual Studio でアンロードせずにファイルを編集することができます。</span><span class="sxs-lookup"><span data-stu-id="034a1-127">The file can be edited without unloading it in Visual Studio:</span></span>

  ![Visual Studio 2017 の CSPROJ の編集コンテキスト メニュー オプション](_static/EditProjectVs2017.png)

## <a name="globalasax-file-replacement"></a><span data-ttu-id="034a1-129">Global.asax ファイルの置換</span><span class="sxs-lookup"><span data-stu-id="034a1-129">Global.asax file replacement</span></span>

<span data-ttu-id="034a1-130">ASP.NET Core では、アプリをブートストラップする新しいメカニズムが導入されました。</span><span class="sxs-lookup"><span data-stu-id="034a1-130">ASP.NET Core introduced a new mechanism for bootstrapping an app.</span></span> <span data-ttu-id="034a1-131">ASP.NET アプリケーションのエントリ ポイントは、 *Global.asax* ファイルです。</span><span class="sxs-lookup"><span data-stu-id="034a1-131">The entry point for ASP.NET applications is the *Global.asax* file.</span></span> <span data-ttu-id="034a1-132">ルート構成、フィルター、領域の登録などのタスクは、 *Global.asax* ファイルで処理されます。</span><span class="sxs-lookup"><span data-stu-id="034a1-132">Tasks such as route configuration and filter and area registrations are handled in the *Global.asax* file.</span></span>

[!code-csharp[](samples/globalasax-sample.cs)]

<span data-ttu-id="034a1-133">このアプローチでは、アプリケーションとその展開先のサーバーが、実装を妨げるような方法で結合されます。</span><span class="sxs-lookup"><span data-stu-id="034a1-133">This approach couples the application and the server to which it's deployed in a way that interferes with the implementation.</span></span> <span data-ttu-id="034a1-134">結合を切り離すため、複数のフレームワークを一緒に使うさらにクリーンな方法を提供する [OWIN](https://owin.org/) が導入されました。</span><span class="sxs-lookup"><span data-stu-id="034a1-134">In an effort to decouple, [OWIN](https://owin.org/) was introduced to provide a cleaner way to use multiple frameworks together.</span></span> <span data-ttu-id="034a1-135">OWIN は、必要なモジュールのみを追加するためのパイプラインを提供します。</span><span class="sxs-lookup"><span data-stu-id="034a1-135">OWIN provides a pipeline to add only the modules needed.</span></span> <span data-ttu-id="034a1-136">ホスティング環境は、[Startup](xref:fundamentals/startup) 関数を取得して、サービスとアプリの要求パイプラインを構成します。</span><span class="sxs-lookup"><span data-stu-id="034a1-136">The hosting environment takes a [Startup](xref:fundamentals/startup) function to configure services and the app's request pipeline.</span></span> <span data-ttu-id="034a1-137">`Startup` は、ミドルウェアのセットをアプリケーションに登録します。</span><span class="sxs-lookup"><span data-stu-id="034a1-137">`Startup` registers a set of middleware with the application.</span></span> <span data-ttu-id="034a1-138">アプリケーションは、要求ごとに、既存のハンドラーのセットに対するリンク リストのヘッド ポインターを指定して、各ミドルウェア コンポーネントを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="034a1-138">For each request, the application calls each of the middleware components with the head pointer of a linked list to an existing set of handlers.</span></span> <span data-ttu-id="034a1-139">各ミドルウェア コンポーネントは、要求処理パイプラインに 1 つ以上のハンドラーを追加できます。</span><span class="sxs-lookup"><span data-stu-id="034a1-139">Each middleware component can add one or more handlers to the request handling pipeline.</span></span> <span data-ttu-id="034a1-140">これは、新しいリストのヘッドであるハンドラーへの参照を返すことによって行われます。</span><span class="sxs-lookup"><span data-stu-id="034a1-140">This is accomplished by returning a reference to the handler that's the new head of the list.</span></span> <span data-ttu-id="034a1-141">各ハンドラーは、リスト内の次のハンドラーを記憶して呼び出します。</span><span class="sxs-lookup"><span data-stu-id="034a1-141">Each handler is responsible for remembering and invoking the next handler in the list.</span></span> <span data-ttu-id="034a1-142">ASP.NET Core では、アプリケーションへのエントリ ポイントは `Startup` であり、 *Global.asax* に依存する必要はなくなりました。</span><span class="sxs-lookup"><span data-stu-id="034a1-142">With ASP.NET Core, the entry point to an application is `Startup`, and you no longer have a dependency on *Global.asax* .</span></span> <span data-ttu-id="034a1-143">.NET Framework で OWIN を使うときは、パイプラインとして次のようなものを使います。</span><span class="sxs-lookup"><span data-stu-id="034a1-143">When using OWIN with .NET Framework, use something like the following as a pipeline:</span></span>

[!code-csharp[](samples/webapi-owin.cs)]

<span data-ttu-id="034a1-144">これにより既定のルートが構成され、既定では Json 経由の XmlSerialization です。</span><span class="sxs-lookup"><span data-stu-id="034a1-144">This configures your default routes, and defaults to XmlSerialization over Json.</span></span> <span data-ttu-id="034a1-145">必要に応じて、このパイプラインに他のミドルウェアを追加します (サービスの読み込み、構成設定、静的ファイルなど)。</span><span class="sxs-lookup"><span data-stu-id="034a1-145">Add other Middleware to this pipeline as needed (loading services, configuration settings, static files, etc.).</span></span>

<span data-ttu-id="034a1-146">ASP.NET Core は同様のアプローチを使いますが、エントリを処理するために OWIN には依存しません。</span><span class="sxs-lookup"><span data-stu-id="034a1-146">ASP.NET Core uses a similar approach, but doesn't rely on OWIN to handle the entry.</span></span> <span data-ttu-id="034a1-147">代わりに、(コンソール アプリケーションと同じように) *Program.cs* の `Main` メソッドを通して行われ、そこから `Startup` が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="034a1-147">Instead, that's done through the *Program.cs* `Main` method (similar to console applications) and `Startup` is loaded through there.</span></span>

[!code-csharp[](samples/program.cs)]

<span data-ttu-id="034a1-148">`Startup` は、`Configure` メソッドを含む必要があります。</span><span class="sxs-lookup"><span data-stu-id="034a1-148">`Startup` must include a `Configure` method.</span></span> <span data-ttu-id="034a1-149">`Configure` では、必要なミドルウェアをパイプラインに追加します。</span><span class="sxs-lookup"><span data-stu-id="034a1-149">In `Configure`, add the necessary middleware to the pipeline.</span></span> <span data-ttu-id="034a1-150">(既定の Web サイト テンプレートからの) 次の例では、複数の拡張メソッドを使って、以下をサポートするパイプラインが構成されています。</span><span class="sxs-lookup"><span data-stu-id="034a1-150">In the following example (from the default web site template), several extension methods are used to configure the pipeline with support for:</span></span>

* [<span data-ttu-id="034a1-151">BrowserLink</span><span class="sxs-lookup"><span data-stu-id="034a1-151">BrowserLink</span></span>](https://vswebessentials.com/features/browserlink)
* <span data-ttu-id="034a1-152">エラー ページ</span><span class="sxs-lookup"><span data-stu-id="034a1-152">Error pages</span></span>
* <span data-ttu-id="034a1-153">静的ファイル</span><span class="sxs-lookup"><span data-stu-id="034a1-153">Static files</span></span>
* <span data-ttu-id="034a1-154">ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="034a1-154">ASP.NET Core MVC</span></span>
* :::no-loc(Identity):::

[!code-csharp[](../../common/samples/WebApplication1/Startup.cs?highlight=8,9,10,14,17,19,21&start=58&end=84)]

<span data-ttu-id="034a1-155">ホストとアプリケーションは切り離されており、将来別のプラットフォームに柔軟に移動できます。</span><span class="sxs-lookup"><span data-stu-id="034a1-155">The host and application have been decoupled, which provides the flexibility of moving to a different platform in the future.</span></span>

<span data-ttu-id="034a1-156">ASP.NET Core のスタートアップとミドルウェアの詳細なリファレンスについては、「」を参照してください <xref:fundamentals/startup> 。</span><span class="sxs-lookup"><span data-stu-id="034a1-156">For a more in-depth reference to ASP.NET Core Startup and Middleware, see <xref:fundamentals/startup>.</span></span>

## <a name="storing-configurations"></a><span data-ttu-id="034a1-157">保存の構成</span><span class="sxs-lookup"><span data-stu-id="034a1-157">Storing configurations</span></span>

<span data-ttu-id="034a1-158">ASP.NET では保存の設定がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="034a1-158">ASP.NET supports storing settings.</span></span> <span data-ttu-id="034a1-159">これらの設定は、たとえば、アプリケーションが展開された環境のサポートに使われます。</span><span class="sxs-lookup"><span data-stu-id="034a1-159">These setting are used, for example, to support the environment to which the applications were deployed.</span></span> <span data-ttu-id="034a1-160">一般的な方法は、すべてのカスタム キー/値ペアを、 *Web.config* ファイルの `<appSettings>` セクションに保存するというものでした。</span><span class="sxs-lookup"><span data-stu-id="034a1-160">A common practice was to store all custom key-value pairs in the `<appSettings>` section of the *Web.config* file:</span></span>

[!code-xml[](samples/webconfig-sample.xml)]

<span data-ttu-id="034a1-161">アプリケーションでは、`System.Configuration` 名前空間内の `ConfigurationManager.AppSettings` コレクションを使ってこれらの設定を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="034a1-161">Applications read these settings using the `ConfigurationManager.AppSettings` collection in the `System.Configuration` namespace:</span></span>

[!code-csharp[](samples/read-webconfig.cs)]

<span data-ttu-id="034a1-162">ASP.NET Core では、アプリケーションの構成データを任意のファイルに保存し、ミドルウェアのブートストラップの一部として読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="034a1-162">ASP.NET Core can store configuration data for the application in any file and load them as part of middleware bootstrapping.</span></span> <span data-ttu-id="034a1-163">プロジェクトテンプレートで使用される既定のファイルは *:::no-loc(appsettings.json):::* 次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="034a1-163">The default file used in the project templates is *:::no-loc(appsettings.json):::* :</span></span>

[!code-json[](samples/appsettings-sample.json)]

<span data-ttu-id="034a1-164">アプリケーション内部の `IConfiguration` のインスタンスにこのファイルを読み込むには、 *Startup.cs* が使われます。</span><span class="sxs-lookup"><span data-stu-id="034a1-164">Loading this file into an instance of `IConfiguration` inside your application is done in *Startup.cs* :</span></span>

[!code-csharp[](samples/startup-builder.cs)]

<span data-ttu-id="034a1-165">アプリでは、`Configuration` から読み取って設定を取得します。</span><span class="sxs-lookup"><span data-stu-id="034a1-165">The app reads from `Configuration` to get the settings:</span></span>

[!code-csharp[](samples/read-appsettings.cs)]

<span data-ttu-id="034a1-166">このアプローチにはプロセスをより堅牢にする拡張機能があります。たとえば、[依存性の注入](xref:fundamentals/dependency-injection) (DI) を使ってサービスとこれらの値を読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="034a1-166">There are extensions to this approach to make the process more robust, such as using [Dependency Injection](xref:fundamentals/dependency-injection) (DI) to load a service with these values.</span></span> <span data-ttu-id="034a1-167">DI アプローチは、厳密に型指定された構成オブジェクトのセットを提供します。</span><span class="sxs-lookup"><span data-stu-id="034a1-167">The DI approach provides a strongly-typed set of configuration objects.</span></span>

```csharp
// Assume AppConfiguration is a class representing a strongly-typed version of AppConfiguration section
services.Configure<AppConfiguration>(Configuration.GetSection("AppConfiguration"));
```

<span data-ttu-id="034a1-168">**注:** ASP.NET Core 構成の詳細なリファレンスについては、「」を参照してください <xref:fundamentals/configuration/index> 。</span><span class="sxs-lookup"><span data-stu-id="034a1-168">**Note:** For a more in-depth reference to ASP.NET Core configuration, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="native-dependency-injection"></a><span data-ttu-id="034a1-169">ネイティブな依存性の注入</span><span class="sxs-lookup"><span data-stu-id="034a1-169">Native dependency injection</span></span>

<span data-ttu-id="034a1-170">大規模で拡張性の高いアプリケーションを構築するときの重要な目標は、コンポーネントとサービスの疎な結合です。</span><span class="sxs-lookup"><span data-stu-id="034a1-170">An important goal when building large, scalable applications is the loose coupling of components and services.</span></span> <span data-ttu-id="034a1-171">[依存関係の挿入](xref:fundamentals/dependency-injection) は、これを実現するための一般的な手法であり、ASP.NET Core のネイティブコンポーネントです。</span><span class="sxs-lookup"><span data-stu-id="034a1-171">[Dependency injection](xref:fundamentals/dependency-injection) is a popular technique for achieving this, and it's a native component of ASP.NET Core.</span></span>

<span data-ttu-id="034a1-172">ASP.NET アプリケーションでは、開発者はサードパーティのライブラリを使用して依存関係の挿入を実装します。</span><span class="sxs-lookup"><span data-stu-id="034a1-172">In ASP.NET applications, developers rely on a third-party library to implement dependency injection.</span></span> <span data-ttu-id="034a1-173">[Unity](https://github.com/unitycontainer/unity) はそのようなライブラリの 1 つであり、Microsoft Patterns & Practices によって提供されます。</span><span class="sxs-lookup"><span data-stu-id="034a1-173">One such library is [Unity](https://github.com/unitycontainer/unity), provided by Microsoft Patterns & Practices.</span></span>

<span data-ttu-id="034a1-174">Unity を使用した依存関係の挿入の設定の例は `IDependencyResolver` 、をラップするを実装することです `UnityContainer` 。</span><span class="sxs-lookup"><span data-stu-id="034a1-174">An example of setting up dependency injection with Unity is implementing `IDependencyResolver` that wraps a `UnityContainer`:</span></span>

[!code-csharp[](samples/sample8.cs)]

<span data-ttu-id="034a1-175">`UnityContainer` のインスタンスを作成し、サービスを登録して、`HttpConfiguration` の依存関係リゾルバーをコンテナー用の `UnityResolver` の新しいインスタンスに設定します。</span><span class="sxs-lookup"><span data-stu-id="034a1-175">Create an instance of your `UnityContainer`, register your service, and set the dependency resolver of `HttpConfiguration` to the new instance of `UnityResolver` for your container:</span></span>

[!code-csharp[](samples/sample9.cs)]

<span data-ttu-id="034a1-176">必要な場所に `IProductRepository` を挿入します。</span><span class="sxs-lookup"><span data-stu-id="034a1-176">Inject `IProductRepository` where needed:</span></span>

[!code-csharp[](samples/sample5.cs)]

<span data-ttu-id="034a1-177">依存関係の挿入は ASP.NET Core の一部であるため、でサービスを追加でき `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="034a1-177">Because dependency injection is part of ASP.NET Core, you can add your service in the `Startup.ConfigureServices`:</span></span>

[!code-csharp[](samples/configure-services.cs)]

<span data-ttu-id="034a1-178">Unity でそうであったように、リポジトリは任意の場所に挿入できます。</span><span class="sxs-lookup"><span data-stu-id="034a1-178">The repository can be injected anywhere, as was true with Unity.</span></span>

<span data-ttu-id="034a1-179">ASP.NET Core での依存関係の挿入の詳細については、「」を参照してください <xref:fundamentals/dependency-injection> 。</span><span class="sxs-lookup"><span data-stu-id="034a1-179">For more information on dependency injection in ASP.NET Core, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="serving-static-files"></a><span data-ttu-id="034a1-180">静的ファイルの提供</span><span class="sxs-lookup"><span data-stu-id="034a1-180">Serving static files</span></span>

<span data-ttu-id="034a1-181">Web 開発の重要な部分は、静的なクライアント側アセットを提供する機能です。</span><span class="sxs-lookup"><span data-stu-id="034a1-181">An important part of web development is the ability to serve static, client-side assets.</span></span> <span data-ttu-id="034a1-182">静的なファイルの最も一般的な例は、HTML、CSS、Javascript、およびイメージです。</span><span class="sxs-lookup"><span data-stu-id="034a1-182">The most common examples of static files are HTML, CSS, Javascript, and images.</span></span> <span data-ttu-id="034a1-183">これらのファイルは、アプリ (または CDN) の公開された場所に保存され、要求によって読み込めるように参照される必要があります。</span><span class="sxs-lookup"><span data-stu-id="034a1-183">These files need to be saved in the published location of the app (or CDN) and referenced so they can be loaded by a request.</span></span> <span data-ttu-id="034a1-184">このプロセスは、ASP.NET Core で変更されました。</span><span class="sxs-lookup"><span data-stu-id="034a1-184">This process has changed in ASP.NET Core.</span></span>

<span data-ttu-id="034a1-185">ASP.NET では、静的ファイルはさまざまなディレクトリに保存され、ビューで参照されます。</span><span class="sxs-lookup"><span data-stu-id="034a1-185">In ASP.NET, static files are stored in various directories and referenced in the views.</span></span>

<span data-ttu-id="034a1-186">ASP.NET Core では、構成が変更されていない限り、静的ファイルは "Web ルート" ( *&lt;コンテンツ ルート&gt;/wwwroot* ) に保存されます。</span><span class="sxs-lookup"><span data-stu-id="034a1-186">In ASP.NET Core, static files are stored in the "web root" ( *&lt;content root&gt;/wwwroot* ), unless configured otherwise.</span></span> <span data-ttu-id="034a1-187">ファイルは、`Startup.Configure` から `UseStaticFiles` 拡張メソッドを呼び出すことによって、要求パイプラインに読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="034a1-187">The files are loaded into the request pipeline by invoking the `UseStaticFiles` extension method from `Startup.Configure`:</span></span>

[!code-csharp[](../../fundamentals/static-files/samples/1.x/StaticFilesSample/StartupStaticFiles.cs?highlight=3&name=snippet_ConfigureMethod)]

<span data-ttu-id="034a1-188">**注:** .NET Framework を対象にする場合は、NuGet パッケージ `Microsoft.AspNetCore.StaticFiles` をインストールします。</span><span class="sxs-lookup"><span data-stu-id="034a1-188">**Note:** If targeting .NET Framework, install the NuGet package `Microsoft.AspNetCore.StaticFiles`.</span></span>

<span data-ttu-id="034a1-189">たとえば、 *wwwroot/images* フォルダー内のイメージ アセットには、ブラウザーから `http://<app>/images/<imageFileName>` などの場所でアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="034a1-189">For example, an image asset in the *wwwroot/images* folder is accessible to the browser at a location such as `http://<app>/images/<imageFileName>`.</span></span>

<span data-ttu-id="034a1-190">**注:** ASP.NET Core での静的ファイルの提供に関する詳細なリファレンスについては、「」を参照してください <xref:fundamentals/static-files> 。</span><span class="sxs-lookup"><span data-stu-id="034a1-190">**Note:** For a more in-depth reference to serving static files in ASP.NET Core, see <xref:fundamentals/static-files>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="034a1-191">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="034a1-191">Additional resources</span></span>

* [<span data-ttu-id="034a1-192">.NET Core にライブラリを移植する</span><span class="sxs-lookup"><span data-stu-id="034a1-192">Porting Libraries to .NET Core</span></span>](/dotnet/core/porting/libraries)
