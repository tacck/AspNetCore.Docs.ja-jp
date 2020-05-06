> [!WARNING]
> <span data-ttu-id="30513-101">ルーティングで[バグ](https://github.com/dotnet/aspnetcore/issues/18677)が原因で、**キャッチオール** パラメーターがルートと正しく一致しない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="30513-101">A **catch-all** parameter may match routes incorrectly due to a [bug](https://github.com/dotnet/aspnetcore/issues/18677) in routing.</span></span> <span data-ttu-id="30513-102">このバグの影響を受けるアプリには、次の特性があります。</span><span class="sxs-lookup"><span data-stu-id="30513-102">Apps impacted by this bug have the following characteristics:</span></span>
>
> * <span data-ttu-id="30513-103">キャッチオール ルート (たとえば、`{**slug}"`)</span><span class="sxs-lookup"><span data-stu-id="30513-103">A catch-all route, for example, `{**slug}"`</span></span>
> * <span data-ttu-id="30513-104">キャッチオール ルートが、一致すべき要求と一致しません。</span><span class="sxs-lookup"><span data-stu-id="30513-104">The catch-all route fails to match requests it should match.</span></span>
> * <span data-ttu-id="30513-105">他のルートを削除すると、キャッチオール ルートが機能し始めます。</span><span class="sxs-lookup"><span data-stu-id="30513-105">Removing other routes makes catch-all route start working.</span></span>
>
> <span data-ttu-id="30513-106">このバグが発生するケースの例については、GitHub のバグ [18677](https://github.com/dotnet/aspnetcore/issues/18677) および [16579](https://github.com/dotnet/aspnetcore/issues/16579) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="30513-106">See GitHub bugs [18677](https://github.com/dotnet/aspnetcore/issues/18677) and [16579](https://github.com/dotnet/aspnetcore/issues/16579) for example cases that hit this bug.</span></span>
>
> <span data-ttu-id="30513-107">このバグについてはオプトイン修正が計画されています。</span><span class="sxs-lookup"><span data-stu-id="30513-107">An opt-in fix for this bug is planned.</span></span> <span data-ttu-id="30513-108">このドキュメントは、修正プログラムがリリースされたときに更新されます。</span><span class="sxs-lookup"><span data-stu-id="30513-108">This doc will be updated when the patch is released.</span></span> <span data-ttu-id="30513-109">修正プログラムがリリースされると、次のコードによって、このバグを修正する内部スイッチが設定されます。</span><span class="sxs-lookup"><span data-stu-id="30513-109">When the patch is released, the following code will set an internal switch that fixes this bug:</span></span>
>
>```
>public static void Main(string[] args)
>{
>    AppContext.SetSwitch("Microsoft.AspNetCore.Routing.UseCorrectCatchAllBehavior", true);
>    CreateHostBuilder(args).Build().Run();
>}
>// Remaining code removed for brevity.
>```