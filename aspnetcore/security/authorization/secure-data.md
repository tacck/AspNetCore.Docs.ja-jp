---
title: 承認によって保護されたユーザーデータを含む ASP.NET Core アプリを作成する
author: rick-anderson
description: 認証によって保護されたユーザーデータを使用して ASP.NET Core web アプリを作成する方法について説明します。 HTTPS、認証、セキュリティ、などが含まれ ASP.NET Core Identity ます。
ms.author: riande
ms.date: 7/18/2020
ms.custom: mvc, seodec18
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/secure-data
ms.openlocfilehash: dc70cfe7cb0c0f044f5f1e7ee68a293b3ea7507f
ms.sourcegitcommit: 04a404a9655c59ad1ea02aff5d399ae1b833ad6a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/03/2021
ms.locfileid: "97854653"
---
# <a name="create-an-aspnet-core-web-app-with-user-data-protected-by-authorization"></a><span data-ttu-id="b5dba-104">認証によって保護されたユーザーデータを使用して ASP.NET Core web アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="b5dba-104">Create an ASP.NET Core web app with user data protected by authorization</span></span>

<span data-ttu-id="b5dba-105">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT) および [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="b5dba-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="b5dba-106">[次の pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)を参照</span><span class="sxs-lookup"><span data-stu-id="b5dba-106">See [this pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b5dba-107">このチュートリアルでは、承認によって保護されたユーザーデータを使用して ASP.NET Core web アプリを作成する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-107">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="b5dba-108">認証済み (登録済み) のユーザーが作成した連絡先の一覧が表示されます。</span><span class="sxs-lookup"><span data-stu-id="b5dba-108">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="b5dba-109">セキュリティグループには次の3つがあります。</span><span class="sxs-lookup"><span data-stu-id="b5dba-109">There are three security groups:</span></span>

* <span data-ttu-id="b5dba-110">**登録されているユーザー** は、すべての承認済みデータを表示したり、自分のデータを編集または削除したりできます。</span><span class="sxs-lookup"><span data-stu-id="b5dba-110">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="b5dba-111">**管理者** は、連絡先データを承認または拒否できます。</span><span class="sxs-lookup"><span data-stu-id="b5dba-111">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="b5dba-112">承認された連絡先だけがユーザーに表示されます。</span><span class="sxs-lookup"><span data-stu-id="b5dba-112">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="b5dba-113">**管理者** は、任意のデータを承認/拒否したり、編集/削除したりできます。</span><span class="sxs-lookup"><span data-stu-id="b5dba-113">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="b5dba-114">このドキュメントの画像は、最新のテンプレートと完全には一致しません。</span><span class="sxs-lookup"><span data-stu-id="b5dba-114">The images in this document don't exactly match the latest templates.</span></span>

<span data-ttu-id="b5dba-115">次の図では、user Rick ( `rick@example.com` ) がサインインしています。</span><span class="sxs-lookup"><span data-stu-id="b5dba-115">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="b5dba-116">Rick は、承認された連絡先を表示したり、[削除] を **編集** したりするだけで、 /  / 連絡先の新しいリンクを **作成** できます。</span><span class="sxs-lookup"><span data-stu-id="b5dba-116">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="b5dba-117">Rick によって作成された最後のレコードにのみ、 **編集** および **削除** のリンクが表示されます。</span><span class="sxs-lookup"><span data-stu-id="b5dba-117">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="b5dba-118">他のユーザーには、マネージャーまたは管理者が状態を "承認済み" に変更するまで、最後のレコードは表示されません。</span><span class="sxs-lookup"><span data-stu-id="b5dba-118">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Rick がサインインしていることを示すスクリーンショット](secure-data/_static/rick.png)

<span data-ttu-id="b5dba-120">次の図で `manager@contoso.com` は、がサインインし、マネージャーのロールに含まれています。</span><span class="sxs-lookup"><span data-stu-id="b5dba-120">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![サインインしたことを示すスクリーンショット manager@contoso.com](secure-data/_static/manager1.png)

<span data-ttu-id="b5dba-122">次の図は、連絡先のマネージャーの詳細ビューを示しています。</span><span class="sxs-lookup"><span data-stu-id="b5dba-122">The following image shows the managers details view of a contact:</span></span>

![連絡先のマネージャーの表示](secure-data/_static/manager.png)

<span data-ttu-id="b5dba-124">[ **承認** ] ボタンと [ **却下** ] ボタンは、マネージャーと管理者に対してのみ表示されます。</span><span class="sxs-lookup"><span data-stu-id="b5dba-124">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="b5dba-125">次の図で `admin@contoso.com` は、がサインインし、管理者のロールに含まれています。</span><span class="sxs-lookup"><span data-stu-id="b5dba-125">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![サインインしたことを示すスクリーンショット admin@contoso.com](secure-data/_static/admin.png)

<span data-ttu-id="b5dba-127">管理者には、すべての特権があります。</span><span class="sxs-lookup"><span data-stu-id="b5dba-127">The administrator has all privileges.</span></span> <span data-ttu-id="b5dba-128">連絡先の読み取り、編集、削除、および連絡先の状態の変更を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="b5dba-128">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="b5dba-129">アプリは、次のモデルを [スキャフォールディング](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) することによって作成されました `Contact` 。</span><span class="sxs-lookup"><span data-stu-id="b5dba-129">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="b5dba-130">このサンプルには、次の承認ハンドラーが含まれています。</span><span class="sxs-lookup"><span data-stu-id="b5dba-130">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="b5dba-131">`ContactIsOwnerAuthorizationHandler`: ユーザーがデータを編集できるようにします。</span><span class="sxs-lookup"><span data-stu-id="b5dba-131">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="b5dba-132">`ContactManagerAuthorizationHandler`: 管理者が連絡先を承認または拒否できるようにします。</span><span class="sxs-lookup"><span data-stu-id="b5dba-132">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="b5dba-133">`ContactAdministratorsAuthorizationHandler`: 管理者は、連絡先を承認または拒否したり、連絡先を編集または削除したりできます。</span><span class="sxs-lookup"><span data-stu-id="b5dba-133">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="b5dba-134">前提条件</span><span class="sxs-lookup"><span data-stu-id="b5dba-134">Prerequisites</span></span>

<span data-ttu-id="b5dba-135">このチュートリアルは高度です。</span><span class="sxs-lookup"><span data-stu-id="b5dba-135">This tutorial is advanced.</span></span> <span data-ttu-id="b5dba-136">次のことを理解している必要があります。</span><span class="sxs-lookup"><span data-stu-id="b5dba-136">You should be familiar with:</span></span>

