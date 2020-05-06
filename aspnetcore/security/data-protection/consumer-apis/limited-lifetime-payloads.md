---
title: 保護されたペイロードの有効期間を制限する ASP.NET Core
author: rick-anderson
description: ASP.NET Core データ保護 Api を使用して、保護されたペイロードの有効期間を制限する方法について説明します。
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/consumer-apis/limited-lifetime-payloads
ms.openlocfilehash: bc1597f75d8c5f786d46e59ac027d01ffca077c0
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82768612"
---
# <a name="limit-the-lifetime-of-protected-payloads-in-aspnet-core"></a><span data-ttu-id="b7506-103">保護されたペイロードの有効期間を制限する ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b7506-103">Limit the lifetime of protected payloads in ASP.NET Core</span></span>

<span data-ttu-id="b7506-104">アプリケーション開発者が、一定の時間が経過すると期限切れになる保護されたペイロードを作成しようとするシナリオがあります。</span><span class="sxs-lookup"><span data-stu-id="b7506-104">There are scenarios where the application developer wants to create a protected payload that expires after a set period of time.</span></span> <span data-ttu-id="b7506-105">たとえば、保護されたペイロードは、1時間だけ有効なパスワードリセットトークンを表す場合があります。</span><span class="sxs-lookup"><span data-stu-id="b7506-105">For instance, the protected payload might represent a password reset token that should only be valid for one hour.</span></span> <span data-ttu-id="b7506-106">開発者は、埋め込み有効期限を含む独自のペイロード形式を作成できますが、上級開発者はこれを行うことをお勧めしますが、このような有効期限を管理する開発者の大部分は煩雑になります。</span><span class="sxs-lookup"><span data-stu-id="b7506-106">It's certainly possible for the developer to create their own payload format that contains an embedded expiration date, and advanced developers may wish to do this anyway, but for the majority of developers managing these expirations can grow tedious.</span></span>

<span data-ttu-id="b7506-107">これを開発者にとって簡単にするために、パッケージ[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/)には、設定した時間が経過すると自動的に期限切れになるペイロードを作成するためのユーティリティ api が含まれています。</span><span class="sxs-lookup"><span data-stu-id="b7506-107">To make this easier for our developer audience, the package [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) contains utility APIs for creating payloads that automatically expire after a set period of time.</span></span> <span data-ttu-id="b7506-108">これらの Api は、 `ITimeLimitedDataProtector`型から切断されます。</span><span class="sxs-lookup"><span data-stu-id="b7506-108">These APIs hang off of the `ITimeLimitedDataProtector` type.</span></span>

## <a name="api-usage"></a><span data-ttu-id="b7506-109">API の使用</span><span class="sxs-lookup"><span data-stu-id="b7506-109">API usage</span></span>

<span data-ttu-id="b7506-110">`ITimeLimitedDataProtector`インターフェイスは、時間制限のある、または有効期限が切れたペイロードを保護および復号化するためのコアインターフェイスです。</span><span class="sxs-lookup"><span data-stu-id="b7506-110">The `ITimeLimitedDataProtector` interface is the core interface for protecting and unprotecting time-limited / self-expiring payloads.</span></span> <span data-ttu-id="b7506-111">のインスタンスを作成するに`ITimeLimitedDataProtector`は、まず、特定の目的で構築された通常の[IDataProtector](xref:security/data-protection/consumer-apis/overview)のインスタンスが必要です。</span><span class="sxs-lookup"><span data-stu-id="b7506-111">To create an instance of an `ITimeLimitedDataProtector`, you'll first need an instance of a regular [IDataProtector](xref:security/data-protection/consumer-apis/overview) constructed with a specific purpose.</span></span> <span data-ttu-id="b7506-112">`IDataProtector`インスタンスが使用可能になったら、 `IDataProtector.ToTimeLimitedDataProtector`拡張メソッドを呼び出して、組み込みの有効期限があるプロテクターを取得します。</span><span class="sxs-lookup"><span data-stu-id="b7506-112">Once the `IDataProtector` instance is available, call the `IDataProtector.ToTimeLimitedDataProtector` extension method to get back a protector with built-in expiration capabilities.</span></span>

<span data-ttu-id="b7506-113">`ITimeLimitedDataProtector`は、次の API サーフェスおよび拡張メソッドを公開します。</span><span class="sxs-lookup"><span data-stu-id="b7506-113">`ITimeLimitedDataProtector` exposes the following API surface and extension methods:</span></span>

* <span data-ttu-id="b7506-114">CreateProtector (文字列の目的): ITimeLimitedDataProtector-この API は、ルートの`IDataProtectionProvider.CreateProtector`時間限定プロテクターから[目的のチェーン](xref:security/data-protection/consumer-apis/purpose-strings)を作成するために使用できるという点で、既存のと似ています。</span><span class="sxs-lookup"><span data-stu-id="b7506-114">CreateProtector(string purpose) : ITimeLimitedDataProtector - This API is similar to the existing `IDataProtectionProvider.CreateProtector` in that it can be used to create [purpose chains](xref:security/data-protection/consumer-apis/purpose-strings) from a root time-limited protector.</span></span>

* <span data-ttu-id="b7506-115">Protect (byte [] plaintext, DateTimeOffset 有効期限): byte []</span><span class="sxs-lookup"><span data-stu-id="b7506-115">Protect(byte[] plaintext, DateTimeOffset expiration) : byte[]</span></span>

* <span data-ttu-id="b7506-116">Protect (byte [] プレーンテキスト、TimeSpan lifetime): byte []</span><span class="sxs-lookup"><span data-stu-id="b7506-116">Protect(byte[] plaintext, TimeSpan lifetime) : byte[]</span></span>

