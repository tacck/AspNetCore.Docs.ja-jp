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
# <a name="policy-based-authorization-in-aspnet-core"></a><span data-ttu-id="60975-103">ASP.NETコアでのポリシーベースの承認</span><span class="sxs-lookup"><span data-stu-id="60975-103">Policy-based authorization in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="60975-104">この範囲の下で、[ロール ベースの承認](xref:security/authorization/roles)と[クレーム ベースの承認](xref:security/authorization/claims)では、要件、要件ハンドラー、および構成済みのポリシーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="60975-104">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="60975-105">これらの構成要素は、コード内の承認評価の式をサポートします。</span><span class="sxs-lookup"><span data-stu-id="60975-105">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="60975-106">結果は、より豊富で再利用可能でテスト可能な承認構造です。</span><span class="sxs-lookup"><span data-stu-id="60975-106">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="60975-107">承認ポリシーは、1 つ以上の要件で構成されます。</span><span class="sxs-lookup"><span data-stu-id="60975-107">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="60975-108">このメソッドは、認証サービスの構成の一部として、次`Startup.ConfigureServices`のメソッドで登録されます。</span><span class="sxs-lookup"><span data-stu-id="60975-108">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53, 58)]

<span data-ttu-id="60975-109">前の例では、"AtLeast21" ポリシーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="60975-109">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="60975-110">最小年齢の要件&mdash;は 1 つであり、要件のパラメーターとして指定されます。</span><span class="sxs-lookup"><span data-stu-id="60975-110">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="60975-111">I認証サービス</span><span class="sxs-lookup"><span data-stu-id="60975-111">IAuthorizationService</span></span> 

<span data-ttu-id="60975-112">承認が成功したかどうかを判断するプライマリ サービス<xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>は、 です。</span><span class="sxs-lookup"><span data-stu-id="60975-112">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

<span data-ttu-id="60975-113">上記のコードでは、 [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs)の 2 つのメソッドを強調表示します。</span><span class="sxs-lookup"><span data-stu-id="60975-113">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="60975-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>は、メソッドのないマーカー サービスであり、承認が成功したかどうかを追跡するためのメカニズムです。</span><span class="sxs-lookup"><span data-stu-id="60975-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="60975-115">要件<xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler>が満たされているかどうかを確認する責任があります。</span><span class="sxs-lookup"><span data-stu-id="60975-115">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
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

<span data-ttu-id="60975-116">クラス<xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext>は、要件が満たされているかどうかを示すためにハンドラーが使用するものです。</span><span class="sxs-lookup"><span data-stu-id="60975-116">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="60975-117">次のコードは、承認サービスの既定の実装の簡略化された (コメントで注釈が付けられた) を示しています。</span><span class="sxs-lookup"><span data-stu-id="60975-117">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

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

<span data-ttu-id="60975-118">次のコードは、典型的`ConfigureServices`なを示しています。</span><span class="sxs-lookup"><span data-stu-id="60975-118">The following code shows a typical `ConfigureServices`:</span></span>

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

<span data-ttu-id="60975-119">または<xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>`[Authorize(Policy = "Something")]`、承認に使用します。</span><span class="sxs-lookup"><span data-stu-id="60975-119">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="applying-policies-to-mvc-controllers"></a><span data-ttu-id="60975-120">MVC コントローラーへのポリシーの適用</span><span class="sxs-lookup"><span data-stu-id="60975-120">Applying policies to MVC controllers</span></span>

