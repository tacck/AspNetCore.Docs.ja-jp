---
title: 'title:ASP.NET Core でホステッド サービスを使用するバックグラウンド タスク author: rick-anderson description:ASP.NET Core でホステッド サービスを使用するバックグラウンド タスクの実装方法について説明します。'
author: rick-anderson
description: "monikerRange: '>= aspnetcore-2.1' ms.author: riande ms.custom: mvc ms.date:02/10/2020 no-loc:"
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/10/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/host/hosted-services
ms.openlocfilehash: 47d0bdda7249232af22ec1c97e7baa710310caed
ms.sourcegitcommit: cb6e3e12641375ea9ad02002388f78ec0989d88e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2020
ms.locfileid: "84253683"
---
# <a name="background-tasks-with-hosted-services-in-aspnet-core"></a><span data-ttu-id="3f551-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3f551-103">'Blazor'</span></span>

<span data-ttu-id="3f551-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3f551-104">'Identity'</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3f551-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3f551-105">'Let's Encrypt'</span></span> <span data-ttu-id="3f551-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3f551-106">'Razor'</span></span> <span data-ttu-id="3f551-107">'SignalR' uid: fundamentals/host/hosted-services</span><span class="sxs-lookup"><span data-stu-id="3f551-107">'SignalR' uid: fundamentals/host/hosted-services</span></span>

* <span data-ttu-id="3f551-108">ASP.NET Core でホステッド サービスを使用するバックグラウンド タスク</span><span class="sxs-lookup"><span data-stu-id="3f551-108">Background tasks with hosted services in ASP.NET Core</span></span>
* <span data-ttu-id="3f551-109">作成者: [Jeow Li Huan](https://github.com/huan086)</span><span class="sxs-lookup"><span data-stu-id="3f551-109">By [Jeow Li Huan](https://github.com/huan086)</span></span> <span data-ttu-id="3f551-110">ASP.NET Core では、バックグラウンド タスクを*ホステッド サービス*として実装することができます。</span><span class="sxs-lookup"><span data-stu-id="3f551-110">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span></span>
* <span data-ttu-id="3f551-111">ホストされるサービスは、<xref:Microsoft.Extensions.Hosting.IHostedService> インターフェイスを実装するバックグラウンド タスク ロジックを持つクラスです。</span><span class="sxs-lookup"><span data-stu-id="3f551-111">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span>

<span data-ttu-id="3f551-112">このトピックでは、3 つのホステッド サービスの例について説明します。</span><span class="sxs-lookup"><span data-stu-id="3f551-112">This topic provides three hosted service examples:</span></span>

## <a name="worker-service-template"></a><span data-ttu-id="3f551-113">タイマーで実行されるバックグラウンド タスク。</span><span class="sxs-lookup"><span data-stu-id="3f551-113">Background task that runs on a timer.</span></span>

<span data-ttu-id="3f551-114">[スコープ サービス](xref:fundamentals/dependency-injection#service-lifetimes)をアクティブ化するホステッド サービス。</span><span class="sxs-lookup"><span data-stu-id="3f551-114">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="3f551-115">スコープ サービスは[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection) を使用できます。</span><span class="sxs-lookup"><span data-stu-id="3f551-115">The scoped service can use [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

<span data-ttu-id="3f551-116">連続して実行される、キューに格納されたバックグラウンド タスク。</span><span class="sxs-lookup"><span data-stu-id="3f551-116">Queued background tasks that run sequentially.</span></span>

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="package"></a><span data-ttu-id="3f551-117">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="3f551-117">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="3f551-118">ワーカー サービス テンプレート</span><span class="sxs-lookup"><span data-stu-id="3f551-118">Worker Service template</span></span> <span data-ttu-id="3f551-119">ASP.NET Core ワーカー サービス テンプレートは、実行時間が長いサービス アプリを作成する場合の出発点として利用できます。</span><span class="sxs-lookup"><span data-stu-id="3f551-119">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span>

<span data-ttu-id="3f551-120">ワーカー サービス テンプレートから作成されたアプリで、そのプロジェクト ファイル内のワーカー SDK が指定されます。</span><span class="sxs-lookup"><span data-stu-id="3f551-120">An app created from the Worker Service template specifies the Worker SDK in its project file:</span></span> <span data-ttu-id="3f551-121">ホステッド サービス アプリの基礎としてテンプレートを使用するには:</span><span class="sxs-lookup"><span data-stu-id="3f551-121">To use the template as a basis for a hosted services app:</span></span>

## <a name="ihostedservice-interface"></a><span data-ttu-id="3f551-122">Package</span><span class="sxs-lookup"><span data-stu-id="3f551-122">Package</span></span>

<span data-ttu-id="3f551-123">ワーカー サービス テンプレートに基づくアプリは `Microsoft.NET.Sdk.Worker` SDK を使用し、[Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) パッケージへの明示的なパッケージ参照を含んでいます。</span><span class="sxs-lookup"><span data-stu-id="3f551-123">An app based on the Worker Service template uses the `Microsoft.NET.Sdk.Worker` SDK and has an explicit package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package.</span></span>

* <span data-ttu-id="3f551-124">たとえば、サンプル アプリのプロジェクト ファイル (*BackgroundTasksSample.csproj*) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3f551-124">For example, see the sample app's project file (*BackgroundTasksSample.csproj*).</span></span> <span data-ttu-id="3f551-125">`Microsoft.NET.Sdk.Web` SDK を使用する Web アプリの場合、[Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) パッケージは共有フレームワークから暗黙的に参照されます。</span><span class="sxs-lookup"><span data-stu-id="3f551-125">For web apps that use the `Microsoft.NET.Sdk.Web` SDK, the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package is referenced implicitly from the shared framework.</span></span>

  * <span data-ttu-id="3f551-126">アプリのプロジェクト ファイル内の明示的なパッケージ参照は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="3f551-126">An explicit package reference in the app's project file isn't required.</span></span>
  * <span data-ttu-id="3f551-127">IHostedService インターフェイス</span><span class="sxs-lookup"><span data-stu-id="3f551-127">IHostedService interface</span></span>

  <span data-ttu-id="3f551-128"><xref:Microsoft.Extensions.Hosting.IHostedService> インターフェイスは、ホストによって管理されるオブジェクトの 2 つのメソッドを定義します。</span><span class="sxs-lookup"><span data-stu-id="3f551-128">The <xref:Microsoft.Extensions.Hosting.IHostedService> interface defines two methods for objects that are managed by the host:</span></span> <span data-ttu-id="3f551-129">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*): `StartAsync` には、バックグラウンド タスクを開始するロジックが含まれています。</span><span class="sxs-lookup"><span data-stu-id="3f551-129">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*): `StartAsync` contains the logic to start the background task.</span></span>

  ```csharp
  using Microsoft.AspNetCore.Hosting;
  using Microsoft.Extensions.DependencyInjection;
  using Microsoft.Extensions.Hosting;

  public class Program
  {
      public static void Main(string[] args)
      {
          CreateHostBuilder(args).Build().Run();
      }

      public static IHostBuilder CreateHostBuilder(string[] args) =>
          Host.CreateDefaultBuilder(args)
              .ConfigureWebHostDefaults(webBuilder =>
              {
                  webBuilder.UseStartup<Startup>();
              })
              .ConfigureServices(services =>
              {
                  services.AddHostedService<VideosWatcher>();
              });
  }
  ```

