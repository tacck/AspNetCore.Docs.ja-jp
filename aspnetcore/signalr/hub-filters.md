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
ms.openlocfilehash: afdb52039c0eff53a421038518c687c78e1d509b
ms.sourcegitcommit: a423e8fcde4b6181a3073ed646a603ba20bfa5f9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2020
ms.locfileid: "84756068"
---
# <a name="use-hub-filters-in-aspnet-core-signalr"></a><span data-ttu-id="927b9-103">ASP.NET Core でハブフィルターを使用するSignalR</span><span class="sxs-lookup"><span data-stu-id="927b9-103">Use hub filters in ASP.NET Core SignalR</span></span>

<span data-ttu-id="927b9-104">ハブフィルター:</span><span class="sxs-lookup"><span data-stu-id="927b9-104">Hub filters:</span></span>

* <span data-ttu-id="927b9-105">は ASP.NET Core 5.0 以降で使用できます。</span><span class="sxs-lookup"><span data-stu-id="927b9-105">Are available in ASP.NET Core 5.0 or later.</span></span>
* <span data-ttu-id="927b9-106">ハブメソッドがクライアントによって呼び出される前と後のロジックの実行を許可します。</span><span class="sxs-lookup"><span data-stu-id="927b9-106">Allow logic to run before and after hub methods are invoked by clients.</span></span>

<span data-ttu-id="927b9-107">この記事では、ハブフィルターの作成と使用に関するガイダンスを提供します。</span><span class="sxs-lookup"><span data-stu-id="927b9-107">This article provides guidance for writing and using hub filters.</span></span>

## <a name="configure-hub-filters"></a><span data-ttu-id="927b9-108">ハブフィルターの構成</span><span class="sxs-lookup"><span data-stu-id="927b9-108">Configure hub filters</span></span>

