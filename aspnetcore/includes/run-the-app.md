# <a name="visual-studio"></a>[<span data-ttu-id="ff0c9-101">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ff0c9-101">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ff0c9-102">Premere CTRL+F5 per l'esecuzione senza il debugger.</span><span class="sxs-lookup"><span data-stu-id="ff0c9-102">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="ff0c9-103">Visual Studio avvia [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) ed esegue l'app.</span><span class="sxs-lookup"><span data-stu-id="ff0c9-103">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="ff0c9-104">La barra degli indirizzi visualizza `localhost:port#` e non `example.com` o simili.</span><span class="sxs-lookup"><span data-stu-id="ff0c9-104">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="ff0c9-105">Ciò accade perché `localhost` è il nome host standard per il computer locale.</span><span class="sxs-lookup"><span data-stu-id="ff0c9-105">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="ff0c9-106">Localhost viene usato solo per le richieste web del computer locale.</span><span class="sxs-lookup"><span data-stu-id="ff0c9-106">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="ff0c9-107">Quando Visual Studio crea un progetto Web, viene usata una porta casuale per il server Web.</span><span class="sxs-lookup"><span data-stu-id="ff0c9-107">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
 
# <a name="visual-studio-code"></a>[<span data-ttu-id="ff0c9-108">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ff0c9-108">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="ff0c9-109">Premere **CTRL+F5** per l'esecuzione senza il debugger.</span><span class="sxs-lookup"><span data-stu-id="ff0c9-109">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="ff0c9-110">Visual Studio Code avvia [Kestrel](xref:fundamentals/servers/kestrel), avvia un browser e passa a `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="ff0c9-110">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="ff0c9-111">La barra degli indirizzi visualizza `localhost:port#` e non `example.com` o simili.</span><span class="sxs-lookup"><span data-stu-id="ff0c9-111">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="ff0c9-112">Ciò accade perché `localhost` è il nome host standard per il computer locale.</span><span class="sxs-lookup"><span data-stu-id="ff0c9-112">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="ff0c9-113">Localhost viene usato solo per le richieste web del computer locale.</span><span class="sxs-lookup"><span data-stu-id="ff0c9-113">Localhost only serves web requests from the local computer.</span></span>

  
# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ff0c9-114">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="ff0c9-114">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="ff0c9-115">Da Visual Studio, premere **opt-Cmd-Return** per eseguire senza il debugger.</span><span class="sxs-lookup"><span data-stu-id="ff0c9-115">From Visual Studio, press **Opt-Cmd-Return** to run without the debugger.</span></span> <span data-ttu-id="ff0c9-116">In alternativa, passare alla barra dei menu e andare a **esegui>avviare senza eseguire debug**.</span><span class="sxs-lookup"><span data-stu-id="ff0c9-116">Alternatively, navigate to the menu bar and go to **Run>Start Without Debugging**.</span></span>

  <span data-ttu-id="ff0c9-117">Visual Studio avvia [Kestrel](xref:fundamentals/servers/kestrel), avvia un browser e passa a `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="ff0c9-117">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

<!-- End of VS tabs -->

---
