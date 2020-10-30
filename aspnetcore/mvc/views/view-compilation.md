---
title: Razor ASP.NET Core でのファイルのコンパイル
author: rick-anderson
description: ASP.NET Core アプリでファイルをコンパイルする方法について説明し Razor ます。
ms.author: riande
ms.custom: mvc
ms.date: 04/14/2020
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
uid: mvc/views/view-compilation
ms.openlocfilehash: 77ca96b329136ee044ab6fc5f6b5ebb5b67fe64c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059079"
---
# <a name="no-locrazor-file-compilation-in-aspnet-core"></a>Razor ASP.NET Core でのファイルのコンパイル

作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.1"

Razor拡張子が *cshtml* のファイルは、 [ Razor SDK](xref:razor-pages/sdk)を使用してビルド時と発行時の両方でコンパイルされます。 プロジェクトを構成することによって、必要に応じてランタイムコンパイルを有効にすることができます。

## <a name="no-locrazor-compilation"></a>Razor asp.net

ファイルのビルド時および発行時のコンパイル Razor は、SDK によって既定で有効になってい Razor ます。 有効にすると、ランタイムコンパイルはビルド時のコンパイルを補完し、 Razor 編集されている場合はファイルを更新できるようにします。

## <a name="enable-runtime-compilation-at-project-creation"></a>プロジェクトの作成時にランタイムコンパイルを有効にする

RazorPages および MVC プロジェクトテンプレートには、プロジェクトの作成時にランタイムコンパイルを有効にするオプションが含まれています。 このオプションは ASP.NET Core 3.1 以降でサポートされています。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[ **新しい ASP.NET Core web アプリケーションの作成** ] ダイアログで次のようにします。

1. [ **Web アプリケーション** ] プロジェクトテンプレートまたは [ **Web アプリケーション (モデルビューコントローラー)** ] プロジェクトテンプレートを選択します。
1. [ **Razor ランタイムコンパイルを有効にする** ] チェックボックスをオンにします。

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

`-rrc`または `--razor-runtime-compilation` テンプレートオプションを使用します。 たとえば、次のコマンドでは、 Razor ランタイムコンパイルが有効になっている新しい Pages プロジェクトが作成されます。

```dotnetcli
dotnet new webapp --razor-runtime-compilation
```

---

## <a name="enable-runtime-compilation-in-an-existing-project"></a>既存のプロジェクトでランタイムコンパイルを有効にする

既存のプロジェクトのすべての環境に対してランタイムコンパイルを有効にするには、次のようにします。

