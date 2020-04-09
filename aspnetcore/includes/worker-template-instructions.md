# <a name="visual-studio"></a>[<span data-ttu-id="8792b-101">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8792b-101">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="8792b-102">Creare un nuovo progetto.</span><span class="sxs-lookup"><span data-stu-id="8792b-102">Create a new project.</span></span>
1. <span data-ttu-id="8792b-103">Selezionare **Servizio di lavoro**.</span><span class="sxs-lookup"><span data-stu-id="8792b-103">Select **Worker Service**.</span></span> <span data-ttu-id="8792b-104">Selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="8792b-104">Select **Next**.</span></span>
1. <span data-ttu-id="8792b-105">Specificare il nome di un progetto nel campo **Nome progetto** oppure accettare il nome predefinito.</span><span class="sxs-lookup"><span data-stu-id="8792b-105">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="8792b-106">Selezionare **Create** (Crea).</span><span class="sxs-lookup"><span data-stu-id="8792b-106">Select **Create**.</span></span>
1. <span data-ttu-id="8792b-107">Nella finestra di dialogo **Crea un nuovo servizio di lavoro** selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="8792b-107">In the **Create a new Worker service** dialog, select **Create**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8792b-108">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="8792b-108">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="8792b-109">Creare un nuovo progetto.</span><span class="sxs-lookup"><span data-stu-id="8792b-109">Create a new project.</span></span>
1. <span data-ttu-id="8792b-110">Seleziona **App** in **.NET Core** nella barra laterale.</span><span class="sxs-lookup"><span data-stu-id="8792b-110">Select **App** under **.NET Core** in the sidebar.</span></span>
1. <span data-ttu-id="8792b-111">Selezionare **Lavoratore** in **ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="8792b-111">Select **Worker** under **ASP.NET Core**.</span></span> <span data-ttu-id="8792b-112">Selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="8792b-112">Select **Next**.</span></span>
1. <span data-ttu-id="8792b-113">Selezionare **.NET Core 3.0** o versione successiva per Framework di **destinazione**.</span><span class="sxs-lookup"><span data-stu-id="8792b-113">Select **.NET Core 3.0** or later for the **Target Framework**.</span></span> <span data-ttu-id="8792b-114">Selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="8792b-114">Select **Next**.</span></span>
1. <span data-ttu-id="8792b-115">Specificare un nome nel campo **Nome progetto.**</span><span class="sxs-lookup"><span data-stu-id="8792b-115">Provide a name in the **Project Name** field.</span></span> <span data-ttu-id="8792b-116">Selezionare **Create** (Crea).</span><span class="sxs-lookup"><span data-stu-id="8792b-116">Select **Create**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="8792b-117">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="8792b-117">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="8792b-118">Usare il servizio di ruolo di lavoro (`worker`) con il comando[dotnet new](/dotnet/core/tools/dotnet-new) da una shell dei comandi.</span><span class="sxs-lookup"><span data-stu-id="8792b-118">Use the Worker Service (`worker`) template with the [dotnet new](/dotnet/core/tools/dotnet-new) command from a command shell.</span></span> <span data-ttu-id="8792b-119">Nell'esempio seguente viene creata un'app del servizio di ruolo di lavoro denominata `ContosoWorker`.</span><span class="sxs-lookup"><span data-stu-id="8792b-119">In the following example, a Worker Service app is created named `ContosoWorker`.</span></span> <span data-ttu-id="8792b-120">Una cartella per l'app `ContosoWorker` viene creata automaticamente quando viene eseguito il comando.</span><span class="sxs-lookup"><span data-stu-id="8792b-120">A folder for the `ContosoWorker` app is created automatically when the command is executed.</span></span>

```dotnetcli
dotnet new worker -o ContosoWorker
```

---