* <span data-ttu-id="3f551-130">`StartAsync` は、以下よりも "*前に*" 呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="3f551-130">`StartAsync` is called *before*:</span></span> <span data-ttu-id="3f551-131">アプリの要求処理パイプラインが構成される (`Startup.Configure`)。</span><span class="sxs-lookup"><span data-stu-id="3f551-131">The app's request processing pipeline is configured (`Startup.Configure`).</span></span> <span data-ttu-id="3f551-132">サーバーが起動して、[IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) がトリガーされる。</span><span class="sxs-lookup"><span data-stu-id="3f551-132">The server is started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span></span>

  <span data-ttu-id="3f551-133">既定の動作を変更して、アプリのパイプラインが構成されて `ApplicationStarted` が呼び出された後で、ホステッド サービスの `StartAsync` が実行するようにできます。</span><span class="sxs-lookup"><span data-stu-id="3f551-133">The default behavior can be changed so that the hosted service's `StartAsync` runs after the app's pipeline has been configured and `ApplicationStarted` is called.</span></span> <span data-ttu-id="3f551-134">既定の動作を変更するには、`ConfigureWebHostDefaults` を呼び出した後でホステッド サービス (次の例では `VideosWatcher`) を追加します。</span><span class="sxs-lookup"><span data-stu-id="3f551-134">To change the default behavior, add the hosted service (`VideosWatcher` in the following example) after calling `ConfigureWebHostDefaults`:</span></span>

  * <span data-ttu-id="3f551-135">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*):ホストが正常なシャットダウンを実行しているときにトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="3f551-135">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*): Triggered when the host is performing a graceful shutdown.</span></span>
  * <span data-ttu-id="3f551-136">`StopAsync` には、バックグラウンド タスクを終了するロジックが含まれています。</span><span class="sxs-lookup"><span data-stu-id="3f551-136">`StopAsync` contains the logic to end the background task.</span></span>

  <span data-ttu-id="3f551-137">アンマネージ リソースを破棄するには、<xref:System.IDisposable> と[ファイナライザー (デストラクター)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) を実装します。</span><span class="sxs-lookup"><span data-stu-id="3f551-137">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span></span>

  <span data-ttu-id="3f551-138">キャンセル トークンには、シャットダウン プロセスが正常に行われないことを示す、既定の 5 秒間のタイムアウトが含まれています。</span><span class="sxs-lookup"><span data-stu-id="3f551-138">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span></span> <span data-ttu-id="3f551-139">キャンセルがトークンに要求された場合:</span><span class="sxs-lookup"><span data-stu-id="3f551-139">When cancellation is requested on the token:</span></span>

  <span data-ttu-id="3f551-140">アプリで実行されている残りのバックグラウンド操作が中止します。</span><span class="sxs-lookup"><span data-stu-id="3f551-140">Any remaining background operations that the app is performing should be aborted.</span></span>

  * <span data-ttu-id="3f551-141">`StopAsync` で呼び出されたすべてのメソッドが速やかに戻ります。</span><span class="sxs-lookup"><span data-stu-id="3f551-141">Any methods called in `StopAsync` should return promptly.</span></span> <span data-ttu-id="3f551-142">ただし、キャンセルが要求された後もタスクは破棄されません&mdash;呼び出し元がすべてのタスクの完了を待機します。</span><span class="sxs-lookup"><span data-stu-id="3f551-142">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span></span>
  * <span data-ttu-id="3f551-143">アプリが予期せずシャットダウンした場合 (たとえば、アプリのプロセスが失敗した場合)、`StopAsync` は呼び出されないことがあります。</span><span class="sxs-lookup"><span data-stu-id="3f551-143">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span></span> <span data-ttu-id="3f551-144">そのため、`StopAsync` で呼び出されたメソッドや行われた操作が実行されない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="3f551-144">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span></span>

