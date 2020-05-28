---
<span data-ttu-id="5aa3c-101">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa3c-101">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa3c-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5aa3c-102">'Blazor'</span></span>
- <span data-ttu-id="5aa3c-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5aa3c-103">'Identity'</span></span>
- <span data-ttu-id="5aa3c-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5aa3c-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa3c-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5aa3c-105">'Razor'</span></span>
- <span data-ttu-id="5aa3c-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa3c-106">'SignalR' uid:</span></span> 

---
# <a name="configure-certificate-authentication-in-aspnet-core"></a><span data-ttu-id="5aa3c-107">ASP.NET Core で証明書認証を構成する</span><span class="sxs-lookup"><span data-stu-id="5aa3c-107">Configure certificate authentication in ASP.NET Core</span></span>

<span data-ttu-id="5aa3c-108">`Microsoft.AspNetCore.Authentication.Certificate`ASP.NET Core の[証明書認証](https://tools.ietf.org/html/rfc5246#section-7.4.4)に似た実装が含まれています。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-108">`Microsoft.AspNetCore.Authentication.Certificate` contains an implementation similar to [Certificate Authentication](https://tools.ietf.org/html/rfc5246#section-7.4.4) for ASP.NET Core.</span></span> <span data-ttu-id="5aa3c-109">証明書の認証は、TLS レベルで実行されますが、その前に ASP.NET Core になります。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-109">Certificate authentication happens at the TLS level, long before it ever gets to ASP.NET Core.</span></span> <span data-ttu-id="5aa3c-110">より正確には、これは証明書を検証し、その証明書をに解決できるイベントを提供する認証ハンドラーです `ClaimsPrincipal` 。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-110">More accurately, this is an authentication handler that validates the certificate and then gives you an event where you can resolve that certificate to a `ClaimsPrincipal`.</span></span> 

<span data-ttu-id="5aa3c-111">証明書認証用に[ホストを構成](#configure-your-host-to-require-certificates)します。これは、IIS、Kestrel、Azure Web Apps など、使用している他のすべてのものになります。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-111">[Configure your host](#configure-your-host-to-require-certificates) for certificate authentication, be it IIS, Kestrel, Azure Web Apps, or whatever else you're using.</span></span>

## <a name="proxy-and-load-balancer-scenarios"></a><span data-ttu-id="5aa3c-112">プロキシとロードバランサーのシナリオ</span><span class="sxs-lookup"><span data-stu-id="5aa3c-112">Proxy and load balancer scenarios</span></span>

<span data-ttu-id="5aa3c-113">証明書認証は、主に、プロキシまたはロードバランサーがクライアントとサーバー間のトラフィックを処理しない場合に使用されるステートフルシナリオです。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-113">Certificate authentication is a stateful scenario primarily used where a proxy or load balancer doesn't handle traffic between clients and servers.</span></span> <span data-ttu-id="5aa3c-114">プロキシまたはロードバランサーが使用されている場合、証明書の認証はプロキシまたはロードバランサーの場合にのみ機能します。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-114">If a proxy or load balancer is used, certificate authentication only works if the proxy or load balancer:</span></span>

* <span data-ttu-id="5aa3c-115">認証を処理します。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-115">Handles the authentication.</span></span>
* <span data-ttu-id="5aa3c-116">認証情報に対して動作するユーザー認証情報をアプリに渡します (要求ヘッダーなど)。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-116">Passes the user authentication information to the app (for example, in a request header), which acts on the authentication information.</span></span>

<span data-ttu-id="5aa3c-117">プロキシとロードバランサーを使用する環境での証明書認証の代わりに、OpenID Connect (OIDC) を使用したフェデレーションサービス (ADFS) Active Directory ます。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-117">An alternative to certificate authentication in environments where proxies and load balancers are used is Active Directory Federated Services (ADFS) with OpenID Connect (OIDC).</span></span>

## <a name="get-started"></a><span data-ttu-id="5aa3c-118">作業開始</span><span class="sxs-lookup"><span data-stu-id="5aa3c-118">Get started</span></span>

<span data-ttu-id="5aa3c-119">HTTPS 証明書を取得して適用し、証明書を要求するように[ホストを構成](#configure-your-host-to-require-certificates)します。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-119">Acquire an HTTPS certificate, apply it, and [configure your host](#configure-your-host-to-require-certificates) to require certificates.</span></span>

<span data-ttu-id="5aa3c-120">Web アプリで、パッケージへの参照を追加し `Microsoft.AspNetCore.Authentication.Certificate` ます。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-120">In your web app, add a reference to the `Microsoft.AspNetCore.Authentication.Certificate` package.</span></span> <span data-ttu-id="5aa3c-121">次に、 `Startup.ConfigureServices` メソッドで、オプションを指定してを呼び出し `services.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).AddCertificate(...);` 、 `OnCertificateValidated` 要求と共に送信されるクライアント証明書に対して補助的な検証を実行するためのデリゲートを提供します。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-121">Then in the `Startup.ConfigureServices` method, call `services.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).AddCertificate(...);` with your options, providing a delegate for `OnCertificateValidated` to do any supplementary validation on the client certificate sent with requests.</span></span> <span data-ttu-id="5aa3c-122">その情報をに変換 `ClaimsPrincipal` し、プロパティに設定し `context.Principal` ます。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-122">Turn that information into a `ClaimsPrincipal` and set it on the `context.Principal` property.</span></span>

<span data-ttu-id="5aa3c-123">認証が失敗した場合、このハンドラーは、 `403 (Forbidden)` 予期したとおりに応答を返し `401 (Unauthorized)` ます。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-123">If authentication fails, this handler returns a `403 (Forbidden)` response rather a `401 (Unauthorized)`, as you might expect.</span></span> <span data-ttu-id="5aa3c-124">これは、最初の TLS 接続中に認証が行われるということです。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-124">The reasoning is that the authentication should happen during the initial TLS connection.</span></span> <span data-ttu-id="5aa3c-125">ハンドラーに到達するまでには遅すぎます。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-125">By the time it reaches the handler, it's too late.</span></span> <span data-ttu-id="5aa3c-126">匿名接続から証明書を使用して接続をアップグレードする方法はありません。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-126">There's no way to upgrade the connection from an anonymous connection to one with a certificate.</span></span>

<span data-ttu-id="5aa3c-127">また `app.UseAuthentication();` 、メソッドにを追加 `Startup.Configure` します。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-127">Also add `app.UseAuthentication();` in the `Startup.Configure` method.</span></span> <span data-ttu-id="5aa3c-128">それ以外の場合、は `HttpContext.User` `ClaimsPrincipal` 証明書から作成されるように設定されません。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-128">Otherwise, the `HttpContext.User` will not be set to `ClaimsPrincipal` created from the certificate.</span></span> <span data-ttu-id="5aa3c-129">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-129">For example:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
        .AddCertificate();
    // All the other service configuration.
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();

    // All the other app configuration.
}
```

<span data-ttu-id="5aa3c-130">前の例では、証明書認証を追加する既定の方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-130">The preceding example demonstrates the default way to add certificate authentication.</span></span> <span data-ttu-id="5aa3c-131">ハンドラーは、共通の証明書のプロパティを使用してユーザープリンシパルを構築します。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-131">The handler constructs a user principal using the common certificate properties.</span></span>

## <a name="configure-certificate-validation"></a><span data-ttu-id="5aa3c-132">証明書の検証の構成</span><span class="sxs-lookup"><span data-stu-id="5aa3c-132">Configure certificate validation</span></span>

<span data-ttu-id="5aa3c-133">ハンドラーには、 `CertificateAuthenticationOptions` 証明書に対して実行する必要のある検証の最小値である組み込みの検証がいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-133">The `CertificateAuthenticationOptions` handler has some built-in validations that are the minimum validations you should perform on a certificate.</span></span> <span data-ttu-id="5aa3c-134">これらの各設定は既定で有効になっています。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-134">Each of these settings is enabled by default.</span></span>

### <a name="allowedcertificatetypes--chained-selfsigned-or-all-chained--selfsigned"></a><span data-ttu-id="5aa3c-135">AllowedCertificateTypes = チェーン、自己署名、またはすべて (チェーン |自己署名済み)</span><span class="sxs-lookup"><span data-stu-id="5aa3c-135">AllowedCertificateTypes = Chained, SelfSigned, or All (Chained | SelfSigned)</span></span>

<span data-ttu-id="5aa3c-136">既定値:`CertificateTypes.Chained`</span><span class="sxs-lookup"><span data-stu-id="5aa3c-136">Default value: `CertificateTypes.Chained`</span></span>

<span data-ttu-id="5aa3c-137">このチェックでは、適切な証明書の種類のみが許可されていることが検証されます。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-137">This check validates that only the appropriate certificate type is allowed.</span></span> <span data-ttu-id="5aa3c-138">アプリで自己署名証明書を使用している場合は、このオプションをまたはに設定する必要があり `CertificateTypes.All` `CertificateTypes.SelfSigned` ます。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-138">If the app is using self-signed certificates, this option needs to be set to `CertificateTypes.All` or `CertificateTypes.SelfSigned`.</span></span>

### <a name="validatecertificateuse"></a><span data-ttu-id="5aa3c-139">ValidateCertificateUse</span><span class="sxs-lookup"><span data-stu-id="5aa3c-139">ValidateCertificateUse</span></span>

<span data-ttu-id="5aa3c-140">既定値:`true`</span><span class="sxs-lookup"><span data-stu-id="5aa3c-140">Default value: `true`</span></span>

<span data-ttu-id="5aa3c-141">このチェックでは、クライアントが提示した証明書にクライアント認証の拡張キー使用法 (EKU) があること、またはまったく Eku がないことを検証します。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-141">This check validates that the certificate presented by the client has the Client Authentication extended key use (EKU), or no EKUs at all.</span></span> <span data-ttu-id="5aa3c-142">仕様として、EKU が指定されていない場合は、すべての Eku が有効と見なされます。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-142">As the specifications say, if no EKU is specified, then all EKUs are deemed valid.</span></span>

### <a name="validatevalidityperiod"></a><span data-ttu-id="5aa3c-143">ValidateValidityPeriod</span><span class="sxs-lookup"><span data-stu-id="5aa3c-143">ValidateValidityPeriod</span></span>

<span data-ttu-id="5aa3c-144">既定値:`true`</span><span class="sxs-lookup"><span data-stu-id="5aa3c-144">Default value: `true`</span></span>

<span data-ttu-id="5aa3c-145">このチェックでは、証明書が有効期間内であることを検証します。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-145">This check validates that the certificate is within its validity period.</span></span> <span data-ttu-id="5aa3c-146">要求が発生するたびに、ハンドラーは、提示されたときに有効だった証明書が現在のセッション中に期限切れにならないようにします。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-146">On each request, the handler ensures that a certificate that was valid when it was presented hasn't expired during its current session.</span></span>

### <a name="revocationflag"></a><span data-ttu-id="5aa3c-147">RevocationFlag</span><span class="sxs-lookup"><span data-stu-id="5aa3c-147">RevocationFlag</span></span>

<span data-ttu-id="5aa3c-148">既定値:`X509RevocationFlag.ExcludeRoot`</span><span class="sxs-lookup"><span data-stu-id="5aa3c-148">Default value: `X509RevocationFlag.ExcludeRoot`</span></span>

<span data-ttu-id="5aa3c-149">チェーン内のどの証明書の失効を確認するかを指定するフラグ。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-149">A flag that specifies which certificates in the chain are checked for revocation.</span></span>

<span data-ttu-id="5aa3c-150">失効確認は、証明書がルート証明書にチェーンされている場合にのみ実行されます。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-150">Revocation checks are only performed when the certificate is chained to a root certificate.</span></span>

### <a name="revocationmode"></a><span data-ttu-id="5aa3c-151">RevocationMode</span><span class="sxs-lookup"><span data-stu-id="5aa3c-151">RevocationMode</span></span>

<span data-ttu-id="5aa3c-152">既定値:`X509RevocationMode.Online`</span><span class="sxs-lookup"><span data-stu-id="5aa3c-152">Default value: `X509RevocationMode.Online`</span></span>

<span data-ttu-id="5aa3c-153">失効確認の実行方法を指定するフラグ。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-153">A flag that specifies how revocation checks are performed.</span></span>

<span data-ttu-id="5aa3c-154">オンラインチェックを指定すると、証明機関への接続中に長い遅延が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-154">Specifying an online check can result in a long delay while the certificate authority is contacted.</span></span>

<span data-ttu-id="5aa3c-155">失効確認は、証明書がルート証明書にチェーンされている場合にのみ実行されます。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-155">Revocation checks are only performed when the certificate is chained to a root certificate.</span></span>

### <a name="can-i-configure-my-app-to-require-a-certificate-only-on-certain-paths"></a><span data-ttu-id="5aa3c-156">特定のパスでのみ証明書を要求するようにアプリを構成することはできますか。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-156">Can I configure my app to require a certificate only on certain paths?</span></span>

<span data-ttu-id="5aa3c-157">これはできません。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-157">This isn't possible.</span></span> <span data-ttu-id="5aa3c-158">証明書の交換は、HTTPS メッセージ交換の開始時に行われます。これは、その接続で最初の要求を受信する前にサーバーによって行われるため、要求フィールドに基づいてスコープを設定することはできません。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-158">Remember the certificate exchange is done that the start of the HTTPS conversation, it's done by the server before the first request is received on that connection so it's not possible to scope based on any request fields.</span></span>

## <a name="handler-events"></a><span data-ttu-id="5aa3c-159">ハンドラーイベント</span><span class="sxs-lookup"><span data-stu-id="5aa3c-159">Handler events</span></span>

<span data-ttu-id="5aa3c-160">ハンドラーには、次の2つのイベントがあります。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-160">The handler has two events:</span></span>

* <span data-ttu-id="5aa3c-161">`OnAuthenticationFailed`: 認証中に例外が発生し、対応できる場合に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-161">`OnAuthenticationFailed`: Called if an exception happens during authentication and allows you to react.</span></span>
* <span data-ttu-id="5aa3c-162">`OnCertificateValidated`: 証明書が検証され、検証が渡され、既定のプリンシパルが作成された後に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-162">`OnCertificateValidated`: Called after the certificate has been validated, passed validation and a default principal has been created.</span></span> <span data-ttu-id="5aa3c-163">このイベントを使用すると、独自の検証を実行したり、プリンシパルを補強したり置き換えることができます。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-163">This event allows you to perform your own validation and augment or replace the principal.</span></span> <span data-ttu-id="5aa3c-164">例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-164">For examples include:</span></span>
  * <span data-ttu-id="5aa3c-165">証明書がサービスに対して認識されているかどうかを確認しています。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-165">Determining if the certificate is known to your services.</span></span>
  * <span data-ttu-id="5aa3c-166">独自のプリンシパルを構築します。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-166">Constructing your own principal.</span></span> <span data-ttu-id="5aa3c-167">`Startup.ConfigureServices` での次の例を検討してください。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-167">Consider the following example in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddAuthentication(
    CertificateAuthenticationDefaults.AuthenticationScheme)
    .AddCertificate(options =>
    {
        options.Events = new CertificateAuthenticationEvents
        {
            OnCertificateValidated = context =>
            {
                var claims = new[]
                {
                    new Claim(
                        ClaimTypes.NameIdentifier, 
                        context.ClientCertificate.Subject,
                        ClaimValueTypes.String, 
                        context.Options.ClaimsIssuer),
                    new Claim(ClaimTypes.Name,
                        context.ClientCertificate.Subject,
                        ClaimValueTypes.String, 
                        context.Options.ClaimsIssuer)
                };

                context.Principal = new ClaimsPrincipal(
                    new ClaimsIdentity(claims, context.Scheme.Name));
                context.Success();

                return Task.CompletedTask;
            }
        };
    });
```

<span data-ttu-id="5aa3c-168">受信証明書が追加の検証を満たしていない場合は、 `context.Fail("failure reason")` 失敗の理由でを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-168">If you find the inbound certificate doesn't meet your extra validation, call `context.Fail("failure reason")` with a failure reason.</span></span>

<span data-ttu-id="5aa3c-169">実際の機能の場合は、データベースまたはその他の種類のユーザーストアに接続する依存関係の挿入に登録されているサービスを呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-169">For real functionality, you'll probably want to call a service registered in dependency injection that connects to a database or other type of user store.</span></span> <span data-ttu-id="5aa3c-170">デリゲートに渡されたコンテキストを使用してサービスにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-170">Access your service by using the context passed into your delegate.</span></span> <span data-ttu-id="5aa3c-171">`Startup.ConfigureServices` での次の例を検討してください。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-171">Consider the following example in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddAuthentication(
    CertificateAuthenticationDefaults.AuthenticationScheme)
    .AddCertificate(options =>
    {
        options.Events = new CertificateAuthenticationEvents
        {
            OnCertificateValidated = context =>
            {
                var validationService =
                    context.HttpContext.RequestServices
                        .GetService<ICertificateValidationService>();
                
                if (validationService.ValidateCertificate(
                    context.ClientCertificate))
                {
                    var claims = new[]
                    {
                        new Claim(
                            ClaimTypes.NameIdentifier, 
                            context.ClientCertificate.Subject, 
                            ClaimValueTypes.String, 
                            context.Options.ClaimsIssuer),
                        new Claim(
                            ClaimTypes.Name, 
                            context.ClientCertificate.Subject, 
                            ClaimValueTypes.String, 
                            context.Options.ClaimsIssuer)
                    };

                    context.Principal = new ClaimsPrincipal(
                        new ClaimsIdentity(claims, context.Scheme.Name));
                    context.Success();
                }                     

                return Task.CompletedTask;
            }
        };
    });
```

<span data-ttu-id="5aa3c-172">概念的には、証明書の検証は承認に関する問題です。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-172">Conceptually, the validation of the certificate is an authorization concern.</span></span> <span data-ttu-id="5aa3c-173">内部ではなく、承認ポリシーの発行者や拇印などのチェックを追加すること `OnCertificateValidated` は、まったく可能です。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-173">Adding a check on, for example, an issuer or thumbprint in an authorization policy, rather than inside `OnCertificateValidated`, is perfectly acceptable.</span></span>

## <a name="configure-your-host-to-require-certificates"></a><span data-ttu-id="5aa3c-174">証明書を要求するようにホストを構成する</span><span class="sxs-lookup"><span data-stu-id="5aa3c-174">Configure your host to require certificates</span></span>

### <a name="kestrel"></a><span data-ttu-id="5aa3c-175">Kestrel</span><span class="sxs-lookup"><span data-stu-id="5aa3c-175">Kestrel</span></span>

<span data-ttu-id="5aa3c-176">*Program.cs*で、次のように Kestrel を構成します。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-176">In *Program.cs*, configure Kestrel as follows:</span></span>

```csharp
public static void Main(string[] args)
{
    CreateHostBuilder(args).Build().Run();
}

public static IHostBuilder CreateHostBuilder(string[] args)
{
    return Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
            webBuilder.ConfigureKestrel(o =>
            {
                o.ConfigureHttpsDefaults(o => 
            o.ClientCertificateMode = 
                ClientCertificateMode.RequireCertificate);
            });
        });
}
```

> [!NOTE]
> <span data-ttu-id="5aa3c-177"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> を呼び出す**前に** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> を呼び出すことで作成されるエンドポイントには既定値が適用されません。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-177">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="iis"></a><span data-ttu-id="5aa3c-178">IIS</span><span class="sxs-lookup"><span data-stu-id="5aa3c-178">IIS</span></span>

<span data-ttu-id="5aa3c-179">IIS マネージャーで、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-179">Complete the following steps in IIS Manager:</span></span>

1. <span data-ttu-id="5aa3c-180">[**接続**] タブからサイトを選択します。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-180">Select your site from the **Connections** tab.</span></span>
1. <span data-ttu-id="5aa3c-181">[**機能ビュー** ] ウィンドウで、[ **SSL 設定**] オプションをダブルクリックします。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-181">Double-click the **SSL Settings** option in the **Features View** window.</span></span>
1. <span data-ttu-id="5aa3c-182">[ **SSL が必要**] チェックボックスをオンにし、[**クライアント証明書**] セクションの [**必須**] オプションを選択します。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-182">Check the **Require SSL** checkbox, and select the **Require** radio button in the **Client certificates** section.</span></span>

![IIS でのクライアント証明書の設定](README-IISConfig.png)

### <a name="azure-and-custom-web-proxies"></a><span data-ttu-id="5aa3c-184">Azure とカスタム web プロキシ</span><span class="sxs-lookup"><span data-stu-id="5aa3c-184">Azure and custom web proxies</span></span>

<span data-ttu-id="5aa3c-185">証明書転送ミドルウェアを構成する方法については、[ホストを参照し、ドキュメントを展開](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding)してください。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-185">See the [host and deploy documentation](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding) for how to configure the certificate forwarding middleware.</span></span>

### <a name="use-certificate-authentication-in-azure-web-apps"></a><span data-ttu-id="5aa3c-186">Azure Web Apps で証明書認証を使用する</span><span class="sxs-lookup"><span data-stu-id="5aa3c-186">Use certificate authentication in Azure Web Apps</span></span>

<span data-ttu-id="5aa3c-187">Azure では、転送構成は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-187">No forwarding configuration is required for Azure.</span></span> <span data-ttu-id="5aa3c-188">これは既に証明書転送ミドルウェアで設定されています。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-188">This is already setup in the certificate forwarding middleware.</span></span>

> [!NOTE]
> <span data-ttu-id="5aa3c-189">これを行うには、CertificateForwardingMiddleware が存在している必要があります。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-189">This requires that the CertificateForwardingMiddleware is present.</span></span>

### <a name="use-certificate-authentication-in-custom-web-proxies"></a><span data-ttu-id="5aa3c-190">カスタム web プロキシで証明書認証を使用する</span><span class="sxs-lookup"><span data-stu-id="5aa3c-190">Use certificate authentication in custom web proxies</span></span>

<span data-ttu-id="5aa3c-191">メソッドを使用して、 `AddCertificateForwarding` 次のように指定します。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-191">The `AddCertificateForwarding` method is used to specify:</span></span>

* <span data-ttu-id="5aa3c-192">クライアントヘッダー名。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-192">The client header name.</span></span>
* <span data-ttu-id="5aa3c-193">(プロパティを使用して) 証明書を読み込む方法 `HeaderConverter` 。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-193">How the certificate is to be loaded (using the `HeaderConverter` property).</span></span>

<span data-ttu-id="5aa3c-194">カスタム web プロキシでは、証明書はカスタム要求ヘッダーとして渡されます。たとえば、のように `X-SSL-CERT` なります。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-194">In custom web proxies, the certificate is passed as a custom request header, for example `X-SSL-CERT`.</span></span> <span data-ttu-id="5aa3c-195">これを使用するには、次のように証明書の転送を構成し `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-195">To use it, configure certificate forwarding in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddCertificateForwarding(options =>
    {
        options.CertificateHeader = "X-SSL-CERT";
        options.HeaderConverter = (headerValue) =>
        {
            X509Certificate2 clientCertificate = null;
        
            if(!string.IsNullOrWhiteSpace(headerValue))
            {
                byte[] bytes = StringToByteArray(headerValue);
                clientCertificate = new X509Certificate2(bytes);
            }

            return clientCertificate;
        };
    });
}