<span data-ttu-id="60975-121">Razor ページを使用している場合は、このドキュメントの[「Razor ページにポリシーを適用する](#applying-policies-to-razor-pages)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="60975-121">If you're using Razor Pages, see [Applying policies to Razor Pages](#applying-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="60975-122">ポリシーは、ポリシー名を持つ属性`[Authorize]`を使用して、コントローラに適用されます。</span><span class="sxs-lookup"><span data-stu-id="60975-122">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="60975-123">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="60975-123">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a><span data-ttu-id="60975-124">Razor ページへのポリシーの適用</span><span class="sxs-lookup"><span data-stu-id="60975-124">Applying policies to Razor Pages</span></span>

<span data-ttu-id="60975-125">ポリシーは、ポリシー名を持つ属性`[Authorize]`を使用して Razor ページに適用されます。</span><span class="sxs-lookup"><span data-stu-id="60975-125">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="60975-126">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="60975-126">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="60975-127">ポリシーは、[承認規則](xref:security/authorization/razor-pages-authorization)を使用して Razor ページに適用することもできます。</span><span class="sxs-lookup"><span data-stu-id="60975-127">Policies can also be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="60975-128">必要条件</span><span class="sxs-lookup"><span data-stu-id="60975-128">Requirements</span></span>

<span data-ttu-id="60975-129">承認要件は、ポリシーが現在のユーザー プリンシパルを評価するために使用できるデータ パラメーターのコレクションです。</span><span class="sxs-lookup"><span data-stu-id="60975-129">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="60975-130">"AtLeast21" ポリシーでは、最小年齢を 1&mdash;つのパラメーターにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="60975-130">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="60975-131">要件は、空のマーカー インターフェイスである[IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement)を実装します。</span><span class="sxs-lookup"><span data-stu-id="60975-131">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="60975-132">パラメーター化された最小年齢要件は、次のように実装できます。</span><span class="sxs-lookup"><span data-stu-id="60975-132">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="60975-133">承認ポリシーに複数の承認要件が含まれている場合、ポリシーの評価を成功させるには、すべての要件が満たされている必要があります。</span><span class="sxs-lookup"><span data-stu-id="60975-133">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="60975-134">つまり、1 つの許可ポリシーに追加された複数の許可要件は **、AND**ベースで処理されます。</span><span class="sxs-lookup"><span data-stu-id="60975-134">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="60975-135">要件には、データやプロパティを含める必要はありません。</span><span class="sxs-lookup"><span data-stu-id="60975-135">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="60975-136">承認ハンドラー</span><span class="sxs-lookup"><span data-stu-id="60975-136">Authorization handlers</span></span>

<span data-ttu-id="60975-137">承認ハンドラーは、要件のプロパティの評価を担当します。</span><span class="sxs-lookup"><span data-stu-id="60975-137">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="60975-138">承認ハンドラーは、指定された[AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext)に対して要件を評価し、アクセスが許可されているかどうかを判断します。</span><span class="sxs-lookup"><span data-stu-id="60975-138">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="60975-139">要件には[複数のハンドラ](#security-authorization-policies-based-multiple-handlers)を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="60975-139">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="60975-140">ハンドラは、処理する必要がある場合は`TRequirement`、[権限ハンドラ\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1)を継承できます。</span><span class="sxs-lookup"><span data-stu-id="60975-140">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="60975-141">または、ハンドラーは、複数の種類の要件を処理する[IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler)を実装する場合があります。</span><span class="sxs-lookup"><span data-stu-id="60975-141">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="60975-142">1 つの要件に対してハンドラーを使用する</span><span class="sxs-lookup"><span data-stu-id="60975-142">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="60975-143">最低年齢ハンドラーが 1 つの要件を使用する 1 対 1 の関係の例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="60975-143">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="60975-144">上記のコードは、現在のユーザー プリンシパルに、既知の信頼された発行者によって発行された誕生日の請求があるかどうかを判断します。</span><span class="sxs-lookup"><span data-stu-id="60975-144">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="60975-145">要求が欠落している場合は承認を行うことはできません。</span><span class="sxs-lookup"><span data-stu-id="60975-145">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="60975-146">クレームが存在する場合、ユーザーの年齢が計算されます。</span><span class="sxs-lookup"><span data-stu-id="60975-146">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="60975-147">ユーザーが要件で定義された最短年齢を満たしている場合、承認は成功したと見なされます。</span><span class="sxs-lookup"><span data-stu-id="60975-147">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="60975-148">承認が成功すると、`context.Succeed`満たされた要件を単独のパラメーターとして呼び出します。</span><span class="sxs-lookup"><span data-stu-id="60975-148">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="60975-149">複数の要件に対してハンドラーを使用する</span><span class="sxs-lookup"><span data-stu-id="60975-149">Use a handler for multiple requirements</span></span>

<span data-ttu-id="60975-150">次に、アクセス許可ハンドラーが 3 種類の要件を処理できる一対多リレーションシップの例を示します。</span><span class="sxs-lookup"><span data-stu-id="60975-150">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="60975-151">上記のコードは[、PendingRequirements を](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;走査します要件を含むプロパティが成功としてマークされていません。</span><span class="sxs-lookup"><span data-stu-id="60975-151">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="60975-152">要件の`ReadPermission`場合、要求されたリソースにアクセスするには、ユーザーが所有者であるかスポンサーである必要があります。</span><span class="sxs-lookup"><span data-stu-id="60975-152">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="60975-153">`EditPermission`または`DeletePermission`要件の場合、要求されたリソースにアクセスするには、所有者である必要があります。</span><span class="sxs-lookup"><span data-stu-id="60975-153">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="60975-154">ハンドラー登録</span><span class="sxs-lookup"><span data-stu-id="60975-154">Handler registration</span></span>

<span data-ttu-id="60975-155">ハンドラーは、構成時にサービス コレクションに登録されます。</span><span class="sxs-lookup"><span data-stu-id="60975-155">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="60975-156">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="60975-156">For example:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53-55, 58)]

<span data-ttu-id="60975-157">上記のコードは、`MinimumAgeHandler`を呼び出`services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`すことによってシングルトンとして登録します。</span><span class="sxs-lookup"><span data-stu-id="60975-157">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="60975-158">組み込みの[サービスの有効期間](xref:fundamentals/dependency-injection#service-lifetimes)を使用してハンドラを登録できます。</span><span class="sxs-lookup"><span data-stu-id="60975-158">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="60975-159">ハンドラは何を返す必要がありますか?</span><span class="sxs-lookup"><span data-stu-id="60975-159">What should a handler return?</span></span>

<span data-ttu-id="60975-160">`Handle`[ハンドラの例](#security-authorization-handler-example)のメソッドは値を返さないことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="60975-160">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="60975-161">成功または失敗の状態はどのように示されていますか?</span><span class="sxs-lookup"><span data-stu-id="60975-161">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="60975-162">ハンドラーは、 を呼`context.Succeed(IAuthorizationRequirement requirement)`び出すことによって成功を示し、正常に検証された要件を渡します。</span><span class="sxs-lookup"><span data-stu-id="60975-162">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="60975-163">同じ要件の他のハンドラーが成功する可能性があるため、ハンドラーは一般的にエラーを処理する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="60975-163">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="60975-164">他の要求ハンドラーが成功した場合でも、エラーを`context.Fail`保証するには、 を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="60975-164">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="60975-165">ハンドラーが 呼`context.Succeed`び`context.Fail`出した場合、 または 他のすべてのハンドラーが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="60975-165">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="60975-166">これにより、別のハンドラーが正常に検証または失敗した場合でも発生するログ記録などの副作用を生成する要件が可能になります。</span><span class="sxs-lookup"><span data-stu-id="60975-166">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="60975-167">に`false`設定すると[、呼び出](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure)されたときに、呼び出し時にハンドラー`context.Fail`の実行が短絡するプロパティ (ASP.NET Core 1.1 以降で利用可能) が発生します。</span><span class="sxs-lookup"><span data-stu-id="60975-167">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="60975-168">`InvokeHandlersAfterFailure`は デフォルト`true`で、 すべてのハンドラが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="60975-168">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="60975-169">認証に失敗した場合でも、承認ハンドラーが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="60975-169">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="60975-170">要件に対して複数のハンドラが必要な理由</span><span class="sxs-lookup"><span data-stu-id="60975-170">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="60975-171">評価を**OR**ベースにする必要がある場合は、1 つの要件に対して複数のハンドラーを実装します。</span><span class="sxs-lookup"><span data-stu-id="60975-171">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="60975-172">たとえば、マイクロソフトにはキー カードだけで開くドアがあります。</span><span class="sxs-lookup"><span data-stu-id="60975-172">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="60975-173">鍵カードを家に置き忘れると、受付係は一時的なステッカーを印刷し、ドアを開けます。</span><span class="sxs-lookup"><span data-stu-id="60975-173">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="60975-174">このシナリオでは、1 つの要件*である BuildingEntry*を使用しますが、複数のハンドラーが 1 つの要件を調べています。</span><span class="sxs-lookup"><span data-stu-id="60975-174">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="60975-175">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="60975-175">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="60975-176">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="60975-176">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="60975-177">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="60975-177">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="60975-178">両方のハンドラが[登録](xref:security/authorization/policies#security-authorization-policies-based-handler-registration)されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="60975-178">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="60975-179">ポリシーが`BuildingEntryRequirement`を評価したときにいずれかのハンドラーが成功した場合、ポリシーの評価は成功します。</span><span class="sxs-lookup"><span data-stu-id="60975-179">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="using-a-func-to-fulfill-a-policy"></a><span data-ttu-id="60975-180">ポリシーを満たすために func を使用する</span><span class="sxs-lookup"><span data-stu-id="60975-180">Using a func to fulfill a policy</span></span>

<span data-ttu-id="60975-181">ポリシーを満たすことがコードで簡単に表現できる場合があります。</span><span class="sxs-lookup"><span data-stu-id="60975-181">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="60975-182">ポリシービルダーでポリシーを設定するときに`Func<AuthorizationHandlerContext, bool>`を提供することができます。 `RequireAssertion`</span><span class="sxs-lookup"><span data-stu-id="60975-182">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="60975-183">たとえば、前`BadgeEntryHandler`の例は次のように書き直されます。</span><span class="sxs-lookup"><span data-stu-id="60975-183">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=42-43,47-53)]

## <a name="accessing-mvc-request-context-in-handlers"></a><span data-ttu-id="60975-184">ハンドラー内の MVC 要求コンテキストへのアクセス</span><span class="sxs-lookup"><span data-stu-id="60975-184">Accessing MVC request context in handlers</span></span>

<span data-ttu-id="60975-185">承認`HandleRequirementAsync`ハンドラーに実装するメソッドには、2 つのパラメーターがあります`AuthorizationHandlerContext`。 `TRequirement`</span><span class="sxs-lookup"><span data-stu-id="60975-185">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="60975-186">MVC や Jabbr などのフレームワークは、追加の情報を渡`Resource``AuthorizationHandlerContext`すために、プロパティに任意のオブジェクトを追加できます。</span><span class="sxs-lookup"><span data-stu-id="60975-186">Frameworks such as MVC or Jabbr are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="60975-187">たとえば、MVC は、プロパティ内の[承認フィルター コンテキスト](/dotnet/api/?term=AuthorizationFilterContext)の`Resource`インスタンスを渡します。</span><span class="sxs-lookup"><span data-stu-id="60975-187">For example, MVC passes an instance of [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) in the `Resource` property.</span></span> <span data-ttu-id="60975-188">このプロパティは、 `HttpContext`、`RouteData`および MVC および Razor ページによって提供されるその他のすべてにアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="60975-188">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="60975-189">プロパティの`Resource`使用はフレームワーク固有です。</span><span class="sxs-lookup"><span data-stu-id="60975-189">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="60975-190">プロパティの情報を`Resource`使用すると、承認ポリシーが特定のフレームワークに制限されます。</span><span class="sxs-lookup"><span data-stu-id="60975-190">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="60975-191">キーワードを使用して`Resource`プロパティを`is`キャストし、キャストが成功したことを確認して、他のフレームワークで実行`InvalidCastException`したときにコードがクラッシュしないようにします。</span><span class="sxs-lookup"><span data-stu-id="60975-191">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

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

<span data-ttu-id="60975-192">この範囲の下で、[ロール ベースの承認](xref:security/authorization/roles)と[クレーム ベースの承認](xref:security/authorization/claims)では、要件、要件ハンドラー、および構成済みのポリシーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="60975-192">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="60975-193">これらの構成要素は、コード内の承認評価の式をサポートします。</span><span class="sxs-lookup"><span data-stu-id="60975-193">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="60975-194">結果は、より豊富で再利用可能でテスト可能な承認構造です。</span><span class="sxs-lookup"><span data-stu-id="60975-194">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="60975-195">承認ポリシーは、1 つ以上の要件で構成されます。</span><span class="sxs-lookup"><span data-stu-id="60975-195">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="60975-196">このメソッドは、認証サービスの構成の一部として、次`Startup.ConfigureServices`のメソッドで登録されます。</span><span class="sxs-lookup"><span data-stu-id="60975-196">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,66)]

