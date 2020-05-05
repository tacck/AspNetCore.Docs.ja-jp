---
title: ASP.NET Core の ObjectPool を使用したオブジェクトの再利用
author: rick-anderson
description: ObjectPool を使用して ASP.NET Core アプリのパフォーマンスを向上させるためのヒントです。
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.date: 04/11/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: performance/ObjectPool
ms.openlocfilehash: f29d15fc1e2d2ad84526598be14638110f08614e
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774783"
---
# <a name="object-reuse-with-objectpool-in-aspnet-core"></a>ASP.NET Core の ObjectPool を使用したオブジェクトの再利用

([上田 Gordon](https://twitter.com/stevejgordon)、[ライアン Nowak](https://github.com/rynowak)、 [Rick Anderson](https://twitter.com/RickAndMSFT) )

<xref:Microsoft.Extensions.ObjectPool>は、オブジェクトのガベージコレクションを許可するのではなく、オブジェクトのグループをメモリに保持して再利用できるようにする ASP.NET Core インフラストラクチャの一部です。

管理されているオブジェクトが次のような場合に、オブジェクトプールを使用することができます。

- 割り当て/初期化にコストがかかります。
- 限られたリソースを表します。
- 予測と頻繁に使用されます。

たとえば、ASP.NET Core framework では、インスタンスを再利用<xref:System.Text.StringBuilder>するために、いくつかの場所でオブジェクトプールを使用します。 `StringBuilder`は、独自のバッファーを割り当てて管理し、文字データを保持します。 ASP.NET Core が機能`StringBuilder`を実装するために定期的に使用し、それらを再利用することによって、パフォーマンスが向上します。

オブジェクトプーリングでは、常にパフォーマンスが向上するわけではありません。

- オブジェクトの初期化コストが高い場合を除き、通常は、プールからオブジェクトを取得する方が遅くなります。
- プールによって管理されるオブジェクトは、プールが割り当て解除されるまで割り当てられません。

オブジェクトプールは、アプリまたはライブラリの現実的なシナリオを使用してパフォーマンスデータを収集した後にのみ使用してください。

**警告: は`ObjectPool`を実装`IDisposable`していません。破棄が必要な型では使用しないことをお勧めします。**

**注: ObjectPool では、割り当てられるオブジェクトの数に制限は設定されません。これにより、保持するオブジェクトの数に制限が適用されます。**

## <a name="concepts"></a>概念

<xref:Microsoft.Extensions.ObjectPool.ObjectPool`1>-基本的なオブジェクトプールの抽象化。 オブジェクトを取得して返すために使用します。

<xref:Microsoft.Extensions.ObjectPool.PooledObjectPolicy%601>-オブジェクトの作成方法と、オブジェクトがプールに返されるときの*リセット*方法をカスタマイズするには、これを実装します。 これは、直接構築するオブジェクトプールに渡すことができます...もしくは

<xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider.Create*>オブジェクトプールを作成するためのファクトリとして機能します。
<!-- REview, there is no ObjectPoolProvider<T> -->

ObjectPool は、次のような複数の方法でアプリで使用できます。

* プールをインスタンス化しています。
* [依存関係の挿入](xref:fundamentals/dependency-injection)(DI) にプールをインスタンスとして登録する。
* を DI `ObjectPoolProvider<>`に登録し、ファクトリとして使用します。

## <a name="how-to-use-objectpool"></a>ObjectPool の使用方法

オブジェクト<xref:Microsoft.Extensions.ObjectPool.ObjectPool`1>を取得し<xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Return*> 、オブジェクトを返すには、を呼び出します。  すべてのオブジェクトを返す必要はありません。 オブジェクトを返さない場合は、ガベージコレクションが実行されます。

## <a name="objectpool-sample"></a>ObjectPool サンプル

コード例を次に示します。

* `ObjectPoolProvider` [依存関係挿入](xref:fundamentals/dependency-injection)(DI) コンテナーにを追加します。
* DI コンテナーに`ObjectPool<StringBuilder>`を追加して構成します。
* を`BirthdayMiddleware`追加します。

[!code-csharp[](ObjectPool/ObjectPoolSample/Startup.cs?name=snippet)]

次のコードはを実装します。`BirthdayMiddleware`

[!code-csharp[](ObjectPool/ObjectPoolSample/BirthdayMiddleware.cs?name=snippet)]