private static byte[] StringToByteArray(string hex)
{
    int NumberChars = hex.Length;
    byte[] bytes = new byte[NumberChars / 2];

    for (int i = 0; i < NumberChars; i += 2)
    {
        bytes[i / 2] = Convert.ToByte(hex.Substring(i, 2), 16);
    }

    return bytes;
}
```

<span data-ttu-id="5aa3c-196">次に、 `Startup.Configure` メソッドはミドルウェアを追加します。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-196">The `Startup.Configure` method then adds the middleware.</span></span> <span data-ttu-id="5aa3c-197">`UseCertificateForwarding`は、およびの呼び出しの前に呼び出され `UseAuthentication` `UseAuthorization` ます。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-197">`UseCertificateForwarding` is called before the calls to `UseAuthentication` and `UseAuthorization`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseRouting();

    app.UseCertificateForwarding();
    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllers();
    });
}
```

<span data-ttu-id="5aa3c-198">別のクラスを使用して、検証ロジックを実装できます。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-198">A separate class can be used to implement validation logic.</span></span> <span data-ttu-id="5aa3c-199">この例では同じ自己署名証明書が使用されているため、使用できるのは証明書のみであることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-199">Because the same self-signed certificate is used in this example, ensure that only your certificate can be used.</span></span> <span data-ttu-id="5aa3c-200">クライアント証明書とサーバー証明書の両方の拇印が一致しているかどうかを検証します。それ以外の場合は、証明書を使用して、認証に十分なものになります。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-200">Validate that the thumbprints of both the client certificate and the server certificate match, otherwise any certificate can be used and will be enough to authenticate.</span></span> <span data-ttu-id="5aa3c-201">これは、メソッドの内部で使用され `AddCertificate` ます。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-201">This would be used inside the `AddCertificate` method.</span></span> <span data-ttu-id="5aa3c-202">中間証明書または子証明書を使用している場合は、ここでサブジェクトまたは発行者を検証することもできます。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-202">You could also validate the subject or the issuer here if you're using intermediate or child certificates.</span></span>

