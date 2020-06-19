---
title: ASP.NET Core でのポリシーベースの承認
author: rick-anderson
description: ASP.NET Core アプリで承認要件を強制するために承認ポリシーハンドラーを作成して使用する方法について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/policies
ms.openlocfilehash: 533bddc9c4499dad99cfdb3089045ea10aed4548
ms.sourcegitcommit: 4437f4c149f1ef6c28796dcfaa2863b4c088169c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/19/2020
ms.locfileid: "85074155"
---
# <a name="policy-based-authorization-in-aspnet-core"></a><span data-ttu-id="36e73-103">ASP.NET Core でのポリシーベースの承認</span><span class="sxs-lookup"><span data-stu-id="36e73-103">Policy-based authorization in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="36e73-104">内部的には、[ロールベースの承認](xref:security/authorization/roles)と[要求ベースの承認](xref:security/authorization/claims)では、要件、要件ハンドラー、および事前に構成されたポリシーを使用します。</span><span class="sxs-lookup"><span data-stu-id="36e73-104">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="36e73-105">これらの構成要素は、コードでの承認評価の式をサポートします。</span><span class="sxs-lookup"><span data-stu-id="36e73-105">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="36e73-106">結果として、より充実した再利用可能な承認の構造が得られます。</span><span class="sxs-lookup"><span data-stu-id="36e73-106">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="36e73-107">承認ポリシーは、1つまたは複数の要件で構成されます。</span><span class="sxs-lookup"><span data-stu-id="36e73-107">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="36e73-108">認証サービス構成の一部として、メソッドに登録され `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="36e73-108">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53, 58)]

<span data-ttu-id="36e73-109">前の例では、"AtLeast21" ポリシーが作成されています。</span><span class="sxs-lookup"><span data-stu-id="36e73-109">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="36e73-110">最小期間の要件が1つあり &mdash; ます。これは、要件にパラメーターとして指定されます。</span><span class="sxs-lookup"><span data-stu-id="36e73-110">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="36e73-111">IAuthorizationService</span><span class="sxs-lookup"><span data-stu-id="36e73-111">IAuthorizationService</span></span> 

<span data-ttu-id="36e73-112">承認が成功したかどうかを判断するプライマリサービスは <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> 次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="36e73-112">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

<span data-ttu-id="36e73-113">上記のコードは、 [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs)の2つのメソッドを強調表示しています。</span><span class="sxs-lookup"><span data-stu-id="36e73-113">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="36e73-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>は、メソッドを持たないマーカーサービスであり、認証が成功したかどうかを追跡するためのメカニズムを備えています。</span><span class="sxs-lookup"><span data-stu-id="36e73-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="36e73-115">各 <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> は、要件が満たされているかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="36e73-115">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
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

<span data-ttu-id="36e73-116">クラスは、 <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> 要件が満たされているかどうかを示すためにハンドラーが使用するものです。</span><span class="sxs-lookup"><span data-stu-id="36e73-116">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="36e73-117">次のコードは、承認サービスの簡略化された (コメントで注釈が付けられた) 既定の実装を示しています。</span><span class="sxs-lookup"><span data-stu-id="36e73-117">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

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

