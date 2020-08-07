---
title: その他の ASP.NET Core データ保護 Api
author: rick-anderson
description: ASP.NET Core Data Protection ISecret インターフェイスについて説明します。
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
uid: security/data-protection/extensibility/misc-apis
ms.openlocfilehash: 2e319cdcec1e005682555c4e03c52632e6d8521a
ms.sourcegitcommit: b0fa7ff0cb158277df61bcd08058a81222c3fe10
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2020
ms.locfileid: "87913812"
---
# <a name="miscellaneous-aspnet-core-data-protection-apis"></a><span data-ttu-id="41b49-103">その他の ASP.NET Core データ保護 Api</span><span class="sxs-lookup"><span data-stu-id="41b49-103">Miscellaneous ASP.NET Core Data Protection APIs</span></span>

<a name="data-protection-extensibility-mics-apis"></a>

>[!WARNING]
> <span data-ttu-id="41b49-104">次のインターフェイスのいずれかを実装する型は、複数の呼び出し元に対してスレッドセーフである必要があります。</span><span class="sxs-lookup"><span data-stu-id="41b49-104">Types that implement any of the following interfaces should be thread-safe for multiple callers.</span></span>

## <a name="isecret"></a><span data-ttu-id="41b49-105">ISecret</span><span class="sxs-lookup"><span data-stu-id="41b49-105">ISecret</span></span>

<span data-ttu-id="41b49-106">インターフェイスは、 `ISecret` 暗号化キーマテリアルなどのシークレット値を表します。</span><span class="sxs-lookup"><span data-stu-id="41b49-106">The `ISecret` interface represents a secret value, such as cryptographic key material.</span></span> <span data-ttu-id="41b49-107">次の API サーフェイスが含まれています。</span><span class="sxs-lookup"><span data-stu-id="41b49-107">It contains the following API surface:</span></span>

* <span data-ttu-id="41b49-108">`Length`: `int`</span><span class="sxs-lookup"><span data-stu-id="41b49-108">`Length`: `int`</span></span>

* <span data-ttu-id="41b49-109">`Dispose()`: `void`</span><span class="sxs-lookup"><span data-stu-id="41b49-109">`Dispose()`: `void`</span></span>

* <span data-ttu-id="41b49-110">`WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`</span><span class="sxs-lookup"><span data-stu-id="41b49-110">`WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`</span></span>

<span data-ttu-id="41b49-111">メソッドは、指定された `WriteSecretIntoBuffer` バッファーに生のシークレット値を設定します。</span><span class="sxs-lookup"><span data-stu-id="41b49-111">The `WriteSecretIntoBuffer` method populates the supplied buffer with the raw secret value.</span></span> <span data-ttu-id="41b49-112">この API がを直接返すのではなく、パラメーターとしてバッファーを受け取る理由は、 `byte[]` 呼び出し元がバッファーオブジェクトをピン留めして、秘密の露出をマネージガベージコレクターに制限する可能性があるためです。</span><span class="sxs-lookup"><span data-stu-id="41b49-112">The reason this API takes the buffer as a parameter rather than returning a `byte[]` directly is that this gives the caller the opportunity to pin the buffer object, limiting secret exposure to the managed garbage collector.</span></span>

<span data-ttu-id="41b49-113">この `Secret` 型は、の具象実装であり、 `ISecret` シークレット値はインプロセスメモリに格納されます。</span><span class="sxs-lookup"><span data-stu-id="41b49-113">The `Secret` type is a concrete implementation of `ISecret` where the secret value is stored in in-process memory.</span></span> <span data-ttu-id="41b49-114">Windows プラットフォームでは、シークレット値は[CryptProtectMemory](/windows/win32/api/dpapi/nf-dpapi-cryptprotectmemory)を使用して暗号化されます。</span><span class="sxs-lookup"><span data-stu-id="41b49-114">On Windows platforms, the secret value is encrypted via [CryptProtectMemory](/windows/win32/api/dpapi/nf-dpapi-cryptprotectmemory).</span></span>
