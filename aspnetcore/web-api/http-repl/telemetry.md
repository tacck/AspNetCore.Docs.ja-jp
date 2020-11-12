---
title: HttpRepl テレメトリ
author: scottaddie
description: HttpRepl によって収集されたテレメトリについて説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.date: 11/11/2020
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
ms.openlocfilehash: 5ff22753f566c494e51dae67c8c4f6371211be78
ms.sourcegitcommit: 202144092067ea81be1dbb229329518d781dbdfb
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2020
ms.locfileid: "94550609"
---
# <a name="httprepl-telemetry"></a><span data-ttu-id="20a5a-103">HttpRepl テレメトリ</span><span class="sxs-lookup"><span data-stu-id="20a5a-103">HttpRepl telemetry</span></span>

<span data-ttu-id="20a5a-104">[HttpRepl](xref:web-api/http-repl)には、使用状況データを収集するテレメトリ機能が含まれています。</span><span class="sxs-lookup"><span data-stu-id="20a5a-104">The [HttpRepl](xref:web-api/http-repl) includes a telemetry feature that collects usage data.</span></span> <span data-ttu-id="20a5a-105">HttpRepl チームは、ツールがどのように使用されているかを理解し、改善できるようにすることが重要です。</span><span class="sxs-lookup"><span data-stu-id="20a5a-105">It's important that the HttpRepl team understands how the tool is used so it can be improved.</span></span>

## <a name="how-to-opt-out"></a><span data-ttu-id="20a5a-106">オプトアウトする方法</span><span class="sxs-lookup"><span data-stu-id="20a5a-106">How to opt out</span></span>

