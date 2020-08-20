---
title: ASP.NET Core Blazor でサポートされているプラットフォーム
author: guardrex
description: ASP.NET Core Blazor でサポートされているプラットフォームについて学習します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
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
uid: blazor/supported-platforms
ms.openlocfilehash: 692ab63bb48dbfa29021d59cdf035e9549d3039c
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88625948"
---
# <a name="aspnet-core-no-locblazor-supported-platforms"></a><span data-ttu-id="7f67a-103">ASP.NET Core Blazor でサポートされているプラットフォーム</span><span class="sxs-lookup"><span data-stu-id="7f67a-103">ASP.NET Core Blazor supported platforms</span></span>

<span data-ttu-id="7f67a-104">作成者: [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="7f67a-104">By [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="browser-requirements"></a><span data-ttu-id="7f67a-105">ブラウザー要件</span><span class="sxs-lookup"><span data-stu-id="7f67a-105">Browser requirements</span></span>

### Blazor WebAssembly

| <span data-ttu-id="7f67a-106">ブラウザー</span><span class="sxs-lookup"><span data-stu-id="7f67a-106">Browser</span></span>                          | <span data-ttu-id="7f67a-107">バージョン</span><span class="sxs-lookup"><span data-stu-id="7f67a-107">Version</span></span>               |
| -------------------------------- | :-------------------: |
| <span data-ttu-id="7f67a-108">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="7f67a-108">Microsoft Edge</span></span>                   | <span data-ttu-id="7f67a-109">[現在]</span><span class="sxs-lookup"><span data-stu-id="7f67a-109">Current</span></span>               |
| <span data-ttu-id="7f67a-110">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="7f67a-110">Mozilla Firefox</span></span>                  | <span data-ttu-id="7f67a-111">[現在]</span><span class="sxs-lookup"><span data-stu-id="7f67a-111">Current</span></span>               |
| <span data-ttu-id="7f67a-112">Google Chrome (Android を含む)</span><span class="sxs-lookup"><span data-stu-id="7f67a-112">Google Chrome, including Android</span></span> | <span data-ttu-id="7f67a-113">[現在]</span><span class="sxs-lookup"><span data-stu-id="7f67a-113">Current</span></span>               |
| <span data-ttu-id="7f67a-114">Safari (iOS を含む)</span><span class="sxs-lookup"><span data-stu-id="7f67a-114">Safari, including iOS</span></span>            | <span data-ttu-id="7f67a-115">[現在]</span><span class="sxs-lookup"><span data-stu-id="7f67a-115">Current</span></span>               |
| <span data-ttu-id="7f67a-116">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="7f67a-116">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="7f67a-117">サポートされていません&dagger;</span><span class="sxs-lookup"><span data-stu-id="7f67a-117">Not Supported&dagger;</span></span> |

<span data-ttu-id="7f67a-118">&dagger;Microsoft Internet Explorer は [WebAssembly](https://webassembly.org) をサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="7f67a-118">&dagger;Microsoft Internet Explorer doesn't support [WebAssembly](https://webassembly.org).</span></span>

### Blazor Server

| <span data-ttu-id="7f67a-119">ブラウザー</span><span class="sxs-lookup"><span data-stu-id="7f67a-119">Browser</span></span>                          | <span data-ttu-id="7f67a-120">バージョン</span><span class="sxs-lookup"><span data-stu-id="7f67a-120">Version</span></span>    |
| -------------------------------- | :--------: |
| <span data-ttu-id="7f67a-121">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="7f67a-121">Microsoft Edge</span></span>                   | <span data-ttu-id="7f67a-122">[現在]</span><span class="sxs-lookup"><span data-stu-id="7f67a-122">Current</span></span>    |
| <span data-ttu-id="7f67a-123">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="7f67a-123">Mozilla Firefox</span></span>                  | <span data-ttu-id="7f67a-124">[現在]</span><span class="sxs-lookup"><span data-stu-id="7f67a-124">Current</span></span>    |
| <span data-ttu-id="7f67a-125">Google Chrome (Android を含む)</span><span class="sxs-lookup"><span data-stu-id="7f67a-125">Google Chrome, including Android</span></span> | <span data-ttu-id="7f67a-126">[現在]</span><span class="sxs-lookup"><span data-stu-id="7f67a-126">Current</span></span>    |
| <span data-ttu-id="7f67a-127">Safari (iOS を含む)</span><span class="sxs-lookup"><span data-stu-id="7f67a-127">Safari, including iOS</span></span>            | <span data-ttu-id="7f67a-128">[現在]</span><span class="sxs-lookup"><span data-stu-id="7f67a-128">Current</span></span>    |
| <span data-ttu-id="7f67a-129">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="7f67a-129">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="7f67a-130">11&dagger;</span><span class="sxs-lookup"><span data-stu-id="7f67a-130">11&dagger;</span></span> |

<span data-ttu-id="7f67a-131">&dagger;追加のポリフィルが必要です (たとえば、[`Polyfill.io`](https://polyfill.io/v3/) バンドルによって Promise を追加できます)。</span><span class="sxs-lookup"><span data-stu-id="7f67a-131">&dagger;Additional polyfills are required (for example, promises can be added via a [`Polyfill.io`](https://polyfill.io/v3/) bundle).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7f67a-132">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="7f67a-132">Additional resources</span></span>

* <xref:blazor/hosting-models>