* [<span data-ttu-id="b5dba-137">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b5dba-137">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="b5dba-138">認証</span><span class="sxs-lookup"><span data-stu-id="b5dba-138">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="b5dba-139">アカウントの確認とパスワードの回復</span><span class="sxs-lookup"><span data-stu-id="b5dba-139">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="b5dba-140">承認</span><span class="sxs-lookup"><span data-stu-id="b5dba-140">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="b5dba-141">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="b5dba-141">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="b5dba-142">スターターおよび完成したアプリ</span><span class="sxs-lookup"><span data-stu-id="b5dba-142">The starter and completed app</span></span>

<span data-ttu-id="b5dba-143">完成し[た](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples)アプリを[ダウンロード](xref:index#how-to-download-a-sample)します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-143">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="b5dba-144">完成したアプリを[テスト](#test-the-completed-app)して、セキュリティ機能に慣れるようにします。</span><span class="sxs-lookup"><span data-stu-id="b5dba-144">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="b5dba-145">スターターアプリ</span><span class="sxs-lookup"><span data-stu-id="b5dba-145">The starter app</span></span>

<span data-ttu-id="b5dba-146">[スターター](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/)アプリを[ダウンロード](xref:index#how-to-download-a-sample)します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-146">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="b5dba-147">アプリを実行し、[ **Contactmanager** ] リンクをタップして、連絡先を作成、編集、および削除できることを確認します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-147">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span> <span data-ttu-id="b5dba-148">スターターアプリを作成するには、「 [スターターアプリを作成](#create-the-starter-app)する」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b5dba-148">To create the starter app, see [Create the starter app](#create-the-starter-app).</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="b5dba-149">ユーザーデータのセキュリティ保護</span><span class="sxs-lookup"><span data-stu-id="b5dba-149">Secure user data</span></span>

<span data-ttu-id="b5dba-150">以下のセクションでは、セキュリティで保護されたユーザーデータアプリを作成するための主要な手順について説明します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-150">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="b5dba-151">完成したプロジェクトを参照した方が便利な場合があります。</span><span class="sxs-lookup"><span data-stu-id="b5dba-151">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="b5dba-152">連絡先データをユーザーに関連付ける</span><span class="sxs-lookup"><span data-stu-id="b5dba-152">Tie the contact data to the user</span></span>

<span data-ttu-id="b5dba-153">ASP.NET ユーザー ID を使用すると、 [Identity](xref:security/authentication/identity) ユーザーがデータを編集できるようになりますが、他のユーザーデータは編集できません。</span><span class="sxs-lookup"><span data-stu-id="b5dba-153">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="b5dba-154">`OwnerID` `ContactStatus` モデルにおよびを追加し `Contact` ます。</span><span class="sxs-lookup"><span data-stu-id="b5dba-154">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final3/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="b5dba-155">`OwnerID` データベース内のテーブルのユーザー ID を示し `AspNetUser` [Identity](xref:security/authentication/identity) ます。</span><span class="sxs-lookup"><span data-stu-id="b5dba-155">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="b5dba-156">フィールドは、 `Status` 一般的なユーザーが連絡先を表示できるかどうかを決定します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-156">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="b5dba-157">新しい移行を作成し、データベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-157">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-no-locidentity"></a><span data-ttu-id="b5dba-158">役割サービスの追加先 Identity</span><span class="sxs-lookup"><span data-stu-id="b5dba-158">Add Role services to Identity</span></span>

<span data-ttu-id="b5dba-159">役割サービスを追加するには、 [Addroles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) を追加します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-159">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet2&highlight=9)]

<a name="rau"></a>

### <a name="require-authenticated-users"></a><span data-ttu-id="b5dba-160">認証されたユーザーが必要</span><span class="sxs-lookup"><span data-stu-id="b5dba-160">Require authenticated users</span></span>

<span data-ttu-id="b5dba-161">ユーザーが認証されるようにするには、フォールバック認証ポリシーを設定します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-161">Set the fallback authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet&highlight=13-99)]

<span data-ttu-id="b5dba-162">前の強調表示されたコードは、 [フォールバック認証ポリシー](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy)を設定します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-162">The preceding highlighted code sets the [fallback authentication policy](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy).</span></span> <span data-ttu-id="b5dba-163">フォールバック認証ポリシーでは、\*\* Razor 認証属性を持つページ、コントローラー、またはアクションメソッドを除き、\* すべての _ ユーザーを認証する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b5dba-163">The fallback authentication policy requires \**_all_* _ users to be authenticated, except for Razor Pages, controllers, or action methods with an authentication attribute.</span></span> <span data-ttu-id="b5dba-164">たとえば、 Razor ページ、コントローラー、アクションメソッド `[AllowAnonymous]` は、 `[Authorize(PolicyName="MyPolicy")]` フォールバック認証ポリシーではなく、適用された認証属性を使用します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-164">For example, Razor Pages, controllers, or action methods with `[AllowAnonymous]` or `[Authorize(PolicyName="MyPolicy")]` use the applied authentication attribute rather than the fallback authentication policy.</span></span>

<span data-ttu-id="b5dba-165">フォールバック認証ポリシー:</span><span class="sxs-lookup"><span data-stu-id="b5dba-165">The fallback authentication policy:</span></span>

<span data-ttu-id="b5dba-166">_ は、認証ポリシーを明示的に指定しないすべての要求に適用されます。</span><span class="sxs-lookup"><span data-stu-id="b5dba-166">_ Is applied to all requests that do not explicitly specify an authentication policy.</span></span> <span data-ttu-id="b5dba-167">エンドポイントのルーティングによって提供される要求の場合、これには承認属性を指定しないエンドポイントが含まれます。</span><span class="sxs-lookup"><span data-stu-id="b5dba-167">For requests served by endpoint routing, this would include any endpoint that does not specify an authorization attribute.</span></span> <span data-ttu-id="b5dba-168">[静的ファイル](xref:fundamentals/static-files)など、承認ミドルウェアの後に他のミドルウェアによって提供される要求の場合、ポリシーがすべての要求に適用されます。</span><span class="sxs-lookup"><span data-stu-id="b5dba-168">For requests served by other middleware after the authorization middleware, such as [static files](xref:fundamentals/static-files), this would apply the policy to all requests.</span></span>

<span data-ttu-id="b5dba-169">代替認証ポリシーを設定して、ユーザーが認証を要求するようにすると、新しく追加されたページとコントローラーが保護され Razor ます。</span><span class="sxs-lookup"><span data-stu-id="b5dba-169">Setting the fallback authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="b5dba-170">既定で認証が必要になることは、新しいコントローラーやページを利用して属性を含めるよりも安全です Razor `[Authorize]` 。</span><span class="sxs-lookup"><span data-stu-id="b5dba-170">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="b5dba-171">クラスには <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions> も含まれ <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.DefaultPolicy?displayProperty=nameWithType> ます。</span><span class="sxs-lookup"><span data-stu-id="b5dba-171">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions> class also contains <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.DefaultPolicy?displayProperty=nameWithType>.</span></span> <span data-ttu-id="b5dba-172">は、 `DefaultPolicy` `[Authorize]` ポリシーが指定されていない場合に、属性で使用されるポリシーです。</span><span class="sxs-lookup"><span data-stu-id="b5dba-172">The `DefaultPolicy` is the policy used with the `[Authorize]` attribute when no policy is specified.</span></span> <span data-ttu-id="b5dba-173">`[Authorize]` はと異なり、名前付きポリシーを含んでいません `[Authorize(PolicyName="MyPolicy")]` 。</span><span class="sxs-lookup"><span data-stu-id="b5dba-173">`[Authorize]` doesn't contain a named policy, unlike `[Authorize(PolicyName="MyPolicy")]`.</span></span>

<span data-ttu-id="b5dba-174">ポリシーの詳細については、「」を参照してください <xref:security/authorization/policies> 。</span><span class="sxs-lookup"><span data-stu-id="b5dba-174">For more information on policies, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="b5dba-175">すべてのユーザーの認証を要求する MVC コントローラーとページの別の方法 Razor は、承認フィルターを追加することです。</span><span class="sxs-lookup"><span data-stu-id="b5dba-175">An alternative way for MVC controllers and Razor Pages to require all users be authenticated is adding an authorization filter:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup2.cs?name=snippet&highlight=14-99)]

<span data-ttu-id="b5dba-176">上記のコードでは、承認フィルターを使用します。フォールバックポリシーを設定するには、エンドポイントルーティングを使用します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-176">The preceding code uses an authorization filter, setting the fallback policy uses endpoint routing.</span></span> <span data-ttu-id="b5dba-177">すべてのユーザーが認証されるようにするには、フォールバックポリシーを設定することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="b5dba-177">Setting the fallback policy is the preferred way to require all users be authenticated.</span></span>

<span data-ttu-id="b5dba-178">[](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) `Index` `Privacy` 匿名ユーザーが登録前にサイトに関する情報を取得できるように、allowanonymous をおよびページに追加します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-178">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the `Index` and `Privacy` pages so anonymous users can get information about the site before they register:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Index.cshtml.cs?highlight=1,7)]

### <a name="configure-the-test-account"></a><span data-ttu-id="b5dba-179">テストアカウントを構成する</span><span class="sxs-lookup"><span data-stu-id="b5dba-179">Configure the test account</span></span>

<span data-ttu-id="b5dba-180">クラスは、 `SeedData` 管理者とマネージャーの2つのアカウントを作成します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-180">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="b5dba-181">これらのアカウントのパスワードを設定するには、 [シークレットマネージャーツール](xref:security/app-secrets) を使用します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-181">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="b5dba-182">プロジェクトディレクトリ ( *Program.cs* を含むディレクトリ) のパスワードを設定します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-182">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="b5dba-183">強力なパスワードが指定されていない場合は、が呼び出されると例外がスローされ `SeedData.Initialize` ます。</span><span class="sxs-lookup"><span data-stu-id="b5dba-183">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="b5dba-184">`Main`テストパスワードを使用するように更新します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-184">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final3/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="b5dba-185">テストアカウントを作成し、連絡先を更新する</span><span class="sxs-lookup"><span data-stu-id="b5dba-185">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="b5dba-186">クラスのメソッドを更新して、 `Initialize` `SeedData` テストアカウントを作成します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-186">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="b5dba-187">管理者のユーザー ID とを `ContactStatus` 連絡先に追加します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-187">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="b5dba-188">いずれかの連絡先を "送信済み" にして、"拒否" するようにします。</span><span class="sxs-lookup"><span data-stu-id="b5dba-188">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="b5dba-189">すべての連絡先にユーザー ID と状態を追加します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-189">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="b5dba-190">1つの連絡先のみが表示されます。</span><span class="sxs-lookup"><span data-stu-id="b5dba-190">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="b5dba-191">所有者、マネージャー、および管理者の承認ハンドラーを作成する</span><span class="sxs-lookup"><span data-stu-id="b5dba-191">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="b5dba-192">`ContactIsOwnerAuthorizationHandler`*承認* フォルダーにクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-192">Create a `ContactIsOwnerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="b5dba-193">は、 `ContactIsOwnerAuthorizationHandler` リソースに対して動作しているユーザーがリソースを所有していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-193">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="b5dba-194">は `ContactIsOwnerAuthorizationHandler` コンテキストを呼び出し [ます。](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) 現在の認証済みユーザーが連絡先の所有者である場合は成功します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-194">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="b5dba-195">通常、承認ハンドラーは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="b5dba-195">Authorization handlers generally:</span></span>

* <span data-ttu-id="b5dba-196">`context.Succeed`要件が満たされた場合は、を返します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-196">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="b5dba-197">`Task.CompletedTask`要件を満たしていない場合は、を返します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-197">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="b5dba-198">`Task.CompletedTask` が成功または失敗ではない場合は &mdash; 、他の承認ハンドラーの実行を許可します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-198">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="b5dba-199">明示的に失敗する必要がある場合は、コンテキストを返し [ます。失敗](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail)。</span><span class="sxs-lookup"><span data-stu-id="b5dba-199">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="b5dba-200">アプリを使用すると、連絡先所有者は自分のデータを編集/削除/作成できます。</span><span class="sxs-lookup"><span data-stu-id="b5dba-200">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="b5dba-201">`ContactIsOwnerAuthorizationHandler` 要件パラメーターで渡された操作を確認する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="b5dba-201">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="b5dba-202">マネージャーの承認ハンドラーを作成する</span><span class="sxs-lookup"><span data-stu-id="b5dba-202">Create a manager authorization handler</span></span>

<span data-ttu-id="b5dba-203">`ContactManagerAuthorizationHandler`*承認* フォルダーにクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-203">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="b5dba-204">は、 `ContactManagerAuthorizationHandler` リソースに対して動作しているユーザーがマネージャーであることを確認します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-204">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="b5dba-205">コンテンツの変更を承認または拒否できるのは、管理者のみです (新規または変更)。</span><span class="sxs-lookup"><span data-stu-id="b5dba-205">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="b5dba-206">管理者の承認ハンドラーを作成する</span><span class="sxs-lookup"><span data-stu-id="b5dba-206">Create an administrator authorization handler</span></span>

<span data-ttu-id="b5dba-207">`ContactAdministratorsAuthorizationHandler`*承認* フォルダーにクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-207">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="b5dba-208">は、 `ContactAdministratorsAuthorizationHandler` リソースに対して動作しているユーザーが管理者であることを確認します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-208">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="b5dba-209">管理者は、すべての操作を実行できます。</span><span class="sxs-lookup"><span data-stu-id="b5dba-209">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="b5dba-210">認証ハンドラーを登録する</span><span class="sxs-lookup"><span data-stu-id="b5dba-210">Register the authorization handlers</span></span>

<span data-ttu-id="b5dba-211">Entity Framework Core を使用するサービスは、 [Addscoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions)を使用して[依存関係の挿入](xref:fundamentals/dependency-injection)に登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b5dba-211">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="b5dba-212">は、 `ContactIsOwnerAuthorizationHandler` [Identity](xref:security/authentication/identity) Entity Framework Core 上に構築された ASP.NET Core を使用します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-212">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="b5dba-213">サービスコレクションにハンドラーを登録し `ContactsController` て、 [依存関係の挿入](xref:fundamentals/dependency-injection)によってで使用できるようにします。</span><span class="sxs-lookup"><span data-stu-id="b5dba-213">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="b5dba-214">の末尾に次のコードを追加し `ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="b5dba-214">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet_defaultPolicy&highlight=23-99)]

