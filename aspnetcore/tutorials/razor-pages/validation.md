---
title: Parte 8, aggiungere la convalida
author: rick-anderson
description: Parte 8 della serie di esercitazioni sulle Razor pagine.
ms.author: riande
ms.custom: mvc
ms.date: 09/29/2020
no-loc:
- Index
- Create
- Delete
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
ms.openlocfilehash: efae7d79ff7a0b351afc68264463546bb26b4424
ms.sourcegitcommit: 91e14f1e2a25c98a57c2217fe91b172e0ff2958c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94422704"
---
# <a name="part-8-add-validation-to-an-aspnet-core-no-locrazor-page"></a>Parte 8: aggiungere la convalida a una Razor pagina ASP.NET Core

Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)

In questa sezione la logica di convalida viene aggiunta al modello `Movie`. Le regole di convalida vengono applicate ogni volta che un utente crea o modifica un film.

## <a name="validation"></a>Convalida

Un concetto di base dello sviluppo del software si chiama [DRY](https://wikipedia.org/wiki/Don%27t_repeat_yourself) (" **D** on't **R** epeat **Y** ourself", Non ripeterti). Razor Le pagine favoriscono lo sviluppo in cui la funzionalità è specificata una sola volta e viene riflessa nell'intera app. DRY contribuisce a:

* Ridurre la quantità di codice in un'app.
* Rendere il codice meno soggetto ad errori e più facile da testare e gestire.

Il supporto della convalida fornito da Razor pagine e Entity Framework è un valido esempio di principio secco:

* Le regole di convalida vengono specificate in modo dichiarativo in un'unica posizione nella classe del modello.
* Le regole vengono applicate ovunque nell'app.

## <a name="add-validation-rules-to-the-movie-model"></a>Aggiungere regole di convalida al modello movie

Lo `DataAnnotations` spazio dei nomi fornisce:

* Set di attributi di convalida predefiniti che vengono applicati in modo dichiarativo a una classe o a una proprietà.
* Formattazione di attributi come `[DataType]` questa guida alla formattazione e non forniscono alcuna convalida.

Aggiornare la classe `Movie` per poter sfruttare gli attributi di convalida `[Required]`, `[StringLength]`, `[RegularExpression]` e `[Range]` predefiniti.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

Gli attributi di convalida specificano il comportamento da applicare alle proprietà del modello a cui sono applicati:

* Gli attributi `[Required]` e `[MinimumLength]` indicano che una proprietà deve avere un valore. Niente impedisce a un utente di immettere spazi vuoti per soddisfare la convalida.
* L'attributo `[RegularExpression]` viene usato per limitare i caratteri che possono essere inseriti. Nel codice precedente, `Genre` :

  * Deve includere solo lettere.
  * La prima lettera deve essere maiuscola. Gli spazi, i numeri e i caratteri speciali non sono consentiti.

* `RegularExpression` `Rating` :

  * Richiede che il primo carattere sia una lettera maiuscola.
  * Consente i caratteri speciali e i numeri negli spazi successivi. "PG-13" è valido per una classificazione, ma ha esito negativo per un `Genre` .

* L'attributo `[Range]` vincola un valore all'interno di un intervallo specificato.
* L' `[StringLength]` attributo può impostare una lunghezza massima di una proprietà di stringa e, facoltativamente, la lunghezza minima.
* I tipi di valore, ad esempio `decimal` , `int` ,, `float` `DateTime` , sono intrinsecamente necessari e non richiedono l' `[Required]` attributo.

Le regole di convalida precedenti vengono usate per la dimostrazione, non sono ottimali per un sistema di produzione. Il precedente, ad esempio, impedisce l'immissione di un film con solo due caratteri e non consente caratteri speciali in `Genre` .

Le regole di convalida applicate automaticamente da ASP.NET Core consentono di:

* Consente di rendere l'app più affidabile.
* Ridurre le possibilità di salvare dati non validi nel database.

### <a name="validation-error-ui-in-no-locrazor-pages"></a>Interfaccia utente degli errori di convalida nelle Razor pagine

Eseguire l'app e passare a Pages/Movies.

Selezionare il **Create nuovo** collegamento. Completare il modulo con alcuni valori non validi. Quando la convalida del lato client jQuery rileva l'errore, viene visualizzato un messaggio di errore.

![Il modulo di vista del film con più errori di convalida del lato client jQuery](validation/_static/val.png)

[!INCLUDE[](~/includes/localization/currency.md)]

Si noti come il modulo ha eseguito automaticamente il rendering di un messaggio di errore di convalida in ogni campo che contiene un valore non valido. Gli errori vengono applicati sia sul lato client, usando JavaScript che jQuery e sul lato server, quando un utente ha JavaScript disabilitato.

Un vantaggio significativo è che **non** è necessario apportare modifiche al codice nelle Create pagine di modifica o. Una volta applicate le annotazioni dei dati al modello, è stata abilitata l'interfaccia utente di convalida. Le Razor pagine create in questa esercitazione hanno selezionato automaticamente le regole di convalida, usando gli attributi di convalida sulle proprietà della `Movie` classe del modello. Convalida del test tramite la pagina Modifica, viene applicata la stessa convalida.

I dati del modulo non vengono registrati nel server fino a quando non sono presenti errori di convalida nel lato client. Verificare che i dati del modulo non siano stati registrati da uno o più degli approcci seguenti:

* Inserire un punto di interruzione nel metodo `OnPostAsync`. Inviare il modulo selezionando **Create** o **salvando**. Il punto di interruzione non viene mai raggiunto.
* Usare lo [Strumento Fiddler](https://www.telerik.com/fiddler).
* Usare gli strumenti di sviluppo del browser per monitorare il traffico di rete.

### <a name="server-side-validation"></a>Convalida sul lato server

Quando JavaScript è disabilitato nel browser, l'invio del modulo con errori verrà inoltrato al server.

Facoltativo, convalida sul lato server del test:

1. Disabilitare JavaScript nel browser. È possibile disabilitare JavaScript usando gli strumenti di sviluppo del browser. Se non è possibile disabilitare JavaScript nel browser, provare con un altro browser.
1. Impostare un punto di pausa nel `OnPostAsync` metodo della Create pagina di modifica o.
1. Inviare un modulo con dati non validi.
1. Verificare che lo stato del modello non sia valido:

   ```csharp
    if (!ModelState.IsValid)
    {
       return Page();
    }
   ```
  
In alternativa, [disabilitare la convalida lato client sul server](xref:mvc/models/validation#disable-client-side-validation).

Il codice seguente mostra una parte della pagina *Create . cshtml* con impalcature più indietro nell'esercitazione. Viene usato dalle Create pagine e modifica per:

* Visualizzare il form iniziale.
* Rivisualizzare il modulo in caso di errore.

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=14-20)]

L'[helper tag di input](xref:mvc/views/working-with-forms) usa gli attributi [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) e produce gli attributi HTML necessari per la convalida jQuery sul lato client. L'[helper tag di convalida](xref:mvc/views/working-with-forms#the-validation-tag-helpers) visualizza gli errori di convalida. Per altre informazioni, vedere [Convalida](xref:mvc/models/validation).

Le Create pagine di modifica e non hanno alcuna regola di convalida. Le regole di convalida e le stringhe di errore vengono specificate solo nella classe `Movie`. Queste regole di convalida vengono applicate automaticamente alle Razor pagine che modificano il `Movie` modello.

Quando la logica di convalida deve cambiare, avviene solo nel modello. La convalida viene applicata in modo coerente in tutta l'applicazione, la logica di convalida viene definita in un'unica posizione. La convalida in un'unica posizione consente di mantenere il codice pulito e rende più semplice la gestione e l'aggiornamento.

## <a name="use-datatype-attributes"></a>USA attributi DataType

Esaminare la classe `Movie`. Lo spazio dei nomi `System.ComponentModel.DataAnnotations` fornisce gli attributi di formattazione oltre al set predefinito di attributi di convalida. L'attributo `[DataType]` viene applicato alle proprietà `ReleaseDate` e `Price`.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie/Models/MovieDateRatingDA.cs?highlight=2,6&name=snippet2)]

Gli `[DataType]` attributi forniscono:

* Suggerimenti per il motore di visualizzazione per formattare i dati.
* Fornisce attributi come `<a>` per gli URL e `<a href="mailto:EmailAddress.com">` per la posta elettronica.

Usare l'attributo `[RegularExpression]` per convalidare il formato dei dati. L'attributo `[DataType]` viene usato per specificare un tipo di dati che è più specifico del tipo intrinseco del database. `[DataType]` gli attributi non sono attributi di convalida. Nell'applicazione di esempio, viene visualizzata solo la data, senza l'ora.

L' `DataType` enumerazione fornisce molti tipi di dati, ad esempio `Date` ,, `Time` `PhoneNumber` , `Currency` , `EmailAddress` e molto altro. 

`[DataType]`Attributi:

* Può consentire all'applicazione di fornire automaticamente funzionalità specifiche del tipo. Ad esempio, è possibile creare un collegamento `mailto:` per `DataType.EmailAddress`.
* Può fornire un selettore `DataType.Date` di data nei browser che supportano HTML5.
* Genera HTML 5 `data-` , pronunciato "Dash di dati", attributi utilizzati dai browser HTML 5.
* **Non** fornire alcuna convalida.

`DataType.Date` non specifica il formato della data visualizzata. Per impostazione predefinita, il campo dei dati viene visualizzato in base ai formati predefiniti per il valore `CultureInfo` del server.

L'annotazione dei dati `[Column(TypeName = "decimal(18, 2)")]` è necessaria per consentire a Entity Framework Core di eseguire correttamente il mapping di `Price` nella valuta del database. Per altre informazioni, vedere [Tipi di dati](/ef/core/modeling/relational/data-types).

L'attributo `[DisplayFormat]` viene usato per specificare in modo esplicito il formato della data:

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
public DateTime ReleaseDate { get; set; }
```

L' `ApplyFormatInEditMode` impostazione specifica che la formattazione verrà applicata quando il valore viene visualizzato per la modifica. Questo comportamento potrebbe non essere desiderato per alcuni campi. Nei valori di valuta, ad esempio, il simbolo di valuta non è in genere desiderato nell'interfaccia utente di modifica.

L'attributo `[DisplayFormat]` può essere usato da solo, ma in genere è consigliabile usare l'attributo `[DataType]`. L'attributo `[DataType]` offre la semantica dei dati anziché specificarne il rendering in una schermata. L' `[DataType]` attributo offre i seguenti vantaggi che non sono disponibili con `[DisplayFormat]` :

* Il browser può abilitare le funzionalità HTML5, ad esempio per visualizzare un controllo calendario, il simbolo di valuta appropriato per le impostazioni locali, i collegamenti alla posta elettronica e così via.
* Per impostazione predefinita, il browser esegue il rendering dei dati usando il formato corretto in base alle impostazioni locali.
* L'attributo `[DataType]` può abilitare il framework di ASP.NET Core a scegliere il modello di campo corretto per il rendering dei dati. `DisplayFormat`, Se usato da solo, usa il modello di stringa.

**Nota:** la convalida jQuery non funziona con l' `[Range]` attributo e con `DateTime` . Ad esempio, il codice seguente visualizzerà sempre un errore di convalida sul lato client, anche quando la data è compreso nell'intervallo specificato:

```csharp
[Range(typeof(DateTime), "1/1/1966", "1/1/2020")]
   ```

È consigliabile evitare la compilazione di date rigide nei modelli, quindi l'uso dell' `[Range]` attributo `DateTime` è sconsigliato. Usare la [configurazione](xref:fundamentals/configuration/index) per gli intervalli di date e altri valori soggetti a modifiche frequenti anziché specificarli nel codice.

Il codice seguente illustra la combinazione di attributi in una sola riga:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDAmult.cs?name=snippet1)]

[Introduzione a Razor Pagine e EF Core](xref:data/ef-rp/intro) Mostra le operazioni di EF core avanzate con le Razor pagine.

### <a name="apply-migrations"></a>Applicare le migrazioni

L'oggetto DataAnnotations applicato alla classe modifica lo schema. Ad esempio, le annotazioni DataAnnotations applicate al campo `Title`:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet11)]

* Limitano i caratteri a 60.
* Non consentono un valore `null`.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

La tabella `Movie` ha attualmente lo schema seguente:

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

Le modifiche dello schema precedenti non determinano la generazione di un'eccezione da parte di EF. Tuttavia, creare una migrazione in modo che lo schema sia coerente con il modello.

Dal menu **Strumenti** selezionare **Gestione pacchetti NuGet > Console di Gestione pacchetti**.
Nella Console di Gestione pacchetti immettere i comandi seguenti:

```powershell
Add-Migration New_DataAnnotations
Update-Database
```

`Update-Database` esegue i metodi `Up` della classe `New_DataAnnotations`. Esaminare il metodo `Up`:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Migrations/20190724163003_New_DataAnnotations.cs?name=snippet)]

La tabella `Movie` aggiornata presenta lo schema seguente:

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

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio per Mac](#tab/visual-studio-code+visual-studio-mac)

Non sono necessarie migrazioni per SQLite.

---

### <a name="publish-to-azure"></a>Pubblicazione in Azure

Per informazioni sulla distribuzione in Azure, vedere [esercitazione: compilare un'app ASP.NET Core in Azure con il database SQL](/azure/app-service/tutorial-dotnetcore-sqldb-app).

Grazie per aver completato questa introduzione alle Razor pagine. [Introduzione a Razor Pagine e EF Core](xref:data/ef-rp/intro) è un'ottima procedura per completare questa esercitazione.

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:mvc/views/working-with-forms>
* <xref:fundamentals/localization>
* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/views/tag-helpers/authoring>

> [!div class="step-by-step"]
> [Precedente: aggiungere un nuovo campo](xref:tutorials/razor-pages/new-field)
