---
title: Metodi e viste del controller in ASP.NET Core
author: rick-anderson
description: Informazioni su come usare metodi, visualizzazioni e DataAnnotations del controller in ASP.NET Core.
ms.author: riande
ms.date: 12/13/2018
uid: tutorials/first-mvc-app/controller-methods-views
ms.openlocfilehash: 87b3cb2f4429157123d30274d1f12cd589c1cc99
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80242510"
---
# <a name="controller-methods-and-views-in-aspnet-core"></a>Metodi e viste del controller in ASP.NET Core

Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)

Le operazioni iniziali con l'app per i film sono state efficaci, ma la presentazione non è ottimale, ad esempio **ReleaseDate** dovrebbe essere scritto come due parole.

![Vista Index: Release Date è un'unica parola (senza spazi) e la data di rilascio di ogni film indica l'ora nel formato 12 AM](working-with-sql/_static/m55.png)

Aprire il file *Models/Movie.cs* e aggiungere le righe evidenziate illustrate di seguito:

[!code-csharp[](start-mvc/sample/MvcMovie22/Models/MovieDateFixed.cs?name=snippet_1&highlight=2,3,12-13,17)]

L'attributo [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) viene esaminato nell'esercitazione successiva. L'attributo [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) specifica il testo da visualizzare per il nome di un campo, in questo caso "Release Date" anziché "ReleaseDate". L'attributo [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) specifica il tipo di dati (Date) e quindi non vengono visualizzate le informazioni sull'ora archiviate nel campo.

L'annotazione dei dati `[Column(TypeName = "decimal(18, 2)")]` è necessaria per consentire a Entity Framework Core di eseguire correttamente il mapping di `Price` nella valuta del database. Per altre informazioni, vedere [Tipi di dati](/ef/core/modeling/relational/data-types).

Passare al controller `Movies` e posizionare il puntatore del mouse su un collegamento **Edit** (Modifica) per visualizzare l'URL di destinazione.

![Finestra del browser con il passaggio del mouse sul collegamento Edit (Modifica) e un URL di collegamento di https://localhost:5001/Movies/Edit/5](~/tutorials/first-mvc-app/controller-methods-views/_static/edit7.png)

I collegamenti **Edit** (Modifica), **Details** (Dettagli) e **Delete** (Elimina) vengono generati dall'helper per i tag di ancoraggio di Core MVC nel file *Views/Movies/Index.cshtml*.

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/IndexOriginal.cshtml?highlight=1-3&range=46-50)]

Gli [helper tag](xref:mvc/views/tag-helpers/intro) consentono al codice lato server di partecipare alla creazione e al rendering di elementi HTML nei file Razor. Nel codice precedente, `AnchorTagHelper` il genera `href` dinamicamente il valore dell'attributo HTML dal metodo di azione del controller e dall'id della route. È possibile utilizzare **Visualizza origine** dal browser preferito o gli strumenti di sviluppo per esaminare il markup generato. Di seguito è riportata una parte del codice HTML generato:

```html
 <td>
    <a href="/Movies/Edit/4"> Edit </a> |
    <a href="/Movies/Details/4"> Details </a> |
    <a href="/Movies/Delete/4"> Delete </a>
</td>
```

Tenere presente il formato per il [routing](xref:mvc/controllers/routing) impostato nel file *Startup.cs*:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_1&highlight=5)]

ASP.NET Core converte `https://localhost:5001/Movies/Edit/4` in una richiesta al metodo di azione `Edit` del controller `Movies` con il parametro `Id` impostato su 4. I metodi del controller sono noti anche come metodi di azione.

Una delle nuove funzionalità più note di ASP.NET Core è rappresentata dagli [helper tag](xref:mvc/views/tag-helpers/intro). Per altre informazioni, vedere [Risorse aggiuntive](#additional-resources).

<a name="get-post"></a>

Aprire il controller `Movies` ed esaminare i due metodi di azione `Edit`. Il codice seguente illustra il metodo `HTTP GET Edit`, che recupera il film e popola il modulo di modifica generato dal file Razor *Edit.cshtml*.

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Controllers/MC1.cs?name=snippet_edit1)]

