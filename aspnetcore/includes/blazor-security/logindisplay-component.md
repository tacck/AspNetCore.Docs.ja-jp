コンポーネント `LoginDisplay` (*Shared/logindisplay*) は、 `MainLayout` コンポーネント (*shared/mainlayout*) にレンダリングされ、次の動作を管理します。

* 認証されたユーザーの場合:
  * 現在のユーザー名を表示します。
  * アプリからログアウトするためのボタンが用意されています。
* 匿名ユーザーの場合、にログインするオプションが用意されています。

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
