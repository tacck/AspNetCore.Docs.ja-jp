---
title: ASP.NET Core の短期データ保護プロバイダー
author: rick-anderson
description: ASP.NET Core 短期データ保護プロバイダーの実装の詳細について説明します。
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/implementation/key-storage-ephemeral
ms.openlocfilehash: baec19ef0c0b1e2bf5c176bf1b3c2245de0d3dd0
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408917"
---
# <a name="ephemeral-data-protection-providers-in-aspnet-core"></a>ASP.NET Core の短期データ保護プロバイダー

<a name="data-protection-implementation-key-storage-ephemeral"></a>

アプリケーションに使い捨てが必要なシナリオがあり `IDataProtectionProvider` ます。 たとえば、開発者が1回限りのコンソールアプリケーションを試している場合や、アプリケーション自体が一時的なものである場合があります (スクリプトまたは単体テストプロジェクト)。 これらのシナリオをサポートするために、 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection/)パッケージには型が含まれてい `EphemeralDataProtectionProvider` ます。 この型は、 `IDataProtectionProvider` キーリポジトリがメモリ内にのみ保持され、バッキングストアに書き出されないの基本実装を提供します。

の各インスタンス `EphemeralDataProtectionProvider` は、独自の一意のマスターキーを使用します。 したがって、をルートとするが、 `IDataProtector` `EphemeralDataProtectionProvider` 保護されたペイロードを生成する場合、同じインスタンスをルートとする同等の `IDataProtector` (同じ[目的](xref:security/data-protection/consumer-apis/purpose-strings#data-protection-consumer-apis-purposes)のチェーンが指定されている) によってのみ、そのペイロードを保護することができ `EphemeralDataProtectionProvider` ます。

をインスタンス化 `EphemeralDataProtectionProvider` し、それを使用してデータを保護および保護解除する例を次に示します。

```csharp
using System;
using Microsoft.AspNetCore.DataProtection;

public class Program
{
    public static void Main(string[] args)
    {
        const string purpose = "Ephemeral.App.v1";

        // create an ephemeral provider and demonstrate that it can round-trip a payload
        var provider = new EphemeralDataProtectionProvider();
        var protector = provider.CreateProtector(purpose);
        Console.Write("Enter input: ");
        string input = Console.ReadLine();

        // protect the payload
        string protectedPayload = protector.Protect(input);
        Console.WriteLine($"Protect returned: {protectedPayload}");

        // unprotect the payload
        string unprotectedPayload = protector.Unprotect(protectedPayload);
        Console.WriteLine($"Unprotect returned: {unprotectedPayload}");

        // if I create a new ephemeral provider, it won't be able to unprotect existing
        // payloads, even if I specify the same purpose
        provider = new EphemeralDataProtectionProvider();
        protector = provider.CreateProtector(purpose);
        unprotectedPayload = protector.Unprotect(protectedPayload); // THROWS
    }
}

/*
* SAMPLE OUTPUT
*
* Enter input: Hello!
* Protect returned: CfDJ8AAAAAAAAAAAAAAAAAAAAA...uGoxWLjGKtm1SkNACQ
* Unprotect returned: Hello!
* << throws CryptographicException >>
*/
```
