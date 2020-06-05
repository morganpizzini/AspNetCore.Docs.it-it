---
title: Aggiungere la convalida a una Razor pagina ASP.NET Core
author: rick-anderson
description: Scopri come aggiungere la convalida a una Razor pagina in ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 7/23/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/validation
ms.openlocfilehash: 7aeb77e836ce0546766d88f2c52f37aaf75c12c4
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2020
ms.locfileid: "84452044"
---
# <a name="add-validation-to-an-aspnet-core-razor-page"></a>Aggiungere la convalida a una Razor pagina ASP.NET Core

Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)

In questa sezione la logica di convalida viene aggiunta al modello `Movie`. Le regole di convalida vengono applicate ogni volta che un utente crea o modifica un film.

## <a name="validation"></a>Convalida

Un concetto di base dello sviluppo del software si chiama [DRY](https://wikipedia.org/wiki/Don%27t_repeat_yourself) ("**D**on't **R**epeat **Y**ourself", Non ripeterti). RazorLe pagine favoriscono lo sviluppo in cui la funzionalità è specificata una sola volta e viene riflessa nell'intera app. DRY contribuisce a:

* Ridurre la quantità di codice in un'app.
* Rendere il codice meno soggetto ad errori e più facile da testare e gestire.

Il supporto della convalida fornito da Razor pagine e Entity Framework è un valido esempio di principio secco. Le regole di convalida vengono specificate in modo dichiarativo in un'unica posizione (nella classe del modello) e le regole vengono applicate ovunque nell'app.

## <a name="add-validation-rules-to-the-movie-model"></a>Aggiungere regole di convalida al modello movie

Lo spazio dei nomi DataAnnotations offre un set di attributi di convalida predefiniti che vengono applicati in modo dichiarativo a una classe o una proprietà. DataAnnotations contiene anche gli attributi di formattazione come `DataType` che guidano nella formattazione e non offrono alcuna convalida.

Aggiornare la classe `Movie` per poter sfruttare gli attributi di convalida `Required`, `StringLength`, `RegularExpression` e `Range` predefiniti.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

Gli attributi di convalida specificano il comportamento da implementare nelle proprietà del modello a cui vengono applicati:

* Gli attributi `Required` e `MinimumLength` indicano che una proprietà deve avere un valore, ma nulla impedisce all'utente di inserire spazi vuoti per soddisfare questa convalida.
* L'attributo `RegularExpression` viene usato per limitare i caratteri che possono essere inseriti. Nel codice precedente "Genre":

  * Deve includere solo lettere.
  * La prima lettera deve essere maiuscola. Gli spazi, i numeri e i caratteri speciali non sono consentiti.

* `RegularExpression` "Rating":

  * Richiede che il primo carattere sia una lettera maiuscola.
  * Consente i caratteri speciali e i numeri negli spazi successivi. "PG-13" è valido per una classificazione, ma non per "Genre".

* L'attributo `Range` vincola un valore all'interno di un intervallo specificato.
* L'attributo `StringLength` consente di impostare la lunghezza massima di una proprietà stringa e, facoltativamente, la lunghezza minima.
* I tipi di valore, ad esempio `decimal`, `int`, `float` e `DateTime`, sono intrinsecamente necessari e non richiedono l'attributo `[Required]`.

L'applicazione automatica di regole di convalida da ASP.NET Core è utile per rendere un'app più solida. In questo modo inoltre non è possibile omettere la convalida di un elemento e quindi inserire involontariamente dati errati nel database.

### <a name="validation-error-ui-in-razor-pages"></a>Interfaccia utente degli errori di convalida nelle Razor pagine

Eseguire l'app e passare a Pages/Movies.

Selezionare il collegamento **Crea nuovo**. Completare il modulo con alcuni valori non validi. Quando la convalida del lato client jQuery rileva l'errore, viene visualizzato un messaggio di errore.

![Il modulo di vista del film con più errori di convalida del lato client jQuery](validation/_static/val.png)

[!INCLUDE[](~/includes/localization/currency.md)]

Si noti come il modulo ha eseguito automaticamente il rendering di un messaggio di errore di convalida in ogni campo che contiene un valore non valido. Gli errori vengono applicati sia sul lato client (uso di JavaScript e jQuery) sia sul lato server (quando un utente ha JavaScript disabilitato).

Un vantaggio significativo è che non c'era **nessuna** modifica del codice necessaria nelle pagine Create o Edit. Una volta che le DataAnnotations sono state applicate al modello, è stata abilitata la convalida dell'interfaccia utente. Le Razor pagine create in questa esercitazione hanno selezionato automaticamente le regole di convalida (usando gli attributi di convalida delle proprietà della `Movie` classe del modello). Convalida del test tramite la pagina Modifica, viene applicata la stessa convalida.

I dati del modulo non vengono registrati nel server fino a quando non sono presenti errori di convalida nel lato client. Verificare che i dati del modulo non siano stati registrati da uno o più degli approcci seguenti:

* Inserire un punto di interruzione nel metodo `OnPostAsync`. Inviare il modulo (selezionare **Crea** o **Salva**). Il punto di interruzione non viene mai raggiunto.
* Usare lo [Strumento Fiddler](https://www.telerik.com/fiddler).
* Usare gli strumenti di sviluppo del browser per monitorare il traffico di rete.

### <a name="server-side-validation"></a>Convalida sul lato server

Quando JavaScript è disabilitato nel browser, l'invio del modulo con errori verrà inoltrato al server.

Facoltativo, convalida sul lato server del test:

* Disabilitare JavaScript nel browser. È possibile disabilitare JavaScript usando gli strumenti di sviluppo del browser. Se non è possibile disabilitare JavaScript nel browser, provare un altro browser.
* Impostare un punto di interruzione nel metodo `OnPostAsync` della pagina Crea o Modifica.
* Inviare un modulo con dati non validi.
* Verificare che lo stato del modello non sia valido:

  ```csharp
   if (!ModelState.IsValid)
   {
      return Page();
   }
  ```
  
In alternativa, è possibile [disabilitare la convalida lato client sul server](xref:mvc/models/validation#disable-client-side-validation).

Il codice seguente mostra una parte della pagina *Create.cshtml* di cui è stato eseguito lo scaffolding in precedenza nell'esercitazione. Viene usato dalle pagine Create e Edit per visualizzare il modulo iniziale e per visualizzare nuovamente il modulo in caso di errore.

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=14-20)]

L'[helper tag di input](xref:mvc/views/working-with-forms) usa gli attributi [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) e produce gli attributi HTML necessari per la convalida jQuery sul lato client. L'[helper tag di convalida](xref:mvc/views/working-with-forms#the-validation-tag-helpers) visualizza gli errori di convalida. Per altre informazioni, vedere [Convalida](xref:mvc/models/validation).

Le pagine Create e Edit non dispongono di nessuna regola di convalida. Le regole di convalida e le stringhe di errore vengono specificate solo nella classe `Movie`. Queste regole di convalida vengono applicate automaticamente alle Razor pagine che modificano il `Movie` modello.

Quando la logica di convalida deve cambiare, avviene solo nel modello. La convalida viene applicata in modo coerente in tutta l'applicazione (la logica di convalida è definita in un'unica posizione). La convalida in un'unica posizione consente di mantenere il codice pulito e rende più semplice la gestione e l'aggiornamento.

## <a name="using-datatype-attributes"></a>Utilizzo degli attributi DataType

Esaminare la classe `Movie`. Lo spazio dei nomi `System.ComponentModel.DataAnnotations` fornisce gli attributi di formattazione oltre al set predefinito di attributi di convalida. L'attributo `DataType` viene applicato alle proprietà `ReleaseDate` e `Price`.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie/Models/MovieDateRatingDA.cs?highlight=2,6&name=snippet2)]

Gli attributi `DataType` forniscono solo gli hint per far sì che il motore di vista formatti i dati (e fornisca gli attributi, ad esempio `<a>` per gli URL e `<a href="mailto:EmailAddress.com">` per la posta elettronica). Usare l'attributo `RegularExpression` per convalidare il formato dei dati. L'attributo `DataType` viene usato per specificare un tipo di dati che è più specifico del tipo intrinseco del database. Gli attributi `DataType` non sono gli attributi di convalida. Nell'applicazione di esempio, viene visualizzata solo la data, senza l'ora.

L'enumerazione `DataType` fornisce per molti tipi di dati, ad esempio Date, Time, PhoneNumber, Currency, EmailAddress e altro ancora. L'attributo `DataType` può anche consentire all'applicazione di fornire automaticamente le funzionalità specifiche del tipo. Ad esempio, è possibile creare un collegamento `mailto:` per `DataType.EmailAddress`. È possibile fornire un selettore di dati per `DataType.Date` nei browser che supportano HTML5. Gli attributi `DataType` generano attributi HTML5 `data-` (pronunciati *data dash*) che poi i browser HTML5 consumano. Gli attributi `DataType`**non** forniscono alcuna convalida.

`DataType.Date` non specifica il formato della data visualizzata. Per impostazione predefinita, il campo dei dati viene visualizzato in base ai formati predefiniti per il valore `CultureInfo` del server.

L'annotazione dei dati `[Column(TypeName = "decimal(18, 2)")]` è necessaria per consentire a Entity Framework Core di eseguire correttamente il mapping di `Price` nella valuta del database. Per altre informazioni, vedere [Tipi di dati](/ef/core/modeling/relational/data-types).

L'attributo `DisplayFormat` viene usato per specificare in modo esplicito il formato della data:

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
public DateTime ReleaseDate { get; set; }
```

L'impostazione `ApplyFormatInEditMode` specifica che deve essere applicata la formattazione quando il valore viene visualizzato per la modifica. Questo comportamento potrebbe non essere desiderato per alcuni campi. Ad esempio, in valori di valuta, è probabile che non si desideri il simbolo di valuta in modalità di modifica dell'interfaccia utente.

L'attributo `DisplayFormat` può essere usato da solo, ma in genere è consigliabile usare l'attributo `DataType`. L'attributo `DataType` fornisce la semantica dei dati anziché la modalità di esecuzione del rendering in una schermata e fornisce i seguenti vantaggi che non si ottengono con DisplayFormat:

* Il browser può abilitare le funzionalità HTML5, ad esempio visualizzare un controllo di calendario, il simbolo della valuta appropriato per le impostazioni locali, i collegamenti alla posta elettronica e così via.
* Per impostazione predefinita, il browser eseguirà il rendering dei dati usando il formato corretto in base alle impostazioni locali del sistema.
* L'attributo `DataType` può abilitare il framework di ASP.NET Core a scegliere il modello di campo corretto per il rendering dei dati. Il `DisplayFormat` se usato da solo usa il modello di stringa.

Nota: la convalida jQuery non funziona con l'attributo `Range` e con `DateTime`. Ad esempio, il codice seguente visualizzerà sempre un errore di convalida sul lato client, anche quando la data è compreso nell'intervallo specificato:

```csharp
[Range(typeof(DateTime), "1/1/1966", "1/1/2020")]
   ```

In genere non è consigliabile compilare date reali nei modelli, quindi l'uso dell'attributo `Range` e di `DateTime` è sconsigliato.

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

Per informazioni sulla distribuzione in Azure, vedere [esercitazione: compilare un'app ASP.NET Core in Azure con il database SQL](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb).

Grazie per aver completato questa introduzione alle Razor pagine. [Introduzione a Razor Pagine e EF Core](xref:data/ef-rp/intro) è un'ottima procedura per completare questa esercitazione.

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:mvc/views/working-with-forms>
* <xref:fundamentals/localization>
* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/views/tag-helpers/authoring>
* [Versione YouTube dell'esercitazione](https://youtu.be/b63m66eu7us)

> [!div class="step-by-step"]
> [Precedente: Aggiunta di un nuovo campo](xref:tutorials/razor-pages/new-field)
