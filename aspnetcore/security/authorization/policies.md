---
title: ASP.NETコアでのポリシーベースの承認
author: rick-anderson
description: ASP.NET Core アプリで承認要件を適用するための承認ポリシー ハンドラーを作成して使用する方法について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2020
uid: security/authorization/policies
ms.openlocfilehash: 66412a6020ea8f12427c8c5b466e1b2eedccf0f9
ms.sourcegitcommit: 77c046331f3d633d7cc247ba77e58b89e254f487
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2020
ms.locfileid: "81488762"
---
# <a name="policy-based-authorization-in-aspnet-core"></a>ASP.NETコアでのポリシーベースの承認

::: moniker range=">= aspnetcore-3.0"

この範囲の下で、[ロール ベースの承認](xref:security/authorization/roles)と[クレーム ベースの承認](xref:security/authorization/claims)では、要件、要件ハンドラー、および構成済みのポリシーが使用されます。 これらの構成要素は、コード内の承認評価の式をサポートします。 結果は、より豊富で再利用可能でテスト可能な承認構造です。

承認ポリシーは、1 つ以上の要件で構成されます。 このメソッドは、認証サービスの構成の一部として、次`Startup.ConfigureServices`のメソッドで登録されます。

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53, 58)]

前の例では、"AtLeast21" ポリシーが作成されます。 最小年齢の要件&mdash;は 1 つであり、要件のパラメーターとして指定されます。

## <a name="iauthorizationservice"></a>I認証サービス 

承認が成功したかどうかを判断するプライマリ サービス<xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>は、 です。

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

上記のコードでは、 [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs)の 2 つのメソッドを強調表示します。

<xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>は、メソッドのないマーカー サービスであり、承認が成功したかどうかを追跡するためのメカニズムです。

要件<xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler>が満たされているかどうかを確認する責任があります。
<!--The following code is a copy/paste from 
https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationHandler.cs -->

```csharp
/// <summary>
/// Classes implementing this interface are able to make a decision if authorization
/// is allowed.
/// </summary>
public interface IAuthorizationHandler
{
    /// <summary>
    /// Makes a decision if authorization is allowed.
    /// </summary>
    /// <param name="context">The authorization information.</param>
    Task HandleAsync(AuthorizationHandlerContext context);
}
```

クラス<xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext>は、要件が満たされているかどうかを示すためにハンドラーが使用するものです。

```csharp
 context.Succeed(requirement)
```

次のコードは、承認サービスの既定の実装の簡略化された (コメントで注釈が付けられた) を示しています。

```csharp
public async Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user, 
             object resource, IEnumerable<IAuthorizationRequirement> requirements)
{
    // Create a tracking context from the authorization inputs.
    var authContext = _contextFactory.CreateContext(requirements, user, resource);

    // By default this returns an IEnumerable<IAuthorizationHandlers> from DI.
    var handlers = await _handlers.GetHandlersAsync(authContext);

    // Invoke all handlers.
    foreach (var handler in handlers)
    {
        await handler.HandleAsync(authContext);
    }

    // Check the context, by default success is when all requirements have been met.
    return _evaluator.Evaluate(authContext);
}
```

