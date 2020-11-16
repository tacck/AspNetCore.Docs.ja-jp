---
title: HttpRepl を使用して Web API をテストする
author: scottaddie
description: HttpRepl .NET Core グローバル ツールを使用して、ASP.NET Core Web API を参照およびテストする方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc, devx-track-azurecli
ms.date: 11/11/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: web-api/http-repl
ms.openlocfilehash: 8dd763d270a00c9a71913d68d6a039b2d98c3864
ms.sourcegitcommit: 1ea3f23bec63e96ffc3a927992f30a5fc0de3ff9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2020
ms.locfileid: "94570212"
---
# <a name="test-web-apis-with-the-httprepl"></a><span data-ttu-id="1d77a-103">HttpRepl を使用して Web API をテストする</span><span class="sxs-lookup"><span data-stu-id="1d77a-103">Test web APIs with the HttpRepl</span></span>

<span data-ttu-id="1d77a-104">作成者: [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="1d77a-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="1d77a-105">HTTP Read-Eval-Print Loop (REPL) は:</span><span class="sxs-lookup"><span data-stu-id="1d77a-105">The HTTP Read-Eval-Print Loop (REPL) is:</span></span>

* <span data-ttu-id="1d77a-106">.NET Core がサポートされているすべての場所でサポートされている、軽量なクロスプラットフォーム コマンドライン ツールです。</span><span class="sxs-lookup"><span data-stu-id="1d77a-106">A lightweight, cross-platform command-line tool that's supported everywhere .NET Core is supported.</span></span>
* <span data-ttu-id="1d77a-107">ASP.NET Core Web API (および ASP.NET 以外の Core Web API) をテストし、その結果を表示する HTTP 要求を作成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="1d77a-107">Used for making HTTP requests to test ASP.NET Core web APIs (and non-ASP.NET Core web APIs) and view their results.</span></span>
* <span data-ttu-id="1d77a-108">localhost や Azure App Service などの任意の環境でホストされている Web API をテストすることができます。</span><span class="sxs-lookup"><span data-stu-id="1d77a-108">Capable of testing web APIs hosted in any environment, including localhost and Azure App Service.</span></span>

<span data-ttu-id="1d77a-109">次の [HTTP 動詞](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods)がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="1d77a-109">The following [HTTP verbs](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods) are supported:</span></span>

* [<span data-ttu-id="1d77a-110">DELETE</span><span class="sxs-lookup"><span data-stu-id="1d77a-110">DELETE</span></span>](#test-http-delete-requests)
* [<span data-ttu-id="1d77a-111">GET</span><span class="sxs-lookup"><span data-stu-id="1d77a-111">GET</span></span>](#test-http-get-requests)
* [<span data-ttu-id="1d77a-112">HEAD</span><span class="sxs-lookup"><span data-stu-id="1d77a-112">HEAD</span></span>](#test-http-head-requests)
* [<span data-ttu-id="1d77a-113">OPTIONS</span><span class="sxs-lookup"><span data-stu-id="1d77a-113">OPTIONS</span></span>](#test-http-options-requests)
* [<span data-ttu-id="1d77a-114">PATCH</span><span class="sxs-lookup"><span data-stu-id="1d77a-114">PATCH</span></span>](#test-http-patch-requests)
* [<span data-ttu-id="1d77a-115">POST</span><span class="sxs-lookup"><span data-stu-id="1d77a-115">POST</span></span>](#test-http-post-requests)
* [<span data-ttu-id="1d77a-116">PUT</span><span class="sxs-lookup"><span data-stu-id="1d77a-116">PUT</span></span>](#test-http-put-requests)

<span data-ttu-id="1d77a-117">先に進むには、[サンプル ASP.NET Core Web API を表示またはダウンロードします](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/http-repl/samples) ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="1d77a-117">To follow along, [view or download the sample ASP.NET Core web API](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/http-repl/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="1d77a-118">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="1d77a-118">Prerequisites</span></span>

* [!INCLUDE [2.1-SDK](~/includes/2.1-SDK.md)]

## <a name="installation"></a><span data-ttu-id="1d77a-119">インストール</span><span class="sxs-lookup"><span data-stu-id="1d77a-119">Installation</span></span>

<span data-ttu-id="1d77a-120">HttpRepl をインストールするには、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="1d77a-120">To install the HttpRepl, run the following command:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.dotnet-httprepl
```

<span data-ttu-id="1d77a-121">[.Net Core グローバル ツール](/dotnet/core/tools/global-tools#install-a-global-tool)は、[Microsoft.dotnet-httprepl](https://www.nuget.org/packages/Microsoft.dotnet-httprepl) NuGet パッケージからインストールされます。</span><span class="sxs-lookup"><span data-stu-id="1d77a-121">A [.NET Core Global Tool](/dotnet/core/tools/global-tools#install-a-global-tool) is installed from the [Microsoft.dotnet-httprepl](https://www.nuget.org/packages/Microsoft.dotnet-httprepl) NuGet package.</span></span>

## <a name="usage"></a><span data-ttu-id="1d77a-122">使用</span><span class="sxs-lookup"><span data-stu-id="1d77a-122">Usage</span></span>

<span data-ttu-id="1d77a-123">ツールのインストールが正常に完了したら、次のコマンドを実行して HttpRepl を開始します。</span><span class="sxs-lookup"><span data-stu-id="1d77a-123">After successful installation of the tool, run the following command to start the HttpRepl:</span></span>

```console
httprepl
```

<span data-ttu-id="1d77a-124">使用可能な HttpRepl コマンドを表示するには、次のコマンドのいずれかを実行します。</span><span class="sxs-lookup"><span data-stu-id="1d77a-124">To view the available HttpRepl commands, run one of the following commands:</span></span>

```console
httprepl -h
```

```console
httprepl --help
```

<span data-ttu-id="1d77a-125">次の出力が表示されます。</span><span class="sxs-lookup"><span data-stu-id="1d77a-125">The following output is displayed:</span></span>

```console
Usage:
  httprepl [<BASE_ADDRESS>] [options]

Arguments:
  <BASE_ADDRESS> - The initial base address for the REPL.

Options:
  -h|--help - Show help information.

Once the REPL starts, these commands are valid:

Setup Commands:
Use these commands to configure the tool for your API server

connect        Configures the directory structure and base address of the api server
set header     Sets or clears a header for all requests. e.g. `set header content-type application/json`

HTTP Commands:
Use these commands to execute requests against your application.

GET            get - Issues a GET request
POST           post - Issues a POST request
PUT            put - Issues a PUT request
DELETE         delete - Issues a DELETE request
PATCH          patch - Issues a PATCH request
HEAD           head - Issues a HEAD request
OPTIONS        options - Issues a OPTIONS request

Navigation Commands:
The REPL allows you to navigate your URL space and focus on specific APIs that you are working on.

ls             Show all endpoints for the current path
cd             Append the given directory to the currently selected path, or move up a path when using `cd ..`

Shell Commands:
Use these commands to interact with the REPL shell.

clear          Removes all text from the shell
echo [on/off]  Turns request echoing on or off, show the request that was made when using request commands
exit           Exit the shell

REPL Customization Commands:
Use these commands to customize the REPL behavior.

pref [get/set] Allows viewing or changing preferences, e.g. 'pref set editor.command.default 'C:\\Program Files\\Microsoft VS Code\\Code.exe'`
run            Runs the script at the given path. A script is a set of commands that can be typed with one command per line
ui             Displays the Swagger UI page, if available, in the default browser

Use `help <COMMAND>` for more detail on an individual command. e.g. `help get`.
For detailed tool info, see https://aka.ms/http-repl-doc.
```

<span data-ttu-id="1d77a-126">HttpRepl では、コマンド補完が提供されています。</span><span class="sxs-lookup"><span data-stu-id="1d77a-126">The HttpRepl offers command completion.</span></span> <span data-ttu-id="1d77a-127"><kbd>Tab</kbd> キーを押すと、入力した文字または API エンドポイントを補完するコマンドの一覧が反復処理されます。</span><span class="sxs-lookup"><span data-stu-id="1d77a-127">Pressing the <kbd>Tab</kbd> key iterates through the list of commands that complete the characters or API endpoint that you typed.</span></span> <span data-ttu-id="1d77a-128">次のセクションでは、使用可能な CLI コマンドの概要を示します。</span><span class="sxs-lookup"><span data-stu-id="1d77a-128">The following sections outline the available CLI commands.</span></span>

## <a name="connect-to-the-web-api"></a><span data-ttu-id="1d77a-129">Web API に接続する</span><span class="sxs-lookup"><span data-stu-id="1d77a-129">Connect to the web API</span></span>

<span data-ttu-id="1d77a-130">次のコマンドを実行して、Web API に接続します。</span><span class="sxs-lookup"><span data-stu-id="1d77a-130">Connect to a web API by running the following command:</span></span>

```console
httprepl <ROOT URI>
```

<span data-ttu-id="1d77a-131">`<ROOT URI>` は、Web API のベース URI です。</span><span class="sxs-lookup"><span data-stu-id="1d77a-131">`<ROOT URI>` is the base URI for the web API.</span></span> <span data-ttu-id="1d77a-132">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="1d77a-132">For example:</span></span>

```console
httprepl https://localhost:5001
```

<span data-ttu-id="1d77a-133">または、HttpRepl の実行中に、次のコマンドをいつでも実行します。</span><span class="sxs-lookup"><span data-stu-id="1d77a-133">Alternatively, run the following command at any time while the HttpRepl is running:</span></span>

```console
connect <ROOT URI>
```

<span data-ttu-id="1d77a-134">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="1d77a-134">For example:</span></span>

```console
(Disconnected)> connect https://localhost:5001
```

### <a name="manually-point-to-the-openapi-description-for-the-web-api"></a><span data-ttu-id="1d77a-135">Web API の OpenAPI の説明を手動でポイントする</span><span class="sxs-lookup"><span data-stu-id="1d77a-135">Manually point to the OpenAPI description for the web API</span></span>

<span data-ttu-id="1d77a-136">上記の connect コマンドでは、自動的に OpenAPI の説明の検索が試みられます。</span><span class="sxs-lookup"><span data-stu-id="1d77a-136">The connect command above will attempt to find the OpenAPI description automatically.</span></span> <span data-ttu-id="1d77a-137">何らかの理由でそれができない場合は、`--openapi` オプションを使用して、Web API の OpenAPI の説明の URI を指定できます。</span><span class="sxs-lookup"><span data-stu-id="1d77a-137">If for some reason it's unable to do so, you can specify the URI of the OpenAPI description for the web API by using the `--openapi` option:</span></span>

```console
connect <ROOT URI> --openapi <OPENAPI DESCRIPTION ADDRESS>
```

<span data-ttu-id="1d77a-138">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="1d77a-138">For example:</span></span>

```console
(Disconnected)> connect https://localhost:5001 --openapi /swagger/v1/swagger.json
```

### <a name="enable-verbose-output-for-details-on-openapi-description-searching-parsing-and-validation"></a><span data-ttu-id="1d77a-139">OpenAPI の説明の検索、解析、検証に関する詳細の詳細出力を有効にする</span><span class="sxs-lookup"><span data-stu-id="1d77a-139">Enable verbose output for details on OpenAPI description searching, parsing, and validation</span></span>

<span data-ttu-id="1d77a-140">`connect` コマンドで `--verbose` オプションを指定すると、ツールで OpenAPI の説明が検索され、解析、検証される場合に、詳細が生成されます。</span><span class="sxs-lookup"><span data-stu-id="1d77a-140">Specifying the `--verbose` option with the `connect` command will produce more details when the tool searches for the OpenAPI description, parses, and validates it.</span></span>

```console
connect <ROOT URI> --verbose
```

<span data-ttu-id="1d77a-141">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="1d77a-141">For example:</span></span>

```console
(Disconnected)> connect https://localhost:5001 --verbose
Checking https://localhost:5001/swagger.json... 404 NotFound
Checking https://localhost:5001/swagger/v1/swagger.json... 404 NotFound
Checking https://localhost:5001/openapi.json... Found
Parsing... Successful (with warnings)
The field 'info' in 'document' object is REQUIRED [#/info]
The field 'paths' in 'document' object is REQUIRED [#/paths]
```

## <a name="navigate-the-web-api"></a><span data-ttu-id="1d77a-142">Web API 内を移動する</span><span class="sxs-lookup"><span data-stu-id="1d77a-142">Navigate the web API</span></span>

### <a name="view-available-endpoints"></a><span data-ttu-id="1d77a-143">使用可能なエンドポイントを表示する</span><span class="sxs-lookup"><span data-stu-id="1d77a-143">View available endpoints</span></span>

<span data-ttu-id="1d77a-144">Web API アドレスの現在のパスにあるさまざまなエンドポイント (コントローラー) を一覧表示するには、`ls` コマンドまたは `dir` コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="1d77a-144">To list the different endpoints (controllers) at the current path of the web API address, run the `ls` or `dir` command:</span></span>

```console
https://localhost:5001/> ls
```

<span data-ttu-id="1d77a-145">次の出力形式が表示されます。</span><span class="sxs-lookup"><span data-stu-id="1d77a-145">The following output format is displayed:</span></span>

```console
.        []
Fruits   [get|post]
People   [get|post]

https://localhost:5001/>
```

<span data-ttu-id="1d77a-146">前の出力では、`Fruits` と `People` の 2 つのコントローラーが使用可能であることが示されています。</span><span class="sxs-lookup"><span data-stu-id="1d77a-146">The preceding output indicates that there are two controllers available: `Fruits` and `People`.</span></span> <span data-ttu-id="1d77a-147">どちらのコントローラーでも、パラメーターなしの HTTP GET 操作と POST 操作がサポートされます。</span><span class="sxs-lookup"><span data-stu-id="1d77a-147">Both controllers support parameterless HTTP GET and POST operations.</span></span>

<span data-ttu-id="1d77a-148">特定のコントローラーに移動すると、詳細がわかります。</span><span class="sxs-lookup"><span data-stu-id="1d77a-148">Navigating into a specific controller reveals more detail.</span></span> <span data-ttu-id="1d77a-149">たとえば、次のコマンドの出力は、`Fruits` コントローラーが HTTP GET、PUT、DELETE の各操作をサポートしていることを示しています。</span><span class="sxs-lookup"><span data-stu-id="1d77a-149">For example, the following command's output shows the `Fruits` controller also supports HTTP GET, PUT, and DELETE operations.</span></span> <span data-ttu-id="1d77a-150">これらの各操作には、ルートで `id` パラメーターが必要です。</span><span class="sxs-lookup"><span data-stu-id="1d77a-150">Each of these operations expects an `id` parameter in the route:</span></span>

```console
https://localhost:5001/fruits> ls
.      [get|post]
..     []
{id}   [get|put|delete]

https://localhost:5001/fruits>
```

<span data-ttu-id="1d77a-151">または、`ui` コマンドを実行して、ブラウザーで Web API の Swagger UI ページを開きます。</span><span class="sxs-lookup"><span data-stu-id="1d77a-151">Alternatively, run the `ui` command to open the web API's Swagger UI page in a browser.</span></span> <span data-ttu-id="1d77a-152">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="1d77a-152">For example:</span></span>

```console
https://localhost:5001/> ui
```

### <a name="navigate-to-an-endpoint"></a><span data-ttu-id="1d77a-153">エンドポイントに移動する</span><span class="sxs-lookup"><span data-stu-id="1d77a-153">Navigate to an endpoint</span></span>

<span data-ttu-id="1d77a-154">Web API の別のエンドポイントに移動するには、`cd` コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="1d77a-154">To navigate to a different endpoint on the web API, run the `cd` command:</span></span>

```console
https://localhost:5001/> cd people
```

<span data-ttu-id="1d77a-155">`cd` コマンドの後のパスでは大文字と小文字が区別されません。</span><span class="sxs-lookup"><span data-stu-id="1d77a-155">The path following the `cd` command is case insensitive.</span></span> <span data-ttu-id="1d77a-156">次の出力形式が表示されます。</span><span class="sxs-lookup"><span data-stu-id="1d77a-156">The following output format is displayed:</span></span>

```console
/people    [get|post]

https://localhost:5001/people>
```

## <a name="customize-the-httprepl"></a><span data-ttu-id="1d77a-157">HttpRepl をカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="1d77a-157">Customize the HttpRepl</span></span>

<span data-ttu-id="1d77a-158">HttpRepl の既定の[色](#set-color-preferences)はカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="1d77a-158">The HttpRepl's default [colors](#set-color-preferences) can be customized.</span></span> <span data-ttu-id="1d77a-159">また、[既定のテキストエディター](#set-the-default-text-editor)を定義することもできます。</span><span class="sxs-lookup"><span data-stu-id="1d77a-159">Additionally, a [default text editor](#set-the-default-text-editor) can be defined.</span></span> <span data-ttu-id="1d77a-160">HttpRepl の設定は、現在のセッションで永続化され、今後のセッションで受け入れられます。</span><span class="sxs-lookup"><span data-stu-id="1d77a-160">The HttpRepl preferences are persisted across the current session and are honored in future sessions.</span></span> <span data-ttu-id="1d77a-161">変更した設定は、次のファイルに格納されます。</span><span class="sxs-lookup"><span data-stu-id="1d77a-161">Once modified, the preferences are stored in the following file:</span></span>

# <a name="linux"></a>[<span data-ttu-id="1d77a-162">Linux</span><span class="sxs-lookup"><span data-stu-id="1d77a-162">Linux</span></span>](#tab/linux)

<span data-ttu-id="1d77a-163">*%HOME%/.httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="1d77a-163">*%HOME%/.httpreplprefs*</span></span>

# <a name="macos"></a>[<span data-ttu-id="1d77a-164">macOS</span><span class="sxs-lookup"><span data-stu-id="1d77a-164">macOS</span></span>](#tab/macos)

<span data-ttu-id="1d77a-165">*%HOME%/.httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="1d77a-165">*%HOME%/.httpreplprefs*</span></span>

# <a name="windows"></a>[<span data-ttu-id="1d77a-166">Windows</span><span class="sxs-lookup"><span data-stu-id="1d77a-166">Windows</span></span>](#tab/windows)

<span data-ttu-id="1d77a-167">*%USERPROFILE%\\.httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="1d77a-167">*%USERPROFILE%\\.httpreplprefs*</span></span>

---

<span data-ttu-id="1d77a-168">*.httpreplprefs* ファイルは起動時に読み込まれ、実行時の変更は監視されません。</span><span class="sxs-lookup"><span data-stu-id="1d77a-168">The *.httpreplprefs* file is loaded on startup and not monitored for changes at runtime.</span></span> <span data-ttu-id="1d77a-169">ファイルに対する手動の変更は、ツールを再起動した後でのみ有効になります。</span><span class="sxs-lookup"><span data-stu-id="1d77a-169">Manual modifications to the file take effect only after restarting the tool.</span></span>

### <a name="view-the-settings"></a><span data-ttu-id="1d77a-170">設定を表示する</span><span class="sxs-lookup"><span data-stu-id="1d77a-170">View the settings</span></span>

<span data-ttu-id="1d77a-171">使用可能な設定を表示するには、`pref get` コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="1d77a-171">To view the available settings, run the `pref get` command.</span></span> <span data-ttu-id="1d77a-172">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="1d77a-172">For example:</span></span>

```console
https://localhost:5001/> pref get
```

<span data-ttu-id="1d77a-173">上記のコマンドでは、使用可能なキーと値のペアが表示されます。</span><span class="sxs-lookup"><span data-stu-id="1d77a-173">The preceding command displays the available key-value pairs:</span></span>

```console
colors.json=Green
colors.json.arrayBrace=BoldCyan
colors.json.comma=BoldYellow
colors.json.name=BoldMagenta
colors.json.nameSeparator=BoldWhite
colors.json.objectBrace=Cyan
colors.protocol=BoldGreen
colors.status=BoldYellow
```

### <a name="set-color-preferences"></a><span data-ttu-id="1d77a-174">色の設定を設定する</span><span class="sxs-lookup"><span data-stu-id="1d77a-174">Set color preferences</span></span>

<span data-ttu-id="1d77a-175">応答の色付けは、現在、JSON でのみサポートされています。</span><span class="sxs-lookup"><span data-stu-id="1d77a-175">Response colorization is currently supported for JSON only.</span></span> <span data-ttu-id="1d77a-176">既定の HttpRepl ツールの色分けをカスタマイズするには、変更する色に対応するキーを見つけます。</span><span class="sxs-lookup"><span data-stu-id="1d77a-176">To customize the default HttpRepl tool coloring, locate the key corresponding to the color to be changed.</span></span> <span data-ttu-id="1d77a-177">キーを検索する方法については、「[設定を表示する](#view-the-settings)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="1d77a-177">For instructions on how to find the keys, see the [View the settings](#view-the-settings) section.</span></span> <span data-ttu-id="1d77a-178">たとえば、次のように、`colors.json` キー値を `Green` から `White` に変更します。</span><span class="sxs-lookup"><span data-stu-id="1d77a-178">For example, change the `colors.json` key value from `Green` to `White` as follows:</span></span>

```console
https://localhost:5001/people> pref set colors.json White
```

<span data-ttu-id="1d77a-179">使用できるのは、[許可されている色](https://github.com/dotnet/HttpRepl/blob/01d5c3c3373e98fe566ff5ef8a17c571de880293/src/Microsoft.Repl/ConsoleHandling/AllowedColors.cs)だけです。</span><span class="sxs-lookup"><span data-stu-id="1d77a-179">Only the [allowed colors](https://github.com/dotnet/HttpRepl/blob/01d5c3c3373e98fe566ff5ef8a17c571de880293/src/Microsoft.Repl/ConsoleHandling/AllowedColors.cs) may be used.</span></span> <span data-ttu-id="1d77a-180">後続の HTTP 要求では、出力が新しい色で表示されます。</span><span class="sxs-lookup"><span data-stu-id="1d77a-180">Subsequent HTTP requests display output with the new coloring.</span></span>

<span data-ttu-id="1d77a-181">特定の色キーが設定されていない場合、より汎用的なキーが考慮されます。</span><span class="sxs-lookup"><span data-stu-id="1d77a-181">When specific color keys aren't set, more generic keys are considered.</span></span> <span data-ttu-id="1d77a-182">このフォールバック動作を示すために、次の例を考えてみます。</span><span class="sxs-lookup"><span data-stu-id="1d77a-182">To demonstrate this fallback behavior, consider the following example:</span></span>

* <span data-ttu-id="1d77a-183">`colors.json.name` に値がない場合は、`colors.json.string` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="1d77a-183">If `colors.json.name` doesn't have a value, `colors.json.string` is used.</span></span>
* <span data-ttu-id="1d77a-184">`colors.json.string` に値がない場合は、`colors.json.literal` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="1d77a-184">If `colors.json.string` doesn't have a value, `colors.json.literal` is used.</span></span>
* <span data-ttu-id="1d77a-185">`colors.json.literal` に値がない場合は、`colors.json` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="1d77a-185">If `colors.json.literal` doesn't have a value, `colors.json` is used.</span></span> 
* <span data-ttu-id="1d77a-186">`colors.json` 値がない場合は、コマンド シェルの既定のテキストの色 (`AllowedColors.None`) が使用されます。</span><span class="sxs-lookup"><span data-stu-id="1d77a-186">If `colors.json` doesn't have a value, the command shell's default text color (`AllowedColors.None`) is used.</span></span>

### <a name="set-indentation-size"></a><span data-ttu-id="1d77a-187">インデントのサイズを設定する</span><span class="sxs-lookup"><span data-stu-id="1d77a-187">Set indentation size</span></span>

<span data-ttu-id="1d77a-188">応答インデント サイズのカスタマイズは、現在、JSON でのみサポートされています。</span><span class="sxs-lookup"><span data-stu-id="1d77a-188">Response indentation size customization is currently supported for JSON only.</span></span> <span data-ttu-id="1d77a-189">既定のサイズは 2 つのスペースです。</span><span class="sxs-lookup"><span data-stu-id="1d77a-189">The default size is two spaces.</span></span> <span data-ttu-id="1d77a-190">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="1d77a-190">For example:</span></span>

```json
[
  {
    "id": 1,
    "name": "Apple"
  },
  {
    "id": 2,
    "name": "Orange"
  },
  {
    "id": 3,
    "name": "Strawberry"
  }
]
```

<span data-ttu-id="1d77a-191">既定のサイズを変更するには、`formatting.json.indentSize` キーを設定します。</span><span class="sxs-lookup"><span data-stu-id="1d77a-191">To change the default size, set the `formatting.json.indentSize` key.</span></span> <span data-ttu-id="1d77a-192">たとえば、常に 4 つのスペースを使用するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="1d77a-192">For example, to always use four spaces:</span></span>

```console
pref set formatting.json.indentSize 4
```

<span data-ttu-id="1d77a-193">後続の応答では、4 つのスペースの設定が優先されます。</span><span class="sxs-lookup"><span data-stu-id="1d77a-193">Subsequent responses honor the setting of four spaces:</span></span>

```json
[
    {
        "id": 1,
        "name": "Apple"
    },
    {
        "id": 2,
        "name": "Orange"
    },
    {
        "id": 3,
        "name": "Strawberry"
    }
]
```

### <a name="set-the-default-text-editor"></a><span data-ttu-id="1d77a-194">既定のテキスト エディターを設定する</span><span class="sxs-lookup"><span data-stu-id="1d77a-194">Set the default text editor</span></span>

<span data-ttu-id="1d77a-195">既定では、HttpRepl には、使用するように構成されたテキスト エディターはありません。</span><span class="sxs-lookup"><span data-stu-id="1d77a-195">By default, the HttpRepl has no text editor configured for use.</span></span> <span data-ttu-id="1d77a-196">HTTP 要求本文を必要とする Web API メソッドをテストするには、既定のテキスト エディターを設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="1d77a-196">To test web API methods requiring an HTTP request body, a default text editor must be set.</span></span> <span data-ttu-id="1d77a-197">HttpRepl ツールによって、要求本文を作成する目的のためだけに構成されたテキスト エディターが起動します。</span><span class="sxs-lookup"><span data-stu-id="1d77a-197">The HttpRepl tool launches the configured text editor for the sole purpose of composing the request body.</span></span> <span data-ttu-id="1d77a-198">次のコマンドを実行して、優先テキストエディターを既定値として設定します。</span><span class="sxs-lookup"><span data-stu-id="1d77a-198">Run the following command to set your preferred text editor as the default:</span></span>

```console
pref set editor.command.default "<EXECUTABLE>"
```

<span data-ttu-id="1d77a-199">上記のコマンドで、`<EXECUTABLE>` はテキスト エディターの実行可能ファイルへの完全なパスです。</span><span class="sxs-lookup"><span data-stu-id="1d77a-199">In the preceding command, `<EXECUTABLE>` is the full path to the text editor's executable file.</span></span> <span data-ttu-id="1d77a-200">たとえば、次のコマンドを実行して、既定のテキストエディターとして Visual Studio Code を設定します。</span><span class="sxs-lookup"><span data-stu-id="1d77a-200">For example, run the following command to set Visual Studio Code as the default text editor:</span></span>

# <a name="linux"></a>[<span data-ttu-id="1d77a-201">Linux</span><span class="sxs-lookup"><span data-stu-id="1d77a-201">Linux</span></span>](#tab/linux)

```console
pref set editor.command.default "/usr/bin/code"
```

# <a name="macos"></a>[<span data-ttu-id="1d77a-202">macOS</span><span class="sxs-lookup"><span data-stu-id="1d77a-202">macOS</span></span>](#tab/macos)

```console
pref set editor.command.default "/Applications/Visual Studio Code.app/Contents/Resources/app/bin/code"
```

# <a name="windows"></a>[<span data-ttu-id="1d77a-203">Windows</span><span class="sxs-lookup"><span data-stu-id="1d77a-203">Windows</span></span>](#tab/windows)

```console
pref set editor.command.default "C:\Program Files\Microsoft VS Code\Code.exe"
```

---

<span data-ttu-id="1d77a-204">特定の CLI 引数を使用して既定のテキスト エディターを起動するには、`editor.command.default.arguments` キーを設定します。</span><span class="sxs-lookup"><span data-stu-id="1d77a-204">To launch the default text editor with specific CLI arguments, set the `editor.command.default.arguments` key.</span></span> <span data-ttu-id="1d77a-205">たとえば、Visual Studio Code が既定のテキスト エディターで、拡張機能が無効になっている新しいセッションでは HttpRepl で常に Visual Studio Code を開きたいとします。</span><span class="sxs-lookup"><span data-stu-id="1d77a-205">For example, assume Visual Studio Code is the default text editor and that you always want the HttpRepl to open Visual Studio Code in a new session with extensions disabled.</span></span> <span data-ttu-id="1d77a-206">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="1d77a-206">Run the following command:</span></span>

```console
pref set editor.command.default.arguments "--disable-extensions --new-window"
```

> [!TIP]
> <span data-ttu-id="1d77a-207">既定のエディターが Visual Studio Code 場合、通常、`-w` または `--wait` 引数を渡して、Visual Studio Code に、ファイルが閉じられてから戻るように待機させます。</span><span class="sxs-lookup"><span data-stu-id="1d77a-207">If your default editor is Visual Studio Code, you'll usually want to pass the `-w` or `--wait` argument to force Visual Studio Code to wait for you to close the file before returning.</span></span>

### <a name="set-the-openapi-description-search-paths"></a><span data-ttu-id="1d77a-208">OpenAPI の説明の検索パスを設定する</span><span class="sxs-lookup"><span data-stu-id="1d77a-208">Set the OpenAPI Description search paths</span></span>

<span data-ttu-id="1d77a-209">HttpRepl には既定の相対パスのセットがあり、`connect` コマンドが `--openapi` オプションを指定せずに実行された場合に、それを使用して OpenAPI の説明が検索されます。</span><span class="sxs-lookup"><span data-stu-id="1d77a-209">By default, the HttpRepl has a set of relative paths that it uses to find the OpenAPI description when executing the `connect` command without the `--openapi` option.</span></span> <span data-ttu-id="1d77a-210">これらの相対パスは、`connect` コマンドで指定されているルート パスおよびベース パスと組み合わされます。</span><span class="sxs-lookup"><span data-stu-id="1d77a-210">These relative paths are combined with the root and base paths specified in the `connect` command.</span></span> <span data-ttu-id="1d77a-211">既定の相対パスは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="1d77a-211">The default relative paths are:</span></span>

- <span data-ttu-id="1d77a-212">*swagger.json*</span><span class="sxs-lookup"><span data-stu-id="1d77a-212">*swagger.json*</span></span>
- <span data-ttu-id="1d77a-213">*swagger/v1/swagger.json*</span><span class="sxs-lookup"><span data-stu-id="1d77a-213">*swagger/v1/swagger.json*</span></span>
- <span data-ttu-id="1d77a-214">*/swagger.json*</span><span class="sxs-lookup"><span data-stu-id="1d77a-214">*/swagger.json*</span></span>
- <span data-ttu-id="1d77a-215">*/swagger/v1/swagger.json*</span><span class="sxs-lookup"><span data-stu-id="1d77a-215">*/swagger/v1/swagger.json*</span></span>
- <span data-ttu-id="1d77a-216">*openapi.json*</span><span class="sxs-lookup"><span data-stu-id="1d77a-216">*openapi.json*</span></span>
- <span data-ttu-id="1d77a-217">*/openapi.json*</span><span class="sxs-lookup"><span data-stu-id="1d77a-217">*/openapi.json*</span></span>

<span data-ttu-id="1d77a-218">環境で別の検索パスのセットを使用するには、ユーザー設定 `swagger.searchPaths` を設定します。</span><span class="sxs-lookup"><span data-stu-id="1d77a-218">To use a different set of search paths in your environment, set the `swagger.searchPaths` preference.</span></span> <span data-ttu-id="1d77a-219">値としては、パイプで区切られた相対パスのリストを指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="1d77a-219">The value must be a pipe-delimited list of relative paths.</span></span> <span data-ttu-id="1d77a-220">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="1d77a-220">For example:</span></span>

```console
pref set swagger.searchPaths "swagger/v2/swagger.json|swagger/v3/swagger.json"
```

<span data-ttu-id="1d77a-221">既定のリストを完全に置き換えるのではなく、パスを追加または削除してリストを変更することもできます。</span><span class="sxs-lookup"><span data-stu-id="1d77a-221">Instead of replacing the default list altogether, the list can also be modified by adding or removing paths.</span></span>

<span data-ttu-id="1d77a-222">既定のリストに 1 つ以上の検索パスを追加するには、`swagger.addToSearchPaths` 設定を指定します。</span><span class="sxs-lookup"><span data-stu-id="1d77a-222">To add one or more search paths to the default list, set the `swagger.addToSearchPaths` preference.</span></span> <span data-ttu-id="1d77a-223">値としては、パイプで区切られた相対パスのリストを指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="1d77a-223">The value must be a pipe-delimited list of relative paths.</span></span> <span data-ttu-id="1d77a-224">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="1d77a-224">For example:</span></span>

```console
pref set swagger.addToSearchPaths "openapi/v2/openapi.json|openapi/v3/openapi.json"
```

<span data-ttu-id="1d77a-225">既定のリストから 1 つ以上の検索パスを削除するには、`swagger.addToSearchPaths` 設定を指定します。</span><span class="sxs-lookup"><span data-stu-id="1d77a-225">To remove one or more search paths from the default list, set the `swagger.addToSearchPaths` preference.</span></span> <span data-ttu-id="1d77a-226">値としては、パイプで区切られた相対パスのリストを指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="1d77a-226">The value must be a pipe-delimited list of relative paths.</span></span> <span data-ttu-id="1d77a-227">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="1d77a-227">For example:</span></span>

```console
pref set swagger.removeFromSearchPaths "swagger.json|/swagger.json"
```

## <a name="test-http-get-requests"></a><span data-ttu-id="1d77a-228">HTTP GET 要求をテストする</span><span class="sxs-lookup"><span data-stu-id="1d77a-228">Test HTTP GET requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="1d77a-229">構文</span><span class="sxs-lookup"><span data-stu-id="1d77a-229">Synopsis</span></span>

```console
get <PARAMETER> [-F|--no-formatting] [-h|--header] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="1d77a-230">引数</span><span class="sxs-lookup"><span data-stu-id="1d77a-230">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="1d77a-231">関連付けられたコントローラー アクション メソッドで求められるルート パラメーター (存在する場合)。</span><span class="sxs-lookup"><span data-stu-id="1d77a-231">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="1d77a-232">オプション</span><span class="sxs-lookup"><span data-stu-id="1d77a-232">Options</span></span>

<span data-ttu-id="1d77a-233">`get` コマンドには以下のオプションを使用できます。</span><span class="sxs-lookup"><span data-stu-id="1d77a-233">The following options are available for the `get` command:</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a><span data-ttu-id="1d77a-234">例</span><span class="sxs-lookup"><span data-stu-id="1d77a-234">Example</span></span>

<span data-ttu-id="1d77a-235">HTTP GET 要求を発行するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="1d77a-235">To issue an HTTP GET request:</span></span>

1. <span data-ttu-id="1d77a-236">それをサポートしているエンドポイントで `get` コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="1d77a-236">Run the `get` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/people> get
    ```

    <span data-ttu-id="1d77a-237">上記のコマンドでは、次の出力形式が表示されます。</span><span class="sxs-lookup"><span data-stu-id="1d77a-237">The preceding command displays the following output format:</span></span>

    ```console
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Fri, 21 Jun 2019 03:38:45 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "name": "Scott Hunter"
      },
      {
        "id": 2,
        "name": "Scott Hanselman"
      },
      {
        "id": 3,
        "name": "Scott Guthrie"
      }
    ]


    https://localhost:5001/people>
    ```

1. <span data-ttu-id="1d77a-238">`get` コマンドにパラメーターを渡すことによって、特定のレコードを取得します。</span><span class="sxs-lookup"><span data-stu-id="1d77a-238">Retrieve a specific record by passing a parameter to the `get` command:</span></span>

    ```console
    https://localhost:5001/people> get 2
    ```

    <span data-ttu-id="1d77a-239">上記のコマンドでは、次の出力形式が表示されます。</span><span class="sxs-lookup"><span data-stu-id="1d77a-239">The preceding command displays the following output format:</span></span>

    ```console
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Fri, 21 Jun 2019 06:17:57 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 2,
        "name": "Scott Hanselman"
      }
    ]


    https://localhost:5001/people>
    ```

## <a name="test-http-post-requests"></a><span data-ttu-id="1d77a-240">HTTP POST 要求をテストする</span><span class="sxs-lookup"><span data-stu-id="1d77a-240">Test HTTP POST requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="1d77a-241">構文</span><span class="sxs-lookup"><span data-stu-id="1d77a-241">Synopsis</span></span>

```console
post <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="1d77a-242">引数</span><span class="sxs-lookup"><span data-stu-id="1d77a-242">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="1d77a-243">関連付けられたコントローラー アクション メソッドで求められるルート パラメーター (存在する場合)。</span><span class="sxs-lookup"><span data-stu-id="1d77a-243">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="1d77a-244">オプション</span><span class="sxs-lookup"><span data-stu-id="1d77a-244">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a><span data-ttu-id="1d77a-245">例</span><span class="sxs-lookup"><span data-stu-id="1d77a-245">Example</span></span>

<span data-ttu-id="1d77a-246">HTTP POST 要求を発行するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="1d77a-246">To issue an HTTP POST request:</span></span>

1. <span data-ttu-id="1d77a-247">それをサポートしているエンドポイントで `post` コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="1d77a-247">Run the `post` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/people> post -h Content-Type=application/json
    ```

    <span data-ttu-id="1d77a-248">前のコマンドでは、`Content-Type` HTTP 要求ヘッダーが JSON の要求本文メディアの種類を示すように設定されています。</span><span class="sxs-lookup"><span data-stu-id="1d77a-248">In the preceding command, the `Content-Type` HTTP request header is set to indicate a request body media type of JSON.</span></span> <span data-ttu-id="1d77a-249">既定のテキスト エディターでは、HTTP 要求本文を表す JSON テンプレートを含む *.tmp* ファイルが開かれます。</span><span class="sxs-lookup"><span data-stu-id="1d77a-249">The default text editor opens a *.tmp* file with a JSON template representing the HTTP request body.</span></span> <span data-ttu-id="1d77a-250">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="1d77a-250">For example:</span></span>

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > <span data-ttu-id="1d77a-251">既定のテキスト エディターを設定するには、「[既定のテキスト エディターを設定する](#set-the-default-text-editor)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="1d77a-251">To set the default text editor, see the [Set the default text editor](#set-the-default-text-editor) section.</span></span>

1. <span data-ttu-id="1d77a-252">モデルの検証要件を満たすように JSON テンプレートを変更します。</span><span class="sxs-lookup"><span data-stu-id="1d77a-252">Modify the JSON template to satisfy model validation requirements:</span></span>

    ```json
    {
      "id": 0,
      "name": "Scott Addie"
    }
    ```

1. <span data-ttu-id="1d77a-253">*.tmp* ファイルを保存して、テキスト エディターを閉じます。</span><span class="sxs-lookup"><span data-stu-id="1d77a-253">Save the *.tmp* file, and close the text editor.</span></span> <span data-ttu-id="1d77a-254">コマンド シェルに次の出力が表示されます。</span><span class="sxs-lookup"><span data-stu-id="1d77a-254">The following output appears in the command shell:</span></span>

    ```console
    HTTP/1.1 201 Created
    Content-Type: application/json; charset=utf-8
    Date: Thu, 27 Jun 2019 21:24:18 GMT
    Location: https://localhost:5001/people/4
    Server: Kestrel
    Transfer-Encoding: chunked

    {
      "id": 4,
      "name": "Scott Addie"
    }


    https://localhost:5001/people>
    ```

## <a name="test-http-put-requests"></a><span data-ttu-id="1d77a-255">HTTP PUT 要求をテストする</span><span class="sxs-lookup"><span data-stu-id="1d77a-255">Test HTTP PUT requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="1d77a-256">構文</span><span class="sxs-lookup"><span data-stu-id="1d77a-256">Synopsis</span></span>

```console
put <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="1d77a-257">引数</span><span class="sxs-lookup"><span data-stu-id="1d77a-257">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="1d77a-258">関連付けられたコントローラー アクション メソッドで求められるルート パラメーター (存在する場合)。</span><span class="sxs-lookup"><span data-stu-id="1d77a-258">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="1d77a-259">オプション</span><span class="sxs-lookup"><span data-stu-id="1d77a-259">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a><span data-ttu-id="1d77a-260">例</span><span class="sxs-lookup"><span data-stu-id="1d77a-260">Example</span></span>

<span data-ttu-id="1d77a-261">HTTP PUT 要求を発行するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="1d77a-261">To issue an HTTP PUT request:</span></span>

1. <span data-ttu-id="1d77a-262">*省略可能* :変更前にデータを表示するには、`get` コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="1d77a-262">*Optional* : Run the `get` command to view the data before modifying it:</span></span>

    ```console
    https://localhost:5001/fruits> get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:07:32 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 2,
        "data": "Orange"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]
    ```

1. <span data-ttu-id="1d77a-263">それをサポートしているエンドポイントで `put` コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="1d77a-263">Run the `put` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/fruits> put 2 -h Content-Type=application/json
    ```

    <span data-ttu-id="1d77a-264">前のコマンドでは、`Content-Type` HTTP 要求ヘッダーが JSON の要求本文メディアの種類を示すように設定されています。</span><span class="sxs-lookup"><span data-stu-id="1d77a-264">In the preceding command, the `Content-Type` HTTP request header is set to indicate a request body media type of JSON.</span></span> <span data-ttu-id="1d77a-265">既定のテキスト エディターでは、HTTP 要求本文を表す JSON テンプレートを含む *.tmp* ファイルが開かれます。</span><span class="sxs-lookup"><span data-stu-id="1d77a-265">The default text editor opens a *.tmp* file with a JSON template representing the HTTP request body.</span></span> <span data-ttu-id="1d77a-266">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="1d77a-266">For example:</span></span>

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > <span data-ttu-id="1d77a-267">既定のテキスト エディターを設定するには、「[既定のテキスト エディターを設定する](#set-the-default-text-editor)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="1d77a-267">To set the default text editor, see the [Set the default text editor](#set-the-default-text-editor) section.</span></span>

1. <span data-ttu-id="1d77a-268">モデルの検証要件を満たすように JSON テンプレートを変更します。</span><span class="sxs-lookup"><span data-stu-id="1d77a-268">Modify the JSON template to satisfy model validation requirements:</span></span>

    ```json
    {
      "id": 2,
      "name": "Cherry"
    }
    ```

1. <span data-ttu-id="1d77a-269">*.tmp* ファイルを保存して、テキスト エディターを閉じます。</span><span class="sxs-lookup"><span data-stu-id="1d77a-269">Save the *.tmp* file, and close the text editor.</span></span> <span data-ttu-id="1d77a-270">コマンド シェルに次の出力が表示されます。</span><span class="sxs-lookup"><span data-stu-id="1d77a-270">The following output appears in the command shell:</span></span>

    ```console
    [main 2019-06-28T17:27:01.805Z] update#setState idle
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:28:21 GMT
    Server: Kestrel
    ```

1. <span data-ttu-id="1d77a-271">*省略可能* :変更を確認するには、`get` コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="1d77a-271">*Optional* : Issue a `get` command to see the modifications.</span></span> <span data-ttu-id="1d77a-272">たとえば、テキスト エディターに「Cherry」と入力すると、`get` によって、次の出力が返されます。</span><span class="sxs-lookup"><span data-stu-id="1d77a-272">For example, if you typed "Cherry" in the text editor, a `get` returns the following output:</span></span>

    ```console
    https://localhost:5001/fruits> get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:08:20 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 2,
        "data": "Cherry"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]


    https://localhost:5001/fruits>
    ```

## <a name="test-http-delete-requests"></a><span data-ttu-id="1d77a-273">HTTP DELETE 要求をテストする</span><span class="sxs-lookup"><span data-stu-id="1d77a-273">Test HTTP DELETE requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="1d77a-274">構文</span><span class="sxs-lookup"><span data-stu-id="1d77a-274">Synopsis</span></span>

```console
delete <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="1d77a-275">引数</span><span class="sxs-lookup"><span data-stu-id="1d77a-275">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="1d77a-276">関連付けられたコントローラー アクション メソッドで求められるルート パラメーター (存在する場合)。</span><span class="sxs-lookup"><span data-stu-id="1d77a-276">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="1d77a-277">オプション</span><span class="sxs-lookup"><span data-stu-id="1d77a-277">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a><span data-ttu-id="1d77a-278">例</span><span class="sxs-lookup"><span data-stu-id="1d77a-278">Example</span></span>

<span data-ttu-id="1d77a-279">HTTP DELETE 要求を発行するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="1d77a-279">To issue an HTTP DELETE request:</span></span>

1. <span data-ttu-id="1d77a-280">*省略可能* :変更前にデータを表示するには、`get` コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="1d77a-280">*Optional* : Run the `get` command to view the data before modifying it:</span></span>

    ```console
    https://localhost:5001/fruits> get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:07:32 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 2,
        "data": "Orange"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]
    ```

1. <span data-ttu-id="1d77a-281">それをサポートしているエンドポイントで `delete` コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="1d77a-281">Run the `delete` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/fruits> delete 2
    ```

    <span data-ttu-id="1d77a-282">上記のコマンドでは、次の出力形式が表示されます。</span><span class="sxs-lookup"><span data-stu-id="1d77a-282">The preceding command displays the following output format:</span></span>

    ```console
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:36:42 GMT
    Server: Kestrel
    ```

1. <span data-ttu-id="1d77a-283">*省略可能* :変更を確認するには、`get` コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="1d77a-283">*Optional* : Issue a `get` command to see the modifications.</span></span> <span data-ttu-id="1d77a-284">この例では、`get` によって次の出力が返されます。</span><span class="sxs-lookup"><span data-stu-id="1d77a-284">In this example, a `get` returns the following output:</span></span>

    ```console
    https://localhost:5001/fruits> get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:16:30 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]


    https://localhost:5001/fruits>
    ```

## <a name="test-http-patch-requests"></a><span data-ttu-id="1d77a-285">HTTP PATCH 要求をテストする</span><span class="sxs-lookup"><span data-stu-id="1d77a-285">Test HTTP PATCH requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="1d77a-286">構文</span><span class="sxs-lookup"><span data-stu-id="1d77a-286">Synopsis</span></span>

```console
patch <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="1d77a-287">引数</span><span class="sxs-lookup"><span data-stu-id="1d77a-287">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="1d77a-288">関連付けられたコントローラー アクション メソッドで求められるルート パラメーター (存在する場合)。</span><span class="sxs-lookup"><span data-stu-id="1d77a-288">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="1d77a-289">オプション</span><span class="sxs-lookup"><span data-stu-id="1d77a-289">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

## <a name="test-http-head-requests"></a><span data-ttu-id="1d77a-290">HTTP HEAD 要求をテストする</span><span class="sxs-lookup"><span data-stu-id="1d77a-290">Test HTTP HEAD requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="1d77a-291">構文</span><span class="sxs-lookup"><span data-stu-id="1d77a-291">Synopsis</span></span>

```console
head <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="1d77a-292">引数</span><span class="sxs-lookup"><span data-stu-id="1d77a-292">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="1d77a-293">関連付けられたコントローラー アクション メソッドで求められるルート パラメーター (存在する場合)。</span><span class="sxs-lookup"><span data-stu-id="1d77a-293">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="1d77a-294">オプション</span><span class="sxs-lookup"><span data-stu-id="1d77a-294">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="test-http-options-requests"></a><span data-ttu-id="1d77a-295">HTTP OPTIONS 要求をテストする</span><span class="sxs-lookup"><span data-stu-id="1d77a-295">Test HTTP OPTIONS requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="1d77a-296">構文</span><span class="sxs-lookup"><span data-stu-id="1d77a-296">Synopsis</span></span>

```console
options <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="1d77a-297">引数</span><span class="sxs-lookup"><span data-stu-id="1d77a-297">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="1d77a-298">関連付けられたコントローラー アクション メソッドで求められるルート パラメーター (存在する場合)。</span><span class="sxs-lookup"><span data-stu-id="1d77a-298">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="1d77a-299">オプション</span><span class="sxs-lookup"><span data-stu-id="1d77a-299">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="set-http-request-headers"></a><span data-ttu-id="1d77a-300">HTTP 要求ヘッダーを設定する</span><span class="sxs-lookup"><span data-stu-id="1d77a-300">Set HTTP request headers</span></span>

<span data-ttu-id="1d77a-301">HTTP 要求ヘッダーを設定するには、次のいずれかの方法を使用します。</span><span class="sxs-lookup"><span data-stu-id="1d77a-301">To set an HTTP request header, use one of the following approaches:</span></span>

* <span data-ttu-id="1d77a-302">HTTP 要求でインラインを設定します。</span><span class="sxs-lookup"><span data-stu-id="1d77a-302">Set inline with the HTTP request.</span></span> <span data-ttu-id="1d77a-303">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="1d77a-303">For example:</span></span>

    ```console
    https://localhost:5001/people> post -h Content-Type=application/json
    ```
    
    <span data-ttu-id="1d77a-304">上記の方法では、個別の HTTP 要求ヘッダーごとに独自の `-h` オプションが必要です。</span><span class="sxs-lookup"><span data-stu-id="1d77a-304">With the preceding approach, each distinct HTTP request header requires its own `-h` option.</span></span>

* <span data-ttu-id="1d77a-305">HTTP 要求を送信する前に設定します。</span><span class="sxs-lookup"><span data-stu-id="1d77a-305">Set before sending the HTTP request.</span></span> <span data-ttu-id="1d77a-306">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="1d77a-306">For example:</span></span>

    ```console
    https://localhost:5001/people> set header Content-Type application/json
    ```
    
    <span data-ttu-id="1d77a-307">要求を送信する前にヘッダーを設定すると、コマンド シェル セッションの間はヘッダーが設定されたままになります。</span><span class="sxs-lookup"><span data-stu-id="1d77a-307">When setting the header before sending a request, the header remains set for the duration of the command shell session.</span></span> <span data-ttu-id="1d77a-308">ヘッダーをクリアするには、空の値を指定します。</span><span class="sxs-lookup"><span data-stu-id="1d77a-308">To clear the header, provide an empty value.</span></span> <span data-ttu-id="1d77a-309">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="1d77a-309">For example:</span></span>
    
    ```console
    https://localhost:5001/people> set header Content-Type
    ```

## <a name="test-secured-endpoints"></a><span data-ttu-id="1d77a-310">セキュリティで保護されたエンドポイントをテストする</span><span class="sxs-lookup"><span data-stu-id="1d77a-310">Test secured endpoints</span></span>

<span data-ttu-id="1d77a-311">HttpRepl は、次の方法で、セキュリティ保護されたエンドポイントのテストをサポートします。</span><span class="sxs-lookup"><span data-stu-id="1d77a-311">The HttpRepl supports the testing of secured endpoints in the following ways:</span></span>

* <span data-ttu-id="1d77a-312">ログインしているユーザーの既定の資格情報を使用する。</span><span class="sxs-lookup"><span data-stu-id="1d77a-312">Via the default credentials of the logged in user.</span></span>
* <span data-ttu-id="1d77a-313">HTTP 要求ヘッダーを使用する。</span><span class="sxs-lookup"><span data-stu-id="1d77a-313">Through the use of HTTP request headers.</span></span>

### <a name="default-credentials"></a><span data-ttu-id="1d77a-314">既定の資格情報</span><span class="sxs-lookup"><span data-stu-id="1d77a-314">Default credentials</span></span>

<span data-ttu-id="1d77a-315">IIS でホストされ、Windows 認証でセキュリティ保護されているテスト対象の Web API を考えてみます。</span><span class="sxs-lookup"><span data-stu-id="1d77a-315">Consider a web API you're testing that's hosted in IIS and secured with Windows authentication.</span></span> <span data-ttu-id="1d77a-316">ツールを実行するユーザーの資格情報が、テスト対象の HTTP エンドポイント全体に送信されるようにしたいと考えます。</span><span class="sxs-lookup"><span data-stu-id="1d77a-316">You want the credentials of the user running the tool to flow across to the HTTP endpoints being tested.</span></span> <span data-ttu-id="1d77a-317">ログインしているユーザーの既定の資格情報を渡すには:</span><span class="sxs-lookup"><span data-stu-id="1d77a-317">To pass the default credentials of the logged in user:</span></span>

1. <span data-ttu-id="1d77a-318">`httpClient.useDefaultCredentials` 設定を `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="1d77a-318">Set the `httpClient.useDefaultCredentials` preference to `true`:</span></span>

    ```console
    pref set httpClient.useDefaultCredentials true
    ```

1. <span data-ttu-id="1d77a-319">Web API に別の要求を送信する前に、ツールを終了して再起動します。</span><span class="sxs-lookup"><span data-stu-id="1d77a-319">Exit and restart the tool before sending another request to the web API.</span></span>
 
### <a name="default-proxy-credentials"></a><span data-ttu-id="1d77a-320">既定のプロキシ資格情報</span><span class="sxs-lookup"><span data-stu-id="1d77a-320">Default proxy credentials</span></span>

<span data-ttu-id="1d77a-321">テスト対象の Web API が Windows 認証でセキュリティ保護されたプロキシの背後にあるシナリオについて考えてみます。</span><span class="sxs-lookup"><span data-stu-id="1d77a-321">Consider a scenario in which the web API you're testing is behind a proxy secured with Windows authentication.</span></span> <span data-ttu-id="1d77a-322">ツールを実行しているユーザーの資格情報をプロキシに送信したいと考えます。</span><span class="sxs-lookup"><span data-stu-id="1d77a-322">You want the credentials of the user running the tool to flow to the proxy.</span></span> <span data-ttu-id="1d77a-323">ログインしているユーザーの既定の資格情報を渡すには:</span><span class="sxs-lookup"><span data-stu-id="1d77a-323">To pass the default credentials of the logged in user:</span></span>

1. <span data-ttu-id="1d77a-324">`httpClient.proxy.useDefaultCredentials` 設定を `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="1d77a-324">Set the `httpClient.proxy.useDefaultCredentials` preference to `true`:</span></span>

    ```console
    pref set httpClient.proxy.useDefaultCredentials true
    ```

1. <span data-ttu-id="1d77a-325">Web API に別の要求を送信する前に、ツールを終了して再起動します。</span><span class="sxs-lookup"><span data-stu-id="1d77a-325">Exit and restart the tool before sending another request to the web API.</span></span>

### <a name="http-request-headers"></a><span data-ttu-id="1d77a-326">HTTP 要求ヘッダー</span><span class="sxs-lookup"><span data-stu-id="1d77a-326">HTTP request headers</span></span>

<span data-ttu-id="1d77a-327">サポートされている認証および認可スキームの例には次のものがあります。</span><span class="sxs-lookup"><span data-stu-id="1d77a-327">Examples of supported authentication and authorization schemes include:</span></span>

* <span data-ttu-id="1d77a-328">basic authentication</span><span class="sxs-lookup"><span data-stu-id="1d77a-328">basic authentication</span></span>
* <span data-ttu-id="1d77a-329">JWT ベアラー トークン</span><span class="sxs-lookup"><span data-stu-id="1d77a-329">JWT bearer tokens</span></span>
* <span data-ttu-id="1d77a-330">ダイジェスト認証</span><span class="sxs-lookup"><span data-stu-id="1d77a-330">digest authentication</span></span>

<span data-ttu-id="1d77a-331">たとえば、次のコマンドを使用して、ベアラー トークンをエンドポイントに送信できます。</span><span class="sxs-lookup"><span data-stu-id="1d77a-331">For example, you can send a bearer token to an endpoint with the following command:</span></span>

```console
set header Authorization "bearer <TOKEN VALUE>"
```

<span data-ttu-id="1d77a-332">Azure でホストされたエンドポイントにアクセスしたり、[Azure REST API](/rest/api/azure/) を使用したりするには、ベアラー トークンが必要です。</span><span class="sxs-lookup"><span data-stu-id="1d77a-332">To access an Azure-hosted endpoint or to use the [Azure REST API](/rest/api/azure/), you need a bearer token.</span></span> <span data-ttu-id="1d77a-333">[Azure CLI](/cli/azure/) で自分の Azure サブスクリプションのベアラー トークンを取得するには、次の手順を使用します。</span><span class="sxs-lookup"><span data-stu-id="1d77a-333">Use the following steps to obtain a bearer token for your Azure subscription via the [Azure CLI](/cli/azure/).</span></span> <span data-ttu-id="1d77a-334">HttpRepl によって、HTTP 要求ヘッダーにベアラー トークンが設定されます。</span><span class="sxs-lookup"><span data-stu-id="1d77a-334">The HttpRepl sets the bearer token in an HTTP request header.</span></span> <span data-ttu-id="1d77a-335">Azure App Service Web Apps の一覧が取得されます。</span><span class="sxs-lookup"><span data-stu-id="1d77a-335">A list of Azure App Service Web Apps is retrieved.</span></span>

1. <span data-ttu-id="1d77a-336">Azure にサインインします。</span><span class="sxs-lookup"><span data-stu-id="1d77a-336">Sign in to Azure:</span></span>

    ```azurecli
    az login
    ```

1. <span data-ttu-id="1d77a-337">次のコマンドで、サブスクリプション ID を取得します。</span><span class="sxs-lookup"><span data-stu-id="1d77a-337">Get your subscription ID with the following command:</span></span>

    ```azurecli
    az account show --query id
    ```

1. <span data-ttu-id="1d77a-338">サブスクリプション ID をコピーし、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="1d77a-338">Copy your subscription ID and run the following command:</span></span>

    ```azurecli
    az account set --subscription "<SUBSCRIPTION ID>"
    ```

1. <span data-ttu-id="1d77a-339">次のコマンドで、ベアラー トークンを取得します。</span><span class="sxs-lookup"><span data-stu-id="1d77a-339">Get your bearer token with the following command:</span></span>

    ```azurecli
    az account get-access-token --query accessToken
    ```

1. <span data-ttu-id="1d77a-340">HttpRepl を使用して、Azure REST API に接続します。</span><span class="sxs-lookup"><span data-stu-id="1d77a-340">Connect to the Azure REST API via the HttpRepl:</span></span>

    ```console
    httprepl https://management.azure.com
    ```

1. <span data-ttu-id="1d77a-341">`Authorization` HTTP 要求ヘッダーを設定します。</span><span class="sxs-lookup"><span data-stu-id="1d77a-341">Set the `Authorization` HTTP request header:</span></span>

    ```console
    https://management.azure.com/> set header Authorization "bearer <ACCESS TOKEN>"
    ```

1. <span data-ttu-id="1d77a-342">サブスクリプションに移動します。</span><span class="sxs-lookup"><span data-stu-id="1d77a-342">Navigate to the subscription:</span></span>

    ```console
    https://management.azure.com/> cd subscriptions/<SUBSCRIPTION ID>
    ```

1. <span data-ttu-id="1d77a-343">サブスクリプションの Azure App Service Web Apps のリストを取得します。</span><span class="sxs-lookup"><span data-stu-id="1d77a-343">Get a list of your subscription's Azure App Service Web Apps:</span></span>

    ```console
    https://management.azure.com/subscriptions/{SUBSCRIPTION ID}> get providers/Microsoft.Web/sites?api-version=2016-08-01
    ```

    <span data-ttu-id="1d77a-344">次の応答が示されます。</span><span class="sxs-lookup"><span data-stu-id="1d77a-344">The following response is displayed:</span></span>

    ```console
    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 35948
    Content-Type: application/json; charset=utf-8
    Date: Thu, 19 Sep 2019 23:04:03 GMT
    Expires: -1
    Pragma: no-cache
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    X-Content-Type-Options: nosniff
    x-ms-correlation-request-id: <em>xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</em>
    x-ms-original-request-ids: <em>xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx;xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</em>
    x-ms-ratelimit-remaining-subscription-reads: 11999
    x-ms-request-id: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    x-ms-routing-request-id: WESTUS:xxxxxxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx
    {
      "value": [
        <AZURE RESOURCES LIST>
      ]
    }
    ```

## <a name="toggle-http-request-display"></a><span data-ttu-id="1d77a-345">HTTP 要求の表示を切り替える</span><span class="sxs-lookup"><span data-stu-id="1d77a-345">Toggle HTTP request display</span></span>

<span data-ttu-id="1d77a-346">既定では、送信中の HTTP 要求の表示は抑制されます。</span><span class="sxs-lookup"><span data-stu-id="1d77a-346">By default, display of the HTTP request being sent is suppressed.</span></span> <span data-ttu-id="1d77a-347">コマンド シェル セッションの期間中は、対応する設定を変更できます。</span><span class="sxs-lookup"><span data-stu-id="1d77a-347">It's possible to change the corresponding setting for the duration of the command shell session.</span></span>

### <a name="enable-request-display"></a><span data-ttu-id="1d77a-348">要求の表示を有効にする</span><span class="sxs-lookup"><span data-stu-id="1d77a-348">Enable request display</span></span>

<span data-ttu-id="1d77a-349">`echo on` コマンドを実行して、送信中の HTTP 要求を表示します。</span><span class="sxs-lookup"><span data-stu-id="1d77a-349">View the HTTP request being sent by running the `echo on` command.</span></span> <span data-ttu-id="1d77a-350">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="1d77a-350">For example:</span></span>

```console
https://localhost:5001/people> echo on
Request echoing is on
```

<span data-ttu-id="1d77a-351">現在のセッションの後続の HTTP 要求では、要求ヘッダーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="1d77a-351">Subsequent HTTP requests in the current session display the request headers.</span></span> <span data-ttu-id="1d77a-352">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="1d77a-352">For example:</span></span>

```console
https://localhost:5001/people> post

[main 2019-06-28T18:50:11.930Z] update#setState idle
Request to https://localhost:5001...

POST /people HTTP/1.1
Content-Length: 41
Content-Type: application/json
User-Agent: HTTP-REPL

{
  "id": 0,
  "name": "Scott Addie"
}

Response from https://localhost:5001...

HTTP/1.1 201 Created
Content-Type: application/json; charset=utf-8
Date: Fri, 28 Jun 2019 18:50:21 GMT
Location: https://localhost:5001/people/4
Server: Kestrel
Transfer-Encoding: chunked

{
  "id": 4,
  "name": "Scott Addie"
}


https://localhost:5001/people>
```

### <a name="disable-request-display"></a><span data-ttu-id="1d77a-353">要求の表示を無効にする</span><span class="sxs-lookup"><span data-stu-id="1d77a-353">Disable request display</span></span>

<span data-ttu-id="1d77a-354">`echo off` コマンドを実行して、送信中の HTTP 要求の表示を抑制します。</span><span class="sxs-lookup"><span data-stu-id="1d77a-354">Suppress display of the HTTP request being sent by running the `echo off` command.</span></span> <span data-ttu-id="1d77a-355">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="1d77a-355">For example:</span></span>

```console
https://localhost:5001/people> echo off
Request echoing is off
```

## <a name="run-a-script"></a><span data-ttu-id="1d77a-356">[スクリプトの実行]</span><span class="sxs-lookup"><span data-stu-id="1d77a-356">Run a script</span></span>

<span data-ttu-id="1d77a-357">HttpRepl コマンドの同じセットを頻繁に実行する場合は、それらをテキスト ファイルに格納することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="1d77a-357">If you frequently execute the same set of HttpRepl commands, consider storing them in a text file.</span></span> <span data-ttu-id="1d77a-358">ファイル内のコマンドは、コマンド ラインで手動で実行されるコマンドと同じ形式になります。</span><span class="sxs-lookup"><span data-stu-id="1d77a-358">Commands in the file take the same form as commands executed manually on the command line.</span></span> <span data-ttu-id="1d77a-359">これらのコマンドは、`run` コマンドを使用してバッチ方式で実行できます。</span><span class="sxs-lookup"><span data-stu-id="1d77a-359">The commands can be executed in a batched fashion using the `run` command.</span></span> <span data-ttu-id="1d77a-360">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="1d77a-360">For example:</span></span>

1. <span data-ttu-id="1d77a-361">改行で区切られた一連のコマンドを含むテキスト ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="1d77a-361">Create a text file containing a set of newline-delimited commands.</span></span> <span data-ttu-id="1d77a-362">例として、次のコマンドを含む *people-script.txt* ファイルについて考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="1d77a-362">To illustrate, consider a *people-script.txt* file containing the following commands:</span></span>

    ```text
    set base https://localhost:5001
    ls
    cd People
    ls
    get 1
    ```

1. <span data-ttu-id="1d77a-363">`run` コマンドを実行し、テキスト ファイルのパスを渡します。</span><span class="sxs-lookup"><span data-stu-id="1d77a-363">Execute the `run` command, passing in the text file's path.</span></span> <span data-ttu-id="1d77a-364">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="1d77a-364">For example:</span></span>

    ```console
    https://localhost:5001/> run C:\http-repl-scripts\people-script.txt
    ```

    <span data-ttu-id="1d77a-365">次のような出力が表示されます。</span><span class="sxs-lookup"><span data-stu-id="1d77a-365">The following output appears:</span></span>

    ```console
    https://localhost:5001/> set base https://localhost:5001
    Using OpenAPI description at https://localhost:5001/swagger/v1/swagger.json
    
    https://localhost:5001/> ls
    .        []
    Fruits   [get|post]
    People   [get|post]
    
    https://localhost:5001/> cd People
    /People    [get|post]
    
    https://localhost:5001/People> ls
    .      [get|post]
    ..     []
    {id}   [get|put|delete]
    
    https://localhost:5001/People> get 1
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Fri, 12 Jul 2019 19:20:10 GMT
    Server: Kestrel
    Transfer-Encoding: chunked
    
    {
      "id": 1,
      "name": "Scott Hunter"
    }
    
    
    https://localhost:5001/People>
    ```

## <a name="clear-the-output"></a><span data-ttu-id="1d77a-366">出力を消去する</span><span class="sxs-lookup"><span data-stu-id="1d77a-366">Clear the output</span></span>

<span data-ttu-id="1d77a-367">HttpRepl ツールによってコマンド シェルに書き込まれたすべての出力を削除するには、`clear` または `cls` コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="1d77a-367">To remove all output written to the command shell by the HttpRepl tool, run the `clear` or `cls` command.</span></span> <span data-ttu-id="1d77a-368">例として、コマンド シェルに次の出力が含まれているとします。</span><span class="sxs-lookup"><span data-stu-id="1d77a-368">To illustrate, imagine the command shell contains the following output:</span></span>

```console
httprepl https://localhost:5001
(Disconnected)> set base "https://localhost:5001"
Using OpenAPI description at https://localhost:5001/swagger/v1/swagger.json

https://localhost:5001/> ls
.        []
Fruits   [get|post]
People   [get|post]

https://localhost:5001/>
```

<span data-ttu-id="1d77a-369">次のコマンドを実行して、出力を消去します。</span><span class="sxs-lookup"><span data-stu-id="1d77a-369">Run the following command to clear the output:</span></span>

```console
https://localhost:5001/> clear
```

<span data-ttu-id="1d77a-370">上記のコマンドを実行すると、コマンド シェルには次の出力のみが含まれます。</span><span class="sxs-lookup"><span data-stu-id="1d77a-370">After running the preceding command, the command shell contains only the following output:</span></span>

```console
https://localhost:5001/>
```

## <a name="additional-resources"></a><span data-ttu-id="1d77a-371">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="1d77a-371">Additional resources</span></span>

* [<span data-ttu-id="1d77a-372">REST API 要求</span><span class="sxs-lookup"><span data-stu-id="1d77a-372">REST API requests</span></span>](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods)
* [<span data-ttu-id="1d77a-373">HttpRepl GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="1d77a-373">HttpRepl GitHub repository</span></span>](https://github.com/dotnet/HttpRepl)
