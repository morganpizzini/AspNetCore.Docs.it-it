---
<span data-ttu-id="afcc6-101">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="afcc6-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="afcc6-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="afcc6-102">'Blazor'</span></span>
- <span data-ttu-id="afcc6-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="afcc6-103">'Identity'</span></span>
- <span data-ttu-id="afcc6-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="afcc6-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="afcc6-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="afcc6-105">'Razor'</span></span>
- <span data-ttu-id="afcc6-106">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="afcc6-106">'SignalR' uid:</span></span> 

---
# <a name="high-performance-logging-with-loggermessage-in-aspnet-core"></a><span data-ttu-id="afcc6-107">Registrazione a prestazioni elevate con LoggerMessage in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="afcc6-107">High-performance logging with LoggerMessage in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="afcc6-108">Le funzionalità di <xref:Microsoft.Extensions.Logging.LoggerMessage> creano delegati inseribili nella cache che richiedono un numero minore di allocazioni di oggetti e riducono il sovraccarico di calcolo rispetto ai [metodi di estensione del logger](xref:Microsoft.Extensions.Logging.LoggerExtensions), ad esempio <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> e <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*>.</span><span class="sxs-lookup"><span data-stu-id="afcc6-108"><xref:Microsoft.Extensions.Logging.LoggerMessage> features create cacheable delegates that require fewer object allocations and reduced computational overhead compared to [logger extension methods](xref:Microsoft.Extensions.Logging.LoggerExtensions), such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> and <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*>.</span></span> <span data-ttu-id="afcc6-109">Per gli scenari di registrazione a prestazioni elevate, usare <xref:Microsoft.Extensions.Logging.LoggerMessage>.</span><span class="sxs-lookup"><span data-stu-id="afcc6-109">For high-performance logging scenarios, use the <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern.</span></span>

<span data-ttu-id="afcc6-110"><xref:Microsoft.Extensions.Logging.LoggerMessage> offre i seguenti vantaggi in termini di prestazioni rispetto ai metodi di estensione del logger:</span><span class="sxs-lookup"><span data-stu-id="afcc6-110"><xref:Microsoft.Extensions.Logging.LoggerMessage> provides the following performance advantages over Logger extension methods:</span></span>

* <span data-ttu-id="afcc6-111">I metodi di estensione del logger richiedono una "conversione boxing" dei tipi di valori, ad esempio `int`, in `object`.</span><span class="sxs-lookup"><span data-stu-id="afcc6-111">Logger extension methods require "boxing" (converting) value types, such as `int`, into `object`.</span></span> <span data-ttu-id="afcc6-112"><xref:Microsoft.Extensions.Logging.LoggerMessage> evita la conversione boxing usando campi <xref:System.Action> statici e metodi di estensione con parametri fortemente tipizzati.</span><span class="sxs-lookup"><span data-stu-id="afcc6-112">The <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern avoids boxing by using static <xref:System.Action> fields and extension methods with strongly-typed parameters.</span></span>
* <span data-ttu-id="afcc6-113">I metodi di estensione del logger devono analizzare il modello di messaggio (stringa di formato denominata) ogni volta che viene scritto un messaggio del log.</span><span class="sxs-lookup"><span data-stu-id="afcc6-113">Logger extension methods must parse the message template (named format string) every time a log message is written.</span></span> <span data-ttu-id="afcc6-114">Solo <xref:Microsoft.Extensions.Logging.LoggerMessage> richiede una sola analisi del modello durante la definizione del messaggio.</span><span class="sxs-lookup"><span data-stu-id="afcc6-114"><xref:Microsoft.Extensions.Logging.LoggerMessage> only requires parsing a template once when the message is defined.</span></span>

<span data-ttu-id="afcc6-115">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/samples/) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="afcc6-115">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="afcc6-116">L'app di esempio illustra le funzionalità di <xref:Microsoft.Extensions.Logging.LoggerMessage> con un sistema di verifica delle offerte di base.</span><span class="sxs-lookup"><span data-stu-id="afcc6-116">The sample app demonstrates <xref:Microsoft.Extensions.Logging.LoggerMessage> features with a basic quote tracking system.</span></span> <span data-ttu-id="afcc6-117">L'app aggiunge ed elimina le offerte usando un database in memoria.</span><span class="sxs-lookup"><span data-stu-id="afcc6-117">The app adds and deletes quotes using an in-memory database.</span></span> <span data-ttu-id="afcc6-118">Durante l'esecuzione di queste operazioni, i messaggi del log vengono generati usando <xref:Microsoft.Extensions.Logging.LoggerMessage>.</span><span class="sxs-lookup"><span data-stu-id="afcc6-118">As these operations occur, log messages are generated using the <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern.</span></span>

## <a name="loggermessagedefine"></a><span data-ttu-id="afcc6-119">LoggerMessage.Define</span><span class="sxs-lookup"><span data-stu-id="afcc6-119">LoggerMessage.Define</span></span>

