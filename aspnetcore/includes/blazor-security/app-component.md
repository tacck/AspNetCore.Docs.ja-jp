コンポーネント`App` (*app.xaml*) は、Blazor Server apps に含ま`App`れるコンポーネントに似ています。

* コンポーネント`CascadingAuthenticationState`は、アプリの`AuthenticationState`残りの部分へのの公開を管理します。
* `AuthorizeRouteView`コンポーネントは、現在のユーザーが特定のページにアクセスする権限を持っていること`RedirectToLogin` 、またはコンポーネントを表示する権限を持っていることを確認します。
* この`RedirectToLogin`コンポーネントは、許可されていないユーザーのログインページへのリダイレクトを管理します。

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
