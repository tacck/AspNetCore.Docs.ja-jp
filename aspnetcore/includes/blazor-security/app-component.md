`App`コンポーネント (*app.xaml*) は、 `App` Blazor Server apps に含まれるコンポーネントに似ています。

* <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState>コンポーネントは、 <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> アプリの残りの部分へのの公開を管理します。
* コンポーネントは、 <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> 現在のユーザーが特定のページにアクセスする権限を持っていること、またはコンポーネントを表示する権限を持っていることを確認し `RedirectToLogin` ます。
* このコンポーネントは、許可されて `RedirectToLogin` いないユーザーのログインページへのリダイレクトを管理します。

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
