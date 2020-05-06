---
title: ASP.NET Core での認証と承認SignalR
author: bradygaster
description: ASP.NET Core SignalRで認証と承認を使用する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/authn-and-authz
ms.openlocfilehash: 0f0bb2040d2407817c91f64a4769e6601c37a07d
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775285"
---
# <a name="authentication-and-authorization-in-aspnet-core-signalr"></a>ASP.NET Core での認証と承認SignalR

By [Andrew Stanton-看護師](https://twitter.com/anurse)

[サンプル コードを表示またはダウンロードする](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/authn-and-authz/sample/) ([ダウンロード方法](xref:index#how-to-download-a-sample))

## <a name="authenticate-users-connecting-to-a-signalr-hub"></a>SignalRハブに接続しているユーザーを認証する

SignalR[ASP.NET Core 認証](xref:security/authentication/identity)と共に使用して、ユーザーを各接続に関連付けることができます。 ハブでは、 [HubConnectionContext](/dotnet/api/microsoft.aspnetcore.signalr.hubconnectioncontext.user)プロパティから認証データにアクセスできます。 認証を使用すると、ユーザーに関連付けられているすべての接続で、ハブがメソッドを呼び出すことができます。 詳細については、「 [」のSignalR「ユーザーとグループの管理](xref:signalr/groups)」を参照してください。 複数の接続を1人のユーザーに関連付けることができます。

認証を使用して ASP.NET Core `Startup.Configure`認証をSignalR使用する例を次に示します。

::: moniker range=">= aspnetcore-3.0"

```csharp
public void Configure(IApplicationBuilder app)
{
    ...

    app.UseStaticFiles();

    app.UseRouting();

    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chat");
        endpoints.MapControllerRoute("default", "{controller=Home}/{action=Index}/{id?}");
    });
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

```csharp
public void Configure(IApplicationBuilder app)
{
    ...

    app.UseStaticFiles();

    app.UseAuthentication();

    app.UseSignalR(hubs =>
    {
        hubs.MapHub<ChatHub>("/chat");
    });

    app.UseMvc(routes =>
    {
        routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
    });
}
```

> [!NOTE]
> SignalRおよび ASP.NET Core 認証ミドルウェアを登録する順序は重要です。 がの`UseAuthentication`ユーザー `UseSignalR`を持つSignalRようにするには`HttpContext`、常にを呼び出します。

::: moniker-end

### <a name="cookie-authentication"></a>Cookie 認証

ブラウザーベースのアプリでは、cookie 認証によって、既存のユーザーの資格SignalR情報を自動的に接続に渡すことができます。 ブラウザークライアントを使用する場合、追加の構成は必要ありません。 ユーザーがアプリにログインしている場合、接続SignalRは自動的にこの認証を継承します。

Cookie はアクセストークンを送信するためのブラウザー固有の方法ですが、ブラウザー以外のクライアントは送信できます。 [.Net クライアント](xref:signalr/dotnet-client)を使用する場合、 `Cookies`プロパティを`.WithUrl`呼び出しでクッキーを指定するように構成できます。 ただし、.NET クライアントから cookie 認証を使用するには、アプリが cookie の認証データを交換するための API を提供する必要があります。

### <a name="bearer-token-authentication"></a>ベアラートークン認証

クライアントは、cookie を使用する代わりにアクセストークンを提供できます。 サーバーはトークンを検証し、それを使用してユーザーを識別します。 この検証は、接続が確立された場合にのみ実行されます。 接続の有効期間中は、トークンの失効を確認するためにサーバーが自動的に再検証されることはありません。

サーバーでは、ベアラートークン認証は[JWT ベアラーミドルウェア](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer)を使用して構成されます。

JavaScript クライアントでは、 [accessTokenFactory](xref:signalr/configuration#configure-bearer-authentication)オプションを使用してトークンを指定できます。

[!code-typescript[Configure Access Token](authn-and-authz/sample/wwwroot/js/chat.ts?range=52-55)]

.NET クライアントには、トークンの構成に使用できる同様の[れる場合](xref:signalr/configuration#configure-bearer-authentication)プロパティがあります。

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options =>
    { 
        options.AccessTokenProvider = () => Task.FromResult(_myAccessToken);
    })
    .Build();
```

> [!NOTE]
> 提供するアクセストークン関数は、によっ**every**てSignalR行われるすべての HTTP 要求の前に呼び出されます。 接続をアクティブな状態に保つために、トークンを更新する必要がある場合 (接続中に期限切れになる可能性があるため)、この関数内からトークンを取得し、更新されたトークンを返します。

標準の web Api では、ベアラートークンは HTTP ヘッダーで送信されます。 ただし、 SignalRでは、一部のトランスポートを使用するときに、これらのヘッダーをブラウザーで設定することはできません。 Websocket およびサーバー送信イベントを使用する場合、トークンはクエリ文字列パラメーターとして送信されます。 サーバーでこれをサポートするには、追加の構成が必要です。

[!code-csharp[Configure Server to accept access token from Query String](authn-and-authz/sample/Startup.cs?name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

> [!NOTE]
> クエリ文字列は、ブラウザー API の制限により、Websocket およびサーバーから送信されたイベントと接続するときにブラウザーで使用されます。 HTTPS を使用する場合、クエリ文字列の値は TLS 接続によって保護されます。 ただし、多くのサーバーはクエリ文字列の値をログに記録します。 詳細については、「 [ASP.NET Core SignalRのセキュリティに関する考慮事項](xref:signalr/security)」を参照してください。 SignalRはヘッダーを使用して、トークンをサポートする環境 (.NET や Java クライアントなど) でトークンを送信します。

### <a name="cookies-vs-bearer-tokens"></a>Cookie とベアラートークン 

Cookie は、ブラウザーに固有のものです。 他の種類のクライアントから送信すると、ベアラートークンの送信と比較して複雑さが増します。 そのため、アプリがブラウザークライアントからのユーザーの認証のみを必要とする場合を除き、cookie 認証は推奨されません。 ベアラートークン認証は、ブラウザークライアント以外のクライアントを使用する場合に推奨される方法です。

### <a name="windows-authentication"></a>[Windows 認証]

アプリで[Windows 認証](xref:security/authentication/windowsauth)が構成されてSignalRいる場合、はその id を使用してハブをセキュリティで保護することができます。 ただし、個々のユーザーにメッセージを送信するには、カスタムユーザー ID プロバイダーを追加する必要があります。 Windows 認証システムは、"名前識別子" 要求を提供しません。 SignalRは、要求を使用してユーザー名を決定します。

を実装`IUserIdProvider`し、識別子として使用する要求の1つをユーザーから取得する新しいクラスを追加します。 たとえば、"Name" 要求 (フォーム`[Domain]\[Username]`の Windows ユーザー名) を使用するには、次のクラスを作成します。

[!code-csharp[Name based provider](authn-and-authz/sample/nameuseridprovider.cs?name=NameUserIdProvider)]

`ClaimTypes.Name`ではなく、の`User`任意の値 (Windows SID 識別子など) を使用できます。

> [!NOTE]
> 選択する値は、システム内のすべてのユーザーの間で一意である必要があります。 そうしないと、1人のユーザーを対象としたメッセージが別のユーザーに送信される可能性があります。

`Startup.ConfigureServices`メソッドにこのコンポーネントを登録します。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ... other services ...

    services.AddSignalR();
    services.AddSingleton<IUserIdProvider, NameUserIdProvider>();
}
```

.NET クライアントでは、 [UseDefaultCredentials](/dotnet/api/microsoft.aspnetcore.http.connections.client.httpconnectionoptions.usedefaultcredentials)プロパティを設定することによって Windows 認証を有効にする必要があります。

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options =>
    {
        options.UseDefaultCredentials = true;
    })
    .Build();
```

Windows 認証は、Microsoft Internet Explorer または Microsoft Edge を使用している場合にのみブラウザークライアントでサポートされます。

### <a name="use-claims-to-customize-identity-handling"></a>要求を使用して id 処理をカスタマイズする

ユーザーを認証するアプリは、 SignalRユーザー要求からユーザー id を派生させることができます。 がユーザー Id SignalRを作成する方法を`IUserIdProvider`指定するには、実装を実装して登録します。

このサンプルコードでは、要求を使用して、識別プロパティとしてユーザーの電子メールアドレスを選択する方法を示します。 

> [!NOTE]
> 選択する値は、システム内のすべてのユーザーの間で一意である必要があります。 そうしないと、1人のユーザーを対象としたメッセージが別のユーザーに送信される可能性があります。

[!code-csharp[Email provider](authn-and-authz/sample/EmailBasedUserIdProvider.cs?name=EmailBasedUserIdProvider)]

アカウント登録によって、種類`ClaimsTypes.Email`がの要求が ASP.NET identity データベースに追加されます。

[!code-csharp[Adding the email to the ASP.NET identity claims](authn-and-authz/sample/pages/account/Register.cshtml.cs?name=AddEmailClaim)]

このコンポーネントをに登録`Startup.ConfigureServices`します。

```csharp
services.AddSingleton<IUserIdProvider, EmailBasedUserIdProvider>();
```

## <a name="authorize-users-to-access-hubs-and-hub-methods"></a>ハブおよびハブのメソッドへのアクセスをユーザーに承認する

既定では、ハブ内のすべてのメソッドを認証されていないユーザーが呼び出すことができます。 認証を要求するには、[承認](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute)属性をハブに適用します。

[!code-csharp[Restrict a hub to only authorized users](authn-and-authz/sample/Hubs/ChatHub.cs?range=8-10,32)]

`[Authorize]`属性のコンストラクター引数とプロパティを使用して、特定の[承認ポリシー](xref:security/authorization/policies)に一致するユーザーのみにアクセスを制限できます。 たとえば、という`MyAuthorizationPolicy`カスタム承認ポリシーがある場合は、次のコードを使用して、そのポリシーに一致するユーザーだけがハブにアクセスできるようにすることができます。

```csharp
[Authorize("MyAuthorizationPolicy")]
public class ChatHub : Hub
{
}
```

個々のハブメソッドに`[Authorize]`も属性を適用できます。 現在のユーザーがメソッドに適用されているポリシーと一致しない場合は、呼び出し元にエラーが返されます。

```csharp
[Authorize]
public class ChatHub : Hub
{
    public async Task Send(string message)
    {
        // ... send a message to all users ...
    }

    [Authorize("Administrators")]
    public void BanUser(string userName)
    {
        // ... ban a user from the chat room (something only Administrators can do) ...
    }
}
```

::: moniker range=">= aspnetcore-3.0"

### <a name="use-authorization-handlers-to-customize-hub-method-authorization"></a>承認ハンドラーを使用してハブメソッドの承認をカスタマイズする

SignalRハブメソッドが承認を必要とする場合に、カスタムリソースを承認ハンドラーに提供します。 リソースは `HubInvocationContext` のインスタンスです。 に`HubInvocationContext`は、 `HubCallerContext`、呼び出されるハブメソッドの名前、およびハブメソッドへの引数が含まれます。

Azure Active Directory による複数の組織でのサインインを可能にするチャットルームの例を考えてみましょう。 Microsoft アカウントを持つユーザーはだれでもチャットにサインインできますが、所有している組織のメンバーだけがユーザーの許可を禁止したり、ユーザーのチャット履歴を表示したりできるようにする必要があります。 さらに、特定のユーザーの特定の機能を制限することもできます。 ASP.NET Core 3.0 の更新された機能を使用すると、これは完全に可能です。 が`DomainRestrictedRequirement`カスタム`IAuthorizationRequirement`として機能することに注意してください。 `HubInvocationContext`リソースパラメーターが渡されたので、内部ロジックはハブが呼び出されているコンテキストを検査し、ユーザーが個々のハブメソッドを実行できるようにすることを決定します。

```csharp
[Authorize]
public class ChatHub : Hub
{
    public void SendMessage(string message)
    {
    }

    [Authorize("DomainRestricted")]
    public void BanUser(string username)
    {
    }

    [Authorize("DomainRestricted")]
    public void ViewUserHistory(string username)
    {
    }
}

public class DomainRestrictedRequirement : 
    AuthorizationHandler<DomainRestrictedRequirement, HubInvocationContext>, 
    IAuthorizationRequirement
{
    protected override Task HandleRequirementAsync(AuthorizationHandlerContext context,
        DomainRestrictedRequirement requirement, 
        HubInvocationContext resource)
    {
        if (IsUserAllowedToDoThis(resource.HubMethodName, context.User.Identity.Name) && 
            context.User.Identity.Name.EndsWith("@microsoft.com"))
        {
            context.Succeed(requirement);
        }
        return Task.CompletedTask;
    }

    private bool IsUserAllowedToDoThis(string hubMethodName,
        string currentUsername)
    {
        return !(currentUsername.Equals("asdf42@microsoft.com") && 
            hubMethodName.Equals("banUser", StringComparison.OrdinalIgnoreCase));
    }
}
```

で`Startup.ConfigureServices`、新しいポリシーを追加し、 `DomainRestrictedRequirement` `DomainRestricted`ポリシーを作成するためのパラメーターとしてカスタム要件を指定します。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ... other services ...

    services
        .AddAuthorization(options =>
        {
            options.AddPolicy("DomainRestricted", policy =>
            {
                policy.Requirements.Add(new DomainRestrictedRequirement());
            });
        });
}
```

前の例では、 `DomainRestrictedRequirement`クラスは`IAuthorizationRequirement`との両方で、 `AuthorizationHandler`その要件に対応しています。 この2つのコンポーネントを別々のクラスに分割して、懸念事項を分けることができます。 この例のアプローチの利点は、要件とハンドラーが同じである`AuthorizationHandler`ため、起動時にを挿入する必要がないことです。

::: moniker-end

## <a name="additional-resources"></a>その他のリソース

* [ASP.NET Core でのベアラートークン認証](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [リソースベースの承認](xref:security/authorization/resourcebased)