* <span data-ttu-id="b7506-117">Protect (byte [] plaintext): byte []</span><span class="sxs-lookup"><span data-stu-id="b7506-117">Protect(byte[] plaintext) : byte[]</span></span>

* <span data-ttu-id="b7506-118">Protect (文字列プレーンテキスト、DateTimeOffset 有効期限): 文字列</span><span class="sxs-lookup"><span data-stu-id="b7506-118">Protect(string plaintext, DateTimeOffset expiration) : string</span></span>

* <span data-ttu-id="b7506-119">Protect (文字列プレーンテキスト、TimeSpan 有効期間): 文字列</span><span class="sxs-lookup"><span data-stu-id="b7506-119">Protect(string plaintext, TimeSpan lifetime) : string</span></span>

* <span data-ttu-id="b7506-120">Protect (文字列プレーンテキスト): 文字列</span><span class="sxs-lookup"><span data-stu-id="b7506-120">Protect(string plaintext) : string</span></span>

<span data-ttu-id="b7506-121">プレーンテキストのみを取得`Protect`するコアメソッドに加えて、ペイロードの有効期限を指定できる新しいオーバーロードがあります。</span><span class="sxs-lookup"><span data-stu-id="b7506-121">In addition to the core `Protect` methods which take only the plaintext, there are new overloads which allow specifying the payload's expiration date.</span></span> <span data-ttu-id="b7506-122">有効期限の日付は、(を`DateTimeOffset`使用して) 絶対日付として指定することも、(を`TimeSpan`使用して現在のシステム時刻から) 相対時刻として指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="b7506-122">The expiration date can be specified as an absolute date (via a `DateTimeOffset`) or as a relative time (from the current system time, via a `TimeSpan`).</span></span> <span data-ttu-id="b7506-123">有効期限がないオーバーロードが呼び出された場合、ペイロードは期限切れにならないと見なされます。</span><span class="sxs-lookup"><span data-stu-id="b7506-123">If an overload which doesn't take an expiration is called, the payload is assumed never to expire.</span></span>

* <span data-ttu-id="b7506-124">保護解除 (byte [] protectedData、out DateTimeOffset 有効期限): byte []</span><span class="sxs-lookup"><span data-stu-id="b7506-124">Unprotect(byte[] protectedData, out DateTimeOffset expiration) : byte[]</span></span>

* <span data-ttu-id="b7506-125">保護解除 (byte [] protectedData): byte []</span><span class="sxs-lookup"><span data-stu-id="b7506-125">Unprotect(byte[] protectedData) : byte[]</span></span>

* <span data-ttu-id="b7506-126">保護解除 (文字列 protectedData、out DateTimeOffset 有効期限): 文字列</span><span class="sxs-lookup"><span data-stu-id="b7506-126">Unprotect(string protectedData, out DateTimeOffset expiration) : string</span></span>

* <span data-ttu-id="b7506-127">保護解除 (string protectedData): 文字列</span><span class="sxs-lookup"><span data-stu-id="b7506-127">Unprotect(string protectedData) : string</span></span>

<span data-ttu-id="b7506-128">メソッド`Unprotect`は、保護されていない元のデータを返します。</span><span class="sxs-lookup"><span data-stu-id="b7506-128">The `Unprotect` methods return the original unprotected data.</span></span> <span data-ttu-id="b7506-129">ペイロードの有効期限がまだ切れていない場合、絶対有効期限は、元の保護されていないデータと共にオプションの out パラメーターとして返されます。</span><span class="sxs-lookup"><span data-stu-id="b7506-129">If the payload hasn't yet expired, the absolute expiration is returned as an optional out parameter along with the original unprotected data.</span></span> <span data-ttu-id="b7506-130">ペイロードの有効期限が切れた場合、保護を解除するメソッドのすべてのオーバーロードは、System.security.cryptography.cryptographicexception> をスローします。</span><span class="sxs-lookup"><span data-stu-id="b7506-130">If the payload is expired, all overloads of the Unprotect method will throw CryptographicException.</span></span>

>[!WARNING]
> <span data-ttu-id="b7506-131">長期間または永続的な永続化を必要とするペイロードを保護するために、これらの Api を使用することは推奨されません。</span><span class="sxs-lookup"><span data-stu-id="b7506-131">It's not advised to use these APIs to protect payloads which require long-term or indefinite persistence.</span></span> <span data-ttu-id="b7506-132">"保護されたペイロードを1か月後に完全に回復できないようにすることはできますか。"</span><span class="sxs-lookup"><span data-stu-id="b7506-132">"Can I afford for the protected payloads to be permanently unrecoverable after a month?"</span></span> <span data-ttu-id="b7506-133">優れた目安として機能します。答えが「いいえ」の場合、開発者は別の Api を検討する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b7506-133">can serve as a good rule of thumb; if the answer is no then developers should consider alternative APIs.</span></span>

<span data-ttu-id="b7506-134">次のサンプルでは、[非 DI コードパス](xref:security/data-protection/configuration/non-di-scenarios)を使用して、データ保護システムをインスタンス化しています。</span><span class="sxs-lookup"><span data-stu-id="b7506-134">The sample below uses the [non-DI code paths](xref:security/data-protection/configuration/non-di-scenarios) for instantiating the data protection system.</span></span> <span data-ttu-id="b7506-135">このサンプルを実行するには、最初に AspNetCore パッケージへの参照を追加したことを確認します。</span><span class="sxs-lookup"><span data-stu-id="b7506-135">To run this sample, ensure that you have first added a reference to the Microsoft.AspNetCore.DataProtection.Extensions package.</span></span>

[!code-csharp[](limited-lifetime-payloads/samples/limitedlifetimepayloads.cs)]