```csharp
using System.IO;
using System.Security.Cryptography.X509Certificates;

namespace AspNetCoreCertificateAuthApi
{
    public class MyCertificateValidationService
    {
        public bool ValidateCertificate(X509Certificate2 clientCertificate)
        {
            // Do not hardcode passwords in production code
            // Use thumbprint or key vault
            var cert = new X509Certificate2(
                Path.Combine("sts_dev_cert.pfx"), "1234");

            if (clientCertificate.Thumbprint == cert.Thumbprint)
            {
                return true;
            }

            return false;
        }
    }
}
```

#### <a name="implement-an-httpclient-using-a-certificate-and-the-httpclienthandler"></a><span data-ttu-id="5aa3c-203">証明書と HttpClientHandler を使用して HttpClient を実装する</span><span class="sxs-lookup"><span data-stu-id="5aa3c-203">Implement an HttpClient using a certificate and the HttpClientHandler</span></span>

<span data-ttu-id="5aa3c-204">HttpClientHandler は、HttpClient クラスのコンストラクターに直接追加できます。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-204">The HttpClientHandler could be added directly in the constructor of the HttpClient class.</span></span> <span data-ttu-id="5aa3c-205">HttpClient のインスタンスを作成するときは注意が必要です。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-205">Care should be taken when creating instances of the HttpClient.</span></span> <span data-ttu-id="5aa3c-206">HttpClient は、要求ごとに証明書を送信します。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-206">The HttpClient will then send the certificate with each request.</span></span>

