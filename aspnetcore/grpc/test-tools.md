---
title: ASP.NET Core で gRPCurl を使用して gRPC サービスをテストする
author: jamesnk
description: gRPC ツールを使用してサービスをテストする方法について説明します。 gRPCurl は、gRPC サービスと対話するためのコマンドライン ツールです。 gRPCui は、対話型の Web UI です。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/09/2020
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
uid: grpc/test-tools
ms.openlocfilehash: d8d12c34a54b278e0b116f964e120047b1d2d5d1
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2021
ms.locfileid: "93058793"
---
# <a name="test-grpc-services-with-grpcurl-in-aspnet-core"></a>ASP.NET Core で gRPCurl を使用して gRPC サービスをテストする

作成者: [James Newton-King](https://twitter.com/jamesnk)

gRPC 用のツールが用意されており、開発者は、クライアント アプリをビルドせずにサービスをテストできます。

* [gRPCurl](https://github.com/fullstorydev/grpcurl) は、gRPC サービスとの対話を提供するコマンドライン ツールです。
* [gRPCui](https://github.com/fullstorydev/grpcui) は gRPCurl の上にビルドされ、Postman や Swagger UI などのツールと同様に、gRPC 用の対話型の Web UI が追加されています。

この記事では、次の方法について説明します。

* gRPCurl と gRPCui をダウンロードしてインストールします。
* gRPC ASP.NET Core アプリを使用して、gRPC リフレクションを設定します。
* `grpcurl` を使用して、gRPC サービスの検出とテストを行います。
* `grpcui` を使用して、ブラウザー経由で gRPC サービスと対話します。

## <a name="about-grpcurl"></a>gRPCurl について

gRPCurl は、gRPC コミュニティによって作成されたコマンドライン ツールです。 次のような機能があります。

* ストリーミング サービスを含む、gRPC サービスの呼び出し。
* [gRPC リフレクション](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md)を使用したサービスの検出。
* gRPC サービスの一覧表示と説明。
* セキュリティで保護されたサーバー (TLS) と安全ではない (プレーンテキスト) サーバーの使用。

`grpcurl` のダウンロードとインストールの詳細については、[gRPCurl GitHub ホームページ](https://github.com/fullstorydev/grpcurl#installation)を参照してください。

![gRPCurl コマンド ライン](~/grpc/test-tools/static/grpcurl.png)

## <a name="set-up-grpc-reflection"></a>gRPC リフレクションの設定

`grpcurl` により、サービスを呼び出す前に、サービスの Protobuf コントラクトが認識されている必要があります。 この作業を実行する 2 つの方法があります。

* サーバーで [gRPC リフレクション](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md) を設定します。 gRPCurl により、サービス コントラクトが自動的に検出されます。
* gRPCurl のコマンドライン引数に `.proto` ファイルを指定します。

gRPC リフレクションを設定して gRPCurl を使用する方が簡単です。 gRPC リフレクションにより、クライアントがサービスを検出するために呼び出すことができるアプリに新しい gRPC サービスが追加されます。

gRPC ASP.NET Core には、[`Grpc.AspNetCore.Server.Reflection`](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection) パッケージとの gRPC リフレクションのサポートが組み込まれています。 アプリでリフレクションを構成するには、次を実行します。

* `Grpc.AspNetCore.Server.Reflection` パッケージ参照を追加します。
* `Startup.cs` にリフレクションを登録します。
  * `AddGrpcReflection` でリフレクションを有効にするサービスを登録します。
  * `MapGrpcReflectionService` を使用して、リフレクション サービス エンドポイントを追加します。

[!code-csharp[](~/grpc/test-tools/Startup.cs?name=snippet_1&highlight=4,15-18)]

gRPC リフレクションが設定されると、以下が行われます。

* gRPC リフレクション サービスがサーバー アプリに追加されます。
* gRPC リフレクションをサポートするクライアント アプリで、リフレクション サービスを呼び出して、サーバーによってホストされるサービスを検出できます。
* gRPC サービスは引き続きクライアントから呼び出されます。 リフレクションにより、サービス検出のみが有効にされ、サーバー側のセキュリティはバイパスされません。 [認証と承認](xref:grpc/authn-and-authz)によって保護されるエンドポイントにより、呼び出し側は、エンドポイントが正常に呼び出されるための資格情報を渡す必要があります。

## <a name="use-grpcurl"></a>`grpcurl` を使用します

`-help` 引数では、`grpcurl` コマンドライン オプションについて説明しています。

```console
$ grpcurl -help
```

### <a name="discover-services"></a>サービスの検出

サーバーによって定義されたサービスを表示するには、`describe` 動詞を使用します。

```console
$ grpcurl localhost:5001 describe
greet.Greeter is a service:
service Greeter {
  rpc SayHello ( .greet.HelloRequest ) returns ( .greet.HelloReply );
  rpc SayHellos ( .greet.HelloRequest ) returns ( stream .greet.HelloReply );
}
grpc.reflection.v1alpha.ServerReflection is a service:
service ServerReflection {
  rpc ServerReflectionInfo ( stream .grpc.reflection.v1alpha.ServerReflectionRequest ) returns ( stream .grpc.reflection.v1alpha.ServerReflectionResponse );
}
```

上記の例の場合:

* サーバー `localhost:5001` で `describe` Verb を実行します。
* gRPC リフレクションによって返されたサービスとメソッドを出力します。
  * `Greeter` は、アプリによって実装されるサービスです。
  * `ServerReflection` は、`Grpc.AspNetCore.Server.Reflection` パッケージによって追加されたサービスです。

詳細を表示するには、`describe` をサービス、メソッド、またはメッセージ名と組み合わせます。

```powershell
$ grpcurl localhost:5001 describe greet.HelloRequest
greet.HelloRequest is a message:
message HelloRequest {
  string name = 1;
}
```

### <a name="call-grpc-services"></a>gRPC サービスの呼び出し

要求メッセージを表す JSON 引数と共にサービス名とメソッド名を指定して、gRPC サービスを呼び出します。 JSON は Protobuf に変換され、サービスに送信されます。

```console
$ grpcurl -d '{ \"name\": \"World\" }' localhost:5001 greet.Greeter/SayHello
{
  "message": "Hello World"
}
```

前の例の場合:

* `-d` 引数を使用して、JSON を含む要求メッセージを指定します。 この引数は、サーバー アドレスとメソッド名の前で指定する必要があります。
* `greeter.Greeter` サービスの `SayHello` メソッドを呼び出します。
* 応答メッセージを JSON として出力します。

## <a name="about-grpcui"></a>gRPCui について

gRPCui は、gRPC の対話型 Web UI です。 gRPCurl の上にビルドされ、Postman や Swagger UI などの HTTP ツールと同様に、gRPC サービスを検出してテストするための GUI が提供されます。

`grpcui` のダウンロードとインストールの詳細については、[gRPCui GitHub ホームページ](https://github.com/fullstorydev/grpcui#installation)を参照してください。

## <a name="using-grpcui"></a>`grpcui` の使用

対話するサーバー アドレスを引数に指定して `grpcui` を実行します。

```powershell
$ grpcui localhost:5001
gRPC Web UI available at http://127.0.0.1:55038/
```

このツールによってブラウザー ウィンドウが起動され、対話型の Web UI が表示されます。 gRPC サービスは、gRPC リフレクションを使用して、自動的に検出されます。

![gRPCui Web UI](~/grpc/test-tools/static/grpcui.png)

## <a name="additional-resources"></a>その他のリソース

* [gRPCurl GitHub ホームページ](https://github.com/fullstorydev/grpcurl)
* [gRPCui GitHub ホームページ](https://github.com/fullstorydev/grpcui)
* [`Grpc.AspNetCore.Server.Reflection`](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection)
