> [!WARNING]
> <span data-ttu-id="a1b0c-101">ルーティングで[バグ](https://github.com/dotnet/aspnetcore/issues/18677)が原因で、**キャッチオール** パラメーターがルートと正しく一致しない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a1b0c-101">A **catch-all** parameter may match routes incorrectly due to a [bug](https://github.com/dotnet/aspnetcore/issues/18677) in routing.</span></span> <span data-ttu-id="a1b0c-102">このバグの影響を受けるアプリには、次の特性があります。</span><span class="sxs-lookup"><span data-stu-id="a1b0c-102">Apps impacted by this bug have the following characteristics:</span></span>
>
> * <span data-ttu-id="a1b0c-103">キャッチオール ルート (たとえば、`{**slug}"`)</span><span class="sxs-lookup"><span data-stu-id="a1b0c-103">A catch-all route, for example, `{**slug}"`</span></span>
> * <span data-ttu-id="a1b0c-104">キャッチオール ルートが、一致すべき要求と一致しません。</span><span class="sxs-lookup"><span data-stu-id="a1b0c-104">The catch-all route fails to match requests it should match.</span></span>
> * <span data-ttu-id="a1b0c-105">他のルートを削除すると、キャッチオール ルートが機能し始めます。</span><span class="sxs-lookup"><span data-stu-id="a1b0c-105">Removing other routes makes catch-all route start working.</span></span>
>
> <span data-ttu-id="a1b0c-106">このバグが発生するケースの例については、GitHub のバグ [18677](https://github.com/dotnet/aspnetcore/issues/18677) および [16579](https://github.com/dotnet/aspnetcore/issues/16579) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a1b0c-106">See GitHub bugs [18677](https://github.com/dotnet/aspnetcore/issues/18677) and [16579](https://github.com/dotnet/aspnetcore/issues/16579) for example cases that hit this bug.</span></span>
>
> <span data-ttu-id="a1b0c-107">このバグのオプトイン修正は、 [.Net Core 3.1.301 SDK 以降](https://dotnet.microsoft.com/download/dotnet-core/3.1)に含まれています。</span><span class="sxs-lookup"><span data-stu-id="a1b0c-107">An opt-in fix for this bug is contained in [.NET Core 3.1.301 SDK and later](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span> <span data-ttu-id="a1b0c-108">次のコードは、このバグを修正する内部スイッチを設定します。</span><span class="sxs-lookup"><span data-stu-id="a1b0c-108">The following code sets an internal switch that fixes this bug:</span></span>
>
>```
>public static void Main(string[] args)
>{
>    AppContext.SetSwitch("Microsoft.AspNetCore.Routing.UseCorrectCatchAllBehavior", 
>                          true);
>    CreateHostBuilder(args).Build().Run();
>}
>// Remaining code removed for brevity.
>```