```csharp
private async Task<JsonDocument> GetApiDataUsingHttpClientHandler()
{
    var cert = new X509Certificate2(Path.Combine(_environment.ContentRootPath, "sts_dev_cert.pfx"), "1234");
    var handler = new HttpClientHandler();
    handler.ClientCertificates.Add(cert);
    var client = new HttpClient(handler);
     
    var request = new HttpRequestMessage()
    {
        RequestUri = new Uri("https://localhost:44379/api/values"),
        Method = HttpMethod.Get,
    };
    var response = await client.SendAsync(request);
    if (response.IsSuccessStatusCode)
    {
        var responseContent = await response.Content.ReadAsStringAsync();
        var data = JsonDocument.Parse(responseContent);
        return data;
    }
 
    throw new ApplicationException($"Status code: {response.StatusCode}, Error: {response.ReasonPhrase}");
}
```

#### <a name="implement-an-httpclient-using-a-certificate-and-a-named-httpclient-from-ihttpclientfactory"></a><span data-ttu-id="5aa3c-207">IHttpClientFactory から証明書と名前付き HttpClient を使用して HttpClient を実装する</span><span class="sxs-lookup"><span data-stu-id="5aa3c-207">Implement an HttpClient using a certificate and a named HttpClient from IHttpClientFactory</span></span> 