次のコードは、典型的`ConfigureServices`なを示しています。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add all of your handlers to DI.
    services.AddSingleton<IAuthorizationHandler, MyHandler1>();
    // MyHandler2, ...

    services.AddSingleton<IAuthorizationHandler, MyHandlerN>();

    // Configure your policies
    services.AddAuthorization(options =>
          options.AddPolicy("Something",
          policy => policy.RequireClaim("Permission", "CanViewPage", "CanViewAnything")));


    services.AddControllersWithViews();
    services.AddRazorPages();
}
```

または<xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>`[Authorize(Policy = "Something")]`、承認に使用します。

## <a name="applying-policies-to-mvc-controllers"></a>MVC コントローラーへのポリシーの適用

Razor ページを使用している場合は、このドキュメントの[「Razor ページにポリシーを適用する](#applying-policies-to-razor-pages)」を参照してください。

ポリシーは、ポリシー名を持つ属性`[Authorize]`を使用して、コントローラに適用されます。 次に例を示します。

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a>Razor ページへのポリシーの適用

ポリシーは、ポリシー名を持つ属性`[Authorize]`を使用して Razor ページに適用されます。 次に例を示します。

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

ポリシーは、[承認規則](xref:security/authorization/razor-pages-authorization)を使用して Razor ページに適用することもできます。

## <a name="requirements"></a>必要条件

承認要件は、ポリシーが現在のユーザー プリンシパルを評価するために使用できるデータ パラメーターのコレクションです。 "AtLeast21" ポリシーでは、最小年齢を 1&mdash;つのパラメーターにする必要があります。 要件は、空のマーカー インターフェイスである[IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement)を実装します。 パラメーター化された最小年齢要件は、次のように実装できます。

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

承認ポリシーに複数の承認要件が含まれている場合、ポリシーの評価を成功させるには、すべての要件が満たされている必要があります。 つまり、1 つの許可ポリシーに追加された複数の許可要件は **、AND**ベースで処理されます。

> [!NOTE]
> 要件には、データやプロパティを含める必要はありません。

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a>承認ハンドラー

承認ハンドラーは、要件のプロパティの評価を担当します。 承認ハンドラーは、指定された[AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext)に対して要件を評価し、アクセスが許可されているかどうかを判断します。

要件には[複数のハンドラ](#security-authorization-policies-based-multiple-handlers)を含めることができます。 ハンドラは、処理する必要がある場合は`TRequirement`、[権限ハンドラ\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1)を継承できます。 または、ハンドラーは、複数の種類の要件を処理する[IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler)を実装する場合があります。

### <a name="use-a-handler-for-one-requirement"></a>1 つの要件に対してハンドラーを使用する

<a name="security-authorization-handler-example"></a>

最低年齢ハンドラーが 1 つの要件を使用する 1 対 1 の関係の例を次に示します。

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

上記のコードは、現在のユーザー プリンシパルに、既知の信頼された発行者によって発行された誕生日の請求があるかどうかを判断します。 要求が欠落している場合は承認を行うことはできません。 クレームが存在する場合、ユーザーの年齢が計算されます。 ユーザーが要件で定義された最短年齢を満たしている場合、承認は成功したと見なされます。 承認が成功すると、`context.Succeed`満たされた要件を単独のパラメーターとして呼び出します。

### <a name="use-a-handler-for-multiple-requirements"></a>複数の要件に対してハンドラーを使用する

次に、アクセス許可ハンドラーが 3 種類の要件を処理できる一対多リレーションシップの例を示します。

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

上記のコードは[、PendingRequirements を](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;走査します要件を含むプロパティが成功としてマークされていません。 要件の`ReadPermission`場合、要求されたリソースにアクセスするには、ユーザーが所有者であるかスポンサーである必要があります。 `EditPermission`または`DeletePermission`要件の場合、要求されたリソースにアクセスするには、所有者である必要があります。

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a>ハンドラー登録

ハンドラーは、構成時にサービス コレクションに登録されます。 次に例を示します。

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53-55, 58)]

上記のコードは、`MinimumAgeHandler`を呼び出`services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`すことによってシングルトンとして登録します。 組み込みの[サービスの有効期間](xref:fundamentals/dependency-injection#service-lifetimes)を使用してハンドラを登録できます。

## <a name="what-should-a-handler-return"></a>ハンドラは何を返す必要がありますか?

`Handle`[ハンドラの例](#security-authorization-handler-example)のメソッドは値を返さないことに注意してください。 成功または失敗の状態はどのように示されていますか?

* ハンドラーは、 を呼`context.Succeed(IAuthorizationRequirement requirement)`び出すことによって成功を示し、正常に検証された要件を渡します。

* 同じ要件の他のハンドラーが成功する可能性があるため、ハンドラーは一般的にエラーを処理する必要はありません。

* 他の要求ハンドラーが成功した場合でも、エラーを`context.Fail`保証するには、 を呼び出します。

ハンドラーが 呼`context.Succeed`び`context.Fail`出した場合、 または 他のすべてのハンドラーが呼び出されます。 これにより、別のハンドラーが正常に検証または失敗した場合でも発生するログ記録などの副作用を生成する要件が可能になります。 に`false`設定すると[、呼び出](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure)されたときに、呼び出し時にハンドラー`context.Fail`の実行が短絡するプロパティ (ASP.NET Core 1.1 以降で利用可能) が発生します。 `InvokeHandlersAfterFailure`は デフォルト`true`で、 すべてのハンドラが呼び出されます。

> [!NOTE]
> 認証に失敗した場合でも、承認ハンドラーが呼び出されます。

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a>要件に対して複数のハンドラが必要な理由

評価を**OR**ベースにする必要がある場合は、1 つの要件に対して複数のハンドラーを実装します。 たとえば、マイクロソフトにはキー カードだけで開くドアがあります。 鍵カードを家に置き忘れると、受付係は一時的なステッカーを印刷し、ドアを開けます。 このシナリオでは、1 つの要件*である BuildingEntry*を使用しますが、複数のハンドラーが 1 つの要件を調べています。

*BuildingEntryRequirement.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

*BadgeEntryHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

*TemporaryStickerHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

両方のハンドラが[登録](xref:security/authorization/policies#security-authorization-policies-based-handler-registration)されていることを確認します。 ポリシーが`BuildingEntryRequirement`を評価したときにいずれかのハンドラーが成功した場合、ポリシーの評価は成功します。

## <a name="using-a-func-to-fulfill-a-policy"></a>ポリシーを満たすために func を使用する

ポリシーを満たすことがコードで簡単に表現できる場合があります。 ポリシービルダーでポリシーを設定するときに`Func<AuthorizationHandlerContext, bool>`を提供することができます。 `RequireAssertion`

たとえば、前`BadgeEntryHandler`の例は次のように書き直されます。

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=42-43,47-53)]

## <a name="accessing-mvc-request-context-in-handlers"></a>ハンドラー内の MVC 要求コンテキストへのアクセス

承認`HandleRequirementAsync`ハンドラーに実装するメソッドには、2 つのパラメーターがあります`AuthorizationHandlerContext`。 `TRequirement` MVC や Jabbr などのフレームワークは、追加の情報を渡`Resource``AuthorizationHandlerContext`すために、プロパティに任意のオブジェクトを追加できます。

たとえば、MVC は、プロパティ内の[承認フィルター コンテキスト](/dotnet/api/?term=AuthorizationFilterContext)の`Resource`インスタンスを渡します。 このプロパティは、 `HttpContext`、`RouteData`および MVC および Razor ページによって提供されるその他のすべてにアクセスできます。

プロパティの`Resource`使用はフレームワーク固有です。 プロパティの情報を`Resource`使用すると、承認ポリシーが特定のフレームワークに制限されます。 キーワードを使用して`Resource`プロパティを`is`キャストし、キャストが成功したことを確認して、他のフレームワークで実行`InvalidCastException`したときにコードがクラッシュしないようにします。

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end


::: moniker range="< aspnetcore-3.0"

この範囲の下で、[ロール ベースの承認](xref:security/authorization/roles)と[クレーム ベースの承認](xref:security/authorization/claims)では、要件、要件ハンドラー、および構成済みのポリシーが使用されます。 これらの構成要素は、コード内の承認評価の式をサポートします。 結果は、より豊富で再利用可能でテスト可能な承認構造です。

承認ポリシーは、1 つ以上の要件で構成されます。 このメソッドは、認証サービスの構成の一部として、次`Startup.ConfigureServices`のメソッドで登録されます。

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,66)]

前の例では、"AtLeast21" ポリシーが作成されます。 最小年齢の要件&mdash;は 1 つであり、要件のパラメーターとして指定されます。

## <a name="iauthorizationservice"></a>I認証サービス 

承認が成功したかどうかを判断するプライマリ サービス<xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>は、 です。

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

上記のコードでは、 [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs)の 2 つのメソッドを強調表示します。

<xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>は、メソッドのないマーカー サービスであり、承認が成功したかどうかを追跡するためのメカニズムです。

要件<xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler>が満たされているかどうかを確認する責任があります。
<!--The following code is a copy/paste from 
https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationHandler.cs -->

```csharp
/// <summary>
/// Classes implementing this interface are able to make a decision if authorization
/// is allowed.
/// </summary>
public interface IAuthorizationHandler
{
    /// <summary>
    /// Makes a decision if authorization is allowed.
    /// </summary>
    /// <param name="context">The authorization information.</param>
    Task HandleAsync(AuthorizationHandlerContext context);
}
```

クラス<xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext>は、要件が満たされているかどうかを示すためにハンドラーが使用するものです。

```csharp
 context.Succeed(requirement)
