---
<span data-ttu-id="c6639-101">title: author: description: monikerRange: ms. author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="c6639-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6639-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6639-102">'Blazor'</span></span>
- <span data-ttu-id="c6639-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6639-103">'Identity'</span></span>
- <span data-ttu-id="c6639-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6639-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6639-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6639-105">'Razor'</span></span>
- <span data-ttu-id="c6639-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c6639-106">'SignalR' uid:</span></span> 

---
# <a name="persist-additional-claims-and-tokens-from-external-providers-in-aspnet-core"></a><span data-ttu-id="c6639-107">ASP.NET Core で外部プロバイダーからの追加の要求とトークンを保持する</span><span class="sxs-lookup"><span data-stu-id="c6639-107">Persist additional claims and tokens from external providers in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c6639-108">ASP.NET Core アプリは、Facebook、Google、Microsoft、Twitter などの外部認証プロバイダーからの追加の要求とトークンを確立できます。</span><span class="sxs-lookup"><span data-stu-id="c6639-108">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="c6639-109">各プロバイダーは、プラットフォーム上のユーザーに関するさまざまな情報を明らかにしますが、ユーザーデータを受け取って追加の要求に変換するパターンは同じです。</span><span class="sxs-lookup"><span data-stu-id="c6639-109">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="c6639-110">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="c6639-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="c6639-111">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="c6639-111">Prerequisites</span></span>

<span data-ttu-id="c6639-112">アプリでサポートする外部認証プロバイダーを決定します。</span><span class="sxs-lookup"><span data-stu-id="c6639-112">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="c6639-113">各プロバイダーについて、アプリを登録し、クライアント ID とクライアントシークレットを取得します。</span><span class="sxs-lookup"><span data-stu-id="c6639-113">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="c6639-114">詳細については、「<xref:security/authentication/social/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c6639-114">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="c6639-115">このサンプルアプリでは、 [Google 認証プロバイダー](xref:security/authentication/google-logins)を使用します。</span><span class="sxs-lookup"><span data-stu-id="c6639-115">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="c6639-116">クライアント ID とクライアントシークレットを設定する</span><span class="sxs-lookup"><span data-stu-id="c6639-116">Set the client ID and client secret</span></span>

<span data-ttu-id="c6639-117">OAuth 認証プロバイダーは、クライアント ID とクライアントシークレットを使用して、アプリとの信頼関係を確立します。</span><span class="sxs-lookup"><span data-stu-id="c6639-117">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="c6639-118">アプリがプロバイダーに登録されるときに、外部認証プロバイダーによってアプリのクライアント ID とクライアントシークレットの値が作成されます。</span><span class="sxs-lookup"><span data-stu-id="c6639-118">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="c6639-119">アプリが使用する各外部プロバイダーは、プロバイダーのクライアント ID とクライアントシークレットとは別に構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c6639-119">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="c6639-120">詳細については、シナリオに当てはまる外部認証プロバイダーのトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="c6639-120">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="c6639-121">Facebook での認証</span><span class="sxs-lookup"><span data-stu-id="c6639-121">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="c6639-122">Google での認証</span><span class="sxs-lookup"><span data-stu-id="c6639-122">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="c6639-123">Microsoft での認証</span><span class="sxs-lookup"><span data-stu-id="c6639-123">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="c6639-124">Twitter での認証</span><span class="sxs-lookup"><span data-stu-id="c6639-124">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="c6639-125">他の認証プロバイダー</span><span class="sxs-lookup"><span data-stu-id="c6639-125">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="c6639-126">OpenIdConnect</span><span class="sxs-lookup"><span data-stu-id="c6639-126">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="c6639-127">このサンプルアプリでは、google によって提供されるクライアント ID とクライアントシークレットを使用して Google 認証プロバイダーを構成します。</span><span class="sxs-lookup"><span data-stu-id="c6639-127">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="c6639-128">認証スコープを確立する</span><span class="sxs-lookup"><span data-stu-id="c6639-128">Establish the authentication scope</span></span>

<span data-ttu-id="c6639-129">を指定して、プロバイダーから取得するアクセス許可の一覧を指定し <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*> ます。</span><span class="sxs-lookup"><span data-stu-id="c6639-129">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="c6639-130">共通外部プロバイダーの認証スコープは、次の表に表示されます。</span><span class="sxs-lookup"><span data-stu-id="c6639-130">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="c6639-131">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="c6639-131">Provider</span></span>  | <span data-ttu-id="c6639-132">Scope</span><span class="sxs-lookup"><span data-stu-id="c6639-132">Scope</span></span>                                                            |
| ---
<span data-ttu-id="c6639-133">title: author: description: monikerRange: ms. author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="c6639-133">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6639-134">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6639-134">'Blazor'</span></span>
- <span data-ttu-id="c6639-135">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6639-135">'Identity'</span></span>
- <span data-ttu-id="c6639-136">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6639-136">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6639-137">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6639-137">'Razor'</span></span>
- <span data-ttu-id="c6639-138">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c6639-138">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c6639-139">title: author: description: monikerRange: ms. author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="c6639-139">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6639-140">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6639-140">'Blazor'</span></span>
- <span data-ttu-id="c6639-141">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6639-141">'Identity'</span></span>
- <span data-ttu-id="c6639-142">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6639-142">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6639-143">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6639-143">'Razor'</span></span>
- <span data-ttu-id="c6639-144">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c6639-144">'SignalR' uid:</span></span> 

