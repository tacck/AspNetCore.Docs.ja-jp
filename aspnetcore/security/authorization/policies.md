---
title: ASP.NET Core でのポリシーベースの承認
author: rick-anderson
description: ASP.NET Core アプリで承認要件を強制するために承認ポリシーハンドラーを作成して使用する方法について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/policies
ms.openlocfilehash: 03d6e7fdc4ab4b5e4925508952bfd6c835d90486
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021277"
---
# <a name="policy-based-authorization-in-aspnet-core"></a><span data-ttu-id="dfa11-103">ASP.NET Core でのポリシーベースの承認</span><span class="sxs-lookup"><span data-stu-id="dfa11-103">Policy-based authorization in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="dfa11-104">内部的には、[ロールベースの承認](xref:security/authorization/roles)と[要求ベースの承認](xref:security/authorization/claims)では、要件、要件ハンドラー、および事前に構成されたポリシーを使用します。</span><span class="sxs-lookup"><span data-stu-id="dfa11-104">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="dfa11-105">これらの構成要素は、コードでの承認評価の式をサポートします。</span><span class="sxs-lookup"><span data-stu-id="dfa11-105">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="dfa11-106">結果として、より充実した再利用可能な承認の構造が得られます。</span><span class="sxs-lookup"><span data-stu-id="dfa11-106">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="dfa11-107">承認ポリシーは、1つまたは複数の要件で構成されます。</span><span class="sxs-lookup"><span data-stu-id="dfa11-107">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="dfa11-108">認証サービス構成の一部として、メソッドに登録され `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="dfa11-108">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53, 58)]

<span data-ttu-id="dfa11-109">前の例では、"AtLeast21" ポリシーが作成されています。</span><span class="sxs-lookup"><span data-stu-id="dfa11-109">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="dfa11-110">最小期間の要件が1つあり &mdash; ます。これは、要件にパラメーターとして指定されます。</span><span class="sxs-lookup"><span data-stu-id="dfa11-110">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="dfa11-111">IAuthorizationService</span><span class="sxs-lookup"><span data-stu-id="dfa11-111">IAuthorizationService</span></span> 

<span data-ttu-id="dfa11-112">承認が成功したかどうかを判断するプライマリサービスは <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> 次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="dfa11-112">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

<span data-ttu-id="dfa11-113">上記のコードは、 [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs)の2つのメソッドを強調表示しています。</span><span class="sxs-lookup"><span data-stu-id="dfa11-113">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="dfa11-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>は、メソッドを持たないマーカーサービスであり、認証が成功したかどうかを追跡するためのメカニズムを備えています。</span><span class="sxs-lookup"><span data-stu-id="dfa11-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="dfa11-115">各 <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> は、要件が満たされているかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="dfa11-115">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
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

<span data-ttu-id="dfa11-116">クラスは、 <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> 要件が満たされているかどうかを示すためにハンドラーが使用するものです。</span><span class="sxs-lookup"><span data-stu-id="dfa11-116">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="dfa11-117">次のコードは、承認サービスの簡略化された (コメントで注釈が付けられた) 既定の実装を示しています。</span><span class="sxs-lookup"><span data-stu-id="dfa11-117">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

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

