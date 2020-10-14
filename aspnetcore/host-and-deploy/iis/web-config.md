---
title: web.config ファイル
author: rick-anderson
description: web.config ファイルの内容と、さまざまな ASP.NET Core モジュール オプションの構成方法について説明します。
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/iis/web-config
ms.openlocfilehash: 4d7305f7184745b66c5de6c86b907d419183cb3f
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/06/2020
ms.locfileid: "91755193"
---
# <a name="webconfig-file"></a><span data-ttu-id="a76f2-103">`web.config` ファイル</span><span class="sxs-lookup"><span data-stu-id="a76f2-103">`web.config` file</span></span>

<span data-ttu-id="a76f2-104">`web.config` は IIS によって、および IIS でホストされるアプリを構成するための [ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)によって読み取られるファイルです。</span><span class="sxs-lookup"><span data-stu-id="a76f2-104">The `web.config` is a file that is read by IIS and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to configure an app hosted with IIS.</span></span>

## <a name="webconfig-file-location"></a><span data-ttu-id="a76f2-105">`web.config` ファイルの場所</span><span class="sxs-lookup"><span data-stu-id="a76f2-105">`web.config` file location</span></span>

<span data-ttu-id="a76f2-106">[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)を正しく設定するためには、`web.config` ファイルが、展開されるアプリの[コンテンツ ルート](xref:fundamentals/index#content-root) パス (通常はアプリ ベース パス) に存在する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a76f2-106">In order to set up the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) correctly, the `web.config` file must be present at the [content root](xref:fundamentals/index#content-root) path (typically the app base path) of the deployed app.</span></span> <span data-ttu-id="a76f2-107">これは、IIS に提供される web サイトの物理パスと同じ場所です。</span><span class="sxs-lookup"><span data-stu-id="a76f2-107">This is the same location as the website physical path provided to IIS.</span></span> <span data-ttu-id="a76f2-108">`web.config` ファイルは、Web 配置を使用して複数のアプリの発行を有効にするため、アプリのルートで必要です。</span><span class="sxs-lookup"><span data-stu-id="a76f2-108">The `web.config` file is required at the root of the app to enable the publishing of multiple apps using Web Deploy.</span></span>

<span data-ttu-id="a76f2-109">アプリの物理パスには、`{ASSEMBLY}.runtimeconfig.json`、`{ASSEMBLY}.xml` (XML ドキュメントのコメント)、`{ASSEMBLY}.deps.json` (プレースホルダー `{ASSEMBLY}` はアセンブリ名) などの機密性の高いファイルが存在します。</span><span class="sxs-lookup"><span data-stu-id="a76f2-109">Sensitive files exist on the app's physical path, such as `{ASSEMBLY}.runtimeconfig.json`, `{ASSEMBLY}.xml` (XML Documentation comments), and `{ASSEMBLY}.deps.json`, where the placeholder `{ASSEMBLY}` is the assembly name.</span></span> <span data-ttu-id="a76f2-110">`web.config` ファイルが存在し、サイトは通常どおり起動した場合、IIS は、これらの機密性の高いファイルが要求された場合にファイルを提供しません。</span><span class="sxs-lookup"><span data-stu-id="a76f2-110">When the `web.config` file is present and the site starts normally, IIS doesn't serve these sensitive files if they're requested.</span></span> <span data-ttu-id="a76f2-111">`web.config`ファイルが存在しないか、不適切な名前が付けられているか、または通常の起動用にサイトを構成できない場合、IIS が機密性の高いファイルを公開する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a76f2-111">If the `web.config` file is missing, incorrectly named, or unable to configure the site for normal startup, IIS may serve sensitive files publicly.</span></span>

<span data-ttu-id="a76f2-112">**`web.config`ファイルは、展開環境に常に存在し、適切な名前が付けられ、通常の起動用にサイトを構成できる必要があります。`web.config` ファイルを運用環境の展開から削除しないでください。**</span><span class="sxs-lookup"><span data-stu-id="a76f2-112">**The `web.config` file must be present in the deployment at all times, correctly named, and able to configure the site for normal start up. Never remove the `web.config` file from a production deployment.**</span></span>

<span data-ttu-id="a76f2-113">プロジェクト内に `web.config` ファイルが存在しない場合、そのファイルは ASP.NET Core モジュールを構成するための適切な `processPath` と `arguments` を使用して作成され、[発行された出力](xref:host-and-deploy/directory-structure)に移行されます。</span><span class="sxs-lookup"><span data-stu-id="a76f2-113">If a `web.config` file isn't present in the project, the file is created with the correct `processPath` and `arguments` to configure the ASP.NET Core Module and moved to [published output](xref:host-and-deploy/directory-structure).</span></span>

<span data-ttu-id="a76f2-114">プロジェクトに `web.config` ファイルが含まれていない場合、そのファイルは ASP.NET Core モジュールを構成するための正しい `processPath` と `arguments` を使用して作成され、発行された出力に移行されます。</span><span class="sxs-lookup"><span data-stu-id="a76f2-114">If a `web.config` file is present in the project, the file is transformed with the correct `processPath` and `arguments` to configure the ASP.NET Core Module and moved to published output.</span></span> <span data-ttu-id="a76f2-115">変換によりファイル内の IIS 構成の設定が変わることはありません。</span><span class="sxs-lookup"><span data-stu-id="a76f2-115">The transformation doesn't modify IIS configuration settings in the file.</span></span>

<span data-ttu-id="a76f2-116">`web.config` ファイルは、アクティブな IIS モジュールを制御する追加の IIS 構成設定を提供する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a76f2-116">The `web.config` file may provide additional IIS configuration settings that control active IIS modules.</span></span> <span data-ttu-id="a76f2-117">ASP.NET Core アプリを使用して要求を処理できる IIS モジュールの詳細については、[IIS モジュール](xref:host-and-deploy/iis/modules)のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="a76f2-117">For information on IIS modules that are capable of processing requests with ASP.NET Core apps, see the [IIS modules](xref:host-and-deploy/iis/modules) topic.</span></span>

<span data-ttu-id="a76f2-118">`web.config` ファイルの作成、変換、および公開は、プロジェクトの公開時に、MSBuild ターゲット (`_TransformWebConfig`) によって処理されます。</span><span class="sxs-lookup"><span data-stu-id="a76f2-118">Creating, transforming, and publishing the `web.config` file is handled by an MSBuild target (`_TransformWebConfig`) when the project is published.</span></span> <span data-ttu-id="a76f2-119">このターゲットは、Web SDK ターゲット (`Microsoft.NET.Sdk.Web`) に存在します。</span><span class="sxs-lookup"><span data-stu-id="a76f2-119">This target is present in the Web SDK targets (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="a76f2-120">SDK は、プロジェクト ファイルの先頭で設定されています。</span><span class="sxs-lookup"><span data-stu-id="a76f2-120">The SDK is set at the top of the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="a76f2-121">Web SDK によって `web.config` ファイルが変換されないようにするため、`<IsTransformWebConfigDisabled>` プロパティをプロジェクト ファイルで使用します。</span><span class="sxs-lookup"><span data-stu-id="a76f2-121">To prevent the Web SDK from transforming the `web.config` file, use the `<IsTransformWebConfigDisabled>` property in the project file:</span></span>

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="a76f2-122">Web SDK ファイルの変換を無効にすると、 `processPath`と`arguments`開発者によって手動で設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a76f2-122">When disabling the Web SDK from transforming the file, the `processPath` and `arguments` should be manually set by the developer.</span></span> <span data-ttu-id="a76f2-123">詳細については、<xref:host-and-deploy/aspnet-core-module> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a76f2-123">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="configuration-of-aspnet-core-module-with-webconfig"></a><span data-ttu-id="a76f2-124">`web.config` を使用した ASP.NET Core モジュールの構成</span><span class="sxs-lookup"><span data-stu-id="a76f2-124">Configuration of ASP.NET Core Module with `web.config`</span></span>

<span data-ttu-id="a76f2-125">ASP.NET Core モジュールは、サイトの `web.config` ファイルの `system.webServer` ノードの `aspNetCore` セクションを使って構成します。</span><span class="sxs-lookup"><span data-stu-id="a76f2-125">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's `web.config` file.</span></span>

<span data-ttu-id="a76f2-126">次に示す `web.config` ファイルは、[フレームワークに依存する展開](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd)用に発行されたもので、サイトの要求を処理するように ASP.NET Core モジュールを構成します。</span><span class="sxs-lookup"><span data-stu-id="a76f2-126">The following `web.config` file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath="dotnet"
                  arguments=".\MyApp.dll"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="a76f2-127">次の `web.config` は、[自己完結型の展開](/dotnet/articles/core/deploying/#self-contained-deployments-scd)用に発行されたものです。</span><span class="sxs-lookup"><span data-stu-id="a76f2-127">The following `web.config` is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath=".\MyApp.exe"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="a76f2-128"><xref:System.Configuration.SectionInformation.InheritInChildApplications%2A> プロパティは、`false` に設定されます。これは、[`<location>`](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) 要素内で指定された設定が、アプリのサブディレクトリにあるアプリによって継承されないことを示します。</span><span class="sxs-lookup"><span data-stu-id="a76f2-128">The <xref:System.Configuration.SectionInformation.InheritInChildApplications%2A> property is set to `false` to indicate that the settings specified within the [`<location>`](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="a76f2-129">アプリが [Azure App Service](https://azure.microsoft.com/services/app-service/) に対して展開されると、`stdoutLogFile` パスは `\\?\%home%\LogFiles\stdout` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="a76f2-129">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="a76f2-130">パスは stdout ログを `LogFiles` フォルダーに保存します。これは、サービスによって自動的に作成される場所です。</span><span class="sxs-lookup"><span data-stu-id="a76f2-130">The path saves stdout logs to the `LogFiles` folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="a76f2-131">IIS サブアプリケーション構成について詳しくは、「<xref:host-and-deploy/iis/index#sub-applications>」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="a76f2-131">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="a76f2-132">`aspNetCore` 要素の属性</span><span class="sxs-lookup"><span data-stu-id="a76f2-132">Attributes of the `aspNetCore` element</span></span>

| <span data-ttu-id="a76f2-133">属性</span><span class="sxs-lookup"><span data-stu-id="a76f2-133">Attribute</span></span> | <span data-ttu-id="a76f2-134">説明</span><span class="sxs-lookup"><span data-stu-id="a76f2-134">Description</span></span> | <span data-ttu-id="a76f2-135">Default</span><span class="sxs-lookup"><span data-stu-id="a76f2-135">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="a76f2-136">省略可能な文字列属性。</span><span class="sxs-lookup"><span data-stu-id="a76f2-136">Optional string attribute.</span></span></p><p><span data-ttu-id="a76f2-137">`processPath` において指定されている実行可能ファイルへの引数です。</span><span class="sxs-lookup"><span data-stu-id="a76f2-137">Arguments to the executable specified in `processPath`.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="a76f2-138">省略可能な Boolean 属性です。</span><span class="sxs-lookup"><span data-stu-id="a76f2-138">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="a76f2-139">true の場合、**502.5 - 処理エラー** ページは抑制され、`web.config` で構成されている 502 状態コード ページが優先されます。</span><span class="sxs-lookup"><span data-stu-id="a76f2-139">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the `web.config` takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="a76f2-140">省略可能な Boolean 属性です。</span><span class="sxs-lookup"><span data-stu-id="a76f2-140">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="a76f2-141">true の場合、トークンは、`%ASPNETCORE_PORT%` 上でリッスンしている子プロセスに、要求ごとの 'MS-ASPNETCORE-WINAUTHTOKEN' ヘッダーとして転送されます。</span><span class="sxs-lookup"><span data-stu-id="a76f2-141">If true, the token is forwarded to the child process listening on `%ASPNETCORE_PORT%` as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="a76f2-142">要求ごとのこのトークンで CloseHandle を呼び出すのは、そのプロセスの役割です。</span><span class="sxs-lookup"><span data-stu-id="a76f2-142">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="a76f2-143">省略可能な文字列属性。</span><span class="sxs-lookup"><span data-stu-id="a76f2-143">Optional string attribute.</span></span></p><p><span data-ttu-id="a76f2-144">ホスティング モデルをインプロセス (`InProcess`/`inprocess`) またはアウト プロセス (`OutOfProcess`/`outofprocess`) として指定します。</span><span class="sxs-lookup"><span data-stu-id="a76f2-144">Specifies the hosting model as in-process (`InProcess`/`inprocess`) or out-of-process (`OutOfProcess`/`outofprocess`).</span></span></p> | `InProcess`<br>`inprocess` |
| `processesPerApplication` | <p><span data-ttu-id="a76f2-145">省略可能な整数属性</span><span class="sxs-lookup"><span data-stu-id="a76f2-145">Optional integer attribute.</span></span></p><p><span data-ttu-id="a76f2-146">アプリごとにスピンアップすることができる `processPath` 設定内で指定したプロセスのインスタンス数が指定されます。</span><span class="sxs-lookup"><span data-stu-id="a76f2-146">Specifies the number of instances of the process specified in the `processPath` setting that can be spun up per app.</span></span></p><p><span data-ttu-id="a76f2-147">&dagger;インプロセス ホスティングの場合、値は `1` に制限されます。</span><span class="sxs-lookup"><span data-stu-id="a76f2-147">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="a76f2-148">設定 `processesPerApplication` は推奨されません。</span><span class="sxs-lookup"><span data-stu-id="a76f2-148">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="a76f2-149">この属性は将来のリリースで削除されます。</span><span class="sxs-lookup"><span data-stu-id="a76f2-149">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="a76f2-150">既定値: `1`</span><span class="sxs-lookup"><span data-stu-id="a76f2-150">Default: `1`</span></span><br><span data-ttu-id="a76f2-151">最小値: `1`</span><span class="sxs-lookup"><span data-stu-id="a76f2-151">Min: `1`</span></span><br><span data-ttu-id="a76f2-152">最大値: `100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="a76f2-152">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="a76f2-153">必須の文字列属性です。</span><span class="sxs-lookup"><span data-stu-id="a76f2-153">Required string attribute.</span></span></p><p><span data-ttu-id="a76f2-154">HTTP 要求をリッスンするプロセスを起動する実行可能ファイルへのパスです。</span><span class="sxs-lookup"><span data-stu-id="a76f2-154">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="a76f2-155">相対パスがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="a76f2-155">Relative paths are supported.</span></span> <span data-ttu-id="a76f2-156">パスが `.` で始まる場合、パスはサイトのルートを基準とする相対パスであると見なされます。</span><span class="sxs-lookup"><span data-stu-id="a76f2-156">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="a76f2-157">省略可能な整数属性</span><span class="sxs-lookup"><span data-stu-id="a76f2-157">Optional integer attribute.</span></span></p><p><span data-ttu-id="a76f2-158">`processPath` で指定されているプロセスが 1 分間にクラッシュできる回数を指定します。</span><span class="sxs-lookup"><span data-stu-id="a76f2-158">Specifies the number of times the process specified in `processPath` is allowed to crash per minute.</span></span> <span data-ttu-id="a76f2-159">この制限を超えた場合、モジュールは、1 分間の残りの間、プロセスの起動を停止します。</span><span class="sxs-lookup"><span data-stu-id="a76f2-159">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="a76f2-160">インプロセス ホスティングではサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="a76f2-160">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="a76f2-161">既定値: `10`</span><span class="sxs-lookup"><span data-stu-id="a76f2-161">Default: `10`</span></span><br><span data-ttu-id="a76f2-162">最小値: `0`</span><span class="sxs-lookup"><span data-stu-id="a76f2-162">Min: `0`</span></span><br><span data-ttu-id="a76f2-163">最大値: `100`</span><span class="sxs-lookup"><span data-stu-id="a76f2-163">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="a76f2-164">省略可能な期間属性。</span><span class="sxs-lookup"><span data-stu-id="a76f2-164">Optional timespan attribute.</span></span></p><p><span data-ttu-id="a76f2-165">%ASPNETCORE_PORT% でリッスンしているプロセスからの応答を ASP.NET Core モジュールが待機する期間を指定します。</span><span class="sxs-lookup"><span data-stu-id="a76f2-165">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="a76f2-166">ASP.NET Core 2.1 以降のリリースに付属する ASP.NET Core モジュールのバージョンでは、`requestTimeout` は時間、分、および秒単位で指定します。</span><span class="sxs-lookup"><span data-stu-id="a76f2-166">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="a76f2-167">インプロセス ホスティングには適用されません。</span><span class="sxs-lookup"><span data-stu-id="a76f2-167">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="a76f2-168">インプロセス ホスティングの場合、アプリによって要求が処理されるまでモジュールは待機します。</span><span class="sxs-lookup"><span data-stu-id="a76f2-168">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="a76f2-169">0 から 59 までが文字列の分セグメントと秒セグメントで有効な値となります。</span><span class="sxs-lookup"><span data-stu-id="a76f2-169">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="a76f2-170">分または秒の値に `60` を入れると *500 - Internal Server Error* が出ます。</span><span class="sxs-lookup"><span data-stu-id="a76f2-170">Use of `60` in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> | <span data-ttu-id="a76f2-171">既定値: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="a76f2-171">Default: `00:02:00`</span></span><br><span data-ttu-id="a76f2-172">最小値: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="a76f2-172">Min: `00:00:00`</span></span><br><span data-ttu-id="a76f2-173">最大値: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="a76f2-173">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="a76f2-174">省略可能な整数属性</span><span class="sxs-lookup"><span data-stu-id="a76f2-174">Optional integer attribute.</span></span></p><p><span data-ttu-id="a76f2-175">`app_offline.htm` ファイルが検出されたときに、モジュールが実行可能ファイルの正常なシャットダウンを待機する秒単位の期間です。</span><span class="sxs-lookup"><span data-stu-id="a76f2-175">Duration in seconds that the module waits for the executable to gracefully shutdown when the `app_offline.htm` file is detected.</span></span></p> | <span data-ttu-id="a76f2-176">既定値: `10`</span><span class="sxs-lookup"><span data-stu-id="a76f2-176">Default: `10`</span></span><br><span data-ttu-id="a76f2-177">最小値: `0`</span><span class="sxs-lookup"><span data-stu-id="a76f2-177">Min: `0`</span></span><br><span data-ttu-id="a76f2-178">最大値: `600`</span><span class="sxs-lookup"><span data-stu-id="a76f2-178">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="a76f2-179">省略可能な整数属性</span><span class="sxs-lookup"><span data-stu-id="a76f2-179">Optional integer attribute.</span></span></p><p><span data-ttu-id="a76f2-180">ポートでリッスンするプロセスを実行可能ファイルが開始するのをモジュールが待機する秒単位の期間です。</span><span class="sxs-lookup"><span data-stu-id="a76f2-180">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="a76f2-181">この制限時間を超えた場合、モジュールはプロセスを強制終了します。</span><span class="sxs-lookup"><span data-stu-id="a76f2-181">If this time limit is exceeded, the module kills the process.</span></span></p><p><span data-ttu-id="a76f2-182">"*インプロセス*" でホスティングしている場合: プロセスは再起動されて**おらず**、`rapidFailsPerMinute` 設定が使用されて**いません**。</span><span class="sxs-lookup"><span data-stu-id="a76f2-182">When hosting *in-process*: The process is **not** restarted and does **not** use the `rapidFailsPerMinute` setting.</span></span></p><p><span data-ttu-id="a76f2-183">"*アウト プロセス*" でホスティングしている場合: 最後のローリング分においてアプリが `rapidFailsPerMinute` 回だけ開始を失敗しているのでないかぎり、モジュールは、新しい要求を受け取るとプロセスの再起動を試み、それ以降に受信した要求に対してプロセスの再起動を試み続けます。</span><span class="sxs-lookup"><span data-stu-id="a76f2-183">When hosting *out-of-process*: The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start `rapidFailsPerMinute` number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="a76f2-184">値 0 (ゼロ) は無限のタイムアウトと見なされ**ません**。</span><span class="sxs-lookup"><span data-stu-id="a76f2-184">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="a76f2-185">既定値: `120`</span><span class="sxs-lookup"><span data-stu-id="a76f2-185">Default: `120`</span></span><br><span data-ttu-id="a76f2-186">最小値: `0`</span><span class="sxs-lookup"><span data-stu-id="a76f2-186">Min: `0`</span></span><br><span data-ttu-id="a76f2-187">最大値: `3600`</span><span class="sxs-lookup"><span data-stu-id="a76f2-187">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="a76f2-188">省略可能な Boolean 属性です。</span><span class="sxs-lookup"><span data-stu-id="a76f2-188">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="a76f2-189">true の場合、`processPath` で指定されているプロセスの `stdout` と `stderr` は、`stdoutLogFile` で指定されているファイルにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="a76f2-189">If true, `stdout` and `stderr` for the process specified in `processPath` are redirected to the file specified in `stdoutLogFile`.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="a76f2-190">省略可能な文字列属性。</span><span class="sxs-lookup"><span data-stu-id="a76f2-190">Optional string attribute.</span></span></p><p><span data-ttu-id="a76f2-191">`processPath` で指定されているプロセスからの `stdout` と `stderr` がログに記録される相対ファイル パスまたは絶対ファイル パスを指定します。</span><span class="sxs-lookup"><span data-stu-id="a76f2-191">Specifies the relative or absolute file path for which `stdout` and `stderr` from the process specified in `processPath` are logged.</span></span> <span data-ttu-id="a76f2-192">相対パスの基準はサイトのルートです。</span><span class="sxs-lookup"><span data-stu-id="a76f2-192">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="a76f2-193">`.` で始まっているパスはすべてサイト ルートに対する相対パスであり、他のすべてのパスは絶対パスとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="a76f2-193">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="a76f2-194">パスで指定されたフォルダーは、ログ ファイルの作成時、モジュールによって作成されます。</span><span class="sxs-lookup"><span data-stu-id="a76f2-194">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="a76f2-195">アンダースコアの区切り記号を使って、タイムスタンプ、プロセス ID、およびファイル拡張子 ( `.log`) が、`stdoutLogFile` パスの最後のセグメントに追加されます。</span><span class="sxs-lookup"><span data-stu-id="a76f2-195">Using underscore delimiters, a timestamp, process ID, and file extension (`.log`) are added to the last segment of the `stdoutLogFile` path.</span></span> <span data-ttu-id="a76f2-196">たとえば、値として `.\logs\stdout` を指定し、2018 年 2 月 5 日の 19:41:32 にプロセス ID 1934 で保存すると、stdout ログは `logs` フォルダーに `stdout_20180205194132_1934.log` として保存されます。</span><span class="sxs-lookup"><span data-stu-id="a76f2-196">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as `stdout_20180205194132_1934.log` in the `logs` folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="set-environment-variables"></a><span data-ttu-id="a76f2-197">環境変数を設定する</span><span class="sxs-lookup"><span data-stu-id="a76f2-197">Set environment variables</span></span>

<span data-ttu-id="a76f2-198">`processPath` 属性で、プロセスに対する環境変数を指定できます。</span><span class="sxs-lookup"><span data-stu-id="a76f2-198">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="a76f2-199">`<environmentVariables>` コレクション要素の `<environmentVariable>` 子要素で、環境変数を指定します。</span><span class="sxs-lookup"><span data-stu-id="a76f2-199">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="a76f2-200">このセクションで設定された環境変数は、システム環境変数より優先されます。</span><span class="sxs-lookup"><span data-stu-id="a76f2-200">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="a76f2-201">次の例では、`web.config` 内に 2 つの環境変数が設定されています。</span><span class="sxs-lookup"><span data-stu-id="a76f2-201">The following example sets two environment variables in `web.config`.</span></span> <span data-ttu-id="a76f2-202">`ASPNETCORE_ENVIRONMENT` は、`Development` に対するアプリの環境を構成します。</span><span class="sxs-lookup"><span data-stu-id="a76f2-202">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="a76f2-203">開発者は、アプリの例外をデバッグするときに[開発者例外ページ](xref:fundamentals/error-handling)を強制的に読み込むため、`web.config` ファイルでこの値を一時的に設定できます。</span><span class="sxs-lookup"><span data-stu-id="a76f2-203">A developer may temporarily set this value in the `web.config` file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="a76f2-204">`CONFIG_DIR` はユーザー定義の環境変数の例です。開発者はここに、アプリの構成ファイルを読み込むためのパスを形成するために起動時に値を読み取るコードを記述してあります。</span><span class="sxs-lookup"><span data-stu-id="a76f2-204">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="inprocess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!NOTE]
> <span data-ttu-id="a76f2-205">`web.config` 内で環境を直接設定する代わりに、[発行プロファイル (`.pubxml`)](xref:host-and-deploy/visual-studio-publish-profiles) またはプロジェクト ファイルに `<EnvironmentName>` プロパティを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="a76f2-205">An alternative to setting the environment directly in `web.config` is to include the `<EnvironmentName>` property in the [publish profile (`.pubxml`)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="a76f2-206">この方法では、プロジェクトが発行されるときに `web.config` に環境が設定されます。</span><span class="sxs-lookup"><span data-stu-id="a76f2-206">This approach sets the environment in `web.config` when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="a76f2-207">インターネットなどの信頼されていないネットワークにアクセスできないステージング サーバーやテスト サーバーでは、`ASPNETCORE_ENVIRONMENT` 環境変数を `Development` に設定するだけです。</span><span class="sxs-lookup"><span data-stu-id="a76f2-207">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="configuration-of-iis-with-webconfig"></a><span data-ttu-id="a76f2-208">`web.config` を使用した IIS の構成</span><span class="sxs-lookup"><span data-stu-id="a76f2-208">Configuration of IIS with `web.config`</span></span>

<span data-ttu-id="a76f2-209">IIS の構成は ASP.NET Core モジュールを使用した ASP.NET Core アプリで機能する IIS シナリオの `web.config` の `<system.webServer>` セクションによる影響を受けます。</span><span class="sxs-lookup"><span data-stu-id="a76f2-209">IIS configuration is influenced by the `<system.webServer>` section of `web.config` for IIS scenarios that are functional for ASP.NET Core apps with the ASP.NET Core Module.</span></span> <span data-ttu-id="a76f2-210">たとえば、IIS の構成は動的な圧縮で機能します。</span><span class="sxs-lookup"><span data-stu-id="a76f2-210">For example, IIS configuration is functional for dynamic compression.</span></span> <span data-ttu-id="a76f2-211">IIS が動的な圧縮を使用するようにサーバー レベルで構成されている場合、アプリの `web.config` ファイルの `<urlCompression>` 要素を使用すると、それを ASP.NET Core アプリに対して無効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="a76f2-211">If IIS is configured at the server level to use dynamic compression, the `<urlCompression>` element in the app's `web.config` file can disable it for an ASP.NET Core app.</span></span>

<span data-ttu-id="a76f2-212">詳細については、次のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="a76f2-212">For more information, see the following topics:</span></span>

* [<span data-ttu-id="a76f2-213">`<system.webServer>` の構成リファレンス</span><span class="sxs-lookup"><span data-stu-id="a76f2-213">Configuration reference for `<system.webServer>`</span></span>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

<span data-ttu-id="a76f2-214">分離されたアプリ プール (IIS 10.0 以降でサポートされています) で実行する個別アプリに対して環境変数を設定するには、IIS のリファレンス ドキュメントで、「[環境変数`<environmentVariables>`](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe)」のトピックにある " *`AppCmd.exe` コマンド*" のセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="a76f2-214">To set environment variables for individual apps running in isolated app pools (supported for IIS 10.0 or later), see the *`AppCmd.exe` command* section of the [Environment Variables `<environmentVariables>`](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic in the IIS reference documentation.</span></span>

## <a name="configuration-sections-of-webconfig"></a><span data-ttu-id="a76f2-215">`web.config` の構成セクション</span><span class="sxs-lookup"><span data-stu-id="a76f2-215">Configuration sections of `web.config`</span></span>

<span data-ttu-id="a76f2-216">`web.config` の ASP.NET 4.x アプリの構成セクションは、ASP.NET Core アプリの構成では使用されません。</span><span class="sxs-lookup"><span data-stu-id="a76f2-216">Configuration sections of ASP.NET 4.x apps in `web.config` aren't used by ASP.NET Core apps for configuration:</span></span>

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

<span data-ttu-id="a76f2-217">ASP.NET Core アプリは、他の構成プロバイダーを使用して構成されます。</span><span class="sxs-lookup"><span data-stu-id="a76f2-217">ASP.NET Core apps are configured using other configuration providers.</span></span> <span data-ttu-id="a76f2-218">詳細については、[構成](xref:fundamentals/configuration/index)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="a76f2-218">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

## <a name="transform-webconfig"></a><span data-ttu-id="a76f2-219">web.config を変換する</span><span class="sxs-lookup"><span data-stu-id="a76f2-219">Transform web.config</span></span>

<span data-ttu-id="a76f2-220">発行時に "`web.config`" を変換する場合は、「<xref:host-and-deploy/iis/transform-webconfig>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a76f2-220">If you need to transform `web.config` on publish, see <xref:host-and-deploy/iis/transform-webconfig>.</span></span> <span data-ttu-id="a76f2-221">発行時に "`web.config`" を変換して、構成、プロファイル、環境に基づいた環境変数を設定しなければならない場合があります。</span><span class="sxs-lookup"><span data-stu-id="a76f2-221">You might need to transform `web.config` on publish to set environment variables based on the configuration, profile, or environment.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a76f2-222">その他の資料</span><span class="sxs-lookup"><span data-stu-id="a76f2-222">Additional resources</span></span>

* [<span data-ttu-id="a76f2-223">IIS \<system.webServer></span><span class="sxs-lookup"><span data-stu-id="a76f2-223">IIS \<system.webServer></span></span>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/iis/modules>
* <xref:host-and-deploy/iis/transform-webconfig>
