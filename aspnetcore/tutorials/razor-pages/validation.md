---
title: Parte 8, aggiungere la convalida
author: rick-anderson
description: Parte 8 della serie di esercitazioni sulle Razor pagine.
ms.author: riande
ms.custom: mvc, contperf-fy21q2
ms.date: 09/29/2020
no-loc:
- Index
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
uid: tutorials/razor-pages/validation
ms.openlocfilehash: 9774607b641005145bdb1c98d850c9ce79a25476
ms.sourcegitcommit: 6299f08aed5b7f0496001d093aae617559d73240
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/14/2020
ms.locfileid: "97486122"
---
# <a name="part-8-of-tutorial-series-on-no-locrazor-pages"></a><span data-ttu-id="c0a91-103">Parte 8 della serie di esercitazioni sulle Razor pagine.</span><span class="sxs-lookup"><span data-stu-id="c0a91-103">Part 8 of tutorial series on Razor Pages.</span></span>

<span data-ttu-id="c0a91-104">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="c0a91-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="c0a91-105">In questa sezione la logica di convalida viene aggiunta al modello `Movie`.</span><span class="sxs-lookup"><span data-stu-id="c0a91-105">In this section, validation logic is added to the `Movie` model.</span></span> <span data-ttu-id="c0a91-106">Le regole di convalida vengono applicate ogni volta che un utente crea o modifica un film.</span><span class="sxs-lookup"><span data-stu-id="c0a91-106">The validation rules are enforced any time a user creates or edits a movie.</span></span>

## <a name="validation"></a><span data-ttu-id="c0a91-107">Convalida</span><span class="sxs-lookup"><span data-stu-id="c0a91-107">Validation</span></span>

