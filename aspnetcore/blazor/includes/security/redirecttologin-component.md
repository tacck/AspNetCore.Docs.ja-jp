<span data-ttu-id="030da-101">`RedirectToLogin` コンポーネント (`Shared/RedirectToLogin.razor`) は:</span><span class="sxs-lookup"><span data-stu-id="030da-101">The `RedirectToLogin` component (`Shared/RedirectToLogin.razor`):</span></span>

* <span data-ttu-id="030da-102">承認されていないユーザーのログイン ページへのリダイレクトを管理します。</span><span class="sxs-lookup"><span data-stu-id="030da-102">Manages redirecting unauthorized users to the login page.</span></span>
* <span data-ttu-id="030da-103">認証が成功した場合にそのページに戻ることができるように、ユーザーがアクセスしようとしている現在の URL を保持します。</span><span class="sxs-lookup"><span data-stu-id="030da-103">Preserves the current URL that the user is attempting to access so that they can be returned to that page if authentication is successful.</span></span>

```razor
@inject NavigationManager Navigation
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@code {
    protected override void OnInitialized()
    {
        Navigation.NavigateTo(
            $"authentication/login?returnUrl={Uri.EscapeDataString(Navigation.Uri)}");
    }
}
```
