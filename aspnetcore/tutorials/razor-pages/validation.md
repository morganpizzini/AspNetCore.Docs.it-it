---
title: Aggiungere la convalida a una pagina Razor ASP.NET Core
author: rick-anderson
description: Informazioni su come aggiungere la convalida a una pagina Razor in ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 7/23/2019
uid: tutorials/razor-pages/validation
ms.openlocfilehash: f283234ed8a32dc9b7904bc6fee1cc9c04741029
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "78666019"
---
# <a name="add-validation-to-an-aspnet-core-razor-page"></a><span data-ttu-id="76300-103">Aggiungere la convalida a una pagina Razor ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="76300-103">Add validation to an ASP.NET Core Razor Page</span></span>

<span data-ttu-id="76300-104">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="76300-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="76300-105">In questa sezione la logica di convalida viene aggiunta al modello `Movie`.</span><span class="sxs-lookup"><span data-stu-id="76300-105">In this section, validation logic is added to the `Movie` model.</span></span> <span data-ttu-id="76300-106">Le regole di convalida vengono applicate ogni volta che un utente crea o modifica un film.</span><span class="sxs-lookup"><span data-stu-id="76300-106">The validation rules are enforced any time a user creates or edits a movie.</span></span>

## <a name="validation"></a><span data-ttu-id="76300-107">Convalida</span><span class="sxs-lookup"><span data-stu-id="76300-107">Validation</span></span>