<span data-ttu-id="afcc6-120">[Define(LogLevel, EventId, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) crea un delegato <xref:System.Action> per la registrazione di un messaggio.</span><span class="sxs-lookup"><span data-stu-id="afcc6-120">[Define(LogLevel, EventId, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) creates an <xref:System.Action> delegate for logging a message.</span></span> <span data-ttu-id="afcc6-121">Gli overload <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> permettono il passaggio di un massimo di sei parametri di tipo in una stringa di formato denominata (modello).</span><span class="sxs-lookup"><span data-stu-id="afcc6-121"><xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> overloads permit passing up to six type parameters to a named format string (template).</span></span>

<span data-ttu-id="afcc6-122">La stringa specificata nel metodo <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> è un modello e non una stringa interpolata.</span><span class="sxs-lookup"><span data-stu-id="afcc6-122">The string provided to the <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> method is a template and not an interpolated string.</span></span> <span data-ttu-id="afcc6-123">I segnaposto vengono inseriti nell'ordine in cui sono specificati i tipi.</span><span class="sxs-lookup"><span data-stu-id="afcc6-123">Placeholders are filled in the order that the types are specified.</span></span> <span data-ttu-id="afcc6-124">I nomi dei segnaposto nel modello devono essere descrittivi e coerenti tra i modelli.</span><span class="sxs-lookup"><span data-stu-id="afcc6-124">Placeholder names in the template should be descriptive and consistent across templates.</span></span> <span data-ttu-id="afcc6-125">Vengono usati come nomi di proprietà all'interno dei dati di log strutturati.</span><span class="sxs-lookup"><span data-stu-id="afcc6-125">They serve as property names within structured log data.</span></span> <span data-ttu-id="afcc6-126">Per i nomi dei segnaposto è consigliabile usare la [convenzione Pascal](/dotnet/standard/design-guidelines/capitalization-conventions).</span><span class="sxs-lookup"><span data-stu-id="afcc6-126">We recommend [Pascal casing](/dotnet/standard/design-guidelines/capitalization-conventions) for placeholder names.</span></span> <span data-ttu-id="afcc6-127">Ad esempio, `{Count}`, `{FirstName}`.</span><span class="sxs-lookup"><span data-stu-id="afcc6-127">For example, `{Count}`, `{FirstName}`.</span></span>

<span data-ttu-id="afcc6-128">Ogni messaggio di log è un elemento <xref:System.Action> contenuto in un campo statico creato da [LoggerMessage.Define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*).</span><span class="sxs-lookup"><span data-stu-id="afcc6-128">Each log message is an <xref:System.Action> held in a static field created by [LoggerMessage.Define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*).</span></span> <span data-ttu-id="afcc6-129">Ad esempio, l'app di esempio crea un campo per descrivere un messaggio di log per una richiesta GET per la pagina di indice (*Internal/LoggerExtensions.cs*):</span><span class="sxs-lookup"><span data-stu-id="afcc6-129">For example, the sample app creates a field to describe a log message for a GET request for the Index page (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet1)]

<span data-ttu-id="afcc6-130">Per <xref:System.Action>, specificare:</span><span class="sxs-lookup"><span data-stu-id="afcc6-130">For the <xref:System.Action>, specify:</span></span>

* <span data-ttu-id="afcc6-131">Il livello del log.</span><span class="sxs-lookup"><span data-stu-id="afcc6-131">The log level.</span></span>
* <span data-ttu-id="afcc6-132">Un identificatore di evento univoco (<xref:Microsoft.Extensions.Logging.EventId>) con il nome del metodo di estensione statico.</span><span class="sxs-lookup"><span data-stu-id="afcc6-132">A unique event identifier (<xref:Microsoft.Extensions.Logging.EventId>) with the name of the static extension method.</span></span>
* <span data-ttu-id="afcc6-133">Il modello di messaggio (stringa di formato denominata).</span><span class="sxs-lookup"><span data-stu-id="afcc6-133">The message template (named format string).</span></span> 

<span data-ttu-id="afcc6-134">Una richiesta per la pagina di indice dell'app di esempio imposta:</span><span class="sxs-lookup"><span data-stu-id="afcc6-134">A request for the Index page of the sample app sets the:</span></span>

* <span data-ttu-id="afcc6-135">Il livello del log su `Information`.</span><span class="sxs-lookup"><span data-stu-id="afcc6-135">Log level to `Information`.</span></span>
* <span data-ttu-id="afcc6-136">L'ID evento su `1` con il nome del metodo `IndexPageRequested`.</span><span class="sxs-lookup"><span data-stu-id="afcc6-136">Event id to `1` with the name of the `IndexPageRequested` method.</span></span>
* <span data-ttu-id="afcc6-137">Il modello di messaggio (stringa di formato denominata) su una stringa.</span><span class="sxs-lookup"><span data-stu-id="afcc6-137">Message template (named format string) to a string.</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet5)]

<span data-ttu-id="afcc6-138">Negli archivi di log strutturati è possibile che venga usato il nome evento quando viene specificato con l'ID evento per arricchire la registrazione.</span><span class="sxs-lookup"><span data-stu-id="afcc6-138">Structured logging stores may use the event name when it's supplied with the event id to enrich logging.</span></span> <span data-ttu-id="afcc6-139">Ad esempio, [Serilog](https://github.com/serilog/serilog-extensions-logging) usa il nome evento.</span><span class="sxs-lookup"><span data-stu-id="afcc6-139">For example, [Serilog](https://github.com/serilog/serilog-extensions-logging) uses the event name.</span></span>

<span data-ttu-id="afcc6-140"><xref:System.Action> viene richiamato attraverso un metodo di estensione fortemente tipizzato.</span><span class="sxs-lookup"><span data-stu-id="afcc6-140">The <xref:System.Action> is invoked through a strongly-typed extension method.</span></span> <span data-ttu-id="afcc6-141">Il metodo `IndexPageRequested` registra un messaggio per la richiesta GET di una pagina di indice nell'app di esempio:</span><span class="sxs-lookup"><span data-stu-id="afcc6-141">The `IndexPageRequested` method logs a message for an Index page GET request in the sample app:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet9)]

<span data-ttu-id="afcc6-142">`IndexPageRequested` viene chiamato nel logger nel metodo `OnGetAsync` in *Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="afcc6-142">`IndexPageRequested` is called on the logger in the `OnGetAsync` method in *Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3)]

<span data-ttu-id="afcc6-143">Esaminare l'output della console dell'app:</span><span class="sxs-lookup"><span data-stu-id="afcc6-143">Inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[1]
      => RequestId:0HL90M6E7PHK4:00000001 RequestPath:/ => /Index
      GET request for Index page
```

<span data-ttu-id="afcc6-144">Per passare i parametri in un messaggio di log, definire un massimo di sei tipi durante la creazione del campo statico.</span><span class="sxs-lookup"><span data-stu-id="afcc6-144">To pass parameters to a log message, define up to six types when creating the static field.</span></span> <span data-ttu-id="afcc6-145">L'app di esempio registra una stringa quando viene aggiunta un'offerta definendo un tipo `string` per il campo <xref:System.Action>:</span><span class="sxs-lookup"><span data-stu-id="afcc6-145">The sample app logs a string when adding a quote by defining a `string` type for the <xref:System.Action> field:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet2)]

<span data-ttu-id="afcc6-146">Il modello di messaggio di log del delegato riceve i valori dei segnaposto dai tipi specificati.</span><span class="sxs-lookup"><span data-stu-id="afcc6-146">The delegate's log message template receives its placeholder values from the types provided.</span></span> <span data-ttu-id="afcc6-147">L'app di esempio definisce un delegato per l'aggiunta di un'offerta dove il parametro dell'offerta è un elemento `string`:</span><span class="sxs-lookup"><span data-stu-id="afcc6-147">The sample app defines a delegate for adding a quote where the quote parameter is a `string`:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet6)]

<span data-ttu-id="afcc6-148">Il metodo di estensione statico per l'aggiunta di un'offerta, `QuoteAdded`, riceve il valore dell'argomento dell'offerta e lo passa al delegato <xref:System.Action>:</span><span class="sxs-lookup"><span data-stu-id="afcc6-148">The static extension method for adding a quote, `QuoteAdded`, receives the quote argument value and passes it to the <xref:System.Action> delegate:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet10)]

<span data-ttu-id="afcc6-149">Nel modello di pagina della pagina di indice (*pages/index. cshtml. cs*) `QuoteAdded` viene chiamato il metodo per registrare il messaggio:</span><span class="sxs-lookup"><span data-stu-id="afcc6-149">In the Index page's page model (*Pages/Index.cshtml.cs*), `QuoteAdded` is called to log the message:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet3&highlight=6)]

<span data-ttu-id="afcc6-150">Esaminare l'output della console dell'app:</span><span class="sxs-lookup"><span data-stu-id="afcc6-150">Inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[2]
      => RequestId:0HL90M6E7PHK5:0000000A RequestPath:/ => /Index
      Quote added (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand')
```

