---
title: ASP.NET Core の短期データ保護プロバイダー
author: rick-anderson
description: ASP.NET Core 短期データ保護プロバイダーの実装の詳細について説明します。
ms.author: riande
ms.date: 10/14/2016
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/data-protection/implementation/key-storage-ephemeral
ms.openlocfilehash: ed0fca88ecf2b002a4421fb120d90adff1b5b12e
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052700"
---
# <a name="ephemeral-data-protection-providers-in-aspnet-core"></a><span data-ttu-id="2e3c1-103">ASP.NET Core の短期データ保護プロバイダー</span><span class="sxs-lookup"><span data-stu-id="2e3c1-103">Ephemeral data protection providers in ASP.NET Core</span></span>

<a name="data-protection-implementation-key-storage-ephemeral"></a>

<span data-ttu-id="2e3c1-104">アプリケーションに使い捨てが必要なシナリオがあり `IDataProtectionProvider` ます。</span><span class="sxs-lookup"><span data-stu-id="2e3c1-104">There are scenarios where an application needs a throwaway `IDataProtectionProvider`.</span></span> <span data-ttu-id="2e3c1-105">たとえば、開発者が1回限りのコンソールアプリケーションを試している場合や、アプリケーション自体が一時的なものである場合があります (スクリプトまたは単体テストプロジェクト)。</span><span class="sxs-lookup"><span data-stu-id="2e3c1-105">For example, the developer might just be experimenting in a one-off console application, or the application itself is transient (it's scripted or a unit test project).</span></span> <span data-ttu-id="2e3c1-106">これらのシナリオをサポートするために、 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection/) パッケージには型が含まれてい `EphemeralDataProtectionProvider` ます。</span><span class="sxs-lookup"><span data-stu-id="2e3c1-106">To support these scenarios the [Microsoft.AspNetCore.DataProtection](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection/) package includes a type `EphemeralDataProtectionProvider`.</span></span> <span data-ttu-id="2e3c1-107">この型は、 `IDataProtectionProvider` キーリポジトリがメモリ内にのみ保持され、バッキングストアに書き出されないの基本実装を提供します。</span><span class="sxs-lookup"><span data-stu-id="2e3c1-107">This type provides a basic implementation of `IDataProtectionProvider` whose key repository is held solely in-memory and isn't written out to any backing store.</span></span>

<span data-ttu-id="2e3c1-108">の各インスタンス `EphemeralDataProtectionProvider` は、独自の一意のマスターキーを使用します。</span><span class="sxs-lookup"><span data-stu-id="2e3c1-108">Each instance of `EphemeralDataProtectionProvider` uses its own unique master key.</span></span> <span data-ttu-id="2e3c1-109">したがって、をルートとするが、 `IDataProtector` `EphemeralDataProtectionProvider` 保護されたペイロードを生成する場合、同じインスタンスをルートとする同等の `IDataProtector` (同じ [目的](xref:security/data-protection/consumer-apis/purpose-strings#data-protection-consumer-apis-purposes) のチェーンが指定されている) によってのみ、そのペイロードを保護することができ `EphemeralDataProtectionProvider` ます。</span><span class="sxs-lookup"><span data-stu-id="2e3c1-109">Therefore, if an `IDataProtector` rooted at an `EphemeralDataProtectionProvider` generates a protected payload, that payload can only be unprotected by an equivalent `IDataProtector` (given the same [purpose](xref:security/data-protection/consumer-apis/purpose-strings#data-protection-consumer-apis-purposes) chain) rooted at the same `EphemeralDataProtectionProvider` instance.</span></span>

<span data-ttu-id="2e3c1-110">をインスタンス化 `EphemeralDataProtectionProvider` し、それを使用してデータを保護および保護解除する例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="2e3c1-110">The following sample demonstrates instantiating an `EphemeralDataProtectionProvider` and using it to protect and unprotect data.</span></span>

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