<span data-ttu-id="5aa3c-208">次の例では、ハンドラーから ClientCertificates プロパティを使用して、クライアント証明書を HttpClientHandler に追加しています。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-208">In the following example, a client certificate is added to a HttpClientHandler using the ClientCertificates property from the handler.</span></span> <span data-ttu-id="5aa3c-209">このハンドラーは、HttpClient の名前付きインスタンスで ConfigurePrimaryHttpMessageHandler メソッドを使用して使用できます。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-209">This handler can then be used in a named instance of a HttpClient using the ConfigurePrimaryHttpMessageHandler method.</span></span> <span data-ttu-id="5aa3c-210">これは、ConfigureServices メソッドの Startup クラスに設定されています。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-210">This is setup in the Startup class in the ConfigureServices method.</span></span>

```csharp
var clientCertificate = 
    new X509Certificate2(
      Path.Combine(_environment.ContentRootPath, "sts_dev_cert.pfx"), "1234");
 
var handler = new HttpClientHandler();
handler.ClientCertificates.Add(clientCertificate);
 
services.AddHttpClient("namedClient", c =>
{
}).ConfigurePrimaryHttpMessageHandler(() => handler);
```

<span data-ttu-id="5aa3c-211">IHttpClientFactory を使用すると、ハンドラーと証明書を使用して名前付きインスタンスを取得できます。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-211">The IHttpClientFactory can then be used to get the named instance with the handler and the certificate.</span></span> <span data-ttu-id="5aa3c-212">インスタンスを取得するには、Startup クラスで定義されているクライアントの名前を持つ CreateClient メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-212">The CreateClient method with the name of the client defined in the Startup class is used to get the instance.</span></span> <span data-ttu-id="5aa3c-213">HTTP 要求は、クライアントを使用して必要に応じて送信できます。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-213">The HTTP request can be sent using the client as required.</span></span>