<span data-ttu-id="60975-197">前の例では、"AtLeast21" ポリシーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="60975-197">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="60975-198">最小年齢の要件&mdash;は 1 つであり、要件のパラメーターとして指定されます。</span><span class="sxs-lookup"><span data-stu-id="60975-198">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="60975-199">I認証サービス</span><span class="sxs-lookup"><span data-stu-id="60975-199">IAuthorizationService</span></span> 

<span data-ttu-id="60975-200">承認が成功したかどうかを判断するプライマリ サービス<xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>は、 です。</span><span class="sxs-lookup"><span data-stu-id="60975-200">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

<span data-ttu-id="60975-201">上記のコードでは、 [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs)の 2 つのメソッドを強調表示します。</span><span class="sxs-lookup"><span data-stu-id="60975-201">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="60975-202"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>は、メソッドのないマーカー サービスであり、承認が成功したかどうかを追跡するためのメカニズムです。</span><span class="sxs-lookup"><span data-stu-id="60975-202"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="60975-203">要件<xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler>が満たされているかどうかを確認する責任があります。</span><span class="sxs-lookup"><span data-stu-id="60975-203">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
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

<span data-ttu-id="60975-204">クラス<xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext>は、要件が満たされているかどうかを示すためにハンドラーが使用するものです。</span><span class="sxs-lookup"><span data-stu-id="60975-204">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="60975-205">次のコードは、承認サービスの既定の実装の簡略化された (コメントで注釈が付けられた) を示しています。</span><span class="sxs-lookup"><span data-stu-id="60975-205">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

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

