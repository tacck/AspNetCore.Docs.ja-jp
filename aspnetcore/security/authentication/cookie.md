---
title: ASP.NET Core せずに cookie 認証を使用するIdentity
author: rick-anderson
description: ASP.NET Core せずに cookie 認証を使用する方法について説明 Identity します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 02/11/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/cookie
ms.openlocfilehash: 401d03352b8c2c040202716bdddf484e3b778f52
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408826"
---
# <a name="use-cookie-authentication-without-aspnet-core-identity"></a>ASP.NET Core せずに cookie 認証を使用するIdentity

作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core Identity は、ログインを作成および管理するためのフル機能を備えた完全な認証プロバイダーです。 ただし、ASP.NET Core のない cookie ベースの認証プロバイダーを Identity 使用することもできます。 詳細については、「<xref:security/authentication/identity>」を参照してください。

[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

サンプルアプリでのデモンストレーションのために、架空のユーザーであるマリア Rodriguez のユーザーアカウントは、アプリにハードコードされています。 **電子メール**アドレスと任意のパスワードを使用して、 `maria.rodriguez@contoso.com` ユーザーにサインインします。 ユーザーは、 `AuthenticateUser` *Pages/Account/Login. cshtml. .cs*ファイルのメソッドで認証されます。 実際の例では、ユーザーはデータベースに対して認証されます。

## <a name="configuration"></a>構成

アプリで[AspNetCore メタパッケージ](xref:fundamentals/metapackage-app)が使用されていない場合は、 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/)パッケージのプロジェクトファイルにパッケージ参照を作成します。

メソッドで、メソッド `Startup.ConfigureServices` とメソッドを使用して認証ミドルウェアサービスを作成し <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> ます。

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet1)]

<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme>に渡されたは、 `AddAuthentication` アプリの既定の認証スキームを設定します。 `AuthenticationScheme`は、cookie 認証のインスタンスが複数存在し、[特定のスキームで承認](xref:security/authorization/limitingidentitybyscheme)する場合に便利です。 `AuthenticationScheme`を[Cookieauthenticationdefaults に設定します。 authenticationscheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme)は、スキームに対して "cookie" の値を提供します。 スキームを区別する任意の文字列値を指定できます。

アプリの認証方式は、アプリの cookie 認証スキームとは異なります。 クッキー認証スキームがに指定されていない場合は <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> 、 `CookieAuthenticationDefaults.AuthenticationScheme` (cookie) を使用します。

認証 cookie の <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> プロパティは、既定でに設定され `true` ています。 サイトビジターがデータコレクションに同意していない場合は、認証 cookie が許可されます。 詳細については、「<xref:security/gdpr#essential-cookies>」を参照してください。

で、とを呼び出して、 `Startup.Configure` `UseAuthentication` プロパティを `UseAuthorization` 設定し、 `HttpContext.User` 要求の承認ミドルウェアを実行します。 `UseAuthentication`を `UseAuthorization` 呼び出す前に、メソッドとメソッドを呼び出し `UseEndpoints` ます。

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet2)]

クラスは、 <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> 認証プロバイダーオプションを構成するために使用されます。

`CookieAuthenticationOptions`メソッドの認証用にサービス構成でを設定し `Startup.ConfigureServices` ます。

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="cookie-policy-middleware"></a>クッキーポリシーミドルウェア

Cookie[ポリシーミドルウェア](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware)を使用すると、cookie ポリシー機能が有効になります。 アプリ処理パイプラインへのミドルウェアの追加は順序に依存しており、 &mdash; パイプラインに登録されている下流コンポーネントにのみ影響します。

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

Cookie <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> を追加または削除したときに cookie 処理のグローバル特性を制御し、クッキー処理ハンドラーにフックするために、クッキーポリシーミドルウェアに提供されているを使用します。

既定 <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> 値は、 `SameSiteMode.Lax` OAuth2 認証を許可することです。 と同じサイトポリシーを厳密に適用するに `SameSiteMode.Strict` は、を設定し `MinimumSameSitePolicy` ます。 この設定は、OAuth2 およびその他のクロスオリジン認証スキームを分割しますが、クロスオリジン要求処理に依存しない他の種類のアプリの cookie セキュリティのレベルを昇格させます。

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

の Cookie ポリシーミドルウェア設定は、次の表 `MinimumSameSitePolicy` `Cookie.SameSite` に従って設定内のの設定に影響を与える可能性が `CookieAuthenticationOptions` あります。

