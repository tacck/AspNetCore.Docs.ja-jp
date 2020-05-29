---
title:'ASP.NET Core Blazor ホスティング モデルの構成' author: description:'Razor コンポーネントを Razor Pages および MVC アプリに統合する方法など、Blazor ホスティング モデルの構成について学習します。'
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a>ASP.NET Core Blazor ホスティング モデルの構成

作成者: [Daniel Roth](https://github.com/danroth27)、[Luke Latham](https://github.com/guardrex)

この記事では、ホスティング モデルの構成について説明します。

## <a name="blazor-webassembly"></a>Blazor WebAssembly

### <a name="environment"></a>環境

アプリをローカルで実行する場合、環境は既定で開発に設定されます。 アプリが発行されると、環境は既定で実稼働になります。

ホストされている Blazor WebAssembly アプリは、`blazor-environment` ヘッダーを追加して環境をブラウザーに送信するミドルウェアを介して、サーバーから環境を取得します。 ヘッダーの値は環境です。 ホストされた Blazor アプリとサーバー アプリによって、同じ環境が共有されます。 環境の構成方法などの詳細については、「<xref:fundamentals/environments>」を参照してください。

ローカルで実行されているスタンドアロン アプリの場合、開発サーバーでは `blazor-environment` ヘッダーが追加され、開発環境が指定されます。 他のホスト環境の環境を指定するには、`blazor-environment` ヘッダーを追加します。

次の IIS の例では、発行された *web.config* ファイルにカスタム ヘッダーを追加しています。 *web.config* ファイルは、*bin/Release/{TARGET FRAMEWORK}/publish* フォルダーにあります。

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
> アプリが *publish* フォルダーに発行されたときに上書きされない IIS 用のカスタム *web.config* ファイルを使用するには、「ASP.NET Core Blazor WebAssembly をホストしてデプロイする<xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>」を参照してください。

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

### <a name="configuration"></a>構成

Blazor WebAssembly では、以下から構成を読み込みます。

* 既定のアプリ設定ファイル
  * *wwwroot/appsettings.json*
  * *wwwroot/appsettings.{ENVIRONMENT}.json*
* アプリによって登録されたその他の[構成プロバイダー](xref:fundamentals/configuration/index)。 すべてのプロバイダーが Blazor WebAssembly アプリに適しているわけではありません。 どのプロバイダーが Blazor WebAssembly でサポートされているかについては、「[Blazor WASM の構成プロバイダーの明確化 (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134)」で追跡されています。

> [!WARNING]
> Blazor WebAssembly アプリでの構成は、ユーザーに表示されます。 **アプリのシークレットや資格情報を構成に保存しないでください。**

構成プロバイダーの詳細については、「<xref:fundamentals/configuration/index>」を参照してください。

#### <a name="app-settings-configuration"></a>アプリ設定の構成

*wwwroot/appsettings.json*:

```json
{
  "message": "Hello from config!"
}
```

構成データにアクセスするために、コンポーネントに <xref:Microsoft.Extensions.Configuration.IConfiguration> インスタンスを挿入します。

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

#### <a name="provider-configuration"></a>プロバイダーの構成

次の例では、<xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> を使用して追加の構成を指定します。

`Program.Main`:

```csharp
using Microsoft.Extensions.Configuration.Memory;

...

var vehicleData = new Dictionary<string, string>()
{
    { "color", "blue" },
    { "type", "car" },
    { "wheels:count", "3" },
    { "wheels:brand", "Blazin" },
    { "wheels:brand:type", "rally" },
    { "wheels:year", "2008" },
};

var memoryConfig = new MemoryConfigurationSource { InitialData = vehicleData };

...

builder.Configuration.Add(memoryConfig);
```

構成データにアクセスするために、コンポーネントに <xref:Microsoft.Extensions.Configuration.IConfiguration> インスタンスを挿入します。

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<h2>Wheels</h2>

<ul>
    <li>Count: @Configuration["wheels:count"]</li>
    <li>Brand: @Configuration["wheels:brand"]</li>
    <li>Type: @Configuration["wheels:brand:type"]</li>
    <li>Year: @Configuration["wheels:year"]</li>
</ul>

@code {
    var wheelsSection = Configuration.GetSection("wheels");
    
    ...
}
```

その他の構成ファイルを *wwwroot* フォルダーから構成に読み取るには、<xref:System.Net.Http.HttpClient> を使用してファイルの内容を取得します。 この方法を使用する場合、既存の <xref:System.Net.Http.HttpClient> サービスの登録では、次の例に示すように、作成されたローカル クライアントを使用してファイルを読み取ることができます。

*wwwroot/cars.json*:

```json
{
    "size": "tiny"
}
```

`Program.Main`:

```csharp
using Microsoft.Extensions.Configuration;

...

var client = new HttpClient()
{
    BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
};

builder.Services.AddTransient(sp => client);

using var response = await client.GetAsync("cars.json");
using var stream = await response.Content.ReadAsStreamAsync();

builder.Configuration.AddJsonStream(stream);
```

#### <a name="authentication-configuration"></a>認証の構成

*wwwroot/appsettings.json*:

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

`Program.Main`:

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions);
```

#### <a name="logging-configuration"></a>ログの構成

*wwwroot/appsettings.json*:

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  }
}
```

`Program.Main`:

```csharp
builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

