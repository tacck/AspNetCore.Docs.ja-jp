---
title: キーが取り消されたペイロードの保護解除 ASP.NET Core
author: rick-anderson
description: ASP.NET Core アプリで、失効した後のキーで保護されたデータの保護を解除する方法について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/consumer-apis/dangerous-unprotect
ms.openlocfilehash: a0b5bb29c509e8cc999b998776da3ab4ec27ec29
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408397"
---
# <a name="unprotect-payloads-whose-keys-have-been-revoked-in-aspnet-core"></a>キーが取り消されたペイロードの保護解除 ASP.NET Core

<a name="data-protection-consumer-apis-dangerous-unprotect"></a>

ASP.NET Core データ保護 Api は、主に機密ペイロードの永続的な永続化のためのものではありません。 [WINDOWS CNG DPAPI](https://msdn.microsoft.com/library/windows/desktop/hh706794%28v=vs.85%29.aspx)や[Azure Rights Management](/rights-management/)などのその他のテクノロジは、無期限のストレージのシナリオに適しています。また、強力なキー管理機能も備えています。 ただし、社外秘データの長期的な保護には、ASP.NET Core データ保護 Api を使用した開発者の禁止はありません。 キーがキーリングから削除されることはありません。そのため、キーが `IDataProtector.Unprotect` 使用可能で有効である限り、常に既存のペイロードを回復できます。

ただし、 `IDataProtector.Unprotect` この場合は例外がスローされるため、開発者が取り消されたキーで保護されたデータの保護を解除しようとすると、問題が発生します。 これは、短期間または一時的なペイロード (認証トークンなど) では問題になる可能性があります。これらの種類のペイロードはシステムによって簡単に再作成でき、少なくともサイトビジターが再度ログインする必要があるためです。 しかし、永続化されたペイロードの場合、throw を使用すると、 `Unprotect` 許容できないデータ損失につながる可能性があります。

## <a name="ipersisteddataprotector"></a>IPersistedDataProtector

失効したキーがある場合でもペイロードを保護できないようにするためのシナリオをサポートするために、データ保護システムには型が含まれてい `IPersistedDataProtector` ます。 のインスタンスを取得するには `IPersistedDataProtector` 、通常の方法でのインスタンスを取得 `IDataProtector` し、 `IDataProtector` をにキャストし `IPersistedDataProtector` ます。

> [!NOTE]
> すべての `IDataProtector` インスタンスをにキャストできるわけではありません `IPersistedDataProtector` 。 開発者は、C# を使用して、無効なキャストによって発生するランタイム例外を回避する必要があります。また、エラーケースを適切に処理できるように準備する必要があります。

`IPersistedDataProtector`では、次の API サーフェイスが公開されます。

```csharp
DangerousUnprotect(byte[] protectedData, bool ignoreRevocationErrors,
     out bool requiresMigration, out bool wasRevoked) : byte[]
```

この API は、(バイト配列として) 保護されたペイロードを取得し、保護されていないペイロードを返します。 文字列ベースのオーバーロードはありません。 2つの出力パラメーターは次のとおりです。

* `requiresMigration`: このペイロードを保護するために使用されるキーがアクティブな既定のキーではなくなった場合 (たとえば、このペイロードを保護するために使用されたキーが古く、キーのローリング操作が行われた場合) は、true に設定されます。 呼び出し元は、ビジネスニーズに応じてペイロードを再保護することを検討できます。

* `wasRevoked`: このペイロードを保護するために使用されたキーが失効した場合、は true に設定されます。

>[!WARNING]
> メソッドに渡すときは、細心の注意 `ignoreRevocationErrors: true` を払って `DangerousUnprotect` ください。 このメソッドを呼び出した後に `wasRevoked` 値が true の場合、このペイロードの保護に使用されるキーは取り消され、ペイロードの信頼性は問題ありとして扱われます。 この場合、信頼されていない web クライアントによって送信されるのではなく、セキュリティで保護されたデータベースからのものであることを保証している場合は、保護されていないペイロードでの操作を続行します。

[!code-csharp[](dangerous-unprotect/samples/dangerous-unprotect.cs)]
