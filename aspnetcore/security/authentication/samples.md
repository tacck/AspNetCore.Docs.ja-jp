---
title: ASP.NET Core の認証サンプル
author: rick-anderson
description: ASP.NET Core リポジトリの認証サンプルへのリンクを提供します。
ms.author: riande
ms.date: 01/31/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/samples
ms.openlocfilehash: 3e5e487adafc09d38400ea58936c5c2e8385e84f
ms.sourcegitcommit: 5a36758cca2861aeb10840093e46d273a6e6e91d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87303600"
---
# <a name="authentication-samples-for-aspnet-core"></a>ASP.NET Core の認証サンプル

作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

[ASP.NET Core リポジトリ](https://github.com/dotnet/AspNetCore)には、 *AspNetCore/src/Security/samples*フォルダーに次の認証サンプルが含まれています。

* [要求の変換](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/ClaimsTransformation)
* [Cookie 認証](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Cookies)
* [カスタムポリシープロバイダー-IAuthorizationPolicyProvider](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/CustomPolicyProvider)
* [動的な認証スキームとオプション](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/DynamicSchemes)
* [外部要求](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Identity.ExternalClaims)
* [要求に基づいて cookie と別の認証スキームのどちらを選択するか](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/PathSchemeSelection)
* [静的ファイルへのアクセスを制限する](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a>サンプルを実行する

* [ブランチ](https://github.com/dotnet/AspNetCore)を選択してください。 たとえば、`release/3.1` のように指定します。
* [ASP.NET Core リポジトリ](https://github.com/dotnet/AspNetCore)を複製またはダウンロードします。
* ASP.NET Core リポジトリの複製に一致する[.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core)バージョンがインストールされていることを確認します。
* *AspNetCore/src/Security/samples*のサンプルに移動し、でサンプルを実行し `dotnet run` ます。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[ASP.NET Core リポジトリ](https://github.com/dotnet/AspNetCore)には、 *AspNetCore/src/Security/samples*フォルダーに次の認証サンプルが含まれています。

* [要求の変換](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/ClaimsTransformation)
* [Cookie 認証](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/Cookies)
* [カスタムポリシープロバイダー-IAuthorizationPolicyProvider](https://github.com/dotnet/AspNetCore/tree/2.1.3/src/Security/samples/CustomPolicyProvider)
* [動的な認証スキームとオプション](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/DynamicSchemes)
* [外部要求](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/Identity.ExternalClaims)
* [要求に基づいて cookie と別の認証スキームのどちらを選択するか](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/PathSchemeSelection)
* [静的ファイルへのアクセスを制限する](https://github.com/dotnet/AspNetCore/tree/2.1.3/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a>サンプルを実行する

* [ブランチ](https://github.com/dotnet/AspNetCore)を選択してください。 たとえば、`release/2.1` のように指定します。
* [ASP.NET Core リポジトリ](https://github.com/dotnet/AspNetCore)を複製またはダウンロードします。
* ASP.NET Core リポジトリの複製に一致する[.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core)バージョンがインストールされていることを確認します。
* *AspNetCore/src/Security/samples*のサンプルに移動し、でサンプルを実行し `dotnet run` ます。

::: moniker-end
