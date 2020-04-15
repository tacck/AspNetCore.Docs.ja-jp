---
title: ASP.NETコアの認証サンプル
author: rick-anderson
description: ASP.NET Core リポジトリの認証サンプルへのリンクを示します。
ms.author: riande
ms.date: 01/31/2019
uid: security/authentication/samples
ms.openlocfilehash: b33eaa5c1bda9e23b2815cd1663e02ae06fec856
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/14/2020
ms.locfileid: "81308216"
---
# <a name="authentication-samples-for-aspnet-core"></a><span data-ttu-id="c0a1f-103">ASP.NETコアの認証サンプル</span><span class="sxs-lookup"><span data-stu-id="c0a1f-103">Authentication samples for ASP.NET Core</span></span>

<span data-ttu-id="c0a1f-104">著者 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="c0a1f-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c0a1f-105">[ASP.NETコアリポジトリ](https://github.com/dotnet/AspNetCore)には *、AspNetCore/src/セキュリティ/サンプル*フォルダーに次の認証サンプルが含まれています。</span><span class="sxs-lookup"><span data-stu-id="c0a1f-105">The [ASP.NET Core repository](https://github.com/dotnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="c0a1f-106">要求の変換</span><span class="sxs-lookup"><span data-stu-id="c0a1f-106">Claims transformation</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/ClaimsTransformation)
* [<span data-ttu-id="c0a1f-107">クッキー認証</span><span class="sxs-lookup"><span data-stu-id="c0a1f-107">Cookie authentication</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Cookies)
* [<span data-ttu-id="c0a1f-108">カスタム ポリシー プロバイダー - I 認証ポリシー プロバイダー</span><span class="sxs-lookup"><span data-stu-id="c0a1f-108">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="c0a1f-109">動的認証スキームとオプション</span><span class="sxs-lookup"><span data-stu-id="c0a1f-109">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/DynamicSchemes)
* [<span data-ttu-id="c0a1f-110">外部クレーム</span><span class="sxs-lookup"><span data-stu-id="c0a1f-110">External claims</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Identity.ExternalClaims)
* [<span data-ttu-id="c0a1f-111">要求に基づいて Cookie と別の認証方式の選択</span><span class="sxs-lookup"><span data-stu-id="c0a1f-111">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="c0a1f-112">静的ファイルへのアクセスを制限します。</span><span class="sxs-lookup"><span data-stu-id="c0a1f-112">Restricts access to static files</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="c0a1f-113">サンプルを実行する</span><span class="sxs-lookup"><span data-stu-id="c0a1f-113">Run the samples</span></span>

* <span data-ttu-id="c0a1f-114">[ブランチ](https://github.com/dotnet/AspNetCore)を選択します。</span><span class="sxs-lookup"><span data-stu-id="c0a1f-114">Select a [branch](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="c0a1f-115">たとえば、`release/3.1`</span><span class="sxs-lookup"><span data-stu-id="c0a1f-115">For example, `release/3.1`</span></span>
* <span data-ttu-id="c0a1f-116">[ASP.NETコア リポジトリ](https://github.com/dotnet/AspNetCore)を複製またはダウンロードする 。</span><span class="sxs-lookup"><span data-stu-id="c0a1f-116">Clone or download the [ASP.NET Core repository](https://github.com/dotnet/AspNetCore).</span></span>
* <span data-ttu-id="c0a1f-117">ASP.NET コア リポジトリの複製に一致する[.NET](https://dotnet.microsoft.com/download/dotnet-core) Core SDK バージョンがインストールされていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="c0a1f-117">Verify you have installed the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="c0a1f-118">*AspNetCore/src/セキュリティ/サンプル*のサンプルに移動し、サンプルを使用して`dotnet run`サンプルを実行します。</span><span class="sxs-lookup"><span data-stu-id="c0a1f-118">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c0a1f-119">[ASP.NETコアリポジトリ](https://github.com/dotnet/AspNetCore)には *、AspNetCore/src/セキュリティ/サンプル*フォルダーに次の認証サンプルが含まれています。</span><span class="sxs-lookup"><span data-stu-id="c0a1f-119">The [ASP.NET Core repository](https://github.com/dotnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="c0a1f-120">要求の変換</span><span class="sxs-lookup"><span data-stu-id="c0a1f-120">Claims transformation</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/ClaimsTransformation)
* [<span data-ttu-id="c0a1f-121">クッキー認証</span><span class="sxs-lookup"><span data-stu-id="c0a1f-121">Cookie authentication</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Cookies)
* [<span data-ttu-id="c0a1f-122">カスタム ポリシー プロバイダー - I 認証ポリシー プロバイダー</span><span class="sxs-lookup"><span data-stu-id="c0a1f-122">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="c0a1f-123">動的認証スキームとオプション</span><span class="sxs-lookup"><span data-stu-id="c0a1f-123">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/DynamicSchemes)
* [<span data-ttu-id="c0a1f-124">外部クレーム</span><span class="sxs-lookup"><span data-stu-id="c0a1f-124">External claims</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Identity.ExternalClaims)
* [<span data-ttu-id="c0a1f-125">要求に基づいて Cookie と別の認証方式の選択</span><span class="sxs-lookup"><span data-stu-id="c0a1f-125">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="c0a1f-126">静的ファイルへのアクセスを制限します。</span><span class="sxs-lookup"><span data-stu-id="c0a1f-126">Restricts access to static files</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="c0a1f-127">サンプルを実行する</span><span class="sxs-lookup"><span data-stu-id="c0a1f-127">Run the samples</span></span>

* <span data-ttu-id="c0a1f-128">[ブランチ](https://github.com/dotnet/AspNetCore)を選択します。</span><span class="sxs-lookup"><span data-stu-id="c0a1f-128">Select a [branch](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="c0a1f-129">たとえば、`release/2.2`</span><span class="sxs-lookup"><span data-stu-id="c0a1f-129">For example, `release/2.2`</span></span>
* <span data-ttu-id="c0a1f-130">[ASP.NETコア リポジトリ](https://github.com/dotnet/AspNetCore)を複製またはダウンロードする 。</span><span class="sxs-lookup"><span data-stu-id="c0a1f-130">Clone or download the [ASP.NET Core repository](https://github.com/dotnet/AspNetCore).</span></span>
* <span data-ttu-id="c0a1f-131">ASP.NET コア リポジトリの複製に一致する[.NET](https://dotnet.microsoft.com/download/dotnet-core) Core SDK バージョンがインストールされていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="c0a1f-131">Verify you have installed the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="c0a1f-132">*AspNetCore/src/セキュリティ/サンプル*のサンプルに移動し、サンプルを使用して`dotnet run`サンプルを実行します。</span><span class="sxs-lookup"><span data-stu-id="c0a1f-132">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end
