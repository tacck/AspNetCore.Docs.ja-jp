::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1bc56-101">Id scaffolder を実行します。</span><span class="sxs-lookup"><span data-stu-id="1bc56-101">Run the Identity scaffolder:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1bc56-102">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1bc56-102">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1bc56-103">**ソリューションエクスプローラー**で、プロジェクトを右クリックし、[ **Add** > **New スキャフォールディング Item**] > ます。</span><span class="sxs-lookup"><span data-stu-id="1bc56-103">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="1bc56-104">[ **Add スキャフォールディング**] ダイアログボックスの左ペインで、[ **Identity** add] を選択し > **Add**ます。</span><span class="sxs-lookup"><span data-stu-id="1bc56-104">From the left pane of the **Add Scaffold** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="1bc56-105">[ **Id の追加**] ダイアログボックスで、必要なオプションを選択します。</span><span class="sxs-lookup"><span data-stu-id="1bc56-105">In the **Add Identity** dialog, select the options you want.</span></span>
  * <span data-ttu-id="1bc56-106">レイアウトファイルが正しくないマークアップで上書きされないように、既存のレイアウトページを選択します。</span><span class="sxs-lookup"><span data-stu-id="1bc56-106">Select your existing layout page so your layout file isn't overwritten with incorrect markup.</span></span> <span data-ttu-id="1bc56-107">既存の\* \_ Layout\*ファイルが選択されている場合、そのファイルは上書きされ**ません**。</span><span class="sxs-lookup"><span data-stu-id="1bc56-107">When an existing *\_Layout.cshtml* file is selected, it is **not** overwritten.</span></span> <span data-ttu-id="1bc56-108">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="1bc56-108">For example:</span></span>
    * <span data-ttu-id="1bc56-109">`~/Pages/Shared/_Layout.cshtml`既存の Razor Pages インフラストラクチャを使用する Razor Pages または Blazor Server プロジェクトの場合</span><span class="sxs-lookup"><span data-stu-id="1bc56-109">`~/Pages/Shared/_Layout.cshtml` for Razor Pages or Blazor Server projects with existing Razor Pages infrastructure</span></span>
    * <span data-ttu-id="1bc56-110">`~/Views/Shared/_Layout.cshtml`mvc プロジェクトの場合、または既存の MVC インフラストラクチャを使用する Blazor Server プロジェクトの場合</span><span class="sxs-lookup"><span data-stu-id="1bc56-110">`~/Views/Shared/_Layout.cshtml` for MVC projects or Blazor Server projects with existing MVC infrastructure</span></span>