<span data-ttu-id="afcc6-151">L'app di esempio implementa un modello [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) per l'eliminazione delle virgolette.</span><span class="sxs-lookup"><span data-stu-id="afcc6-151">The sample app implements a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) pattern for quote deletion.</span></span> <span data-ttu-id="afcc6-152">Per un'operazione di eliminazione con esito positivo, viene registrato un messaggio informativo.</span><span class="sxs-lookup"><span data-stu-id="afcc6-152">An informational message is logged for a successful delete operation.</span></span> <span data-ttu-id="afcc6-153">Per un'operazione di eliminazione con la generazione di un'eccezione, viene registrato un messaggio di errore.</span><span class="sxs-lookup"><span data-stu-id="afcc6-153">An error message is logged for a delete operation when an exception is thrown.</span></span> <span data-ttu-id="afcc6-154">Il messaggio di log per un'operazione di eliminazione con esito negativo include l'analisi dello stack dell'eccezione (*Internal/LoggerExtensions.cs*):</span><span class="sxs-lookup"><span data-stu-id="afcc6-154">The log message for the unsuccessful delete operation includes the exception stack trace (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet3)]

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet7)]

<span data-ttu-id="afcc6-155">Si noti come l'eccezione viene passata al delegato in `QuoteDeleteFailed`:</span><span class="sxs-lookup"><span data-stu-id="afcc6-155">Note how the exception is passed to the delegate in `QuoteDeleteFailed`:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet11)]

