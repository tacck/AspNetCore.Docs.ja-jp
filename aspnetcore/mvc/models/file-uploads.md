---
<span data-ttu-id="b47dc-101">title: author: description: monikerRange: ms. author: ms. custom: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="b47dc-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b47dc-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b47dc-102">'Blazor'</span></span>
- <span data-ttu-id="b47dc-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b47dc-103">'Identity'</span></span>
- <span data-ttu-id="b47dc-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b47dc-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="b47dc-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b47dc-105">'Razor'</span></span>
- <span data-ttu-id="b47dc-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="b47dc-106">'SignalR' uid:</span></span> 

---
# <a name="upload-files-in-aspnet-core"></a><span data-ttu-id="b47dc-107">ASP.NET Core でファイルをアップロードする</span><span class="sxs-lookup"><span data-stu-id="b47dc-107">Upload files in ASP.NET Core</span></span>

<span data-ttu-id="b47dc-108">[上田氏](https://ardalis.com/)との[嵐](https://github.com/rutix)</span><span class="sxs-lookup"><span data-stu-id="b47dc-108">By [Steve Smith](https://ardalis.com/) and [Rutger Storm](https://github.com/rutix)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b47dc-109">ASP.NET Core では、小さいファイルの場合はバッファー モデル バインドを使用し、大きいファイルの場合は非バッファー ストリーミングを使用して、1 つ以上のファイルのアップロードがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="b47dc-109">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="b47dc-110">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="b47dc-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="b47dc-111">セキュリティに関する考慮事項</span><span class="sxs-lookup"><span data-stu-id="b47dc-111">Security considerations</span></span>

<span data-ttu-id="b47dc-112">サーバーにファイルをアップロードする機能をユーザーに提供するときは、十分に注意してください。</span><span class="sxs-lookup"><span data-stu-id="b47dc-112">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="b47dc-113">攻撃者が次のようなことを試みる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="b47dc-113">Attackers may attempt to:</span></span>

* <span data-ttu-id="b47dc-114">[サービス拒否](/windows-hardware/drivers/ifs/denial-of-service)攻撃を実行する。</span><span class="sxs-lookup"><span data-stu-id="b47dc-114">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="b47dc-115">ウイルスまたはマルウェアをアップロードする。</span><span class="sxs-lookup"><span data-stu-id="b47dc-115">Upload viruses or malware.</span></span>
* <span data-ttu-id="b47dc-116">ネットワークやサーバーを他の方法で侵害する。</span><span class="sxs-lookup"><span data-stu-id="b47dc-116">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="b47dc-117">攻撃の成功の可能性を少なくするセキュリティ手順は、次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="b47dc-117">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="b47dc-118">専用のファイル アップロード領域 (できれば、システム ドライブ以外) にファイルをアップロードします。</span><span class="sxs-lookup"><span data-stu-id="b47dc-118">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="b47dc-119">専用の場所を使用すると、アップロードされるファイルにセキュリティ制限を適用しやすくなります。</span><span class="sxs-lookup"><span data-stu-id="b47dc-119">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="b47dc-120">ファイルのアップロード場所に対する実行アクセス許可を無効にします。&dagger;</span><span class="sxs-lookup"><span data-stu-id="b47dc-120">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="b47dc-121">アプリと同じディレクトリ ツリーに、アップロードしたファイルを保持**しないでください**。&dagger;</span><span class="sxs-lookup"><span data-stu-id="b47dc-121">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="b47dc-122">アプリによって決められた安全なファイル名を使用します。</span><span class="sxs-lookup"><span data-stu-id="b47dc-122">Use a safe file name determined by the app.</span></span> <span data-ttu-id="b47dc-123">ユーザーが指定したファイル名や、アップロードしたファイルの信頼されていないファイル名を使用しないでください。 &dagger;HTML は、表示時に信頼されていないファイル名をエンコードします。</span><span class="sxs-lookup"><span data-stu-id="b47dc-123">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="b47dc-124">たとえば、ファイル名をログに記録したり、UI に表示したりし Razor ます (出力を自動的に HTML エンコードします)。</span><span class="sxs-lookup"><span data-stu-id="b47dc-124">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="b47dc-125">アプリの設計仕様に対して承認されているファイル拡張子のみを許可します。&dagger;</span><span class="sxs-lookup"><span data-stu-id="b47dc-125">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="b47dc-126">クライアント側のチェックがサーバーで実行されていることを確認します。 &dagger;クライアント側のチェックは簡単に回避できます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-126">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="b47dc-127">アップロードされたファイルのサイズをチェックします。</span><span class="sxs-lookup"><span data-stu-id="b47dc-127">Check the size of an uploaded file.</span></span> <span data-ttu-id="b47dc-128">サイズの大きなアップロードを防ぐために、最大サイズ制限を設定します。&dagger;</span><span class="sxs-lookup"><span data-stu-id="b47dc-128">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="b47dc-129">同じ名前でアップロードされたファイルによってファイルが上書きされないようにする必要があるときは、ファイルをアップロードする前に、データベースまたは物理ストレージに対してファイル名を確認します。</span><span class="sxs-lookup"><span data-stu-id="b47dc-129">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="b47dc-130">**ファイルを格納する前に、アップロードされる内容に対してウイルス/マルウェア スキャナーを実行します。**</span><span class="sxs-lookup"><span data-stu-id="b47dc-130">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="b47dc-131">&dagger;サンプル アプリで、条件を満たす方法が示されています。</span><span class="sxs-lookup"><span data-stu-id="b47dc-131">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="b47dc-132">システムへの悪意のあるコードのアップロードは、頻繁に次のような内容のコードの実行するための足がかりとなります。</span><span class="sxs-lookup"><span data-stu-id="b47dc-132">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="b47dc-133">システムの制御を完全に掌握する。</span><span class="sxs-lookup"><span data-stu-id="b47dc-133">Completely gain control of a system.</span></span>
> * <span data-ttu-id="b47dc-134">システムがクラッシュする結果で、システムを過負荷状態にする。</span><span class="sxs-lookup"><span data-stu-id="b47dc-134">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="b47dc-135">ユーザーまたはシステムのデータを破壊する。</span><span class="sxs-lookup"><span data-stu-id="b47dc-135">Compromise user or system data.</span></span>
> * <span data-ttu-id="b47dc-136">パブリック UI に落書きする。</span><span class="sxs-lookup"><span data-stu-id="b47dc-136">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="b47dc-137">ユーザーからファイルを受け入れる際の外部アクセスによる攻撃を減らす方法については、次の資料を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b47dc-137">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * [<span data-ttu-id="b47dc-138">Unrestricted File Upload (ファイルの無制限のアップロード)</span><span class="sxs-lookup"><span data-stu-id="b47dc-138">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [<span data-ttu-id="b47dc-139">Azure セキュリティ: ユーザーから受け入れるファイルに適切な管理制御を整備する</span><span class="sxs-lookup"><span data-stu-id="b47dc-139">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="b47dc-140">サンプル アプリの例など、セキュリティ対策の実装の詳細については、「[検証](#validation)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="b47dc-140">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="b47dc-141">ストレージのシナリオ</span><span class="sxs-lookup"><span data-stu-id="b47dc-141">Storage scenarios</span></span>

<span data-ttu-id="b47dc-142">ファイルの一般的なストレージ オプションには次のようなものがあります。</span><span class="sxs-lookup"><span data-stu-id="b47dc-142">Common storage options for files include:</span></span>

* <span data-ttu-id="b47dc-143">データベース</span><span class="sxs-lookup"><span data-stu-id="b47dc-143">Database</span></span>

  * <span data-ttu-id="b47dc-144">小さいファイルをアップロードする場合、物理ストレージ (ファイル システムまたはネットワーク共有) のオプションよりデータベースの方が速いことがよくあります。</span><span class="sxs-lookup"><span data-stu-id="b47dc-144">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="b47dc-145">多くの場合、ユーザー データに対するデータベース レコードの取得でファイルの内容を同時に提供できるため (たとえば、アバター イメージ)、データベースの方が物理的なストレージ オプションより便利です。</span><span class="sxs-lookup"><span data-stu-id="b47dc-145">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="b47dc-146">データベースは、データ ストレージ サービスを使用するよりコストが低くなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="b47dc-146">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="b47dc-147">物理ストレージ (ファイル システムまたはネットワーク共有)</span><span class="sxs-lookup"><span data-stu-id="b47dc-147">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="b47dc-148">大きいファイルのアップロードの場合:</span><span class="sxs-lookup"><span data-stu-id="b47dc-148">For large file uploads:</span></span>
    * <span data-ttu-id="b47dc-149">データベースの制限によって、アップロードのサイズが制限される場合があります。</span><span class="sxs-lookup"><span data-stu-id="b47dc-149">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="b47dc-150">多くの場合、物理ストレージはデータベース内のストレージより高コストです。</span><span class="sxs-lookup"><span data-stu-id="b47dc-150">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="b47dc-151">物理ストレージは、データ ストレージ サービスを使用するよりコストが低くなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="b47dc-151">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="b47dc-152">アプリのプロセスには、ストレージの場所に対する読み取りと書き込みのアクセス許可が必要です。</span><span class="sxs-lookup"><span data-stu-id="b47dc-152">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="b47dc-153">**実行アクセス許可は付与しないでください。**</span><span class="sxs-lookup"><span data-stu-id="b47dc-153">**Never grant execute permission.**</span></span>

* <span data-ttu-id="b47dc-154">データ ストレージ サービス (例: [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span><span class="sxs-lookup"><span data-stu-id="b47dc-154">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="b47dc-155">通常、サービスでは、大抵の場合に単一障害点となるオンプレミス ソリューションより高いスケーラビリティと回復性が提供されます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-155">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="b47dc-156">大規模なストレージ インフラストラクチャのシナリオでは、サービスのコストが低下する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="b47dc-156">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="b47dc-157">詳細については、「[クイックスタート: .net を使用してオブジェクトストレージに blob を作成する](/azure/storage/blobs/storage-quickstart-blobs-dotnet)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b47dc-157">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="b47dc-158">ファイル アップロードのシナリオ</span><span class="sxs-lookup"><span data-stu-id="b47dc-158">File upload scenarios</span></span>

<span data-ttu-id="b47dc-159">ファイルをアップロードするための一般的な 2 つの方法は、バッファーリングとストリーミングです。</span><span class="sxs-lookup"><span data-stu-id="b47dc-159">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="b47dc-160">**Buffering**</span><span class="sxs-lookup"><span data-stu-id="b47dc-160">**Buffering**</span></span>

<span data-ttu-id="b47dc-161">ファイル全体が <xref:Microsoft.AspNetCore.Http.IFormFile> に読み込まれます。これは、ファイルの処理または保存に使用される C# でのファイルの表現です。</span><span class="sxs-lookup"><span data-stu-id="b47dc-161">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="b47dc-162">ファイルのアップロードで使用されるリソース (ディスク、メモリM) は、同時ファイル アップロードの数とサイズによって異なります。</span><span class="sxs-lookup"><span data-stu-id="b47dc-162">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="b47dc-163">アプリであまり多くのアップロードをバッファーに格納しようとすると、メモリまたはディスク領域が不足したときにサイトがクラッシュします。</span><span class="sxs-lookup"><span data-stu-id="b47dc-163">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="b47dc-164">ファイルのアップロードのサイズまたは頻度によりアプリのリソースが不足する場合は、ストリーミングを使用します。</span><span class="sxs-lookup"><span data-stu-id="b47dc-164">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="b47dc-165">1 つで 64 KB を超えるバッファー ファイルは、メモリからディスク上の一時ファイルに移動されます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-165">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="b47dc-166">小さいファイルのバッファーリングについては、後のセクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="b47dc-166">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="b47dc-167">物理ストレージ</span><span class="sxs-lookup"><span data-stu-id="b47dc-167">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* <span data-ttu-id="b47dc-168">[[データベース]](#upload-small-files-with-buffered-model-binding-to-a-database)</span><span class="sxs-lookup"><span data-stu-id="b47dc-168">[Database](#upload-small-files-with-buffered-model-binding-to-a-database)</span></span>

<span data-ttu-id="b47dc-169">**ストリーミング**</span><span class="sxs-lookup"><span data-stu-id="b47dc-169">**Streaming**</span></span>

<span data-ttu-id="b47dc-170">ファイルはマルチパート要求から受信され、アプリによって直接処理または保存されます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-170">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="b47dc-171">ストリーミングによってパフォーマンスが大幅に向上することはありません。</span><span class="sxs-lookup"><span data-stu-id="b47dc-171">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="b47dc-172">ストリーミングを使用すると、ファイルをアップロードするときのメモリまたはディスク領域の需要を減らすことができます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-172">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="b47dc-173">大きいファイルのストリーミングについては、「[ストリーミングを使用して大きいファイルをアップロードする](#upload-large-files-with-streaming)」で説明します。</span><span class="sxs-lookup"><span data-stu-id="b47dc-173">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="b47dc-174">バッファー モデル バインドを使用して小さいファイルを物理ストレージにアップロードする</span><span class="sxs-lookup"><span data-stu-id="b47dc-174">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="b47dc-175">小さいファイルをアップロードするには、マルチパート形式を使用するか、または JavaScript を使用して POST 要求を作成します。</span><span class="sxs-lookup"><span data-stu-id="b47dc-175">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="b47dc-176">次の例では、ページフォームを使用して Razor 1 つのファイルをアップロードします (サンプルアプリの*Pages/BufferedSingleFileUploadPhysical* )。</span><span class="sxs-lookup"><span data-stu-id="b47dc-176">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

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

<span data-ttu-id="b47dc-177">次の例は前の例と似ていますが、以下の点が異なります。</span><span class="sxs-lookup"><span data-stu-id="b47dc-177">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="b47dc-178">JavaScript の ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) を使用して、フォームのデータを送信します。</span><span class="sxs-lookup"><span data-stu-id="b47dc-178">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="b47dc-179">検証は行われません。</span><span class="sxs-lookup"><span data-stu-id="b47dc-179">There's no validation.</span></span>

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

<span data-ttu-id="b47dc-180">[Fetch API がサポートされていない](https://caniuse.com/#feat=fetch)クライアントに対して JavaScript でフォーム POST を実行するには、次のいずれかの方法を使用します。</span><span class="sxs-lookup"><span data-stu-id="b47dc-180">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="b47dc-181">Fetch Polyfill を使用します (例: [window.fetch polyfill (github/fetch)](https://github.com/github/fetch))。</span><span class="sxs-lookup"><span data-stu-id="b47dc-181">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="b47dc-182">`XMLHttpRequest`を使用します。</span><span class="sxs-lookup"><span data-stu-id="b47dc-182">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="b47dc-183">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="b47dc-183">For example:</span></span>

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

<span data-ttu-id="b47dc-184">ファイルのアップロードをサポートするには、HTML フォームで `multipart/form-data` のエンコード タイプ (`enctype`) を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b47dc-184">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="b47dc-185">`files` 入力要素で複数のファイルのアップロードをサポートするには、`<input>` 要素で `multiple` 属性を指定します。</span><span class="sxs-lookup"><span data-stu-id="b47dc-185">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="b47dc-186">サーバーにアップロードされた個々のファイルには、<xref:Microsoft.AspNetCore.Http.IFormFile> を使用して[モデル バインド](xref:mvc/models/model-binding)でアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-186">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="b47dc-187">サンプル アプリでは、データベースおよび物理ストレージのシナリオでの複数のバッファー ファイル アップロードが示されています。</span><span class="sxs-lookup"><span data-stu-id="b47dc-187">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename"></a>

> [!WARNING]
> <span data-ttu-id="b47dc-188"><xref:Microsoft.AspNetCore.Http.IFormFile> の `FileName` プロパティは、表示とログ記録の目的以外に使用**しないでください**。</span><span class="sxs-lookup"><span data-stu-id="b47dc-188">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="b47dc-189">表示またはログ記録を行うときに、ファイル名を HTML エンコードします。</span><span class="sxs-lookup"><span data-stu-id="b47dc-189">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="b47dc-190">攻撃者は、完全パスまたは相対パスを含む悪意のあるファイル名を提供することがあります。</span><span class="sxs-lookup"><span data-stu-id="b47dc-190">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="b47dc-191">アプリケーションで次の処理を行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="b47dc-191">Applications should:</span></span>
>
> * <span data-ttu-id="b47dc-192">ユーザーが指定したファイル名からパスを削除します。</span><span class="sxs-lookup"><span data-stu-id="b47dc-192">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="b47dc-193">UI またはログ記録のために、HTML エンコードされ、パスが削除されたファイル名を保存します。</span><span class="sxs-lookup"><span data-stu-id="b47dc-193">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="b47dc-194">ストレージ用に新しいランダムなファイル名を生成します。</span><span class="sxs-lookup"><span data-stu-id="b47dc-194">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="b47dc-195">次のコードでは、ファイル名からパスを削除します。</span><span class="sxs-lookup"><span data-stu-id="b47dc-195">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="b47dc-196">これまでに示した例では、セキュリティ上の考慮事項については考えられていません。</span><span class="sxs-lookup"><span data-stu-id="b47dc-196">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="b47dc-197">以下のセクションおよび[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)で、追加の情報が提供されています。</span><span class="sxs-lookup"><span data-stu-id="b47dc-197">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="b47dc-198">セキュリティに関する考慮事項</span><span class="sxs-lookup"><span data-stu-id="b47dc-198">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="b47dc-199">検証</span><span class="sxs-lookup"><span data-stu-id="b47dc-199">Validation</span></span>](#validation)

<span data-ttu-id="b47dc-200">モデル バインドと <xref:Microsoft.AspNetCore.Http.IFormFile> を使用してファイルをアップロードする場合、アクション メソッドでは以下を受け入れることができます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-200">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="b47dc-201">1つの <xref:Microsoft.AspNetCore.Http.IFormFile>。</span><span class="sxs-lookup"><span data-stu-id="b47dc-201">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="b47dc-202">複数のファイルを表す次のいずれかのコレクション:</span><span class="sxs-lookup"><span data-stu-id="b47dc-202">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="b47dc-203">[表](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="b47dc-203">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="b47dc-204">バインドでは、名前でフォーム ファイルが照合されます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-204">Binding matches form files by name.</span></span> <span data-ttu-id="b47dc-205">たとえば、HTML の `<input type="file" name="formFile">` の `name` の値は、バインドされた C# のパラメーター/プロパティと一致する必要があります (`FormFile`)。</span><span class="sxs-lookup"><span data-stu-id="b47dc-205">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="b47dc-206">詳細については、「[name 属性の値を POST メソッドのパラメーター名に一致させる](#match-name-attribute-value-to-parameter-name-of-post-method)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b47dc-206">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="b47dc-207">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="b47dc-207">The following example:</span></span>

* <span data-ttu-id="b47dc-208">アップロードされた 1 つ以上のファイルをループします。</span><span class="sxs-lookup"><span data-stu-id="b47dc-208">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="b47dc-209">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) 使用して、ファイル名を含むファイルの完全なパスを返します。</span><span class="sxs-lookup"><span data-stu-id="b47dc-209">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="b47dc-210">アプリによって生成されたファイル名を使用して、ローカル ファイル システムにファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="b47dc-210">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="b47dc-211">アップロードされたファイルの合計数とサイズを返します。</span><span class="sxs-lookup"><span data-stu-id="b47dc-211">Returns the total number and size of files uploaded.</span></span>

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

<span data-ttu-id="b47dc-212">パスを除いてファイル名を生成するには、`Path.GetRandomFileName` を使用します。</span><span class="sxs-lookup"><span data-stu-id="b47dc-212">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="b47dc-213">次の例では、構成からパスを取得します。</span><span class="sxs-lookup"><span data-stu-id="b47dc-213">In the following example, the path is obtained from configuration:</span></span>

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

<span data-ttu-id="b47dc-214"> に渡すパスには、ファイル名が含まれている "<xref:System.IO.FileStream> *必要があります*"。</span><span class="sxs-lookup"><span data-stu-id="b47dc-214">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="b47dc-215">ファイル名を指定しないと、実行時に <xref:System.UnauthorizedAccessException> がスローされます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-215">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="b47dc-216"><xref:Microsoft.AspNetCore.Http.IFormFile> の方法を使用してアップロードされたファイルは、処理の前に、サーバー上のメモリまたはディスクのバッファーに格納されます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-216">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="b47dc-217">アクション メソッド内では、<xref:Microsoft.AspNetCore.Http.IFormFile> の内容には <xref:System.IO.Stream> としてアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-217">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="b47dc-218">ローカル ファイル システムに加えて、ネットワーク共有またはファイル ストレージ サービス ([Azure Blob Storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs) など) にファイルを保存することができます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-218">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="b47dc-219">アップロードのために複数のファイルをループし、安全なファイル名を使用する別の例については、サンプル アプリの *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b47dc-219">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="b47dc-220">以前の一時ファイルを削除せずに、65,535 個より多くのファイルを作成すると、[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) で <xref:System.IO.IOException> がスローされます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-220">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="b47dc-221">65,535 ファイルの制限は、サーバーごとの制限です。</span><span class="sxs-lookup"><span data-stu-id="b47dc-221">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="b47dc-222">Windows OS でのこの制限の詳細については、次のトピックの「解説」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b47dc-222">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="b47dc-223">GetTempFileNameA 関数</span><span class="sxs-lookup"><span data-stu-id="b47dc-223">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="b47dc-224">バッファー モデル バインドを使用して小さいファイルをデータベースにアップロードする</span><span class="sxs-lookup"><span data-stu-id="b47dc-224">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="b47dc-225">[Entity Framework](/ef/core/index) を使用してデータベースにバイナリ ファイル データを格納するには、エンティティで <xref:System.Byte> 配列プロパティを定義します。</span><span class="sxs-lookup"><span data-stu-id="b47dc-225">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="b47dc-226"><xref:Microsoft.AspNetCore.Http.IFormFile> が含まれるクラスに対してページ モデル プロパティを指定します。</span><span class="sxs-lookup"><span data-stu-id="b47dc-226">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

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
> <span data-ttu-id="b47dc-227"><xref:Microsoft.AspNetCore.Http.IFormFile> は、アクション メソッドのパラメーターとして直接、またはバインドされたモデル プロパティとして、使用することができます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-227"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="b47dc-228">前の例では、バインドされたモデル プロパティが使用されています。</span><span class="sxs-lookup"><span data-stu-id="b47dc-228">The prior example uses a bound model property.</span></span>

<span data-ttu-id="b47dc-229">は、 `FileUpload` ページ形式で使用され Razor ます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-229">The `FileUpload` is used in the Razor Pages form:</span></span>

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

<span data-ttu-id="b47dc-230">フォームがサーバーに POST されるときに、<xref:Microsoft.AspNetCore.Http.IFormFile> をストリームにコピーし、バイト配列としてデータベースに保存します。</span><span class="sxs-lookup"><span data-stu-id="b47dc-230">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="b47dc-231">次の例では、`_dbContext` によってアプリのデータベース コンテキストが格納されます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-231">In the following example, `_dbContext` stores the app's database context:</span></span>

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

<span data-ttu-id="b47dc-232">前の例は、次のサンプル アプリで示されているシナリオに似ています。</span><span class="sxs-lookup"><span data-stu-id="b47dc-232">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="b47dc-233">*Pages/BufferedSingleFileUploadDb.cshtml*</span><span class="sxs-lookup"><span data-stu-id="b47dc-233">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="b47dc-234">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="b47dc-234">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="b47dc-235">パフォーマンスに悪影響を与える可能性があるため、リレーショナル データベースにバイナリ データを格納する場合は注意してください。</span><span class="sxs-lookup"><span data-stu-id="b47dc-235">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="b47dc-236">検証を行わずに <xref:Microsoft.AspNetCore.Http.IFormFile> の `FileName` プロパティに依存したり、信頼したりしないでください。</span><span class="sxs-lookup"><span data-stu-id="b47dc-236">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="b47dc-237">`FileName` プロパティは、表示目的でのみ、HTML エンコードした後でだけ、使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b47dc-237">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="b47dc-238">示した例では、セキュリティ上の考慮事項については考えられていません。</span><span class="sxs-lookup"><span data-stu-id="b47dc-238">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="b47dc-239">以下のセクションおよび[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)で、追加の情報が提供されています。</span><span class="sxs-lookup"><span data-stu-id="b47dc-239">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="b47dc-240">セキュリティに関する考慮事項</span><span class="sxs-lookup"><span data-stu-id="b47dc-240">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="b47dc-241">検証</span><span class="sxs-lookup"><span data-stu-id="b47dc-241">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="b47dc-242">ストリーミングを使用して大きいファイルをアップロードする</span><span class="sxs-lookup"><span data-stu-id="b47dc-242">Upload large files with streaming</span></span>

<span data-ttu-id="b47dc-243">次の例では、JavaScript を使用してファイルをコントローラーのアクションにストリーミングする方法を示します。</span><span class="sxs-lookup"><span data-stu-id="b47dc-243">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="b47dc-244">ファイルの偽造防止トークンは、カスタム フィルター属性を使用して生成され、要求本文ではなくクライアント HTTP ヘッダーに渡されます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-244">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="b47dc-245">アクション メソッドではアップロードされたデータが直接処理されるため、フォーム モデル バインドは別のカスタム フィルターでは無効になります。</span><span class="sxs-lookup"><span data-stu-id="b47dc-245">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="b47dc-246">アクション内では、フォームのコンテンツが `MultipartReader` を使用して読み取られます。その場合、各 `MultipartSection` が読み取られ、必要に応じて、ファイルが処理されるかコンテンツが格納されます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-246">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="b47dc-247">マルチパート セクションが読み取られた後、アクションで独自のモデル バインドが実行されます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-247">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="b47dc-248">最初のページ応答ではフォームが読み込まれ、Cookie に偽造防止トークンが保存されます (`GenerateAntiforgeryTokenCookieAttribute` 属性を使用)。</span><span class="sxs-lookup"><span data-stu-id="b47dc-248">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="b47dc-249">その属性では、ASP.NET Core の組み込みの[偽造防止サポート](xref:security/anti-request-forgery)を使用して、要求トークンで Cookie が設定されます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-249">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="b47dc-250">`DisableFormValueModelBindingAttribute` は、モデル バインドを無効にするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-250">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="b47dc-251">このサンプルアプリで `GenerateAntiforgeryTokenCookieAttribute` は、とは、ページ `DisableFormValueModelBindingAttribute` `/StreamedSingleFileUploadDb` `/StreamedSingleFileUploadPhysical` `Startup.ConfigureServices` [ Razor 表記規則](xref:razor-pages/razor-pages-conventions)を使用するとのページアプリケーションモデルにフィルターとして適用されます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-251">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_AddRazorPages&highlight=8-11,17-20)]

<span data-ttu-id="b47dc-252">モデル バインドではフォームが読み取られないため、フォームからバインドされているパラメーターはバインドされません (クエリ、ルート、ヘッダーは引き続き機能します)。</span><span class="sxs-lookup"><span data-stu-id="b47dc-252">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="b47dc-253">アクション メソッドでは、`Request` プロパティが直接操作されます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-253">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="b47dc-254">`MultipartReader` は各セクションを読み取るために使用されます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-254">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="b47dc-255">キー/値データは `KeyValueAccumulator` に格納されます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-255">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="b47dc-256">マルチパート セクションが読み取られた後、`KeyValueAccumulator` の内容を使用して、フォーム データがモデル タイプにバインドされます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-256">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="b47dc-257">EF Core でデータベースにストリーミングするための完全な `StreamingController.UploadDatabase` メソッド:</span><span class="sxs-lookup"><span data-stu-id="b47dc-257">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="b47dc-258">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span><span class="sxs-lookup"><span data-stu-id="b47dc-258">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="b47dc-259">物理的な場所にストリーミングするための完全な `StreamingController.UploadPhysical` メソッド:</span><span class="sxs-lookup"><span data-stu-id="b47dc-259">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="b47dc-260">サンプル アプリでは、検証チェックは `FileHelpers.ProcessStreamedFile` によって処理されます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-260">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="b47dc-261">検証</span><span class="sxs-lookup"><span data-stu-id="b47dc-261">Validation</span></span>

<span data-ttu-id="b47dc-262">サンプル アプリの `FileHelpers` クラスでは、バッファーリングされた <xref:Microsoft.AspNetCore.Http.IFormFile> とストリーミングされたファイルのアップロードに関するいくつかのチェックが示されています。</span><span class="sxs-lookup"><span data-stu-id="b47dc-262">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="b47dc-263">サンプル アプリでのバッファーリングされたファイルのアップロード <xref:Microsoft.AspNetCore.Http.IFormFile> の処理については、*Utilities/FileHelpers.cs* ファイルの `ProcessFormFile` メソッドを参照してください。</span><span class="sxs-lookup"><span data-stu-id="b47dc-263">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="b47dc-264">ストリーミングされたファイルの処理については、同じファイルの `ProcessStreamedFile` メソッドを参照してください。</span><span class="sxs-lookup"><span data-stu-id="b47dc-264">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="b47dc-265">サンプル アプリで示されている検証処理メソッドでは、アップロードされたファイルの内容はスキャンされません。</span><span class="sxs-lookup"><span data-stu-id="b47dc-265">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="b47dc-266">ほとんどの運用シナリオでは、ファイルをユーザーまたは他のシステムで使用できるようにする前に、ウイルス/マルウェア スキャナー API が使用されます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-266">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="b47dc-267">このトピックのサンプルでは検証技法の実際の例が示されていますが、次の場合を除き、運用アプリでは `FileHelpers` クラスを実装しないでください。</span><span class="sxs-lookup"><span data-stu-id="b47dc-267">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="b47dc-268">実装を完全に理解している。</span><span class="sxs-lookup"><span data-stu-id="b47dc-268">Fully understand the implementation.</span></span>
> * <span data-ttu-id="b47dc-269">アプリの環境と仕様に合わせて実装が適切に変更されている。</span><span class="sxs-lookup"><span data-stu-id="b47dc-269">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="b47dc-270">**これらの要件に対応することなく、アプリにセキュリティ コードをむやみに実装しないでください。**</span><span class="sxs-lookup"><span data-stu-id="b47dc-270">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="b47dc-271">コンテンツの検証</span><span class="sxs-lookup"><span data-stu-id="b47dc-271">Content validation</span></span>

<span data-ttu-id="b47dc-272">**アップロードされた内容に対してサードパーティ製のウイルス/マルウェア スキャン API を使用します。**</span><span class="sxs-lookup"><span data-stu-id="b47dc-272">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="b47dc-273">大容量のシナリオでは、ファイルのスキャンに大量のサーバー リソースが必要です。</span><span class="sxs-lookup"><span data-stu-id="b47dc-273">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="b47dc-274">ファイルのスキャンによって要求の処理パフォーマンスが低下する場合は、スキャン処理を[バックグラウンド サービス](xref:fundamentals/host/hosted-services)にオフロードすることを検討してください (たとえば、アプリのサーバーとは異なるサーバーで実行されているサービス)。</span><span class="sxs-lookup"><span data-stu-id="b47dc-274">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="b47dc-275">通常、アップロードされたファイルは、バックグラウンドのウイルス検索プログラムによってチェックされるまで、検疫された領域に保持されます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-275">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="b47dc-276">合格したファイルは、通常のファイル ストレージの場所に移動されます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-276">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="b47dc-277">これらの手順は、通常、ファイルのスキャン状態を示すデータベース レコードと共に実行されます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-277">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="b47dc-278">このような方法を使用することで、アプリとアプリ サーバーは要求への応答に集中できます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-278">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="b47dc-279">ファイル拡張子の検証</span><span class="sxs-lookup"><span data-stu-id="b47dc-279">File extension validation</span></span>

<span data-ttu-id="b47dc-280">アップロードされたファイルの拡張子を、許可されている拡張子のリストで確認する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b47dc-280">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="b47dc-281">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="b47dc-281">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="b47dc-282">ファイルのシグネチャの検証</span><span class="sxs-lookup"><span data-stu-id="b47dc-282">File signature validation</span></span>

<span data-ttu-id="b47dc-283">ファイルのシグネチャは、ファイルの先頭にある最初の数バイトによって決定されます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-283">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="b47dc-284">これらのバイトを使用して、拡張子がファイルの内容と一致するかどうかを示すことができます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-284">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="b47dc-285">サンプル アプリでは、いくつかの一般的なファイルの種類についてファイルのシグネチャがチェックされています。</span><span class="sxs-lookup"><span data-stu-id="b47dc-285">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="b47dc-286">次の例では、JPEG イメージのファイルのシグネチャが、ファイルに対してチェックされています。</span><span class="sxs-lookup"><span data-stu-id="b47dc-286">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

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

<span data-ttu-id="b47dc-287">追加のファイル シグネチャを取得するには、「[ファイル シグネチャ データベース](https://www.filesignatures.net/)」および公式なファイルの仕様を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b47dc-287">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="b47dc-288">ファイル名のセキュリティ</span><span class="sxs-lookup"><span data-stu-id="b47dc-288">File name security</span></span>

<span data-ttu-id="b47dc-289">ファイルを物理ストレージに保存する場合は、クライアント指定のファイル名を使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="b47dc-289">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="b47dc-290">[Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) または [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) を使用して、ファイルの安全なファイル名を作成し、一時ストレージの完全なパス (ファイル名を含む) を作成します。</span><span class="sxs-lookup"><span data-stu-id="b47dc-290">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

Razor<span data-ttu-id="b47dc-291">プロパティ値が自動的に HTML エンコードされて表示されます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-291"> automatically HTML encodes property values for display.</span></span> <span data-ttu-id="b47dc-292">次のコードは安全に使用できます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-292">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="b47dc-293">の外部 Razor では、常に <xref:System.Net.WebUtility.HtmlEncode*> ユーザーの要求の内容をファイル名で指定します。</span><span class="sxs-lookup"><span data-stu-id="b47dc-293">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="b47dc-294">多くの実装には、ファイルが存在するかどうかのチェックを含める必要があります。そうしないと、同じ名前のファイルによってファイルが上書きされます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-294">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="b47dc-295">アプリの仕様を満たす追加のロジックを提供します。</span><span class="sxs-lookup"><span data-stu-id="b47dc-295">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="b47dc-296">サイズの検証</span><span class="sxs-lookup"><span data-stu-id="b47dc-296">Size validation</span></span>

<span data-ttu-id="b47dc-297">アップロードされるファイルのサイズを制限します。</span><span class="sxs-lookup"><span data-stu-id="b47dc-297">Limit the size of uploaded files.</span></span>

<span data-ttu-id="b47dc-298">サンプル アプリでは、ファイルのサイズは 2 MB (バイト単位) に制限されています。</span><span class="sxs-lookup"><span data-stu-id="b47dc-298">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="b47dc-299">その制限は、*appsettings.json* ファイルの [Configuration](xref:fundamentals/configuration/index) によって提供されます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-299">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="b47dc-300">`FileSizeLimit` が `PageModel` クラスに挿入されます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-300">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

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

<span data-ttu-id="b47dc-301">ファイルのサイズが制限を超えると、ファイルは拒否されます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-301">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="b47dc-302">name 属性の値を POST メソッドのパラメーター名に一致させる</span><span class="sxs-lookup"><span data-stu-id="b47dc-302">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="b47dc-303">フォームデータを Razor ポストするか、JavaScript の直接を使用する非フォームでは `FormData` 、フォームの要素で指定された名前、または `FormData` コントローラーのアクション内のパラメーターの名前と一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b47dc-303">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="b47dc-304">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="b47dc-304">In the following example:</span></span>

* <span data-ttu-id="b47dc-305">`<input>` 要素を使用すると、`name` 属性には値 `battlePlans` が設定されます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-305">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="b47dc-306">JavaScript で `FormData` を使用すると、name には値 `battlePlans` が設定されます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-306">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="b47dc-307">C# メソッドのパラメーターに一致する名前を使用します (`battlePlans`)。</span><span class="sxs-lookup"><span data-stu-id="b47dc-307">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="b47dc-308">Razorという名前のページページハンドラーメソッドの場合 `Upload` :</span><span class="sxs-lookup"><span data-stu-id="b47dc-308">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="b47dc-309">MVC POST コントローラー アクション メソッドの場合:</span><span class="sxs-lookup"><span data-stu-id="b47dc-309">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="b47dc-310">サーバーとアプリの構成</span><span class="sxs-lookup"><span data-stu-id="b47dc-310">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="b47dc-311">マルチパート本文の長さの制限</span><span class="sxs-lookup"><span data-stu-id="b47dc-311">Multipart body length limit</span></span>

<span data-ttu-id="b47dc-312"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> では、各マルチパート本文の長さの制限が設定されます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-312"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="b47dc-313">この制限を超えるフォーム セクションでは、解析時に <xref:System.IO.InvalidDataException> がスローされます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-313">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="b47dc-314">既定値は 134,217,728 (128 MB) です。</span><span class="sxs-lookup"><span data-stu-id="b47dc-314">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="b47dc-315">制限をカスタマイズするには、`Startup.ConfigureServices` の設定 <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> を使用します。</span><span class="sxs-lookup"><span data-stu-id="b47dc-315">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="b47dc-316">単一ページまたはアクションに対する <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> を設定するには、<xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> を使用します。</span><span class="sxs-lookup"><span data-stu-id="b47dc-316"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="b47dc-317">RazorPages アプリで、で[規則](xref:razor-pages/razor-pages-conventions)を適用したフィルターを適用し `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-317">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddRazorPages()
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
    });
```

<span data-ttu-id="b47dc-318">Razorページアプリまたは MVC アプリで、ページモデルまたはアクションメソッドにフィルターを適用します。</span><span class="sxs-lookup"><span data-stu-id="b47dc-318">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="b47dc-319">Kestrel の最大要求本文サイズ</span><span class="sxs-lookup"><span data-stu-id="b47dc-319">Kestrel maximum request body size</span></span>

<span data-ttu-id="b47dc-320">Kestrel によってホストされるアプリの場合、既定の要求本文の最大サイズは、30,000,000 バイトです。これは約 28.6 MB になります。</span><span class="sxs-lookup"><span data-stu-id="b47dc-320">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="b47dc-321">制限をカスタマイズするには、[MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel サーバー オプションを使用します。</span><span class="sxs-lookup"><span data-stu-id="b47dc-321">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

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

<span data-ttu-id="b47dc-322">単一ページまたはアクションに対する [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) を設定するには、<xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> を使用します。</span><span class="sxs-lookup"><span data-stu-id="b47dc-322"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="b47dc-323">RazorPages アプリで、で[規則](xref:razor-pages/razor-pages-conventions)を適用したフィルターを適用し `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-323">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddRazorPages()
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
    });
```

<span data-ttu-id="b47dc-324">Razorページアプリまたは MVC アプリで、フィルターをページハンドラークラスまたはアクションメソッドに適用します。</span><span class="sxs-lookup"><span data-stu-id="b47dc-324">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

<span data-ttu-id="b47dc-325">は、 `RequestSizeLimitAttribute` ディレクティブを使用して適用することもでき [`@attribute`](xref:mvc/views/razor#attribute) Razor ます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-325">The `RequestSizeLimitAttribute` can also be applied using the [`@attribute`](xref:mvc/views/razor#attribute) Razor directive:</span></span>

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="b47dc-326">その他の Kestrel の制限</span><span class="sxs-lookup"><span data-stu-id="b47dc-326">Other Kestrel limits</span></span>

<span data-ttu-id="b47dc-327">Kestrel によってホストされるアプリには、他の Kestrel の制限が適用される場合があります。</span><span class="sxs-lookup"><span data-stu-id="b47dc-327">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="b47dc-328">クライアントの最大接続数</span><span class="sxs-lookup"><span data-stu-id="b47dc-328">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="b47dc-329">要求と応答のデータ レート</span><span class="sxs-lookup"><span data-stu-id="b47dc-329">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a><span data-ttu-id="b47dc-330">IIS の内容の長さの制限</span><span class="sxs-lookup"><span data-stu-id="b47dc-330">IIS content length limit</span></span>

<span data-ttu-id="b47dc-331">既定の要求の制限 (`maxAllowedContentLength`) は、30,000,000 バイトです。これは約 28.6 MB になります。</span><span class="sxs-lookup"><span data-stu-id="b47dc-331">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6MB.</span></span> <span data-ttu-id="b47dc-332">*web.config* ファイルで制限をカスタマイズします。</span><span class="sxs-lookup"><span data-stu-id="b47dc-332">Customize the limit in the *web.config* file:</span></span>

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

<span data-ttu-id="b47dc-333">この設定は IIS にのみ適用されます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-333">This setting only applies to IIS.</span></span> <span data-ttu-id="b47dc-334">Kestrel でホストする場合、既定ではこの動作は発生しません。</span><span class="sxs-lookup"><span data-stu-id="b47dc-334">The behavior doesn't occur by default when hosting on Kestrel.</span></span> <span data-ttu-id="b47dc-335">詳細については、「[要求制限 \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b47dc-335">For more information, see [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="b47dc-336">ASP.NET Core モジュールでの制限または IIS 要求フィルター モジュールの存在により、アップロードが 2 または 4 GB に制限される場合があります。</span><span class="sxs-lookup"><span data-stu-id="b47dc-336">Limitations in the ASP.NET Core Module or presence of the IIS Request Filtering Module may limit uploads to either 2 or 4 GB.</span></span> <span data-ttu-id="b47dc-337">詳細については、「[サイズが 2 GB を超えるファイルをアップロードできない (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b47dc-337">For more information, see [Unable to upload file greater than 2GB in size (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="b47dc-338">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="b47dc-338">Troubleshoot</span></span>

<span data-ttu-id="b47dc-339">ファイルのアップロード時に発生する一般的ないくつかの問題と、考えられる解決策を以下に示します。</span><span class="sxs-lookup"><span data-stu-id="b47dc-339">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="b47dc-340">IIS サーバーに展開したときの "見つかりません" エラー</span><span class="sxs-lookup"><span data-stu-id="b47dc-340">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="b47dc-341">次のエラーは、アップロードされるファイルがサーバーの構成されている内容の長さを超えていることを示します。</span><span class="sxs-lookup"><span data-stu-id="b47dc-341">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="b47dc-342">制限を増やす方法の詳細については、「[IIS の内容の長さの制限](#iis-content-length-limit)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="b47dc-342">For more information on increasing the limit, see the [IIS content length limit](#iis-content-length-limit) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="b47dc-343">接続エラー</span><span class="sxs-lookup"><span data-stu-id="b47dc-343">Connection failure</span></span>

<span data-ttu-id="b47dc-344">接続エラーおよびサーバー接続のリセットは、アップロードされるファイルが Kestrel の最大要求本文サイズを超えていることを示している場合があります。</span><span class="sxs-lookup"><span data-stu-id="b47dc-344">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="b47dc-345">詳細については、[Kestrel の最大要求本文サイズ](#kestrel-maximum-request-body-size)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="b47dc-345">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="b47dc-346">Kestrel クライアント接続の制限の調整も必要な場合があります。</span><span class="sxs-lookup"><span data-stu-id="b47dc-346">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="b47dc-347">IFormFile での null 参照例外</span><span class="sxs-lookup"><span data-stu-id="b47dc-347">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="b47dc-348">コントローラーが <xref:Microsoft.AspNetCore.Http.IFormFile> を使用してアップロードされたファイルを受け取っても、値が `null` の場合は、HTML フォームで `multipart/form-data` に値 `enctype` が指定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="b47dc-348">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="b47dc-349">この属性が `<form>` 要素で設定されていない場合、ファイルはアップロードされず、バインドされた <xref:Microsoft.AspNetCore.Http.IFormFile> 引数は `null` になります。</span><span class="sxs-lookup"><span data-stu-id="b47dc-349">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="b47dc-350">また、[フォーム データでのアップロードの名前がアプリの名前と一致する](#match-name-attribute-value-to-parameter-name-of-post-method)ことを確認します。</span><span class="sxs-lookup"><span data-stu-id="b47dc-350">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="b47dc-351">ストリームが長すぎる</span><span class="sxs-lookup"><span data-stu-id="b47dc-351">Stream was too long</span></span>

<span data-ttu-id="b47dc-352">このトピックの例は、アップロードされたファイル コンテンツを保持するために <xref:System.IO.MemoryStream> に依存しています。</span><span class="sxs-lookup"><span data-stu-id="b47dc-352">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="b47dc-353">`int.MaxValue` のサイズ制限は `MemoryStream` です。</span><span class="sxs-lookup"><span data-stu-id="b47dc-353">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="b47dc-354">アプリのファイル アップロード シナリオで 50 MB を超えるファイル コンテンツを保持する必要がある場合は、アップロードされたファイルのコンテンツを 1 つの `MemoryStream` に依存することなく保持する別のアプローチを使用します。</span><span class="sxs-lookup"><span data-stu-id="b47dc-354">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b47dc-355">ASP.NET Core では、小さいファイルの場合はバッファー モデル バインドを使用し、大きいファイルの場合は非バッファー ストリーミングを使用して、1 つ以上のファイルのアップロードがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="b47dc-355">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="b47dc-356">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="b47dc-356">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="b47dc-357">セキュリティに関する考慮事項</span><span class="sxs-lookup"><span data-stu-id="b47dc-357">Security considerations</span></span>

<span data-ttu-id="b47dc-358">サーバーにファイルをアップロードする機能をユーザーに提供するときは、十分に注意してください。</span><span class="sxs-lookup"><span data-stu-id="b47dc-358">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="b47dc-359">攻撃者が次のようなことを試みる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="b47dc-359">Attackers may attempt to:</span></span>

* <span data-ttu-id="b47dc-360">[サービス拒否](/windows-hardware/drivers/ifs/denial-of-service)攻撃を実行する。</span><span class="sxs-lookup"><span data-stu-id="b47dc-360">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="b47dc-361">ウイルスまたはマルウェアをアップロードする。</span><span class="sxs-lookup"><span data-stu-id="b47dc-361">Upload viruses or malware.</span></span>
* <span data-ttu-id="b47dc-362">ネットワークやサーバーを他の方法で侵害する。</span><span class="sxs-lookup"><span data-stu-id="b47dc-362">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="b47dc-363">攻撃の成功の可能性を少なくするセキュリティ手順は、次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="b47dc-363">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="b47dc-364">専用のファイル アップロード領域 (できれば、システム ドライブ以外) にファイルをアップロードします。</span><span class="sxs-lookup"><span data-stu-id="b47dc-364">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="b47dc-365">専用の場所を使用すると、アップロードされるファイルにセキュリティ制限を適用しやすくなります。</span><span class="sxs-lookup"><span data-stu-id="b47dc-365">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="b47dc-366">ファイルのアップロード場所に対する実行アクセス許可を無効にします。&dagger;</span><span class="sxs-lookup"><span data-stu-id="b47dc-366">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="b47dc-367">アプリと同じディレクトリ ツリーに、アップロードしたファイルを保持**しないでください**。&dagger;</span><span class="sxs-lookup"><span data-stu-id="b47dc-367">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="b47dc-368">アプリによって決められた安全なファイル名を使用します。</span><span class="sxs-lookup"><span data-stu-id="b47dc-368">Use a safe file name determined by the app.</span></span> <span data-ttu-id="b47dc-369">ユーザーが指定したファイル名や、アップロードしたファイルの信頼されていないファイル名を使用しないでください。 &dagger;HTML は、表示時に信頼されていないファイル名をエンコードします。</span><span class="sxs-lookup"><span data-stu-id="b47dc-369">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="b47dc-370">たとえば、ファイル名をログに記録したり、UI に表示したりし Razor ます (出力を自動的に HTML エンコードします)。</span><span class="sxs-lookup"><span data-stu-id="b47dc-370">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="b47dc-371">アプリの設計仕様に対して承認されているファイル拡張子のみを許可します。&dagger;</span><span class="sxs-lookup"><span data-stu-id="b47dc-371">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="b47dc-372">クライアント側のチェックがサーバーで実行されていることを確認します。 &dagger;クライアント側のチェックは簡単に回避できます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-372">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="b47dc-373">アップロードされたファイルのサイズをチェックします。</span><span class="sxs-lookup"><span data-stu-id="b47dc-373">Check the size of an uploaded file.</span></span> <span data-ttu-id="b47dc-374">サイズの大きなアップロードを防ぐために、最大サイズ制限を設定します。&dagger;</span><span class="sxs-lookup"><span data-stu-id="b47dc-374">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="b47dc-375">同じ名前でアップロードされたファイルによってファイルが上書きされないようにする必要があるときは、ファイルをアップロードする前に、データベースまたは物理ストレージに対してファイル名を確認します。</span><span class="sxs-lookup"><span data-stu-id="b47dc-375">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="b47dc-376">**ファイルを格納する前に、アップロードされる内容に対してウイルス/マルウェア スキャナーを実行します。**</span><span class="sxs-lookup"><span data-stu-id="b47dc-376">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="b47dc-377">&dagger;サンプル アプリで、条件を満たす方法が示されています。</span><span class="sxs-lookup"><span data-stu-id="b47dc-377">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="b47dc-378">システムへの悪意のあるコードのアップロードは、頻繁に次のような内容のコードの実行するための足がかりとなります。</span><span class="sxs-lookup"><span data-stu-id="b47dc-378">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="b47dc-379">システムの制御を完全に掌握する。</span><span class="sxs-lookup"><span data-stu-id="b47dc-379">Completely gain control of a system.</span></span>
> * <span data-ttu-id="b47dc-380">システムがクラッシュする結果で、システムを過負荷状態にする。</span><span class="sxs-lookup"><span data-stu-id="b47dc-380">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="b47dc-381">ユーザーまたはシステムのデータを破壊する。</span><span class="sxs-lookup"><span data-stu-id="b47dc-381">Compromise user or system data.</span></span>
> * <span data-ttu-id="b47dc-382">パブリック UI に落書きする。</span><span class="sxs-lookup"><span data-stu-id="b47dc-382">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="b47dc-383">ユーザーからファイルを受け入れる際の外部アクセスによる攻撃を減らす方法については、次の資料を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b47dc-383">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * [<span data-ttu-id="b47dc-384">Unrestricted File Upload (ファイルの無制限のアップロード)</span><span class="sxs-lookup"><span data-stu-id="b47dc-384">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [<span data-ttu-id="b47dc-385">Azure セキュリティ: ユーザーから受け入れるファイルに適切な管理制御を整備する</span><span class="sxs-lookup"><span data-stu-id="b47dc-385">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="b47dc-386">サンプル アプリの例など、セキュリティ対策の実装の詳細については、「[検証](#validation)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="b47dc-386">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="b47dc-387">ストレージのシナリオ</span><span class="sxs-lookup"><span data-stu-id="b47dc-387">Storage scenarios</span></span>

<span data-ttu-id="b47dc-388">ファイルの一般的なストレージ オプションには次のようなものがあります。</span><span class="sxs-lookup"><span data-stu-id="b47dc-388">Common storage options for files include:</span></span>

* <span data-ttu-id="b47dc-389">データベース</span><span class="sxs-lookup"><span data-stu-id="b47dc-389">Database</span></span>

  * <span data-ttu-id="b47dc-390">小さいファイルをアップロードする場合、物理ストレージ (ファイル システムまたはネットワーク共有) のオプションよりデータベースの方が速いことがよくあります。</span><span class="sxs-lookup"><span data-stu-id="b47dc-390">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="b47dc-391">多くの場合、ユーザー データに対するデータベース レコードの取得でファイルの内容を同時に提供できるため (たとえば、アバター イメージ)、データベースの方が物理的なストレージ オプションより便利です。</span><span class="sxs-lookup"><span data-stu-id="b47dc-391">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="b47dc-392">データベースは、データ ストレージ サービスを使用するよりコストが低くなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="b47dc-392">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="b47dc-393">物理ストレージ (ファイル システムまたはネットワーク共有)</span><span class="sxs-lookup"><span data-stu-id="b47dc-393">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="b47dc-394">大きいファイルのアップロードの場合:</span><span class="sxs-lookup"><span data-stu-id="b47dc-394">For large file uploads:</span></span>
    * <span data-ttu-id="b47dc-395">データベースの制限によって、アップロードのサイズが制限される場合があります。</span><span class="sxs-lookup"><span data-stu-id="b47dc-395">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="b47dc-396">多くの場合、物理ストレージはデータベース内のストレージより高コストです。</span><span class="sxs-lookup"><span data-stu-id="b47dc-396">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="b47dc-397">物理ストレージは、データ ストレージ サービスを使用するよりコストが低くなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="b47dc-397">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="b47dc-398">アプリのプロセスには、ストレージの場所に対する読み取りと書き込みのアクセス許可が必要です。</span><span class="sxs-lookup"><span data-stu-id="b47dc-398">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="b47dc-399">**実行アクセス許可は付与しないでください。**</span><span class="sxs-lookup"><span data-stu-id="b47dc-399">**Never grant execute permission.**</span></span>

* <span data-ttu-id="b47dc-400">データ ストレージ サービス (例: [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span><span class="sxs-lookup"><span data-stu-id="b47dc-400">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="b47dc-401">通常、サービスでは、大抵の場合に単一障害点となるオンプレミス ソリューションより高いスケーラビリティと回復性が提供されます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-401">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="b47dc-402">大規模なストレージ インフラストラクチャのシナリオでは、サービスのコストが低下する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="b47dc-402">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="b47dc-403">詳細については、「[クイックスタート: .net を使用してオブジェクトストレージに blob を作成する](/azure/storage/blobs/storage-quickstart-blobs-dotnet)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b47dc-403">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span> <span data-ttu-id="b47dc-404">そのトピックでは <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*> が示されていますが、<xref:System.IO.Stream> を使用する場合は、<xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> を使用して <xref:System.IO.FileStream> を Blob Storage に保存することもできます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-404">The topic demonstrates <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, but <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> can be used to save a <xref:System.IO.FileStream> to blob storage when working with a <xref:System.IO.Stream>.</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="b47dc-405">ファイル アップロードのシナリオ</span><span class="sxs-lookup"><span data-stu-id="b47dc-405">File upload scenarios</span></span>

<span data-ttu-id="b47dc-406">ファイルをアップロードするための一般的な 2 つの方法は、バッファーリングとストリーミングです。</span><span class="sxs-lookup"><span data-stu-id="b47dc-406">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="b47dc-407">**Buffering**</span><span class="sxs-lookup"><span data-stu-id="b47dc-407">**Buffering**</span></span>

<span data-ttu-id="b47dc-408">ファイル全体が <xref:Microsoft.AspNetCore.Http.IFormFile> に読み込まれます。これは、ファイルの処理または保存に使用される C# でのファイルの表現です。</span><span class="sxs-lookup"><span data-stu-id="b47dc-408">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="b47dc-409">ファイルのアップロードで使用されるリソース (ディスク、メモリM) は、同時ファイル アップロードの数とサイズによって異なります。</span><span class="sxs-lookup"><span data-stu-id="b47dc-409">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="b47dc-410">アプリであまり多くのアップロードをバッファーに格納しようとすると、メモリまたはディスク領域が不足したときにサイトがクラッシュします。</span><span class="sxs-lookup"><span data-stu-id="b47dc-410">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="b47dc-411">ファイルのアップロードのサイズまたは頻度によりアプリのリソースが不足する場合は、ストリーミングを使用します。</span><span class="sxs-lookup"><span data-stu-id="b47dc-411">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="b47dc-412">1 つで 64 KB を超えるバッファー ファイルは、メモリからディスク上の一時ファイルに移動されます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-412">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="b47dc-413">小さいファイルのバッファーリングについては、後のセクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="b47dc-413">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="b47dc-414">物理ストレージ</span><span class="sxs-lookup"><span data-stu-id="b47dc-414">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* <span data-ttu-id="b47dc-415">[[データベース]](#upload-small-files-with-buffered-model-binding-to-a-database)</span><span class="sxs-lookup"><span data-stu-id="b47dc-415">[Database](#upload-small-files-with-buffered-model-binding-to-a-database)</span></span>

<span data-ttu-id="b47dc-416">**ストリーミング**</span><span class="sxs-lookup"><span data-stu-id="b47dc-416">**Streaming**</span></span>

<span data-ttu-id="b47dc-417">ファイルはマルチパート要求から受信され、アプリによって直接処理または保存されます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-417">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="b47dc-418">ストリーミングによってパフォーマンスが大幅に向上することはありません。</span><span class="sxs-lookup"><span data-stu-id="b47dc-418">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="b47dc-419">ストリーミングを使用すると、ファイルをアップロードするときのメモリまたはディスク領域の需要を減らすことができます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-419">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="b47dc-420">大きいファイルのストリーミングについては、「[ストリーミングを使用して大きいファイルをアップロードする](#upload-large-files-with-streaming)」で説明します。</span><span class="sxs-lookup"><span data-stu-id="b47dc-420">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="b47dc-421">バッファー モデル バインドを使用して小さいファイルを物理ストレージにアップロードする</span><span class="sxs-lookup"><span data-stu-id="b47dc-421">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="b47dc-422">小さいファイルをアップロードするには、マルチパート形式を使用するか、または JavaScript を使用して POST 要求を作成します。</span><span class="sxs-lookup"><span data-stu-id="b47dc-422">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="b47dc-423">次の例では、ページフォームを使用して Razor 1 つのファイルをアップロードします (サンプルアプリの*Pages/BufferedSingleFileUploadPhysical* )。</span><span class="sxs-lookup"><span data-stu-id="b47dc-423">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

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

<span data-ttu-id="b47dc-424">次の例は前の例と似ていますが、以下の点が異なります。</span><span class="sxs-lookup"><span data-stu-id="b47dc-424">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="b47dc-425">JavaScript の ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) を使用して、フォームのデータを送信します。</span><span class="sxs-lookup"><span data-stu-id="b47dc-425">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="b47dc-426">検証は行われません。</span><span class="sxs-lookup"><span data-stu-id="b47dc-426">There's no validation.</span></span>

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

<span data-ttu-id="b47dc-427">[Fetch API がサポートされていない](https://caniuse.com/#feat=fetch)クライアントに対して JavaScript でフォーム POST を実行するには、次のいずれかの方法を使用します。</span><span class="sxs-lookup"><span data-stu-id="b47dc-427">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="b47dc-428">Fetch Polyfill を使用します (例: [window.fetch polyfill (github/fetch)](https://github.com/github/fetch))。</span><span class="sxs-lookup"><span data-stu-id="b47dc-428">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="b47dc-429">`XMLHttpRequest`を使用します。</span><span class="sxs-lookup"><span data-stu-id="b47dc-429">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="b47dc-430">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="b47dc-430">For example:</span></span>

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

<span data-ttu-id="b47dc-431">ファイルのアップロードをサポートするには、HTML フォームで `multipart/form-data` のエンコード タイプ (`enctype`) を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b47dc-431">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="b47dc-432">`files` 入力要素で複数のファイルのアップロードをサポートするには、`<input>` 要素で `multiple` 属性を指定します。</span><span class="sxs-lookup"><span data-stu-id="b47dc-432">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="b47dc-433">サーバーにアップロードされた個々のファイルには、<xref:Microsoft.AspNetCore.Http.IFormFile> を使用して[モデル バインド](xref:mvc/models/model-binding)でアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-433">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="b47dc-434">サンプル アプリでは、データベースおよび物理ストレージのシナリオでの複数のバッファー ファイル アップロードが示されています。</span><span class="sxs-lookup"><span data-stu-id="b47dc-434">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename2"></a>

> [!WARNING]
> <span data-ttu-id="b47dc-435"><xref:Microsoft.AspNetCore.Http.IFormFile> の `FileName` プロパティは、表示とログ記録の目的以外に使用**しないでください**。</span><span class="sxs-lookup"><span data-stu-id="b47dc-435">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="b47dc-436">表示またはログ記録を行うときに、ファイル名を HTML エンコードします。</span><span class="sxs-lookup"><span data-stu-id="b47dc-436">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="b47dc-437">攻撃者は、完全パスまたは相対パスを含む悪意のあるファイル名を提供することがあります。</span><span class="sxs-lookup"><span data-stu-id="b47dc-437">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="b47dc-438">アプリケーションで次の処理を行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="b47dc-438">Applications should:</span></span>
>
> * <span data-ttu-id="b47dc-439">ユーザーが指定したファイル名からパスを削除します。</span><span class="sxs-lookup"><span data-stu-id="b47dc-439">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="b47dc-440">UI またはログ記録のために、HTML エンコードされ、パスが削除されたファイル名を保存します。</span><span class="sxs-lookup"><span data-stu-id="b47dc-440">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="b47dc-441">ストレージ用に新しいランダムなファイル名を生成します。</span><span class="sxs-lookup"><span data-stu-id="b47dc-441">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="b47dc-442">次のコードでは、ファイル名からパスを削除します。</span><span class="sxs-lookup"><span data-stu-id="b47dc-442">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="b47dc-443">これまでに示した例では、セキュリティ上の考慮事項については考えられていません。</span><span class="sxs-lookup"><span data-stu-id="b47dc-443">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="b47dc-444">以下のセクションおよび[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)で、追加の情報が提供されています。</span><span class="sxs-lookup"><span data-stu-id="b47dc-444">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="b47dc-445">セキュリティに関する考慮事項</span><span class="sxs-lookup"><span data-stu-id="b47dc-445">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="b47dc-446">検証</span><span class="sxs-lookup"><span data-stu-id="b47dc-446">Validation</span></span>](#validation)

<span data-ttu-id="b47dc-447">モデル バインドと <xref:Microsoft.AspNetCore.Http.IFormFile> を使用してファイルをアップロードする場合、アクション メソッドでは以下を受け入れることができます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-447">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="b47dc-448">1つの <xref:Microsoft.AspNetCore.Http.IFormFile>。</span><span class="sxs-lookup"><span data-stu-id="b47dc-448">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="b47dc-449">複数のファイルを表す次のいずれかのコレクション:</span><span class="sxs-lookup"><span data-stu-id="b47dc-449">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="b47dc-450">[表](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="b47dc-450">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="b47dc-451">バインドでは、名前でフォーム ファイルが照合されます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-451">Binding matches form files by name.</span></span> <span data-ttu-id="b47dc-452">たとえば、HTML の `<input type="file" name="formFile">` の `name` の値は、バインドされた C# のパラメーター/プロパティと一致する必要があります (`FormFile`)。</span><span class="sxs-lookup"><span data-stu-id="b47dc-452">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="b47dc-453">詳細については、「[name 属性の値を POST メソッドのパラメーター名に一致させる](#match-name-attribute-value-to-parameter-name-of-post-method)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b47dc-453">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="b47dc-454">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="b47dc-454">The following example:</span></span>

* <span data-ttu-id="b47dc-455">アップロードされた 1 つ以上のファイルをループします。</span><span class="sxs-lookup"><span data-stu-id="b47dc-455">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="b47dc-456">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) 使用して、ファイル名を含むファイルの完全なパスを返します。</span><span class="sxs-lookup"><span data-stu-id="b47dc-456">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="b47dc-457">アプリによって生成されたファイル名を使用して、ローカル ファイル システムにファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="b47dc-457">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="b47dc-458">アップロードされたファイルの合計数とサイズを返します。</span><span class="sxs-lookup"><span data-stu-id="b47dc-458">Returns the total number and size of files uploaded.</span></span>

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

<span data-ttu-id="b47dc-459">パスを除いてファイル名を生成するには、`Path.GetRandomFileName` を使用します。</span><span class="sxs-lookup"><span data-stu-id="b47dc-459">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="b47dc-460">次の例では、構成からパスを取得します。</span><span class="sxs-lookup"><span data-stu-id="b47dc-460">In the following example, the path is obtained from configuration:</span></span>

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

<span data-ttu-id="b47dc-461"> に渡すパスには、ファイル名が含まれている "<xref:System.IO.FileStream> *必要があります*"。</span><span class="sxs-lookup"><span data-stu-id="b47dc-461">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="b47dc-462">ファイル名を指定しないと、実行時に <xref:System.UnauthorizedAccessException> がスローされます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-462">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="b47dc-463"><xref:Microsoft.AspNetCore.Http.IFormFile> の方法を使用してアップロードされたファイルは、処理の前に、サーバー上のメモリまたはディスクのバッファーに格納されます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-463">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="b47dc-464">アクション メソッド内では、<xref:Microsoft.AspNetCore.Http.IFormFile> の内容には <xref:System.IO.Stream> としてアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-464">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="b47dc-465">ローカル ファイル システムに加えて、ネットワーク共有またはファイル ストレージ サービス ([Azure Blob Storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs) など) にファイルを保存することができます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-465">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="b47dc-466">アップロードのために複数のファイルをループし、安全なファイル名を使用する別の例については、サンプル アプリの *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b47dc-466">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="b47dc-467">以前の一時ファイルを削除せずに、65,535 個より多くのファイルを作成すると、[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) で <xref:System.IO.IOException> がスローされます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-467">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="b47dc-468">65,535 ファイルの制限は、サーバーごとの制限です。</span><span class="sxs-lookup"><span data-stu-id="b47dc-468">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="b47dc-469">Windows OS でのこの制限の詳細については、次のトピックの「解説」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b47dc-469">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="b47dc-470">GetTempFileNameA 関数</span><span class="sxs-lookup"><span data-stu-id="b47dc-470">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="b47dc-471">バッファー モデル バインドを使用して小さいファイルをデータベースにアップロードする</span><span class="sxs-lookup"><span data-stu-id="b47dc-471">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="b47dc-472">[Entity Framework](/ef/core/index) を使用してデータベースにバイナリ ファイル データを格納するには、エンティティで <xref:System.Byte> 配列プロパティを定義します。</span><span class="sxs-lookup"><span data-stu-id="b47dc-472">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="b47dc-473"><xref:Microsoft.AspNetCore.Http.IFormFile> が含まれるクラスに対してページ モデル プロパティを指定します。</span><span class="sxs-lookup"><span data-stu-id="b47dc-473">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

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
> <span data-ttu-id="b47dc-474"><xref:Microsoft.AspNetCore.Http.IFormFile> は、アクション メソッドのパラメーターとして直接、またはバインドされたモデル プロパティとして、使用することができます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-474"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="b47dc-475">前の例では、バインドされたモデル プロパティが使用されています。</span><span class="sxs-lookup"><span data-stu-id="b47dc-475">The prior example uses a bound model property.</span></span>

<span data-ttu-id="b47dc-476">は、 `FileUpload` ページ形式で使用され Razor ます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-476">The `FileUpload` is used in the Razor Pages form:</span></span>

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

<span data-ttu-id="b47dc-477">フォームがサーバーに POST されるときに、<xref:Microsoft.AspNetCore.Http.IFormFile> をストリームにコピーし、バイト配列としてデータベースに保存します。</span><span class="sxs-lookup"><span data-stu-id="b47dc-477">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="b47dc-478">次の例では、`_dbContext` によってアプリのデータベース コンテキストが格納されます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-478">In the following example, `_dbContext` stores the app's database context:</span></span>

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

<span data-ttu-id="b47dc-479">前の例は、次のサンプル アプリで示されているシナリオに似ています。</span><span class="sxs-lookup"><span data-stu-id="b47dc-479">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="b47dc-480">*Pages/BufferedSingleFileUploadDb.cshtml*</span><span class="sxs-lookup"><span data-stu-id="b47dc-480">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="b47dc-481">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="b47dc-481">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="b47dc-482">パフォーマンスに悪影響を与える可能性があるため、リレーショナル データベースにバイナリ データを格納する場合は注意してください。</span><span class="sxs-lookup"><span data-stu-id="b47dc-482">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="b47dc-483">検証を行わずに <xref:Microsoft.AspNetCore.Http.IFormFile> の `FileName` プロパティに依存したり、信頼したりしないでください。</span><span class="sxs-lookup"><span data-stu-id="b47dc-483">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="b47dc-484">`FileName` プロパティは、表示目的でのみ、HTML エンコードした後でだけ、使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b47dc-484">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="b47dc-485">示した例では、セキュリティ上の考慮事項については考えられていません。</span><span class="sxs-lookup"><span data-stu-id="b47dc-485">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="b47dc-486">以下のセクションおよび[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)で、追加の情報が提供されています。</span><span class="sxs-lookup"><span data-stu-id="b47dc-486">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="b47dc-487">セキュリティに関する考慮事項</span><span class="sxs-lookup"><span data-stu-id="b47dc-487">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="b47dc-488">検証</span><span class="sxs-lookup"><span data-stu-id="b47dc-488">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="b47dc-489">ストリーミングを使用して大きいファイルをアップロードする</span><span class="sxs-lookup"><span data-stu-id="b47dc-489">Upload large files with streaming</span></span>

<span data-ttu-id="b47dc-490">次の例では、JavaScript を使用してファイルをコントローラーのアクションにストリーミングする方法を示します。</span><span class="sxs-lookup"><span data-stu-id="b47dc-490">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="b47dc-491">ファイルの偽造防止トークンは、カスタム フィルター属性を使用して生成され、要求本文ではなくクライアント HTTP ヘッダーに渡されます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-491">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="b47dc-492">アクション メソッドではアップロードされたデータが直接処理されるため、フォーム モデル バインドは別のカスタム フィルターでは無効になります。</span><span class="sxs-lookup"><span data-stu-id="b47dc-492">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="b47dc-493">アクション内では、フォームのコンテンツが `MultipartReader` を使用して読み取られます。その場合、各 `MultipartSection` が読み取られ、必要に応じて、ファイルが処理されるかコンテンツが格納されます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-493">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="b47dc-494">マルチパート セクションが読み取られた後、アクションで独自のモデル バインドが実行されます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-494">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="b47dc-495">最初のページ応答ではフォームが読み込まれ、Cookie に偽造防止トークンが保存されます (`GenerateAntiforgeryTokenCookieAttribute` 属性を使用)。</span><span class="sxs-lookup"><span data-stu-id="b47dc-495">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="b47dc-496">その属性では、ASP.NET Core の組み込みの[偽造防止サポート](xref:security/anti-request-forgery)を使用して、要求トークンで Cookie が設定されます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-496">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="b47dc-497">`DisableFormValueModelBindingAttribute` は、モデル バインドを無効にするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-497">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="b47dc-498">このサンプルアプリで `GenerateAntiforgeryTokenCookieAttribute` は、とは、ページ `DisableFormValueModelBindingAttribute` `/StreamedSingleFileUploadDb` `/StreamedSingleFileUploadPhysical` `Startup.ConfigureServices` [ Razor 表記規則](xref:razor-pages/razor-pages-conventions)を使用するとのページアプリケーションモデルにフィルターとして適用されます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-498">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Startup.cs?name=snippet_AddMvc&highlight=8-11,17-20)]

<span data-ttu-id="b47dc-499">モデル バインドではフォームが読み取られないため、フォームからバインドされているパラメーターはバインドされません (クエリ、ルート、ヘッダーは引き続き機能します)。</span><span class="sxs-lookup"><span data-stu-id="b47dc-499">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="b47dc-500">アクション メソッドでは、`Request` プロパティが直接操作されます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-500">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="b47dc-501">`MultipartReader` は各セクションを読み取るために使用されます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-501">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="b47dc-502">キー/値データは `KeyValueAccumulator` に格納されます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-502">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="b47dc-503">マルチパート セクションが読み取られた後、`KeyValueAccumulator` の内容を使用して、フォーム データがモデル タイプにバインドされます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-503">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="b47dc-504">EF Core でデータベースにストリーミングするための完全な `StreamingController.UploadDatabase` メソッド:</span><span class="sxs-lookup"><span data-stu-id="b47dc-504">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="b47dc-505">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span><span class="sxs-lookup"><span data-stu-id="b47dc-505">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="b47dc-506">物理的な場所にストリーミングするための完全な `StreamingController.UploadPhysical` メソッド:</span><span class="sxs-lookup"><span data-stu-id="b47dc-506">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="b47dc-507">サンプル アプリでは、検証チェックは `FileHelpers.ProcessStreamedFile` によって処理されます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-507">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="b47dc-508">検証</span><span class="sxs-lookup"><span data-stu-id="b47dc-508">Validation</span></span>

<span data-ttu-id="b47dc-509">サンプル アプリの `FileHelpers` クラスでは、バッファーリングされた <xref:Microsoft.AspNetCore.Http.IFormFile> とストリーミングされたファイルのアップロードに関するいくつかのチェックが示されています。</span><span class="sxs-lookup"><span data-stu-id="b47dc-509">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="b47dc-510">サンプル アプリでのバッファーリングされたファイルのアップロード <xref:Microsoft.AspNetCore.Http.IFormFile> の処理については、*Utilities/FileHelpers.cs* ファイルの `ProcessFormFile` メソッドを参照してください。</span><span class="sxs-lookup"><span data-stu-id="b47dc-510">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="b47dc-511">ストリーミングされたファイルの処理については、同じファイルの `ProcessStreamedFile` メソッドを参照してください。</span><span class="sxs-lookup"><span data-stu-id="b47dc-511">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="b47dc-512">サンプル アプリで示されている検証処理メソッドでは、アップロードされたファイルの内容はスキャンされません。</span><span class="sxs-lookup"><span data-stu-id="b47dc-512">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="b47dc-513">ほとんどの運用シナリオでは、ファイルをユーザーまたは他のシステムで使用できるようにする前に、ウイルス/マルウェア スキャナー API が使用されます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-513">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="b47dc-514">このトピックのサンプルでは検証技法の実際の例が示されていますが、次の場合を除き、運用アプリでは `FileHelpers` クラスを実装しないでください。</span><span class="sxs-lookup"><span data-stu-id="b47dc-514">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="b47dc-515">実装を完全に理解している。</span><span class="sxs-lookup"><span data-stu-id="b47dc-515">Fully understand the implementation.</span></span>
> * <span data-ttu-id="b47dc-516">アプリの環境と仕様に合わせて実装が適切に変更されている。</span><span class="sxs-lookup"><span data-stu-id="b47dc-516">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="b47dc-517">**これらの要件に対応することなく、アプリにセキュリティ コードをむやみに実装しないでください。**</span><span class="sxs-lookup"><span data-stu-id="b47dc-517">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="b47dc-518">コンテンツの検証</span><span class="sxs-lookup"><span data-stu-id="b47dc-518">Content validation</span></span>

<span data-ttu-id="b47dc-519">**アップロードされた内容に対してサードパーティ製のウイルス/マルウェア スキャン API を使用します。**</span><span class="sxs-lookup"><span data-stu-id="b47dc-519">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="b47dc-520">大容量のシナリオでは、ファイルのスキャンに大量のサーバー リソースが必要です。</span><span class="sxs-lookup"><span data-stu-id="b47dc-520">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="b47dc-521">ファイルのスキャンによって要求の処理パフォーマンスが低下する場合は、スキャン処理を[バックグラウンド サービス](xref:fundamentals/host/hosted-services)にオフロードすることを検討してください (たとえば、アプリのサーバーとは異なるサーバーで実行されているサービス)。</span><span class="sxs-lookup"><span data-stu-id="b47dc-521">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="b47dc-522">通常、アップロードされたファイルは、バックグラウンドのウイルス検索プログラムによってチェックされるまで、検疫された領域に保持されます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-522">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="b47dc-523">合格したファイルは、通常のファイル ストレージの場所に移動されます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-523">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="b47dc-524">これらの手順は、通常、ファイルのスキャン状態を示すデータベース レコードと共に実行されます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-524">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="b47dc-525">このような方法を使用することで、アプリとアプリ サーバーは要求への応答に集中できます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-525">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="b47dc-526">ファイル拡張子の検証</span><span class="sxs-lookup"><span data-stu-id="b47dc-526">File extension validation</span></span>

<span data-ttu-id="b47dc-527">アップロードされたファイルの拡張子を、許可されている拡張子のリストで確認する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b47dc-527">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="b47dc-528">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="b47dc-528">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="b47dc-529">ファイルのシグネチャの検証</span><span class="sxs-lookup"><span data-stu-id="b47dc-529">File signature validation</span></span>

<span data-ttu-id="b47dc-530">ファイルのシグネチャは、ファイルの先頭にある最初の数バイトによって決定されます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-530">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="b47dc-531">これらのバイトを使用して、拡張子がファイルの内容と一致するかどうかを示すことができます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-531">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="b47dc-532">サンプル アプリでは、いくつかの一般的なファイルの種類についてファイルのシグネチャがチェックされています。</span><span class="sxs-lookup"><span data-stu-id="b47dc-532">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="b47dc-533">次の例では、JPEG イメージのファイルのシグネチャが、ファイルに対してチェックされています。</span><span class="sxs-lookup"><span data-stu-id="b47dc-533">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

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

<span data-ttu-id="b47dc-534">追加のファイル シグネチャを取得するには、「[ファイル シグネチャ データベース](https://www.filesignatures.net/)」および公式なファイルの仕様を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b47dc-534">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="b47dc-535">ファイル名のセキュリティ</span><span class="sxs-lookup"><span data-stu-id="b47dc-535">File name security</span></span>

<span data-ttu-id="b47dc-536">ファイルを物理ストレージに保存する場合は、クライアント指定のファイル名を使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="b47dc-536">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="b47dc-537">[Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) または [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) を使用して、ファイルの安全なファイル名を作成し、一時ストレージの完全なパス (ファイル名を含む) を作成します。</span><span class="sxs-lookup"><span data-stu-id="b47dc-537">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

Razor<span data-ttu-id="b47dc-538">プロパティ値が自動的に HTML エンコードされて表示されます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-538"> automatically HTML encodes property values for display.</span></span> <span data-ttu-id="b47dc-539">次のコードは安全に使用できます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-539">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="b47dc-540">の外部 Razor では、常に <xref:System.Net.WebUtility.HtmlEncode*> ユーザーの要求の内容をファイル名で指定します。</span><span class="sxs-lookup"><span data-stu-id="b47dc-540">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="b47dc-541">多くの実装には、ファイルが存在するかどうかのチェックを含める必要があります。そうしないと、同じ名前のファイルによってファイルが上書きされます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-541">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="b47dc-542">アプリの仕様を満たす追加のロジックを提供します。</span><span class="sxs-lookup"><span data-stu-id="b47dc-542">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="b47dc-543">サイズの検証</span><span class="sxs-lookup"><span data-stu-id="b47dc-543">Size validation</span></span>

<span data-ttu-id="b47dc-544">アップロードされるファイルのサイズを制限します。</span><span class="sxs-lookup"><span data-stu-id="b47dc-544">Limit the size of uploaded files.</span></span>

<span data-ttu-id="b47dc-545">サンプル アプリでは、ファイルのサイズは 2 MB (バイト単位) に制限されています。</span><span class="sxs-lookup"><span data-stu-id="b47dc-545">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="b47dc-546">その制限は、*appsettings.json* ファイルの [Configuration](xref:fundamentals/configuration/index) によって提供されます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-546">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="b47dc-547">`FileSizeLimit` が `PageModel` クラスに挿入されます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-547">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

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

<span data-ttu-id="b47dc-548">ファイルのサイズが制限を超えると、ファイルは拒否されます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-548">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="b47dc-549">name 属性の値を POST メソッドのパラメーター名に一致させる</span><span class="sxs-lookup"><span data-stu-id="b47dc-549">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="b47dc-550">フォームデータを Razor ポストするか、JavaScript の直接を使用する非フォームでは `FormData` 、フォームの要素で指定された名前、または `FormData` コントローラーのアクション内のパラメーターの名前と一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b47dc-550">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="b47dc-551">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="b47dc-551">In the following example:</span></span>

* <span data-ttu-id="b47dc-552">`<input>` 要素を使用すると、`name` 属性には値 `battlePlans` が設定されます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-552">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="b47dc-553">JavaScript で `FormData` を使用すると、name には値 `battlePlans` が設定されます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-553">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="b47dc-554">C# メソッドのパラメーターに一致する名前を使用します (`battlePlans`)。</span><span class="sxs-lookup"><span data-stu-id="b47dc-554">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="b47dc-555">Razorという名前のページページハンドラーメソッドの場合 `Upload` :</span><span class="sxs-lookup"><span data-stu-id="b47dc-555">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="b47dc-556">MVC POST コントローラー アクション メソッドの場合:</span><span class="sxs-lookup"><span data-stu-id="b47dc-556">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="b47dc-557">サーバーとアプリの構成</span><span class="sxs-lookup"><span data-stu-id="b47dc-557">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="b47dc-558">マルチパート本文の長さの制限</span><span class="sxs-lookup"><span data-stu-id="b47dc-558">Multipart body length limit</span></span>

<span data-ttu-id="b47dc-559"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> では、各マルチパート本文の長さの制限が設定されます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-559"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="b47dc-560">この制限を超えるフォーム セクションでは、解析時に <xref:System.IO.InvalidDataException> がスローされます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-560">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="b47dc-561">既定値は 134,217,728 (128 MB) です。</span><span class="sxs-lookup"><span data-stu-id="b47dc-561">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="b47dc-562">制限をカスタマイズするには、`Startup.ConfigureServices` の設定 <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> を使用します。</span><span class="sxs-lookup"><span data-stu-id="b47dc-562">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="b47dc-563">単一ページまたはアクションに対する <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> を設定するには、<xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> を使用します。</span><span class="sxs-lookup"><span data-stu-id="b47dc-563"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="b47dc-564">RazorPages アプリで、で[規則](xref:razor-pages/razor-pages-conventions)を適用したフィルターを適用し `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-564">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="b47dc-565">Razorページアプリまたは MVC アプリで、ページモデルまたはアクションメソッドにフィルターを適用します。</span><span class="sxs-lookup"><span data-stu-id="b47dc-565">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="b47dc-566">Kestrel の最大要求本文サイズ</span><span class="sxs-lookup"><span data-stu-id="b47dc-566">Kestrel maximum request body size</span></span>

<span data-ttu-id="b47dc-567">Kestrel によってホストされるアプリの場合、既定の要求本文の最大サイズは、30,000,000 バイトです。これは約 28.6 MB になります。</span><span class="sxs-lookup"><span data-stu-id="b47dc-567">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="b47dc-568">制限をカスタマイズするには、[MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel サーバー オプションを使用します。</span><span class="sxs-lookup"><span data-stu-id="b47dc-568">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

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

<span data-ttu-id="b47dc-569">単一ページまたはアクションに対する [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) を設定するには、<xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> を使用します。</span><span class="sxs-lookup"><span data-stu-id="b47dc-569"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="b47dc-570">RazorPages アプリで、で[規則](xref:razor-pages/razor-pages-conventions)を適用したフィルターを適用し `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-570">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="b47dc-571">Razorページアプリまたは MVC アプリで、フィルターをページハンドラークラスまたはアクションメソッドに適用します。</span><span class="sxs-lookup"><span data-stu-id="b47dc-571">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="b47dc-572">その他の Kestrel の制限</span><span class="sxs-lookup"><span data-stu-id="b47dc-572">Other Kestrel limits</span></span>

<span data-ttu-id="b47dc-573">Kestrel によってホストされるアプリには、他の Kestrel の制限が適用される場合があります。</span><span class="sxs-lookup"><span data-stu-id="b47dc-573">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="b47dc-574">クライアントの最大接続数</span><span class="sxs-lookup"><span data-stu-id="b47dc-574">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="b47dc-575">要求と応答のデータ レート</span><span class="sxs-lookup"><span data-stu-id="b47dc-575">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a><span data-ttu-id="b47dc-576">IIS の内容の長さの制限</span><span class="sxs-lookup"><span data-stu-id="b47dc-576">IIS content length limit</span></span>

<span data-ttu-id="b47dc-577">既定の要求の制限 (`maxAllowedContentLength`) は、30,000,000 バイトです。これは約 28.6 MB になります。</span><span class="sxs-lookup"><span data-stu-id="b47dc-577">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6MB.</span></span> <span data-ttu-id="b47dc-578">*web.config* ファイルで制限をカスタマイズします。</span><span class="sxs-lookup"><span data-stu-id="b47dc-578">Customize the limit in the *web.config* file:</span></span>

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

<span data-ttu-id="b47dc-579">この設定は IIS にのみ適用されます。</span><span class="sxs-lookup"><span data-stu-id="b47dc-579">This setting only applies to IIS.</span></span> <span data-ttu-id="b47dc-580">Kestrel でホストする場合、既定ではこの動作は発生しません。</span><span class="sxs-lookup"><span data-stu-id="b47dc-580">The behavior doesn't occur by default when hosting on Kestrel.</span></span> <span data-ttu-id="b47dc-581">詳細については、「[要求制限 \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b47dc-581">For more information, see [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="b47dc-582">ASP.NET Core モジュールでの制限または IIS 要求フィルター モジュールの存在により、アップロードが 2 または 4 GB に制限される場合があります。</span><span class="sxs-lookup"><span data-stu-id="b47dc-582">Limitations in the ASP.NET Core Module or presence of the IIS Request Filtering Module may limit uploads to either 2 or 4 GB.</span></span> <span data-ttu-id="b47dc-583">詳細については、「[サイズが 2 GB を超えるファイルをアップロードできない (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b47dc-583">For more information, see [Unable to upload file greater than 2GB in size (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="b47dc-584">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="b47dc-584">Troubleshoot</span></span>

<span data-ttu-id="b47dc-585">ファイルのアップロード時に発生する一般的ないくつかの問題と、考えられる解決策を以下に示します。</span><span class="sxs-lookup"><span data-stu-id="b47dc-585">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="b47dc-586">IIS サーバーに展開したときの "見つかりません" エラー</span><span class="sxs-lookup"><span data-stu-id="b47dc-586">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="b47dc-587">次のエラーは、アップロードされるファイルがサーバーの構成されている内容の長さを超えていることを示します。</span><span class="sxs-lookup"><span data-stu-id="b47dc-587">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="b47dc-588">制限を増やす方法の詳細については、「[IIS の内容の長さの制限](#iis-content-length-limit)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="b47dc-588">For more information on increasing the limit, see the [IIS content length limit](#iis-content-length-limit) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="b47dc-589">接続エラー</span><span class="sxs-lookup"><span data-stu-id="b47dc-589">Connection failure</span></span>

<span data-ttu-id="b47dc-590">接続エラーおよびサーバー接続のリセットは、アップロードされるファイルが Kestrel の最大要求本文サイズを超えていることを示している場合があります。</span><span class="sxs-lookup"><span data-stu-id="b47dc-590">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="b47dc-591">詳細については、[Kestrel の最大要求本文サイズ](#kestrel-maximum-request-body-size)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="b47dc-591">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="b47dc-592">Kestrel クライアント接続の制限の調整も必要な場合があります。</span><span class="sxs-lookup"><span data-stu-id="b47dc-592">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="b47dc-593">IFormFile での null 参照例外</span><span class="sxs-lookup"><span data-stu-id="b47dc-593">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="b47dc-594">コントローラーが <xref:Microsoft.AspNetCore.Http.IFormFile> を使用してアップロードされたファイルを受け取っても、値が `null` の場合は、HTML フォームで `multipart/form-data` に値 `enctype` が指定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="b47dc-594">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="b47dc-595">この属性が `<form>` 要素で設定されていない場合、ファイルはアップロードされず、バインドされた <xref:Microsoft.AspNetCore.Http.IFormFile> 引数は `null` になります。</span><span class="sxs-lookup"><span data-stu-id="b47dc-595">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="b47dc-596">また、[フォーム データでのアップロードの名前がアプリの名前と一致する](#match-name-attribute-value-to-parameter-name-of-post-method)ことを確認します。</span><span class="sxs-lookup"><span data-stu-id="b47dc-596">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="b47dc-597">ストリームが長すぎる</span><span class="sxs-lookup"><span data-stu-id="b47dc-597">Stream was too long</span></span>

<span data-ttu-id="b47dc-598">このトピックの例は、アップロードされたファイル コンテンツを保持するために <xref:System.IO.MemoryStream> に依存しています。</span><span class="sxs-lookup"><span data-stu-id="b47dc-598">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="b47dc-599">`int.MaxValue` のサイズ制限は `MemoryStream` です。</span><span class="sxs-lookup"><span data-stu-id="b47dc-599">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="b47dc-600">アプリのファイル アップロード シナリオで 50 MB を超えるファイル コンテンツを保持する必要がある場合は、アップロードされたファイルのコンテンツを 1 つの `MemoryStream` に依存することなく保持する別のアプローチを使用します。</span><span class="sxs-lookup"><span data-stu-id="b47dc-600">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end


## <a name="additional-resources"></a><span data-ttu-id="b47dc-601">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="b47dc-601">Additional resources</span></span>

* [<span data-ttu-id="b47dc-602">HTTP 接続要求をドレインしています</span><span class="sxs-lookup"><span data-stu-id="b47dc-602">HTTP connection request draining</span></span>](xref:fundamentals/servers/kestrel#http11-request-draining)
* [<span data-ttu-id="b47dc-603">Unrestricted File Upload (ファイルの無制限のアップロード)</span><span class="sxs-lookup"><span data-stu-id="b47dc-603">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
* [<span data-ttu-id="b47dc-604">Azure のセキュリティ: セキュリティフレーム: 入力の検証 |対策</span><span class="sxs-lookup"><span data-stu-id="b47dc-604">Azure Security: Security Frame: Input Validation | Mitigations</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation)
* [<span data-ttu-id="b47dc-605">Azure クラウドの設計パターン: Valet キーパターン</span><span class="sxs-lookup"><span data-stu-id="b47dc-605">Azure Cloud Design Patterns: Valet Key pattern</span></span>](/azure/architecture/patterns/valet-key)