```csharp
private readonly IHttpClientFactory _clientFactory;
 
public ApiService(IHttpClientFactory clientFactory)
{
    _clientFactory = clientFactory;
}
 
private async Task<JsonDocument> GetApiDataWithNamedClient()
{
    var client = _clientFactory.CreateClient("namedClient");
 
    var request = new HttpRequestMessage()
    {
        RequestUri = new Uri("https://localhost:44379/api/values"),
        Method = HttpMethod.Get,
    };
    var response = await client.SendAsync(request);
    if (response.IsSuccessStatusCode)
    {
        var responseContent = await response.Content.ReadAsStringAsync();
        var data = JsonDocument.Parse(responseContent);
        return data;
    }
 
    throw new ApplicationException($"Status code: {response.StatusCode}, Error: {response.ReasonPhrase}");
}
```

<span data-ttu-id="5aa3c-214">正しい証明書がサーバーに送信されると、データが返されます。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-214">If the correct certificate is sent to the server, the data is returned.</span></span> <span data-ttu-id="5aa3c-215">証明書または間違った証明書が送信されない場合は、HTTP 403 ステータスコードが返されます。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-215">If no certificate or the wrong certificate is sent, an HTTP 403 status code is returned.</span></span>

### <a name="create-certificates-in-powershell"></a><span data-ttu-id="5aa3c-216">PowerShell での証明書の作成</span><span class="sxs-lookup"><span data-stu-id="5aa3c-216">Create certificates in PowerShell</span></span>

