* `-F|--no-formatting`

  <span data-ttu-id="7f684-101">Flag la cui presenza elimina la formattazione della risposta HTTP.</span><span class="sxs-lookup"><span data-stu-id="7f684-101">A flag whose presence suppresses HTTP response formatting.</span></span>

* `-h|--header`

  <span data-ttu-id="7f684-102">Imposta un'intestazione della richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="7f684-102">Sets an HTTP request header.</span></span> <span data-ttu-id="7f684-103">Sono supportati i due formati di valore seguenti:</span><span class="sxs-lookup"><span data-stu-id="7f684-103">The following two value formats are supported:</span></span>

  * `{header}={value}`
  * `{header}:{value}`

* `--response:body`

  <span data-ttu-id="7f684-104">Specifica un file in cui deve essere scritto il corpo della risposta HTTP.</span><span class="sxs-lookup"><span data-stu-id="7f684-104">Specifies a file to which the HTTP response body should be written.</span></span> <span data-ttu-id="7f684-105">Ad esempio, `--response:body "C:\response.json"`</span><span class="sxs-lookup"><span data-stu-id="7f684-105">For example, `--response:body "C:\response.json"`.</span></span> <span data-ttu-id="7f684-106">Se il file non esiste, verrà creato.</span><span class="sxs-lookup"><span data-stu-id="7f684-106">The file is created if it doesn't exist.</span></span>

* `--response:headers`

  <span data-ttu-id="7f684-107">Specifica un file in cui devono essere scritte le intestazioni della risposta HTTP.</span><span class="sxs-lookup"><span data-stu-id="7f684-107">Specifies a file to which the HTTP response headers should be written.</span></span> <span data-ttu-id="7f684-108">Ad esempio, `--response:headers "C:\response.txt"`</span><span class="sxs-lookup"><span data-stu-id="7f684-108">For example, `--response:headers "C:\response.txt"`.</span></span> <span data-ttu-id="7f684-109">Se il file non esiste, verrà creato.</span><span class="sxs-lookup"><span data-stu-id="7f684-109">The file is created if it doesn't exist.</span></span>

* `-s|--streaming`

  <span data-ttu-id="7f684-110">Flag la cui presenza abilita il flusso della risposta HTTP.</span><span class="sxs-lookup"><span data-stu-id="7f684-110">A flag whose presence enables streaming of the HTTP response.</span></span>