<span data-ttu-id="3f551-145">既定の 5 秒のシャットダウン タイムアウトを延長するには、次を設定します。</span><span class="sxs-lookup"><span data-stu-id="3f551-145">To extend the default five second shutdown timeout, set:</span></span> <span data-ttu-id="3f551-146"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> (汎用ホストを使用するとき)</span><span class="sxs-lookup"><span data-stu-id="3f551-146"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span></span>

## <a name="backgroundservice-base-class"></a><span data-ttu-id="3f551-147">詳細については、「<xref:fundamentals/host/generic-host#shutdown-timeout>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3f551-147">For more information, see <xref:fundamentals/host/generic-host#shutdown-timeout>.</span></span>

<span data-ttu-id="3f551-148">シャットダウン タイムアウトのホスト構成設定 (Web ホストを使用するとき)</span><span class="sxs-lookup"><span data-stu-id="3f551-148">Shutdown timeout host configuration setting when using Web Host.</span></span>

<span data-ttu-id="3f551-149">詳細については、「<xref:fundamentals/host/web-host#shutdown-timeout>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3f551-149">For more information, see <xref:fundamentals/host/web-host#shutdown-timeout>.</span></span> <span data-ttu-id="3f551-150">ホステッド サービスは、アプリの起動時に一度アクティブ化され、アプリのシャットダウン時に正常にシャットダウンされます。</span><span class="sxs-lookup"><span data-stu-id="3f551-150">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span></span> <span data-ttu-id="3f551-151">バックグラウンド タスクの実行中にエラーがスローされた場合、`StopAsync` が呼び出されていなくても `Dispose` を呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="3f551-151">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span></span> <span data-ttu-id="3f551-152">BackgroundService 基底クラス</span><span class="sxs-lookup"><span data-stu-id="3f551-152">BackgroundService base class</span></span> <span data-ttu-id="3f551-153"><xref:Microsoft.Extensions.Hosting.BackgroundService> は、長期 <xref:Microsoft.Extensions.Hosting.IHostedService> を実装するための基底クラスです。</span><span class="sxs-lookup"><span data-stu-id="3f551-153"><xref:Microsoft.Extensions.Hosting.BackgroundService> is a base class for implementing a long running <xref:Microsoft.Extensions.Hosting.IHostedService>.</span></span>

<span data-ttu-id="3f551-154">[ExecuteAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.ExecuteAsync*) は、バックグラウンド サービスを実行するために呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="3f551-154">[ExecuteAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.ExecuteAsync*) is called to run the background service.</span></span> <span data-ttu-id="3f551-155">この実装では、バックグラウンド サービスの有効期間全体を表す <xref:System.Threading.Tasks.Task> が返されます。</span><span class="sxs-lookup"><span data-stu-id="3f551-155">The implementation returns a <xref:System.Threading.Tasks.Task> that represents the entire lifetime of the background service.</span></span> <span data-ttu-id="3f551-156">`await` を呼び出すなどして [ExecuteAsync が非同期になる](https://github.com/dotnet/extensions/issues/2149)まで、以降のサービスは開始されません。</span><span class="sxs-lookup"><span data-stu-id="3f551-156">No further services are started until [ExecuteAsync becomes asynchronous](https://github.com/dotnet/extensions/issues/2149), such as by calling `await`.</span></span> <span data-ttu-id="3f551-157">`ExecuteAsync` では、長時間の初期化作業を実行しないようにしてください。</span><span class="sxs-lookup"><span data-stu-id="3f551-157">Avoid performing long, blocking initialization work in `ExecuteAsync`.</span></span>

## <a name="timed-background-tasks"></a><span data-ttu-id="3f551-158">ホストは [StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.StopAsync*) でブロックされ、`ExecuteAsync` の完了を待機します。</span><span class="sxs-lookup"><span data-stu-id="3f551-158">The host blocks in [StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.StopAsync*) waiting for `ExecuteAsync` to complete.</span></span>

<span data-ttu-id="3f551-159">[IHostedService.StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) が呼び出されると、キャンセル トークンがトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="3f551-159">The cancellation token is triggered when [IHostedService.StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) is called.</span></span> <span data-ttu-id="3f551-160">サービスを正常にシャットダウンするためのキャンセル トークンが起動すると、`ExecuteAsync` の実装はすぐに終了します。</span><span class="sxs-lookup"><span data-stu-id="3f551-160">Your implementation of `ExecuteAsync` should finish promptly when the cancellation token is fired in order to gracefully shut down the service.</span></span> <span data-ttu-id="3f551-161">それ以外の場合は、シャットダウンのタイムアウト時にサービスが強制的にシャットダウンします。</span><span class="sxs-lookup"><span data-stu-id="3f551-161">Otherwise, the service ungracefully shuts down at the shutdown timeout.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=16-17,34,41)]