<span data-ttu-id="afcc6-156">Nel modello di pagina per la pagina di indice l'eliminazione di un'offerta con esito positivo chiama il metodo `QuoteDeleted` nel logger.</span><span class="sxs-lookup"><span data-stu-id="afcc6-156">In the page model for the Index page, a successful quote deletion calls the `QuoteDeleted` method on the logger.</span></span> <span data-ttu-id="afcc6-157">Quando non viene trovata un'offerta per l'eliminazione, viene generata un'eccezione <xref:System.ArgumentNullException>.</span><span class="sxs-lookup"><span data-stu-id="afcc6-157">When a quote isn't found for deletion, an <xref:System.ArgumentNullException> is thrown.</span></span> <span data-ttu-id="afcc6-158">L'eccezione viene intercettata dall'istruzione [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) e registrata chiamando il `QuoteDeleteFailed` metodo sul logger nel blocco [catch](/dotnet/csharp/language-reference/keywords/try-catch) (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="afcc6-158">The exception is trapped by the [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement and logged by calling the `QuoteDeleteFailed` method on the logger in the [catch](/dotnet/csharp/language-reference/keywords/try-catch) block (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet5&highlight=9,13)]

<span data-ttu-id="afcc6-159">Quando un'offerta viene eliminata, esaminare l'output della console dell'app:</span><span class="sxs-lookup"><span data-stu-id="afcc6-159">When a quote is successfully deleted, inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:00000016 RequestPath:/ => /Index
      Quote deleted (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand' Id = 1)
```

<span data-ttu-id="afcc6-160">Quando l'eliminazione di un'offerta ha esito negativo, esaminare l'output della console dell'app.</span><span class="sxs-lookup"><span data-stu-id="afcc6-160">When quote deletion fails, inspect the app's console output.</span></span> <span data-ttu-id="afcc6-161">Si noti che l'eccezione è inclusa nel messaggio di log:</span><span class="sxs-lookup"><span data-stu-id="afcc6-161">Note that the exception is included in the log message:</span></span>

```console
LoggerMessageSample.Pages.IndexModel: Error: Quote delete failed (Id = 999)

System.NullReferenceException: Object reference not set to an instance of an object.
   at lambda_method(Closure , ValueBuffer )
   at System.Linq.Enumerable.SelectEnumerableIterator`2.MoveNext()
   at Microsoft.EntityFrameworkCore.InMemory.Query.Internal.InMemoryShapedQueryCompilingExpressionVisitor.AsyncQueryingEnumerable`1.AsyncEnumerator.MoveNextAsync()
   at Microsoft.EntityFrameworkCore.Query.ShapedQueryCompilingExpressionVisitor.SingleOrDefaultAsync[TSource](IAsyncEnumerable`1 asyncEnumerable, CancellationToken cancellationToken)
   at Microsoft.EntityFrameworkCore.Query.ShapedQueryCompilingExpressionVisitor.SingleOrDefaultAsync[TSource](IAsyncEnumerable`1 asyncEnumerable, CancellationToken cancellationToken)
   at LoggerMessageSample.Pages.IndexModel.OnPostDeleteQuoteAsync(Int32 id) in c:\Users\guard\Documents\GitHub\Docs\aspnetcore\fundamentals\logging\loggermessage\samples\3.x\LoggerMessageSample\Pages\Index.cshtml.cs:line 77
```

## <a name="loggermessagedefinescope"></a><span data-ttu-id="afcc6-162">LoggerMessage.DefineScope</span><span class="sxs-lookup"><span data-stu-id="afcc6-162">LoggerMessage.DefineScope</span></span>

<span data-ttu-id="afcc6-163">[DefineScope(String)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*) crea un delegato <xref:System.Func%601> per definire un [ambito del log](xref:fundamentals/logging/index#log-scopes).</span><span class="sxs-lookup"><span data-stu-id="afcc6-163">[DefineScope(String)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*) creates a <xref:System.Func%601> delegate for defining a [log scope](xref:fundamentals/logging/index#log-scopes).</span></span> <span data-ttu-id="afcc6-164">Gli overload <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> permettono il passaggio di un massimo di tre parametri di tipo in una stringa di formato denominata (modello).</span><span class="sxs-lookup"><span data-stu-id="afcc6-164"><xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> overloads permit passing up to three type parameters to a named format string (template).</span></span>

<span data-ttu-id="afcc6-165">Come avviene per il metodo <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*>, la stringa specificata nel metodo <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> è un modello e non una stringa interpolata.</span><span class="sxs-lookup"><span data-stu-id="afcc6-165">As is the case with the <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> method, the string provided to the <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> method is a template and not an interpolated string.</span></span> <span data-ttu-id="afcc6-166">I segnaposto vengono inseriti nell'ordine in cui sono specificati i tipi.</span><span class="sxs-lookup"><span data-stu-id="afcc6-166">Placeholders are filled in the order that the types are specified.</span></span> <span data-ttu-id="afcc6-167">I nomi dei segnaposto nel modello devono essere descrittivi e coerenti tra i modelli.</span><span class="sxs-lookup"><span data-stu-id="afcc6-167">Placeholder names in the template should be descriptive and consistent across templates.</span></span> <span data-ttu-id="afcc6-168">Vengono usati come nomi di proprietà all'interno dei dati di log strutturati.</span><span class="sxs-lookup"><span data-stu-id="afcc6-168">They serve as property names within structured log data.</span></span> <span data-ttu-id="afcc6-169">Per i nomi dei segnaposto è consigliabile usare la [convenzione Pascal](/dotnet/standard/design-guidelines/capitalization-conventions).</span><span class="sxs-lookup"><span data-stu-id="afcc6-169">We recommend [Pascal casing](/dotnet/standard/design-guidelines/capitalization-conventions) for placeholder names.</span></span> <span data-ttu-id="afcc6-170">Ad esempio, `{Count}`, `{FirstName}`.</span><span class="sxs-lookup"><span data-stu-id="afcc6-170">For example, `{Count}`, `{FirstName}`.</span></span>

<span data-ttu-id="afcc6-171">Definire un [ambito del log](xref:fundamentals/logging/index#log-scopes) da applicare a una serie di messaggi di log usando il metodo <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*>.</span><span class="sxs-lookup"><span data-stu-id="afcc6-171">Define a [log scope](xref:fundamentals/logging/index#log-scopes) to apply to a series of log messages using the <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> method.</span></span>

<span data-ttu-id="afcc6-172">L'app di esempio include un pulsante **Clear All** (Cancella tutto) per eliminare tutte le offerte nel database.</span><span class="sxs-lookup"><span data-stu-id="afcc6-172">The sample app has a **Clear All** button for deleting all of the quotes in the database.</span></span> <span data-ttu-id="afcc6-173">Le offerte vengono eliminate rimuovendole una alla volta.</span><span class="sxs-lookup"><span data-stu-id="afcc6-173">The quotes are deleted by removing them one at a time.</span></span> <span data-ttu-id="afcc6-174">Ogni volta che viene eliminata un'offerta, viene chiamato il metodo `QuoteDeleted` nel logger.</span><span class="sxs-lookup"><span data-stu-id="afcc6-174">Each time a quote is deleted, the `QuoteDeleted` method is called on the logger.</span></span> <span data-ttu-id="afcc6-175">Viene aggiunto un ambito di log a questi messaggi di log.</span><span class="sxs-lookup"><span data-stu-id="afcc6-175">A log scope is added to these log messages.</span></span>

<span data-ttu-id="afcc6-176">Abilitare `IncludeScopes` nella sezione del logger di console di *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="afcc6-176">Enable `IncludeScopes` in the console logger section of *appsettings.json*:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/appsettings.json?highlight=3-5)]

<span data-ttu-id="afcc6-177">Per creare un ambito di log, aggiungere un campo per inserire un delegato <xref:System.Func%601> per l'ambito.</span><span class="sxs-lookup"><span data-stu-id="afcc6-177">To create a log scope, add a field to hold a <xref:System.Func%601> delegate for the scope.</span></span> <span data-ttu-id="afcc6-178">L'app di esempio crea un campo denominato `_allQuotesDeletedScope` (*Internal/LoggerExtensions.cs*):</span><span class="sxs-lookup"><span data-stu-id="afcc6-178">The sample app creates a field called `_allQuotesDeletedScope` (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet4)]

<span data-ttu-id="afcc6-179">Usare <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> per creare il delegato.</span><span class="sxs-lookup"><span data-stu-id="afcc6-179">Use <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> to create the delegate.</span></span> <span data-ttu-id="afcc6-180">È possibile specificare un massimo di tre tipi da usare come argomenti di modello quando viene richiamato il delegato.</span><span class="sxs-lookup"><span data-stu-id="afcc6-180">Up to three types can be specified for use as template arguments when the delegate is invoked.</span></span> <span data-ttu-id="afcc6-181">L'app di esempio usa un modello di messaggio che include il numero di offerte eliminate (un tipo `int`):</span><span class="sxs-lookup"><span data-stu-id="afcc6-181">The sample app uses a message template that includes the number of deleted quotes (an `int` type):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet8)]

<span data-ttu-id="afcc6-182">Specificare un metodo di estensione statico per il messaggio di log.</span><span class="sxs-lookup"><span data-stu-id="afcc6-182">Provide a static extension method for the log message.</span></span> <span data-ttu-id="afcc6-183">Includere i parametri di tipo per le proprietà denominate visualizzate nel modello di messaggio.</span><span class="sxs-lookup"><span data-stu-id="afcc6-183">Include any type parameters for named properties that appear in the message template.</span></span> <span data-ttu-id="afcc6-184">L'app di esempio accetta un numero `count` di offerte da eliminare e restituisce `_allQuotesDeletedScope`:</span><span class="sxs-lookup"><span data-stu-id="afcc6-184">The sample app takes in a `count` of quotes to delete and returns `_allQuotesDeletedScope`:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet12)]

<span data-ttu-id="afcc6-185">L'ambito esegue il wrapping delle chiamate di estensione di registrazione in un blocco [using](/dotnet/csharp/language-reference/keywords/using-statement):</span><span class="sxs-lookup"><span data-stu-id="afcc6-185">The scope wraps the logging extension calls in a [using](/dotnet/csharp/language-reference/keywords/using-statement) block:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet4&highlight=5-6,14)]

<span data-ttu-id="afcc6-186">Esaminare i messaggi di log nell'output della console dell'app.</span><span class="sxs-lookup"><span data-stu-id="afcc6-186">Inspect the log messages in the app's console output.</span></span> <span data-ttu-id="afcc6-187">Il risultato seguente mostra tre offerte eliminate con il messaggio di ambito di log incluso:</span><span class="sxs-lookup"><span data-stu-id="afcc6-187">The following result shows three quotes deleted with the log scope message included:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 1' Id = 2)
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 2' Id = 3)
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 3' Id = 4)
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="afcc6-188">Le funzionalità di <xref:Microsoft.Extensions.Logging.LoggerMessage> creano delegati inseribili nella cache che richiedono un numero minore di allocazioni di oggetti e riducono il sovraccarico di calcolo rispetto ai [metodi di estensione del logger](xref:Microsoft.Extensions.Logging.LoggerExtensions), ad esempio <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> e <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*>.</span><span class="sxs-lookup"><span data-stu-id="afcc6-188"><xref:Microsoft.Extensions.Logging.LoggerMessage> features create cacheable delegates that require fewer object allocations and reduced computational overhead compared to [logger extension methods](xref:Microsoft.Extensions.Logging.LoggerExtensions), such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> and <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*>.</span></span> <span data-ttu-id="afcc6-189">Per gli scenari di registrazione a prestazioni elevate, usare <xref:Microsoft.Extensions.Logging.LoggerMessage>.</span><span class="sxs-lookup"><span data-stu-id="afcc6-189">For high-performance logging scenarios, use the <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern.</span></span>

<span data-ttu-id="afcc6-190"><xref:Microsoft.Extensions.Logging.LoggerMessage> offre i seguenti vantaggi in termini di prestazioni rispetto ai metodi di estensione del logger:</span><span class="sxs-lookup"><span data-stu-id="afcc6-190"><xref:Microsoft.Extensions.Logging.LoggerMessage> provides the following performance advantages over Logger extension methods:</span></span>

* <span data-ttu-id="afcc6-191">I metodi di estensione del logger richiedono una "conversione boxing" dei tipi di valori, ad esempio `int`, in `object`.</span><span class="sxs-lookup"><span data-stu-id="afcc6-191">Logger extension methods require "boxing" (converting) value types, such as `int`, into `object`.</span></span> <span data-ttu-id="afcc6-192"><xref:Microsoft.Extensions.Logging.LoggerMessage> evita la conversione boxing usando campi <xref:System.Action> statici e metodi di estensione con parametri fortemente tipizzati.</span><span class="sxs-lookup"><span data-stu-id="afcc6-192">The <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern avoids boxing by using static <xref:System.Action> fields and extension methods with strongly-typed parameters.</span></span>
* <span data-ttu-id="afcc6-193">I metodi di estensione del logger devono analizzare il modello di messaggio (stringa di formato denominata) ogni volta che viene scritto un messaggio del log.</span><span class="sxs-lookup"><span data-stu-id="afcc6-193">Logger extension methods must parse the message template (named format string) every time a log message is written.</span></span> <span data-ttu-id="afcc6-194">Solo <xref:Microsoft.Extensions.Logging.LoggerMessage> richiede una sola analisi del modello durante la definizione del messaggio.</span><span class="sxs-lookup"><span data-stu-id="afcc6-194"><xref:Microsoft.Extensions.Logging.LoggerMessage> only requires parsing a template once when the message is defined.</span></span>

<span data-ttu-id="afcc6-195">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/samples/) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="afcc6-195">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="afcc6-196">L'app di esempio illustra le funzionalità di <xref:Microsoft.Extensions.Logging.LoggerMessage> con un sistema di verifica delle offerte di base.</span><span class="sxs-lookup"><span data-stu-id="afcc6-196">The sample app demonstrates <xref:Microsoft.Extensions.Logging.LoggerMessage> features with a basic quote tracking system.</span></span> <span data-ttu-id="afcc6-197">L'app aggiunge ed elimina le offerte usando un database in memoria.</span><span class="sxs-lookup"><span data-stu-id="afcc6-197">The app adds and deletes quotes using an in-memory database.</span></span> <span data-ttu-id="afcc6-198">Durante l'esecuzione di queste operazioni, i messaggi del log vengono generati usando <xref:Microsoft.Extensions.Logging.LoggerMessage>.</span><span class="sxs-lookup"><span data-stu-id="afcc6-198">As these operations occur, log messages are generated using the <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern.</span></span>

## <a name="loggermessagedefine"></a><span data-ttu-id="afcc6-199">LoggerMessage.Define</span><span class="sxs-lookup"><span data-stu-id="afcc6-199">LoggerMessage.Define</span></span>

<span data-ttu-id="afcc6-200">[Define(LogLevel, EventId, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) crea un delegato <xref:System.Action> per la registrazione di un messaggio.</span><span class="sxs-lookup"><span data-stu-id="afcc6-200">[Define(LogLevel, EventId, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) creates an <xref:System.Action> delegate for logging a message.</span></span> <span data-ttu-id="afcc6-201">Gli overload <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> permettono il passaggio di un massimo di sei parametri di tipo in una stringa di formato denominata (modello).</span><span class="sxs-lookup"><span data-stu-id="afcc6-201"><xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> overloads permit passing up to six type parameters to a named format string (template).</span></span>

<span data-ttu-id="afcc6-202">La stringa specificata nel metodo <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> è un modello e non una stringa interpolata.</span><span class="sxs-lookup"><span data-stu-id="afcc6-202">The string provided to the <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> method is a template and not an interpolated string.</span></span> <span data-ttu-id="afcc6-203">I segnaposto vengono inseriti nell'ordine in cui sono specificati i tipi.</span><span class="sxs-lookup"><span data-stu-id="afcc6-203">Placeholders are filled in the order that the types are specified.</span></span> <span data-ttu-id="afcc6-204">I nomi dei segnaposto nel modello devono essere descrittivi e coerenti tra i modelli.</span><span class="sxs-lookup"><span data-stu-id="afcc6-204">Placeholder names in the template should be descriptive and consistent across templates.</span></span> <span data-ttu-id="afcc6-205">Vengono usati come nomi di proprietà all'interno dei dati di log strutturati.</span><span class="sxs-lookup"><span data-stu-id="afcc6-205">They serve as property names within structured log data.</span></span> <span data-ttu-id="afcc6-206">Per i nomi dei segnaposto è consigliabile usare la [convenzione Pascal](/dotnet/standard/design-guidelines/capitalization-conventions).</span><span class="sxs-lookup"><span data-stu-id="afcc6-206">We recommend [Pascal casing](/dotnet/standard/design-guidelines/capitalization-conventions) for placeholder names.</span></span> <span data-ttu-id="afcc6-207">Ad esempio, `{Count}`, `{FirstName}`.</span><span class="sxs-lookup"><span data-stu-id="afcc6-207">For example, `{Count}`, `{FirstName}`.</span></span>

<span data-ttu-id="afcc6-208">Ogni messaggio di log è un elemento <xref:System.Action> contenuto in un campo statico creato da [LoggerMessage.Define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*).</span><span class="sxs-lookup"><span data-stu-id="afcc6-208">Each log message is an <xref:System.Action> held in a static field created by [LoggerMessage.Define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*).</span></span> <span data-ttu-id="afcc6-209">Ad esempio, l'app di esempio crea un campo per descrivere un messaggio di log per una richiesta GET per la pagina di indice (*Internal/LoggerExtensions.cs*):</span><span class="sxs-lookup"><span data-stu-id="afcc6-209">For example, the sample app creates a field to describe a log message for a GET request for the Index page (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet1)]

<span data-ttu-id="afcc6-210">Per <xref:System.Action>, specificare:</span><span class="sxs-lookup"><span data-stu-id="afcc6-210">For the <xref:System.Action>, specify:</span></span>

* <span data-ttu-id="afcc6-211">Il livello del log.</span><span class="sxs-lookup"><span data-stu-id="afcc6-211">The log level.</span></span>
* <span data-ttu-id="afcc6-212">Un identificatore di evento univoco (<xref:Microsoft.Extensions.Logging.EventId>) con il nome del metodo di estensione statico.</span><span class="sxs-lookup"><span data-stu-id="afcc6-212">A unique event identifier (<xref:Microsoft.Extensions.Logging.EventId>) with the name of the static extension method.</span></span>
* <span data-ttu-id="afcc6-213">Il modello di messaggio (stringa di formato denominata).</span><span class="sxs-lookup"><span data-stu-id="afcc6-213">The message template (named format string).</span></span> 

<span data-ttu-id="afcc6-214">Una richiesta per la pagina di indice dell'app di esempio imposta:</span><span class="sxs-lookup"><span data-stu-id="afcc6-214">A request for the Index page of the sample app sets the:</span></span>

* <span data-ttu-id="afcc6-215">Il livello del log su `Information`.</span><span class="sxs-lookup"><span data-stu-id="afcc6-215">Log level to `Information`.</span></span>
* <span data-ttu-id="afcc6-216">L'ID evento su `1` con il nome del metodo `IndexPageRequested`.</span><span class="sxs-lookup"><span data-stu-id="afcc6-216">Event id to `1` with the name of the `IndexPageRequested` method.</span></span>
* <span data-ttu-id="afcc6-217">Il modello di messaggio (stringa di formato denominata) su una stringa.</span><span class="sxs-lookup"><span data-stu-id="afcc6-217">Message template (named format string) to a string.</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet5)]

<span data-ttu-id="afcc6-218">Negli archivi di log strutturati è possibile che venga usato il nome evento quando viene specificato con l'ID evento per arricchire la registrazione.</span><span class="sxs-lookup"><span data-stu-id="afcc6-218">Structured logging stores may use the event name when it's supplied with the event id to enrich logging.</span></span> <span data-ttu-id="afcc6-219">Ad esempio, [Serilog](https://github.com/serilog/serilog-extensions-logging) usa il nome evento.</span><span class="sxs-lookup"><span data-stu-id="afcc6-219">For example, [Serilog](https://github.com/serilog/serilog-extensions-logging) uses the event name.</span></span>

<span data-ttu-id="afcc6-220"><xref:System.Action> viene richiamato attraverso un metodo di estensione fortemente tipizzato.</span><span class="sxs-lookup"><span data-stu-id="afcc6-220">The <xref:System.Action> is invoked through a strongly-typed extension method.</span></span> <span data-ttu-id="afcc6-221">Il metodo `IndexPageRequested` registra un messaggio per la richiesta GET di una pagina di indice nell'app di esempio:</span><span class="sxs-lookup"><span data-stu-id="afcc6-221">The `IndexPageRequested` method logs a message for an Index page GET request in the sample app:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet9)]

<span data-ttu-id="afcc6-222">`IndexPageRequested` viene chiamato nel logger nel metodo `OnGetAsync` in *Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="afcc6-222">`IndexPageRequested` is called on the logger in the `OnGetAsync` method in *Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3)]

<span data-ttu-id="afcc6-223">Esaminare l'output della console dell'app:</span><span class="sxs-lookup"><span data-stu-id="afcc6-223">Inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[1]
      => RequestId:0HL90M6E7PHK4:00000001 RequestPath:/ => /Index
      GET request for Index page
```

<span data-ttu-id="afcc6-224">Per passare i parametri in un messaggio di log, definire un massimo di sei tipi durante la creazione del campo statico.</span><span class="sxs-lookup"><span data-stu-id="afcc6-224">To pass parameters to a log message, define up to six types when creating the static field.</span></span> <span data-ttu-id="afcc6-225">L'app di esempio registra una stringa quando viene aggiunta un'offerta definendo un tipo `string` per il campo <xref:System.Action>:</span><span class="sxs-lookup"><span data-stu-id="afcc6-225">The sample app logs a string when adding a quote by defining a `string` type for the <xref:System.Action> field:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet2)]