Il codice seguente illustra il metodo `HTTP POST Edit`, che elabora i valori di film inviati:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit1)]

Il codice seguente illustra il metodo `HTTP POST Edit`, che elabora i valori di film inviati:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2)]

::: moniker-end

L'attributo `[Bind]` è un modo per proteggersi dall'[overposting](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application#overpost). È necessario includere solo le proprietà nell'attributo `[Bind]` che si vuole modificare. Per altre informazioni, vedere [Proteggere il controller dall'overposting](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application). [ViewModel](https://rachelappel.com/use-viewmodels-to-manage-data-amp-organize-code-in-asp-net-mvc-applications/) offre un approccio alternativo per evitare l'overposting.

Si noti che il secondo metodo di azione `Edit` è preceduto dall'attributo `[HttpPost]`.

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Controllers/MC1.cs?name=snippet_edit2&highlight=1)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2&highlight=4)]

::: moniker-end

L'attributo `HttpPost` specifica che questo metodo `Edit` può essere richiamato *solo* per le richieste `POST`. È possibile applicare l'attributo `[HttpGet]` al primo metodo di modifica, ma non è necessario perché l'impostazione predefinita è `[HttpGet]`.

L'attributo `ValidateAntiForgeryToken` viene usato per [impedire la falsificazione di una richiesta](xref:security/anti-request-forgery) ed è accoppiato a un token antifalsificazione generato nel file di vista di modifica (*Views/Movies/Edit.cshtml*). Il file di vista di modifica genera il token antifalsificazione con l'[helper tag di modulo](xref:mvc/views/working-with-forms).

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/Edit.cshtml?range=9)]

L'[helper tag](xref:mvc/views/working-with-forms) genera un token antifalsificazione nascosto che deve corrispondere al token antifalsificazione generato `[ValidateAntiForgeryToken]` nel metodo `Edit` del controller di film. Per altre informazioni, vedere [Richiesta intersito falsa](xref:security/anti-request-forgery).

Il metodo `HttpGet Edit` accetta il parametro `ID`, cerca il film tramite il metodo `FindAsync` Entity Framework e restituisce il film selezionato nella vista Edit. Se non vengono trovati film, viene restituito l'errore HTTP 404 `NotFound`.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Controllers/MC1.cs?name=snippet_edit1)]

Quando il sistema di scaffolding ha creato la vista Edit, ha esaminato la classe `Movie` e il codice creato per eseguire il rendering degli elementi `<label>` e `<input>` per ogni proprietà della classe. L'esempio seguente illustra la vista Edit (Modifica) generata dal sistema di scaffolding di Visual Studio:

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/EditOriginal.cshtml)]

Si noti come il modello di vista contiene un'istruzione `@model MvcMovie.Models.Movie` all'inizio del file. `@model MvcMovie.Models.Movie` specifica che il modelli previsto dalla vista per il modello di vista sia di tipo `Movie`.

Il codice di scaffolding usa diversi metodi helper tag per semplificare il markup HTML. L'[helper tag di etichetta](xref:mvc/views/working-with-forms) visualizza il nome del campo ("Title", "ReleaseDate", "Genre" o "Price"). L'[helper tag di input](xref:mvc/views/working-with-forms) esegue il rendering di un elemento `<input>` HTML. L'[helper tag di convalida](xref:mvc/views/working-with-forms) visualizza eventuali messaggi di convalida associati a questa proprietà.

Eseguire l'applicazione e passare all'URL `/Movies`. Fare clic su un collegamento **Modifica.** Nel browser visualizzare l'origine per la pagina. Il codice HTML generato per l'elemento `<form>` è riportato di seguito.

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Shared/edit_view_source.html?highlight=1,6,10,17,24,28)]

Gli elementi `<input>` si trovano in un elemento `HTML <form>` il cui attributo `action` è impostato per inviare all'URL `/Movies/Edit/id`. I dati del modulo verranno inviati al server quando si fa clic sul pulsante `Save`. L'ultima riga prima dell'elemento `</form>` di chiusura mostra il token [XSRF](xref:security/anti-request-forgery) nascosto generato dall'[helper tag del modulo](xref:mvc/views/working-with-forms).