<span data-ttu-id="c0a91-108">Un concetto di base dello sviluppo del software si chiama [DRY](https://wikipedia.org/wiki/Don%27t_repeat_yourself) ("**D** on't **R** epeat **Y** ourself", Non ripeterti).</span><span class="sxs-lookup"><span data-stu-id="c0a91-108">A key tenet of software development is called [DRY](https://wikipedia.org/wiki/Don%27t_repeat_yourself) ("**D** on't **R** epeat **Y** ourself").</span></span> <span data-ttu-id="c0a91-109">Razor Le pagine favoriscono lo sviluppo in cui la funzionalità è specificata una sola volta e viene riflessa nell'intera app.</span><span class="sxs-lookup"><span data-stu-id="c0a91-109">Razor Pages encourages development where functionality is specified once, and it's reflected throughout the app.</span></span> <span data-ttu-id="c0a91-110">DRY contribuisce a:</span><span class="sxs-lookup"><span data-stu-id="c0a91-110">DRY can help:</span></span>

* <span data-ttu-id="c0a91-111">Ridurre la quantità di codice in un'app.</span><span class="sxs-lookup"><span data-stu-id="c0a91-111">Reduce the amount of code in an app.</span></span>
* <span data-ttu-id="c0a91-112">Rendere il codice meno soggetto ad errori e più facile da testare e gestire.</span><span class="sxs-lookup"><span data-stu-id="c0a91-112">Make the code less error prone, and easier to test and maintain.</span></span>

<span data-ttu-id="c0a91-113">Il supporto della convalida fornito da Razor pagine e Entity Framework è un valido esempio di principio secco:</span><span class="sxs-lookup"><span data-stu-id="c0a91-113">The validation support provided by Razor Pages and Entity Framework is a good example of the DRY principle:</span></span>

* <span data-ttu-id="c0a91-114">Le regole di convalida vengono specificate in modo dichiarativo in un'unica posizione nella classe del modello.</span><span class="sxs-lookup"><span data-stu-id="c0a91-114">Validation rules are declaratively specified in one place, in the model class.</span></span>
* <span data-ttu-id="c0a91-115">Le regole vengono applicate ovunque nell'app.</span><span class="sxs-lookup"><span data-stu-id="c0a91-115">Rules are enforced everywhere in the app.</span></span>

## <a name="add-validation-rules-to-the-movie-model"></a><span data-ttu-id="c0a91-116">Aggiungere regole di convalida al modello movie</span><span class="sxs-lookup"><span data-stu-id="c0a91-116">Add validation rules to the movie model</span></span>

<span data-ttu-id="c0a91-117">Lo `DataAnnotations` spazio dei nomi fornisce:</span><span class="sxs-lookup"><span data-stu-id="c0a91-117">The `DataAnnotations` namespace provides:</span></span>

* <span data-ttu-id="c0a91-118">Set di attributi di convalida predefiniti che vengono applicati in modo dichiarativo a una classe o a una proprietà.</span><span class="sxs-lookup"><span data-stu-id="c0a91-118">A set of built-in validation attributes that are applied declaratively to a class or property.</span></span>
* <span data-ttu-id="c0a91-119">Formattazione di attributi come `[DataType]` questa guida alla formattazione e non forniscono alcuna convalida.</span><span class="sxs-lookup"><span data-stu-id="c0a91-119">Formatting attributes like `[DataType]` that help with formatting and don't provide any validation.</span></span>

<span data-ttu-id="c0a91-120">Aggiornare la classe `Movie` per poter sfruttare gli attributi di convalida `[Required]`, `[StringLength]`, `[RegularExpression]` e `[Range]` predefiniti.</span><span class="sxs-lookup"><span data-stu-id="c0a91-120">Update the `Movie` class to take advantage of the built-in `[Required]`, `[StringLength]`, `[RegularExpression]`, and `[Range]` validation attributes.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

<span data-ttu-id="c0a91-121">Gli attributi di convalida specificano il comportamento da applicare alle proprietà del modello a cui sono applicati:</span><span class="sxs-lookup"><span data-stu-id="c0a91-121">The validation attributes specify behavior to enforce on the model properties they're applied to:</span></span>

* <span data-ttu-id="c0a91-122">Gli attributi `[Required]` e `[MinimumLength]` indicano che una proprietà deve avere un valore.</span><span class="sxs-lookup"><span data-stu-id="c0a91-122">The `[Required]` and `[MinimumLength]` attributes indicate that a property must have a value.</span></span> <span data-ttu-id="c0a91-123">Niente impedisce a un utente di immettere spazi vuoti per soddisfare la convalida.</span><span class="sxs-lookup"><span data-stu-id="c0a91-123">Nothing prevents a user from entering white space to satisfy this validation.</span></span>
* <span data-ttu-id="c0a91-124">L'attributo `[RegularExpression]` viene usato per limitare i caratteri che possono essere inseriti.</span><span class="sxs-lookup"><span data-stu-id="c0a91-124">The `[RegularExpression]` attribute is used to limit what characters can be input.</span></span> <span data-ttu-id="c0a91-125">Nel codice precedente, `Genre` :</span><span class="sxs-lookup"><span data-stu-id="c0a91-125">In the preceding code, `Genre`:</span></span>

  * <span data-ttu-id="c0a91-126">Deve includere solo lettere.</span><span class="sxs-lookup"><span data-stu-id="c0a91-126">Must only use letters.</span></span>
  * <span data-ttu-id="c0a91-127">La prima lettera deve essere maiuscola.</span><span class="sxs-lookup"><span data-stu-id="c0a91-127">The first letter is required to be uppercase.</span></span> <span data-ttu-id="c0a91-128">Gli spazi, i numeri e i caratteri speciali non sono consentiti.</span><span class="sxs-lookup"><span data-stu-id="c0a91-128">White space, numbers, and special characters are not allowed.</span></span>

* <span data-ttu-id="c0a91-129">`RegularExpression` `Rating` :</span><span class="sxs-lookup"><span data-stu-id="c0a91-129">The `RegularExpression` `Rating`:</span></span>

  * <span data-ttu-id="c0a91-130">Richiede che il primo carattere sia una lettera maiuscola.</span><span class="sxs-lookup"><span data-stu-id="c0a91-130">Requires that the first character be an uppercase letter.</span></span>
  * <span data-ttu-id="c0a91-131">Consente i caratteri speciali e i numeri negli spazi successivi.</span><span class="sxs-lookup"><span data-stu-id="c0a91-131">Allows special characters and numbers in subsequent spaces.</span></span> <span data-ttu-id="c0a91-132">"PG-13" è valido per una classificazione, ma ha esito negativo per un `Genre` .</span><span class="sxs-lookup"><span data-stu-id="c0a91-132">"PG-13" is valid for a rating, but fails for a `Genre`.</span></span>

* <span data-ttu-id="c0a91-133">L'attributo `[Range]` vincola un valore all'interno di un intervallo specificato.</span><span class="sxs-lookup"><span data-stu-id="c0a91-133">The `[Range]` attribute constrains a value to within a specified range.</span></span>
* <span data-ttu-id="c0a91-134">L' `[StringLength]` attributo può impostare una lunghezza massima di una proprietà di stringa e, facoltativamente, la lunghezza minima.</span><span class="sxs-lookup"><span data-stu-id="c0a91-134">The `[StringLength]` attribute can set a maximum length of a string property, and optionally its minimum length.</span></span>
* <span data-ttu-id="c0a91-135">I tipi di valore, ad esempio `decimal` , `int` ,, `float` `DateTime` , sono intrinsecamente necessari e non richiedono l' `[Required]` attributo.</span><span class="sxs-lookup"><span data-stu-id="c0a91-135">Value types, such as `decimal`, `int`, `float`, `DateTime`, are inherently required and don't need the `[Required]` attribute.</span></span>

<span data-ttu-id="c0a91-136">Le regole di convalida precedenti vengono usate per la dimostrazione, non sono ottimali per un sistema di produzione.</span><span class="sxs-lookup"><span data-stu-id="c0a91-136">The preceding validation rules are used for demonstration, they are not optimal for a production system.</span></span> <span data-ttu-id="c0a91-137">Il precedente, ad esempio, impedisce l'immissione di un film con solo due caratteri e non consente caratteri speciali in `Genre` .</span><span class="sxs-lookup"><span data-stu-id="c0a91-137">For example, the preceding prevents entering a movie with only two chars and doesn't allow special characters in `Genre`.</span></span>

<span data-ttu-id="c0a91-138">Le regole di convalida applicate automaticamente da ASP.NET Core consentono di:</span><span class="sxs-lookup"><span data-stu-id="c0a91-138">Having validation rules automatically enforced by ASP.NET Core helps:</span></span>

* <span data-ttu-id="c0a91-139">Consente di rendere l'app più affidabile.</span><span class="sxs-lookup"><span data-stu-id="c0a91-139">Helps make the app more robust.</span></span>
* <span data-ttu-id="c0a91-140">Ridurre le possibilità di salvare dati non validi nel database.</span><span class="sxs-lookup"><span data-stu-id="c0a91-140">Reduce chances of saving invalid data to the database.</span></span>

### <a name="validation-error-ui-in-no-locrazor-pages"></a><span data-ttu-id="c0a91-141">Interfaccia utente degli errori di convalida nelle Razor pagine</span><span class="sxs-lookup"><span data-stu-id="c0a91-141">Validation Error UI in Razor Pages</span></span>

<span data-ttu-id="c0a91-142">Eseguire l'app e passare a Pages/Movies.</span><span class="sxs-lookup"><span data-stu-id="c0a91-142">Run the app and navigate to Pages/Movies.</span></span>

<span data-ttu-id="c0a91-143">Selezionare il collegamento **Crea nuovo**.</span><span class="sxs-lookup"><span data-stu-id="c0a91-143">Select the **Create New** link.</span></span> <span data-ttu-id="c0a91-144">Completare il modulo con alcuni valori non validi.</span><span class="sxs-lookup"><span data-stu-id="c0a91-144">Complete the form with some invalid values.</span></span> <span data-ttu-id="c0a91-145">Quando la convalida del lato client jQuery rileva l'errore, viene visualizzato un messaggio di errore.</span><span class="sxs-lookup"><span data-stu-id="c0a91-145">When jQuery client-side validation detects the error, it displays an error message.</span></span>

![Il modulo di vista del film con più errori di convalida del lato client jQuery](validation/_static/val.png)

[!INCLUDE[](~/includes/localization/currency.md)]

<span data-ttu-id="c0a91-147">Si noti come il modulo ha eseguito automaticamente il rendering di un messaggio di errore di convalida in ogni campo che contiene un valore non valido.</span><span class="sxs-lookup"><span data-stu-id="c0a91-147">Notice how the form has automatically rendered a validation error message in each field containing an invalid value.</span></span> <span data-ttu-id="c0a91-148">Gli errori vengono applicati sia sul lato client, usando JavaScript che jQuery e sul lato server, quando un utente ha JavaScript disabilitato.</span><span class="sxs-lookup"><span data-stu-id="c0a91-148">The errors are enforced both client-side, using JavaScript and jQuery, and server-side, when a user has JavaScript disabled.</span></span>

<span data-ttu-id="c0a91-149">Un vantaggio significativo è dovuto al fatto che **non** sono state apportate modifiche al codice nelle pagine di creazione o modifica.</span><span class="sxs-lookup"><span data-stu-id="c0a91-149">A significant benefit is that **no** code changes were necessary in the Create or Edit pages.</span></span> <span data-ttu-id="c0a91-150">Una volta applicate le annotazioni dei dati al modello, è stata abilitata l'interfaccia utente di convalida.</span><span class="sxs-lookup"><span data-stu-id="c0a91-150">Once data annotations were applied to the model, the validation UI was enabled.</span></span> <span data-ttu-id="c0a91-151">Le Razor pagine create in questa esercitazione hanno selezionato automaticamente le regole di convalida, usando gli attributi di convalida sulle proprietà della `Movie` classe del modello.</span><span class="sxs-lookup"><span data-stu-id="c0a91-151">The Razor Pages created in this tutorial automatically picked up the validation rules, using validation attributes on the properties of the `Movie` model class.</span></span> <span data-ttu-id="c0a91-152">Convalida del test tramite la pagina Modifica, viene applicata la stessa convalida.</span><span class="sxs-lookup"><span data-stu-id="c0a91-152">Test validation using the Edit page, the same validation is applied.</span></span>

<span data-ttu-id="c0a91-153">I dati del modulo non vengono registrati nel server fino a quando non sono presenti errori di convalida nel lato client.</span><span class="sxs-lookup"><span data-stu-id="c0a91-153">The form data isn't posted to the server until there are no client-side validation errors.</span></span> <span data-ttu-id="c0a91-154">Verificare che i dati del modulo non siano stati registrati da uno o più degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="c0a91-154">Verify form data isn't posted by one or more of the following approaches:</span></span>

* <span data-ttu-id="c0a91-155">Inserire un punto di interruzione nel metodo `OnPostAsync`.</span><span class="sxs-lookup"><span data-stu-id="c0a91-155">Put a break point in the `OnPostAsync` method.</span></span> <span data-ttu-id="c0a91-156">Inviare il modulo selezionando **Crea** o **Salva**.</span><span class="sxs-lookup"><span data-stu-id="c0a91-156">Submit the form by selecting **Create** or **Save**.</span></span> <span data-ttu-id="c0a91-157">Il punto di interruzione non viene mai raggiunto.</span><span class="sxs-lookup"><span data-stu-id="c0a91-157">The break point is never hit.</span></span>
* <span data-ttu-id="c0a91-158">Usare lo [Strumento Fiddler](https://www.telerik.com/fiddler).</span><span class="sxs-lookup"><span data-stu-id="c0a91-158">Use the [Fiddler tool](https://www.telerik.com/fiddler).</span></span>
* <span data-ttu-id="c0a91-159">Usare gli strumenti di sviluppo del browser per monitorare il traffico di rete.</span><span class="sxs-lookup"><span data-stu-id="c0a91-159">Use the browser developer tools to monitor network traffic.</span></span>

### <a name="server-side-validation"></a><span data-ttu-id="c0a91-160">Convalida sul lato server</span><span class="sxs-lookup"><span data-stu-id="c0a91-160">Server-side validation</span></span>

<span data-ttu-id="c0a91-161">Quando JavaScript è disabilitato nel browser, l'invio del modulo con errori verrà inoltrato al server.</span><span class="sxs-lookup"><span data-stu-id="c0a91-161">When JavaScript is disabled in the browser, submitting the form with errors will post to the server.</span></span>

<span data-ttu-id="c0a91-162">Facoltativo, convalida sul lato server del test:</span><span class="sxs-lookup"><span data-stu-id="c0a91-162">Optional, test server-side validation:</span></span>

1. <span data-ttu-id="c0a91-163">Disabilitare JavaScript nel browser.</span><span class="sxs-lookup"><span data-stu-id="c0a91-163">Disable JavaScript in the browser.</span></span> <span data-ttu-id="c0a91-164">È possibile disabilitare JavaScript usando gli strumenti di sviluppo del browser.</span><span class="sxs-lookup"><span data-stu-id="c0a91-164">JavaScript can be disabled using browser's developer tools.</span></span> <span data-ttu-id="c0a91-165">Se non è possibile disabilitare JavaScript nel browser, provare con un altro browser.</span><span class="sxs-lookup"><span data-stu-id="c0a91-165">If JavaScript cannot be disabled in the browser, try another browser.</span></span>
1. <span data-ttu-id="c0a91-166">Impostare un punto di interruzione nel metodo `OnPostAsync` della pagina Crea o Modifica.</span><span class="sxs-lookup"><span data-stu-id="c0a91-166">Set a break point in the `OnPostAsync` method of the Create or Edit page.</span></span>
1. <span data-ttu-id="c0a91-167">Inviare un modulo con dati non validi.</span><span class="sxs-lookup"><span data-stu-id="c0a91-167">Submit a form with invalid data.</span></span>
1. <span data-ttu-id="c0a91-168">Verificare che lo stato del modello non sia valido:</span><span class="sxs-lookup"><span data-stu-id="c0a91-168">Verify the model state is invalid:</span></span>

   ```csharp
    if (!ModelState.IsValid)
    {
       return Page();
    }
   ```
  
<span data-ttu-id="c0a91-169">In alternativa, [disabilitare la convalida lato client sul server](xref:mvc/models/validation#disable-client-side-validation).</span><span class="sxs-lookup"><span data-stu-id="c0a91-169">Alternatively, [Disable client-side validation on the server](xref:mvc/models/validation#disable-client-side-validation).</span></span>

<span data-ttu-id="c0a91-170">Il codice seguente mostra una parte della pagina *Create.cshtml* di cui è stato eseguito lo scaffolding in precedenza nell'esercitazione.</span><span class="sxs-lookup"><span data-stu-id="c0a91-170">The following code shows a portion of the *Create.cshtml* page scaffolded earlier in the tutorial.</span></span> <span data-ttu-id="c0a91-171">Viene usato dalle pagine Crea e modifica per:</span><span class="sxs-lookup"><span data-stu-id="c0a91-171">It's used by the Create and Edit pages to:</span></span>

* <span data-ttu-id="c0a91-172">Visualizzare il form iniziale.</span><span class="sxs-lookup"><span data-stu-id="c0a91-172">Display the initial form.</span></span>
* <span data-ttu-id="c0a91-173">Rivisualizzare il modulo in caso di errore.</span><span class="sxs-lookup"><span data-stu-id="c0a91-173">Redisplay the form in the event of an error.</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=14-20)]

<span data-ttu-id="c0a91-174">L'[helper tag di input](xref:mvc/views/working-with-forms) usa gli attributi [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) e produce gli attributi HTML necessari per la convalida jQuery sul lato client.</span><span class="sxs-lookup"><span data-stu-id="c0a91-174">The [Input Tag Helper](xref:mvc/views/working-with-forms) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span> <span data-ttu-id="c0a91-175">L'[helper tag di convalida](xref:mvc/views/working-with-forms#the-validation-tag-helpers) visualizza gli errori di convalida.</span><span class="sxs-lookup"><span data-stu-id="c0a91-175">The [Validation Tag Helper](xref:mvc/views/working-with-forms#the-validation-tag-helpers) displays validation errors.</span></span> <span data-ttu-id="c0a91-176">Per altre informazioni, vedere [Convalida](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="c0a91-176">See [Validation](xref:mvc/models/validation) for more information.</span></span>

<span data-ttu-id="c0a91-177">Le pagine Create e Edit non dispongono di nessuna regola di convalida.</span><span class="sxs-lookup"><span data-stu-id="c0a91-177">The Create and Edit pages have no validation rules in them.</span></span> <span data-ttu-id="c0a91-178">Le regole di convalida e le stringhe di errore vengono specificate solo nella classe `Movie`.</span><span class="sxs-lookup"><span data-stu-id="c0a91-178">The validation rules and the error strings are specified only in the `Movie` class.</span></span> <span data-ttu-id="c0a91-179">Queste regole di convalida vengono applicate automaticamente alle Razor pagine che modificano il `Movie` modello.</span><span class="sxs-lookup"><span data-stu-id="c0a91-179">These validation rules are automatically applied to Razor Pages that edit the `Movie` model.</span></span>

<span data-ttu-id="c0a91-180">Quando la logica di convalida deve cambiare, avviene solo nel modello.</span><span class="sxs-lookup"><span data-stu-id="c0a91-180">When validation logic needs to change, it's done only in the model.</span></span> <span data-ttu-id="c0a91-181">La convalida viene applicata in modo coerente in tutta l'applicazione, la logica di convalida viene definita in un'unica posizione.</span><span class="sxs-lookup"><span data-stu-id="c0a91-181">Validation is applied consistently throughout the application, validation logic is defined in one place.</span></span> <span data-ttu-id="c0a91-182">La convalida in un'unica posizione consente di mantenere il codice pulito e rende più semplice la gestione e l'aggiornamento.</span><span class="sxs-lookup"><span data-stu-id="c0a91-182">Validation in one place helps keep the code clean, and makes it easier to maintain and update.</span></span>

## <a name="use-datatype-attributes"></a><span data-ttu-id="c0a91-183">USA attributi DataType</span><span class="sxs-lookup"><span data-stu-id="c0a91-183">Use DataType Attributes</span></span>

<span data-ttu-id="c0a91-184">Esaminare la classe `Movie`.</span><span class="sxs-lookup"><span data-stu-id="c0a91-184">Examine the `Movie` class.</span></span> <span data-ttu-id="c0a91-185">Lo spazio dei nomi `System.ComponentModel.DataAnnotations` fornisce gli attributi di formattazione oltre al set predefinito di attributi di convalida.</span><span class="sxs-lookup"><span data-stu-id="c0a91-185">The `System.ComponentModel.DataAnnotations` namespace provides formatting attributes in addition to the built-in set of validation attributes.</span></span> <span data-ttu-id="c0a91-186">L'attributo `[DataType]` viene applicato alle proprietà `ReleaseDate` e `Price`.</span><span class="sxs-lookup"><span data-stu-id="c0a91-186">The `[DataType]` attribute is applied to the `ReleaseDate` and `Price` properties.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie/Models/MovieDateRatingDA.cs?highlight=2,6&name=snippet2)]

<span data-ttu-id="c0a91-187">Gli `[DataType]` attributi forniscono:</span><span class="sxs-lookup"><span data-stu-id="c0a91-187">The `[DataType]` attributes provide:</span></span>

* <span data-ttu-id="c0a91-188">Suggerimenti per il motore di visualizzazione per formattare i dati.</span><span class="sxs-lookup"><span data-stu-id="c0a91-188">Hints for the view engine to format the data.</span></span>
* <span data-ttu-id="c0a91-189">Fornisce attributi come `<a>` per gli URL e `<a href="mailto:EmailAddress.com">` per la posta elettronica.</span><span class="sxs-lookup"><span data-stu-id="c0a91-189">Supplies attributes such as `<a>` for URL's and `<a href="mailto:EmailAddress.com">` for email.</span></span>

<span data-ttu-id="c0a91-190">Usare l'attributo `[RegularExpression]` per convalidare il formato dei dati.</span><span class="sxs-lookup"><span data-stu-id="c0a91-190">Use the `[RegularExpression]` attribute to validate the format of the data.</span></span> <span data-ttu-id="c0a91-191">L'attributo `[DataType]` viene usato per specificare un tipo di dati che è più specifico del tipo intrinseco del database.</span><span class="sxs-lookup"><span data-stu-id="c0a91-191">The `[DataType]` attribute is used to specify a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="c0a91-192">`[DataType]` gli attributi non sono attributi di convalida.</span><span class="sxs-lookup"><span data-stu-id="c0a91-192">`[DataType]` attributes aren't validation attributes.</span></span> <span data-ttu-id="c0a91-193">Nell'applicazione di esempio, viene visualizzata solo la data, senza l'ora.</span><span class="sxs-lookup"><span data-stu-id="c0a91-193">In the sample application, only the date is displayed, without time.</span></span>

<span data-ttu-id="c0a91-194">L' `DataType` enumerazione fornisce molti tipi di dati, ad esempio `Date` ,, `Time` `PhoneNumber` , `Currency` , `EmailAddress` e molto altro.</span><span class="sxs-lookup"><span data-stu-id="c0a91-194">The `DataType` enumeration provides many data types, such as `Date`, `Time`, `PhoneNumber`, `Currency`, `EmailAddress`, and more.</span></span> 

<span data-ttu-id="c0a91-195">`[DataType]`Attributi:</span><span class="sxs-lookup"><span data-stu-id="c0a91-195">The `[DataType]` attributes:</span></span>

* <span data-ttu-id="c0a91-196">Può consentire all'applicazione di fornire automaticamente funzionalità specifiche del tipo.</span><span class="sxs-lookup"><span data-stu-id="c0a91-196">Can enable the application to automatically provide type-specific features.</span></span> <span data-ttu-id="c0a91-197">Ad esempio, è possibile creare un collegamento `mailto:` per `DataType.EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="c0a91-197">For example, a `mailto:` link can be created for `DataType.EmailAddress`.</span></span>
* <span data-ttu-id="c0a91-198">Può fornire un selettore `DataType.Date` di data nei browser che supportano HTML5.</span><span class="sxs-lookup"><span data-stu-id="c0a91-198">Can provide a date selector `DataType.Date` in browsers that support HTML5.</span></span>
* <span data-ttu-id="c0a91-199">Genera HTML 5 `data-` , pronunciato "Dash di dati", attributi utilizzati dai browser HTML 5.</span><span class="sxs-lookup"><span data-stu-id="c0a91-199">Emit HTML 5 `data-`, pronounced "data dash", attributes that HTML 5 browsers consume.</span></span>
* <span data-ttu-id="c0a91-200">**Non** fornire alcuna convalida.</span><span class="sxs-lookup"><span data-stu-id="c0a91-200">Do **not** provide any validation.</span></span>

<span data-ttu-id="c0a91-201">`DataType.Date` non specifica il formato della data visualizzata.</span><span class="sxs-lookup"><span data-stu-id="c0a91-201">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="c0a91-202">Per impostazione predefinita, il campo dei dati viene visualizzato in base ai formati predefiniti per il valore `CultureInfo` del server.</span><span class="sxs-lookup"><span data-stu-id="c0a91-202">By default, the data field is displayed according to the default formats based on the server's `CultureInfo`.</span></span>

<span data-ttu-id="c0a91-203">L'annotazione dei dati `[Column(TypeName = "decimal(18, 2)")]` è necessaria per consentire a Entity Framework Core di eseguire correttamente il mapping di `Price` nella valuta del database.</span><span class="sxs-lookup"><span data-stu-id="c0a91-203">The `[Column(TypeName = "decimal(18, 2)")]` data annotation is required so Entity Framework Core can correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="c0a91-204">Per altre informazioni, vedere [Tipi di dati](/ef/core/modeling/relational/data-types).</span><span class="sxs-lookup"><span data-stu-id="c0a91-204">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>

<span data-ttu-id="c0a91-205">L'attributo `[DisplayFormat]` viene usato per specificare in modo esplicito il formato della data:</span><span class="sxs-lookup"><span data-stu-id="c0a91-205">The `[DisplayFormat]` attribute is used to explicitly specify the date format:</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
public DateTime ReleaseDate { get; set; }
```

<span data-ttu-id="c0a91-206">L' `ApplyFormatInEditMode` impostazione specifica che la formattazione verrà applicata quando il valore viene visualizzato per la modifica.</span><span class="sxs-lookup"><span data-stu-id="c0a91-206">The `ApplyFormatInEditMode` setting specifies that the formatting will be applied when the value is displayed for editing.</span></span> <span data-ttu-id="c0a91-207">Questo comportamento potrebbe non essere desiderato per alcuni campi.</span><span class="sxs-lookup"><span data-stu-id="c0a91-207">That behavior may not be wanted for some fields.</span></span> <span data-ttu-id="c0a91-208">Nei valori di valuta, ad esempio, il simbolo di valuta non è in genere desiderato nell'interfaccia utente di modifica.</span><span class="sxs-lookup"><span data-stu-id="c0a91-208">For example, in currency values, the currency symbol is usually not wanted in the edit UI.</span></span>

<span data-ttu-id="c0a91-209">L'attributo `[DisplayFormat]` può essere usato da solo, ma in genere è consigliabile usare l'attributo `[DataType]`.</span><span class="sxs-lookup"><span data-stu-id="c0a91-209">The `[DisplayFormat]` attribute can be used by itself, but it's generally a good idea to use the `[DataType]` attribute.</span></span> <span data-ttu-id="c0a91-210">L'attributo `[DataType]` offre la semantica dei dati anziché specificarne il rendering in una schermata.</span><span class="sxs-lookup"><span data-stu-id="c0a91-210">The `[DataType]` attribute conveys the semantics of the data as opposed to how to render it on a screen.</span></span> <span data-ttu-id="c0a91-211">L' `[DataType]` attributo offre i seguenti vantaggi che non sono disponibili con `[DisplayFormat]` :</span><span class="sxs-lookup"><span data-stu-id="c0a91-211">The `[DataType]` attribute provides the following benefits that aren't available with `[DisplayFormat]`:</span></span>

* <span data-ttu-id="c0a91-212">Il browser può abilitare le funzionalità HTML5, ad esempio per visualizzare un controllo calendario, il simbolo di valuta appropriato per le impostazioni locali, i collegamenti alla posta elettronica e così via.</span><span class="sxs-lookup"><span data-stu-id="c0a91-212">The browser can enable HTML5 features, for example to show a calendar control, the locale-appropriate currency symbol, email links, etc.</span></span>
* <span data-ttu-id="c0a91-213">Per impostazione predefinita, il browser esegue il rendering dei dati usando il formato corretto in base alle impostazioni locali.</span><span class="sxs-lookup"><span data-stu-id="c0a91-213">By default, the browser renders data using the correct format based on its locale.</span></span>
* <span data-ttu-id="c0a91-214">L'attributo `[DataType]` può abilitare il framework di ASP.NET Core a scegliere il modello di campo corretto per il rendering dei dati.</span><span class="sxs-lookup"><span data-stu-id="c0a91-214">The `[DataType]` attribute can enable the ASP.NET Core framework to choose the right field template to render the data.</span></span> <span data-ttu-id="c0a91-215">`DisplayFormat`, Se usato da solo, usa il modello di stringa.</span><span class="sxs-lookup"><span data-stu-id="c0a91-215">The `DisplayFormat`, if used by itself, uses the string template.</span></span>

<span data-ttu-id="c0a91-216">**Nota:** la convalida jQuery non funziona con l' `[Range]` attributo e con `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="c0a91-216">**Note:** jQuery validation doesn't work with the `[Range]` attribute and `DateTime`.</span></span> <span data-ttu-id="c0a91-217">Ad esempio, il codice seguente visualizzerà sempre un errore di convalida sul lato client, anche quando la data è compreso nell'intervallo specificato:</span><span class="sxs-lookup"><span data-stu-id="c0a91-217">For example, the following code will always display a client-side validation error, even when the date is in the specified range:</span></span>

```csharp
[Range(typeof(DateTime), "1/1/1966", "1/1/2020")]
   ```

<span data-ttu-id="c0a91-218">È consigliabile evitare la compilazione di date rigide nei modelli, quindi l'uso dell' `[Range]` attributo `DateTime` è sconsigliato.</span><span class="sxs-lookup"><span data-stu-id="c0a91-218">It's a best practice to avoid compiling hard dates in models, so using the `[Range]` attribute and `DateTime` is discouraged.</span></span> <span data-ttu-id="c0a91-219">Usare la [configurazione](xref:fundamentals/configuration/index) per gli intervalli di date e altri valori soggetti a modifiche frequenti anziché specificarli nel codice.</span><span class="sxs-lookup"><span data-stu-id="c0a91-219">Use [Configuration](xref:fundamentals/configuration/index) for date ranges and other values that are subject to frequent change rather than specifying it in code.</span></span>

<span data-ttu-id="c0a91-220">Il codice seguente illustra la combinazione di attributi in una sola riga:</span><span class="sxs-lookup"><span data-stu-id="c0a91-220">The following code shows combining attributes on one line:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDAmult.cs?name=snippet1)]

