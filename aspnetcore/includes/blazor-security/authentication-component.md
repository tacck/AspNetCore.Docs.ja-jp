`Authentication` コンポーネント (`Pages/Authentication.razor`) によって生成されるページによって、さまざまな認証ステージを処理するために必要なルートが定義されます。

<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> コンポーネント:

* [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) パッケージによって提供されます。
* 認証の各段階における適切なアクションの実行を管理します。

```razor
@page "/authentication/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code {
    [Parameter]
    public string Action { get; set; }
}
```