<span data-ttu-id="60975-206">次のコードは、典型的`ConfigureServices`なを示しています。</span><span class="sxs-lookup"><span data-stu-id="60975-206">The following code shows a typical `ConfigureServices`:</span></span>

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

<span data-ttu-id="60975-207">または<xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>`[Authorize(Policy = "Something")]`、承認に使用します。</span><span class="sxs-lookup"><span data-stu-id="60975-207">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="applying-policies-to-mvc-controllers"></a><span data-ttu-id="60975-208">MVC コントローラーへのポリシーの適用</span><span class="sxs-lookup"><span data-stu-id="60975-208">Applying policies to MVC controllers</span></span>

<span data-ttu-id="60975-209">Razor ページを使用している場合は、このドキュメントの[「Razor ページにポリシーを適用する](#applying-policies-to-razor-pages)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="60975-209">If you're using Razor Pages, see [Applying policies to Razor Pages](#applying-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="60975-210">ポリシーは、ポリシー名を持つ属性`[Authorize]`を使用して、コントローラに適用されます。</span><span class="sxs-lookup"><span data-stu-id="60975-210">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="60975-211">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="60975-211">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a><span data-ttu-id="60975-212">Razor ページへのポリシーの適用</span><span class="sxs-lookup"><span data-stu-id="60975-212">Applying policies to Razor Pages</span></span>

<span data-ttu-id="60975-213">ポリシーは、ポリシー名を持つ属性`[Authorize]`を使用して Razor ページに適用されます。</span><span class="sxs-lookup"><span data-stu-id="60975-213">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="60975-214">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="60975-214">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="60975-215">ポリシーは、[承認規則](xref:security/authorization/razor-pages-authorization)を使用して Razor ページに適用することもできます。</span><span class="sxs-lookup"><span data-stu-id="60975-215">Policies can also be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="60975-216">必要条件</span><span class="sxs-lookup"><span data-stu-id="60975-216">Requirements</span></span>

<span data-ttu-id="60975-217">承認要件は、ポリシーが現在のユーザー プリンシパルを評価するために使用できるデータ パラメーターのコレクションです。</span><span class="sxs-lookup"><span data-stu-id="60975-217">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="60975-218">"AtLeast21" ポリシーでは、最小年齢を 1&mdash;つのパラメーターにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="60975-218">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="60975-219">要件は、空のマーカー インターフェイスである[IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement)を実装します。</span><span class="sxs-lookup"><span data-stu-id="60975-219">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="60975-220">パラメーター化された最小年齢要件は、次のように実装できます。</span><span class="sxs-lookup"><span data-stu-id="60975-220">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="60975-221">承認ポリシーに複数の承認要件が含まれている場合、ポリシーの評価を成功させるには、すべての要件が満たされている必要があります。</span><span class="sxs-lookup"><span data-stu-id="60975-221">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="60975-222">つまり、1 つの許可ポリシーに追加された複数の許可要件は **、AND**ベースで処理されます。</span><span class="sxs-lookup"><span data-stu-id="60975-222">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="60975-223">要件には、データやプロパティを含める必要はありません。</span><span class="sxs-lookup"><span data-stu-id="60975-223">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="60975-224">承認ハンドラー</span><span class="sxs-lookup"><span data-stu-id="60975-224">Authorization handlers</span></span>

<span data-ttu-id="60975-225">承認ハンドラーは、要件のプロパティの評価を担当します。</span><span class="sxs-lookup"><span data-stu-id="60975-225">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="60975-226">承認ハンドラーは、指定された[AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext)に対して要件を評価し、アクセスが許可されているかどうかを判断します。</span><span class="sxs-lookup"><span data-stu-id="60975-226">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="60975-227">要件には[複数のハンドラ](#security-authorization-policies-based-multiple-handlers)を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="60975-227">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="60975-228">ハンドラは、処理する必要がある場合は`TRequirement`、[権限ハンドラ\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1)を継承できます。</span><span class="sxs-lookup"><span data-stu-id="60975-228">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="60975-229">または、ハンドラーは、複数の種類の要件を処理する[IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler)を実装する場合があります。</span><span class="sxs-lookup"><span data-stu-id="60975-229">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="60975-230">1 つの要件に対してハンドラーを使用する</span><span class="sxs-lookup"><span data-stu-id="60975-230">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="60975-231">最低年齢ハンドラーが 1 つの要件を使用する 1 対 1 の関係の例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="60975-231">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="60975-232">上記のコードは、現在のユーザー プリンシパルに、既知の信頼された発行者によって発行された誕生日の請求があるかどうかを判断します。</span><span class="sxs-lookup"><span data-stu-id="60975-232">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="60975-233">要求が欠落している場合は承認を行うことはできません。</span><span class="sxs-lookup"><span data-stu-id="60975-233">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="60975-234">クレームが存在する場合、ユーザーの年齢が計算されます。</span><span class="sxs-lookup"><span data-stu-id="60975-234">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="60975-235">ユーザーが要件で定義された最短年齢を満たしている場合、承認は成功したと見なされます。</span><span class="sxs-lookup"><span data-stu-id="60975-235">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="60975-236">承認が成功すると、`context.Succeed`満たされた要件を単独のパラメーターとして呼び出します。</span><span class="sxs-lookup"><span data-stu-id="60975-236">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="60975-237">複数の要件に対してハンドラーを使用する</span><span class="sxs-lookup"><span data-stu-id="60975-237">Use a handler for multiple requirements</span></span>

<span data-ttu-id="60975-238">次に、アクセス許可ハンドラーが 3 種類の要件を処理できる一対多リレーションシップの例を示します。</span><span class="sxs-lookup"><span data-stu-id="60975-238">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="60975-239">上記のコードは[、PendingRequirements を](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;走査します要件を含むプロパティが成功としてマークされていません。</span><span class="sxs-lookup"><span data-stu-id="60975-239">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="60975-240">要件の`ReadPermission`場合、要求されたリソースにアクセスするには、ユーザーが所有者であるかスポンサーである必要があります。</span><span class="sxs-lookup"><span data-stu-id="60975-240">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="60975-241">`EditPermission`または`DeletePermission`要件の場合、要求されたリソースにアクセスするには、所有者である必要があります。</span><span class="sxs-lookup"><span data-stu-id="60975-241">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="60975-242">ハンドラー登録</span><span class="sxs-lookup"><span data-stu-id="60975-242">Handler registration</span></span>

<span data-ttu-id="60975-243">ハンドラーは、構成時にサービス コレクションに登録されます。</span><span class="sxs-lookup"><span data-stu-id="60975-243">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="60975-244">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="60975-244">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,62-63,66)]

<span data-ttu-id="60975-245">上記のコードは、`MinimumAgeHandler`を呼び出`services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`すことによってシングルトンとして登録します。</span><span class="sxs-lookup"><span data-stu-id="60975-245">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="60975-246">組み込みの[サービスの有効期間](xref:fundamentals/dependency-injection#service-lifetimes)を使用してハンドラを登録できます。</span><span class="sxs-lookup"><span data-stu-id="60975-246">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="60975-247">ハンドラは何を返す必要がありますか?</span><span class="sxs-lookup"><span data-stu-id="60975-247">What should a handler return?</span></span>

<span data-ttu-id="60975-248">`Handle`[ハンドラの例](#security-authorization-handler-example)のメソッドは値を返さないことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="60975-248">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="60975-249">成功または失敗の状態はどのように示されていますか?</span><span class="sxs-lookup"><span data-stu-id="60975-249">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="60975-250">ハンドラーは、 を呼`context.Succeed(IAuthorizationRequirement requirement)`び出すことによって成功を示し、正常に検証された要件を渡します。</span><span class="sxs-lookup"><span data-stu-id="60975-250">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="60975-251">同じ要件の他のハンドラーが成功する可能性があるため、ハンドラーは一般的にエラーを処理する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="60975-251">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="60975-252">他の要求ハンドラーが成功した場合でも、エラーを`context.Fail`保証するには、 を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="60975-252">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="60975-253">ハンドラーが 呼`context.Succeed`び`context.Fail`出した場合、 または 他のすべてのハンドラーが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="60975-253">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="60975-254">これにより、別のハンドラーが正常に検証または失敗した場合でも発生するログ記録などの副作用を生成する要件が可能になります。</span><span class="sxs-lookup"><span data-stu-id="60975-254">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="60975-255">に`false`設定すると[、呼び出](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure)されたときに、呼び出し時にハンドラー`context.Fail`の実行が短絡するプロパティ (ASP.NET Core 1.1 以降で利用可能) が発生します。</span><span class="sxs-lookup"><span data-stu-id="60975-255">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="60975-256">`InvokeHandlersAfterFailure`は デフォルト`true`で、 すべてのハンドラが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="60975-256">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="60975-257">認証に失敗した場合でも、承認ハンドラーが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="60975-257">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="60975-258">要件に対して複数のハンドラが必要な理由</span><span class="sxs-lookup"><span data-stu-id="60975-258">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="60975-259">評価を**OR**ベースにする必要がある場合は、1 つの要件に対して複数のハンドラーを実装します。</span><span class="sxs-lookup"><span data-stu-id="60975-259">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="60975-260">たとえば、マイクロソフトにはキー カードだけで開くドアがあります。</span><span class="sxs-lookup"><span data-stu-id="60975-260">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="60975-261">鍵カードを家に置き忘れると、受付係は一時的なステッカーを印刷し、ドアを開けます。</span><span class="sxs-lookup"><span data-stu-id="60975-261">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="60975-262">このシナリオでは、1 つの要件*である BuildingEntry*を使用しますが、複数のハンドラーが 1 つの要件を調べています。</span><span class="sxs-lookup"><span data-stu-id="60975-262">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="60975-263">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="60975-263">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="60975-264">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="60975-264">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="60975-265">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="60975-265">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="60975-266">両方のハンドラが[登録](xref:security/authorization/policies#security-authorization-policies-based-handler-registration)されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="60975-266">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="60975-267">ポリシーが`BuildingEntryRequirement`を評価したときにいずれかのハンドラーが成功した場合、ポリシーの評価は成功します。</span><span class="sxs-lookup"><span data-stu-id="60975-267">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="using-a-func-to-fulfill-a-policy"></a><span data-ttu-id="60975-268">ポリシーを満たすために func を使用する</span><span class="sxs-lookup"><span data-stu-id="60975-268">Using a func to fulfill a policy</span></span>

<span data-ttu-id="60975-269">ポリシーを満たすことがコードで簡単に表現できる場合があります。</span><span class="sxs-lookup"><span data-stu-id="60975-269">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="60975-270">ポリシービルダーでポリシーを設定するときに`Func<AuthorizationHandlerContext, bool>`を提供することができます。 `RequireAssertion`</span><span class="sxs-lookup"><span data-stu-id="60975-270">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="60975-271">たとえば、前`BadgeEntryHandler`の例は次のように書き直されます。</span><span class="sxs-lookup"><span data-stu-id="60975-271">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=50-51,55-61)]

## <a name="accessing-mvc-request-context-in-handlers"></a><span data-ttu-id="60975-272">ハンドラー内の MVC 要求コンテキストへのアクセス</span><span class="sxs-lookup"><span data-stu-id="60975-272">Accessing MVC request context in handlers</span></span>

<span data-ttu-id="60975-273">承認`HandleRequirementAsync`ハンドラーに実装するメソッドには、2 つのパラメーターがあります`AuthorizationHandlerContext`。 `TRequirement`</span><span class="sxs-lookup"><span data-stu-id="60975-273">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="60975-274">MVC や SignalR などのフレームワークは、追加の情報を渡`Resource``AuthorizationHandlerContext`すために、プロパティに任意のオブジェクトを追加する自由です。</span><span class="sxs-lookup"><span data-stu-id="60975-274">Frameworks such as MVC or SignalR are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="60975-275">エンドポイント ルーティングを使用する場合、承認は通常、認証ミドルウェアによって処理されます。</span><span class="sxs-lookup"><span data-stu-id="60975-275">When using endpoint routing, authorization is typically handled by the Authorization Middleware.</span></span> <span data-ttu-id="60975-276">この場合、`Resource`プロパティは<xref:Microsoft.AspNetCore.Http.Endpoint>のインスタンスです。</span><span class="sxs-lookup"><span data-stu-id="60975-276">In this case, the `Resource` property is an instance of <xref:Microsoft.AspNetCore.Http.Endpoint>.</span></span> <span data-ttu-id="60975-277">エンドポイントを使用して、ルーティング先のリソースをプローブできます。</span><span class="sxs-lookup"><span data-stu-id="60975-277">The endpoint can be used to probe the underlying the resource to which you're routing.</span></span> <span data-ttu-id="60975-278">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="60975-278">For example:</span></span>

```csharp
if (context.Resource is Endpoint endpoint)
{
   var actionDescriptor = endpoint.Metadata.GetMetadata<ControllerActionDescriptor>();
   ...
}
```

<span data-ttu-id="60975-279">従来のルーティング、または承認が MVC の承認フィルターの一部として発生した場合`Resource`、の<xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext>値はインスタンスです。</span><span class="sxs-lookup"><span data-stu-id="60975-279">With traditional routing, or when authorization happens as part of MVC's authorization filter, the value of `Resource` is an <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> instance.</span></span> <span data-ttu-id="60975-280">このプロパティは、 `HttpContext`、`RouteData`および MVC および Razor ページによって提供されるその他のすべてにアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="60975-280">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="60975-281">プロパティの`Resource`使用はフレームワーク固有です。</span><span class="sxs-lookup"><span data-stu-id="60975-281">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="60975-282">プロパティの情報を`Resource`使用すると、承認ポリシーが特定のフレームワークに制限されます。</span><span class="sxs-lookup"><span data-stu-id="60975-282">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="60975-283">キーワードを使用して`Resource`プロパティを`is`キャストし、キャストが成功したことを確認して、他のフレームワークで実行`InvalidCastException`したときにコードがクラッシュしないようにします。</span><span class="sxs-lookup"><span data-stu-id="60975-283">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end
