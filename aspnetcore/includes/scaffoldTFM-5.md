スキャフォールディング エラーが発生した場合は、ターゲット フレームワーク モニカー (TFM) がプロジェクト ファイル内の NuGet パッケージのバージョンと一致していることを確認します。 たとえば、次のプロジェクト ファイルでは、.NET Core のバージョン 5.0 と、一覧表示された NuGet パッケージが使用されます。

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">

  <PropertyGroup>
    <TargetFramework>net5.0</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore" Version="5.0.0-*" />
    <PackageReference Include="Microsoft.AspNetCore.Identity.EntityFrameworkCore" Version="5.0.0-*" />
    <PackageReference Include="Microsoft.AspNetCore.Identity.UI" Version="5.0.0-*" />
    <PackageReference Include="Microsoft.EntityFrameworkCore.Design" Version="5.0.0-*" />
    <PackageReference Include="Microsoft.EntityFrameworkCore.SqlServer" Version="5.0.0-*" />
    <PackageReference Include="Microsoft.EntityFrameworkCore.Tools" Version="5.0.0-*" />
    <PackageReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Design" Version="5.0.0-*" />
  </ItemGroup>

</Project>
```