<span data-ttu-id="afcc6-226">Il modello di messaggio di log del delegato riceve i valori dei segnaposto dai tipi specificati.</span><span class="sxs-lookup"><span data-stu-id="afcc6-226">The delegate's log message template receives its placeholder values from the types provided.</span></span> <span data-ttu-id="afcc6-227">L'app di esempio definisce un delegato per l'aggiunta di un'offerta dove il parametro dell'offerta è un elemento `string`:</span><span class="sxs-lookup"><span data-stu-id="afcc6-227">The sample app defines a delegate for adding a quote where the quote parameter is a `string`:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet6)]

<span data-ttu-id="afcc6-228">Il metodo di estensione statico per l'aggiunta di un'offerta, `QuoteAdded`, riceve il valore dell'argomento dell'offerta e lo passa al delegato <xref:System.Action>:</span><span class="sxs-lookup"><span data-stu-id="afcc6-228">The static extension method for adding a quote, `QuoteAdded`, receives the quote argument value and passes it to the <xref:System.Action> delegate:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet10)]

<span data-ttu-id="afcc6-229">Nel modello di pagina della pagina di indice (*pages/index. cshtml. cs*) `QuoteAdded` viene chiamato il metodo per registrare il messaggio:</span><span class="sxs-lookup"><span data-stu-id="afcc6-229">In the Index page's page model (*Pages/Index.cshtml.cs*), `QuoteAdded` is called to log the message:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet3&highlight=6)]

