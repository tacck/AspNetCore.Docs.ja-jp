<span data-ttu-id="2948e-101">コンポーネント `LoginDisplay` (*Shared/logindisplay*) は、 `MainLayout` コンポーネント (*shared/mainlayout*) にレンダリングされ、次の動作を管理します。</span><span class="sxs-lookup"><span data-stu-id="2948e-101">The `LoginDisplay` component (*Shared/LoginDisplay.razor*) is rendered in the `MainLayout` component (*Shared/MainLayout.razor*) and manages the following behaviors:</span></span>

* <span data-ttu-id="2948e-102">認証されたユーザーの場合:</span><span class="sxs-lookup"><span data-stu-id="2948e-102">For authenticated users:</span></span>
  * <span data-ttu-id="2948e-103">現在のユーザー名を表示します。</span><span class="sxs-lookup"><span data-stu-id="2948e-103">Displays the current username.</span></span>
  * <span data-ttu-id="2948e-104">アプリからログアウトするためのボタンが用意されています。</span><span class="sxs-lookup"><span data-stu-id="2948e-104">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="2948e-105">匿名ユーザーの場合、にログインするオプションが用意されています。</span><span class="sxs-lookup"><span data-stu-id="2948e-105">For anonymous users, offers the option to log in.</span></span>

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
