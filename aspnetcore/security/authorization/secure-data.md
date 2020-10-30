---
title: 承認によって保護されたユーザーデータを含む ASP.NET Core アプリを作成する
author: rick-anderson
description: '認証によって保護されたユーザーデータを使用して ASP.NET Core web アプリを作成する方法について説明します。 HTTPS、認証、セキュリティ、などが含まれ :::no-loc(ASP.NET Core Identity)::: ます。'
ms.author: riande
ms.date: 7/18/2020
ms.custom: mvc, seodec18
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/authorization/secure-data
ms.openlocfilehash: accfd46fa72c33976f8af2a39267c993447e036e
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051942"
---
# <a name="create-an-aspnet-core-web-app-with-user-data-protected-by-authorization"></a><span data-ttu-id="c7a43-104">認証によって保護されたユーザーデータを使用して ASP.NET Core web アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="c7a43-104">Create an ASP.NET Core web app with user data protected by authorization</span></span>

<span data-ttu-id="c7a43-105">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT) および [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="c7a43-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="c7a43-106">[次の pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)を参照</span><span class="sxs-lookup"><span data-stu-id="c7a43-106">See [this pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c7a43-107">このチュートリアルでは、承認によって保護されたユーザーデータを使用して ASP.NET Core web アプリを作成する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-107">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="c7a43-108">認証済み (登録済み) のユーザーが作成した連絡先の一覧が表示されます。</span><span class="sxs-lookup"><span data-stu-id="c7a43-108">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="c7a43-109">セキュリティグループには次の3つがあります。</span><span class="sxs-lookup"><span data-stu-id="c7a43-109">There are three security groups:</span></span>

* <span data-ttu-id="c7a43-110">**登録されているユーザー** は、すべての承認済みデータを表示したり、自分のデータを編集または削除したりできます。</span><span class="sxs-lookup"><span data-stu-id="c7a43-110">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="c7a43-111">**管理者** は、連絡先データを承認または拒否できます。</span><span class="sxs-lookup"><span data-stu-id="c7a43-111">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="c7a43-112">承認された連絡先だけがユーザーに表示されます。</span><span class="sxs-lookup"><span data-stu-id="c7a43-112">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="c7a43-113">**管理者** は、任意のデータを承認/拒否したり、編集/削除したりできます。</span><span class="sxs-lookup"><span data-stu-id="c7a43-113">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="c7a43-114">このドキュメントの画像は、最新のテンプレートと完全には一致しません。</span><span class="sxs-lookup"><span data-stu-id="c7a43-114">The images in this document don't exactly match the latest templates.</span></span>

<span data-ttu-id="c7a43-115">次の図では、user Rick ( `rick@example.com` ) がサインインしています。</span><span class="sxs-lookup"><span data-stu-id="c7a43-115">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="c7a43-116">Rick は、承認された連絡先を表示したり、[削除] を **編集** したりするだけで、 / **Delete** / 連絡先の新しいリンクを **作成** できます。</span><span class="sxs-lookup"><span data-stu-id="c7a43-116">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="c7a43-117">Rick によって作成された最後のレコードにのみ、 **編集** および **削除** のリンクが表示されます。</span><span class="sxs-lookup"><span data-stu-id="c7a43-117">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="c7a43-118">他のユーザーには、マネージャーまたは管理者が状態を "承認済み" に変更するまで、最後のレコードは表示されません。</span><span class="sxs-lookup"><span data-stu-id="c7a43-118">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Rick がサインインしていることを示すスクリーンショット](secure-data/_static/rick.png)

<span data-ttu-id="c7a43-120">次の図で `manager@contoso.com` は、がサインインし、マネージャーのロールに含まれています。</span><span class="sxs-lookup"><span data-stu-id="c7a43-120">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![サインインしたことを示すスクリーンショット manager@contoso.com](secure-data/_static/manager1.png)

<span data-ttu-id="c7a43-122">次の図は、連絡先のマネージャーの詳細ビューを示しています。</span><span class="sxs-lookup"><span data-stu-id="c7a43-122">The following image shows the managers details view of a contact:</span></span>

![連絡先のマネージャーの表示](secure-data/_static/manager.png)

<span data-ttu-id="c7a43-124">[ **承認** ] ボタンと [ **却下** ] ボタンは、マネージャーと管理者に対してのみ表示されます。</span><span class="sxs-lookup"><span data-stu-id="c7a43-124">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="c7a43-125">次の図で `admin@contoso.com` は、がサインインし、管理者のロールに含まれています。</span><span class="sxs-lookup"><span data-stu-id="c7a43-125">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![サインインしたことを示すスクリーンショット admin@contoso.com](secure-data/_static/admin.png)

<span data-ttu-id="c7a43-127">管理者には、すべての特権があります。</span><span class="sxs-lookup"><span data-stu-id="c7a43-127">The administrator has all privileges.</span></span> <span data-ttu-id="c7a43-128">連絡先の読み取り、編集、削除、および連絡先の状態の変更を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="c7a43-128">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="c7a43-129">アプリは、次のモデルを [スキャフォールディング](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) することによって作成されました `Contact` 。</span><span class="sxs-lookup"><span data-stu-id="c7a43-129">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="c7a43-130">このサンプルには、次の承認ハンドラーが含まれています。</span><span class="sxs-lookup"><span data-stu-id="c7a43-130">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="c7a43-131">`ContactIsOwnerAuthorizationHandler`: ユーザーがデータを編集できるようにします。</span><span class="sxs-lookup"><span data-stu-id="c7a43-131">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="c7a43-132">`ContactManagerAuthorizationHandler`: 管理者が連絡先を承認または拒否できるようにします。</span><span class="sxs-lookup"><span data-stu-id="c7a43-132">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="c7a43-133">`ContactAdministratorsAuthorizationHandler`: 管理者は、連絡先を承認または拒否したり、連絡先を編集または削除したりできます。</span><span class="sxs-lookup"><span data-stu-id="c7a43-133">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="c7a43-134">[前提条件]</span><span class="sxs-lookup"><span data-stu-id="c7a43-134">Prerequisites</span></span>

<span data-ttu-id="c7a43-135">このチュートリアルは高度です。</span><span class="sxs-lookup"><span data-stu-id="c7a43-135">This tutorial is advanced.</span></span> <span data-ttu-id="c7a43-136">次のことを理解している必要があります。</span><span class="sxs-lookup"><span data-stu-id="c7a43-136">You should be familiar with:</span></span>

