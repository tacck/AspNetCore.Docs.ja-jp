---
title: ASP.NET Core Blazor の環境
author: guardrex
description: Blazor WebAssembly アプリの環境を設定する方法など、Blazor の環境について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
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
uid: blazor/fundamentals/environments
ms.openlocfilehash: 61d46e0bd83d8bd82bf7faaf9d8f2fecbacc2ffa
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056037"
---
# <a name="aspnet-core-no-locblazor-environments"></a>ASP.NET Core Blazor の環境

> [!NOTE]
> このトピックの対象は、Blazor WebAssembly です。 ASP.NET Core アプリの構成に関する一般的なガイダンスについては、「<xref:fundamentals/environments>」を参照してください。

アプリをローカルで実行する場合、環境は既定で開発に設定されます。 アプリが発行されると、環境は既定で実稼働になります。

ホストされている Blazor WebAssembly アプリは、`blazor-environment` ヘッダーを追加して環境をブラウザーに送信するミドルウェアを介して、サーバーから環境を取得します。 ヘッダーの値は環境です。 ホストされた Blazor アプリとサーバー アプリによって、同じ環境が共有されます。 環境の構成方法などの詳細については、「<xref:fundamentals/environments>」を参照してください。

ローカルで実行されているスタンドアロン アプリの場合、開発サーバーでは `blazor-environment` ヘッダーが追加され、開発環境が指定されます。 他のホスト環境の環境を指定するには、`blazor-environment` ヘッダーを追加します。

次の IIS の例では、発行された `web.config` ファイルにカスタム ヘッダーを追加しています。 `web.config` ファイルは `bin/Release/{TARGET FRAMEWORK}/publish` フォルダー内に配置されます。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <system.webServer>

    ...

    <httpProtocol>
      <customHeaders>
        <add name="blazor-environment" value="Staging" />
      </customHeaders>
    </httpProtocol>
  </system.webServer>
</configuration>
```

> [!NOTE]
> アプリが `publish` フォルダーに発行されたときに上書きされない IIS 用のカスタム `web.config` ファイルを使用するには、「<xref:blazor/host-and-deploy/webassembly#use-a-custom-webconfig>」を参照してください。

<xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> を挿入し、<xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> プロパティを読み取ることで、コンポーネント内のアプリの環境を取得します。

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

起動時に、<xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> では、<xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> プロパティを使用して <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> が公開されます。これにより、開発者は環境固有のロジックをコードに含めることができます。

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

次の便利な拡張メソッドを使用すると、現在の環境で開発、運用、ステージング、およびカスタムの環境名を確認できます。

* `IsDevelopment()`
* `IsProduction()`
* `IsStaging()`
* `IsEnvironment("{ENVIRONMENT NAME}")`

```csharp
if (builder.HostEnvironment.IsStaging())
{
    ...
};

if (builder.HostEnvironment.IsEnvironment("Custom"))
{
    ...
};
```

<xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> プロパティは、<xref:Microsoft.AspNetCore.Components.NavigationManager> サービスを利用できないときの起動時に使用できます。

## <a name="additional-resources"></a>その他の技術情報

* <xref:fundamentals/environments>
