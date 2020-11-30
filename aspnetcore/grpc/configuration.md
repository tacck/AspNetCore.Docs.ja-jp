---
title: .NET 構成のための gRPC
author: jamesnk
description: .NET アプリのために gRPC を構成する方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 11/23/2020
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
uid: grpc/configuration
ms.openlocfilehash: 617c042c628dc431391f39c2ecb2d2f9c9463fa5
ms.sourcegitcommit: aa85f2911792a1e4783bcabf0da3b3e7e218f63a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/23/2020
ms.locfileid: "95417592"
---
# <a name="grpc-for-net-configuration"></a>.NET 構成のための gRPC

## <a name="configure-services-options"></a>サービス オプションを構成する

gRPC サービスは、*Startup.cs* の `AddGrpc` によって構成されます。 次の表では、gRPC サービスを構成するためのオプションについて説明します。

| オプション | 既定値 | 説明 |
| ------ | ------------- | ----------- |
| MaxSendMessageSize | `null` | サーバーから送信できる最大メッセージ サイズ (バイト単位)。 構成された最大メッセージ サイズを超えるメッセージを送信しようとすると、例外が発生します。 `null` に設定する場合、メッセージ サイズは制限されません。 |
| MaxReceiveMessageSize | 4 MB | サーバーで受信できる最大メッセージ サイズ (バイト単位)。 サーバーでこの制限を超えるメッセージを受信すると、例外がスローされます。 この値を大きくすると、サーバーはより大きなメッセージを受け取れますが、メモリの消費に悪影響を与える可能性があります。 `null` に設定する場合、メッセージ サイズは制限されません。 |
| EnableDetailedErrors | `false` | `true` の場合、サービス メソッドで例外がスローされると、詳細な例外メッセージがクライアントに返されます。 既定値は、`false` です。 `EnableDetailedErrors` を `true` に設定すると、機密情報が漏洩する可能性があります。 |
| CompressionProviders | gzip | メッセージの圧縮と圧縮解除に使用される圧縮プロバイダーのコレクション。 カスタム圧縮プロバイダーを作成し、コレクションに追加することができます。 既定で構成されているプロバイダーは、**gzip** 圧縮をサポートしています。 |
| <span style="word-break:normal;word-wrap:normal">ResponseCompressionAlgorithm</span> | `null` | サーバーから送信されるメッセージの圧縮に使用される圧縮アルゴリズム。 このアルゴリズムは、`CompressionProviders` の圧縮プロバイダーと一致している必要があります。 アルゴリズムで応答を圧縮するには、そのアルゴリズムをサポートしていることをクライアントが **grpc-accept-encoding** ヘッダーで送信することによって、そのことを示す必要があります。 |
| ResponseCompressionLevel | `null` | サーバーから送信されるメッセージの圧縮に使用される圧縮レベル。 |
| インターセプター | None | 各 gRPC 呼び出しで実行されるインターセプターのコレクション。 インターセプターは、登録されている順序で実行されます。 グローバルに構成されたインターセプターは、1 つのサービスに対してインターセプターが構成される前に実行されます。 gRPC インターセプターの詳細については、「[gRPC インターセプターとミドルウェア](xref:grpc/migration#grpc-interceptors-vs-middleware)」を参照してください。 |
| IgnoreUnknownServices | `false` | `true` の場合、不明なサービスおよびメソッドへの呼び出しでは **UNIMPLEMENTED** 状態は返されず、要求は ASP.NET Core に登録された次のミドルウェアに渡されます。 |

`Startup.ConfigureServices` の `AddGrpc` 呼び出しに対して options デリゲートを指定することで、すべてのサービスに対してオプションを構成できます。

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup.cs?name=snippet)]

1 つのサービスのオプションは、`AddGrpc` で提供されるグローバル オプションをオーバーライドします。また、`AddServiceOptions<TService>` を使用して構成することができます。

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup2.cs?name=snippet)]

## <a name="configure-client-options"></a>クライアント オプションを構成する

gRPC のクライアント構成は `GrpcChannelOptions` で設定されます。 次の表では、gRPC チャネルを構成するためのオプションについて説明します。

| オプション | 既定値 | 説明 |
| ------ | ------------- | ----------- |
| HttpHandler | 新しいインスタンス | gRPC 呼び出しを行うために使用される `HttpMessageHandler`。 カスタム `HttpClientHandler` を構成したり、gRPC 呼び出しの HTTP パイプラインに追加のハンドラーを加えたりするために、クライアントを設定できます。 `HttpMessageHandler` が指定されていない場合、自動廃棄のチャネルのために新しい `HttpClientHandler` インスタンスが作成されます。 |
| HttpClient | `null` | gRPC 呼び出しを行うために使用される `HttpClient`。 この設定は、`HttpHandler` に代わる方法です。 |
| DisposeHttpClient | `false` | `true` に設定し、`HttpMessageHandler` または `HttpClient` が指定されている場合、`GrpcChannel` が廃棄されたときに (該当する) `HttpHandler` または `HttpClient` が廃棄されます。 |
| LoggerFactory | `null` | gRPC 呼び出しに関する情報をログに記録するため、クライアントによって使用される `LoggerFactory`。 `LoggerFactory` インスタンスは、依存関係の挿入から解決することも、`LoggerFactory.Create` を使用して作成することもできます。 ログ記録を構成する例については、「<xref:grpc/diagnostics#grpc-client-logging>」を参照してください。 |
| MaxSendMessageSize | `null` | クライアントから送信できる最大メッセージ サイズ (バイト単位)。 構成された最大メッセージ サイズを超えるメッセージを送信しようとすると、例外が発生します。 `null` に設定する場合、メッセージ サイズは制限されません。 |
| <span style="word-break:normal;word-wrap:normal">MaxReceiveMessageSize</span> | 4 MB | クライアントで受信できる最大メッセージ サイズ (バイト単位)。 クライアントでこの制限を超えるメッセージを受信すると、例外がスローされます。 この値を大きくすると、クライアントはより大きなメッセージを受け取れますが、メモリの消費に悪影響を与える可能性があります。 `null` に設定する場合、メッセージ サイズは制限されません。 |
| 資格情報: | `null` | `ChannelCredentials` のインスタンス。 資格情報は、認証メタデータを gRPC 呼び出しに追加するために使用します。 |
| CompressionProviders | gzip | メッセージの圧縮と圧縮解除に使用される圧縮プロバイダーのコレクション。 カスタム圧縮プロバイダーを作成し、コレクションに追加することができます。 既定で構成されているプロバイダーは、**gzip** 圧縮をサポートしています。 |
| ThrowOperationCanceledOnCancellation | `false` | `true` に設定されている場合、呼び出しが取り消されたとき、または期限を超えたときに、クライアントから <xref:System.OperationCanceledException> がスローされます。 |

コード例を次に示します。

* チャネルで送受信する最大メッセージ サイズを設定します。
* クライアントを作成します。

[!code-csharp[](~/grpc/configuration/sample/Program.cs?name=snippet&highlight=3-8)]

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a>その他の技術情報

* <xref:grpc/aspnetcore>
* <xref:grpc/client>
* <xref:grpc/diagnostics>
* <xref:tutorials/grpc/grpc-start>
