---
title: その他の ASP.NET Core データ保護 Api
author: rick-anderson
description: ASP.NET Core Data Protection ISecret インターフェイスについて説明します。
ms.author: riande
ms.date: 10/14/2016
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/extensibility/misc-apis
ms.openlocfilehash: bd772b11300cca8896ed512da1cd12c49e6f104b
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060756"
---
# <a name="miscellaneous-aspnet-core-data-protection-apis"></a>その他の ASP.NET Core データ保護 Api

<a name="data-protection-extensibility-mics-apis"></a>

>[!WARNING]
> 次のインターフェイスのいずれかを実装する型は、複数の呼び出し元に対してスレッドセーフである必要があります。

## <a name="isecret"></a>ISecret

インターフェイスは、 `ISecret` 暗号化キーマテリアルなどのシークレット値を表します。 次の API サーフェイスが含まれています。

* `Length`: `int`

* `Dispose()`: `void`

* `WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`

メソッドは、指定された `WriteSecretIntoBuffer` バッファーに生のシークレット値を設定します。 この API がを直接返すのではなく、パラメーターとしてバッファーを受け取る理由は、 `byte[]` 呼び出し元がバッファーオブジェクトをピン留めして、秘密の露出をマネージガベージコレクターに制限する可能性があるためです。

この `Secret` 型は、の具象実装であり、 `ISecret` シークレット値はインプロセスメモリに格納されます。 Windows プラットフォームでは、シークレット値は [CryptProtectMemory](/windows/win32/api/dpapi/nf-dpapi-cryptprotectmemory)を使用して暗号化されます。
