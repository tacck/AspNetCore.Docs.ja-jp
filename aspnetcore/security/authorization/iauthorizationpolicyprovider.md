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
# <a name="custom-authorization-policy-providers-using-iauthorizationpolicyprovider-in-aspnet-core"></a>ASP.NET コアで IAuthorizationPolicyProvider を使用するカスタム承認ポリシー プロバイダー 

作成者: [Mike Rousos](https://github.com/mjrousos)

通常、[ポリシー ベースの認可](xref:security/authorization/policies)を使用する場合、`AuthorizationOptions.AddPolicy`ポリシーは認可サービスの構成の一部として呼び出すことによって登録されます。 シナリオによっては、この方法ですべての承認ポリシーを登録できない場合があります (または望ましいとは限りません)。 このような場合は、カスタム`IAuthorizationPolicyProvider`を使用して承認ポリシーの提供方法を制御できます。

カスタム[IAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider)が役に立つシナリオの例を次に示します。

* 外部サービスを使用してポリシー評価を行う。
* さまざまな部屋番号や年齢など、さまざまな範囲のポリシーを使用するため、個々の承認ポリシーを`AuthorizationOptions.AddPolicy`呼び出しで追加しても意味がありません。
* 外部データ ソース (データベースなど) の情報に基づいて実行時にポリシーを作成するか、別のメカニズムを使用して動的に承認要件を決定する。

[AspNetCore GitHub リポジトリ](https://github.com/dotnet/AspNetCore)から[サンプル コードを表示またはダウンロード](https://github.com/dotnet/aspnetcore/tree/v3.1.3/src/Security/samples/CustomPolicyProvider)します。 ドットネット/AspNetCore リポジトリ ZIP ファイルをダウンロードします。 ファイルを解凍します。 *src/セキュリティ/サンプル/カスタムポリシープロバイダ*プロジェクトフォルダに移動します。

## <a name="customize-policy-retrieval"></a>ポリシーの取得のカスタマイズ

ASP.NET Core アプリは、インターフェイス`IAuthorizationPolicyProvider`の実装を使用して承認ポリシーを取得します。 既定では、[既定の承認ポリシー プロバイダー](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider)が登録され、使用されます。 `DefaultAuthorizationPolicyProvider`は、呼び`AuthorizationOptions`出しで`IServiceCollection.AddAuthorization`提供された からポリシーを返します。

アプリの[依存関係の注入](xref:fundamentals/dependency-injection)コンテナーに`IAuthorizationPolicyProvider`別の実装を登録することによって、この動作をカスタマイズします。 

この`IAuthorizationPolicyProvider`インターフェイスには、次の 3 つの API が含まれています。

* [指定](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_)された名前の承認ポリシーを返します。
* [既定](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync)の承認ポリシー (ポリシーが指定されていない属性に使用される`[Authorize]`ポリシー) を返します。 
* [フォールバック](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getfallbackpolicyasync)認証ポリシー (ポリシーが指定されていない場合に承認ミドルウェアによって使用されるポリシー) を返します。 

これらの API を実装することで、承認ポリシーの提供方法をカスタマイズできます。

## <a name="parameterized-authorize-attribute-example"></a>パラメーター化された承認属性の例

便利`IAuthorizationPolicyProvider`なシナリオの 1 つは`[Authorize]`、パラメーターに依存する要件を持つカスタム属性を有効にすることです。 たとえば、ポリシー[ベースの承認](xref:security/authorization/policies)ドキュメントでは、年齢ベースの ("AtLeast21") ポリシーがサンプルとして使用されました。 アプリ内の異なるコントローラーアクションを*異なる*年齢のユーザーが使用できるようにする場合は、年齢ベースのポリシーを多数用意すると便利です。 でアプリケーションが必要`AuthorizationOptions`とするさまざまな年齢ベースのポリシーをすべて登録する代わりに、カスタム`IAuthorizationPolicyProvider`でポリシーを動的に生成できます。 ポリシーを簡単に使用するには、 などの`[MinimumAgeAuthorize(20)]`カスタム承認属性を使用してアクションにアポイントを付けることができます。

## <a name="custom-authorization-attributes"></a>カスタム認証属性

許可ポリシーは、名前で識別されます。 前述の`MinimumAgeAuthorizeAttribute`カスタムは、対応する承認ポリシーを取得するために使用できる文字列に引数をマップする必要があります。 プロパティを派生`AuthorizeAttribute`し、`Age``AuthorizeAttribute.Policy`プロパティをラップさせることによってこれを行うことができます。

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

この属性型には、`Policy`ハードコーディングされたプレフィックス ( )`"MinimumAge"`に基づく文字列と、コンストラクターを介して渡される整数が含まれます。

パラメーターとして整数を受け取る以外は、他`Authorize`の属性と同じ方法でアクションに適用できます。

```csharp
[MinimumAgeAuthorize(10)]
public IActionResult RequiresMinimumAge10()
```

## <a name="custom-iauthorizationpolicyprovider"></a>カスタム I 承認ポリシープロバイダー

このカスタム`MinimumAgeAuthorizeAttribute`により、必要な最小期間に対して承認ポリシーを要求することが容易になります。 次に解決する問題は、これらの異なる年齢のすべての承認ポリシーが使用可能であることを確認することです。 ここで便利`IAuthorizationPolicyProvider`です。

を使用`MinimumAgeAuthorizationAttribute`する場合、承認ポリシー名は パターン`"MinimumAge" + Age`に従うので`IAuthorizationPolicyProvider`、カスタムは次の方法で承認ポリシーを生成する必要があります。

* ポリシー名から経過時間を解析します。
* 新規`AuthorizationPolicyBuilder`の作成に使用する`AuthorizationPolicy`
* この例と以下の例では、ユーザーがクッキーを介して認証されていると仮定します。 は`AuthorizationPolicyBuilder`、少なくとも 1 つの承認スキーム名を使用して構築するか、常に成功する必要があります。 それ以外の場合は、ユーザーにチャレンジを提供する方法に関する情報はなく、例外がスローされます。
* での経過時間に基づいてポリシーに要件`AuthorizationPolicyBuilder.AddRequirements`を追加します。 その他のシナリオでは、 、`RequireClaim``RequireRole`または`RequireUserName`を使用できます。

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

## <a name="multiple-authorization-policy-providers"></a>複数の承認ポリシー プロバイダー

カスタム`IAuthorizationPolicyProvider`実装を使用する場合、core ASP.NETは`IAuthorizationPolicyProvider`のインスタンスを 1 つだけ使用することに注意してください。 カスタム プロバイダーが、使用されるすべてのポリシー名に承認ポリシーを提供できない場合は、バックアップ プロバイダーに従う必要があります。 

たとえば、カスタムの経過時間ポリシーと従来のロールベースのポリシー取得の両方を必要とするアプリケーションを考えてみます。 このようなアプリでは、次のようなカスタム承認ポリシー プロバイダーを使用できます。

* ポリシー名の解析を試みます。 
* ポリシー名に年齢が含まれていない場合`DefaultAuthorizationPolicyProvider`は、別のポリシー プロバイダ (など) を呼び出します。

上記の`IAuthorizationPolicyProvider`実装例は、そのコンストラクターでバックアップ`DefaultAuthorizationPolicyProvider`ポリシー プロバイダーを作成することで、そのを使用するように更新できます (ポリシー名が予期されるパターンの 'MinimumAge' + age と一致しない場合に使用されます)。

```csharp
private DefaultAuthorizationPolicyProvider BackupPolicyProvider { get; }

public MinimumAgePolicyProvider(IOptions<AuthorizationOptions> options)
{
    // ASP.NET Core only uses one authorization policy provider, so if the custom implementation
    // doesn't handle all policies it should fall back to an alternate provider.
    BackupPolicyProvider = new DefaultAuthorizationPolicyProvider(options);
}
```

その後、`GetPolicyAsync`メソッドを更新して、null`BackupPolicyProvider`を返す代わりにを使用できます。

```csharp
...
return BackupPolicyProvider.GetPolicyAsync(policyName);
```

## <a name="default-policy"></a>既定のポリシー

名前付き承認ポリシーを提供することに加えて`IAuthorizationPolicyProvider`、ポリシー名`GetDefaultPolicyAsync`を指定せずに属性に対`[Authorize]`して承認ポリシーを提供するために、カスタムが実装する必要があります。

多くの場合、この認可属性は認証されたユーザーのみを必要とするため、必要なポリシーを に呼`RequireAuthenticatedUser`び出して作成できます。

```csharp
public Task<AuthorizationPolicy> GetDefaultPolicyAsync() => 
    Task.FromResult(new AuthorizationPolicyBuilder(CookieAuthenticationDefaults.AuthenticationScheme).RequireAuthenticatedUser().Build());
```

カスタム`IAuthorizationPolicyProvider`の全ての側面と同様に、必要に応じてカスタマイズできます。 場合によっては、フォールバック`IAuthorizationPolicyProvider`からデフォルトのポリシーを取得することが望ましい場合があります。

## <a name="fallback-policy"></a>フォールバック ポリシー

カスタム`IAuthorizationPolicyProvider`は、ポリシーを組`GetFallbackPolicyAsync`み合わせるときに、ポリシーが指定されていない場合に使用[されるポリシーを提供するために](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicy.combine)、オプションで実装できます。 NULL`GetFallbackPolicyAsync`以外のポリシーを返す場合、要求にポリシーが指定されていない場合、返されるポリシーは承認ミドルウェアによって使用されます。

フォールバック ポリシーが必要ない場合、プロバイダーはフォールバック`null`プロバイダーに戻るか、または遅延できます。

```csharp
public Task<AuthorizationPolicy> GetFallbackPolicyAsync() => 
    Task.FromResult<AuthorizationPolicy>(null);
```

## <a name="use-a-custom-iauthorizationpolicyprovider"></a>カスタム I 承認ポリシー プロバイダーを使用します。

からカスタム ポリシーを使用`IAuthorizationPolicyProvider`するには、次の操作を行う必要があります。

* すべてのポリシー`AuthorizationHandler`ベースの承認シナリオと同様に、依存関係の挿入 ([ポリシー ベースの承認](xref:security/authorization/policies#authorization-handlers)で説明) に適切な型を登録します。
* 既定のポリシー`IAuthorizationPolicyProvider`プロバイダーを置き換えるために、アプリの依存関係の`Startup.ConfigureServices`差し込みサービス コレクション ( で ) にカスタムの種類を登録します。

```csharp
services.AddSingleton<IAuthorizationPolicyProvider, MinimumAgePolicyProvider>();
```

完全なカスタム`IAuthorizationPolicyProvider`サンプルは、[ドットネット/aspnetcore GitHub リポジトリ](https://github.com/dotnet/aspnetcore/tree/v3.1.3/src/Security/samples/CustomPolicyProvider)で入手できます。
