---
title: Controllo delle versioni dei servizi gRPC
author: jamesnk
description: Scopri come versione dei servizi gRPC.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 01/09/2020
uid: grpc/versioning
ms.openlocfilehash: 9bd76009ba28a1abef25a98686afea6753d4a8f4
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664115"
---
# <a name="versioning-grpc-services"></a>Controllo delle versioni dei servizi gRPC

Di [James Newton-King](https://twitter.com/jamesnk)

Le nuove funzionalità aggiunte a un'app possono richiedere servizi gRPC forniti ai client per cambiare, a volte in modi imprevisti e dirompendo. Quando i servizi gRPC cambiano:

* Occorre considerare l'impatto delle modifiche sui clienti.
* Deve essere implementata una strategia di controllo delle versioni per supportare le modifiche.

## <a name="backwards-compatibility"></a>Compatibilità con le versioni precedenti

Il protocollo gRPC è progettato per supportare i servizi che cambiano nel tempo. In genere, le aggiunte ai servizi e ai metodi gRPC sono unificatori. Le modifiche non dirompete consentono ai client esistenti di continuare a lavorare senza modifiche. La modifica o l'eliminazione dei servizi gRPC sono modifiche di rilievo. Quando i servizi gRPC presentano modifiche di rilievo, i client che utilizzano tale servizio devono essere aggiornati e ridistribuiti.

Apportare modifiche non dirigorie a un servizio presenta una serie di vantaggi:Making un-breaking changes to a service has a number of benefits:

* I client esistenti continuano a essere eseguiti.
* Evita il lavoro necessario per notificare ai client le modifiche di rilievo e aggiornarle.
* È necessario documentare e gestire una sola versione del servizio.

### <a name="non-breaking-changes"></a>Modifiche che non causano un'interruzione

Queste modifiche sono uni-break a livello di protocollo gRPC e .NET livello binario.

* **Aggiunta di un nuovo servizio**
* **Aggiunta di un nuovo metodo a un servizioAdding a new method to a service**
* **Aggiunta di un campo a un messaggio** di richiesta: i campi aggiunti a un messaggio di richiesta vengono deserializzati con il valore [predefinito](https://developers.google.com/protocol-buffers/docs/proto3#default) sul server quando non vengono impostati. Per essere una modifica unificatore, il servizio deve avere esito positivo quando il nuovo campo non è impostato dai client meno recenti.
* **Aggiunta di un campo a un messaggio** di risposta: i campi aggiunti a un messaggio di risposta vengono deserializzati nella raccolta di campi [sconosciuti](https://developers.google.com/protocol-buffers/docs/proto3#unknowns) del messaggio nel client.
* **Aggiunta di un valore a un'enumerazione:** le enumerazioni vengono serializzate come valore numerico. I nuovi valori di enumerazione vengono deserializzati sul client nel valore enum senza un nome enum. Per essere una modifica unificatore, i client meno recenti devono essere eseguiti correttamente quando si riceve il nuovo valore di enumerazione.

### <a name="binary-breaking-changes"></a>Modifiche di rilievo binarie

Le modifiche seguenti sono unificatori a livello di protocollo gRPC, ma il client deve essere aggiornato se esegue l'aggiornamento al contratto *.proto* o all'assembly .NET del client più recente. La compatibilità binaria è importante se si prevede di pubblicare una libreria gRPC in NuGet.Binary compatibility is important if you plan to publish a gRPC library to NuGet.

* **Rimozione di un campo:** i valori da un campo rimosso vengono deserializzati nei [campi sconosciuti](https://developers.google.com/protocol-buffers/docs/proto3#unknowns)di un messaggio. Non si tratta di una modifica di interruzione del protocollo gRPC, ma il client deve essere aggiornato se esegue l'aggiornamento al contratto più recente. È importante che un numero di campo rimosso non venga riutilizzato accidentalmente in futuro. Per assicurarti che ciò non accada, specifica i numeri di campo e i nomi eliminati nel messaggio utilizzando la parola chiave [riservata](https://developers.google.com/protocol-buffers/docs/proto3#reserved) di Protobuf.
* **Ridenominazione di un messaggio:** i nomi dei messaggi non vengono in genere inviati in rete, pertanto non si tratta di una modifica di interruzione del protocollo gRPC. Il client dovrà essere aggiornato se esegue l'aggiornamento al contratto più recente. Una situazione in cui i nomi dei messaggi **vengono** inviati in rete è con [qualsiasi](https://developers.google.com/protocol-buffers/docs/proto3#any) campi, quando il nome del messaggio viene utilizzato per identificare il tipo di messaggio.
* **La modifica** `csharp_namespace` di csharp_namespace - La modifica modificherà lo spazio dei nomi dei tipi .NET generati. Non si tratta di una modifica di interruzione del protocollo gRPC, ma il client deve essere aggiornato se esegue l'aggiornamento al contratto più recente.

### <a name="protocol-breaking-changes"></a>Modifiche all'interruzione del protocollo

Gli elementi seguenti sono le modifiche di interruzione binaria e di protocollo:The following items are protocol and binary breaking changes:

* **Ridenominazione di un campo** - Con il contenuto Protobuf, i nomi dei campi vengono utilizzati solo nel codice generato. Il numero di campo viene utilizzato per identificare i campi sulla rete. La ridenominazione di un campo non è una modifica di violazione del protocollo per Protobuf. Tuttavia, se un server utilizza contenuto JSON, la ridenominazione di un campo è una modifica sostanziale.
* **Modifica di un tipo di dati di campo:** la modifica del tipo di dati di un campo in un [tipo incompatibile](https://developers.google.com/protocol-buffers/docs/proto3#updating) causerà errori durante la deserializzazione del messaggio. Anche se il nuovo tipo di dati è compatibile, è probabile che il client debba essere aggiornato per supportare il nuovo tipo se esegue l'aggiornamento al contratto più recente.
* **Modifica di un numero** di campo - Con i payload Protobuf, il numero di campo viene utilizzato per identificare i campi sulla rete.
* **Ridenominazione di un pacchetto, un servizio o un metodo:** gRPC utilizza il nome del pacchetto, il nome del servizio e il nome del metodo per compilare l'URL. Il client ottiene uno stato *UNIMPLEMENTED* dal server.
* **Rimozione di un servizio o di** un metodo: il client ottiene uno stato *UNIMPLEMENTED* dal server quando chiama il metodo rimosso.

### <a name="behavior-breaking-changes"></a>Modifiche all'interruzione del comportamento

Quando si apportano modifiche non dirompente, è inoltre necessario considerare se i client meno recenti possono continuare a lavorare con il nuovo comportamento del servizio. Ad esempio, l'aggiunta di un nuovo campo a un messaggio di richiesta:For example, adding a new field to a request message:

* Non è un cambiamento di violazione del protocollo.
* La restituzione di uno stato di errore sul server se il nuovo campo non è impostato rende una modifica sostanziale per i client precedenti.

La compatibilità del comportamento è determinata dal codice specifico dell'app.

## <a name="version-number-services"></a>Servizi con numero di versione

I servizi dovrebbero sforzarsi di rimanere compatibili con le versioni precedenti con i vecchi clienti. Le modifiche apportate all'app potrebbero richiedere modifiche di rilievo. Rompere i vecchi clienti e costringerli ad essere aggiornati insieme al servizio non è una buona esperienza utente. Un modo per mantenere la compatibilità con le versioni precedenti mentre si apportano modifiche di rilievo consiste nel pubblicare più versioni di un servizio.

gRPC supporta un [identificatore](https://developers.google.com/protocol-buffers/docs/proto3#packages) di pacchetto facoltativo, che funziona in modo molto simile a uno spazio dei nomi .NET. Infatti, `package` l'oggetto verrà utilizzato come spazio dei `option csharp_namespace` nomi .NET per i tipi .NET generati se non è impostato nel file *proto.* Il pacchetto può essere utilizzato per specificare un numero di versione per il servizio e i relativi messaggi:The package can be used to specify a version number for your service and its messages:

[!code-protobuf[](versioning/sample/greet.v1.proto?highlight=3)]

Il nome del pacchetto viene combinato con il nome del servizio per identificare un indirizzo del servizio. Un indirizzo del servizio consente a più versioni di un servizio di essere ospitate side-by-side:A service address allows multiple versions of a service to be hosted side-by-side:

* `greet.v1.Greeter`
* `greet.v2.Greeter`

Le implementazioni del servizio con controllo delle versioni sono registrate in *Startup.cs*:

```csharp
app.UseEndpoints(endpoints =>
{
    // Implements greet.v1.Greeter
    endpoints.MapGrpcService<GreeterServiceV1>();

    // Implements greet.v2.Greeter
    endpoints.MapGrpcService<GreeterServiceV2>();
});
```

L'inclusione di un numero di versione nel nome del pacchetto offre la possibilità di pubblicare una versione *v2* del servizio con modifiche di rilievo, continuando a supportare i client meno recenti che chiamano la versione *v1.* Dopo aver aggiornato i client per utilizzare il servizio *v2,* è possibile scegliere di rimuovere la versione precedente. Quando si pianifica la pubblicazione di più versioni di un servizio:When planning to publish multiple versions of a service:

* Evitare modifiche di rilievo se ragionevole.
* Non aggiornare il numero di versione a meno che non si apportano modifiche di rilievo.
* Aggiornare il numero di versione quando si apportano modifiche di rilievo.

La pubblicazione di più versioni di un servizio lo duplica. Per ridurre la duplicazione, è consigliabile spostare la logica di business dalle implementazioni del servizio a una posizione centralizzata che può essere riutilizzata dalle implementazioni vecchie e nuove:To reduce duplication, consider moving business logic from the service implementations to a centralized location that can be reused by the old and new implementations:

[!code-csharp[](versioning/sample/GreeterServiceV1.cs?highlight=10,19)]

I servizi e i messaggi generati con nomi di pacchetto diversi sono **di tipi .NET diversi.** Lo spostamento della logica di business in una posizione centralizzata richiede il mapping dei messaggi a tipi comuni.