<span data-ttu-id="afcc6-230">Esaminare l'output della console dell'app:</span><span class="sxs-lookup"><span data-stu-id="afcc6-230">Inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[2]
      => RequestId:0HL90M6E7PHK5:0000000A RequestPath:/ => /Index
      Quote added (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand')
```

<span data-ttu-id="afcc6-231">L'app di esempio implementa un modello [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) per l'eliminazione delle virgolette.</span><span class="sxs-lookup"><span data-stu-id="afcc6-231">The sample app implements a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) pattern for quote deletion.</span></span> <span data-ttu-id="afcc6-232">Per un'operazione di eliminazione con esito positivo, viene registrato un messaggio informativo.</span><span class="sxs-lookup"><span data-stu-id="afcc6-232">An informational message is logged for a successful delete operation.</span></span> <span data-ttu-id="afcc6-233">Per un'operazione di eliminazione con la generazione di un'eccezione, viene registrato un messaggio di errore.</span><span class="sxs-lookup"><span data-stu-id="afcc6-233">An error message is logged for a delete operation when an exception is thrown.</span></span> <span data-ttu-id="afcc6-234">Il messaggio di log per un'operazione di eliminazione con esito negativo include l'analisi dello stack dell'eccezione (*Internal/LoggerExtensions.cs*):</span><span class="sxs-lookup"><span data-stu-id="afcc6-234">The log message for the unsuccessful delete operation includes the exception stack trace (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet3)]

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet7)]

<span data-ttu-id="afcc6-235">Si noti come l'eccezione viene passata al delegato in `QuoteDeleteFailed`:</span><span class="sxs-lookup"><span data-stu-id="afcc6-235">Note how the exception is passed to the delegate in `QuoteDeleteFailed`:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet11)]

<span data-ttu-id="afcc6-236">Nel modello di pagina per la pagina di indice l'eliminazione di un'offerta con esito positivo chiama il metodo `QuoteDeleted` nel logger.</span><span class="sxs-lookup"><span data-stu-id="afcc6-236">In the page model for the Index page, a successful quote deletion calls the `QuoteDeleted` method on the logger.</span></span> <span data-ttu-id="afcc6-237">Quando non viene trovata un'offerta per l'eliminazione, viene generata un'eccezione <xref:System.ArgumentNullException>.</span><span class="sxs-lookup"><span data-stu-id="afcc6-237">When a quote isn't found for deletion, an <xref:System.ArgumentNullException> is thrown.</span></span> <span data-ttu-id="afcc6-238">L'eccezione viene intercettata dall'istruzione [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) e registrata chiamando il `QuoteDeleteFailed` metodo sul logger nel blocco [catch](/dotnet/csharp/language-reference/keywords/try-catch) (*pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="afcc6-238">The exception is trapped by the [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement and logged by calling the `QuoteDeleteFailed` method on the logger in the [catch](/dotnet/csharp/language-reference/keywords/try-catch) block (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet5&highlight=14,18)]

<span data-ttu-id="afcc6-239">Quando un'offerta viene eliminata, esaminare l'output della console dell'app:</span><span class="sxs-lookup"><span data-stu-id="afcc6-239">When a quote is successfully deleted, inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:00000016 RequestPath:/ => /Index
      Quote deleted (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand' Id = 1)
```

<span data-ttu-id="afcc6-240">Quando l'eliminazione di un'offerta ha esito negativo, esaminare l'output della console dell'app.</span><span class="sxs-lookup"><span data-stu-id="afcc6-240">When quote deletion fails, inspect the app's console output.</span></span> <span data-ttu-id="afcc6-241">Si noti che l'eccezione è inclusa nel messaggio di log:</span><span class="sxs-lookup"><span data-stu-id="afcc6-241">Note that the exception is included in the log message:</span></span>

```console
fail: LoggerMessageSample.Pages.IndexModel[5]
      => RequestId:0HL90M6E7PHK5:00000010 RequestPath:/ => /Index
      Quote delete failed (Id = 999)
System.ArgumentNullException: Value cannot be null.
Parameter name: entity
   at Microsoft.EntityFrameworkCore.Utilities.Check.NotNull[T]
       (T value, String parameterName)
   at Microsoft.EntityFrameworkCore.DbContext.Remove[TEntity](TEntity entity)
   at Microsoft.EntityFrameworkCore.Internal.InternalDbSet`1.Remove(TEntity entity)
   at LoggerMessageSample.Pages.IndexModel.<OnPostDeleteQuoteAsync>d__14.MoveNext() 
      in <PATH>\sample\Pages\Index.cshtml.cs:line 87
```

## <a name="loggermessagedefinescope"></a><span data-ttu-id="afcc6-242">LoggerMessage.DefineScope</span><span class="sxs-lookup"><span data-stu-id="afcc6-242">LoggerMessage.DefineScope</span></span>

<span data-ttu-id="afcc6-243">[DefineScope(String)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*) crea un delegato <xref:System.Func%601> per definire un [ambito del log](xref:fundamentals/logging/index#log-scopes).</span><span class="sxs-lookup"><span data-stu-id="afcc6-243">[DefineScope(String)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*) creates a <xref:System.Func%601> delegate for defining a [log scope](xref:fundamentals/logging/index#log-scopes).</span></span> <span data-ttu-id="afcc6-244">Gli overload <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> permettono il passaggio di un massimo di tre parametri di tipo in una stringa di formato denominata (modello).</span><span class="sxs-lookup"><span data-stu-id="afcc6-244"><xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> overloads permit passing up to three type parameters to a named format string (template).</span></span>

<span data-ttu-id="afcc6-245">Come avviene per il metodo <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*>, la stringa specificata nel metodo <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> è un modello e non una stringa interpolata.</span><span class="sxs-lookup"><span data-stu-id="afcc6-245">As is the case with the <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> method, the string provided to the <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> method is a template and not an interpolated string.</span></span> <span data-ttu-id="afcc6-246">I segnaposto vengono inseriti nell'ordine in cui sono specificati i tipi.</span><span class="sxs-lookup"><span data-stu-id="afcc6-246">Placeholders are filled in the order that the types are specified.</span></span> <span data-ttu-id="afcc6-247">I nomi dei segnaposto nel modello devono essere descrittivi e coerenti tra i modelli.</span><span class="sxs-lookup"><span data-stu-id="afcc6-247">Placeholder names in the template should be descriptive and consistent across templates.</span></span> <span data-ttu-id="afcc6-248">Vengono usati come nomi di proprietà all'interno dei dati di log strutturati.</span><span class="sxs-lookup"><span data-stu-id="afcc6-248">They serve as property names within structured log data.</span></span> <span data-ttu-id="afcc6-249">Per i nomi dei segnaposto è consigliabile usare la [convenzione Pascal](/dotnet/standard/design-guidelines/capitalization-conventions).</span><span class="sxs-lookup"><span data-stu-id="afcc6-249">We recommend [Pascal casing](/dotnet/standard/design-guidelines/capitalization-conventions) for placeholder names.</span></span> <span data-ttu-id="afcc6-250">Ad esempio, `{Count}`, `{FirstName}`.</span><span class="sxs-lookup"><span data-stu-id="afcc6-250">For example, `{Count}`, `{FirstName}`.</span></span>

<span data-ttu-id="afcc6-251">Definire un [ambito del log](xref:fundamentals/logging/index#log-scopes) da applicare a una serie di messaggi di log usando il metodo <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*>.</span><span class="sxs-lookup"><span data-stu-id="afcc6-251">Define a [log scope](xref:fundamentals/logging/index#log-scopes) to apply to a series of log messages using the <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> method.</span></span>

<span data-ttu-id="afcc6-252">L'app di esempio include un pulsante **Clear All** (Cancella tutto) per eliminare tutte le offerte nel database.</span><span class="sxs-lookup"><span data-stu-id="afcc6-252">The sample app has a **Clear All** button for deleting all of the quotes in the database.</span></span> <span data-ttu-id="afcc6-253">Le offerte vengono eliminate rimuovendole una alla volta.</span><span class="sxs-lookup"><span data-stu-id="afcc6-253">The quotes are deleted by removing them one at a time.</span></span> <span data-ttu-id="afcc6-254">Ogni volta che viene eliminata un'offerta, viene chiamato il metodo `QuoteDeleted` nel logger.</span><span class="sxs-lookup"><span data-stu-id="afcc6-254">Each time a quote is deleted, the `QuoteDeleted` method is called on the logger.</span></span> <span data-ttu-id="afcc6-255">Viene aggiunto un ambito di log a questi messaggi di log.</span><span class="sxs-lookup"><span data-stu-id="afcc6-255">A log scope is added to these log messages.</span></span>

<span data-ttu-id="afcc6-256">Abilitare `IncludeScopes` nella sezione del logger di console di *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="afcc6-256">Enable `IncludeScopes` in the console logger section of *appsettings.json*:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/appsettings.json?highlight=3-5)]

<span data-ttu-id="afcc6-257">Per creare un ambito di log, aggiungere un campo per inserire un delegato <xref:System.Func%601> per l'ambito.</span><span class="sxs-lookup"><span data-stu-id="afcc6-257">To create a log scope, add a field to hold a <xref:System.Func%601> delegate for the scope.</span></span> <span data-ttu-id="afcc6-258">L'app di esempio crea un campo denominato `_allQuotesDeletedScope` (*Internal/LoggerExtensions.cs*):</span><span class="sxs-lookup"><span data-stu-id="afcc6-258">The sample app creates a field called `_allQuotesDeletedScope` (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet4)]

<span data-ttu-id="afcc6-259">Usare <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> per creare il delegato.</span><span class="sxs-lookup"><span data-stu-id="afcc6-259">Use <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> to create the delegate.</span></span> <span data-ttu-id="afcc6-260">È possibile specificare un massimo di tre tipi da usare come argomenti di modello quando viene richiamato il delegato.</span><span class="sxs-lookup"><span data-stu-id="afcc6-260">Up to three types can be specified for use as template arguments when the delegate is invoked.</span></span> <span data-ttu-id="afcc6-261">L'app di esempio usa un modello di messaggio che include il numero di offerte eliminate (un tipo `int`):</span><span class="sxs-lookup"><span data-stu-id="afcc6-261">The sample app uses a message template that includes the number of deleted quotes (an `int` type):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet8)]

<span data-ttu-id="afcc6-262">Specificare un metodo di estensione statico per il messaggio di log.</span><span class="sxs-lookup"><span data-stu-id="afcc6-262">Provide a static extension method for the log message.</span></span> <span data-ttu-id="afcc6-263">Includere i parametri di tipo per le proprietà denominate visualizzate nel modello di messaggio.</span><span class="sxs-lookup"><span data-stu-id="afcc6-263">Include any type parameters for named properties that appear in the message template.</span></span> <span data-ttu-id="afcc6-264">L'app di esempio accetta un numero `count` di offerte da eliminare e restituisce `_allQuotesDeletedScope`:</span><span class="sxs-lookup"><span data-stu-id="afcc6-264">The sample app takes in a `count` of quotes to delete and returns `_allQuotesDeletedScope`:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet12)]

<span data-ttu-id="afcc6-265">L'ambito esegue il wrapping delle chiamate di estensione di registrazione in un blocco [using](/dotnet/csharp/language-reference/keywords/using-statement):</span><span class="sxs-lookup"><span data-stu-id="afcc6-265">The scope wraps the logging extension calls in a [using](/dotnet/csharp/language-reference/keywords/using-statement) block:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet4&highlight=5-6,14)]

<span data-ttu-id="afcc6-266">Esaminare i messaggi di log nell'output della console dell'app.</span><span class="sxs-lookup"><span data-stu-id="afcc6-266">Inspect the log messages in the app's console output.</span></span> <span data-ttu-id="afcc6-267">Il risultato seguente mostra tre offerte eliminate con il messaggio di ambito di log incluso:</span><span class="sxs-lookup"><span data-stu-id="afcc6-267">The following result shows three quotes deleted with the log scope message included:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 1' Id = 2)
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 2' Id = 3)
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 3' Id = 4)
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="afcc6-268">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="afcc6-268">Additional resources</span></span>

* [<span data-ttu-id="afcc6-269">Registrazione</span><span class="sxs-lookup"><span data-stu-id="afcc6-269">Logging</span></span>](xref:fundamentals/logging/index)