| MinimumSameSitePolicy | SameSite | 結果の SameSite 設定 |
| --------------------- | --------------- | --------------------------------- |
| SameSiteMode なし     | SameSiteMode なし<br>SameSiteMode 厳密でない<br>SameSiteMode Strict | SameSiteMode なし<br>SameSiteMode 厳密でない<br>SameSiteMode Strict |
| SameSiteMode 厳密でない      | SameSiteMode なし<br>SameSiteMode 厳密でない<br>SameSiteMode Strict | SameSiteMode 厳密でない<br>SameSiteMode 厳密でない<br>SameSiteMode Strict |
| SameSiteMode Strict   | SameSiteMode なし<br>SameSiteMode 厳密でない<br>SameSiteMode Strict | SameSiteMode Strict<br>SameSiteMode Strict<br>SameSiteMode Strict |

## <a name="create-an-authentication-cookie"></a>認証 cookie を作成する

ユーザー情報を保持する cookie を作成するには、を構築 <xref:System.Security.Claims.ClaimsPrincipal> します。 ユーザー情報がシリアル化され、cookie に格納されます。 

任意の <xref:System.Security.Claims.ClaimsIdentity> が必要なを使用してを作成 <xref:System.Security.Claims.Claim> し <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> 、を呼び出してユーザーにサインインします。

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

`SignInAsync`暗号化されたクッキーを作成し、現在の応答に追加します。 が指定されていない場合は `AuthenticationScheme` 、既定のスキームが使用されます。

<xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri>は、既定ではいくつかの特定のパス (ログインパスやログアウトパスなど) でのみ使用されます。 詳細については、「 [Cookieauthenticationhandler source](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/Cookies/src/CookieAuthenticationHandler.cs#L334)」を参照してください。

暗号化には ASP.NET Core の[データ保護](xref:security/data-protection/using-data-protection)システムが使用されます。 複数のコンピューターでホストされているアプリの場合、アプリ間で負荷を分散する場合、または web ファームを使用する場合は、同じキーリングとアプリ識別子を使用するように[データ保護を構成](xref:security/data-protection/configuration/overview)します。

## <a name="sign-out"></a>サインアウト

現在のユーザーをサインアウトして cookie を削除するには、次のように呼び出し <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> ます。

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

`CookieAuthenticationDefaults.AuthenticationScheme`(または "cookie") がスキームとして使用されていない場合 (たとえば、"ContosoCookie")、認証プロバイダーを構成するときに使用するスキームを指定します。 それ以外の場合は、既定のスキームが使用されます。

## <a name="react-to-back-end-changes"></a>バックエンドの変更に対処する

Cookie が作成されると、cookie は id の単一のソースになります。 バックエンドシステムでユーザーアカウントが無効になっている場合は、次のようになります。

* アプリの cookie 認証システムは、認証 cookie に基づいて要求を処理し続けます。
* 認証 cookie が有効である限り、ユーザーはアプリにサインインしたままになります。

<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*>イベントを使用して、cookie id の検証をインターセプトおよびオーバーライドできます。 すべての要求で cookie を検証すると、アプリにアクセスするユーザーが失効するリスクが軽減されます。

Cookie を検証する方法の1つは、ユーザーデータベースがいつ変更されたかを追跡することです。 ユーザーの cookie が発行されてからデータベースが変更されていない場合、cookie が有効である場合、ユーザーを再認証する必要はありません。 サンプルアプリでは、データベースはに実装され、 `IUserRepository` 値を格納し `LastChanged` ます。 データベースでユーザーが更新されると、 `LastChanged` 値は現在の時刻に設定されます。

データベースが値に基づいて変更されたときに cookie を無効にするには `LastChanged` 、 `LastChanged` データベースから現在の値を含むクレームを使用して cookie を作成し `LastChanged` ます。

```csharp
var claims = new List<Claim>
{
    new Claim(ClaimTypes.Name, user.Email),
    new Claim("LastChanged", {Database Value})
};

var claimsIdentity = new ClaimsIdentity(
    claims, 
    CookieAuthenticationDefaults.AuthenticationScheme);

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme, 
    new ClaimsPrincipal(claimsIdentity));
```

イベントのオーバーライドを実装するには `ValidatePrincipal` 、から派生するクラスに次のシグネチャを持つメソッドを記述し <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents> ます。

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

の実装例を次に示し `CookieAuthenticationEvents` ます。

```csharp
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.Cookies;

public class CustomCookieAuthenticationEvents : CookieAuthenticationEvents
{
    private readonly IUserRepository _userRepository;

    public CustomCookieAuthenticationEvents(IUserRepository userRepository)
    {
        // Get the database from registered DI services.
        _userRepository = userRepository;
    }

    public override async Task ValidatePrincipal(CookieValidatePrincipalContext context)
    {
        var userPrincipal = context.Principal;

        // Look for the LastChanged claim.
        var lastChanged = (from c in userPrincipal.Claims
                           where c.Type == "LastChanged"
                           select c.Value).FirstOrDefault();

        if (string.IsNullOrEmpty(lastChanged) ||
            !_userRepository.ValidateLastChanged(lastChanged))
        {
            context.RejectPrincipal();

            await context.HttpContext.SignOutAsync(
                CookieAuthenticationDefaults.AuthenticationScheme);
        }
    }
}
```

Cookie サービスの登録時に、メソッドでイベントインスタンスを登録 `Startup.ConfigureServices` します。 クラスの[スコープ付きサービス登録](xref:fundamentals/dependency-injection#service-lifetimes)を指定し `CustomCookieAuthenticationEvents` ます。

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

ユーザーの名前が、 &mdash; セキュリティに影響しない決定をどのような方法でも更新している場合を考えてみます。 ユーザープリンシパルを非破壊更新する場合は、を呼び出し、 `context.ReplacePrincipal` `context.ShouldRenew` プロパティをに設定し `true` ます。

> [!WARNING]
> ここで説明する方法は、すべての要求でトリガーされます。 すべての要求に対して認証 cookie を検証すると、アプリのパフォーマンスが大幅に低下する可能性があります。

## <a name="persistent-cookies"></a>永続的な cookie

Cookie がブラウザーセッション間で保持されるようにすることもできます。 この永続化を有効にする必要があるのは、サインイン時に [忘れずに保存] チェックボックスをオンにして、明示的なユーザーの同意を有効にすることだけです。 

次のコードスニペットは、ブラウザーのクロージャを介して存続する id とそれに対応する cookie を作成します。 以前に構成したスライド式有効期限の設定はすべて受け入れられます。 ブラウザーを閉じている間に cookie の有効期限が切れると、ブラウザーは再起動後に cookie をクリアします。

<xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent>をに設定し `true` <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> ます。

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

## <a name="absolute-cookie-expiration"></a>クッキーの絶対有効期限

絶対有効期限は、で設定でき <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> ます。 永続的なクッキーを作成するには、 `IsPersistent` も設定する必要があります。 それ以外の場合、cookie はセッションベースの有効期間で作成され、保持する認証チケットの前または後に有効期限が切れる可能性があります。 を設定すると、 `ExpiresUtc` のオプションの値がオーバーライドされ <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> ます (設定されている場合)。

次のコードスニペットでは、20分間継続する id とそれに対応する cookie を作成します。 これにより、以前に構成したスライド式有効期限の設定は無視されます。

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true,
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core Identity は、ログインを作成および管理するためのフル機能を備えた完全な認証プロバイダーです。 ただし、ASP.NET Core のない cookie ベースの認証認証プロバイダーを Identity 使用できます。 詳細については、「<xref:security/authentication/identity>」を参照してください。

[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

サンプルアプリでのデモンストレーションのために、架空のユーザーであるマリア Rodriguez のユーザーアカウントは、アプリにハードコードされています。 **電子メール**アドレスと任意のパスワードを使用して、 `maria.rodriguez@contoso.com` ユーザーにサインインします。 ユーザーは、 `AuthenticateUser` *Pages/Account/Login. cshtml. .cs*ファイルのメソッドで認証されます。 実際の例では、ユーザーはデータベースに対して認証されます。

## <a name="configuration"></a>構成

アプリで[AspNetCore メタパッケージ](xref:fundamentals/metapackage-app)が使用されていない場合は、 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/)パッケージのプロジェクトファイルにパッケージ参照を作成します。

メソッドで、メソッド `Startup.ConfigureServices` とメソッドを使用して認証ミドルウェアサービスを作成し <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> ます。

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet1)]

<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme>に渡されたは、 `AddAuthentication` アプリの既定の認証スキームを設定します。 `AuthenticationScheme`は、cookie 認証のインスタンスが複数存在し、[特定のスキームで承認](xref:security/authorization/limitingidentitybyscheme)する場合に便利です。 `AuthenticationScheme`を[Cookieauthenticationdefaults に設定します。 authenticationscheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme)は、スキームに対して "cookie" の値を提供します。 スキームを区別する任意の文字列値を指定できます。

アプリの認証方式は、アプリの cookie 認証スキームとは異なります。 クッキー認証スキームがに指定されていない場合は <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> 、 `CookieAuthenticationDefaults.AuthenticationScheme` (cookie) を使用します。