<span data-ttu-id="927b9-109">ハブフィルターは、グローバルまたはハブの種類ごとに適用できます。</span><span class="sxs-lookup"><span data-stu-id="927b9-109">Hub filters can be applied globally or per hub type.</span></span> <span data-ttu-id="927b9-110">フィルターを追加する順序は、フィルターが実行される順序です。</span><span class="sxs-lookup"><span data-stu-id="927b9-110">The order in which filters are added is the order in which the filters run.</span></span> <span data-ttu-id="927b9-111">グローバルハブフィルターは、ローカルのハブフィルターの前に実行します。</span><span class="sxs-lookup"><span data-stu-id="927b9-111">Global hub filters run before local hub filters.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(options =>
    {
        // Global filters will run first
        options.AddFilter<CustomFilter>();
    }).AddHubOptions<ChatHub>(options =>
    {
        // Local filters will run second
        options.AddFilter<CustomFilter2>();
    });
}
```

<span data-ttu-id="927b9-112">ハブフィルターは、次のいずれかの方法で追加できます。</span><span class="sxs-lookup"><span data-stu-id="927b9-112">A hub filter can be added in one of the following ways:</span></span>

* <span data-ttu-id="927b9-113">具象型によるフィルターの追加:</span><span class="sxs-lookup"><span data-stu-id="927b9-113">Add a filter by concrete type:</span></span>

    ```csharp
    hubOptions.AddFilter<TFilter>();
    ```

    <span data-ttu-id="927b9-114">これは、依存関係の挿入 (DI) またはアクティブ化された型によって解決されます。</span><span class="sxs-lookup"><span data-stu-id="927b9-114">This will be resolved from dependency injection (DI) or type activated.</span></span>

* <span data-ttu-id="927b9-115">ランタイム型によるフィルターの追加:</span><span class="sxs-lookup"><span data-stu-id="927b9-115">Add a filter by runtime type:</span></span>

    ```csharp
    hubOptions.AddFilter(typeof(TFilter));
    ```

    <span data-ttu-id="927b9-116">これは、DI またはアクティブ化された型から解決されます。</span><span class="sxs-lookup"><span data-stu-id="927b9-116">This will be resolved from DI or type activated.</span></span>

* <span data-ttu-id="927b9-117">インスタンスによるフィルターの追加:</span><span class="sxs-lookup"><span data-stu-id="927b9-117">Add a filter by instance:</span></span>

    ```csharp
    hubOptions.AddFilter(new MyFilter());
    ```

    <span data-ttu-id="927b9-118">このインスタンスは、シングルトンのように使用されます。</span><span class="sxs-lookup"><span data-stu-id="927b9-118">This instance will be used like a singleton.</span></span> <span data-ttu-id="927b9-119">すべてのハブメソッドの呼び出しで同じインスタンスが使用されます。</span><span class="sxs-lookup"><span data-stu-id="927b9-119">All hub method invocations will use the same instance.</span></span>

<span data-ttu-id="927b9-120">ハブのフィルターは、ハブの呼び出しごとに作成および破棄されます。</span><span class="sxs-lookup"><span data-stu-id="927b9-120">Hub filters are created and disposed per hub invocation.</span></span> <span data-ttu-id="927b9-121">グローバル状態をフィルターに格納する場合、または状態を指定しない場合は、パフォーマンスを向上させるために、ハブフィルターの種類をシングルトンとして DI に追加します。</span><span class="sxs-lookup"><span data-stu-id="927b9-121">If you want to store global state in the filter, or no state, add the hub filter type to DI as a singleton for better performance.</span></span> <span data-ttu-id="927b9-122">または、可能な場合は、フィルターをインスタンスとして追加します。</span><span class="sxs-lookup"><span data-stu-id="927b9-122">Alternatively, add the filter as an instance if you can.</span></span>

## <a name="create-hub-filters"></a><span data-ttu-id="927b9-123">ハブフィルターを作成する</span><span class="sxs-lookup"><span data-stu-id="927b9-123">Create hub filters</span></span>

<span data-ttu-id="927b9-124">を継承するクラスを宣言してフィルターを作成 `IHubFilter` し、メソッドを追加し `InvokeMethodAsync` ます。</span><span class="sxs-lookup"><span data-stu-id="927b9-124">Create a filter by declaring a class that inherits from `IHubFilter`, and add the `InvokeMethodAsync` method.</span></span> <span data-ttu-id="927b9-125">また、との各 `OnConnectedAsync` `OnDisconnectedAsync` メソッドをラップするために、必要に応じてを実装することもでき `OnConnectedAsync` `OnDisconnectedAsync` ます。</span><span class="sxs-lookup"><span data-stu-id="927b9-125">There is also `OnConnectedAsync` and `OnDisconnectedAsync` that can optionally be implemented to wrap the `OnConnectedAsync` and `OnDisconnectedAsync` hub methods respectively.</span></span>

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

<span data-ttu-id="927b9-126">フィルターはミドルウェアとよく似ています。</span><span class="sxs-lookup"><span data-stu-id="927b9-126">Filters are very similar to middleware.</span></span> <span data-ttu-id="927b9-127">メソッドは、 `next` 次のフィルターを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="927b9-127">The `next` method invokes the next filter.</span></span> <span data-ttu-id="927b9-128">最後のフィルターは、ハブメソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="927b9-128">The final filter will invoke the hub method.</span></span> <span data-ttu-id="927b9-129">また、 `next` から結果を返す前に、ハブメソッドが呼び出された後で、ロジックを待機して実行した結果を格納することもでき `next` ます。</span><span class="sxs-lookup"><span data-stu-id="927b9-129">Filters can also store the result from awaiting `next` and run logic after the hub method has been called before returning the result from `next`.</span></span>

<span data-ttu-id="927b9-130">フィルターでハブメソッドの呼び出しをスキップするには、を呼び出すのではなく、型の例外をスローし `HubException` `next` ます。</span><span class="sxs-lookup"><span data-stu-id="927b9-130">To skip a hub method invocation in a filter, throw an exception of type `HubException` instead of calling `next`.</span></span> <span data-ttu-id="927b9-131">結果が予期されていた場合、クライアントはエラーを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="927b9-131">The client will receive an error if it was expecting a result.</span></span>

## <a name="use-hub-filters"></a><span data-ttu-id="927b9-132">ハブフィルターを使用する</span><span class="sxs-lookup"><span data-stu-id="927b9-132">Use hub filters</span></span>

<span data-ttu-id="927b9-133">フィルターロジックを記述するときは、ハブメソッド名をチェックするのではなく、ハブメソッドの属性を使用して、ジェネリックにしてください。</span><span class="sxs-lookup"><span data-stu-id="927b9-133">When writing the filter logic, try to make it generic by using attributes on hub methods instead of checking for hub method names.</span></span>

<span data-ttu-id="927b9-134">禁止語句のハブメソッド引数をチェックし、見つかった句を置き換えるフィルターを考えてみ `***` ます。</span><span class="sxs-lookup"><span data-stu-id="927b9-134">Consider a filter that will check a hub method argument for banned phrases and replace any phrases it finds with `***`.</span></span>
<span data-ttu-id="927b9-135">この例で `LanguageFilterAttribute` は、クラスが定義されているとします。</span><span class="sxs-lookup"><span data-stu-id="927b9-135">For this example, assume a `LanguageFilterAttribute` class is defined.</span></span> <span data-ttu-id="927b9-136">クラスには、 `FilterArgument` 属性を使用するときに設定できるという名前のプロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="927b9-136">The class has a property named `FilterArgument` that can be set when using the attribute.</span></span>

1. <span data-ttu-id="927b9-137">クリーニングする文字列引数を持つハブメソッドに属性を配置します。</span><span class="sxs-lookup"><span data-stu-id="927b9-137">Place the attribute on the hub method that has a string argument to be cleaned:</span></span>

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

1. <span data-ttu-id="927b9-138">ハブフィルターを定義して属性を確認し、ハブメソッド引数の禁止された語句を次のように置き換え `***` ます。</span><span class="sxs-lookup"><span data-stu-id="927b9-138">Define a hub filter to check for the attribute and replace banned phrases in a hub method argument with `***`:</span></span>

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

1. <span data-ttu-id="927b9-139">メソッドにハブフィルターを登録し `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="927b9-139">Register the hub filter in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="927b9-140">すべての呼び出しに対して禁止語句の一覧の再初期化を避けるために、ハブフィルターはシングルトンとして登録されます。</span><span class="sxs-lookup"><span data-stu-id="927b9-140">To avoid reinitializing the banned phrases list for every invocation, the hub filter is registered as a singleton:</span></span>

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

