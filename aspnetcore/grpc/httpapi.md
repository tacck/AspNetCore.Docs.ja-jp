---
title: gRPC から JSON Web API を作成する
author: jamesnk
description: gRPC サービス用の JSON HTTP API を作成する方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/28/2020
no-loc:
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
uid: grpc/httpapi
ms.openlocfilehash: 21f47b889014ad4ff66d4cb710aed0159298f0cc
ms.sourcegitcommit: 7258e94cf60c16e5b6883138e5e68516751ead0f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/29/2020
ms.locfileid: "89102875"
---
# <a name="create-json-web-apis-from-grpc"></a>gRPC から JSON Web API を作成する

作成者: [James Newton-King](https://twitter.com/jamesnk)

> [!IMPORTANT]
> gRPC HTTP API は、コミットされた製品ではなく、試験段階のプロジェクトです。 次の目的があります。
>
> * gRPC サービス用の JSON Web API を作成するための方法が機能することをテストする。
> * .NET 開発者にとってこの方法が有効かどうかのフィードバックを得る。
>
> 開発者が望み、生産性が向上するものを構築するために、[フィードバックをお寄せください](https://github.com/grpc/grpc-dotnet/issues/167)。

gRPC は、アプリ間で通信するための最新の方法です。 gRPC は、HTTP/2、ストリーミング、Protobuf、メッセージ コントラクトを使用して、高パフォーマンスのリアルタイム サービスを作成します。

gRPC の制限事項の 1 つが、すべてのプラットフォームで使用できるわけではないということです。 ブラウザーでは HTTP/2 が完全にサポートされていないため、REST と JSON が、ブラウザー アプリにデータを取り込む主な方法になります。 gRPC にはベネフィットがありますが、REST と JSON にも最新のアプリで重要な役割があります。 gRPC ***および*** JSON Web API を構築すると、アプリ開発に不要なオーバーヘッドが発生します。

このドキュメントでは、gRPC サービスを使用して JSON Web API を作成する方法について説明します。

## <a name="grpc-http-api"></a>gRPC HTTP API

gRPC HTTP API は、gRPC サービス用の RESTful JSON API を作成する ASP.NET Core 向けの試験段階の拡張機能です。 構成が完了すると、gRPC HTTP API により、アプリが、使い慣れた HTTP の次のような概念を使用して gRPC サービスを呼び出せるようになります。

* HTTP 動詞
* URL パラメーター バインディング
* JSON の要求/応答

サービスの呼び出しには、gRPC も引き続き使用できます。

### <a name="usage"></a>使用法

1. [Microsoft.AspNetCore.Grpc.HttpApi](https://www.nuget.org/packages/Microsoft.AspNetCore.Grpc.HttpApi) へのパッケージ参照を追加します。
1. `AddGrpcHttpApi` で *Startup.cs* にサービスを登録します。
1. [google/api/http.proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/http.proto) と [google/api/annotations.proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/annotations.proto) のファイルをプロジェクトに追加します。
1. HTTP のバインディングとルートを使用して、 *.proto* ファイル内の gRPC メソッドに注釈を付けます。

```protobuf
syntax = "proto3";

import "google/api/annotations.proto";

package greet;

service Greeter {
  rpc SayHello (HelloRequest) returns (HelloReply) {
    option (google.api.http) = {
      get: "v1/greeter/{name}"
    };
  }
}

message HelloRequest {
  string name = 1;
}

message HelloReply {
  string message = 1;
}
```

`SayHello` gRPC メソッドを gRPC+Protobuf として、そして HTTP API として呼び出すことができるようになりました。

* 要求: `HTTP/1.1 GET /v1/greeter/world`
* 応答: `{ "message": "Hello world" }`

サーバー ログには、gRPC サービスによって HTTP 呼び出しが実行されることが示されます。 gRPC HTTP API は、受信 HTTP 要求を gRPC メッセージにマップし、応答メッセージを JSON に変換します。

```
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/1.1 GET https://localhost:5001/v1/greeter/world
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - v1/greeter/{name}'
info: Server.GreeterService[0]
      Sending hello to world
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - v1/greeter/{name}'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 1.996ms 200 application/json
```

これが基本的な例です。 よりカスタマイズしたオプションについては、「[HttpRule](https://cloud.google.com/service-infrastructure/docs/service-management/reference/rpc/google.api#google.api.HttpRule)」を参照してください。

### <a name="grpc-http-api-vs-grpc-web"></a>gRPC HTTP API と gRPC-Web

gRPC HTTP API と gRPC-Web の両方で、gRPC サービスをブラウザーから呼び出すことができます。 ただし、それぞれの動作は次のように異なります。

* gRPC-Web では、ブラウザー アプリが gRPC-Web クライアントと Protobuf を使用してブラウザーから gRPC サービスを呼び出すことができます。 gRPC-Web では、ブラウザー アプリで gRPC クライアントを生成する必要があります。また、小さい Protobuf メッセージをすばやく送信できるという利点があります。
* gRPC HTTP API を使用すると、JSON と RESTful API のように、ブラウザー アプリで gRPC サービスを呼び出すことができます。 ブラウザー アプリで gRPC クライアントを生成する必要はありません。gRPC について知る必要もありません。

生成されたクライアントは gRPC HTTP API 用には作成されません。 前の `Greeter` サービスは、ブラウザーの JavaScript API を使用して呼び出すことができます。

```javascript
var name = nameInput.value;

fetch("/v1/greeter/" + name).then(function (response) {
  response.json().then(function (data) {
    console.log("Result: " + data.message);
  });
});
```

### <a name="experimental-status"></a>試験段階の状態

gRPC HTTP API は試験段階です。 完全ではなく、サポートもされていません。 私たちは、このテクノロジと、アプリ開発者が gRPC と JSON サービスを同時に作成できるようになる機能に関心を持っています。 gRPC HTTP API を完成させることは確約していません。

私たちは gRPC HTTP API に対する開発者の関心を測りたいと考えています。 gRPC HTTP API に興味がある場合は、[フィードバックをお寄せください](https://github.com/grpc/grpc-dotnet/issues/167)。

## <a name="grpc-gateway"></a>grpc-gateway

[grpc-gateway](https://grpc-ecosystem.github.io/grpc-gateway/) は、gRPC サービスから RESTful JSON API を作成するための別のテクノロジです。 同じ *.proto* の注釈を使用して、HTTP の概念を gRPC サービスにマップします。

grpc-gateway と gRPC HTTP API の最大の違いは、grpc-gateway ではコード生成を使用してリバースプロキシ サーバーが作成されることです。 リバースプロキシでは、RESTful 呼び出しが gRPC に変換され、gRPC サービスに送信されます。

grpc-gateway のインストールと使用については、[grpc-gateway のドキュメント](https://grpc-ecosystem.github.io/grpc-gateway/docs/usage.html)を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [google.api.HttpRule のドキュメント](https://cloud.google.com/service-infrastructure/docs/service-management/reference/rpc/google.api#google.api.HttpRule)
* <xref:grpc/browser>
