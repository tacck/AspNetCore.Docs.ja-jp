<span data-ttu-id="7b795-101">`Authentication` コンポーネント (`Pages/Authentication.razor`) によって生成されるページによって、さまざまな認証ステージを処理するために必要なルートが定義されます。</span><span class="sxs-lookup"><span data-stu-id="7b795-101">The page produced by the `Authentication` component (`Pages/Authentication.razor`) defines the routes required for handling different authentication stages.</span></span>

<span data-ttu-id="7b795-102"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> コンポーネント:</span><span class="sxs-lookup"><span data-stu-id="7b795-102">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> component:</span></span>

* <span data-ttu-id="7b795-103">[`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) パッケージによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="7b795-103">Is provided by the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package.</span></span>
* <span data-ttu-id="7b795-104">認証の各段階における適切なアクションの実行を管理します。</span><span class="sxs-lookup"><span data-stu-id="7b795-104">Manages performing the appropriate actions at each stage of authentication.</span></span>

```razor
@page "/authentication/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code {
    [Parameter]
    public string Action { get; set; }
}
```