<span data-ttu-id="5aa3c-217">証明書の作成は、このフローを設定するうえで最も難しい部分です。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-217">Creating the certificates is the hardest part in setting up this flow.</span></span> <span data-ttu-id="5aa3c-218">ルート証明書は、PowerShell コマンドレットを使用して作成でき `New-SelfSignedCertificate` ます。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-218">A root certificate can be created using the `New-SelfSignedCertificate` PowerShell cmdlet.</span></span> <span data-ttu-id="5aa3c-219">証明書を作成するときは、強力なパスワードを使用します。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-219">When creating the certificate, use a strong password.</span></span> <span data-ttu-id="5aa3c-220">ここで示すように、 `KeyUsageProperty` パラメーターとパラメーターを追加することが重要です `KeyUsage` 。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-220">It's important to add the `KeyUsageProperty` parameter and the `KeyUsage` parameter as shown.</span></span>

#### <a name="create-root-ca"></a><span data-ttu-id="5aa3c-221">ルート CA の作成</span><span class="sxs-lookup"><span data-stu-id="5aa3c-221">Create root CA</span></span>

```powershell
New-SelfSignedCertificate -DnsName "root_ca_dev_damienbod.com", "root_ca_dev_damienbod.com" -CertStoreLocation "cert:\LocalMachine\My" -NotAfter (Get-Date).AddYears(20) -FriendlyName "root_ca_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\root_ca_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath root_ca_dev_damienbod.crt
```

> [!NOTE]
> <span data-ttu-id="5aa3c-222">`-DnsName`パラメーター値は、アプリの配置ターゲットと一致している必要があります。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-222">The `-DnsName` parameter value must match the deployment target of the app.</span></span> <span data-ttu-id="5aa3c-223">たとえば、開発用の "localhost" などです。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-223">For example, "localhost" for development.</span></span>

#### <a name="install-in-the-trusted-root"></a><span data-ttu-id="5aa3c-224">信頼されたルートにインストールする</span><span class="sxs-lookup"><span data-stu-id="5aa3c-224">Install in the trusted root</span></span>

<span data-ttu-id="5aa3c-225">ルート証明書は、ホストシステムで信頼されている必要があります。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-225">The root certificate needs to be trusted on your host system.</span></span> <span data-ttu-id="5aa3c-226">証明機関によって作成されていないルート証明書は、既定では信頼されません。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-226">A root certificate which was not created by a certificate authority won't be trusted by default.</span></span> <span data-ttu-id="5aa3c-227">次のリンクを使用して、Windows でこれを実現する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-227">The following link explains how this can be accomplished on Windows:</span></span>

https://social.msdn.microsoft.com/Forums/SqlServer/5ed119ef-1704-4be4-8a4f-ef11de7c8f34/a-certificate-chain-processed-but-terminated-in-a-root-certificate-which-is-not-trusted-by-the

#### <a name="intermediate-certificate"></a><span data-ttu-id="5aa3c-228">中間証明機関の証明書</span><span class="sxs-lookup"><span data-stu-id="5aa3c-228">Intermediate certificate</span></span>

<span data-ttu-id="5aa3c-229">ルート証明書から中間証明書を作成できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-229">An intermediate certificate can now be created from the root certificate.</span></span> <span data-ttu-id="5aa3c-230">これはすべてのユースケースで必須ではありませんが、多くの証明書を作成したり、証明書のグループをアクティブ化または無効にしたりする必要がある場合があります。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-230">This isn't required for all use cases, but you might need to create many certificates or need to activate or disable groups of certificates.</span></span> <span data-ttu-id="5aa3c-231">`TextExtension`パラメーターは、証明書の基本的な制約でパスの長さを設定するために必要です。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-231">The `TextExtension` parameter is required to set the path length in the basic constraints of the certificate.</span></span>

<span data-ttu-id="5aa3c-232">その後、中間証明書を Windows ホストシステムの信頼された中間証明書に追加できます。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-232">The intermediate certificate can then be added to the trusted intermediate certificate in the Windows host system.</span></span>