* <span data-ttu-id="1bc56-111">既存のデータコンテキストを使用するには、オーバーライドするファイルを少なくとも1つ選択します。</span><span class="sxs-lookup"><span data-stu-id="1bc56-111">To use your existing data context, select at least one file to override.</span></span> <span data-ttu-id="1bc56-112">データコンテキストを追加するには、少なくとも1つのファイルを選択する必要があります。</span><span class="sxs-lookup"><span data-stu-id="1bc56-112">You must select at least one file to add your data context.</span></span>
  * <span data-ttu-id="1bc56-113">データコンテキストクラスを選択します。</span><span class="sxs-lookup"><span data-stu-id="1bc56-113">Select your data context class.</span></span>
  * <span data-ttu-id="1bc56-114">**[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="1bc56-114">Select **Add**.</span></span>
* <span data-ttu-id="1bc56-115">新しいユーザーコンテキストを作成し、場合によっては Id のカスタムユーザークラスを作成するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="1bc56-115">To create a new user context and possibly create a custom user class for Identity:</span></span>
  * <span data-ttu-id="1bc56-116">**+** 新しい**データコンテキストクラス**を作成するには、このボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="1bc56-116">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="1bc56-117">既定値をそのまま使用するか、クラス (など) を指定し `MyApplication.Data.ApplicationDbContext` ます。</span><span class="sxs-lookup"><span data-stu-id="1bc56-117">Accept the default value or specify a class (for example, `MyApplication.Data.ApplicationDbContext`).</span></span>
  * <span data-ttu-id="1bc56-118">**[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="1bc56-118">Select **Add**.</span></span>

<span data-ttu-id="1bc56-119">注: 新しいユーザーコンテキストを作成している場合は、上書きするファイルを選択する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="1bc56-119">Note: If you're creating a new user context, you don't have to select a file to override.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="1bc56-120">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="1bc56-120">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="1bc56-121">ASP.NET Core scaffolder をまだインストールしていない場合は、ここでインストールします。</span><span class="sxs-lookup"><span data-stu-id="1bc56-121">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="1bc56-122">必要な NuGet パッケージ参照をプロジェクトファイル (*.csproj*) に追加します。</span><span class="sxs-lookup"><span data-stu-id="1bc56-122">Add required NuGet package references to the project file (*.csproj*).</span></span> <span data-ttu-id="1bc56-123">プロジェクトディレクトリで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="1bc56-123">Run the following commands in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

<span data-ttu-id="1bc56-124">次のコマンドを実行して、Identity scaffolder オプションの一覧を表示します。</span><span class="sxs-lookup"><span data-stu-id="1bc56-124">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

[!INCLUDE[](~/includes/scaffoldTFM.md)]

<span data-ttu-id="1bc56-125">プロジェクトフォルダーで、必要なオプションを指定して Id scaffolder を実行します。</span><span class="sxs-lookup"><span data-stu-id="1bc56-125">In the project folder, run the Identity scaffolder with the options you want.</span></span> <span data-ttu-id="1bc56-126">たとえば、既定の UI とファイルの最小数で id を設定するには、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="1bc56-126">For example, to setup identity with the default UI and the minimum number of files, run the following command.</span></span> <span data-ttu-id="1bc56-127">DB コンテキストの正しい完全修飾名を使用します。</span><span class="sxs-lookup"><span data-stu-id="1bc56-127">Use the correct fully qualified name for your DB context:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login"
```

<span data-ttu-id="1bc56-128">PowerShell では、コマンドの区切り記号としてセミコロンを使用します。</span><span class="sxs-lookup"><span data-stu-id="1bc56-128">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="1bc56-129">PowerShell を使用する場合は、ファイルの一覧でセミコロンをエスケープするか、ファイルの一覧を二重引用符で囲みます。</span><span class="sxs-lookup"><span data-stu-id="1bc56-129">When using PowerShell, escape the semi-colons in the file list or put the file list in double quotes.</span></span> <span data-ttu-id="1bc56-130">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="1bc56-130">For example:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="1bc56-131">フラグやフラグを指定せずに Identity scaffolder を実行すると、 `--files` `--useDefaultUI` 使用可能なすべての id UI ページがプロジェクトに作成されます。</span><span class="sxs-lookup"><span data-stu-id="1bc56-131">If you run the Identity scaffolder without specifying the `--files` flag or the `--useDefaultUI` flag, all the available Identity UI pages will be created in your project.</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1bc56-132">Id scaffolder を実行します。</span><span class="sxs-lookup"><span data-stu-id="1bc56-132">Run the Identity scaffolder:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1bc56-133">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1bc56-133">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1bc56-134">**ソリューションエクスプローラー**で、プロジェクトを右クリックし、[ **Add** > **New スキャフォールディング Item**] > ます。</span><span class="sxs-lookup"><span data-stu-id="1bc56-134">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="1bc56-135">[ **Add スキャフォールディング**] ダイアログボックスの左ペインで、[ **Identity** add] を選択し > **Add**ます。</span><span class="sxs-lookup"><span data-stu-id="1bc56-135">From the left pane of the **Add Scaffold** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="1bc56-136">[ **Id の追加**] ダイアログボックスで、必要なオプションを選択します。</span><span class="sxs-lookup"><span data-stu-id="1bc56-136">In the **Add Identity** dialog, select the options you want.</span></span>
  * <span data-ttu-id="1bc56-137">既存のレイアウトページを選択するか、レイアウトファイルが正しくないマークアップで上書きされます。</span><span class="sxs-lookup"><span data-stu-id="1bc56-137">Select your existing layout page, or your layout file will be overwritten with incorrect markup.</span></span> <span data-ttu-id="1bc56-138">既存の\* \_ Layout\*ファイルが選択されている場合、そのファイルは上書きされ**ません**。</span><span class="sxs-lookup"><span data-stu-id="1bc56-138">When an existing *\_Layout.cshtml* file is selected, it is **not** overwritten.</span></span> <span data-ttu-id="1bc56-139">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="1bc56-139">For example:</span></span>
    * <span data-ttu-id="1bc56-140">`~/Pages/Shared/_Layout.cshtml`Razor Pages</span><span class="sxs-lookup"><span data-stu-id="1bc56-140">`~/Pages/Shared/_Layout.cshtml` for Razor Pages</span></span>
    * <span data-ttu-id="1bc56-141">`~/Views/Shared/_Layout.cshtml`MVC プロジェクトの場合</span><span class="sxs-lookup"><span data-stu-id="1bc56-141">`~/Views/Shared/_Layout.cshtml` for MVC projects</span></span>
* <span data-ttu-id="1bc56-142">既存のデータコンテキストを使用するには、オーバーライドするファイルを少なくとも1つ選択します。</span><span class="sxs-lookup"><span data-stu-id="1bc56-142">To use your existing data context, select at least one file to override.</span></span> <span data-ttu-id="1bc56-143">データコンテキストを追加するには、少なくとも1つのファイルを選択する必要があります。</span><span class="sxs-lookup"><span data-stu-id="1bc56-143">You must select at least one file to add your data context.</span></span>
  * <span data-ttu-id="1bc56-144">データコンテキストクラスを選択します。</span><span class="sxs-lookup"><span data-stu-id="1bc56-144">Select your data context class.</span></span>
  * <span data-ttu-id="1bc56-145">**[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="1bc56-145">Select **Add**.</span></span>
* <span data-ttu-id="1bc56-146">新しいユーザーコンテキストを作成し、場合によっては Id のカスタムユーザークラスを作成するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="1bc56-146">To create a new user context and possibly create a custom user class for Identity:</span></span>
  * <span data-ttu-id="1bc56-147">**+** 新しい**データコンテキストクラス**を作成するには、このボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="1bc56-147">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="1bc56-148">既定値をそのまま使用するか、クラス (など) を指定し `MyApplication.Data.ApplicationDbContext` ます。</span><span class="sxs-lookup"><span data-stu-id="1bc56-148">Accept the default value or specify a class (for example, `MyApplication.Data.ApplicationDbContext`).</span></span>
  * <span data-ttu-id="1bc56-149">**[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="1bc56-149">Select **Add**.</span></span>

<span data-ttu-id="1bc56-150">注: 新しいユーザーコンテキストを作成している場合は、上書きするファイルを選択する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="1bc56-150">Note: If you're creating a new user context, you don't have to select a file to override.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="1bc56-151">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="1bc56-151">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="1bc56-152">ASP.NET Core scaffolder をまだインストールしていない場合は、ここでインストールします。</span><span class="sxs-lookup"><span data-stu-id="1bc56-152">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="1bc56-153">[VisualStudio](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/)にパッケージ参照を追加し、プロジェクトファイル (*.csproj*) に追加してください。</span><span class="sxs-lookup"><span data-stu-id="1bc56-153">Add a package reference to [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) to the project file (*.csproj*).</span></span> <span data-ttu-id="1bc56-154">プロジェクトディレクトリで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="1bc56-154">Run the following commands in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

<span data-ttu-id="1bc56-155">次のコマンドを実行して、Identity scaffolder オプションの一覧を表示します。</span><span class="sxs-lookup"><span data-stu-id="1bc56-155">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

<span data-ttu-id="1bc56-156">プロジェクトフォルダーで、必要なオプションを指定して Id scaffolder を実行します。</span><span class="sxs-lookup"><span data-stu-id="1bc56-156">In the project folder, run the Identity scaffolder with the options you want.</span></span> <span data-ttu-id="1bc56-157">たとえば、既定の UI とファイルの最小数で id を設定するには、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="1bc56-157">For example, to setup identity with the default UI and the minimum number of files, run the following command.</span></span> <span data-ttu-id="1bc56-158">DB コンテキストの正しい完全修飾名を使用します。</span><span class="sxs-lookup"><span data-stu-id="1bc56-158">Use the correct fully qualified name for your DB context:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login"
```

<span data-ttu-id="1bc56-159">PowerShell では、コマンドの区切り記号としてセミコロンを使用します。</span><span class="sxs-lookup"><span data-stu-id="1bc56-159">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="1bc56-160">PowerShell を使用する場合は、ファイルの一覧でセミコロンをエスケープするか、ファイルの一覧を二重引用符で囲みます。</span><span class="sxs-lookup"><span data-stu-id="1bc56-160">When using PowerShell, escape the semi-colons in the file list or put the file list in double quotes.</span></span> <span data-ttu-id="1bc56-161">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="1bc56-161">For example:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="1bc56-162">フラグやフラグを指定せずに Identity scaffolder を実行すると、 `--files` `--useDefaultUI` 使用可能なすべての id UI ページがプロジェクトに作成されます。</span><span class="sxs-lookup"><span data-stu-id="1bc56-162">If you run the Identity scaffolder without specifying the `--files` flag or the `--useDefaultUI` flag, all the available Identity UI pages will be created in your project.</span></span>

---

::: moniker-end
