---
title: ASP.NET Core Blazor アプリのエラーを処理する
author: guardrex
description: ASP.NET Core Blazor で、ハンドルされない例外を Blazor で管理する方法と、エラーを検出して処理するアプリを開発する方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/handle-errors
ms.openlocfilehash: 7eefc988da275c0efccd97958d04e76f04e7cad4
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82772114"
---
# <a name="handle-errors-in-aspnet-core-blazor-apps"></a><span data-ttu-id="a2dfa-103">ASP.NET Core Blazor アプリのエラーを処理する</span><span class="sxs-lookup"><span data-stu-id="a2dfa-103">Handle errors in ASP.NET Core Blazor apps</span></span>

<span data-ttu-id="a2dfa-104">作成者: [Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="a2dfa-104">By [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

<span data-ttu-id="a2dfa-105">この記事では、ハンドルされない例外を Blazor で管理する方法と、エラーを検出して処理するアプリを開発する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-105">This article describes how Blazor manages unhandled exceptions and how to develop apps that detect and handle errors.</span></span>

## <a name="detailed-errors-during-development"></a><span data-ttu-id="a2dfa-106">開発中の詳細なエラー</span><span class="sxs-lookup"><span data-stu-id="a2dfa-106">Detailed errors during development</span></span>

<span data-ttu-id="a2dfa-107">開発中に Blazor アプリが正常に機能していない場合、アプリからの詳細なエラー情報を受け取ることで、問題のトラブルシューティングと修正に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-107">When a Blazor app isn't functioning properly during development, receiving detailed error information from the app assists in troubleshooting and fixing the issue.</span></span> <span data-ttu-id="a2dfa-108">エラーが発生すると Blazor アプリによって画面の下部に金色のバーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-108">When an error occurs, Blazor apps display a gold bar at the bottom of the screen:</span></span>

* <span data-ttu-id="a2dfa-109">開発中は、金色のバーによってブラウザー コンソールが表示され、そこで例外を確認できます。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-109">During development, the gold bar directs you to the browser console, where you can see the exception.</span></span>
* <span data-ttu-id="a2dfa-110">実稼働環境では、金色のバーによって、エラーが発生したことがユーザーに通知され、ブラウザーの更新が推奨されます。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-110">In production, the gold bar notifies the user that an error has occurred and recommends refreshing the browser.</span></span>

<span data-ttu-id="a2dfa-111">このエラー処理エクスペリエンスの UI は、Blazor プロジェクト テンプレートの一部です。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-111">The UI for this error handling experience is part of the Blazor project templates.</span></span>

<span data-ttu-id="a2dfa-112">Blazor WebAssembly アプリでは、*wwwroot/index.html* ファイルでエクスペリエンスをカスタマイズします。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-112">In a Blazor WebAssembly app, customize the experience in the *wwwroot/index.html* file:</span></span>

```html
<div id="blazor-error-ui">
    An unhandled error has occurred.
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

<span data-ttu-id="a2dfa-113">Blazor Server アプリでは、*Pages/_Host.cshtml* ファイルでエクスペリエンスをカスタマイズします。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-113">In a Blazor Server app, customize the experience in the *Pages/_Host.cshtml* file:</span></span>

```cshtml
<div id="blazor-error-ui">
    <environment include="Staging,Production">
        An error has occurred. This application may no longer respond until reloaded.
    </environment>
    <environment include="Development">
        An unhandled exception has occurred. See browser dev tools for details.
    </environment>
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

<span data-ttu-id="a2dfa-114">`blazor-error-ui` 要素は、Blazor テンプレート (*wwwroot/css/site.css*) に含まれるスタイルによって非表示にされ、エラーが発生したときに表示されます。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-114">The `blazor-error-ui` element is hidden by the styles included in the Blazor templates (*wwwroot/css/site.css*) and then shown when an error occurs:</span></span>

```css
#blazor-error-ui {
    background: lightyellow;
    bottom: 0;
    box-shadow: 0 -1px 2px rgba(0, 0, 0, 0.2);
    display: none;
    left: 0;
    padding: 0.6rem 1.25rem 0.7rem 1.25rem;
    position: fixed;
    width: 100%;
    z-index: 1000;
}

#blazor-error-ui .dismiss {
    cursor: pointer;
    position: absolute;
    right: 0.75rem;
    top: 0.5rem;
}
```

## <a name="how-a-blazor-server-app-reacts-to-unhandled-exceptions"></a><span data-ttu-id="a2dfa-115">ハンドルされない例外に対して Blazor Server アプリがどのように反応するか</span><span class="sxs-lookup"><span data-stu-id="a2dfa-115">How a Blazor Server app reacts to unhandled exceptions</span></span>

Blazor<span data-ttu-id="a2dfa-116"> Server はステートフルなフレームワークです。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-116"> Server is a stateful framework.</span></span> <span data-ttu-id="a2dfa-117">ユーザーはアプリを操作するときに、*回線*と呼ばれる、サーバーへの接続を維持します。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-117">While users interact with an app, they maintain a connection to the server known as a *circuit*.</span></span> <span data-ttu-id="a2dfa-118">回線では、アクティブなコンポーネント インスタンスに加えて、次のような状態の他の多くの側面が保持されます。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-118">The circuit holds active component instances, plus many other aspects of state, such as:</span></span>

* <span data-ttu-id="a2dfa-119">コンポーネントの表示される最新の出力。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-119">The most recent rendered output of components.</span></span>
* <span data-ttu-id="a2dfa-120">クライアント側のイベントによってトリガーされる可能性がある、イベント処理デリゲートの現在のセット。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-120">The current set of event-handling delegates that could be triggered by client-side events.</span></span>

<span data-ttu-id="a2dfa-121">ユーザーが複数のブラウザー タブでアプリを開いた場合は、独立した回線が複数あります。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-121">If a user opens the app in multiple browser tabs, they have multiple independent circuits.</span></span>

Blazor<span data-ttu-id="a2dfa-122"> は、ハンドルされない例外が発生した回線に対して、ほとんどの例外を致命的として処理します。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-122"> treats most unhandled exceptions as fatal to the circuit where they occur.</span></span> <span data-ttu-id="a2dfa-123">ハンドルされない例外のために回線が終了された場合、ユーザーはページを再読み込みして新しい回線を作成するだけで、アプリの操作を続行できます。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-123">If a circuit is terminated due to an unhandled exception, the user can only continue to interact with the app by reloading the page to create a new circuit.</span></span> <span data-ttu-id="a2dfa-124">他のユーザーや他のブラウザー タブの回線である、終了された回線以外の回線は影響を受けません。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-124">Circuits outside of the one that's terminated, which are circuits for other users or other browser tabs, aren't affected.</span></span> <span data-ttu-id="a2dfa-125">このシナリオは、クラッシュしたデスクトップ アプリに似ています。クラッシュしたアプリを再起動する必要がありますが、他のアプリは影響を受けません。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-125">This scenario is similar to a desktop app that crashes&mdash;the crashed app must be restarted, but other apps aren't affected.</span></span>

<span data-ttu-id="a2dfa-126">回線は、以下の理由でハンドルされない例外が発生したときに終了されます。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-126">A circuit is terminated when an unhandled exception occurs for the following reasons:</span></span>

* <span data-ttu-id="a2dfa-127">ハンドルされない例外によって、回線が未定義の状態のままになることがよくあります。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-127">An unhandled exception often leaves the circuit in an undefined state.</span></span>
* <span data-ttu-id="a2dfa-128">ハンドルされない例外の後は、アプリの通常動作を保証できません。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-128">The app's normal operation can't be guaranteed after an unhandled exception.</span></span>
* <span data-ttu-id="a2dfa-129">回線が存続している場合は、アプリにセキュリティの脆弱性が表れることがあります。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-129">Security vulnerabilities may appear in the app if the circuit continues.</span></span>

## <a name="manage-unhandled-exceptions-in-developer-code"></a><span data-ttu-id="a2dfa-130">ハンドルされない例外を開発者コードで管理する</span><span class="sxs-lookup"><span data-stu-id="a2dfa-130">Manage unhandled exceptions in developer code</span></span>

<span data-ttu-id="a2dfa-131">エラー後にアプリを続行するには、アプリがエラー処理ロジックを備えている必要があります。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-131">For an app to continue after an error, the app must have error handling logic.</span></span> <span data-ttu-id="a2dfa-132">この記事の後のセクションでは、ハンドルされない例外の潜在的原因について説明します。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-132">Later sections of this article describe potential sources of unhandled exceptions.</span></span>

<span data-ttu-id="a2dfa-133">運用環境では、フレームワークの例外メッセージやスタック トレースを UI に表示しないでください。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-133">In production, don't render framework exception messages or stack traces in the UI.</span></span> <span data-ttu-id="a2dfa-134">例外メッセージやスタック トレースを表示すると以下の可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-134">Rendering exception messages or stack traces could:</span></span>

* <span data-ttu-id="a2dfa-135">エンド ユーザーに機密情報が開示される。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-135">Disclose sensitive information to end users.</span></span>
* <span data-ttu-id="a2dfa-136">悪意のあるユーザーが、アプリ、サーバー、またはネットワークのセキュリティを侵害する可能性のある脆弱性をアプリの中で発見する助けになる。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-136">Help a malicious user discover weaknesses in an app that can compromise the security of the app, server, or network.</span></span>

## <a name="log-errors-with-a-persistent-provider"></a><span data-ttu-id="a2dfa-137">永続的プロバイダーを使用してエラーをログに記録する</span><span class="sxs-lookup"><span data-stu-id="a2dfa-137">Log errors with a persistent provider</span></span>

<span data-ttu-id="a2dfa-138">ハンドルされない例外が発生した場合、例外は、サービス コンテナー内に構成されている <xref:Microsoft.Extensions.Logging.ILogger> インスタンスにログ記録されます。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-138">If an unhandled exception occurs, the exception is logged to <xref:Microsoft.Extensions.Logging.ILogger> instances configured in the service container.</span></span> <span data-ttu-id="a2dfa-139">既定では、Blazor アプリは、コンソール ログ プロバイダーを使用してコンソール出力にログを記録します。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-139">By default, Blazor apps log to console output with the Console Logging Provider.</span></span> <span data-ttu-id="a2dfa-140">ログ サイズやログのローテーションを管理するプロバイダーを使用して、より永続的な場所にログを記録することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-140">Consider logging to a more permanent location with a provider that manages log size and log rotation.</span></span> <span data-ttu-id="a2dfa-141">詳細については、「<xref:fundamentals/logging/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-141">For more information, see <xref:fundamentals/logging/index>.</span></span>

<span data-ttu-id="a2dfa-142">開発中、Blazor は通常、デバッグの助けになるよう、ブラウザーのコンソールに例外の完全な詳細情報を送信します。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-142">During development, Blazor usually sends the full details of exceptions to the browser's console to aid in debugging.</span></span> <span data-ttu-id="a2dfa-143">運用環境では、ブラウザー コンソールの詳細なエラーは既定で無効になっています。つまり、エラーはクライアントに送信されませんが、例外の完全な詳細はサーバー側でログに記録され続けます。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-143">In production, detailed errors in the browser's console are disabled by default, which means that errors aren't sent to clients but the exception's full details are still logged server-side.</span></span> <span data-ttu-id="a2dfa-144">詳細については、「<xref:fundamentals/error-handling>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-144">For more information, see <xref:fundamentals/error-handling>.</span></span>

<span data-ttu-id="a2dfa-145">ログに記録するインシデントと、ログに記録されるインシデントの重大度レベルを決定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-145">You must decide which incidents to log and the level of severity of logged incidents.</span></span> <span data-ttu-id="a2dfa-146">悪意のあるユーザーが、意図的にエラーをトリガーできる可能性もあります。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-146">Hostile users might be able to trigger errors deliberately.</span></span> <span data-ttu-id="a2dfa-147">たとえば、製品の詳細を表示するコンポーネントの URL に不明な `ProductId` が指定されているエラーのインシデントは、ログに記録しないようにします。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-147">For example, don't log an incident from an error where an unknown `ProductId` is supplied in the URL of a component that displays product details.</span></span> <span data-ttu-id="a2dfa-148">ログ記録では、すべてのエラーを重大度の高いインシデントとして扱わないようにしてください。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-148">Not all errors should be treated as high-severity incidents for logging.</span></span>

<span data-ttu-id="a2dfa-149">詳細については、「<xref:fundamentals/logging/index#create-logs-in-blazor>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-149">For more information, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>

## <a name="places-where-errors-may-occur"></a><span data-ttu-id="a2dfa-150">エラーが発生する可能性のある場所</span><span class="sxs-lookup"><span data-stu-id="a2dfa-150">Places where errors may occur</span></span>

<span data-ttu-id="a2dfa-151">ハンドルされない例外は、以下のいずれかの場所で、フレームワークとアプリ コードによってトリガーされることがあります。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-151">Framework and app code may trigger unhandled exceptions in any of the following locations:</span></span>

* [<span data-ttu-id="a2dfa-152">コンポーネントのインスタンス化</span><span class="sxs-lookup"><span data-stu-id="a2dfa-152">Component instantiation</span></span>](#component-instantiation)
* [<span data-ttu-id="a2dfa-153">ライフサイクル メソッド</span><span class="sxs-lookup"><span data-stu-id="a2dfa-153">Lifecycle methods</span></span>](#lifecycle-methods)
* [<span data-ttu-id="a2dfa-154">レンダリング ロジック</span><span class="sxs-lookup"><span data-stu-id="a2dfa-154">Rendering logic</span></span>](#rendering-logic)
* [<span data-ttu-id="a2dfa-155">イベント ハンドラー</span><span class="sxs-lookup"><span data-stu-id="a2dfa-155">Event handlers</span></span>](#event-handlers)
* [<span data-ttu-id="a2dfa-156">コンポーネントの廃棄</span><span class="sxs-lookup"><span data-stu-id="a2dfa-156">Component disposal</span></span>](#component-disposal)
* [<span data-ttu-id="a2dfa-157">JavaScript 相互運用</span><span class="sxs-lookup"><span data-stu-id="a2dfa-157">JavaScript interop</span></span>](#javascript-interop)
* <span data-ttu-id="a2dfa-158">[Blazor Server の再レンダリング](#blazor-server-prerendering)</span><span class="sxs-lookup"><span data-stu-id="a2dfa-158">[Blazor Server rerendering](#blazor-server-prerendering)</span></span>

<span data-ttu-id="a2dfa-159">上記のハンドルされない例外については、この記事の後続のセクションで説明しています。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-159">The preceding unhandled exceptions are described in the following sections of this article.</span></span>

### <a name="component-instantiation"></a><span data-ttu-id="a2dfa-160">コンポーネントのインスタンス化</span><span class="sxs-lookup"><span data-stu-id="a2dfa-160">Component instantiation</span></span>

<span data-ttu-id="a2dfa-161">Blazor によってコンポーネントのインスタンスが作成されるとき:</span><span class="sxs-lookup"><span data-stu-id="a2dfa-161">When Blazor creates an instance of a component:</span></span>

* <span data-ttu-id="a2dfa-162">コンポーネントのコンストラクターが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-162">The component's constructor is invoked.</span></span>
* <span data-ttu-id="a2dfa-163">[`@inject`](xref:blazor/dependency-injection#request-a-service-in-a-component) ディレクティブ、または [`[Inject]`](xref:blazor/dependency-injection#request-a-service-in-a-component) 属性を介して、コンポーネントのコンストラクターに渡されるシングルトン以外の DI サービスのコンストラクターが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-163">The constructors of any non-singleton DI services supplied to the component's constructor via the [`@inject`](xref:blazor/dependency-injection#request-a-service-in-a-component) directive or the [`[Inject]`](xref:blazor/dependency-injection#request-a-service-in-a-component) attribute are invoked.</span></span>

<span data-ttu-id="a2dfa-164">実行されたいずれかのコンストラクターまたは任意の `[Inject]` プロパティのセッターがハンドルされない例外をスローすると、Blazor Server の回線が失敗します。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-164">A Blazor Server circuit fails when any executed constructor or a setter for any `[Inject]` property throws an unhandled exception.</span></span> <span data-ttu-id="a2dfa-165">フレームワークではコンポーネントをインスタンス化できないため、この例外は致命的です。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-165">The exception is fatal because the framework can't instantiate the component.</span></span> <span data-ttu-id="a2dfa-166">コンストラクターのロジックによって例外がスローされる可能性がある場合、アプリでは、エラー処理とログ記録を含む [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) ステートメントを使用して、例外をトラップする必要があります。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-166">If constructor logic may throw exceptions, the app should trap the exceptions using a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

### <a name="lifecycle-methods"></a><span data-ttu-id="a2dfa-167">ライフサイクル メソッド</span><span class="sxs-lookup"><span data-stu-id="a2dfa-167">Lifecycle methods</span></span>

<span data-ttu-id="a2dfa-168">コンポーネントのライフサイクルの間、Blazor によって以下の[ライフサイクル メソッド](xref:blazor/lifecycle)が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-168">During the lifetime of a component, Blazor invokes the following [lifecycle methods](xref:blazor/lifecycle):</span></span>

* `OnInitialized` / `OnInitializedAsync`
* `OnParametersSet` / `OnParametersSetAsync`
* `ShouldRender` / `ShouldRenderAsync`
* `OnAfterRender` / `OnAfterRenderAsync`

<span data-ttu-id="a2dfa-169">いずれかのライフサイクル メソッドが同期的または非同期的に例外をスローした場合、例外は Blazor Server 回線にとって致命的です。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-169">If any lifecycle method throws an exception, synchronously or asynchronously, the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="a2dfa-170">コンポーネントでライフサイクル メソッドのエラーに対処するには、エラー処理ロジックを追加します。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-170">For components to deal with errors in lifecycle methods, add error handling logic.</span></span>

<span data-ttu-id="a2dfa-171">`OnParametersSetAsync` によって製品を取得するメソッドを呼び出す次の例では:</span><span class="sxs-lookup"><span data-stu-id="a2dfa-171">In the following example where `OnParametersSetAsync` calls a method to obtain a product:</span></span>

* <span data-ttu-id="a2dfa-172">`ProductRepository.GetProductByIdAsync` メソッドでスローされた例外は `try-catch` ステートメントによって処理されます。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-172">An exception thrown in the `ProductRepository.GetProductByIdAsync` method is handled by a `try-catch` statement.</span></span>
* <span data-ttu-id="a2dfa-173">`catch` ブロックの実行時には:</span><span class="sxs-lookup"><span data-stu-id="a2dfa-173">When the `catch` block is executed:</span></span>
  * <span data-ttu-id="a2dfa-174">`loadFailed` が `true` に設定されます。これがユーザーにエラー メッセージを表示するために使われます。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-174">`loadFailed` is set to `true`, which is used to display an error message to the user.</span></span>
  * <span data-ttu-id="a2dfa-175">エラーがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-175">The error is logged.</span></span>

[!code-razor[](handle-errors/samples_snapshot/3.x/product-details.razor?highlight=11,27-39)]

### <a name="rendering-logic"></a><span data-ttu-id="a2dfa-176">レンダリング ロジック</span><span class="sxs-lookup"><span data-stu-id="a2dfa-176">Rendering logic</span></span>

<span data-ttu-id="a2dfa-177">`.razor` コンポーネント ファイル内の宣言マークアップは、`BuildRenderTree` と呼ばれる C# メソッドにコンパイルされます。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-177">The declarative markup in a `.razor` component file is compiled into a C# method called `BuildRenderTree`.</span></span> <span data-ttu-id="a2dfa-178">コンポーネントがレンダリングされるときには、`BuildRenderTree` が実行されて、レンダリングされたコンポーネントの要素、テキスト、および子コンポーネントを記述するデータ構造が構築されます。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-178">When a component renders, `BuildRenderTree` executes and builds up a data structure describing the elements, text, and child components of the rendered component.</span></span>

<span data-ttu-id="a2dfa-179">レンダリング ロジックは例外をスローすることがあります。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-179">Rendering logic can throw an exception.</span></span> <span data-ttu-id="a2dfa-180">このシナリオの例は、`@someObject.PropertyName` が評価されても `@someObject` が `null` であるときに発生しています。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-180">An example of this scenario occurs when `@someObject.PropertyName` is evaluated but `@someObject` is `null`.</span></span> <span data-ttu-id="a2dfa-181">レンダリング ロジックによってスローされるハンドルされない例外は、Blazor Server 回線にとって致命的です。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-181">An unhandled exception thrown by rendering logic is fatal to a Blazor Server circuit.</span></span>

<span data-ttu-id="a2dfa-182">レンダリング ロジックで null 参照例外が発生しないようにするには、そのメンバーにアクセスする前に `null` オブジェクトかどうかを調べます。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-182">To prevent a null reference exception in rendering logic, check for a `null` object before accessing its members.</span></span> <span data-ttu-id="a2dfa-183">次の例では、`person.Address` が `null` の場合は `person.Address` プロパティにアクセスしません。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-183">In the following example, `person.Address` properties aren't accessed if `person.Address` is `null`:</span></span>

[!code-razor[](handle-errors/samples_snapshot/3.x/person-example.razor?highlight=1)]

<span data-ttu-id="a2dfa-184">上記のコードは、`person` が `null` でないことを前提としています。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-184">The preceding code assumes that `person` isn't `null`.</span></span> <span data-ttu-id="a2dfa-185">多くの場合、コードの構造によって、コンポーネントがレンダリングされる時点でオブジェクトの存在が保証されます。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-185">Often, the structure of the code guarantees that an object exists at the time the component is rendered.</span></span> <span data-ttu-id="a2dfa-186">そのような場合は、レンダリング ロジックで `null` かどうかを調べる必要はありません。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-186">In those cases, it isn't necessary to check for `null` in rendering logic.</span></span> <span data-ttu-id="a2dfa-187">前の例では、`person` が存在することを保証できるのは、コンポーネントがインスタンス化されるときに `person` が作成されるためです。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-187">In the prior example, `person` might be guaranteed to exist because `person` is created when the component is instantiated.</span></span>

### <a name="event-handlers"></a><span data-ttu-id="a2dfa-188">イベント ハンドラー</span><span class="sxs-lookup"><span data-stu-id="a2dfa-188">Event handlers</span></span>

<span data-ttu-id="a2dfa-189">クライアント側のコードでは、以下を使用して、イベント ハンドラーが作成されるときに C# コードの呼び出しをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-189">Client-side code triggers invocations of C# code when event handlers are created using:</span></span>

* `@onclick`
* `@onchange`
* <span data-ttu-id="a2dfa-190">その他の `@on...` 属性</span><span class="sxs-lookup"><span data-stu-id="a2dfa-190">Other `@on...` attributes</span></span>
* `@bind`

<span data-ttu-id="a2dfa-191">これらのシナリオでは、イベント ハンドラー コードによって、ハンドルされない例外がスローされることがあります。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-191">Event handler code might throw an unhandled exception in these scenarios.</span></span>

<span data-ttu-id="a2dfa-192">イベント ハンドラーがハンドルされない例外をスローした場合 (たとえば、データベース クエリが失敗した)、例外は Blazor Server 回線にとって致命的です。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-192">If an event handler throws an unhandled exception (for example, a database query fails), the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="a2dfa-193">アプリが外部の理由で失敗する可能性のあるコードを呼び出す場合は、エラー処理とログ記録を含む [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) ステートメントを使用して、例外をトラップします。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-193">If the app calls code that could fail for external reasons, trap exceptions using a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="a2dfa-194">ユーザー コードで例外のトラップと処理が行われない場合は、フレームワークによって例外がログに記録され、回線が終了されます。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-194">If user code doesn't trap and handle the exception, the framework logs the exception and terminates the circuit.</span></span>

### <a name="component-disposal"></a><span data-ttu-id="a2dfa-195">コンポーネントの廃棄</span><span class="sxs-lookup"><span data-stu-id="a2dfa-195">Component disposal</span></span>

<span data-ttu-id="a2dfa-196">たとえばユーザーが別のページに移動したため、コンポーネントが UI から削除されることがあります。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-196">A component may be removed from the UI, for example, because the user has navigated to another page.</span></span> <span data-ttu-id="a2dfa-197"><xref:System.IDisposable?displayProperty=fullName> を実装しているコンポーネントが UI から削除されると、フレームワークにより、コンポーネントの <xref:System.IDisposable.Dispose*> メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-197">When a component that implements <xref:System.IDisposable?displayProperty=fullName> is removed from the UI, the framework calls the component's <xref:System.IDisposable.Dispose*> method.</span></span>

<span data-ttu-id="a2dfa-198">コンポーネントの `Dispose` メソッドがハンドルされない例外をスローした場合、例外は Blazor Server 回線にとって致命的です。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-198">If the component's `Dispose` method throws an unhandled exception, the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="a2dfa-199">破棄のロジックによって例外がスローされる可能性がある場合、アプリでは、エラー処理とログ記録を含む [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) ステートメントを使用して、例外をトラップする必要があります。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-199">If disposal logic may throw exceptions, the app should trap the exceptions using a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="a2dfa-200">コンポーネントの破棄の詳細については、「<xref:blazor/lifecycle#component-disposal-with-idisposable>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-200">For more information on component disposal, see <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span></span>

### <a name="javascript-interop"></a><span data-ttu-id="a2dfa-201">JavaScript 相互運用</span><span class="sxs-lookup"><span data-stu-id="a2dfa-201">JavaScript interop</span></span>

<span data-ttu-id="a2dfa-202">`IJSRuntime.InvokeAsync<T>` を使用すると、.NET コードによって、ユーザーのブラウザーで JavaScript ランタイムの非同期呼び出しを行えます。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-202">`IJSRuntime.InvokeAsync<T>` allows .NET code to make asynchronous calls to the JavaScript runtime in the user's browser.</span></span>

<span data-ttu-id="a2dfa-203">`InvokeAsync<T>` を使用するエラー処理には、以下の条件が適用されます。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-203">The following conditions apply to error handling with `InvokeAsync<T>`:</span></span>

* <span data-ttu-id="a2dfa-204">`InvokeAsync<T>` の呼び出しが同期的に失敗した場合は、.NET 例外が発生します。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-204">If a call to `InvokeAsync<T>` fails synchronously, a .NET exception occurs.</span></span> <span data-ttu-id="a2dfa-205">たとえば、指定された引数をシリアル化できないため、`InvokeAsync<T>` の呼び出しが失敗することがあります。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-205">A call to `InvokeAsync<T>` may fail, for example, because the supplied arguments can't be serialized.</span></span> <span data-ttu-id="a2dfa-206">開発者コードで例外をキャッチする必要があります。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-206">Developer code must catch the exception.</span></span> <span data-ttu-id="a2dfa-207">イベント ハンドラーまたはコンポーネントのライフサイクル メソッドのアプリ コードで例外が処理されない場合、結果の例外は Blazor Server 回線にとって致命的です。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-207">If app code in an event handler or component lifecycle method doesn't handle an exception, the resulting exception is fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="a2dfa-208">`InvokeAsync<T>` の呼び出しが非同期に失敗した場合、.NET <xref:System.Threading.Tasks.Task> が失敗します。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-208">If a call to `InvokeAsync<T>` fails asynchronously, the .NET <xref:System.Threading.Tasks.Task> fails.</span></span> <span data-ttu-id="a2dfa-209">たとえば、JavaScript 側のコードが例外をスローしたり、`rejected` として完了した `Promise` を返したりするために、`InvokeAsync<T>` の呼び出しが失敗することがあります。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-209">A call to `InvokeAsync<T>` may fail, for example, because the JavaScript-side code throws an exception or returns a `Promise` that completed as `rejected`.</span></span> <span data-ttu-id="a2dfa-210">開発者コードで例外をキャッチする必要があります。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-210">Developer code must catch the exception.</span></span> <span data-ttu-id="a2dfa-211">[await](/dotnet/csharp/language-reference/keywords/await) 演算子を使用する場合は、エラー処理とログ記録を含む [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) ステートメントでメソッド呼び出しをラップすることを検討してください。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-211">If using the [await](/dotnet/csharp/language-reference/keywords/await) operator, consider wrapping the method call in a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span> <span data-ttu-id="a2dfa-212">そうしないと、失敗したコードにより、Blazor Server 回線にとって致命的なハンドルされない例外が発生する結果となります。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-212">Otherwise, the failing code results in an unhandled exception that's fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="a2dfa-213">既定では、`InvokeAsync<T>` の呼び出しは一定の期間内に完了する必要があります。そうでないと呼び出しがタイムアウトになります。既定のタイムアウト期間は 1 分です。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-213">By default, calls to `InvokeAsync<T>` must complete within a certain period or else the call times out. The default timeout period is one minute.</span></span> <span data-ttu-id="a2dfa-214">タイムアウトにより、完了メッセージを送り返さないネットワーク接続や JavaScript コードでの損失からコードを保護します。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-214">The timeout protects the code against a loss in network connectivity or JavaScript code that never sends back a completion message.</span></span> <span data-ttu-id="a2dfa-215">呼び出しがタイムアウトになった場合、結果の `Task` は <xref:System.OperationCanceledException> で失敗します。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-215">If the call times out, the resulting `Task` fails with an <xref:System.OperationCanceledException>.</span></span> <span data-ttu-id="a2dfa-216">ログ記録を使用して例外をトラップし、処理します。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-216">Trap and process the exception with logging.</span></span>

<span data-ttu-id="a2dfa-217">同様に、JavaScript コードでは、[`[JSInvokable]`](xref:blazor/call-dotnet-from-javascript) 属性によって示される .NET メソッドの呼び出しを開始することがあります。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-217">Similarly, JavaScript code may initiate calls to .NET methods indicated by the [`[JSInvokable]`](xref:blazor/call-dotnet-from-javascript) attribute.</span></span> <span data-ttu-id="a2dfa-218">ハンドルされない例外が、これらの .NET メソッドでスローされた場合:</span><span class="sxs-lookup"><span data-stu-id="a2dfa-218">If these .NET methods throw an unhandled exception:</span></span>

* <span data-ttu-id="a2dfa-219">例外は Blazor Server 回線にとって致命的なものとして扱われません。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-219">The exception isn't treated as fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="a2dfa-220">JavaScript 側の `Promise` が拒否されます。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-220">The JavaScript-side `Promise` is rejected.</span></span>

<span data-ttu-id="a2dfa-221">.NET 側か、メソッド呼び出しの JavaScript 側か、どちらでエラー処理コードを使用するかを選択できます。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-221">You have the option of using error handling code on either the .NET side or the JavaScript side of the method call.</span></span>

<span data-ttu-id="a2dfa-222">詳細については、次の記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-222">For more information, see the following articles:</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>

### <a name="blazor-server-prerendering"></a>Blazor<span data-ttu-id="a2dfa-223"> Server の事前レンダリング</span><span class="sxs-lookup"><span data-stu-id="a2dfa-223"> Server prerendering</span></span>

<span data-ttu-id="a2dfa-224">レンダリングされた HTML マークアップがユーザーの初期 HTTP 要求の一部として返されるように、[コンポーネント タグ ヘルパー](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)を使用して Blazor コンポーネントを事前レンダリングすることができます。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-224">Blazor components can be prerendered using the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) so that their rendered HTML markup is returned as part of the user's initial HTTP request.</span></span> <span data-ttu-id="a2dfa-225">これは以下によって機能します。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-225">This works by:</span></span>

* <span data-ttu-id="a2dfa-226">同じページに含まれるすべての事前レンダリング コンポーネントに対する新しい回線を作成する。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-226">Creating a new circuit for all of the prerendered components that are part of the same page.</span></span>
* <span data-ttu-id="a2dfa-227">初期 HTML を生成する。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-227">Generating the initial HTML.</span></span>
* <span data-ttu-id="a2dfa-228">ユーザーのブラウザーが同じサーバーに戻る SignalR 接続を確立するまで、回線を `disconnected` として扱う。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-228">Treating the circuit as `disconnected` until the user's browser establishes a SignalR connection back to the same server.</span></span> <span data-ttu-id="a2dfa-229">接続が確立されると、回線でのインタラクティビティが再開され、コンポーネントの HTML マークアップが更新されます。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-229">When the connection is established, interactivity on the circuit is resumed and the components' HTML markup is updated.</span></span>

<span data-ttu-id="a2dfa-230">ライフサイクル メソッドやレンダリング ロジックの実行中など、事前レンダリングでいずれかのコンポーネントからハンドルされない例外がスローされた場合:</span><span class="sxs-lookup"><span data-stu-id="a2dfa-230">If any component throws an unhandled exception during prerendering, for example, during a lifecycle method or in rendering logic:</span></span>

* <span data-ttu-id="a2dfa-231">例外は回線にとって致命的です。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-231">The exception is fatal to the circuit.</span></span>
* <span data-ttu-id="a2dfa-232">その例外は、`Component` タグ ヘルパーの呼び出し履歴から破棄されます。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-232">The exception is thrown up the call stack from the `Component` Tag Helper.</span></span> <span data-ttu-id="a2dfa-233">そのため、開発者コードによって例外が明示的にキャッチされない限り、HTTP 要求全体が失敗します。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-233">Therefore, the entire HTTP request fails unless the exception is explicitly caught by developer code.</span></span>

<span data-ttu-id="a2dfa-234">事前レンダリングが失敗する通常の状況では、コンポーネントのビルドとレンダリングを続行しても意味がありません。これは、動作中のコンポーネントはレンダリングできないためです。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-234">Under normal circumstances when prerendering fails, continuing to build and render the component doesn't make sense because a working component can't be rendered.</span></span>

<span data-ttu-id="a2dfa-235">事前レンダリング中に発生する可能性のあるエラーに耐えるには、例外をスローする可能性のあるコンポーネント内にエラー処理ロジックを配置する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-235">To tolerate errors that may occur during prerendering, error handling logic must be placed inside a component that may throw exceptions.</span></span> <span data-ttu-id="a2dfa-236">エラー処理とログ記録を含む [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) ステートメントを使用してください。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-236">Use [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statements with error handling and logging.</span></span> <span data-ttu-id="a2dfa-237">`try-catch` ステートメント内に `Component` タグ ヘルパーをラップするのではなく、`Component` タグ ヘルパーによってレンダリングされるコンポーネント内にエラー処理ロジックを配置します。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-237">Instead of wrapping the `Component` Tag Helper in a `try-catch` statement, place error handling logic in the component rendered by the `Component` Tag Helper.</span></span>

## <a name="advanced-scenarios"></a><span data-ttu-id="a2dfa-238">高度なシナリオ</span><span class="sxs-lookup"><span data-stu-id="a2dfa-238">Advanced scenarios</span></span>

### <a name="recursive-rendering"></a><span data-ttu-id="a2dfa-239">再帰的レンダリング</span><span class="sxs-lookup"><span data-stu-id="a2dfa-239">Recursive rendering</span></span>

<span data-ttu-id="a2dfa-240">コンポーネントは、再帰的に入れ子にすることができます。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-240">Components can be nested recursively.</span></span> <span data-ttu-id="a2dfa-241">これは、再帰的なデータ構造を表現する場合に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-241">This is useful for representing recursive data structures.</span></span> <span data-ttu-id="a2dfa-242">たとえば `TreeNode` コンポーネントで、ノードの子ごとにより多くの `TreeNode` コンポーネントをレンダリングできます。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-242">For example, a `TreeNode` component can render more `TreeNode` components for each of the node's children.</span></span>

<span data-ttu-id="a2dfa-243">再帰的にレンダリングする場合は、無限の再帰となるようなコーディング パターンは回避します。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-243">When rendering recursively, avoid coding patterns that result in infinite recursion:</span></span>

* <span data-ttu-id="a2dfa-244">循環が含まれるデータ構造は再帰的にレンダリングしないでください。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-244">Don't recursively render a data structure that contains a cycle.</span></span> <span data-ttu-id="a2dfa-245">たとえば、子にそれ自体が含まれるツリー ノードはレンダリングしないでください。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-245">For example, don't render a tree node whose children includes itself.</span></span>
* <span data-ttu-id="a2dfa-246">循環を含むひと続きのレイアウトは作成しないでください。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-246">Don't create a chain of layouts that contain a cycle.</span></span> <span data-ttu-id="a2dfa-247">たとえば、レイアウトがそれ自体であるレイアウトは作成しないようにします。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-247">For example, don't create a layout whose layout is itself.</span></span>
* <span data-ttu-id="a2dfa-248">エンドユーザーが、悪意のあるデータ入力や JavaScript の相互運用呼び出しを通して、再帰による不変 (ルール) を犯さないようにします。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-248">Don't allow an end user to violate recursion invariants (rules) through malicious data entry or JavaScript interop calls.</span></span>

<span data-ttu-id="a2dfa-249">レンダリング中の無限ループ:</span><span class="sxs-lookup"><span data-stu-id="a2dfa-249">Infinite loops during rendering:</span></span>

* <span data-ttu-id="a2dfa-250">レンダリング プロセスが永久に続行されるようになります。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-250">Causes the rendering process to continue forever.</span></span>
* <span data-ttu-id="a2dfa-251">これは終了しないループを作成するのと同じです。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-251">Is equivalent to creating an unterminated loop.</span></span>

<span data-ttu-id="a2dfa-252">これらのシナリオでは、影響を受けた Blazor Server 回線が失敗し、スレッドでは通常、以下のことが試みられます。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-252">In these scenarios, an affected Blazor Server circuit fails, and the thread usually attempts to:</span></span>

* <span data-ttu-id="a2dfa-253">オペレーティング システムで許されている限りの CPU 時間を無期限に消費します。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-253">Consume as much CPU time as permitted by the operating system, indefinitely.</span></span>
* <span data-ttu-id="a2dfa-254">サーバー メモリの量を無制限に消費します。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-254">Consume an unlimited amount of server memory.</span></span> <span data-ttu-id="a2dfa-255">メモリを無制限に使用することは、すべての反復処理で、終了しないループによってコレクションにエントリが追加されるシナリオと同じです。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-255">Consuming unlimited memory is equivalent to the scenario where an unterminated loop adds entries to a collection on every iteration.</span></span>

<span data-ttu-id="a2dfa-256">無限の再帰パターンを回避するには、再帰的なレンダリング コードに適切な停止条件が含まれるようにします。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-256">To avoid infinite recursion patterns, ensure that recursive rendering code contains suitable stopping conditions.</span></span>

### <a name="custom-render-tree-logic"></a><span data-ttu-id="a2dfa-257">カスタム レンダリング ツリーのロジック</span><span class="sxs-lookup"><span data-stu-id="a2dfa-257">Custom render tree logic</span></span>

<span data-ttu-id="a2dfa-258">ほとんどの Blazor コンポーネントは *.razor* ファイルとして実装され、出力をレンダリングするために `RenderTreeBuilder` 上で動作するロジックを生成するためにコンパイルされます。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-258">Most Blazor components are implemented as *.razor* files and are compiled to produce logic that operates on a `RenderTreeBuilder` to render their output.</span></span> <span data-ttu-id="a2dfa-259">開発者は、手続き型の C# コードを使用して `RenderTreeBuilder` ロジックを手動で実装できます。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-259">A developer may manually implement `RenderTreeBuilder` logic using procedural C# code.</span></span> <span data-ttu-id="a2dfa-260">詳細については、「<xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-260">For more information, see <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>.</span></span>

> [!WARNING]
> <span data-ttu-id="a2dfa-261">手動のレンダー ツリー ビルダー ロジックの使用は、高度で安全ではないシナリオと考えられています。一般のコンポーネント開発には推奨されません。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-261">Use of manual render tree builder logic is considered an advanced and unsafe scenario, not recommended for general component development.</span></span>

<span data-ttu-id="a2dfa-262">`RenderTreeBuilder` コードが記述される場合は、開発者がコードの正確性を保証する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-262">If `RenderTreeBuilder` code is written, the developer must guarantee the correctness of the code.</span></span> <span data-ttu-id="a2dfa-263">たとえば、開発者は以下のことを確認する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-263">For example, the developer must ensure that:</span></span>

* <span data-ttu-id="a2dfa-264">`OpenElement` と `CloseElement` の呼び出しが正しく調整されている。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-264">Calls to `OpenElement` and `CloseElement` are correctly balanced.</span></span>
* <span data-ttu-id="a2dfa-265">正しい場所にのみ属性が追加される。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-265">Attributes are only added in the correct places.</span></span>

<span data-ttu-id="a2dfa-266">手動レンダー ツリー ビルダーのロジックが正しくないと、クラッシュ、サーバーのハング、セキュリティ脆弱性など、不特定の未定義の動作が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-266">Incorrect manual render tree builder logic can cause arbitrary undefined behavior, including crashes, server hangs, and security vulnerabilities.</span></span>

<span data-ttu-id="a2dfa-267">手動のレンダリング ツリー ビルダー ロジックは、アセンブリ コードや MSIL 命令を手動で記述するのと同じレベルの複雑さと、同じレベルの*危険性*を伴うことを考慮に入れてください。</span><span class="sxs-lookup"><span data-stu-id="a2dfa-267">Consider manual render tree builder logic on the same level of complexity and with the same level of *danger* as writing assembly code or MSIL instructions by hand.</span></span>
