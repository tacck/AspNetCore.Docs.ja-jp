---
title: ASP.NET Core でのデータ保護 Api の概要
author: rick-anderson
description: ASP.NET Core データ保護 Api を使用して、アプリのデータを保護および復号化する方法について説明します。
ms.author: riande
ms.date: 11/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/using-data-protection
ms.openlocfilehash: d5e9e61db39a67e8ccb7b345dfa4c97353312857
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774263"
---
# <a name="get-started-with-the-data-protection-apis-in-aspnet-core"></a>ASP.NET Core でのデータ保護 Api の概要

<a name="security-data-protection-getting-started"></a>

データの保護は、次の手順で最も簡単に行うことができます。

1. データ保護プロバイダーからデータプロテクターを作成します。

2. 保護する`Protect`データを指定してメソッドを呼び出します。

3. プレーンテキスト`Unprotect`に戻すデータを使用して、メソッドを呼び出します。

ASP.NET Core やなど、ほとんどのフレームワークとアプリケーションモデルSignalRでは、データ保護システムが既に構成されており、依存関係の挿入によってアクセスするサービスコンテナーに追加されています。 次のサンプルでは、依存関係の挿入のためのサービスコンテナーの構成、データ保護スタックの登録、DI を使用したデータ保護プロバイダーの受信、保護機能の作成、データの復号化の保護を行う方法を示します。

[!code-csharp[](../../security/data-protection/using-data-protection/samples/protectunprotect.cs?highlight=26,34,35,36,37,38,39,40)]

保護機能を作成するときは、1つまたは複数の[目的の文字列](xref:security/data-protection/consumer-apis/purpose-strings)を指定する必要があります。 目的の文字列は、コンシューマー間の分離を提供します。 たとえば、目的の文字列が "green" で作成されたプロテクターは、"紫" の目的で保護機能によって提供されるデータの保護を解除することはできません。

>[!TIP]
> との`IDataProtectionProvider`インスタンス`IDataProtector`は、複数の呼び出し元に対してスレッドセーフです。 コンポーネント`IDataProtector`がへ`CreateProtector`の呼び出しによってへの参照を取得する`Protect`と、その参照がと`Unprotect`の複数の呼び出しに使用されることを意図しています。
>
>保護され`Unprotect`たペイロードを検証または解読できない場合、の呼び出しは system.security.cryptography.cryptographicexception> をスローします。 コンポーネントによっては、保護解除操作中のエラーを無視することが必要になる場合があります。認証クッキーを読み取るコンポーネントは、このエラーを処理し、要求を完全に失敗させるのではなく、cookie がまったくないかのように要求を処理することがあります。 この動作を必要とするコンポーネントは、すべての例外を飲み込みするのではなく、System.security.cryptography.cryptographicexception> を明示的にキャッチする必要があります。