<span data-ttu-id="20a5a-107">HttpRepl テレメトリ機能は、既定で有効になっています。</span><span class="sxs-lookup"><span data-stu-id="20a5a-107">The HttpRepl telemetry feature is enabled by default.</span></span> <span data-ttu-id="20a5a-108">製品利用統計情報機能をオプトアウトするには、`DOTNET_HTTPREPL_TELEMETRY_OPTOUT` 環境変数を `1` または `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="20a5a-108">To opt out of the telemetry feature, set the `DOTNET_HTTPREPL_TELEMETRY_OPTOUT` environment variable to `1` or `true`.</span></span>

## <a name="disclosure"></a><span data-ttu-id="20a5a-109">開示</span><span class="sxs-lookup"><span data-stu-id="20a5a-109">Disclosure</span></span>

<span data-ttu-id="20a5a-110">HttpRepl では、ツールを初めて実行したときに、次のようなテキストが表示されます。</span><span class="sxs-lookup"><span data-stu-id="20a5a-110">The HttpRepl displays text similar to the following when you first run the tool.</span></span> <span data-ttu-id="20a5a-111">テキストは、実行しているツールのバージョンによって多少異なる場合があります。</span><span class="sxs-lookup"><span data-stu-id="20a5a-111">Text may vary slightly depending on the version of the tool you're running.</span></span> <span data-ttu-id="20a5a-112">この "最初の実行" の際に、Microsoft がデータ回収に関して通知する方法が示されます。</span><span class="sxs-lookup"><span data-stu-id="20a5a-112">This "first run" experience is how Microsoft notifies you about data collection.</span></span>

```console
Telemetry
---------
The .NET tools collect usage data in order to help us improve your experience. It is collected by Microsoft and shared with the community. You can opt-out of telemetry by setting the DOTNET_HTTPREPL_TELEMETRY_OPTOUT environment variable to '1' or 'true' using your favorite shell.
```

<span data-ttu-id="20a5a-113">"最初の実行" のエクスペリエンステキストを抑制するに `DOTNET_HTTPREPL_SKIP_FIRST_TIME_EXPERIENCE` は、環境変数をまたはに設定し `1` `true` ます。</span><span class="sxs-lookup"><span data-stu-id="20a5a-113">To suppress the "first run" experience text, set the `DOTNET_HTTPREPL_SKIP_FIRST_TIME_EXPERIENCE` environment variable to `1` or `true`.</span></span>

## <a name="data-points"></a><span data-ttu-id="20a5a-114">データ ポイント</span><span class="sxs-lookup"><span data-stu-id="20a5a-114">Data points</span></span>

<span data-ttu-id="20a5a-115">テレメトリ機能は次のものではありません。</span><span class="sxs-lookup"><span data-stu-id="20a5a-115">The telemetry feature doesn't:</span></span>

* <span data-ttu-id="20a5a-116">ユーザー名、電子メールアドレス、Url などの個人データを収集します。</span><span class="sxs-lookup"><span data-stu-id="20a5a-116">Collect personal data, such as usernames, email addresses, or URLs.</span></span>
* <span data-ttu-id="20a5a-117">HTTP 要求または応答をスキャンします。</span><span class="sxs-lookup"><span data-stu-id="20a5a-117">Scan your HTTP requests or responses.</span></span>

<span data-ttu-id="20a5a-118">データは、Microsoft サーバーに安全に送信され、制限付きアクセスで保持されます。</span><span class="sxs-lookup"><span data-stu-id="20a5a-118">The data is sent securely to Microsoft servers and held under restricted access.</span></span>

<span data-ttu-id="20a5a-119">ユーザーのプライバシー保護は Microsoft にとって重要です。</span><span class="sxs-lookup"><span data-stu-id="20a5a-119">Protecting your privacy is important to us.</span></span> <span data-ttu-id="20a5a-120">テレメトリ機能が機密データを収集していると思われる場合、またはデータの安全でまたは不適切な処理が行われていると思われる場合は、次のいずれかの操作を実行します。</span><span class="sxs-lookup"><span data-stu-id="20a5a-120">If you suspect the telemetry feature is collecting sensitive data or the data is being insecurely or inappropriately handled, take one of the following actions:</span></span>

* <span data-ttu-id="20a5a-121">[Dotnet/httprepl](https://github.com/dotnet/httprepl/issues)リポジトリに問題を記載してください。</span><span class="sxs-lookup"><span data-stu-id="20a5a-121">File an issue in the [dotnet/httprepl](https://github.com/dotnet/httprepl/issues) repository.</span></span>
* <span data-ttu-id="20a5a-122">調査のために電子メールを送信 [dotnet@microsoft.com](mailto:dotnet@microsoft.com) します。</span><span class="sxs-lookup"><span data-stu-id="20a5a-122">Send an email to [dotnet@microsoft.com](mailto:dotnet@microsoft.com) for investigation.</span></span>

<span data-ttu-id="20a5a-123">テレメトリ機能は、次のデータを収集します。</span><span class="sxs-lookup"><span data-stu-id="20a5a-123">The telemetry feature collects the following data.</span></span>

| <span data-ttu-id="20a5a-124">.NET SDK のバージョン</span><span class="sxs-lookup"><span data-stu-id="20a5a-124">.NET SDK versions</span></span> | <span data-ttu-id="20a5a-125">Data</span><span class="sxs-lookup"><span data-stu-id="20a5a-125">Data</span></span> |
|--------------|------|
| <span data-ttu-id="20a5a-126">>= 5.0</span><span class="sxs-lookup"><span data-stu-id="20a5a-126">>=5.0</span></span>        | <span data-ttu-id="20a5a-127">呼び出しのタイムスタンプ。</span><span class="sxs-lookup"><span data-stu-id="20a5a-127">Timestamp of invocation.</span></span> |
| <span data-ttu-id="20a5a-128">>= 5.0</span><span class="sxs-lookup"><span data-stu-id="20a5a-128">>=5.0</span></span>        | <span data-ttu-id="20a5a-129">地理的な場所を決定するために使用される3オクテットの IP アドレス。</span><span class="sxs-lookup"><span data-stu-id="20a5a-129">Three-octet IP address used to determine the geographical location.</span></span> |
| <span data-ttu-id="20a5a-130">>= 5.0</span><span class="sxs-lookup"><span data-stu-id="20a5a-130">>=5.0</span></span>        | <span data-ttu-id="20a5a-131">オペレーティング システムとバージョン。</span><span class="sxs-lookup"><span data-stu-id="20a5a-131">Operating system and version.</span></span> |
| <span data-ttu-id="20a5a-132">>= 5.0</span><span class="sxs-lookup"><span data-stu-id="20a5a-132">>=5.0</span></span>        | <span data-ttu-id="20a5a-133">ツールが実行されているランタイム ID (RID)。</span><span class="sxs-lookup"><span data-stu-id="20a5a-133">Runtime ID (RID) the tool is running on.</span></span> |
| <span data-ttu-id="20a5a-134">>= 5.0</span><span class="sxs-lookup"><span data-stu-id="20a5a-134">>=5.0</span></span>        | <span data-ttu-id="20a5a-135">ツールがコンテナーで実行されているかどうか。</span><span class="sxs-lookup"><span data-stu-id="20a5a-135">Whether the tool is running in a container.</span></span> |
| <span data-ttu-id="20a5a-136">>= 5.0</span><span class="sxs-lookup"><span data-stu-id="20a5a-136">>=5.0</span></span>        | <span data-ttu-id="20a5a-137">ハッシュされたメディア Access Control (MAC) アドレス: コンピューターの暗号 (SHA256) ハッシュされた一意の ID。</span><span class="sxs-lookup"><span data-stu-id="20a5a-137">Hashed Media Access Control (MAC) address: a cryptographically (SHA256) hashed and unique ID for a machine.</span></span> |
| <span data-ttu-id="20a5a-138">>= 5.0</span><span class="sxs-lookup"><span data-stu-id="20a5a-138">>=5.0</span></span>        | <span data-ttu-id="20a5a-139">カーネル バージョン。</span><span class="sxs-lookup"><span data-stu-id="20a5a-139">Kernel version.</span></span> |
| <span data-ttu-id="20a5a-140">>= 5.0</span><span class="sxs-lookup"><span data-stu-id="20a5a-140">>=5.0</span></span>        | <span data-ttu-id="20a5a-141">HttpRepl のバージョン。</span><span class="sxs-lookup"><span data-stu-id="20a5a-141">HttpRepl version.</span></span> |
| <span data-ttu-id="20a5a-142">>= 5.0</span><span class="sxs-lookup"><span data-stu-id="20a5a-142">>=5.0</span></span>        | <span data-ttu-id="20a5a-143">ツールが、、のいずれかの引数を使用して開始されたかどうか `help` `run` `connect` 。</span><span class="sxs-lookup"><span data-stu-id="20a5a-143">Whether the tool was started with `help`, `run`, or `connect` arguments.</span></span> <span data-ttu-id="20a5a-144">実際の引数値は収集されません。</span><span class="sxs-lookup"><span data-stu-id="20a5a-144">Actual argument values aren't collected.</span></span> |
| <span data-ttu-id="20a5a-145">>= 5.0</span><span class="sxs-lookup"><span data-stu-id="20a5a-145">>=5.0</span></span>        | <span data-ttu-id="20a5a-146">呼び出されたコマンド (など `get` ) と、成功したかどうか。</span><span class="sxs-lookup"><span data-stu-id="20a5a-146">Command invoked (for example, `get`) and whether it succeeded.</span></span> |
| <span data-ttu-id="20a5a-147">>= 5.0</span><span class="sxs-lookup"><span data-stu-id="20a5a-147">>=5.0</span></span>        | <span data-ttu-id="20a5a-148">コマンドの場合、 `connect` `root` 、、のいずれかの `base` 引数が指定されているかどうか `openapi` 。</span><span class="sxs-lookup"><span data-stu-id="20a5a-148">For the `connect` command, whether the `root`, `base`, or `openapi` arguments were supplied.</span></span> <span data-ttu-id="20a5a-149">実際の引数値は収集されません。</span><span class="sxs-lookup"><span data-stu-id="20a5a-149">Actual argument values aren't collected.</span></span> |
| <span data-ttu-id="20a5a-150">>= 5.0</span><span class="sxs-lookup"><span data-stu-id="20a5a-150">>=5.0</span></span>        | <span data-ttu-id="20a5a-151">コマンドの場合 `pref` `get` は、またはが発行されたかどうか、 `set` およびアクセスされた設定があるかどうか。</span><span class="sxs-lookup"><span data-stu-id="20a5a-151">For the `pref` command, whether a `get` or `set` was issued and which preference was accessed.</span></span> <span data-ttu-id="20a5a-152">よく知られていない場合は、名前がハッシュ化されます。</span><span class="sxs-lookup"><span data-stu-id="20a5a-152">If not a well-known preference, the name is hashed.</span></span> <span data-ttu-id="20a5a-153">値は収集されません。</span><span class="sxs-lookup"><span data-stu-id="20a5a-153">The value isn't collected.</span></span> |
| <span data-ttu-id="20a5a-154">>= 5.0</span><span class="sxs-lookup"><span data-stu-id="20a5a-154">>=5.0</span></span>        | <span data-ttu-id="20a5a-155">コマンドの場合 `set header` 、設定されているヘッダー名。</span><span class="sxs-lookup"><span data-stu-id="20a5a-155">For the `set header` command, the header name being set.</span></span> <span data-ttu-id="20a5a-156">Well-known ヘッダーでない場合は、名前がハッシュ化されます。</span><span class="sxs-lookup"><span data-stu-id="20a5a-156">If not a well-known header, the name is hashed.</span></span> <span data-ttu-id="20a5a-157">値は収集されません。</span><span class="sxs-lookup"><span data-stu-id="20a5a-157">The value isn't collected.</span></span> |
| <span data-ttu-id="20a5a-158">>= 5.0</span><span class="sxs-lookup"><span data-stu-id="20a5a-158">>=5.0</span></span>        | <span data-ttu-id="20a5a-159">コマンドの場合は `connect` 、の特殊なケースが使用されているかどうか、 `dotnet new webapi` および、優先順位によってバイパスされたかどうかを指定します。</span><span class="sxs-lookup"><span data-stu-id="20a5a-159">For the `connect` command, whether a special case for `dotnet new webapi` was used and, whether it was bypassed via preference.</span></span> |
| <span data-ttu-id="20a5a-160">>= 5.0</span><span class="sxs-lookup"><span data-stu-id="20a5a-160">>=5.0</span></span>        | <span data-ttu-id="20a5a-161">すべての HTTP コマンド (GET、POST、PUT など) について、各オプションが指定されているかどうかを示します。</span><span class="sxs-lookup"><span data-stu-id="20a5a-161">For all HTTP commands (for example, GET, POST, PUT), whether each of the options was specified.</span></span> <span data-ttu-id="20a5a-162">オプションの値は収集されません。</span><span class="sxs-lookup"><span data-stu-id="20a5a-162">The values of the options aren't collected.</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="20a5a-163">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="20a5a-163">Additional resources</span></span>

* [<span data-ttu-id="20a5a-164">.NET Core SDK 製品利用統計情報</span><span class="sxs-lookup"><span data-stu-id="20a5a-164">.NET Core SDK telemetry</span></span>](/dotnet/core/tools/telemetry)
* [<span data-ttu-id="20a5a-165">テレメトリデータを .NET Core CLI する</span><span class="sxs-lookup"><span data-stu-id="20a5a-165">.NET Core CLI telemetry data</span></span>](https://dotnet.microsoft.com/platform/telemetry)
