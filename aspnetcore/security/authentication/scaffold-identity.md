---
title: IdentityASP.NET Core プロジェクトでのスキャフォールディング
author: rick-anderson
description: ASP.NET Core のプロジェクトでスキャフォールディングする方法について説明 Identity します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/1/2020
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
uid: security/authentication/scaffold-identity
ms.openlocfilehash: 813dd7837c265c78c584d66dd51bc23399d12fbe
ms.sourcegitcommit: 5156eab2118584405eb663e1fcd82f8bd7764504
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/31/2020
ms.locfileid: "93141496"
---
# <a name="scaffold-no-locidentity-in-aspnet-core-projects"></a>IdentityASP.NET Core プロジェクトでのスキャフォールディング

作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core は [ASP.NET Core Identity](xref:security/authentication/identity) [ Razor クラスライブラリ](xref:razor-pages/ui-class)としてを提供します。 を含むアプリケーション Identity では、scaffolder を適用して、 Identity Razor クラスライブラリ (rcl) に含まれているソースコードを選択的に追加できます。 コードを変更して動作を変更できるように、ソース コードを生成できます。 たとえば、登録で使用するコードを生成するようにスキャフォルダーに指示できます。 生成されたコードは、Identity RCL の同じコードよりも優先されます。 UI を完全に制御し、既定の RCL を使用しないようにするには、「 [完全な Identity ui ソースの作成](#full)」セクションを参照してください。

認証を含ま **ない** アプリケーションでは、scaffolder を適用して rcl パッケージを追加でき Identity ます。 生成される Identity コードの選択オプションがあります。

Scaffolder は、必要なコードの大部分を生成しますが、プロセスを完了するにはプロジェクトを更新する必要があります。 このドキュメントでは、スキャフォールディングの更新を完了するために必要な手順について説明し Identity ます。

ファイルの違いを示すソース管理システムを使用して、変更を元に戻すことをお勧めします。 Scaffolder を実行した後に変更を確認し Identity ます。

サービスは、 [2 要素認証](xref:security/authentication/identity-enable-qrcodes)、アカウントの [確認とパスワードの回復](xref:security/authentication/accconfirm)、およびの他のセキュリティ機能を使用する場合に必要です Identity 。 サービスまたはサービススタブは、スキャフォールディング時に生成されません Identity 。 これらの機能を有効にするサービスは、手動で追加する必要があります。 たとえば、「 [電子メールの確認を要求する](xref:security/authentication/accconfirm#require-email-confirmation)」を参照してください。

Identity新しいデータコンテキストを使用して、既存の個別のアカウントを持つプロジェクトにスキャフォールディングする場合:

* で `Startup.ConfigureServices` 、の呼び出しを削除します。
  * `AddDbContext`
  * `AddDefaultIdentity`

たとえば、 `AddDbContext` と `AddDefaultIdentity` は次のコードでコメントアウトされます。

[!code-csharp[](scaffold-identity/3.1sample/StartupRemove.cs?name=snippet)]

上記のコードでは、 *区分/ Identity / Identity HostingStartup.cs* に複製されたコードをコメントアウトします。

通常、個別のアカウントで作成されたアプリは、新しいデータコンテキストを作成し **ない** ようにする必要があります。

## <a name="scaffold-no-locidentity-into-an-empty-project"></a>Identity空のプロジェクトにスキャフォールディング

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

`Startup`次のようなコードでクラスを更新します。

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-without-existing-authorization"></a>Identity Razor 既存の承認なしでプロジェクトにスキャフォールディング

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

Identityは _Areas/HostingStartup.cs * で構成され Identity / Identity ます。 詳細については、「 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)」を参照してください。

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a>移行、UseAuthentication、およびレイアウト

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a>認証を有効にする

`Startup`次のようなコードでクラスを更新します。

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a>レイアウトの変更

省略可能: 次のように、ログイン部分 ( `_LoginPartial` ) をレイアウトファイルに追加します。

[!code-cshtml[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-with-authorization"></a>Identity Razor 承認を使用してプロジェクトにスキャフォールディング

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

一部 Identity のオプションは、 *Areas/ Identity / Identity HostingStartup.cs* で構成されています。 詳細については、「 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)」を参照してください。

## <a name="scaffold-no-locidentity-into-an-mvc-project-without-existing-authorization"></a>Identity既存の承認なしで MVC プロジェクトにスキャフォールディング

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

省略可能: `_LoginPartial` *Views/Shared/_Layout cshtml* ファイルにログイン部分 () を追加します。

[!code-cshtml[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* *Pages/shared/_LoginPartial cshtml* ファイルを *Views/shared/_LoginPartial* に移動します。

Identityは、 *Areas/ Identity / Identity HostingStartup.cs* で構成されます。 詳細については、「IHostingStartup」を参照してください。

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

`Startup`次のようなコードでクラスを更新します。

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-no-locidentity-into-an-mvc-project-with-authorization"></a>Identity承認を使用した MVC プロジェクトへのスキャフォールディング

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

## <a name="scaffold-no-locidentity-into-a-no-locblazor-server-project-without-existing-authorization"></a>Identity Blazor Server 既存の承認なしでプロジェクトにスキャフォールディング

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Identityは、 *Areas/ Identity / Identity HostingStartup.cs* で構成されます。 詳細については、「 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)」を参照してください。

### <a name="migrations"></a>移行

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

### <a name="pass-an-xsrf-token-to-the-app"></a>XSRF トークンをアプリに渡す

トークンは、次のようにコンポーネントに渡すことができます。

* 認証トークンがプロビジョニングされ、認証に保存されると cookie 、コンポーネントに渡すことができます。
* Razor コンポーネントでを直接使用することはできない `HttpContext` ため、のログアウトエンドポイントをに送信するための [要求防止偽造 (XSRF) トークン](xref:security/anti-request-forgery) を取得する方法はありません Identity `/Identity/Account/Logout` 。 XSRF トークンは、コンポーネントに渡すことができます。

詳細については、「<xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>」を参照してください。

*Pages/_Host cshtml* ファイルで、クラスおよびクラスに追加した後、トークンを設定し `InitialApplicationState` `TokenProvider` ます。

```csharp
@inject Microsoft.AspNetCore.Antiforgery.IAntiforgery Xsrf

...

var tokens = new InitialApplicationState
{
    ...

    XsrfToken = Xsrf.GetAndStoreTokens(HttpContext).RequestToken
};
```

`App`コンポーネント ( *app.xaml* ) を更新して、次の `InitialState.XsrfToken` ものを割り当てます。

```csharp
@inject TokenProvider TokenProvider

...

TokenProvider.XsrfToken = InitialState.XsrfToken;
```

トピック「」で説明されているサービスは、 `TokenProvider` `LoginDisplay` 次の [レイアウトと認証フローの変更](#layout-and-authentication-flow-changes) セクションのコンポーネントで使用されます。

### <a name="enable-authentication"></a>認証を有効にする

クラスの場合 `Startup` :

* ページサービスがに追加されていることを確認 Razor `Startup.ConfigureServices` します。
* [TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app)を使用する場合は、サービスを登録します。
* `UseDatabaseErrorPage`開発環境用のアプリケーションビルダーでを呼び出し `Startup.Configure` ます。
* `UseAuthentication`との後にを呼び出し `UseAuthorization` `UseRouting` ます。
* ページのエンドポイントを追加 Razor します。

[!code-csharp[](scaffold-identity/3.1sample/StartupBlazor.cs?highlight=3,6,14,27-28,32)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-and-authentication-flow-changes"></a>レイアウトと認証のフローの変更

`RedirectToLogin`プロジェクトルートのアプリの *共有* フォルダーにコンポーネント ( *redirecttologin. razor* ) を追加します。

```razor
@inject NavigationManager Navigation
@code {
    protected override void OnInitialized()
    {
        Navigation.NavigateTo("Identity/Account/Login?returnUrl=" +
            Uri.EscapeDataString(Navigation.Uri), true);
    }
}
```

`LoginDisplay`アプリの *共有* フォルダーにコンポーネント ( *logindisplay. razor* ) を追加します。 [TokenProvider サービス](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app)は、ログアウトエンドポイントにポストする HTML フォームの XSRF トークンを提供し Identity ます。

```razor
@using Microsoft.AspNetCore.Components.Authorization
@inject NavigationManager Navigation
@inject TokenProvider TokenProvider

<AuthorizeView>
    <Authorized>
        <a href="Identity/Account/Manage/Index">
            Hello, @context.User.Identity.Name!
        </a>
        <form action="/Identity/Account/Logout?returnUrl=%2F" method="post">
            <button class="nav-link btn btn-link" type="submit">Logout</button>
            <input name="__RequestVerificationToken" type="hidden" 
                value="@TokenProvider.XsrfToken">
        </form>
    </Authorized>
    <NotAuthorized>
        <a href="Identity/Account/Register">Register</a>
        <a href="Identity/Account/Login">Login</a>
    </NotAuthorized>
</AuthorizeView>
```

`MainLayout`コンポーネント ( *Shared/mainlayout. razor* ) で、 `LoginDisplay` 最上位の要素のコンテンツにコンポーネントを追加し `<div>` ます。

```razor
<div class="top-row px-4 auth">
    <LoginDisplay />
    <a href="https://docs.microsoft.com/aspnet/" target="_blank">About</a>
</div>
```

### <a name="style-authentication-endpoints"></a>認証エンドポイントのスタイルを適用する

ではページページが使用されるため、 Blazor Server Razor Identity ビジターがページとコンポーネントの間を移動すると、UI のスタイルが変更され Identity ます。 Incongruous スタイルに対処するには、次の2つのオプションがあります。

#### <a name="build-no-locidentity-components"></a>ビルド Identity コンポーネント

ページではなくコンポーネントを使用する方法 Identity は、コンポーネントを構築することです Identity 。 `SignInManager`とは `UserManager` コンポーネントでサポートされていないため Razor 、アプリの API エンドポイントを使用して、 Blazor Server ユーザーアカウントの操作を処理します。

#### <a name="use-a-custom-layout-with-no-locblazor-app-styles"></a>アプリスタイルでカスタムレイアウトを使用する Blazor

Identityページのレイアウトとスタイルを変更して、既定のテーマを使用するページを生成することができ Blazor ます。

> [!NOTE]
> このセクションの例は、カスタマイズの開始点にすぎません。 最適なユーザーエクスペリエンスを実現するには、追加の作業が必要になる可能性があります。

新しい `NavMenu_IdentityLayout` コンポーネント ( *Shared/NavMenu_ Identity Layout* ) を作成します。 コンポーネントのマークアップとコードについては、アプリのコンポーネントの同じコンテンツを使用し `NavMenu` ます ( *共有/ナビゲーションメニュー。 razor* )。 コンポーネントの `NavLink` 自動リダイレクトが `RedirectToLogin` 認証または承認を必要とするコンポーネントに対して失敗するため、匿名にアクセスできないコンポーネントに対してを除去します。

*Pages/Shared/Layout. cshtml* ファイルで、次の変更を行います。

* Razorタグヘルパーと *共有* フォルダー内のアプリのコンポーネントを使用するために、ファイルの先頭にディレクティブを追加します。

  ```cshtml
  @addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
  @using {APPLICATION ASSEMBLY}.Shared
  ```

  `{APPLICATION ASSEMBLY}`をアプリのアセンブリ名に置き換えます。

* `<base>`コンテンツにタグと Blazor スタイルシートを追加し `<link>` `<head>` ます。

  ```cshtml
  <base href="~/" />
  <link rel="stylesheet" href="~/css/site.css" />
  ```

* タグの内容 `<body>` を次のように変更します。

  ```cshtml
  <div class="sidebar" style="float:left">
      <component type="typeof(NavMenu_IdentityLayout)" 
          render-mode="ServerPrerendered" />
  </div>

  <div class="main" style="padding-left:250px">
      <div class="top-row px-4">
          @{
              var result = Engine.FindView(ViewContext, "_LoginPartial", 
                  isMainPage: false);
          }
          @if (result.Success)
          {
              await Html.RenderPartialAsync("_LoginPartial");
          }
          else
          {
              throw new InvalidOperationException("The default Identity UI " +
                  "layout requires a partial view '_LoginPartial'.");
          }
          <a href="https://docs.microsoft.com/aspnet/" target="_blank">About</a>
      </div>

      <div class="content px-4">
          @RenderBody()
      </div>
  </div>

  <script src="~/Identity/lib/jquery/dist/jquery.min.js"></script>
  <script src="~/Identity/lib/bootstrap/dist/js/bootstrap.bundle.min.js"></script>
  <script src="~/Identity/js/site.js" asp-append-version="true"></script>
  @RenderSection("Scripts", required: false)
  <script src="_framework/blazor.server.js"></script>
  ```

## <a name="scaffold-no-locidentity-into-a-no-locblazor-server-project-with-authorization"></a>Identity Blazor Server 承認を使用してプロジェクトにスキャフォールディング

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

一部 Identity のオプションは、 *Areas/ Identity / Identity HostingStartup.cs* で構成されています。 詳細については、「 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)」を参照してください。

## <a name="standalone-or-hosted-no-locblazor-webassembly-apps"></a>スタンドアロンまたはホストされた Blazor WebAssembly アプリ

クライアント側 Blazor WebAssembly アプリは独自の Identity UI アプローチを使用し、スキャフォールディングを使用することはできません ASP.NET Core Identity 。 ホストされたソリューションのサーバー側 ASP.NET Core アプリ Blazor は、 Razor この記事のページ/MVC ガイダンスに従うことができ、をサポートする他の種類の ASP.NET Core アプリと同様に構成され Identity ます。

フレームワークには、 Blazor Razor コンポーネントバージョンの Identity UI ページは含まれません。 Identity UI Razor コンポーネントは、サポートされていないサードパーティソースから作成または取得できます。

詳細については、「 [ Blazor セキュリティと Identity 記事](xref:blazor/security/index)」を参照してください。

<a name="full"></a>

## <a name="create-full-no-locidentity-ui-source"></a>完全な Identity UI ソースの作成

UI の完全な制御を維持するには、scaffolder を実行し、 Identity Identity [ **すべてのファイルを上書き** する] を選択します。

次の強調表示されたコードは、 Identity ASP.NET Core 2.1 web アプリで既定の UI をに置き換える変更を示して Identity います。 この操作を行うと、UI を完全に制御でき Identity ます。

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

既定値 Identity は、次のコードで置き換えられます。

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

次のコードでは、 [Loginpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath)、 [logoutpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)、および [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath)を設定しています。

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

`IEmailSender`の実装を登録します。次に例を示します。

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->

## <a name="password-configuration"></a>パスワードの構成

がで構成されている場合は <xref:Microsoft.AspNetCore.Identity.PasswordOptions> `Startup.ConfigureServices` 、スキャフォールディング pages のプロパティに[ `[StringLength]` 属性](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute)の構成が必要になることがあり `Password` Identity ます。 `InputModel``Password`プロパティは次のファイルにあります。

* `Areas/Identity/Pages/Account/Register.cshtml.cs`
* `Areas/Identity/Pages/Account/ResetPassword.cshtml.cs`

## <a name="disable-a-page"></a>ページを無効にする

このセクションでは、登録ページを無効にする方法について説明しますが、この方法を使用して任意のページを無効にすることができます。

ユーザー登録を無効にするには:

* スキャフォールディング Identity 。 Account、Account. Login、および Account. RegisterConfirmation を含めます。 次に例を示します。

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* ユーザーがこのエンドポイントから登録できないように、 *区分/ Identity /Pages/Account/Register.cshtml.cs* を更新します。

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* 次の変更との整合性を保つために、 *区分/////また Identity* はを更新します。

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* *区分///////// Identity ////* から登録リンクをコメントアウトまたは削除します。

  ```cshtml
  @*
  <p>
      <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
  </p>
  *@
  ```

* [ *区分/ページ/ Identity アカウント/* 登録の確認] ページを更新します。

  * コードとリンクを cshtml ファイルから削除します。
  * から確認コードを削除し `PageModel` ます。

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
* ユーザーがデータベースに追加され Identity ます。
* ユーザーに通知され、パスワードを変更するように指示されます。

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

次のコードは、ユーザーの追加の概要を示しています。

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

同様の方法で、運用環境のシナリオにも対応できます。

## <a name="prevent-publish-of-static-no-locidentity-assets"></a>静的アセットの発行を禁止する Identity

静的なアセットが web ルートに発行されないようにするに Identity は、「」を参照してください <xref:security/authentication/identity#prevent-publish-of-static-identity-assets> 。

## <a name="additional-resources"></a>その他の資料

* [ASP.NET Core 2.1 以降に認証コードが変更された](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core 2.1 以降は、 [ASP.NET Core Identity](xref:security/authentication/identity) [ Razor クラスライブラリ](xref:razor-pages/ui-class)としてを提供します。 を含むアプリケーション Identity では、scaffolder を適用して、 Identity Razor クラスライブラリ (rcl) に含まれているソースコードを選択的に追加できます。 コードを変更して動作を変更できるように、ソース コードを生成できます。 たとえば、登録で使用するコードを生成するようにスキャフォルダーに指示できます。 生成されたコードは、Identity RCL の同じコードよりも優先されます。 UI を完全に制御し、既定の RCL を使用しないようにするには、「 [完全な ID UI ソースの作成](#full)」セクションを参照してください。

認証を含ま **ない** アプリケーションでは、scaffolder を適用して rcl パッケージを追加でき Identity ます。 生成される Identity コードの選択オプションがあります。

Scaffolder は、必要なコードの大部分を生成しますが、プロセスを完了するにはプロジェクトを更新する必要があります。 このドキュメントでは、スキャフォールディングの更新を完了するために必要な手順について説明し Identity ます。

Scaffolder を Identity 実行すると、プロジェクトディレクトリに *ScaffoldingReadme.txt* ファイルが作成されます。 *ScaffoldingReadme.txt* ファイルには、スキャフォールディングの更新を完了するために必要な手順に関する一般的な指示が含まれてい Identity ます。 このドキュメントには、 *ScaffoldingReadme.txt* ファイルよりも完全な手順が含まれています。

ファイルの違いを示すソース管理システムを使用して、変更を元に戻すことをお勧めします。 Scaffolder を実行した後に変更を確認し Identity ます。

> [!NOTE]
> サービスは、 [2 要素認証](xref:security/authentication/identity-enable-qrcodes)、アカウントの [確認とパスワードの回復](xref:security/authentication/accconfirm)、およびの他のセキュリティ機能を使用する場合に必要です Identity 。 サービスまたはサービススタブは、スキャフォールディング時に生成されません Identity 。 これらの機能を有効にするサービスは、手動で追加する必要があります。 たとえば、「 [電子メールの確認を要求する](xref:security/authentication/accconfirm#require-email-confirmation)」を参照してください。

## <a name="scaffold-no-locidentity-into-an-empty-project"></a>Identity空のプロジェクトにスキャフォールディング

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

次の強調表示された呼び出しをクラスに追加し `Startup` ます。

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-without-existing-authorization"></a>Identity Razor 既存の承認なしでプロジェクトにスキャフォールディング

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

Identityは、 *Areas/ Identity / Identity HostingStartup.cs* で構成されます。 詳細については、「 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)」を参照してください。

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a>移行、UseAuthentication、およびレイアウト

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a>認証を有効にする

`Configure`クラスのメソッドで `Startup` 、の後にを呼び出し <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> `UseStaticFiles` ます。

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a>レイアウトの変更

省略可能: 次のように、ログイン部分 ( `_LoginPartial` ) をレイアウトファイルに追加します。

[!code-cshtml[](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-with-authorization"></a>Identity Razor 承認を使用してプロジェクトにスキャフォールディング

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

一部 Identity のオプションは、 *Areas/ Identity / Identity HostingStartup.cs* で構成されています。 詳細については、「 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)」を参照してください。

## <a name="scaffold-no-locidentity-into-an-mvc-project-without-existing-authorization"></a>Identity既存の承認なしで MVC プロジェクトにスキャフォールディング

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

省略可能: `_LoginPartial` *Views/Shared/_Layout cshtml* ファイルにログイン部分 () を追加します。

[!code-cshtml[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* *Pages/shared/_LoginPartial cshtml* ファイルを *Views/shared/_LoginPartial* に移動します。

Identityは、 *Areas/ Identity / Identity HostingStartup.cs* で構成されます。 詳細については、「IHostingStartup」を参照してください。

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

呼び出しの <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> 後 `UseStaticFiles` :

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-no-locidentity-into-an-mvc-project-with-authorization"></a>Identity承認を使用した MVC プロジェクトへのスキャフォールディング

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

*ページ/共有* フォルダーとそのフォルダー内のファイルを削除します。

<a name="full"></a>

## <a name="create-full-no-locidentity-ui-source"></a>完全な Identity UI ソースの作成

UI の完全な制御を維持するには、scaffolder を実行し、 Identity Identity [ **すべてのファイルを上書き** する] を選択します。

次の強調表示されたコードは、 Identity ASP.NET Core 2.1 web アプリで既定の UI をに置き換える変更を示して Identity います。 この操作を行うと、UI を完全に制御でき Identity ます。

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

既定値 Identity は、次のコードで置き換えられます。

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

次のコードでは、 [Loginpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath)、 [logoutpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)、および [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath)を設定しています。

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

`IEmailSender`の実装を登録します。次に例を示します。

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->

## <a name="password-configuration"></a>パスワードの構成

がで構成されている場合は <xref:Microsoft.AspNetCore.Identity.PasswordOptions> `Startup.ConfigureServices` 、スキャフォールディング pages のプロパティに[ `[StringLength]` 属性](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute)の構成が必要になることがあり `Password` Identity ます。 `InputModel``Password`プロパティは次のファイルにあります。

* `Areas/Identity/Pages/Account/Register.cshtml.cs`
* `Areas/Identity/Pages/Account/ResetPassword.cshtml.cs`

## <a name="disable-register-page"></a>登録の無効化ページ

ユーザー登録を無効にするには:

* スキャフォールディング Identity 。 Account、Account. Login、および Account. RegisterConfirmation を含めます。 次に例を示します。

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* ユーザーがこのエンドポイントから登録できないように、 *区分/ Identity /Pages/Account/Register.cshtml.cs* を更新します。

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* 次の変更との整合性を保つために、 *区分/////また Identity* はを更新します。

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* *区分///////// Identity ////* から登録リンクをコメントアウトまたは削除します。

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* [ *区分/ページ/ Identity アカウント/* 登録の確認] ページを更新します。

  * コードとリンクを cshtml ファイルから削除します。
  * から確認コードを削除し `PageModel` ます。

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
* ユーザーがデータベースに追加され Identity ます。
* ユーザーに通知され、パスワードを変更するように指示されます。

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

次のコードは、ユーザーの追加の概要を示しています。

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

同様の方法で、運用環境のシナリオにも対応できます。

## <a name="additional-resources"></a>その他の資料

* [ASP.NET Core 2.1 以降に認証コードが変更された](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end
