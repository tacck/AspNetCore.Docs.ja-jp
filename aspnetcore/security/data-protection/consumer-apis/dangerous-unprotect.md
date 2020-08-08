---
title: キーが取り消されたペイロードの保護解除 ASP.NET Core
author: rick-anderson
description: ASP.NET Core アプリで、失効した後のキーで保護されたデータの保護を解除する方法について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/consumer-apis/dangerous-unprotect
ms.openlocfilehash: 29bd9010bc9f2d9799d079e44e7b3faa359699b2
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2020
ms.locfileid: "88019717"
---
# <a name="unprotect-payloads-whose-keys-have-been-revoked-in-aspnet-core"></a><span data-ttu-id="5ad8b-103">キーが取り消されたペイロードの保護解除 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5ad8b-103">Unprotect payloads whose keys have been revoked in ASP.NET Core</span></span>

<a name="data-protection-consumer-apis-dangerous-unprotect"></a>

<span data-ttu-id="5ad8b-104">ASP.NET Core データ保護 Api は、主に機密ペイロードの永続的な永続化のためのものではありません。</span><span class="sxs-lookup"><span data-stu-id="5ad8b-104">The ASP.NET Core data protection APIs are not primarily intended for indefinite persistence of confidential payloads.</span></span> <span data-ttu-id="5ad8b-105">[WINDOWS CNG DPAPI](/windows/win32/seccng/cng-dpapi)や[Azure Rights Management](/rights-management/)などのその他のテクノロジは、無期限のストレージのシナリオに適しています。また、強力なキー管理機能も備えています。</span><span class="sxs-lookup"><span data-stu-id="5ad8b-105">Other technologies like [Windows CNG DPAPI](/windows/win32/seccng/cng-dpapi) and [Azure Rights Management](/rights-management/) are more suited to the scenario of indefinite storage, and they have correspondingly strong key management capabilities.</span></span> <span data-ttu-id="5ad8b-106">ただし、社外秘データの長期的な保護には、ASP.NET Core データ保護 Api を使用した開発者の禁止はありません。</span><span class="sxs-lookup"><span data-stu-id="5ad8b-106">That said, there's nothing prohibiting a developer from using the ASP.NET Core data protection APIs for long-term protection of confidential data.</span></span> <span data-ttu-id="5ad8b-107">キーがキーリングから削除されることはありません。そのため、キーが `IDataProtector.Unprotect` 使用可能で有効である限り、常に既存のペイロードを回復できます。</span><span class="sxs-lookup"><span data-stu-id="5ad8b-107">Keys are never removed from the key ring, so `IDataProtector.Unprotect` can always recover existing payloads as long as the keys are available and valid.</span></span>

<span data-ttu-id="5ad8b-108">ただし、 `IDataProtector.Unprotect` この場合は例外がスローされるため、開発者が取り消されたキーで保護されたデータの保護を解除しようとすると、問題が発生します。</span><span class="sxs-lookup"><span data-stu-id="5ad8b-108">However, an issue arises when the developer tries to unprotect data that has been protected with a revoked key, as `IDataProtector.Unprotect` will throw an exception in this case.</span></span> <span data-ttu-id="5ad8b-109">これは、短期間または一時的なペイロード (認証トークンなど) では問題になる可能性があります。これらの種類のペイロードはシステムによって簡単に再作成でき、少なくともサイトビジターが再度ログインする必要があるためです。</span><span class="sxs-lookup"><span data-stu-id="5ad8b-109">This might be fine for short-lived or transient payloads (like authentication tokens), as these kinds of payloads can easily be recreated by the system, and at worst the site visitor might be required to log in again.</span></span> <span data-ttu-id="5ad8b-110">しかし、永続化されたペイロードの場合、throw を使用すると、 `Unprotect` 許容できないデータ損失につながる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="5ad8b-110">But for persisted payloads, having `Unprotect` throw could lead to unacceptable data loss.</span></span>

## <a name="ipersisteddataprotector"></a><span data-ttu-id="5ad8b-111">IPersistedDataProtector</span><span class="sxs-lookup"><span data-stu-id="5ad8b-111">IPersistedDataProtector</span></span>

<span data-ttu-id="5ad8b-112">失効したキーがある場合でもペイロードを保護できないようにするためのシナリオをサポートするために、データ保護システムには型が含まれてい `IPersistedDataProtector` ます。</span><span class="sxs-lookup"><span data-stu-id="5ad8b-112">To support the scenario of allowing payloads to be unprotected even in the face of revoked keys, the data protection system contains an `IPersistedDataProtector` type.</span></span> <span data-ttu-id="5ad8b-113">のインスタンスを取得するには `IPersistedDataProtector` 、通常の方法でのインスタンスを取得 `IDataProtector` し、 `IDataProtector` をにキャストし `IPersistedDataProtector` ます。</span><span class="sxs-lookup"><span data-stu-id="5ad8b-113">To get an instance of `IPersistedDataProtector`, simply get an instance of `IDataProtector` in the normal fashion and try casting the `IDataProtector` to `IPersistedDataProtector`.</span></span>

