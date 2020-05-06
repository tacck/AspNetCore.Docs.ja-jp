---
title: ASP.NET Core Blazor のホストと展開
author: guardrex
description: Blazor アプリをホストおよび展開する方法を説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/30/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/blazor/index
ms.openlocfilehash: 9d57b81cd813d02a65b6d3a39c7f1a1aa8a069c7
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775168"
---
# <a name="host-and-deploy-aspnet-core-blazor"></a><span data-ttu-id="4767e-103">ASP.NET Core Blazor のホストと展開</span><span class="sxs-lookup"><span data-stu-id="4767e-103">Host and deploy ASP.NET Core Blazor</span></span>

<span data-ttu-id="4767e-104">作成者: [Luke Latham](https://github.com/guardrex)、[Rainer Stropek](https://www.timecockpit.com)、[Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="4767e-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

## <a name="publish-the-app"></a><span data-ttu-id="4767e-105">アプリの発行</span><span class="sxs-lookup"><span data-stu-id="4767e-105">Publish the app</span></span>

<span data-ttu-id="4767e-106">アプリは、リリース構成での展開のために発行されます。</span><span class="sxs-lookup"><span data-stu-id="4767e-106">Apps are published for deployment in Release configuration.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4767e-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4767e-107">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="4767e-108">**[ビルド]**  >  **[Publish {APPLICATION}]\({アプリケーション} を発行する\)** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4767e-108">Select **Build** > **Publish {APPLICATION}** from the navigation bar.</span></span>
1. <span data-ttu-id="4767e-109">*[publish target]\(発行先\)* を選択します。</span><span class="sxs-lookup"><span data-stu-id="4767e-109">Select the *publish target*.</span></span> <span data-ttu-id="4767e-110">ローカルに発行するには、 **[フォルダー]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4767e-110">To publish locally, select **Folder**.</span></span>
1. <span data-ttu-id="4767e-111">**[フォルダーの選択]** フィールド内で既定の場所を受け入れるか、または別の場所を指定します。</span><span class="sxs-lookup"><span data-stu-id="4767e-111">Accept the default location in the **Choose a folder** field or specify a different location.</span></span> <span data-ttu-id="4767e-112">**[発行]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="4767e-112">Select the **Publish** button.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4767e-113">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4767e-113">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="4767e-114">**[ビルド]**  >  **[Publish to Folder]\(フォルダーに発行\)** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4767e-114">Select **Build** > **Publish to Folder**.</span></span>
1. <span data-ttu-id="4767e-115">発行されたアセットを受信するフォルダーを確認し、 **[Publish]\(発行\)** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4767e-115">Confirm the folder to receive the published assets and select **Publish**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="4767e-116">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="4767e-116">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="4767e-117">[dotnet publish](/dotnet/core/tools/dotnet-publish) コマンドを使用して、リリース構成によってアプリを発行します。</span><span class="sxs-lookup"><span data-stu-id="4767e-117">Use the [dotnet publish](/dotnet/core/tools/dotnet-publish) command to publish the app with a Release configuration:</span></span>

```dotnetcli
dotnet publish -c Release
```

---

<span data-ttu-id="4767e-118">アプリを発行すると、プロジェクトの依存関係の[復元](/dotnet/core/tools/dotnet-restore)がトリガーされ、展開されるアセットを作成する前にプロジェクトが[ビルド](/dotnet/core/tools/dotnet-build)されます。</span><span class="sxs-lookup"><span data-stu-id="4767e-118">Publishing the app triggers a [restore](/dotnet/core/tools/dotnet-restore) of the project's dependencies and [builds](/dotnet/core/tools/dotnet-build) the project before creating the assets for deployment.</span></span> <span data-ttu-id="4767e-119">ビルド プロセスの一環として、アプリのダウンロード サイズを縮小し読み込み時間を短縮するため、未使用のメソッドおよびアセンブリが削除されます。</span><span class="sxs-lookup"><span data-stu-id="4767e-119">As part of the build process, unused methods and assemblies are removed to reduce app download size and load times.</span></span>

<span data-ttu-id="4767e-120">発行場所:</span><span class="sxs-lookup"><span data-stu-id="4767e-120">Publish locations:</span></span>

* Blazor<span data-ttu-id="4767e-121"> WebAssembly</span><span class="sxs-lookup"><span data-stu-id="4767e-121"> WebAssembly</span></span>
  * <span data-ttu-id="4767e-122">スタンドアロン &ndash; アプリは " */bin/Release/{ターゲット フレームワーク}/publish/wwwroot*" フォルダーに発行されます。</span><span class="sxs-lookup"><span data-stu-id="4767e-122">Standalone &ndash; The app is published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder.</span></span> <span data-ttu-id="4767e-123">アプリを静的サイトとして展開するには、*wwwroot* フォルダーの内容を静的サイトのホストにコピーします。</span><span class="sxs-lookup"><span data-stu-id="4767e-123">To deploy the app as a static site, copy the contents of the *wwwroot* folder to the static site host.</span></span>
  * <span data-ttu-id="4767e-124">ホステッド &ndash; クライアント Blazor WebAssembly アプリは、サーバ ーアプリの他の静的な Web アセットと共に、サーバー アプリの " */bin/Release/{ターゲット フレームワーク}/publish/wwwroot*" フォルダーに発行されます。</span><span class="sxs-lookup"><span data-stu-id="4767e-124">Hosted &ndash; The client Blazor WebAssembly app is published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="4767e-125">*publish* フォルダーの内容をホストに展開します。</span><span class="sxs-lookup"><span data-stu-id="4767e-125">Deploy the contents of the *publish* folder to the host.</span></span>
* Blazor<span data-ttu-id="4767e-126"> サーバー &ndash; アプリは " */bin/Release/{ターゲット フレームワーク}/publish*" フォルダーに発行されます。</span><span class="sxs-lookup"><span data-stu-id="4767e-126"> Server &ndash; The app is published into the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span> <span data-ttu-id="4767e-127">*publish* フォルダーの内容をホストに展開します。</span><span class="sxs-lookup"><span data-stu-id="4767e-127">Deploy the contents of the *publish* folder to the host.</span></span>

<span data-ttu-id="4767e-128">フォルダー内のアセットは、Web サーバーに展開されます。</span><span class="sxs-lookup"><span data-stu-id="4767e-128">The assets in the folder are deployed to the web server.</span></span> <span data-ttu-id="4767e-129">展開のプロセスが手動であるか自動であるかは、ご使用の展開ツールによって異なります。</span><span class="sxs-lookup"><span data-stu-id="4767e-129">Deployment might be a manual or automated process depending on the development tools in use.</span></span>

## <a name="app-base-path"></a><span data-ttu-id="4767e-130">アプリのベース パス</span><span class="sxs-lookup"><span data-stu-id="4767e-130">App base path</span></span>

<span data-ttu-id="4767e-131">*アプリのベース パス*とは、アプリのルート URL パスのことです。</span><span class="sxs-lookup"><span data-stu-id="4767e-131">The *app base path* is the app's root URL path.</span></span> <span data-ttu-id="4767e-132">次の ASP.NET Core アプリと Blazor サブアプリについて考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="4767e-132">Consider the following ASP.NET Core app and Blazor sub-app:</span></span>

* <span data-ttu-id="4767e-133">ASP.NET Core アプリは `MyApp` と命名します。</span><span class="sxs-lookup"><span data-stu-id="4767e-133">The ASP.NET Core app is named `MyApp`:</span></span>
  * <span data-ttu-id="4767e-134">このアプリは、物理的に *d:/MyApp* にあります。</span><span class="sxs-lookup"><span data-stu-id="4767e-134">The app physically resides at *d:/MyApp*.</span></span>
  * <span data-ttu-id="4767e-135">要求は、`https://www.contoso.com/{MYAPP RESOURCE}` で受信されます。</span><span class="sxs-lookup"><span data-stu-id="4767e-135">Requests are received at `https://www.contoso.com/{MYAPP RESOURCE}`.</span></span>
* <span data-ttu-id="4767e-136">`CoolApp` という名前の Blazor アプリは `MyApp` のサブアプリです。</span><span class="sxs-lookup"><span data-stu-id="4767e-136">A Blazor app named `CoolApp` is a sub-app of `MyApp`:</span></span>
  * <span data-ttu-id="4767e-137">このサブアプリは、物理的に *d:/MyApp/CoolApp* にあります。</span><span class="sxs-lookup"><span data-stu-id="4767e-137">The sub-app physically resides at *d:/MyApp/CoolApp*.</span></span>
  * <span data-ttu-id="4767e-138">要求は、`https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}` で受信されます。</span><span class="sxs-lookup"><span data-stu-id="4767e-138">Requests are received at `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}`.</span></span>

<span data-ttu-id="4767e-139">`CoolApp` に対して構成を追加指定しない場合、このシナリオではサブ アプリにはそれがサーバー上のどこの場所にあるかわかりません。</span><span class="sxs-lookup"><span data-stu-id="4767e-139">Without specifying additional configuration for `CoolApp`, the sub-app in this scenario has no knowledge of where it resides on the server.</span></span> <span data-ttu-id="4767e-140">たとえば、相対 URL パス `/CoolApp/` にあることがわからない場合、アプリはそのリソースに対する正しい相対 URL を作成できません。</span><span class="sxs-lookup"><span data-stu-id="4767e-140">For example, the app can't construct correct relative URLs to its resources without knowing that it resides at the relative URL path `/CoolApp/`.</span></span>

<span data-ttu-id="4767e-141">`<base>` タグの `href` 属性は、Blazor アプリのベース パスの `https://www.contoso.com/CoolApp/` に構成を指定するため、*Pages/_Host.cshtml* ファイル (Blazor サーバー) または *wwwroot/index.html* ファイル (Blazor WebAssembly) の相対ルート パスに設定されます。</span><span class="sxs-lookup"><span data-stu-id="4767e-141">To provide configuration for the Blazor app's base path of `https://www.contoso.com/CoolApp/`, the `<base>` tag's `href` attribute is set to the relative root path in the *Pages/_Host.cshtml* file (Blazor Server) or *wwwroot/index.html* file (Blazor WebAssembly):</span></span>

```html
<base href="/CoolApp/">
```

Blazor<span data-ttu-id="4767e-142"> サーバー アプリはさらに、`Startup.Configure` のアプリの要求パイプラインで <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> を呼び出すことによって、サーバー側のベース パスを設定します。</span><span class="sxs-lookup"><span data-stu-id="4767e-142"> Server apps additionally set the server-side base path by calling <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> in the app's request pipeline of `Startup.Configure`:</span></span>

```csharp
app.UsePathBase("/CoolApp");
```

<span data-ttu-id="4767e-143">相対 URL を指定することにより、ルート ディレクトリに存在しないコンポーネントでアプリのルート パスへの相対 URL を構築できます。</span><span class="sxs-lookup"><span data-stu-id="4767e-143">By providing the relative URL path, a component that isn't in the root directory can construct URLs relative to the app's root path.</span></span> <span data-ttu-id="4767e-144">ディレクトリ構造の別のレベルに存在するコンポーネントが、アプリ内のさまざまな場所にある他のリソースに対するリンクを構築できます。</span><span class="sxs-lookup"><span data-stu-id="4767e-144">Components at different levels of the directory structure can build links to other resources at locations throughout the app.</span></span> <span data-ttu-id="4767e-145">アプリのベース パスは、リンクの `href` ターゲットがアプリのベース パス URI 空間内にある、選択されたハイパーリンクの阻止にも使用されます。</span><span class="sxs-lookup"><span data-stu-id="4767e-145">The app base path is also used to intercept selected hyperlinks where the `href` target of the link is within the app base path URI space.</span></span> <span data-ttu-id="4767e-146">内部のナビゲーションは、Blazor ルーターによって処理されます。</span><span class="sxs-lookup"><span data-stu-id="4767e-146">The Blazor router handles the internal navigation.</span></span>

<span data-ttu-id="4767e-147">多くのホスティング シナリオでは、アプリへの相対 URL パスは、アプリのルートです。</span><span class="sxs-lookup"><span data-stu-id="4767e-147">In many hosting scenarios, the relative URL path to the app is the root of the app.</span></span> <span data-ttu-id="4767e-148">これらの場合、アプリの相対 URL ベース パスにフォワード スラッシュ (`<base href="/" />`) が付きます。これは、Blazor アプリの既定の構成です。</span><span class="sxs-lookup"><span data-stu-id="4767e-148">In these cases, the app's relative URL base path is a forward slash (`<base href="/" />`), which is the default configuration for a Blazor app.</span></span> <span data-ttu-id="4767e-149">GitHub ページと IIS サブアプリなど、その他のホスティング シナリオの場合、アプリのベースパスは、アプリへのサーバーの相対 URL パスに設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="4767e-149">In other hosting scenarios, such as GitHub Pages and IIS sub-apps, the app base path must be set to the server's relative URL path of the app.</span></span>

<span data-ttu-id="4767e-150">アプリのベース パスを設定するには、*Pages/_Host.cshtml* ファイル (Blazor サーバー) または *wwwroot/index.html* ファイル (Blazor WebAssembly) の `<head>` タグ要素内の `<base>` を更新します。</span><span class="sxs-lookup"><span data-stu-id="4767e-150">To set the app's base path, update the `<base>` tag within the `<head>` tag elements of the *Pages/_Host.cshtml* file (Blazor Server) or *wwwroot/index.html* file (Blazor WebAssembly).</span></span> <span data-ttu-id="4767e-151">`href` 属性値を `/{RELATIVE URL PATH}/` (末尾にスラッシュが必要) に設定します。ここで、`{RELATIVE URL PATH}` は、アプリの完全な相対 URL パスです。</span><span class="sxs-lookup"><span data-stu-id="4767e-151">Set the `href` attribute value to `/{RELATIVE URL PATH}/` (the trailing slash is required), where `{RELATIVE URL PATH}` is the app's full relative URL path.</span></span>

<span data-ttu-id="4767e-152">ルート以外の相対 URL パスが構成されている Blazor WebAssembly アプリの場合 (例: `<base href="/CoolApp/">`)、そのアプリは*ローカルで実行*されると自身のリソースを見つけることができません。</span><span class="sxs-lookup"><span data-stu-id="4767e-152">For an Blazor WebAssembly app with a non-root relative URL path (for example, `<base href="/CoolApp/">`), the app fails to find its resources *when run locally*.</span></span> <span data-ttu-id="4767e-153">ローカルでの開発およびテスト中は、実行時の `<base>` タグの `href` 値と一致する*パス ベース*引数を指定することで、この問題を克服することができます。</span><span class="sxs-lookup"><span data-stu-id="4767e-153">To overcome this problem during local development and testing, you can supply a *path base* argument that matches the `href` value of the `<base>` tag at runtime.</span></span> <span data-ttu-id="4767e-154">末尾にはスラッシュを含めないでください。</span><span class="sxs-lookup"><span data-stu-id="4767e-154">Don't include a trailing slash.</span></span> <span data-ttu-id="4767e-155">アプリをローカルで実行しているときにパス ベースの引数を渡すには、アプリのディレクトリから `--pathbase` オプションを指定して `dotnet run` コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="4767e-155">To pass the path base argument when running the app locally, execute the `dotnet run` command from the app's directory with the `--pathbase` option:</span></span>

```dotnetcli
dotnet run --pathbase=/{RELATIVE URL PATH (no trailing slash)}
```

<span data-ttu-id="4767e-156">相対 URL パスが `/CoolApp/` (`<base href="/CoolApp/">`) の Blazor WebAssembly アプリについては、このコマンドは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="4767e-156">For a Blazor WebAssembly app with a relative URL path of `/CoolApp/` (`<base href="/CoolApp/">`), the command is:</span></span>

```dotnetcli
dotnet run --pathbase=/CoolApp
```

<span data-ttu-id="4767e-157">Blazor WebAssembly アプリは `http://localhost:port/CoolApp` でローカルで応答します。</span><span class="sxs-lookup"><span data-stu-id="4767e-157">The Blazor WebAssembly app responds locally at `http://localhost:port/CoolApp`.</span></span>

## <a name="deployment"></a><span data-ttu-id="4767e-158">配置</span><span class="sxs-lookup"><span data-stu-id="4767e-158">Deployment</span></span>

<span data-ttu-id="4767e-159">展開のガイダンスについては、次のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="4767e-159">For deployment guidance, see the following topics:</span></span>

* <xref:host-and-deploy/blazor/webassembly>
* <xref:host-and-deploy/blazor/server>
