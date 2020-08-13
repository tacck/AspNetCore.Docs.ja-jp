---
title: ASP.NET Core Blazor でサポートされているプラットフォーム
author: guardrex
description: ASP.NET Core Blazor でサポートされているプラットフォームについて学習します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/supported-platforms
ms.openlocfilehash: 67896bcdd5d99ff2c9cf22e3902262f9513eb4f6
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2020
ms.locfileid: "88013529"
---
# <a name="aspnet-core-no-locblazor-supported-platforms"></a><span data-ttu-id="1dd8a-103">ASP.NET Core Blazor でサポートされているプラットフォーム</span><span class="sxs-lookup"><span data-stu-id="1dd8a-103">ASP.NET Core Blazor supported platforms</span></span>

<span data-ttu-id="1dd8a-104">作成者: [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="1dd8a-104">By [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="browser-requirements"></a><span data-ttu-id="1dd8a-105">ブラウザー要件</span><span class="sxs-lookup"><span data-stu-id="1dd8a-105">Browser requirements</span></span>

### Blazor WebAssembly

| <span data-ttu-id="1dd8a-106">ブラウザー</span><span class="sxs-lookup"><span data-stu-id="1dd8a-106">Browser</span></span>                          | <span data-ttu-id="1dd8a-107">バージョン</span><span class="sxs-lookup"><span data-stu-id="1dd8a-107">Version</span></span>               |
| -------------------------------- | :-------------------: |
| <span data-ttu-id="1dd8a-108">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="1dd8a-108">Microsoft Edge</span></span>                   | <span data-ttu-id="1dd8a-109">[現在]</span><span class="sxs-lookup"><span data-stu-id="1dd8a-109">Current</span></span>               |
| <span data-ttu-id="1dd8a-110">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="1dd8a-110">Mozilla Firefox</span></span>                  | <span data-ttu-id="1dd8a-111">[現在]</span><span class="sxs-lookup"><span data-stu-id="1dd8a-111">Current</span></span>               |
| <span data-ttu-id="1dd8a-112">Google Chrome (Android を含む)</span><span class="sxs-lookup"><span data-stu-id="1dd8a-112">Google Chrome, including Android</span></span> | <span data-ttu-id="1dd8a-113">[現在]</span><span class="sxs-lookup"><span data-stu-id="1dd8a-113">Current</span></span>               |
| <span data-ttu-id="1dd8a-114">Safari (iOS を含む)</span><span class="sxs-lookup"><span data-stu-id="1dd8a-114">Safari, including iOS</span></span>            | <span data-ttu-id="1dd8a-115">[現在]</span><span class="sxs-lookup"><span data-stu-id="1dd8a-115">Current</span></span>               |
| <span data-ttu-id="1dd8a-116">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="1dd8a-116">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="1dd8a-117">サポートされていません&dagger;</span><span class="sxs-lookup"><span data-stu-id="1dd8a-117">Not Supported&dagger;</span></span> |

<span data-ttu-id="1dd8a-118">&dagger;Microsoft Internet Explorer は [WebAssembly](https://webassembly.org) をサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="1dd8a-118">&dagger;Microsoft Internet Explorer doesn't support [WebAssembly](https://webassembly.org).</span></span>

### Blazor Server

| <span data-ttu-id="1dd8a-119">ブラウザー</span><span class="sxs-lookup"><span data-stu-id="1dd8a-119">Browser</span></span>                          | <span data-ttu-id="1dd8a-120">バージョン</span><span class="sxs-lookup"><span data-stu-id="1dd8a-120">Version</span></span>    |
| -------------------------------- | :--------: |
| <span data-ttu-id="1dd8a-121">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="1dd8a-121">Microsoft Edge</span></span>                   | <span data-ttu-id="1dd8a-122">[現在]</span><span class="sxs-lookup"><span data-stu-id="1dd8a-122">Current</span></span>    |
| <span data-ttu-id="1dd8a-123">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="1dd8a-123">Mozilla Firefox</span></span>                  | <span data-ttu-id="1dd8a-124">[現在]</span><span class="sxs-lookup"><span data-stu-id="1dd8a-124">Current</span></span>    |
| <span data-ttu-id="1dd8a-125">Google Chrome (Android を含む)</span><span class="sxs-lookup"><span data-stu-id="1dd8a-125">Google Chrome, including Android</span></span> | <span data-ttu-id="1dd8a-126">[現在]</span><span class="sxs-lookup"><span data-stu-id="1dd8a-126">Current</span></span>    |
| <span data-ttu-id="1dd8a-127">Safari (iOS を含む)</span><span class="sxs-lookup"><span data-stu-id="1dd8a-127">Safari, including iOS</span></span>            | <span data-ttu-id="1dd8a-128">[現在]</span><span class="sxs-lookup"><span data-stu-id="1dd8a-128">Current</span></span>    |
| <span data-ttu-id="1dd8a-129">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="1dd8a-129">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="1dd8a-130">11&dagger;</span><span class="sxs-lookup"><span data-stu-id="1dd8a-130">11&dagger;</span></span> |

<span data-ttu-id="1dd8a-131">&dagger;追加のポリフィルが必要です (たとえば、[`Polyfill.io`](https://polyfill.io/v3/) バンドルによって Promise を追加できます)。</span><span class="sxs-lookup"><span data-stu-id="1dd8a-131">&dagger;Additional polyfills are required (for example, promises can be added via a [`Polyfill.io`](https://polyfill.io/v3/) bundle).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1dd8a-132">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="1dd8a-132">Additional resources</span></span>

* <xref:blazor/hosting-models>
