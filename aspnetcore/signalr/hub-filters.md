---
title: ASP.NET Core でハブフィルターを使用するSignalR
author: brecon
description: ASP.NET Core でハブフィルターを使用する方法について説明 SignalR します。
monikerRange: '>= aspnetcore-5.0'
ms.author: brecon
ms.custom: mvc
ms.date: 05/22/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/hub-filters
ms.openlocfilehash: 26bbd175c9cd4053692d2adeca345891af0ba10e
ms.sourcegitcommit: 6371114344a5f4fbc5d4a119b0be1ad3762e0216
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/10/2020
ms.locfileid: "84680244"
---
# <a name="use-hub-filters-in-aspnet-core-signalr"></a>ASP.NET Core でハブフィルターを使用するSignalR

ハブフィルター:

* は ASP.NET Core 5.0 以降で使用できます。
* ハブメソッドがクライアントによって呼び出される前と後のロジックの実行を許可します。

この記事では、ハブフィルターの作成と使用に関するガイダンスを提供します。

## <a name="configure-hub-filters"></a>ハブフィルターの構成

ハブフィルターは、グローバルまたはハブの種類ごとに適用できます。 フィルターを追加する順序は、フィルターが実行される順序です。 グローバルハブフィルターは、ローカルのハブフィルターの前に実行します。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(options =>
    {
        // Global filters will run first
        options.AddFilter<CustomFilter>();
    }).AddHubOptions<MyHub>(options =>
    {
        // Local filters will run second
        options.AddFilter<CustomFilter2>();
    });
}
```

ハブフィルターは、次のいずれかの方法で追加できます。

* 具象型によるフィルターの追加:

    ```csharp
    hubOptions.AddFilter<TFilter>();
    ```

    これは、依存関係の挿入 (DI) またはアクティブ化された型によって解決されます。

* ランタイム型によるフィルターの追加:

    ```csharp
    hubOptions.AddFilter(typeof(TFilter));
    ```

    これは、DI またはアクティブ化された型から解決されます。

* インスタンスによるフィルターの追加:

    ```csharp
    hubOptions.AddFilter(new MyFilter());
    ```

    このインスタンスは、シングルトンのように使用されます。 すべてのハブメソッドの呼び出しで同じインスタンスが使用されます。

ハブのフィルターは、ハブの呼び出しごとに作成および破棄されます。 グローバル状態をフィルターに格納する場合、または状態を指定しない場合は、パフォーマンスを向上させるために、ハブフィルターの種類をシングルトンとして DI に追加します。 または、可能な場合は、フィルターをインスタンスとして追加します。

## <a name="create-hub-filters"></a>ハブフィルターを作成する

を継承するクラスを宣言してフィルターを作成 `IHubFilter` し、メソッドを追加し `InvokeMethodAsync` ます。 また、との各 `OnConnectedAsync` `OnDisconnectedAsync` メソッドをラップするために、必要に応じてを実装することもでき `OnConnectedAsync` `OnDisconnectedAsync` ます。

```csharp
public class CustomFilter : IHubFilter
{
    public async ValueTask<object> InvokeMethodAsync(
        HubInvocationContext invocationContext, Func<HubInvocationContext, ValueTask<object>> next)
    {
        Console.WriteLine($"Calling hub method '{invocationContext.HubMethodName}'");
        try
        {
            return await next(invocationContext);
        }
        catch (Exception ex)
        {
            Console.WriteLine($"Exception calling '{invocationContext.HubMethodName}'");
            throw ex;
        }
    }

    // Optional method
    public Task OnConnectedAsync(HubLifetimeContext context, Func<HubLifetimeContext, Task> next)
    {
        return next(context);
    }