```

次のコードは、承認サービスの既定の実装の簡略化された (コメントで注釈が付けられた) を示しています。

```csharp
public async Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user, 
             object resource, IEnumerable<IAuthorizationRequirement> requirements)
{
    // Create a tracking context from the authorization inputs.
    var authContext = _contextFactory.CreateContext(requirements, user, resource);

    // By default this returns an IEnumerable<IAuthorizationHandlers> from DI.
    var handlers = await _handlers.GetHandlersAsync(authContext);

    // Invoke all handlers.
    foreach (var handler in handlers)
    {
        await handler.HandleAsync(authContext);
    }

    // Check the context, by default success is when all requirements have been met.
    return _evaluator.Evaluate(authContext);
}
```

次のコードは、典型的`ConfigureServices`なを示しています。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add all of your handlers to DI.
    services.AddSingleton<IAuthorizationHandler, MyHandler1>();
    // MyHandler2, ...

    services.AddSingleton<IAuthorizationHandler, MyHandlerN>();

    // Configure your policies
    services.AddAuthorization(options =>
          options.AddPolicy("Something",
          policy => policy.RequireClaim("Permission", "CanViewPage", "CanViewAnything")));


    services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
}
```

または<xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>`[Authorize(Policy = "Something")]`、承認に使用します。