* [<span data-ttu-id="c7a43-137">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c7a43-137">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="c7a43-138">認証</span><span class="sxs-lookup"><span data-stu-id="c7a43-138">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="c7a43-139">アカウントの確認とパスワードの回復</span><span class="sxs-lookup"><span data-stu-id="c7a43-139">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="c7a43-140">承認</span><span class="sxs-lookup"><span data-stu-id="c7a43-140">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="c7a43-141">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="c7a43-141">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="c7a43-142">スターターおよび完成したアプリ</span><span class="sxs-lookup"><span data-stu-id="c7a43-142">The starter and completed app</span></span>

<span data-ttu-id="c7a43-143">完成し[た](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples)アプリを[ダウンロード](xref:index#how-to-download-a-sample)します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-143">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="c7a43-144">完成したアプリを[テスト](#test-the-completed-app)して、セキュリティ機能に慣れるようにします。</span><span class="sxs-lookup"><span data-stu-id="c7a43-144">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="c7a43-145">スターターアプリ</span><span class="sxs-lookup"><span data-stu-id="c7a43-145">The starter app</span></span>

<span data-ttu-id="c7a43-146">[スターター](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/)アプリを[ダウンロード](xref:index#how-to-download-a-sample)します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-146">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="c7a43-147">アプリを実行し、[ **Contactmanager** ] リンクをタップして、連絡先を作成、編集、および削除できることを確認します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-147">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="c7a43-148">ユーザーデータのセキュリティ保護</span><span class="sxs-lookup"><span data-stu-id="c7a43-148">Secure user data</span></span>

<span data-ttu-id="c7a43-149">以下のセクションでは、セキュリティで保護されたユーザーデータアプリを作成するための主要な手順について説明します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-149">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="c7a43-150">完成したプロジェクトを参照した方が便利な場合があります。</span><span class="sxs-lookup"><span data-stu-id="c7a43-150">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="c7a43-151">連絡先データをユーザーに関連付ける</span><span class="sxs-lookup"><span data-stu-id="c7a43-151">Tie the contact data to the user</span></span>

<span data-ttu-id="c7a43-152">ASP.NET ユーザー ID を使用すると、 [:::no-loc(Identity):::](xref:security/authentication/identity) ユーザーがデータを編集できるようになりますが、他のユーザーデータは編集できません。</span><span class="sxs-lookup"><span data-stu-id="c7a43-152">Use the ASP.NET [:::no-loc(Identity):::](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="c7a43-153">`OwnerID` `ContactStatus` モデルにおよびを追加し `Contact` ます。</span><span class="sxs-lookup"><span data-stu-id="c7a43-153">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final3/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="c7a43-154">`OwnerID` データベース内のテーブルのユーザー ID を示し `AspNetUser` [:::no-loc(Identity):::](xref:security/authentication/identity) ます。</span><span class="sxs-lookup"><span data-stu-id="c7a43-154">`OwnerID` is the user's ID from the `AspNetUser` table in the [:::no-loc(Identity):::](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="c7a43-155">フィールドは、 `Status` 一般的なユーザーが連絡先を表示できるかどうかを決定します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-155">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="c7a43-156">新しい移行を作成し、データベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-156">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-no-locidentity"></a><span data-ttu-id="c7a43-157">役割サービスの追加先 :::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="c7a43-157">Add Role services to :::no-loc(Identity):::</span></span>

<span data-ttu-id="c7a43-158">役割サービスを追加するには、 [Addroles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_:::no-loc(Identity):::_:::no-loc(Identity):::Builder_AddRoles__1) を追加します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-158">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_:::no-loc(Identity):::_:::no-loc(Identity):::Builder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet2&highlight=9)]

<a name="rau"></a>

### <a name="require-authenticated-users"></a><span data-ttu-id="c7a43-159">認証されたユーザーが必要</span><span class="sxs-lookup"><span data-stu-id="c7a43-159">Require authenticated users</span></span>

<span data-ttu-id="c7a43-160">ユーザーが認証されるようにするには、フォールバック認証ポリシーを設定します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-160">Set the fallback authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet&highlight=13-99)]

<span data-ttu-id="c7a43-161">前の強調表示されたコードは、 [フォールバック認証ポリシー](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy)を設定します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-161">The preceding highlighted code sets the [fallback authentication policy](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy).</span></span> <span data-ttu-id="c7a43-162">フォールバック認証ポリシーでは、 *_all_* :::no-loc(Razor)::: 認証属性を持つページ、コントローラー、またはアクションメソッドを除き、\* すべての _ ユーザーを認証する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c7a43-162">The fallback authentication policy requires \* *_all_* _ users to be authenticated, except for :::no-loc(Razor)::: Pages, controllers, or action methods with an authentication attribute.</span></span> <span data-ttu-id="c7a43-163">たとえば、 :::no-loc(Razor)::: ページ、コントローラー、アクションメソッド `[AllowAnonymous]` は、 `[Authorize(PolicyName="MyPolicy")]` フォールバック認証ポリシーではなく、適用された認証属性を使用します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-163">For example, :::no-loc(Razor)::: Pages, controllers, or action methods with `[AllowAnonymous]` or `[Authorize(PolicyName="MyPolicy")]` use the applied authentication attribute rather than the fallback authentication policy.</span></span>

<span data-ttu-id="c7a43-164">フォールバック認証ポリシー:</span><span class="sxs-lookup"><span data-stu-id="c7a43-164">The fallback authentication policy:</span></span>

<span data-ttu-id="c7a43-165">_ は、認証ポリシーを明示的に指定しないすべての要求に適用されます。</span><span class="sxs-lookup"><span data-stu-id="c7a43-165">_ Is applied to all requests that do not explicitly specify an authentication policy.</span></span> <span data-ttu-id="c7a43-166">エンドポイントのルーティングによって提供される要求の場合、これには承認属性を指定しないエンドポイントが含まれます。</span><span class="sxs-lookup"><span data-stu-id="c7a43-166">For requests served by endpoint routing, this would include any endpoint that does not specify an authorization attribute.</span></span> <span data-ttu-id="c7a43-167">[静的ファイル](xref:fundamentals/static-files)など、承認ミドルウェアの後に他のミドルウェアによって提供される要求の場合、ポリシーがすべての要求に適用されます。</span><span class="sxs-lookup"><span data-stu-id="c7a43-167">For requests served by other middleware after the authorization middleware, such as [static files](xref:fundamentals/static-files), this would apply the policy to all requests.</span></span>

<span data-ttu-id="c7a43-168">代替認証ポリシーを設定して、ユーザーが認証を要求するようにすると、新しく追加されたページとコントローラーが保護され :::no-loc(Razor)::: ます。</span><span class="sxs-lookup"><span data-stu-id="c7a43-168">Setting the fallback authentication policy to require users to be authenticated protects newly added :::no-loc(Razor)::: Pages and controllers.</span></span> <span data-ttu-id="c7a43-169">既定で認証が必要になることは、新しいコントローラーやページを利用して属性を含めるよりも安全です :::no-loc(Razor)::: `[Authorize]` 。</span><span class="sxs-lookup"><span data-stu-id="c7a43-169">Having authentication required by default is more secure than relying on new controllers and :::no-loc(Razor)::: Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="c7a43-170">クラスには <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions> も含まれ <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.DefaultPolicy?displayProperty=nameWithType> ます。</span><span class="sxs-lookup"><span data-stu-id="c7a43-170">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions> class also contains <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.DefaultPolicy?displayProperty=nameWithType>.</span></span> <span data-ttu-id="c7a43-171">は、 `DefaultPolicy` `[Authorize]` ポリシーが指定されていない場合に、属性で使用されるポリシーです。</span><span class="sxs-lookup"><span data-stu-id="c7a43-171">The `DefaultPolicy` is the policy used with the `[Authorize]` attribute when no policy is specified.</span></span> <span data-ttu-id="c7a43-172">`[Authorize]` はと異なり、名前付きポリシーを含んでいません `[Authorize(PolicyName="MyPolicy")]` 。</span><span class="sxs-lookup"><span data-stu-id="c7a43-172">`[Authorize]` doesn't contain a named policy, unlike `[Authorize(PolicyName="MyPolicy")]`.</span></span>

<span data-ttu-id="c7a43-173">ポリシーの詳細については、「」を参照してください <xref:security/authorization/policies> 。</span><span class="sxs-lookup"><span data-stu-id="c7a43-173">For more information on policies, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="c7a43-174">すべてのユーザーの認証を要求する MVC コントローラーとページの別の方法 :::no-loc(Razor)::: は、承認フィルターを追加することです。</span><span class="sxs-lookup"><span data-stu-id="c7a43-174">An alternative way for MVC controllers and :::no-loc(Razor)::: Pages to require all users be authenticated is adding an authorization filter:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup2.cs?name=snippet&highlight=14-99)]

<span data-ttu-id="c7a43-175">上記のコードでは、承認フィルターを使用します。フォールバックポリシーを設定するには、エンドポイントルーティングを使用します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-175">The preceding code uses an authorization filter, setting the fallback policy uses endpoint routing.</span></span> <span data-ttu-id="c7a43-176">すべてのユーザーが認証されるようにするには、フォールバックポリシーを設定することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="c7a43-176">Setting the fallback policy is the preferred way to require all users be authenticated.</span></span>

<span data-ttu-id="c7a43-177">[AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) `Index` `Privacy` 匿名ユーザーが登録前にサイトに関する情報を取得できるように、allowanonymous をおよびページに追加します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-177">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the `Index` and `Privacy` pages so anonymous users can get information about the site before they register:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Index.cshtml.cs?highlight=1,7)]

### <a name="configure-the-test-account"></a><span data-ttu-id="c7a43-178">テストアカウントを構成する</span><span class="sxs-lookup"><span data-stu-id="c7a43-178">Configure the test account</span></span>

<span data-ttu-id="c7a43-179">クラスは、 `SeedData` 管理者とマネージャーの2つのアカウントを作成します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-179">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="c7a43-180">これらのアカウントのパスワードを設定するには、 [シークレットマネージャーツール](xref:security/app-secrets) を使用します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-180">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="c7a43-181">プロジェクトディレクトリ ( *Program.cs* を含むディレクトリ) のパスワードを設定します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-181">Set the password from the project directory (the directory containing *Program.cs* ):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="c7a43-182">強力なパスワードが指定されていない場合は、が呼び出されると例外がスローされ `SeedData.Initialize` ます。</span><span class="sxs-lookup"><span data-stu-id="c7a43-182">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="c7a43-183">`Main`テストパスワードを使用するように更新します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-183">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final3/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="c7a43-184">テストアカウントを作成し、連絡先を更新する</span><span class="sxs-lookup"><span data-stu-id="c7a43-184">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="c7a43-185">クラスのメソッドを更新して、 `Initialize` `SeedData` テストアカウントを作成します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-185">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="c7a43-186">管理者のユーザー ID とを `ContactStatus` 連絡先に追加します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-186">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="c7a43-187">いずれかの連絡先を "送信済み" にして、"拒否" するようにします。</span><span class="sxs-lookup"><span data-stu-id="c7a43-187">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="c7a43-188">すべての連絡先にユーザー ID と状態を追加します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-188">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="c7a43-189">1つの連絡先のみが表示されます。</span><span class="sxs-lookup"><span data-stu-id="c7a43-189">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="c7a43-190">所有者、マネージャー、および管理者の承認ハンドラーを作成する</span><span class="sxs-lookup"><span data-stu-id="c7a43-190">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="c7a43-191">`ContactIsOwnerAuthorizationHandler`*承認* フォルダーにクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-191">Create a `ContactIsOwnerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="c7a43-192">は、 `ContactIsOwnerAuthorizationHandler` リソースに対して動作しているユーザーがリソースを所有していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-192">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="c7a43-193">は `ContactIsOwnerAuthorizationHandler` コンテキストを呼び出し [ます。](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) 現在の認証済みユーザーが連絡先の所有者である場合は成功します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-193">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="c7a43-194">通常、承認ハンドラーは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="c7a43-194">Authorization handlers generally:</span></span>

* <span data-ttu-id="c7a43-195">`context.Succeed`要件が満たされた場合は、を返します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-195">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="c7a43-196">`Task.CompletedTask`要件を満たしていない場合は、を返します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-196">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="c7a43-197">`Task.CompletedTask` が成功または失敗ではない場合は &mdash; 、他の承認ハンドラーの実行を許可します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-197">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="c7a43-198">明示的に失敗する必要がある場合は、コンテキストを返し [ます。失敗](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail)。</span><span class="sxs-lookup"><span data-stu-id="c7a43-198">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="c7a43-199">アプリを使用すると、連絡先所有者は自分のデータを編集/削除/作成できます。</span><span class="sxs-lookup"><span data-stu-id="c7a43-199">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="c7a43-200">`ContactIsOwnerAuthorizationHandler` 要件パラメーターで渡された操作を確認する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="c7a43-200">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="c7a43-201">マネージャーの承認ハンドラーを作成する</span><span class="sxs-lookup"><span data-stu-id="c7a43-201">Create a manager authorization handler</span></span>

<span data-ttu-id="c7a43-202">`ContactManagerAuthorizationHandler`*承認* フォルダーにクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-202">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="c7a43-203">は、 `ContactManagerAuthorizationHandler` リソースに対して動作しているユーザーがマネージャーであることを確認します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-203">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="c7a43-204">コンテンツの変更を承認または拒否できるのは、管理者のみです (新規または変更)。</span><span class="sxs-lookup"><span data-stu-id="c7a43-204">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="c7a43-205">管理者の承認ハンドラーを作成する</span><span class="sxs-lookup"><span data-stu-id="c7a43-205">Create an administrator authorization handler</span></span>

<span data-ttu-id="c7a43-206">`ContactAdministratorsAuthorizationHandler`*承認* フォルダーにクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-206">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="c7a43-207">は、 `ContactAdministratorsAuthorizationHandler` リソースに対して動作しているユーザーが管理者であることを確認します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-207">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="c7a43-208">管理者は、すべての操作を実行できます。</span><span class="sxs-lookup"><span data-stu-id="c7a43-208">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="c7a43-209">認証ハンドラーを登録する</span><span class="sxs-lookup"><span data-stu-id="c7a43-209">Register the authorization handlers</span></span>

<span data-ttu-id="c7a43-210">Entity Framework Core を使用するサービスは、 [Addscoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions)を使用して[依存関係の挿入](xref:fundamentals/dependency-injection)に登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c7a43-210">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="c7a43-211">は、 `ContactIsOwnerAuthorizationHandler` [:::no-loc(Identity):::](xref:security/authentication/identity) Entity Framework Core 上に構築された ASP.NET Core を使用します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-211">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [:::no-loc(Identity):::](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="c7a43-212">サービスコレクションにハンドラーを登録し `ContactsController` て、 [依存関係の挿入](xref:fundamentals/dependency-injection)によってで使用できるようにします。</span><span class="sxs-lookup"><span data-stu-id="c7a43-212">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="c7a43-213">の末尾に次のコードを追加し `ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="c7a43-213">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet_defaultPolicy&highlight=23-99)]

