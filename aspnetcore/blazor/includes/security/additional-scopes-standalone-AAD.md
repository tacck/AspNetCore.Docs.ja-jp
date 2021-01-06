<xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions.DefaultAccessTokenScopes> で `User.Read` アクセス許可に対して <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> を追加します。

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes
        .Add("https://graph.microsoft.com/User.Read");
});
```
