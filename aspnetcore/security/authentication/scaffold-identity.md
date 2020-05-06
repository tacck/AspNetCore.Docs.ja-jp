---
title: ASP.NET Core Identityプロジェクトでのスキャフォールディング
author: rick-anderson
description: ASP.NET Core のプロジェクトでIdentityスキャフォールディングする方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/1/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/scaffold-identity
ms.openlocfilehash: 6f1ff69863e14c73e90496ea61188387f5267b19
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82768391"
---
# <a name="scaffold-identity-in-aspnet-core-projects"></a>ASP.NET Core Identityプロジェクトでのスキャフォールディング

作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core は、 [ Razorクラスライブラリ](xref:razor-pages/ui-class)として[ASP.NET Core Identity ](xref:security/authentication/identity)を提供します。 を含むIdentityアプリケーションでは、scaffolder を適用して、 Identity Razorクラスライブラリ (rcl) に含まれているソースコードを選択的に追加できます。 コードを変更して動作を変更できるように、ソース コードを生成できます。 たとえば、登録で使用するコードを生成するようにスキャフォルダーに指示できます。 生成されたコードは、 Identity rcl の同じコードよりも優先されます。 UI を完全に制御し、既定の RCL を使用しないようにするには、「[完全な ID UI ソースの作成](#full)」セクションを参照してください。

認証を含ま**ない**アプリケーションでは、scaffolder を適用して rcl Identityパッケージを追加できます。 生成するコードを選択Identityすることもできます。

Scaffolder は、必要なコードの大部分を生成しますが、プロセスを完了するにはプロジェクトを更新する必要があります。 このドキュメントでは、 Identityスキャフォールディングの更新を完了するために必要な手順について説明します。

ファイルの違いを示すソース管理システムを使用して、変更を元に戻すことをお勧めします。 Scaffolder をIdentity実行した後に変更を確認します。

サービスは、 [2 要素認証](xref:security/authentication/identity-enable-qrcodes)、アカウントの[確認とパスワードの回復](xref:security/authentication/accconfirm)、およびの他のセキュリティIdentity機能を使用する場合に必要です。 サービスまたはサービススタブは、スキャフォールディングIdentity時に生成されません。 これらの機能を有効にするサービスは、手動で追加する必要があります。 たとえば、「[電子メールの確認を要求する](xref:security/authentication/accconfirm#require-email-confirmation)」を参照してください。

新しいデータIdentityコンテキストを使用して、既存の個別のアカウントを持つプロジェクトにスキャフォールディングする場合:

* で`Startup.ConfigureServices`、の呼び出しを削除します。
  * `AddDbContext`
  * `AddDefaultIdentity`

たとえば、 `AddDbContext`と`AddDefaultIdentity`は次のコードでコメントアウトされます。

[!code-csharp[](scaffold-identity/3.1sample/StartupRemove.cs?name=snippet)]

上記のコードは、*区分/Identity/IdentityHostingStartup.cs*で重複するコードをコメントアウトします。

通常、個別のアカウントで作成されたアプリは、新しいデータコンテキストを作成し***ない***ようにする必要があります。

## <a name="scaffold-identity-into-an-empty-project"></a>Id を空のプロジェクトにスキャフォールディング

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

次の`Startup`ようなコードでクラスを更新します。

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a>既存のRazor承認なしで id をプロジェクトにスキャフォールディング

<!--  Updated for 3.0
set projNam=RPnoAuth
set projType=webapp

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef database drop
dotnet ef migrations add CreateIdentitySchema0
dotnet ef database update
-->

<!-- ERROR
There is already an object named 'AspNetRoles' in the database.

Fixed via dotnet ef database drop
before dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Identityは、 *Areas/Identity/IdentityHostingStartup.cs*で構成されます。 詳細については、「 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)」を参照してください。

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a>移行、UseAuthentication、およびレイアウト

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a>認証を有効にする

次の`Startup`ようなコードでクラスを更新します。

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a>レイアウトの変更

省略可能: 次のように`_LoginPartial`、ログイン部分 () をレイアウトファイルに追加します。

[!code-html[Main](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a>承認を使用しRazorて id をプロジェクトにスキャフォールディング

<!--
Use >=2.1: dotnet new webapp -au Individual -o RPauth
Use = 2.0: dotnet new razor -au Individual -o RPauth
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]
一部Identityのオプションは、 *Areas/Identity/IdentityHostingStartup.cs*で構成されています。 詳細については、「 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)」を参照してください。

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a>既存の承認なしでスキャフォールディング identity を MVC プロジェクトに

<!--
set projNam=MvcNoAuth
set projType=mvc
set version=2.1.0

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v %version%
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

省略可能:`_LoginPartial` *Views/Shared/_Layout cshtml*ファイルにログイン部分 () を追加します。

[!code-html[Main](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* *Pages/shared/_LoginPartial cshtml*ファイルを*Views/shared/_LoginPartial*に移動します。

Identityは、 *Areas/Identity/IdentityHostingStartup.cs*で構成されます。 詳細については、「IHostingStartup」を参照してください。

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

次の`Startup`ようなコードでクラスを更新します。

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a>承認を使用して id を MVC プロジェクトにスキャフォールディング

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a>完全な id UI ソースの作成

Identity UI の完全な制御を維持するにはIdentity 、scaffolder を実行し、[すべてのファイルを**上書き**する] を選択します。

次の強調表示されたコードは、ASP.NET Core Identity 2.1 web Identityアプリで既定の UI をに置き換える変更を示しています。 この操作を行うと、 Identity UI を完全に制御できます。

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

既定値Identityは、次のコードで置き換えられます。

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

次のコードでは、 [Loginpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath)、 [logoutpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)、および[AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath)を設定しています。

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

の`IEmailSender`実装を登録します。次に例を示します。

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a>登録の無効化ページ

ユーザー登録を無効にするには:

* スキャフォールディングIdentity。 Account、Account. Login、および Account. RegisterConfirmation を含めます。 次に例を示します。

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* ユーザーがこのエンドポイントから登録できないように、*区分/Identity/Pages/Account/Register.cshtml.cs*を更新します。

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* 次の変更との整合性を保つために、*区分/////またIdentity* はを更新します。

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* *区分/////////Identity////* から登録リンクをコメントアウトまたは削除します。

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* [*区分/ページIdentity/アカウント/* 登録の確認] ページを更新します。

  * コードとリンクを cshtml ファイルから削除します。
  * から確認コードを削除し`PageModel`ます。

  ```csharp
   [AllowAnonymous]
    public class RegisterConfirmationModel : PageModel
    {
        public IActionResult OnGet()
        {  
            return Page();
        }
    }
  ```
  
### <a name="use-another-app-to-add-users"></a>別のアプリを使用してユーザーを追加する

Web アプリの外部にユーザーを追加するためのメカニズムを提供します。 ユーザーを追加するためのオプションは次のとおりです。

* 専用管理者 web アプリ。
* コンソールアプリ。

次のコードは、ユーザーを追加する方法の概要を示しています。

* ユーザーの一覧がメモリに読み込まれます。
* ユーザーごとに一意の強力なパスワードが生成されます。
* ユーザーがIdentityデータベースに追加されます。
* ユーザーに通知され、パスワードを変更するように指示されます。

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

次のコードは、ユーザーの追加の概要を示しています。

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

同様の方法で、運用環境のシナリオにも対応できます。

## <a name="prevent-publish-of-static-identity-assets"></a>静的Identityアセットの発行を禁止する

静的Identityなアセットが web ルートに発行され<xref:security/authentication/identity#prevent-publish-of-static-identity-assets>ないようにするには、「」を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [ASP.NET Core 2.1 以降に認証コードが変更された](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core 2.1 以降では、 [ Razorクラスライブラリ](xref:razor-pages/ui-class)として[ASP.NET Core Identity ](xref:security/authentication/identity)が提供されます。 を含むIdentityアプリケーションでは、scaffolder を適用して、 Identity Razorクラスライブラリ (rcl) に含まれているソースコードを選択的に追加できます。 コードを変更して動作を変更できるように、ソース コードを生成できます。 たとえば、登録で使用するコードを生成するようにスキャフォルダーに指示できます。 生成されたコードは、 Identity rcl の同じコードよりも優先されます。 UI を完全に制御し、既定の RCL を使用しないようにするには、「[完全な ID UI ソースの作成](#full)」セクションを参照してください。

認証を含ま**ない**アプリケーションでは、scaffolder を適用して rcl Identityパッケージを追加できます。 生成するコードを選択Identityすることもできます。

Scaffolder は、必要なコードの大部分を生成しますが、プロセスを完了するにはプロジェクトを更新する必要があります。 このドキュメントでは、 Identityスキャフォールディングの更新を完了するために必要な手順について説明します。

Identity Scaffolder を実行すると、 *ScaffoldingReadme*ファイルがプロジェクトディレクトリに作成されます。 *ScaffoldingReadme*ファイルには、 Identityスキャフォールディングの更新を完了するために必要な手順に関する一般的な指示が含まれています。 このドキュメントには、 *ScaffoldingReadme*ファイルよりも完全な手順が含まれています。

ファイルの違いを示すソース管理システムを使用して、変更を元に戻すことをお勧めします。 Scaffolder をIdentity実行した後に変更を確認します。

> [!NOTE]
> サービスは、 [2 要素認証](xref:security/authentication/identity-enable-qrcodes)、アカウントの[確認とパスワードの回復](xref:security/authentication/accconfirm)、およびの他のセキュリティIdentity機能を使用する場合に必要です。 サービスまたはサービススタブは、スキャフォールディングIdentity時に生成されません。 これらの機能を有効にするサービスは、手動で追加する必要があります。 たとえば、「[電子メールの確認を要求する](xref:security/authentication/accconfirm#require-email-confirmation)」を参照してください。

## <a name="scaffold-identity-into-an-empty-project"></a>Id を空のプロジェクトにスキャフォールディング

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

次の強調表示された`Startup`呼び出しをクラスに追加します。

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a>既存のRazor承認なしで id をプロジェクトにスキャフォールディング

<!--  Updated for 3.0
set projNam=RPnoAuth
set projType=webapp

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Identityは、 *Areas/Identity/IdentityHostingStartup.cs*で構成されます。 詳細については、「 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)」を参照してください。

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a>移行、UseAuthentication、およびレイアウト

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a>認証を有効にする

クラスの`Configure`メソッドで、 `UseStaticFiles`次のように[useauthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)を呼び出します。 `Startup`

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a>レイアウトの変更

省略可能: 次のように`_LoginPartial`、ログイン部分 () をレイアウトファイルに追加します。

[!code-html[Main](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a>承認を使用しRazorて id をプロジェクトにスキャフォールディング

<!--
Use >=2.1: dotnet new webapp -au Individual -o RPauth
Use = 2.0: dotnet new razor -au Individual -o RPauth
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]
一部Identityのオプションは、 *Areas/Identity/IdentityHostingStartup.cs*で構成されています。 詳細については、「 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)」を参照してください。

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a>既存の承認なしでスキャフォールディング identity を MVC プロジェクトに

<!--
set projNam=MvcNoAuth
set projType=mvc
set version=2.1.0

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v %version%
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

省略可能:`_LoginPartial` *Views/Shared/_Layout cshtml*ファイルにログイン部分 () を追加します。

[!code-html[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* *Pages/shared/_LoginPartial cshtml*ファイルを*Views/shared/_LoginPartial*に移動します。

Identityは、 *Areas/Identity/IdentityHostingStartup.cs*で構成されます。 詳細については、「IHostingStartup」を参照してください。

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

次の後`UseStaticFiles`に[useauthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)を呼び出します。

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a>承認を使用して id を MVC プロジェクトにスキャフォールディング

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

*ページ/共有*フォルダーとそのフォルダー内のファイルを削除します。

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a>完全な id UI ソースの作成

Identity UI の完全な制御を維持するにはIdentity 、scaffolder を実行し、[すべてのファイルを**上書き**する] を選択します。

次の強調表示されたコードは、ASP.NET Core Identity 2.1 web Identityアプリで既定の UI をに置き換える変更を示しています。 この操作を行うと、 Identity UI を完全に制御できます。

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

既定値Identityは、次のコードで置き換えられます。

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

次のコードでは、 [Loginpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath)、 [logoutpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)、および[AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath)を設定しています。

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

の`IEmailSender`実装を登録します。次に例を示します。

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a>登録の無効化ページ

ユーザー登録を無効にするには:

* スキャフォールディングIdentity。 Account、Account. Login、および Account. RegisterConfirmation を含めます。 次に例を示します。

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* ユーザーがこのエンドポイントから登録できないように、*区分/Identity/Pages/Account/Register.cshtml.cs*を更新します。

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* 次の変更との整合性を保つために、*区分/////またIdentity* はを更新します。

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* *区分/////////Identity////* から登録リンクをコメントアウトまたは削除します。

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* [*区分/ページIdentity/アカウント/* 登録の確認] ページを更新します。

  * コードとリンクを cshtml ファイルから削除します。
  * から確認コードを削除し`PageModel`ます。

  ```csharp
   [AllowAnonymous]
    public class RegisterConfirmationModel : PageModel
    {
        public IActionResult OnGet()
        {  
            return Page();
        }
    }
  ```
  
### <a name="use-another-app-to-add-users"></a>別のアプリを使用してユーザーを追加する

Web アプリの外部にユーザーを追加するためのメカニズムを提供します。 ユーザーを追加するためのオプションは次のとおりです。

* 専用管理者 web アプリ。
* コンソールアプリ。

次のコードは、ユーザーを追加する方法の概要を示しています。

* ユーザーの一覧がメモリに読み込まれます。
* ユーザーごとに一意の強力なパスワードが生成されます。
* ユーザーがIdentityデータベースに追加されます。
* ユーザーに通知され、パスワードを変更するように指示されます。

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

次のコードは、ユーザーの追加の概要を示しています。

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

同様の方法で、運用環境のシナリオにも対応できます。

## <a name="additional-resources"></a>その他のリソース

* [ASP.NET Core 2.1 以降に認証コードが変更された](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end