#### <a name="host-builder-configuration"></a>ホスト ビルダーの構成

`Program.Main`:

```csharp
var hostname = builder.Configuration["HostName"];
```

#### <a name="cached-configuration"></a>キャッシュされた構成

構成ファイルは、オフラインで使用できるようにキャッシュされます。 [プログレッシブ Web アプリケーション (PWA)](xref:blazor/progressive-web-app) では、新しい展開を作成するときにのみ構成ファイルを更新できます。 次の理由により、展開間で構成ファイルを編集しても意味がありません。

* ユーザーには、引き続き使用するファイルのキャッシュされたバージョンがあります。
* PWA の *service-worker.js* ファイルと *service-worker-assets.js* ファイルは、コンパイル時に再構築される必要があります。これにより、ユーザーの次回のオンライン アクセス時に、アプリが再展開されたことが通知されます。

PWA によるバックグラウンド更新の処理方法の詳細については、「<xref:blazor/progressive-web-app#background-updates>」を参照してください。

### <a name="logging"></a>ログの記録

Blazor WebAssembly ログ記録の詳細については、「<xref:fundamentals/logging/index#create-logs-in-blazor>」を参照してください。

## <a name="blazor-server"></a>Blazor サーバー

### <a name="reflect-the-connection-state-in-the-ui"></a>UI に接続状態を反映する

接続が失われたことがクライアントで検出されると、クライアントによって再接続が試行される間、ユーザーに対して既定の UI が表示されます。 再接続に失敗した場合、ユーザーには再試行のオプションが表示されます。

UI をカスタマイズするには、 *_Host.cshtml* Razor ページの `<body>` に、`components-reconnect-modal` の `id` を持つ要素を定義します。

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

次の表では、`components-reconnect-modal` 要素に適用される CSS クラスについて説明します。

| CSS クラス                       | 示す内容&hellip; |
| ---
title:'ASP.NET Core Blazor ホスティング モデルの構成' author: description:'Razor コンポーネントを Razor Pages および MVC アプリに統合する方法など、Blazor ホスティング モデルの構成について学習します。'
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title:'ASP.NET Core Blazor ホスティング モデルの構成' author: description:'Razor コンポーネントを Razor Pages および MVC アプリに統合する方法など、Blazor ホスティング モデルの構成について学習します。'
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title:'ASP.NET Core Blazor ホスティング モデルの構成' author: description:'Razor コンポーネントを Razor Pages および MVC アプリに統合する方法など、Blazor ホスティング モデルの構成について学習します。'
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title:'ASP.NET Core Blazor ホスティング モデルの構成' author: description:'Razor コンポーネントを Razor Pages および MVC アプリに統合する方法など、Blazor ホスティング モデルの構成について学習します。'
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title:'ASP.NET Core Blazor ホスティング モデルの構成' author: description:'Razor コンポーネントを Razor Pages および MVC アプリに統合する方法など、Blazor ホスティング モデルの構成について学習します。'
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title:'ASP.NET Core Blazor ホスティング モデルの構成' author: description:'Razor コンポーネントを Razor Pages および MVC アプリに統合する方法など、Blazor ホスティング モデルの構成について学習します。'
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title:'ASP.NET Core Blazor ホスティング モデルの構成' author: description:'Razor コンポーネントを Razor Pages および MVC アプリに統合する方法など、Blazor ホスティング モデルの構成について学習します。'
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title:'ASP.NET Core Blazor ホスティング モデルの構成' author: description:'Razor コンポーネントを Razor Pages および MVC アプリに統合する方法など、Blazor ホスティング モデルの構成について学習します。'
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title:'ASP.NET Core Blazor ホスティング モデルの構成' author: description:'Razor コンポーネントを Razor Pages および MVC アプリに統合する方法など、Blazor ホスティング モデルの構成について学習します。'
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title:'ASP.NET Core Blazor ホスティング モデルの構成' author: description:'Razor コンポーネントを Razor Pages および MVC アプリに統合する方法など、Blazor ホスティング モデルの構成について学習します。'
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title:'ASP.NET Core Blazor ホスティング モデルの構成' author: description:'Razor コンポーネントを Razor Pages および MVC アプリに統合する方法など、Blazor ホスティング モデルの構成について学習します。'
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title:'ASP.NET Core Blazor ホスティング モデルの構成' author: description:'Razor コンポーネントを Razor Pages および MVC アプリに統合する方法など、Blazor ホスティング モデルの構成について学習します。'
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title:'ASP.NET Core Blazor ホスティング モデルの構成' author: description:'Razor コンポーネントを Razor Pages および MVC アプリに統合する方法など、Blazor ホスティング モデルの構成について学習します。'
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