<span data-ttu-id="3f551-162">詳細については、「[IHostedService インターフェイス](#ihostedservice-interface)」のセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="3f551-162">For more information, see the [IHostedService interface](#ihostedservice-interface) section.</span></span> <span data-ttu-id="3f551-163">時間が指定されたバックグラウンド タスク</span><span class="sxs-lookup"><span data-stu-id="3f551-163">Timed background tasks</span></span>

<span data-ttu-id="3f551-164">時間が指定されたバックグラウンド タスクは、[System.Threading.Timer](xref:System.Threading.Timer) クラスを利用します。</span><span class="sxs-lookup"><span data-stu-id="3f551-164">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a><span data-ttu-id="3f551-165">このタイマーはタスクの `DoWork` メソッドをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="3f551-165">The timer triggers the task's `DoWork` method.</span></span>

<span data-ttu-id="3f551-166">タイマーは `StopAsync` で無効になり、`Dispose` でサービス コンテナーが破棄されたときに破棄されます。</span><span class="sxs-lookup"><span data-stu-id="3f551-166">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span></span> <span data-ttu-id="3f551-167">前の `DoWork` の実行が完了するまで <xref:System.Threading.Timer> は待機されないため、ここで示したアプローチはすべてのシナリオに適しているとは限りません。</span><span class="sxs-lookup"><span data-stu-id="3f551-167">The <xref:System.Threading.Timer> doesn't wait for previous executions of `DoWork` to finish, so the approach shown might not be suitable for every scenario.</span></span>

<span data-ttu-id="3f551-168">[Interlocked.Increment](xref:System.Threading.Interlocked.Increment*) は、アトミック操作として実行カウンターをインクリメントするために使用されされます。これにより、複数のスレッドによって `executionCount` が同時に更新されなくなります。</span><span class="sxs-lookup"><span data-stu-id="3f551-168">[Interlocked.Increment](xref:System.Threading.Interlocked.Increment*) is used to increment the execution counter as an atomic operation, which ensures that multiple threads don't update `executionCount` concurrently.</span></span> <span data-ttu-id="3f551-169">サービスは、`AddHostedService` 拡張メソッドを使用して `IHostBuilder.ConfigureServices` (*Program.cs*) に登録されます。</span><span class="sxs-lookup"><span data-stu-id="3f551-169">The service is registered in `IHostBuilder.ConfigureServices` (*Program.cs*) with the `AddHostedService` extension method:</span></span>

* <span data-ttu-id="3f551-170">バックグラウンド タスクでスコープ サービスを使用する</span><span class="sxs-lookup"><span data-stu-id="3f551-170">Consuming a scoped service in a background task</span></span> <span data-ttu-id="3f551-171">[BackgroundService](#backgroundservice-base-class) 内で[スコープ サービス](xref:fundamentals/dependency-injection#service-lifetimes)を使用するには、スコープを作成します。</span><span class="sxs-lookup"><span data-stu-id="3f551-171">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within a [BackgroundService](#backgroundservice-base-class), create a scope.</span></span> <span data-ttu-id="3f551-172">既定では、ホステッド サービスのスコープは作成されません。</span><span class="sxs-lookup"><span data-stu-id="3f551-172">No scope is created for a hosted service by default.</span></span>
* <span data-ttu-id="3f551-173">バックグラウンド タスクのスコープ サービスには、バックグラウンド タスクのロジックが含まれています。</span><span class="sxs-lookup"><span data-stu-id="3f551-173">The scoped background task service contains the background task's logic.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

<span data-ttu-id="3f551-174">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="3f551-174">In the following example:</span></span> <span data-ttu-id="3f551-175">サービスは非同期です。</span><span class="sxs-lookup"><span data-stu-id="3f551-175">The service is asynchronous.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=19,22-35)]

