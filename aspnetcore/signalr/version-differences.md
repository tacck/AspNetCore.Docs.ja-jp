---
title: と ASP.NET Core SignalRの違いSignalR
author: bradygaster
description: と ASP.NET Core SignalRの違いSignalR
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.date: 11/21/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/version-differences
ms.openlocfilehash: 58d134ae971bace178561322f1c8a6351432be03
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82772580"
---
# <a name="differences-between-aspnet-signalr-and-aspnet-core-signalr"></a>ASP.NET SignalR と ASP.NET Core SignalR の違い

ASP.NET Core SignalR は、ASP.NET SignalR のクライアントまたはサーバーと互換性がありません。 この記事では ASP.NET Core SignalR で削除または変更された機能について詳しく説明します。

## <a name="how-to-identify-the-signalr-version"></a>SignalR のバージョンを識別する方法

::: moniker range=">= aspnetcore-3.0"

|                      | ASP.NET SignalR | ASP.NET Core SignalR |
| -------------------- | --------------- | -------------------- |
| サーバー NuGet パッケージ | [SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/) | [なし] : [AspNetCore](xref:fundamentals/metapackage-app)共有フレームワークに含まれています。 |
| クライアント NuGet パッケージ | [SignalR (Microsoft AspNet. クライアント)](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)<br>[SignalR (Microsoft AspNet)](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/) | [AspNetCore. SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) |
| JavaScript クライアント npm パッケージ | [signalr](https://www.npmjs.com/package/signalr) | [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) |
| Java クライアント | [GitHub リポジトリ](https://github.com/SignalR/java-client)(非推奨)  | Maven パッケージ[signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr) |
| サーバーアプリの種類 | ASP.NET (System.web) または OWIN 自己ホスト | ASP.NET Core |
| サポートされているサーバープラットフォーム | .NET Framework 4.5 以降 | .NET Core 3.0 以降 |

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

|                      | ASP.NET SignalR | ASP.NET Core SignalR |
| -------------------- | --------------- | -------------------- |
| サーバー NuGet パッケージ | [Microsoft AspNet。SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/) | [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.net Core)<br>[AspNetCore。SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/) (.NET Framework) |
| クライアント NuGet パッケージ | [Microsoft AspNet。SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)<br>[Microsoft AspNet。SignalR.NODE.JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/) | [AspNetCore。SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) |
| JavaScript クライアント npm パッケージ | [signalr](https://www.npmjs.com/package/signalr) | [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) |
| Java クライアント | [GitHub リポジトリ](https://github.com/SignalR/java-client)(非推奨)  | Maven パッケージ[signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr) |
| サーバーアプリの種類 | ASP.NET (System.web) または OWIN 自己ホスト | ASP.NET Core |
| サポートされているサーバープラットフォーム | .NET Framework 4.5 以降 | .NET Framework 4.6.1 以降<br>.NET Core 2.1 以降 |

::: moniker-end

## <a name="feature-differences"></a>機能の違い

### <a name="automatic-reconnects"></a>自動再接続

::: moniker range=">= aspnetcore-3.0"

ASP.NET SignalRの場合:

* 既定ではSignalR 、は、接続が切断された場合にサーバーへの再接続を試みます。 

ASP.NET Core SignalR:

* 自動再接続は、 [.net クライアント](xref:signalr/dotnet-client#automatically-reconnect)と[JavaScript クライアント](xref:signalr/javascript-client#automatically-reconnect)の両方にオプトインされています。

```csharp
HubConnection connection = new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chatHub"))
    .WithAutomaticReconnect()
    .Build();
```

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core 3.0 より前でSignalRは、は自動再接続をサポートしていません。 クライアントが切断されている場合、ユーザーは新しい接続を明示的に開始して再接続する必要があります。 ASP.NET SignalRではSignalR 、接続が切断された場合、はサーバーへの再接続を試みます。

::: moniker-end

### <a name="protocol-support"></a>プロトコルのサポート

ASP.NET Core SignalRは、 [messagepack](xref:signalr/messagepackhubprotocol)に基づく新しいバイナリプロトコルだけでなく、JSON もサポートしています。 さらに、カスタムプロトコルを作成することもできます。

### <a name="transports"></a>トランスポート

永続的フレーム転送は ASP.NET Core SignalRではサポートされていません。

## <a name="differences-on-the-server"></a>サーバーの相違点

ASP.NET Core SignalRサーバー側ライブラリは、 [AspNetCore](xref:fundamentals/metapackage-app)に含まれています。これは、と MVC プロジェクトの両方Razorの**ASP.NET Core Web アプリケーション**テンプレートで使用されます。

ASP.NET Core SignalRは ASP.NET Core ミドルウェアです。 のを<xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddSignalR%2A> `Startup.ConfigureServices`呼び出すことによって構成する必要があります。

```csharp
services.AddSignalR()
```

::: moniker range=">= aspnetcore-3.0"

ルーティングを構成するには、メソッド内の<xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> `Startup.Configure`メソッド呼び出し内のハブにルートをマップします。

```csharp
app.UseRouting();

app.UseEndpoints(endpoints =>
{
    endpoints.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

ルーティングを構成するには、メソッド内の<xref:Microsoft.AspNetCore.Builder.SignalRAppBuilderExtensions.UseSignalR%2A> `Startup.Configure`メソッド呼び出し内のハブにルートをマップします。

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

### <a name="sticky-sessions"></a>固定セッション

ASP.NET SignalRのスケールアウトモデルを使用すると、クライアントは再接続し、ファーム内の任意のサーバーにメッセージを送信できます。 ASP.NET Core SignalRでは、クライアントは接続中に同じサーバーと通信する必要があります。 Redis を使用したスケールアウトの場合、これは固定セッションが必要であることを意味します。 [Azure SignalRサービス](/azure/azure-signalr/)を使用したスケールアウトでは、サービスがクライアントへの接続を処理するため、固定セッションは必要ありません。

### <a name="single-hub-per-connection"></a>接続ごとに1つのハブ

ASP.NET Core SignalRでは、接続モデルが単純化されています。 複数のハブへのアクセスを共有するために使用される単一の接続ではなく、1つのハブに直接接続されます。

### <a name="streaming"></a>ストリーミング

ASP.NET Core SignalRは、ハブからクライアントへの[データのストリーミング](xref:signalr/streaming)をサポートするようになりました。

### <a name="state"></a>State

クライアントとハブ (多くの場合はと呼ばれ`HubState`ます) の間で任意の状態を渡す機能は削除されており、進行状況メッセージもサポートされています。 現時点では、対応するハブプロキシはありません。

### <a name="persistentconnection-removal"></a>PersistentConnection の削除

ASP.NET Core SignalRでは、 [PersistentConnection](https://docs.microsoft.com/previous-versions/aspnet/jj919047(v%3dvs.118))クラスは削除されています。

### <a name="globalhost"></a>GlobalHost

ASP.NET Core には、フレームワークに依存関係の挿入 (DI) が組み込まれています。 サービスは DI を使用して[HubContext](xref:signalr/hubcontext)にアクセスできます。 を`GlobalHost` `HubContext`取得するために ASP.NET SignalR SignalRで使用されるオブジェクトが ASP.NET Core に存在しません。

### <a name="hubpipeline"></a>HubPipeline

ASP.NET Core SignalRはモジュールを`HubPipeline`サポートしていません。

## <a name="differences-on-the-client"></a>クライアントの相違点

### <a name="typescript"></a>TypeScript

ASP.NET Core SignalRクライアントは[TypeScript](https://www.typescriptlang.org/)で記述されています。 Javascript[クライアント](xref:signalr/javascript-client)を使用する場合は、javascript または TypeScript で記述できます。

### <a name="the-javascript-client-is-hosted-at-npm"></a>JavaScript クライアントは npm でホストされます。

::: moniker range=">= aspnetcore-3.0"

ASP.NET のバージョンでは、JavaScript クライアントは Visual Studio の NuGet パッケージを使用して取得されました。 ASP.NET Core のバージョンでは、 [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) npm パッケージに JavaScript ライブラリが含まれています。 このパッケージは、 **ASP.NET Core Web アプリケーション**テンプレートには含まれていません。 Npm パッケージを取得してインストール`@microsoft/signalr`するには、npm を使用します。

```console
npm init -y
npm install @microsoft/signalr
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

ASP.NET のバージョンでは、JavaScript クライアントは Visual Studio の NuGet パッケージを使用して取得されました。 ASP.NET Core のバージョンでは、 [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) npm パッケージに JavaScript ライブラリが含まれています。 このパッケージは、 **ASP.NET Core Web アプリケーション**テンプレートには含まれていません。 Npm パッケージを取得してインストール`@aspnet/signalr`するには、npm を使用します。

```console
npm init -y
npm install @aspnet/signalr
```

::: moniker-end

### <a name="jquery"></a>jQuery

JQuery への依存関係は削除されましたが、プロジェクトは引き続き jQuery を使用できます。

### <a name="internet-explorer-support"></a>Internet Explorer のサポート

ASP.NET Core SignalRには、Microsoft internet explorer 11 以降 ( SignalR ASP.NET でサポートされている microsoft internet explorer 8 以降) が必要です。

### <a name="javascript-client-method-syntax"></a>JavaScript クライアントメソッドの構文

::: moniker range=">= aspnetcore-3.0"

JavaScript の構文は、ASP.NET バージョンのから変更SignalRされました。 `$connection`オブジェクトを使用するのではなく、 [HubConnectionBuilder](/javascript/api/@aspnet/signalr/hubconnectionbuilder) API を使用して接続を作成します。

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

[On](/javascript/api/@microsoft/signalr/HubConnection#on)メソッドを使用して、ハブが呼び出すことができるクライアントメソッドを指定します。

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

JavaScript の構文は、ASP.NET バージョンのから変更SignalRされました。 `$connection`オブジェクトを使用するのではなく、 [HubConnectionBuilder](/javascript/api/@microsoft/signalr/hubconnectionbuilder) API を使用して接続を作成します。

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

[On](/javascript/api/@aspnet/signalr/HubConnection#on)メソッドを使用して、ハブが呼び出すことができるクライアントメソッドを指定します。

::: moniker-end

```javascript
connection.on("ReceiveMessage", (user, message) => {
    const msg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
    const encodedMsg = `${user} says ${msg}`;
    console.log(encodedMsg);
});
```

クライアントメソッドを作成したら、ハブ接続を開始します。 [Catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch)メソッドをチェーンして、エラーをログに記録または処理します。

```javascript
connection.start().catch(err => console.error(err));
```

### <a name="hub-proxies"></a>ハブプロキシ

::: moniker range=">= aspnetcore-3.0"

ハブプロキシが自動的に生成されなくなりました。 代わりに、メソッド名が文字列として[invoke](/javascript/api/@microsoft/signalr/hubconnection#invoke) API に渡されます。

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

ハブプロキシが自動的に生成されなくなりました。 代わりに、メソッド名が文字列として[invoke](/javascript/api/@aspnet/signalr/hubconnection#invoke) API に渡されます。

::: moniker-end

### <a name="net-and-other-clients"></a>.NET とその他のクライアント

[AspNetCore.SignalR.クライアント](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client)NuGet パッケージには、ASP.NET Core SignalR用の .net クライアントライブラリが含まれています。

ハブへ<xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder>の接続のインスタンスを作成して構築するには、を使用します。

```csharp
connection = new HubConnectionBuilder()
    .WithUrl("url")
    .Build();
```

## <a name="scaleout-differences"></a>スケールアウトの相違点

ASP.NET SignalRは SQL Server と Redis をサポートしています。 ASP.NET Core SignalRではSignalR 、Azure サービスと Redis がサポートされています。

### <a name="aspnet"></a>ASP.NET

* [SignalRAzure Service Bus によるスケールアウト](/aspnet/signalr/overview/performance/scaleout-with-windows-azure-service-bus)
* [SignalRRedis によるスケールアウト](/aspnet/signalr/overview/performance/scaleout-with-redis)
* [SignalRSQL Server によるスケールアウト](/aspnet/signalr/overview/performance/scaleout-with-sql-server)

### <a name="aspnet-core"></a>ASP.NET Core

* [Azure SignalRサービス](/azure/azure-signalr/)
* [Redis バックプレーン](xref:signalr/redis-backplane)

## <a name="additional-resources"></a>その他のリソース

* [ハブ](xref:signalr/hubs)
* [JavaScript クライアント](xref:signalr/javascript-client)
* [.NET クライアント](xref:signalr/dotnet-client)
* [サポートされているプラットフォーム](xref:signalr/supported-platforms)
