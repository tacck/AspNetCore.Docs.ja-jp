<span data-ttu-id="88437-101">コンポーネントによって生成されるページ `Authentication` (*Pages/Authentication. razor*) は、さまざまな認証ステージを処理するために必要なルートを定義します。</span><span class="sxs-lookup"><span data-stu-id="88437-101">The page produced by the `Authentication` component (*Pages/Authentication.razor*) defines the routes required for handling different authentication stages.</span></span>

<span data-ttu-id="88437-102"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView>コンポーネント:</span><span class="sxs-lookup"><span data-stu-id="88437-102">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> component:</span></span>

* <span data-ttu-id="88437-103">は、 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/)パッケージによって提供されています。</span><span class="sxs-lookup"><span data-stu-id="88437-103">Is provided by the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package.</span></span>
* <span data-ttu-id="88437-104">認証の各段階で、適切な操作の実行を管理します。</span><span class="sxs-lookup"><span data-stu-id="88437-104">Manages performing the appropriate actions at each stage of authentication.</span></span>

```razor
@page "/authentication/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code {
    [Parameter]
    public string Action { get; set; }
}
```
