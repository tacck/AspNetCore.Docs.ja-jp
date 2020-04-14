---
title: ASP.NET Core での Razor ファイルのコンパイル
author: rick-anderson
description: ASP.NET Core アプリで Razor ファイルのコンパイルがどのように行われるかについて説明します。
ms.author: riande
ms.custom: mvc
ms.date: 04/13/2020
uid: mvc/views/view-compilation
ms.openlocfilehash: 67bbeb88cd944791b522900b69bd10cff38c9f3a
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277271"
---
# <a name="razor-file-compilation-in-aspnet-core"></a>ASP.NET Core での Razor ファイルのコンパイル

著者 [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.1"

*.cshtml* 拡張子の Razor ファイルは、[Razor SDK](xref:razor-pages/sdk) を使用してビルド時と公開時にコンパイルされます。 プロジェクトを構成することによって、ランタイム コンパイルを有効にすることもできます。

## <a name="razor-compilation"></a>Razor コンパイル

Razor ファイルのビルド時および公開時のコンパイルは、Razor SDK によって既定で有効になっています。 有効にすると、ランタイム コンパイルはビルド時のコンパイルを補完し、編集された場合に Razor ファイルを更新できるようにします。

## <a name="enable-runtime-compilation-at-project-creation"></a>プロジェクト作成時に実行時コンパイルを有効にする

Razor ページと MVC プロジェクト テンプレートには、プロジェクトの作成時にランタイム コンパイルを有効にするオプションが含まれています。 このオプションは、ASP.NET Core 3.1 以降でサポートされています。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[**新しいASP.NETコア Web アプリケーションの作成**] ダイアログで、次の手順を実行します。

1. **Web アプリケーション**または Web**アプリケーション (モデル ビュー コントローラー)** プロジェクト テンプレートを選択します。
1. [Razor**ランタイム コンパイルを有効にする**] チェック ボックスをオンにします。

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

または`--razor-runtime-compilation``-rrc`テンプレート オプションを使用します。 たとえば、次のコマンドは、ランタイム コンパイルが有効になっている新しい Razor ページ プロジェクトを作成します。

```dotnetcli
dotnet new webapp --razor-runtime-compilation
```

---

## <a name="enable-runtime-compilation-in-an-existing-project"></a>既存のプロジェクトでランタイム コンパイルを有効にする

既存のプロジェクトのすべての環境でランタイム コンパイルを有効にするには、次の手順を実行します。

1. パッケージ[を](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/)インストールします。
1. プロジェクトの `Startup.ConfigureServices` メソッドを更新して、<xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*> の呼び出しを含めます。 次に例を示します。

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

## <a name="conditionally-enable-runtime-compilation-in-an-existing-project"></a>既存のプロジェクトで条件付きで実行時コンパイルを有効にする

実行時コンパイルをローカル開発でのみ有効にできます。 このようにして条件付きで有効にすることにより、次のような出力が発行されるようにできます。

* コンパイル済みのビューを使用する。
* 運用環境のファイル監視を有効にしない。

開発環境でのみランタイム コンパイルを有効にするには、次の手順を実行します。

1. パッケージ[を](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/)インストールします。
1. 起動`environmentVariables`*設定.json*で起動プロファイルセクションを変更します。
    * 確認`ASPNETCORE_ENVIRONMENT`は に`"Development"`設定されています。
    * `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` を `"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"` に設定します。

次の例では、開発環境でランタイム コンパイルが有効化され、起動`IIS Express``RazorPagesApp`プロファイルが有効になります。

[!code-json[](~/mvc/views/view-compilation/samples/3.1/launchSettings.json?highlight=15-16,24-25)]

プロジェクトの`Startup`クラスでコードを変更する必要はありません。 実行時に、ASP.NET Core は、 で[アセンブリ レベルの HostingStartup 属性](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute)を`Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`検索します。 この`HostingStartup`属性は、実行するアプリのスタートアップ コードを指定します。 このスタートアップ コードは、実行時のコンパイルを有効にします。

## <a name="additional-resources"></a>その他のリソース

* [プロパティを構築し、カミソリコンパイルオンを発行します](xref:razor-pages/sdk#properties)。
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* プロジェクト間[でのランタイム コンパイル](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation)の動作を示すサンプルについては、GitHub のランタイム コンパイル サンプルを参照してください。

::: moniker-end

::: moniker range="= aspnetcore-3.0"

*.cshtml* 拡張子の Razor ファイルは、[Razor SDK](xref:razor-pages/sdk) を使用してビルド時と公開時にコンパイルされます。 実行時コンパイルは、アプリケーションを構成することで必要に応じて有効にできることがあります。

## <a name="razor-compilation"></a>Razor コンパイル

Razor ファイルのビルド時および公開時のコンパイルは、Razor SDK によって既定で有効になっています。 有効にすると、ランタイム コンパイルはビルド時のコンパイルを補完し、編集された場合に Razor ファイルを更新できるようにします。

## <a name="runtime-compilation"></a>実行時のコンパイル

すべての環境と構成モードで実行時コンパイルを有効にするには、次のようにします。

1. パッケージ[を](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/)インストールします。

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

1. アクティブな `Configuration` 値に基づいて、[Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) パッケージを条件付きで参照します。

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation" Version="3.1.0" Condition="'$(Configuration)' == 'Debug'" />
    ```

1. プロジェクトの `Startup.ConfigureServices` メソッドを更新して、`AddRazorRuntimeCompilation` の呼び出しを含めます。 `ASPNETCORE_ENVIRONMENT` 変数が `Development` に設定されている場合にのみ Debug モードで実行されるように、条件付きで `AddRazorRuntimeCompilation` を実行します。

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.0/Startup.cs?name=snippet)]

## <a name="additional-resources"></a>その他のリソース

* [プロパティを構築し、カミソリコンパイルオンを発行します](xref:razor-pages/sdk#properties)。
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* プロジェクト間[でのランタイム コンパイル](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation)の動作を示すサンプルについては、GitHub のランタイム コンパイル サンプルを参照してください。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

関連する Razor ページまたは MVC ビューが呼び出されたときに、Razor ファイルが実行時にコンパイルされます。 Razor ファイルは、[Razor SDK](xref:razor-pages/sdk) を使用してビルド時と公開時にコンパイルされます。

## <a name="razor-compilation"></a>Razor コンパイル

Razor ファイルのビルドおよび発行時のコンパイルは、既定で Razor SDK によって有効になっています。 更新後の Razor ファイルの編集は、ビルド時にサポートされています。 既定では、コンパイルされた *Views.dll* のみがアプリと共に展開されます。Razor ファイルのコンパイルに必要な *.cshtml* ファイルまたは参照アセンブリはアプリと共に展開されません。

> [!IMPORTANT]
> プリコンパイル ツールは非推奨とされており、ASP.NET Core 3.0 では削除されます。 [Razor Sdk](xref:razor-pages/sdk) に移行することをお勧めします。
>
> Razor SDK は、プロジェクト ファイルにプリコンパイル固有のプロパティが設定されていない場合のみ有効です。 たとえば、*.csproj* ファイルの `MvcRazorCompileOnPublish` プロパティを `true` に設定して、Razor SDK を無効にします。

## <a name="runtime-compilation"></a>実行時のコンパイル

ビルド時のコンパイルは Razor ファイルの実行時コンパイルによって補完されます。 ASP.NET Core MVC は、*.cshtml* ファイルの内容が変更されたとき、Razor ファイルを再コンパイルします。

## <a name="additional-resources"></a>その他のリソース

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end