<span data-ttu-id="3f551-176">`DoWork` メソッドは `Task` を返します。</span><span class="sxs-lookup"><span data-stu-id="3f551-176">The `DoWork` method returns a `Task`.</span></span> <span data-ttu-id="3f551-177">デモンストレーションのために、10 秒の遅延が `DoWork` メソッドで待機されます。</span><span class="sxs-lookup"><span data-stu-id="3f551-177">For demonstration purposes, a delay of ten seconds is awaited in the `DoWork` method.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet2)]

## <a name="queued-background-tasks"></a><span data-ttu-id="3f551-178"><xref:Microsoft.Extensions.Logging.ILogger> がサービスに挿入されます。</span><span class="sxs-lookup"><span data-stu-id="3f551-178">An <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service.</span></span>

<span data-ttu-id="3f551-179">ホステッド サービスはスコープを作成してバックグラウンド タスクのスコープ サービスを解決し、`DoWork` メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="3f551-179">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

<span data-ttu-id="3f551-180">`ExecuteAsync` で待機していた `DoWork` が `Task` を返します。</span><span class="sxs-lookup"><span data-stu-id="3f551-180">`DoWork` returns a `Task`, which is awaited in `ExecuteAsync`:</span></span>

* <span data-ttu-id="3f551-181">サービスは `IHostBuilder.ConfigureServices` (*Program.cs*) に登録されています。</span><span class="sxs-lookup"><span data-stu-id="3f551-181">The services are registered in `IHostBuilder.ConfigureServices` (*Program.cs*).</span></span>
* <span data-ttu-id="3f551-182">ホステッド サービスは、`AddHostedService` 拡張メソッドを使用して登録されます。</span><span class="sxs-lookup"><span data-stu-id="3f551-182">The hosted service is registered with the `AddHostedService` extension method:</span></span>
* <span data-ttu-id="3f551-183">キューに格納されたバックグラウンド タスク</span><span class="sxs-lookup"><span data-stu-id="3f551-183">Queued background tasks</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=28-29,33)]

<span data-ttu-id="3f551-184">バックグラウンド タスク キューは、.NET 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> に基づいています。</span><span class="sxs-lookup"><span data-stu-id="3f551-184">A background task queue is based on the .NET 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*>:</span></span>

* <span data-ttu-id="3f551-185">次の `QueueHostedService` の例では以下のようになります。</span><span class="sxs-lookup"><span data-stu-id="3f551-185">In the following `QueueHostedService` example:</span></span>
* <span data-ttu-id="3f551-186">`ExecuteAsync` で待機していた `BackgroundProcessing` メソッドが `Task`を返します。</span><span class="sxs-lookup"><span data-stu-id="3f551-186">The `BackgroundProcessing` method returns a `Task`, which is awaited in `ExecuteAsync`.</span></span>
* <span data-ttu-id="3f551-187">`BackgroundProcessing` で、キュー内のバックグラウンド タスクがデキューされ、実行されます。</span><span class="sxs-lookup"><span data-stu-id="3f551-187">Background tasks in the queue are dequeued and executed in `BackgroundProcessing`.</span></span>
  * <span data-ttu-id="3f551-188">作業項目が待機されてから、`StopAsync` でサービスが停止します。</span><span class="sxs-lookup"><span data-stu-id="3f551-188">Work items are awaited before the service stops in `StopAsync`.</span></span>
  * <span data-ttu-id="3f551-189">`MonitorLoop` サービスは、`w` キーが入力デバイスで選択されると常に、ホステッド サービスのためにタスクのエンキューを処理します。</span><span class="sxs-lookup"><span data-stu-id="3f551-189">A `MonitorLoop` service handles enqueuing tasks for the hosted service whenever the `w` key is selected on an input device:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/MonitorLoop.cs?name=snippet_Monitor&highlight=7,33)]

<span data-ttu-id="3f551-190">`IBackgroundTaskQueue` が `MonitorLoop` サービスに挿入されます。</span><span class="sxs-lookup"><span data-stu-id="3f551-190">The `IBackgroundTaskQueue` is injected into the `MonitorLoop` service.</span></span> <span data-ttu-id="3f551-191">`IBackgroundTaskQueue.QueueBackgroundWorkItem` が呼び出され、作業項目がエンキューされます。</span><span class="sxs-lookup"><span data-stu-id="3f551-191">`IBackgroundTaskQueue.QueueBackgroundWorkItem` is called to enqueue a work item.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet3)]

<span data-ttu-id="3f551-192">作業項目により、実行時間の長いバックグラウンド タスクがシミュレートされます。</span><span class="sxs-lookup"><span data-stu-id="3f551-192">The work item simulates a long-running background task:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet4)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3f551-193">5 秒間の遅延が 3 回実行されます (`Task.Delay`)。</span><span class="sxs-lookup"><span data-stu-id="3f551-193">Three 5-second delays are executed (`Task.Delay`).</span></span> <span data-ttu-id="3f551-194">タスクが取り消された場合、`try-catch` ステートメントによって <xref:System.OperationCanceledException> がトラップされます。</span><span class="sxs-lookup"><span data-stu-id="3f551-194">A `try-catch` statement traps <xref:System.OperationCanceledException> if the task is cancelled.</span></span> <span data-ttu-id="3f551-195">サービスは `IHostBuilder.ConfigureServices` (*Program.cs*) に登録されています。</span><span class="sxs-lookup"><span data-stu-id="3f551-195">The services are registered in `IHostBuilder.ConfigureServices` (*Program.cs*).</span></span>

