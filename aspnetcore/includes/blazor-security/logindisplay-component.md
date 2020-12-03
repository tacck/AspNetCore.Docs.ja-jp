<span data-ttu-id="cb3c6-101">`LoginDisplay` コンポーネント (`Shared/LoginDisplay.razor`) は `MainLayout` コンポーネント (`Shared/MainLayout.razor`) でレンダリングされます。このコンポーネントによって次の動作が管理されます。</span><span class="sxs-lookup"><span data-stu-id="cb3c6-101">The `LoginDisplay` component (`Shared/LoginDisplay.razor`) is rendered in the `MainLayout` component (`Shared/MainLayout.razor`) and manages the following behaviors:</span></span>

* <span data-ttu-id="cb3c6-102">認証されたユーザーの場合:</span><span class="sxs-lookup"><span data-stu-id="cb3c6-102">For authenticated users:</span></span>
  * <span data-ttu-id="cb3c6-103">現在のユーザー名が表示されます。</span><span class="sxs-lookup"><span data-stu-id="cb3c6-103">Displays the current username.</span></span>
  * <span data-ttu-id="cb3c6-104">アプリからログアウトするためのボタンが用意されます。</span><span class="sxs-lookup"><span data-stu-id="cb3c6-104">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="cb3c6-105">匿名ユーザーの場合は、ログインするオプションが用意されます。</span><span class="sxs-lookup"><span data-stu-id="cb3c6-105">For anonymous users, offers the option to log in.</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        Hello, @context.User.Identity.Name!
        <button class="nav-link btn btn-link" @onclick="BeginLogout">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/login">Log in</a>
    </NotAuthorized>
</AuthorizeView>

@code {
    private async Task BeginLogout(MouseEventArgs args)
    {
        await SignOutManager.SetSignOutState();
        Navigation.NavigateTo("authentication/logout");
    }
}
```
