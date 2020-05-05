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
# <a name="miscellaneous-aspnet-core-data-protection-apis"></a>その他の ASP.NET Core データ保護 Api

<a name="data-protection-extensibility-mics-apis"></a>

>[!WARNING]
> 次のインターフェイスのいずれかを実装する型は、複数の呼び出し元に対してスレッドセーフである必要があります。

## <a name="isecret"></a>ISecret

インターフェイス`ISecret`は、暗号化キーマテリアルなどのシークレット値を表します。 次の API サーフェイスが含まれています。

* `Length`: `int`

* `Dispose()`: `void`

* `WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`

メソッド`WriteSecretIntoBuffer`は、指定されたバッファーに生のシークレット値を設定します。 この API がを`byte[]`直接返すのではなく、パラメーターとしてバッファーを受け取る理由は、呼び出し元がバッファーオブジェクトをピン留めして、秘密の露出をマネージガベージコレクターに制限する可能性があるためです。

この`Secret`型は、の`ISecret`具象実装であり、シークレット値はインプロセスメモリに格納されます。 Windows プラットフォームでは、シークレット値は[CryptProtectMemory](https://msdn.microsoft.com/library/windows/desktop/aa380262(v=vs.85).aspx)を使用して暗号化されます。