---------------- | --- title:'ASP.NET Core Blazor ホスティング モデルの構成' author: description:'Razor コンポーネントを Razor Pages および MVC アプリに統合する方法など、Blazor ホスティング モデルの構成について学習します。'
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title:'ASP.NET Core Blazor ホスティング モデルの構成' author: description:'Razor コンポーネントを Razor Pages および MVC アプリに統合する方法など、Blazor ホスティング モデルの構成について学習します。'
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title:'ASP.NET Core Blazor ホスティング モデルの構成' author: description:'Razor コンポーネントを Razor Pages および MVC アプリに統合する方法など、Blazor ホスティング モデルの構成について学習します。'
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title:'ASP.NET Core Blazor ホスティング モデルの構成' author: description:'Razor コンポーネントを Razor Pages および MVC アプリに統合する方法など、Blazor ホスティング モデルの構成について学習します。'
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title:'ASP.NET Core Blazor ホスティング モデルの構成' author: description:'Razor コンポーネントを Razor Pages および MVC アプリに統合する方法など、Blazor ホスティング モデルの構成について学習します。'
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title:'ASP.NET Core Blazor ホスティング モデルの構成' author: description:'Razor コンポーネントを Razor Pages および MVC アプリに統合する方法など、Blazor ホスティング モデルの構成について学習します。'
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

--------- | | `components-reconnect-show`     | 接続が失われました。 クライアントによって再接続が試行されています。 モーダルを表示します。 | | `components-reconnect-hide`     | サーバーへのアクティブな接続が再確立されます。 モーダルを非表示にします。 | | `components-reconnect-failed`   | 再接続に失敗しました。ネットワーク障害が原因である可能性があります。 再接続を試みるには、`window.Blazor.reconnect()` を呼び出します。 | | `components-reconnect-rejected` | 再接続が拒否されました。 サーバーに到達したが接続が拒否されたため、サーバー上のユーザーの状態が失われました。 アプリを再度読み込むには、`location.reload()` を呼び出します。 この接続状態は、次の場合に発生する可能性があります。<ul><li>サーバー側回線でクラッシュが発生した場合。</li><li>クライアントが長時間切断されているため、サーバーでユーザーの状態が削除された場合。 ユーザーが対話しているコンポーネントのインスタンスは破棄されます。</li><li>サーバーが再起動されたか、アプリのワーカー プロセスがリサイクルされた場合。</li></ul> |

### <a name="render-mode"></a>表示モード

Blazor サーバー アプリは、サーバーへのクライアント接続が確立される前に、サーバー上の UI をプリレンダリングするよう既定で設定されます。 これは、 *_Host.cshtml* Razor ページで設定されます。

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> によって、コンポーネントに対して以下の構成が行われます。

* ページに事前レンダリングするかどうか。
* ページに静的 HTML としてレンダリングするかどうか。または、ユーザー エージェントから Blazor アプリをブートストラップするために必要な情報が含まれているかどうか。

| <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> | 説明 |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | コンポーネントを静的 HTML にレンダリングし、Blazor Server アプリのマーカーを含めます。 このマーカーは、ユーザー エージェントの起動時に Blazor アプリをブートストラップするために使用されます。 |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Blazor Server アプリのマーカーをレンダリングします。 コンポーネントからの出力は含められません。 このマーカーは、ユーザー エージェントの起動時に Blazor アプリをブートストラップするために使用されます。 |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | コンポーネントを静的 HTML にレンダリングします。 |

静的 HTML ページからのサーバー コンポーネントのレンダリングは、サポートされていません。

### <a name="configure-the-signalr-client-for-blazor-server-apps"></a>Blazor サーバー アプリ用に SignalR クライアントを構成する

場合によっては、Blazor サーバー アプリによって使用される SignalR クライアントを構成する必要があります。 たとえば、接続の問題を診断するために SignalR クライアントのログ記録を構成できます。

*Pages/_Host* ファイルで SignalR クライアントを構成するには、次の手順に従います。

* `blazor.server.js` スクリプトの `<script>` タグに `autostart="false"` 属性を追加します。
* `Blazor.start` を呼び出し、SignalR ビルダーを指定する構成オブジェクトを渡します。

```html
<script src="_framework/blazor.server.js" autostart="false"></script>
<script>
  Blazor.start({
    configureSignalR: function (builder) {
      builder.configureLogging("information"); // LogLevel.Information
    }
  });
</script>
```

### <a name="logging"></a>ログの記録

Blazor サーバー ログのサポートの詳細については、「<xref:fundamentals/logging/index#create-logs-in-blazor>」を参照してください。