認証 cookie の <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> プロパティは、既定でに設定され `true` ています。 サイトビジターがデータコレクションに同意していない場合は、認証 cookie が許可されます。 詳細については、「<xref:security/gdpr#essential-cookies>」を参照してください。

メソッドで、 `Startup.Configure` メソッドを呼び出し `UseAuthentication` て、プロパティを設定する認証ミドルウェアを呼び出し `HttpContext.User` ます。 `UseAuthentication`またはを呼び出す前に、メソッドを呼び出し `UseMvcWithDefaultRoute` `UseMvc` ます。

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet2)]

クラスは、 <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> 認証プロバイダーオプションを構成するために使用されます。

`CookieAuthenticationOptions`メソッドの認証用にサービス構成でを設定し `Startup.ConfigureServices` ます。

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="cookie-policy-middleware"></a>クッキーポリシーミドルウェア

Cookie[ポリシーミドルウェア](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware)を使用すると、cookie ポリシー機能が有効になります。 アプリ処理パイプラインへのミドルウェアの追加は順序に依存しており、 &mdash; パイプラインに登録されている下流コンポーネントにのみ影響します。

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

Cookie <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> を追加または削除したときに cookie 処理のグローバル特性を制御し、クッキー処理ハンドラーにフックするために、クッキーポリシーミドルウェアに提供されているを使用します。

既定 <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> 値は、 `SameSiteMode.Lax` OAuth2 認証を許可することです。 と同じサイトポリシーを厳密に適用するに `SameSiteMode.Strict` は、を設定し `MinimumSameSitePolicy` ます。 この設定は、OAuth2 およびその他のクロスオリジン認証スキームを分割しますが、クロスオリジン要求処理に依存しない他の種類のアプリの cookie セキュリティのレベルを昇格させます。

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

の Cookie ポリシーミドルウェア設定は、次の表 `MinimumSameSitePolicy` `Cookie.SameSite` に従って設定内のの設定に影響を与える可能性が `CookieAuthenticationOptions` あります。

| MinimumSameSitePolicy | SameSite | 結果の SameSite 設定 |
| --------------------- | --------------- | --------------------------------- |
| SameSiteMode なし     | SameSiteMode なし<br>SameSiteMode 厳密でない<br>SameSiteMode Strict | SameSiteMode なし<br>SameSiteMode 厳密でない<br>SameSiteMode Strict |
| SameSiteMode 厳密でない      | SameSiteMode なし<br>SameSiteMode 厳密でない<br>SameSiteMode Strict | SameSiteMode 厳密でない<br>SameSiteMode 厳密でない<br>SameSiteMode Strict |
| SameSiteMode Strict   | SameSiteMode なし<br>SameSiteMode 厳密でない<br>SameSiteMode Strict | SameSiteMode Strict<br>SameSiteMode Strict<br>SameSiteMode Strict |

## <a name="create-an-authentication-cookie"></a>認証 cookie を作成する

ユーザー情報を保持する cookie を作成するには、を構築 <xref:System.Security.Claims.ClaimsPrincipal> します。 ユーザー情報がシリアル化され、cookie に格納されます。 

任意の <xref:System.Security.Claims.ClaimsIdentity> が必要なを使用してを作成 <xref:System.Security.Claims.Claim> し <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> 、を呼び出してユーザーにサインインします。

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

`SignInAsync`暗号化されたクッキーを作成し、現在の応答に追加します。 が指定されていない場合は `AuthenticationScheme` 、既定のスキームが使用されます。

暗号化には ASP.NET Core の[データ保護](xref:security/data-protection/using-data-protection)システムが使用されます。 複数のコンピューターでホストされているアプリの場合、アプリ間で負荷を分散する場合、または web ファームを使用する場合は、同じキーリングとアプリ識別子を使用するように[データ保護を構成](xref:security/data-protection/configuration/overview)します。

## <a name="sign-out"></a>サインアウト

現在のユーザーをサインアウトして cookie を削除するには、次のように呼び出し <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> ます。

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

`CookieAuthenticationDefaults.AuthenticationScheme`(または "cookie") がスキームとして使用されていない場合 (たとえば、"ContosoCookie")、認証プロバイダーを構成するときに使用するスキームを指定します。 それ以外の場合は、既定のスキームが使用されます。

## <a name="react-to-back-end-changes"></a>バックエンドの変更に対処する

Cookie が作成されると、cookie は id の単一のソースになります。 バックエンドシステムでユーザーアカウントが無効になっている場合は、次のようになります。

* アプリの cookie 認証システムは、認証 cookie に基づいて要求を処理し続けます。
* 認証 cookie が有効である限り、ユーザーはアプリにサインインしたままになります。