<span data-ttu-id="36e73-118">一般的なコードを次に示し `ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="36e73-118">The following code shows a typical `ConfigureServices`:</span></span>

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

<span data-ttu-id="36e73-119"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>承認にはまたはを使用 `[Authorize(Policy = "Something")]` します。</span><span class="sxs-lookup"><span data-stu-id="36e73-119">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="apply-policies-to-mvc-controllers"></a><span data-ttu-id="36e73-120">MVC コントローラーにポリシーを適用する</span><span class="sxs-lookup"><span data-stu-id="36e73-120">Apply policies to MVC controllers</span></span>

<span data-ttu-id="36e73-121">ページを使用している場合は Razor 、このドキュメントの「 [ Razor ページにポリシーを適用する](#apply-policies-to-razor-pages)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="36e73-121">If you're using Razor Pages, see [Apply policies to Razor Pages](#apply-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="36e73-122">ポリシーは、ポリシー名を持つ属性を使用して、コントローラーに適用され `[Authorize]` ます。</span><span class="sxs-lookup"><span data-stu-id="36e73-122">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="36e73-123">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="36e73-123">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="apply-policies-to-razor-pages"></a><span data-ttu-id="36e73-124">ページにポリシーを適用する Razor</span><span class="sxs-lookup"><span data-stu-id="36e73-124">Apply policies to Razor Pages</span></span>

<span data-ttu-id="36e73-125">ポリシーは Razor 、ポリシー名を持つ属性を使用して、ページに適用され `[Authorize]` ます。</span><span class="sxs-lookup"><span data-stu-id="36e73-125">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="36e73-126">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="36e73-126">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="36e73-127">ポリシーは Razor 、[承認規則](xref:security/authorization/razor-pages-authorization)を使用してページに適用することもできます。</span><span class="sxs-lookup"><span data-stu-id="36e73-127">Policies can also be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="36e73-128">必要条件</span><span class="sxs-lookup"><span data-stu-id="36e73-128">Requirements</span></span>

<span data-ttu-id="36e73-129">承認要件とは、ポリシーが現在のユーザープリンシパルを評価するために使用できるデータパラメーターのコレクションです。</span><span class="sxs-lookup"><span data-stu-id="36e73-129">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="36e73-130">"AtLeast21" ポリシーでは、最小有効期間を1つのパラメーターとして指定する必要が &mdash; あります。</span><span class="sxs-lookup"><span data-stu-id="36e73-130">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="36e73-131">要件には、空のマーカーインターフェイスである[IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement)が実装されています。</span><span class="sxs-lookup"><span data-stu-id="36e73-131">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="36e73-132">パラメーター化された最小年齢要件は、次のように実装できます。</span><span class="sxs-lookup"><span data-stu-id="36e73-132">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="36e73-133">承認ポリシーに複数の承認要件が含まれている場合は、ポリシーの評価を成功させるためにすべての要件を満たしている必要があります。</span><span class="sxs-lookup"><span data-stu-id="36e73-133">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="36e73-134">つまり、1つの承認ポリシーに追加された複数の承認要件は、**と**の基準で処理されます。</span><span class="sxs-lookup"><span data-stu-id="36e73-134">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="36e73-135">要件には、データまたはプロパティを含める必要はありません。</span><span class="sxs-lookup"><span data-stu-id="36e73-135">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="36e73-136">承認ハンドラー</span><span class="sxs-lookup"><span data-stu-id="36e73-136">Authorization handlers</span></span>

<span data-ttu-id="36e73-137">承認ハンドラーは、要件のプロパティを評価する役割を担います。</span><span class="sxs-lookup"><span data-stu-id="36e73-137">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="36e73-138">承認ハンドラーは、指定された[authorizationhandler コンテキスト](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext)に対して要件を評価して、アクセスが許可されているかどうかを判断します。</span><span class="sxs-lookup"><span data-stu-id="36e73-138">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="36e73-139">要件には、[複数のハンドラー](#security-authorization-policies-based-multiple-handlers)を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="36e73-139">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="36e73-140">ハンドラーは[Authorizationhandler \<TRequirement> ](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1)を継承できます。ここで、 `TRequirement` は処理する必要がある要件です。</span><span class="sxs-lookup"><span data-stu-id="36e73-140">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="36e73-141">また、1つのハンドラーで[IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler)を実装して、複数の種類の要件を処理することもできます。</span><span class="sxs-lookup"><span data-stu-id="36e73-141">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="36e73-142">1つの要件に対してハンドラーを使用する</span><span class="sxs-lookup"><span data-stu-id="36e73-142">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="36e73-143">次に示すのは、最小年齢ハンドラが1つの要件を利用する一対一のリレーションシップの例です。</span><span class="sxs-lookup"><span data-stu-id="36e73-143">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="36e73-144">前のコードは、現在のユーザープリンシパルが、既知の信頼された発行者によって発行された生年月日を持っているかどうかを判断します。</span><span class="sxs-lookup"><span data-stu-id="36e73-144">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="36e73-145">要求が欠落している場合は、承認を行うことができません。この場合、完了したタスクが返されます。</span><span class="sxs-lookup"><span data-stu-id="36e73-145">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="36e73-146">クレームが存在する場合は、ユーザーの年齢が計算されます。</span><span class="sxs-lookup"><span data-stu-id="36e73-146">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="36e73-147">ユーザーが要件によって定義された最小経過期間を満たしている場合、承認は成功したと見なされます。</span><span class="sxs-lookup"><span data-stu-id="36e73-147">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="36e73-148">承認が成功すると、 `context.Succeed` は、その唯一のパラメーターとして満たされた要件で呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="36e73-148">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="36e73-149">複数の要件に対してハンドラーを使用する</span><span class="sxs-lookup"><span data-stu-id="36e73-149">Use a handler for multiple requirements</span></span>

<span data-ttu-id="36e73-150">次に、アクセス許可ハンドラーが3種類の要件を処理できる一対多リレーションシップの例を示します。</span><span class="sxs-lookup"><span data-stu-id="36e73-150">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="36e73-151">上記のコードは[PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)、 &mdash; 成功とマークされていない要件を含むプロパティを pendingrequirements 要件にたどります。</span><span class="sxs-lookup"><span data-stu-id="36e73-151">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="36e73-152">要件として `ReadPermission` 、要求されたリソースにアクセスするには、ユーザーが所有者またはスポンサーである必要があります。</span><span class="sxs-lookup"><span data-stu-id="36e73-152">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="36e73-153">または要件の場合は、 `EditPermission` `DeletePermission` 要求されたリソースにアクセスするための所有者である必要があります。</span><span class="sxs-lookup"><span data-stu-id="36e73-153">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="36e73-154">ハンドラーの登録</span><span class="sxs-lookup"><span data-stu-id="36e73-154">Handler registration</span></span>

<span data-ttu-id="36e73-155">ハンドラーは、構成中にサービスコレクションに登録されます。</span><span class="sxs-lookup"><span data-stu-id="36e73-155">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="36e73-156">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="36e73-156">For example:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53-55, 58)]

<span data-ttu-id="36e73-157">前のコードでは、を `MinimumAgeHandler` 呼び出すことによってシングルトンとして登録され `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();` ます。</span><span class="sxs-lookup"><span data-stu-id="36e73-157">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="36e73-158">ハンドラーは、組み込みの[サービス有効期間](xref:fundamentals/dependency-injection#service-lifetimes)のいずれかを使用して登録できます。</span><span class="sxs-lookup"><span data-stu-id="36e73-158">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="36e73-159">ハンドラーが返すもの</span><span class="sxs-lookup"><span data-stu-id="36e73-159">What should a handler return?</span></span>

<span data-ttu-id="36e73-160">`Handle`[ハンドラーの例](#security-authorization-handler-example)のメソッドは値を返さないことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="36e73-160">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="36e73-161">成功または失敗のいずれかの状態はどのように示されますか。</span><span class="sxs-lookup"><span data-stu-id="36e73-161">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="36e73-162">ハンドラーはを呼び出して成功を示し `context.Succeed(IAuthorizationRequirement requirement)` 、正常に検証された要件を渡します。</span><span class="sxs-lookup"><span data-stu-id="36e73-162">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="36e73-163">同じ要件の他のハンドラーが成功する可能性があるため、通常、ハンドラーはエラーを処理する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="36e73-163">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="36e73-164">エラーを保証するには、他の要件ハンドラーが成功したとしても、を呼び出し `context.Fail` ます。</span><span class="sxs-lookup"><span data-stu-id="36e73-164">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="36e73-165">ハンドラーが `context.Succeed` またはを呼び出すと `context.Fail` 、他のすべてのハンドラーが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="36e73-165">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="36e73-166">これにより、別のハンドラーが要件を正常に検証または失敗した場合でも、ログ記録などの副作用を生成することができます。</span><span class="sxs-lookup"><span data-stu-id="36e73-166">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="36e73-167">に設定すると `false` 、 [Invokeハンドラの terfailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure)プロパティ (ASP.NET Core 1.1 以降で使用可能) が呼び出されたときにハンドラーの実行をショートサーキットし `context.Fail` ます。</span><span class="sxs-lookup"><span data-stu-id="36e73-167">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="36e73-168">`InvokeHandlersAfterFailure`既定値は `true` です。この場合、すべてのハンドラーが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="36e73-168">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="36e73-169">認証ハンドラーは、認証が失敗した場合でも呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="36e73-169">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="36e73-170">1つの要件に対して複数のハンドラーが必要なのはなぜですか。</span><span class="sxs-lookup"><span data-stu-id="36e73-170">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="36e73-171">評価を**または**ベースにする場合は、1つの要件に対して複数のハンドラーを実装します。</span><span class="sxs-lookup"><span data-stu-id="36e73-171">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="36e73-172">たとえば、Microsoft には、キーカードだけを開くドアがあります。</span><span class="sxs-lookup"><span data-stu-id="36e73-172">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="36e73-173">キーカードを自宅に置いた場合、受付によって一時的なステッカーが印刷され、ドアが開きます。</span><span class="sxs-lookup"><span data-stu-id="36e73-173">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="36e73-174">このシナリオでは、1つの要件*BuildingEntry*がありますが、複数のハンドラーが1つの要件を調べています。</span><span class="sxs-lookup"><span data-stu-id="36e73-174">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="36e73-175">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="36e73-175">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="36e73-176">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="36e73-176">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="36e73-177">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="36e73-177">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="36e73-178">両方のハンドラーが[登録](xref:security/authorization/policies#security-authorization-policies-based-handler-registration)されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="36e73-178">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="36e73-179">ポリシーによってが評価されたときにいずれかのハンドラーが成功した場合 `BuildingEntryRequirement` 、ポリシーの評価は成功します。</span><span class="sxs-lookup"><span data-stu-id="36e73-179">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="use-a-func-to-fulfill-a-policy"></a><span data-ttu-id="36e73-180">Func を使用してポリシーを満たす</span><span class="sxs-lookup"><span data-stu-id="36e73-180">Use a func to fulfill a policy</span></span>

<span data-ttu-id="36e73-181">ポリシーを実現することは、コード内で簡単に行うことができます。</span><span class="sxs-lookup"><span data-stu-id="36e73-181">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="36e73-182">ポリシー `Func<AuthorizationHandlerContext, bool>` ビルダーを使用してポリシーを構成するときに、を指定することができ `RequireAssertion` ます。</span><span class="sxs-lookup"><span data-stu-id="36e73-182">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="36e73-183">たとえば、前のは次のように `BadgeEntryHandler` 書き換えることができます。</span><span class="sxs-lookup"><span data-stu-id="36e73-183">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=42-43,47-53)]

## <a name="access-mvc-request-context-in-handlers"></a><span data-ttu-id="36e73-184">ハンドラーの MVC 要求コンテキストへのアクセス</span><span class="sxs-lookup"><span data-stu-id="36e73-184">Access MVC request context in handlers</span></span>

<span data-ttu-id="36e73-185">`HandleRequirementAsync`承認ハンドラーに実装するメソッドには、とという2つのパラメーターがあります。 `AuthorizationHandlerContext` `TRequirement`</span><span class="sxs-lookup"><span data-stu-id="36e73-185">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="36e73-186">MVC やなどのフレームワークは SignalR 、のプロパティに任意のオブジェクトを追加して、追加情報を渡すことが `Resource` `AuthorizationHandlerContext` できます。</span><span class="sxs-lookup"><span data-stu-id="36e73-186">Frameworks such as MVC or SignalR are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="36e73-187">エンドポイントルーティングを使用する場合、承認は通常、承認ミドルウェアによって処理されます。</span><span class="sxs-lookup"><span data-stu-id="36e73-187">When using endpoint routing, authorization is typically handled by the Authorization Middleware.</span></span> <span data-ttu-id="36e73-188">この場合、 `Resource` プロパティはのインスタンス <xref:Microsoft.AspNetCore.Http.Endpoint> です。</span><span class="sxs-lookup"><span data-stu-id="36e73-188">In this case, the `Resource` property is an instance of <xref:Microsoft.AspNetCore.Http.Endpoint>.</span></span> <span data-ttu-id="36e73-189">エンドポイントを使用して、ルーティング先の基になるリソースを調べることができます。</span><span class="sxs-lookup"><span data-stu-id="36e73-189">The endpoint can be used to probe the underlying resource to which you're routing.</span></span> <span data-ttu-id="36e73-190">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="36e73-190">For example:</span></span>

```csharp
if (context.Resource is Endpoint endpoint)
{
   var actionDescriptor = endpoint.Metadata.GetMetadata<ControllerActionDescriptor>();
   ...
}
```

<span data-ttu-id="36e73-191">エンドポイントは、現在のへのアクセスを提供しません `HttpContext` 。</span><span class="sxs-lookup"><span data-stu-id="36e73-191">The endpoint doesn't provide access to the current `HttpContext`.</span></span> <span data-ttu-id="36e73-192">エンドポイントルーティングを使用する場合は、を使用して、 `IHttpContextAcessor` `HttpContext` 承認ハンドラー内でにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="36e73-192">When using endpoint routing, use `IHttpContextAcessor` to access `HttpContext` inside of an authorization handler.</span></span> <span data-ttu-id="36e73-193">詳細については、「[カスタムコンポーネントからの HttpContext の使用](xref:fundamentals/httpcontext#use-httpcontext-from-custom-components)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="36e73-193">For more information, see [Use HttpContext from custom components](xref:fundamentals/httpcontext#use-httpcontext-from-custom-components).</span></span>

<span data-ttu-id="36e73-194">従来のルーティングでは、または MVC の承認フィルターの一部として承認が行われた場合、の値 `Resource` は <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> インスタンスになります。</span><span class="sxs-lookup"><span data-stu-id="36e73-194">With traditional routing, or when authorization happens as part of MVC's authorization filter, the value of `Resource` is an <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> instance.</span></span> <span data-ttu-id="36e73-195">このプロパティは、、 `HttpContext` `RouteData` 、および MVC とページによって提供されるすべてのものへのアクセスを提供し Razor ます。</span><span class="sxs-lookup"><span data-stu-id="36e73-195">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="36e73-196">プロパティの使用 `Resource` はフレームワーク固有です。</span><span class="sxs-lookup"><span data-stu-id="36e73-196">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="36e73-197">プロパティの情報を使用すると、 `Resource` 承認ポリシーが特定のフレームワークに限定されます。</span><span class="sxs-lookup"><span data-stu-id="36e73-197">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="36e73-198">`Resource`キーワードを使用してプロパティをキャストし、キャストが成功したことを確認して、 `is` `InvalidCastException` 他のフレームワークで実行したときにコードがクラッシュしないようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="36e73-198">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

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

<span data-ttu-id="36e73-199">内部的には、[ロールベースの承認](xref:security/authorization/roles)と[要求ベースの承認](xref:security/authorization/claims)では、要件、要件ハンドラー、および事前に構成されたポリシーを使用します。</span><span class="sxs-lookup"><span data-stu-id="36e73-199">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="36e73-200">これらの構成要素は、コードでの承認評価の式をサポートします。</span><span class="sxs-lookup"><span data-stu-id="36e73-200">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="36e73-201">結果として、より充実した再利用可能な承認の構造が得られます。</span><span class="sxs-lookup"><span data-stu-id="36e73-201">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="36e73-202">承認ポリシーは、1つまたは複数の要件で構成されます。</span><span class="sxs-lookup"><span data-stu-id="36e73-202">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="36e73-203">認証サービス構成の一部として、メソッドに登録され `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="36e73-203">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,66)]

