<span data-ttu-id="7364a-101">`App` コンポーネント (`App.razor`) は、Blazor Server アプリにある `App` コンポーネントに似ています。</span><span class="sxs-lookup"><span data-stu-id="7364a-101">The `App` component (`App.razor`) is similar to the `App` component found in Blazor Server apps:</span></span>

* <span data-ttu-id="7364a-102"><xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> コンポーネントによって、アプリの残りの部分に <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> を公開する動作が管理されます。</span><span class="sxs-lookup"><span data-stu-id="7364a-102">The <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> component manages exposing the <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> to the rest of the app.</span></span>
* <span data-ttu-id="7364a-103"><xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> コンポーネントによって、現在のユーザーには所与のページへのアクセスが許可されます。それ以外では、`RedirectToLogin` コンポーネントがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="7364a-103">The <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> component makes sure that the current user is authorized to access a given page or otherwise renders the `RedirectToLogin` component.</span></span>
* <span data-ttu-id="7364a-104">`RedirectToLogin` コンポーネントによって、承認されていないユーザーのログイン ページへのリダイレクトが管理されます。</span><span class="sxs-lookup"><span data-stu-id="7364a-104">The `RedirectToLogin` component manages redirecting unauthorized users to the login page.</span></span>

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
