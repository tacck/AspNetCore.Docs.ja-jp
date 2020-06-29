`App` コンポーネント (`App.razor`) は、Blazor Server アプリにある `App` コンポーネントに似ています。

* <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> コンポーネントによって、アプリの残りの部分に <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> を公開する動作が管理されます。
* <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> コンポーネントによって、現在のユーザーには所与のページへのアクセスが許可されます。それ以外では、`RedirectToLogin` コンポーネントがレンダリングされます。
* `RedirectToLogin` コンポーネントによって、承認されていないユーザーのログイン ページへのリダイレクトが管理されます。

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="@typeof(Program).Assembly">
        <Found Context="routeData">
            <AuthorizeRouteView RouteData="@routeData" 
                DefaultLayout="@typeof(MainLayout)">
                <NotAuthorized>
                    @if (!context.User.Identity.IsAuthenticated)
                    {
                        <RedirectToLogin />
                    }
                    else
                    {
                        <p>
                            You are not authorized to access 
                            this resource.
                        </p>
                    }
                </NotAuthorized>
            </AuthorizeRouteView>
        </Found>
        <NotFound>
            <LayoutView Layout="@typeof(MainLayout)">
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </NotFound>
    </Router>
</CascadingAuthenticationState>
```
