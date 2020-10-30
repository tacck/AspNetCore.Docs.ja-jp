---
title: ASP.NET Core のカスタム承認ポリシープロバイダー
author: mjrousos
description: ASP.NET Core アプリでカスタム IAuthorizationPolicyProvider を使用して、承認ポリシーを動的に生成する方法について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 11/14/2019
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
uid: security/authorization/iauthorizationpolicyprovider
ms.openlocfilehash: 0f6f628cee0623c21a2a93aa11470005f8c78c58
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053138"
---
# <a name="custom-authorization-policy-providers-using-iauthorizationpolicyprovider-in-aspnet-core"></a>ASP.NET Core で IAuthorizationPolicyProvider を使用するカスタム承認ポリシープロバイダー 

作成者: [Mike Rousos](https://github.com/mjrousos)

通常、 [ポリシーベースの承認](xref:security/authorization/policies)を使用する場合、ポリシーは、 `AuthorizationOptions.AddPolicy` 承認サービス構成の一部としてを呼び出すことによって登録されます。 場合によっては、すべての承認ポリシーをこの方法で登録することができない (または望ましくない) ことがあります。 そのような場合は、[カスタム `IAuthorizationPolicyProvider` を使用](#ci)して、承認ポリシーの提供方法を制御できます。

カスタム [IAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider) が役に立つ可能性のあるシナリオの例を次に示します。

* 外部サービスを使用してポリシーの評価を提供する。
* 多数のポリシー (部屋番号や年齢など) を使用すると、個々の承認ポリシーを1つの呼び出しで追加することは意味がありません `AuthorizationOptions.AddPolicy` 。
* 外部データソース (データベースなど) の情報に基づいて実行時にポリシーを作成するか、別のメカニズムを使用して承認要件を動的に決定します。

[AspNetCore GitHub リポジトリ](https://github.com/dotnet/AspNetCore)から[サンプルコードを表示またはダウンロード](https://github.com/dotnet/aspnetcore/tree/v3.1.3/src/Security/samples/CustomPolicyProvider)します。 Dotnet/AspNetCore リポジトリ ZIP ファイルをダウンロードします。 ファイルを解凍します。 *Src/Security/samples/CustomPolicyProvider* プロジェクトフォルダーに移動します。

## <a name="customize-policy-retrieval"></a>ポリシーの取得のカスタマイズ

ASP.NET Core アプリは、インターフェイスの実装を使用して `IAuthorizationPolicyProvider` 承認ポリシーを取得します。 既定では、 [Defaultauthorizationpolicyprovider](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider) が登録され、使用されます。 `DefaultAuthorizationPolicyProvider` 呼び出しで指定されたからポリシーを返し `AuthorizationOptions` `IServiceCollection.AddAuthorization` ます。

この動作をカスタマイズするには `IAuthorizationPolicyProvider` 、アプリの [依存関係挿入](xref:fundamentals/dependency-injection) コンテナーに別の実装を登録します。 

インターフェイスには、 `IAuthorizationPolicyProvider` 次の3つの api が含まれます。

* [Getpolicyasync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_) は、指定された名前の承認ポリシーを返します。
* [GetDefaultPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync) では、既定の承認ポリシー (ポリシーが `[Authorize]` 指定されていない属性に使用されるポリシー) が返されます。 
* [Getfallbackpolicyasync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getfallbackpolicyasync) は、フォールバック承認ポリシー (ポリシーが指定されていない場合に、承認ミドルウェアによって使用されるポリシー) を返します。 

これらの Api を実装することで、承認ポリシーの提供方法をカスタマイズできます。

## <a name="parameterized-authorize-attribute-example"></a>パラメーター化された承認属性の例

が便利なシナリオの1つ `IAuthorizationPolicyProvider` は `[Authorize]` 、パラメーターに依存する要件を持つカスタム属性を有効にすることです。 たとえば、 [ポリシーベースの承認](xref:security/authorization/policies) ドキュメントでは、age ベース ("AtLeast21") ポリシーがサンプルとして使用されていました。 アプリ内のさまざまなコントローラーアクションを *異なる* 年齢のユーザーが使用できるようにする必要がある場合は、さまざまな年齢ベースのポリシーを作成すると便利です。 アプリケーションが必要とするさまざまな有効期間ベースのポリシーをすべて登録する代わりに、 `AuthorizationOptions` カスタムのを使用してポリシーを動的に生成でき `IAuthorizationPolicyProvider` ます。 ポリシーを簡単に使用できるようにするには、のようなカスタム承認属性を使用してアクションに注釈を設定し `[MinimumAgeAuthorize(20)]` ます。

## <a name="custom-authorization-attributes"></a>カスタム承認属性

承認ポリシーは、名前によって識別されます。 前に説明したカスタムは、 `MinimumAgeAuthorizeAttribute` 対応する承認ポリシーを取得するために使用できる文字列に引数をマップする必要があります。 これを行うには、から派生させ、プロパティをラップする必要が `AuthorizeAttribute` `Age` `AuthorizeAttribute.Policy` あります。

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

この属性の型には、ハードコーディングされた `Policy` プレフィックス ( `"MinimumAge"` ) と、コンストラクターを介して渡される整数に基づく文字列があります。

`Authorize`パラメーターとして整数を受け取る点を除いて、他の属性と同じようにアクションに適用できます。

```csharp
[MinimumAgeAuthorize(10)]
public IActionResult RequiresMinimumAge10()
```

## <a name="custom-iauthorizationpolicyprovider"></a>カスタム IAuthorizationPolicyProvider

カスタムを `MinimumAgeAuthorizeAttribute` 使用すると、必要最小限の期間に承認ポリシーを簡単に要求できます。 解決すべき次の問題は、すべての異なる年齢に対して承認ポリシーを使用できるようにすることです。 ここでは、が `IAuthorizationPolicyProvider` 役に立ちます。

を使用する場合、 `MinimumAgeAuthorizationAttribute` 承認ポリシー名はパターンに従い `"MinimumAge" + Age` ます。そのため、カスタムでは、 `IAuthorizationPolicyProvider` 次の方法で承認ポリシーを生成する必要があります。

* ポリシー名から age を解析しています。
* を使用して `AuthorizationPolicyBuilder` 新しいを作成する `AuthorizationPolicy`
* 次の例では、ユーザーがによって認証されることを前提として cookie います。 は、 `AuthorizationPolicyBuilder` 少なくとも1つの認証スキーム名を使用して構築するか、常に成功する必要があります。 そうしないと、ユーザーにチャレンジを提供する方法に関する情報はなく、例外がスローされます。
* の年齢に基づいてポリシーに要件を追加 `AuthorizationPolicyBuilder.AddRequirements` します。 他のシナリオでは、代わりに、、またはを使用することもでき `RequireClaim` `RequireRole` `RequireUserName` ます。

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

## <a name="multiple-authorization-policy-providers"></a>複数の承認ポリシープロバイダー

カスタム実装を使用する場合 `IAuthorizationPolicyProvider` 、ASP.NET Core はのインスタンスを1つだけ使用することに注意してください `IAuthorizationPolicyProvider` 。 使用されるすべてのポリシー名に対してカスタムプロバイダーが承認ポリシーを提供できない場合は、バックアッププロバイダーに従う必要があります。 

たとえば、カスタムの年齢ポリシーと従来のロールベースのポリシーの取得の両方を必要とするアプリケーションについて考えてみます。 このようなアプリでは、次のようなカスタム承認ポリシープロバイダーを使用できます。

* ポリシー名の解析を試みます。 
* `DefaultAuthorizationPolicyProvider`ポリシー名に age が含まれていない場合は、別のポリシープロバイダー (など) を呼び出します。

上の例の `IAuthorizationPolicyProvider` 実装は、コンストラクターでバックアップポリシープロバイダーを作成することによって、を使用するように更新でき `DefaultAuthorizationPolicyProvider` ます (ポリシー名が予期された "minimumage" + age のパターンと一致しない場合に使用されます)。

```csharp
private DefaultAuthorizationPolicyProvider BackupPolicyProvider { get; }

public MinimumAgePolicyProvider(IOptions<AuthorizationOptions> options)
{
    // ASP.NET Core only uses one authorization policy provider, so if the custom implementation
    // doesn't handle all policies it should fall back to an alternate provider.
    BackupPolicyProvider = new DefaultAuthorizationPolicyProvider(options);
}
```

次に、 `GetPolicyAsync` メソッドを更新して、null を返すのではなく、を使用するようにすることができ `BackupPolicyProvider` ます。

```csharp
...
return BackupPolicyProvider.GetPolicyAsync(policyName);
```

## <a name="default-policy"></a>既定のポリシー

名前付き承認ポリシーを提供するだけでなく、カスタムでを `IAuthorizationPolicyProvider` 実装し `GetDefaultPolicyAsync` て、 `[Authorize]` ポリシー名が指定されていない属性に対して承認ポリシーを提供する必要があります。

多くの場合、この承認属性には認証されたユーザーのみが必要であるため、の呼び出しで必要なポリシーを作成でき `RequireAuthenticatedUser` ます。

```csharp
public Task<AuthorizationPolicy> GetDefaultPolicyAsync() => 
    Task.FromResult(new AuthorizationPolicyBuilder(CookieAuthenticationDefaults.AuthenticationScheme).RequireAuthenticatedUser().Build());
```

カスタムのすべての側面と同様に、必要に応じ `IAuthorizationPolicyProvider` てこれをカスタマイズできます。 場合によっては、フォールバックから既定のポリシーを取得することが望ましい場合があり `IAuthorizationPolicyProvider` ます。

## <a name="fallback-policy"></a>フォールバックポリシー

カスタムは、必要に応じてを実装して、ポリシーを `IAuthorizationPolicyProvider` `GetFallbackPolicyAsync` [組み合わせる](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicy.combine) とき、およびポリシーが指定されていない場合に使用されるポリシーを提供できます。 が `GetFallbackPolicyAsync` null 以外のポリシーを返す場合、要求にポリシーが指定されていない場合は、返されたポリシーが承認ミドルウェアによって使用されます。

フォールバックポリシーが必要ない場合、プロバイダーは `null` フォールバックプロバイダーを返すか、フォールバックプロバイダーに遅延させることができます。

```csharp
public Task<AuthorizationPolicy> GetFallbackPolicyAsync() => 
    Task.FromResult<AuthorizationPolicy>(null);
```

<a name="ci"></a>

## <a name="use-a-custom-iauthorizationpolicyprovider"></a>カスタム IAuthorizationPolicyProvider を使用する

からカスタムポリシーを使用するには `IAuthorizationPolicyProvider` 、 **must** * 次のようにします。

_ ポリシーベースの `AuthorizationHandler` 承認シナリオと同様に、適切な型を依存関係の挿入 ( [ポリシーベースの承認](xref:security/authorization/policies#authorization-handlers)で説明) に登録します。
* `IAuthorizationPolicyProvider`のアプリの依存関係挿入サービスコレクションにカスタム型を登録して `Startup.ConfigureServices` 、既定のポリシープロバイダーを置き換えます。

  ```csharp
  services.AddSingleton<IAuthorizationPolicyProvider, MinimumAgePolicyProvider>();
  ```

完全なカスタム `IAuthorizationPolicyProvider` サンプルについては、 [dotnet/aspnetcore GitHub リポジトリ](https://github.com/dotnet/aspnetcore/tree/v3.1.3/src/Security/samples/CustomPolicyProvider)を参照してください。