<span data-ttu-id="c7a43-214">`ContactAdministratorsAuthorizationHandler` と `ContactManagerAuthorizationHandler` はシングルトンとして追加されます。</span><span class="sxs-lookup"><span data-stu-id="c7a43-214">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="c7a43-215">これらは EF を使用せず、必要なすべての情報がメソッドのパラメーターに含まれているため、シングルトンになり `Context` `HandleRequirementAsync` ます。</span><span class="sxs-lookup"><span data-stu-id="c7a43-215">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="c7a43-216">認証のサポート</span><span class="sxs-lookup"><span data-stu-id="c7a43-216">Support authorization</span></span>

<span data-ttu-id="c7a43-217">このセクションでは、ページを更新 :::no-loc(Razor)::: し、操作要件クラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-217">In this section, you update the :::no-loc(Razor)::: Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="c7a43-218">Contact operation の要件クラスを確認する</span><span class="sxs-lookup"><span data-stu-id="c7a43-218">Review the contact operations requirements class</span></span>

<span data-ttu-id="c7a43-219">クラスを確認 `ContactOperations` します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-219">Review the `ContactOperations` class.</span></span> <span data-ttu-id="c7a43-220">このクラスには、アプリがサポートする要件が含まれています。</span><span class="sxs-lookup"><span data-stu-id="c7a43-220">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-no-locrazor-pages"></a><span data-ttu-id="c7a43-221">連絡先ページの基本クラスを作成する :::no-loc(Razor):::</span><span class="sxs-lookup"><span data-stu-id="c7a43-221">Create a base class for the Contacts :::no-loc(Razor)::: Pages</span></span>

<span data-ttu-id="c7a43-222">[連絡先] ページで使用されるサービスを含む基本クラスを作成 :::no-loc(Razor)::: します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-222">Create a base class that contains the services used in the contacts :::no-loc(Razor)::: Pages.</span></span> <span data-ttu-id="c7a43-223">基本クラスは、初期化コードを1つの場所に配置します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-223">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="c7a43-224">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="c7a43-224">The preceding code:</span></span>

* <span data-ttu-id="c7a43-225">`IAuthorizationService`認証ハンドラーにアクセスするためのサービスを追加します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-225">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="c7a43-226">サービスを追加し :::no-loc(Identity)::: `UserManager` ます。</span><span class="sxs-lookup"><span data-stu-id="c7a43-226">Adds the :::no-loc(Identity)::: `UserManager` service.</span></span>
* <span data-ttu-id="c7a43-227">`ApplicationDbContext` を追加します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-227">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="c7a43-228">CreateModel を更新する</span><span class="sxs-lookup"><span data-stu-id="c7a43-228">Update the CreateModel</span></span>

<span data-ttu-id="c7a43-229">Create page model コンストラクターを更新して、 `DI_BasePageModel` 基本クラスを使用します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-229">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="c7a43-230">`CreateModel.OnPostAsync`メソッドを次のように更新します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-230">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="c7a43-231">モデルにユーザー ID を追加し `Contact` ます。</span><span class="sxs-lookup"><span data-stu-id="c7a43-231">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="c7a43-232">承認ハンドラーを呼び出して、ユーザーが連絡先を作成するアクセス許可を持っていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-232">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="c7a43-233">IndexModel を更新する</span><span class="sxs-lookup"><span data-stu-id="c7a43-233">Update the IndexModel</span></span>

<span data-ttu-id="c7a43-234">`OnGetAsync`承認された連絡先だけが一般ユーザーに表示されるように、メソッドを更新します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-234">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="c7a43-235">EditModel を更新する</span><span class="sxs-lookup"><span data-stu-id="c7a43-235">Update the EditModel</span></span>

<span data-ttu-id="c7a43-236">ユーザーが連絡先を所有していることを確認するための承認ハンドラーを追加します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-236">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="c7a43-237">リソース承認が検証されているため、 `[Authorize]` 属性では不十分です。</span><span class="sxs-lookup"><span data-stu-id="c7a43-237">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="c7a43-238">属性が評価された場合、アプリにはリソースへのアクセス権がありません。</span><span class="sxs-lookup"><span data-stu-id="c7a43-238">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="c7a43-239">リソースベースの承認は、必須である必要があります。</span><span class="sxs-lookup"><span data-stu-id="c7a43-239">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="c7a43-240">アプリケーションがリソースにアクセスできるようになったら、そのアプリをページモデルに読み込むか、ハンドラー自体に読み込むことによって、チェックを実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c7a43-240">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="c7a43-241">リソースキーを渡すことによって、リソースに頻繁にアクセスします。</span><span class="sxs-lookup"><span data-stu-id="c7a43-241">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="c7a43-242">DeleteModel を更新する</span><span class="sxs-lookup"><span data-stu-id="c7a43-242">Update the DeleteModel</span></span>

<span data-ttu-id="c7a43-243">承認ハンドラーを使用するように delete ページモデルを更新して、ユーザーが連絡先に対する delete 権限を持っていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-243">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="c7a43-244">ビューに承認サービスを挿入する</span><span class="sxs-lookup"><span data-stu-id="c7a43-244">Inject the authorization service into the views</span></span>