* <span data-ttu-id="3f551-196">ホステッド サービスは、`AddHostedService` 拡張メソッドを使用して登録されます。</span><span class="sxs-lookup"><span data-stu-id="3f551-196">The hosted service is registered with the `AddHostedService` extension method:</span></span>
* <span data-ttu-id="3f551-197">`MonitorLoop` は `Program.Main` で開始されます。</span><span class="sxs-lookup"><span data-stu-id="3f551-197">`MonitorLoop` is started in `Program.Main`:</span></span> <span data-ttu-id="3f551-198">ASP.NET Core では、バックグラウンド タスクを*ホステッド サービス*として実装することができます。</span><span class="sxs-lookup"><span data-stu-id="3f551-198">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span></span>
* <span data-ttu-id="3f551-199">ホストされるサービスは、<xref:Microsoft.Extensions.Hosting.IHostedService> インターフェイスを実装するバックグラウンド タスク ロジックを持つクラスです。</span><span class="sxs-lookup"><span data-stu-id="3f551-199">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span>

<span data-ttu-id="3f551-200">このトピックでは、3 つのホステッド サービスの例について説明します。</span><span class="sxs-lookup"><span data-stu-id="3f551-200">This topic provides three hosted service examples:</span></span>

## <a name="package"></a><span data-ttu-id="3f551-201">タイマーで実行されるバックグラウンド タスク。</span><span class="sxs-lookup"><span data-stu-id="3f551-201">Background task that runs on a timer.</span></span>

<span data-ttu-id="3f551-202">[スコープ サービス](xref:fundamentals/dependency-injection#service-lifetimes)をアクティブ化するホステッド サービス。</span><span class="sxs-lookup"><span data-stu-id="3f551-202">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="ihostedservice-interface"></a><span data-ttu-id="3f551-203">スコープ サービスは[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection) を使用できます</span><span class="sxs-lookup"><span data-stu-id="3f551-203">The scoped service can use [dependency injection (DI)](xref:fundamentals/dependency-injection)</span></span>

<span data-ttu-id="3f551-204">連続して実行される、キューに格納されたバックグラウンド タスク。</span><span class="sxs-lookup"><span data-stu-id="3f551-204">Queued background tasks that run sequentially.</span></span> <span data-ttu-id="3f551-205">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="3f551-205">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

* <span data-ttu-id="3f551-206">Package</span><span class="sxs-lookup"><span data-stu-id="3f551-206">Package</span></span> <span data-ttu-id="3f551-207">[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)を参照するか、[Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) パッケージへのパッケージ参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="3f551-207">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package.</span></span> <span data-ttu-id="3f551-208">IHostedService インターフェイス</span><span class="sxs-lookup"><span data-stu-id="3f551-208">IHostedService interface</span></span>

* <span data-ttu-id="3f551-209">ホステッド サービスでは、<xref:Microsoft.Extensions.Hosting.IHostedService> インターフェイスを実装します。</span><span class="sxs-lookup"><span data-stu-id="3f551-209">Hosted services implement the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="3f551-210">このインターフェイスは、ホストによって管理されるオブジェクトの 2 つのメソッドを定義します。</span><span class="sxs-lookup"><span data-stu-id="3f551-210">The interface defines two methods for objects that are managed by the host:</span></span> <span data-ttu-id="3f551-211">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*): `StartAsync` には、バックグラウンド タスクを開始するロジックが含まれています。</span><span class="sxs-lookup"><span data-stu-id="3f551-211">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*): `StartAsync` contains the logic to start the background task.</span></span>

  <span data-ttu-id="3f551-212">[Web ホスト](xref:fundamentals/host/web-host) を使用する場合は、サーバーが起動し、[IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) がトリガーされた後で、`StartAsync` が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="3f551-212">When using the [Web Host](xref:fundamentals/host/web-host), `StartAsync` is called after the server has started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span></span> <span data-ttu-id="3f551-213">[汎用ホスト](xref:fundamentals/host/generic-host) を使用する場合は、`ApplicationStarted` がトリガーされる前に `StartAsync` が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="3f551-213">When using the [Generic Host](xref:fundamentals/host/generic-host), `StartAsync` is called before `ApplicationStarted` is triggered.</span></span>

  * <span data-ttu-id="3f551-214">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*):ホストが正常なシャットダウンを実行しているときにトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="3f551-214">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*): Triggered when the host is performing a graceful shutdown.</span></span>
  * <span data-ttu-id="3f551-215">`StopAsync` には、バックグラウンド タスクを終了するロジックが含まれています。</span><span class="sxs-lookup"><span data-stu-id="3f551-215">`StopAsync` contains the logic to end the background task.</span></span>

  <span data-ttu-id="3f551-216">アンマネージ リソースを破棄するには、<xref:System.IDisposable> と[ファイナライザー (デストラクター)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) を実装します。</span><span class="sxs-lookup"><span data-stu-id="3f551-216">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span></span>

  <span data-ttu-id="3f551-217">キャンセル トークンには、シャットダウン プロセスが正常に行われないことを示す、既定の 5 秒間のタイムアウトが含まれています。</span><span class="sxs-lookup"><span data-stu-id="3f551-217">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span></span> <span data-ttu-id="3f551-218">キャンセルがトークンに要求された場合:</span><span class="sxs-lookup"><span data-stu-id="3f551-218">When cancellation is requested on the token:</span></span>

  <span data-ttu-id="3f551-219">アプリで実行されている残りのバックグラウンド操作が中止します。</span><span class="sxs-lookup"><span data-stu-id="3f551-219">Any remaining background operations that the app is performing should be aborted.</span></span>

  * <span data-ttu-id="3f551-220">`StopAsync` で呼び出されたすべてのメソッドが速やかに戻ります。</span><span class="sxs-lookup"><span data-stu-id="3f551-220">Any methods called in `StopAsync` should return promptly.</span></span> <span data-ttu-id="3f551-221">ただし、キャンセルが要求された後もタスクは破棄されません&mdash;呼び出し元がすべてのタスクの完了を待機します。</span><span class="sxs-lookup"><span data-stu-id="3f551-221">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span></span>
  * <span data-ttu-id="3f551-222">アプリが予期せずシャットダウンした場合 (たとえば、アプリのプロセスが失敗した場合)、`StopAsync` は呼び出されないことがあります。</span><span class="sxs-lookup"><span data-stu-id="3f551-222">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span></span> <span data-ttu-id="3f551-223">そのため、`StopAsync` で呼び出されたメソッドや行われた操作が実行されない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="3f551-223">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span></span>

