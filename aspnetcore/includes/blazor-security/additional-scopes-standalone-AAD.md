<span data-ttu-id="3d2c9-101"><xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions.DefaultAccessTokenScopes> で `User.Read` アクセス許可に対して <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> を追加します。</span><span class="sxs-lookup"><span data-stu-id="3d2c9-101">Add a <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> for `User.Read` permission with <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions.DefaultAccessTokenScopes>:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes
        .Add("https://graph.microsoft.com/User.Read");
});
```