<span data-ttu-id="b5dba-215">`ContactAdministratorsAuthorizationHandler` と `ContactManagerAuthorizationHandler` はシングルトンとして追加されます。</span><span class="sxs-lookup"><span data-stu-id="b5dba-215">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="b5dba-216">これらは EF を使用せず、必要なすべての情報がメソッドのパラメーターに含まれているため、シングルトンになり `Context` `HandleRequirementAsync` ます。</span><span class="sxs-lookup"><span data-stu-id="b5dba-216">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="b5dba-217">認証のサポート</span><span class="sxs-lookup"><span data-stu-id="b5dba-217">Support authorization</span></span>

<span data-ttu-id="b5dba-218">このセクションでは、ページを更新 Razor し、操作要件クラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-218">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="b5dba-219">Contact operation の要件クラスを確認する</span><span class="sxs-lookup"><span data-stu-id="b5dba-219">Review the contact operations requirements class</span></span>

<span data-ttu-id="b5dba-220">クラスを確認 `ContactOperations` します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-220">Review the `ContactOperations` class.</span></span> <span data-ttu-id="b5dba-221">このクラスには、アプリがサポートする要件が含まれています。</span><span class="sxs-lookup"><span data-stu-id="b5dba-221">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-no-locrazor-pages"></a><span data-ttu-id="b5dba-222">連絡先ページの基本クラスを作成する Razor</span><span class="sxs-lookup"><span data-stu-id="b5dba-222">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="b5dba-223">[連絡先] ページで使用されるサービスを含む基本クラスを作成 Razor します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-223">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="b5dba-224">基本クラスは、初期化コードを1つの場所に配置します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-224">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="b5dba-225">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="b5dba-225">The preceding code:</span></span>

* <span data-ttu-id="b5dba-226">`IAuthorizationService`認証ハンドラーにアクセスするためのサービスを追加します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-226">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="b5dba-227">サービスを追加し Identity `UserManager` ます。</span><span class="sxs-lookup"><span data-stu-id="b5dba-227">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="b5dba-228">`ApplicationDbContext` を追加します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-228">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="b5dba-229">CreateModel を更新する</span><span class="sxs-lookup"><span data-stu-id="b5dba-229">Update the CreateModel</span></span>

<span data-ttu-id="b5dba-230">Create page model コンストラクターを更新して、 `DI_BasePageModel` 基本クラスを使用します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-230">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="b5dba-231">`CreateModel.OnPostAsync`メソッドを次のように更新します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-231">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="b5dba-232">モデルにユーザー ID を追加し `Contact` ます。</span><span class="sxs-lookup"><span data-stu-id="b5dba-232">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="b5dba-233">承認ハンドラーを呼び出して、ユーザーが連絡先を作成するアクセス許可を持っていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-233">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="b5dba-234">IndexModel を更新する</span><span class="sxs-lookup"><span data-stu-id="b5dba-234">Update the IndexModel</span></span>

<span data-ttu-id="b5dba-235">`OnGetAsync`承認された連絡先だけが一般ユーザーに表示されるように、メソッドを更新します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-235">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="b5dba-236">EditModel を更新する</span><span class="sxs-lookup"><span data-stu-id="b5dba-236">Update the EditModel</span></span>

<span data-ttu-id="b5dba-237">ユーザーが連絡先を所有していることを確認するための承認ハンドラーを追加します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-237">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="b5dba-238">リソース承認が検証されているため、 `[Authorize]` 属性では不十分です。</span><span class="sxs-lookup"><span data-stu-id="b5dba-238">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="b5dba-239">属性が評価された場合、アプリにはリソースへのアクセス権がありません。</span><span class="sxs-lookup"><span data-stu-id="b5dba-239">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="b5dba-240">リソースベースの承認は、必須である必要があります。</span><span class="sxs-lookup"><span data-stu-id="b5dba-240">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="b5dba-241">アプリケーションがリソースにアクセスできるようになったら、そのアプリをページモデルに読み込むか、ハンドラー自体に読み込むことによって、チェックを実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b5dba-241">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="b5dba-242">リソースキーを渡すことによって、リソースに頻繁にアクセスします。</span><span class="sxs-lookup"><span data-stu-id="b5dba-242">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="b5dba-243">DeleteModel を更新する</span><span class="sxs-lookup"><span data-stu-id="b5dba-243">Update the DeleteModel</span></span>

<span data-ttu-id="b5dba-244">承認ハンドラーを使用するように delete ページモデルを更新して、ユーザーが連絡先に対する delete 権限を持っていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-244">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="b5dba-245">ビューに承認サービスを挿入する</span><span class="sxs-lookup"><span data-stu-id="b5dba-245">Inject the authorization service into the views</span></span>

