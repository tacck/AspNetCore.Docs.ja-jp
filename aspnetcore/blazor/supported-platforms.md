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
# <a name="aspnet-core-no-locblazor-supported-platforms"></a>ASP.NET Core Blazor でサポートされているプラットフォーム

作成者: [Luke Latham](https://github.com/guardrex)

::: moniker range=">= aspnetcore-5.0"

Blazor WebAssembly と Blazor Server は、次の表に示すブラウザーでサポートされています。

| ブラウザー                          | バージョン         |
| -------------------------------- | --------------- |
| Apple Safari (iOS を含む)      | [現在]&dagger; |
| Google Chrome (Android を含む) | [現在]&dagger; |
| Microsoft Edge                   | [現在]&dagger; |
| Mozilla Firefox                  | [現在]&dagger; |  

&dagger; *[現在]* は、ブラウザーの最新バージョンを示します。  

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## Blazor WebAssembly

| ブラウザー                          | バージョン               |
| -------------------------------- | --------------------- |
| Apple Safari (iOS を含む)      | [現在]&dagger;       |
| Google Chrome (Android を含む) | [現在]&dagger;       |
| Microsoft Edge                   | [現在]&dagger;       |
| Microsoft Internet Explorer      | サポートされていません&Dagger; |
| Mozilla Firefox                  | [現在]&dagger;       |  

&dagger; *[現在]* は、ブラウザーの最新バージョンを示します。  
&Dagger;Microsoft Internet Explorer は [WebAssembly](https://webassembly.org) をサポートしていません。

## Blazor Server

| ブラウザー                          | バージョン         |
| -------------------------------- | --------------- |
| Apple Safari (iOS を含む)      | [現在]&dagger; |
| Google Chrome (Android を含む) | [現在]&dagger; |
| Microsoft Edge                   | [現在]&dagger; |
| Microsoft Internet Explorer      | 11&Dagger;      |
| Mozilla Firefox                  | [現在]&dagger; |

&dagger; *[現在]* は、ブラウザーの最新バージョンを示します。  
&Dagger;追加のポリフィルが必要です。 たとえば、[`Polyfill.io`](https://polyfill.io/v3/) バンドルによって Promise を追加できます

::: moniker-end

## <a name="additional-resources"></a>その他の技術情報

* <xref:blazor/hosting-models>
* <xref:signalr/supported-platforms>
