---
title: ASP.NET MVC から ASP.NET Core MVC への移行について説明します
author: wadepickett
description: ASP.NET MVC プロジェクトの ASP.NET Core MVC への移行を開始する方法について説明します。
ms.author: wpickett
ms.date: 06/18/2020
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
uid: migration/mvc
ms.openlocfilehash: d615f67fc5cb23499ee7e14b747390a7a1b5a693
ms.sourcegitcommit: f09407d128634d200c893bfb1c163e87fa47a161
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88865130"
---
# <a name="migrate-from-aspnet-mvc-to-aspnet-core-mvc"></a>ASP.NET MVC から ASP.NET Core MVC への移行

::: moniker range=">= aspnetcore-3.0"

この記事では、ASP.NET MVC プロジェクトの [ASP.NET CORE mvc](xref:mvc/overview)への移行を開始する方法について説明します。 プロセスでは、ASP.NET MVC からの関連する変更が強調表示されます。

ASP.NET MVC からの移行は、複数の手順から成るプロセスです。 この記事には、次の内容が含まれます。

* 初期セットアップ。
* 基本的なコントローラーとビュー。
* 静的コンテンツ。
* クライアント側の依存関係。

構成とコードの移行につい Identity ては、「 [ASP.NET Core への構成の移行](xref:migration/configuration) 」および「 [認証の移行」および「 Identity ASP.NET Core への移行](xref:migration/identity)」を参照してください

## <a name="prerequisites"></a>前提条件

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs-3.1.md)]

## <a name="create-the-starter-aspnet-mvc-project"></a>Starter ASP.NET MVC プロジェクトを作成する

移行するために Visual Studio で ASP.NET MVC プロジェクトの例を作成します。

1. **[ファイル]** メニューで、 **[新規作成]** > **[プロジェクト]** の順に選択します。
1. [ **ASP.NET Web アプリケーション (.NET Framework)** ] を選択し、[ **次へ**] を選択します。
1. 名前空間が次の手順で作成した ASP.NET Core プロジェクトと一致するように、プロジェクトに *WebApp1* という名前を指定します。 **[作成]** を選択します。
1. [ **MVC**] を選択し、[ **作成**] を選択します。

## <a name="create-the-aspnet-core-project"></a>ASP.NET Core プロジェクトを作成する

新しい ASP.NET Core プロジェクトで移行する新しいソリューションを作成します。

1. Visual Studio の2番目のインスタンスを起動します。
1. **[ファイル]** メニューで、 **[新規作成]** > **[プロジェクト]** の順に選択します。
1. [ **ASP.NET Web Core Web アプリケーション** ] を選択し、[ **次へ**] を選択します。
1. [ **新しいプロジェクトの構成** ] ダイアログで、プロジェクトに *WebApp1*という名前を設定します。
1. 同じプロジェクト名を使用するように、場所を前のプロジェクトとは別のディレクトリに設定します。 同じ名前空間を使用すると、2つのプロジェクト間でコードを簡単にコピーできるようになります。 **[作成]** を選択します。
1. **[新しい ASP.NET Core Web アプリケーションを作成する]** ダイアログで、 **[.NET Core]** と **[ASP.NET Core 3.1]** が選択されていることを確認します。 [ **Web アプリケーション (モデルビューコントローラー)** ] プロジェクトテンプレートを選択し、[ **作成**] を選択します。

## <a name="configure-the-aspnet-core-site-to-use-mvc"></a>MVC を使用するように ASP.NET Core サイトを構成する

ASP.NET Core 3.0 以降のプロジェクトでは、.NET Framework はサポートされているターゲットフレームワークではなくなりました。 プロジェクトは .NET Core をターゲットにする必要があります。 MVC を含む ASP.NET Core 共有フレームワークは、.NET Core ランタイムインストールの一部です。 プロジェクト ファイル内で `Microsoft.NET.Sdk.Web` SDK を使用すると、共有フレームワークが自動的に参照されます。

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