## <a name="the-hubinvocationcontext-object"></a><span data-ttu-id="927b9-141">HubInvocationContext オブジェクト</span><span class="sxs-lookup"><span data-stu-id="927b9-141">The HubInvocationContext object</span></span>

<span data-ttu-id="927b9-142">には、 `HubInvocationContext` 現在のハブメソッドの呼び出しに関する情報が含まれています。</span><span class="sxs-lookup"><span data-stu-id="927b9-142">The `HubInvocationContext` contains information for the current hub method invocation.</span></span>

| <span data-ttu-id="927b9-143">プロパティ</span><span class="sxs-lookup"><span data-stu-id="927b9-143">Property</span></span> | <span data-ttu-id="927b9-144">説明</span><span class="sxs-lookup"><span data-stu-id="927b9-144">Description</span></span> | <span data-ttu-id="927b9-145">Type</span><span class="sxs-lookup"><span data-stu-id="927b9-145">Type</span></span> |
| ------ | ------ | ----------- |
| `Context ` | <span data-ttu-id="927b9-146">には、 `HubCallerContext` 接続に関する情報が含まれています。</span><span class="sxs-lookup"><span data-stu-id="927b9-146">The `HubCallerContext` contains information about the connection.</span></span> | `HubCallerContext` |
| `Hub` | <span data-ttu-id="927b9-147">このハブメソッドの呼び出しに使用されているハブのインスタンス。</span><span class="sxs-lookup"><span data-stu-id="927b9-147">The instance of the Hub being used for this hub method invocation.</span></span> | `Hub` |
| `HubMethodName` | <span data-ttu-id="927b9-148">呼び出されるハブメソッドの名前。</span><span class="sxs-lookup"><span data-stu-id="927b9-148">The name of the hub method being invoked.</span></span> | `string` |
| `HubMethodArguments` | <span data-ttu-id="927b9-149">ハブメソッドに渡される引数のリスト。</span><span class="sxs-lookup"><span data-stu-id="927b9-149">The list of arguments being passed to the hub method.</span></span> | `IReadOnlyList<string>` |
| `ServiceProvider` | <span data-ttu-id="927b9-150">このハブメソッド呼び出しのスコープ付きサービスプロバイダー。</span><span class="sxs-lookup"><span data-stu-id="927b9-150">The scoped service provider for this hub method invocation.</span></span> | `IServiceProvider` |
| `HubMethod` | <span data-ttu-id="927b9-151">ハブメソッド情報。</span><span class="sxs-lookup"><span data-stu-id="927b9-151">The hub method information.</span></span> | `MethodInfo` |

