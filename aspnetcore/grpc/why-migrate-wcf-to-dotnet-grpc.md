---
title: ASP.NET Core gRPC に WCF を移行する理由
author: markrendle
description: この記事では、最新のアーキテクチャおよびプラットフォームへの移行を望んでいる Windows Communication Foundation (WCF) 開発者に ASP.NET Core gRPC が適している理由の概要を説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: wpickett
ms.date: 09/02/2020
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
uid: grpc/wcf
ms.openlocfilehash: 26629b4aa5510f4ef5f53f57b64e45f6c32d4014
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2021
ms.locfileid: "93058689"
---
# <a name="grpc-for-windows-communication-foundation-wcf-developers"></a>Windows Communication Foundation (WCF) 開発者向けの gRPC

この記事では、最新のアーキテクチャおよびプラットフォームへの移行を望んでいる Windows Communication Foundation (WCF) 開発者に ASP.NET Core gRPC が適している理由の概要を説明します。

## <a name="comparison-to-wcf"></a>WCF との比較

gRPC における実装およびアプローチは異なりますが、gRPC でのサービスの開発および利用のエクスペリエンスは、WCF 開発者にとって直観的なものとなります。 WCF と gRPC は、次に示す同じ目標を掲げた RPC (リモート プロシージャ コール) フレームワークです:

* クライアントとサーバーが同じプラットフォーム上にあるかのようにコードを書くことができるようにすること。
* ポータブル ネットワーク API の簡素化を実現すること。

この 2 つのプラットフォームはインターフェイスを宣言するプロセスはそれぞれ異なりますが、インターフェイスの宣言および実装の要件を共有します。 gRPC がサポートする多くの種類の RPC 呼び出しは、WCF サービスで使用可能なバインディングに適切にマップされます。 詳細と例については、「[WCF ソリューションを gRPC に移行する](/dotnet/architecture/grpc-for-wcf-developers/migrate-wcf-to-grpc)」を参照してください。

## <a name="benefits-of-grpc"></a>gRPC の利点

次の理由により、gRPC は他のアプローチよりも優れたフレームワークを提供します。

### <a name="performance"></a>パフォーマンス

gRPC では HTTP/2 が使用されます。 HTTP/1.1 とは対照的に、HTTP/2 は次のとおりです。

* より小規模で高速なバイナリ プロトコルです。
* コンピューターが解析を行う場合により効率的です。
* 単一の接続での多重化要求がサポートされています。 多重化すると、複数の要求を単一の接続で送信することができ、要求が相互にブロックし合うことはありません。 HTTP/1.1 では、ブロックは "ヘッドオブライン (HOL) ブロック" と呼ばれています。

gRPC によって、効率的なバイナリ形式である Protobuf が使用され、メッセージがシリアル化されます。 Protobuf メッセージは次のとおりです。
* シリアル化および逆シリアル化が迅速に行われます。
* 使用する帯域幅がテキストベースの形式よりも小さくなります。 

gRPC は、帯域幅に制限があるモバイルデバイスおよびネットワークに適したソリューションです。

### <a name="interoperability"></a>相互運用性

主要なすべてのプログラミング言語とプラットフォーム (.NET、Java、Python、Go、C++、Node.js、Swift、Dart、Ruby、PHP など) 用に gRPC ツールおよびライブラリが用意されています。 Protobuf バイナリ ワイヤ形式と、各プラットフォームでの効率的なコード生成により、開発者はクロスプラットフォームのパフォーマンスに優れたアプリを構築できます。

### <a name="usability-and-productivity"></a>使いやすさと生産性

gRPC は包括的な RPC ソリューションです。 複数の言語とプラットフォームで一貫して動作します。 また、優れたツールも用意されていて、定型コードの大部分が自動的に生成されます。 WCF と同様に、gRPC でもメッセージと厳密に型指定されたクライアントが自動的に生成されます。 開発者の時間は解放され、ビジネスロジックに集中できるようになります。

### <a name="streaming"></a>ストリーム

gRPC には、WCF の全二重サービスと同様の機能を提供する、完全な双方向ストリーミングが用意されています。 gRPC ストリーミングは、通常のインターネット接続、ロード バランサー、およびサービス メッシュを介して動作させることができます。

### <a name="deadlines-timeouts-and-cancellation"></a>期限、タイムアウト、およびキャンセル

gRPC を使用すると、RPC が終了するまでの最長時間をクライアントで指定できます。 指定した期限を超過した場合、サーバーではクライアントに関係なく操作をキャンセルすることができます。 期限とキャンセルを後続の gRPC 呼び出しを通じて伝達すると、リソース使用量の制限を楽に適用することができます。 クライアントでは、期限を超過したとき、または必要に応じてそれよりも早く操作を停止することができます。 たとえば、クライアントによって、ユーザーの介入を理由に操作を停止することができます。

### <a name="security"></a>セキュリティ

gRPC で TLS と HTTP/2 を使用して、クライアントとサーバーの間にエンドツーエンドの暗号化された接続を提供できます。 クライアント証明書の認証がサポートされているので、クライアントとサーバーの間のセキュリティと信頼性が向上します。

## <a name="grpc-as-a-migration-path-for-wcf-to-net-core-and-net-5"></a>WCF から .NET Core および .NET 5 への移行パスとしての gRPC

.NET Core と .NET 5 は、さまざまなプラットフォームにわたってサービスを提供したい開発者に Microsoft がリモート通信ソリューションを提供する方法に変化をもたらします。 .NET Core と .NET 5 により、[WCF サービスの呼び出し](/dotnet/core/additional-tools/wcf-web-service-reference-guide)はサポートされますが、WCF をホストするためのサーバー側のサポートは提供されません。

WCF アプリを最新化するためのパスとして推奨されるのは、次の 2 つです。

* gRPC は最新のテクノロジに基づいて構築されていて、RPC アプリの開発者コミュニティで最も人気のある選択肢として登場しています。 .NET Core 3.0 以降、最新の .NET プラットフォームによって gRPC に対する優れたサポートが提供されています。 gRPC を使用するように WCF サービスを移行すると、最新のアプリで必要とされる RPC 機能、パフォーマンス、および相互運用性を容易に実現できます。

* [CoreWCF](https://github.com/CoreWCF/CoreWCF) とは、WCF サービスをホストするためのサポートを .NET Core および .NET 5 に導入するためのコミュニティの取り組みです。 プレビュー リリースが利用できます。プロジェクトは実稼働に対応するための準備に取り組んでいます。 CoreWCF でサポートされるのは、WCF の機能のサブセットのみとなっているため、それを使用できるように移行する .NET Framework アプリによって、コードの変更とテストが正常に行われる必要があります。 WCF サービスを呼び出す既存のクライアントとの互換性がアプリで維持される必要がある場合、CoreWCF は適切な選択肢となります。

## <a name="get-started"></a>作業開始

ASP.NET Core で gRPC サービスを構築する場合の WCF 開発者向けの詳細なガイダンスについては、「[WCF 開発者向け ASP.NET Core gRPC](/dotnet/architecture/grpc-for-wcf-developers)」を参照してください。
