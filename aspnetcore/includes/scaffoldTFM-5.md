<span data-ttu-id="b376b-101">スキャフォールディング エラーが発生した場合は、ターゲット フレームワーク モニカー (TFM) がプロジェクト ファイル内の NuGet パッケージのバージョンと一致していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="b376b-101">If you get a scaffolding error, verify the Target Framework Moniker (TFM) matches the NuGet package version in the project file.</span></span> <span data-ttu-id="b376b-102">たとえば、次のプロジェクト ファイルでは、.NET Core のバージョン 5.0 と、一覧表示された NuGet パッケージが使用されます。</span><span class="sxs-lookup"><span data-stu-id="b376b-102">For example, the following project file uses version 5.0 for .NET and the listed NuGet packages:</span></span>

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
