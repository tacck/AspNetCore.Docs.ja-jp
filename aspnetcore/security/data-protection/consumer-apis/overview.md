---
title: ASP.NET Core のコンシューマー Api の概要
author: rick-anderson
description: ASP.NET Core データ保護ライブラリ内で使用できるさまざまなコンシューマー Api の簡単な概要を説明します。
ms.author: riande
ms.date: 06/11/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/consumer-apis/overview
ms.openlocfilehash: 0bb163b677062c46a077731c014a40c602764594
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/26/2020
ms.locfileid: "85406720"
---
# <a name="consumer-apis-overview-for-aspnet-core"></a>ASP.NET Core のコンシューマー Api の概要

`IDataProtectionProvider`インターフェイスと `IDataProtector` インターフェイスは、コンシューマーがデータ保護システムを使用するための基本的なインターフェイスです。 これらは、 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Abstractions/)パッケージに格納されています。

## <a name="idataprotectionprovider"></a>IDataProtectionProvider

プロバイダーインターフェイスは、データ保護システムのルートを表します。 データの保護または保護解除に直接使用することはできません。 代わりに、コンシューマーはを呼び出すことによってへの参照を取得する必要があり `IDataProtector` `IDataProtectionProvider.CreateProtector(purpose)` ます。ここで、目的は、目的のコンシューマーユースケースを説明する文字列です。 このパラメーターの目的と適切な値を選択する方法の詳細については、「[目的の文字列](xref:security/data-protection/consumer-apis/purpose-strings)」を参照してください。

## <a name="idataprotector"></a>IDataProtector

プロテクターインターフェイスは、の呼び出しによって返され `CreateProtector` ます。コンシューマーはこのインターフェイスを使用して、保護および保護解除操作を実行できます。

データを保護するには、データをメソッドに渡し `Protect` ます。 基本インターフェイスは byte []-> byte [] を変換するメソッドを定義しますが、文字列 > 文字列を変換するオーバーロード (拡張メソッドとして提供される) もあります。 2つのメソッドによって提供されるセキュリティは同じです。開発者は、ユースケースに最も便利なオーバーロードのいずれかを選択する必要があります。 選択されたオーバーロードに関係なく、Protect メソッドによって返される値は保護されるようになりました (読んだりと校正)。アプリケーションは信頼されていないクライアントに送信できます。

以前に保護されていたデータの一部を保護解除するには、保護されたデータをメソッドに渡し `Unprotect` ます。 (開発者の便宜のために、byte [] ベースのオーバーロードと文字列ベースのオーバーロードがあります)。このに対するの以前の呼び出しによって保護されたペイロードが生成された場合 `Protect` `IDataProtector` 、メソッドは、保護されていない `Unprotect` 元のペイロードを返します。 保護されたペイロードが改ざんされた場合、または別のによって生成された場合、 `IDataProtector` `Unprotect` メソッドは system.security.cryptography.cryptographicexception> をスローします。

同一の概念は、 `IDataProtector` 目的の概念に戻ります。 2つの `IDataProtector` インスタンスが同じルートから生成され、の呼び出しで別の目的の文字列を使用して生成された場合 `IDataProtectionProvider` `IDataProtectionProvider.CreateProtector` 、それらは[異なるプロテクター](xref:security/data-protection/consumer-apis/purpose-strings)と見なされ、もう一方のインスタンスによって生成されたペイロードの保護を解除することはできません。

## <a name="consuming-these-interfaces"></a>これらのインターフェイスの使用

DI 対応のコンポーネントの場合、使用目的は、コンポーネントが `IDataProtectionProvider` コンストラクター内でパラメーターを受け取り、コンポーネントがインスタンス化されるときに DI システムが自動的にこのサービスを提供することです。

> [!NOTE]
> 一部のアプリケーション (コンソールアプリケーションや ASP.NET 4.x アプリケーションなど) が DI に対応していない可能性があるため、ここで説明するメカニズムを使用することはできません。 これらのシナリオでは、di を介さずにプロバイダーのインスタンスを取得する方法の詳細については、 [Di 非対応のシナリオ](xref:security/data-protection/configuration/non-di-scenarios)に関するドキュメントを参照してください `IDataProtection` 。

次の例では、3つの概念を示します。

1. [データ保護システム](xref:security/data-protection/configuration/overview)をサービスコンテナーに追加します。

2. DI を使用して、のインスタンスを受信する `IDataProtectionProvider`

3. からを作成 `IDataProtector` `IDataProtectionProvider` し、それを使用してデータを保護および保護解除します。

[!code-csharp[](../using-data-protection/samples/protectunprotect.cs?highlight=26,34,35,36,37,38,39,40)]

パッケージ AspNetCore には、 `IServiceProvider.GetDataProtector` 開発者の便宜として拡張メソッドが含まれています。 サービスプロバイダーからを取得し、を呼び出すことで、単一の操作としてカプセル化し `IDataProtectionProvider` `IDataProtectionProvider.CreateProtector` ます。 次の例では、その使用方法を示します。

[!code-csharp[](./overview/samples/getdataprotector.cs?highlight=15)]

>[!TIP]
> との `IDataProtectionProvider` インスタンス `IDataProtector` は、複数の呼び出し元に対してスレッドセーフです。 コンポーネントがへの呼び出しによってへの参照を取得すると、その `IDataProtector` `CreateProtector` 参照がとの複数の呼び出しに使用されることを意図して `Protect` `Unprotect` います。 保護された `Unprotect` ペイロードを検証または解読できない場合、の呼び出しは system.security.cryptography.cryptographicexception> をスローします。 コンポーネントによっては、保護解除操作中のエラーを無視することが必要になる場合があります。認証クッキーを読み取るコンポーネントは、このエラーを処理し、要求を完全に失敗させるのではなく、cookie がまったくないかのように要求を処理することがあります。 この動作を必要とするコンポーネントは、すべての例外を飲み込みするのではなく、System.security.cryptography.cryptographicexception> を明示的にキャッチする必要があります。