<span data-ttu-id="76300-108">Un concetto di base dello sviluppo del software si chiama [DRY](https://wikipedia.org/wiki/Don%27t_repeat_yourself) ("**D**on't **R**epeat **Y**ourself", Non ripeterti).</span><span class="sxs-lookup"><span data-stu-id="76300-108">A key tenet of software development is called [DRY](https://wikipedia.org/wiki/Don%27t_repeat_yourself) ("**D**on't **R**epeat **Y**ourself").</span></span> <span data-ttu-id="76300-109">Le pagine Razor promuovono lo sviluppo in cui la funzionalità è stata specificata una volta e questa modifica è riflessa sull'intera app.</span><span class="sxs-lookup"><span data-stu-id="76300-109">Razor Pages encourages development where functionality is specified once, and it's reflected throughout the app.</span></span> <span data-ttu-id="76300-110">DRY contribuisce a:</span><span class="sxs-lookup"><span data-stu-id="76300-110">DRY can help:</span></span>

* <span data-ttu-id="76300-111">Ridurre la quantità di codice in un'app.</span><span class="sxs-lookup"><span data-stu-id="76300-111">Reduce the amount of code in an app.</span></span>
* <span data-ttu-id="76300-112">Rendere il codice meno soggetto ad errori e più facile da testare e gestire.</span><span class="sxs-lookup"><span data-stu-id="76300-112">Make the code less error prone, and easier to test and maintain.</span></span>

<span data-ttu-id="76300-113">Il supporto della convalida fornito dalle pagine di Razor e da Entity Framework è un buon esempio del principio di DRY.</span><span class="sxs-lookup"><span data-stu-id="76300-113">The validation support provided by Razor Pages and Entity Framework is a good example of the DRY principle.</span></span> <span data-ttu-id="76300-114">Le regole di convalida vengono specificate in modo dichiarativo in un'unica posizione (nella classe del modello) e le regole vengono applicate ovunque nell'app.</span><span class="sxs-lookup"><span data-stu-id="76300-114">Validation rules are declaratively specified in one place (in the model class), and the rules are enforced everywhere in the app.</span></span>

## <a name="add-validation-rules-to-the-movie-model"></a><span data-ttu-id="76300-115">Aggiungere regole di convalida al modello movie</span><span class="sxs-lookup"><span data-stu-id="76300-115">Add validation rules to the movie model</span></span>

<span data-ttu-id="76300-116">Lo spazio dei nomi DataAnnotations offre un set di attributi di convalida predefiniti che vengono applicati in modo dichiarativo a una classe o una proprietà.</span><span class="sxs-lookup"><span data-stu-id="76300-116">The DataAnnotations namespace provides a set of built-in validation attributes that are applied declaratively to a class or property.</span></span> <span data-ttu-id="76300-117">DataAnnotations contiene anche gli attributi di formattazione come `DataType` che guidano nella formattazione e non offrono alcuna convalida.</span><span class="sxs-lookup"><span data-stu-id="76300-117">DataAnnotations also contains formatting attributes like `DataType` that help with formatting and don't provide any validation.</span></span>

<span data-ttu-id="76300-118">Aggiornare la classe `Movie` per poter sfruttare gli attributi di convalida `Required`, `StringLength`, `RegularExpression` e `Range` predefiniti.</span><span class="sxs-lookup"><span data-stu-id="76300-118">Update the `Movie` class to take advantage of the built-in `Required`, `StringLength`, `RegularExpression`, and `Range` validation attributes.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

<span data-ttu-id="76300-119">Gli attributi di convalida specificano il comportamento da implementare nelle proprietà del modello a cui vengono applicati:</span><span class="sxs-lookup"><span data-stu-id="76300-119">The validation attributes specify behavior that you want to enforce on the model properties they're applied to:</span></span>

* <span data-ttu-id="76300-120">Gli attributi `Required` e `MinimumLength` indicano che una proprietà deve avere un valore, ma nulla impedisce all'utente di inserire spazi vuoti per soddisfare questa convalida.</span><span class="sxs-lookup"><span data-stu-id="76300-120">The `Required` and `MinimumLength` attributes indicate that a property must have a value; but nothing prevents a user from entering white space to satisfy this validation.</span></span>
* <span data-ttu-id="76300-121">L'attributo `RegularExpression` viene usato per limitare i caratteri che possono essere inseriti.</span><span class="sxs-lookup"><span data-stu-id="76300-121">The `RegularExpression` attribute is used to limit what characters can be input.</span></span> <span data-ttu-id="76300-122">Nel codice precedente "Genre":</span><span class="sxs-lookup"><span data-stu-id="76300-122">In the preceding code, "Genre":</span></span>

  * <span data-ttu-id="76300-123">Deve includere solo lettere.</span><span class="sxs-lookup"><span data-stu-id="76300-123">Must only use letters.</span></span>
  * <span data-ttu-id="76300-124">La prima lettera deve essere maiuscola.</span><span class="sxs-lookup"><span data-stu-id="76300-124">The first letter is required to be uppercase.</span></span> <span data-ttu-id="76300-125">Gli spazi, i numeri e i caratteri speciali non sono consentiti.</span><span class="sxs-lookup"><span data-stu-id="76300-125">White space, numbers, and special characters are not allowed.</span></span>

* <span data-ttu-id="76300-126">`RegularExpression` "Rating":</span><span class="sxs-lookup"><span data-stu-id="76300-126">The `RegularExpression` "Rating":</span></span>

  * <span data-ttu-id="76300-127">Richiede che il primo carattere sia una lettera maiuscola.</span><span class="sxs-lookup"><span data-stu-id="76300-127">Requires that the first character be an uppercase letter.</span></span>
  * <span data-ttu-id="76300-128">Consente i caratteri speciali e i numeri negli spazi successivi.</span><span class="sxs-lookup"><span data-stu-id="76300-128">Allows special characters and numbers in  subsequent spaces.</span></span> <span data-ttu-id="76300-129">"PG-13" è valido per una classificazione, ma non per "Genre".</span><span class="sxs-lookup"><span data-stu-id="76300-129">"PG-13" is valid for a rating, but fails for a "Genre".</span></span>

* <span data-ttu-id="76300-130">L'attributo `Range` vincola un valore all'interno di un intervallo specificato.</span><span class="sxs-lookup"><span data-stu-id="76300-130">The `Range` attribute constrains a value to within a specified range.</span></span>
* <span data-ttu-id="76300-131">L'attributo `StringLength` consente di impostare la lunghezza massima di una proprietà stringa e, facoltativamente, la lunghezza minima.</span><span class="sxs-lookup"><span data-stu-id="76300-131">The `StringLength` attribute lets you set the maximum length of a string property, and optionally its minimum length.</span></span>
* <span data-ttu-id="76300-132">I tipi di valore, ad esempio `decimal`, `int`, `float` e `DateTime`, sono intrinsecamente necessari e non richiedono l'attributo `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="76300-132">Value types (such as `decimal`, `int`, `float`, `DateTime`) are inherently required and don't need the `[Required]` attribute.</span></span>

<span data-ttu-id="76300-133">L'applicazione automatica di regole di convalida da ASP.NET Core è utile per rendere un'app più solida.</span><span class="sxs-lookup"><span data-stu-id="76300-133">Having validation rules automatically enforced by ASP.NET Core helps make your app more robust.</span></span> <span data-ttu-id="76300-134">In questo modo inoltre non è possibile omettere la convalida di un elemento e quindi inserire involontariamente dati errati nel database.</span><span class="sxs-lookup"><span data-stu-id="76300-134">It also ensures that you can't forget to validate something and inadvertently let bad data into the database.</span></span>

### <a name="validation-error-ui-in-razor-pages"></a><span data-ttu-id="76300-135">Errore di convalida dell'interfaccia utente nelle pagine Razor</span><span class="sxs-lookup"><span data-stu-id="76300-135">Validation Error UI in Razor Pages</span></span>

<span data-ttu-id="76300-136">Eseguire l'app e passare a Pages/Movies.</span><span class="sxs-lookup"><span data-stu-id="76300-136">Run the app and navigate to Pages/Movies.</span></span>

<span data-ttu-id="76300-137">Selezionare il collegamento **Crea nuovo**.</span><span class="sxs-lookup"><span data-stu-id="76300-137">Select the **Create New** link.</span></span> <span data-ttu-id="76300-138">Completare il modulo con alcuni valori non validi.</span><span class="sxs-lookup"><span data-stu-id="76300-138">Complete the form with some invalid values.</span></span> <span data-ttu-id="76300-139">Quando la convalida del lato client jQuery rileva l'errore, viene visualizzato un messaggio di errore.</span><span class="sxs-lookup"><span data-stu-id="76300-139">When jQuery client-side validation detects the error, it displays an error message.</span></span>

![Il modulo di vista del film con più errori di convalida del lato client jQuery](validation/_static/val.png)

[!INCLUDE[](~/includes/localization/currency.md)]

<span data-ttu-id="76300-141">Si noti come il modulo ha eseguito automaticamente il rendering di un messaggio di errore di convalida in ogni campo che contiene un valore non valido.</span><span class="sxs-lookup"><span data-stu-id="76300-141">Notice how the form has automatically rendered a validation error message in each field containing an invalid value.</span></span> <span data-ttu-id="76300-142">Gli errori vengono applicati sia sul lato client (uso di JavaScript e jQuery) sia sul lato server (quando un utente ha JavaScript disabilitato).</span><span class="sxs-lookup"><span data-stu-id="76300-142">The errors are enforced both client-side (using JavaScript and jQuery) and server-side (when a user has JavaScript disabled).</span></span>

<span data-ttu-id="76300-143">Un vantaggio significativo è che non c'era **nessuna** modifica del codice necessaria nelle pagine Create o Edit.</span><span class="sxs-lookup"><span data-stu-id="76300-143">A significant benefit is that **no** code changes were necessary in the Create  or Edit pages.</span></span> <span data-ttu-id="76300-144">Una volta che le DataAnnotations sono state applicate al modello, è stata abilitata la convalida dell'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="76300-144">Once DataAnnotations were applied to the model, the validation UI was enabled.</span></span> <span data-ttu-id="76300-145">Le pagine Razor create in questa esercitazione hanno selezionato automaticamente le regole di convalida (usando gli attributi di convalida delle proprietà della classe `Movie` del modello).</span><span class="sxs-lookup"><span data-stu-id="76300-145">The Razor Pages created in this tutorial automatically picked up the validation rules (using validation attributes on the properties of the `Movie` model class).</span></span> <span data-ttu-id="76300-146">Convalida del test tramite la pagina Modifica, viene applicata la stessa convalida.</span><span class="sxs-lookup"><span data-stu-id="76300-146">Test validation using the Edit page, the same validation is applied.</span></span>

<span data-ttu-id="76300-147">I dati del modulo non vengono registrati nel server fino a quando non sono presenti errori di convalida nel lato client.</span><span class="sxs-lookup"><span data-stu-id="76300-147">The form data isn't posted to the server until there are no client-side validation errors.</span></span> <span data-ttu-id="76300-148">Verificare che i dati del modulo non siano stati registrati da uno o più degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="76300-148">Verify form data isn't posted by one or more of the following approaches:</span></span>

* <span data-ttu-id="76300-149">Inserire un punto di interruzione nel metodo `OnPostAsync`.</span><span class="sxs-lookup"><span data-stu-id="76300-149">Put a break point in the `OnPostAsync` method.</span></span> <span data-ttu-id="76300-150">Inviare il modulo (selezionare **Crea** o **Salva**).</span><span class="sxs-lookup"><span data-stu-id="76300-150">Submit the form (select **Create** or **Save**).</span></span> <span data-ttu-id="76300-151">Il punto di interruzione non viene mai raggiunto.</span><span class="sxs-lookup"><span data-stu-id="76300-151">The break point is never hit.</span></span>
* <span data-ttu-id="76300-152">Usare lo [Strumento Fiddler](https://www.telerik.com/fiddler).</span><span class="sxs-lookup"><span data-stu-id="76300-152">Use the [Fiddler tool](https://www.telerik.com/fiddler).</span></span>
* <span data-ttu-id="76300-153">Usare gli strumenti di sviluppo del browser per monitorare il traffico di rete.</span><span class="sxs-lookup"><span data-stu-id="76300-153">Use the browser developer tools to monitor network traffic.</span></span>

### <a name="server-side-validation"></a><span data-ttu-id="76300-154">Convalida sul lato server</span><span class="sxs-lookup"><span data-stu-id="76300-154">Server-side validation</span></span>

<span data-ttu-id="76300-155">Quando JavaScript è disabilitato nel browser, l'invio del modulo con errori verrà inoltrato al server.</span><span class="sxs-lookup"><span data-stu-id="76300-155">When JavaScript is disabled in the browser, submitting the form with errors will post to the server.</span></span>

<span data-ttu-id="76300-156">Facoltativo, convalida sul lato server del test:</span><span class="sxs-lookup"><span data-stu-id="76300-156">Optional, test server-side validation:</span></span>

* <span data-ttu-id="76300-157">Disabilitare JavaScript nel browser.</span><span class="sxs-lookup"><span data-stu-id="76300-157">Disable JavaScript in the browser.</span></span> <span data-ttu-id="76300-158">È possibile disabilitare JavaScript usando gli strumenti di sviluppo del browser.</span><span class="sxs-lookup"><span data-stu-id="76300-158">You can disable JavaScript using browser's developer tools.</span></span> <span data-ttu-id="76300-159">Se non è possibile disabilitare JavaScript nel browser, provare un altro browser.</span><span class="sxs-lookup"><span data-stu-id="76300-159">If you can't disable JavaScript in the browser, try another browser.</span></span>
* <span data-ttu-id="76300-160">Impostare un punto di interruzione nel metodo `OnPostAsync` della pagina Crea o Modifica.</span><span class="sxs-lookup"><span data-stu-id="76300-160">Set a break point in the `OnPostAsync` method of the Create or Edit page.</span></span>
* <span data-ttu-id="76300-161">Inviare un modulo con dati non validi.</span><span class="sxs-lookup"><span data-stu-id="76300-161">Submit a form with invalid data.</span></span>
* <span data-ttu-id="76300-162">Verificare che lo stato del modello non sia valido:</span><span class="sxs-lookup"><span data-stu-id="76300-162">Verify the model state is invalid:</span></span>

  ```csharp
   if (!ModelState.IsValid)
   {
      return Page();
   }
  ```
  
<span data-ttu-id="76300-163">In alternativa, è possibile [disabilitare la convalida lato client sul server](xref:mvc/models/validation#disable-client-side-validation).</span><span class="sxs-lookup"><span data-stu-id="76300-163">Alternatively, you can [Disable client-side validation on the server](xref:mvc/models/validation#disable-client-side-validation).</span></span>

<span data-ttu-id="76300-164">Il codice seguente mostra una parte della pagina *Create.cshtml* di cui è stato eseguito lo scaffolding in precedenza nell'esercitazione.</span><span class="sxs-lookup"><span data-stu-id="76300-164">The following code shows a portion of the *Create.cshtml* page scaffolded earlier in the tutorial.</span></span> <span data-ttu-id="76300-165">Viene usato dalle pagine Create e Edit per visualizzare il modulo iniziale e per visualizzare nuovamente il modulo in caso di errore.</span><span class="sxs-lookup"><span data-stu-id="76300-165">It's used by the Create and Edit pages to display the initial form and to redisplay the form in the event of an error.</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=14-20)]

<span data-ttu-id="76300-166">L'[helper tag di input](xref:mvc/views/working-with-forms) usa gli attributi [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) e produce gli attributi HTML necessari per la convalida jQuery sul lato client.</span><span class="sxs-lookup"><span data-stu-id="76300-166">The [Input Tag Helper](xref:mvc/views/working-with-forms) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span> <span data-ttu-id="76300-167">L'[helper tag di convalida](xref:mvc/views/working-with-forms#the-validation-tag-helpers) visualizza gli errori di convalida.</span><span class="sxs-lookup"><span data-stu-id="76300-167">The [Validation Tag Helper](xref:mvc/views/working-with-forms#the-validation-tag-helpers) displays validation errors.</span></span> <span data-ttu-id="76300-168">Per altre informazioni, vedere [Convalida](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="76300-168">See [Validation](xref:mvc/models/validation) for more information.</span></span>

<span data-ttu-id="76300-169">Le pagine Create e Edit non dispongono di nessuna regola di convalida.</span><span class="sxs-lookup"><span data-stu-id="76300-169">The Create and Edit pages have no validation rules in them.</span></span> <span data-ttu-id="76300-170">Le regole di convalida e le stringhe di errore vengono specificate solo nella classe `Movie`.</span><span class="sxs-lookup"><span data-stu-id="76300-170">The validation rules and the error strings are specified only in the `Movie` class.</span></span> <span data-ttu-id="76300-171">Queste regole di convalida vengono applicate automaticamente alle pagine Razor che modificano il modello `Movie`.</span><span class="sxs-lookup"><span data-stu-id="76300-171">These validation rules are automatically applied to Razor Pages that edit the `Movie` model.</span></span>

<span data-ttu-id="76300-172">Quando la logica di convalida deve cambiare, avviene solo nel modello.</span><span class="sxs-lookup"><span data-stu-id="76300-172">When validation logic needs to change, it's done only in the model.</span></span> <span data-ttu-id="76300-173">La convalida viene applicata in modo coerente in tutta l'applicazione (la logica di convalida è definita in un'unica posizione).</span><span class="sxs-lookup"><span data-stu-id="76300-173">Validation is applied consistently throughout the application (validation logic is defined in one place).</span></span> <span data-ttu-id="76300-174">La convalida in un'unica posizione consente di mantenere il codice pulito e rende più semplice la gestione e l'aggiornamento.</span><span class="sxs-lookup"><span data-stu-id="76300-174">Validation in one place helps keep the code clean, and makes it easier to maintain and update.</span></span>

## <a name="using-datatype-attributes"></a><span data-ttu-id="76300-175">Utilizzo degli attributi DataType</span><span class="sxs-lookup"><span data-stu-id="76300-175">Using DataType Attributes</span></span>

<span data-ttu-id="76300-176">Esaminare la classe `Movie`.</span><span class="sxs-lookup"><span data-stu-id="76300-176">Examine the `Movie` class.</span></span> <span data-ttu-id="76300-177">Lo spazio dei nomi `System.ComponentModel.DataAnnotations` fornisce gli attributi di formattazione oltre al set predefinito di attributi di convalida.</span><span class="sxs-lookup"><span data-stu-id="76300-177">The `System.ComponentModel.DataAnnotations` namespace provides formatting attributes in addition to the built-in set of validation attributes.</span></span> <span data-ttu-id="76300-178">L'attributo `DataType` viene applicato alle proprietà `ReleaseDate` e `Price`.</span><span class="sxs-lookup"><span data-stu-id="76300-178">The `DataType` attribute is applied to the `ReleaseDate` and `Price` properties.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie/Models/MovieDateRatingDA.cs?highlight=2,6&name=snippet2)]

<span data-ttu-id="76300-179">Gli attributi `DataType` forniscono solo gli hint per far sì che il motore di vista formatti i dati (e fornisca gli attributi, ad esempio `<a>` per gli URL e `<a href="mailto:EmailAddress.com">` per la posta elettronica).</span><span class="sxs-lookup"><span data-stu-id="76300-179">The `DataType` attributes only provide hints for the view engine to format the data (and supplies attributes such as `<a>` for URL's and `<a href="mailto:EmailAddress.com">` for email).</span></span> <span data-ttu-id="76300-180">Usare l'attributo `RegularExpression` per convalidare il formato dei dati.</span><span class="sxs-lookup"><span data-stu-id="76300-180">Use the `RegularExpression` attribute to validate the format of the data.</span></span> <span data-ttu-id="76300-181">L'attributo `DataType` viene usato per specificare un tipo di dati che è più specifico del tipo intrinseco del database.</span><span class="sxs-lookup"><span data-stu-id="76300-181">The `DataType` attribute is used to specify a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="76300-182">Gli attributi `DataType` non sono gli attributi di convalida.</span><span class="sxs-lookup"><span data-stu-id="76300-182">`DataType` attributes are not validation attributes.</span></span> <span data-ttu-id="76300-183">Nell'applicazione di esempio, viene visualizzata solo la data, senza l'ora.</span><span class="sxs-lookup"><span data-stu-id="76300-183">In the sample application, only the date is displayed, without time.</span></span>

<span data-ttu-id="76300-184">L'enumerazione `DataType` fornisce per molti tipi di dati, ad esempio Date, Time, PhoneNumber, Currency, EmailAddress e altro ancora.</span><span class="sxs-lookup"><span data-stu-id="76300-184">The `DataType` Enumeration provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, and more.</span></span> <span data-ttu-id="76300-185">L'attributo `DataType` può anche consentire all'applicazione di fornire automaticamente le funzionalità specifiche del tipo.</span><span class="sxs-lookup"><span data-stu-id="76300-185">The `DataType` attribute can also enable the application to automatically provide type-specific features.</span></span> <span data-ttu-id="76300-186">Ad esempio, è possibile creare un collegamento `mailto:` per `DataType.EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="76300-186">For example, a `mailto:` link can be created for `DataType.EmailAddress`.</span></span> <span data-ttu-id="76300-187">È possibile fornire un selettore di dati per `DataType.Date` nei browser che supportano HTML5.</span><span class="sxs-lookup"><span data-stu-id="76300-187">A date selector can be provided for `DataType.Date` in browsers that support HTML5.</span></span> <span data-ttu-id="76300-188">Gli attributi `DataType` generano attributi HTML5 `data-` (pronunciati *data dash*) che poi i browser HTML5 consumano.</span><span class="sxs-lookup"><span data-stu-id="76300-188">The `DataType` attributes emits HTML 5 `data-` (pronounced data dash) attributes that HTML 5 browsers consume.</span></span> <span data-ttu-id="76300-189">Gli attributi `DataType`**non** forniscono alcuna convalida.</span><span class="sxs-lookup"><span data-stu-id="76300-189">The `DataType` attributes do **not** provide any validation.</span></span>

<span data-ttu-id="76300-190">`DataType.Date` non specifica il formato della data visualizzata.</span><span class="sxs-lookup"><span data-stu-id="76300-190">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="76300-191">Per impostazione predefinita, il campo dei dati viene visualizzato in base ai formati predefiniti per il valore `CultureInfo` del server.</span><span class="sxs-lookup"><span data-stu-id="76300-191">By default, the data field is displayed according to the default formats based on the server's `CultureInfo`.</span></span>

<span data-ttu-id="76300-192">L'annotazione dei dati `[Column(TypeName = "decimal(18, 2)")]` è necessaria per consentire a Entity Framework Core di eseguire correttamente il mapping di `Price` nella valuta del database.</span><span class="sxs-lookup"><span data-stu-id="76300-192">The `[Column(TypeName = "decimal(18, 2)")]` data annotation is required so Entity Framework Core can correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="76300-193">Per altre informazioni, vedere [Tipi di dati](/ef/core/modeling/relational/data-types).</span><span class="sxs-lookup"><span data-stu-id="76300-193">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>

<span data-ttu-id="76300-194">L'attributo `DisplayFormat` viene usato per specificare in modo esplicito il formato della data:</span><span class="sxs-lookup"><span data-stu-id="76300-194">The `DisplayFormat` attribute is used to explicitly specify the date format:</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
public DateTime ReleaseDate { get; set; }
```

<span data-ttu-id="76300-195">L'impostazione `ApplyFormatInEditMode` specifica che deve essere applicata la formattazione quando il valore viene visualizzato per la modifica.</span><span class="sxs-lookup"><span data-stu-id="76300-195">The `ApplyFormatInEditMode` setting specifies that the formatting should be applied when the value is displayed for editing.</span></span> <span data-ttu-id="76300-196">Questo comportamento potrebbe non essere desiderato per alcuni campi.</span><span class="sxs-lookup"><span data-stu-id="76300-196">You might not want that behavior for some fields.</span></span> <span data-ttu-id="76300-197">Ad esempio, in valori di valuta, è probabile che non si desideri il simbolo di valuta in modalità di modifica dell'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="76300-197">For example, in currency values, you probably don't want the currency symbol in the edit UI.</span></span>

<span data-ttu-id="76300-198">L'attributo `DisplayFormat` può essere usato da solo, ma in genere è consigliabile usare l'attributo `DataType`.</span><span class="sxs-lookup"><span data-stu-id="76300-198">The `DisplayFormat` attribute can be used by itself, but it's generally a good idea to use the `DataType` attribute.</span></span> <span data-ttu-id="76300-199">L'attributo `DataType` fornisce la semantica dei dati anziché la modalità di esecuzione del rendering in una schermata e fornisce i seguenti vantaggi che non si ottengono con DisplayFormat:</span><span class="sxs-lookup"><span data-stu-id="76300-199">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen, and provides the following benefits that you don't get with DisplayFormat:</span></span>

* <span data-ttu-id="76300-200">Il browser può abilitare le funzionalità HTML5, ad esempio visualizzare un controllo di calendario, il simbolo della valuta appropriato per le impostazioni locali, i collegamenti alla posta elettronica e così via.</span><span class="sxs-lookup"><span data-stu-id="76300-200">The browser can enable HTML5 features (for example to show a calendar control, the locale-appropriate currency symbol, email links, etc.)</span></span>
* <span data-ttu-id="76300-201">Per impostazione predefinita, il browser eseguirà il rendering dei dati usando il formato corretto in base alle impostazioni locali del sistema.</span><span class="sxs-lookup"><span data-stu-id="76300-201">By default, the browser will render data using the correct format based on your locale.</span></span>
* <span data-ttu-id="76300-202">L'attributo `DataType` può abilitare il framework di ASP.NET Core a scegliere il modello di campo corretto per il rendering dei dati.</span><span class="sxs-lookup"><span data-stu-id="76300-202">The `DataType` attribute can enable the ASP.NET Core framework to choose the right field template to render the data.</span></span> <span data-ttu-id="76300-203">Il `DisplayFormat` se usato da solo usa il modello di stringa.</span><span class="sxs-lookup"><span data-stu-id="76300-203">The `DisplayFormat` if used by itself uses the string template.</span></span>

<span data-ttu-id="76300-204">Nota: la convalida jQuery non funziona con l'attributo `Range` e con `DateTime`.</span><span class="sxs-lookup"><span data-stu-id="76300-204">Note: jQuery validation doesn't work with the `Range` attribute and `DateTime`.</span></span> <span data-ttu-id="76300-205">Ad esempio, il codice seguente visualizzerà sempre un errore di convalida sul lato client, anche quando la data è compreso nell'intervallo specificato:</span><span class="sxs-lookup"><span data-stu-id="76300-205">For example, the following code will always display a client-side validation error, even when the date is in the specified range:</span></span>

```csharp
[Range(typeof(DateTime), "1/1/1966", "1/1/2020")]
   ```

<span data-ttu-id="76300-206">In genere non è consigliabile compilare date reali nei modelli, quindi l'uso dell'attributo `Range` e di `DateTime` è sconsigliato.</span><span class="sxs-lookup"><span data-stu-id="76300-206">It's generally not a good practice to compile hard dates in your models, so using the `Range` attribute and `DateTime` is discouraged.</span></span>

<span data-ttu-id="76300-207">Il codice seguente illustra la combinazione di attributi in una sola riga:</span><span class="sxs-lookup"><span data-stu-id="76300-207">The following code shows combining attributes on one line:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDAmult.cs?name=snippet1)]

<span data-ttu-id="76300-208">[Introduzione a Razor Pages ed Entity Framework Core](xref:data/ef-rp/intro) include operazioni avanzate di Entity Framework Core con Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="76300-208">[Get started with Razor Pages and EF Core](xref:data/ef-rp/intro) shows advanced EF Core operations with Razor Pages.</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="76300-209">Applicare le migrazioni</span><span class="sxs-lookup"><span data-stu-id="76300-209">Apply migrations</span></span>

<span data-ttu-id="76300-210">Le annotazioni DataAnnotations applicate alla classe modificano lo schema.</span><span class="sxs-lookup"><span data-stu-id="76300-210">The DataAnnotations applied to the class change the schema.</span></span> <span data-ttu-id="76300-211">Ad esempio, le annotazioni DataAnnotations applicate al campo `Title`:</span><span class="sxs-lookup"><span data-stu-id="76300-211">For example, the DataAnnotations applied to the `Title` field:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet11)]

* <span data-ttu-id="76300-212">Limitano i caratteri a 60.</span><span class="sxs-lookup"><span data-stu-id="76300-212">Limits the characters to 60.</span></span>
* <span data-ttu-id="76300-213">Non consentono un valore `null`.</span><span class="sxs-lookup"><span data-stu-id="76300-213">Doesn't allow a `null` value.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="76300-214">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="76300-214">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="76300-215">La tabella `Movie` ha attualmente lo schema seguente:</span><span class="sxs-lookup"><span data-stu-id="76300-215">The `Movie` table currently has the following schema:</span></span>

```sql
CREATE TABLE [dbo].[Movie] (
    [ID]          INT             IDENTITY (1, 1) NOT NULL,
    [Title]       NVARCHAR (MAX)  NULL,
    [ReleaseDate] DATETIME2 (7)   NOT NULL,
    [Genre]       NVARCHAR (MAX)  NULL,
    [Price]       DECIMAL (18, 2) NOT NULL,
    [Rating]      NVARCHAR (MAX)  NULL,
    CONSTRAINT [PK_Movie] PRIMARY KEY CLUSTERED ([ID] ASC)
);
```

<span data-ttu-id="76300-216">Le modifiche dello schema precedenti non determinano la generazione di un'eccezione da parte di EF.</span><span class="sxs-lookup"><span data-stu-id="76300-216">The preceding schema changes don't cause EF to throw an exception.</span></span> <span data-ttu-id="76300-217">Tuttavia, creare una migrazione in modo che lo schema sia coerente con il modello.</span><span class="sxs-lookup"><span data-stu-id="76300-217">However, create a migration so the schema is consistent with the model.</span></span>

<span data-ttu-id="76300-218">Dal menu **Strumenti** selezionare **Gestione pacchetti NuGet > Console di Gestione pacchetti**.</span><span class="sxs-lookup"><span data-stu-id="76300-218">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
<span data-ttu-id="76300-219">Nella Console di Gestione pacchetti immettere i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="76300-219">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration New_DataAnnotations
Update-Database
```

<span data-ttu-id="76300-220">`Update-Database` esegue i metodi `Up` della classe `New_DataAnnotations`.</span><span class="sxs-lookup"><span data-stu-id="76300-220">`Update-Database` runs the `Up` methods of the `New_DataAnnotations` class.</span></span> <span data-ttu-id="76300-221">Esaminare il metodo `Up`:</span><span class="sxs-lookup"><span data-stu-id="76300-221">Examine the `Up` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Migrations/20190724163003_New_DataAnnotations.cs?name=snippet)]

<span data-ttu-id="76300-222">La tabella `Movie` aggiornata presenta lo schema seguente:</span><span class="sxs-lookup"><span data-stu-id="76300-222">The updated `Movie` table has the following schema:</span></span>

```sql
CREATE TABLE [dbo].[Movie] (
    [ID]          INT             IDENTITY (1, 1) NOT NULL,
    [Title]       NVARCHAR (60)   NOT NULL,
    [ReleaseDate] DATETIME2 (7)   NOT NULL,
    [Genre]       NVARCHAR (30)   NOT NULL,
    [Price]       DECIMAL (18, 2) NOT NULL,
    [Rating]      NVARCHAR (5)    NOT NULL,
    CONSTRAINT [PK_Movie] PRIMARY KEY CLUSTERED ([ID] ASC)
);
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="76300-223">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="76300-223">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="76300-224">Non sono necessarie migrazioni per SQLite.</span><span class="sxs-lookup"><span data-stu-id="76300-224">Migrations are not required for SQLite.</span></span>

---

### <a name="publish-to-azure"></a><span data-ttu-id="76300-225">Pubblicazione in Azure</span><span class="sxs-lookup"><span data-stu-id="76300-225">Publish to Azure</span></span>

<span data-ttu-id="76300-226">Per informazioni sulla distribuzione in Azure, vedere [Esercitazione: Creare un'app ASP.NET Core in Azure con il database SQL.](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)</span><span class="sxs-lookup"><span data-stu-id="76300-226">For information on deploying to Azure, see [Tutorial: Build an ASP.NET Core app in Azure with SQL Database](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb).</span></span>

<span data-ttu-id="76300-227">Grazie aver completato questa introduzione alle pagine Razor.</span><span class="sxs-lookup"><span data-stu-id="76300-227">Thanks for completing this introduction to Razor Pages.</span></span> <span data-ttu-id="76300-228">[Get started with Razor Pages and EF Core](xref:data/ef-rp/intro) (Introduzione a Razor Pages ed Entity Framework Core) è un complemento ideale per questa esercitazione.</span><span class="sxs-lookup"><span data-stu-id="76300-228">[Get started with Razor Pages and EF Core](xref:data/ef-rp/intro) is an excellent follow up to this tutorial.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="76300-229">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="76300-229">Additional resources</span></span>

* <xref:mvc/views/working-with-forms>
* <xref:fundamentals/localization>
* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/views/tag-helpers/authoring>
* [<span data-ttu-id="76300-230">Versione YouTube dell'esercitazione</span><span class="sxs-lookup"><span data-stu-id="76300-230">YouTube version of this tutorial</span></span>](https://youtu.be/b63m66eu7us)

> [!div class="step-by-step"]
> [<span data-ttu-id="76300-231">Precedente: Aggiunta di un nuovo campo</span><span class="sxs-lookup"><span data-stu-id="76300-231">Previous: Adding a new field</span></span>](xref:tutorials/razor-pages/new-field)
