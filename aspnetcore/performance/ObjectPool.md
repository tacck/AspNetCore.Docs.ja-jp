---
title: ASP.NET Core の ObjectPool を使用したオブジェクトの再利用
author: rick-anderson
description: ObjectPool を使用して ASP.NET Core アプリのパフォーマンスを向上させるためのヒントです。
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.date: 04/11/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: performance/ObjectPool
ms.openlocfilehash: 9df7f370eb550172493478bcd8d94a9541926fec
ms.sourcegitcommit: 895e952aec11c91d703fbdd3640a979307b8cc67
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2020
ms.locfileid: "85793555"
---
# <a name="object-reuse-with-objectpool-in-aspnet-core"></a>ASP.NET Core の ObjectPool を使用したオブジェクトの再利用

[上田 Gordon](https://twitter.com/stevejgordon)、[ライアン Nowak](https://github.com/rynowak)、および[Günther](https://github.com/gfoidl)があります。

<xref:Microsoft.Extensions.ObjectPool>は、オブジェクトのガベージコレクションを許可するのではなく、オブジェクトのグループをメモリに保持して再利用できるようにする ASP.NET Core インフラストラクチャの一部です。

管理されているオブジェクトが次のような場合に、オブジェクトプールを使用することができます。

- 割り当て/初期化にコストがかかります。
- 限られたリソースを表します。
- 予測と頻繁に使用されます。

たとえば、ASP.NET Core framework では、インスタンスを再利用するために、いくつかの場所でオブジェクトプールを使用し <xref:System.Text.StringBuilder> ます。 `StringBuilder`は、独自のバッファーを割り当てて管理し、文字データを保持します。 ASP.NET Core `StringBuilder` が機能を実装するために定期的に使用し、それらを再利用することによって、パフォーマンスが向上します。

オブジェクトプーリングでは、常にパフォーマンスが向上するわけではありません。

- オブジェクトの初期化コストが高い場合を除き、通常は、プールからオブジェクトを取得する方が遅くなります。
- プールによって管理されるオブジェクトは、プールが割り当て解除されるまで割り当てられません。

オブジェクトプールは、アプリまたはライブラリの現実的なシナリオを使用してパフォーマンスデータを収集した後にのみ使用してください。

::: moniker range="< aspnetcore-3.0"
**警告: はを `ObjectPool` 実装していません `IDisposable` 。破棄が必要な型では使用しないことをお勧めします。** `ObjectPool`ASP.NET Core 3.0 以降では、をサポートして `IDisposable` います。
::: moniker-end

**注: ObjectPool では、割り当てられるオブジェクトの数に制限は設定されません。これにより、保持するオブジェクトの数に制限が適用されます。**

## <a name="concepts"></a>概念

<xref:Microsoft.Extensions.ObjectPool.ObjectPool`1>-基本的なオブジェクトプールの抽象化。 オブジェクトを取得して返すために使用します。

<xref:Microsoft.Extensions.ObjectPool.PooledObjectPolicy%601>-オブジェクトの作成方法と、オブジェクトがプールに返されるときの*リセット*方法をカスタマイズするには、これを実装します。 これは、直接構築するオブジェクトプールに渡すことができます...もしくは

<xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider.Create*>オブジェクトプールを作成するためのファクトリとして機能します。
<!-- REview, there is no ObjectPoolProvider<T> -->

ObjectPool は、次のような複数の方法でアプリで使用できます。

* プールをインスタンス化しています。
* [依存関係の挿入](xref:fundamentals/dependency-injection)(DI) にプールをインスタンスとして登録する。
* を `ObjectPoolProvider<>` DI に登録し、ファクトリとして使用します。

## <a name="how-to-use-objectpool"></a>ObjectPool の使用方法

<xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Get*>オブジェクトを取得し、オブジェクトを返すには、を呼び出し <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Return*> ます。  すべてのオブジェクトを返す必要はありません。 オブジェクトを返さない場合は、ガベージコレクションが実行されます。

::: moniker range=">= aspnetcore-3.0"
<xref:Microsoft.Extensions.ObjectPool.DefaultObjectPoolProvider>が使用され、を実装する場合 `T` `IDisposable` :

* プールに返され***ない***項目は破棄されます。
* プールが DI によって破棄されると、プール内のすべての項目が破棄されます。

注: プールが破棄された後:

* を呼び出す `Get` と、がスローさ `ObjectDisposedException` れます。
* `return`指定された項目を破棄します。

::: moniker-end

## <a name="objectpool-sample"></a>ObjectPool サンプル

コード例を次に示します。

* `ObjectPoolProvider`[依存関係挿入](xref:fundamentals/dependency-injection)(DI) コンテナーにを追加します。
* DI コンテナーにを追加して構成し `ObjectPool<StringBuilder>` ます。
* を追加し `BirthdayMiddleware` ます。

[!code-csharp[](ObjectPool/ObjectPoolSample/Startup.cs?name=snippet)]

次のコードはを実装します。`BirthdayMiddleware`

[!code-csharp[](ObjectPool/ObjectPoolSample/BirthdayMiddleware.cs?name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]
