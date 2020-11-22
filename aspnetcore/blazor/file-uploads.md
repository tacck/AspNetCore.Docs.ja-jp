---
title: ASP.NET Core Blazor ファイルのアップロード
author: guardrex
description: InputFile コンポーネントを使用して Blazor 内のファイルをアップロードする方法について説明します。
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
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
ms.date: 10/27/2020
uid: blazor/file-uploads
ms.openlocfilehash: 77c2874eef788b8083758c087913a7a04c55fa2b
ms.sourcegitcommit: 54fdca99f30b18d69cf0753ca3c84c7dab8f2b0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2020
ms.locfileid: "94691171"
---
# <a name="aspnet-core-no-locblazor-file-uploads"></a><span data-ttu-id="7e66a-103">ASP.NET Core Blazor ファイルのアップロード</span><span class="sxs-lookup"><span data-stu-id="7e66a-103">ASP.NET Core Blazor file uploads</span></span>

<span data-ttu-id="7e66a-104">作成者: [Daniel Roth](https://github.com/danroth27)、[Pranav Krishnamoorthy](https://github.com/pranavkm)</span><span class="sxs-lookup"><span data-stu-id="7e66a-104">By [Daniel Roth](https://github.com/danroth27) and [Pranav Krishnamoorthy](https://github.com/pranavkm)</span></span>

<span data-ttu-id="7e66a-105">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="7e66a-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="7e66a-106">ファイルのアップロードなど、.NET コードにブラウザー ファイル データを読み込むには、`InputFile` コンポーネントを使用します。</span><span class="sxs-lookup"><span data-stu-id="7e66a-106">Use the `InputFile` component to read browser file data into .NET code, including for file uploads.</span></span>

> [!WARNING]
> <span data-ttu-id="7e66a-107">ファイルのアップロードに関するセキュリティのベスト プラクティスに常に従ってください。</span><span class="sxs-lookup"><span data-stu-id="7e66a-107">Always follow file upload security best practices.</span></span> <span data-ttu-id="7e66a-108">詳細については、「<xref:mvc/models/file-uploads#security-considerations>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7e66a-108">For more information, see <xref:mvc/models/file-uploads#security-considerations>.</span></span>

## <a name="inputfile-component"></a><span data-ttu-id="7e66a-109">`InputFile` コンポーネント</span><span class="sxs-lookup"><span data-stu-id="7e66a-109">`InputFile` component</span></span>

<span data-ttu-id="7e66a-110">`InputFile` コンポーネントは `file` 型の HTML 入力としてレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="7e66a-110">The `InputFile` component renders as an HTML input of type `file`.</span></span>

<span data-ttu-id="7e66a-111">既定では、ユーザーは単一ファイルを選択します。</span><span class="sxs-lookup"><span data-stu-id="7e66a-111">By default, the user selects single files.</span></span> <span data-ttu-id="7e66a-112">`multiple` 属性を追加して、ユーザーが一度に複数のファイルをアップロードできるようにします。</span><span class="sxs-lookup"><span data-stu-id="7e66a-112">Add the `multiple` attribute to permit the user to upload multiple files at once.</span></span> <span data-ttu-id="7e66a-113">ユーザーが 1 つ以上のファイルを選択すると、`InputFile` コンポーネントによって `OnChange` イベントが起動され、選択したファイル リストと各ファイルの詳細へのアクセスを提供する `InputFileChangeEventArgs` が渡されます。</span><span class="sxs-lookup"><span data-stu-id="7e66a-113">When one or more files is selected by the user, the `InputFile` component fires an `OnChange` event and passes in an `InputFileChangeEventArgs` that provides access to the selected file list and details about each file.</span></span>

<span data-ttu-id="7e66a-114">ユーザーが選択したファイルからデータを読み取るには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="7e66a-114">To read data from a user-selected file:</span></span>

* <span data-ttu-id="7e66a-115">ファイルで `Microsoft.AspNetCore.Components.Forms.IBrowserFile.OpenReadStream` を呼び出し、返されるストリームから読み取ります。</span><span class="sxs-lookup"><span data-stu-id="7e66a-115">Call `Microsoft.AspNetCore.Components.Forms.IBrowserFile.OpenReadStream` on the file and read from the returned stream.</span></span> <span data-ttu-id="7e66a-116">詳細については、「[ファイル ストリーム](#file-streams)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="7e66a-116">For more information, see the [File streams](#file-streams) section.</span></span>
* <span data-ttu-id="7e66a-117">`OpenReadStream` によって返される <xref:System.IO.Stream> には、読み取られる `Stream` の最大サイズがバイト単位で適用されます。</span><span class="sxs-lookup"><span data-stu-id="7e66a-117">The <xref:System.IO.Stream> returned by `OpenReadStream` enforces a maximum size in bytes of the `Stream` being read.</span></span> <span data-ttu-id="7e66a-118">既定では、それ以降の読み取りで例外が発生する前に、サイズが 512,000 バイト (500 KB) 以下のファイルを読み取ることができます。</span><span class="sxs-lookup"><span data-stu-id="7e66a-118">By default, files no larger than 512,000 bytes (500 KB) in size are allowed to be read before any further reads would result in an exception.</span></span> <span data-ttu-id="7e66a-119">このような制限は、開発者が誤って大きいファイルをメモリに読み取ってしまうことを防ぐために存在します。</span><span class="sxs-lookup"><span data-stu-id="7e66a-119">This limit is present to prevent developers from accidentally reading large files in to memory.</span></span> <span data-ttu-id="7e66a-120">`Microsoft.AspNetCore.Components.Forms.IBrowserFile.OpenReadStream` の `maxAllowedSize` パラメーターを使用すると、必要に応じてより大きなサイズを指定できます。</span><span class="sxs-lookup"><span data-stu-id="7e66a-120">The `maxAllowedSize` parameter on `Microsoft.AspNetCore.Components.Forms.IBrowserFile.OpenReadStream` can be used to specify a larger size if required.</span></span>
* <span data-ttu-id="7e66a-121">受信ファイル ストリームを直接メモリに読み取ることは避けてください。</span><span class="sxs-lookup"><span data-stu-id="7e66a-121">Avoid reading the incoming file stream directly into memory.</span></span> <span data-ttu-id="7e66a-122">たとえば、ファイル バイトを <xref:System.IO.MemoryStream> にコピーしたり、バイト配列として読み取ることは避けてください。</span><span class="sxs-lookup"><span data-stu-id="7e66a-122">For example, don't copy file bytes into a <xref:System.IO.MemoryStream> or read as a byte array.</span></span> <span data-ttu-id="7e66a-123">このような方法は、特に Blazor Server で、パフォーマンスおよびセキュリティの問題を引き起こす可能性があります。</span><span class="sxs-lookup"><span data-stu-id="7e66a-123">These approaches can result in performance and security problems, especially in Blazor Server.</span></span> <span data-ttu-id="7e66a-124">代わりに、ファイル バイトを外部ストア (BLOB、またはディスク上のファイルなど) にコピーすることを検討してください。</span><span class="sxs-lookup"><span data-stu-id="7e66a-124">Instead, consider copying file bytes to an external store, such as a a blob or a file on disk.</span></span>

<span data-ttu-id="7e66a-125">イメージ ファイルを受信するコンポーネントは、ファイルの便利な `RequestImageFileAsync` メソッドを呼び出して、イメージがアプリにストリームされる前に、ブラウザーの JavaScript ランタイム内のイメージ データのサイズを変更できます。</span><span class="sxs-lookup"><span data-stu-id="7e66a-125">A component that receives an image file can call the `RequestImageFileAsync` convenience method on the file to resize the image data within the browser's JavaScript runtime before the image is streamed into the app.</span></span>

<span data-ttu-id="7e66a-126">次の例は、コンポーネントでの複数のイメージ ファイルのアップロードを示しています。</span><span class="sxs-lookup"><span data-stu-id="7e66a-126">The following example demonstrates multiple image file upload in a component.</span></span> <span data-ttu-id="7e66a-127">`InputFileChangeEventArgs.GetMultipleFiles` では、複数のファイルを読み取ることができます。</span><span class="sxs-lookup"><span data-stu-id="7e66a-127">`InputFileChangeEventArgs.GetMultipleFiles` allows reading multiple files.</span></span> <span data-ttu-id="7e66a-128">悪意のあるユーザーがアプリで想定されているよりも多くのファイルをアップロードするのを防ぐために、読み取るファイルの予想最大数を指定します。</span><span class="sxs-lookup"><span data-stu-id="7e66a-128">Specify the maximum number of files you expect to read to prevent a malicious user from uploading a larger number of files than the app expects.</span></span> <span data-ttu-id="7e66a-129">ファイルのアップロードが複数のファイルをサポートしていない場合、`InputFileChangeEventArgs.File` では、最初のファイルのみを読み取ることができます。</span><span class="sxs-lookup"><span data-stu-id="7e66a-129">`InputFileChangeEventArgs.File` allows reading the first and only file if the file upload does not support multiple files.</span></span>

> [!NOTE]
> <span data-ttu-id="7e66a-130"><xref:Microsoft.AspNetCore.Components.Forms.InputFileChangeEventArgs> は <xref:Microsoft.AspNetCore.Components.Forms?displayProperty=fullName> 名前空間にあります。これは、通常、アプリの `_Imports.razor` ファイル内の名前空間の 1 つです。</span><span class="sxs-lookup"><span data-stu-id="7e66a-130"><xref:Microsoft.AspNetCore.Components.Forms.InputFileChangeEventArgs> is in the <xref:Microsoft.AspNetCore.Components.Forms?displayProperty=fullName> namespace, which is typically one of the namespaces in the app's `_Imports.razor` file.</span></span>

```razor
<h3>Upload PNG images</h3>

<p>
    <InputFile OnChange="@OnInputFileChange" multiple />
</p>

@if (imageDataUrls.Count > 0)
{
    <h4>Images</h4>

    <div class="card" style="width:30rem;">
        <div class="card-body">
            @foreach (var imageDataUrl in imageDataUrls)
            {
                <img class="rounded m-1" src="@imageDataUrl" />
            }
        </div>
    </div>
}

@code {
    private IList<string> imageDataUrls = new List<string>();

    private async Task OnInputFileChange(InputFileChangeEventArgs e)
    {
        var maxAllowedFiles = 3;
        var format = "image/png";

        foreach (var imageFile in e.GetMultipleFiles(maxAllowedFiles))
        {
            var resizedImageFile = await imageFile.RequestImageFileAsync(format, 
                100, 100);
            var buffer = new byte[resizedImageFile.Size];
            await resizedImageFile.OpenReadStream().ReadAsync(buffer);
            var imageDataUrl = 
                $"data:{format};base64,{Convert.ToBase64String(buffer)}";
            imageDataUrls.Add(imageDataUrl);
        }
    }
}
```

<span data-ttu-id="7e66a-131">`IBrowserFile` は、[ブラウザーによって公開される](https://developer.mozilla.org/docs/Web/API/File#Instance_properties)メタデータをプロパティとして返します。</span><span class="sxs-lookup"><span data-stu-id="7e66a-131">`IBrowserFile` returns metadata [exposed by the browser](https://developer.mozilla.org/docs/Web/API/File#Instance_properties) as properties.</span></span> <span data-ttu-id="7e66a-132">このメタデータは、事前検証に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="7e66a-132">This metadata can be useful to preliminary validation.</span></span> <span data-ttu-id="7e66a-133">例については、[`FileUpload.razor` および `FilePreview.razor` の各サンプル コンポーネント](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7e66a-133">For example, see the [`FileUpload.razor` and `FilePreview.razor` sample components](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/).</span></span>

## <a name="file-streams"></a><span data-ttu-id="7e66a-134">ファイル ストリーム</span><span class="sxs-lookup"><span data-stu-id="7e66a-134">File streams</span></span>

<span data-ttu-id="7e66a-135">Blazor WebAssembly アプリでは、データはブラウザー内の .NET コードに直接ストリームされます。</span><span class="sxs-lookup"><span data-stu-id="7e66a-135">In a Blazor WebAssembly app, the data is streamed directly into the .NET code within the browser.</span></span>

<span data-ttu-id="7e66a-136">Blazor Server アプリでは、ファイルがストリームから読み取られるときに、ファイル データがサーバー上の .NET コードに SignalR 接続を介してストリームされます。</span><span class="sxs-lookup"><span data-stu-id="7e66a-136">In a Blazor Server app, the file data is streamed over the SignalR connection into .NET code on the server as the file is read from the stream.</span></span> <span data-ttu-id="7e66a-137">[`Forms.RemoteBrowserFileStreamOptions`](https://github.com/dotnet/aspnetcore/blob/master/src/Components/Web/src/Forms/InputFile/RemoteBrowserFileStreamOptions.cs) では、Blazor Server のファイル アップロード特性を構成することができます。</span><span class="sxs-lookup"><span data-stu-id="7e66a-137">[`Forms.RemoteBrowserFileStreamOptions`](https://github.com/dotnet/aspnetcore/blob/master/src/Components/Web/src/Forms/InputFile/RemoteBrowserFileStreamOptions.cs) allows configuring file upload characteristics for Blazor Server.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7e66a-138">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="7e66a-138">Additional resources</span></span>

* <xref:mvc/models/file-uploads#security-considerations>
* <xref:blazor/forms-validation>