## <a name="processing-the-post-request"></a>Elaborazione della richiesta POST

Nell'elenco seguente viene indicata la versione `[HttpPost]` del metodo di azione `Edit`.

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Controllers/MC1.cs?name=snippet_edit2)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2)]

::: moniker-end

L'attributo `[ValidateAntiForgeryToken]` convalida il token [XSRF](xref:security/anti-request-forgery) nascosto generato dal generatore di token antifalsificazione nell'[helper tag del modulo](xref:mvc/views/working-with-forms)

Il sistema di [associazione di modelli](xref:mvc/models/model-binding) accetta i valori del modulo inseriti e crea un oggetto `Movie` passato come parametro `movie`. Il metodo `ModelState.IsValid` verifica che i dati inviati nel formato possano essere usati per cambiare (modificare o aggiornare) un oggetto `Movie`. Se sono validi, i dati vengono salvati. I dati dei film aggiornati (modificati) vengono salvati nel database chiamando il metodo `SaveChangesAsync` del contesto di database. Dopo avere salvato i dati, il codice reindirizza l'utente al metodo di azione `Index` della classe `MoviesController`, che visualizza la raccolta di film, incluse le modifiche appena apportate.

Prima di inviare il modulo al server, la convalida sul lato client controlla tutte le regole di convalida nei campi. Se sono presenti errori di convalida, viene visualizzato un messaggio di errore e il modulo non viene inviato. Se JavaScript è disabilitato, non verrà eseguita la convalida sul lato client, ma il server rileverà i valori inviati non validi e i valori del modulo verranno visualizzati nuovamente con messaggi di errore. Più avanti in questa esercitazione la [convalida del modello](xref:mvc/models/validation) verrà esaminata in maggiore dettaglio. L'[helper tag di convalida](xref:mvc/views/working-with-forms) nel modello di vista *Views/Movies/Edit.cshtml* gestisce la visualizzazione dei messaggi di errore appropriati.

![Vista Edit (Modifica): un'eccezione per un valore di prezzo non corretto di abc indica che il campo del prezzo deve essere un numero. Un'eccezione per un valore della data di rilascio non corretto di xyz indica di immettere una data valida.](~/tutorials/first-mvc-app/controller-methods-views/_static/val.png)

Tutti i metodi `HttpGet` nel controller di film seguono un pattern simile. Ricevono un oggetto film (o un elenco di oggetti nel caso di `Index`) e passano l'oggetto (modello) alla vista. Il metodo `Create` passa un oggetto vuoto alla vista `Create`. Tutti i metodi che creano, modificano, eliminano o cambiano in altro modo i dati, eseguono questa operazione nell'overload `[HttpPost]` del metodo. La modifica dei dati in un metodo `HTTP GET` è un rischio per la sicurezza. La modifica dei dati in un metodo `HTTP GET` viola anche le procedure consigliate HTTP e il pattern [REST](http://rest.elkstein.org/) architetturale, che specifica che le richieste GET non devono modificare lo stato dell'applicazione. In altre parole, l'esecuzione di un'operazione GET deve essere sicura, senza effetti collaterali e non modificare dati persistenti.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Globalizzazione e localizzazione](xref:fundamentals/localization)
* [Introduzione agli helper tag](xref:mvc/views/tag-helpers/intro)
* [Creare helper tag](xref:mvc/views/tag-helpers/authoring)
* [Richiesta intersito falsa](xref:security/anti-request-forgery)
* Proteggere il controller dall'[dall'overposting](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application).
* [ViewModel](https://rachelappel.com/use-viewmodels-to-manage-data-amp-organize-code-in-asp-net-mvc-applications/)
* [Helper tag di modulo](xref:mvc/views/working-with-forms)
* [Helper tag di input](xref:mvc/views/working-with-forms)
* [Helper tag di etichetta](xref:mvc/views/working-with-forms)
* [Helper tag di selezione](xref:mvc/views/working-with-forms)
* [Helper tag di convalida](xref:mvc/views/working-with-forms)

> [!div class="step-by-step"]
> [Successivo](working-with-sql.md)
> [precedente](search.md)  
