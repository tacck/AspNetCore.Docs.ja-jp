---
title: ASP.NET Core でファイルをアップロードする
author: rick-anderson
description: モデル バインドとストリーミングを使用して、ASP.NET Core MVC でファイルをアップロードする方法。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/03/2020
no-loc:
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
uid: mvc/models/file-uploads
ms.openlocfilehash: 93ffa3a5313e63a1e9b98fb5bf9788944254213f
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88635217"
---
# <a name="upload-files-in-aspnet-core"></a><span data-ttu-id="5c4ac-103">ASP.NET Core でファイルをアップロードする</span><span class="sxs-lookup"><span data-stu-id="5c4ac-103">Upload files in ASP.NET Core</span></span>

<span data-ttu-id="5c4ac-104">[上田氏](https://ardalis.com/)との[嵐](https://github.com/rutix)</span><span class="sxs-lookup"><span data-stu-id="5c4ac-104">By [Steve Smith](https://ardalis.com/) and [Rutger Storm](https://github.com/rutix)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5c4ac-105">ASP.NET Core では、小さいファイルの場合はバッファー モデル バインドを使用し、大きいファイルの場合は非バッファー ストリーミングを使用して、1 つ以上のファイルのアップロードがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-105">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="5c4ac-106">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="5c4ac-107">セキュリティの考慮事項</span><span class="sxs-lookup"><span data-stu-id="5c4ac-107">Security considerations</span></span>

<span data-ttu-id="5c4ac-108">サーバーにファイルをアップロードする機能をユーザーに提供するときは、十分に注意してください。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-108">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="5c4ac-109">攻撃者が次のようなことを試みる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-109">Attackers may attempt to:</span></span>

* <span data-ttu-id="5c4ac-110">[サービス拒否](/windows-hardware/drivers/ifs/denial-of-service)攻撃を実行する。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-110">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="5c4ac-111">ウイルスまたはマルウェアをアップロードする。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-111">Upload viruses or malware.</span></span>
* <span data-ttu-id="5c4ac-112">ネットワークやサーバーを他の方法で侵害する。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-112">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="5c4ac-113">攻撃の成功の可能性を少なくするセキュリティ手順は、次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-113">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="5c4ac-114">専用のファイル アップロード領域 (できれば、システム ドライブ以外) にファイルをアップロードします。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-114">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="5c4ac-115">専用の場所を使用すると、アップロードされるファイルにセキュリティ制限を適用しやすくなります。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-115">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="5c4ac-116">ファイルのアップロード場所に対する実行アクセス許可を無効にします。&dagger;</span><span class="sxs-lookup"><span data-stu-id="5c4ac-116">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="5c4ac-117">アプリと同じディレクトリ ツリーに、アップロードしたファイルを保持**しないでください**。&dagger;</span><span class="sxs-lookup"><span data-stu-id="5c4ac-117">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="5c4ac-118">アプリによって決められた安全なファイル名を使用します。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-118">Use a safe file name determined by the app.</span></span> <span data-ttu-id="5c4ac-119">ユーザーが指定したファイル名や、アップロードしたファイルの信頼されていないファイル名を使用しないでください。 &dagger; HTML は、表示時に信頼されていないファイル名をエンコードします。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-119">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="5c4ac-120">たとえば、ファイル名をログに記録したり、UI に表示したりし Razor ます (出力を自動的に HTML エンコードします)。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-120">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="5c4ac-121">アプリの設計仕様に対して承認されているファイル拡張子のみを許可します。&dagger;</span><span class="sxs-lookup"><span data-stu-id="5c4ac-121">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="5c4ac-122">クライアント側のチェックがサーバーで実行されていることを確認します。 &dagger; クライアント側のチェックは簡単に回避できます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-122">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="5c4ac-123">アップロードされたファイルのサイズをチェックします。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-123">Check the size of an uploaded file.</span></span> <span data-ttu-id="5c4ac-124">サイズの大きなアップロードを防ぐために、最大サイズ制限を設定します。&dagger;</span><span class="sxs-lookup"><span data-stu-id="5c4ac-124">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="5c4ac-125">同じ名前でアップロードされたファイルによってファイルが上書きされないようにする必要があるときは、ファイルをアップロードする前に、データベースまたは物理ストレージに対してファイル名を確認します。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-125">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="5c4ac-126">**ファイルを格納する前に、アップロードされる内容に対してウイルス/マルウェア スキャナーを実行します。**</span><span class="sxs-lookup"><span data-stu-id="5c4ac-126">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="5c4ac-127">&dagger;サンプル アプリで、条件を満たす方法が示されています。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-127">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="5c4ac-128">システムへの悪意のあるコードのアップロードは、頻繁に次のような内容のコードの実行するための足がかりとなります。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-128">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="5c4ac-129">システムの制御を完全に掌握する。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-129">Completely gain control of a system.</span></span>
> * <span data-ttu-id="5c4ac-130">システムがクラッシュする結果で、システムを過負荷状態にする。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-130">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="5c4ac-131">ユーザーまたはシステムのデータを破壊する。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-131">Compromise user or system data.</span></span>
> * <span data-ttu-id="5c4ac-132">パブリック UI に落書きする。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-132">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="5c4ac-133">ユーザーからファイルを受け入れる際の外部アクセスによる攻撃を減らす方法については、次の資料を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-133">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * [<span data-ttu-id="5c4ac-134">Unrestricted File Upload (ファイルの無制限のアップロード)</span><span class="sxs-lookup"><span data-stu-id="5c4ac-134">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [<span data-ttu-id="5c4ac-135">Azure セキュリティ: ユーザーから受け入れるファイルに適切な管理制御を整備する</span><span class="sxs-lookup"><span data-stu-id="5c4ac-135">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="5c4ac-136">サンプル アプリの例など、セキュリティ対策の実装の詳細については、「[検証](#validation)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-136">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="5c4ac-137">ストレージのシナリオ</span><span class="sxs-lookup"><span data-stu-id="5c4ac-137">Storage scenarios</span></span>

<span data-ttu-id="5c4ac-138">ファイルの一般的なストレージ オプションには次のようなものがあります。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-138">Common storage options for files include:</span></span>

* <span data-ttu-id="5c4ac-139">データベース</span><span class="sxs-lookup"><span data-stu-id="5c4ac-139">Database</span></span>

  * <span data-ttu-id="5c4ac-140">小さいファイルをアップロードする場合、物理ストレージ (ファイル システムまたはネットワーク共有) のオプションよりデータベースの方が速いことがよくあります。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-140">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="5c4ac-141">多くの場合、ユーザー データに対するデータベース レコードの取得でファイルの内容を同時に提供できるため (たとえば、アバター イメージ)、データベースの方が物理的なストレージ オプションより便利です。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-141">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="5c4ac-142">データベースは、データ ストレージ サービスを使用するよりコストが低くなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-142">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="5c4ac-143">物理ストレージ (ファイル システムまたはネットワーク共有)</span><span class="sxs-lookup"><span data-stu-id="5c4ac-143">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="5c4ac-144">大きいファイルのアップロードの場合:</span><span class="sxs-lookup"><span data-stu-id="5c4ac-144">For large file uploads:</span></span>
    * <span data-ttu-id="5c4ac-145">データベースの制限によって、アップロードのサイズが制限される場合があります。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-145">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="5c4ac-146">多くの場合、物理ストレージはデータベース内のストレージより高コストです。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-146">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="5c4ac-147">物理ストレージは、データ ストレージ サービスを使用するよりコストが低くなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-147">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="5c4ac-148">アプリのプロセスには、ストレージの場所に対する読み取りと書き込みのアクセス許可が必要です。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-148">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="5c4ac-149">**実行アクセス許可は付与しないでください。**</span><span class="sxs-lookup"><span data-stu-id="5c4ac-149">**Never grant execute permission.**</span></span>

* <span data-ttu-id="5c4ac-150">データ ストレージ サービス (例: [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span><span class="sxs-lookup"><span data-stu-id="5c4ac-150">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="5c4ac-151">通常、サービスでは、大抵の場合に単一障害点となるオンプレミス ソリューションより高いスケーラビリティと回復性が提供されます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-151">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="5c4ac-152">大規模なストレージ インフラストラクチャのシナリオでは、サービスのコストが低下する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-152">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="5c4ac-153">詳細については、「 [クイックスタート: .net を使用してオブジェクトストレージに blob を作成する](/azure/storage/blobs/storage-quickstart-blobs-dotnet)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-153">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="5c4ac-154">ファイル アップロードのシナリオ</span><span class="sxs-lookup"><span data-stu-id="5c4ac-154">File upload scenarios</span></span>

<span data-ttu-id="5c4ac-155">ファイルをアップロードするための一般的な 2 つの方法は、バッファーリングとストリーミングです。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-155">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="5c4ac-156">**バッファリング**</span><span class="sxs-lookup"><span data-stu-id="5c4ac-156">**Buffering**</span></span>

<span data-ttu-id="5c4ac-157">ファイル全体が <xref:Microsoft.AspNetCore.Http.IFormFile> に読み込まれます。これは、ファイルの処理または保存に使用される C# でのファイルの表現です。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-157">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="5c4ac-158">ファイルのアップロードで使用されるリソース (ディスク、メモリM) は、同時ファイル アップロードの数とサイズによって異なります。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-158">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="5c4ac-159">アプリであまり多くのアップロードをバッファーに格納しようとすると、メモリまたはディスク領域が不足したときにサイトがクラッシュします。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-159">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="5c4ac-160">ファイルのアップロードのサイズまたは頻度によりアプリのリソースが不足する場合は、ストリーミングを使用します。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-160">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="5c4ac-161">1 つで 64 KB を超えるバッファー ファイルは、メモリからディスク上の一時ファイルに移動されます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-161">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="5c4ac-162">小さいファイルのバッファーリングについては、後のセクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-162">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="5c4ac-163">物理ストレージ</span><span class="sxs-lookup"><span data-stu-id="5c4ac-163">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* <span data-ttu-id="5c4ac-164">[[データベース]](#upload-small-files-with-buffered-model-binding-to-a-database)</span><span class="sxs-lookup"><span data-stu-id="5c4ac-164">[Database](#upload-small-files-with-buffered-model-binding-to-a-database)</span></span>

<span data-ttu-id="5c4ac-165">**ストリーミング**</span><span class="sxs-lookup"><span data-stu-id="5c4ac-165">**Streaming**</span></span>

<span data-ttu-id="5c4ac-166">ファイルはマルチパート要求から受信され、アプリによって直接処理または保存されます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-166">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="5c4ac-167">ストリーミングによってパフォーマンスが大幅に向上することはありません。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-167">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="5c4ac-168">ストリーミングを使用すると、ファイルをアップロードするときのメモリまたはディスク領域の需要を減らすことができます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-168">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="5c4ac-169">大きいファイルのストリーミングについては、「[ストリーミングを使用して大きいファイルをアップロードする](#upload-large-files-with-streaming)」で説明します。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-169">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="5c4ac-170">バッファー モデル バインドを使用して小さいファイルを物理ストレージにアップロードする</span><span class="sxs-lookup"><span data-stu-id="5c4ac-170">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="5c4ac-171">小さいファイルをアップロードするには、マルチパート形式を使用するか、または JavaScript を使用して POST 要求を作成します。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-171">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="5c4ac-172">次の例では、ページフォームを使用して Razor 1 つのファイルをアップロードします (サンプルアプリの*Pages/BufferedSingleFileUploadPhysical* )。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-172">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
            <span asp-validation-for="FileUpload.FormFile"></span>
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload" />
</form>
```

<span data-ttu-id="5c4ac-173">次の例は前の例と似ていますが、以下の点が異なります。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-173">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="5c4ac-174">JavaScript の ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) を使用して、フォームのデータを送信します。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-174">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="5c4ac-175">検証は行われません。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-175">There's no validation.</span></span>

```cshtml
<form action="BufferedSingleFileUploadPhysical/?handler=Upload" 
      enctype="multipart/form-data" onsubmit="AJAXSubmit(this);return false;" 
      method="post">
    <dl>
        <dt>
            <label for="FileUpload_FormFile">File</label>
        </dt>
        <dd>
            <input id="FileUpload_FormFile" type="file" 
                name="FileUpload.FormFile" />
        </dd>
    </dl>

    <input class="btn" type="submit" value="Upload" />

    <div style="margin-top:15px">
        <output name="result"></output>
    </div>
</form>

<script>
  async function AJAXSubmit (oFormElement) {
    var resultElement = oFormElement.elements.namedItem("result");
    const formData = new FormData(oFormElement);

    try {
    const response = await fetch(oFormElement.action, {
      method: 'POST',
      body: formData
    });

    if (response.ok) {
      window.location.href = '/';
    }

    resultElement.value = 'Result: ' + response.status + ' ' + 
      response.statusText;
    } catch (error) {
      console.error('Error:', error);
    }
  }
</script>
```

<span data-ttu-id="5c4ac-176">[Fetch API がサポートされていない](https://caniuse.com/#feat=fetch)クライアントに対して JavaScript でフォーム POST を実行するには、次のいずれかの方法を使用します。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-176">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="5c4ac-177">Fetch Polyfill を使用します (例: [window.fetch polyfill (github/fetch)](https://github.com/github/fetch))。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-177">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="5c4ac-178">`XMLHttpRequest` を使用してください。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-178">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="5c4ac-179">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-179">For example:</span></span>

  ```javascript
  <script>
    "use strict";

    function AJAXSubmit (oFormElement) {
      var oReq = new XMLHttpRequest();
      oReq.onload = function(e) { 
      oFormElement.elements.namedItem("result").value = 
        'Result: ' + this.status + ' ' + this.statusText;
      };
      oReq.open("post", oFormElement.action);
      oReq.send(new FormData(oFormElement));
    }
  </script>
  ```

<span data-ttu-id="5c4ac-180">ファイルのアップロードをサポートするには、HTML フォームで `multipart/form-data` のエンコード タイプ (`enctype`) を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-180">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="5c4ac-181">`files` 入力要素で複数のファイルのアップロードをサポートするには、`<input>` 要素で `multiple` 属性を指定します。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-181">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="5c4ac-182">サーバーにアップロードされた個々のファイルには、<xref:Microsoft.AspNetCore.Http.IFormFile> を使用して[モデル バインド](xref:mvc/models/model-binding)でアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-182">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="5c4ac-183">サンプル アプリでは、データベースおよび物理ストレージのシナリオでの複数のバッファー ファイル アップロードが示されています。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-183">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename"></a>

> [!WARNING]
> <span data-ttu-id="5c4ac-184"><xref:Microsoft.AspNetCore.Http.IFormFile> の `FileName` プロパティは、表示とログ記録の目的以外に使用**しないでください**。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-184">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="5c4ac-185">表示またはログ記録を行うときに、ファイル名を HTML エンコードします。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-185">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="5c4ac-186">攻撃者は、完全パスまたは相対パスを含む悪意のあるファイル名を提供することがあります。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-186">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="5c4ac-187">アプリケーションで次の処理を行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-187">Applications should:</span></span>
>
> * <span data-ttu-id="5c4ac-188">ユーザーが指定したファイル名からパスを削除します。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-188">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="5c4ac-189">UI またはログ記録のために、HTML エンコードされ、パスが削除されたファイル名を保存します。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-189">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="5c4ac-190">ストレージ用に新しいランダムなファイル名を生成します。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-190">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="5c4ac-191">次のコードでは、ファイル名からパスを削除します。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-191">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="5c4ac-192">これまでに示した例では、セキュリティ上の考慮事項については考えられていません。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-192">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="5c4ac-193">以下のセクションおよび[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)で、追加の情報が提供されています。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-193">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="5c4ac-194">セキュリティの考慮事項</span><span class="sxs-lookup"><span data-stu-id="5c4ac-194">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="5c4ac-195">検証</span><span class="sxs-lookup"><span data-stu-id="5c4ac-195">Validation</span></span>](#validation)

<span data-ttu-id="5c4ac-196">モデル バインドと <xref:Microsoft.AspNetCore.Http.IFormFile> を使用してファイルをアップロードする場合、アクション メソッドでは以下を受け入れることができます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-196">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="5c4ac-197">1つの <xref:Microsoft.AspNetCore.Http.IFormFile>。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-197">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="5c4ac-198">複数のファイルを表す次のいずれかのコレクション:</span><span class="sxs-lookup"><span data-stu-id="5c4ac-198">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="5c4ac-199">[表](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="5c4ac-199">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="5c4ac-200">バインドでは、名前でフォーム ファイルが照合されます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-200">Binding matches form files by name.</span></span> <span data-ttu-id="5c4ac-201">たとえば、HTML の `<input type="file" name="formFile">` の `name` の値は、バインドされた C# のパラメーター/プロパティと一致する必要があります (`FormFile`)。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-201">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="5c4ac-202">詳細については、「[name 属性の値を POST メソッドのパラメーター名に一致させる](#match-name-attribute-value-to-parameter-name-of-post-method)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-202">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="5c4ac-203">次のような例です。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-203">The following example:</span></span>

* <span data-ttu-id="5c4ac-204">アップロードされた 1 つ以上のファイルをループします。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-204">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="5c4ac-205">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) 使用して、ファイル名を含むファイルの完全なパスを返します。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-205">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="5c4ac-206">アプリによって生成されたファイル名を使用して、ローカル ファイル システムにファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-206">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="5c4ac-207">アップロードされたファイルの合計数とサイズを返します。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-207">Returns the total number and size of files uploaded.</span></span>

```csharp
public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> files)
{
    long size = files.Sum(f => f.Length);

    foreach (var formFile in files)
    {
        if (formFile.Length > 0)
        {
            var filePath = Path.GetTempFileName();

            using (var stream = System.IO.File.Create(filePath))
            {
                await formFile.CopyToAsync(stream);
            }
        }
    }

    // Process uploaded files
    // Don't rely on or trust the FileName property without validation.

    return Ok(new { count = files.Count, size });
}
```

<span data-ttu-id="5c4ac-208">パスを除いてファイル名を生成するには、`Path.GetRandomFileName` を使用します。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-208">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="5c4ac-209">次の例では、構成からパスを取得します。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-209">In the following example, the path is obtained from configuration:</span></span>

```csharp
foreach (var formFile in files)
{
    if (formFile.Length > 0)
    {
        var filePath = Path.Combine(_config["StoredFilesPath"], 
            Path.GetRandomFileName());

        using (var stream = System.IO.File.Create(filePath))
        {
            await formFile.CopyToAsync(stream);
        }
    }
}
```

<span data-ttu-id="5c4ac-210"> に渡すパスには、ファイル名が含まれている "<xref:System.IO.FileStream> *必要があります*"。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-210">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="5c4ac-211">ファイル名を指定しないと、実行時に <xref:System.UnauthorizedAccessException> がスローされます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-211">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="5c4ac-212"><xref:Microsoft.AspNetCore.Http.IFormFile> の方法を使用してアップロードされたファイルは、処理の前に、サーバー上のメモリまたはディスクのバッファーに格納されます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-212">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="5c4ac-213">アクション メソッド内では、<xref:Microsoft.AspNetCore.Http.IFormFile> の内容には <xref:System.IO.Stream> としてアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-213">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="5c4ac-214">ローカル ファイル システムに加えて、ネットワーク共有またはファイル ストレージ サービス ([Azure Blob Storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs) など) にファイルを保存することができます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-214">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="5c4ac-215">アップロードのために複数のファイルをループし、安全なファイル名を使用する別の例については、サンプル アプリの *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-215">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="5c4ac-216">以前の一時ファイルを削除せずに、65,535 個より多くのファイルを作成すると、[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) で <xref:System.IO.IOException> がスローされます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-216">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="5c4ac-217">65,535 ファイルの制限は、サーバーごとの制限です。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-217">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="5c4ac-218">Windows OS でのこの制限の詳細については、次のトピックの「解説」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-218">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="5c4ac-219">GetTempFileNameA 関数</span><span class="sxs-lookup"><span data-stu-id="5c4ac-219">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="5c4ac-220">バッファー モデル バインドを使用して小さいファイルをデータベースにアップロードする</span><span class="sxs-lookup"><span data-stu-id="5c4ac-220">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="5c4ac-221">[Entity Framework](/ef/core/index) を使用してデータベースにバイナリ ファイル データを格納するには、エンティティで <xref:System.Byte> 配列プロパティを定義します。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-221">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="5c4ac-222"><xref:Microsoft.AspNetCore.Http.IFormFile> が含まれるクラスに対してページ モデル プロパティを指定します。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-222">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

```csharp
public class BufferedSingleFileUploadDbModel : PageModel
{
    ...

    [BindProperty]
    public BufferedSingleFileUploadDb FileUpload { get; set; }

    ...
}

public class BufferedSingleFileUploadDb
{
    [Required]
    [Display(Name="File")]
    public IFormFile FormFile { get; set; }
}
```

> [!NOTE]
> <span data-ttu-id="5c4ac-223"><xref:Microsoft.AspNetCore.Http.IFormFile> は、アクション メソッドのパラメーターとして直接、またはバインドされたモデル プロパティとして、使用することができます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-223"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="5c4ac-224">前の例では、バインドされたモデル プロパティが使用されています。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-224">The prior example uses a bound model property.</span></span>

<span data-ttu-id="5c4ac-225">は、 `FileUpload` ページ形式で使用され Razor ます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-225">The `FileUpload` is used in the Razor Pages form:</span></span>

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload">
</form>
```

<span data-ttu-id="5c4ac-226">フォームがサーバーに POST されるときに、<xref:Microsoft.AspNetCore.Http.IFormFile> をストリームにコピーし、バイト配列としてデータベースに保存します。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-226">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="5c4ac-227">次の例では、`_dbContext` によってアプリのデータベース コンテキストが格納されます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-227">In the following example, `_dbContext` stores the app's database context:</span></span>

```csharp
public async Task<IActionResult> OnPostUploadAsync()
{
    using (var memoryStream = new MemoryStream())
    {
        await FileUpload.FormFile.CopyToAsync(memoryStream);

        // Upload the file if less than 2 MB
        if (memoryStream.Length < 2097152)
        {
            var file = new AppFile()
            {
                Content = memoryStream.ToArray()
            };

            _dbContext.File.Add(file);

            await _dbContext.SaveChangesAsync();
        }
        else
        {
            ModelState.AddModelError("File", "The file is too large.");
        }
    }

    return Page();
}
```

<span data-ttu-id="5c4ac-228">前の例は、次のサンプル アプリで示されているシナリオに似ています。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-228">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="5c4ac-229">*Pages/BufferedSingleFileUploadDb.cshtml*</span><span class="sxs-lookup"><span data-stu-id="5c4ac-229">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="5c4ac-230">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="5c4ac-230">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="5c4ac-231">パフォーマンスに悪影響を与える可能性があるため、リレーショナル データベースにバイナリ データを格納する場合は注意してください。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-231">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="5c4ac-232">検証を行わずに <xref:Microsoft.AspNetCore.Http.IFormFile> の `FileName` プロパティに依存したり、信頼したりしないでください。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-232">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="5c4ac-233">`FileName` プロパティは、表示目的でのみ、HTML エンコードした後でだけ、使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-233">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="5c4ac-234">示した例では、セキュリティ上の考慮事項については考えられていません。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-234">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="5c4ac-235">以下のセクションおよび[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)で、追加の情報が提供されています。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-235">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="5c4ac-236">セキュリティの考慮事項</span><span class="sxs-lookup"><span data-stu-id="5c4ac-236">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="5c4ac-237">検証</span><span class="sxs-lookup"><span data-stu-id="5c4ac-237">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="5c4ac-238">ストリーミングを使用して大きいファイルをアップロードする</span><span class="sxs-lookup"><span data-stu-id="5c4ac-238">Upload large files with streaming</span></span>

<span data-ttu-id="5c4ac-239">次の例では、JavaScript を使用してファイルをコントローラーのアクションにストリーミングする方法を示します。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-239">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="5c4ac-240">ファイルの偽造防止トークンは、カスタム フィルター属性を使用して生成され、要求本文ではなくクライアント HTTP ヘッダーに渡されます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-240">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="5c4ac-241">アクション メソッドではアップロードされたデータが直接処理されるため、フォーム モデル バインドは別のカスタム フィルターでは無効になります。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-241">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="5c4ac-242">アクション内では、フォームのコンテンツが `MultipartReader` を使用して読み取られます。その場合、各 `MultipartSection` が読み取られ、必要に応じて、ファイルが処理されるかコンテンツが格納されます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-242">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="5c4ac-243">マルチパート セクションが読み取られた後、アクションで独自のモデル バインドが実行されます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-243">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="5c4ac-244">最初のページ応答では、フォームが読み込まれ、(属性を使用して) にアンチ偽造トークンが保存され cookie `GenerateAntiforgeryTokenCookieAttribute` ます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-244">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="5c4ac-245">属性は、ASP.NET Core の組み込みの [アンチ偽造サポート](xref:security/anti-request-forgery) を使用して、要求トークンを使用してを設定し cookie ます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-245">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="5c4ac-246">`DisableFormValueModelBindingAttribute` は、モデル バインドを無効にするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-246">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="5c4ac-247">このサンプルアプリで `GenerateAntiforgeryTokenCookieAttribute` は、とは、ページ `DisableFormValueModelBindingAttribute` `/StreamedSingleFileUploadDb` `/StreamedSingleFileUploadPhysical` `Startup.ConfigureServices` [ Razor 表記規則](xref:razor-pages/razor-pages-conventions)を使用するとのページアプリケーションモデルにフィルターとして適用されます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-247">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_AddRazorPages&highlight=7-10,16-19)]

<span data-ttu-id="5c4ac-248">モデル バインドではフォームが読み取られないため、フォームからバインドされているパラメーターはバインドされません (クエリ、ルート、ヘッダーは引き続き機能します)。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-248">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="5c4ac-249">アクション メソッドでは、`Request` プロパティが直接操作されます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-249">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="5c4ac-250">`MultipartReader` は各セクションを読み取るために使用されます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-250">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="5c4ac-251">キー/値データは `KeyValueAccumulator` に格納されます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-251">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="5c4ac-252">マルチパート セクションが読み取られた後、`KeyValueAccumulator` の内容を使用して、フォーム データがモデル タイプにバインドされます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-252">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="5c4ac-253">EF Core でデータベースにストリーミングするための完全な `StreamingController.UploadDatabase` メソッド:</span><span class="sxs-lookup"><span data-stu-id="5c4ac-253">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="5c4ac-254">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span><span class="sxs-lookup"><span data-stu-id="5c4ac-254">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="5c4ac-255">物理的な場所にストリーミングするための完全な `StreamingController.UploadPhysical` メソッド:</span><span class="sxs-lookup"><span data-stu-id="5c4ac-255">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="5c4ac-256">サンプル アプリでは、検証チェックは `FileHelpers.ProcessStreamedFile` によって処理されます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-256">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="5c4ac-257">検証</span><span class="sxs-lookup"><span data-stu-id="5c4ac-257">Validation</span></span>

<span data-ttu-id="5c4ac-258">サンプル アプリの `FileHelpers` クラスでは、バッファーリングされた <xref:Microsoft.AspNetCore.Http.IFormFile> とストリーミングされたファイルのアップロードに関するいくつかのチェックが示されています。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-258">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="5c4ac-259">サンプル アプリでのバッファーリングされたファイルのアップロード <xref:Microsoft.AspNetCore.Http.IFormFile> の処理については、*Utilities/FileHelpers.cs* ファイルの `ProcessFormFile` メソッドを参照してください。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-259">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="5c4ac-260">ストリーミングされたファイルの処理については、同じファイルの `ProcessStreamedFile` メソッドを参照してください。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-260">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="5c4ac-261">サンプル アプリで示されている検証処理メソッドでは、アップロードされたファイルの内容はスキャンされません。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-261">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="5c4ac-262">ほとんどの運用シナリオでは、ファイルをユーザーまたは他のシステムで使用できるようにする前に、ウイルス/マルウェア スキャナー API が使用されます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-262">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="5c4ac-263">このトピックのサンプルでは検証技法の実際の例が示されていますが、次の場合を除き、運用アプリでは `FileHelpers` クラスを実装しないでください。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-263">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="5c4ac-264">実装を完全に理解している。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-264">Fully understand the implementation.</span></span>
> * <span data-ttu-id="5c4ac-265">アプリの環境と仕様に合わせて実装が適切に変更されている。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-265">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="5c4ac-266">**これらの要件に対応することなく、アプリにセキュリティ コードをむやみに実装しないでください。**</span><span class="sxs-lookup"><span data-stu-id="5c4ac-266">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="5c4ac-267">コンテンツの検証</span><span class="sxs-lookup"><span data-stu-id="5c4ac-267">Content validation</span></span>

<span data-ttu-id="5c4ac-268">**アップロードされた内容に対してサードパーティ製のウイルス/マルウェア スキャン API を使用します。**</span><span class="sxs-lookup"><span data-stu-id="5c4ac-268">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="5c4ac-269">大容量のシナリオでは、ファイルのスキャンに大量のサーバー リソースが必要です。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-269">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="5c4ac-270">ファイルのスキャンによって要求の処理パフォーマンスが低下する場合は、スキャン処理を[バックグラウンド サービス](xref:fundamentals/host/hosted-services)にオフロードすることを検討してください (たとえば、アプリのサーバーとは異なるサーバーで実行されているサービス)。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-270">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="5c4ac-271">通常、アップロードされたファイルは、バックグラウンドのウイルス検索プログラムによってチェックされるまで、検疫された領域に保持されます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-271">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="5c4ac-272">合格したファイルは、通常のファイル ストレージの場所に移動されます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-272">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="5c4ac-273">これらの手順は、通常、ファイルのスキャン状態を示すデータベース レコードと共に実行されます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-273">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="5c4ac-274">このような方法を使用することで、アプリとアプリ サーバーは要求への応答に集中できます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-274">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="5c4ac-275">ファイル拡張子の検証</span><span class="sxs-lookup"><span data-stu-id="5c4ac-275">File extension validation</span></span>

<span data-ttu-id="5c4ac-276">アップロードされたファイルの拡張子を、許可されている拡張子のリストで確認する必要があります。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-276">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="5c4ac-277">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-277">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="5c4ac-278">ファイルのシグネチャの検証</span><span class="sxs-lookup"><span data-stu-id="5c4ac-278">File signature validation</span></span>

<span data-ttu-id="5c4ac-279">ファイルのシグネチャは、ファイルの先頭にある最初の数バイトによって決定されます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-279">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="5c4ac-280">これらのバイトを使用して、拡張子がファイルの内容と一致するかどうかを示すことができます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-280">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="5c4ac-281">サンプル アプリでは、いくつかの一般的なファイルの種類についてファイルのシグネチャがチェックされています。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-281">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="5c4ac-282">次の例では、JPEG イメージのファイルのシグネチャが、ファイルに対してチェックされています。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-282">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

```csharp
private static readonly Dictionary<string, List<byte[]>> _fileSignature = 
    new Dictionary<string, List<byte[]>>
{
    { ".jpeg", new List<byte[]>
        {
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE2 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE3 },
        }
    },
};

using (var reader = new BinaryReader(uploadedFileData))
{
    var signatures = _fileSignature[ext];
    var headerBytes = reader.ReadBytes(signatures.Max(m => m.Length));
    
    return signatures.Any(signature => 
        headerBytes.Take(signature.Length).SequenceEqual(signature));
}
```

<span data-ttu-id="5c4ac-283">追加のファイル シグネチャを取得するには、「[ファイル シグネチャ データベース](https://www.filesignatures.net/)」および公式なファイルの仕様を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-283">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="5c4ac-284">ファイル名のセキュリティ</span><span class="sxs-lookup"><span data-stu-id="5c4ac-284">File name security</span></span>

<span data-ttu-id="5c4ac-285">ファイルを物理ストレージに保存する場合は、クライアント指定のファイル名を使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-285">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="5c4ac-286">[Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) または [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) を使用して、ファイルの安全なファイル名を作成し、一時ストレージの完全なパス (ファイル名を含む) を作成します。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-286">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

<span data-ttu-id="5c4ac-287">Razor プロパティ値が自動的に HTML エンコードされて表示されます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-287">Razor automatically HTML encodes property values for display.</span></span> <span data-ttu-id="5c4ac-288">次のコードは安全に使用できます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-288">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="5c4ac-289">の外部 Razor では、常に <xref:System.Net.WebUtility.HtmlEncode*> ユーザーの要求の内容をファイル名で指定します。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-289">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="5c4ac-290">多くの実装には、ファイルが存在するかどうかのチェックを含める必要があります。そうしないと、同じ名前のファイルによってファイルが上書きされます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-290">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="5c4ac-291">アプリの仕様を満たす追加のロジックを提供します。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-291">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="5c4ac-292">サイズの検証</span><span class="sxs-lookup"><span data-stu-id="5c4ac-292">Size validation</span></span>

<span data-ttu-id="5c4ac-293">アップロードされるファイルのサイズを制限します。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-293">Limit the size of uploaded files.</span></span>

<span data-ttu-id="5c4ac-294">サンプル アプリでは、ファイルのサイズは 2 MB (バイト単位) に制限されています。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-294">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="5c4ac-295">その制限は、*appsettings.json* ファイルの [Configuration](xref:fundamentals/configuration/index) によって提供されます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-295">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="5c4ac-296">`FileSizeLimit` が `PageModel` クラスに挿入されます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-296">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

```csharp
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    private readonly long _fileSizeLimit;

    public BufferedSingleFileUploadPhysicalModel(IConfiguration config)
    {
        _fileSizeLimit = config.GetValue<long>("FileSizeLimit");
    }

    ...
}
```

<span data-ttu-id="5c4ac-297">ファイルのサイズが制限を超えると、ファイルは拒否されます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-297">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="5c4ac-298">name 属性の値を POST メソッドのパラメーター名に一致させる</span><span class="sxs-lookup"><span data-stu-id="5c4ac-298">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="5c4ac-299">フォームデータを Razor ポストするか、JavaScript の直接を使用する非フォームでは `FormData` 、フォームの要素で指定された名前、または `FormData` コントローラーのアクション内のパラメーターの名前と一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-299">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="5c4ac-300">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-300">In the following example:</span></span>

* <span data-ttu-id="5c4ac-301">`<input>` 要素を使用すると、`name` 属性には値 `battlePlans` が設定されます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-301">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="5c4ac-302">JavaScript で `FormData` を使用すると、name には値 `battlePlans` が設定されます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-302">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="5c4ac-303">C# メソッドのパラメーターに一致する名前を使用します (`battlePlans`)。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-303">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="5c4ac-304">Razorという名前のページページハンドラーメソッドの場合 `Upload` :</span><span class="sxs-lookup"><span data-stu-id="5c4ac-304">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="5c4ac-305">MVC POST コントローラー アクション メソッドの場合:</span><span class="sxs-lookup"><span data-stu-id="5c4ac-305">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="5c4ac-306">サーバーとアプリの構成</span><span class="sxs-lookup"><span data-stu-id="5c4ac-306">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="5c4ac-307">マルチパート本文の長さの制限</span><span class="sxs-lookup"><span data-stu-id="5c4ac-307">Multipart body length limit</span></span>

<span data-ttu-id="5c4ac-308"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> では、各マルチパート本文の長さの制限が設定されます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-308"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="5c4ac-309">この制限を超えるフォーム セクションでは、解析時に <xref:System.IO.InvalidDataException> がスローされます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-309">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="5c4ac-310">既定値は 134,217,728 (128 MB) です。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-310">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="5c4ac-311">制限をカスタマイズするには、`Startup.ConfigureServices` の設定 <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> を使用します。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-311">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<FormOptions>(options =>
    {
        // Set the limit to 256 MB
        options.MultipartBodyLengthLimit = 268435456;
    });
}
```

<span data-ttu-id="5c4ac-312">単一ページまたはアクションに対する <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> を設定するには、<xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> を使用します。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-312"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="5c4ac-313">RazorPages アプリで、で[規則](xref:razor-pages/razor-pages-conventions)を適用したフィルターを適用し `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-313">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddRazorPages(options =>
{
    options.Conventions
        .AddPageApplicationModelConvention("/FileUploadPage",
            model.Filters.Add(
                new RequestFormLimitsAttribute()
                {
                    // Set the limit to 256 MB
                    MultipartBodyLengthLimit = 268435456
                });
});
```

<span data-ttu-id="5c4ac-314">Razorページアプリまたは MVC アプリで、ページモデルまたはアクションメソッドにフィルターを適用します。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-314">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="5c4ac-315">Kestrel の最大要求本文サイズ</span><span class="sxs-lookup"><span data-stu-id="5c4ac-315">Kestrel maximum request body size</span></span>

<span data-ttu-id="5c4ac-316">Kestrel によってホストされるアプリの場合、既定の要求本文の最大サイズは、30,000,000 バイトです。これは約 28.6 MB になります。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-316">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="5c4ac-317">制限をカスタマイズするには、[MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel サーバー オプションを使用します。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-317">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel((context, options) =>
            {
                // Handle requests up to 50 MB
                options.Limits.MaxRequestBodySize = 52428800;
            })
            .UseStartup<Startup>();
        });
```

<span data-ttu-id="5c4ac-318">単一ページまたはアクションに対する [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) を設定するには、<xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> を使用します。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-318"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="5c4ac-319">RazorPages アプリで、で[規則](xref:razor-pages/razor-pages-conventions)を適用したフィルターを適用し `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-319">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddRazorPages(options =>
{
    options.Conventions
        .AddPageApplicationModelConvention("/FileUploadPage",
            model =>
            {
                // Handle requests up to 50 MB
                model.Filters.Add(
                    new RequestSizeLimitAttribute(52428800));
            });
});
```

<span data-ttu-id="5c4ac-320">Razorページアプリまたは MVC アプリで、フィルターをページハンドラークラスまたはアクションメソッドに適用します。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-320">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

<span data-ttu-id="5c4ac-321">は、 `RequestSizeLimitAttribute` ディレクティブを使用して適用することもでき [`@attribute`](xref:mvc/views/razor#attribute) Razor ます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-321">The `RequestSizeLimitAttribute` can also be applied using the [`@attribute`](xref:mvc/views/razor#attribute) Razor directive:</span></span>

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="5c4ac-322">その他の Kestrel の制限</span><span class="sxs-lookup"><span data-stu-id="5c4ac-322">Other Kestrel limits</span></span>

<span data-ttu-id="5c4ac-323">Kestrel によってホストされるアプリには、他の Kestrel の制限が適用される場合があります。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-323">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="5c4ac-324">クライアントの最大接続数</span><span class="sxs-lookup"><span data-stu-id="5c4ac-324">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="5c4ac-325">要求と応答のデータ レート</span><span class="sxs-lookup"><span data-stu-id="5c4ac-325">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a><span data-ttu-id="5c4ac-326">IIS の内容の長さの制限</span><span class="sxs-lookup"><span data-stu-id="5c4ac-326">IIS content length limit</span></span>

<span data-ttu-id="5c4ac-327">既定の要求の制限 (`maxAllowedContentLength`) は、30,000,000 バイトです。これは約 28.6 MB になります。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-327">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6MB.</span></span> <span data-ttu-id="5c4ac-328">*web.config* ファイルで制限をカスタマイズします。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-328">Customize the limit in the *web.config* file:</span></span>

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <!-- Handle requests up to 50 MB -->
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

<span data-ttu-id="5c4ac-329">この設定は IIS にのみ適用されます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-329">This setting only applies to IIS.</span></span> <span data-ttu-id="5c4ac-330">Kestrel でホストする場合、既定ではこの動作は発生しません。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-330">The behavior doesn't occur by default when hosting on Kestrel.</span></span> <span data-ttu-id="5c4ac-331">詳細については、「[要求の制限 \<requestLimits> ](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-331">For more information, see [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="5c4ac-332">ASP.NET Core モジュールでの制限または IIS 要求フィルター モジュールの存在により、アップロードが 2 または 4 GB に制限される場合があります。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-332">Limitations in the ASP.NET Core Module or presence of the IIS Request Filtering Module may limit uploads to either 2 or 4 GB.</span></span> <span data-ttu-id="5c4ac-333">詳細については、「[サイズが 2 GB を超えるファイルをアップロードできない (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-333">For more information, see [Unable to upload file greater than 2GB in size (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="5c4ac-334">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="5c4ac-334">Troubleshoot</span></span>

<span data-ttu-id="5c4ac-335">ファイルのアップロード時に発生する一般的ないくつかの問題と、考えられる解決策を以下に示します。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-335">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="5c4ac-336">IIS サーバーに展開したときの "見つかりません" エラー</span><span class="sxs-lookup"><span data-stu-id="5c4ac-336">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="5c4ac-337">次のエラーは、アップロードされるファイルがサーバーの構成されている内容の長さを超えていることを示します。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-337">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="5c4ac-338">制限を増やす方法の詳細については、「[IIS の内容の長さの制限](#iis-content-length-limit)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-338">For more information on increasing the limit, see the [IIS content length limit](#iis-content-length-limit) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="5c4ac-339">接続エラー</span><span class="sxs-lookup"><span data-stu-id="5c4ac-339">Connection failure</span></span>

<span data-ttu-id="5c4ac-340">接続エラーおよびサーバー接続のリセットは、アップロードされるファイルが Kestrel の最大要求本文サイズを超えていることを示している場合があります。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-340">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="5c4ac-341">詳細については、[Kestrel の最大要求本文サイズ](#kestrel-maximum-request-body-size)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-341">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="5c4ac-342">Kestrel クライアント接続の制限の調整も必要な場合があります。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-342">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="5c4ac-343">IFormFile での null 参照例外</span><span class="sxs-lookup"><span data-stu-id="5c4ac-343">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="5c4ac-344">コントローラーが <xref:Microsoft.AspNetCore.Http.IFormFile> を使用してアップロードされたファイルを受け取っても、値が `null` の場合は、HTML フォームで `multipart/form-data` に値 `enctype` が指定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-344">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="5c4ac-345">この属性が `<form>` 要素で設定されていない場合、ファイルはアップロードされず、バインドされた <xref:Microsoft.AspNetCore.Http.IFormFile> 引数は `null` になります。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-345">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="5c4ac-346">また、[フォーム データでのアップロードの名前がアプリの名前と一致する](#match-name-attribute-value-to-parameter-name-of-post-method)ことを確認します。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-346">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="5c4ac-347">ストリームが長すぎる</span><span class="sxs-lookup"><span data-stu-id="5c4ac-347">Stream was too long</span></span>

<span data-ttu-id="5c4ac-348">このトピックの例は、アップロードされたファイル コンテンツを保持するために <xref:System.IO.MemoryStream> に依存しています。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-348">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="5c4ac-349">`int.MaxValue` のサイズ制限は `MemoryStream` です。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-349">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="5c4ac-350">アプリのファイル アップロード シナリオで 50 MB を超えるファイル コンテンツを保持する必要がある場合は、アップロードされたファイルのコンテンツを 1 つの `MemoryStream` に依存することなく保持する別のアプローチを使用します。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-350">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5c4ac-351">ASP.NET Core では、小さいファイルの場合はバッファー モデル バインドを使用し、大きいファイルの場合は非バッファー ストリーミングを使用して、1 つ以上のファイルのアップロードがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-351">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="5c4ac-352">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-352">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="5c4ac-353">セキュリティの考慮事項</span><span class="sxs-lookup"><span data-stu-id="5c4ac-353">Security considerations</span></span>

<span data-ttu-id="5c4ac-354">サーバーにファイルをアップロードする機能をユーザーに提供するときは、十分に注意してください。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-354">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="5c4ac-355">攻撃者が次のようなことを試みる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-355">Attackers may attempt to:</span></span>

* <span data-ttu-id="5c4ac-356">[サービス拒否](/windows-hardware/drivers/ifs/denial-of-service)攻撃を実行する。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-356">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="5c4ac-357">ウイルスまたはマルウェアをアップロードする。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-357">Upload viruses or malware.</span></span>
* <span data-ttu-id="5c4ac-358">ネットワークやサーバーを他の方法で侵害する。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-358">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="5c4ac-359">攻撃の成功の可能性を少なくするセキュリティ手順は、次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-359">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="5c4ac-360">専用のファイル アップロード領域 (できれば、システム ドライブ以外) にファイルをアップロードします。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-360">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="5c4ac-361">専用の場所を使用すると、アップロードされるファイルにセキュリティ制限を適用しやすくなります。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-361">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="5c4ac-362">ファイルのアップロード場所に対する実行アクセス許可を無効にします。&dagger;</span><span class="sxs-lookup"><span data-stu-id="5c4ac-362">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="5c4ac-363">アプリと同じディレクトリ ツリーに、アップロードしたファイルを保持**しないでください**。&dagger;</span><span class="sxs-lookup"><span data-stu-id="5c4ac-363">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="5c4ac-364">アプリによって決められた安全なファイル名を使用します。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-364">Use a safe file name determined by the app.</span></span> <span data-ttu-id="5c4ac-365">ユーザーが指定したファイル名や、アップロードしたファイルの信頼されていないファイル名を使用しないでください。 &dagger; HTML は、表示時に信頼されていないファイル名をエンコードします。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-365">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="5c4ac-366">たとえば、ファイル名をログに記録したり、UI に表示したりし Razor ます (出力を自動的に HTML エンコードします)。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-366">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="5c4ac-367">アプリの設計仕様に対して承認されているファイル拡張子のみを許可します。&dagger;</span><span class="sxs-lookup"><span data-stu-id="5c4ac-367">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="5c4ac-368">クライアント側のチェックがサーバーで実行されていることを確認します。 &dagger; クライアント側のチェックは簡単に回避できます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-368">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="5c4ac-369">アップロードされたファイルのサイズをチェックします。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-369">Check the size of an uploaded file.</span></span> <span data-ttu-id="5c4ac-370">サイズの大きなアップロードを防ぐために、最大サイズ制限を設定します。&dagger;</span><span class="sxs-lookup"><span data-stu-id="5c4ac-370">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="5c4ac-371">同じ名前でアップロードされたファイルによってファイルが上書きされないようにする必要があるときは、ファイルをアップロードする前に、データベースまたは物理ストレージに対してファイル名を確認します。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-371">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="5c4ac-372">**ファイルを格納する前に、アップロードされる内容に対してウイルス/マルウェア スキャナーを実行します。**</span><span class="sxs-lookup"><span data-stu-id="5c4ac-372">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="5c4ac-373">&dagger;サンプル アプリで、条件を満たす方法が示されています。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-373">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="5c4ac-374">システムへの悪意のあるコードのアップロードは、頻繁に次のような内容のコードの実行するための足がかりとなります。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-374">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="5c4ac-375">システムの制御を完全に掌握する。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-375">Completely gain control of a system.</span></span>
> * <span data-ttu-id="5c4ac-376">システムがクラッシュする結果で、システムを過負荷状態にする。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-376">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="5c4ac-377">ユーザーまたはシステムのデータを破壊する。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-377">Compromise user or system data.</span></span>
> * <span data-ttu-id="5c4ac-378">パブリック UI に落書きする。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-378">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="5c4ac-379">ユーザーからファイルを受け入れる際の外部アクセスによる攻撃を減らす方法については、次の資料を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-379">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * [<span data-ttu-id="5c4ac-380">Unrestricted File Upload (ファイルの無制限のアップロード)</span><span class="sxs-lookup"><span data-stu-id="5c4ac-380">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [<span data-ttu-id="5c4ac-381">Azure セキュリティ: ユーザーから受け入れるファイルに適切な管理制御を整備する</span><span class="sxs-lookup"><span data-stu-id="5c4ac-381">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="5c4ac-382">サンプル アプリの例など、セキュリティ対策の実装の詳細については、「[検証](#validation)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-382">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="5c4ac-383">ストレージのシナリオ</span><span class="sxs-lookup"><span data-stu-id="5c4ac-383">Storage scenarios</span></span>

<span data-ttu-id="5c4ac-384">ファイルの一般的なストレージ オプションには次のようなものがあります。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-384">Common storage options for files include:</span></span>

* <span data-ttu-id="5c4ac-385">データベース</span><span class="sxs-lookup"><span data-stu-id="5c4ac-385">Database</span></span>

  * <span data-ttu-id="5c4ac-386">小さいファイルをアップロードする場合、物理ストレージ (ファイル システムまたはネットワーク共有) のオプションよりデータベースの方が速いことがよくあります。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-386">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="5c4ac-387">多くの場合、ユーザー データに対するデータベース レコードの取得でファイルの内容を同時に提供できるため (たとえば、アバター イメージ)、データベースの方が物理的なストレージ オプションより便利です。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-387">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="5c4ac-388">データベースは、データ ストレージ サービスを使用するよりコストが低くなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-388">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="5c4ac-389">物理ストレージ (ファイル システムまたはネットワーク共有)</span><span class="sxs-lookup"><span data-stu-id="5c4ac-389">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="5c4ac-390">大きいファイルのアップロードの場合:</span><span class="sxs-lookup"><span data-stu-id="5c4ac-390">For large file uploads:</span></span>
    * <span data-ttu-id="5c4ac-391">データベースの制限によって、アップロードのサイズが制限される場合があります。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-391">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="5c4ac-392">多くの場合、物理ストレージはデータベース内のストレージより高コストです。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-392">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="5c4ac-393">物理ストレージは、データ ストレージ サービスを使用するよりコストが低くなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-393">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="5c4ac-394">アプリのプロセスには、ストレージの場所に対する読み取りと書き込みのアクセス許可が必要です。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-394">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="5c4ac-395">**実行アクセス許可は付与しないでください。**</span><span class="sxs-lookup"><span data-stu-id="5c4ac-395">**Never grant execute permission.**</span></span>

* <span data-ttu-id="5c4ac-396">データ ストレージ サービス (例: [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span><span class="sxs-lookup"><span data-stu-id="5c4ac-396">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="5c4ac-397">通常、サービスでは、大抵の場合に単一障害点となるオンプレミス ソリューションより高いスケーラビリティと回復性が提供されます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-397">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="5c4ac-398">大規模なストレージ インフラストラクチャのシナリオでは、サービスのコストが低下する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-398">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="5c4ac-399">詳細については、「 [クイックスタート: .net を使用してオブジェクトストレージに blob を作成する](/azure/storage/blobs/storage-quickstart-blobs-dotnet)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-399">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span> <span data-ttu-id="5c4ac-400">そのトピックでは <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*> が示されていますが、<xref:System.IO.Stream> を使用する場合は、<xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> を使用して <xref:System.IO.FileStream> を Blob Storage に保存することもできます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-400">The topic demonstrates <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, but <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> can be used to save a <xref:System.IO.FileStream> to blob storage when working with a <xref:System.IO.Stream>.</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="5c4ac-401">ファイル アップロードのシナリオ</span><span class="sxs-lookup"><span data-stu-id="5c4ac-401">File upload scenarios</span></span>

<span data-ttu-id="5c4ac-402">ファイルをアップロードするための一般的な 2 つの方法は、バッファーリングとストリーミングです。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-402">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="5c4ac-403">**バッファリング**</span><span class="sxs-lookup"><span data-stu-id="5c4ac-403">**Buffering**</span></span>

<span data-ttu-id="5c4ac-404">ファイル全体が <xref:Microsoft.AspNetCore.Http.IFormFile> に読み込まれます。これは、ファイルの処理または保存に使用される C# でのファイルの表現です。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-404">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="5c4ac-405">ファイルのアップロードで使用されるリソース (ディスク、メモリM) は、同時ファイル アップロードの数とサイズによって異なります。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-405">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="5c4ac-406">アプリであまり多くのアップロードをバッファーに格納しようとすると、メモリまたはディスク領域が不足したときにサイトがクラッシュします。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-406">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="5c4ac-407">ファイルのアップロードのサイズまたは頻度によりアプリのリソースが不足する場合は、ストリーミングを使用します。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-407">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="5c4ac-408">1 つで 64 KB を超えるバッファー ファイルは、メモリからディスク上の一時ファイルに移動されます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-408">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="5c4ac-409">小さいファイルのバッファーリングについては、後のセクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-409">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="5c4ac-410">物理ストレージ</span><span class="sxs-lookup"><span data-stu-id="5c4ac-410">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* <span data-ttu-id="5c4ac-411">[[データベース]](#upload-small-files-with-buffered-model-binding-to-a-database)</span><span class="sxs-lookup"><span data-stu-id="5c4ac-411">[Database](#upload-small-files-with-buffered-model-binding-to-a-database)</span></span>

<span data-ttu-id="5c4ac-412">**ストリーミング**</span><span class="sxs-lookup"><span data-stu-id="5c4ac-412">**Streaming**</span></span>

<span data-ttu-id="5c4ac-413">ファイルはマルチパート要求から受信され、アプリによって直接処理または保存されます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-413">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="5c4ac-414">ストリーミングによってパフォーマンスが大幅に向上することはありません。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-414">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="5c4ac-415">ストリーミングを使用すると、ファイルをアップロードするときのメモリまたはディスク領域の需要を減らすことができます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-415">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="5c4ac-416">大きいファイルのストリーミングについては、「[ストリーミングを使用して大きいファイルをアップロードする](#upload-large-files-with-streaming)」で説明します。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-416">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="5c4ac-417">バッファー モデル バインドを使用して小さいファイルを物理ストレージにアップロードする</span><span class="sxs-lookup"><span data-stu-id="5c4ac-417">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="5c4ac-418">小さいファイルをアップロードするには、マルチパート形式を使用するか、または JavaScript を使用して POST 要求を作成します。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-418">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="5c4ac-419">次の例では、ページフォームを使用して Razor 1 つのファイルをアップロードします (サンプルアプリの*Pages/BufferedSingleFileUploadPhysical* )。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-419">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
            <span asp-validation-for="FileUpload.FormFile"></span>
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload" />
</form>
```

<span data-ttu-id="5c4ac-420">次の例は前の例と似ていますが、以下の点が異なります。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-420">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="5c4ac-421">JavaScript の ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) を使用して、フォームのデータを送信します。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-421">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="5c4ac-422">検証は行われません。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-422">There's no validation.</span></span>

```cshtml
<form action="BufferedSingleFileUploadPhysical/?handler=Upload" 
      enctype="multipart/form-data" onsubmit="AJAXSubmit(this);return false;" 
      method="post">
    <dl>
        <dt>
            <label for="FileUpload_FormFile">File</label>
        </dt>
        <dd>
            <input id="FileUpload_FormFile" type="file" 
                name="FileUpload.FormFile" />
        </dd>
    </dl>

    <input class="btn" type="submit" value="Upload" />

    <div style="margin-top:15px">
        <output name="result"></output>
    </div>
</form>

<script>
  async function AJAXSubmit (oFormElement) {
    var resultElement = oFormElement.elements.namedItem("result");
    const formData = new FormData(oFormElement);

    try {
    const response = await fetch(oFormElement.action, {
      method: 'POST',
      body: formData
    });

    if (response.ok) {
      window.location.href = '/';
    }

    resultElement.value = 'Result: ' + response.status + ' ' + 
      response.statusText;
    } catch (error) {
      console.error('Error:', error);
    }
  }
</script>
```

<span data-ttu-id="5c4ac-423">[Fetch API がサポートされていない](https://caniuse.com/#feat=fetch)クライアントに対して JavaScript でフォーム POST を実行するには、次のいずれかの方法を使用します。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-423">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="5c4ac-424">Fetch Polyfill を使用します (例: [window.fetch polyfill (github/fetch)](https://github.com/github/fetch))。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-424">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="5c4ac-425">`XMLHttpRequest` を使用してください。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-425">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="5c4ac-426">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-426">For example:</span></span>

  ```javascript
  <script>
    "use strict";

    function AJAXSubmit (oFormElement) {
      var oReq = new XMLHttpRequest();
      oReq.onload = function(e) { 
      oFormElement.elements.namedItem("result").value = 
        'Result: ' + this.status + ' ' + this.statusText;
      };
      oReq.open("post", oFormElement.action);
      oReq.send(new FormData(oFormElement));
    }
  </script>
  ```

<span data-ttu-id="5c4ac-427">ファイルのアップロードをサポートするには、HTML フォームで `multipart/form-data` のエンコード タイプ (`enctype`) を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-427">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="5c4ac-428">`files` 入力要素で複数のファイルのアップロードをサポートするには、`<input>` 要素で `multiple` 属性を指定します。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-428">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="5c4ac-429">サーバーにアップロードされた個々のファイルには、<xref:Microsoft.AspNetCore.Http.IFormFile> を使用して[モデル バインド](xref:mvc/models/model-binding)でアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-429">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="5c4ac-430">サンプル アプリでは、データベースおよび物理ストレージのシナリオでの複数のバッファー ファイル アップロードが示されています。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-430">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename2"></a>

> [!WARNING]
> <span data-ttu-id="5c4ac-431"><xref:Microsoft.AspNetCore.Http.IFormFile> の `FileName` プロパティは、表示とログ記録の目的以外に使用**しないでください**。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-431">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="5c4ac-432">表示またはログ記録を行うときに、ファイル名を HTML エンコードします。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-432">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="5c4ac-433">攻撃者は、完全パスまたは相対パスを含む悪意のあるファイル名を提供することがあります。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-433">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="5c4ac-434">アプリケーションで次の処理を行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-434">Applications should:</span></span>
>
> * <span data-ttu-id="5c4ac-435">ユーザーが指定したファイル名からパスを削除します。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-435">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="5c4ac-436">UI またはログ記録のために、HTML エンコードされ、パスが削除されたファイル名を保存します。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-436">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="5c4ac-437">ストレージ用に新しいランダムなファイル名を生成します。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-437">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="5c4ac-438">次のコードでは、ファイル名からパスを削除します。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-438">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="5c4ac-439">これまでに示した例では、セキュリティ上の考慮事項については考えられていません。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-439">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="5c4ac-440">以下のセクションおよび[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)で、追加の情報が提供されています。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-440">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="5c4ac-441">セキュリティの考慮事項</span><span class="sxs-lookup"><span data-stu-id="5c4ac-441">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="5c4ac-442">検証</span><span class="sxs-lookup"><span data-stu-id="5c4ac-442">Validation</span></span>](#validation)

<span data-ttu-id="5c4ac-443">モデル バインドと <xref:Microsoft.AspNetCore.Http.IFormFile> を使用してファイルをアップロードする場合、アクション メソッドでは以下を受け入れることができます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-443">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="5c4ac-444">1つの <xref:Microsoft.AspNetCore.Http.IFormFile>。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-444">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="5c4ac-445">複数のファイルを表す次のいずれかのコレクション:</span><span class="sxs-lookup"><span data-stu-id="5c4ac-445">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="5c4ac-446">[表](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="5c4ac-446">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="5c4ac-447">バインドでは、名前でフォーム ファイルが照合されます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-447">Binding matches form files by name.</span></span> <span data-ttu-id="5c4ac-448">たとえば、HTML の `<input type="file" name="formFile">` の `name` の値は、バインドされた C# のパラメーター/プロパティと一致する必要があります (`FormFile`)。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-448">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="5c4ac-449">詳細については、「[name 属性の値を POST メソッドのパラメーター名に一致させる](#match-name-attribute-value-to-parameter-name-of-post-method)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-449">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="5c4ac-450">次のような例です。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-450">The following example:</span></span>

* <span data-ttu-id="5c4ac-451">アップロードされた 1 つ以上のファイルをループします。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-451">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="5c4ac-452">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) 使用して、ファイル名を含むファイルの完全なパスを返します。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-452">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="5c4ac-453">アプリによって生成されたファイル名を使用して、ローカル ファイル システムにファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-453">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="5c4ac-454">アップロードされたファイルの合計数とサイズを返します。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-454">Returns the total number and size of files uploaded.</span></span>

```csharp
public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> files)
{
    long size = files.Sum(f => f.Length);

    foreach (var formFile in files)
    {
        if (formFile.Length > 0)
        {
            var filePath = Path.GetTempFileName();

            using (var stream = System.IO.File.Create(filePath))
            {
                await formFile.CopyToAsync(stream);
            }
        }
    }

    // Process uploaded files
    // Don't rely on or trust the FileName property without validation.

    return Ok(new { count = files.Count, size });
}
```

<span data-ttu-id="5c4ac-455">パスを除いてファイル名を生成するには、`Path.GetRandomFileName` を使用します。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-455">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="5c4ac-456">次の例では、構成からパスを取得します。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-456">In the following example, the path is obtained from configuration:</span></span>

```csharp
foreach (var formFile in files)
{
    if (formFile.Length > 0)
    {
        var filePath = Path.Combine(_config["StoredFilesPath"], 
            Path.GetRandomFileName());

        using (var stream = System.IO.File.Create(filePath))
        {
            await formFile.CopyToAsync(stream);
        }
    }
}
```

<span data-ttu-id="5c4ac-457"> に渡すパスには、ファイル名が含まれている "<xref:System.IO.FileStream> *必要があります*"。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-457">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="5c4ac-458">ファイル名を指定しないと、実行時に <xref:System.UnauthorizedAccessException> がスローされます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-458">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="5c4ac-459"><xref:Microsoft.AspNetCore.Http.IFormFile> の方法を使用してアップロードされたファイルは、処理の前に、サーバー上のメモリまたはディスクのバッファーに格納されます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-459">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="5c4ac-460">アクション メソッド内では、<xref:Microsoft.AspNetCore.Http.IFormFile> の内容には <xref:System.IO.Stream> としてアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-460">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="5c4ac-461">ローカル ファイル システムに加えて、ネットワーク共有またはファイル ストレージ サービス ([Azure Blob Storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs) など) にファイルを保存することができます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-461">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="5c4ac-462">アップロードのために複数のファイルをループし、安全なファイル名を使用する別の例については、サンプル アプリの *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-462">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="5c4ac-463">以前の一時ファイルを削除せずに、65,535 個より多くのファイルを作成すると、[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) で <xref:System.IO.IOException> がスローされます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-463">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="5c4ac-464">65,535 ファイルの制限は、サーバーごとの制限です。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-464">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="5c4ac-465">Windows OS でのこの制限の詳細については、次のトピックの「解説」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-465">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="5c4ac-466">GetTempFileNameA 関数</span><span class="sxs-lookup"><span data-stu-id="5c4ac-466">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="5c4ac-467">バッファー モデル バインドを使用して小さいファイルをデータベースにアップロードする</span><span class="sxs-lookup"><span data-stu-id="5c4ac-467">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="5c4ac-468">[Entity Framework](/ef/core/index) を使用してデータベースにバイナリ ファイル データを格納するには、エンティティで <xref:System.Byte> 配列プロパティを定義します。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-468">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="5c4ac-469"><xref:Microsoft.AspNetCore.Http.IFormFile> が含まれるクラスに対してページ モデル プロパティを指定します。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-469">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

```csharp
public class BufferedSingleFileUploadDbModel : PageModel
{
    ...

    [BindProperty]
    public BufferedSingleFileUploadDb FileUpload { get; set; }

    ...
}

public class BufferedSingleFileUploadDb
{
    [Required]
    [Display(Name="File")]
    public IFormFile FormFile { get; set; }
}
```

> [!NOTE]
> <span data-ttu-id="5c4ac-470"><xref:Microsoft.AspNetCore.Http.IFormFile> は、アクション メソッドのパラメーターとして直接、またはバインドされたモデル プロパティとして、使用することができます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-470"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="5c4ac-471">前の例では、バインドされたモデル プロパティが使用されています。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-471">The prior example uses a bound model property.</span></span>

<span data-ttu-id="5c4ac-472">は、 `FileUpload` ページ形式で使用され Razor ます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-472">The `FileUpload` is used in the Razor Pages form:</span></span>

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload">
</form>
```

<span data-ttu-id="5c4ac-473">フォームがサーバーに POST されるときに、<xref:Microsoft.AspNetCore.Http.IFormFile> をストリームにコピーし、バイト配列としてデータベースに保存します。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-473">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="5c4ac-474">次の例では、`_dbContext` によってアプリのデータベース コンテキストが格納されます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-474">In the following example, `_dbContext` stores the app's database context:</span></span>

```csharp
public async Task<IActionResult> OnPostUploadAsync()
{
    using (var memoryStream = new MemoryStream())
    {
        await FileUpload.FormFile.CopyToAsync(memoryStream);

        // Upload the file if less than 2 MB
        if (memoryStream.Length < 2097152)
        {
            var file = new AppFile()
            {
                Content = memoryStream.ToArray()
            };

            _dbContext.File.Add(file);

            await _dbContext.SaveChangesAsync();
        }
        else
        {
            ModelState.AddModelError("File", "The file is too large.");
        }
    }

    return Page();
}
```

<span data-ttu-id="5c4ac-475">前の例は、次のサンプル アプリで示されているシナリオに似ています。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-475">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="5c4ac-476">*Pages/BufferedSingleFileUploadDb.cshtml*</span><span class="sxs-lookup"><span data-stu-id="5c4ac-476">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="5c4ac-477">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="5c4ac-477">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="5c4ac-478">パフォーマンスに悪影響を与える可能性があるため、リレーショナル データベースにバイナリ データを格納する場合は注意してください。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-478">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="5c4ac-479">検証を行わずに <xref:Microsoft.AspNetCore.Http.IFormFile> の `FileName` プロパティに依存したり、信頼したりしないでください。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-479">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="5c4ac-480">`FileName` プロパティは、表示目的でのみ、HTML エンコードした後でだけ、使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-480">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="5c4ac-481">示した例では、セキュリティ上の考慮事項については考えられていません。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-481">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="5c4ac-482">以下のセクションおよび[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)で、追加の情報が提供されています。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-482">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="5c4ac-483">セキュリティの考慮事項</span><span class="sxs-lookup"><span data-stu-id="5c4ac-483">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="5c4ac-484">検証</span><span class="sxs-lookup"><span data-stu-id="5c4ac-484">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="5c4ac-485">ストリーミングを使用して大きいファイルをアップロードする</span><span class="sxs-lookup"><span data-stu-id="5c4ac-485">Upload large files with streaming</span></span>

<span data-ttu-id="5c4ac-486">次の例では、JavaScript を使用してファイルをコントローラーのアクションにストリーミングする方法を示します。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-486">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="5c4ac-487">ファイルの偽造防止トークンは、カスタム フィルター属性を使用して生成され、要求本文ではなくクライアント HTTP ヘッダーに渡されます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-487">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="5c4ac-488">アクション メソッドではアップロードされたデータが直接処理されるため、フォーム モデル バインドは別のカスタム フィルターでは無効になります。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-488">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="5c4ac-489">アクション内では、フォームのコンテンツが `MultipartReader` を使用して読み取られます。その場合、各 `MultipartSection` が読み取られ、必要に応じて、ファイルが処理されるかコンテンツが格納されます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-489">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="5c4ac-490">マルチパート セクションが読み取られた後、アクションで独自のモデル バインドが実行されます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-490">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="5c4ac-491">最初のページ応答では、フォームが読み込まれ、(属性を使用して) にアンチ偽造トークンが保存され cookie `GenerateAntiforgeryTokenCookieAttribute` ます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-491">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="5c4ac-492">属性は、ASP.NET Core の組み込みの [アンチ偽造サポート](xref:security/anti-request-forgery) を使用して、要求トークンを使用してを設定し cookie ます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-492">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="5c4ac-493">`DisableFormValueModelBindingAttribute` は、モデル バインドを無効にするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-493">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="5c4ac-494">このサンプルアプリで `GenerateAntiforgeryTokenCookieAttribute` は、とは、ページ `DisableFormValueModelBindingAttribute` `/StreamedSingleFileUploadDb` `/StreamedSingleFileUploadPhysical` `Startup.ConfigureServices` [ Razor 表記規則](xref:razor-pages/razor-pages-conventions)を使用するとのページアプリケーションモデルにフィルターとして適用されます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-494">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Startup.cs?name=snippet_AddMvc&highlight=8-11,17-20)]

<span data-ttu-id="5c4ac-495">モデル バインドではフォームが読み取られないため、フォームからバインドされているパラメーターはバインドされません (クエリ、ルート、ヘッダーは引き続き機能します)。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-495">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="5c4ac-496">アクション メソッドでは、`Request` プロパティが直接操作されます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-496">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="5c4ac-497">`MultipartReader` は各セクションを読み取るために使用されます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-497">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="5c4ac-498">キー/値データは `KeyValueAccumulator` に格納されます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-498">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="5c4ac-499">マルチパート セクションが読み取られた後、`KeyValueAccumulator` の内容を使用して、フォーム データがモデル タイプにバインドされます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-499">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="5c4ac-500">EF Core でデータベースにストリーミングするための完全な `StreamingController.UploadDatabase` メソッド:</span><span class="sxs-lookup"><span data-stu-id="5c4ac-500">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="5c4ac-501">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span><span class="sxs-lookup"><span data-stu-id="5c4ac-501">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="5c4ac-502">物理的な場所にストリーミングするための完全な `StreamingController.UploadPhysical` メソッド:</span><span class="sxs-lookup"><span data-stu-id="5c4ac-502">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="5c4ac-503">サンプル アプリでは、検証チェックは `FileHelpers.ProcessStreamedFile` によって処理されます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-503">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="5c4ac-504">検証</span><span class="sxs-lookup"><span data-stu-id="5c4ac-504">Validation</span></span>

<span data-ttu-id="5c4ac-505">サンプル アプリの `FileHelpers` クラスでは、バッファーリングされた <xref:Microsoft.AspNetCore.Http.IFormFile> とストリーミングされたファイルのアップロードに関するいくつかのチェックが示されています。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-505">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="5c4ac-506">サンプル アプリでのバッファーリングされたファイルのアップロード <xref:Microsoft.AspNetCore.Http.IFormFile> の処理については、*Utilities/FileHelpers.cs* ファイルの `ProcessFormFile` メソッドを参照してください。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-506">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="5c4ac-507">ストリーミングされたファイルの処理については、同じファイルの `ProcessStreamedFile` メソッドを参照してください。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-507">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="5c4ac-508">サンプル アプリで示されている検証処理メソッドでは、アップロードされたファイルの内容はスキャンされません。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-508">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="5c4ac-509">ほとんどの運用シナリオでは、ファイルをユーザーまたは他のシステムで使用できるようにする前に、ウイルス/マルウェア スキャナー API が使用されます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-509">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="5c4ac-510">このトピックのサンプルでは検証技法の実際の例が示されていますが、次の場合を除き、運用アプリでは `FileHelpers` クラスを実装しないでください。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-510">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="5c4ac-511">実装を完全に理解している。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-511">Fully understand the implementation.</span></span>
> * <span data-ttu-id="5c4ac-512">アプリの環境と仕様に合わせて実装が適切に変更されている。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-512">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="5c4ac-513">**これらの要件に対応することなく、アプリにセキュリティ コードをむやみに実装しないでください。**</span><span class="sxs-lookup"><span data-stu-id="5c4ac-513">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="5c4ac-514">コンテンツの検証</span><span class="sxs-lookup"><span data-stu-id="5c4ac-514">Content validation</span></span>

<span data-ttu-id="5c4ac-515">**アップロードされた内容に対してサードパーティ製のウイルス/マルウェア スキャン API を使用します。**</span><span class="sxs-lookup"><span data-stu-id="5c4ac-515">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="5c4ac-516">大容量のシナリオでは、ファイルのスキャンに大量のサーバー リソースが必要です。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-516">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="5c4ac-517">ファイルのスキャンによって要求の処理パフォーマンスが低下する場合は、スキャン処理を[バックグラウンド サービス](xref:fundamentals/host/hosted-services)にオフロードすることを検討してください (たとえば、アプリのサーバーとは異なるサーバーで実行されているサービス)。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-517">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="5c4ac-518">通常、アップロードされたファイルは、バックグラウンドのウイルス検索プログラムによってチェックされるまで、検疫された領域に保持されます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-518">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="5c4ac-519">合格したファイルは、通常のファイル ストレージの場所に移動されます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-519">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="5c4ac-520">これらの手順は、通常、ファイルのスキャン状態を示すデータベース レコードと共に実行されます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-520">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="5c4ac-521">このような方法を使用することで、アプリとアプリ サーバーは要求への応答に集中できます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-521">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="5c4ac-522">ファイル拡張子の検証</span><span class="sxs-lookup"><span data-stu-id="5c4ac-522">File extension validation</span></span>

<span data-ttu-id="5c4ac-523">アップロードされたファイルの拡張子を、許可されている拡張子のリストで確認する必要があります。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-523">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="5c4ac-524">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-524">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="5c4ac-525">ファイルのシグネチャの検証</span><span class="sxs-lookup"><span data-stu-id="5c4ac-525">File signature validation</span></span>

<span data-ttu-id="5c4ac-526">ファイルのシグネチャは、ファイルの先頭にある最初の数バイトによって決定されます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-526">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="5c4ac-527">これらのバイトを使用して、拡張子がファイルの内容と一致するかどうかを示すことができます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-527">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="5c4ac-528">サンプル アプリでは、いくつかの一般的なファイルの種類についてファイルのシグネチャがチェックされています。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-528">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="5c4ac-529">次の例では、JPEG イメージのファイルのシグネチャが、ファイルに対してチェックされています。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-529">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

```csharp
private static readonly Dictionary<string, List<byte[]>> _fileSignature = 
    new Dictionary<string, List<byte[]>>
{
    { ".jpeg", new List<byte[]>
        {
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE2 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE3 },
        }
    },
};

using (var reader = new BinaryReader(uploadedFileData))
{
    var signatures = _fileSignature[ext];
    var headerBytes = reader.ReadBytes(signatures.Max(m => m.Length));
    
    return signatures.Any(signature => 
        headerBytes.Take(signature.Length).SequenceEqual(signature));
}
```

<span data-ttu-id="5c4ac-530">追加のファイル シグネチャを取得するには、「[ファイル シグネチャ データベース](https://www.filesignatures.net/)」および公式なファイルの仕様を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-530">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="5c4ac-531">ファイル名のセキュリティ</span><span class="sxs-lookup"><span data-stu-id="5c4ac-531">File name security</span></span>

<span data-ttu-id="5c4ac-532">ファイルを物理ストレージに保存する場合は、クライアント指定のファイル名を使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-532">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="5c4ac-533">[Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) または [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) を使用して、ファイルの安全なファイル名を作成し、一時ストレージの完全なパス (ファイル名を含む) を作成します。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-533">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

<span data-ttu-id="5c4ac-534">Razor プロパティ値が自動的に HTML エンコードされて表示されます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-534">Razor automatically HTML encodes property values for display.</span></span> <span data-ttu-id="5c4ac-535">次のコードは安全に使用できます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-535">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="5c4ac-536">の外部 Razor では、常に <xref:System.Net.WebUtility.HtmlEncode*> ユーザーの要求の内容をファイル名で指定します。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-536">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="5c4ac-537">多くの実装には、ファイルが存在するかどうかのチェックを含める必要があります。そうしないと、同じ名前のファイルによってファイルが上書きされます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-537">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="5c4ac-538">アプリの仕様を満たす追加のロジックを提供します。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-538">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="5c4ac-539">サイズの検証</span><span class="sxs-lookup"><span data-stu-id="5c4ac-539">Size validation</span></span>

<span data-ttu-id="5c4ac-540">アップロードされるファイルのサイズを制限します。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-540">Limit the size of uploaded files.</span></span>

<span data-ttu-id="5c4ac-541">サンプル アプリでは、ファイルのサイズは 2 MB (バイト単位) に制限されています。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-541">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="5c4ac-542">その制限は、*appsettings.json* ファイルの [Configuration](xref:fundamentals/configuration/index) によって提供されます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-542">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="5c4ac-543">`FileSizeLimit` が `PageModel` クラスに挿入されます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-543">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

```csharp
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    private readonly long _fileSizeLimit;

    public BufferedSingleFileUploadPhysicalModel(IConfiguration config)
    {
        _fileSizeLimit = config.GetValue<long>("FileSizeLimit");
    }

    ...
}
```

<span data-ttu-id="5c4ac-544">ファイルのサイズが制限を超えると、ファイルは拒否されます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-544">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="5c4ac-545">name 属性の値を POST メソッドのパラメーター名に一致させる</span><span class="sxs-lookup"><span data-stu-id="5c4ac-545">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="5c4ac-546">フォームデータを Razor ポストするか、JavaScript の直接を使用する非フォームでは `FormData` 、フォームの要素で指定された名前、または `FormData` コントローラーのアクション内のパラメーターの名前と一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-546">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="5c4ac-547">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-547">In the following example:</span></span>

* <span data-ttu-id="5c4ac-548">`<input>` 要素を使用すると、`name` 属性には値 `battlePlans` が設定されます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-548">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="5c4ac-549">JavaScript で `FormData` を使用すると、name には値 `battlePlans` が設定されます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-549">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="5c4ac-550">C# メソッドのパラメーターに一致する名前を使用します (`battlePlans`)。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-550">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="5c4ac-551">Razorという名前のページページハンドラーメソッドの場合 `Upload` :</span><span class="sxs-lookup"><span data-stu-id="5c4ac-551">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="5c4ac-552">MVC POST コントローラー アクション メソッドの場合:</span><span class="sxs-lookup"><span data-stu-id="5c4ac-552">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="5c4ac-553">サーバーとアプリの構成</span><span class="sxs-lookup"><span data-stu-id="5c4ac-553">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="5c4ac-554">マルチパート本文の長さの制限</span><span class="sxs-lookup"><span data-stu-id="5c4ac-554">Multipart body length limit</span></span>

<span data-ttu-id="5c4ac-555"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> では、各マルチパート本文の長さの制限が設定されます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-555"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="5c4ac-556">この制限を超えるフォーム セクションでは、解析時に <xref:System.IO.InvalidDataException> がスローされます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-556">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="5c4ac-557">既定値は 134,217,728 (128 MB) です。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-557">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="5c4ac-558">制限をカスタマイズするには、`Startup.ConfigureServices` の設定 <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> を使用します。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-558">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<FormOptions>(options =>
    {
        // Set the limit to 256 MB
        options.MultipartBodyLengthLimit = 268435456;
    });
}
```

<span data-ttu-id="5c4ac-559">単一ページまたはアクションに対する <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> を設定するには、<xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> を使用します。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-559"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="5c4ac-560">RazorPages アプリで、で[規則](xref:razor-pages/razor-pages-conventions)を適用したフィルターを適用し `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-560">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.Conventions
            .AddPageApplicationModelConvention("/FileUploadPage",
                model.Filters.Add(
                    new RequestFormLimitsAttribute()
                    {
                        // Set the limit to 256 MB
                        MultipartBodyLengthLimit = 268435456
                    });
    })
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

<span data-ttu-id="5c4ac-561">Razorページアプリまたは MVC アプリで、ページモデルまたはアクションメソッドにフィルターを適用します。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-561">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="5c4ac-562">Kestrel の最大要求本文サイズ</span><span class="sxs-lookup"><span data-stu-id="5c4ac-562">Kestrel maximum request body size</span></span>

<span data-ttu-id="5c4ac-563">Kestrel によってホストされるアプリの場合、既定の要求本文の最大サイズは、30,000,000 バイトです。これは約 28.6 MB になります。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-563">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="5c4ac-564">制限をカスタマイズするには、[MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel サーバー オプションを使用します。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-564">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            // Handle requests up to 50 MB
            options.Limits.MaxRequestBodySize = 52428800;
        });
```

<span data-ttu-id="5c4ac-565">単一ページまたはアクションに対する [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) を設定するには、<xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> を使用します。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-565"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="5c4ac-566">RazorPages アプリで、で[規則](xref:razor-pages/razor-pages-conventions)を適用したフィルターを適用し `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-566">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.Conventions
            .AddPageApplicationModelConvention("/FileUploadPage",
                model =>
                {
                    // Handle requests up to 50 MB
                    model.Filters.Add(
                        new RequestSizeLimitAttribute(52428800));
                });
    })
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

<span data-ttu-id="5c4ac-567">Razorページアプリまたは MVC アプリで、フィルターをページハンドラークラスまたはアクションメソッドに適用します。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-567">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="5c4ac-568">その他の Kestrel の制限</span><span class="sxs-lookup"><span data-stu-id="5c4ac-568">Other Kestrel limits</span></span>

<span data-ttu-id="5c4ac-569">Kestrel によってホストされるアプリには、他の Kestrel の制限が適用される場合があります。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-569">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="5c4ac-570">クライアントの最大接続数</span><span class="sxs-lookup"><span data-stu-id="5c4ac-570">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="5c4ac-571">要求と応答のデータ レート</span><span class="sxs-lookup"><span data-stu-id="5c4ac-571">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a><span data-ttu-id="5c4ac-572">IIS の内容の長さの制限</span><span class="sxs-lookup"><span data-stu-id="5c4ac-572">IIS content length limit</span></span>

<span data-ttu-id="5c4ac-573">既定の要求の制限 (`maxAllowedContentLength`) は、30,000,000 バイトです。これは約 28.6 MB になります。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-573">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6MB.</span></span> <span data-ttu-id="5c4ac-574">*web.config* ファイルで制限をカスタマイズします。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-574">Customize the limit in the *web.config* file:</span></span>

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <!-- Handle requests up to 50 MB -->
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

<span data-ttu-id="5c4ac-575">この設定は IIS にのみ適用されます。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-575">This setting only applies to IIS.</span></span> <span data-ttu-id="5c4ac-576">Kestrel でホストする場合、既定ではこの動作は発生しません。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-576">The behavior doesn't occur by default when hosting on Kestrel.</span></span> <span data-ttu-id="5c4ac-577">詳細については、「[要求の制限 \<requestLimits> ](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-577">For more information, see [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="5c4ac-578">ASP.NET Core モジュールでの制限または IIS 要求フィルター モジュールの存在により、アップロードが 2 または 4 GB に制限される場合があります。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-578">Limitations in the ASP.NET Core Module or presence of the IIS Request Filtering Module may limit uploads to either 2 or 4 GB.</span></span> <span data-ttu-id="5c4ac-579">詳細については、「[サイズが 2 GB を超えるファイルをアップロードできない (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-579">For more information, see [Unable to upload file greater than 2GB in size (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="5c4ac-580">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="5c4ac-580">Troubleshoot</span></span>

<span data-ttu-id="5c4ac-581">ファイルのアップロード時に発生する一般的ないくつかの問題と、考えられる解決策を以下に示します。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-581">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="5c4ac-582">IIS サーバーに展開したときの "見つかりません" エラー</span><span class="sxs-lookup"><span data-stu-id="5c4ac-582">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="5c4ac-583">次のエラーは、アップロードされるファイルがサーバーの構成されている内容の長さを超えていることを示します。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-583">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="5c4ac-584">制限を増やす方法の詳細については、「[IIS の内容の長さの制限](#iis-content-length-limit)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-584">For more information on increasing the limit, see the [IIS content length limit](#iis-content-length-limit) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="5c4ac-585">接続エラー</span><span class="sxs-lookup"><span data-stu-id="5c4ac-585">Connection failure</span></span>

<span data-ttu-id="5c4ac-586">接続エラーおよびサーバー接続のリセットは、アップロードされるファイルが Kestrel の最大要求本文サイズを超えていることを示している場合があります。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-586">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="5c4ac-587">詳細については、[Kestrel の最大要求本文サイズ](#kestrel-maximum-request-body-size)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-587">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="5c4ac-588">Kestrel クライアント接続の制限の調整も必要な場合があります。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-588">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="5c4ac-589">IFormFile での null 参照例外</span><span class="sxs-lookup"><span data-stu-id="5c4ac-589">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="5c4ac-590">コントローラーが <xref:Microsoft.AspNetCore.Http.IFormFile> を使用してアップロードされたファイルを受け取っても、値が `null` の場合は、HTML フォームで `multipart/form-data` に値 `enctype` が指定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-590">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="5c4ac-591">この属性が `<form>` 要素で設定されていない場合、ファイルはアップロードされず、バインドされた <xref:Microsoft.AspNetCore.Http.IFormFile> 引数は `null` になります。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-591">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="5c4ac-592">また、[フォーム データでのアップロードの名前がアプリの名前と一致する](#match-name-attribute-value-to-parameter-name-of-post-method)ことを確認します。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-592">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="5c4ac-593">ストリームが長すぎる</span><span class="sxs-lookup"><span data-stu-id="5c4ac-593">Stream was too long</span></span>

<span data-ttu-id="5c4ac-594">このトピックの例は、アップロードされたファイル コンテンツを保持するために <xref:System.IO.MemoryStream> に依存しています。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-594">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="5c4ac-595">`int.MaxValue` のサイズ制限は `MemoryStream` です。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-595">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="5c4ac-596">アプリのファイル アップロード シナリオで 50 MB を超えるファイル コンテンツを保持する必要がある場合は、アップロードされたファイルのコンテンツを 1 つの `MemoryStream` に依存することなく保持する別のアプローチを使用します。</span><span class="sxs-lookup"><span data-stu-id="5c4ac-596">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end


## <a name="additional-resources"></a><span data-ttu-id="5c4ac-597">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="5c4ac-597">Additional resources</span></span>

* [<span data-ttu-id="5c4ac-598">HTTP 接続要求をドレインしています</span><span class="sxs-lookup"><span data-stu-id="5c4ac-598">HTTP connection request draining</span></span>](xref:fundamentals/servers/kestrel#http11-request-draining)
* [<span data-ttu-id="5c4ac-599">Unrestricted File Upload (ファイルの無制限のアップロード)</span><span class="sxs-lookup"><span data-stu-id="5c4ac-599">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
* [<span data-ttu-id="5c4ac-600">Azure のセキュリティ: セキュリティフレーム: 入力の検証 |対策</span><span class="sxs-lookup"><span data-stu-id="5c4ac-600">Azure Security: Security Frame: Input Validation | Mitigations</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation)
* [<span data-ttu-id="5c4ac-601">Azure クラウドの設計パターン: Valet キーパターン</span><span class="sxs-lookup"><span data-stu-id="5c4ac-601">Azure Cloud Design Patterns: Valet Key pattern</span></span>](/azure/architecture/patterns/valet-key)
