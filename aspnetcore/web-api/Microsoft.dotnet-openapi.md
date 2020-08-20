---
title: OpenAPI を使用した ASP.NET Core アプリの開発
author: ryanbrandenburg
description: Microsoft.dotnet-openapi ツールを使用して OpenAPI ファイルへの参照を追加する方法を説明します。
ms.author: rybrande
ms.date: 09/26/2019
monikerRange: '>= aspnetcore-3.0'
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
uid: web-api/Microsoft.dotnet-openapi
ms.openlocfilehash: 45921deb35452876b0a92a8731da68539a880c1d
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88626559"
---
# <a name="develop-aspnet-core-apps-using-openapi-tools"></a><span data-ttu-id="5727c-103">OpenAPI ツールを使用した ASP.NET Core アプリの開発</span><span class="sxs-lookup"><span data-stu-id="5727c-103">Develop ASP.NET Core apps using OpenAPI tools</span></span>

<span data-ttu-id="5727c-104">作成者: Ryan Brandenburg</span><span class="sxs-lookup"><span data-stu-id="5727c-104">By Ryan Brandenburg</span></span>

<span data-ttu-id="5727c-105">[Microsoft.dotnet-openapi](https://www.nuget.org/packages/Microsoft.dotnet-openapi) は、プロジェクト内での [OpenAPI](https://github.com/OAI/OpenAPI-Specification) 参照を管理するための [.NET Core グローバル ツール](/dotnet/core/tools/global-tools)です。</span><span class="sxs-lookup"><span data-stu-id="5727c-105">[Microsoft.dotnet-openapi](https://www.nuget.org/packages/Microsoft.dotnet-openapi) is a [.NET Core Global Tool](/dotnet/core/tools/global-tools) for managing [OpenAPI](https://github.com/OAI/OpenAPI-Specification) references within a project.</span></span>

## <a name="installation"></a><span data-ttu-id="5727c-106">インストール</span><span class="sxs-lookup"><span data-stu-id="5727c-106">Installation</span></span>

<span data-ttu-id="5727c-107">`Microsoft.dotnet-openapi` をインストールするには次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="5727c-107">To install `Microsoft.dotnet-openapi`, run the following command:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.dotnet-openapi
```

## <a name="add"></a><span data-ttu-id="5727c-108">追加</span><span class="sxs-lookup"><span data-stu-id="5727c-108">Add</span></span>

<span data-ttu-id="5727c-109">このページのいずれかのコマンドを使用して OpenAPI 参照を追加すると、 `<OpenApiReference />` *.csproj* ファイルに次のような要素が追加されます。</span><span class="sxs-lookup"><span data-stu-id="5727c-109">Adding an OpenAPI reference using any of the commands on this page adds an `<OpenApiReference />` element similar to the following to the *.csproj* file:</span></span>

```xml
<OpenApiReference Include="openapi.json" />
```

<span data-ttu-id="5727c-110">前の参照は、生成されるクライアント コードをアプリが呼び出すために必要です。</span><span class="sxs-lookup"><span data-stu-id="5727c-110">The preceding reference is required for the app to call the generated client code.</span></span>

<!-- TODO: Restore after https://github.com/dotnet/AspNetCore/issues/12738
### Add Project

#### Options

| Short option | Long option | Description | Example |
|-------|------|-------|---------|
| -p|--project | The project to operate on. |dotnet openapi add project *--project .\Ref.csproj* ../Ref/ProjRef.csproj |

#### Arguments

|  Argument  | Description | Example |
|-------------|-------------|---------|
| source-file | The source to create a reference from. Must be a project file. |dotnet openapi add project *../Ref/ProjRef.csproj* | -->

### <a name="add-file"></a><span data-ttu-id="5727c-111">ファイルの追加</span><span class="sxs-lookup"><span data-stu-id="5727c-111">Add File</span></span>

#### <a name="options"></a><span data-ttu-id="5727c-112">オプション</span><span class="sxs-lookup"><span data-stu-id="5727c-112">Options</span></span>

| <span data-ttu-id="5727c-113">短いオプション</span><span class="sxs-lookup"><span data-stu-id="5727c-113">Short option</span></span>| <span data-ttu-id="5727c-114">長いオプション</span><span class="sxs-lookup"><span data-stu-id="5727c-114">Long option</span></span>| <span data-ttu-id="5727c-115">説明</span><span class="sxs-lookup"><span data-stu-id="5727c-115">Description</span></span> | <span data-ttu-id="5727c-116">例</span><span class="sxs-lookup"><span data-stu-id="5727c-116">Example</span></span> |
|-------|------|-------|---------|
| <span data-ttu-id="5727c-117">-p</span><span class="sxs-lookup"><span data-stu-id="5727c-117">-p</span></span>|<span data-ttu-id="5727c-118">--updateProject</span><span class="sxs-lookup"><span data-stu-id="5727c-118">--updateProject</span></span> | <span data-ttu-id="5727c-119">操作対象のプロジェクト。</span><span class="sxs-lookup"><span data-stu-id="5727c-119">The project to operate on.</span></span> |<span data-ttu-id="5727c-120">dotnet openapi add file *--updateProject .\Ref.csproj* .\OpenAPI.json</span><span class="sxs-lookup"><span data-stu-id="5727c-120">dotnet openapi add file *--updateProject .\Ref.csproj* .\OpenAPI.json</span></span> |
| <span data-ttu-id="5727c-121">-c</span><span class="sxs-lookup"><span data-stu-id="5727c-121">-c</span></span>|<span data-ttu-id="5727c-122">--code-generator</span><span class="sxs-lookup"><span data-stu-id="5727c-122">--code-generator</span></span>| <span data-ttu-id="5727c-123">参照に適用するコード ジェネレーター。</span><span class="sxs-lookup"><span data-stu-id="5727c-123">The code generator to apply to the reference.</span></span> <span data-ttu-id="5727c-124">オプションは `NSwagCSharp` と `NSwagTypeScript` です。</span><span class="sxs-lookup"><span data-stu-id="5727c-124">Options are `NSwagCSharp` and `NSwagTypeScript`.</span></span> <span data-ttu-id="5727c-125">`--code-generator` を指定しない場合、ツールは既定で `NSwagCSharp` になります。</span><span class="sxs-lookup"><span data-stu-id="5727c-125">If `--code-generator` is not specified the tooling defaults to `NSwagCSharp`.</span></span>|<span data-ttu-id="5727c-126">dotnet openapi add file .\OpenApi.json --code-generator</span><span class="sxs-lookup"><span data-stu-id="5727c-126">dotnet openapi add file .\OpenApi.json --code-generator</span></span>
| <span data-ttu-id="5727c-127">-H</span><span class="sxs-lookup"><span data-stu-id="5727c-127">-h</span></span>|<span data-ttu-id="5727c-128">--help</span><span class="sxs-lookup"><span data-stu-id="5727c-128">--help</span></span>|<span data-ttu-id="5727c-129">ヘルプ情報を表示します。</span><span class="sxs-lookup"><span data-stu-id="5727c-129">Show help information</span></span>|<span data-ttu-id="5727c-130">dotnet openapi add file --help</span><span class="sxs-lookup"><span data-stu-id="5727c-130">dotnet openapi add file --help</span></span>|

#### <a name="arguments"></a><span data-ttu-id="5727c-131">引数</span><span class="sxs-lookup"><span data-stu-id="5727c-131">Arguments</span></span>

|  <span data-ttu-id="5727c-132">引数</span><span class="sxs-lookup"><span data-stu-id="5727c-132">Argument</span></span>  | <span data-ttu-id="5727c-133">説明</span><span class="sxs-lookup"><span data-stu-id="5727c-133">Description</span></span> | <span data-ttu-id="5727c-134">例</span><span class="sxs-lookup"><span data-stu-id="5727c-134">Example</span></span> |
|-------------|-------------|---------|
| <span data-ttu-id="5727c-135">source-file</span><span class="sxs-lookup"><span data-stu-id="5727c-135">source-file</span></span> | <span data-ttu-id="5727c-136">参照の作成元のソース。</span><span class="sxs-lookup"><span data-stu-id="5727c-136">The source to create a reference from.</span></span> <span data-ttu-id="5727c-137">OpenAPI ファイルである必要があります。</span><span class="sxs-lookup"><span data-stu-id="5727c-137">Must be an OpenAPI file.</span></span> |<span data-ttu-id="5727c-138">dotnet openapi add file *.\OpenAPI.json*</span><span class="sxs-lookup"><span data-stu-id="5727c-138">dotnet openapi add file *.\OpenAPI.json*</span></span> |

### <a name="add-url"></a><span data-ttu-id="5727c-139">URL の追加</span><span class="sxs-lookup"><span data-stu-id="5727c-139">Add URL</span></span>

#### <a name="options"></a><span data-ttu-id="5727c-140">オプション</span><span class="sxs-lookup"><span data-stu-id="5727c-140">Options</span></span>

| <span data-ttu-id="5727c-141">短いオプション</span><span class="sxs-lookup"><span data-stu-id="5727c-141">Short option</span></span>| <span data-ttu-id="5727c-142">長いオプション</span><span class="sxs-lookup"><span data-stu-id="5727c-142">Long option</span></span>| <span data-ttu-id="5727c-143">説明</span><span class="sxs-lookup"><span data-stu-id="5727c-143">Description</span></span> | <span data-ttu-id="5727c-144">例</span><span class="sxs-lookup"><span data-stu-id="5727c-144">Example</span></span> |
|-------|------|-------------|---------|
| <span data-ttu-id="5727c-145">-p</span><span class="sxs-lookup"><span data-stu-id="5727c-145">-p</span></span>|<span data-ttu-id="5727c-146">--updateProject</span><span class="sxs-lookup"><span data-stu-id="5727c-146">--updateProject</span></span> | <span data-ttu-id="5727c-147">操作対象のプロジェクト。</span><span class="sxs-lookup"><span data-stu-id="5727c-147">The project to operate on.</span></span> |<span data-ttu-id="5727c-148">dotnet openapi add url *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="5727c-148">dotnet openapi add url *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="5727c-149">-o</span><span class="sxs-lookup"><span data-stu-id="5727c-149">-o</span></span>|<span data-ttu-id="5727c-150">--output-file</span><span class="sxs-lookup"><span data-stu-id="5727c-150">--output-file</span></span> | <span data-ttu-id="5727c-151">OpenAPI ファイルのローカル コピーを配置する場所。</span><span class="sxs-lookup"><span data-stu-id="5727c-151">Where to place the local copy of the OpenAPI file.</span></span> |<span data-ttu-id="5727c-152">dotnet openapi add url `https://contoso.com/openapi.json` *--output-file myclient.json*</span><span class="sxs-lookup"><span data-stu-id="5727c-152">dotnet openapi add url `https://contoso.com/openapi.json` *--output-file myclient.json*</span></span> |
| <span data-ttu-id="5727c-153">-c</span><span class="sxs-lookup"><span data-stu-id="5727c-153">-c</span></span>|<span data-ttu-id="5727c-154">--code-generator</span><span class="sxs-lookup"><span data-stu-id="5727c-154">--code-generator</span></span>| <span data-ttu-id="5727c-155">参照に適用するコード ジェネレーター。</span><span class="sxs-lookup"><span data-stu-id="5727c-155">The code generator to apply to the reference.</span></span> <span data-ttu-id="5727c-156">オプションは `NSwagCSharp` と `NSwagTypeScript` です。</span><span class="sxs-lookup"><span data-stu-id="5727c-156">Options are `NSwagCSharp` and `NSwagTypeScript`.</span></span> |<span data-ttu-id="5727c-157">dotnet openapi add file .\OpenApi.json --code-generator</span><span class="sxs-lookup"><span data-stu-id="5727c-157">dotnet openapi add file .\OpenApi.json --code-generator</span></span>
| <span data-ttu-id="5727c-158">-H</span><span class="sxs-lookup"><span data-stu-id="5727c-158">-h</span></span>|<span data-ttu-id="5727c-159">--help</span><span class="sxs-lookup"><span data-stu-id="5727c-159">--help</span></span>|<span data-ttu-id="5727c-160">ヘルプ情報を表示します。</span><span class="sxs-lookup"><span data-stu-id="5727c-160">Show help information</span></span>|<span data-ttu-id="5727c-161">dotnet openapi add url --help</span><span class="sxs-lookup"><span data-stu-id="5727c-161">dotnet openapi add url --help</span></span>|

#### <a name="arguments"></a><span data-ttu-id="5727c-162">引数</span><span class="sxs-lookup"><span data-stu-id="5727c-162">Arguments</span></span>

|  <span data-ttu-id="5727c-163">引数</span><span class="sxs-lookup"><span data-stu-id="5727c-163">Argument</span></span>  | <span data-ttu-id="5727c-164">説明</span><span class="sxs-lookup"><span data-stu-id="5727c-164">Description</span></span> | <span data-ttu-id="5727c-165">例</span><span class="sxs-lookup"><span data-stu-id="5727c-165">Example</span></span> |
|-------------|-------------|---------|
| <span data-ttu-id="5727c-166">source-URL</span><span class="sxs-lookup"><span data-stu-id="5727c-166">source-URL</span></span> | <span data-ttu-id="5727c-167">参照の作成元のソース。</span><span class="sxs-lookup"><span data-stu-id="5727c-167">The source to create a reference from.</span></span> <span data-ttu-id="5727c-168">URL である必要があります。</span><span class="sxs-lookup"><span data-stu-id="5727c-168">Must be a URL.</span></span> |<span data-ttu-id="5727c-169">dotnet openapi add url `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="5727c-169">dotnet openapi add url `https://contoso.com/openapi.json`</span></span> |

## <a name="remove"></a><span data-ttu-id="5727c-170">[削除]</span><span class="sxs-lookup"><span data-stu-id="5727c-170">Remove</span></span>

<span data-ttu-id="5727c-171">指定したファイル名と一致する OpenAPI 参照を *.csproj* ファイルから削除します。</span><span class="sxs-lookup"><span data-stu-id="5727c-171">Removes the OpenAPI reference matching the given filename from the *.csproj* file.</span></span> <span data-ttu-id="5727c-172">OpenAPI 参照が削除されると、クライアントは生成されません。</span><span class="sxs-lookup"><span data-stu-id="5727c-172">When the OpenAPI reference is removed, clients won't be generated.</span></span> <span data-ttu-id="5727c-173">ローカルの *.json* および *.yaml* ファイルは削除されます。</span><span class="sxs-lookup"><span data-stu-id="5727c-173">Local *.json* and *.yaml* files are deleted.</span></span>

### <a name="options"></a><span data-ttu-id="5727c-174">オプション</span><span class="sxs-lookup"><span data-stu-id="5727c-174">Options</span></span>

| <span data-ttu-id="5727c-175">短いオプション</span><span class="sxs-lookup"><span data-stu-id="5727c-175">Short option</span></span>| <span data-ttu-id="5727c-176">長いオプション</span><span class="sxs-lookup"><span data-stu-id="5727c-176">Long option</span></span>| <span data-ttu-id="5727c-177">説明</span><span class="sxs-lookup"><span data-stu-id="5727c-177">Description</span></span>| <span data-ttu-id="5727c-178">例</span><span class="sxs-lookup"><span data-stu-id="5727c-178">Example</span></span> |
|-------|------|------------|---------|
| <span data-ttu-id="5727c-179">-p</span><span class="sxs-lookup"><span data-stu-id="5727c-179">-p</span></span>|<span data-ttu-id="5727c-180">--updateProject</span><span class="sxs-lookup"><span data-stu-id="5727c-180">--updateProject</span></span> | <span data-ttu-id="5727c-181">操作対象のプロジェクト。</span><span class="sxs-lookup"><span data-stu-id="5727c-181">The project to operate on.</span></span> |<span data-ttu-id="5727c-182">dotnet openapi remove *--updateProject .\Ref.csproj* .\OpenAPI.json</span><span class="sxs-lookup"><span data-stu-id="5727c-182">dotnet openapi remove *--updateProject .\Ref.csproj* .\OpenAPI.json</span></span> |
| <span data-ttu-id="5727c-183">-H</span><span class="sxs-lookup"><span data-stu-id="5727c-183">-h</span></span>|<span data-ttu-id="5727c-184">--help</span><span class="sxs-lookup"><span data-stu-id="5727c-184">--help</span></span>|<span data-ttu-id="5727c-185">ヘルプ情報を表示します。</span><span class="sxs-lookup"><span data-stu-id="5727c-185">Show help information</span></span>|<span data-ttu-id="5727c-186">dotnet openapi remove --help</span><span class="sxs-lookup"><span data-stu-id="5727c-186">dotnet openapi remove --help</span></span>|

### <a name="arguments"></a><span data-ttu-id="5727c-187">引数</span><span class="sxs-lookup"><span data-stu-id="5727c-187">Arguments</span></span>

|  <span data-ttu-id="5727c-188">引数</span><span class="sxs-lookup"><span data-stu-id="5727c-188">Argument</span></span>  | <span data-ttu-id="5727c-189">説明</span><span class="sxs-lookup"><span data-stu-id="5727c-189">Description</span></span>| <span data-ttu-id="5727c-190">例</span><span class="sxs-lookup"><span data-stu-id="5727c-190">Example</span></span> |
| ------------|------------|---------|
| <span data-ttu-id="5727c-191">source-file</span><span class="sxs-lookup"><span data-stu-id="5727c-191">source-file</span></span> | <span data-ttu-id="5727c-192">削除する参照のソース。</span><span class="sxs-lookup"><span data-stu-id="5727c-192">The source to remove the reference to.</span></span> |<span data-ttu-id="5727c-193">dotnet openapi remove *.\OpenAPI.json*</span><span class="sxs-lookup"><span data-stu-id="5727c-193">dotnet openapi remove *.\OpenAPI.json*</span></span> |

## <a name="refresh"></a><span data-ttu-id="5727c-194">更新</span><span class="sxs-lookup"><span data-stu-id="5727c-194">Refresh</span></span>

<span data-ttu-id="5727c-195">ダウンロード URL の最新のコンテンツを使用してダウンロードされたファイルのローカル バージョンを更新します。</span><span class="sxs-lookup"><span data-stu-id="5727c-195">Refreshes the local version of a file that was downloaded using the latest content from the download URL.</span></span>

### <a name="options"></a><span data-ttu-id="5727c-196">オプション</span><span class="sxs-lookup"><span data-stu-id="5727c-196">Options</span></span>

| <span data-ttu-id="5727c-197">短いオプション</span><span class="sxs-lookup"><span data-stu-id="5727c-197">Short option</span></span>| <span data-ttu-id="5727c-198">長いオプション</span><span class="sxs-lookup"><span data-stu-id="5727c-198">Long option</span></span>| <span data-ttu-id="5727c-199">説明</span><span class="sxs-lookup"><span data-stu-id="5727c-199">Description</span></span> | <span data-ttu-id="5727c-200">例</span><span class="sxs-lookup"><span data-stu-id="5727c-200">Example</span></span> |
|-------|------|-------------|---------|
| <span data-ttu-id="5727c-201">-p</span><span class="sxs-lookup"><span data-stu-id="5727c-201">-p</span></span>|<span data-ttu-id="5727c-202">--updateProject</span><span class="sxs-lookup"><span data-stu-id="5727c-202">--updateProject</span></span> | <span data-ttu-id="5727c-203">操作対象のプロジェクト。</span><span class="sxs-lookup"><span data-stu-id="5727c-203">The project to operate on.</span></span> | <span data-ttu-id="5727c-204">dotnet openapi refresh *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="5727c-204">dotnet openapi refresh *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="5727c-205">-H</span><span class="sxs-lookup"><span data-stu-id="5727c-205">-h</span></span>|<span data-ttu-id="5727c-206">--help</span><span class="sxs-lookup"><span data-stu-id="5727c-206">--help</span></span>|<span data-ttu-id="5727c-207">ヘルプ情報を表示します。</span><span class="sxs-lookup"><span data-stu-id="5727c-207">Show help information</span></span>|<span data-ttu-id="5727c-208">dotnet openapi refresh --help</span><span class="sxs-lookup"><span data-stu-id="5727c-208">dotnet openapi refresh --help</span></span>|

### <a name="arguments"></a><span data-ttu-id="5727c-209">引数</span><span class="sxs-lookup"><span data-stu-id="5727c-209">Arguments</span></span>

|  <span data-ttu-id="5727c-210">引数</span><span class="sxs-lookup"><span data-stu-id="5727c-210">Argument</span></span>  | <span data-ttu-id="5727c-211">説明</span><span class="sxs-lookup"><span data-stu-id="5727c-211">Description</span></span> | <span data-ttu-id="5727c-212">例</span><span class="sxs-lookup"><span data-stu-id="5727c-212">Example</span></span> |
| ------------|-------------|---------|
| <span data-ttu-id="5727c-213">source-URL</span><span class="sxs-lookup"><span data-stu-id="5727c-213">source-URL</span></span> | <span data-ttu-id="5727c-214">最新状態にする参照の URL。</span><span class="sxs-lookup"><span data-stu-id="5727c-214">The URL to refresh the reference from.</span></span> | <span data-ttu-id="5727c-215">dotnet openapi refresh `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="5727c-215">dotnet openapi refresh `https://contoso.com/openapi.json`</span></span> |