<span data-ttu-id="b5dba-246">現在、UI には、ユーザーが変更できない連絡先の編集と削除のリンクが表示されています。</span><span class="sxs-lookup"><span data-stu-id="b5dba-246">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="b5dba-247">*ページ/_ViewImports* ファイルに承認サービスを挿入して、すべてのビューで使用できるようにします。</span><span class="sxs-lookup"><span data-stu-id="b5dba-247">Inject the authorization service in the *Pages/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="b5dba-248">前のマークアップは、いくつかのステートメントを追加し `using` ます。</span><span class="sxs-lookup"><span data-stu-id="b5dba-248">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="b5dba-249">*Pages/Contacts/Index. cshtml* の **編集** と **削除** のリンクを更新して、適切なアクセス許可を持つユーザーにのみ表示されるようにします。</span><span class="sxs-lookup"><span data-stu-id="b5dba-249">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="b5dba-250">データを変更するアクセス許可がないユーザーからのリンクを非表示にしても、アプリはセキュリティで保護されません。</span><span class="sxs-lookup"><span data-stu-id="b5dba-250">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="b5dba-251">リンクを非表示にすると、有効なリンクのみが表示されるため、アプリのユーザーがわかりやすくなります。</span><span class="sxs-lookup"><span data-stu-id="b5dba-251">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="b5dba-252">ユーザーは、生成された Url をハッキングして、所有していないデータに対する編集操作と削除操作を呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="b5dba-252">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="b5dba-253">Razorページまたはコントローラーは、データをセキュリティで保護するためにアクセスチェックを強制する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b5dba-253">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="b5dba-254">更新プログラムの詳細</span><span class="sxs-lookup"><span data-stu-id="b5dba-254">Update Details</span></span>

<span data-ttu-id="b5dba-255">上司が連絡先を承認または拒否できるように、詳細ビューを更新します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-255">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="b5dba-256">詳細ページモデルを更新します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-256">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="b5dba-257">ロールに対するユーザーの追加または削除</span><span class="sxs-lookup"><span data-stu-id="b5dba-257">Add or remove a user to a role</span></span>