1. AspNetCore をインストールします。 [ RazorRuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet パッケージ。
1. プロジェクトの `Startup.ConfigureServices` メソッドを更新して、<xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*> の呼び出しを含めます。 次に例を示します。

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

## <a name="conditionally-enable-runtime-compilation-in-an-existing-project"></a>既存のプロジェクトでのランタイムコンパイルを条件付きで有効にする

実行時コンパイルをローカル開発でのみ有効にできます。 このようにして条件付きで有効にすることにより、次のような出力が発行されるようにできます。

* コンパイル済みのビューを使用する。
* 運用環境のファイル監視を有効にしない。

開発環境でのみランタイムコンパイルを有効にするには、次のようにします。

1. AspNetCore をインストールします。 [ RazorRuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet パッケージ。
1. launchSettings.jsの [起動プロファイル `environmentVariables` ] セクションを次 *のように* 変更します。
    * `ASPNETCORE_ENVIRONMENT`がに設定されていることを確認し `"Development"` ます。
    * `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` を `"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"` に設定します。

次の例では、および起動プロファイルの開発環境でランタイムコンパイルが有効になってい `IIS Express` `RazorPagesApp` ます。

[!code-json[](~/mvc/views/view-compilation/samples/3.1/launchSettings.json?highlight=15-16,24-25)]

プロジェクトのクラスでコードを変更する必要はありません `Startup` 。 実行時に、ASP.NET Core によって、で [アセンブリレベルの HostingStartup 属性](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) が検索さ `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation` れます。 属性は、 `HostingStartup` 実行するアプリスタートアップコードを指定します。 このスタートアップコードは、ランタイムコンパイルを有効にします。

## <a name="enable-runtime-compilation-for-a-no-locrazor-class-library"></a>クラスライブラリのランタイムコンパイルを有効にする Razor

Razorページプロジェクトが *MyClassLib* という名前の [ Razor クラスライブラリ (rcl)](xref:razor-pages/ui-class)を参照するシナリオについて考えてみます。 RCL には、すべてのチームの MVC およびページプロジェクトで使用される *_Layout の cshtml* ファイルが含まれています Razor 。 その RCL の *_Layout* ファイルのランタイムコンパイルを有効にします。 Pages プロジェクトで次の変更を行い Razor ます。

1. 「 [条件付きで既存のプロジェクトのランタイムコンパイルを有効にする」](#conditionally-enable-runtime-compilation-in-an-existing-project)の手順に従って、ランタイムコンパイルを有効にします。
1. でランタイムコンパイルオプションを構成し `Startup.ConfigureServices` ます。

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.1/Startup.cs?name=snippet_ConfigureServices&highlight=5-10)]

    前のコードでは、 *MyClassLib* rcl への絶対パスが構築されています。 [Physicalfileprovider API](xref:fundamentals/file-providers#physicalfileprovider)は、その絶対パスでディレクトリとファイルを検索するために使用されます。 最後に、 `PhysicalFileProvider` インスタンスがファイルプロバイダーコレクションに追加されます。これにより、RCL の *cshtml* ファイルにアクセスできるようになります。

## <a name="additional-resources"></a>その他の資料

* [ Razor CompileOnBuild プロパティと Razor CompileOnPublish](xref:razor-pages/sdk#properties)プロパティ。
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

Razor拡張子が *cshtml* のファイルは、 [ Razor SDK](xref:razor-pages/sdk)を使用してビルド時と発行時の両方でコンパイルされます。 実行時コンパイルは、アプリケーションを構成することで必要に応じて有効にできることがあります。

## <a name="no-locrazor-compilation"></a>Razor asp.net

ファイルのビルド時および発行時のコンパイル Razor は、SDK によって既定で有効になってい Razor ます。 有効にすると、ランタイムコンパイルはビルド時のコンパイルを補完し、 Razor 編集されている場合はファイルを更新できるようにします。

## <a name="runtime-compilation"></a>実行時のコンパイル

すべての環境と構成モードで実行時コンパイルを有効にするには、次のようにします。

1. AspNetCore をインストールします。 [ RazorRuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet パッケージ。

1. プロジェクトの `Startup.ConfigureServices` メソッドを更新して、<xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*> の呼び出しを含めます。 次に例を示します。

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

### <a name="conditionally-enable-runtime-compilation"></a>実行時コンパイルを条件付きで有効にする

実行時コンパイルをローカル開発でのみ有効にできます。 このようにして条件付きで有効にすることにより、次のような出力が発行されるようにできます。

* コンパイル済みのビューを使用する。
* サイズが小さい。
* 運用環境のファイル監視を有効にしない。

環境や構成モードに基づく実行時コンパイルを有効にするには、次のようにします。

1. 条件付きで AspNetCore を参照します。 [ Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/)アクティブな値に基づいてパッケージを RuntimeCompilation `Configuration` :

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation" Version="3.1.0" Condition="'$(Configuration)' == 'Debug'" />
    ```

1. プロジェクトの `Startup.ConfigureServices` メソッドを更新して、`AddRazorRuntimeCompilation` の呼び出しを含めます。 `ASPNETCORE_ENVIRONMENT` 変数が `Development` に設定されている場合にのみ Debug モードで実行されるように、条件付きで `AddRazorRuntimeCompilation` を実行します。

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.0/Startup.cs?name=snippet)]

## <a name="additional-resources"></a>その他の資料

* [ Razor CompileOnBuild プロパティと Razor CompileOnPublish](xref:razor-pages/sdk#properties)プロパティ。
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* プロジェクト間でランタイムコンパイルを行う方法を示すサンプルについては、 [GitHub のランタイムコンパイルサンプル](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) を参照してください。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ファイルは、 Razor 関連付けられた Razor ページまたは MVC ビューが呼び出されたときに、実行時にコンパイルされます。 Razorファイルは、 [ Razor SDK](xref:razor-pages/sdk)を使用してビルド時と発行時の両方でコンパイルされます。

## <a name="no-locrazor-compilation"></a>Razor asp.net

ファイルのビルドおよび発行時のコンパイル Razor は、SDK によって既定で有効になってい Razor ます。 Razor更新後のファイルの編集は、ビルド時にサポートされます。 既定では、コンパイルされた *Views.dll* と、ファイルのコンパイルに必要なファイルまたは参照アセンブリは、 *.cshtml* Razor アプリと共に配置されます。

> [!IMPORTANT]
> プリコンパイル ツールは非推奨とされており、ASP.NET Core 3.0 では削除されます。 [ Razor Sdk](xref:razor-pages/sdk)に移行することをお勧めします。
>
> SDK は、 Razor プリコンパイル固有のプロパティがプロジェクトファイルに設定されていない場合にのみ有効です。 たとえば、 *.csproj* ファイルの `MvcRazorCompileOnPublish` プロパティをに設定すると、 `true` SDK が無効になり Razor ます。

## <a name="runtime-compilation"></a>実行時のコンパイル

ビルド時のコンパイルは、ファイルのランタイムコンパイルによって補完され Razor ます。 ASP.NET Core Razor のファイルの内容が変更されると、MVC によってファイルが再コンパイルさ *れます* 。

## <a name="additional-resources"></a>その他のリソース

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end
