---
title: .NET Core および ASP.NET Core でのログ記録
author: rick-anderson
description: Microsoft.Extensions.Logging NuGet パッケージで提供されるログ記録フレームワークの使用方法について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 6/29/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/logging/index
ms.openlocfilehash: a4962c3132c60b68cb2d4b5a97e9b082aba15d15
ms.sourcegitcommit: 50e7c970f327dbe92d45eaf4c21caa001c9106d0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2020
ms.locfileid: "87330774"
---
# <a name="logging-in-net-core-and-aspnet-core"></a>.NET Core および ASP.NET Core でのログ記録

::: moniker range=">= aspnetcore-3.0"

作成者: [Kirk Larkin](https://twitter.com/serpent5)、[Juergen Gutsch](https://github.com/JuergenGutsch)、[Rick Anderson](https://twitter.com/RickAndMSFT)

.NET Core では、組み込みやサード パーティ製のさまざまなログ プロバイダーと連携するログ API がサポートされています。 この記事では、組み込みプロバイダーと共にログ API を使用する方法について説明します。

この記事に記載されているほとんどのコード例は、ASP.NET Core アプリのものです。 これらのコード スニペットのログ記録固有の部分は、[汎用ホスト](xref:fundamentals/host/generic-host)を使用するすべての .NET Core アプリに適用されます。 ASP.NET Core Web アプリ テンプレートでは、汎用ホストが使用されます。

[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples/3.x)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

<a name="lp"></a>

## <a name="logging-providers"></a>ログ プロバイダー

ログを表示する `Console` プロバイダーを除き、ログ プロバイダーはログを保存します。 たとえば、Azure Application Insights プロバイダーでは、Azure Application Insights にログが保存されます。 複数のプロバイダーを有効にすることができます。

既定の ASP.NET Core Web アプリ テンプレートでは:

* [汎用ホスト](xref:fundamentals/host/generic-host)が使用されます。
* <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> が呼び出されます。これにより、次のログ プロバイダーが追加されます。
  * [コンソール](#console)
  * [デバッグ](#debug)
  * [EventSource](#event-source)
  * [EventLog](#welog):Windows のみ

[!code-csharp[](index/samples/3.x/TodoApiDTO/Program.cs?name=snippet_TemplateCode&highlight=9)]

上記のコードは、ASP.NET Core Web アプリ テンプレートを使用して作成された `Program` クラスを示しています。 以降のいくつかのセクションでは、汎用ホストを使用する ASP.NET Core Web アプリ テンプレートに基づくサンプルを提供します。 [ホスト コンソール以外のアプリ](#nhca)については、このドキュメントで後ほど説明します。

`Host.CreateDefaultBuilder` によって追加されたログ プロバイダーの既定のセットをオーバーライドするには、`ClearProviders` を呼び出し、必要なログ プロバイダーを追加します。 コード例を次に示します。

* <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> を呼び出して、ビルダーからすべての <xref:Microsoft.Extensions.Logging.ILoggerProvider> インスタンスを削除します。
* [Console](#console) ログ プロバイダーを追加します。

[!code-csharp[](index/samples/3.x/TodoApiDTO/Program.cs?name=snippet_AddProvider&highlight=5-6)]

その他のプロバイダーについては、以下を参照してください。

* [組み込みのログ プロバイダー](#bilp)
* [サードパーティ製のログ プロバイダー](#third-party-logging-providers)

## <a name="create-logs"></a>ログを作成する

ログを作成するには、[依存関係の挿入](xref:fundamentals/dependency-injection) (DI) から <xref:Microsoft.Extensions.Logging.ILogger%601> オブジェクトを使用します。

次のような例です。

* `AboutModel` 型の完全修飾名のログ "*カテゴリ*" を使用する、ロガー `ILogger<AboutModel>` を作成します。 ログのカテゴリは、各ログに関連付けられている文字列です。
* <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> を呼び出して、`Information` レベルでログを記録します。 ログの "*レベル*" は、ログに記録されるイベントの重大度を示します。

[!code-csharp[](index/samples/3.x/TodoApiDTO/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=5,14)]

[レベル](#log-level)と[カテゴリ](#log-category)については、このドキュメントで後ほど詳しく説明します。

Blazor の詳細については、このドキュメントの[ Blazor および Blazor WebAssembly でのログの作成](#clib)に関するセクションを参照してください。

[Main および Startup でログを作成](#clms)に関するセクションには、`Main` と `Startup` でログを作成する方法が示されています。

## <a name="configure-logging"></a>ログの構成

一般的に、ログの構成は *appsettings*.`{Environment}` *.json* ファイルの `Logging` セクションで指定されます。 次の*appsettings.Development.json* ファイルは、ASP.NET Core Web アプリ テンプレートによって生成されます。

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.Development.json)]

上記の JSON では、次のように指定されています。

* `"Default"`、`"Microsoft"`、および `"Microsoft.Hosting.Lifetime"` の各カテゴリが指定されています。
* `"Microsoft"` カテゴリは、`"Microsoft"` で始まるすべてのカテゴリに適用されます。 たとえば、この設定は `"Microsoft.AspNetCore.Routing.EndpointMiddleware"` カテゴリに適用されます。
* `"Microsoft"` カテゴリでは、ログ レベルが `Warning` 以上のログが記録されます。
* `"Microsoft.Hosting.Lifetime"` カテゴリは `"Microsoft"` カテゴリよりも詳細なので、`"Microsoft.Hosting.Lifetime"` カテゴリではログ レベルが "情報" 以上のログが記録されます。
* 特定のログ プロバイダーが指定されていないため、`LogLevel` は、[Windows EventLog](#welog) を除くすべての有効なログ プロバイダーに適用されます。

`Logging` プロパティには <xref:Microsoft.Extensions.Logging.LogLevel> およびログ プロバイダーのプロパティを含めることができます。 `LogLevel` により、選択したカテゴリに対するログの最小[レベル](#log-level)が指定されます。 上記の JSON では、`Information` と `Warning` のログ レベルが指定されています。 `LogLevel` はログの重大度を 0 - 6 の範囲で示します。

`Trace` = 0、`Debug` = 1、`Information` = 2、`Warning` = 3、`Error` = 4、`Critical` = 5、`None` = 6。

`LogLevel` を指定すると、指定したレベル以上のメッセージに対してログが有効になります。 上記の JSON では、`Default` カテゴリは `Information` 以上に対してのみログに記録されます。 たとえば、`Information`、`Warning`、`Error`、`Critical` のメッセージがログに記録されます。 `LogLevel` が指定されていない場合、ログは既定で `Information` レベルになります。 詳細については、「[ログ レベル](#llvl)」を参照してください。

プロバイダー プロパティで `LogLevel` プロパティを指定できます。 プロバイダーの下の `LogLevel` によって、そのプロバイダーのログに記録するレベルが指定され、プロバイダー以外のログ設定がオーバーライドされます。 以下の *appsettings.json* ファイルについて考えます:

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.Prod2.json)]

`Logging.{providername}.LogLevel` の設定により `Logging.LogLevel` の設定がオーバーライドされます。 上記の JSON では、`Debug` プロバイダーの既定のログ レベルは `Information` に設定されています。

`Logging:Debug:LogLevel:Default:Information`

上記の設定により、`Microsoft.Hosting` 以外のすべての `Logging:Debug:` カテゴリに `Information` ログ レベルが指定されます。 特定のカテゴリが一覧表示されると、特定のカテゴリによって既定のカテゴリがオーバーライドされます。 上記の JSON では、`Logging:Debug:LogLevel` カテゴリ `"Microsoft.Hosting"` と `"Default"` によって、`Logging:LogLevel` の設定がオーバーライドされます。

最小ログ レベルは、次のいずれかに指定できます。

* 特定のプロバイダー: たとえば、`Logging:EventSource:LogLevel:Default:Information`
* 特定のカテゴリ: たとえば、`Logging:LogLevel:Microsoft:Warning`
* すべてのプロバイダーとすべてのカテゴリ: `Logging:LogLevel:Default:Warning`

最小レベルを下回るログは、次のことが "***行われません***"。

* プロバイダーに渡される。
* ログに記録または表示される。

すべてのログを抑制するには、[LogLevel.None](xref:Microsoft.Extensions.Logging.LogLevel) を指定します。 `LogLevel.None` の値は、`LogLevel.Critical` (5) より大きい 6 です。

プロバイダーで[ログのスコープ](#logscopes)がサポートされている場合、`IncludeScopes` によってそれを有効にするかどうかが指定されます。 詳細については、「[ログ スコープ](#logscopes)」を参照してください。

次の *appsettings.json* ファイルには、既定で有効になっているすべてのプロバイダーが含まれています。

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.Production.json)]

上記のサンプルについて:

* カテゴリとレベルは推奨値ではありません。 このサンプルは、すべての既定のプロバイダーを表示するために提供されています。
* `Logging.{providername}.LogLevel` の設定により `Logging.LogLevel` の設定がオーバーライドされます。 たとえば、`Debug.LogLevel.Default` のレベルにより `LogLevel.Default` のレベルがオーバーライドされます。
* 各既定のプロバイダーの "*別名*" が使用されます。 各プロバイダーでは "*エイリアス*" が定義されます。これは構成で完全修飾型名の代わりに使用できます。 組み込みプロバイダーの別名には次のものがあります。
  * コンソール
  * デバッグ
  * EventSource
  * EventLog
  * AzureAppServicesFile
  * AzureAppServicesBlob
  * ApplicationInsights

## <a name="set-log-level-by-command-line-environment-variables-and-other-configuration"></a>コマンド ライン、環境変数、およびその他の構成でログ レベルを設定する

ログ レベルは、いずれかの[構成プロバイダー](xref:fundamentals/configuration/index)で設定できます。 

[!INCLUDE[](~/includes/environmentVarableColon.md)]

次のコマンドは:

* 環境キー `Logging:LogLevel:Microsoft` を Windows の `Information` の値に設定します。
* ASP.NET Core Web アプリケーション テンプレートを使用して作成されたアプリを使用する際に、設定をテストします。 `dotnet run` コマンドは、`set` を使用した後、プロジェクト ディレクトリで実行する必要があります。

```cmd
set Logging__LogLevel__Microsoft=Information
dotnet run
```

上記の環境設定は:

* コマンド ウィンドウから起動されたプロセスでのみ設定可能です。
* Visual Studio で起動されたブラウザーでは読み取れません。

次の [setx](/windows-server/administration/windows-commands/setx) コマンドは、Windows 上で環境キーと値も設定します。 `set` とは異なり、`setx` 設定は保持されます。 `/M` スイッチにより、システム環境で変数が設定されます。 `/M` が使用されていない場合には、ユーザー環境変数が設定されます。

```cmd
setx Logging__LogLevel__Microsoft=Information /M
```

[Azure App Service](https://azure.microsoft.com/services/app-service/) で、 **設定 > 構成** ページの**新しいアプリケーション設定**を選択します。 Azure App Service アプリケーションの設定は：

* 保存時に暗号化され、暗号化されたチャネルで送信されます。
* 環境変数として公開されます。

詳細については、「[Azure アプリ: Azure Portal を使用してアプリの構成をオーバーライドする](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal)」を参照してください。

環境変数を使用して ASP.NET Core 構成値を設定する方法の詳細については、「[環境変数](xref:fundamentals/configuration/index#environment-variables)」を参照してください。 コマンド ライン、Azure Key Vault、Azure App Configuration、その他のファイル形式など、他の構成ソースの使用の詳細については、「<xref:fundamentals/configuration/index>」を参照してください。

## <a name="how-filtering-rules-are-applied"></a>フィルター規則を適用する方法

<xref:Microsoft.Extensions.Logging.ILogger%601> オブジェクトを作成すると、<xref:Microsoft.Extensions.Logging.ILoggerFactory> オブジェクトによって、そのロガーに適用するプロバイダーごとに 1 つの規則が選択されます。 `ILogger` インスタンスによって書き込まれるすべてのメッセージは、選択した規則に基づいてフィルター処理されます。 使用できる規則から、各プロバイダーとカテゴリのペアごとに最も明確な規則が選択されます。

特定のカテゴリに `ILogger` が作成されるときに、各プロバイダーに次のアルゴリズムが使用されます。

* プロバイダーとそのエイリアスと一致するすべての規則が選択されます。 一致が見つからない場合は、空のプロバイダーですべての規則が選択されます。
* 前の手順の結果、最も長いカテゴリのプレフィックスが一致する規則が選択されます。 一致が見つからない場合は、カテゴリを指定しないすべての規則が選択されます。
* 複数の規則が選択されている場合は、**最後**の 1 つが使用されます。
* 規則が選択されていない場合は、`MinimumLevel` が使用されます。

<a name="dnrvs"></a>

## <a name="logging-output-from-dotnet-run-and-visual-studio"></a>dotnet run および Visual Studio からのログ出力

[既定のログ プロバイダー](#lp)で作成されたログが表示されます。

* Visual Studio 内
  * デバッグ時はデバッグ出力ウィンドウ内。
  * ASP.NET Core Web サーバー ウィンドウ内。
* アプリが `dotnet run` で実行されている場合はコンソール ウィンドウ内。

"Microsoft" カテゴリから始まるログは、ASP.NET Core のフレームワークのコードからのログです。 ASP.NET Core とアプリケーション コードでは、同じログ API とログ プロバイダーが使用されます。

<a name="lcat"></a>

## <a name="log-category"></a>ログのカテゴリ

`ILogger` オブジェクトが作成されるときに、"*カテゴリ*" が指定されます。 このカテゴリは、`ILogger` のインスタンスによって作成される各ログ メッセージと共に含められます。 カテゴリ文字列は任意ですが、規則ではクラス名を使用します。 たとえば、コントローラーでは、名前が `"TodoApi.Controllers.TodoController"` になる場合があります。 ASP.NET Core Web アプリでは、`ILogger<T>` を使用して、カテゴリとして `T` の完全修飾型名を使用する `ILogger` インスタンスが自動的に取得されます。

[!code-csharp[](index/samples/3.x/TodoApiDTO/Pages/Privacy.cshtml.cs?name=snippet)]

カテゴリを明示的に指定するには、`ILoggerFactory.CreateLogger` を呼び出します。

[!code-csharp[](index/samples/3.x/TodoApiDTO/Pages/Contact.cshtml.cs?name=snippet)]

`ILogger<T>` は、`T` の完全修飾型名を使用した `CreateLogger` の呼び出しと同じです。

<a name="llvl"></a>

## <a name="log-level"></a>ログ レベル

次の表に、<xref:Microsoft.Extensions.Logging.LogLevel> 値、便利な `Log{LogLevel}` 拡張メソッド、推奨される使用法を示します。

| LogLevel | [値] | Method | 説明 |
| -------- | ----- | ------ | ----------- |
| [トレース](xref:Microsoft.Extensions.Logging.LogLevel) | 0 | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogTrace%2A> | 最も詳細なメッセージが含まれます。 これらのメッセージには、機密性の高いアプリ データが含まれる場合があります。 これらのメッセージは既定で無効になっているため、運用環境では有効に "***しないでください***"。 |
| [デバッグ](xref:Microsoft.Extensions.Logging.LogLevel) | 1 | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug%2A> | デバッグと開発用。 量が多いため、運用環境では慎重に使用してください。 |
| [情報](xref:Microsoft.Extensions.Logging.LogLevel) | 2 | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation%2A> | アプリの一般的なフローを追跡します。 長期的な値が含まれている可能性があります。 |
| [警告](xref:Microsoft.Extensions.Logging.LogLevel) | 3 | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> | 異常なイベントや予期しないイベント用。 通常、アプリが失敗する原因にならないエラーや条件が含まれます。 |
| [エラー](xref:Microsoft.Extensions.Logging.LogLevel) | 4 | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A> | 処理できないエラーと例外の場合。 これらのメッセージは、アプリ全体のエラーではなく、現在の操作や要求における失敗を示します。 |
| [重大](xref:Microsoft.Extensions.Logging.LogLevel) | 5 | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogCritical%2A> | 即時の注意が必要なエラーの場合。 例: データ損失のシナリオ、ディスク領域不足。 |
| [None](xref:Microsoft.Extensions.Logging.LogLevel) | 6 | | ログのカテゴリにメッセージを記述しないように指定します。 |

前の表では、重大度が最も低い方から高い方に `LogLevel` が一覧表示されています。

[Log](xref:Microsoft.Extensions.Logging.LoggerExtensions) メソッドの最初のパラメーター <xref:Microsoft.Extensions.Logging.LogLevel> は、ログの重大度を示します。 ほとんどの開発者は、`Log(LogLevel, ...)` を呼び出すのではなく、[Log{LogLevel}](xref:Microsoft.Extensions.Logging.LoggerExtensions) 拡張メソッドを呼び出します。 `Log{LogLevel}` 拡張メソッドによって、[Log メソッドが呼び出され、LogLevel が指定されます](https://github.com/dotnet/extensions/blob/release/3.1/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs)。 たとえば、次の 2 つのログ呼び出しは、機能的に同等で、同じログが生成されます。

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TestController.cs?name=snippet0&highlight=6-7)]

`MyLogEvents.TestItem` はイベント ID です。 `MyLogEvents` はサンプル アプリの一部であり、[ログ イベント ID](#leid) セクションに表示されます。

[!INCLUDE[](~/includes/MyDisplayRouteInfoBoth.md)]

`Information` および `Warning` ログを作成するコードを次に示します。

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet_CallLogMethods&highlight=4,10)]

上記のコードでは、最初の `Log{LogLevel}` パラメーター `MyLogEvents.GetItem` は、[ログ イベント ID](#leid) です。 2 つ目のパラメーターは、他のメソッド パラメーターによって提供される引数値のプレースホルダーを含むメッセージ テンプレートです。 メソッド パラメーターについては、このドキュメントで後述する[メッセージ テンプレート](#lmt)のセクションで説明します。

適切な `Log{LogLevel}` メソッドを呼び出して、特定のストレージ メディアに書き込むログ出力量を制御します。 次に例を示します。

* 運用環境:
  * `Trace` または `Information` のレベルでログを記録すると、詳細なログ メッセージが大量に生成されます。 コストを制御し、データ ストレージの上限を超えないようにするには、`Trace` および `Information` のレベルのメッセージを、大容量の低コストのデータ ストアに記録します。 `Trace` と `Information` を特定のカテゴリに制限することを検討してください。
  * `Warning` から `Critical` のレベルでログを記録しても、ログ メッセージはほとんど生成されません。
    * 通常、コストとストレージの制限は考慮されません。
    * ログの数が少ないほど、データ ストアをより柔軟に選択できるようになります。
* 開発中:
  * `Warning` を設定します。
  * トラブルシューティングの際に `Trace` または `Information` のメッセージを追加します。 出力を制限するには、調査中のカテゴリに対してのみ `Trace` または `Information` を設定します。

ASP.NET Core では、フレームワーク イベントのログが書き込まれます。 たとえば、次のログ出力について考えてみます。

* ASP.NET Core テンプレートを使用して作成された Razor Pages アプリ
* `Logging:Console:LogLevel:Microsoft:Information` に設定されているログ
* プライバシー ページへの移動

```console
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 GET https://localhost:5001/Privacy
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint '/Privacy'
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[3]
      Route matched with {page = "/Privacy"}. Executing page /Privacy
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[101]
      Executing handler method DefaultRP.Pages.PrivacyModel.OnGet - ModelState is Valid
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[102]
      Executed handler method OnGet, returned result .
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[103]
      Executing an implicit handler method - ModelState is Valid
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[104]
      Executed an implicit handler method, returned result Microsoft.AspNetCore.Mvc.RazorPages.PageResult.
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[4]
      Executed page /Privacy in 74.5188ms
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint '/Privacy'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 149.3023ms 200 text/html; charset=utf-8
```

次の JSON は `Logging:Console:LogLevel:Microsoft:Information` を設定します。

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.MSFT.json)]

<a name="leid"></a>

## <a name="log-event-id"></a>ログ イベント ID

各ログで "*イベント ID*" を指定できます。 このサンプル アプリでは、`MyLogEvents` クラスを使用してイベント ID を定義します。

<!-- Review: to bad there is no way to use an enum for event ID's -->
[!code-csharp[](index/samples/3.x/TodoApiDTO/Models/MyLogEvents.cs?name=snippet_LoggingEvents)]

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet_CallLogMethods&highlight=4,10)]

イベント ID によって一連のイベントが関連付けられます。 たとえば、ページ上に項目の一覧を表示する機能に関連するすべてのログを 1001 に設定します。

ログ プロバイダーでは、ID フィールドやログ メッセージにイベント ID が格納されたり、またはまったく格納されなかったりする場合があります。 Debug プロバイダーでイベント ID が表示されることはありません。 Console プロバイダーでは、カテゴリの後のブラケット内にイベント ID が表示されます。

```console
info: TodoApi.Controllers.TodoItemsController[1002]
      Getting item 1
warn: TodoApi.Controllers.TodoItemsController[4000]
      Get(1) NOT FOUND
```

一部のログ プロバイダーでは、イベント ID がフィールドに格納されます。これにより、ID に対するフィルター処理が可能になります。

<a name="lmt"></a>

## <a name="log-message-template"></a>ログ メッセージ テンプレート
<!-- Review, Each log API uses a message template. -->
各ログ API では、メッセージ テンプレートが使用されます。 メッセージ テンプレートには、指定される引数のためのプレースホルダーを含めることができます。 プレースホルダーには、数値ではなく名前を使用します。

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet_CallLogMethods&highlight=4,10)]

プレースホルダーの名前ではなく、プレースホルダーの順序によって、値の指定に使用されるパラメーターが決まります。 次のコードでは、パラメーター名がメッセージ テンプレート内のシーケンスの外にあります。

```csharp
string p1 = "param1";
string p2 = "param2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

上記のコードでは、シーケンス内のパラメーター値を含むログ メッセージが作成されます。

```text
Parameter values: param1, param2
```

この方法により、ログ プロバイダーが [セマンティック ログまたは構造化ログ](https://github.com/NLog/NLog/wiki/How-to-use-structured-logging)を実装できるようになります。 書式設定されたメッセージ テンプレートだけでなく、引数自体がログ システムに渡されます。 これにより、ログ プロバイダーはフィールドとしてパラメーター値を格納することができます。 たとえば、次のロガー メソッドについて考えてみます。

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

たとえば、Azure Table Storage にログを記録する場合:

* 各 Azure Table エンティティには、`ID` プロパティと `RequestTime` プロパティを含めることができます。
* プロパティを持つテーブルによって、ログに記録されたデータに対するクエリが簡略化されます。 たとえば、クエリによって、特定の `RequestTime` の範囲内のすべてのログを検索できます。テキスト メッセージから時間を解析する必要はありません。

## <a name="log-exceptions"></a>例外をログに記録する

ロガー メソッドには、例外パラメーターを受け取るオーバーロードがあります。

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TestController.cs?name=snippet_Exp)]

[!INCLUDE[](~/includes/MyDisplayRouteInfoBoth.md)]

例外ログはプロバイダー固有です。

### <a name="default-log-level"></a>既定のログ レベル

既定のログ レベルが設定されていない場合、既定のログ レベル値は `Information` になります。

たとえば、次の Web アプリについて考えてみます。

* ASP.NET Web アプリ テンプレートを使用して作成された。
* *appsettings.json* と *appsettings.Development.json* が削除または名前が変更された。

上記の設定で、プライバシー ページまたはホーム ページに移動すると、カテゴリ名に `Microsoft` が含まれる多くの`Trace`、`Debug`、`Information` のメッセージが生成されます。

次のコードは、既定のログ レベルが構成で設定されていない場合に、既定のログ レベルを設定します。

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_MinLevel&highlight=10)]

<!-- review required: I say this a couple times -->
一般に、ログ レベルは、コードではなく構成で指定する必要があります。

### <a name="filter-function"></a>フィルター関数

フィルター関数は、構成またはコードによって規則が割り当てられていないすべてのプロバイダーとカテゴリに対して呼び出されます。

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_FilterFunction)]

上記のコードでは、カテゴリに `Controller` または `Microsoft` が含まれ、ログ レベルが `Information` 以上の場合にコンソール ログが表示されます。

一般に、ログ レベルは、コードではなく構成で指定する必要があります。

## <a name="aspnet-core-and-ef-core-categories"></a>ASP.NET Core と EF Core のカテゴリ

次の表に、ASP.NET Core と Entity Framework Core で使用されるいくつかのカテゴリと、ログに関するメモを示します。

| カテゴリ                            | メモ |
| ----------------------------------- | ----- |
| Microsoft.AspNetCore                | ASP.NET Core の一般的な診断。 |
| Microsoft.AspNetCore.DataProtection | どのキーが検討、検索、および使用されたか。 |
| Microsoft.AspNetCore.HostFiltering  | 許可されるホスト。 |
| Microsoft.AspNetCore.Hosting        | HTTP 要求が完了するまでにかかった時間と、それらの開始時刻。 どのホスティング スタートアップ アセンブリが読み込まれたか。 |
| Microsoft.AspNetCore.Mvc            | MVC と Razor の診断。 モデルの構築、フィルター処理の実行、ビューのコンパイル、アクションの選択。 |
| Microsoft.AspNetCore.Routing        | 一致する情報をルーティングします。 |
| Microsoft.AspNetCore.Server         | 接続の開始、停止、キープ アライブ応答。 HTTPS 証明書情報。 |
| Microsoft.AspNetCore.StaticFiles    | 提供されるファイル。 |
| Microsoft.EntityFrameworkCore       | Entity Framework Core の一般的な診断。 データベースのアクティビティと構成、変更の検出、移行。 |

コンソール ウィンドウに他のカテゴリを表示するには、**appsettings.Development.json** を次のように設定します。

[!code-json[](index/samples/3.x/MyMain/appsettings.Trace.json)]

<!-- Review: What other providers support scopes? Console is not generally used in staging/production  -->

<a name="logscopes"></a>

## <a name="log-scopes"></a>ログのスコープ

 "*スコープ*" では、論理操作のセットをグループ化できます。 このグループ化を使用して、セットの一部として作成される各ログに同じデータをアタッチすることができます。 たとえば、トランザクション処理の一部として作成されるすべてのログに、トランザクション ID を含めることができます。

スコープは:

* <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> メソッドによって返される <xref:System.IDisposable> 型です。
* 破棄されるまで継続します。

次のプロバイダーではスコープがサポートされています。

* `Console`
* [AzureAppServicesFile と AzureAppServicesBlob](xref:Microsoft.Extensions.Logging.AzureAppServices.BatchingLoggerOptions.IncludeScopes)

ロガーの呼び出しを `using` ブロックでラップすることによって、スコープを使用します。

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TestController.cs?name=snippet_Scopes)]

次の JSON では、Console プロバイダーのスコープを有効にしています。

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.Scopes.json)]

次のコードでは、Console プロバイダーのスコープを有効にしています。

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_Scopes)]

一般に、ログは、コードではなく構成で指定する必要があります。

<a name="bilp"></a>

## <a name="built-in-logging-providers"></a>組み込みのログ プロバイダー

ASP.NET Core には、次のログ プロバイダーが含まれています。

* [コンソール](#console)
* [デバッグ](#debug)
* [EventSource](#event-source)
* [EventLog](#welog)
* [AzureAppServicesFile と AzureAppServicesBlob](#azure-app-service)
* [ApplicationInsights](#azure-application-insights)

ASP.NET Core モジュールを使用した `stdout` およびデバッグ ログの詳細については、「<xref:test/troubleshoot-azure-iis>」および「<xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>」を参照してください。

### <a name="console"></a>コンソール

`Console` プロバイダーでは、コンソールへの出力がログに記録されます。 開発中の `Console` ログを表示する方法の詳細については、「[dotnet run および Visual Studio からのログ出力](#dnrvs)」を参照してください。

### <a name="debug"></a>デバッグ

`Debug` プロバイダーでは、[System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) クラスを使用してログ出力が書き込まれます。 `Debug` プロバイダーに書き込むために `System.Diagnostics.Debug.WriteLine` が呼び出されます。

Linux では、`Debug` プロバイダーのログの場所は配布によって異なり、次のいずれかになります。

* */var/log/message*
* */var/log/syslog*

### <a name="event-source"></a>イベント ソース

`EventSource` プロバイダーでは、`Microsoft-Extensions-Logging` という名前のクロスプラットフォーム イベント ソースに書き込まれます。 Windows では、プロバイダーによって [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803) が使用されます。

#### <a name="dotnet-trace-tooling"></a>dotnet trace ツール

[dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) ツールは、実行中のプロセスの .NET Core のトレースのコレクションを有効にする、クロスプラットフォームの CLI グローバル ツールです。 このツールでは、<xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource> を使用して <xref:Microsoft.Extensions.Logging.EventSource> プロバイダー データを収集します。

インストール手順については、[dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) に関するページを参照してください。

dotnet trace ツールを使用して、アプリからトレースを収集します。

1. `dotnet run` コマンドを使用してアプリを実行します。
1. .NET Core アプリのプロセス識別子 (PID) を決定します。
   * Windows では、次の方法のいずれかを使用します。
     * タスク マネージャー (Ctrl + Alt + Del)
     * [tasklist コマンド](/windows-server/administration/windows-commands/tasklist)
     * [Get-Process PowerShell コマンド](/powershell/module/microsoft.powershell.management/get-process)
   * Linux では、[pidof コマンド](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html)を使用します。

   アプリのアセンブリと同じ名前を持つプロセスの PID を検索します。

1. `dotnet trace` コマンドを実行します。

   一般的なコマンド構文

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"
   ```

   PowerShell コマンド シェルを使用する場合は、`--providers` 値を単一引用符 (`'`) で囲みます。

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers 'Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"'
   ```

   Windows 以外のプラットフォームでは、`-f speedscope` オプションを追加して、出力トレース ファイルの形式を `speedscope` に変更します。

   | キーワード | 説明 |
   | :-----: | ----------- |
   | 1       | `LoggingEventSource` に関するメタ イベントをログに記録します。 `ILogger` からのイベントは記録されません。 |
   | 2       | `ILogger.Log()` が呼び出されたときに、`Message` イベントをオンにします。 プログラムで (書式設定されずに) 情報が提供されます。 |
   | 4       | `ILogger.Log()` が呼び出されたときに、`FormatMessage` イベントをオンにします。 書式設定された文字列バージョンの情報が提供されます。 |
   | 8       | `ILogger.Log()` が呼び出されたときに、`MessageJson` イベントをオンにします。 引数の JSON 表現が提供されます。 |

   | イベント レベル | 説明     |
   | :---------: | --------------- |
   | 0           | `LogAlways`     |
   | 1           | `Critical`      |
   | 2           | `Error`         |
   | 3           | `Warning`       |
   | 4           | `Informational` |
   | 5           | `Verbose`       |

   `{Logger Category}` と `{Event Level}` の `FilterSpecs` エントリは、追加のログ フィルター条件を表します。 セミコロン (`;`) で `FilterSpecs` エントリを区切ります。

   Windows コマンド シェルを使用した例 (`--providers` 値を囲む単一引用符**なし**):

   ```dotnetcli
   dotnet trace collect -p {PID} --providers Microsoft-Extensions-Logging:4:2:FilterSpecs=\"Microsoft.AspNetCore.Hosting*:4\"
   ```

   上記のコマンドにより次のことがアクティブになります。

   * エラー (`2`) に対して書式設定された文字列 (`4`) を生成するイベント ソース ロガー。
   * `Informational` ログ レベル (`4`) での `Microsoft.AspNetCore.Hosting` のログ記録。

1. Enter キーまたは Ctrl + C キーを押すことで、dotnet trace ツールを停止します。

   トレースは、`dotnet trace` コマンドが実行されたフォルダーに *trace.nettrace* という名前で保存されます。

1. [Perfview](#perfview) を使用してトレースを開きます。 *trace.nettrace* ファイルを開き、トレース イベントを調べます。

アプリで `CreateDefaultBuilder` を使ってホストがビルドされない場合は、[イベント ソース プロバイダー](#event-source-provider)をアプリのログ構成に追加します。

詳細については次を参照してください:

* [パフォーマンス分析ユーティリティのトレース (dotnet-trace)](/dotnet/core/diagnostics/dotnet-trace) (.NET Core ドキュメント)
* [パフォーマンス分析ユーティリティのトレース (dotnet-trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (dotnet/diagnostics GitHub リポジトリ ドキュメント)
* [LoggingEventSource クラス](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (.NET API ブラウザー)
* <xref:System.Diagnostics.Tracing.EventLevel>
* [LoggingEventSource 参照ソース (3.0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs):別のバージョンの参照ソースを取得するには、分岐を `release/{Version}` に変更します。ここでは、`{Version}` は目的の ASP.NET Core のバージョンです。
* [Perfview](#perfview):イベント ソース トレースの表示に役立ちます。

#### <a name="perfview"></a>Perfview

ログの収集と表示には、[PerfView ユーティリティ](https://github.com/Microsoft/perfview)を使用します。 ETW ログを表示できる他のツールはありますが、ASP.NET Core から出力される ETW イベントを操作する場合、PerfView は最適なエクスペリエンスを提供します。

このプロバイダーでログに記録されるイベントを収集するように PerfView を構成するには、 **[追加プロバイダー]** の一覧に文字列 `*Microsoft-Extensions-Logging` を追加します 文字列の先頭にある `*` を忘れないでください。

<a name="welog"></a>

### <a name="windows-eventlog"></a>Windows EventLog

`EventLog` プロバイダーにより、ログ出力が Windows イベント ログに送信されます。 他のプロバイダーとは異なり、`EventLog` プロバイダーでは既定のプロバイダー以外の設定が継承 "***されません***"。 `EventLog` ログ設定が指定されていない場合は、[既定の LogLevel.Warning](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L99-L103) になります。

<xref:Microsoft.Extensions.Logging.LogLevel.Warning?displayProperty=nameWithType> より下のイベントをログに記録するには、ログ レベルを明示的に設定してください。 次の例では、イベント ログの既定のログ レベルを <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType> に設定します。

```json
"Logging": {
  "EventLog": {
    "LogLevel": {
      "Default": "Information"
    }
  }
}
```

[AddEventLog のオーバーロード](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions)を使用すると、<xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings> を渡すことができます。 `null` または指定しない場合は、次の既定の設定が使用されます。

* `LogName`:"Application"
* `SourceName`: ".NET Runtime"
* `MachineName`:ローカル コンピューター名が使用されます。

次のコードでは、`SourceName` が既定値の `".NET Runtime"` から `MyLogs` に変更されます。

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippetEventLog)]

### <a name="azure-app-service"></a>Azure App Service

[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) プロバイダー パッケージは、Azure App Service アプリのファイル システムのテキスト ファイルと、Azure Storage アカウントの [BLOB ストレージ](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage)にログを書き込みます。

プロバイダー パッケージは、共有フレームワークに含まれていません。 プロバイダーを使用するには、プロバイダー パッケージをプロジェクトに追加します。

プロバイダーの設定を構成するには、次の例のように <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> と <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions> を使用します。

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_AzLogOptions)]

Azure App Service にデプロイされると、Azure portal の **[App Service]** ページの [[App Service ログ]](/azure/app-service/web-sites-enable-diagnostic-log/#enable-application-logging-windows) セクションの設定がアプリで使用されます。 次の設定が更新されると、アプリの再起動や再デプロイを必要とせずに、変更がすぐに有効になります。

* **[アプリケーション ログ (ファイル システム)]**
* **[アプリケーション ログ (BLOB)]**

ログ ファイルの既定の場所は、*D:\\home\\LogFiles\\Application* です。既定のファイル名は *diagnostics-yyyymmdd.txt* です。 既定のファイル サイズ制限は 10 MB です。保持されるファイルの既定の最大数は 2 です。 既定の BLOB 名は *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt* です。

このプロバイダーは、プロジェクトが Azure 環境で実行される場合にのみログを記録します。

#### <a name="azure-log-streaming"></a>Azure ログのストリーミング

Azure ログのストリーミングでは、以下からのリアル タイムでのログ アクティビティの表示がサポートされています。

* アプリ サーバー
* Web サーバー
* 失敗した要求のトレース

Azure ログのストリーミングを構成するには

* アプリのポータル ページから **[App Service ログ]** ページに移動します。
* **[アプリケーション ログ (ファイル システム)]** を **[オン]** に設定します。
* ログ **[レベル]** を選択します。 この設定は、Azure ログ ストリーミングにのみ適用されます。

**[ログ ストリーム]** ページに移動して、ログを表示します。 ログに記録されたメッセージは、`ILogger` インターフェイスを使用してログに記録されます。

### <a name="azure-application-insights"></a>Azure Application Insights

[Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) プロバイダー パッケージでは、[Azure Application Insights](/azure/azure-monitor/app/cloudservices) にログが書き込まれます。 Application Insights は、Web アプリを監視するサービスであり、クエリを実行してテレメトリ データを分析するためのツールを提供します。 このプロバイダーを使用する場合は、Application Insights ツールを使ってクエリを実行し、ログを分析できます。

ログ プロバイダーは、[Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) の依存関係として組み込まれており、ASP.NET Core で利用可能なすべてのテレメトリを提供するパッケージです。 このパッケージを使用する場合は、プロバイダー パッケージをインストールする必要はありません。

[Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) パッケージは、ASP.NET Core ではなく、ASP.NET 4.x 用です。

詳細については、次のリソースを参照してください。

* [Application Insights の概要](/azure/application-insights/app-insights-overview)
* [Application Insights for ASP.NET Core アプリケーション](/azure/azure-monitor/app/asp-net-core) - ログ記録と共に完全な Application Insights テレメトリを実装する場合は、ここから開始します。
* [ApplicationInsightsLoggerProvider for .NET Core ILogger ログ](/azure/azure-monitor/app/ilogger) - ログ プロバイダーを実装し、Application Insights テレメトリのそれ以外の部分は除く場合は、ここから開始します。
* [Application Insights のログ アダプター](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs)。
* [Application Insights SDK のインストール、構成、および初期化](/learn/modules/instrument-web-app-code-with-application-insights) - Microsoft Learn サイト上にある対話型のチュートリアルです。

## <a name="third-party-logging-providers"></a>サードパーティ製のログ プロバイダー

ASP.NET Core で使用できるサードパーティ製のログ記録フレームワークをいくつか紹介します。

* [elmah.io](https://elmah.io/) ([GitHub リポジトリ](https://github.com/elmahio/Elmah.Io.Extensions.Logging))
* [Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub リポジトリ](https://github.com/mattwcole/gelf-extensions-logging))
* [JSNLog](https://jsnlog.com/) ([GitHub リポジトリ](https://github.com/mperdeck/jsnlog))
* [KissLog.net](https://kisslog.net/) ([GitHub リポジトリ](https://github.com/catalingavan/KissLog-net))
* [Log4Net](https://logging.apache.org/log4net/) ([GitHub リポジトリ](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))
* [Loggr](https://loggr.net/) ([GitHub リポジトリ](https://github.com/imobile3/Loggr.Extensions.Logging))
* [NLog](https://nlog-project.org/) ([GitHub リポジトリ](https://github.com/NLog/NLog.Extensions.Logging))
* [PLogger](https://www.nuget.org/packages/InvertedSoftware.PLogger.Core/) ([GitHub リポジトリ](https://github.com/invertedsoftware/InvertedSoftware.PLogger.Core))
* [Sentry](https://sentry.io/welcome/) ([GitHub リポジトリ](https://github.com/getsentry/sentry-dotnet))
* [Serilog](https://serilog.net/) ([GitHub リポジトリ](https://github.com/serilog/serilog-aspnetcore))
* [Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github リポジトリ](https://github.com/googleapis/google-cloud-dotnet))

一部のサードパーティ製フレームワークは、[セマンティック ログ記録 (構造化ログ記録とも呼ばれます)](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging) を実行できます。

サード パーティ製フレームワークを使用することは、組み込みのプロバイダーのいずれかを使用することと似ています。

1. プロジェクトに NuGet パッケージを追加します。
1. ログ記録フレームワークによって提供される `ILoggerFactory` 拡張メソッドを呼び出します。

詳細については、各プロバイダーのドキュメントをご覧ください。 サード パーティ製のログ プロバイダーは、Microsoft ではサポートされていません。

<a name="nhca"></a>

## <a name="non-host-console-app"></a>ホスト コンソール以外のアプリ

非 Web コンソール アプリで汎用ホストを使用する方法の例については、[バックグラウンド タスクのサンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) (<xref:fundamentals/host/hosted-services>) の *Program.cs* ファイルを参照してください。

汎用ホストを使用しないアプリのログ記録コードは、[プロバイダーの追加](#add-providers)方法と[ロガーの作成](#create-logs)方法によって異なります。 

### <a name="logging-providers"></a>ログ プロバイダー

ホスト コンソール以外のアプリでは、`LoggerFactory` を作成するときにプロバイダーの `Add{provider name}` 拡張メソッドを呼び出します。

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=11-12)]

### <a name="create-logs"></a>ログを作成する

ログを作成するには、<xref:Microsoft.Extensions.Logging.ILogger%601> オブジェクトを使用します。 `ILogger` を作成するには、`LoggerFactory` を使用します。

次の例では、カテゴリが `LoggingConsoleApp.Program` のロガーを作成します。

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=14)]

次の ASP.NET Core の例では、ロガーを使用して、レベルが `Information` のログを作成します。 ログの "*レベル*" は、ログに記録されるイベントの重大度を示します。

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=15)]

[レベル](#log-level)と[カテゴリ](#log-category)については、このドキュメントで詳しく説明しています。

<a name="lhc"></a>

## <a name="log-during-host-construction"></a>ホストの構築時のログ記録

ホストの構築時のログ記録は、直接サポートされていません。 ただし、別のロガーを使用することができます。 次の例では、`CreateHostBuilder` でログを記録するために、[Serilog](https://serilog.net/) ロガーが使用されています。 `AddSerilog` では、`Log.Logger` で指定された静的な構成が使用されます。

```csharp
using System;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Hosting;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args)
    {
        var builtConfig = new ConfigurationBuilder()
            .AddJsonFile("appsettings.json")
            .AddCommandLine(args)
            .Build();

        Log.Logger = new LoggerConfiguration()
            .WriteTo.Console()
            .WriteTo.File(builtConfig["Logging:FilePath"])
            .CreateLogger();

        try
        {
            return Host.CreateDefaultBuilder(args)
                .ConfigureServices((context, services) =>
                {
                    services.AddRazorPages();
                })
                .ConfigureAppConfiguration((hostingContext, config) =>
                {
                    config.AddConfiguration(builtConfig);
                })
                .ConfigureLogging(logging =>
                {   
                    logging.AddSerilog();
                })
                .ConfigureWebHostDefaults(webBuilder =>
                {
                    webBuilder.UseStartup<Startup>();
                });
        }
        catch (Exception ex)
        {
            Log.Fatal(ex, "Host builder error");

            throw;
        }
        finally
        {
            Log.CloseAndFlush();
        }
    }
}
```

<a name="csdi"></a>

## <a name="configure-a-service-that-depends-on-ilogger"></a>ILogger に依存するサービスを構成する

ASP.NET Core の以前のバージョンでは Web ホスト用に別の DI コンテナーが作成されるため、ロガーの `Startup` へのコンストラクター挿入が機能します。 汎用ホストに対して 1 つのコンテナーのみが作成される理由については、[破壊的変更に関するお知らせ](https://github.com/aspnet/Announcements/issues/353)に関する記事を参照してください。

`ILogger<T>` に依存するサービスを構成するには、コンストラクターの挿入を使用するか、ファクトリ メソッドを指定します。 ファクトリ メソッドの方法は、他の選択肢がない場合にのみお勧めします。 たとえば、DI によって提供される `ILogger<T>` インスタンスを必要とするサービスについて考えてみます。

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup2.cs?name=snippet_ConfigureServices&highlight=6-10)]

上記の強調表示されているコードは、DI コンテナーで `MyService` のインスタンスを初めて構築する必要があるときに実行される [Func](/dotnet/api/system.func-2) です。 この方法では、任意の登録済みサービスにアクセスできます。

<a name="clms"></a>

## <a name="create-logs-in-main"></a>Main でログを作成する

次のコードでは、ホストを構築した後に DI から `ILogger` インスタンスを取得することによって `Main` でログを記録します。

[!code-csharp[](index/samples/3.x/TodoApiDTO/Program.cs?name=snippet_LogProgram)]

### <a name="create-logs-in-startup"></a>Startup でログを作成する

次のコードは、`Startup.Configure` にログを書き込みます。

[!code-csharp[](index/samples/3.x/TodoApiDTO/Startup.cs?name=snippet_Configure)]

`Startup.ConfigureServices` メソッドでの DI コンテナーの設定が完了する前にログを書き込むことはサポートされていません。

* `Startup` コンストラクターへのロガーの挿入はサポートされていません。
* `Startup.ConfigureServices` メソッド シグネチャへのロガーの挿入はサポートされていません

この制限の理由は、ログ記録は DI と構成に依存しており、さらに構成は DI に依存しているためです。 DI コンテナーは、`ConfigureServices` が完了するまで設定されません。

`ILogger<T>` に依存するサービスの構成、または `Startup` へのロガーのコンストラクター挿入が以前のバージョンで機能した理由については、[ILogger に依存するサービスの構成](#csdi)に関するページを参照してください。

### <a name="no-asynchronous-logger-methods"></a>非同期でないロガー メソッド

ログ記録は高速に実行され、非同期コードのパフォーマンス コストを下回る必要があります。 ログ データ ストアが低速の場合は、そこへ直接書き込まないでください。 まずログ メッセージを高速なストアに書き込んでから、後で低速なストアに移動することを検討してください。 たとえば、SQL Server にログを記録する場合、`Log` メソッドは同期しているため、`Log` メソッドで直接それを行わないでください。 代わりに、ログ メッセージをインメモリ キューに同期的に追加し、バックグラウンド ワーカーにキューからメッセージをプルさせて、SQL Server にデータをプッシュする非同期処理を実行させます。 詳細については、[この](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub の問題を参照してください。

<a name="clib"></a>

## <a name="change-log-levels-in-a-running-app"></a>実行中のアプリのログ レベルを変更する

ログ API には、アプリの実行中にログ レベルを変更するシナリオは含まれていません。 ただし、一部の構成プロバイダーは構成を再読み込みすることができ、ログ構成に直ちに影響します。 たとえば、[ファイル構成プロバイダー](xref:fundamentals/configuration/index#file-configuration-provider)では、既定でログ構成が再度読み込まれます。 アプリの実行中にコードの構成が変更された場合、アプリは [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) を呼び出して、アプリのログ構成を更新できます。

## <a name="ilogger-and-iloggerfactory"></a>ILogger と ILoggerFactory

<xref:Microsoft.Extensions.Logging.ILogger%601> と <xref:Microsoft.Extensions.Logging.ILoggerFactory> のインターフェイスと実装は .NET Core SDK に含まれています。 これらは、次の NuGet パッケージでも入手できます。  

* インターフェイスは、[Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/) にあります。
* 既定の実装は、[Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) にあります。

<!-- review. Why would you want to hard code filtering rules in code? -->
<a name="fric"></a>

## <a name="apply-log-filter-rules-in-code"></a>コードでログ フィルター規則を適用する

ログ フィルター規則を設定するには、[構成](xref:fundamentals/configuration/index)を使用することをお勧めします。

コードにフィルター規則を登録する方法を次の例に示します。

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_FilterInCode)]

`logging.AddFilter("System", LogLevel.Debug)` は `System` カテゴリとログ レベル `Debug` を指定します。 特定のプロバイダーが構成されていないため、フィルターはすべてのプロバイダーに適用されます。

`AddFilter<DebugLoggerProvider>("Microsoft", LogLevel.Information)` は以下を指定します。

* `Debug` ログ プロバイダー。
* ログ レベル `Information` 以上。
* `"Microsoft"` で始まるすべてのカテゴリ。

## <a name="create-a-custom-logger"></a>カスタム ロガーを作成する

カスタム ロガーを追加するには、<xref:Microsoft.Extensions.Logging.ILoggerFactory> を使用して <xref:Microsoft.Extensions.Logging.ILoggerProvider> を追加します。

```csharp
public void Configure(
    IApplicationBuilder app,
    IWebHostEnvironment env,
    ILoggerFactory loggerFactory)
{
    loggerFactory.AddProvider(new CustomLoggerProvider(new CustomLoggerConfiguration()));
```

`ILoggerProvider` により、1 つ以上の `ILogger` インスタンスが作成されます。 `ILogger` インスタンスは、情報をログに記録するためにフレームワークによって使用されます。

### <a name="sample-custom-logger-configuration"></a>カスタム ロガーの構成のサンプル

サンプルでは次のことが行われます。

* イベント ID とログ レベルによってログ コンソールの色を設定する非常に基本的なサンプルとして設計されています。 ロガーは通常、イベント ID によって変更されることはなく、ログ レベルに固有のものではありません。
* 次の構成の種類を使用して、ログ レベルとイベント ID ごとに異なるカラー コンソール エントリを作成します。

[!code-csharp[](index/samples/3.x/CustomLogger/ColorConsoleLogger/ColorConsoleLoggerConfiguration.cs?name=snippet)]

上記のコードでは、既定のレベルを `Warning` に設定し、色を `Yellow` に設定しています。 `EventId` が 0 に設定されている場合は、すべてのイベントをログに記録します。

### <a name="create-the-custom-logger"></a>カスタム ロガーを作成する

`ILogger` 実装カテゴリ名は、通常、ログ ソースです。 たとえば、ロガーが作成される型は次のようになります。

[!code-csharp[](index/samples/3.x/CustomLogger/ColorConsoleLogger/ColorConsoleLogger.cs?name=snippet)]

上記のコードでは次の操作が行われます。

* カテゴリ名ごとにロガー インスタンスを作成します。
* 各 `logLevel` に一意のロガーがあるようにするため、`IsEnabled` で `logLevel == _config.LogLevel` を確認します。 一般に、ロガーは、すべての上位ログ レベルに対しても有効にする必要があります。

```csharp
public bool IsEnabled(LogLevel logLevel)
{
    return logLevel >= _config.LogLevel;
}
```

### <a name="create-the-custom-loggerprovider"></a>カスタム LoggerProvider を作成する

`LoggerProvider` は、ロガー インスタンスを作成するクラスです。 カテゴリごとにロガー インスタンスを作成する必要はないかもしれませんが、これは NLog や log4net などの一部のロガーでは理にかなっています。 これを行うと、必要に応じて、カテゴリごとに異なるログ出力ターゲットを選択することもできます。

[!code-csharp[](index/samples/3.x/CustomLogger/ColorConsoleLogger/ColorConsoleLoggerProvider.cs?name=snippet)]

上記のコードでは、<xref:Microsoft.Build.Logging.LoggerDescription.CreateLogger*> によってカテゴリ名ごとに `ColorConsoleLogger` のインスタンスが 1 つ作成され、それが [`ConcurrentDictionary<TKey,TValue>`](/dotnet/api/system.collections.concurrent.concurrentdictionary-2) に格納されます。

### <a name="usage-and-registration-of-the-custom-logger"></a>カスタム ロガーの使用と登録

`Startup.Configure` にロガーを登録します。

[!code-csharp[](index/samples/3.x/CustomLogger/Startup.cs?name=snippet)]

上記のコードでは、`ILoggerFactory` に少なくとも 1 つの拡張メソッドを指定します。

[!code-csharp[](index/samples/3.x/CustomLogger/ColorConsoleLogger/ColorConsoleLoggerExtensions.cs?name=snippet)]

## <a name="additional-resources"></a>その他の技術情報

* <xref:fundamentals/logging/loggermessage>
* ログのバグは、[github.com/dotnet/runtime/](https://github.com/dotnet/runtime/issues) リポジトリに作成する必要があります。
* <xref:blazor/fundamentals/logging>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

作成者: [Tom Dykstra](https://github.com/tdykstra)、[Steve Smith](https://ardalis.com/)

.NET Core では、組み込みやサード パーティ製のさまざまなログ プロバイダーと連携するログ API がサポートされています。 この記事では、組み込みプロバイダーと共にログ API を使用する方法について説明します。

[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

## <a name="add-providers"></a>プロバイダーを追加する

ログ プロバイダーによってログが表示または格納されます。 たとえば、Console プロバイダーによってコンソール上にログが表示され、Azure Application Insights プロバイダーによってそれらが Azure Application Insights に格納されます。 複数のプロバイダーを追加することで、複数の宛先にログを送信することができます。

プロバイダーを追加するには、*Program.cs* でプロバイダーの `Add{provider name}` 拡張メソッドを呼び出します。

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=18-20)]

上記のコードには、`Microsoft.Extensions.Logging` と `Microsoft.Extensions.Configuration` への参照が必要です。

既定のプロジェクト テンプレートでは、次のログ プロバイダーを追加する <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> が呼び出されます。

* コンソール
* デバッグ
* EventSource (ASP.NET Core 2.2 以降)

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_TemplateCode&highlight=7)]

`CreateDefaultBuilder` を使用する場合は、既定のプロバイダーを自分で選択したものと置き換えることができます。 <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> を呼び出し、目的のプロバイダーを追加します。

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=18-22)]

この記事の後半では、[組み込みログ プロバイダー](#built-in-logging-providers)と[サードパーティ製ログ プロバイダー](#third-party-logging-providers)について説明します。

## <a name="create-logs"></a>ログを作成する

ログを作成するには、<xref:Microsoft.Extensions.Logging.ILogger%601> オブジェクトを使用します。 Web アプリまたはホステッド サービスで、依存関係の挿入 (DI) から `ILogger` を取得します。 ホスト コンソール以外のアプリでは、`LoggerFactory` を使用して `ILogger` を作成します。

次の ASP.NET Core の例では、カテゴリが `TodoApiSample.Pages.AboutModel` のロガーを作成します。 ログの "*カテゴリ*" は、各ログに関連付けられている文字列です。 DI で提供される `ILogger<T>` インスタンスでは、カテゴリとして型 `T` の完全修飾名を持つログが作成されます。 

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

次の ASP.NET Core とコンソール アプリの例では、ロガーを使用して、レベルが `Information` のログを作成します。 ログの "*レベル*" は、ログに記録されるイベントの重大度を示します。

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

[レベル](#log-level)と[カテゴリ](#log-category)の詳細については、この記事で後ほど説明します。

### <a name="create-logs-in-startup"></a>Startup でログを作成する

`Startup` クラスでログを作成するには、コンストラクター シグネチャに `ILogger` パラメーターを追加します。

[!code-csharp[](index/samples/2.x/TodoApiSample/Startup.cs?name=snippet_Startup&highlight=3,5,8,20,27)]

### <a name="create-logs-in-the-program-class"></a>Program クラスでログを作成する

`Program` クラスでログを作成するには、DI から `ILogger` インスタンスを取得します。

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

ホストの構築時のログ記録は、直接サポートされていません。 ただし、別のロガーを使用することができます。 次の例では、`CreateWebHostBuilder` でログを記録するために、[Serilog](https://serilog.net/) ロガーが使用されています。 `AddSerilog` では、`Log.Logger` で指定された静的な構成が使用されます。

```csharp
using System;
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args)
    {
        var builtConfig = new ConfigurationBuilder()
            .AddJsonFile("appsettings.json")
            .AddCommandLine(args)
            .Build();

        Log.Logger = new LoggerConfiguration()
            .WriteTo.Console()
            .WriteTo.File(builtConfig["Logging:FilePath"])
            .CreateLogger();

        try
        {
            return WebHost.CreateDefaultBuilder(args)
                .ConfigureServices((context, services) =>
                {
                    services.AddMvc();
                })
                .ConfigureAppConfiguration((hostingContext, config) =>
                {
                    config.AddConfiguration(builtConfig);
                })
                .ConfigureLogging(logging =>
                {
                    logging.AddSerilog();
                })
                .UseStartup<Startup>();
        }
        catch (Exception ex)
        {
            Log.Fatal(ex, "Host builder error");

            throw;
        }
        finally
        {
            Log.CloseAndFlush();
        }
    }
}
```

### <a name="no-asynchronous-logger-methods"></a>非同期でないロガー メソッド

ログ記録は高速に実行され、非同期コードのパフォーマンス コストを下回る必要があります。 ログ記録のデータ ストアが低速の場合は、そこへ直接書き込むべきではありません。 まずログ メッセージを高速なストアに書き込んでから、後で低速なストアに移動する方法を検討してください。 たとえば、SQL Server にログインしている場合、それを `Log` メソッドで直接実行したくはないでしょう。`Log` が同期メソッドであるためです。 代わりに、ログ メッセージをインメモリ キューに同期的に追加し、バックグラウンド ワーカーにキューからメッセージをプルさせて、SQL Server にデータをプッシュする非同期処理を実行させます。 詳細については、[この](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub の問題を参照してください。

## <a name="configuration"></a>構成

ログ プロバイダーの構成は、1 つまたは複数の構成プロバイダーによって提供されます。

* ファイル形式 (INI、JSON、および XML)。
* コマンド ライン引数。
* 環境変数。
* メモリ内 .NET オブジェクト。
* 暗号化されていない[シークレット マネージャー](xref:security/app-secrets)の記憶域。
* [Azure Key Vault](xref:security/key-vault-configuration) などの暗号化されたユーザー ストア。
* カスタム プロバイダー (インストール済みまたは作成済み)。

たとえば、一般的に、ログの構成はアプリ設定ファイルの `Logging` セクションで指定されます。 次の例は、一般的な *appsettings.Development.json* ファイルの内容を示しています。

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Debug",
      "System": "Information",
      "Microsoft": "Information"
    },
    "Console":
    {
      "IncludeScopes": true
    }
  }
}
```

`Logging` プロパティには `LogLevel` およびログ プロバイダーのプロパティ (Console が示されています) を含めることができます。

`Logging` の下の `LogLevel` プロパティでは、選択したカテゴリに対するログの最小の[レベル](#log-level)が指定されます。 この例では、`System` と `Microsoft` カテゴリが `Information` レベルで、その他はすべて `Debug` レベルでログに記録します。

`Logging` の下のその他のプロパティではログ プロバイダーが指定されます。 この例では、Console プロバイダーです。 プロバイダーで[ログのスコープ](#log-scopes)がサポートされている場合、`IncludeScopes` によってそれを有効にするかどうかが指定されます。 プロバイダーのプロパティ (例の `Console` など) では、`LogLevel` プロパティが指定される場合があります。 プロバイダーの下の `LogLevel` では、そのプロバイダーのログのレベルが指定されます。

`Logging.{providername}.LogLevel` でレベルが指定される場合、それによって `Logging.LogLevel` で設定されたものはすべてオーバーライドされます。 たとえば、次の JSON について考えます。

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.MSFT.json)]

上記の JSON では、`Console` プロバイダーの設定によって上記の (既定の) ログ レベルがオーバーライドされます。

ログ API には、アプリの実行中にログ レベルを変更するシナリオは含まれていません。 ただし、一部の構成プロバイダーは構成を再読み込みすることができ、ログ構成に直ちに影響します。 たとえば、設定ファイルを読み取るために `CreateDefaultBuilder` によって追加される [ファイル構成プロバイダー](xref:fundamentals/configuration/index#file-configuration-provider)は、既定でログ構成を再読み込みします。 アプリの実行中にコードの構成が変更された場合、アプリは [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) を呼び出して、アプリのログ構成を更新できます。

構成プロバイダーの実装について詳しくは、<xref:fundamentals/configuration/index> をご覧ください。

## <a name="sample-logging-output"></a>サンプルのログ記録の出力

前のセクションで紹介したサンプル コードでは、コマンド ラインからアプリを実行するとコンソールにログが表示されます。 コンソールの出力例は次のとおりです。

```console
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:5000/api/todo/0
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
info: TodoApi.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 42.9286ms
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[2]
      Request finished in 148.889ms 404
```

前のログは、`http://localhost:5000/api/todo/0` のサンプル アプリに向けて HTTP Get 要求を作成することで生成されました。

Visual Studio でサンプル アプリを実行したときに [デバッグ] ウィンドウに表示されるログと同じログの例を、次に示します。

```console
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request starting HTTP/1.1 GET http://localhost:53104/api/todo/0  
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
TodoApi.Controllers.TodoController:Information: Getting item 0
TodoApi.Controllers.TodoController:Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult:Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 152.5657ms
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 316.3195ms 404
```

前のセクションで紹介した `ILogger` の呼び出しで作成されるログは、"TodoApi" から始まります。 "Microsoft" カテゴリから始まるログは、ASP.NET Core のフレームワークのコードからのログです。 ASP.NET Core とアプリケーション コードでは、同じログ API とログ プロバイダーが使用されています。

以降、この記事では、ログ記録の詳細とオプションについて説明します。

## <a name="nuget-packages"></a>NuGet パッケージ

`ILogger` および `ILoggerFactory` インターフェイスは、[Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/) 内にあり、それらの既定の実装は [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) 内にあります。

## <a name="log-category"></a>ログのカテゴリ

`ILogger` オブジェクトが作成されるときに、"*カテゴリ*" が指定されます。 このカテゴリは、`ILogger` のインスタンスによって作成される各ログ メッセージと共に含められます。 カテゴリには任意の文字列を指定できますが、"TodoApi.Controllers.TodoController" などのクラス名を使用するのが慣例です。

`ILogger<T>` を使用して、カテゴリとして `T` の完全修飾型名が使用される `ILogger` インスタンスを取得します。

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

カテゴリを明示的に指定するには、`ILoggerFactory.CreateLogger` を呼び出します。

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

`ILogger<T>` は、`T` の完全修飾型名を使用した `CreateLogger` の呼び出しと同じです。

## <a name="log-level"></a>ログ レベル

すべてのログで <xref:Microsoft.Extensions.Logging.LogLevel> 値が指定されます。 ログ レベルは、重大度または重要度を示します。 たとえば、メソッドが正常に終了した場合は `Information` ログを、メソッドが *404 Not Found* 状態コードを返した場合は `Warning` ログを書き込む場合があります。

`Information` および `Warning` ログを作成するコードを次に示します。

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

上記のコードでは、`MyLogEvents.GetItem` パラメーターと `MyLogEvents.GetItemNotFound` パラメーターは [ログ イベント ID](#log-event-id)です。 2 つ目のパラメーターは、他のメソッド パラメーターによって提供される引数値のプレースホルダーを含むメッセージ テンプレートです。 メソッド パラメーターについては、この記事の「[ログ メッセージ テンプレート](#lmt)」のセクションで説明されています。

メソッド名にレベルを含むログ メソッド (たとえば `LogInformation` や `LogWarning`) は、[ILogger の拡張メソッド](xref:Microsoft.Extensions.Logging.LoggerExtensions)です。 これらのメソッドでは、`LogLevel` パラメーターを受け取る `Log` メソッドが呼び出されます。 これらの拡張メソッドのいずれかではなく、`Log` メソッドを直接呼び出すことができますが、構文は比較的複雑です。 詳細については、<xref:Microsoft.Extensions.Logging.ILogger> および[ロガー拡張ソース コード](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs)に関するページを参照してください。

ASP.NET Core には、次のログ レベルが定義されています (重大度の低いものから高い順)。

* Trace = 0

  通常はデバッグでのみ役立つ情報の場合。 これらのメッセージには機密性の高いアプリケーション データが含まれる可能性があるため、運用環境は有効にしないことをお勧めします。 *既定で無効です。*

* Debug = 1

  開発とデバッグで役立つ可能性がある情報の場合。 例:`Entering method Configure with flag set to true.``Debug` レベルのログは、ログのサイズが大きくなるため、トラブルシューティングの場合を除き運用環境では有効にしません。

* Information = 2

  アプリの一般的なフローを追跡する場合。 通常、これらのログには、長期的な値があります。 例 : `Request received for path /api/todo`

* Warning = 3

  アプリのフローで異常なイベントや予期しないイベントが発生した場合。 アプリの停止の原因にはならないが、調査する必要があるエラーやその他の状態がここに含まれる場合があります。 `Warning` ログ レベルが使用される一般的な場所として、例外の処理があります。 例 : `FileNotFoundException for file quotes.txt.`

* Error = 4

  処理できないエラーと例外の場合。 これらのメッセージは、アプリ全体のエラーではなく、現在のアクティビティまたは操作 (現在の HTTP 要求など) におけるエラーを示します。 ログ メッセージの例: `Cannot insert record due to duplicate key violation.`

* Critical = 5

  即時の注意が必要なエラーの場合。 例: データ損失のシナリオ、ディスク領域不足。

ログ レベルを使用して、特定のストレージ メディアまたは表示ウィンドウに書き込むログの出力量を制御します。 次に例を示します。

* 運用環境:
  * `Trace` から `Information` までのレベルでログを記録すると、詳細なログ メッセージが大量に生成されます。 コストを制御し、データ ストレージの上限を超えないようにするには、`Trace` から `Information` のレベルのメッセージを、大量の低コストのデータ ストアに記録します。
  * `Warning` から `Critical` までのレベルでログを記録すると、通常はより少ないログ メッセージが生成されます。 そのため、コストとストレージの制限は通常は問題にならないため、データ ストアの選択肢がより柔軟になります。
* 開発中:
  * コンソールに `Warning` から `Critical` のメッセージを記録します。
  * トラブルシューティングの際に `Trace` から `Information` のメッセージを追加します。

この記事で後述する「[ログのフィルター処理](#log-filtering)」セクションでは、プロバイダーで処理するログ レベルの制御方法について説明します。

ASP.NET Core では、フレームワーク イベントのログが書き込まれます。 この記事の前のログの例では、`Information` レベル以下のログを除外したため、`Debug` または `Trace` レベルのログは作成されませんでした。 `Debug` ログを示すために構成したサンプル アプリを実行することで生成されるコンソールのログの例を、次に示します。

```console
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:62555/api/todo/0
dbug: Microsoft.AspNetCore.Routing.Tree.TreeRouter[1]
      Request successfully matched the route with name 'GetTodo' and template 'api/Todo/{id}'.
dbug: Microsoft.AspNetCore.Mvc.Internal.ActionSelector[2]
      Action 'TodoApi.Controllers.TodoController.Update (TodoApi)' with id '089d59b6-92ec-472d-b552-cc613dfd625d' did not match the constraint 'Microsoft.AspNetCore.Mvc.Internal.HttpMethodActionConstraint'
dbug: Microsoft.AspNetCore.Mvc.Internal.ActionSelector[2]
      Action 'TodoApi.Controllers.TodoController.Delete (TodoApi)' with id 'f3476abe-4bd9-4ad3-9261-3ead09607366' did not match the constraint 'Microsoft.AspNetCore.Mvc.Internal.HttpMethodActionConstraint'
dbug: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action TodoApi.Controllers.TodoController.GetById (TodoApi)
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
info: TodoApi.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
dbug: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action method TodoApi.Controllers.TodoController.GetById (TodoApi), returned result Microsoft.AspNetCore.Mvc.NotFoundResult.
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 0.8788ms
dbug: Microsoft.AspNetCore.Server.Kestrel[9]
      Connection id "0HL6L7NEFF2QD" completed keep alive response.
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[2]
      Request finished in 2.7286ms 404
```

## <a name="log-event-id"></a>ログ イベント ID

各ログで "*イベント ID*" を指定できます。 サンプル アプリでは、この処理にローカルで定義された `LoggingEvents` クラスを使用します。

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

イベント ID によって一連のイベントが関連付けられます。 たとえば、ページ上に項目の一覧を表示する機能に関連するすべてのログを 1001 に設定します。

ログ プロバイダーでは、ID フィールドやログ メッセージにイベント ID が格納されたり、またはまったく格納されなかったりする場合があります。 Debug プロバイダーでイベント ID が表示されることはありません。 Console プロバイダーでは、カテゴリの後のブラケット内にイベント ID が表示されます。

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a>ログ メッセージ テンプレート

各ログでメッセージ テンプレートが指定されます。 メッセージ テンプレートには、指定される引数のためのプレースホルダーを含めることができます。 プレースホルダーには、数値ではなく名前を使用します。

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

プレースホルダーの名前ではなく、プレースホルダーの順序によって、値の指定に使用されるパラメーターが決まります。 次のコードでは、パラメーター名がメッセージ テンプレート内のシーケンスの外にあることに注意してください。

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

このコードでは、シーケンス内のパラメーター値を含むログ メッセージが作成されます。

```text
Parameter values: parm1, parm2
```

ログ プロバイダーが[セマンティック ログ記録 (または構造化ログ記録)](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)を実装できるようにするために、ログ フレームワークはこのように動作します。 書式設定されたメッセージ テンプレートだけでなく、引数自体がログ システムに渡されます。 この情報により、ログ プロバイダーはフィールドとしてパラメーター値を格納することができます。 たとえば、つぎのようなロガー メソッドの呼び出しがあるとします。

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

Azure Table Storage にログを送信する場合、各 Azure Table エンティティに `ID` および `RequestTime` プロパティを指定し、ログ データのクエリを簡略化することができます。 クエリによって指定した `RequestTime` の範囲内のすべてのログを検索できます。テキスト メッセージから時間を解析する必要はありません。

## <a name="logging-exceptions"></a>ログ記録の例外

ロガー メソッドには、次の例のように、例外で渡すことができるオーバーロードがあります。

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

プロバイダーごとに、例外情報の処理方法は異なります。 前述のコードの Debug プロバイダーの出力の例を次に示します。

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a>ログのフィルター処理

特定のプロバイダーとカテゴリ、またはすべてのプロバイダーまたはすべてのカテゴリに最小ログ レベルを指定できます。 最小レベルを下回るログは、そのプロバイダーに渡されないので、表示または保存されません。

すべてのログを抑制するには、最小ログ レベルに `LogLevel.None` を指定します。 `LogLevel.None` の整数値は 6 であり、`LogLevel.Critical` (5) を超えます。

### <a name="create-filter-rules-in-configuration"></a>構成にフィルター規則を作成する

プロジェクト テンプレート コードは `CreateDefaultBuilder` を呼び出して、コンソール、デバッグ、EventSource (ASP.NET Core 2.2 以降) のプロバイダーのログ記録を設定します。 `Logging`この記事で既に説明[したように、`CreateDefaultBuilder` メソッドでは、](#configuration) セクションで構成を検索するようにログが設定されます。

次の例のように、構成データでは、プロバイダーとカテゴリごとに最小ログ レベルを指定します。

[!code-json[](index/samples/2.x/TodoApiSample/appsettings.json)]

この JSON では、6 個のフィルター規則を作成します。1 つは Debug プロバイダー用、4 つは Console プロバイダー用、1 つはすべてのプロバイダー用です。 `ILogger` オブジェクトが作成されると、各プロバイダーに対して 1 つの規則が選択されます。

### <a name="filter-rules-in-code"></a>コードのフィルター規則

コードにフィルター規則を登録する方法を次の例に示します。

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

2 つ目の `AddFilter` では、プロバイダーの種類名を使用して Debug プロバイダーを指定します。 1 つ目の `AddFilter` は、プロバイダーの種類を指定していないため、すべてのプロバイダーに適用されます。

### <a name="how-filtering-rules-are-applied"></a>フィルター規則を適用する方法

前の例の構成データと `AddFilter` コードでは、次の表に示す規則を作成します。 最初の 6 つは構成例、最後の 2 つはコード例のものです。

| 数値 | プロバイダー      | 以下から始まるカテゴリ          | 最小ログ レベル |
| :----: | ------------- | --------------------------------------- | ----------------- |
| 1      | デバッグ         | すべてのカテゴリ                          | 情報       |
| 2      | コンソール       | Microsoft.AspNetCore.Mvc.Razor.Internal | 警告           |
| 3      | コンソール       | Microsoft.AspNetCore.Mvc.Razor.Razor    | デバッグ             |
| 4      | コンソール       | Microsoft.AspNetCore.Mvc.Razor          | Error             |
| 5      | コンソール       | すべてのカテゴリ                          | 情報       |
| 6      | すべてのプロバイダー | すべてのカテゴリ                          | デバッグ             |
| 7      | すべてのプロバイダー | システム                                  | デバッグ             |
| 8      | デバッグ         | Microsoft                               | トレース             |

`ILogger` オブジェクトを作成すると、`ILoggerFactory` オブジェクトによって、そのロガーに適用するプロバイダーごとに 1 つの規則が選択されます。 `ILogger` インスタンスによって書き込まれるすべてのメッセージは、選択した規則に基づいてフィルター処理されます。 使用できる規則から、各プロバイダーとカテゴリのペアごとに該当する最も限定的な規則が選択されます。

特定のカテゴリに `ILogger` が作成されるときに、各プロバイダーに次のアルゴリズムが使用されます。

* プロバイダーとそのエイリアスと一致するすべての規則が選択されます。 一致が見つからない場合は、空のプロバイダーですべての規則が選択されます。
* 前の手順の結果、最も長いカテゴリのプレフィックスが一致する規則が選択されます。 一致が見つからない場合は、カテゴリを指定しないすべての規則が選択されます。
* 複数の規則が選択されている場合は、**最後**の 1 つが使用されます。
* 規則が選択されていない場合は、`MinimumLevel` が使用されます。

前の規則一覧を使用して、カテゴリ "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine" に `ILogger` オブジェクトを作成するとします。

* Debug プロバイダーの場合、規則 1、6、8 が適用されます。 規則 8 が最も限定的なので、規則 8 が選択されます。
* Console プロバイダーの場合、規則 3、4、5、6 が適用されます。 規則 3 が最も限定的です。

作成される `ILogger` インスタンスでは、Debug プロバイダーに `Trace` レベル以上のログが送信されます。 `Debug` レベル以上のログが Console プロバイダーに送信されます。

### <a name="provider-aliases"></a>プロバイダーのエイリアス

各プロバイダーでは "*エイリアス*" が定義されます。これは構成で完全修飾型名の代わりに使用できます。  組み込みのプロバイダーの場合は、次のエイリアスを使用してください。

* コンソール
* デバッグ
* EventSource
* EventLog
* TraceSource
* AzureAppServicesFile
* AzureAppServicesBlob
* ApplicationInsights

### <a name="default-minimum-level"></a>既定の最小レベル

指定したプロバイダーとカテゴリに適用される構成またはコードの規則がない場合にのみ反映される最小レベルの設定があります。 最小レベルを設定する方法を次の例に示します。

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

最小レベルを明示的に設定しない場合、既定値は `Information` です。これは、`Trace` および `Debug` ログが無視されることを意味します。

### <a name="filter-functions"></a>フィルター関数

フィルター関数は、構成またはコードによって規則が割り当てられていないすべてのプロバイダーとカテゴリについて呼び出されます。 関数内のコードから、プロバイダーの種類、カテゴリ、ログ レベルにアクセスすることができます。 次に例を示します。

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

## <a name="system-categories-and-levels"></a>システムのカテゴリとレベル

ASP.NET Core と Entity Framework Core によって使用されるいくつかのカテゴリと、それらから想定されるログの種類に関するメモを、次に示します。

| カテゴリ                            | メモ |
| ----------------------------------- | ----- |
| Microsoft.AspNetCore                | ASP.NET Core の一般的な診断。 |
| Microsoft.AspNetCore.DataProtection | どのキーが検討、検索、および使用されたか。 |
| Microsoft.AspNetCore.HostFiltering  | 許可されるホスト。 |
| Microsoft.AspNetCore.Hosting        | HTTP 要求が完了するまでにかかった時間と、それらの開始時刻。 どのホスティング スタートアップ アセンブリが読み込まれたか。 |
| Microsoft.AspNetCore.Mvc            | MVC と Razor の診断。 モデルの構築、フィルター処理の実行、ビューのコンパイル、アクションの選択。 |
| Microsoft.AspNetCore.Routing        | 一致する情報をルーティングします。 |
| Microsoft.AspNetCore.Server         | 接続の開始、停止、キープ アライブ応答。 HTTPS 証明書情報。 |
| Microsoft.AspNetCore.StaticFiles    | 提供されるファイル。 |
| Microsoft.EntityFrameworkCore       | Entity Framework Core の一般的な診断。 データベースのアクティビティと構成、変更の検出、移行。 |

## <a name="log-scopes"></a>ログのスコープ

 "*スコープ*" では、論理操作のセットをグループ化できます。 このグループ化を使用して、セットの一部として作成される各ログに同じデータをアタッチすることができます。 たとえば、トランザクション処理の一部として作成されるすべてのログに、トランザクション ID を含めることができます。

スコープは <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> メソッドから返される `IDisposable` の種類であり、破棄されるまで継続します。 ロガーの呼び出しを `using` ブロックでラップすることによって、スコープを使用します。

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

次のコードでは、Console プロバイダーのスコープを有効にしています。

*Program.cs*:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

> [!NOTE]
> スコープベースのログ記録を有効にするには、`IncludeScopes` コンソールのロガー オプションを構成する必要があります。
>
> 構成について詳しくは、「[構成](#configuration)」セクションをご覧ください。

各ログ メッセージには、スコープ内の情報が含まれます。

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a>組み込みのログ プロバイダー

ASP.NET Core には次のプロバイダーが付属しています。

* [コンソール](#console-provider)
* [デバッグ](#debug-provider)
* [EventSource](#event-source-provider)
* [EventLog](#windows-eventlog-provider)
* [TraceSource](#tracesource-provider)
* [AzureAppServicesFile](#azure-app-service-provider)
* [AzureAppServicesBlob](#azure-app-service-provider)
* [ApplicationInsights](#azure-application-insights-trace-logging)

ASP.NET Core モジュールを使用した stdout およびデバッグ ログの詳細については、「<xref:test/troubleshoot-azure-iis>」および「<xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>」を参照してください。

### <a name="console-provider"></a>Console プロバイダー

[Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) プロバイダー パッケージは、ログ出力をコンソールに送信します。 

```csharp
logging.AddConsole();
```

コンソールのログ出力を確認するには、プロジェクト フォルダーでコマンド プロンプトを開き、次のコマンドを実行します。

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a>Debug プロバイダー

[Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) プロバイダー パッケージは、[System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) クラス (`Debug.WriteLine` メソッドの呼び出し) を使用してログの出力を書き込みます。

Linux では、このプロバイダーから */var/log/message* にログが書き込まれます。

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a>イベント ソース プロバイダー

[Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) プロバイダー パッケージでは、`Microsoft-Extensions-Logging` という名前でイベント ソース クロスプラットフォームに書き込みます。 Windows では、プロバイダーによって [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803) が使用されます。

```csharp
logging.AddEventSourceLogger();
```

イベント ソース プロバイダーは、ホストをビルドするために `CreateDefaultBuilder` が呼び出されたときに、自動的に追加されます。

ログの収集と表示には、[PerfView ユーティリティ](https://github.com/Microsoft/perfview)を使用します。 ETW ログを表示できる他のツールはありますが、ASP.NET Core から出力される ETW イベントを操作する場合、PerfView は最適なエクスペリエンスを提供します。

このプロバイダーでログに記録されるイベントを収集するように PerfView を構成するには、 **[追加プロバイダー]** の一覧に文字列 `*Microsoft-Extensions-Logging` を追加します (文字列の先頭に忘れずにアスタリスクを付けてください)。

![Perfview の追加プロバイダー](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a>Windows EventLog プロバイダー

[Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) プロバイダー パッケージは、ログ出力を Windows イベント ログに送信します。

```csharp
logging.AddEventLog();
```

[AddEventLog のオーバーロード](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions)を使用すると、<xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings> を渡すことができます。 `null` または指定しない場合は、次の既定の設定が使用されます。

* `LogName`:"Application"
* `SourceName`: ".NET Runtime"
* `MachineName`:ローカル コンピューター名が使用されます。

[警告レベル以上](#log-level)のイベントがログに記録されます。 次の例では、イベント ログの既定のログ レベルを <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType> に設定します。

```json
"Logging": {
  "EventLog": {
    "LogLevel": {
      "Default": "Information"
    }
  }
}
```

### <a name="tracesource-provider"></a>TraceSource プロバイダー

[Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) プロバイダー パッケージでは、<xref:System.Diagnostics.TraceSource> ライブラリとプロバイダーが使用されます。

```csharp
logging.AddTraceSource(sourceSwitchName);
```

[AddTraceSource オーバーロード](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions)を使用すると、ソース スイッチとトレース リスナーを渡すことができます。

このプロバイダーを使用するには、アプリを (.NET Core ではなく) .NET Framework 上で実行する必要があります。 このプロバイダーでは、サンプル アプリで使用されている <xref:System.Diagnostics.TextWriterTraceListener> など、さまざまな[リスナー](/dotnet/framework/debug-trace-profile/trace-listeners)にメッセージをルーティングさせることができます。

### <a name="azure-app-service-provider"></a>Azure App Service プロバイダー

[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) プロバイダー パッケージは、Azure App Service アプリのファイル システムのテキスト ファイルと、Azure Storage アカウントの [BLOB ストレージ](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage)にログを書き込みます。

```csharp
logging.AddAzureWebAppDiagnostics();
```

プロバイダー パッケージは、[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)には含まれません。 .NET Framework をターゲットとする場合、または `Microsoft.AspNetCore.App` を参照している場合は、プロバイダー パッケージをプロジェクトに追加します。 

<xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> のオーバーロードによって <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings> を渡すことができます。 設定オブジェクトで既定の設定をオーバーライドできます。たとえば、ログの出力テンプレート、BLOB 名、ファイル サイズの制限などです。 ("*出力テンプレート*" は、`ILogger` メソッドの呼び出しと共に指定されるものに加えて、すべてのログに適用されるメッセージ テンプレートです。)

アプリケーションを App Service アプリにデプロイすると、Azure portal の **[App Service]** ページの [[App Service ログ]](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) セクションで指定された設定が適用されます。 次の設定が更新されると、アプリの再起動や再デプロイを必要とせずに、変更がすぐに有効になります。

* **[アプリケーション ログ (ファイル システム)]**
* **[アプリケーション ログ (BLOB)]**

ログ ファイルの既定の場所は、*D:\\home\\LogFiles\\Application* です。既定のファイル名は *diagnostics-yyyymmdd.txt* です。 既定のファイル サイズ制限は 10 MB です。保持されるファイルの既定の最大数は 2 です。 既定の BLOB 名は *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt* です。

このプロバイダーは、プロジェクトが Azure 環境で実行される場合にのみ機能します。 プロジェクトをローカルで実行しても、効果はありません&mdash;BLOB のローカル ファイルまたはローカル開発ストレージへの書き込みは行われません。

#### <a name="azure-log-streaming"></a>Azure ログのストリーミング

Azure ログのストリーミングを使用すると、以下からリアル タイムでログ アクティビティを確認できます。

* アプリ サーバー
* Web サーバー
* 失敗した要求のトレース

Azure ログのストリーミングを構成するには

* アプリのポータル ページから **[App Service ログ]** ページに移動します。
* **[アプリケーション ログ (ファイル システム)]** を **[オン]** に設定します。
* ログ **[レベル]** を選択します。 この設定は、Azure ログのストリーミングにのみ適用され、アプリ内の他のログ プロバイダーには適用されません。

**[ログ ストリーム]** ページに移動して、アプリのメッセージを確認します。 これらはアプリによって、`ILogger` インターフェイスを介してログに記録されます。

### <a name="azure-application-insights-trace-logging"></a>Azure Application Insights のトレース ログ

[Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) プロバイダー パッケージでは、Azure Application Insights にログを書き込みます。 Application Insights は、Web アプリを監視するサービスであり、クエリを実行してテレメトリ データを分析するためのツールを提供します。 このプロバイダーを使用する場合は、Application Insights ツールを使ってクエリを実行し、ログを分析できます。

ログ プロバイダーは、[Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) の依存関係として組み込まれており、ASP.NET Core で利用可能なすべてのテレメトリを提供するパッケージです。 このパッケージを使用する場合は、プロバイダー パッケージをインストールする必要はありません。

ASP.NET 4.x. に対応している [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) パッケージを使用しないでください。

詳細については、次のリソースを参照してください。

* [Application Insights の概要](/azure/application-insights/app-insights-overview)
* [Application Insights for ASP.NET Core アプリケーション](/azure/azure-monitor/app/asp-net-core) - ログ記録と共に完全な Application Insights テレメトリを実装する場合は、ここから開始します。
* [ApplicationInsightsLoggerProvider for .NET Core ILogger ログ](/azure/azure-monitor/app/ilogger) - ログ プロバイダーを実装し、Application Insights テレメトリのそれ以外の部分は除く場合は、ここから開始します。
* [Application Insights のログ アダプター](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs)。
* [Application Insights SDK のインストール、構成、および初期化](/learn/modules/instrument-web-app-code-with-application-insights) - Microsoft Learn サイト上にある対話型のチュートリアルです。

## <a name="third-party-logging-providers"></a>サードパーティ製のログ プロバイダー

ASP.NET Core で使用できるサードパーティ製のログ記録フレームワークをいくつか紹介します。

* [elmah.io](https://elmah.io/) ([GitHub リポジトリ](https://github.com/elmahio/Elmah.Io.Extensions.Logging))
* [Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub リポジトリ](https://github.com/mattwcole/gelf-extensions-logging))
* [JSNLog](https://jsnlog.com/) ([GitHub リポジトリ](https://github.com/mperdeck/jsnlog))
* [KissLog.net](https://kisslog.net/) ([GitHub リポジトリ](https://github.com/catalingavan/KissLog-net))
* [Log4Net](https://logging.apache.org/log4net/) ([GitHub リポジトリ](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))
* [Loggr](https://loggr.net/) ([GitHub リポジトリ](https://github.com/imobile3/Loggr.Extensions.Logging))
* [NLog](https://nlog-project.org/) ([GitHub リポジトリ](https://github.com/NLog/NLog.Extensions.Logging))
* [Sentry](https://sentry.io/welcome/) ([GitHub リポジトリ](https://github.com/getsentry/sentry-dotnet))
* [Serilog](https://serilog.net/) ([GitHub リポジトリ](https://github.com/serilog/serilog-aspnetcore))
* [Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github リポジトリ](https://github.com/googleapis/google-cloud-dotnet))

一部のサードパーティ製フレームワークは、[セマンティック ログ記録 (構造化ログ記録とも呼ばれます)](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging) を実行できます。

サード パーティ製フレームワークを使用することは、組み込みのプロバイダーのいずれかを使用することと似ています。

1. プロジェクトに NuGet パッケージを追加します。
1. ログ記録フレームワークによって提供される `ILoggerFactory` 拡張メソッドを呼び出します。

詳細については、各プロバイダーのドキュメントをご覧ください。 サード パーティ製のログ プロバイダーは、Microsoft ではサポートされていません。

## <a name="additional-resources"></a>その他の技術情報

* <xref:fundamentals/logging/loggermessage>

::: moniker-end
