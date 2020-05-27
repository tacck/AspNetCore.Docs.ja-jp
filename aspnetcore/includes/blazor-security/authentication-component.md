コンポーネントによって生成されるページ `Authentication` (*Pages/Authentication. razor*) は、さまざまな認証ステージを処理するために必要なルートを定義します。

<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView>コンポーネント:

* は、 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/)パッケージによって提供されています。
* 認証の各段階で、適切な操作の実行を管理します。

```razor
@page "/authentication/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code {
    [Parameter]
    public string Action { get; set; }
}
```