詳細については、「 [Framework リファレンス](xref:migration/22-to-30#framework-reference)」を参照してください。

ASP.NET Core では、 `Startup` クラスは次のようになります。

* *Global.asax*を置き換えます。
* すべてのアプリスタートアップタスクを処理します。

詳細については、「<xref:fundamentals/startup>」を参照してください。

ASP.NET Core プロジェクトで、 *Startup.cs* ファイルを開きます。

[!code-csharp[](mvc/samples/3.x/Startup.cs?highlight=13,30,32&name=snippet)]

ASP.NET Core アプリでは、ミドルウェアを使用してフレームワークの機能を選択する必要があります。 テンプレートで生成された以前のコードは、次のサービスとミドルウェアを追加します。

* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A>拡張メソッドは、コントローラー、API 関連の機能、およびビューの MVC サービスサポートを登録します。 MVC サービスの登録オプションの詳細については、「 [mvc サービスの登録](xref:migration/22-to-30#mvc-service-registration)」を参照してください。
* <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A>拡張メソッドは、静的ファイルハンドラーを追加し `Microsoft.AspNetCore.StaticFiles` ます。 拡張メソッドは、の `UseStaticFiles` 前に呼び出す必要があり `UseRouting` ます。 詳細については、「<xref:fundamentals/static-files>」を参照してください。
* <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A>拡張メソッドは、ルーティングを追加します。 詳細については、「<xref:fundamentals/routing>」を参照してください。

この既存の構成には、サンプルの ASP.NET MVC プロジェクトを移行するために必要なものが含まれています。 ASP.NET Core ミドルウェアオプションの詳細については、「」を参照してください <xref:fundamentals/startup> 。

## <a name="migrate-controllers-and-views"></a>コントローラーとビューの移行

ASP.NET Core プロジェクトでは、新しい空のコントローラークラスとビュークラスが追加され、移行元の ASP.NET MVC プロジェクトのコントローラーおよびビュークラスと同じ名前を使用してプレースホルダーとして機能するようになります。

ASP.NET Core *WebApp1* プロジェクトには、ASP.NET MVC プロジェクトと同じ名前の最小サンプルコントローラーとビューが既に含まれています。 これらは、ASP.NET MVC *WebApp1* プロジェクトから移行する ASP.NET mvc コントローラーとビューのプレースホルダーとして機能します。

1. ASP.NET MVC からメソッドをコピーして、 `HomeController` 新しい ASP.NET Core メソッドを置き換え `HomeController` ます。 アクションメソッドの戻り値の型を変更する必要はありません。 ASP.NET MVC 組み込みテンプレートのコントローラーアクションメソッドの戻り値の型はです <https://docs.microsoft.com/dotnet/api/system.web.mvc.actionresult?view=aspnet-mvc-5.2> 。 ASP.NET CORE mvc では、アクションメソッドは代わりにを返し `IActionResult` ます。 `ActionResult` は、`IActionResult` を実装します。
1. ASP.NET Core プロジェクトで、 *Views/Home*ディレクトリを右クリックし、[既存項目の**追加**] を選択し > **Existing Item**ます。
1. [ **既存項目の追加** ] ダイアログで、ASP.NET MVC *WebApp1* プロジェクトの *Views/Home* ディレクトリに移動します。
1. [*バージョン情報*]、[*連絡先.* *..* ............................... Razor **Add**

詳細については、次のトピックを参照してください。 <xref:mvc/controllers/actions> および <xref:mvc/views/overview>

## <a name="test-each-method"></a>各メソッドをテストする

各コントローラーエンドポイントはテストできます。ただし、レイアウトとスタイルについては、ドキュメントの後半で説明します。

1. ASP.NET Core アプリを実行します。
1. 現在のポート番号を ASP.NET Core プロジェクトで使用されているポート番号に置き換えることによって、実行中の ASP.NET Core アプリのブラウザーから表示されたビューを呼び出します。 たとえば、`https://localhost:44375/home/about` のようにします。

## <a name="migrate-static-content"></a>静的コンテンツの移行

ASP.NET MVC 5 以前では、静的コンテンツは web プロジェクトのルートディレクトリからホストされており、サーバー側ファイルと混在していました。 ASP.NET Core では、静的ファイルはプロジェクトの [web ルート](xref:fundamentals/index#web-root) ディレクトリ内に格納されます。 既定のディレクトリは *{content root}/wwwroot*ですが、変更することができます。 詳細については、「[ASP.NET Core の静的ファイル](xref:fundamentals/static-files#serve-static-files)」をご覧ください。

ASP.NET MVC *WebApp1*プロジェクトの静的コンテンツを ASP.NET Core *WebApp1*プロジェクトの*wwwroot*ディレクトリにコピーします。

1. ASP.NET Core プロジェクトで、 *wwwroot*ディレクトリを右クリックし、[既存項目の**追加**] を選択し > **Existing Item**ます。
1. [ **既存項目の追加** ] ダイアログで、ASP.NET MVC *WebApp1* プロジェクトに移動します。
1. *Favicon*ファイルを選択し、[**追加**] を選択して、既存のファイルを置き換えます。

## <a name="migrate-the-layout-files"></a>レイアウトファイルを移行する

ASP.NET MVC プロジェクトのレイアウトファイルを ASP.NET Core プロジェクトにコピーします。

1. ASP.NET Core プロジェクトで、 *Views*ディレクトリを右クリックし、[既存項目の**追加**] を選択し > **Existing Item**ます。
1. [ **既存項目の追加** ] ダイアログで、ASP.NET MVC *WebApp1* プロジェクトの *Views* ディレクトリに移動します。
1. *_ViewStart*ファイルを選択し、[**追加**] を選択します。

ASP.NET MVC プロジェクトの共有レイアウトファイルを ASP.NET Core プロジェクトにコピーします。

1. ASP.NET Core プロジェクトで、 *Views/Shared*ディレクトリを右クリックし、[既存項目の**追加**] を選択し > **Existing Item**ます。
1. [ **既存項目の追加** ] ダイアログで、ASP.NET MVC *WebApp1* プロジェクトの *Views/Shared* ディレクトリに移動します。
1. _Layout の *cshtml* ファイルを選択し、[ **追加**] を選択して既存のファイルを置き換えます。

ASP.NET Core プロジェクトで、 *_Layout* ファイルを開きます。 次に示す完成したコードと一致するように、次の変更を行います。

次の完成したコードに一致するように、ブートストラップ CSS インクルードを更新します。

1. `@Styles.Render("~/Content/css")` `<link>` *ブートストラップ*を読み込む要素で置き換えます (下記参照)。
1. `@Scripts.Render("~/bundles/modernizr")` を削除します。

ブートストラップ CSS インクルードの完全な置換マークアップ:

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

以下の完成したコードに一致するように、jQuery とブートストラップの JavaScript インクルードを更新します。

1. `@Scripts.Render("~/bundles/jquery")`要素で置き換え `<script>` ます (下記参照)。
1. `@Scripts.Render("~/bundles/bootstrap")`要素で置き換え `<script>` ます (下記参照)。

JQuery とブートストラップの JavaScript インクルードのために完了した置換マークアップ:

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

更新された *_Layout* ファイルは次のようになります。

[!code-cshtml[](mvc/samples/3.x/Views/Shared/_Layout.cshtml?highlight=7-10,40-42)]

ブラウザーでサイトを表示します。 必要なスタイルが設定された状態で表示されます。

## <a name="configure-bundling-and-minification"></a>バンドルと縮小を構成する

ASP.NET Core は、 [WebOptimizer](https://github.com/ligershark/WebOptimizer) やその他の類似のライブラリなど、いくつかのオープンソースバンドルおよび縮小ソリューションと互換性があります。 ASP.NET Core は、ネイティブのバンドルと縮小ソリューションを提供しません。 バンドルと縮小の構成の詳細については、「 [バンドルと縮小](xref:client-side/bundling-and-minification)」を参照してください。

## <a name="solve-http-500-errors"></a>HTTP 500 エラーを解決する

問題の原因としては、問題の原因に関する情報が含まれていない HTTP 500 のエラーメッセージが表示されることがあります。 たとえば、 *Views/_ViewImports cshtml* ファイルにプロジェクト内に存在しない名前空間が含まれている場合、HTTP 500 エラーが生成されます。 ASP.NET Core アプリでは、既定で `UseDeveloperExceptionPage` 、拡張機能がに追加され、 `IApplicationBuilder` 環境の *開発*時に実行されます。 詳細については、次のコードを参考にしてください。

[!code-csharp[](mvc/samples/3.x/Startup.cs?highlight=17-21&name=snippet)]

ASP.NET Core は、未処理の例外を HTTP 500 エラー応答に変換します。 通常、サーバーに関する機密情報が漏えいするのを防ぐために、エラーの詳細はこれらの応答に含まれていません。 詳細については、「 [開発者向け例外ページ](xref:fundamentals/error-handling#developer-exception-page)」を参照してください。

## <a name="next-steps"></a>次の手順

* <xref:migration/identity>

## <a name="additional-resources"></a>その他の技術情報

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

この記事では、ASP.NET MVC プロジェクトの [ASP.NET CORE mvc](xref:mvc/overview) 2.2 への移行を開始する方法について説明します。 このプロセスでは、ASP.NET MVC から変更されたものの多くを強調表示します。 ASP.NET MVC からの移行は、複数の手順から成るプロセスです。 この記事には、次の内容が含まれます。

* 初期セットアップ
* 基本的なコントローラーとビュー
* 静的コンテンツ
* クライアント側の依存関係。

構成とコードの移行につい Identity ては、「」および「」を参照してください <xref:migration/configuration> <xref:migration/identity> 。

> [!NOTE]
> サンプルのバージョン番号が最新ではない可能性があります。それに応じてプロジェクトを更新してください。

## <a name="create-the-starter-aspnet-mvc-project"></a>Starter ASP.NET MVC プロジェクトを作成する

アップグレードを示すために、まず ASP.NET MVC アプリを作成します。 名前空間が次の手順で作成した ASP.NET Core プロジェクトと一致するように、 *WebApp1* という名前を付けて作成します。

![Visual Studio の [新しいプロジェクト] ダイアログ](mvc/_static/new-project.png)

![新しい Web アプリケーションダイアログ: ASP.NET templates パネルで選択された MVC プロジェクトテンプレート](mvc/_static/new-project-select-mvc-template.png)

*省略可能:* ソリューションの名前を *WebApp1* から *Mvc5*に変更します。 Visual Studio に新しいソリューション名 (*Mvc5*) が表示されます。これにより、次のプロジェクトからこのプロジェクトを簡単に指定できます。

## <a name="create-the-aspnet-core-project"></a>ASP.NET Core プロジェクトを作成する

2つのプロジェクトの名前空間が一致するように、前のプロジェクト (*WebApp1*) と同じ名前の新しい*空*の ASP.NET Core web アプリを作成します。 同じ名前空間を使用すると、2つのプロジェクト間でコードを簡単にコピーできます。 同じ名前を使用する前のプロジェクトとは別のディレクトリにこのプロジェクトを作成します。

![[新しいプロジェクト] ダイアログ](mvc/_static/new_core.png)

![New ASP.NET Web Application dialog: ASP.NET Core テンプレートパネルで空のプロジェクトテンプレートが選択されました](mvc/_static/new-project-select-empty-aspnet5-template.png)

* *省略可能:**Web アプリケーション*プロジェクトテンプレートを使用して、新しい ASP.NET Core アプリを作成します。 プロジェクトに *WebApp1*という名前を設定し、 **個々のユーザーアカウント**の認証オプションを選択します。 このアプリの名前を *FullAspNetCore*に変更します。 このプロジェクトを作成すると、変換の時間が短縮されます。 最終的な結果は、テンプレートによって生成されるコードで表示できます。また、コードを変換プロジェクトにコピーしたり、テンプレートで生成されたプロジェクトと比較したりすることができます。

## <a name="configure-the-site-to-use-mvc"></a>MVC を使用するようにサイトを構成する

* .NET Core を対象とする場合、 [AspNetCore メタパッケージ](xref:fundamentals/metapackage-app) は既定で参照されます。 このパッケージには、MVC アプリで一般的に使用されるパッケージが含まれています。 .NET Framework を対象とする場合は、パッケージ参照をプロジェクトファイルに個別に一覧表示する必要があります。

`Microsoft.AspNetCore.Mvc` は ASP.NET Core MVC フレームワークです。 `Microsoft.AspNetCore.StaticFiles` 静的ファイルハンドラーです。 ASP.NET Core アプリは、静的ファイルの提供など、ミドルウェアに対して明示的にオプトインされています。 詳しくは、[静的ファイル](xref:fundamentals/static-files)に関するページをご覧ください。

* *Startup.cs*ファイルを開き、次のコードに一致するようにコードを変更します。

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=7,20-25&name=snippet)]

<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>拡張メソッドは、静的ファイルハンドラーを追加します。 詳細については、「 [アプリケーションの起動](xref:fundamentals/startup) と [ルーティング](xref:fundamentals/routing)」を参照してください。

## <a name="add-a-controller-and-view"></a>コントローラーとビューを追加する

このセクションでは、最小コントローラーとビューを追加して、次のセクションで移行する ASP.NET MVC コントローラーとビューのプレースホルダーとして機能します。

* *Controllers*ディレクトリを追加します。

* *HomeController.cs*という名前の**コントローラークラス**を*Controllers*ディレクトリに追加します。

![[新しい項目の追加] ダイアログ](mvc/_static/add_mvc_ctl.png)

* *Views*ディレクトリを追加します。

* *ビュー/ホーム*ディレクトリを追加します。

* *Views/Home*ディレクトリに、 *Index. cshtml*という名前の** Razor ビュー**を追加します。

![[新しい項目の追加] ダイアログ](mvc/_static/view.png)

プロジェクトの構造は次のようになります。

![WebApp1 のファイルとディレクトリを示すソリューションエクスプローラー](mvc/_static/project-structure-controller-view.png)

*Views/Home/Index.cshtml* ファイルの内容を次のマークアップに置き換えます。

```html
<h1>Hello world!</h1>
```

アプリを実行します。

![Microsoft Edge で開いている Web アプリ](mvc/_static/hello-world.png)

詳細については、「 [コントローラー](xref:mvc/controllers/actions) と [ビュー](xref:mvc/views/overview)」を参照してください。

次の機能を使用するには、例の ASP.NET MVC プロジェクトから ASP.NET Core プロジェクトへの移行が必要です。

* クライアント側コンテンツ (CSS、フォント、およびスクリプト)

* controllers

* views

* モデル

* まとめる

* filters

* ログイン/ログアウト Identity (これは次のチュートリアルで行います)

## <a name="controllers-and-views"></a>コントローラーとビュー

* ASP.NET MVC の各メソッド `HomeController` を新しいにコピーし `HomeController` ます。 ASP.NET MVC では、組み込みテンプレートのコントローラーアクションメソッドの戻り値の型はです <https://docs.microsoft.com/dotnet/api/system.web.mvc.actionresult?view=aspnet-mvc-5.2> 。 ASP.NET CORE mvc では、アクションメソッドは代わりにを返し `IActionResult` ます。 `ActionResult``IActionResult`を実装するので、アクションメソッドの戻り値の型を変更する必要はありません。

* ASP.NET MVC プロジェクトの *About. cshtml*、 *Contact.* cshtml、および *Index. cshtml* Razor view ファイルを ASP.NET Core プロジェクトにコピーします。

## <a name="test-each-method"></a>各メソッドをテストする

レイアウトファイルとスタイルはまだ移行されていないため、表示されたビューには、ビューファイルのコンテンツのみが含まれます。 ビューとビューに対して生成されたレイアウトファイルは、 `About` `Contact` まだ使用できません。

現在のポート番号を ASP.NET core プロジェクトで使用されているポート番号に置き換えることによって、実行中の ASP.NET core アプリのブラウザーから表示されたビューを呼び出します。 (例: `https://localhost:44375/home/about`)。

![連絡先ページ](mvc/_static/contact-page.png)

スタイル設定とメニュー項目がないことに注意してください。 スタイルは次のセクションで修正されます。

## <a name="static-content"></a>静的コンテンツ

ASP.NET MVC 5 以前では、静的コンテンツは web プロジェクトのルートからホストされており、サーバー側ファイルと混在していました。 ASP.NET Core では、静的なコンテンツは *wwwroot* ディレクトリでホストされます。 ASP.NET MVC アプリの静的コンテンツを ASP.NET Core プロジェクトの *wwwroot* ディレクトリにコピーします。 このサンプルの変換の例を次に示します。

* ASP.NET MVC プロジェクトの *favicon* ファイルを ASP.NET Core プロジェクトの *wwwroot* ディレクトリにコピーします。

ASP.NET MVC プロジェクトでは、そのスタイルに [ブートストラップ](https://getbootstrap.com/) を使用して、ブートストラップファイルを *Content* および *Scripts* ディレクトリに格納します。 ASP.NET MVC プロジェクトを生成したテンプレートは、レイアウトファイル内のブートストラップ (*Views/Shared/_Layout*) を参照します。 *bootstrap.js*と*ブートストラップの .css*ファイルは、ASP.NET MVC プロジェクトから新しいプロジェクトの*wwwroot*ディレクトリにコピーできます。 代わりに、このドキュメントでは、次のセクションで、CDNs を使用したブートストラップ (およびその他のクライアント側ライブラリ) のサポートを追加します。

## <a name="migrate-the-layout-file"></a>レイアウトファイルを移行する

* ASP.NET MVC プロジェクトの*views*ディレクトリの *_ViewStart*ファイルを ASP.NET Core プロジェクトの*views*ディレクトリにコピーします。 *_ViewStart* ASP.NET Core MVC では、このファイルは変更されていません。

* *ビュー/共有*ディレクトリを作成します。

* *省略可能:**FullAspNetCore* MVC プロジェクトの*views*ディレクトリから、ASP.NET Core プロジェクトの*views*ディレクトリに *_ViewImports*をコピーします。 *_ViewImports*ファイル内の名前空間宣言を削除します。 *_ViewImports*のファイルは、すべてのビューファイルの名前空間を提供し、[タグヘルパー](xref:mvc/views/tag-helpers/intro)を取り込みます。 タグヘルパーは、新しいレイアウトファイルで使用されます。 *_ViewImports* 、ASP.NET Core の新しいファイルです。

* ASP.NET MVC プロジェクトの*views/shared*ディレクトリにある *_Layout*ファイルを、ASP.NET Core プロジェクトの*views/shared*ディレクトリにコピーします。

*_Layout*ファイルを開き、次のように変更します (完成したコードは次のようになります)。

* `@Styles.Render("~/Content/css")` `<link>` *ブートストラップ*を読み込む要素で置き換えます (下記参照)。

* `@Scripts.Render("~/bundles/modernizr")` を削除します。

* 行をコメントアウト `@Html.Partial("_LoginPartial")` します (行をで囲み `@*...*@` ます)。 詳細については、「[認証と Identity ASP.NET Core への移行](xref:migration/identity)」を参照してください。

* `@Scripts.Render("~/bundles/jquery")`要素で置き換え `<script>` ます (下記参照)。

* `@Scripts.Render("~/bundles/bootstrap")`要素で置き換え `<script>` ます (下記参照)。

ブートストラップ CSS インクルードの置換マークアップ:

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

JQuery および Bootstrap JavaScript インクルードの置換マークアップ:

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

更新された *_Layout* ファイルは次のようになります。

[!code-cshtml[](mvc/samples/2.x/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

ブラウザーでサイトを表示します。 これで、必要なスタイルが適切に読み込まれます。

* *省略可能:* 新しいレイアウトファイルを使用してみてください。 *FullAspNetCore*プロジェクトからレイアウトファイルをコピーします。 新しいレイアウトファイルは [タグヘルパー](xref:mvc/views/tag-helpers/intro) を使用し、その他の機能強化が行われています。

## <a name="configure-bundling-and-minification"></a>バンドルと縮小を構成する

バンドルと縮小を構成する方法の詳細については、「 [バンドルと縮小](xref:client-side/bundling-and-minification)」を参照してください。

## <a name="solve-http-500-errors"></a>HTTP 500 エラーを解決する

問題の原因として、問題の原因に関する情報が含まれていない HTTP 500 エラーメッセージが発生することがあります。 たとえば、 *Views/_ViewImports cshtml* ファイルに、プロジェクトに存在しない名前空間が含まれている場合、HTTP 500 エラーが生成されます。 ASP.NET Core アプリでは、既定で `UseDeveloperExceptionPage` 、拡張機能がに追加され、 `IApplicationBuilder` 構成が *開発*されるときに実行されます。 次のコードの例を参照してください。

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=11-15&name=snippet)]

ASP.NET Core は、未処理の例外を HTTP 500 エラー応答に変換します。 通常、サーバーに関する機密情報が漏えいするのを防ぐために、エラーの詳細はこれらの応答に含まれていません。 詳細については、「 [開発者向け例外ページ](xref:fundamentals/error-handling#developer-exception-page)」を参照してください。

## <a name="additional-resources"></a>その他の技術情報

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>

::: moniker-end

::: moniker range="<= aspnetcore-2.1"

この記事では、ASP.NET MVC プロジェクトの [ASP.NET CORE mvc](xref:mvc/overview) 2.1 への移行を開始する方法について説明します。 このプロセスでは、ASP.NET MVC から変更されたものの多くを強調表示します。 ASP.NET MVC からの移行は、複数の手順から成るプロセスです。 この記事には、次の内容が含まれます。

* 初期セットアップ
* 基本的なコントローラーとビュー
* 静的コンテンツ
* クライアント側の依存関係。

構成とコードの移行につい Identity ては、「 [ASP.NET Core への構成の移行](xref:migration/configuration) 」および「 [認証の移行」および「 Identity ASP.NET Core への移行](xref:migration/identity)」を参照してください

> [!NOTE]
> サンプルのバージョン番号が最新ではない可能性があります。それに応じてプロジェクトを更新してください。

## <a name="create-the-starter-aspnet-mvc-project"></a>Starter ASP.NET MVC プロジェクトを作成する

アップグレードを示すために、まず ASP.NET MVC アプリを作成します。 名前空間が次の手順で作成した ASP.NET Core プロジェクトと一致するように、 *WebApp1* という名前を付けて作成します。

![Visual Studio の [新しいプロジェクト] ダイアログ](mvc/_static/new-project.png)

![新しい Web アプリケーションダイアログ: ASP.NET templates パネルで選択された MVC プロジェクトテンプレート](mvc/_static/new-project-select-mvc-template.png)

*省略可能:* ソリューションの名前を *WebApp1* から *Mvc5*に変更します。 Visual Studio に新しいソリューション名 (*Mvc5*) が表示されます。これにより、次のプロジェクトからこのプロジェクトを簡単に指定できます。

## <a name="create-the-aspnet-core-project"></a>ASP.NET Core プロジェクトを作成する

2つのプロジェクトの名前空間が一致するように、前のプロジェクト (*WebApp1*) と同じ名前の新しい*空*の ASP.NET Core web アプリを作成します。 同じ名前空間を使用すると、2つのプロジェクト間でコードを簡単にコピーできます。 同じ名前を使用する前のプロジェクトとは別のディレクトリにこのプロジェクトを作成します。

![[新しいプロジェクト] ダイアログ](mvc/_static/new_core.png)

![New ASP.NET Web Application dialog: ASP.NET Core テンプレートパネルで空のプロジェクトテンプレートが選択されました](mvc/_static/new-project-select-empty-aspnet5-template.png)

* *省略可能:**Web アプリケーション*プロジェクトテンプレートを使用して、新しい ASP.NET Core アプリを作成します。 プロジェクトに *WebApp1*という名前を設定し、 **個々のユーザーアカウント**の認証オプションを選択します。 このアプリの名前を *FullAspNetCore*に変更します。 このプロジェクトを作成すると、変換の時間が短縮されます。 最終的な結果は、テンプレートによって生成されるコードで表示できます。また、コードを変換プロジェクトにコピーしたり、テンプレートで生成されたプロジェクトと比較したりすることができます。

## <a name="configure-the-site-to-use-mvc"></a>MVC を使用するようにサイトを構成する

* .NET Core を対象とする場合、 [AspNetCore メタパッケージ](xref:fundamentals/metapackage-app) は既定で参照されます。 このパッケージには、MVC アプリで一般的に使用されるパッケージが含まれています。 .NET Framework を対象とする場合は、パッケージ参照をプロジェクトファイルに個別に一覧表示する必要があります。

`Microsoft.AspNetCore.Mvc` は ASP.NET Core MVC フレームワークです。 `Microsoft.AspNetCore.StaticFiles` 静的ファイルハンドラーです。 ASP.NET Core アプリは、静的ファイルの提供など、ミドルウェアに対して明示的にオプトインされています。 詳しくは、[静的ファイル](xref:fundamentals/static-files)に関するページをご覧ください。

* *Startup.cs*ファイルを開き、次のコードに一致するようにコードを変更します。

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=7,20-25&name=snippet)]

<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>拡張メソッドは、静的ファイルハンドラーを追加します。 `UseMvc`拡張メソッドは、ルーティングを追加します。 詳細については、「 [アプリケーションの起動](xref:fundamentals/startup) と [ルーティング](xref:fundamentals/routing)」を参照してください。

## <a name="add-a-controller-and-view"></a>コントローラーとビューを追加する

このセクションでは、最小コントローラーとビューを追加して、次のセクションで移行する ASP.NET MVC コントローラーとビューのプレースホルダーとして機能します。

* *Controllers*ディレクトリを追加します。

* *HomeController.cs*という名前の**コントローラークラス**を*Controllers*ディレクトリに追加します。

![[新しい項目の追加] ダイアログ](mvc/_static/add_mvc_ctl.png)

* *Views*ディレクトリを追加します。

* *ビュー/ホーム*ディレクトリを追加します。

* *Views/Home*ディレクトリに、 *Index. cshtml*という名前の** Razor ビュー**を追加します。

![[新しい項目の追加] ダイアログ](mvc/_static/view.png)

プロジェクトの構造は次のようになります。

![WebApp1 のファイルとディレクトリを示すソリューションエクスプローラー](mvc/_static/project-structure-controller-view.png)

*Views/Home/Index.cshtml* ファイルの内容を次のマークアップに置き換えます。

```html
<h1>Hello world!</h1>
```

アプリを実行します。

![Microsoft Edge で開いている Web アプリ](mvc/_static/hello-world.png)

詳細については、「 [コントローラー](xref:mvc/controllers/actions) と [ビュー](xref:mvc/views/overview)」を参照してください。

次の機能を使用するには、例の ASP.NET MVC プロジェクトから ASP.NET Core プロジェクトへの移行が必要です。

* クライアント側コンテンツ (CSS、フォント、およびスクリプト)

* controllers

* views

* モデル

* まとめる

* filters

* ログイン/ログアウト Identity (これは次のチュートリアルで行います)

## <a name="controllers-and-views"></a>コントローラーとビュー

* ASP.NET MVC の各メソッド `HomeController` を新しいにコピーし `HomeController` ます。 ASP.NET MVC では、組み込みテンプレートのコントローラーアクションメソッドの戻り値の型はです <https://docs.microsoft.com/dotnet/api/system.web.mvc.actionresult?view=aspnet-mvc-5.2> 。 ASP.NET CORE mvc では、アクションメソッドは代わりにを返し `IActionResult` ます。 `ActionResult``IActionResult`を実装するので、アクションメソッドの戻り値の型を変更する必要はありません。

* ASP.NET MVC プロジェクトの *About. cshtml*、 *Contact.* cshtml、および *Index. cshtml* Razor view ファイルを ASP.NET Core プロジェクトにコピーします。

## <a name="test-each-method"></a>各メソッドをテストする

レイアウトファイルとスタイルはまだ移行されていないため、表示されたビューには、ビューファイルのコンテンツのみが含まれます。 ビューとビューに対して生成されたレイアウトファイルは、 `About` `Contact` まだ使用できません。

* 現在のポート番号を ASP.NET core プロジェクトで使用されているポート番号に置き換えることによって、実行中の ASP.NET core アプリのブラウザーから表示されたビューを呼び出します。 (例: `https://localhost:44375/home/about`)。

![連絡先ページ](mvc/_static/contact-page.png)

スタイル設定とメニュー項目がないことに注意してください。 スタイルは次のセクションで修正されます。

## <a name="static-content"></a>静的コンテンツ

ASP.NET MVC 5 以前では、静的コンテンツは web プロジェクトのルートからホストされており、サーバー側ファイルと混在していました。 ASP.NET Core では、静的なコンテンツは *wwwroot* ディレクトリでホストされます。 ASP.NET MVC アプリの静的コンテンツを ASP.NET Core プロジェクトの *wwwroot* ディレクトリにコピーします。 このサンプルの変換の例を次に示します。

* ASP.NET MVC プロジェクトの *favicon* ファイルを ASP.NET Core プロジェクトの *wwwroot* ディレクトリにコピーします。

ASP.NET MVC プロジェクトでは、そのスタイルに [ブートストラップ](https://getbootstrap.com/) を使用して、ブートストラップファイルを *Content* および *Scripts* ディレクトリに格納します。 ASP.NET MVC プロジェクトを生成したテンプレートは、レイアウトファイル内のブートストラップ (*Views/Shared/_Layout*) を参照します。 *bootstrap.js*と*ブートストラップの .css*ファイルは、ASP.NET MVC プロジェクトから新しいプロジェクトの*wwwroot*ディレクトリにコピーできます。 代わりに、このドキュメントでは、次のセクションで、CDNs を使用したブートストラップ (およびその他のクライアント側ライブラリ) のサポートを追加します。

## <a name="migrate-the-layout-file"></a>レイアウトファイルを移行する

* ASP.NET MVC プロジェクトの*views*ディレクトリの *_ViewStart*ファイルを ASP.NET Core プロジェクトの*views*ディレクトリにコピーします。 *_ViewStart* ASP.NET Core MVC では、このファイルは変更されていません。

* *ビュー/共有*ディレクトリを作成します。

* *省略可能:**FullAspNetCore* MVC プロジェクトの*views*ディレクトリから、ASP.NET Core プロジェクトの*views*ディレクトリに *_ViewImports*をコピーします。 *_ViewImports*ファイル内の名前空間宣言を削除します。 *_ViewImports*のファイルは、すべてのビューファイルの名前空間を提供し、[タグヘルパー](xref:mvc/views/tag-helpers/intro)を取り込みます。 タグヘルパーは、新しいレイアウトファイルで使用されます。 *_ViewImports* 、ASP.NET Core の新しいファイルです。

* ASP.NET MVC プロジェクトの*views/shared*ディレクトリにある *_Layout*ファイルを、ASP.NET Core プロジェクトの*views/shared*ディレクトリにコピーします。

*_Layout*ファイルを開き、次のように変更します (完成したコードは次のようになります)。

* `@Styles.Render("~/Content/css")` `<link>` *ブートストラップ*を読み込む要素で置き換えます (下記参照)。

* `@Scripts.Render("~/bundles/modernizr")` を削除します。

* 行をコメントアウト `@Html.Partial("_LoginPartial")` します (行をで囲み `@*...*@` ます)。 詳細については、「[認証と Identity ASP.NET Core への移行](xref:migration/identity)」を参照してください。

* `@Scripts.Render("~/bundles/jquery")`要素で置き換え `<script>` ます (下記参照)。

* `@Scripts.Render("~/bundles/bootstrap")`要素で置き換え `<script>` ます (下記参照)。

ブートストラップ CSS インクルードの置換マークアップ:

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

JQuery および Bootstrap JavaScript インクルードの置換マークアップ:

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

更新された *_Layout* ファイルは次のようになります。

[!code-cshtml[](mvc/samples/2.x/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

ブラウザーでサイトを表示します。 これで、必要なスタイルが適切に読み込まれます。

* *省略可能:* 新しいレイアウトファイルを使用してみてください。 *FullAspNetCore*プロジェクトからレイアウトファイルをコピーします。 新しいレイアウトファイルは [タグヘルパー](xref:mvc/views/tag-helpers/intro) を使用し、その他の機能強化が行われています。

## <a name="configure-bundling-and-minification"></a>バンドルと縮小を構成する

バンドルと縮小を構成する方法の詳細については、「 [バンドルと縮小](xref:client-side/bundling-and-minification)」を参照してください。

## <a name="solve-http-500-errors"></a>HTTP 500 エラーを解決する

問題の原因として、問題の原因に関する情報が含まれていない HTTP 500 エラーメッセージが発生することがあります。 たとえば、 *Views/_ViewImports cshtml* ファイルに、プロジェクトに存在しない名前空間が含まれている場合、HTTP 500 エラーが生成されます。 ASP.NET Core アプリでは、既定で `UseDeveloperExceptionPage` 、拡張機能がに追加され、 `IApplicationBuilder` 構成が *開発*されるときに実行されます。 次のコードの例を参照してください。

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=11-15&name=snippet)]

ASP.NET Core は、未処理の例外を HTTP 500 エラー応答に変換します。 通常、サーバーに関する機密情報が漏えいするのを防ぐために、エラーの詳細はこれらの応答に含まれていません。 詳細については、「 [開発者向け例外ページ](xref:fundamentals/error-handling#developer-exception-page)」を参照してください。

## <a name="additional-resources"></a>その他の技術情報

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>

::: moniker-end