<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*>イベントを使用して、cookie id の検証をインターセプトおよびオーバーライドできます。 すべての要求で cookie を検証すると、アプリにアクセスするユーザーが失効するリスクが軽減されます。

Cookie を検証する方法の1つは、ユーザーデータベースがいつ変更されたかを追跡することです。 ユーザーの cookie が発行されてからデータベースが変更されていない場合、cookie が有効である場合、ユーザーを再認証する必要はありません。 サンプルアプリでは、データベースはに実装され、 `IUserRepository` 値を格納し `LastChanged` ます。 データベースでユーザーが更新されると、 `LastChanged` 値は現在の時刻に設定されます。

データベースが値に基づいて変更されたときに cookie を無効にするには `LastChanged` 、 `LastChanged` データベースから現在の値を含むクレームを使用して cookie を作成し `LastChanged` ます。

```csharp
var claims = new List<Claim>
{
    new Claim(ClaimTypes.Name, user.Email),
    new Claim("LastChanged", {Database Value})
};

var claimsIdentity = new ClaimsIdentity(
    claims, 
    CookieAuthenticationDefaults.AuthenticationScheme);

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme, 
    new ClaimsPrincipal(claimsIdentity));
```

イベントのオーバーライドを実装するには `ValidatePrincipal` 、から派生するクラスに次のシグネチャを持つメソッドを記述し <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents> ます。

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

の実装例を次に示し `CookieAuthenticationEvents` ます。

```csharp
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.Cookies;

public class CustomCookieAuthenticationEvents : CookieAuthenticationEvents
{
    private readonly IUserRepository _userRepository;

    public CustomCookieAuthenticationEvents(IUserRepository userRepository)
    {
        // Get the database from registered DI services.
        _userRepository = userRepository;
    }

    public override async Task ValidatePrincipal(CookieValidatePrincipalContext context)
    {
        var userPrincipal = context.Principal;

        // Look for the LastChanged claim.
        var lastChanged = (from c in userPrincipal.Claims
                           where c.Type == "LastChanged"
                           select c.Value).FirstOrDefault();

        if (string.IsNullOrEmpty(lastChanged) ||
            !_userRepository.ValidateLastChanged(lastChanged))
        {
            context.RejectPrincipal();

            await context.HttpContext.SignOutAsync(
                CookieAuthenticationDefaults.AuthenticationScheme);
        }
    }
}
```

Cookie サービスの登録時に、メソッドでイベントインスタンスを登録 `Startup.ConfigureServices` します。 クラスの[スコープ付きサービス登録](xref:fundamentals/dependency-injection#service-lifetimes)を指定し `CustomCookieAuthenticationEvents` ます。

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

ユーザーの名前が、 &mdash; セキュリティに影響しない決定をどのような方法でも更新している場合を考えてみます。 ユーザープリンシパルを非破壊更新する場合は、を呼び出し、 `context.ReplacePrincipal` `context.ShouldRenew` プロパティをに設定し `true` ます。

> [!WARNING]
> ここで説明する方法は、すべての要求でトリガーされます。 すべての要求に対して認証 cookie を検証すると、アプリのパフォーマンスが大幅に低下する可能性があります。

## <a name="persistent-cookies"></a>永続的な cookie

Cookie がブラウザーセッション間で保持されるようにすることもできます。 この永続化を有効にする必要があるのは、サインイン時に [忘れずに保存] チェックボックスをオンにして、明示的なユーザーの同意を有効にすることだけです。 

次のコードスニペットは、ブラウザーのクロージャを介して存続する id とそれに対応する cookie を作成します。 以前に構成したスライド式有効期限の設定はすべて受け入れられます。 ブラウザーを閉じている間に cookie の有効期限が切れると、ブラウザーは再起動後に cookie をクリアします。

<xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent>をに設定し `true` <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> ます。

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

## <a name="absolute-cookie-expiration"></a>クッキーの絶対有効期限

絶対有効期限は、で設定でき <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> ます。 永続的なクッキーを作成するには、 `IsPersistent` も設定する必要があります。 それ以外の場合、cookie はセッションベースの有効期間で作成され、保持する認証チケットの前または後に有効期限が切れる可能性があります。 を設定すると、 `ExpiresUtc` のオプションの値がオーバーライドされ <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions> ます (設定されている場合)。

次のコードスニペットでは、20分間継続する id とそれに対応する cookie を作成します。 これにより、以前に構成したスライド式有効期限の設定は無視されます。

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true,
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

::: moniker-end

## <a name="additional-resources"></a>その他の資料

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [ポリシーベースのロールチェック](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>
