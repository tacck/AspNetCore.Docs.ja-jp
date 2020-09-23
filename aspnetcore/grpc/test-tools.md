---
title: gRPC ツールを使用したサービスのテスト
author: jamesnk
description: gRPC ツールを使用してサービスをテストする方法について説明します。 gRPCurl は、gRPC サービスと対話するためのコマンドライン ツールです。 gRPCui は、対話型の Web UI です。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/09/2020
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
uid: grpc/test-tools
ms.openlocfilehash: ba51d9b5db2e9fbc7583856d79ab8658eff9b586
ms.sourcegitcommit: a07f83b00db11f32313045b3492e5d1ff83c4437
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/15/2020
ms.locfileid: "90594374"
---
# <a name="test-services-with-grpc-tools"></a>gRPC ツールを使用したサービスのテスト

作成者: [James Newton-King](https://twitter.com/jamesnk)

ツールは gRPC で使用できます。これにより、開発者は、クライアント アプリをビルドせずにサービスをテストできます。 [gRPCurl](https://github.com/fullstorydev/grpcurl) は、gRPC サービスとの対話を提供するコマンドライン ツールです。 [gRPCui](https://github.com/fullstorydev/grpcui) では、gRPC 用の対話型 Web UI が追加されます。

この記事では、次の方法について説明します。

* gRPCurl と gRPCui をダウンロードしてインストールします。
* gRPC ASP.NET Core を使用して、gRPC リフレクションをセットアップします。
* `grpcurl` を使用して、gRPC サービスの検出とテストを行います。
* `grpcui` を使用して、ブラウザー経由で gRPC サービスと対話します。

## <a name="about-grpcurl"></a>gRPCurl について

gRPCurl は、gRPC コミュニティによって作成されたコマンドライン ツールです。 次のような機能があります。

* ストリーミング サービスを含む、gRPC サービスの呼び出し。
* [gRPC リフレクション](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md)を使用したサービスの検出。
* gRPC サービスの一覧表示と説明。
* セキュリティで保護されたサーバー (TLS) と安全ではない (プレーンテキスト) サーバーの使用。

`grpcurl` のダウンロードとインストールの詳細については、[gRPCurl GitHub ホームページ](https://github.com/fullstorydev/grpcurl#installation)を参照してください。

## <a name="setup-grpc-reflection"></a>gRPC リフレクションの設定

`grpcurl` では、サービスを呼び出す前に、サービスの Protobuf コントラクトの認識が行われている必要があります。 この作業を実行する 2 つの方法があります。

* gRPC リフレクションを使用して、サービス コントラクトを検出します。
* コマンドライン引数に *.proto* ファイルを指定します。

gRPC リフレクションとサービス検出で gRPCurl を使用する方が簡単です。 gRPC ASP.NET Core には、[Grpc.AspNetCore.Server.Reflection](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection) パッケージを使用した gRPC リフレクションのサポートが組み込まれています。 アプリでリフレクションを構成するには、次を実行します。

* `Grpc.AspNetCore.Server.Reflection` パッケージ参照を追加します。
* *Startup.cs* にリフレクションを登録します。
  * `AddGrpcReflection` でリフレクションを有効にするサービスを登録します。
  * `MapGrpcReflectionService` でリフレクション サービス エンドポイントを追加します。

[!code-csharp[](~/grpc/test-tools/Startup.cs?name=snippet_1&highlight=4,14)]

## <a name="use-grpcurl"></a>`grpcurl` を使用します

`-help` 引数では、`grpcurl` コマンドライン オプションについて説明しています。

```powershell
> grpcurl.exe -help
```

### <a name="discover-services"></a>サービスの検出

サーバーによって定義されたサービスを表示するには、`describe` 動詞を使用します。

```powershell
> grpcurl.exe localhost:5001 describe
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

* サーバー `localhost:5001` で動詞 `describe` を実行します。
* gRPC リフレクションによって返されたサービスとメソッドを出力します。
  * `Greeter` は、アプリによって実装されるサービスです。
  * `ServerReflection` は、`Grpc.AspNetCore.Server.Reflection` パッケージによって追加されたサービスです。

`describe` と、サービス名、メソッド名、またはメッセージ名を組み合わせて、詳細を表示します。

```powershell
> grpcurl.exe localhost:5001 describe greet.HelloRequest
greet.HelloRequest is a message:
message HelloRequest {
  string name = 1;
}
```

### <a name="call-grpc-services"></a>gRPC サービスの呼び出し

要求メッセージを表す JSON 引数と共にサービス名とメソッド名を指定して、gRPC サービスを呼び出します。 JSON は Protobuf に変換され、サービスに送信されます。

```powershell
> grpcurl.exe -d '{ \"name\": \"World\" }' localhost:5001 greet.Greeter/SayHello
{
  "message": "Hello World"
}
```

上記の例の場合:

* `-d` 引数によって、JSON を含む要求メッセージが指定されます。 この引数は、サーバー アドレスとメソッド名の前で指定する必要があります。
* `greeter.Greeter` サービスの `SayHello` メソッドを呼び出します。
* 応答メッセージを JSON として出力します。

## <a name="about-grpcui"></a>gRPCui について

gRPCui は、gRPC の対話型 Web UI です。 これは、gRPCurl の上にビルドされ、Postman などの HTTP ツールと同様に、gRPC サービスを検出してテストするための GUI を提供します。

`grpcui` のダウンロードとインストールの詳細については、[gRPCui GitHub ホームページ](https://github.com/fullstorydev/grpcui#installation)を参照してください。

## <a name="using-grpcui"></a>`grpcui` の使用

対話するサーバー アドレスを使用して、`grpcui` を引数として実行します。

```powershell
> grpcui.exe localhost:5001
gRPC Web UI available at http://127.0.0.1:55038/
```

このツールを使用すると、ブラウザー ウィンドウが開き、対話型 Web UI が表示されます。 gRPC サービスは、gRPC リフレクションを使用して、自動的に検出されます。

![gRPCui Web UI](~/grpc/test-tools/static/grpcui.png)

## <a name="additional-resources"></a>その他のリソース

* [gRPCurl GitHub ホームページ](https://github.com/fullstorydev/grpcurl)
* [gRPCui GitHub ホームページ](https://github.com/fullstorydev/grpcui)
* [Grpc.AspNetCore.Server.Reflection](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection)