<span data-ttu-id="dfa11-118">一般的なコードを次に示し `ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="dfa11-118">The following code shows a typical `ConfigureServices`:</span></span>

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

<span data-ttu-id="dfa11-119"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>承認にはまたはを使用 `[Authorize(Policy = "Something")]` します。</span><span class="sxs-lookup"><span data-stu-id="dfa11-119">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="apply-policies-to-mvc-controllers"></a><span data-ttu-id="dfa11-120">MVC コントローラーにポリシーを適用する</span><span class="sxs-lookup"><span data-stu-id="dfa11-120">Apply policies to MVC controllers</span></span>

<span data-ttu-id="dfa11-121">ページを使用している場合は Razor 、このドキュメントの「 [ Razor ページにポリシーを適用する](#apply-policies-to-razor-pages)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dfa11-121">If you're using Razor Pages, see [Apply policies to Razor Pages](#apply-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="dfa11-122">ポリシーは、ポリシー名を持つ属性を使用して、コントローラーに適用され `[Authorize]` ます。</span><span class="sxs-lookup"><span data-stu-id="dfa11-122">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="dfa11-123">例:</span><span class="sxs-lookup"><span data-stu-id="dfa11-123">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="apply-policies-to-no-locrazor-pages"></a><span data-ttu-id="dfa11-124">ページにポリシーを適用する Razor</span><span class="sxs-lookup"><span data-stu-id="dfa11-124">Apply policies to Razor Pages</span></span>

<span data-ttu-id="dfa11-125">ポリシーは Razor 、ポリシー名を持つ属性を使用して、ページに適用され `[Authorize]` ます。</span><span class="sxs-lookup"><span data-stu-id="dfa11-125">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="dfa11-126">例:</span><span class="sxs-lookup"><span data-stu-id="dfa11-126">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="dfa11-127">ポリシーをページハンドラーレベルで適用することはでき***ません***。ポリシーは Razor ページに適用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="dfa11-127">Policies can ***not*** be applied at the Razor Page handler level, they must be applied to the Page.</span></span>

<span data-ttu-id="dfa11-128">ポリシーは Razor 、[承認規則](xref:security/authorization/razor-pages-authorization)を使用してページに適用できます。</span><span class="sxs-lookup"><span data-stu-id="dfa11-128">Policies can be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="dfa11-129">要件</span><span class="sxs-lookup"><span data-stu-id="dfa11-129">Requirements</span></span>

<span data-ttu-id="dfa11-130">承認要件とは、ポリシーが現在のユーザープリンシパルを評価するために使用できるデータパラメーターのコレクションです。</span><span class="sxs-lookup"><span data-stu-id="dfa11-130">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="dfa11-131">"AtLeast21" ポリシーでは、最小有効期間を1つのパラメーターとして指定する必要が &mdash; あります。</span><span class="sxs-lookup"><span data-stu-id="dfa11-131">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="dfa11-132">要件には、空のマーカーインターフェイスである[IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement)が実装されています。</span><span class="sxs-lookup"><span data-stu-id="dfa11-132">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="dfa11-133">パラメーター化された最小年齢要件は、次のように実装できます。</span><span class="sxs-lookup"><span data-stu-id="dfa11-133">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="dfa11-134">承認ポリシーに複数の承認要件が含まれている場合は、ポリシーの評価を成功させるためにすべての要件を満たしている必要があります。</span><span class="sxs-lookup"><span data-stu-id="dfa11-134">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="dfa11-135">つまり、1つの承認ポリシーに追加された複数の承認要件は、**と**の基準で処理されます。</span><span class="sxs-lookup"><span data-stu-id="dfa11-135">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="dfa11-136">要件には、データまたはプロパティを含める必要はありません。</span><span class="sxs-lookup"><span data-stu-id="dfa11-136">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="dfa11-137">承認ハンドラー</span><span class="sxs-lookup"><span data-stu-id="dfa11-137">Authorization handlers</span></span>

<span data-ttu-id="dfa11-138">承認ハンドラーは、要件のプロパティを評価する役割を担います。</span><span class="sxs-lookup"><span data-stu-id="dfa11-138">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="dfa11-139">承認ハンドラーは、指定された[authorizationhandler コンテキスト](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext)に対して要件を評価して、アクセスが許可されているかどうかを判断します。</span><span class="sxs-lookup"><span data-stu-id="dfa11-139">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="dfa11-140">要件には、[複数のハンドラー](#security-authorization-policies-based-multiple-handlers)を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="dfa11-140">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="dfa11-141">ハンドラーは[Authorizationhandler \<TRequirement> ](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1)を継承できます。ここで、 `TRequirement` は処理する必要がある要件です。</span><span class="sxs-lookup"><span data-stu-id="dfa11-141">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="dfa11-142">また、1つのハンドラーで[IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler)を実装して、複数の種類の要件を処理することもできます。</span><span class="sxs-lookup"><span data-stu-id="dfa11-142">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="dfa11-143">1つの要件に対してハンドラーを使用する</span><span class="sxs-lookup"><span data-stu-id="dfa11-143">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="dfa11-144">次に示すのは、最小年齢ハンドラが1つの要件を利用する一対一のリレーションシップの例です。</span><span class="sxs-lookup"><span data-stu-id="dfa11-144">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="dfa11-145">前のコードは、現在のユーザープリンシパルが、既知の信頼された発行者によって発行された生年月日を持っているかどうかを判断します。</span><span class="sxs-lookup"><span data-stu-id="dfa11-145">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="dfa11-146">要求が欠落している場合は、承認を行うことができません。この場合、完了したタスクが返されます。</span><span class="sxs-lookup"><span data-stu-id="dfa11-146">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="dfa11-147">クレームが存在する場合は、ユーザーの年齢が計算されます。</span><span class="sxs-lookup"><span data-stu-id="dfa11-147">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="dfa11-148">ユーザーが要件によって定義された最小経過期間を満たしている場合、承認は成功したと見なされます。</span><span class="sxs-lookup"><span data-stu-id="dfa11-148">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="dfa11-149">承認が成功すると、 `context.Succeed` は、その唯一のパラメーターとして満たされた要件で呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="dfa11-149">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="dfa11-150">複数の要件に対してハンドラーを使用する</span><span class="sxs-lookup"><span data-stu-id="dfa11-150">Use a handler for multiple requirements</span></span>

<span data-ttu-id="dfa11-151">次に、アクセス許可ハンドラーが3種類の要件を処理できる一対多リレーションシップの例を示します。</span><span class="sxs-lookup"><span data-stu-id="dfa11-151">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="dfa11-152">上記のコードは[PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)、 &mdash; 成功とマークされていない要件を含むプロパティを pendingrequirements 要件にたどります。</span><span class="sxs-lookup"><span data-stu-id="dfa11-152">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="dfa11-153">要件として `ReadPermission` 、要求されたリソースにアクセスするには、ユーザーが所有者またはスポンサーである必要があります。</span><span class="sxs-lookup"><span data-stu-id="dfa11-153">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="dfa11-154">または要件の場合は、 `EditPermission` `DeletePermission` 要求されたリソースにアクセスするための所有者である必要があります。</span><span class="sxs-lookup"><span data-stu-id="dfa11-154">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="dfa11-155">ハンドラーの登録</span><span class="sxs-lookup"><span data-stu-id="dfa11-155">Handler registration</span></span>

<span data-ttu-id="dfa11-156">ハンドラーは、構成中にサービスコレクションに登録されます。</span><span class="sxs-lookup"><span data-stu-id="dfa11-156">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="dfa11-157">例:</span><span class="sxs-lookup"><span data-stu-id="dfa11-157">For example:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53-55, 58)]

<span data-ttu-id="dfa11-158">前のコードでは、を `MinimumAgeHandler` 呼び出すことによってシングルトンとして登録され `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();` ます。</span><span class="sxs-lookup"><span data-stu-id="dfa11-158">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="dfa11-159">ハンドラーは、組み込みの[サービス有効期間](xref:fundamentals/dependency-injection#service-lifetimes)のいずれかを使用して登録できます。</span><span class="sxs-lookup"><span data-stu-id="dfa11-159">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="dfa11-160">ハンドラーが返すもの</span><span class="sxs-lookup"><span data-stu-id="dfa11-160">What should a handler return?</span></span>

<span data-ttu-id="dfa11-161">`Handle`[ハンドラーの例](#security-authorization-handler-example)のメソッドは値を返さないことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="dfa11-161">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="dfa11-162">成功または失敗のいずれかの状態はどのように示されますか。</span><span class="sxs-lookup"><span data-stu-id="dfa11-162">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="dfa11-163">ハンドラーはを呼び出して成功を示し `context.Succeed(IAuthorizationRequirement requirement)` 、正常に検証された要件を渡します。</span><span class="sxs-lookup"><span data-stu-id="dfa11-163">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="dfa11-164">同じ要件の他のハンドラーが成功する可能性があるため、通常、ハンドラーはエラーを処理する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="dfa11-164">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="dfa11-165">エラーを保証するには、他の要件ハンドラーが成功したとしても、を呼び出し `context.Fail` ます。</span><span class="sxs-lookup"><span data-stu-id="dfa11-165">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="dfa11-166">ハンドラーが `context.Succeed` またはを呼び出すと `context.Fail` 、他のすべてのハンドラーが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="dfa11-166">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="dfa11-167">これにより、別のハンドラーが要件を正常に検証または失敗した場合でも、ログ記録などの副作用を生成することができます。</span><span class="sxs-lookup"><span data-stu-id="dfa11-167">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="dfa11-168">に設定すると `false` 、 [Invokeハンドラの terfailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure)プロパティ (ASP.NET Core 1.1 以降で使用可能) が呼び出されたときにハンドラーの実行をショートサーキットし `context.Fail` ます。</span><span class="sxs-lookup"><span data-stu-id="dfa11-168">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="dfa11-169">`InvokeHandlersAfterFailure`既定値は `true` です。この場合、すべてのハンドラーが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="dfa11-169">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="dfa11-170">認証ハンドラーは、認証が失敗した場合でも呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="dfa11-170">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="dfa11-171">1つの要件に対して複数のハンドラーが必要なのはなぜですか。</span><span class="sxs-lookup"><span data-stu-id="dfa11-171">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="dfa11-172">評価を**または**ベースにする場合は、1つの要件に対して複数のハンドラーを実装します。</span><span class="sxs-lookup"><span data-stu-id="dfa11-172">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="dfa11-173">たとえば、Microsoft には、キーカードだけを開くドアがあります。</span><span class="sxs-lookup"><span data-stu-id="dfa11-173">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="dfa11-174">キーカードを自宅に置いた場合、受付によって一時的なステッカーが印刷され、ドアが開きます。</span><span class="sxs-lookup"><span data-stu-id="dfa11-174">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="dfa11-175">このシナリオでは、1つの要件*BuildingEntry*がありますが、複数のハンドラーが1つの要件を調べています。</span><span class="sxs-lookup"><span data-stu-id="dfa11-175">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="dfa11-176">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="dfa11-176">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="dfa11-177">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="dfa11-177">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="dfa11-178">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="dfa11-178">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="dfa11-179">両方のハンドラーが[登録](xref:security/authorization/policies#security-authorization-policies-based-handler-registration)されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="dfa11-179">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="dfa11-180">ポリシーによってが評価されたときにいずれかのハンドラーが成功した場合 `BuildingEntryRequirement` 、ポリシーの評価は成功します。</span><span class="sxs-lookup"><span data-stu-id="dfa11-180">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="use-a-func-to-fulfill-a-policy"></a><span data-ttu-id="dfa11-181">Func を使用してポリシーを満たす</span><span class="sxs-lookup"><span data-stu-id="dfa11-181">Use a func to fulfill a policy</span></span>

<span data-ttu-id="dfa11-182">ポリシーを実現することは、コード内で簡単に行うことができます。</span><span class="sxs-lookup"><span data-stu-id="dfa11-182">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="dfa11-183">ポリシー `Func<AuthorizationHandlerContext, bool>` ビルダーを使用してポリシーを構成するときに、を指定することができ `RequireAssertion` ます。</span><span class="sxs-lookup"><span data-stu-id="dfa11-183">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="dfa11-184">たとえば、前のは次のように `BadgeEntryHandler` 書き換えることができます。</span><span class="sxs-lookup"><span data-stu-id="dfa11-184">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=42-43,47-53)]

## <a name="access-mvc-request-context-in-handlers"></a><span data-ttu-id="dfa11-185">ハンドラーの MVC 要求コンテキストへのアクセス</span><span class="sxs-lookup"><span data-stu-id="dfa11-185">Access MVC request context in handlers</span></span>

<span data-ttu-id="dfa11-186">`HandleRequirementAsync`承認ハンドラーに実装するメソッドには、とという2つのパラメーターがあります。 `AuthorizationHandlerContext` `TRequirement`</span><span class="sxs-lookup"><span data-stu-id="dfa11-186">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="dfa11-187">MVC やなどのフレームワークは SignalR 、のプロパティに任意のオブジェクトを追加して、追加情報を渡すことが `Resource` `AuthorizationHandlerContext` できます。</span><span class="sxs-lookup"><span data-stu-id="dfa11-187">Frameworks such as MVC or SignalR are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="dfa11-188">エンドポイントルーティングを使用する場合、承認は通常、承認ミドルウェアによって処理されます。</span><span class="sxs-lookup"><span data-stu-id="dfa11-188">When using endpoint routing, authorization is typically handled by the Authorization Middleware.</span></span> <span data-ttu-id="dfa11-189">この場合、 `Resource` プロパティはのインスタンス <xref:Microsoft.AspNetCore.Http.Endpoint> です。</span><span class="sxs-lookup"><span data-stu-id="dfa11-189">In this case, the `Resource` property is an instance of <xref:Microsoft.AspNetCore.Http.Endpoint>.</span></span> <span data-ttu-id="dfa11-190">エンドポイントを使用して、ルーティング先の基になるリソースを調べることができます。</span><span class="sxs-lookup"><span data-stu-id="dfa11-190">The endpoint can be used to probe the underlying resource to which you're routing.</span></span> <span data-ttu-id="dfa11-191">例:</span><span class="sxs-lookup"><span data-stu-id="dfa11-191">For example:</span></span>

```csharp
if (context.Resource is Endpoint endpoint)
{
   var actionDescriptor = endpoint.Metadata.GetMetadata<ControllerActionDescriptor>();
   ...
}
```

<span data-ttu-id="dfa11-192">エンドポイントは、現在のへのアクセスを提供しません `HttpContext` 。</span><span class="sxs-lookup"><span data-stu-id="dfa11-192">The endpoint doesn't provide access to the current `HttpContext`.</span></span> <span data-ttu-id="dfa11-193">エンドポイントルーティングを使用する場合は、を使用して、 `IHttpContextAcessor` `HttpContext` 承認ハンドラー内でにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="dfa11-193">When using endpoint routing, use `IHttpContextAcessor` to access `HttpContext` inside of an authorization handler.</span></span> <span data-ttu-id="dfa11-194">詳細については、「[カスタムコンポーネントからの HttpContext の使用](xref:fundamentals/httpcontext#use-httpcontext-from-custom-components)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dfa11-194">For more information, see [Use HttpContext from custom components](xref:fundamentals/httpcontext#use-httpcontext-from-custom-components).</span></span>

<span data-ttu-id="dfa11-195">従来のルーティングでは、または MVC の承認フィルターの一部として承認が行われた場合、の値 `Resource` は <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> インスタンスになります。</span><span class="sxs-lookup"><span data-stu-id="dfa11-195">With traditional routing, or when authorization happens as part of MVC's authorization filter, the value of `Resource` is an <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> instance.</span></span> <span data-ttu-id="dfa11-196">このプロパティは、、 `HttpContext` `RouteData` 、および MVC とページによって提供されるすべてのものへのアクセスを提供し Razor ます。</span><span class="sxs-lookup"><span data-stu-id="dfa11-196">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="dfa11-197">プロパティの使用 `Resource` はフレームワーク固有です。</span><span class="sxs-lookup"><span data-stu-id="dfa11-197">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="dfa11-198">プロパティの情報を使用すると、 `Resource` 承認ポリシーが特定のフレームワークに限定されます。</span><span class="sxs-lookup"><span data-stu-id="dfa11-198">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="dfa11-199">`Resource`キーワードを使用してプロパティをキャストし、キャストが成功したことを確認して、 `is` `InvalidCastException` 他のフレームワークで実行したときにコードがクラッシュしないようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="dfa11-199">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

## <a name="globally-require-all-users-to-be-authenticated"></a><span data-ttu-id="dfa11-200">すべてのユーザーをグローバルに認証する必要があります</span><span class="sxs-lookup"><span data-stu-id="dfa11-200">Globally require all users to be authenticated</span></span>

[!INCLUDE[](~/includes/requireAuth.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="dfa11-201">内部的には、[ロールベースの承認](xref:security/authorization/roles)と[要求ベースの承認](xref:security/authorization/claims)では、要件、要件ハンドラー、および事前に構成されたポリシーを使用します。</span><span class="sxs-lookup"><span data-stu-id="dfa11-201">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="dfa11-202">これらの構成要素は、コードでの承認評価の式をサポートします。</span><span class="sxs-lookup"><span data-stu-id="dfa11-202">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="dfa11-203">結果として、より充実した再利用可能な承認の構造が得られます。</span><span class="sxs-lookup"><span data-stu-id="dfa11-203">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="dfa11-204">承認ポリシーは、1つまたは複数の要件で構成されます。</span><span class="sxs-lookup"><span data-stu-id="dfa11-204">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="dfa11-205">認証サービス構成の一部として、メソッドに登録され `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="dfa11-205">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,66)]

