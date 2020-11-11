---
title: HTTP REPL テレメトリ
author: scottaddie
description: HTTP REPL によって収集されたテレメトリについて説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.date: 11/10/2020
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
uid: web-api/http-repl/telemetry
ms.openlocfilehash: 8590959e43c2dda69090acb358e740b271426a44
ms.sourcegitcommit: fb72e9c1ae5b279817f1fb4b46a52170449b6f30
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2020
ms.locfileid: "94502004"
---
# <a name="http-repl-telemetry"></a><span data-ttu-id="2d313-103">HTTP REPL テレメトリ</span><span class="sxs-lookup"><span data-stu-id="2d313-103">HTTP REPL telemetry</span></span>

<span data-ttu-id="2d313-104">[HTTP Read-Eval-Print Loop (REPL)](xref:web-api/http-repl)には、使用状況データを収集するテレメトリ機能が含まれています。</span><span class="sxs-lookup"><span data-stu-id="2d313-104">The [HTTP Read-Eval-Print Loop (REPL)](xref:web-api/http-repl) includes a telemetry feature that collects usage data.</span></span> <span data-ttu-id="2d313-105">HTTP REPL チームが、ツールの使用方法を理解し、改善できるようにすることが重要です。</span><span class="sxs-lookup"><span data-stu-id="2d313-105">It's important that the HTTP REPL team understands how the tool is used so it can be improved.</span></span>

## <a name="how-to-opt-out"></a><span data-ttu-id="2d313-106">オプトアウトする方法</span><span class="sxs-lookup"><span data-stu-id="2d313-106">How to opt out</span></span>

