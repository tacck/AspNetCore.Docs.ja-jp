---
title: ASP.NET Core の認証サンプル
author: rick-anderson
description: ASP.NET Core リポジトリの認証サンプルへのリンクを提供します。
ms.author: riande
ms.date: 01/31/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/samples
ms.openlocfilehash: 7cd0fe60d7917abda7d8ac0e071deca13a4136ce
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776553"
---
# <a name="authentication-samples-for-aspnet-core"></a><span data-ttu-id="ec099-103">ASP.NET Core の認証サンプル</span><span class="sxs-lookup"><span data-stu-id="ec099-103">Authentication samples for ASP.NET Core</span></span>

<span data-ttu-id="ec099-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="ec099-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ec099-105">[ASP.NET Core リポジトリ](https://github.com/dotnet/AspNetCore)には、 *AspNetCore/src/Security/samples*フォルダーに次の認証サンプルが含まれています。</span><span class="sxs-lookup"><span data-stu-id="ec099-105">The [ASP.NET Core repository](https://github.com/dotnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="ec099-106">要求の変換</span><span class="sxs-lookup"><span data-stu-id="ec099-106">Claims transformation</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/ClaimsTransformation)
* [<span data-ttu-id="ec099-107">Cookie 認証</span><span class="sxs-lookup"><span data-stu-id="ec099-107">Cookie authentication</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Cookies)
* [<span data-ttu-id="ec099-108">カスタムポリシープロバイダー-IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="ec099-108">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="ec099-109">動的な認証スキームとオプション</span><span class="sxs-lookup"><span data-stu-id="ec099-109">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/DynamicSchemes)
* <span data-ttu-id="ec099-110">[外部要求](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Identity.ExternalClaims)</span><span class="sxs-lookup"><span data-stu-id="ec099-110">[External claims](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Identity.ExternalClaims)</span></span>
* [<span data-ttu-id="ec099-111">要求に基づいて cookie と別の認証スキームのどちらを選択するか</span><span class="sxs-lookup"><span data-stu-id="ec099-111">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="ec099-112">静的ファイルへのアクセスを制限する</span><span class="sxs-lookup"><span data-stu-id="ec099-112">Restricts access to static files</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="ec099-113">サンプルを実行する</span><span class="sxs-lookup"><span data-stu-id="ec099-113">Run the samples</span></span>

* <span data-ttu-id="ec099-114">[ブランチ](https://github.com/dotnet/AspNetCore)を選択してください。</span><span class="sxs-lookup"><span data-stu-id="ec099-114">Select a [branch](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="ec099-115">たとえば、`release/3.1` のように指定します。</span><span class="sxs-lookup"><span data-stu-id="ec099-115">For example, `release/3.1`</span></span>
* <span data-ttu-id="ec099-116">[ASP.NET Core リポジトリ](https://github.com/dotnet/AspNetCore)を複製またはダウンロードします。</span><span class="sxs-lookup"><span data-stu-id="ec099-116">Clone or download the [ASP.NET Core repository](https://github.com/dotnet/AspNetCore).</span></span>
* <span data-ttu-id="ec099-117">ASP.NET Core リポジトリの複製に一致する[.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core)バージョンがインストールされていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="ec099-117">Verify you have installed the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="ec099-118">*AspNetCore/src/Security/samples*のサンプルに移動し、で`dotnet run`サンプルを実行します。</span><span class="sxs-lookup"><span data-stu-id="ec099-118">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ec099-119">[ASP.NET Core リポジトリ](https://github.com/dotnet/AspNetCore)には、 *AspNetCore/src/Security/samples*フォルダーに次の認証サンプルが含まれています。</span><span class="sxs-lookup"><span data-stu-id="ec099-119">The [ASP.NET Core repository](https://github.com/dotnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="ec099-120">要求の変換</span><span class="sxs-lookup"><span data-stu-id="ec099-120">Claims transformation</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/ClaimsTransformation)
* [<span data-ttu-id="ec099-121">Cookie 認証</span><span class="sxs-lookup"><span data-stu-id="ec099-121">Cookie authentication</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Cookies)
* [<span data-ttu-id="ec099-122">カスタムポリシープロバイダー-IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="ec099-122">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="ec099-123">動的な認証スキームとオプション</span><span class="sxs-lookup"><span data-stu-id="ec099-123">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/DynamicSchemes)
* <span data-ttu-id="ec099-124">[外部要求](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Identity.ExternalClaims)</span><span class="sxs-lookup"><span data-stu-id="ec099-124">[External claims](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Identity.ExternalClaims)</span></span>
* [<span data-ttu-id="ec099-125">要求に基づいて cookie と別の認証スキームのどちらを選択するか</span><span class="sxs-lookup"><span data-stu-id="ec099-125">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="ec099-126">静的ファイルへのアクセスを制限する</span><span class="sxs-lookup"><span data-stu-id="ec099-126">Restricts access to static files</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="ec099-127">サンプルを実行する</span><span class="sxs-lookup"><span data-stu-id="ec099-127">Run the samples</span></span>

* <span data-ttu-id="ec099-128">[ブランチ](https://github.com/dotnet/AspNetCore)を選択してください。</span><span class="sxs-lookup"><span data-stu-id="ec099-128">Select a [branch](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="ec099-129">たとえば、`release/2.2` のように指定します。</span><span class="sxs-lookup"><span data-stu-id="ec099-129">For example, `release/2.2`</span></span>
* <span data-ttu-id="ec099-130">[ASP.NET Core リポジトリ](https://github.com/dotnet/AspNetCore)を複製またはダウンロードします。</span><span class="sxs-lookup"><span data-stu-id="ec099-130">Clone or download the [ASP.NET Core repository](https://github.com/dotnet/AspNetCore).</span></span>
* <span data-ttu-id="ec099-131">ASP.NET Core リポジトリの複製に一致する[.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core)バージョンがインストールされていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="ec099-131">Verify you have installed the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="ec099-132">*AspNetCore/src/Security/samples*のサンプルに移動し、で`dotnet run`サンプルを実行します。</span><span class="sxs-lookup"><span data-stu-id="ec099-132">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end