<span data-ttu-id="c6639-145">----- |---title: author: description: monikerRange: ms. author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="c6639-145">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6639-146">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6639-146">'Blazor'</span></span>
- <span data-ttu-id="c6639-147">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6639-147">'Identity'</span></span>
- <span data-ttu-id="c6639-148">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6639-148">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6639-149">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6639-149">'Razor'</span></span>
- <span data-ttu-id="c6639-150">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c6639-150">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c6639-151">title: author: description: monikerRange: ms. author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="c6639-151">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6639-152">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6639-152">'Blazor'</span></span>
- <span data-ttu-id="c6639-153">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6639-153">'Identity'</span></span>
- <span data-ttu-id="c6639-154">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6639-154">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6639-155">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6639-155">'Razor'</span></span>
- <span data-ttu-id="c6639-156">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c6639-156">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c6639-157">title: author: description: monikerRange: ms. author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="c6639-157">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6639-158">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6639-158">'Blazor'</span></span>
- <span data-ttu-id="c6639-159">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6639-159">'Identity'</span></span>
- <span data-ttu-id="c6639-160">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6639-160">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6639-161">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6639-161">'Razor'</span></span>
- <span data-ttu-id="c6639-162">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c6639-162">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c6639-163">title: author: description: monikerRange: ms. author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="c6639-163">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6639-164">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6639-164">'Blazor'</span></span>
- <span data-ttu-id="c6639-165">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6639-165">'Identity'</span></span>
- <span data-ttu-id="c6639-166">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6639-166">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6639-167">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6639-167">'Razor'</span></span>
- <span data-ttu-id="c6639-168">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c6639-168">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c6639-169">title: author: description: monikerRange: ms. author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="c6639-169">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6639-170">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6639-170">'Blazor'</span></span>
- <span data-ttu-id="c6639-171">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6639-171">'Identity'</span></span>
- <span data-ttu-id="c6639-172">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6639-172">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6639-173">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6639-173">'Razor'</span></span>
- <span data-ttu-id="c6639-174">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c6639-174">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c6639-175">title: author: description: monikerRange: ms. author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="c6639-175">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6639-176">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6639-176">'Blazor'</span></span>
- <span data-ttu-id="c6639-177">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6639-177">'Identity'</span></span>
- <span data-ttu-id="c6639-178">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6639-178">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6639-179">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6639-179">'Razor'</span></span>
- <span data-ttu-id="c6639-180">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c6639-180">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c6639-181">title: author: description: monikerRange: ms. author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="c6639-181">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6639-182">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6639-182">'Blazor'</span></span>
- <span data-ttu-id="c6639-183">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6639-183">'Identity'</span></span>
- <span data-ttu-id="c6639-184">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6639-184">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6639-185">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6639-185">'Razor'</span></span>
- <span data-ttu-id="c6639-186">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c6639-186">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c6639-187">title: author: description: monikerRange: ms. author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="c6639-187">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6639-188">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6639-188">'Blazor'</span></span>
- <span data-ttu-id="c6639-189">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6639-189">'Identity'</span></span>
- <span data-ttu-id="c6639-190">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6639-190">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6639-191">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6639-191">'Razor'</span></span>
- <span data-ttu-id="c6639-192">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c6639-192">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c6639-193">title: author: description: monikerRange: ms. author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="c6639-193">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6639-194">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6639-194">'Blazor'</span></span>
- <span data-ttu-id="c6639-195">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6639-195">'Identity'</span></span>
- <span data-ttu-id="c6639-196">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6639-196">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6639-197">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6639-197">'Razor'</span></span>
- <span data-ttu-id="c6639-198">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c6639-198">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c6639-199">title: author: description: monikerRange: ms. author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="c6639-199">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6639-200">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6639-200">'Blazor'</span></span>
- <span data-ttu-id="c6639-201">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6639-201">'Identity'</span></span>
- <span data-ttu-id="c6639-202">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6639-202">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6639-203">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6639-203">'Razor'</span></span>
- <span data-ttu-id="c6639-204">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c6639-204">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c6639-205">title: author: description: monikerRange: ms. author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="c6639-205">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6639-206">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6639-206">'Blazor'</span></span>
- <span data-ttu-id="c6639-207">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6639-207">'Identity'</span></span>
- <span data-ttu-id="c6639-208">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6639-208">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6639-209">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6639-209">'Razor'</span></span>
- <span data-ttu-id="c6639-210">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c6639-210">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c6639-211">title: author: description: monikerRange: ms. author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="c6639-211">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6639-212">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6639-212">'Blazor'</span></span>
- <span data-ttu-id="c6639-213">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6639-213">'Identity'</span></span>
- <span data-ttu-id="c6639-214">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6639-214">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6639-215">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6639-215">'Razor'</span></span>
- <span data-ttu-id="c6639-216">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c6639-216">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c6639-217">title: author: description: monikerRange: ms. author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="c6639-217">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6639-218">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6639-218">'Blazor'</span></span>
- <span data-ttu-id="c6639-219">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6639-219">'Identity'</span></span>
- <span data-ttu-id="c6639-220">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6639-220">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6639-221">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6639-221">'Razor'</span></span>
- <span data-ttu-id="c6639-222">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c6639-222">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c6639-223">title: author: description: monikerRange: ms. author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="c6639-223">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6639-224">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6639-224">'Blazor'</span></span>
- <span data-ttu-id="c6639-225">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6639-225">'Identity'</span></span>
- <span data-ttu-id="c6639-226">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6639-226">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6639-227">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6639-227">'Razor'</span></span>
- <span data-ttu-id="c6639-228">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c6639-228">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c6639-229">title: author: description: monikerRange: ms. author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="c6639-229">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6639-230">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6639-230">'Blazor'</span></span>
- <span data-ttu-id="c6639-231">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6639-231">'Identity'</span></span>
- <span data-ttu-id="c6639-232">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6639-232">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6639-233">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6639-233">'Razor'</span></span>
- <span data-ttu-id="c6639-234">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c6639-234">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c6639-235">title: author: description: monikerRange: ms. author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="c6639-235">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6639-236">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6639-236">'Blazor'</span></span>
- <span data-ttu-id="c6639-237">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6639-237">'Identity'</span></span>
- <span data-ttu-id="c6639-238">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6639-238">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6639-239">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6639-239">'Razor'</span></span>
- <span data-ttu-id="c6639-240">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c6639-240">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c6639-241">title: author: description: monikerRange: ms. author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="c6639-241">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6639-242">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6639-242">'Blazor'</span></span>
- <span data-ttu-id="c6639-243">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6639-243">'Identity'</span></span>
- <span data-ttu-id="c6639-244">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6639-244">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6639-245">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6639-245">'Razor'</span></span>
- <span data-ttu-id="c6639-246">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c6639-246">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c6639-247">title: author: description: monikerRange: ms. author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="c6639-247">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6639-248">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6639-248">'Blazor'</span></span>
- <span data-ttu-id="c6639-249">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6639-249">'Identity'</span></span>
- <span data-ttu-id="c6639-250">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6639-250">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6639-251">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6639-251">'Razor'</span></span>
- <span data-ttu-id="c6639-252">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c6639-252">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c6639-253">title: author: description: monikerRange: ms. author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="c6639-253">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6639-254">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6639-254">'Blazor'</span></span>
- <span data-ttu-id="c6639-255">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6639-255">'Identity'</span></span>
- <span data-ttu-id="c6639-256">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6639-256">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6639-257">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6639-257">'Razor'</span></span>
- <span data-ttu-id="c6639-258">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c6639-258">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c6639-259">title: author: description: monikerRange: ms. author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="c6639-259">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6639-260">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6639-260">'Blazor'</span></span>
- <span data-ttu-id="c6639-261">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6639-261">'Identity'</span></span>
- <span data-ttu-id="c6639-262">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6639-262">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6639-263">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6639-263">'Razor'</span></span>
- <span data-ttu-id="c6639-264">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c6639-264">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c6639-265">title: author: description: monikerRange: ms. author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="c6639-265">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6639-266">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6639-266">'Blazor'</span></span>
- <span data-ttu-id="c6639-267">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6639-267">'Identity'</span></span>
- <span data-ttu-id="c6639-268">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6639-268">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6639-269">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6639-269">'Razor'</span></span>
- <span data-ttu-id="c6639-270">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c6639-270">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c6639-271">title: author: description: monikerRange: ms. author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="c6639-271">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6639-272">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6639-272">'Blazor'</span></span>
- <span data-ttu-id="c6639-273">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6639-273">'Identity'</span></span>
- <span data-ttu-id="c6639-274">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6639-274">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6639-275">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6639-275">'Razor'</span></span>
- <span data-ttu-id="c6639-276">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c6639-276">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c6639-277">title: author: description: monikerRange: ms. author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="c6639-277">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6639-278">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6639-278">'Blazor'</span></span>
- <span data-ttu-id="c6639-279">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6639-279">'Identity'</span></span>
- <span data-ttu-id="c6639-280">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6639-280">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6639-281">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6639-281">'Razor'</span></span>
- <span data-ttu-id="c6639-282">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c6639-282">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c6639-283">title: author: description: monikerRange: ms. author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="c6639-283">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6639-284">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6639-284">'Blazor'</span></span>
- <span data-ttu-id="c6639-285">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6639-285">'Identity'</span></span>
- <span data-ttu-id="c6639-286">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6639-286">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6639-287">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6639-287">'Razor'</span></span>
- <span data-ttu-id="c6639-288">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c6639-288">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c6639-289">title: author: description: monikerRange: ms. author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="c6639-289">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6639-290">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6639-290">'Blazor'</span></span>
- <span data-ttu-id="c6639-291">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6639-291">'Identity'</span></span>
- <span data-ttu-id="c6639-292">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6639-292">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6639-293">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6639-293">'Razor'</span></span>
- <span data-ttu-id="c6639-294">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c6639-294">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c6639-295">title: author: description: monikerRange: ms. author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="c6639-295">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6639-296">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6639-296">'Blazor'</span></span>
- <span data-ttu-id="c6639-297">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6639-297">'Identity'</span></span>
- <span data-ttu-id="c6639-298">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6639-298">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6639-299">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6639-299">'Razor'</span></span>
- <span data-ttu-id="c6639-300">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c6639-300">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c6639-301">title: author: description: monikerRange: ms. author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="c6639-301">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6639-302">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6639-302">'Blazor'</span></span>
- <span data-ttu-id="c6639-303">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6639-303">'Identity'</span></span>
- <span data-ttu-id="c6639-304">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6639-304">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6639-305">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6639-305">'Razor'</span></span>
- <span data-ttu-id="c6639-306">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c6639-306">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c6639-307">title: author: description: monikerRange: ms. author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="c6639-307">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6639-308">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6639-308">'Blazor'</span></span>
- <span data-ttu-id="c6639-309">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6639-309">'Identity'</span></span>
- <span data-ttu-id="c6639-310">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6639-310">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6639-311">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6639-311">'Razor'</span></span>
- <span data-ttu-id="c6639-312">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c6639-312">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c6639-313">title: author: description: monikerRange: ms. author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="c6639-313">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6639-314">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6639-314">'Blazor'</span></span>
- <span data-ttu-id="c6639-315">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6639-315">'Identity'</span></span>
- <span data-ttu-id="c6639-316">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6639-316">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6639-317">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6639-317">'Razor'</span></span>
- <span data-ttu-id="c6639-318">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c6639-318">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c6639-319">title: author: description: monikerRange: ms. author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="c6639-319">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6639-320">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6639-320">'Blazor'</span></span>
- <span data-ttu-id="c6639-321">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6639-321">'Identity'</span></span>
- <span data-ttu-id="c6639-322">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6639-322">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6639-323">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6639-323">'Razor'</span></span>
- <span data-ttu-id="c6639-324">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c6639-324">'SignalR' uid:</span></span> 

