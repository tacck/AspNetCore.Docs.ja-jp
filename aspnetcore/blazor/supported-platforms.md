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
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/supported-platforms
ms.openlocfilehash: 99f14486711c2dd2a634bc51b27a8e3891deee1a
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2020
ms.locfileid: "85243227"
---
# <a name="aspnet-core-blazor-supported-platforms"></a>ASP.NET Core Blazor でサポートされているプラットフォーム

作成者: [Luke Latham](https://github.com/guardrex)

## <a name="browser-requirements"></a>ブラウザー要件

### <a name="blazor-webassembly"></a>Blazor WebAssembly

| ブラウザー                          | バージョン               |
| -------------------------------- | :-------------------: |
| Microsoft Edge                   | [現在]               |
| Mozilla Firefox                  | [現在]               |
| Google Chrome (Android を含む) | [現在]               |
| Safari (iOS を含む)            | [現在]               |
| Microsoft Internet Explorer      | サポートされていません&dagger; |

&dagger;Microsoft Internet Explorer は [WebAssembly](https://webassembly.org) をサポートしていません。

### <a name="blazor-server"></a>Blazor サーバー

| ブラウザー                          | バージョン    |
| -------------------------------- | :--------: |
| Microsoft Edge                   | [現在]    |
| Mozilla Firefox                  | [現在]    |
| Google Chrome (Android を含む) | [現在]    |
| Safari (iOS を含む)            | [現在]    |
| Microsoft Internet Explorer      | 11&dagger; |

&dagger;追加のポリフィルが必要です (たとえば、[`Polyfill.io`](https://polyfill.io/v3/) バンドルによって Promise を追加できます)。

## <a name="additional-resources"></a>その他の技術情報

* <xref:blazor/hosting-models>
