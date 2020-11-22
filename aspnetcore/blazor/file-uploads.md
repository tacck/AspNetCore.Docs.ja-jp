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
# <a name="aspnet-core-no-locblazor-file-uploads"></a>ASP.NET Core Blazor ファイルのアップロード

作成者: [Daniel Roth](https://github.com/danroth27)、[Pranav Krishnamoorthy](https://github.com/pranavkm)

[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

ファイルのアップロードなど、.NET コードにブラウザー ファイル データを読み込むには、`InputFile` コンポーネントを使用します。

> [!WARNING]
> ファイルのアップロードに関するセキュリティのベスト プラクティスに常に従ってください。 詳細については、「<xref:mvc/models/file-uploads#security-considerations>」を参照してください。

## <a name="inputfile-component"></a>`InputFile` コンポーネント

`InputFile` コンポーネントは `file` 型の HTML 入力としてレンダリングされます。

既定では、ユーザーは単一ファイルを選択します。 `multiple` 属性を追加して、ユーザーが一度に複数のファイルをアップロードできるようにします。 ユーザーが 1 つ以上のファイルを選択すると、`InputFile` コンポーネントによって `OnChange` イベントが起動され、選択したファイル リストと各ファイルの詳細へのアクセスを提供する `InputFileChangeEventArgs` が渡されます。

ユーザーが選択したファイルからデータを読み取るには、次のようにします。

* ファイルで `Microsoft.AspNetCore.Components.Forms.IBrowserFile.OpenReadStream` を呼び出し、返されるストリームから読み取ります。 詳細については、「[ファイル ストリーム](#file-streams)」セクションを参照してください。
* `OpenReadStream` によって返される <xref:System.IO.Stream> には、読み取られる `Stream` の最大サイズがバイト単位で適用されます。 既定では、それ以降の読み取りで例外が発生する前に、サイズが 512,000 バイト (500 KB) 以下のファイルを読み取ることができます。 このような制限は、開発者が誤って大きいファイルをメモリに読み取ってしまうことを防ぐために存在します。 `Microsoft.AspNetCore.Components.Forms.IBrowserFile.OpenReadStream` の `maxAllowedSize` パラメーターを使用すると、必要に応じてより大きなサイズを指定できます。
* 受信ファイル ストリームを直接メモリに読み取ることは避けてください。 たとえば、ファイル バイトを <xref:System.IO.MemoryStream> にコピーしたり、バイト配列として読み取ることは避けてください。 このような方法は、特に Blazor Server で、パフォーマンスおよびセキュリティの問題を引き起こす可能性があります。 代わりに、ファイル バイトを外部ストア (BLOB、またはディスク上のファイルなど) にコピーすることを検討してください。

イメージ ファイルを受信するコンポーネントは、ファイルの便利な `RequestImageFileAsync` メソッドを呼び出して、イメージがアプリにストリームされる前に、ブラウザーの JavaScript ランタイム内のイメージ データのサイズを変更できます。

次の例は、コンポーネントでの複数のイメージ ファイルのアップロードを示しています。 `InputFileChangeEventArgs.GetMultipleFiles` では、複数のファイルを読み取ることができます。 悪意のあるユーザーがアプリで想定されているよりも多くのファイルをアップロードするのを防ぐために、読み取るファイルの予想最大数を指定します。 ファイルのアップロードが複数のファイルをサポートしていない場合、`InputFileChangeEventArgs.File` では、最初のファイルのみを読み取ることができます。

> [!NOTE]
> <xref:Microsoft.AspNetCore.Components.Forms.InputFileChangeEventArgs> は <xref:Microsoft.AspNetCore.Components.Forms?displayProperty=fullName> 名前空間にあります。これは、通常、アプリの `_Imports.razor` ファイル内の名前空間の 1 つです。

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

`IBrowserFile` は、[ブラウザーによって公開される](https://developer.mozilla.org/docs/Web/API/File#Instance_properties)メタデータをプロパティとして返します。 このメタデータは、事前検証に役立ちます。 例については、[`FileUpload.razor` および `FilePreview.razor` の各サンプル コンポーネント](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/)を参照してください。

## <a name="file-streams"></a>ファイル ストリーム

Blazor WebAssembly アプリでは、データはブラウザー内の .NET コードに直接ストリームされます。

Blazor Server アプリでは、ファイルがストリームから読み取られるときに、ファイル データがサーバー上の .NET コードに SignalR 接続を介してストリームされます。 [`Forms.RemoteBrowserFileStreamOptions`](https://github.com/dotnet/aspnetcore/blob/master/src/Components/Web/src/Forms/InputFile/RemoteBrowserFileStreamOptions.cs) では、Blazor Server のファイル アップロード特性を構成することができます。

## <a name="additional-resources"></a>その他のリソース

* <xref:mvc/models/file-uploads#security-considerations>
* <xref:blazor/forms-validation>