<span data-ttu-id="2d313-107">HTTP REPL テレメトリ機能は、既定で有効になっています。</span><span class="sxs-lookup"><span data-stu-id="2d313-107">The HTTP REPL telemetry feature is enabled by default.</span></span> <span data-ttu-id="2d313-108">製品利用統計情報機能をオプトアウトするには、`DOTNET_HTTPREPL_TELEMETRY_OPTOUT` 環境変数を `1` または `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="2d313-108">To opt out of the telemetry feature, set the `DOTNET_HTTPREPL_TELEMETRY_OPTOUT` environment variable to `1` or `true`.</span></span>

## <a name="disclosure"></a><span data-ttu-id="2d313-109">開示</span><span class="sxs-lookup"><span data-stu-id="2d313-109">Disclosure</span></span>

<span data-ttu-id="2d313-110">HttpRepl では、ツールを初めて実行するときに、次のようなテキストが表示されます。</span><span class="sxs-lookup"><span data-stu-id="2d313-110">HttpRepl displays text similar to the following when you first run the tool.</span></span> <span data-ttu-id="2d313-111">テキストは、実行しているツールのバージョンによって多少異なる場合があります。</span><span class="sxs-lookup"><span data-stu-id="2d313-111">Text may vary slightly depending on the version of the tool you're running.</span></span> <span data-ttu-id="2d313-112">この "最初の実行" の際に、Microsoft がデータ回収に関して通知する方法が示されます。</span><span class="sxs-lookup"><span data-stu-id="2d313-112">This "first run" experience is how Microsoft notifies you about data collection.</span></span>

```console
Telemetry
---------
The .NET Core tools collect usage data in order to help us improve your experience. It is collected by Microsoft and shared with the community. You can opt-out of telemetry by setting the DOTNET_HTTPREPL_TELEMETRY_OPTOUT environment variable to '1' or 'true' using your favorite shell.
```

## <a name="data-points"></a><span data-ttu-id="2d313-113">データ ポイント</span><span class="sxs-lookup"><span data-stu-id="2d313-113">Data points</span></span>

<span data-ttu-id="2d313-114">テレメトリ機能は次のものではありません。</span><span class="sxs-lookup"><span data-stu-id="2d313-114">The telemetry feature doesn't:</span></span>

* <span data-ttu-id="2d313-115">ユーザー名、電子メールアドレス、Url などの個人データを収集します。</span><span class="sxs-lookup"><span data-stu-id="2d313-115">Collect personal data, such as usernames, email addresses, or URLs.</span></span>
* <span data-ttu-id="2d313-116">HTTP 要求または応答をスキャンします。</span><span class="sxs-lookup"><span data-stu-id="2d313-116">Scan your HTTP requests or responses.</span></span>

<span data-ttu-id="2d313-117">データは、Microsoft サーバーに安全に送信され、制限付きアクセスで保持されます。</span><span class="sxs-lookup"><span data-stu-id="2d313-117">The data is sent securely to Microsoft servers and held under restricted access.</span></span>

<span data-ttu-id="2d313-118">ユーザーのプライバシー保護は Microsoft にとって重要です。</span><span class="sxs-lookup"><span data-stu-id="2d313-118">Protecting your privacy is important to us.</span></span> <span data-ttu-id="2d313-119">テレメトリ機能が機密データを収集していると思われる場合、またはデータの安全でまたは不適切な処理が行われていると思われる場合は、次のいずれかの操作を実行します。</span><span class="sxs-lookup"><span data-stu-id="2d313-119">If you suspect the telemetry feature is collecting sensitive data or the data is being insecurely or inappropriately handled, take one of the following actions:</span></span>

* <span data-ttu-id="2d313-120">[Dotnet/httprepl](https://github.com/dotnet/httprepl/issues)リポジトリに問題を記載してください。</span><span class="sxs-lookup"><span data-stu-id="2d313-120">File an issue in the [dotnet/httprepl](https://github.com/dotnet/httprepl/issues) repository.</span></span>
* <span data-ttu-id="2d313-121">調査のために電子メールを送信 [dotnet@microsoft.com](mailto:dotnet@microsoft.com) します。</span><span class="sxs-lookup"><span data-stu-id="2d313-121">Send an email to [dotnet@microsoft.com](mailto:dotnet@microsoft.com) for investigation.</span></span>

<span data-ttu-id="2d313-122">テレメトリ機能は、次のデータを収集します。</span><span class="sxs-lookup"><span data-stu-id="2d313-122">The telemetry feature collects the following data.</span></span>

| <span data-ttu-id="2d313-123">.NET SDK のバージョン</span><span class="sxs-lookup"><span data-stu-id="2d313-123">.NET SDK versions</span></span> | <span data-ttu-id="2d313-124">データ</span><span class="sxs-lookup"><span data-stu-id="2d313-124">Data</span></span> |
|--------------|------|
| <span data-ttu-id="2d313-125">>= 5.0</span><span class="sxs-lookup"><span data-stu-id="2d313-125">>=5.0</span></span>        | <span data-ttu-id="2d313-126">呼び出しのタイムスタンプ。</span><span class="sxs-lookup"><span data-stu-id="2d313-126">Timestamp of invocation.</span></span> |
| <span data-ttu-id="2d313-127">>= 5.0</span><span class="sxs-lookup"><span data-stu-id="2d313-127">>=5.0</span></span>        | <span data-ttu-id="2d313-128">地理的な場所を決定するために使用される3オクテットの IP アドレス。</span><span class="sxs-lookup"><span data-stu-id="2d313-128">Three-octet IP address used to determine the geographical location.</span></span> |
| <span data-ttu-id="2d313-129">>= 5.0</span><span class="sxs-lookup"><span data-stu-id="2d313-129">>=5.0</span></span>        | <span data-ttu-id="2d313-130">オペレーティング システムとバージョン。</span><span class="sxs-lookup"><span data-stu-id="2d313-130">Operating system and version.</span></span> |
| <span data-ttu-id="2d313-131">>= 5.0</span><span class="sxs-lookup"><span data-stu-id="2d313-131">>=5.0</span></span>        | <span data-ttu-id="2d313-132">ツールが実行されているランタイム ID (RID)。</span><span class="sxs-lookup"><span data-stu-id="2d313-132">Runtime ID (RID) the tool is running on.</span></span> |
| <span data-ttu-id="2d313-133">>= 5.0</span><span class="sxs-lookup"><span data-stu-id="2d313-133">>=5.0</span></span>        | <span data-ttu-id="2d313-134">ツールがコンテナーで実行されているかどうか。</span><span class="sxs-lookup"><span data-stu-id="2d313-134">Whether the tool is running in a container.</span></span> |
| <span data-ttu-id="2d313-135">>= 5.0</span><span class="sxs-lookup"><span data-stu-id="2d313-135">>=5.0</span></span>        | <span data-ttu-id="2d313-136">ハッシュされたメディア Access Control (MAC) アドレス: コンピューターの暗号 (SHA256) ハッシュされた一意の ID。</span><span class="sxs-lookup"><span data-stu-id="2d313-136">Hashed Media Access Control (MAC) address: a cryptographically (SHA256) hashed and unique ID for a machine.</span></span> |
| <span data-ttu-id="2d313-137">>= 5.0</span><span class="sxs-lookup"><span data-stu-id="2d313-137">>=5.0</span></span>        | <span data-ttu-id="2d313-138">カーネル バージョン。</span><span class="sxs-lookup"><span data-stu-id="2d313-138">Kernel version.</span></span> |
| <span data-ttu-id="2d313-139">>= 5.0</span><span class="sxs-lookup"><span data-stu-id="2d313-139">>=5.0</span></span>        | <span data-ttu-id="2d313-140">HTTP REPL バージョン。</span><span class="sxs-lookup"><span data-stu-id="2d313-140">HTTP REPL version.</span></span> |
| <span data-ttu-id="2d313-141">>= 5.0</span><span class="sxs-lookup"><span data-stu-id="2d313-141">>=5.0</span></span>        | <span data-ttu-id="2d313-142">ツールが、、のいずれかの引数を使用して開始されたかどうか `help` `run` `connect` 。</span><span class="sxs-lookup"><span data-stu-id="2d313-142">Whether the tool was started with `help`, `run`, or `connect` arguments.</span></span> <span data-ttu-id="2d313-143">実際の引数値は収集されません。</span><span class="sxs-lookup"><span data-stu-id="2d313-143">Actual argument values aren't collected.</span></span> |
| <span data-ttu-id="2d313-144">>= 5.0</span><span class="sxs-lookup"><span data-stu-id="2d313-144">>=5.0</span></span>        | <span data-ttu-id="2d313-145">呼び出されたコマンド (など `get` ) と、成功したかどうか。</span><span class="sxs-lookup"><span data-stu-id="2d313-145">Command invoked (for example, `get`) and whether it succeeded.</span></span> |
| <span data-ttu-id="2d313-146">>= 5.0</span><span class="sxs-lookup"><span data-stu-id="2d313-146">>=5.0</span></span>        | <span data-ttu-id="2d313-147">コマンドの場合、 `connect` `root` 、、のいずれかの `base` 引数が指定されているかどうか `openapi` 。</span><span class="sxs-lookup"><span data-stu-id="2d313-147">For the `connect` command, whether the `root`, `base`, or `openapi` arguments were supplied.</span></span> <span data-ttu-id="2d313-148">実際の引数値は収集されません。</span><span class="sxs-lookup"><span data-stu-id="2d313-148">Actual argument values aren't collected.</span></span> |
| <span data-ttu-id="2d313-149">>= 5.0</span><span class="sxs-lookup"><span data-stu-id="2d313-149">>=5.0</span></span>        | <span data-ttu-id="2d313-150">コマンドの場合 `pref` `get` は、またはが発行されたかどうか、 `set` およびアクセスされた設定があるかどうか。</span><span class="sxs-lookup"><span data-stu-id="2d313-150">For the `pref` command, whether a `get` or `set` was issued and which preference was accessed.</span></span> <span data-ttu-id="2d313-151">よく知られていない場合は、名前がハッシュ化されます。</span><span class="sxs-lookup"><span data-stu-id="2d313-151">If not a well-known preference, the name is hashed.</span></span> <span data-ttu-id="2d313-152">値は収集されません。</span><span class="sxs-lookup"><span data-stu-id="2d313-152">The value isn't collected.</span></span> |
| <span data-ttu-id="2d313-153">>= 5.0</span><span class="sxs-lookup"><span data-stu-id="2d313-153">>=5.0</span></span>        | <span data-ttu-id="2d313-154">コマンドの場合 `set header` 、設定されているヘッダー名。</span><span class="sxs-lookup"><span data-stu-id="2d313-154">For the `set header` command, the header name being set.</span></span> <span data-ttu-id="2d313-155">Well-known ヘッダーでない場合は、名前がハッシュ化されます。</span><span class="sxs-lookup"><span data-stu-id="2d313-155">If not a well-known header, the name is hashed.</span></span> <span data-ttu-id="2d313-156">値は収集されません。</span><span class="sxs-lookup"><span data-stu-id="2d313-156">The value isn't collected.</span></span> |
| <span data-ttu-id="2d313-157">>= 5.0</span><span class="sxs-lookup"><span data-stu-id="2d313-157">>=5.0</span></span>        | <span data-ttu-id="2d313-158">コマンドの場合は `connect` 、の特殊なケースが使用されているかどうか、 `dotnet new webapi` および、優先順位によってバイパスされたかどうかを指定します。</span><span class="sxs-lookup"><span data-stu-id="2d313-158">For the `connect` command, whether a special case for `dotnet new webapi` was used and, whether it was bypassed via preference.</span></span> |
| <span data-ttu-id="2d313-159">>= 5.0</span><span class="sxs-lookup"><span data-stu-id="2d313-159">>=5.0</span></span>        | <span data-ttu-id="2d313-160">すべての HTTP コマンド (GET、POST、PUT など) について、各オプションが指定されているかどうかを示します。</span><span class="sxs-lookup"><span data-stu-id="2d313-160">For all HTTP commands (for example, GET, POST, PUT), whether each of the options was specified.</span></span> <span data-ttu-id="2d313-161">オプションの値は収集されません。</span><span class="sxs-lookup"><span data-stu-id="2d313-161">The values of the options aren't collected.</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="2d313-162">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="2d313-162">Additional resources</span></span>

* [<span data-ttu-id="2d313-163">.NET Core SDK 製品利用統計情報</span><span class="sxs-lookup"><span data-stu-id="2d313-163">.NET Core SDK telemetry</span></span>](/dotnet/core/tools/telemetry)
* [<span data-ttu-id="2d313-164">テレメトリデータを .NET Core CLI する</span><span class="sxs-lookup"><span data-stu-id="2d313-164">.NET Core CLI telemetry data</span></span>](https://dotnet.microsoft.com/platform/telemetry)