    // Optional method
    public Task OnDisconnectedAsync(
        HubLifetimeContext context, Exception exception, Func<HubLifetimeContext, Exception, Task> next)
    {
        return next(context, exception);
    }
}
```

フィルターはミドルウェアとよく似ています。 メソッドは、 `next` 次のフィルターを呼び出します。 最後のフィルターは、ハブメソッドを呼び出します。 また、 `next` から結果を返す前に、ハブメソッドが呼び出された後で、ロジックを待機して実行した結果を格納することもでき `next` ます。

フィルターでハブメソッドの呼び出しをスキップするには、を呼び出すのではなく、型の例外をスローし `HubException` `next` ます。 結果が予期されていた場合、クライアントはエラーを受け取ります。

## <a name="use-hub-filters"></a>ハブフィルターを使用する

フィルターロジックを記述するときは、ハブメソッド名をチェックするのではなく、ハブメソッドの属性を使用して、ジェネリックにしてください。

禁止語句のハブメソッド引数をチェックし、見つかった句を置き換えるフィルターを考えてみ `***` ます。
この例で `LanguageFilterAttribute` は、クラスが定義されているとします。 クラスには、 `FilterArgument` 属性を使用するときに設定できるという名前のプロパティがあります。

1. クリーニングする文字列引数を持つハブメソッドに属性を配置します。

    ```csharp
    public class ChatHub
    {
        [LanguageFilter(filterArgument: 0)]
        public async Task SendMessage(string message, string username)
        {
            await Clients.All.SendAsync("SendMessage", $"{username} says: {message}");
        }
    }
    ```

1. ハブフィルターを定義して属性を確認し、ハブメソッド引数の禁止された語句を次のように置き換え `***` ます。

    ```csharp
    public class LanguageFilter : IHubFilter
    {
        // populated from a file or inline
        private List<string> bannedPhrases = new List<string> { "async void", ".Result" };

        public async ValueTask<object> InvokeMethodAsync(HubInvocationContext invocationContext, 
            Func<HubInvocationContext, ValueTask<object>> next)
        {
            var languageFilter = (LanguageFilterAttribute)Attribute.GetCustomAttribute(
                invocationContext.HubMethod, typeof(LanguageFilterAttribute));
            if (languageFilter != null &&
                invocationContext.HubMethodArguments.Count > languageFilter.FilterArgument &&
                invocationContext.HubMethodArguments[languageFilter.FilterArgument] is string str)
            {
                foreach (var bannedPhrase in bannedPhrases)
                {
                    str = str.Replace(bannedPhrase, "***");
                }

                arguments = invocationContext.HubMethodArguments.ToArray();
                arguments[languageFilter.FilterArgument] = str;
                invocationContext = new HubInvocationContext(invocationContext.Context,
                    invocationContext.ServiceProvider,
                    invocationContext.Hub,
                    invocationContext.HubMethod,
                    arguments);
            }

            return await next(invocationContext);
        }
    }
    ```

1. メソッドにハブフィルターを登録し `Startup.ConfigureServices` ます。 すべての呼び出しに対して禁止語句の一覧の再初期化を避けるために、ハブフィルターはシングルトンとして登録されます。

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSignalR(hubOptions =>
        {
            hubOptions.AddFilter<LanguageFilter>();
        });
    
        services.AddSingleton<LanguageFilter>();
    }
    ```

## <a name="the-hubinvocationcontext-object"></a>HubInvocationContext オブジェクト

には、 `HubInvocationContext` 現在のハブメソッドの呼び出しに関する情報が含まれています。

| プロパティ | 説明 | 種類 |
| ------ | ------ | ----------- |
| `Context ` | には、 `HubCallerContext` 接続に関する情報が含まれています。 | `HubCallerContext` |
| `Hub` | このハブメソッドの呼び出しに使用されているハブのインスタンス。 | `Hub` |
| `HubMethodName` | 呼び出されるハブメソッドの名前。 | `string` |
| `HubMethodArguments` | ハブメソッドに渡される引数のリスト。 | `IReadOnlyList<string>` |
| `ServiceProvider` | このハブメソッド呼び出しのスコープ付きサービスプロバイダー。 | `IServiceProvider` |
| `HubMethod` | ハブメソッド情報。 | `MethodInfo` |

## <a name="the-hublifetimecontext-object"></a>、のオブジェクト

には、 `HubLifetimeContext` およびハブメソッドの情報が含まれてい `OnConnectedAsync` `OnDisconnectedAsync` ます。

| プロパティ | 説明 | 種類 |
| ------ | ------ | ----------- |
| `Context ` | には、 `HubCallerContext` 接続に関する情報が含まれています。 | `HubCallerContext` |
| `Hub` | このハブメソッドの呼び出しに使用されているハブのインスタンス。 | `Hub` |
| `ServiceProvider` | このハブメソッド呼び出しのスコープ付きサービスプロバイダー。 | `IServiceProvider` |

## <a name="authorization-and-filters"></a>承認とフィルター

ハブフィルターの前に実行される[ハブメソッドの承認属性](xref:signalr/authn-and-authz#use-authorization-handlers-to-customize-hub-method-authorization)。
