Id scaffolder を実行します。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* **ソリューションエクスプローラー**で、プロジェクトを右クリックし、[ **Add**  >  **New スキャフォールディング Item**] > ます。
* [ **Add New スキャフォールディング Item** ] ダイアログボックスの左ペインで、[ **Identity**add] を選択し  >  **Add**ます。
* [ **Id の追加**] ダイアログボックスで、必要なオプションを選択します。
  * 既存のレイアウトページを選択するか、正しくないマークアップでレイアウトファイルが上書きされます:
    * `~/Pages/Shared/_Layout.cshtml`Razor Pages
    * `~/Views/Shared/_Layout.cshtml`MVC プロジェクトの場合
    * Blazor サーバーテンプレート () から作成された Blazor サーバーアプリ `blazorserver` は、既定で Razor Pages または MVC 用に構成されていません。 レイアウトページのエントリは空白のままにします。
  * **+** 新しい**データコンテキストクラス**を作成するには、このボタンをクリックします。 既定値をそのまま使用するか、クラス (など) を指定し `MyApplication.Data.ApplicationDbContext` ます。
* **[追加]** を選択します。

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

ASP.NET Core scaffolder をまだインストールしていない場合は、ここでインストールします。

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

必要な NuGet パッケージ参照をプロジェクトファイル (*.csproj*) に追加します。 プロジェクトディレクトリで次のコマンドを実行します。

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

次のコマンドを実行して、Identity scaffolder オプションの一覧を表示します。

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

[!INCLUDE[](~/includes/scaffoldTFM.md)]

プロジェクトフォルダーで、必要なオプションを指定して Id scaffolder を実行します。 たとえば、既定の UI とファイルの最小数で id を設定するには、次のコマンドを実行します。

```dotnetcli
dotnet aspnet-codegenerator identity --useDefaultUI
```

---