<span data-ttu-id="3f551-224">既定の 5 秒のシャットダウン タイムアウトを延長するには、次を設定します。</span><span class="sxs-lookup"><span data-stu-id="3f551-224">To extend the default five second shutdown timeout, set:</span></span> <span data-ttu-id="3f551-225"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> (汎用ホストを使用するとき)</span><span class="sxs-lookup"><span data-stu-id="3f551-225"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span></span>

## <a name="timed-background-tasks"></a><span data-ttu-id="3f551-226">詳細については、「<xref:fundamentals/host/generic-host#shutdown-timeout>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3f551-226">For more information, see <xref:fundamentals/host/generic-host#shutdown-timeout>.</span></span>

<span data-ttu-id="3f551-227">シャットダウン タイムアウトのホスト構成設定 (Web ホストを使用するとき)</span><span class="sxs-lookup"><span data-stu-id="3f551-227">Shutdown timeout host configuration setting when using Web Host.</span></span> <span data-ttu-id="3f551-228">詳細については、「<xref:fundamentals/host/web-host#shutdown-timeout>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3f551-228">For more information, see <xref:fundamentals/host/web-host#shutdown-timeout>.</span></span> <span data-ttu-id="3f551-229">ホステッド サービスは、アプリの起動時に一度アクティブ化され、アプリのシャットダウン時に正常にシャットダウンされます。</span><span class="sxs-lookup"><span data-stu-id="3f551-229">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=15-16,30,37)]

<span data-ttu-id="3f551-230">バックグラウンド タスクの実行中にエラーがスローされた場合、`StopAsync` が呼び出されていなくても `Dispose` を呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="3f551-230">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span></span>

<span data-ttu-id="3f551-231">時間が指定されたバックグラウンド タスク</span><span class="sxs-lookup"><span data-stu-id="3f551-231">Timed background tasks</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a><span data-ttu-id="3f551-232">時間が指定されたバックグラウンド タスクは、[System.Threading.Timer](xref:System.Threading.Timer) クラスを利用します。</span><span class="sxs-lookup"><span data-stu-id="3f551-232">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span></span>

<span data-ttu-id="3f551-233">このタイマーはタスクの `DoWork` メソッドをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="3f551-233">The timer triggers the task's `DoWork` method.</span></span> <span data-ttu-id="3f551-234">タイマーは `StopAsync` で無効になり、`Dispose` でサービス コンテナーが破棄されたときに破棄されます。</span><span class="sxs-lookup"><span data-stu-id="3f551-234">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span></span>

<span data-ttu-id="3f551-235">前の `DoWork` の実行が完了するまで <xref:System.Threading.Timer> は待機されないため、ここで示したアプローチはすべてのシナリオに適しているとは限りません。</span><span class="sxs-lookup"><span data-stu-id="3f551-235">The <xref:System.Threading.Timer> doesn't wait for previous executions of `DoWork` to finish, so the approach shown might not be suitable for every scenario.</span></span> <span data-ttu-id="3f551-236">サービスは、`AddHostedService` 拡張メソッドを使用して `Startup.ConfigureServices` に登録されます。</span><span class="sxs-lookup"><span data-stu-id="3f551-236">The service is registered in `Startup.ConfigureServices` with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

