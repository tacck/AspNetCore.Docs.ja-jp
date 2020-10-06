---
title: ASP.NET Core Blazor ファイルのアップロード
author: guardrex
description: InputFile コンポーネントを使用して Blazor 内のファイルをアップロードする方法について説明します。
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/29/2020
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
uid: blazor/file-uploads
ms.openlocfilehash: 06d1464cb731a8008362fc911f463e4ff8a37b6b
ms.sourcegitcommit: d1a897ebd89daa05170ac448e4831d327f6b21a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2020
ms.locfileid: "91606663"
---
# <a name="aspnet-core-no-locblazor-file-uploads"></a><span data-ttu-id="8fd04-103">ASP.NET Core Blazor ファイルのアップロード</span><span class="sxs-lookup"><span data-stu-id="8fd04-103">ASP.NET Core Blazor file uploads</span></span>

<span data-ttu-id="8fd04-104">作成者: [Daniel Roth](https://github.com/danroth27)、[Pranav Krishnamoorthy](https://github.com/pranavkm)</span><span class="sxs-lookup"><span data-stu-id="8fd04-104">By [Daniel Roth](https://github.com/danroth27) and [Pranav Krishnamoorthy](https://github.com/pranavkm)</span></span>

<span data-ttu-id="8fd04-105">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="8fd04-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="8fd04-106">ファイルのアップロードなど、.NET コードにブラウザー ファイル データを読み込むには、`InputFile` コンポーネントを使用します。</span><span class="sxs-lookup"><span data-stu-id="8fd04-106">Use the `InputFile` component to read browser file data into .NET code, including for file uploads.</span></span>

> [!WARNING]
> <span data-ttu-id="8fd04-107">ファイルのアップロードに関するセキュリティのベスト プラクティスに常に従ってください。</span><span class="sxs-lookup"><span data-stu-id="8fd04-107">Always follow file upload security best practices.</span></span> <span data-ttu-id="8fd04-108">詳細については、「<xref:mvc/models/file-uploads#security-considerations>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8fd04-108">For more information, see <xref:mvc/models/file-uploads#security-considerations>.</span></span>

## <a name="inputfile-component"></a><span data-ttu-id="8fd04-109">`InputFile` コンポーネント</span><span class="sxs-lookup"><span data-stu-id="8fd04-109">`InputFile` component</span></span>

<span data-ttu-id="8fd04-110">`InputFile` コンポーネントは `file` 型の HTML 入力としてレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="8fd04-110">The `InputFile` component renders as an HTML input of type `file`.</span></span>

<span data-ttu-id="8fd04-111">既定では、ユーザーは単一ファイルを選択します。</span><span class="sxs-lookup"><span data-stu-id="8fd04-111">By default, the user selects single files.</span></span> <span data-ttu-id="8fd04-112">`multiple` 属性を追加して、ユーザーが一度に複数のファイルをアップロードできるようにします。</span><span class="sxs-lookup"><span data-stu-id="8fd04-112">Add the `multiple` attribute to permit the user to upload multiple files at once.</span></span> <span data-ttu-id="8fd04-113">ユーザーが 1 つ以上のファイルを選択すると、`InputFile` コンポーネントによって `OnChange` イベントが起動され、選択したファイル リストと各ファイルの詳細へのアクセスを提供する `InputFileChangeEventArgs` が渡されます。</span><span class="sxs-lookup"><span data-stu-id="8fd04-113">When one or more files is selected by the user, the `InputFile` component fires an `OnChange` event and passes in an `InputFileChangeEventArgs` that provides access to the selected file list and details about each file.</span></span>

<span data-ttu-id="8fd04-114">ユーザーが選択したファイルからデータを読み取るには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="8fd04-114">To read data from a user-selected file:</span></span>

* <span data-ttu-id="8fd04-115">ファイルで `OpenReadStream` を呼び出し、返されるストリームから読み取ります。</span><span class="sxs-lookup"><span data-stu-id="8fd04-115">Call `OpenReadStream` on the file and read from the returned stream.</span></span> <span data-ttu-id="8fd04-116">詳細については、「[ファイル ストリーム](#file-streams)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="8fd04-116">For more information, see the [File streams](#file-streams) section.</span></span>
* <span data-ttu-id="8fd04-117">`ReadAsync` を使用してください。</span><span class="sxs-lookup"><span data-stu-id="8fd04-117">Use `ReadAsync`.</span></span> <span data-ttu-id="8fd04-118">`ReadAsync` では既定で、サイズが 524,288 KB (512 KB) 未満のファイルのみ、読み取りが許可されます。</span><span class="sxs-lookup"><span data-stu-id="8fd04-118">By default, `ReadAsync` only allows reading a file smaller than 524,288 KB (512 KB) in size.</span></span> <span data-ttu-id="8fd04-119">このような制限は、開発者が誤って大きいファイルをメモリに読み取ってしまうことを防ぐために存在します。</span><span class="sxs-lookup"><span data-stu-id="8fd04-119">This limit is present to prevent developers from accidentally reading large files in to memory.</span></span> <span data-ttu-id="8fd04-120">大きいファイルをサポートする必要がある場合は、予想される最大ファイル サイズについて、妥当なおおよその数値を指定します。</span><span class="sxs-lookup"><span data-stu-id="8fd04-120">Specify a reasonable approximation for the maximum expected file size if larger files must be supported.</span></span> <span data-ttu-id="8fd04-121">受信ファイル ストリームを直接メモリに読み取ることは避けてください。</span><span class="sxs-lookup"><span data-stu-id="8fd04-121">Avoid reading the incoming file stream directly into memory.</span></span> <span data-ttu-id="8fd04-122">たとえば、ファイル バイトを <xref:System.IO.MemoryStream> にコピーしたり、バイト配列として読み取ることは避けてください。</span><span class="sxs-lookup"><span data-stu-id="8fd04-122">For example, don't copy file bytes into a <xref:System.IO.MemoryStream> or read as a byte array.</span></span> <span data-ttu-id="8fd04-123">このような方法は、特に Blazor Server で、パフォーマンスおよびセキュリティの問題を引き起こす可能性があります。</span><span class="sxs-lookup"><span data-stu-id="8fd04-123">These approaches can result in performance and security problems, especially in Blazor Server.</span></span> <span data-ttu-id="8fd04-124">代わりに、ファイル バイトを外部ストア (BLOB、またはディスク上のファイルなど) にコピーすることを検討してください。</span><span class="sxs-lookup"><span data-stu-id="8fd04-124">Instead, consider copying file bytes to an external store, such as a a blob or a file on disk.</span></span>

<span data-ttu-id="8fd04-125">イメージ ファイルを受信するコンポーネントは、ファイルの便利な `RequestImageFileAsync` メソッドを呼び出して、イメージがアプリにストリームされる前に、ブラウザーの JavaScript ランタイム内のイメージ データのサイズを変更できます。</span><span class="sxs-lookup"><span data-stu-id="8fd04-125">A component that receives an image file can call the `RequestImageFileAsync` convenience method on the file to resize the image data within the browser's JavaScript runtime before the image is streamed into the app.</span></span>

<span data-ttu-id="8fd04-126">次の例は、コンポーネントでの複数のイメージ ファイルのアップロードを示しています。</span><span class="sxs-lookup"><span data-stu-id="8fd04-126">The following example demonstrates multiple image file upload in a component.</span></span> <span data-ttu-id="8fd04-127">`InputFileChangeEventArgs.GetMultipleFiles` では、複数のファイルを読み取ることができます。</span><span class="sxs-lookup"><span data-stu-id="8fd04-127">`InputFileChangeEventArgs.GetMultipleFiles` allows reading multiple files.</span></span> <span data-ttu-id="8fd04-128">悪意のあるユーザーがアプリで想定されているよりも多くのファイルをアップロードするのを防ぐために、読み取るファイルの予想最大数を指定します。</span><span class="sxs-lookup"><span data-stu-id="8fd04-128">Specify the maximum number of files you expect to read to prevent a malicious user from uploading a larger number of files than the app expects.</span></span> <span data-ttu-id="8fd04-129">ファイルのアップロードが複数のファイルをサポートしていない場合、`InputFileChangeEventArgs.File` では、最初のファイルのみを読み取ることができます。</span><span class="sxs-lookup"><span data-stu-id="8fd04-129">`InputFileChangeEventArgs.File` allows reading the first and only file if the file upload does not support multiple files.</span></span>

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
    IList<string> imageDataUrls = new List<string>();

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

<span data-ttu-id="8fd04-130">`IBrowserFile` は、[ブラウザーによって公開される](https://developer.mozilla.org/docs/Web/API/File#Instance_properties)メタデータをプロパティとして返します。</span><span class="sxs-lookup"><span data-stu-id="8fd04-130">`IBrowserFile` returns metadata [exposed by the browser](https://developer.mozilla.org/docs/Web/API/File#Instance_properties) as properties.</span></span> <span data-ttu-id="8fd04-131">このメタデータは、事前検証に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="8fd04-131">This metadata can be useful to preliminary validation.</span></span> <span data-ttu-id="8fd04-132">例については、[`FileUpload.razor` および `FilePreview.razor` の各サンプル コンポーネント](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8fd04-132">For example, see the [`FileUpload.razor` and `FilePreview.razor` sample components](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/).</span></span>

## <a name="file-streams"></a><span data-ttu-id="8fd04-133">ファイル ストリーム</span><span class="sxs-lookup"><span data-stu-id="8fd04-133">File streams</span></span>

<span data-ttu-id="8fd04-134">Blazor WebAssembly アプリでは、データはブラウザー内の .NET コードに直接ストリームされます。</span><span class="sxs-lookup"><span data-stu-id="8fd04-134">In a Blazor WebAssembly app, the data is streamed directly into the .NET code within the browser.</span></span>

<span data-ttu-id="8fd04-135">Blazor Server アプリでは、ファイルがストリームから読み取られるときに、ファイル データがサーバー上の .NET コードに SignalR 接続を介してストリームされます。</span><span class="sxs-lookup"><span data-stu-id="8fd04-135">In a Blazor Server app, the file data is streamed over the SignalR connection into .NET code on the server as the file is read from the stream.</span></span> <span data-ttu-id="8fd04-136">[`Forms.RemoteBrowserFileStreamOptions`](https://github.com/dotnet/aspnetcore/blob/master/src/Components/Web/src/Forms/InputFile/RemoteBrowserFileStreamOptions.cs) では、Blazor Server のファイル アップロード特性を構成することができます。</span><span class="sxs-lookup"><span data-stu-id="8fd04-136">[`Forms.RemoteBrowserFileStreamOptions`](https://github.com/dotnet/aspnetcore/blob/master/src/Components/Web/src/Forms/InputFile/RemoteBrowserFileStreamOptions.cs) allows configuring file upload characteristics for Blazor Server.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8fd04-137">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="8fd04-137">Additional resources</span></span>

* <xref:mvc/models/file-uploads#security-considerations>
* <xref:blazor/forms-validation>