<span data-ttu-id="dfa11-206">前の例では、"AtLeast21" ポリシーが作成されています。</span><span class="sxs-lookup"><span data-stu-id="dfa11-206">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="dfa11-207">最小期間の要件が1つあり &mdash; ます。これは、要件にパラメーターとして指定されます。</span><span class="sxs-lookup"><span data-stu-id="dfa11-207">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="dfa11-208">IAuthorizationService</span><span class="sxs-lookup"><span data-stu-id="dfa11-208">IAuthorizationService</span></span> 

<span data-ttu-id="dfa11-209">承認が成功したかどうかを判断するプライマリサービスは <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> 次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="dfa11-209">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

<span data-ttu-id="dfa11-210">上記のコードは、 [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs)の2つのメソッドを強調表示しています。</span><span class="sxs-lookup"><span data-stu-id="dfa11-210">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="dfa11-211"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>は、メソッドを持たないマーカーサービスであり、認証が成功したかどうかを追跡するためのメカニズムを備えています。</span><span class="sxs-lookup"><span data-stu-id="dfa11-211"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="dfa11-212">各 <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> は、要件が満たされているかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="dfa11-212">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
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

<span data-ttu-id="dfa11-213">クラスは、 <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> 要件が満たされているかどうかを示すためにハンドラーが使用するものです。</span><span class="sxs-lookup"><span data-stu-id="dfa11-213">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="dfa11-214">次のコードは、承認サービスの簡略化された (コメントで注釈が付けられた) 既定の実装を示しています。</span><span class="sxs-lookup"><span data-stu-id="dfa11-214">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

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

