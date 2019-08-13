---
title: Razor コンポーネントクラスライブラリの ASP.NET Core
author: guardrex
description: コンポーネントを外部コンポーネントライブラリから Blazor アプリに組み込む方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 07/02/2019
uid: blazor/class-libraries
ms.openlocfilehash: 402b7b072554f63f85e7cf5e55336104d235a071
ms.sourcegitcommit: 776367717e990bdd600cb3c9148ffb905d56862d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2019
ms.locfileid: "68948442"
---
# <a name="aspnet-core-razor-components-class-libraries"></a>Razor コンポーネントクラスライブラリの ASP.NET Core

[Simon Timms](https://github.com/stimms)

コンポーネントは、プロジェクト間で[Razor クラスライブラリ (RCL)](xref:razor-pages/ui-class)で共有できます。 *Razor コンポーネントクラスライブラリ*は、次のものからインクルードできます。

* ソリューション内の別のプロジェクト。
* NuGet パッケージ。
* 参照された .NET ライブラリです。

コンポーネントが通常の .NET 型であるのと同様に、RCL によって提供されるコンポーネントは通常の .NET アセンブリです。

## <a name="create-an-rcl"></a>RCL を作成する

<xref:blazor/get-started>記事のガイダンスに従って、Blazor 用に環境を構成します。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

1. 新しいプロジェクトを作成します。
1. **[ASP.NET Core Web アプリケーション]** を選択します。           **[次へ]** を選択します。
1. **[プロジェクト名]** フィールドにプロジェクト名を入力するか、既定のプロジェクト名をそのまま使用します。 このトピックの例では、プロジェクト名`MyComponentLib1`を使用します。 **[作成]** を選択します。
1. **[新しい ASP.NET Core Web アプリケーションを作成する]** ダイアログで、 **[.NET Core]** と **[ASP.NET Core 3.0]** が選択されていることを確認します。
1. **Razor クラスライブラリ**テンプレートを選択します。 **[作成]** を選択します。
1. RCL をソリューションに追加します。
   1. ソリューションを右クリックします。 [**既存のプロジェクト**の**追加** > ] を選択します。
   1. RCL のプロジェクトファイルに移動します。
   1. RCL のプロジェクトファイル ( *.csproj*) を選択します。
1. アプリから RCL の参照を追加します。
   1. アプリプロジェクトを右クリックします。 [**参照**の**追加** > ] を選択します。
   1. RCL プロジェクトを選択します。 **[OK]** を選択します。

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

1. コマンドシェルの[dotnet new](/dotnet/core/tools/dotnet-new)コマンドで`razorclasslib` **Razor クラスライブラリ**テンプレート () を使用します。 次の例では、という名前`MyComponentLib1`の rcl が作成されます。 を保持`MyComponentLib1`するフォルダーは、コマンドの実行時に自動的に作成されます。

   ```console
   dotnet new razorclasslib -o MyComponentLib1
   ```

1. 既存のプロジェクトにライブラリを追加するには、コマンドシェルで dotnet の [[参照の追加](/dotnet/core/tools/dotnet-add-reference)] コマンドを使用します。 次の例では、RCL がアプリに追加されています。 ライブラリへのパスを使用して、アプリのプロジェクトフォルダーから次のコマンドを実行します。

   ```console
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="rcls-not-supported-for-client-side-apps"></a>クライアント側アプリでは、RCLs はサポートされていません

現在の ASP.NET Core 3.0 プレビューでは、Razor クラスライブラリは Blazor クライアント側アプリと互換性がありません。 Blazor クライアント側アプリの場合は、コマンドシェルで`blazorlib`テンプレートによって作成された Blazor コンポーネントライブラリを使用します。

```console
dotnet new blazorlib -o MyComponentLib1
```

`blazorlib`テンプレートを使用するコンポーネントライブラリには、イメージ、JavaScript、スタイルシートなどの静的ファイルを含めることができます。 ビルド時には、静的ファイルがビルドされたアセンブリファイル ( *.dll*) に埋め込まれます。これにより、リソースを含める方法を気にすることなく、コンポーネントを使用できます。 `content`ディレクトリに含まれるすべてのファイルは、埋め込みリソースとしてマークされます。

## <a name="consume-a-library-component"></a>ライブラリコンポーネントの使用

別のプロジェクトのライブラリで定義されているコンポーネントを使用するには、次のいずれかの方法を使用します。

* 名前空間で完全な型名を使用します。
* Razor の[ \@using](xref:mvc/views/razor#using)ディレクティブを使用します。 個々のコンポーネントを名前で追加することができます。

次の例では`MyComponentLib1` 、は`SalesReport`コンポーネントを含むコンポーネントライブラリです。

名前`SalesReport`空間を持つ完全な型名を使用して、コンポーネントを参照できます。

```cshtml
<h1>Hello, world!</h1>

Welcome to your new app.

<MyComponentLib1.SalesReport />
```

`@using`ディレクティブを使用してライブラリがスコープ内にある場合は、このコンポーネントを参照することもできます。

```cshtml
@using MyComponentLib1

<h1>Hello, world!</h1>

Welcome to your new app.

<SalesReport />
```

ライブラリの`@using MyComponentLib1`コンポーネントをプロジェクト全体で使用できるようにするには、トップレベルの*インポートの razor*ファイルにディレクティブを含めます。 ディレクティブを任意のレベルの*インポート*ファイルに追加して、名前空間をフォルダー内の1つのページまたは一連のページに適用します。

## <a name="build-pack-and-ship-to-nuget"></a>NuGet のビルド、パック、出荷

コンポーネントライブラリは標準の .NET ライブラリであるため、パッケージを NuGet にパッケージ化して配布することは、ライブラリをパッケージ化して NuGet に配布する場合と変わりはありません。 パッケージ化は、コマンドシェルで[dotnet pack](/dotnet/core/tools/dotnet-pack)コマンドを使用して実行されます。

```console
dotnet pack
```

コマンドシェルで[dotnet nuget publish](/dotnet/core/tools/dotnet-nuget-push)コマンドを使用して、nuget にパッケージをアップロードします。

```console
dotnet nuget publish
```

`blazorlib`テンプレートを使用すると、静的リソースが NuGet パッケージに含まれます。 ライブラリコンシューマーは自動的にスクリプトとスタイルシートを受け取るため、リソースを手動でインストールする必要はありません。

## <a name="create-a-razor-components-class-library-with-static-assets"></a>静的なアセットを含む Razor コンポーネントクラスライブラリを作成する

RCL には、静的なアセットを含めることができます。 この静的アセットは、ライブラリを使用するすべてのアプリで使用できます。 詳細については、「 <xref:razor-pages/ui-class#create-an-rcl-with-static-assets> 」を参照してください。

## <a name="additional-resources"></a>その他の資料

* <xref:razor-pages/ui-class>