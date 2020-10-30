---
title: ASP.NET Core でのサブキーの派生と認証された暗号化
author: rick-anderson
description: ASP.NET Core データ保護サブキーの派生と認証された暗号化の実装の詳細について説明します。
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
uid: security/data-protection/implementation/subkeyderivation
ms.openlocfilehash: efe8ad2f71feda9cbc1693d362e30eff29cbcd74
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060158"
---
# <a name="subkey-derivation-and-authenticated-encryption-in-aspnet-core"></a>ASP.NET Core でのサブキーの派生と認証された暗号化

<a name="data-protection-implementation-subkey-derivation"></a>

キーリングのほとんどのキーには何らかの形式のエントロピが含まれ、"CBC-mode encryption + HMAC validation" または "GCM encryption + validation" というアルゴリズム情報が含まれます。 このような場合は、このキーのマスターキーマテリアル (または KM) として埋め込みエントロピを参照し、実際の暗号化操作に使用するキーを派生させるキー派生関数を実行します。

> [!NOTE]
> キーは抽象的であり、カスタム実装は次のように動作しない可能性があります。 `IAuthenticatedEncryptor`組み込みのファクトリのいずれかを使用するのではなく、キーが独自のを実装する場合、このセクションで説明するメカニズムは適用されなくなります。

<a name="data-protection-implementation-subkey-derivation-aad"></a>

## <a name="additional-authenticated-data-and-subkey-derivation"></a>追加の認証データとサブキーの派生

インターフェイスは、 `IAuthenticatedEncryptor` 認証されたすべての暗号化操作のコアインターフェイスとして機能します。 この `Encrypt` メソッドは、プレーンテキストと Additional認証 Ateddata (AAD) という2つのバッファーを受け取ります。 プレーンテキストコンテンツは、の呼び出しを変更せずにフローします `IDataProtector.Protect` が、AAD はシステムによって生成され、3つのコンポーネントで構成されます。

1. このバージョンのデータ保護システムを識別する32ビットマジックヘッダー 09 F0 C9 F0。

2. 128ビットキー id。

3. この操作を実行しているを作成した目的のチェーンから形成された可変長文字列。 `IDataProtector`

AAD は3つのすべてのコンポーネントの組に対して一意であるため、すべての暗号化操作で KM 自体を使用するのではなく、それを使用して KM から新しいキーを派生させることができます。 を呼び出すたびに `IAuthenticatedEncryptor.Encrypt` 、次のキー派生プロセスが実行されます。

`( K_E, K_H ) = SP800_108_CTR_HMACSHA512(K_M, AAD, contextHeader || keyModifier)`

ここでは、カウンタモードで NIST SP800-108 KDF を呼び出しています ( [NIST SP800-108](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-108.pdf), Sec. 5.1 を参照)。次のパラメーターがあります。

* キー派生キー (KDK) = `K_M`

* PRF = HMACSHA512

* ラベル = Additional認証 Ateddata

* context = contextHeader | |keyModifier

コンテキストヘッダーは可変長であり、基本的には、とを派生させるアルゴリズムの拇印として機能し `K_E` `K_H` ます。 キー修飾子は、の各呼び出しに対してランダムに生成された128ビット文字列であり、 `Encrypt` KDF への他のすべての入力が定数であっても、KH がこの特定の認証暗号化操作に対して一意であることを保証するために役立ちます。

CBC モード暗号化 + HMAC 検証操作の場合、 `| K_E |` は対称ブロック暗号キーの長さで、 `| K_H |` は hmac ルーチンのダイジェストサイズです。 GCM 暗号化 + 検証操作の場合は、 `| K_H | = 0` 。

## <a name="cbc-mode-encryption--hmac-validation"></a>CBC モード暗号化 + HMAC 検証

`K_E`上記のメカニズムを使用してが生成されたら、ランダムな初期化ベクターを生成し、対称ブロック暗号アルゴリズムを実行してプレーンテキストを暗号化します。 次に、初期化ベクターと暗号化テキストを、キーで初期化された HMAC ルーチンを通じて実行し、 `K_H` MAC を生成します。 このプロセスと戻り値は、次のようにグラフィカルに表示されます。

![CBC モードのプロセスと戻り値](subkeyderivation/_static/cbcprocess.png)

`output:= keyModifier || iv || E_cbc (K_E,iv,data) || HMAC(K_H, iv || E_cbc (K_E,iv,data))`

> [!NOTE]
> `IDataProtector.Protect`実装では、出力する[マジックヘッダーとキー id](xref:security/data-protection/implementation/authenticated-encryption-details)が、呼び出し元に返される前に付加されます。 マジックヘッダーとキー id は暗黙的に [AAD](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation-aad)に含まれるため、キー修飾子は kdf に入力として渡されるため、最終的に返されるペイロードのすべての1バイトが MAC によって認証されます。

## <a name="galoiscounter-mode-encryption--validation"></a>Galois/カウンタモードの暗号化 + 検証

`K_E`上記のメカニズムを使用してが生成されたら、ランダムな96ビット nonce を生成し、対称ブロック暗号アルゴリズムを実行してプレーンテキストを暗号化し、128ビット認証タグを生成します。

![GCM モードのプロセスと戻り値](subkeyderivation/_static/galoisprocess.png)

`output := keyModifier || nonce || E_gcm (K_E,nonce,data) || authTag`

> [!NOTE]
> GCM は、AAD の概念をネイティブでサポートしていますが、引き続き、元の KDF にのみ AAD を供給し、AAD パラメーターの空の文字列を GCM に渡すことをオプトインします。 この理由は2つのフォールドです。 まず、 [機敏性をサポートするために](xref:security/data-protection/implementation/context-headers#data-protection-implementation-context-headers) 、 `K_M` 暗号化キーとして直接使用することは避けたいと考えています。 さらに、GCM では、入力に対して非常に厳密な一意性要件が課されます。 GCM 暗号化ルーチンが、同じ (キー、nonce) ペアを持つ2つ以上の個別の入力データセットに対して呼び出される確率は、2 ^ 32 を超えることはできません。 この問題を解決した場合、2 ^ `K_E` 32 を超える暗号化操作を実行する前に、32の制限の afoul を実行することはできません。 非常に多くの操作が行われているように見えますが、高トラフィックの web サーバーでは、これらのキーの通常の有効期間内に、わずか数日で40億の要求を通過させることができます。 2 ^-32 の確率制限に準拠したままにするため、128ビットのキー修飾子と96ビットの nonce が引き続き使用されます。これにより、任意のの使用可能な操作数が大幅に拡張され `K_M` ます。 設計を簡単にするために、CBC 操作と GCM 操作の間で KDF コードパスを共有しています。 AAD は既に KDF で検討されているため、GCM ルーチンに転送する必要はありません。