<span data-ttu-id="36e73-204">前の例では、"AtLeast21" ポリシーが作成されています。</span><span class="sxs-lookup"><span data-stu-id="36e73-204">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="36e73-205">最小期間の要件が1つあり &mdash; ます。これは、要件にパラメーターとして指定されます。</span><span class="sxs-lookup"><span data-stu-id="36e73-205">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="36e73-206">IAuthorizationService</span><span class="sxs-lookup"><span data-stu-id="36e73-206">IAuthorizationService</span></span> 

<span data-ttu-id="36e73-207">承認が成功したかどうかを判断するプライマリサービスは <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> 次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="36e73-207">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

<span data-ttu-id="36e73-208">上記のコードは、 [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs)の2つのメソッドを強調表示しています。</span><span class="sxs-lookup"><span data-stu-id="36e73-208">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="36e73-209"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>は、メソッドを持たないマーカーサービスであり、認証が成功したかどうかを追跡するためのメカニズムを備えています。</span><span class="sxs-lookup"><span data-stu-id="36e73-209"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="36e73-210">各 <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> は、要件が満たされているかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="36e73-210">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
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

<span data-ttu-id="36e73-211">クラスは、 <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> 要件が満たされているかどうかを示すためにハンドラーが使用するものです。</span><span class="sxs-lookup"><span data-stu-id="36e73-211">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="36e73-212">次のコードは、承認サービスの簡略化された (コメントで注釈が付けられた) 既定の実装を示しています。</span><span class="sxs-lookup"><span data-stu-id="36e73-212">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

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

