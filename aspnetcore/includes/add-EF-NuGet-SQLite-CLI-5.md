次の .NET CLI コマンドを実行します。

```dotnetcli
dotnet tool install --global dotnet-ef
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.Extensions.Logging.Debug
```

上記のコマンドにより次が追加されます。

* [aspnet-codegenerator スキャフォールディング ツール](xref:fundamentals/tools/dotnet-aspnet-codegenerator)。
* .NET CLI 用の EF Core ツール。
* EF Core SQLite プロバイダー。これにより、EF Core パッケージが依存関係としてインストールされます。
* スキャフォールディングに必要なパッケージ: `Microsoft.VisualStudio.Web.CodeGeneration.Design` と `Microsoft.EntityFrameworkCore.SqlServer`。

アプリが環境ごとにデータベースコンテキストを構成できるようにする複数の環境構成に関するガイダンスについては、「<xref:fundamentals/environments#environment-based-startup-class-and-methods>」を参照してください。

[!INCLUDE[](~/includes/scaffoldTFM-5.md)]
