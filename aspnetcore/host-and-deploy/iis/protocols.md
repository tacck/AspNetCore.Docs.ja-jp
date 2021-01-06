---
title: IIS 上で HTTP/2 と共に ASP.NET Core を使用する
author: rick-anderson
description: IIS で HTTP/2 機能を使用する方法について説明します。
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
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
uid: host-and-deploy/iis/protocols
ms.openlocfilehash: 4d0dc1239467e92c4127f631709c2ffd6098bfc5
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2021
ms.locfileid: "93057415"
---
# <a name="use-aspnet-core-with-http2-on-iis"></a>IIS 上で HTTP/2 と共に ASP.NET Core を使用する

作成者: [Justin Kotalik](https://github.com/jkotalik)

[HTTP/2](https://httpwg.org/specs/rfc7540.html) は、次の IIS 展開シナリオでの ASP.NET Core でサポートされます。

* Windows Server 2016 以降、Windows 10 以降
* IIS 10 以降
* TLS 1.2 以降の接続
* [アウト プロセス](xref:host-and-deploy/iis/index#out-of-process-hosting-model)でホスティングしている場合: 一般向けのエッジ サーバーでは HTTP/2 を使用しますが、[Kestrel サーバー](xref:fundamentals/servers/kestrel)へのリバース プロキシ接続では HTTP/1.1 を使用します。

インプロセスの展開の場合、HTTP/2 接続が確立されると、[`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) によって `HTTP/2` が報告されます。 アウトプロセスの展開の場合、HTTP/2 接続が確立されると、[`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) によって `HTTP/1.1` が報告されます。

インプロセスおよびアウトプロセス ホスティング モデルの詳細については、<xref:host-and-deploy/aspnet-core-module> をご覧ください。

HTTPS/TLS 接続においては、既定で HTTP/2 が有効にされます。 HTTP/2 接続が確立されない場合、接続は HTTP/1.1 にフォールバックします。 IIS 展開での HTTP/2 構成の詳細については、[IIS での HTTP/2](/iis/get-started/whats-new-in-iis-10/http2-on-iis) に関するページを参照してください。

## <a name="advanced-http2-features-to-support-grpc"></a>gRPC をサポートする高度な HTTP/2 機能

IIS 内の追加の HTTP/2 機能により、gRPC がサポートされています。これには、応答トレーラーやリセット フレームの送信に関するサポートが含まれます。

IIS 上で gRPC を実行するための要件:

* インプロセス ホスティング。
* Windows 10、OS Build 20300.1000 以降。 Windows Insider ビルドの使用が必要になる場合があります。
* TLS 1.2 以降の接続

### <a name="trailers"></a>予告編

[!INCLUDE[](~/includes/trailers.md)]

### <a name="reset"></a>Reset

[!INCLUDE[](~/includes/reset.md)]
