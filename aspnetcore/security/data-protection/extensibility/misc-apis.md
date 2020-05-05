---
title: その他の ASP.NET Core データ保護 Api
author: rick-anderson
description: ASP.NET Core Data Protection ISecret インターフェイスについて説明します。
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/extensibility/misc-apis
ms.openlocfilehash: a07ccc3645a9a8132fd5290e7c43f353f74aca05
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776982"
---
# <a name="miscellaneous-aspnet-core-data-protection-apis"></a><span data-ttu-id="c5a05-103">その他の ASP.NET Core データ保護 Api</span><span class="sxs-lookup"><span data-stu-id="c5a05-103">Miscellaneous ASP.NET Core Data Protection APIs</span></span>

<a name="data-protection-extensibility-mics-apis"></a>

>[!WARNING]
> <span data-ttu-id="c5a05-104">次のインターフェイスのいずれかを実装する型は、複数の呼び出し元に対してスレッドセーフである必要があります。</span><span class="sxs-lookup"><span data-stu-id="c5a05-104">Types that implement any of the following interfaces should be thread-safe for multiple callers.</span></span>

## <a name="isecret"></a><span data-ttu-id="c5a05-105">ISecret</span><span class="sxs-lookup"><span data-stu-id="c5a05-105">ISecret</span></span>

<span data-ttu-id="c5a05-106">インターフェイス`ISecret`は、暗号化キーマテリアルなどのシークレット値を表します。</span><span class="sxs-lookup"><span data-stu-id="c5a05-106">The `ISecret` interface represents a secret value, such as cryptographic key material.</span></span> <span data-ttu-id="c5a05-107">次の API サーフェイスが含まれています。</span><span class="sxs-lookup"><span data-stu-id="c5a05-107">It contains the following API surface:</span></span>

* <span data-ttu-id="c5a05-108">`Length`: `int`</span><span class="sxs-lookup"><span data-stu-id="c5a05-108">`Length`: `int`</span></span>

* <span data-ttu-id="c5a05-109">`Dispose()`: `void`</span><span class="sxs-lookup"><span data-stu-id="c5a05-109">`Dispose()`: `void`</span></span>

* <span data-ttu-id="c5a05-110">`WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`</span><span class="sxs-lookup"><span data-stu-id="c5a05-110">`WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`</span></span>

<span data-ttu-id="c5a05-111">メソッド`WriteSecretIntoBuffer`は、指定されたバッファーに生のシークレット値を設定します。</span><span class="sxs-lookup"><span data-stu-id="c5a05-111">The `WriteSecretIntoBuffer` method populates the supplied buffer with the raw secret value.</span></span> <span data-ttu-id="c5a05-112">この API がを`byte[]`直接返すのではなく、パラメーターとしてバッファーを受け取る理由は、呼び出し元がバッファーオブジェクトをピン留めして、秘密の露出をマネージガベージコレクターに制限する可能性があるためです。</span><span class="sxs-lookup"><span data-stu-id="c5a05-112">The reason this API takes the buffer as a parameter rather than returning a `byte[]` directly is that this gives the caller the opportunity to pin the buffer object, limiting secret exposure to the managed garbage collector.</span></span>

<span data-ttu-id="c5a05-113">この`Secret`型は、の`ISecret`具象実装であり、シークレット値はインプロセスメモリに格納されます。</span><span class="sxs-lookup"><span data-stu-id="c5a05-113">The `Secret` type is a concrete implementation of `ISecret` where the secret value is stored in in-process memory.</span></span> <span data-ttu-id="c5a05-114">Windows プラットフォームでは、シークレット値は[CryptProtectMemory](https://msdn.microsoft.com/library/windows/desktop/aa380262(v=vs.85).aspx)を使用して暗号化されます。</span><span class="sxs-lookup"><span data-stu-id="c5a05-114">On Windows platforms, the secret value is encrypted via [CryptProtectMemory](https://msdn.microsoft.com/library/windows/desktop/aa380262(v=vs.85).aspx).</span></span>
