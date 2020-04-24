<span data-ttu-id="da4ae-101">コンポーネント`App` (*app.xaml*) は、Blazor Server apps に含ま`App`れるコンポーネントに似ています。</span><span class="sxs-lookup"><span data-stu-id="da4ae-101">The `App` component (*App.razor*) is similar to the `App` component found in Blazor Server apps:</span></span>

* <span data-ttu-id="da4ae-102">コンポーネント`CascadingAuthenticationState`は、アプリの`AuthenticationState`残りの部分へのの公開を管理します。</span><span class="sxs-lookup"><span data-stu-id="da4ae-102">The `CascadingAuthenticationState` component manages exposing the `AuthenticationState` to the rest of the app.</span></span>
* <span data-ttu-id="da4ae-103">`AuthorizeRouteView`コンポーネントは、現在のユーザーが特定のページにアクセスする権限を持っていること`RedirectToLogin` 、またはコンポーネントを表示する権限を持っていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="da4ae-103">The `AuthorizeRouteView` component makes sure that the current user is authorized to access a given page or otherwise renders the `RedirectToLogin` component.</span></span>
* <span data-ttu-id="da4ae-104">この`RedirectToLogin`コンポーネントは、許可されていないユーザーのログインページへのリダイレクトを管理します。</span><span class="sxs-lookup"><span data-stu-id="da4ae-104">The `RedirectToLogin` component manages redirecting unauthorized users to the login page.</span></span>

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