<span data-ttu-id="c7a43-245">現在、UI には、ユーザーが変更できない連絡先の編集と削除のリンクが表示されています。</span><span class="sxs-lookup"><span data-stu-id="c7a43-245">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="c7a43-246">*ページ/_ViewImports* ファイルに承認サービスを挿入して、すべてのビューで使用できるようにします。</span><span class="sxs-lookup"><span data-stu-id="c7a43-246">Inject the authorization service in the *Pages/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="c7a43-247">前のマークアップは、いくつかのステートメントを追加し `using` ます。</span><span class="sxs-lookup"><span data-stu-id="c7a43-247">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="c7a43-248">*Pages/Contacts/Index. cshtml* の **編集** と **削除** のリンクを更新して、適切なアクセス許可を持つユーザーにのみ表示されるようにします。</span><span class="sxs-lookup"><span data-stu-id="c7a43-248">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="c7a43-249">データを変更するアクセス許可がないユーザーからのリンクを非表示にしても、アプリはセキュリティで保護されません。</span><span class="sxs-lookup"><span data-stu-id="c7a43-249">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="c7a43-250">リンクを非表示にすると、有効なリンクのみが表示されるため、アプリのユーザーがわかりやすくなります。</span><span class="sxs-lookup"><span data-stu-id="c7a43-250">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="c7a43-251">ユーザーは、生成された Url をハッキングして、所有していないデータに対する編集操作と削除操作を呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="c7a43-251">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="c7a43-252">:::no-loc(Razor):::ページまたはコントローラーは、データをセキュリティで保護するためにアクセスチェックを強制する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c7a43-252">The :::no-loc(Razor)::: Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="c7a43-253">更新プログラムの詳細</span><span class="sxs-lookup"><span data-stu-id="c7a43-253">Update Details</span></span>

<span data-ttu-id="c7a43-254">上司が連絡先を承認または拒否できるように、詳細ビューを更新します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-254">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="c7a43-255">詳細ページモデルを更新します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-255">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="c7a43-256">ロールに対するユーザーの追加または削除</span><span class="sxs-lookup"><span data-stu-id="c7a43-256">Add or remove a user to a role</span></span>

