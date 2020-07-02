---
title: ASP.NET Core Blazor でサポートされているプラットフォーム
author: guardrex
description: ASP.NET Core Blazor でサポートされているプラットフォームについて学習します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/supported-platforms
ms.openlocfilehash: adf27fa84acb3929a1639b561c728c2db29723f6
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/26/2020
ms.locfileid: "85401936"
---
# <a name="aspnet-core-blazor-supported-platforms"></a><span data-ttu-id="e6c52-103">ASP.NET Core Blazor でサポートされているプラットフォーム</span><span class="sxs-lookup"><span data-stu-id="e6c52-103">ASP.NET Core Blazor supported platforms</span></span>

<span data-ttu-id="e6c52-104">作成者: [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="e6c52-104">By [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="browser-requirements"></a><span data-ttu-id="e6c52-105">ブラウザー要件</span><span class="sxs-lookup"><span data-stu-id="e6c52-105">Browser requirements</span></span>

### Blazor WebAssembly

| <span data-ttu-id="e6c52-106">ブラウザー</span><span class="sxs-lookup"><span data-stu-id="e6c52-106">Browser</span></span>                          | <span data-ttu-id="e6c52-107">バージョン</span><span class="sxs-lookup"><span data-stu-id="e6c52-107">Version</span></span>               |
| -------------------------------- | :-------------------: |
| <span data-ttu-id="e6c52-108">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="e6c52-108">Microsoft Edge</span></span>                   | <span data-ttu-id="e6c52-109">[現在]</span><span class="sxs-lookup"><span data-stu-id="e6c52-109">Current</span></span>               |
| <span data-ttu-id="e6c52-110">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="e6c52-110">Mozilla Firefox</span></span>                  | <span data-ttu-id="e6c52-111">[現在]</span><span class="sxs-lookup"><span data-stu-id="e6c52-111">Current</span></span>               |
| <span data-ttu-id="e6c52-112">Google Chrome (Android を含む)</span><span class="sxs-lookup"><span data-stu-id="e6c52-112">Google Chrome, including Android</span></span> | <span data-ttu-id="e6c52-113">[現在]</span><span class="sxs-lookup"><span data-stu-id="e6c52-113">Current</span></span>               |
| <span data-ttu-id="e6c52-114">Safari (iOS を含む)</span><span class="sxs-lookup"><span data-stu-id="e6c52-114">Safari, including iOS</span></span>            | <span data-ttu-id="e6c52-115">[現在]</span><span class="sxs-lookup"><span data-stu-id="e6c52-115">Current</span></span>               |
| <span data-ttu-id="e6c52-116">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="e6c52-116">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="e6c52-117">サポートされていません&dagger;</span><span class="sxs-lookup"><span data-stu-id="e6c52-117">Not Supported&dagger;</span></span> |

<span data-ttu-id="e6c52-118">&dagger;Microsoft Internet Explorer は [WebAssembly](https://webassembly.org) をサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="e6c52-118">&dagger;Microsoft Internet Explorer doesn't support [WebAssembly](https://webassembly.org).</span></span>

### Blazor Server

| <span data-ttu-id="e6c52-119">ブラウザー</span><span class="sxs-lookup"><span data-stu-id="e6c52-119">Browser</span></span>                          | <span data-ttu-id="e6c52-120">バージョン</span><span class="sxs-lookup"><span data-stu-id="e6c52-120">Version</span></span>    |
| -------------------------------- | :--------: |
| <span data-ttu-id="e6c52-121">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="e6c52-121">Microsoft Edge</span></span>                   | <span data-ttu-id="e6c52-122">[現在]</span><span class="sxs-lookup"><span data-stu-id="e6c52-122">Current</span></span>    |
| <span data-ttu-id="e6c52-123">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="e6c52-123">Mozilla Firefox</span></span>                  | <span data-ttu-id="e6c52-124">[現在]</span><span class="sxs-lookup"><span data-stu-id="e6c52-124">Current</span></span>    |
| <span data-ttu-id="e6c52-125">Google Chrome (Android を含む)</span><span class="sxs-lookup"><span data-stu-id="e6c52-125">Google Chrome, including Android</span></span> | <span data-ttu-id="e6c52-126">[現在]</span><span class="sxs-lookup"><span data-stu-id="e6c52-126">Current</span></span>    |
| <span data-ttu-id="e6c52-127">Safari (iOS を含む)</span><span class="sxs-lookup"><span data-stu-id="e6c52-127">Safari, including iOS</span></span>            | <span data-ttu-id="e6c52-128">[現在]</span><span class="sxs-lookup"><span data-stu-id="e6c52-128">Current</span></span>    |
| <span data-ttu-id="e6c52-129">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="e6c52-129">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="e6c52-130">11&dagger;</span><span class="sxs-lookup"><span data-stu-id="e6c52-130">11&dagger;</span></span> |

<span data-ttu-id="e6c52-131">&dagger;追加のポリフィルが必要です (たとえば、[`Polyfill.io`](https://polyfill.io/v3/) バンドルによって Promise を追加できます)。</span><span class="sxs-lookup"><span data-stu-id="e6c52-131">&dagger;Additional polyfills are required (for example, promises can be added via a [`Polyfill.io`](https://polyfill.io/v3/) bundle).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e6c52-132">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="e6c52-132">Additional resources</span></span>

* <xref:blazor/hosting-models>