## <a name="the-hublifetimecontext-object"></a><span data-ttu-id="927b9-152">、のオブジェクト</span><span class="sxs-lookup"><span data-stu-id="927b9-152">The HubLifetimeContext object</span></span>

<span data-ttu-id="927b9-153">には、 `HubLifetimeContext` およびハブメソッドの情報が含まれてい `OnConnectedAsync` `OnDisconnectedAsync` ます。</span><span class="sxs-lookup"><span data-stu-id="927b9-153">The `HubLifetimeContext` contains information for the `OnConnectedAsync` and `OnDisconnectedAsync` hub methods.</span></span>

| <span data-ttu-id="927b9-154">プロパティ</span><span class="sxs-lookup"><span data-stu-id="927b9-154">Property</span></span> | <span data-ttu-id="927b9-155">説明</span><span class="sxs-lookup"><span data-stu-id="927b9-155">Description</span></span> | <span data-ttu-id="927b9-156">Type</span><span class="sxs-lookup"><span data-stu-id="927b9-156">Type</span></span> |
| ------ | ------ | ----------- |
| `Context ` | <span data-ttu-id="927b9-157">には、 `HubCallerContext` 接続に関する情報が含まれています。</span><span class="sxs-lookup"><span data-stu-id="927b9-157">The `HubCallerContext` contains information about the connection.</span></span> | `HubCallerContext` |
| `Hub` | <span data-ttu-id="927b9-158">このハブメソッドの呼び出しに使用されているハブのインスタンス。</span><span class="sxs-lookup"><span data-stu-id="927b9-158">The instance of the Hub being used for this hub method invocation.</span></span> | `Hub` |
| `ServiceProvider` | <span data-ttu-id="927b9-159">このハブメソッド呼び出しのスコープ付きサービスプロバイダー。</span><span class="sxs-lookup"><span data-stu-id="927b9-159">The scoped service provider for this hub method invocation.</span></span> | `IServiceProvider` |

## <a name="authorization-and-filters"></a><span data-ttu-id="927b9-160">承認とフィルター</span><span class="sxs-lookup"><span data-stu-id="927b9-160">Authorization and filters</span></span>

<span data-ttu-id="927b9-161">ハブフィルターの前に実行される[ハブメソッドの承認属性](xref:signalr/authn-and-authz#use-authorization-handlers-to-customize-hub-method-authorization)。</span><span class="sxs-lookup"><span data-stu-id="927b9-161">[Authorize attributes on hub methods](xref:signalr/authn-and-authz#use-authorization-handlers-to-customize-hub-method-authorization) run before hub filters.</span></span>
