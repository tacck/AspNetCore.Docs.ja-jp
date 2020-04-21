---
title: ASP.NET コアのカスタム承認ポリシー プロバイダー
author: mjrousos
description: ASP.NET コア アプリでカスタム IAuthorizationPolicyProvider を使用して、承認ポリシーを動的に生成する方法について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 11/14/2019
uid: security/authorization/iauthorizationpolicyprovider
ms.openlocfilehash: 2c67e25ff73bc8c3a5f3af4730a509b2385fc1cf
ms.sourcegitcommit: 5547d920f322e5a823575c031529e4755ab119de
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661770"
---
# <a name="custom-authorization-policy-providers-using-iauthorizationpolicyprovider-in-aspnet-core"></a><span data-ttu-id="512f0-103">ASP.NET コアで IAuthorizationPolicyProvider を使用するカスタム承認ポリシー プロバイダー</span><span class="sxs-lookup"><span data-stu-id="512f0-103">Custom Authorization Policy Providers using IAuthorizationPolicyProvider in ASP.NET Core</span></span> 

<span data-ttu-id="512f0-104">作成者: [Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="512f0-104">By [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="512f0-105">通常、[ポリシー ベースの認可](xref:security/authorization/policies)を使用する場合、`AuthorizationOptions.AddPolicy`ポリシーは認可サービスの構成の一部として呼び出すことによって登録されます。</span><span class="sxs-lookup"><span data-stu-id="512f0-105">Typically when using [policy-based authorization](xref:security/authorization/policies), policies are registered by calling `AuthorizationOptions.AddPolicy` as part of authorization service configuration.</span></span> <span data-ttu-id="512f0-106">シナリオによっては、この方法ですべての承認ポリシーを登録できない場合があります (または望ましいとは限りません)。</span><span class="sxs-lookup"><span data-stu-id="512f0-106">In some scenarios, it may not be possible (or desirable) to register all authorization policies in this way.</span></span> <span data-ttu-id="512f0-107">このような場合は、カスタム`IAuthorizationPolicyProvider`を使用して承認ポリシーの提供方法を制御できます。</span><span class="sxs-lookup"><span data-stu-id="512f0-107">In those cases, you can use a custom `IAuthorizationPolicyProvider` to control how authorization policies are supplied.</span></span>

<span data-ttu-id="512f0-108">カスタム[IAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider)が役に立つシナリオの例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="512f0-108">Examples of scenarios where a custom [IAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider) may be useful include:</span></span>

* <span data-ttu-id="512f0-109">外部サービスを使用してポリシー評価を行う。</span><span class="sxs-lookup"><span data-stu-id="512f0-109">Using an external service to provide policy evaluation.</span></span>
* <span data-ttu-id="512f0-110">さまざまな部屋番号や年齢など、さまざまな範囲のポリシーを使用するため、個々の承認ポリシーを`AuthorizationOptions.AddPolicy`呼び出しで追加しても意味がありません。</span><span class="sxs-lookup"><span data-stu-id="512f0-110">Using a large range of policies (for different room numbers or ages, for example), so it doesn't make sense to add each individual authorization policy with an `AuthorizationOptions.AddPolicy` call.</span></span>
* <span data-ttu-id="512f0-111">外部データ ソース (データベースなど) の情報に基づいて実行時にポリシーを作成するか、別のメカニズムを使用して動的に承認要件を決定する。</span><span class="sxs-lookup"><span data-stu-id="512f0-111">Creating policies at runtime based on information in an external data source (like a database) or determining authorization requirements dynamically through another mechanism.</span></span>

<span data-ttu-id="512f0-112">[AspNetCore GitHub リポジトリ](https://github.com/dotnet/AspNetCore)から[サンプル コードを表示またはダウンロード](https://github.com/dotnet/aspnetcore/tree/v3.1.3/src/Security/samples/CustomPolicyProvider)します。</span><span class="sxs-lookup"><span data-stu-id="512f0-112">[View or download sample code](https://github.com/dotnet/aspnetcore/tree/v3.1.3/src/Security/samples/CustomPolicyProvider) from the [AspNetCore GitHub repository](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="512f0-113">ドットネット/AspNetCore リポジトリ ZIP ファイルをダウンロードします。</span><span class="sxs-lookup"><span data-stu-id="512f0-113">Download the dotnet/AspNetCore repository ZIP file.</span></span> <span data-ttu-id="512f0-114">ファイルを解凍します。</span><span class="sxs-lookup"><span data-stu-id="512f0-114">Unzip the file.</span></span> <span data-ttu-id="512f0-115">*src/セキュリティ/サンプル/カスタムポリシープロバイダ*プロジェクトフォルダに移動します。</span><span class="sxs-lookup"><span data-stu-id="512f0-115">Navigate to the *src/Security/samples/CustomPolicyProvider* project folder.</span></span>

## <a name="customize-policy-retrieval"></a><span data-ttu-id="512f0-116">ポリシーの取得のカスタマイズ</span><span class="sxs-lookup"><span data-stu-id="512f0-116">Customize policy retrieval</span></span>

<span data-ttu-id="512f0-117">ASP.NET Core アプリは、インターフェイス`IAuthorizationPolicyProvider`の実装を使用して承認ポリシーを取得します。</span><span class="sxs-lookup"><span data-stu-id="512f0-117">ASP.NET Core apps use an implementation of the `IAuthorizationPolicyProvider` interface to retrieve authorization policies.</span></span> <span data-ttu-id="512f0-118">既定では、[既定の承認ポリシー プロバイダー](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider)が登録され、使用されます。</span><span class="sxs-lookup"><span data-stu-id="512f0-118">By default, [DefaultAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider) is registered and used.</span></span> <span data-ttu-id="512f0-119">`DefaultAuthorizationPolicyProvider`は、呼び`AuthorizationOptions`出しで`IServiceCollection.AddAuthorization`提供された からポリシーを返します。</span><span class="sxs-lookup"><span data-stu-id="512f0-119">`DefaultAuthorizationPolicyProvider` returns policies from the `AuthorizationOptions` provided in an `IServiceCollection.AddAuthorization` call.</span></span>

<span data-ttu-id="512f0-120">アプリの[依存関係の注入](xref:fundamentals/dependency-injection)コンテナーに`IAuthorizationPolicyProvider`別の実装を登録することによって、この動作をカスタマイズします。</span><span class="sxs-lookup"><span data-stu-id="512f0-120">Customize this behavior by registering a different `IAuthorizationPolicyProvider` implementation in the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> 

<span data-ttu-id="512f0-121">この`IAuthorizationPolicyProvider`インターフェイスには、次の 3 つの API が含まれています。</span><span class="sxs-lookup"><span data-stu-id="512f0-121">The `IAuthorizationPolicyProvider` interface contains three APIs:</span></span>

* <span data-ttu-id="512f0-122">[指定](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_)された名前の承認ポリシーを返します。</span><span class="sxs-lookup"><span data-stu-id="512f0-122">[GetPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_) returns an authorization policy for a given name.</span></span>
* <span data-ttu-id="512f0-123">[既定](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync)の承認ポリシー (ポリシーが指定されていない属性に使用される`[Authorize]`ポリシー) を返します。</span><span class="sxs-lookup"><span data-stu-id="512f0-123">[GetDefaultPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync) returns the default authorization policy (the policy used for `[Authorize]` attributes without a policy specified).</span></span> 
* <span data-ttu-id="512f0-124">[フォールバック](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getfallbackpolicyasync)認証ポリシー (ポリシーが指定されていない場合に承認ミドルウェアによって使用されるポリシー) を返します。</span><span class="sxs-lookup"><span data-stu-id="512f0-124">[GetFallbackPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getfallbackpolicyasync) returns the fallback authorization policy (the policy used by the Authorization Middleware when no policy is specified).</span></span> 

<span data-ttu-id="512f0-125">これらの API を実装することで、承認ポリシーの提供方法をカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="512f0-125">By implementing these APIs, you can customize how authorization policies are provided.</span></span>

## <a name="parameterized-authorize-attribute-example"></a><span data-ttu-id="512f0-126">パラメーター化された承認属性の例</span><span class="sxs-lookup"><span data-stu-id="512f0-126">Parameterized authorize attribute example</span></span>

<span data-ttu-id="512f0-127">便利`IAuthorizationPolicyProvider`なシナリオの 1 つは`[Authorize]`、パラメーターに依存する要件を持つカスタム属性を有効にすることです。</span><span class="sxs-lookup"><span data-stu-id="512f0-127">One scenario where `IAuthorizationPolicyProvider` is useful is enabling custom `[Authorize]` attributes whose requirements depend on a parameter.</span></span> <span data-ttu-id="512f0-128">たとえば、ポリシー[ベースの承認](xref:security/authorization/policies)ドキュメントでは、年齢ベースの ("AtLeast21") ポリシーがサンプルとして使用されました。</span><span class="sxs-lookup"><span data-stu-id="512f0-128">For example, in [policy-based authorization](xref:security/authorization/policies) documentation, an age-based (“AtLeast21”) policy was used as a sample.</span></span> <span data-ttu-id="512f0-129">アプリ内の異なるコントローラーアクションを*異なる*年齢のユーザーが使用できるようにする場合は、年齢ベースのポリシーを多数用意すると便利です。</span><span class="sxs-lookup"><span data-stu-id="512f0-129">If different controller actions in an app should be made available to users of *different* ages, it might be useful to have many different age-based policies.</span></span> <span data-ttu-id="512f0-130">でアプリケーションが必要`AuthorizationOptions`とするさまざまな年齢ベースのポリシーをすべて登録する代わりに、カスタム`IAuthorizationPolicyProvider`でポリシーを動的に生成できます。</span><span class="sxs-lookup"><span data-stu-id="512f0-130">Instead of registering all the different age-based policies that the application will need in `AuthorizationOptions`, you can generate the policies dynamically with a custom `IAuthorizationPolicyProvider`.</span></span> <span data-ttu-id="512f0-131">ポリシーを簡単に使用するには、 などの`[MinimumAgeAuthorize(20)]`カスタム承認属性を使用してアクションにアポイントを付けることができます。</span><span class="sxs-lookup"><span data-stu-id="512f0-131">To make using the policies easier, you can annotate actions with custom authorization attribute like `[MinimumAgeAuthorize(20)]`.</span></span>

## <a name="custom-authorization-attributes"></a><span data-ttu-id="512f0-132">カスタム認証属性</span><span class="sxs-lookup"><span data-stu-id="512f0-132">Custom Authorization attributes</span></span>

<span data-ttu-id="512f0-133">許可ポリシーは、名前で識別されます。</span><span class="sxs-lookup"><span data-stu-id="512f0-133">Authorization policies are identified by their names.</span></span> <span data-ttu-id="512f0-134">前述の`MinimumAgeAuthorizeAttribute`カスタムは、対応する承認ポリシーを取得するために使用できる文字列に引数をマップする必要があります。</span><span class="sxs-lookup"><span data-stu-id="512f0-134">The custom `MinimumAgeAuthorizeAttribute` described previously needs to map arguments into a string that can be used to retrieve the corresponding authorization policy.</span></span> <span data-ttu-id="512f0-135">プロパティを派生`AuthorizeAttribute`し、`Age``AuthorizeAttribute.Policy`プロパティをラップさせることによってこれを行うことができます。</span><span class="sxs-lookup"><span data-stu-id="512f0-135">You can do this by deriving from `AuthorizeAttribute` and making the `Age` property wrap the `AuthorizeAttribute.Policy` property.</span></span>

```csharp
internal class MinimumAgeAuthorizeAttribute : AuthorizeAttribute
{
    const string POLICY_PREFIX = "MinimumAge";

    public MinimumAgeAuthorizeAttribute(int age) => Age = age;

    // Get or set the Age property by manipulating the underlying Policy property
    public int Age
    {
        get
        {
            if (int.TryParse(Policy.Substring(POLICY_PREFIX.Length), out var age))
            {
                return age;
            }
            return default(int);
        }
        set
        {
            Policy = $"{POLICY_PREFIX}{value.ToString()}";
        }
    }
}
```

<span data-ttu-id="512f0-136">この属性型には、`Policy`ハードコーディングされたプレフィックス ( )`"MinimumAge"`に基づく文字列と、コンストラクターを介して渡される整数が含まれます。</span><span class="sxs-lookup"><span data-stu-id="512f0-136">This attribute type has a `Policy` string based on the hard-coded prefix (`"MinimumAge"`) and an integer passed in via the constructor.</span></span>

<span data-ttu-id="512f0-137">パラメーターとして整数を受け取る以外は、他`Authorize`の属性と同じ方法でアクションに適用できます。</span><span class="sxs-lookup"><span data-stu-id="512f0-137">You can apply it to actions in the same way as other `Authorize` attributes except that it takes an integer as a parameter.</span></span>

```csharp
[MinimumAgeAuthorize(10)]
public IActionResult RequiresMinimumAge10()
```

## <a name="custom-iauthorizationpolicyprovider"></a><span data-ttu-id="512f0-138">カスタム I 承認ポリシープロバイダー</span><span class="sxs-lookup"><span data-stu-id="512f0-138">Custom IAuthorizationPolicyProvider</span></span>

<span data-ttu-id="512f0-139">このカスタム`MinimumAgeAuthorizeAttribute`により、必要な最小期間に対して承認ポリシーを要求することが容易になります。</span><span class="sxs-lookup"><span data-stu-id="512f0-139">The custom `MinimumAgeAuthorizeAttribute` makes it easy to request authorization policies for any minimum age desired.</span></span> <span data-ttu-id="512f0-140">次に解決する問題は、これらの異なる年齢のすべての承認ポリシーが使用可能であることを確認することです。</span><span class="sxs-lookup"><span data-stu-id="512f0-140">The next problem to solve is making sure that authorization policies are available for all of those different ages.</span></span> <span data-ttu-id="512f0-141">ここで便利`IAuthorizationPolicyProvider`です。</span><span class="sxs-lookup"><span data-stu-id="512f0-141">This is where an `IAuthorizationPolicyProvider` is useful.</span></span>

<span data-ttu-id="512f0-142">を使用`MinimumAgeAuthorizationAttribute`する場合、承認ポリシー名は パターン`"MinimumAge" + Age`に従うので`IAuthorizationPolicyProvider`、カスタムは次の方法で承認ポリシーを生成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="512f0-142">When using `MinimumAgeAuthorizationAttribute`, the authorization policy names will follow the pattern `"MinimumAge" + Age`, so the custom `IAuthorizationPolicyProvider` should generate authorization policies by:</span></span>

* <span data-ttu-id="512f0-143">ポリシー名から経過時間を解析します。</span><span class="sxs-lookup"><span data-stu-id="512f0-143">Parsing the age from the policy name.</span></span>
* <span data-ttu-id="512f0-144">新規`AuthorizationPolicyBuilder`の作成に使用する`AuthorizationPolicy`</span><span class="sxs-lookup"><span data-stu-id="512f0-144">Using `AuthorizationPolicyBuilder` to create a new `AuthorizationPolicy`</span></span>
* <span data-ttu-id="512f0-145">この例と以下の例では、ユーザーがクッキーを介して認証されていると仮定します。</span><span class="sxs-lookup"><span data-stu-id="512f0-145">In this and following examples it will be assumed that the user is authenticated via a cookie.</span></span> <span data-ttu-id="512f0-146">は`AuthorizationPolicyBuilder`、少なくとも 1 つの承認スキーム名を使用して構築するか、常に成功する必要があります。</span><span class="sxs-lookup"><span data-stu-id="512f0-146">The `AuthorizationPolicyBuilder` should either be constructed with at least one authorization scheme name or always succeed.</span></span> <span data-ttu-id="512f0-147">それ以外の場合は、ユーザーにチャレンジを提供する方法に関する情報はなく、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="512f0-147">Otherwise there is no information on how to provide a challenge to the user and an exception will be thrown.</span></span>
* <span data-ttu-id="512f0-148">での経過時間に基づいてポリシーに要件`AuthorizationPolicyBuilder.AddRequirements`を追加します。</span><span class="sxs-lookup"><span data-stu-id="512f0-148">Adding requirements to the policy based on the age with `AuthorizationPolicyBuilder.AddRequirements`.</span></span> <span data-ttu-id="512f0-149">その他のシナリオでは、 、`RequireClaim``RequireRole`または`RequireUserName`を使用できます。</span><span class="sxs-lookup"><span data-stu-id="512f0-149">In other scenarios, you might use `RequireClaim`, `RequireRole`, or `RequireUserName` instead.</span></span>

```csharp
internal class MinimumAgePolicyProvider : IAuthorizationPolicyProvider
{
    const string POLICY_PREFIX = "MinimumAge";

    // Policies are looked up by string name, so expect 'parameters' (like age)
    // to be embedded in the policy names. This is abstracted away from developers
    // by the more strongly-typed attributes derived from AuthorizeAttribute
    // (like [MinimumAgeAuthorize()] in this sample)
    public Task<AuthorizationPolicy> GetPolicyAsync(string policyName)
    {
        if (policyName.StartsWith(POLICY_PREFIX, StringComparison.OrdinalIgnoreCase) &&
            int.TryParse(policyName.Substring(POLICY_PREFIX.Length), out var age))
        {
            var policy = new AuthorizationPolicyBuilder(CookieAuthenticationDefaults.AuthenticationScheme);
            policy.AddRequirements(new MinimumAgeRequirement(age));
            return Task.FromResult(policy.Build());
        }

        return Task.FromResult<AuthorizationPolicy>(null);
    }
}
```

## <a name="multiple-authorization-policy-providers"></a><span data-ttu-id="512f0-150">複数の承認ポリシー プロバイダー</span><span class="sxs-lookup"><span data-stu-id="512f0-150">Multiple authorization policy providers</span></span>

<span data-ttu-id="512f0-151">カスタム`IAuthorizationPolicyProvider`実装を使用する場合、core ASP.NETは`IAuthorizationPolicyProvider`のインスタンスを 1 つだけ使用することに注意してください。</span><span class="sxs-lookup"><span data-stu-id="512f0-151">When using custom `IAuthorizationPolicyProvider` implementations, keep in mind that ASP.NET Core only uses one instance of `IAuthorizationPolicyProvider`.</span></span> <span data-ttu-id="512f0-152">カスタム プロバイダーが、使用されるすべてのポリシー名に承認ポリシーを提供できない場合は、バックアップ プロバイダーに従う必要があります。</span><span class="sxs-lookup"><span data-stu-id="512f0-152">If a custom provider isn't able to provide authorization policies for all policy names that will be used, it should defer to a backup provider.</span></span> 

<span data-ttu-id="512f0-153">たとえば、カスタムの経過時間ポリシーと従来のロールベースのポリシー取得の両方を必要とするアプリケーションを考えてみます。</span><span class="sxs-lookup"><span data-stu-id="512f0-153">For example, consider an application that needs both custom age policies and more traditional role-based policy retrieval.</span></span> <span data-ttu-id="512f0-154">このようなアプリでは、次のようなカスタム承認ポリシー プロバイダーを使用できます。</span><span class="sxs-lookup"><span data-stu-id="512f0-154">Such an app could use a custom authorization policy provider that:</span></span>

* <span data-ttu-id="512f0-155">ポリシー名の解析を試みます。</span><span class="sxs-lookup"><span data-stu-id="512f0-155">Attempts to parse policy names.</span></span> 
* <span data-ttu-id="512f0-156">ポリシー名に年齢が含まれていない場合`DefaultAuthorizationPolicyProvider`は、別のポリシー プロバイダ (など) を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="512f0-156">Calls into a different policy provider (like `DefaultAuthorizationPolicyProvider`) if the policy name doesn't contain an age.</span></span>

<span data-ttu-id="512f0-157">上記の`IAuthorizationPolicyProvider`実装例は、そのコンストラクターでバックアップ`DefaultAuthorizationPolicyProvider`ポリシー プロバイダーを作成することで、そのを使用するように更新できます (ポリシー名が予期されるパターンの 'MinimumAge' + age と一致しない場合に使用されます)。</span><span class="sxs-lookup"><span data-stu-id="512f0-157">The example `IAuthorizationPolicyProvider` implementation shown above can be updated to use the `DefaultAuthorizationPolicyProvider` by creating a backup policy provider in its constructor (to be used in case the policy name doesn't match its expected pattern of 'MinimumAge' + age).</span></span>

```csharp
private DefaultAuthorizationPolicyProvider BackupPolicyProvider { get; }

public MinimumAgePolicyProvider(IOptions<AuthorizationOptions> options)
{
    // ASP.NET Core only uses one authorization policy provider, so if the custom implementation
    // doesn't handle all policies it should fall back to an alternate provider.
    BackupPolicyProvider = new DefaultAuthorizationPolicyProvider(options);
}
```

<span data-ttu-id="512f0-158">その後、`GetPolicyAsync`メソッドを更新して、null`BackupPolicyProvider`を返す代わりにを使用できます。</span><span class="sxs-lookup"><span data-stu-id="512f0-158">Then, the `GetPolicyAsync` method can be updated to use the `BackupPolicyProvider` instead of returning null:</span></span>

```csharp
...
return BackupPolicyProvider.GetPolicyAsync(policyName);
```

## <a name="default-policy"></a><span data-ttu-id="512f0-159">既定のポリシー</span><span class="sxs-lookup"><span data-stu-id="512f0-159">Default policy</span></span>

<span data-ttu-id="512f0-160">名前付き承認ポリシーを提供することに加えて`IAuthorizationPolicyProvider`、ポリシー名`GetDefaultPolicyAsync`を指定せずに属性に対`[Authorize]`して承認ポリシーを提供するために、カスタムが実装する必要があります。</span><span class="sxs-lookup"><span data-stu-id="512f0-160">In addition to providing named authorization policies, a custom `IAuthorizationPolicyProvider` needs to implement `GetDefaultPolicyAsync` to provide an authorization policy for `[Authorize]` attributes without a policy name specified.</span></span>

<span data-ttu-id="512f0-161">多くの場合、この認可属性は認証されたユーザーのみを必要とするため、必要なポリシーを に呼`RequireAuthenticatedUser`び出して作成できます。</span><span class="sxs-lookup"><span data-stu-id="512f0-161">In many cases, this authorization attribute only requires an authenticated user, so you can make the necessary policy with a call to `RequireAuthenticatedUser`:</span></span>

```csharp
public Task<AuthorizationPolicy> GetDefaultPolicyAsync() => 
    Task.FromResult(new AuthorizationPolicyBuilder(CookieAuthenticationDefaults.AuthenticationScheme).RequireAuthenticatedUser().Build());
```

<span data-ttu-id="512f0-162">カスタム`IAuthorizationPolicyProvider`の全ての側面と同様に、必要に応じてカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="512f0-162">As with all aspects of a custom `IAuthorizationPolicyProvider`, you can customize this, as needed.</span></span> <span data-ttu-id="512f0-163">場合によっては、フォールバック`IAuthorizationPolicyProvider`からデフォルトのポリシーを取得することが望ましい場合があります。</span><span class="sxs-lookup"><span data-stu-id="512f0-163">In some cases, it may be desirable to retrieve the default policy from a fallback `IAuthorizationPolicyProvider`.</span></span>

## <a name="fallback-policy"></a><span data-ttu-id="512f0-164">フォールバック ポリシー</span><span class="sxs-lookup"><span data-stu-id="512f0-164">Fallback policy</span></span>

<span data-ttu-id="512f0-165">カスタム`IAuthorizationPolicyProvider`は、ポリシーを組`GetFallbackPolicyAsync`み合わせるときに、ポリシーが指定されていない場合に使用[されるポリシーを提供するために](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicy.combine)、オプションで実装できます。</span><span class="sxs-lookup"><span data-stu-id="512f0-165">A custom `IAuthorizationPolicyProvider` can optionally implement `GetFallbackPolicyAsync` to provide a policy that's used when [combining policies](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicy.combine) and when no policies are specified.</span></span> <span data-ttu-id="512f0-166">NULL`GetFallbackPolicyAsync`以外のポリシーを返す場合、要求にポリシーが指定されていない場合、返されるポリシーは承認ミドルウェアによって使用されます。</span><span class="sxs-lookup"><span data-stu-id="512f0-166">If `GetFallbackPolicyAsync` returns a non-null policy, the returned policy is used by the Authorization Middleware when no policies are specified for the request.</span></span>

<span data-ttu-id="512f0-167">フォールバック ポリシーが必要ない場合、プロバイダーはフォールバック`null`プロバイダーに戻るか、または遅延できます。</span><span class="sxs-lookup"><span data-stu-id="512f0-167">If no fallback policy is required, the provider can return `null` or defer to the fallback provider:</span></span>

```csharp
public Task<AuthorizationPolicy> GetFallbackPolicyAsync() => 
    Task.FromResult<AuthorizationPolicy>(null);
```

## <a name="use-a-custom-iauthorizationpolicyprovider"></a><span data-ttu-id="512f0-168">カスタム I 承認ポリシー プロバイダーを使用します。</span><span class="sxs-lookup"><span data-stu-id="512f0-168">Use a custom IAuthorizationPolicyProvider</span></span>

<span data-ttu-id="512f0-169">からカスタム ポリシーを使用`IAuthorizationPolicyProvider`するには、次の操作を行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="512f0-169">To use custom policies from an `IAuthorizationPolicyProvider`, you must:</span></span>

* <span data-ttu-id="512f0-170">すべてのポリシー`AuthorizationHandler`ベースの承認シナリオと同様に、依存関係の挿入 ([ポリシー ベースの承認](xref:security/authorization/policies#authorization-handlers)で説明) に適切な型を登録します。</span><span class="sxs-lookup"><span data-stu-id="512f0-170">Register the appropriate `AuthorizationHandler` types with dependency injection (described in [policy-based authorization](xref:security/authorization/policies#authorization-handlers)), as with all policy-based authorization scenarios.</span></span>
* <span data-ttu-id="512f0-171">既定のポリシー`IAuthorizationPolicyProvider`プロバイダーを置き換えるために、アプリの依存関係の`Startup.ConfigureServices`差し込みサービス コレクション ( で ) にカスタムの種類を登録します。</span><span class="sxs-lookup"><span data-stu-id="512f0-171">Register the custom `IAuthorizationPolicyProvider` type in the app's dependency injection service collection (in `Startup.ConfigureServices`) to replace the default policy provider.</span></span>

```csharp
services.AddSingleton<IAuthorizationPolicyProvider, MinimumAgePolicyProvider>();
```

<span data-ttu-id="512f0-172">完全なカスタム`IAuthorizationPolicyProvider`サンプルは、[ドットネット/aspnetcore GitHub リポジトリ](https://github.com/dotnet/aspnetcore/tree/v3.1.3/src/Security/samples/CustomPolicyProvider)で入手できます。</span><span class="sxs-lookup"><span data-stu-id="512f0-172">A complete custom `IAuthorizationPolicyProvider` sample is available in the [dotnet/aspnetcore GitHub repository](https://github.com/dotnet/aspnetcore/tree/v3.1.3/src/Security/samples/CustomPolicyProvider).</span></span>