<span data-ttu-id="c0a91-221">[Introduzione a Razor Pagine e EF Core](xref:data/ef-rp/intro) Mostra le operazioni di EF core avanzate con le Razor pagine.</span><span class="sxs-lookup"><span data-stu-id="c0a91-221">[Get started with Razor Pages and EF Core](xref:data/ef-rp/intro) shows advanced EF Core operations with Razor Pages.</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="c0a91-222">Applicare le migrazioni</span><span class="sxs-lookup"><span data-stu-id="c0a91-222">Apply migrations</span></span>

<span data-ttu-id="c0a91-223">L'oggetto DataAnnotations applicato alla classe modifica lo schema.</span><span class="sxs-lookup"><span data-stu-id="c0a91-223">The DataAnnotations applied to the class changes the schema.</span></span> <span data-ttu-id="c0a91-224">Ad esempio, le annotazioni DataAnnotations applicate al campo `Title`:</span><span class="sxs-lookup"><span data-stu-id="c0a91-224">For example, the DataAnnotations applied to the `Title` field:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet11)]

* <span data-ttu-id="c0a91-225">Limitano i caratteri a 60.</span><span class="sxs-lookup"><span data-stu-id="c0a91-225">Limits the characters to 60.</span></span>
* <span data-ttu-id="c0a91-226">Non consentono un valore `null`.</span><span class="sxs-lookup"><span data-stu-id="c0a91-226">Doesn't allow a `null` value.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c0a91-227">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c0a91-227">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c0a91-228">La tabella `Movie` ha attualmente lo schema seguente:</span><span class="sxs-lookup"><span data-stu-id="c0a91-228">The `Movie` table currently has the following schema:</span></span>

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