## <a name="applying-policies-to-mvc-controllers"></a>MVC コントローラーへのポリシーの適用

Razor ページを使用している場合は、このドキュメントの[「Razor ページにポリシーを適用する](#applying-policies-to-razor-pages)」を参照してください。

ポリシーは、ポリシー名を持つ属性`[Authorize]`を使用して、コントローラに適用されます。 次に例を示します。

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a>Razor ページへのポリシーの適用

ポリシーは、ポリシー名を持つ属性`[Authorize]`を使用して Razor ページに適用されます。 次に例を示します。

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

ポリシーは、[承認規則](xref:security/authorization/razor-pages-authorization)を使用して Razor ページに適用することもできます。

## <a name="requirements"></a>必要条件

承認要件は、ポリシーが現在のユーザー プリンシパルを評価するために使用できるデータ パラメーターのコレクションです。 "AtLeast21" ポリシーでは、最小年齢を 1&mdash;つのパラメーターにする必要があります。 要件は、空のマーカー インターフェイスである[IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement)を実装します。 パラメーター化された最小年齢要件は、次のように実装できます。

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

承認ポリシーに複数の承認要件が含まれている場合、ポリシーの評価を成功させるには、すべての要件が満たされている必要があります。 つまり、1 つの許可ポリシーに追加された複数の許可要件は **、AND**ベースで処理されます。

> [!NOTE]
> 要件には、データやプロパティを含める必要はありません。

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a>承認ハンドラー

承認ハンドラーは、要件のプロパティの評価を担当します。 承認ハンドラーは、指定された[AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext)に対して要件を評価し、アクセスが許可されているかどうかを判断します。

要件には[複数のハンドラ](#security-authorization-policies-based-multiple-handlers)を含めることができます。 ハンドラは、処理する必要がある場合は`TRequirement`、[権限ハンドラ\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1)を継承できます。 または、ハンドラーは、複数の種類の要件を処理する[IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler)を実装する場合があります。

### <a name="use-a-handler-for-one-requirement"></a>1 つの要件に対してハンドラーを使用する

<a name="security-authorization-handler-example"></a>

最低年齢ハンドラーが 1 つの要件を使用する 1 対 1 の関係の例を次に示します。

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

上記のコードは、現在のユーザー プリンシパルに、既知の信頼された発行者によって発行された誕生日の請求があるかどうかを判断します。 要求が欠落している場合は承認を行うことはできません。 クレームが存在する場合、ユーザーの年齢が計算されます。 ユーザーが要件で定義された最短年齢を満たしている場合、承認は成功したと見なされます。 承認が成功すると、`context.Succeed`満たされた要件を単独のパラメーターとして呼び出します。

### <a name="use-a-handler-for-multiple-requirements"></a>複数の要件に対してハンドラーを使用する

次に、アクセス許可ハンドラーが 3 種類の要件を処理できる一対多リレーションシップの例を示します。

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

上記のコードは[、PendingRequirements を](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;走査します要件を含むプロパティが成功としてマークされていません。 要件の`ReadPermission`場合、要求されたリソースにアクセスするには、ユーザーが所有者であるかスポンサーである必要があります。 `EditPermission`または`DeletePermission`要件の場合、要求されたリソースにアクセスするには、所有者である必要があります。

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a>ハンドラー登録

ハンドラーは、構成時にサービス コレクションに登録されます。 次に例を示します。

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,62-63,66)]

上記のコードは、`MinimumAgeHandler`を呼び出`services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`すことによってシングルトンとして登録します。 組み込みの[サービスの有効期間](xref:fundamentals/dependency-injection#service-lifetimes)を使用してハンドラを登録できます。

## <a name="what-should-a-handler-return"></a>ハンドラは何を返す必要がありますか?

`Handle`[ハンドラの例](#security-authorization-handler-example)のメソッドは値を返さないことに注意してください。 成功または失敗の状態はどのように示されていますか?

* ハンドラーは、 を呼`context.Succeed(IAuthorizationRequirement requirement)`び出すことによって成功を示し、正常に検証された要件を渡します。

* 同じ要件の他のハンドラーが成功する可能性があるため、ハンドラーは一般的にエラーを処理する必要はありません。

* 他の要求ハンドラーが成功した場合でも、エラーを`context.Fail`保証するには、 を呼び出します。

ハンドラーが 呼`context.Succeed`び`context.Fail`出した場合、 または 他のすべてのハンドラーが呼び出されます。 これにより、別のハンドラーが正常に検証または失敗した場合でも発生するログ記録などの副作用を生成する要件が可能になります。 に`false`設定すると[、呼び出](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure)されたときに、呼び出し時にハンドラー`context.Fail`の実行が短絡するプロパティ (ASP.NET Core 1.1 以降で利用可能) が発生します。 `InvokeHandlersAfterFailure`は デフォルト`true`で、 すべてのハンドラが呼び出されます。

> [!NOTE]
> 認証に失敗した場合でも、承認ハンドラーが呼び出されます。

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a>要件に対して複数のハンドラが必要な理由

評価を**OR**ベースにする必要がある場合は、1 つの要件に対して複数のハンドラーを実装します。 たとえば、マイクロソフトにはキー カードだけで開くドアがあります。 鍵カードを家に置き忘れると、受付係は一時的なステッカーを印刷し、ドアを開けます。 このシナリオでは、1 つの要件*である BuildingEntry*を使用しますが、複数のハンドラーが 1 つの要件を調べています。

*BuildingEntryRequirement.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

*BadgeEntryHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

*TemporaryStickerHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

両方のハンドラが[登録](xref:security/authorization/policies#security-authorization-policies-based-handler-registration)されていることを確認します。 ポリシーが`BuildingEntryRequirement`を評価したときにいずれかのハンドラーが成功した場合、ポリシーの評価は成功します。

## <a name="using-a-func-to-fulfill-a-policy"></a>ポリシーを満たすために func を使用する

ポリシーを満たすことがコードで簡単に表現できる場合があります。 ポリシービルダーでポリシーを設定するときに`Func<AuthorizationHandlerContext, bool>`を提供することができます。 `RequireAssertion`

たとえば、前`BadgeEntryHandler`の例は次のように書き直されます。

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=50-51,55-61)]

## <a name="accessing-mvc-request-context-in-handlers"></a>ハンドラー内の MVC 要求コンテキストへのアクセス

承認`HandleRequirementAsync`ハンドラーに実装するメソッドには、2 つのパラメーターがあります`AuthorizationHandlerContext`。 `TRequirement` MVC や SignalR などのフレームワークは、追加の情報を渡`Resource``AuthorizationHandlerContext`すために、プロパティに任意のオブジェクトを追加する自由です。

エンドポイント ルーティングを使用する場合、承認は通常、認証ミドルウェアによって処理されます。 この場合、`Resource`プロパティは<xref:Microsoft.AspNetCore.Http.Endpoint>のインスタンスです。 エンドポイントを使用して、ルーティング先のリソースをプローブできます。 次に例を示します。

```csharp
if (context.Resource is Endpoint endpoint)
{
   var actionDescriptor = endpoint.Metadata.GetMetadata<ControllerActionDescriptor>();
   ...
}
```

従来のルーティング、または承認が MVC の承認フィルターの一部として発生した場合`Resource`、の<xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext>値はインスタンスです。 このプロパティは、 `HttpContext`、`RouteData`および MVC および Razor ページによって提供されるその他のすべてにアクセスできます。

プロパティの`Resource`使用はフレームワーク固有です。 プロパティの情報を`Resource`使用すると、承認ポリシーが特定のフレームワークに制限されます。 キーワードを使用して`Resource`プロパティを`is`キャストし、キャストが成功したことを確認して、他のフレームワークで実行`InvalidCastException`したときにコードがクラッシュしないようにします。

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end
