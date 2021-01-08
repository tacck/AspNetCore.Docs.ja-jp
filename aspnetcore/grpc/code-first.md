---
title: .NET を使用したコードファーストの gRPC サービスとクライアント
author: jamesnk
description: .NET を使用したコードファーストの gRPC を作成する際の基本的な概念について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 01/04/2021
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
uid: grpc/code-first
ms.openlocfilehash: 6856770a57d900a4953dad294236cb4d08479d9d
ms.sourcegitcommit: 53e01d6e9b70a18a05618f0011cf115a16633c21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/05/2021
ms.locfileid: "97880619"
---
# <a name="code-first-grpc-services-and-clients-with-net"></a>.NET を使用したコードファーストの gRPC サービスとクライアント

作成者: [James Newton-King](https://twitter.com/jamesnk)、[Marc Gravell](https://twitter.com/marcgravell)

コードファースト gRPC では、.NET の型を使用して、サービスとメッセージ コントラクトを定義します。

コードファーストは、システム全体で .NET を使用する場合に適しています。

* .Net サービスとデータ コントラクトの型は、.NET サーバーとクライアントの間で共有できます。
* `.proto` ファイルおよびコード生成でコントラクトを定義する必要がなくなります。

複数の言語の多言語システムでは、コードファーストはお勧めできません。 .NET サービスとデータ コントラクトの型は、.NET 以外のプラットフォームでは使用できません。 コードファーストを使用して作成された gRPC サービスを呼び出すには、他のプラットフォームでサービスと一致する `.proto` コントラクトを作成する必要があります。

## <a name="protobuf-netgrpc"></a>protobuf-net.Grpc

> [!IMPORTANT]
> protobuf-net.Grpc の支援が必要な場合は、[protobuf-net.Grpc の Web サイト](https://protobuf-net.github.io/protobuf-net.Grpc/)を参照するか、または [protobuf-net.Grpc GitHub リポジトリ](https://github.com/protobuf-net/protobuf-net.Grpc)でイシューを作成してください。

[protobuf-net.Grpc](https://protobuf-net.github.io/protobuf-net.Grpc/) はコミュニティ プロジェクトであり、Microsoft ではサポートされていません。 これにより、`Grpc.AspNetCore` と `Grpc.Net.Client` に対するコードファーストのサポートが追加されます。 属性で注釈が付けられた .NET 型を使用して、アプリの gRPC サービスとメッセージが定義されます。

コードファーストの gRPC サービスを作成するための最初の手順は、コード コントラクトを定義することです。

* サーバーとクライアントによって共有される新しいプロジェクトを作成します。
* [protobuf-net.Grpc](https://www.nuget.org/packages/protobuf-net.Grpc) パッケージ リファレンスを追加します。
* サービスとデータ コントラクトの型を作成します。

[!code-csharp[](code-first/Contracts.cs)]

上記のコードでは次の操作が行われます。

* `HelloRequest` および `HelloReply` メッセージを定義します。
* 単項 `SayHelloAsync` gRPC メソッドを使用して `IGreeterService` コントラクト インターフェイスを定義します。

サービス コントラクトがサーバーに実装され、クライアントから呼び出されます。 サービス インターフェイスに定義されているメソッドは、単項、サーバー ストリーミング、クライアント ストリーミング、または双方向ストリーミングのいずれであるかによって、特定の署名と一致する必要があります。

サービス コントラクトの定義の詳細については、[protobuf-net.Grpc の概要ドキュメント](https://protobuf-net.github.io/protobuf-net.Grpc/gettingstarted)を参照してください。

## <a name="create-a-code-first-grpc-service"></a>コードファースト gRPC サービスを作成する

ASP.NET Core アプリに gRPC コードファースト サービスを追加するには、次のようにします。

* [protobuf-net.Grpc.AspNetCore](https://www.nuget.org/packages/protobuf-net.Grpc.AspNetCore) パッケージ リファレンスを追加します。
* 共有コードコントラクト プロジェクトへの参照を追加します。

新しい `GreeterService.cs` ファイルを作成し、`IGreeterService` サービス インターフェイスを実装します。

[!code-csharp[](code-first/GreeterService.cs?highlight=1)]

`Startup.cs` ファイルを更新します。

[!code-csharp[](code-first/Startup.cs?highlight=3,17)]

上記のコードにより、次のことが行われます。

* `AddCodeFirstGrpc` により、コードファーストを有効にするサービスが登録されます。
* `MapGrpcService<GreeterService>` により、コードファーストのサービス エンドポイントが追加されます。

コードファーストで実装された gRPC サービスと `.proto` ファイルは、同じアプリ内で共存できます。 すべての gRPC サービスでは、[gRPC サービス構成](xref:grpc/configuration#configure-services-options)が使されます。

## <a name="create-a-code-first-grpc-client"></a>コードファースト gRPC クライアントを作成する

コードファースト gRPC クライアントは、サービス コントラクトを使用して gRPC サービスを呼び出します。 コードファースト クライアントを使用して gRPC サービスを呼び出すには、次のようにします。

* [protobuf-net.Grpc](https://www.nuget.org/packages/protobuf-net.Grpc) パッケージ リファレンスを追加します。
* 共有コードコントラクト プロジェクトへの参照を追加します。

[!code-csharp[](code-first/Program.cs?highlight=2,4-5)]

上記のコードでは次の操作が行われます。

* gRPC チャネルを作成します。
* `CreateGrpcService<IGreeterService>` 拡張メソッドを使用して、チャネルからコードファースト クライアントを作成します。
* `SayHelloAsync` を使用して gRPC サービスを呼び出します。

コードファースト gRPC クライアントがチャネルから作成されます。 通常のクライアントと同様に、コードファースト クライアントは、その[チャネル構成](xref:grpc/configuration#configure-client-options)を使用します。

## <a name="additional-resources"></a>その他のリソース

* [protobuf-net.Grpc Web サイト](https://protobuf-net.github.io/protobuf-net.Grpc/)
* [protobuf-net.Grpc GitHub リポジトリ](https://github.com/protobuf-net/protobuf-net.Grpc)