<span data-ttu-id="c0a91-229">Le modifiche dello schema precedenti non determinano la generazione di un'eccezione da parte di EF.</span><span class="sxs-lookup"><span data-stu-id="c0a91-229">The preceding schema changes don't cause EF to throw an exception.</span></span> <span data-ttu-id="c0a91-230">Tuttavia, creare una migrazione in modo che lo schema sia coerente con il modello.</span><span class="sxs-lookup"><span data-stu-id="c0a91-230">However, create a migration so the schema is consistent with the model.</span></span>

<span data-ttu-id="c0a91-231">Dal menu **Strumenti** selezionare **Gestione pacchetti NuGet > Console di Gestione pacchetti**.</span><span class="sxs-lookup"><span data-stu-id="c0a91-231">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
<span data-ttu-id="c0a91-232">Nella Console di Gestione pacchetti immettere i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="c0a91-232">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration New_DataAnnotations
Update-Database
```

<span data-ttu-id="c0a91-233">`Update-Database` esegue i metodi `Up` della classe `New_DataAnnotations`.</span><span class="sxs-lookup"><span data-stu-id="c0a91-233">`Update-Database` runs the `Up` methods of the `New_DataAnnotations` class.</span></span> <span data-ttu-id="c0a91-234">Esaminare il metodo `Up`:</span><span class="sxs-lookup"><span data-stu-id="c0a91-234">Examine the `Up` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Migrations/20190724163003_New_DataAnnotations.cs?name=snippet)]

<span data-ttu-id="c0a91-235">La tabella `Movie` aggiornata presenta lo schema seguente:</span><span class="sxs-lookup"><span data-stu-id="c0a91-235">The updated `Movie` table has the following schema:</span></span>

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

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="c0a91-236">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="c0a91-236">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="c0a91-237">Non sono necessarie migrazioni per SQLite.</span><span class="sxs-lookup"><span data-stu-id="c0a91-237">Migrations are not required for SQLite.</span></span>

---

### <a name="publish-to-azure"></a><span data-ttu-id="c0a91-238">Pubblicazione in Azure</span><span class="sxs-lookup"><span data-stu-id="c0a91-238">Publish to Azure</span></span>

<span data-ttu-id="c0a91-239">Per informazioni sulla distribuzione in Azure, vedere [esercitazione: compilare un'app ASP.NET Core in Azure con il database SQL](/azure/app-service/tutorial-dotnetcore-sqldb-app).</span><span class="sxs-lookup"><span data-stu-id="c0a91-239">For information on deploying to Azure, see [Tutorial: Build an ASP.NET Core app in Azure with SQL Database](/azure/app-service/tutorial-dotnetcore-sqldb-app).</span></span>

<span data-ttu-id="c0a91-240">Grazie per aver completato questa introduzione alle Razor pagine.</span><span class="sxs-lookup"><span data-stu-id="c0a91-240">Thanks for completing this introduction to Razor Pages.</span></span> <span data-ttu-id="c0a91-241">[Introduzione a Razor Pagine e EF Core](xref:data/ef-rp/intro) è un'ottima procedura per completare questa esercitazione.</span><span class="sxs-lookup"><span data-stu-id="c0a91-241">[Get started with Razor Pages and EF Core](xref:data/ef-rp/intro) is an excellent follow up to this tutorial.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c0a91-242">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="c0a91-242">Additional resources</span></span>

* <xref:mvc/views/working-with-forms>
* <xref:fundamentals/localization>
* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/views/tag-helpers/authoring>

> [!div class="step-by-step"]
> [<span data-ttu-id="c0a91-243">Precedente: aggiungere un nuovo campo</span><span class="sxs-lookup"><span data-stu-id="c0a91-243">Previous: Add a new field</span></span>](xref:tutorials/razor-pages/new-field)
