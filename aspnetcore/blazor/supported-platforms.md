---
title: ASP.NET Core Blazor でサポートされているプラットフォーム
author: guardrex
description: ASP.NET Core Blazor でサポートされているプラットフォームについて学習します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/01/2020
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
ms.openlocfilehash: 1ffe98636ed200adbf00e89c2c3499eb69792d3f
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/06/2020
ms.locfileid: "91754542"
---
# <a name="aspnet-core-no-locblazor-supported-platforms"></a><span data-ttu-id="4566e-103">ASP.NET Core Blazor でサポートされているプラットフォーム</span><span class="sxs-lookup"><span data-stu-id="4566e-103">ASP.NET Core Blazor supported platforms</span></span>

<span data-ttu-id="4566e-104">作成者: [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="4566e-104">By [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="4566e-105">Blazor WebAssembly と Blazor Server は、次の表に示すブラウザーでサポートされています。</span><span class="sxs-lookup"><span data-stu-id="4566e-105">Blazor WebAssembly and Blazor Server are supported in the browsers shown in the following table.</span></span>

| <span data-ttu-id="4566e-106">ブラウザー</span><span class="sxs-lookup"><span data-stu-id="4566e-106">Browser</span></span>                          | <span data-ttu-id="4566e-107">バージョン</span><span class="sxs-lookup"><span data-stu-id="4566e-107">Version</span></span>         |
| -------------------------------- | --------------- |
| <span data-ttu-id="4566e-108">Apple Safari (iOS を含む)</span><span class="sxs-lookup"><span data-stu-id="4566e-108">Apple Safari, including iOS</span></span>      | <span data-ttu-id="4566e-109">[現在]&dagger;</span><span class="sxs-lookup"><span data-stu-id="4566e-109">Current&dagger;</span></span> |
| <span data-ttu-id="4566e-110">Google Chrome (Android を含む)</span><span class="sxs-lookup"><span data-stu-id="4566e-110">Google Chrome, including Android</span></span> | <span data-ttu-id="4566e-111">[現在]&dagger;</span><span class="sxs-lookup"><span data-stu-id="4566e-111">Current&dagger;</span></span> |
| <span data-ttu-id="4566e-112">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="4566e-112">Microsoft Edge</span></span>                   | <span data-ttu-id="4566e-113">[現在]&dagger;</span><span class="sxs-lookup"><span data-stu-id="4566e-113">Current&dagger;</span></span> |
| <span data-ttu-id="4566e-114">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="4566e-114">Mozilla Firefox</span></span>                  | <span data-ttu-id="4566e-115">[現在]&dagger;</span><span class="sxs-lookup"><span data-stu-id="4566e-115">Current&dagger;</span></span> |  

<span data-ttu-id="4566e-116">&dagger; *[現在]* は、ブラウザーの最新バージョンを示します。</span><span class="sxs-lookup"><span data-stu-id="4566e-116">&dagger;*Current* refers to the latest version of the browser.</span></span>  

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## Blazor WebAssembly

| <span data-ttu-id="4566e-117">ブラウザー</span><span class="sxs-lookup"><span data-stu-id="4566e-117">Browser</span></span>                          | <span data-ttu-id="4566e-118">バージョン</span><span class="sxs-lookup"><span data-stu-id="4566e-118">Version</span></span>               |
| -------------------------------- | --------------------- |
| <span data-ttu-id="4566e-119">Apple Safari (iOS を含む)</span><span class="sxs-lookup"><span data-stu-id="4566e-119">Apple Safari, including iOS</span></span>      | <span data-ttu-id="4566e-120">[現在]&dagger;</span><span class="sxs-lookup"><span data-stu-id="4566e-120">Current&dagger;</span></span>       |
| <span data-ttu-id="4566e-121">Google Chrome (Android を含む)</span><span class="sxs-lookup"><span data-stu-id="4566e-121">Google Chrome, including Android</span></span> | <span data-ttu-id="4566e-122">[現在]&dagger;</span><span class="sxs-lookup"><span data-stu-id="4566e-122">Current&dagger;</span></span>       |
| <span data-ttu-id="4566e-123">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="4566e-123">Microsoft Edge</span></span>                   | <span data-ttu-id="4566e-124">[現在]&dagger;</span><span class="sxs-lookup"><span data-stu-id="4566e-124">Current&dagger;</span></span>       |
| <span data-ttu-id="4566e-125">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="4566e-125">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="4566e-126">サポートされていません&Dagger;</span><span class="sxs-lookup"><span data-stu-id="4566e-126">Not Supported&Dagger;</span></span> |
| <span data-ttu-id="4566e-127">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="4566e-127">Mozilla Firefox</span></span>                  | <span data-ttu-id="4566e-128">[現在]&dagger;</span><span class="sxs-lookup"><span data-stu-id="4566e-128">Current&dagger;</span></span>       |  

<span data-ttu-id="4566e-129">&dagger; *[現在]* は、ブラウザーの最新バージョンを示します。</span><span class="sxs-lookup"><span data-stu-id="4566e-129">&dagger;*Current* refers to the latest version of the browser.</span></span>  
<span data-ttu-id="4566e-130">&Dagger;Microsoft Internet Explorer は [WebAssembly](https://webassembly.org) をサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="4566e-130">&Dagger;Microsoft Internet Explorer doesn't support [WebAssembly](https://webassembly.org).</span></span>

## Blazor Server

| <span data-ttu-id="4566e-131">ブラウザー</span><span class="sxs-lookup"><span data-stu-id="4566e-131">Browser</span></span>                          | <span data-ttu-id="4566e-132">バージョン</span><span class="sxs-lookup"><span data-stu-id="4566e-132">Version</span></span>         |
| -------------------------------- | --------------- |
| <span data-ttu-id="4566e-133">Apple Safari (iOS を含む)</span><span class="sxs-lookup"><span data-stu-id="4566e-133">Apple Safari, including iOS</span></span>      | <span data-ttu-id="4566e-134">[現在]&dagger;</span><span class="sxs-lookup"><span data-stu-id="4566e-134">Current&dagger;</span></span> |
| <span data-ttu-id="4566e-135">Google Chrome (Android を含む)</span><span class="sxs-lookup"><span data-stu-id="4566e-135">Google Chrome, including Android</span></span> | <span data-ttu-id="4566e-136">[現在]&dagger;</span><span class="sxs-lookup"><span data-stu-id="4566e-136">Current&dagger;</span></span> |
| <span data-ttu-id="4566e-137">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="4566e-137">Microsoft Edge</span></span>                   | <span data-ttu-id="4566e-138">[現在]&dagger;</span><span class="sxs-lookup"><span data-stu-id="4566e-138">Current&dagger;</span></span> |
| <span data-ttu-id="4566e-139">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="4566e-139">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="4566e-140">11&Dagger;</span><span class="sxs-lookup"><span data-stu-id="4566e-140">11&Dagger;</span></span>      |
| <span data-ttu-id="4566e-141">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="4566e-141">Mozilla Firefox</span></span>                  | <span data-ttu-id="4566e-142">[現在]&dagger;</span><span class="sxs-lookup"><span data-stu-id="4566e-142">Current&dagger;</span></span> |

<span data-ttu-id="4566e-143">&dagger; *[現在]* は、ブラウザーの最新バージョンを示します。</span><span class="sxs-lookup"><span data-stu-id="4566e-143">&dagger;*Current* refers to the latest version of the browser.</span></span>  
<span data-ttu-id="4566e-144">&Dagger;追加のポリフィルが必要です。</span><span class="sxs-lookup"><span data-stu-id="4566e-144">&Dagger;Additional polyfills are required.</span></span> <span data-ttu-id="4566e-145">たとえば、[`Polyfill.io`](https://polyfill.io/v3/) バンドルによって Promise を追加できます</span><span class="sxs-lookup"><span data-stu-id="4566e-145">For example, promises can be added via a [`Polyfill.io`](https://polyfill.io/v3/) bundle.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="4566e-146">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="4566e-146">Additional resources</span></span>

* <xref:blazor/hosting-models>
* <xref:signalr/supported-platforms>