<span data-ttu-id="3f551-237">バックグラウンド タスクでスコープ サービスを使用する</span><span class="sxs-lookup"><span data-stu-id="3f551-237">Consuming a scoped service in a background task</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=29-36)]

<span data-ttu-id="3f551-238">`IHostedService` 内で[スコープ サービス](xref:fundamentals/dependency-injection#service-lifetimes)を使用するには、スコープを作成します。</span><span class="sxs-lookup"><span data-stu-id="3f551-238">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within an `IHostedService`, create a scope.</span></span> <span data-ttu-id="3f551-239">既定では、ホステッド サービスのスコープは作成されません。</span><span class="sxs-lookup"><span data-stu-id="3f551-239">No scope is created for a hosted service by default.</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet2)]

## <a name="queued-background-tasks"></a><span data-ttu-id="3f551-240">バックグラウンド タスクのスコープ サービスには、バックグラウンド タスクのロジックが含まれています。</span><span class="sxs-lookup"><span data-stu-id="3f551-240">The scoped background task service contains the background task's logic.</span></span>

<span data-ttu-id="3f551-241">次の例では、<xref:Microsoft.Extensions.Logging.ILogger> がサービスに挿入されています。</span><span class="sxs-lookup"><span data-stu-id="3f551-241">In the following example, an <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

<span data-ttu-id="3f551-242">ホステッド サービスはスコープを作成してバックグラウンド タスクのスコープ サービスを解決し、`DoWork` メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="3f551-242">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=21,25)]

<span data-ttu-id="3f551-243">サービスは `Startup.ConfigureServices` に登録されています。</span><span class="sxs-lookup"><span data-stu-id="3f551-243">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="3f551-244">`IHostedService` の実装は、`AddHostedService` 拡張メソッドで登録されます。</span><span class="sxs-lookup"><span data-stu-id="3f551-244">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet3)]

<span data-ttu-id="3f551-245">キューに格納されたバックグラウンド タスク</span><span class="sxs-lookup"><span data-stu-id="3f551-245">Queued background tasks</span></span>

* <span data-ttu-id="3f551-246">バックグラウンド タスク キューは、.NET Framework 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([暫定的に ASP.NET Core に組み込まれる予定](https://github.com/aspnet/Hosting/issues/1280)) に基づいています。</span><span class="sxs-lookup"><span data-stu-id="3f551-246">A background task queue is based on the .NET Framework 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([tentatively scheduled to be built-in for ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span></span>
* <span data-ttu-id="3f551-247">`QueueHostedService` では、キュー内のバックグラウンド タスクは [BackgroundService](#backgroundservice-base-class) としてデキューされ、実行されます。これは、長時間実行する `IHostedService` を構成するための基本クラスです。</span><span class="sxs-lookup"><span data-stu-id="3f551-247">In `QueueHostedService`, background tasks in the queue are dequeued and executed as a [BackgroundService](#backgroundservice-base-class), which is a base class for implementing a long running `IHostedService`:</span></span> <span data-ttu-id="3f551-248">サービスは `Startup.ConfigureServices` に登録されています。</span><span class="sxs-lookup"><span data-stu-id="3f551-248">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="3f551-249">`IHostedService` の実装は、`AddHostedService` 拡張メソッドで登録されます。</span><span class="sxs-lookup"><span data-stu-id="3f551-249">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="3f551-250">インデックス ページ モデル クラスで:</span><span class="sxs-lookup"><span data-stu-id="3f551-250">In the Index page model class:</span></span> <span data-ttu-id="3f551-251">`IBackgroundTaskQueue` がコンストラクターに挿入され、`Queue` に割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="3f551-251">The `IBackgroundTaskQueue` is injected into the constructor and assigned to `Queue`.</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet2)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="3f551-252"><xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> が挿入され、`_serviceScopeFactory` に割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="3f551-252">An <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> is injected and assigned to `_serviceScopeFactory`.</span></span>

* <span data-ttu-id="3f551-253">ファクトリは、スコープ内でサービス作成するための <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> のインスタンス作成に使用されます。</span><span class="sxs-lookup"><span data-stu-id="3f551-253">The factory is used to create instances of <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>, which is used to create services within a scope.</span></span>
* <span data-ttu-id="3f551-254">スコープは、アプリの `AppDbContext` ([スコープ サービス](xref:fundamentals/dependency-injection#service-lifetimes)) を使用し、データベース レコードを `IBackgroundTaskQueue` (シングルトン サービス) に書き込むために作成されます。</span><span class="sxs-lookup"><span data-stu-id="3f551-254">A scope is created in order to use the app's `AppDbContext` (a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes)) to write database records in the `IBackgroundTaskQueue` (a singleton service).</span></span>
* <xref:System.Threading.Timer>