<span data-ttu-id="c6639-325">-------------------------------- | |Facebook |`https://www.facebook.com/dialog/oauth`                          |
|Google |`https://www.googleapis.com/auth/userinfo.profile`               |
|Microsoft |`https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
|Twitter |`https://api.twitter.com/oauth/authenticate`                     |</span><span class="sxs-lookup"><span data-stu-id="c6639-325">-------------------------------- | | Facebook  | `https://www.facebook.com/dialog/oauth`                          |
| Google    | `https://www.googleapis.com/auth/userinfo.profile`               |
| Microsoft | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| Twitter   | `https://api.twitter.com/oauth/authenticate`                     |</span></span>

<span data-ttu-id="c6639-326">サンプルアプリで `userinfo.profile` <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> は、がで呼び出されたときに、Google のスコープがフレームワークによって自動的に追加され <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder> ます。</span><span class="sxs-lookup"><span data-stu-id="c6639-326">In the sample app, Google's `userinfo.profile` scope is automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="c6639-327">アプリに追加のスコープが必要な場合は、それらをオプションに追加します。</span><span class="sxs-lookup"><span data-stu-id="c6639-327">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="c6639-328">次の例で `https://www.googleapis.com/auth/user.birthday.read` は、ユーザーの誕生日を取得するために Google スコープが追加されています。</span><span class="sxs-lookup"><span data-stu-id="c6639-328">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added in order to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="c6639-329">ユーザーデータキーをマップして要求を作成する</span><span class="sxs-lookup"><span data-stu-id="c6639-329">Map user data keys and create claims</span></span>

<span data-ttu-id="c6639-330">プロバイダーのオプションで、 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> サインイン時に読み取るアプリ id の外部プロバイダーの JSON ユーザーデータのキー/サブキーごとに、またはを指定します。</span><span class="sxs-lookup"><span data-stu-id="c6639-330">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="c6639-331">要求の種類の詳細については、「」を参照してください <xref:System.Security.Claims.ClaimTypes> 。</span><span class="sxs-lookup"><span data-stu-id="c6639-331">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="c6639-332">このサンプルアプリでは、 `urn:google:locale` `urn:google:picture` `locale` Google ユーザーデータのキーとキーから、ロケール () と画像 () の要求を作成し `picture` ます。</span><span class="sxs-lookup"><span data-stu-id="c6639-332">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="c6639-333">では、 `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync` <xref:Microsoft.AspNetCore.Identity.IdentityUser> ( `ApplicationUser` ) がを使用してアプリにサインイン <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*> します。</span><span class="sxs-lookup"><span data-stu-id="c6639-333">In `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, an <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="c6639-334">サインインプロセス中に、で <xref:Microsoft.AspNetCore.Identity.UserManager%601> `ApplicationUser` 使用可能なユーザーデータの要求を格納でき <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*> ます。</span><span class="sxs-lookup"><span data-stu-id="c6639-334">During the sign in process, the <xref:Microsoft.AspNetCore.Identity.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="c6639-335">サンプルアプリでは、 `OnPostConfirmationAsync` (*Account/externallogin. cshtml*) によって、 `urn:google:locale` `urn:google:picture` `ApplicationUser` 次の要求を含む、サインインしたのロケール () と画像 () の要求が確立 <xref:System.Security.Claims.ClaimTypes.GivenName> されます。</span><span class="sxs-lookup"><span data-stu-id="c6639-335">In the sample app, `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="c6639-336">既定では、ユーザーの要求は認証 cookie に格納されます。</span><span class="sxs-lookup"><span data-stu-id="c6639-336">By default, a user's claims are stored in the authentication cookie.</span></span> <span data-ttu-id="c6639-337">認証 cookie が大きすぎると、次の理由により、アプリのエラーが発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="c6639-337">If the authentication cookie is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="c6639-338">ブラウザーは cookie ヘッダーが長すぎることを検出します。</span><span class="sxs-lookup"><span data-stu-id="c6639-338">The browser detects that the cookie header is too long.</span></span>
* <span data-ttu-id="c6639-339">要求の全体的なサイズが大きすぎます。</span><span class="sxs-lookup"><span data-stu-id="c6639-339">The overall size of the request is too large.</span></span>

