# <a name="visual-studio"></a>[<span data-ttu-id="df7d5-101">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="df7d5-101">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="df7d5-102">新しいプロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="df7d5-102">Create a new project.</span></span>
1. <span data-ttu-id="df7d5-103">**[ワーカー サービス]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="df7d5-103">Select **Worker Service**.</span></span> <span data-ttu-id="df7d5-104">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="df7d5-104">Select **Next**.</span></span>
1. <span data-ttu-id="df7d5-105">**[プロジェクト名]** フィールドにプロジェクト名を入力するか、既定のプロジェクト名をそのまま使用します。</span><span class="sxs-lookup"><span data-stu-id="df7d5-105">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="df7d5-106">**作成** を選択します。</span><span class="sxs-lookup"><span data-stu-id="df7d5-106">Select **Create**.</span></span>
1. <span data-ttu-id="df7d5-107">**[新しい Worker サービスを作成します]** ダイアログで、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="df7d5-107">In the **Create a new Worker service** dialog, select **Create**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="df7d5-108">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="df7d5-108">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="df7d5-109">新しいプロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="df7d5-109">Create a new project.</span></span>
1. <span data-ttu-id="df7d5-110">サイドバーの **[.NET Core]** の下で **[アプリ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="df7d5-110">Select **App** under **.NET Core** in the sidebar.</span></span>
1. <span data-ttu-id="df7d5-111">**[ASP.NET Core]** の下の **[ワーカー]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="df7d5-111">Select **Worker** under **ASP.NET Core**.</span></span> <span data-ttu-id="df7d5-112">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="df7d5-112">Select **Next**.</span></span>
1. <span data-ttu-id="df7d5-113">**[ターゲット フレームワーク]** で **[.NET Core 3.0]** またはそれ以降を選択します。</span><span class="sxs-lookup"><span data-stu-id="df7d5-113">Select **.NET Core 3.0** or later for the **Target Framework**.</span></span> <span data-ttu-id="df7d5-114">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="df7d5-114">Select **Next**.</span></span>
1. <span data-ttu-id="df7d5-115">**[プロジェクト名]** フィールドに名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="df7d5-115">Provide a name in the **Project Name** field.</span></span> <span data-ttu-id="df7d5-116">**作成** を選択します。</span><span class="sxs-lookup"><span data-stu-id="df7d5-116">Select **Create**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="df7d5-117">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="df7d5-117">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="df7d5-118">コマンド シェルから [dotnet new](/dotnet/core/tools/dotnet-new) コマンドと共にワーカー サービス (`worker`) テンプレートを使用します。</span><span class="sxs-lookup"><span data-stu-id="df7d5-118">Use the Worker Service (`worker`) template with the [dotnet new](/dotnet/core/tools/dotnet-new) command from a command shell.</span></span> <span data-ttu-id="df7d5-119">次の例では、`ContosoWorker` という名前のワーカー サービス アプリが作成されます。</span><span class="sxs-lookup"><span data-stu-id="df7d5-119">In the following example, a Worker Service app is created named `ContosoWorker`.</span></span> <span data-ttu-id="df7d5-120">このコマンドが実行されると、`ContosoWorker` アプリ用のフォルダーが自動的に作成されます。</span><span class="sxs-lookup"><span data-stu-id="df7d5-120">A folder for the `ContosoWorker` app is created automatically when the command is executed.</span></span>

```dotnetcli
dotnet new worker -o ContosoWorker
```

---
