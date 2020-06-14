> [!WARNING]
> ルーティングで[バグ](https://github.com/dotnet/aspnetcore/issues/18677)が原因で、**キャッチオール** パラメーターがルートと正しく一致しない可能性があります。 このバグの影響を受けるアプリには、次の特性があります。
>
> * キャッチオール ルート (たとえば、`{**slug}"`)
> * キャッチオール ルートが、一致すべき要求と一致しません。
> * 他のルートを削除すると、キャッチオール ルートが機能し始めます。
>
> このバグが発生するケースの例については、GitHub のバグ [18677](https://github.com/dotnet/aspnetcore/issues/18677) および [16579](https://github.com/dotnet/aspnetcore/issues/16579) を参照してください。
>
> このバグのオプトイン修正は、 [.Net Core 3.1.301 SDK 以降](https://dotnet.microsoft.com/download/dotnet-core/3.1)に含まれています。 次のコードは、このバグを修正する内部スイッチを設定します。
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