<span data-ttu-id="dfa11-215">一般的なコードを次に示し `ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="dfa11-215">The following code shows a typical `ConfigureServices`:</span></span>

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

<span data-ttu-id="dfa11-216"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>承認にはまたはを使用 `[Authorize(Policy = "Something")]` します。</span><span class="sxs-lookup"><span data-stu-id="dfa11-216">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="apply-policies-to-mvc-controllers"></a><span data-ttu-id="dfa11-217">MVC コントローラーにポリシーを適用する</span><span class="sxs-lookup"><span data-stu-id="dfa11-217">Apply policies to MVC controllers</span></span>

<span data-ttu-id="dfa11-218">ページを使用している場合は Razor 、このドキュメントの「 [ Razor ページにポリシーを適用する](#apply-policies-to-razor-pages)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dfa11-218">If you're using Razor Pages, see [Apply policies to Razor Pages](#apply-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="dfa11-219">ポリシーは、ポリシー名を持つ属性を使用して、コントローラーに適用され `[Authorize]` ます。</span><span class="sxs-lookup"><span data-stu-id="dfa11-219">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="dfa11-220">例:</span><span class="sxs-lookup"><span data-stu-id="dfa11-220">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="apply-policies-to-no-locrazor-pages"></a><span data-ttu-id="dfa11-221">ページにポリシーを適用する Razor</span><span class="sxs-lookup"><span data-stu-id="dfa11-221">Apply policies to Razor Pages</span></span>

<span data-ttu-id="dfa11-222">ポリシーは Razor 、ポリシー名を持つ属性を使用して、ページに適用され `[Authorize]` ます。</span><span class="sxs-lookup"><span data-stu-id="dfa11-222">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="dfa11-223">例:</span><span class="sxs-lookup"><span data-stu-id="dfa11-223">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="dfa11-224">ポリシーは Razor 、[承認規則](xref:security/authorization/razor-pages-authorization)を使用してページに適用することもできます。</span><span class="sxs-lookup"><span data-stu-id="dfa11-224">Policies can also be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="dfa11-225">要件</span><span class="sxs-lookup"><span data-stu-id="dfa11-225">Requirements</span></span>

<span data-ttu-id="dfa11-226">承認要件とは、ポリシーが現在のユーザープリンシパルを評価するために使用できるデータパラメーターのコレクションです。</span><span class="sxs-lookup"><span data-stu-id="dfa11-226">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="dfa11-227">"AtLeast21" ポリシーでは、最小有効期間を1つのパラメーターとして指定する必要が &mdash; あります。</span><span class="sxs-lookup"><span data-stu-id="dfa11-227">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="dfa11-228">要件には、空のマーカーインターフェイスである[IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement)が実装されています。</span><span class="sxs-lookup"><span data-stu-id="dfa11-228">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="dfa11-229">パラメーター化された最小年齢要件は、次のように実装できます。</span><span class="sxs-lookup"><span data-stu-id="dfa11-229">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="dfa11-230">承認ポリシーに複数の承認要件が含まれている場合は、ポリシーの評価を成功させるためにすべての要件を満たしている必要があります。</span><span class="sxs-lookup"><span data-stu-id="dfa11-230">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="dfa11-231">つまり、1つの承認ポリシーに追加された複数の承認要件は、**と**の基準で処理されます。</span><span class="sxs-lookup"><span data-stu-id="dfa11-231">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="dfa11-232">要件には、データまたはプロパティを含める必要はありません。</span><span class="sxs-lookup"><span data-stu-id="dfa11-232">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="dfa11-233">承認ハンドラー</span><span class="sxs-lookup"><span data-stu-id="dfa11-233">Authorization handlers</span></span>

<span data-ttu-id="dfa11-234">承認ハンドラーは、要件のプロパティを評価する役割を担います。</span><span class="sxs-lookup"><span data-stu-id="dfa11-234">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="dfa11-235">承認ハンドラーは、指定された[authorizationhandler コンテキスト](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext)に対して要件を評価して、アクセスが許可されているかどうかを判断します。</span><span class="sxs-lookup"><span data-stu-id="dfa11-235">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="dfa11-236">要件には、[複数のハンドラー](#security-authorization-policies-based-multiple-handlers)を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="dfa11-236">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="dfa11-237">ハンドラーは[Authorizationhandler \<TRequirement> ](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1)を継承できます。ここで、 `TRequirement` は処理する必要がある要件です。</span><span class="sxs-lookup"><span data-stu-id="dfa11-237">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="dfa11-238">また、1つのハンドラーで[IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler)を実装して、複数の種類の要件を処理することもできます。</span><span class="sxs-lookup"><span data-stu-id="dfa11-238">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="dfa11-239">1つの要件に対してハンドラーを使用する</span><span class="sxs-lookup"><span data-stu-id="dfa11-239">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="dfa11-240">次に示すのは、最小年齢ハンドラが1つの要件を利用する一対一のリレーションシップの例です。</span><span class="sxs-lookup"><span data-stu-id="dfa11-240">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="dfa11-241">前のコードは、現在のユーザープリンシパルが、既知の信頼された発行者によって発行された生年月日を持っているかどうかを判断します。</span><span class="sxs-lookup"><span data-stu-id="dfa11-241">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="dfa11-242">要求が欠落している場合は、承認を行うことができません。この場合、完了したタスクが返されます。</span><span class="sxs-lookup"><span data-stu-id="dfa11-242">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="dfa11-243">クレームが存在する場合は、ユーザーの年齢が計算されます。</span><span class="sxs-lookup"><span data-stu-id="dfa11-243">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="dfa11-244">ユーザーが要件によって定義された最小経過期間を満たしている場合、承認は成功したと見なされます。</span><span class="sxs-lookup"><span data-stu-id="dfa11-244">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="dfa11-245">承認が成功すると、 `context.Succeed` は、その唯一のパラメーターとして満たされた要件で呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="dfa11-245">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="dfa11-246">複数の要件に対してハンドラーを使用する</span><span class="sxs-lookup"><span data-stu-id="dfa11-246">Use a handler for multiple requirements</span></span>

<span data-ttu-id="dfa11-247">次に、アクセス許可ハンドラーが3種類の要件を処理できる一対多リレーションシップの例を示します。</span><span class="sxs-lookup"><span data-stu-id="dfa11-247">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="dfa11-248">上記のコードは[PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)、 &mdash; 成功とマークされていない要件を含むプロパティを pendingrequirements 要件にたどります。</span><span class="sxs-lookup"><span data-stu-id="dfa11-248">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="dfa11-249">要件として `ReadPermission` 、要求されたリソースにアクセスするには、ユーザーが所有者またはスポンサーである必要があります。</span><span class="sxs-lookup"><span data-stu-id="dfa11-249">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="dfa11-250">または要件の場合は、 `EditPermission` `DeletePermission` 要求されたリソースにアクセスするための所有者である必要があります。</span><span class="sxs-lookup"><span data-stu-id="dfa11-250">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="dfa11-251">ハンドラーの登録</span><span class="sxs-lookup"><span data-stu-id="dfa11-251">Handler registration</span></span>

<span data-ttu-id="dfa11-252">ハンドラーは、構成中にサービスコレクションに登録されます。</span><span class="sxs-lookup"><span data-stu-id="dfa11-252">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="dfa11-253">例:</span><span class="sxs-lookup"><span data-stu-id="dfa11-253">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,62-63,66)]

<span data-ttu-id="dfa11-254">前のコードでは、を `MinimumAgeHandler` 呼び出すことによってシングルトンとして登録され `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();` ます。</span><span class="sxs-lookup"><span data-stu-id="dfa11-254">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="dfa11-255">ハンドラーは、組み込みの[サービス有効期間](xref:fundamentals/dependency-injection#service-lifetimes)のいずれかを使用して登録できます。</span><span class="sxs-lookup"><span data-stu-id="dfa11-255">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="dfa11-256">ハンドラーが返すもの</span><span class="sxs-lookup"><span data-stu-id="dfa11-256">What should a handler return?</span></span>

<span data-ttu-id="dfa11-257">`Handle`[ハンドラーの例](#security-authorization-handler-example)のメソッドは値を返さないことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="dfa11-257">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="dfa11-258">成功または失敗のいずれかの状態はどのように示されますか。</span><span class="sxs-lookup"><span data-stu-id="dfa11-258">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="dfa11-259">ハンドラーはを呼び出して成功を示し `context.Succeed(IAuthorizationRequirement requirement)` 、正常に検証された要件を渡します。</span><span class="sxs-lookup"><span data-stu-id="dfa11-259">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="dfa11-260">同じ要件の他のハンドラーが成功する可能性があるため、通常、ハンドラーはエラーを処理する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="dfa11-260">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="dfa11-261">エラーを保証するには、他の要件ハンドラーが成功したとしても、を呼び出し `context.Fail` ます。</span><span class="sxs-lookup"><span data-stu-id="dfa11-261">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="dfa11-262">ハンドラーが `context.Succeed` またはを呼び出すと `context.Fail` 、他のすべてのハンドラーが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="dfa11-262">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="dfa11-263">これにより、別のハンドラーが要件を正常に検証または失敗した場合でも、ログ記録などの副作用を生成することができます。</span><span class="sxs-lookup"><span data-stu-id="dfa11-263">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="dfa11-264">に設定すると `false` 、 [Invokeハンドラの terfailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure)プロパティ (ASP.NET Core 1.1 以降で使用可能) が呼び出されたときにハンドラーの実行をショートサーキットし `context.Fail` ます。</span><span class="sxs-lookup"><span data-stu-id="dfa11-264">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="dfa11-265">`InvokeHandlersAfterFailure`既定値は `true` です。この場合、すべてのハンドラーが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="dfa11-265">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="dfa11-266">認証ハンドラーは、認証が失敗した場合でも呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="dfa11-266">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="dfa11-267">1つの要件に対して複数のハンドラーが必要なのはなぜですか。</span><span class="sxs-lookup"><span data-stu-id="dfa11-267">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="dfa11-268">評価を**または**ベースにする場合は、1つの要件に対して複数のハンドラーを実装します。</span><span class="sxs-lookup"><span data-stu-id="dfa11-268">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="dfa11-269">たとえば、Microsoft には、キーカードだけを開くドアがあります。</span><span class="sxs-lookup"><span data-stu-id="dfa11-269">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="dfa11-270">キーカードを自宅に置いた場合、受付によって一時的なステッカーが印刷され、ドアが開きます。</span><span class="sxs-lookup"><span data-stu-id="dfa11-270">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="dfa11-271">このシナリオでは、1つの要件*BuildingEntry*がありますが、複数のハンドラーが1つの要件を調べています。</span><span class="sxs-lookup"><span data-stu-id="dfa11-271">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="dfa11-272">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="dfa11-272">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="dfa11-273">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="dfa11-273">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="dfa11-274">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="dfa11-274">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="dfa11-275">両方のハンドラーが[登録](xref:security/authorization/policies#security-authorization-policies-based-handler-registration)されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="dfa11-275">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="dfa11-276">ポリシーによってが評価されたときにいずれかのハンドラーが成功した場合 `BuildingEntryRequirement` 、ポリシーの評価は成功します。</span><span class="sxs-lookup"><span data-stu-id="dfa11-276">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="use-a-func-to-fulfill-a-policy"></a><span data-ttu-id="dfa11-277">Func を使用してポリシーを満たす</span><span class="sxs-lookup"><span data-stu-id="dfa11-277">Use a func to fulfill a policy</span></span>

<span data-ttu-id="dfa11-278">ポリシーを実現することは、コード内で簡単に行うことができます。</span><span class="sxs-lookup"><span data-stu-id="dfa11-278">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="dfa11-279">ポリシー `Func<AuthorizationHandlerContext, bool>` ビルダーを使用してポリシーを構成するときに、を指定することができ `RequireAssertion` ます。</span><span class="sxs-lookup"><span data-stu-id="dfa11-279">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="dfa11-280">たとえば、前のは次のように `BadgeEntryHandler` 書き換えることができます。</span><span class="sxs-lookup"><span data-stu-id="dfa11-280">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=50-51,55-61)]

## <a name="access-mvc-request-context-in-handlers"></a><span data-ttu-id="dfa11-281">ハンドラーの MVC 要求コンテキストへのアクセス</span><span class="sxs-lookup"><span data-stu-id="dfa11-281">Access MVC request context in handlers</span></span>

<span data-ttu-id="dfa11-282">`HandleRequirementAsync`承認ハンドラーに実装するメソッドには、とという2つのパラメーターがあります。 `AuthorizationHandlerContext` `TRequirement`</span><span class="sxs-lookup"><span data-stu-id="dfa11-282">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="dfa11-283">MVC やなどのフレームワークは SignalR 、のプロパティに任意のオブジェクトを追加して、追加情報を渡すことが `Resource` `AuthorizationHandlerContext` できます。</span><span class="sxs-lookup"><span data-stu-id="dfa11-283">Frameworks such as MVC or SignalR are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="dfa11-284">たとえば、MVC は、プロパティに[Authorizationfiltercontext](/dotnet/api/?term=AuthorizationFilterContext)のインスタンスを渡し `Resource` ます。</span><span class="sxs-lookup"><span data-stu-id="dfa11-284">For example, MVC passes an instance of [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) in the `Resource` property.</span></span> <span data-ttu-id="dfa11-285">このプロパティは、、 `HttpContext` `RouteData` 、および MVC とページによって提供されるすべてのものへのアクセスを提供し Razor ます。</span><span class="sxs-lookup"><span data-stu-id="dfa11-285">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="dfa11-286">プロパティの使用 `Resource` はフレームワーク固有です。</span><span class="sxs-lookup"><span data-stu-id="dfa11-286">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="dfa11-287">プロパティの情報を使用すると、 `Resource` 承認ポリシーが特定のフレームワークに限定されます。</span><span class="sxs-lookup"><span data-stu-id="dfa11-287">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="dfa11-288">`Resource`キーワードを使用してプロパティをキャストし、キャストが成功したことを確認して、 `is` `InvalidCastException` 他のフレームワークで実行したときにコードがクラッシュしないようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="dfa11-288">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end