<span data-ttu-id="36e73-213">一般的なコードを次に示し `ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="36e73-213">The following code shows a typical `ConfigureServices`:</span></span>

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

<span data-ttu-id="36e73-214"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>承認にはまたはを使用 `[Authorize(Policy = "Something")]` します。</span><span class="sxs-lookup"><span data-stu-id="36e73-214">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="apply-policies-to-mvc-controllers"></a><span data-ttu-id="36e73-215">MVC コントローラーにポリシーを適用する</span><span class="sxs-lookup"><span data-stu-id="36e73-215">Apply policies to MVC controllers</span></span>

<span data-ttu-id="36e73-216">ページを使用している場合は Razor 、このドキュメントの「 [ Razor ページにポリシーを適用する](#apply-policies-to-razor-pages)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="36e73-216">If you're using Razor Pages, see [Apply policies to Razor Pages](#apply-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="36e73-217">ポリシーは、ポリシー名を持つ属性を使用して、コントローラーに適用され `[Authorize]` ます。</span><span class="sxs-lookup"><span data-stu-id="36e73-217">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="36e73-218">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="36e73-218">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="apply-policies-to-razor-pages"></a><span data-ttu-id="36e73-219">ページにポリシーを適用する Razor</span><span class="sxs-lookup"><span data-stu-id="36e73-219">Apply policies to Razor Pages</span></span>

<span data-ttu-id="36e73-220">ポリシーは Razor 、ポリシー名を持つ属性を使用して、ページに適用され `[Authorize]` ます。</span><span class="sxs-lookup"><span data-stu-id="36e73-220">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="36e73-221">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="36e73-221">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="36e73-222">ポリシーは Razor 、[承認規則](xref:security/authorization/razor-pages-authorization)を使用してページに適用することもできます。</span><span class="sxs-lookup"><span data-stu-id="36e73-222">Policies can also be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="36e73-223">必要条件</span><span class="sxs-lookup"><span data-stu-id="36e73-223">Requirements</span></span>

<span data-ttu-id="36e73-224">承認要件とは、ポリシーが現在のユーザープリンシパルを評価するために使用できるデータパラメーターのコレクションです。</span><span class="sxs-lookup"><span data-stu-id="36e73-224">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="36e73-225">"AtLeast21" ポリシーでは、最小有効期間を1つのパラメーターとして指定する必要が &mdash; あります。</span><span class="sxs-lookup"><span data-stu-id="36e73-225">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="36e73-226">要件には、空のマーカーインターフェイスである[IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement)が実装されています。</span><span class="sxs-lookup"><span data-stu-id="36e73-226">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="36e73-227">パラメーター化された最小年齢要件は、次のように実装できます。</span><span class="sxs-lookup"><span data-stu-id="36e73-227">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="36e73-228">承認ポリシーに複数の承認要件が含まれている場合は、ポリシーの評価を成功させるためにすべての要件を満たしている必要があります。</span><span class="sxs-lookup"><span data-stu-id="36e73-228">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="36e73-229">つまり、1つの承認ポリシーに追加された複数の承認要件は、**と**の基準で処理されます。</span><span class="sxs-lookup"><span data-stu-id="36e73-229">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="36e73-230">要件には、データまたはプロパティを含める必要はありません。</span><span class="sxs-lookup"><span data-stu-id="36e73-230">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="36e73-231">承認ハンドラー</span><span class="sxs-lookup"><span data-stu-id="36e73-231">Authorization handlers</span></span>

<span data-ttu-id="36e73-232">承認ハンドラーは、要件のプロパティを評価する役割を担います。</span><span class="sxs-lookup"><span data-stu-id="36e73-232">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="36e73-233">承認ハンドラーは、指定された[authorizationhandler コンテキスト](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext)に対して要件を評価して、アクセスが許可されているかどうかを判断します。</span><span class="sxs-lookup"><span data-stu-id="36e73-233">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="36e73-234">要件には、[複数のハンドラー](#security-authorization-policies-based-multiple-handlers)を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="36e73-234">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="36e73-235">ハンドラーは[Authorizationhandler \<TRequirement> ](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1)を継承できます。ここで、 `TRequirement` は処理する必要がある要件です。</span><span class="sxs-lookup"><span data-stu-id="36e73-235">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="36e73-236">また、1つのハンドラーで[IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler)を実装して、複数の種類の要件を処理することもできます。</span><span class="sxs-lookup"><span data-stu-id="36e73-236">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="36e73-237">1つの要件に対してハンドラーを使用する</span><span class="sxs-lookup"><span data-stu-id="36e73-237">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="36e73-238">次に示すのは、最小年齢ハンドラが1つの要件を利用する一対一のリレーションシップの例です。</span><span class="sxs-lookup"><span data-stu-id="36e73-238">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="36e73-239">前のコードは、現在のユーザープリンシパルが、既知の信頼された発行者によって発行された生年月日を持っているかどうかを判断します。</span><span class="sxs-lookup"><span data-stu-id="36e73-239">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="36e73-240">要求が欠落している場合は、承認を行うことができません。この場合、完了したタスクが返されます。</span><span class="sxs-lookup"><span data-stu-id="36e73-240">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="36e73-241">クレームが存在する場合は、ユーザーの年齢が計算されます。</span><span class="sxs-lookup"><span data-stu-id="36e73-241">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="36e73-242">ユーザーが要件によって定義された最小経過期間を満たしている場合、承認は成功したと見なされます。</span><span class="sxs-lookup"><span data-stu-id="36e73-242">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="36e73-243">承認が成功すると、 `context.Succeed` は、その唯一のパラメーターとして満たされた要件で呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="36e73-243">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="36e73-244">複数の要件に対してハンドラーを使用する</span><span class="sxs-lookup"><span data-stu-id="36e73-244">Use a handler for multiple requirements</span></span>

<span data-ttu-id="36e73-245">次に、アクセス許可ハンドラーが3種類の要件を処理できる一対多リレーションシップの例を示します。</span><span class="sxs-lookup"><span data-stu-id="36e73-245">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="36e73-246">上記のコードは[PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)、 &mdash; 成功とマークされていない要件を含むプロパティを pendingrequirements 要件にたどります。</span><span class="sxs-lookup"><span data-stu-id="36e73-246">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="36e73-247">要件として `ReadPermission` 、要求されたリソースにアクセスするには、ユーザーが所有者またはスポンサーである必要があります。</span><span class="sxs-lookup"><span data-stu-id="36e73-247">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="36e73-248">または要件の場合は、 `EditPermission` `DeletePermission` 要求されたリソースにアクセスするための所有者である必要があります。</span><span class="sxs-lookup"><span data-stu-id="36e73-248">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="36e73-249">ハンドラーの登録</span><span class="sxs-lookup"><span data-stu-id="36e73-249">Handler registration</span></span>

<span data-ttu-id="36e73-250">ハンドラーは、構成中にサービスコレクションに登録されます。</span><span class="sxs-lookup"><span data-stu-id="36e73-250">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="36e73-251">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="36e73-251">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,62-63,66)]

<span data-ttu-id="36e73-252">前のコードでは、を `MinimumAgeHandler` 呼び出すことによってシングルトンとして登録され `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();` ます。</span><span class="sxs-lookup"><span data-stu-id="36e73-252">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="36e73-253">ハンドラーは、組み込みの[サービス有効期間](xref:fundamentals/dependency-injection#service-lifetimes)のいずれかを使用して登録できます。</span><span class="sxs-lookup"><span data-stu-id="36e73-253">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="36e73-254">ハンドラーが返すもの</span><span class="sxs-lookup"><span data-stu-id="36e73-254">What should a handler return?</span></span>

<span data-ttu-id="36e73-255">`Handle`[ハンドラーの例](#security-authorization-handler-example)のメソッドは値を返さないことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="36e73-255">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="36e73-256">成功または失敗のいずれかの状態はどのように示されますか。</span><span class="sxs-lookup"><span data-stu-id="36e73-256">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="36e73-257">ハンドラーはを呼び出して成功を示し `context.Succeed(IAuthorizationRequirement requirement)` 、正常に検証された要件を渡します。</span><span class="sxs-lookup"><span data-stu-id="36e73-257">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="36e73-258">同じ要件の他のハンドラーが成功する可能性があるため、通常、ハンドラーはエラーを処理する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="36e73-258">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="36e73-259">エラーを保証するには、他の要件ハンドラーが成功したとしても、を呼び出し `context.Fail` ます。</span><span class="sxs-lookup"><span data-stu-id="36e73-259">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="36e73-260">ハンドラーが `context.Succeed` またはを呼び出すと `context.Fail` 、他のすべてのハンドラーが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="36e73-260">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="36e73-261">これにより、別のハンドラーが要件を正常に検証または失敗した場合でも、ログ記録などの副作用を生成することができます。</span><span class="sxs-lookup"><span data-stu-id="36e73-261">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="36e73-262">に設定すると `false` 、 [Invokeハンドラの terfailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure)プロパティ (ASP.NET Core 1.1 以降で使用可能) が呼び出されたときにハンドラーの実行をショートサーキットし `context.Fail` ます。</span><span class="sxs-lookup"><span data-stu-id="36e73-262">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="36e73-263">`InvokeHandlersAfterFailure`既定値は `true` です。この場合、すべてのハンドラーが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="36e73-263">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="36e73-264">認証ハンドラーは、認証が失敗した場合でも呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="36e73-264">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="36e73-265">1つの要件に対して複数のハンドラーが必要なのはなぜですか。</span><span class="sxs-lookup"><span data-stu-id="36e73-265">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="36e73-266">評価を**または**ベースにする場合は、1つの要件に対して複数のハンドラーを実装します。</span><span class="sxs-lookup"><span data-stu-id="36e73-266">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="36e73-267">たとえば、Microsoft には、キーカードだけを開くドアがあります。</span><span class="sxs-lookup"><span data-stu-id="36e73-267">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="36e73-268">キーカードを自宅に置いた場合、受付によって一時的なステッカーが印刷され、ドアが開きます。</span><span class="sxs-lookup"><span data-stu-id="36e73-268">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="36e73-269">このシナリオでは、1つの要件*BuildingEntry*がありますが、複数のハンドラーが1つの要件を調べています。</span><span class="sxs-lookup"><span data-stu-id="36e73-269">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="36e73-270">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="36e73-270">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="36e73-271">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="36e73-271">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="36e73-272">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="36e73-272">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="36e73-273">両方のハンドラーが[登録](xref:security/authorization/policies#security-authorization-policies-based-handler-registration)されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="36e73-273">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="36e73-274">ポリシーによってが評価されたときにいずれかのハンドラーが成功した場合 `BuildingEntryRequirement` 、ポリシーの評価は成功します。</span><span class="sxs-lookup"><span data-stu-id="36e73-274">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="use-a-func-to-fulfill-a-policy"></a><span data-ttu-id="36e73-275">Func を使用してポリシーを満たす</span><span class="sxs-lookup"><span data-stu-id="36e73-275">Use a func to fulfill a policy</span></span>

<span data-ttu-id="36e73-276">ポリシーを実現することは、コード内で簡単に行うことができます。</span><span class="sxs-lookup"><span data-stu-id="36e73-276">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="36e73-277">ポリシー `Func<AuthorizationHandlerContext, bool>` ビルダーを使用してポリシーを構成するときに、を指定することができ `RequireAssertion` ます。</span><span class="sxs-lookup"><span data-stu-id="36e73-277">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="36e73-278">たとえば、前のは次のように `BadgeEntryHandler` 書き換えることができます。</span><span class="sxs-lookup"><span data-stu-id="36e73-278">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=50-51,55-61)]

## <a name="access-mvc-request-context-in-handlers"></a><span data-ttu-id="36e73-279">ハンドラーの MVC 要求コンテキストへのアクセス</span><span class="sxs-lookup"><span data-stu-id="36e73-279">Access MVC request context in handlers</span></span>

<span data-ttu-id="36e73-280">`HandleRequirementAsync`承認ハンドラーに実装するメソッドには、とという2つのパラメーターがあります。 `AuthorizationHandlerContext` `TRequirement`</span><span class="sxs-lookup"><span data-stu-id="36e73-280">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="36e73-281">MVC やなどのフレームワークは SignalR 、のプロパティに任意のオブジェクトを追加して、追加情報を渡すことが `Resource` `AuthorizationHandlerContext` できます。</span><span class="sxs-lookup"><span data-stu-id="36e73-281">Frameworks such as MVC or SignalR are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="36e73-282">たとえば、MVC は、プロパティに[Authorizationfiltercontext](/dotnet/api/?term=AuthorizationFilterContext)のインスタンスを渡し `Resource` ます。</span><span class="sxs-lookup"><span data-stu-id="36e73-282">For example, MVC passes an instance of [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) in the `Resource` property.</span></span> <span data-ttu-id="36e73-283">このプロパティは、、 `HttpContext` `RouteData` 、および MVC とページによって提供されるすべてのものへのアクセスを提供し Razor ます。</span><span class="sxs-lookup"><span data-stu-id="36e73-283">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="36e73-284">プロパティの使用 `Resource` はフレームワーク固有です。</span><span class="sxs-lookup"><span data-stu-id="36e73-284">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="36e73-285">プロパティの情報を使用すると、 `Resource` 承認ポリシーが特定のフレームワークに限定されます。</span><span class="sxs-lookup"><span data-stu-id="36e73-285">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="36e73-286">`Resource`キーワードを使用してプロパティをキャストし、キャストが成功したことを確認して、 `is` `InvalidCastException` 他のフレームワークで実行したときにコードがクラッシュしないようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="36e73-286">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end