<span data-ttu-id="b5dba-258">次の情報については、 [この問題](https://github.com/dotnet/AspNetCore.Docs/issues/8502) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b5dba-258">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="b5dba-259">ユーザーから特権を削除しています。</span><span class="sxs-lookup"><span data-stu-id="b5dba-259">Removing privileges from a user.</span></span> <span data-ttu-id="b5dba-260">たとえば、チャットアプリでユーザーのミュートを行います。</span><span class="sxs-lookup"><span data-stu-id="b5dba-260">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="b5dba-261">ユーザーへの特権の追加。</span><span class="sxs-lookup"><span data-stu-id="b5dba-261">Adding privileges to a user.</span></span>

<a name="challenge"></a>

## <a name="differences-between-challenge-and-forbid"></a><span data-ttu-id="b5dba-262">チャレンジと禁止の違い</span><span class="sxs-lookup"><span data-stu-id="b5dba-262">Differences between Challenge and Forbid</span></span>

<span data-ttu-id="b5dba-263">このアプリでは、認証された [ユーザーを要求](#rau)する既定のポリシーを設定します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-263">This app sets the default policy to [require authenticated users](#rau).</span></span> <span data-ttu-id="b5dba-264">次のコードでは、匿名ユーザーを許可します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-264">The following code allows anonymous users.</span></span> <span data-ttu-id="b5dba-265">匿名ユーザーは、チャレンジと禁止の違いを示すことができます。</span><span class="sxs-lookup"><span data-stu-id="b5dba-265">Anonymous users are allowed to show the differences between Challenge vs Forbid.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details2.cshtml.cs?name=snippet)]

<span data-ttu-id="b5dba-266">上記のコードにより、次のことが行われます。</span><span class="sxs-lookup"><span data-stu-id="b5dba-266">In the preceding code:</span></span>

* <span data-ttu-id="b5dba-267">ユーザーが認証 **されていない** 場合は、 `ChallengeResult` が返されます。</span><span class="sxs-lookup"><span data-stu-id="b5dba-267">When the user is **not** authenticated, a `ChallengeResult` is returned.</span></span> <span data-ttu-id="b5dba-268">が返されると、 `ChallengeResult` ユーザーはサインインページにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="b5dba-268">When a `ChallengeResult` is returned, the user is redirected to the sign-in page.</span></span>
* <span data-ttu-id="b5dba-269">ユーザーが認証されているが承認されていない場合は、 `ForbidResult` が返されます。</span><span class="sxs-lookup"><span data-stu-id="b5dba-269">When the user is authenticated, but not authorized, a `ForbidResult` is returned.</span></span> <span data-ttu-id="b5dba-270">が返されると、ユーザーは [ `ForbidResult` アクセス拒否] ページにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="b5dba-270">When a `ForbidResult` is returned, the user is redirected to the access denied page.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="b5dba-271">完成したアプリをテストする</span><span class="sxs-lookup"><span data-stu-id="b5dba-271">Test the completed app</span></span>

<span data-ttu-id="b5dba-272">シードされたユーザーアカウントのパスワードをまだ設定していない場合は、 [シークレットマネージャーツール](xref:security/app-secrets#secret-manager) を使用してパスワードを設定します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-272">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="b5dba-273">強力なパスワードを選択する: 8 個以上の文字と、少なくとも1つの大文字、数字、および記号を使用します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-273">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="b5dba-274">たとえば、は `Passw0rd!` 強力なパスワードの要件を満たしています。</span><span class="sxs-lookup"><span data-stu-id="b5dba-274">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="b5dba-275">プロジェクトのフォルダーから次のコマンドを実行します。ここで、 `<PW>` はパスワードです。</span><span class="sxs-lookup"><span data-stu-id="b5dba-275">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

<span data-ttu-id="b5dba-276">アプリに連絡先がある場合:</span><span class="sxs-lookup"><span data-stu-id="b5dba-276">If the app has contacts:</span></span>

* <span data-ttu-id="b5dba-277">テーブル内のすべてのレコードを削除 `Contact` します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-277">Delete all of the records in the `Contact` table.</span></span>
* <span data-ttu-id="b5dba-278">アプリケーションを再起動してデータベースをシード処理します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-278">Restart the app to seed the database.</span></span>

<span data-ttu-id="b5dba-279">完成したアプリをテストする簡単な方法は、3つの異なるブラウザー (または incognito/InPrivate セッション) を起動することです。</span><span class="sxs-lookup"><span data-stu-id="b5dba-279">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="b5dba-280">1つのブラウザーで、新しいユーザーを登録します (たとえば、 `test@example.com` )。</span><span class="sxs-lookup"><span data-stu-id="b5dba-280">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="b5dba-281">別のユーザーを使用して各ブラウザーにサインインします。</span><span class="sxs-lookup"><span data-stu-id="b5dba-281">Sign in to each browser with a different user.</span></span> <span data-ttu-id="b5dba-282">次の操作を確認します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-282">Verify the following operations:</span></span>

* <span data-ttu-id="b5dba-283">登録済みのユーザーは、すべての承認済み連絡先データを表示できます。</span><span class="sxs-lookup"><span data-stu-id="b5dba-283">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="b5dba-284">登録されているユーザーは、自分のデータを編集または削除できます。</span><span class="sxs-lookup"><span data-stu-id="b5dba-284">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="b5dba-285">マネージャーは、連絡先データを承認/拒否することができます。</span><span class="sxs-lookup"><span data-stu-id="b5dba-285">Managers can approve/reject contact data.</span></span> <span data-ttu-id="b5dba-286">このビューには、 `Details` [ **承認** ] ボタンと [ **却下** ] ボタンが表示されます。</span><span class="sxs-lookup"><span data-stu-id="b5dba-286">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="b5dba-287">管理者は、すべてのデータを承認/拒否し、編集/削除することができます。</span><span class="sxs-lookup"><span data-stu-id="b5dba-287">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="b5dba-288">ユーザー</span><span class="sxs-lookup"><span data-stu-id="b5dba-288">User</span></span>                | <span data-ttu-id="b5dba-289">アプリによるシード処理</span><span class="sxs-lookup"><span data-stu-id="b5dba-289">Seeded by the app</span></span> | <span data-ttu-id="b5dba-290">オプション</span><span class="sxs-lookup"><span data-stu-id="b5dba-290">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="b5dba-291">いいえ</span><span class="sxs-lookup"><span data-stu-id="b5dba-291">No</span></span>                | <span data-ttu-id="b5dba-292">独自のデータを編集または削除します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-292">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="b5dba-293">はい</span><span class="sxs-lookup"><span data-stu-id="b5dba-293">Yes</span></span>               | <span data-ttu-id="b5dba-294">自分のデータを承認/拒否し、編集/削除します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-294">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="b5dba-295">はい</span><span class="sxs-lookup"><span data-stu-id="b5dba-295">Yes</span></span>               | <span data-ttu-id="b5dba-296">すべてのデータを承認/拒否し、編集/削除します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-296">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="b5dba-297">管理者のブラウザーで連絡先を作成します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-297">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="b5dba-298">管理者の連絡先から削除と編集のための URL をコピーします。</span><span class="sxs-lookup"><span data-stu-id="b5dba-298">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="b5dba-299">これらのリンクをテストユーザーのブラウザーに貼り付けて、テストユーザーがこれらの操作を実行できないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-299">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="b5dba-300">スターターアプリを作成する</span><span class="sxs-lookup"><span data-stu-id="b5dba-300">Create the starter app</span></span>

* <span data-ttu-id="b5dba-301">Razor"ContactManager" という名前のページアプリを作成する</span><span class="sxs-lookup"><span data-stu-id="b5dba-301">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="b5dba-302">**個々のユーザーアカウント** を使用してアプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-302">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="b5dba-303">名前空間がサンプルで使用される名前空間と一致するように、"ContactManager" という名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-303">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="b5dba-304">`-uld` SQLite ではなく LocalDB を指定します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-304">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="b5dba-305">*モデル/Contact を追加します。*</span><span class="sxs-lookup"><span data-stu-id="b5dba-305">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="b5dba-306">モデルをスキャフォールディング `Contact` します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-306">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="b5dba-307">初期移行を作成し、データベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-307">Create initial migration and update the database:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
dotnet ef database drop -f
dotnet ef migrations add initial
dotnet ef database update
```

<span data-ttu-id="b5dba-308">コマンドでバグが発生した場合は `dotnet aspnet-codegenerator razorpage` 、 [GitHub の問題](https://github.com/aspnet/Scaffolding/issues/984)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b5dba-308">If you experience a bug with the `dotnet aspnet-codegenerator razorpage` command, see [this GitHub issue](https://github.com/aspnet/Scaffolding/issues/984).</span></span>

* <span data-ttu-id="b5dba-309">*Pages/Shared/_Layout cshtml* ファイルで **contactmanager** アンカーを更新します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-309">Update the **ContactManager** anchor in the *Pages/Shared/_Layout.cshtml* file:</span></span>

 ```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Contacts/Index">ContactManager</a>
  ```

* <span data-ttu-id="b5dba-310">連絡先の作成、編集、および削除によるアプリのテスト</span><span class="sxs-lookup"><span data-stu-id="b5dba-310">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="b5dba-311">データベースのシード</span><span class="sxs-lookup"><span data-stu-id="b5dba-311">Seed the database</span></span>

<span data-ttu-id="b5dba-312">[SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs)クラスを *Data* フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-312">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) class to the *Data* folder:</span></span>

[!code-csharp[](secure-data/samples/starter3/Data/SeedData.cs)]

<span data-ttu-id="b5dba-313">呼び出し `SeedData.Initialize` 元 `Main` :</span><span class="sxs-lookup"><span data-stu-id="b5dba-313">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter3/Program.cs)]

<span data-ttu-id="b5dba-314">アプリによってデータベースがシード処理されたことをテストします。</span><span class="sxs-lookup"><span data-stu-id="b5dba-314">Test that the app seeded the database.</span></span> <span data-ttu-id="b5dba-315">Contact DB に行がある場合、seed メソッドは実行されません。</span><span class="sxs-lookup"><span data-stu-id="b5dba-315">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

<span data-ttu-id="b5dba-316">このチュートリアルでは、承認によって保護されたユーザーデータを使用して ASP.NET Core web アプリを作成する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-316">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="b5dba-317">認証済み (登録済み) のユーザーが作成した連絡先の一覧が表示されます。</span><span class="sxs-lookup"><span data-stu-id="b5dba-317">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="b5dba-318">セキュリティグループには次の3つがあります。</span><span class="sxs-lookup"><span data-stu-id="b5dba-318">There are three security groups:</span></span>

* <span data-ttu-id="b5dba-319">**登録されているユーザー** は、すべての承認済みデータを表示したり、自分のデータを編集または削除したりできます。</span><span class="sxs-lookup"><span data-stu-id="b5dba-319">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="b5dba-320">**管理者** は、連絡先データを承認または拒否できます。</span><span class="sxs-lookup"><span data-stu-id="b5dba-320">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="b5dba-321">承認された連絡先だけがユーザーに表示されます。</span><span class="sxs-lookup"><span data-stu-id="b5dba-321">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="b5dba-322">**管理者** は、任意のデータを承認/拒否したり、編集/削除したりできます。</span><span class="sxs-lookup"><span data-stu-id="b5dba-322">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="b5dba-323">次の図では、user Rick ( `rick@example.com` ) がサインインしています。</span><span class="sxs-lookup"><span data-stu-id="b5dba-323">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="b5dba-324">Rick は、承認された連絡先を表示したり、[削除] を **編集** したりするだけで、 /  / 連絡先の新しいリンクを **作成** できます。</span><span class="sxs-lookup"><span data-stu-id="b5dba-324">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="b5dba-325">Rick によって作成された最後のレコードにのみ、 **編集** および **削除** のリンクが表示されます。</span><span class="sxs-lookup"><span data-stu-id="b5dba-325">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="b5dba-326">他のユーザーには、マネージャーまたは管理者が状態を "承認済み" に変更するまで、最後のレコードは表示されません。</span><span class="sxs-lookup"><span data-stu-id="b5dba-326">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Rick がサインインしていることを示すスクリーンショット](secure-data/_static/rick.png)

<span data-ttu-id="b5dba-328">次の図で `manager@contoso.com` は、がサインインし、マネージャーのロールに含まれています。</span><span class="sxs-lookup"><span data-stu-id="b5dba-328">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![サインインしたことを示すスクリーンショット manager@contoso.com](secure-data/_static/manager1.png)

<span data-ttu-id="b5dba-330">次の図は、連絡先のマネージャーの詳細ビューを示しています。</span><span class="sxs-lookup"><span data-stu-id="b5dba-330">The following image shows the managers details view of a contact:</span></span>

![連絡先のマネージャーの表示](secure-data/_static/manager.png)

<span data-ttu-id="b5dba-332">[ **承認** ] ボタンと [ **却下** ] ボタンは、マネージャーと管理者に対してのみ表示されます。</span><span class="sxs-lookup"><span data-stu-id="b5dba-332">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="b5dba-333">次の図で `admin@contoso.com` は、がサインインし、管理者のロールに含まれています。</span><span class="sxs-lookup"><span data-stu-id="b5dba-333">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![サインインしたことを示すスクリーンショット admin@contoso.com](secure-data/_static/admin.png)

<span data-ttu-id="b5dba-335">管理者には、すべての特権があります。</span><span class="sxs-lookup"><span data-stu-id="b5dba-335">The administrator has all privileges.</span></span> <span data-ttu-id="b5dba-336">連絡先の読み取り、編集、削除、および連絡先の状態の変更を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="b5dba-336">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="b5dba-337">アプリは、次のモデルを [スキャフォールディング](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) することによって作成されました `Contact` 。</span><span class="sxs-lookup"><span data-stu-id="b5dba-337">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="b5dba-338">このサンプルには、次の承認ハンドラーが含まれています。</span><span class="sxs-lookup"><span data-stu-id="b5dba-338">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="b5dba-339">`ContactIsOwnerAuthorizationHandler`: ユーザーがデータを編集できるようにします。</span><span class="sxs-lookup"><span data-stu-id="b5dba-339">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="b5dba-340">`ContactManagerAuthorizationHandler`: 管理者が連絡先を承認または拒否できるようにします。</span><span class="sxs-lookup"><span data-stu-id="b5dba-340">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="b5dba-341">`ContactAdministratorsAuthorizationHandler`: 管理者は、連絡先を承認または拒否したり、連絡先を編集または削除したりできます。</span><span class="sxs-lookup"><span data-stu-id="b5dba-341">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="b5dba-342">前提条件</span><span class="sxs-lookup"><span data-stu-id="b5dba-342">Prerequisites</span></span>

<span data-ttu-id="b5dba-343">このチュートリアルは高度です。</span><span class="sxs-lookup"><span data-stu-id="b5dba-343">This tutorial is advanced.</span></span> <span data-ttu-id="b5dba-344">次のことを理解している必要があります。</span><span class="sxs-lookup"><span data-stu-id="b5dba-344">You should be familiar with:</span></span>

* [<span data-ttu-id="b5dba-345">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b5dba-345">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="b5dba-346">認証</span><span class="sxs-lookup"><span data-stu-id="b5dba-346">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="b5dba-347">アカウントの確認とパスワードの回復</span><span class="sxs-lookup"><span data-stu-id="b5dba-347">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="b5dba-348">承認</span><span class="sxs-lookup"><span data-stu-id="b5dba-348">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="b5dba-349">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="b5dba-349">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="b5dba-350">スターターおよび完成したアプリ</span><span class="sxs-lookup"><span data-stu-id="b5dba-350">The starter and completed app</span></span>

<span data-ttu-id="b5dba-351">完成し[た](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples)アプリを[ダウンロード](xref:index#how-to-download-a-sample)します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-351">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="b5dba-352">完成したアプリを[テスト](#test-the-completed-app)して、セキュリティ機能に慣れるようにします。</span><span class="sxs-lookup"><span data-stu-id="b5dba-352">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="b5dba-353">スターターアプリ</span><span class="sxs-lookup"><span data-stu-id="b5dba-353">The starter app</span></span>

<span data-ttu-id="b5dba-354">[スターター](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/)アプリを[ダウンロード](xref:index#how-to-download-a-sample)します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-354">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="b5dba-355">アプリを実行し、[ **Contactmanager** ] リンクをタップして、連絡先を作成、編集、および削除できることを確認します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-355">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="b5dba-356">ユーザーデータのセキュリティ保護</span><span class="sxs-lookup"><span data-stu-id="b5dba-356">Secure user data</span></span>

<span data-ttu-id="b5dba-357">以下のセクションでは、セキュリティで保護されたユーザーデータアプリを作成するための主要な手順について説明します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-357">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="b5dba-358">完成したプロジェクトを参照した方が便利な場合があります。</span><span class="sxs-lookup"><span data-stu-id="b5dba-358">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="b5dba-359">連絡先データをユーザーに関連付ける</span><span class="sxs-lookup"><span data-stu-id="b5dba-359">Tie the contact data to the user</span></span>

<span data-ttu-id="b5dba-360">ASP.NET ユーザー ID を使用すると、 [Identity](xref:security/authentication/identity) ユーザーがデータを編集できるようになりますが、他のユーザーデータは編集できません。</span><span class="sxs-lookup"><span data-stu-id="b5dba-360">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="b5dba-361">`OwnerID` `ContactStatus` モデルにおよびを追加し `Contact` ます。</span><span class="sxs-lookup"><span data-stu-id="b5dba-361">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="b5dba-362">`OwnerID` データベース内のテーブルのユーザー ID を示し `AspNetUser` [Identity](xref:security/authentication/identity) ます。</span><span class="sxs-lookup"><span data-stu-id="b5dba-362">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="b5dba-363">フィールドは、 `Status` 一般的なユーザーが連絡先を表示できるかどうかを決定します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-363">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="b5dba-364">新しい移行を作成し、データベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-364">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-no-locidentity"></a><span data-ttu-id="b5dba-365">役割サービスの追加先 Identity</span><span class="sxs-lookup"><span data-stu-id="b5dba-365">Add Role services to Identity</span></span>

<span data-ttu-id="b5dba-366">役割サービスを追加するには、 [Addroles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) を追加します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-366">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet2&highlight=11)]

### <a name="require-authenticated-users"></a><span data-ttu-id="b5dba-367">認証されたユーザーが必要</span><span class="sxs-lookup"><span data-stu-id="b5dba-367">Require authenticated users</span></span>

<span data-ttu-id="b5dba-368">ユーザーが認証されるようにするには、既定の認証ポリシーを設定します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-368">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet&highlight=17-99)] 

 <span data-ttu-id="b5dba-369">Razorページ、コントローラー、またはアクションメソッドレベルで、属性を使用して認証を無効にすることができ `[AllowAnonymous]` ます。</span><span class="sxs-lookup"><span data-stu-id="b5dba-369">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="b5dba-370">既定の認証ポリシーを設定すると、ユーザーの認証が必要になり、新しく追加されたページとコントローラーは保護され Razor ます。</span><span class="sxs-lookup"><span data-stu-id="b5dba-370">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="b5dba-371">既定で認証が必要になることは、新しいコントローラーやページを利用して属性を含めるよりも安全です Razor `[Authorize]` 。</span><span class="sxs-lookup"><span data-stu-id="b5dba-371">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="b5dba-372">匿名ユーザーが登録前にサイトに関する情報を取得できるように、 [Allowanonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) を Index、About、および Contact の各ページに追加します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-372">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index, About, and Contact pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Index.cshtml.cs?highlight=1,6)]

### <a name="configure-the-test-account"></a><span data-ttu-id="b5dba-373">テストアカウントを構成する</span><span class="sxs-lookup"><span data-stu-id="b5dba-373">Configure the test account</span></span>

<span data-ttu-id="b5dba-374">クラスは、 `SeedData` 管理者とマネージャーの2つのアカウントを作成します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-374">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="b5dba-375">これらのアカウントのパスワードを設定するには、 [シークレットマネージャーツール](xref:security/app-secrets) を使用します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-375">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="b5dba-376">プロジェクトディレクトリ ( *Program.cs* を含むディレクトリ) のパスワードを設定します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-376">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="b5dba-377">強力なパスワードが指定されていない場合は、が呼び出されると例外がスローされ `SeedData.Initialize` ます。</span><span class="sxs-lookup"><span data-stu-id="b5dba-377">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="b5dba-378">`Main`テストパスワードを使用するように更新します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-378">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="b5dba-379">テストアカウントを作成し、連絡先を更新する</span><span class="sxs-lookup"><span data-stu-id="b5dba-379">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="b5dba-380">クラスのメソッドを更新して、 `Initialize` `SeedData` テストアカウントを作成します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-380">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="b5dba-381">管理者のユーザー ID とを `ContactStatus` 連絡先に追加します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-381">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="b5dba-382">いずれかの連絡先を "送信済み" にして、"拒否" するようにします。</span><span class="sxs-lookup"><span data-stu-id="b5dba-382">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="b5dba-383">すべての連絡先にユーザー ID と状態を追加します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-383">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="b5dba-384">1つの連絡先のみが表示されます。</span><span class="sxs-lookup"><span data-stu-id="b5dba-384">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="b5dba-385">所有者、マネージャー、および管理者の承認ハンドラーを作成する</span><span class="sxs-lookup"><span data-stu-id="b5dba-385">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="b5dba-386">*承認* フォルダーを作成し、 `ContactIsOwnerAuthorizationHandler` そこにクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-386">Create an *Authorization* folder and create a `ContactIsOwnerAuthorizationHandler` class in it.</span></span> <span data-ttu-id="b5dba-387">は、 `ContactIsOwnerAuthorizationHandler` リソースに対して動作しているユーザーがリソースを所有していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-387">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="b5dba-388">は `ContactIsOwnerAuthorizationHandler` コンテキストを呼び出し [ます。](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) 現在の認証済みユーザーが連絡先の所有者である場合は成功します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-388">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="b5dba-389">通常、承認ハンドラーは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="b5dba-389">Authorization handlers generally:</span></span>

* <span data-ttu-id="b5dba-390">`context.Succeed`要件が満たされた場合は、を返します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-390">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="b5dba-391">`Task.CompletedTask`要件を満たしていない場合は、を返します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-391">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="b5dba-392">`Task.CompletedTask` が成功または失敗ではない場合は &mdash; 、他の承認ハンドラーの実行を許可します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-392">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="b5dba-393">明示的に失敗する必要がある場合は、コンテキストを返し [ます。失敗](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail)。</span><span class="sxs-lookup"><span data-stu-id="b5dba-393">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="b5dba-394">アプリを使用すると、連絡先所有者は自分のデータを編集/削除/作成できます。</span><span class="sxs-lookup"><span data-stu-id="b5dba-394">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="b5dba-395">`ContactIsOwnerAuthorizationHandler` 要件パラメーターで渡された操作を確認する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="b5dba-395">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="b5dba-396">マネージャーの承認ハンドラーを作成する</span><span class="sxs-lookup"><span data-stu-id="b5dba-396">Create a manager authorization handler</span></span>

<span data-ttu-id="b5dba-397">`ContactManagerAuthorizationHandler`*承認* フォルダーにクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-397">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="b5dba-398">は、 `ContactManagerAuthorizationHandler` リソースに対して動作しているユーザーがマネージャーであることを確認します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-398">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="b5dba-399">コンテンツの変更を承認または拒否できるのは、管理者のみです (新規または変更)。</span><span class="sxs-lookup"><span data-stu-id="b5dba-399">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="b5dba-400">管理者の承認ハンドラーを作成する</span><span class="sxs-lookup"><span data-stu-id="b5dba-400">Create an administrator authorization handler</span></span>

<span data-ttu-id="b5dba-401">`ContactAdministratorsAuthorizationHandler`*承認* フォルダーにクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-401">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="b5dba-402">は、 `ContactAdministratorsAuthorizationHandler` リソースに対して動作しているユーザーが管理者であることを確認します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-402">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="b5dba-403">管理者は、すべての操作を実行できます。</span><span class="sxs-lookup"><span data-stu-id="b5dba-403">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="b5dba-404">認証ハンドラーを登録する</span><span class="sxs-lookup"><span data-stu-id="b5dba-404">Register the authorization handlers</span></span>

<span data-ttu-id="b5dba-405">Entity Framework Core を使用するサービスは、 [Addscoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions)を使用して[依存関係の挿入](xref:fundamentals/dependency-injection)に登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b5dba-405">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="b5dba-406">は、 `ContactIsOwnerAuthorizationHandler` [Identity](xref:security/authentication/identity) Entity Framework Core 上に構築された ASP.NET Core を使用します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-406">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="b5dba-407">サービスコレクションにハンドラーを登録し `ContactsController` て、 [依存関係の挿入](xref:fundamentals/dependency-injection)によってで使用できるようにします。</span><span class="sxs-lookup"><span data-stu-id="b5dba-407">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="b5dba-408">の末尾に次のコードを追加し `ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="b5dba-408">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

<span data-ttu-id="b5dba-409">`ContactAdministratorsAuthorizationHandler` と `ContactManagerAuthorizationHandler` はシングルトンとして追加されます。</span><span class="sxs-lookup"><span data-stu-id="b5dba-409">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="b5dba-410">これらは EF を使用せず、必要なすべての情報がメソッドのパラメーターに含まれているため、シングルトンになり `Context` `HandleRequirementAsync` ます。</span><span class="sxs-lookup"><span data-stu-id="b5dba-410">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="b5dba-411">認証のサポート</span><span class="sxs-lookup"><span data-stu-id="b5dba-411">Support authorization</span></span>

<span data-ttu-id="b5dba-412">このセクションでは、ページを更新 Razor し、操作要件クラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-412">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="b5dba-413">Contact operation の要件クラスを確認する</span><span class="sxs-lookup"><span data-stu-id="b5dba-413">Review the contact operations requirements class</span></span>

<span data-ttu-id="b5dba-414">クラスを確認 `ContactOperations` します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-414">Review the `ContactOperations` class.</span></span> <span data-ttu-id="b5dba-415">このクラスには、アプリがサポートする要件が含まれています。</span><span class="sxs-lookup"><span data-stu-id="b5dba-415">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-no-locrazor-pages"></a><span data-ttu-id="b5dba-416">連絡先ページの基本クラスを作成する Razor</span><span class="sxs-lookup"><span data-stu-id="b5dba-416">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="b5dba-417">[連絡先] ページで使用されるサービスを含む基本クラスを作成 Razor します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-417">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="b5dba-418">基本クラスは、初期化コードを1つの場所に配置します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-418">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="b5dba-419">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="b5dba-419">The preceding code:</span></span>

* <span data-ttu-id="b5dba-420">`IAuthorizationService`認証ハンドラーにアクセスするためのサービスを追加します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-420">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="b5dba-421">サービスを追加し Identity `UserManager` ます。</span><span class="sxs-lookup"><span data-stu-id="b5dba-421">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="b5dba-422">`ApplicationDbContext` を追加します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-422">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="b5dba-423">CreateModel を更新する</span><span class="sxs-lookup"><span data-stu-id="b5dba-423">Update the CreateModel</span></span>

<span data-ttu-id="b5dba-424">Create page model コンストラクターを更新して、 `DI_BasePageModel` 基本クラスを使用します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-424">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="b5dba-425">`CreateModel.OnPostAsync`メソッドを次のように更新します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-425">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="b5dba-426">モデルにユーザー ID を追加し `Contact` ます。</span><span class="sxs-lookup"><span data-stu-id="b5dba-426">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="b5dba-427">承認ハンドラーを呼び出して、ユーザーが連絡先を作成するアクセス許可を持っていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-427">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="b5dba-428">IndexModel を更新する</span><span class="sxs-lookup"><span data-stu-id="b5dba-428">Update the IndexModel</span></span>

<span data-ttu-id="b5dba-429">`OnGetAsync`承認された連絡先だけが一般ユーザーに表示されるように、メソッドを更新します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-429">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="b5dba-430">EditModel を更新する</span><span class="sxs-lookup"><span data-stu-id="b5dba-430">Update the EditModel</span></span>

<span data-ttu-id="b5dba-431">ユーザーが連絡先を所有していることを確認するための承認ハンドラーを追加します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-431">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="b5dba-432">リソース承認が検証されているため、 `[Authorize]` 属性では不十分です。</span><span class="sxs-lookup"><span data-stu-id="b5dba-432">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="b5dba-433">属性が評価された場合、アプリにはリソースへのアクセス権がありません。</span><span class="sxs-lookup"><span data-stu-id="b5dba-433">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="b5dba-434">リソースベースの承認は、必須である必要があります。</span><span class="sxs-lookup"><span data-stu-id="b5dba-434">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="b5dba-435">アプリケーションがリソースにアクセスできるようになったら、そのアプリをページモデルに読み込むか、ハンドラー自体に読み込むことによって、チェックを実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b5dba-435">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="b5dba-436">リソースキーを渡すことによって、リソースに頻繁にアクセスします。</span><span class="sxs-lookup"><span data-stu-id="b5dba-436">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="b5dba-437">DeleteModel を更新する</span><span class="sxs-lookup"><span data-stu-id="b5dba-437">Update the DeleteModel</span></span>

<span data-ttu-id="b5dba-438">承認ハンドラーを使用するように delete ページモデルを更新して、ユーザーが連絡先に対する delete 権限を持っていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-438">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="b5dba-439">ビューに承認サービスを挿入する</span><span class="sxs-lookup"><span data-stu-id="b5dba-439">Inject the authorization service into the views</span></span>

<span data-ttu-id="b5dba-440">現在、UI には、ユーザーが変更できない連絡先の編集と削除のリンクが表示されています。</span><span class="sxs-lookup"><span data-stu-id="b5dba-440">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="b5dba-441">すべてのビューで使用できるように、 *views/_ViewImports cshtml* ファイルに承認サービスを挿入します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-441">Inject the authorization service in the *Views/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="b5dba-442">前のマークアップは、いくつかのステートメントを追加し `using` ます。</span><span class="sxs-lookup"><span data-stu-id="b5dba-442">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="b5dba-443">*Pages/Contacts/Index. cshtml* の **編集** と **削除** のリンクを更新して、適切なアクセス許可を持つユーザーにのみ表示されるようにします。</span><span class="sxs-lookup"><span data-stu-id="b5dba-443">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="b5dba-444">データを変更するアクセス許可がないユーザーからのリンクを非表示にしても、アプリはセキュリティで保護されません。</span><span class="sxs-lookup"><span data-stu-id="b5dba-444">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="b5dba-445">リンクを非表示にすると、有効なリンクのみが表示されるため、アプリのユーザーがわかりやすくなります。</span><span class="sxs-lookup"><span data-stu-id="b5dba-445">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="b5dba-446">ユーザーは、生成された Url をハッキングして、所有していないデータに対する編集操作と削除操作を呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="b5dba-446">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="b5dba-447">Razorページまたはコントローラーは、データをセキュリティで保護するためにアクセスチェックを強制する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b5dba-447">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="b5dba-448">更新プログラムの詳細</span><span class="sxs-lookup"><span data-stu-id="b5dba-448">Update Details</span></span>

<span data-ttu-id="b5dba-449">上司が連絡先を承認または拒否できるように、詳細ビューを更新します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-449">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="b5dba-450">詳細ページモデルを更新します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-450">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="b5dba-451">ロールに対するユーザーの追加または削除</span><span class="sxs-lookup"><span data-stu-id="b5dba-451">Add or remove a user to a role</span></span>

<span data-ttu-id="b5dba-452">次の情報については、 [この問題](https://github.com/dotnet/AspNetCore.Docs/issues/8502) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b5dba-452">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="b5dba-453">ユーザーから特権を削除しています。</span><span class="sxs-lookup"><span data-stu-id="b5dba-453">Removing privileges from a user.</span></span> <span data-ttu-id="b5dba-454">たとえば、チャットアプリでユーザーのミュートを行います。</span><span class="sxs-lookup"><span data-stu-id="b5dba-454">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="b5dba-455">ユーザーへの特権の追加。</span><span class="sxs-lookup"><span data-stu-id="b5dba-455">Adding privileges to a user.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="b5dba-456">完成したアプリをテストする</span><span class="sxs-lookup"><span data-stu-id="b5dba-456">Test the completed app</span></span>

<span data-ttu-id="b5dba-457">シードされたユーザーアカウントのパスワードをまだ設定していない場合は、 [シークレットマネージャーツール](xref:security/app-secrets#secret-manager) を使用してパスワードを設定します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-457">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="b5dba-458">強力なパスワードを選択する: 8 個以上の文字と、少なくとも1つの大文字、数字、および記号を使用します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-458">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="b5dba-459">たとえば、は `Passw0rd!` 強力なパスワードの要件を満たしています。</span><span class="sxs-lookup"><span data-stu-id="b5dba-459">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="b5dba-460">プロジェクトのフォルダーから次のコマンドを実行します。ここで、 `<PW>` はパスワードです。</span><span class="sxs-lookup"><span data-stu-id="b5dba-460">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

* <span data-ttu-id="b5dba-461">データベースを削除して更新する</span><span class="sxs-lookup"><span data-stu-id="b5dba-461">Drop and update the Database</span></span>

  ```dotnetcli
  dotnet ef database drop -f
  dotnet ef database update  
  ```

* <span data-ttu-id="b5dba-462">アプリケーションを再起動してデータベースをシード処理します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-462">Restart the app to seed the database.</span></span>

<span data-ttu-id="b5dba-463">完成したアプリをテストする簡単な方法は、3つの異なるブラウザー (または incognito/InPrivate セッション) を起動することです。</span><span class="sxs-lookup"><span data-stu-id="b5dba-463">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="b5dba-464">1つのブラウザーで、新しいユーザーを登録します (たとえば、 `test@example.com` )。</span><span class="sxs-lookup"><span data-stu-id="b5dba-464">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="b5dba-465">別のユーザーを使用して各ブラウザーにサインインします。</span><span class="sxs-lookup"><span data-stu-id="b5dba-465">Sign in to each browser with a different user.</span></span> <span data-ttu-id="b5dba-466">次の操作を確認します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-466">Verify the following operations:</span></span>

* <span data-ttu-id="b5dba-467">登録済みのユーザーは、すべての承認済み連絡先データを表示できます。</span><span class="sxs-lookup"><span data-stu-id="b5dba-467">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="b5dba-468">登録されているユーザーは、自分のデータを編集または削除できます。</span><span class="sxs-lookup"><span data-stu-id="b5dba-468">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="b5dba-469">マネージャーは、連絡先データを承認/拒否することができます。</span><span class="sxs-lookup"><span data-stu-id="b5dba-469">Managers can approve/reject contact data.</span></span> <span data-ttu-id="b5dba-470">このビューには、 `Details` [ **承認** ] ボタンと [ **却下** ] ボタンが表示されます。</span><span class="sxs-lookup"><span data-stu-id="b5dba-470">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="b5dba-471">管理者は、すべてのデータを承認/拒否し、編集/削除することができます。</span><span class="sxs-lookup"><span data-stu-id="b5dba-471">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="b5dba-472">ユーザー</span><span class="sxs-lookup"><span data-stu-id="b5dba-472">User</span></span>                | <span data-ttu-id="b5dba-473">アプリによるシード処理</span><span class="sxs-lookup"><span data-stu-id="b5dba-473">Seeded by the app</span></span> | <span data-ttu-id="b5dba-474">オプション</span><span class="sxs-lookup"><span data-stu-id="b5dba-474">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="b5dba-475">いいえ</span><span class="sxs-lookup"><span data-stu-id="b5dba-475">No</span></span>                | <span data-ttu-id="b5dba-476">独自のデータを編集または削除します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-476">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="b5dba-477">はい</span><span class="sxs-lookup"><span data-stu-id="b5dba-477">Yes</span></span>               | <span data-ttu-id="b5dba-478">自分のデータを承認/拒否し、編集/削除します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-478">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="b5dba-479">はい</span><span class="sxs-lookup"><span data-stu-id="b5dba-479">Yes</span></span>               | <span data-ttu-id="b5dba-480">すべてのデータを承認/拒否し、編集/削除します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-480">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="b5dba-481">管理者のブラウザーで連絡先を作成します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-481">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="b5dba-482">管理者の連絡先から削除と編集のための URL をコピーします。</span><span class="sxs-lookup"><span data-stu-id="b5dba-482">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="b5dba-483">これらのリンクをテストユーザーのブラウザーに貼り付けて、テストユーザーがこれらの操作を実行できないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-483">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="b5dba-484">スターターアプリを作成する</span><span class="sxs-lookup"><span data-stu-id="b5dba-484">Create the starter app</span></span>

* <span data-ttu-id="b5dba-485">Razor"ContactManager" という名前のページアプリを作成する</span><span class="sxs-lookup"><span data-stu-id="b5dba-485">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="b5dba-486">**個々のユーザーアカウント** を使用してアプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-486">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="b5dba-487">名前空間がサンプルで使用される名前空間と一致するように、"ContactManager" という名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-487">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="b5dba-488">`-uld` SQLite ではなく LocalDB を指定します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-488">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="b5dba-489">*モデル/Contact を追加します。*</span><span class="sxs-lookup"><span data-stu-id="b5dba-489">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="b5dba-490">モデルをスキャフォールディング `Contact` します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-490">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="b5dba-491">初期移行を作成し、データベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-491">Create initial migration and update the database:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
  dotnet ef database drop -f
  dotnet ef migrations add initial
  dotnet ef database update
  ```

* <span data-ttu-id="b5dba-492">*Pages/_Layout cshtml* ファイルで **contactmanager** アンカーを更新します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-492">Update the **ContactManager** anchor in the *Pages/_Layout.cshtml* file:</span></span>

  ```cshtml
  <a asp-page="/Contacts/Index" class="navbar-brand">ContactManager</a>
  ```

* <span data-ttu-id="b5dba-493">連絡先の作成、編集、および削除によるアプリのテスト</span><span class="sxs-lookup"><span data-stu-id="b5dba-493">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="b5dba-494">データベースのシード</span><span class="sxs-lookup"><span data-stu-id="b5dba-494">Seed the database</span></span>

<span data-ttu-id="b5dba-495">[SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs)クラスを *Data* フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="b5dba-495">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) class to the *Data* folder.</span></span>

<span data-ttu-id="b5dba-496">呼び出し `SeedData.Initialize` 元 `Main` :</span><span class="sxs-lookup"><span data-stu-id="b5dba-496">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

<span data-ttu-id="b5dba-497">アプリによってデータベースがシード処理されたことをテストします。</span><span class="sxs-lookup"><span data-stu-id="b5dba-497">Test that the app seeded the database.</span></span> <span data-ttu-id="b5dba-498">Contact DB に行がある場合、seed メソッドは実行されません。</span><span class="sxs-lookup"><span data-stu-id="b5dba-498">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a><span data-ttu-id="b5dba-499">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="b5dba-499">Additional resources</span></span>

* [<span data-ttu-id="b5dba-500">Azure App Service で .NET Core および SQL Database のアプリを作成する</span><span class="sxs-lookup"><span data-stu-id="b5dba-500">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* <span data-ttu-id="b5dba-501">[ASP.NET Core の承認ラボ](https://github.com/blowdart/AspNetAuthorizationWorkshop)。</span><span class="sxs-lookup"><span data-stu-id="b5dba-501">[ASP.NET Core Authorization Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span></span> <span data-ttu-id="b5dba-502">このチュートリアルで紹介するセキュリティ機能の詳細については、このラボを参照してください。</span><span class="sxs-lookup"><span data-stu-id="b5dba-502">This lab goes into more detail on the security features introduced in this tutorial.</span></span>
* <xref:security/authorization/introduction>
* [<span data-ttu-id="b5dba-503">カスタムポリシーベースの承認</span><span class="sxs-lookup"><span data-stu-id="b5dba-503">Custom policy-based authorization</span></span>](xref:security/authorization/policies)