<span data-ttu-id="c6639-340">ユーザーの要求を処理するために大量のユーザーデータが必要な場合は、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="c6639-340">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="c6639-341">要求処理のユーザー要求の数とサイズは、アプリで必要なもののみに制限します。</span><span class="sxs-lookup"><span data-stu-id="c6639-341">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="c6639-342">Cookie 認証ミドルウェアのカスタムを使用し <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> て、 <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> 要求間で id を格納します。</span><span class="sxs-lookup"><span data-stu-id="c6639-342">Use a custom <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> for the Cookie Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="c6639-343">小さいセッション識別子キーをクライアントに送信するだけで、サーバー上の大量の id 情報を保持します。</span><span class="sxs-lookup"><span data-stu-id="c6639-343">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="c6639-344">アクセストークンを保存する</span><span class="sxs-lookup"><span data-stu-id="c6639-344">Save the access token</span></span>

<span data-ttu-id="c6639-345"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*>承認が成功した後に、アクセストークンと更新トークンをに格納するかどうかを定義し <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> ます。</span><span class="sxs-lookup"><span data-stu-id="c6639-345"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="c6639-346">`SaveTokens`は、 `false` 最終的な認証クッキーのサイズを小さくするために、既定でに設定されます。</span><span class="sxs-lookup"><span data-stu-id="c6639-346">`SaveTokens` is set to `false` by default to reduce the size of the final authentication cookie.</span></span>