```powershell
$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

$parentcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint of the root..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "intermediate_dev_damienbod.com" -Signer $parentcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "intermediate_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature -TextExtension @("2.5.29.19={text}CA=1&pathlength=1")

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\intermediate_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath intermediate_dev_damienbod.crt
```

#### <a name="create-child-certificate-from-intermediate-certificate"></a><span data-ttu-id="5aa3c-233">中間証明書から子証明書を作成する</span><span class="sxs-lookup"><span data-stu-id="5aa3c-233">Create child certificate from intermediate certificate</span></span>

<span data-ttu-id="5aa3c-234">子証明書は、中間証明書から作成できます。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-234">A child certificate can be created from the intermediate certificate.</span></span> <span data-ttu-id="5aa3c-235">これは end エンティティであり、さらに子証明書を作成する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-235">This is the end entity and doesn't need to create more child certificates.</span></span>

```powershell
$parentcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint from the Intermediate certificate..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_a_dev_damienbod.com" -Signer $parentcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_a_dev_damienbod.com"

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath child_a_dev_damienbod.crt
```

#### <a name="create-child-certificate-from-root-certificate"></a><span data-ttu-id="5aa3c-236">ルート証明書から子証明書を作成する</span><span class="sxs-lookup"><span data-stu-id="5aa3c-236">Create child certificate from root certificate</span></span>

<span data-ttu-id="5aa3c-237">子証明書は、ルート証明書から直接作成することもできます。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-237">A child certificate can also be created from the root certificate directly.</span></span> 

```powershell
$rootcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint from the root cert..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_a_dev_damienbod.com" -Signer $rootcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_a_dev_damienbod.com"

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath child_a_dev_damienbod.crt
```

#### <a name="example-root---intermediate-certificate---certificate"></a><span data-ttu-id="5aa3c-238">ルート中間証明書の例-証明書</span><span class="sxs-lookup"><span data-stu-id="5aa3c-238">Example root - intermediate certificate - certificate</span></span>

```powershell
$mypwdroot = ConvertTo-SecureString -String "1234" -Force -AsPlainText
$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

New-SelfSignedCertificate -DnsName "root_ca_dev_damienbod.com", "root_ca_dev_damienbod.com" -CertStoreLocation "cert:\LocalMachine\My" -NotAfter (Get-Date).AddYears(20) -FriendlyName "root_ca_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature

Get-ChildItem -Path cert:\localMachine\my\0C89639E4E2998A93E423F919B36D4009A0F9991 | Export-PfxCertificate -FilePath C:\git\root_ca_dev_damienbod.pfx -Password $mypwdroot

Export-Certificate -Cert cert:\localMachine\my\0C89639E4E2998A93E423F919B36D4009A0F9991 -FilePath root_ca_dev_damienbod.crt

$rootcert = ( Get-ChildItem -Path cert:\LocalMachine\My\0C89639E4E2998A93E423F919B36D4009A0F9991 )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_a_dev_damienbod.com" -Signer $rootcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_a_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature -TextExtension @("2.5.29.19={text}CA=1&pathlength=1")

Get-ChildItem -Path cert:\localMachine\my\BA9BF91ED35538A01375EFC212A2F46104B33A44 | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\BA9BF91ED35538A01375EFC212A2F46104B33A44 -FilePath child_a_dev_damienbod.crt

$parentcert = ( Get-ChildItem -Path cert:\LocalMachine\My\BA9BF91ED35538A01375EFC212A2F46104B33A44 )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_b_from_a_dev_damienbod.com" -Signer $parentcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_b_from_a_dev_damienbod.com" 

Get-ChildItem -Path cert:\localMachine\my\141594A0AE38CBBECED7AF680F7945CD51D8F28A | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_b_from_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\141594A0AE38CBBECED7AF680F7945CD51D8F28A -FilePath child_b_from_a_dev_damienbod.crt
```

<span data-ttu-id="5aa3c-239">ルート証明書、中間証明書、または子証明書を使用する場合は、必要に応じて拇印または PublicKey を使用して証明書を検証できます。</span><span class="sxs-lookup"><span data-stu-id="5aa3c-239">When using the root, intermediate, or child certificates, the certificates can be validated using the Thumbprint or PublicKey as required.</span></span>

```csharp
using System.Collections.Generic;
using System.IO;
using System.Security.Cryptography.X509Certificates;

namespace AspNetCoreCertificateAuthApi
{
    public class MyCertificateValidationService 
    {
        public bool ValidateCertificate(X509Certificate2 clientCertificate)
        {
            return CheckIfThumbprintIsValid(clientCertificate);
        }

        private bool CheckIfThumbprintIsValid(X509Certificate2 clientCertificate)
        {
            var listOfValidThumbprints = new List<string>
            {
                "141594A0AE38CBBECED7AF680F7945CD51D8F28A",
                "0C89639E4E2998A93E423F919B36D4009A0F9991",
                "BA9BF91ED35538A01375EFC212A2F46104B33A44"
            };

            if (listOfValidThumbprints.Contains(clientCertificate.Thumbprint))
            {
                return true;
            }

            return false;
        }
    }
}
```