<span data-ttu-id="c7a43-257">次の情報については、 [この問題](https://github.com/dotnet/AspNetCore.Docs/issues/8502) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c7a43-257">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="c7a43-258">ユーザーから特権を削除しています。</span><span class="sxs-lookup"><span data-stu-id="c7a43-258">Removing privileges from a user.</span></span> <span data-ttu-id="c7a43-259">たとえば、チャットアプリでユーザーのミュートを行います。</span><span class="sxs-lookup"><span data-stu-id="c7a43-259">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="c7a43-260">ユーザーへの特権の追加。</span><span class="sxs-lookup"><span data-stu-id="c7a43-260">Adding privileges to a user.</span></span>

<a name="challenge"></a>

## <a name="differences-between-challenge-and-forbid"></a><span data-ttu-id="c7a43-261">チャレンジと禁止の違い</span><span class="sxs-lookup"><span data-stu-id="c7a43-261">Differences between Challenge and Forbid</span></span>

<span data-ttu-id="c7a43-262">このアプリでは、認証された [ユーザーを要求](#rau)する既定のポリシーを設定します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-262">This app sets the default policy to [require authenticated users](#rau).</span></span> <span data-ttu-id="c7a43-263">次のコードでは、匿名ユーザーを許可します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-263">The following code allows anonymous users.</span></span> <span data-ttu-id="c7a43-264">匿名ユーザーは、チャレンジと禁止の違いを示すことができます。</span><span class="sxs-lookup"><span data-stu-id="c7a43-264">Anonymous users are allowed to show the differences between Challenge vs Forbid.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details2.cshtml.cs?name=snippet)]

<span data-ttu-id="c7a43-265">上記のコードにより、次のことが行われます。</span><span class="sxs-lookup"><span data-stu-id="c7a43-265">In the preceding code:</span></span>

* <span data-ttu-id="c7a43-266">ユーザーが認証 **されていない** 場合は、 `ChallengeResult` が返されます。</span><span class="sxs-lookup"><span data-stu-id="c7a43-266">When the user is **not** authenticated, a `ChallengeResult` is returned.</span></span> <span data-ttu-id="c7a43-267">が返されると、 `ChallengeResult` ユーザーはサインインページにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="c7a43-267">When a `ChallengeResult` is returned, the user is redirected to the sign-in page.</span></span>
* <span data-ttu-id="c7a43-268">ユーザーが認証されているが承認されていない場合は、 `ForbidResult` が返されます。</span><span class="sxs-lookup"><span data-stu-id="c7a43-268">When the user is authenticated, but not authorized, a `ForbidResult` is returned.</span></span> <span data-ttu-id="c7a43-269">が返されると、ユーザーは [ `ForbidResult` アクセス拒否] ページにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="c7a43-269">When a `ForbidResult` is returned, the user is redirected to the access denied page.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="c7a43-270">完成したアプリをテストする</span><span class="sxs-lookup"><span data-stu-id="c7a43-270">Test the completed app</span></span>

<span data-ttu-id="c7a43-271">シードされたユーザーアカウントのパスワードをまだ設定していない場合は、 [シークレットマネージャーツール](xref:security/app-secrets#secret-manager) を使用してパスワードを設定します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-271">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="c7a43-272">強力なパスワードを選択する: 8 個以上の文字と、少なくとも1つの大文字、数字、および記号を使用します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-272">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="c7a43-273">たとえば、は `Passw0rd!` 強力なパスワードの要件を満たしています。</span><span class="sxs-lookup"><span data-stu-id="c7a43-273">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="c7a43-274">プロジェクトのフォルダーから次のコマンドを実行します。ここで、 `<PW>` はパスワードです。</span><span class="sxs-lookup"><span data-stu-id="c7a43-274">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

<span data-ttu-id="c7a43-275">アプリに連絡先がある場合:</span><span class="sxs-lookup"><span data-stu-id="c7a43-275">If the app has contacts:</span></span>

* <span data-ttu-id="c7a43-276">テーブル内のすべてのレコードを削除 `Contact` します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-276">Delete all of the records in the `Contact` table.</span></span>
* <span data-ttu-id="c7a43-277">アプリケーションを再起動してデータベースをシード処理します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-277">Restart the app to seed the database.</span></span>

<span data-ttu-id="c7a43-278">完成したアプリをテストする簡単な方法は、3つの異なるブラウザー (または incognito/InPrivate セッション) を起動することです。</span><span class="sxs-lookup"><span data-stu-id="c7a43-278">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="c7a43-279">1つのブラウザーで、新しいユーザーを登録します (たとえば、 `test@example.com` )。</span><span class="sxs-lookup"><span data-stu-id="c7a43-279">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="c7a43-280">別のユーザーを使用して各ブラウザーにサインインします。</span><span class="sxs-lookup"><span data-stu-id="c7a43-280">Sign in to each browser with a different user.</span></span> <span data-ttu-id="c7a43-281">次の操作を確認します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-281">Verify the following operations:</span></span>

* <span data-ttu-id="c7a43-282">登録済みのユーザーは、すべての承認済み連絡先データを表示できます。</span><span class="sxs-lookup"><span data-stu-id="c7a43-282">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="c7a43-283">登録されているユーザーは、自分のデータを編集または削除できます。</span><span class="sxs-lookup"><span data-stu-id="c7a43-283">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="c7a43-284">マネージャーは、連絡先データを承認/拒否することができます。</span><span class="sxs-lookup"><span data-stu-id="c7a43-284">Managers can approve/reject contact data.</span></span> <span data-ttu-id="c7a43-285">このビューには、 `Details` [ **承認** ] ボタンと [ **却下** ] ボタンが表示されます。</span><span class="sxs-lookup"><span data-stu-id="c7a43-285">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="c7a43-286">管理者は、すべてのデータを承認/拒否し、編集/削除することができます。</span><span class="sxs-lookup"><span data-stu-id="c7a43-286">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="c7a43-287">User</span><span class="sxs-lookup"><span data-stu-id="c7a43-287">User</span></span>                | <span data-ttu-id="c7a43-288">アプリによるシード処理</span><span class="sxs-lookup"><span data-stu-id="c7a43-288">Seeded by the app</span></span> | <span data-ttu-id="c7a43-289">オプション</span><span class="sxs-lookup"><span data-stu-id="c7a43-289">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="c7a43-290">いいえ</span><span class="sxs-lookup"><span data-stu-id="c7a43-290">No</span></span>                | <span data-ttu-id="c7a43-291">独自のデータを編集または削除します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-291">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="c7a43-292">はい</span><span class="sxs-lookup"><span data-stu-id="c7a43-292">Yes</span></span>               | <span data-ttu-id="c7a43-293">自分のデータを承認/拒否し、編集/削除します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-293">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="c7a43-294">はい</span><span class="sxs-lookup"><span data-stu-id="c7a43-294">Yes</span></span>               | <span data-ttu-id="c7a43-295">すべてのデータを承認/拒否し、編集/削除します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-295">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="c7a43-296">管理者のブラウザーで連絡先を作成します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-296">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="c7a43-297">管理者の連絡先から削除と編集のための URL をコピーします。</span><span class="sxs-lookup"><span data-stu-id="c7a43-297">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="c7a43-298">これらのリンクをテストユーザーのブラウザーに貼り付けて、テストユーザーがこれらの操作を実行できないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-298">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="c7a43-299">スターターアプリを作成する</span><span class="sxs-lookup"><span data-stu-id="c7a43-299">Create the starter app</span></span>

* <span data-ttu-id="c7a43-300">:::no-loc(Razor):::"ContactManager" という名前のページアプリを作成する</span><span class="sxs-lookup"><span data-stu-id="c7a43-300">Create a :::no-loc(Razor)::: Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="c7a43-301">**個々のユーザーアカウント** を使用してアプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-301">Create the app with **Individual User Accounts** .</span></span>
  * <span data-ttu-id="c7a43-302">名前空間がサンプルで使用される名前空間と一致するように、"ContactManager" という名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-302">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="c7a43-303">`-uld` SQLite ではなく LocalDB を指定します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-303">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="c7a43-304">*モデル/Contact を追加します。*</span><span class="sxs-lookup"><span data-stu-id="c7a43-304">Add *Models/Contact.cs* :</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="c7a43-305">モデルをスキャフォールディング `Contact` します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-305">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="c7a43-306">初期移行を作成し、データベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-306">Create initial migration and update the database:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
dotnet ef database drop -f
dotnet ef migrations add initial
dotnet ef database update
```

<span data-ttu-id="c7a43-307">コマンドでバグが発生した場合は `dotnet aspnet-codegenerator razorpage` 、 [GitHub の問題](https://github.com/aspnet/Scaffolding/issues/984)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c7a43-307">If you experience a bug with the `dotnet aspnet-codegenerator razorpage` command, see [this GitHub issue](https://github.com/aspnet/Scaffolding/issues/984).</span></span>

* <span data-ttu-id="c7a43-308">*Pages/Shared/_Layout cshtml* ファイルで **contactmanager** アンカーを更新します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-308">Update the **ContactManager** anchor in the *Pages/Shared/_Layout.cshtml* file:</span></span>

 ```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Contacts/Index">ContactManager</a>
  ```

* <span data-ttu-id="c7a43-309">連絡先の作成、編集、および削除によるアプリのテスト</span><span class="sxs-lookup"><span data-stu-id="c7a43-309">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="c7a43-310">データベースのシード</span><span class="sxs-lookup"><span data-stu-id="c7a43-310">Seed the database</span></span>

<span data-ttu-id="c7a43-311">[SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs)クラスを *Data* フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-311">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) class to the *Data* folder:</span></span>

[!code-csharp[](secure-data/samples/starter3/Data/SeedData.cs)]

<span data-ttu-id="c7a43-312">呼び出し `SeedData.Initialize` 元 `Main` :</span><span class="sxs-lookup"><span data-stu-id="c7a43-312">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter3/Program.cs)]

<span data-ttu-id="c7a43-313">アプリによってデータベースがシード処理されたことをテストします。</span><span class="sxs-lookup"><span data-stu-id="c7a43-313">Test that the app seeded the database.</span></span> <span data-ttu-id="c7a43-314">Contact DB に行がある場合、seed メソッドは実行されません。</span><span class="sxs-lookup"><span data-stu-id="c7a43-314">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

<span data-ttu-id="c7a43-315">このチュートリアルでは、承認によって保護されたユーザーデータを使用して ASP.NET Core web アプリを作成する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-315">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="c7a43-316">認証済み (登録済み) のユーザーが作成した連絡先の一覧が表示されます。</span><span class="sxs-lookup"><span data-stu-id="c7a43-316">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="c7a43-317">セキュリティグループには次の3つがあります。</span><span class="sxs-lookup"><span data-stu-id="c7a43-317">There are three security groups:</span></span>

* <span data-ttu-id="c7a43-318">**登録されているユーザー** は、すべての承認済みデータを表示したり、自分のデータを編集または削除したりできます。</span><span class="sxs-lookup"><span data-stu-id="c7a43-318">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="c7a43-319">**管理者** は、連絡先データを承認または拒否できます。</span><span class="sxs-lookup"><span data-stu-id="c7a43-319">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="c7a43-320">承認された連絡先だけがユーザーに表示されます。</span><span class="sxs-lookup"><span data-stu-id="c7a43-320">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="c7a43-321">**管理者** は、任意のデータを承認/拒否したり、編集/削除したりできます。</span><span class="sxs-lookup"><span data-stu-id="c7a43-321">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="c7a43-322">次の図では、user Rick ( `rick@example.com` ) がサインインしています。</span><span class="sxs-lookup"><span data-stu-id="c7a43-322">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="c7a43-323">Rick は、承認された連絡先を表示したり、[削除] を **編集** したりするだけで、 / **Delete** / 連絡先の新しいリンクを **作成** できます。</span><span class="sxs-lookup"><span data-stu-id="c7a43-323">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="c7a43-324">Rick によって作成された最後のレコードにのみ、 **編集** および **削除** のリンクが表示されます。</span><span class="sxs-lookup"><span data-stu-id="c7a43-324">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="c7a43-325">他のユーザーには、マネージャーまたは管理者が状態を "承認済み" に変更するまで、最後のレコードは表示されません。</span><span class="sxs-lookup"><span data-stu-id="c7a43-325">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Rick がサインインしていることを示すスクリーンショット](secure-data/_static/rick.png)

<span data-ttu-id="c7a43-327">次の図で `manager@contoso.com` は、がサインインし、マネージャーのロールに含まれています。</span><span class="sxs-lookup"><span data-stu-id="c7a43-327">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![サインインしたことを示すスクリーンショット manager@contoso.com](secure-data/_static/manager1.png)

<span data-ttu-id="c7a43-329">次の図は、連絡先のマネージャーの詳細ビューを示しています。</span><span class="sxs-lookup"><span data-stu-id="c7a43-329">The following image shows the managers details view of a contact:</span></span>

![連絡先のマネージャーの表示](secure-data/_static/manager.png)

<span data-ttu-id="c7a43-331">[ **承認** ] ボタンと [ **却下** ] ボタンは、マネージャーと管理者に対してのみ表示されます。</span><span class="sxs-lookup"><span data-stu-id="c7a43-331">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="c7a43-332">次の図で `admin@contoso.com` は、がサインインし、管理者のロールに含まれています。</span><span class="sxs-lookup"><span data-stu-id="c7a43-332">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![サインインしたことを示すスクリーンショット admin@contoso.com](secure-data/_static/admin.png)

<span data-ttu-id="c7a43-334">管理者には、すべての特権があります。</span><span class="sxs-lookup"><span data-stu-id="c7a43-334">The administrator has all privileges.</span></span> <span data-ttu-id="c7a43-335">連絡先の読み取り、編集、削除、および連絡先の状態の変更を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="c7a43-335">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="c7a43-336">アプリは、次のモデルを [スキャフォールディング](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) することによって作成されました `Contact` 。</span><span class="sxs-lookup"><span data-stu-id="c7a43-336">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="c7a43-337">このサンプルには、次の承認ハンドラーが含まれています。</span><span class="sxs-lookup"><span data-stu-id="c7a43-337">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="c7a43-338">`ContactIsOwnerAuthorizationHandler`: ユーザーがデータを編集できるようにします。</span><span class="sxs-lookup"><span data-stu-id="c7a43-338">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="c7a43-339">`ContactManagerAuthorizationHandler`: 管理者が連絡先を承認または拒否できるようにします。</span><span class="sxs-lookup"><span data-stu-id="c7a43-339">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="c7a43-340">`ContactAdministratorsAuthorizationHandler`: 管理者は、連絡先を承認または拒否したり、連絡先を編集または削除したりできます。</span><span class="sxs-lookup"><span data-stu-id="c7a43-340">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="c7a43-341">[前提条件]</span><span class="sxs-lookup"><span data-stu-id="c7a43-341">Prerequisites</span></span>

<span data-ttu-id="c7a43-342">このチュートリアルは高度です。</span><span class="sxs-lookup"><span data-stu-id="c7a43-342">This tutorial is advanced.</span></span> <span data-ttu-id="c7a43-343">次のことを理解している必要があります。</span><span class="sxs-lookup"><span data-stu-id="c7a43-343">You should be familiar with:</span></span>

* [<span data-ttu-id="c7a43-344">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c7a43-344">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="c7a43-345">認証</span><span class="sxs-lookup"><span data-stu-id="c7a43-345">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="c7a43-346">アカウントの確認とパスワードの回復</span><span class="sxs-lookup"><span data-stu-id="c7a43-346">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="c7a43-347">承認</span><span class="sxs-lookup"><span data-stu-id="c7a43-347">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="c7a43-348">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="c7a43-348">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="c7a43-349">スターターおよび完成したアプリ</span><span class="sxs-lookup"><span data-stu-id="c7a43-349">The starter and completed app</span></span>

<span data-ttu-id="c7a43-350">完成し[た](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples)アプリを[ダウンロード](xref:index#how-to-download-a-sample)します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-350">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="c7a43-351">完成したアプリを[テスト](#test-the-completed-app)して、セキュリティ機能に慣れるようにします。</span><span class="sxs-lookup"><span data-stu-id="c7a43-351">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="c7a43-352">スターターアプリ</span><span class="sxs-lookup"><span data-stu-id="c7a43-352">The starter app</span></span>

<span data-ttu-id="c7a43-353">[スターター](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/)アプリを[ダウンロード](xref:index#how-to-download-a-sample)します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-353">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="c7a43-354">アプリを実行し、[ **Contactmanager** ] リンクをタップして、連絡先を作成、編集、および削除できることを確認します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-354">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="c7a43-355">ユーザーデータのセキュリティ保護</span><span class="sxs-lookup"><span data-stu-id="c7a43-355">Secure user data</span></span>

<span data-ttu-id="c7a43-356">以下のセクションでは、セキュリティで保護されたユーザーデータアプリを作成するための主要な手順について説明します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-356">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="c7a43-357">完成したプロジェクトを参照した方が便利な場合があります。</span><span class="sxs-lookup"><span data-stu-id="c7a43-357">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="c7a43-358">連絡先データをユーザーに関連付ける</span><span class="sxs-lookup"><span data-stu-id="c7a43-358">Tie the contact data to the user</span></span>

<span data-ttu-id="c7a43-359">ASP.NET ユーザー ID を使用すると、 [:::no-loc(Identity):::](xref:security/authentication/identity) ユーザーがデータを編集できるようになりますが、他のユーザーデータは編集できません。</span><span class="sxs-lookup"><span data-stu-id="c7a43-359">Use the ASP.NET [:::no-loc(Identity):::](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="c7a43-360">`OwnerID` `ContactStatus` モデルにおよびを追加し `Contact` ます。</span><span class="sxs-lookup"><span data-stu-id="c7a43-360">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="c7a43-361">`OwnerID` データベース内のテーブルのユーザー ID を示し `AspNetUser` [:::no-loc(Identity):::](xref:security/authentication/identity) ます。</span><span class="sxs-lookup"><span data-stu-id="c7a43-361">`OwnerID` is the user's ID from the `AspNetUser` table in the [:::no-loc(Identity):::](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="c7a43-362">フィールドは、 `Status` 一般的なユーザーが連絡先を表示できるかどうかを決定します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-362">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="c7a43-363">新しい移行を作成し、データベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-363">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-no-locidentity"></a><span data-ttu-id="c7a43-364">役割サービスの追加先 :::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="c7a43-364">Add Role services to :::no-loc(Identity):::</span></span>

<span data-ttu-id="c7a43-365">役割サービスを追加するには、 [Addroles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_:::no-loc(Identity):::_:::no-loc(Identity):::Builder_AddRoles__1) を追加します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-365">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_:::no-loc(Identity):::_:::no-loc(Identity):::Builder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet2&highlight=11)]

### <a name="require-authenticated-users"></a><span data-ttu-id="c7a43-366">認証されたユーザーが必要</span><span class="sxs-lookup"><span data-stu-id="c7a43-366">Require authenticated users</span></span>

<span data-ttu-id="c7a43-367">ユーザーが認証されるようにするには、既定の認証ポリシーを設定します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-367">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet&highlight=17-99)] 

 <span data-ttu-id="c7a43-368">:::no-loc(Razor):::ページ、コントローラー、またはアクションメソッドレベルで、属性を使用して認証を無効にすることができ `[AllowAnonymous]` ます。</span><span class="sxs-lookup"><span data-stu-id="c7a43-368">You can opt out of authentication at the :::no-loc(Razor)::: Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="c7a43-369">既定の認証ポリシーを設定すると、ユーザーの認証が必要になり、新しく追加されたページとコントローラーは保護され :::no-loc(Razor)::: ます。</span><span class="sxs-lookup"><span data-stu-id="c7a43-369">Setting the default authentication policy to require users to be authenticated protects newly added :::no-loc(Razor)::: Pages and controllers.</span></span> <span data-ttu-id="c7a43-370">既定で認証が必要になることは、新しいコントローラーやページを利用して属性を含めるよりも安全です :::no-loc(Razor)::: `[Authorize]` 。</span><span class="sxs-lookup"><span data-stu-id="c7a43-370">Having authentication required by default is more secure than relying on new controllers and :::no-loc(Razor)::: Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="c7a43-371">匿名ユーザーが登録前にサイトに関する情報を取得できるように、 [Allowanonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) を Index、About、および Contact の各ページに追加します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-371">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index, About, and Contact pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Index.cshtml.cs?highlight=1,6)]

### <a name="configure-the-test-account"></a><span data-ttu-id="c7a43-372">テストアカウントを構成する</span><span class="sxs-lookup"><span data-stu-id="c7a43-372">Configure the test account</span></span>

<span data-ttu-id="c7a43-373">クラスは、 `SeedData` 管理者とマネージャーの2つのアカウントを作成します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-373">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="c7a43-374">これらのアカウントのパスワードを設定するには、 [シークレットマネージャーツール](xref:security/app-secrets) を使用します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-374">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="c7a43-375">プロジェクトディレクトリ ( *Program.cs* を含むディレクトリ) のパスワードを設定します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-375">Set the password from the project directory (the directory containing *Program.cs* ):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="c7a43-376">強力なパスワードが指定されていない場合は、が呼び出されると例外がスローされ `SeedData.Initialize` ます。</span><span class="sxs-lookup"><span data-stu-id="c7a43-376">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="c7a43-377">`Main`テストパスワードを使用するように更新します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-377">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="c7a43-378">テストアカウントを作成し、連絡先を更新する</span><span class="sxs-lookup"><span data-stu-id="c7a43-378">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="c7a43-379">クラスのメソッドを更新して、 `Initialize` `SeedData` テストアカウントを作成します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-379">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="c7a43-380">管理者のユーザー ID とを `ContactStatus` 連絡先に追加します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-380">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="c7a43-381">いずれかの連絡先を "送信済み" にして、"拒否" するようにします。</span><span class="sxs-lookup"><span data-stu-id="c7a43-381">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="c7a43-382">すべての連絡先にユーザー ID と状態を追加します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-382">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="c7a43-383">1つの連絡先のみが表示されます。</span><span class="sxs-lookup"><span data-stu-id="c7a43-383">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="c7a43-384">所有者、マネージャー、および管理者の承認ハンドラーを作成する</span><span class="sxs-lookup"><span data-stu-id="c7a43-384">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="c7a43-385">*承認* フォルダーを作成し、 `ContactIsOwnerAuthorizationHandler` そこにクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-385">Create an *Authorization* folder and create a `ContactIsOwnerAuthorizationHandler` class in it.</span></span> <span data-ttu-id="c7a43-386">は、 `ContactIsOwnerAuthorizationHandler` リソースに対して動作しているユーザーがリソースを所有していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-386">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="c7a43-387">は `ContactIsOwnerAuthorizationHandler` コンテキストを呼び出し [ます。](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) 現在の認証済みユーザーが連絡先の所有者である場合は成功します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-387">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="c7a43-388">通常、承認ハンドラーは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="c7a43-388">Authorization handlers generally:</span></span>

* <span data-ttu-id="c7a43-389">`context.Succeed`要件が満たされた場合は、を返します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-389">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="c7a43-390">`Task.CompletedTask`要件を満たしていない場合は、を返します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-390">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="c7a43-391">`Task.CompletedTask` が成功または失敗ではない場合は &mdash; 、他の承認ハンドラーの実行を許可します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-391">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="c7a43-392">明示的に失敗する必要がある場合は、コンテキストを返し [ます。失敗](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail)。</span><span class="sxs-lookup"><span data-stu-id="c7a43-392">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="c7a43-393">アプリを使用すると、連絡先所有者は自分のデータを編集/削除/作成できます。</span><span class="sxs-lookup"><span data-stu-id="c7a43-393">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="c7a43-394">`ContactIsOwnerAuthorizationHandler` 要件パラメーターで渡された操作を確認する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="c7a43-394">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="c7a43-395">マネージャーの承認ハンドラーを作成する</span><span class="sxs-lookup"><span data-stu-id="c7a43-395">Create a manager authorization handler</span></span>

<span data-ttu-id="c7a43-396">`ContactManagerAuthorizationHandler`*承認* フォルダーにクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-396">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="c7a43-397">は、 `ContactManagerAuthorizationHandler` リソースに対して動作しているユーザーがマネージャーであることを確認します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-397">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="c7a43-398">コンテンツの変更を承認または拒否できるのは、管理者のみです (新規または変更)。</span><span class="sxs-lookup"><span data-stu-id="c7a43-398">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="c7a43-399">管理者の承認ハンドラーを作成する</span><span class="sxs-lookup"><span data-stu-id="c7a43-399">Create an administrator authorization handler</span></span>

<span data-ttu-id="c7a43-400">`ContactAdministratorsAuthorizationHandler`*承認* フォルダーにクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-400">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="c7a43-401">は、 `ContactAdministratorsAuthorizationHandler` リソースに対して動作しているユーザーが管理者であることを確認します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-401">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="c7a43-402">管理者は、すべての操作を実行できます。</span><span class="sxs-lookup"><span data-stu-id="c7a43-402">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="c7a43-403">認証ハンドラーを登録する</span><span class="sxs-lookup"><span data-stu-id="c7a43-403">Register the authorization handlers</span></span>

<span data-ttu-id="c7a43-404">Entity Framework Core を使用するサービスは、 [Addscoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions)を使用して[依存関係の挿入](xref:fundamentals/dependency-injection)に登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c7a43-404">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="c7a43-405">は、 `ContactIsOwnerAuthorizationHandler` [:::no-loc(Identity):::](xref:security/authentication/identity) Entity Framework Core 上に構築された ASP.NET Core を使用します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-405">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [:::no-loc(Identity):::](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="c7a43-406">サービスコレクションにハンドラーを登録し `ContactsController` て、 [依存関係の挿入](xref:fundamentals/dependency-injection)によってで使用できるようにします。</span><span class="sxs-lookup"><span data-stu-id="c7a43-406">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="c7a43-407">の末尾に次のコードを追加し `ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="c7a43-407">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

<span data-ttu-id="c7a43-408">`ContactAdministratorsAuthorizationHandler` と `ContactManagerAuthorizationHandler` はシングルトンとして追加されます。</span><span class="sxs-lookup"><span data-stu-id="c7a43-408">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="c7a43-409">これらは EF を使用せず、必要なすべての情報がメソッドのパラメーターに含まれているため、シングルトンになり `Context` `HandleRequirementAsync` ます。</span><span class="sxs-lookup"><span data-stu-id="c7a43-409">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="c7a43-410">認証のサポート</span><span class="sxs-lookup"><span data-stu-id="c7a43-410">Support authorization</span></span>

<span data-ttu-id="c7a43-411">このセクションでは、ページを更新 :::no-loc(Razor)::: し、操作要件クラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-411">In this section, you update the :::no-loc(Razor)::: Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="c7a43-412">Contact operation の要件クラスを確認する</span><span class="sxs-lookup"><span data-stu-id="c7a43-412">Review the contact operations requirements class</span></span>

<span data-ttu-id="c7a43-413">クラスを確認 `ContactOperations` します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-413">Review the `ContactOperations` class.</span></span> <span data-ttu-id="c7a43-414">このクラスには、アプリがサポートする要件が含まれています。</span><span class="sxs-lookup"><span data-stu-id="c7a43-414">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-no-locrazor-pages"></a><span data-ttu-id="c7a43-415">連絡先ページの基本クラスを作成する :::no-loc(Razor):::</span><span class="sxs-lookup"><span data-stu-id="c7a43-415">Create a base class for the Contacts :::no-loc(Razor)::: Pages</span></span>

<span data-ttu-id="c7a43-416">[連絡先] ページで使用されるサービスを含む基本クラスを作成 :::no-loc(Razor)::: します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-416">Create a base class that contains the services used in the contacts :::no-loc(Razor)::: Pages.</span></span> <span data-ttu-id="c7a43-417">基本クラスは、初期化コードを1つの場所に配置します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-417">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="c7a43-418">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="c7a43-418">The preceding code:</span></span>

* <span data-ttu-id="c7a43-419">`IAuthorizationService`認証ハンドラーにアクセスするためのサービスを追加します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-419">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="c7a43-420">サービスを追加し :::no-loc(Identity)::: `UserManager` ます。</span><span class="sxs-lookup"><span data-stu-id="c7a43-420">Adds the :::no-loc(Identity)::: `UserManager` service.</span></span>
* <span data-ttu-id="c7a43-421">`ApplicationDbContext` を追加します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-421">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="c7a43-422">CreateModel を更新する</span><span class="sxs-lookup"><span data-stu-id="c7a43-422">Update the CreateModel</span></span>

<span data-ttu-id="c7a43-423">Create page model コンストラクターを更新して、 `DI_BasePageModel` 基本クラスを使用します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-423">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="c7a43-424">`CreateModel.OnPostAsync`メソッドを次のように更新します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-424">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="c7a43-425">モデルにユーザー ID を追加し `Contact` ます。</span><span class="sxs-lookup"><span data-stu-id="c7a43-425">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="c7a43-426">承認ハンドラーを呼び出して、ユーザーが連絡先を作成するアクセス許可を持っていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-426">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="c7a43-427">IndexModel を更新する</span><span class="sxs-lookup"><span data-stu-id="c7a43-427">Update the IndexModel</span></span>

<span data-ttu-id="c7a43-428">`OnGetAsync`承認された連絡先だけが一般ユーザーに表示されるように、メソッドを更新します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-428">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="c7a43-429">EditModel を更新する</span><span class="sxs-lookup"><span data-stu-id="c7a43-429">Update the EditModel</span></span>

<span data-ttu-id="c7a43-430">ユーザーが連絡先を所有していることを確認するための承認ハンドラーを追加します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-430">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="c7a43-431">リソース承認が検証されているため、 `[Authorize]` 属性では不十分です。</span><span class="sxs-lookup"><span data-stu-id="c7a43-431">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="c7a43-432">属性が評価された場合、アプリにはリソースへのアクセス権がありません。</span><span class="sxs-lookup"><span data-stu-id="c7a43-432">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="c7a43-433">リソースベースの承認は、必須である必要があります。</span><span class="sxs-lookup"><span data-stu-id="c7a43-433">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="c7a43-434">アプリケーションがリソースにアクセスできるようになったら、そのアプリをページモデルに読み込むか、ハンドラー自体に読み込むことによって、チェックを実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c7a43-434">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="c7a43-435">リソースキーを渡すことによって、リソースに頻繁にアクセスします。</span><span class="sxs-lookup"><span data-stu-id="c7a43-435">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="c7a43-436">DeleteModel を更新する</span><span class="sxs-lookup"><span data-stu-id="c7a43-436">Update the DeleteModel</span></span>

<span data-ttu-id="c7a43-437">承認ハンドラーを使用するように delete ページモデルを更新して、ユーザーが連絡先に対する delete 権限を持っていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-437">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="c7a43-438">ビューに承認サービスを挿入する</span><span class="sxs-lookup"><span data-stu-id="c7a43-438">Inject the authorization service into the views</span></span>

<span data-ttu-id="c7a43-439">現在、UI には、ユーザーが変更できない連絡先の編集と削除のリンクが表示されています。</span><span class="sxs-lookup"><span data-stu-id="c7a43-439">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="c7a43-440">すべてのビューで使用できるように、 *views/_ViewImports cshtml* ファイルに承認サービスを挿入します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-440">Inject the authorization service in the *Views/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="c7a43-441">前のマークアップは、いくつかのステートメントを追加し `using` ます。</span><span class="sxs-lookup"><span data-stu-id="c7a43-441">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="c7a43-442">*Pages/Contacts/Index. cshtml* の **編集** と **削除** のリンクを更新して、適切なアクセス許可を持つユーザーにのみ表示されるようにします。</span><span class="sxs-lookup"><span data-stu-id="c7a43-442">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="c7a43-443">データを変更するアクセス許可がないユーザーからのリンクを非表示にしても、アプリはセキュリティで保護されません。</span><span class="sxs-lookup"><span data-stu-id="c7a43-443">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="c7a43-444">リンクを非表示にすると、有効なリンクのみが表示されるため、アプリのユーザーがわかりやすくなります。</span><span class="sxs-lookup"><span data-stu-id="c7a43-444">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="c7a43-445">ユーザーは、生成された Url をハッキングして、所有していないデータに対する編集操作と削除操作を呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="c7a43-445">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="c7a43-446">:::no-loc(Razor):::ページまたはコントローラーは、データをセキュリティで保護するためにアクセスチェックを強制する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c7a43-446">The :::no-loc(Razor)::: Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="c7a43-447">更新プログラムの詳細</span><span class="sxs-lookup"><span data-stu-id="c7a43-447">Update Details</span></span>

<span data-ttu-id="c7a43-448">上司が連絡先を承認または拒否できるように、詳細ビューを更新します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-448">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="c7a43-449">詳細ページモデルを更新します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-449">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="c7a43-450">ロールに対するユーザーの追加または削除</span><span class="sxs-lookup"><span data-stu-id="c7a43-450">Add or remove a user to a role</span></span>

<span data-ttu-id="c7a43-451">次の情報については、 [この問題](https://github.com/dotnet/AspNetCore.Docs/issues/8502) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c7a43-451">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="c7a43-452">ユーザーから特権を削除しています。</span><span class="sxs-lookup"><span data-stu-id="c7a43-452">Removing privileges from a user.</span></span> <span data-ttu-id="c7a43-453">たとえば、チャットアプリでユーザーのミュートを行います。</span><span class="sxs-lookup"><span data-stu-id="c7a43-453">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="c7a43-454">ユーザーへの特権の追加。</span><span class="sxs-lookup"><span data-stu-id="c7a43-454">Adding privileges to a user.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="c7a43-455">完成したアプリをテストする</span><span class="sxs-lookup"><span data-stu-id="c7a43-455">Test the completed app</span></span>

<span data-ttu-id="c7a43-456">シードされたユーザーアカウントのパスワードをまだ設定していない場合は、 [シークレットマネージャーツール](xref:security/app-secrets#secret-manager) を使用してパスワードを設定します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-456">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="c7a43-457">強力なパスワードを選択する: 8 個以上の文字と、少なくとも1つの大文字、数字、および記号を使用します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-457">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="c7a43-458">たとえば、は `Passw0rd!` 強力なパスワードの要件を満たしています。</span><span class="sxs-lookup"><span data-stu-id="c7a43-458">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="c7a43-459">プロジェクトのフォルダーから次のコマンドを実行します。ここで、 `<PW>` はパスワードです。</span><span class="sxs-lookup"><span data-stu-id="c7a43-459">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

* <span data-ttu-id="c7a43-460">データベースを削除して更新する</span><span class="sxs-lookup"><span data-stu-id="c7a43-460">Drop and update the Database</span></span>

  ```dotnetcli
  dotnet ef database drop -f
  dotnet ef database update  
  ```

* <span data-ttu-id="c7a43-461">アプリケーションを再起動してデータベースをシード処理します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-461">Restart the app to seed the database.</span></span>

<span data-ttu-id="c7a43-462">完成したアプリをテストする簡単な方法は、3つの異なるブラウザー (または incognito/InPrivate セッション) を起動することです。</span><span class="sxs-lookup"><span data-stu-id="c7a43-462">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="c7a43-463">1つのブラウザーで、新しいユーザーを登録します (たとえば、 `test@example.com` )。</span><span class="sxs-lookup"><span data-stu-id="c7a43-463">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="c7a43-464">別のユーザーを使用して各ブラウザーにサインインします。</span><span class="sxs-lookup"><span data-stu-id="c7a43-464">Sign in to each browser with a different user.</span></span> <span data-ttu-id="c7a43-465">次の操作を確認します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-465">Verify the following operations:</span></span>

* <span data-ttu-id="c7a43-466">登録済みのユーザーは、すべての承認済み連絡先データを表示できます。</span><span class="sxs-lookup"><span data-stu-id="c7a43-466">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="c7a43-467">登録されているユーザーは、自分のデータを編集または削除できます。</span><span class="sxs-lookup"><span data-stu-id="c7a43-467">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="c7a43-468">マネージャーは、連絡先データを承認/拒否することができます。</span><span class="sxs-lookup"><span data-stu-id="c7a43-468">Managers can approve/reject contact data.</span></span> <span data-ttu-id="c7a43-469">このビューには、 `Details` [ **承認** ] ボタンと [ **却下** ] ボタンが表示されます。</span><span class="sxs-lookup"><span data-stu-id="c7a43-469">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="c7a43-470">管理者は、すべてのデータを承認/拒否し、編集/削除することができます。</span><span class="sxs-lookup"><span data-stu-id="c7a43-470">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="c7a43-471">User</span><span class="sxs-lookup"><span data-stu-id="c7a43-471">User</span></span>                | <span data-ttu-id="c7a43-472">アプリによるシード処理</span><span class="sxs-lookup"><span data-stu-id="c7a43-472">Seeded by the app</span></span> | <span data-ttu-id="c7a43-473">オプション</span><span class="sxs-lookup"><span data-stu-id="c7a43-473">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="c7a43-474">いいえ</span><span class="sxs-lookup"><span data-stu-id="c7a43-474">No</span></span>                | <span data-ttu-id="c7a43-475">独自のデータを編集または削除します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-475">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="c7a43-476">はい</span><span class="sxs-lookup"><span data-stu-id="c7a43-476">Yes</span></span>               | <span data-ttu-id="c7a43-477">自分のデータを承認/拒否し、編集/削除します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-477">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="c7a43-478">はい</span><span class="sxs-lookup"><span data-stu-id="c7a43-478">Yes</span></span>               | <span data-ttu-id="c7a43-479">すべてのデータを承認/拒否し、編集/削除します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-479">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="c7a43-480">管理者のブラウザーで連絡先を作成します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-480">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="c7a43-481">管理者の連絡先から削除と編集のための URL をコピーします。</span><span class="sxs-lookup"><span data-stu-id="c7a43-481">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="c7a43-482">これらのリンクをテストユーザーのブラウザーに貼り付けて、テストユーザーがこれらの操作を実行できないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-482">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="c7a43-483">スターターアプリを作成する</span><span class="sxs-lookup"><span data-stu-id="c7a43-483">Create the starter app</span></span>

* <span data-ttu-id="c7a43-484">:::no-loc(Razor):::"ContactManager" という名前のページアプリを作成する</span><span class="sxs-lookup"><span data-stu-id="c7a43-484">Create a :::no-loc(Razor)::: Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="c7a43-485">**個々のユーザーアカウント** を使用してアプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-485">Create the app with **Individual User Accounts** .</span></span>
  * <span data-ttu-id="c7a43-486">名前空間がサンプルで使用される名前空間と一致するように、"ContactManager" という名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-486">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="c7a43-487">`-uld` SQLite ではなく LocalDB を指定します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-487">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="c7a43-488">*モデル/Contact を追加します。*</span><span class="sxs-lookup"><span data-stu-id="c7a43-488">Add *Models/Contact.cs* :</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="c7a43-489">モデルをスキャフォールディング `Contact` します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-489">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="c7a43-490">初期移行を作成し、データベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-490">Create initial migration and update the database:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
  dotnet ef database drop -f
  dotnet ef migrations add initial
  dotnet ef database update
  ```

* <span data-ttu-id="c7a43-491">*Pages/_Layout cshtml* ファイルで **contactmanager** アンカーを更新します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-491">Update the **ContactManager** anchor in the *Pages/_Layout.cshtml* file:</span></span>

  ```cshtml
  <a asp-page="/Contacts/Index" class="navbar-brand">ContactManager</a>
  ```

* <span data-ttu-id="c7a43-492">連絡先の作成、編集、および削除によるアプリのテスト</span><span class="sxs-lookup"><span data-stu-id="c7a43-492">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="c7a43-493">データベースのシード</span><span class="sxs-lookup"><span data-stu-id="c7a43-493">Seed the database</span></span>

<span data-ttu-id="c7a43-494">[SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs)クラスを *Data* フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="c7a43-494">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) class to the *Data* folder.</span></span>

<span data-ttu-id="c7a43-495">呼び出し `SeedData.Initialize` 元 `Main` :</span><span class="sxs-lookup"><span data-stu-id="c7a43-495">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

<span data-ttu-id="c7a43-496">アプリによってデータベースがシード処理されたことをテストします。</span><span class="sxs-lookup"><span data-stu-id="c7a43-496">Test that the app seeded the database.</span></span> <span data-ttu-id="c7a43-497">Contact DB に行がある場合、seed メソッドは実行されません。</span><span class="sxs-lookup"><span data-stu-id="c7a43-497">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a><span data-ttu-id="c7a43-498">その他の資料</span><span class="sxs-lookup"><span data-stu-id="c7a43-498">Additional resources</span></span>

* [<span data-ttu-id="c7a43-499">Azure App Service で .NET Core および SQL Database のアプリを作成する</span><span class="sxs-lookup"><span data-stu-id="c7a43-499">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* <span data-ttu-id="c7a43-500">[ASP.NET Core の承認ラボ](https://github.com/blowdart/AspNetAuthorizationWorkshop)。</span><span class="sxs-lookup"><span data-stu-id="c7a43-500">[ASP.NET Core Authorization Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span></span> <span data-ttu-id="c7a43-501">このチュートリアルで紹介するセキュリティ機能の詳細については、このラボを参照してください。</span><span class="sxs-lookup"><span data-stu-id="c7a43-501">This lab goes into more detail on the security features introduced in this tutorial.</span></span>
* <xref:security/authorization/introduction>
* [<span data-ttu-id="c7a43-502">カスタムポリシーベースの承認</span><span class="sxs-lookup"><span data-stu-id="c7a43-502">Custom policy-based authorization</span></span>](xref:security/authorization/policies)