<span data-ttu-id="c6639-347">サンプルアプリでは、の値をに設定し `SaveTokens` `true` <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> ます。</span><span class="sxs-lookup"><span data-stu-id="c6639-347">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="c6639-348">を実行するときに、 `OnPostConfirmationAsync` の外部プロバイダーからアクセストークン ([Externallogininfo. authenticationtokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) をに格納 `ApplicationUser` `AuthenticationProperties` します。</span><span class="sxs-lookup"><span data-stu-id="c6639-348">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="c6639-349">サンプルアプリでは、アクセストークン `OnPostConfirmationAsync` (新しいユーザー登録) と `OnGetCallbackAsync` (以前に登録されたユーザー) を*Account/externallogin. cshtml. cs*に保存します。</span><span class="sxs-lookup"><span data-stu-id="c6639-349">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs*:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="c6639-350">カスタムトークンを追加する方法</span><span class="sxs-lookup"><span data-stu-id="c6639-350">How to add additional custom tokens</span></span>

<span data-ttu-id="c6639-351">の一部として格納されているカスタムトークンを追加する方法を示すために、 `SaveTokens` サンプルアプリでは、の AuthenticationToken.Name の現在のを使用してを追加し <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> <xref:System.DateTime> [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) `TicketCreated` ます。</span><span class="sxs-lookup"><span data-stu-id="c6639-351">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="c6639-352">要求の作成と追加</span><span class="sxs-lookup"><span data-stu-id="c6639-352">Creating and adding claims</span></span>

<span data-ttu-id="c6639-353">フレームワークには、要求を作成してコレクションに追加するための一般的なアクションと拡張メソッドが用意されています。</span><span class="sxs-lookup"><span data-stu-id="c6639-353">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="c6639-354">詳細については、「 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> 」および「 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c6639-354">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="c6639-355">ユーザーは、から派生し、抽象メソッドを実装することによって、カスタムアクションを定義でき <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> ます。</span><span class="sxs-lookup"><span data-stu-id="c6639-355">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="c6639-356">詳細については、「<xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c6639-356">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="c6639-357">要求アクションと要求の削除</span><span class="sxs-lookup"><span data-stu-id="c6639-357">Removal of claim actions and claims</span></span>

<span data-ttu-id="c6639-358">[Claimactioncollection。 Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*)は、指定されたのすべての要求アクションを <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> コレクションから削除します。</span><span class="sxs-lookup"><span data-stu-id="c6639-358">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="c6639-359">[DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*)は、指定されたの要求を <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> id から削除します。</span><span class="sxs-lookup"><span data-stu-id="c6639-359">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="c6639-360"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*>は、主に[OpenID connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc)と共に使用して、プロトコルによって生成された要求を削除します。</span><span class="sxs-lookup"><span data-stu-id="c6639-360"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="c6639-361">サンプルアプリの出力</span><span class="sxs-lookup"><span data-stu-id="c6639-361">Sample app output</span></span>

```
User Claims

http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier
    9b342344f-7aab-43c2-1ac1-ba75912ca999
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name
    someone@gmail.com
AspNet.Identity.SecurityStamp
    7D4312MOWRYYBFI1KXRPHGOSTBVWSFDE
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname
    Judy
urn:google:locale
    en
urn:google:picture
    https://lh4.googleusercontent.com/-XXXXXX/XXXXXX/XXXXXX/XXXXXX/photo.jpg

Authentication Properties

.Token.access_token
    yc23.AlvoZqz56...1lxltXV7D-ZWP9
.Token.token_type
    Bearer
.Token.expires_at
    2019-04-11T22:14:51.0000000+00:00
.Token.TicketCreated
    4/11/2019 9:14:52 PM
.TokenNames
    access_token;token_type;expires_at;TicketCreated
.persistent
.issued
    Thu, 11 Apr 2019 20:51:06 GMT
.expires
    Thu, 25 Apr 2019 20:51:06 GMT

```

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c6639-362">ASP.NET Core アプリは、Facebook、Google、Microsoft、Twitter などの外部認証プロバイダーからの追加の要求とトークンを確立できます。</span><span class="sxs-lookup"><span data-stu-id="c6639-362">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="c6639-363">各プロバイダーは、プラットフォーム上のユーザーに関するさまざまな情報を明らかにしますが、ユーザーデータを受け取って追加の要求に変換するパターンは同じです。</span><span class="sxs-lookup"><span data-stu-id="c6639-363">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="c6639-364">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="c6639-364">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="c6639-365">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="c6639-365">Prerequisites</span></span>

<span data-ttu-id="c6639-366">アプリでサポートする外部認証プロバイダーを決定します。</span><span class="sxs-lookup"><span data-stu-id="c6639-366">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="c6639-367">各プロバイダーについて、アプリを登録し、クライアント ID とクライアントシークレットを取得します。</span><span class="sxs-lookup"><span data-stu-id="c6639-367">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="c6639-368">詳細については、「<xref:security/authentication/social/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c6639-368">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="c6639-369">このサンプルアプリでは、 [Google 認証プロバイダー](xref:security/authentication/google-logins)を使用します。</span><span class="sxs-lookup"><span data-stu-id="c6639-369">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="c6639-370">クライアント ID とクライアントシークレットを設定する</span><span class="sxs-lookup"><span data-stu-id="c6639-370">Set the client ID and client secret</span></span>

<span data-ttu-id="c6639-371">OAuth 認証プロバイダーは、クライアント ID とクライアントシークレットを使用して、アプリとの信頼関係を確立します。</span><span class="sxs-lookup"><span data-stu-id="c6639-371">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="c6639-372">アプリがプロバイダーに登録されるときに、外部認証プロバイダーによってアプリのクライアント ID とクライアントシークレットの値が作成されます。</span><span class="sxs-lookup"><span data-stu-id="c6639-372">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="c6639-373">アプリが使用する各外部プロバイダーは、プロバイダーのクライアント ID とクライアントシークレットとは別に構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c6639-373">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="c6639-374">詳細については、シナリオに当てはまる外部認証プロバイダーのトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="c6639-374">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="c6639-375">Facebook での認証</span><span class="sxs-lookup"><span data-stu-id="c6639-375">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="c6639-376">Google での認証</span><span class="sxs-lookup"><span data-stu-id="c6639-376">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="c6639-377">Microsoft での認証</span><span class="sxs-lookup"><span data-stu-id="c6639-377">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="c6639-378">Twitter での認証</span><span class="sxs-lookup"><span data-stu-id="c6639-378">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="c6639-379">他の認証プロバイダー</span><span class="sxs-lookup"><span data-stu-id="c6639-379">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="c6639-380">OpenIdConnect</span><span class="sxs-lookup"><span data-stu-id="c6639-380">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="c6639-381">このサンプルアプリでは、google によって提供されるクライアント ID とクライアントシークレットを使用して Google 認証プロバイダーを構成します。</span><span class="sxs-lookup"><span data-stu-id="c6639-381">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="c6639-382">認証スコープを確立する</span><span class="sxs-lookup"><span data-stu-id="c6639-382">Establish the authentication scope</span></span>

<span data-ttu-id="c6639-383">を指定して、プロバイダーから取得するアクセス許可の一覧を指定し <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*> ます。</span><span class="sxs-lookup"><span data-stu-id="c6639-383">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="c6639-384">共通外部プロバイダーの認証スコープは、次の表に表示されます。</span><span class="sxs-lookup"><span data-stu-id="c6639-384">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="c6639-385">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="c6639-385">Provider</span></span>  | <span data-ttu-id="c6639-386">Scope</span><span class="sxs-lookup"><span data-stu-id="c6639-386">Scope</span></span>                                                            |
| ---
<span data-ttu-id="c6639-387">title: author: description: monikerRange: ms. author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="c6639-387">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6639-388">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6639-388">'Blazor'</span></span>
- <span data-ttu-id="c6639-389">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6639-389">'Identity'</span></span>
- <span data-ttu-id="c6639-390">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6639-390">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6639-391">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6639-391">'Razor'</span></span>
- <span data-ttu-id="c6639-392">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c6639-392">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c6639-393">title: author: description: monikerRange: ms. author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="c6639-393">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6639-394">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6639-394">'Blazor'</span></span>
- <span data-ttu-id="c6639-395">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6639-395">'Identity'</span></span>
- <span data-ttu-id="c6639-396">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6639-396">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6639-397">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6639-397">'Razor'</span></span>
- <span data-ttu-id="c6639-398">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c6639-398">'SignalR' uid:</span></span> 

<span data-ttu-id="c6639-399">----- |---title: author: description: monikerRange: ms. author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="c6639-399">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6639-400">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6639-400">'Blazor'</span></span>
- <span data-ttu-id="c6639-401">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6639-401">'Identity'</span></span>
- <span data-ttu-id="c6639-402">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6639-402">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6639-403">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6639-403">'Razor'</span></span>
- <span data-ttu-id="c6639-404">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c6639-404">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c6639-405">title: author: description: monikerRange: ms. author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="c6639-405">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6639-406">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6639-406">'Blazor'</span></span>
- <span data-ttu-id="c6639-407">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6639-407">'Identity'</span></span>
- <span data-ttu-id="c6639-408">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6639-408">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6639-409">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6639-409">'Razor'</span></span>
- <span data-ttu-id="c6639-410">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c6639-410">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c6639-411">title: author: description: monikerRange: ms. author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="c6639-411">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6639-412">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6639-412">'Blazor'</span></span>
- <span data-ttu-id="c6639-413">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6639-413">'Identity'</span></span>
- <span data-ttu-id="c6639-414">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6639-414">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6639-415">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6639-415">'Razor'</span></span>
- <span data-ttu-id="c6639-416">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c6639-416">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c6639-417">title: author: description: monikerRange: ms. author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="c6639-417">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6639-418">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6639-418">'Blazor'</span></span>
- <span data-ttu-id="c6639-419">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6639-419">'Identity'</span></span>
- <span data-ttu-id="c6639-420">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6639-420">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6639-421">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6639-421">'Razor'</span></span>
- <span data-ttu-id="c6639-422">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c6639-422">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c6639-423">title: author: description: monikerRange: ms. author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="c6639-423">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6639-424">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6639-424">'Blazor'</span></span>
- <span data-ttu-id="c6639-425">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6639-425">'Identity'</span></span>
- <span data-ttu-id="c6639-426">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6639-426">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6639-427">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6639-427">'Razor'</span></span>
- <span data-ttu-id="c6639-428">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c6639-428">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c6639-429">title: author: description: monikerRange: ms. author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="c6639-429">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6639-430">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6639-430">'Blazor'</span></span>
- <span data-ttu-id="c6639-431">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6639-431">'Identity'</span></span>
- <span data-ttu-id="c6639-432">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6639-432">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6639-433">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6639-433">'Razor'</span></span>
- <span data-ttu-id="c6639-434">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c6639-434">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c6639-435">title: author: description: monikerRange: ms. author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="c6639-435">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6639-436">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6639-436">'Blazor'</span></span>
- <span data-ttu-id="c6639-437">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6639-437">'Identity'</span></span>
- <span data-ttu-id="c6639-438">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6639-438">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6639-439">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6639-439">'Razor'</span></span>
- <span data-ttu-id="c6639-440">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c6639-440">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c6639-441">title: author: description: monikerRange: ms. author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="c6639-441">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6639-442">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6639-442">'Blazor'</span></span>
- <span data-ttu-id="c6639-443">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6639-443">'Identity'</span></span>
- <span data-ttu-id="c6639-444">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6639-444">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6639-445">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6639-445">'Razor'</span></span>
- <span data-ttu-id="c6639-446">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c6639-446">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c6639-447">title: author: description: monikerRange: ms. author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="c6639-447">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6639-448">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6639-448">'Blazor'</span></span>
- <span data-ttu-id="c6639-449">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6639-449">'Identity'</span></span>
- <span data-ttu-id="c6639-450">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6639-450">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6639-451">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6639-451">'Razor'</span></span>
- <span data-ttu-id="c6639-452">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c6639-452">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c6639-453">title: author: description: monikerRange: ms. author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="c6639-453">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6639-454">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6639-454">'Blazor'</span></span>
- <span data-ttu-id="c6639-455">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6639-455">'Identity'</span></span>
- <span data-ttu-id="c6639-456">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6639-456">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6639-457">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6639-457">'Razor'</span></span>
- <span data-ttu-id="c6639-458">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c6639-458">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c6639-459">title: author: description: monikerRange: ms. author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="c6639-459">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6639-460">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6639-460">'Blazor'</span></span>
- <span data-ttu-id="c6639-461">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6639-461">'Identity'</span></span>
- <span data-ttu-id="c6639-462">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6639-462">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6639-463">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6639-463">'Razor'</span></span>
- <span data-ttu-id="c6639-464">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c6639-464">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c6639-465">title: author: description: monikerRange: ms. author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="c6639-465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6639-466">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6639-466">'Blazor'</span></span>
- <span data-ttu-id="c6639-467">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6639-467">'Identity'</span></span>
- <span data-ttu-id="c6639-468">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6639-468">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6639-469">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6639-469">'Razor'</span></span>
- <span data-ttu-id="c6639-470">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c6639-470">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c6639-471">title: author: description: monikerRange: ms. author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="c6639-471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6639-472">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6639-472">'Blazor'</span></span>
- <span data-ttu-id="c6639-473">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6639-473">'Identity'</span></span>
- <span data-ttu-id="c6639-474">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6639-474">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6639-475">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6639-475">'Razor'</span></span>
- <span data-ttu-id="c6639-476">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c6639-476">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c6639-477">title: author: description: monikerRange: ms. author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="c6639-477">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6639-478">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6639-478">'Blazor'</span></span>
- <span data-ttu-id="c6639-479">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6639-479">'Identity'</span></span>
- <span data-ttu-id="c6639-480">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6639-480">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6639-481">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6639-481">'Razor'</span></span>
- <span data-ttu-id="c6639-482">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c6639-482">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c6639-483">title: author: description: monikerRange: ms. author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="c6639-483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6639-484">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6639-484">'Blazor'</span></span>
- <span data-ttu-id="c6639-485">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6639-485">'Identity'</span></span>
- <span data-ttu-id="c6639-486">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6639-486">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6639-487">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6639-487">'Razor'</span></span>
- <span data-ttu-id="c6639-488">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c6639-488">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c6639-489">title: author: description: monikerRange: ms. author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="c6639-489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6639-490">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6639-490">'Blazor'</span></span>
- <span data-ttu-id="c6639-491">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6639-491">'Identity'</span></span>
- <span data-ttu-id="c6639-492">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6639-492">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6639-493">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6639-493">'Razor'</span></span>
- <span data-ttu-id="c6639-494">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c6639-494">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c6639-495">title: author: description: monikerRange: ms. author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="c6639-495">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6639-496">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6639-496">'Blazor'</span></span>
- <span data-ttu-id="c6639-497">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6639-497">'Identity'</span></span>
- <span data-ttu-id="c6639-498">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6639-498">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6639-499">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6639-499">'Razor'</span></span>
- <span data-ttu-id="c6639-500">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c6639-500">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c6639-501">title: author: description: monikerRange: ms. author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="c6639-501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6639-502">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6639-502">'Blazor'</span></span>
- <span data-ttu-id="c6639-503">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6639-503">'Identity'</span></span>
- <span data-ttu-id="c6639-504">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6639-504">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6639-505">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6639-505">'Razor'</span></span>
- <span data-ttu-id="c6639-506">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c6639-506">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c6639-507">title: author: description: monikerRange: ms. author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="c6639-507">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6639-508">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6639-508">'Blazor'</span></span>
- <span data-ttu-id="c6639-509">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6639-509">'Identity'</span></span>
- <span data-ttu-id="c6639-510">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6639-510">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6639-511">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6639-511">'Razor'</span></span>
- <span data-ttu-id="c6639-512">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c6639-512">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c6639-513">title: author: description: monikerRange: ms. author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="c6639-513">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6639-514">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6639-514">'Blazor'</span></span>
- <span data-ttu-id="c6639-515">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6639-515">'Identity'</span></span>
- <span data-ttu-id="c6639-516">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6639-516">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6639-517">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6639-517">'Razor'</span></span>
- <span data-ttu-id="c6639-518">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c6639-518">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c6639-519">title: author: description: monikerRange: ms. author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="c6639-519">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6639-520">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6639-520">'Blazor'</span></span>
- <span data-ttu-id="c6639-521">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6639-521">'Identity'</span></span>
- <span data-ttu-id="c6639-522">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6639-522">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6639-523">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6639-523">'Razor'</span></span>
- <span data-ttu-id="c6639-524">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c6639-524">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c6639-525">title: author: description: monikerRange: ms. author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="c6639-525">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6639-526">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6639-526">'Blazor'</span></span>
- <span data-ttu-id="c6639-527">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6639-527">'Identity'</span></span>
- <span data-ttu-id="c6639-528">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6639-528">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6639-529">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6639-529">'Razor'</span></span>
- <span data-ttu-id="c6639-530">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c6639-530">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c6639-531">title: author: description: monikerRange: ms. author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="c6639-531">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6639-532">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6639-532">'Blazor'</span></span>
- <span data-ttu-id="c6639-533">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6639-533">'Identity'</span></span>
- <span data-ttu-id="c6639-534">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6639-534">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6639-535">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6639-535">'Razor'</span></span>
- <span data-ttu-id="c6639-536">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c6639-536">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c6639-537">title: author: description: monikerRange: ms. author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="c6639-537">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6639-538">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6639-538">'Blazor'</span></span>
- <span data-ttu-id="c6639-539">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6639-539">'Identity'</span></span>
- <span data-ttu-id="c6639-540">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6639-540">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6639-541">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6639-541">'Razor'</span></span>
- <span data-ttu-id="c6639-542">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c6639-542">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c6639-543">title: author: description: monikerRange: ms. author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="c6639-543">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6639-544">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6639-544">'Blazor'</span></span>
- <span data-ttu-id="c6639-545">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6639-545">'Identity'</span></span>
- <span data-ttu-id="c6639-546">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6639-546">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6639-547">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6639-547">'Razor'</span></span>
- <span data-ttu-id="c6639-548">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c6639-548">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c6639-549">title: author: description: monikerRange: ms. author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="c6639-549">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6639-550">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6639-550">'Blazor'</span></span>
- <span data-ttu-id="c6639-551">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6639-551">'Identity'</span></span>
- <span data-ttu-id="c6639-552">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6639-552">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6639-553">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6639-553">'Razor'</span></span>
- <span data-ttu-id="c6639-554">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c6639-554">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c6639-555">title: author: description: monikerRange: ms. author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="c6639-555">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6639-556">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6639-556">'Blazor'</span></span>
- <span data-ttu-id="c6639-557">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6639-557">'Identity'</span></span>
- <span data-ttu-id="c6639-558">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6639-558">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6639-559">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6639-559">'Razor'</span></span>
- <span data-ttu-id="c6639-560">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c6639-560">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c6639-561">title: author: description: monikerRange: ms. author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="c6639-561">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6639-562">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6639-562">'Blazor'</span></span>
- <span data-ttu-id="c6639-563">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6639-563">'Identity'</span></span>
- <span data-ttu-id="c6639-564">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6639-564">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6639-565">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6639-565">'Razor'</span></span>
- <span data-ttu-id="c6639-566">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c6639-566">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c6639-567">title: author: description: monikerRange: ms. author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="c6639-567">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6639-568">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6639-568">'Blazor'</span></span>
- <span data-ttu-id="c6639-569">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6639-569">'Identity'</span></span>
- <span data-ttu-id="c6639-570">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6639-570">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6639-571">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6639-571">'Razor'</span></span>
- <span data-ttu-id="c6639-572">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c6639-572">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c6639-573">title: author: description: monikerRange: ms. author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="c6639-573">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c6639-574">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c6639-574">'Blazor'</span></span>
- <span data-ttu-id="c6639-575">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c6639-575">'Identity'</span></span>
- <span data-ttu-id="c6639-576">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c6639-576">'Let's Encrypt'</span></span>
- <span data-ttu-id="c6639-577">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c6639-577">'Razor'</span></span>
- <span data-ttu-id="c6639-578">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c6639-578">'SignalR' uid:</span></span> 

<span data-ttu-id="c6639-579">-------------------------------- | |Facebook |`https://www.facebook.com/dialog/oauth`                          |
|Google |`https://www.googleapis.com/auth/userinfo.profile`               |
|Microsoft |`https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
|Twitter |`https://api.twitter.com/oauth/authenticate`                     |</span><span class="sxs-lookup"><span data-stu-id="c6639-579">-------------------------------- | | Facebook  | `https://www.facebook.com/dialog/oauth`                          |
| Google    | `https://www.googleapis.com/auth/userinfo.profile`               |
| Microsoft | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| Twitter   | `https://api.twitter.com/oauth/authenticate`                     |</span></span>

<span data-ttu-id="c6639-580">サンプルアプリで `userinfo.profile` <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> は、がで呼び出されたときに、Google のスコープがフレームワークによって自動的に追加され <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder> ます。</span><span class="sxs-lookup"><span data-stu-id="c6639-580">In the sample app, Google's `userinfo.profile` scope is automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="c6639-581">アプリに追加のスコープが必要な場合は、それらをオプションに追加します。</span><span class="sxs-lookup"><span data-stu-id="c6639-581">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="c6639-582">次の例で `https://www.googleapis.com/auth/user.birthday.read` は、ユーザーの誕生日を取得するために Google スコープが追加されています。</span><span class="sxs-lookup"><span data-stu-id="c6639-582">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added in order to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="c6639-583">ユーザーデータキーをマップして要求を作成する</span><span class="sxs-lookup"><span data-stu-id="c6639-583">Map user data keys and create claims</span></span>

<span data-ttu-id="c6639-584">プロバイダーのオプションで、 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> サインイン時に読み取るアプリ id の外部プロバイダーの JSON ユーザーデータのキー/サブキーごとに、またはを指定します。</span><span class="sxs-lookup"><span data-stu-id="c6639-584">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="c6639-585">要求の種類の詳細については、「」を参照してください <xref:System.Security.Claims.ClaimTypes> 。</span><span class="sxs-lookup"><span data-stu-id="c6639-585">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="c6639-586">このサンプルアプリでは、 `urn:google:locale` `urn:google:picture` `locale` Google ユーザーデータのキーとキーから、ロケール () と画像 () の要求を作成し `picture` ます。</span><span class="sxs-lookup"><span data-stu-id="c6639-586">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="c6639-587">では、 `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync` <xref:Microsoft.AspNetCore.Identity.IdentityUser> ( `ApplicationUser` ) がを使用してアプリにサインイン <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*> します。</span><span class="sxs-lookup"><span data-stu-id="c6639-587">In `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, an <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="c6639-588">サインインプロセス中に、で <xref:Microsoft.AspNetCore.Identity.UserManager%601> `ApplicationUser` 使用可能なユーザーデータの要求を格納でき <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*> ます。</span><span class="sxs-lookup"><span data-stu-id="c6639-588">During the sign in process, the <xref:Microsoft.AspNetCore.Identity.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="c6639-589">サンプルアプリでは、 `OnPostConfirmationAsync` (*Account/externallogin. cshtml*) によって、 `urn:google:locale` `urn:google:picture` `ApplicationUser` 次の要求を含む、サインインしたのロケール () と画像 () の要求が確立 <xref:System.Security.Claims.ClaimTypes.GivenName> されます。</span><span class="sxs-lookup"><span data-stu-id="c6639-589">In the sample app, `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="c6639-590">既定では、ユーザーの要求は認証 cookie に格納されます。</span><span class="sxs-lookup"><span data-stu-id="c6639-590">By default, a user's claims are stored in the authentication cookie.</span></span> <span data-ttu-id="c6639-591">認証 cookie が大きすぎると、次の理由により、アプリのエラーが発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="c6639-591">If the authentication cookie is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="c6639-592">ブラウザーは cookie ヘッダーが長すぎることを検出します。</span><span class="sxs-lookup"><span data-stu-id="c6639-592">The browser detects that the cookie header is too long.</span></span>
* <span data-ttu-id="c6639-593">要求の全体的なサイズが大きすぎます。</span><span class="sxs-lookup"><span data-stu-id="c6639-593">The overall size of the request is too large.</span></span>

<span data-ttu-id="c6639-594">ユーザーの要求を処理するために大量のユーザーデータが必要な場合は、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="c6639-594">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="c6639-595">要求処理のユーザー要求の数とサイズは、アプリで必要なもののみに制限します。</span><span class="sxs-lookup"><span data-stu-id="c6639-595">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="c6639-596">Cookie 認証ミドルウェアのカスタムを使用し <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> て、 <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> 要求間で id を格納します。</span><span class="sxs-lookup"><span data-stu-id="c6639-596">Use a custom <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> for the Cookie Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="c6639-597">小さいセッション識別子キーをクライアントに送信するだけで、サーバー上の大量の id 情報を保持します。</span><span class="sxs-lookup"><span data-stu-id="c6639-597">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="c6639-598">アクセストークンを保存する</span><span class="sxs-lookup"><span data-stu-id="c6639-598">Save the access token</span></span>

<span data-ttu-id="c6639-599"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*>承認が成功した後に、アクセストークンと更新トークンをに格納するかどうかを定義し <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> ます。</span><span class="sxs-lookup"><span data-stu-id="c6639-599"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="c6639-600">`SaveTokens`は、 `false` 最終的な認証クッキーのサイズを小さくするために、既定でに設定されます。</span><span class="sxs-lookup"><span data-stu-id="c6639-600">`SaveTokens` is set to `false` by default to reduce the size of the final authentication cookie.</span></span>

<span data-ttu-id="c6639-601">サンプルアプリでは、の値をに設定し `SaveTokens` `true` <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> ます。</span><span class="sxs-lookup"><span data-stu-id="c6639-601">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="c6639-602">を実行するときに、 `OnPostConfirmationAsync` の外部プロバイダーからアクセストークン ([Externallogininfo. authenticationtokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) をに格納 `ApplicationUser` `AuthenticationProperties` します。</span><span class="sxs-lookup"><span data-stu-id="c6639-602">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="c6639-603">サンプルアプリでは、アクセストークン `OnPostConfirmationAsync` (新しいユーザー登録) と `OnGetCallbackAsync` (以前に登録されたユーザー) を*Account/externallogin. cshtml. cs*に保存します。</span><span class="sxs-lookup"><span data-stu-id="c6639-603">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs*:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="c6639-604">カスタムトークンを追加する方法</span><span class="sxs-lookup"><span data-stu-id="c6639-604">How to add additional custom tokens</span></span>

<span data-ttu-id="c6639-605">の一部として格納されているカスタムトークンを追加する方法を示すために、 `SaveTokens` サンプルアプリでは、の AuthenticationToken.Name の現在のを使用してを追加し <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> <xref:System.DateTime> [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) `TicketCreated` ます。</span><span class="sxs-lookup"><span data-stu-id="c6639-605">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="c6639-606">要求の作成と追加</span><span class="sxs-lookup"><span data-stu-id="c6639-606">Creating and adding claims</span></span>

<span data-ttu-id="c6639-607">フレームワークには、要求を作成してコレクションに追加するための一般的なアクションと拡張メソッドが用意されています。</span><span class="sxs-lookup"><span data-stu-id="c6639-607">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="c6639-608">詳細については、「 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> 」および「 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c6639-608">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="c6639-609">ユーザーは、から派生し、抽象メソッドを実装することによって、カスタムアクションを定義でき <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> ます。</span><span class="sxs-lookup"><span data-stu-id="c6639-609">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="c6639-610">詳細については、「<xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c6639-610">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="c6639-611">要求アクションと要求の削除</span><span class="sxs-lookup"><span data-stu-id="c6639-611">Removal of claim actions and claims</span></span>

<span data-ttu-id="c6639-612">[Claimactioncollection。 Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*)は、指定されたのすべての要求アクションを <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> コレクションから削除します。</span><span class="sxs-lookup"><span data-stu-id="c6639-612">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="c6639-613">[DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*)は、指定されたの要求を <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> id から削除します。</span><span class="sxs-lookup"><span data-stu-id="c6639-613">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="c6639-614"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*>は、主に[OpenID connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc)と共に使用して、プロトコルによって生成された要求を削除します。</span><span class="sxs-lookup"><span data-stu-id="c6639-614"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="c6639-615">サンプルアプリの出力</span><span class="sxs-lookup"><span data-stu-id="c6639-615">Sample app output</span></span>

```
User Claims

http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier
    9b342344f-7aab-43c2-1ac1-ba75912ca999
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name
    someone@gmail.com
AspNet.Identity.SecurityStamp
    7D4312MOWRYYBFI1KXRPHGOSTBVWSFDE
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname
    Judy
urn:google:locale
    en
urn:google:picture
    https://lh4.googleusercontent.com/-XXXXXX/XXXXXX/XXXXXX/XXXXXX/photo.jpg

Authentication Properties

.Token.access_token
    yc23.AlvoZqz56...1lxltXV7D-ZWP9
.Token.token_type
    Bearer
.Token.expires_at
    2019-04-11T22:14:51.0000000+00:00
.Token.TicketCreated
    4/11/2019 9:14:52 PM
.TokenNames
    access_token;token_type;expires_at;TicketCreated
.persistent
.issued
    Thu, 11 Apr 2019 20:51:06 GMT
.expires
    Thu, 25 Apr 2019 20:51:06 GMT

```

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="c6639-616">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="c6639-616">Additional resources</span></span>

* <span data-ttu-id="c6639-617">[dotnet/AspNetCore engineering の社会 Alsample アプリ](https://github.com/dotnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample): リンクされたサンプルアプリは、 [Dotnet/AspNetCore GitHub リポジトリの](https://github.com/dotnet/AspNetCore) `master` エンジニアリングブランチにあります。</span><span class="sxs-lookup"><span data-stu-id="c6639-617">[dotnet/AspNetCore engineering SocialSample app](https://github.com/dotnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample): The linked sample app is on the [dotnet/AspNetCore GitHub repo's](https://github.com/dotnet/AspNetCore) `master` engineering branch.</span></span> <span data-ttu-id="c6639-618">ブランチには、 `master` ASP.NET Core の次のリリースでアクティブな開発のコードが含まれています。</span><span class="sxs-lookup"><span data-stu-id="c6639-618">The `master` branch contains code under active development for the next release of ASP.NET Core.</span></span> <span data-ttu-id="c6639-619">リリースされたバージョンの ASP.NET Core 用のサンプルアプリのバージョンを表示するには、[**ブランチ**] ドロップダウンリストを使用してリリースブランチを選択します (たとえば、 `release/{X.Y}` )。</span><span class="sxs-lookup"><span data-stu-id="c6639-619">To see a version of the sample app for a released version of ASP.NET Core, use the **Branch** drop down list to select a release branch (for example `release/{X.Y}`).</span></span>