> [!NOTE]
> <span data-ttu-id="5ad8b-114">すべての `IDataProtector` インスタンスをにキャストできるわけではありません `IPersistedDataProtector` 。</span><span class="sxs-lookup"><span data-stu-id="5ad8b-114">Not all `IDataProtector` instances can be cast to `IPersistedDataProtector`.</span></span> <span data-ttu-id="5ad8b-115">開発者は、C# を使用して、無効なキャストによって発生するランタイム例外を回避する必要があります。また、エラーケースを適切に処理できるように準備する必要があります。</span><span class="sxs-lookup"><span data-stu-id="5ad8b-115">Developers should use the C# as operator or similar to avoid runtime exceptions caused by invalid casts, and they should be prepared to handle the failure case appropriately.</span></span>

<span data-ttu-id="5ad8b-116">`IPersistedDataProtector`では、次の API サーフェイスが公開されます。</span><span class="sxs-lookup"><span data-stu-id="5ad8b-116">`IPersistedDataProtector` exposes the following API surface:</span></span>

```csharp
DangerousUnprotect(byte[] protectedData, bool ignoreRevocationErrors,
     out bool requiresMigration, out bool wasRevoked) : byte[]
```

<span data-ttu-id="5ad8b-117">この API は、(バイト配列として) 保護されたペイロードを取得し、保護されていないペイロードを返します。</span><span class="sxs-lookup"><span data-stu-id="5ad8b-117">This API takes the protected payload (as a byte array) and returns the unprotected payload.</span></span> <span data-ttu-id="5ad8b-118">文字列ベースのオーバーロードはありません。</span><span class="sxs-lookup"><span data-stu-id="5ad8b-118">There's no string-based overload.</span></span> <span data-ttu-id="5ad8b-119">2つの出力パラメーターは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="5ad8b-119">The two out parameters are as follows.</span></span>

* <span data-ttu-id="5ad8b-120">`requiresMigration`: このペイロードを保護するために使用されるキーがアクティブな既定のキーではなくなった場合 (たとえば、このペイロードを保護するために使用されたキーが古く、キーのローリング操作が行われた場合) は、true に設定されます。</span><span class="sxs-lookup"><span data-stu-id="5ad8b-120">`requiresMigration`: will be set to true if the key used to protect this payload is no longer the active default key, e.g., the key used to protect this payload is old and a key rolling operation has since taken place.</span></span> <span data-ttu-id="5ad8b-121">呼び出し元は、ビジネスニーズに応じてペイロードを再保護することを検討できます。</span><span class="sxs-lookup"><span data-stu-id="5ad8b-121">The caller may wish to consider reprotecting the payload depending on their business needs.</span></span>

* <span data-ttu-id="5ad8b-122">`wasRevoked`: このペイロードを保護するために使用されたキーが失効した場合、は true に設定されます。</span><span class="sxs-lookup"><span data-stu-id="5ad8b-122">`wasRevoked`: will be set to true if the key used to protect this payload was revoked.</span></span>

>[!WARNING]
> <span data-ttu-id="5ad8b-123">メソッドに渡すときは、細心の注意 `ignoreRevocationErrors: true` を払って `DangerousUnprotect` ください。</span><span class="sxs-lookup"><span data-stu-id="5ad8b-123">Exercise extreme caution when passing `ignoreRevocationErrors: true` to the `DangerousUnprotect` method.</span></span> <span data-ttu-id="5ad8b-124">このメソッドを呼び出した後に `wasRevoked` 値が true の場合、このペイロードの保護に使用されるキーは取り消され、ペイロードの信頼性は問題ありとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="5ad8b-124">If after calling this method the `wasRevoked` value is true, then the key used to protect this payload was revoked, and the payload's authenticity should be treated as suspect.</span></span> <span data-ttu-id="5ad8b-125">この場合、信頼されていない web クライアントによって送信されるのではなく、セキュリティで保護されたデータベースからのものであることを保証している場合は、保護されていないペイロードでの操作を続行します。</span><span class="sxs-lookup"><span data-stu-id="5ad8b-125">In this case, only continue operating on the unprotected payload if you have some separate assurance that it's authentic, e.g. that it's coming from a secure database rather than being sent by an untrusted web client.</span></span>

[!code-csharp[](dangerous-unprotect/samples/dangerous-unprotect.cs)]
