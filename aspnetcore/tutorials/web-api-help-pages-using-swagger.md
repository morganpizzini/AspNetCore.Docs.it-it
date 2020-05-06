---
title: Pagine della Guida dell'API Web ASP.NET Core con Swagger/OpenAPI
author: RicoSuter
description: In questa esercitazione viene descritta una procedura dettagliata per aggiungere Swagger e generare la documentazione e le pagine della Guida di un'app API Web.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/07/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/web-api-help-pages-using-swagger
ms.openlocfilehash: bde38fcbc11ef36c42523acb182fc62a934821c3
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774522"
---
# <a name="aspnet-core-web-api-help-pages-with-swagger--openapi"></a>Pagine della Guida dell'API Web ASP.NET Core con Swagger/OpenAPI

Di [Christoph Nienaber](https://twitter.com/zuckerthoben) e [Rico Suter](https://blog.rsuter.com/)

Quando si usa un'API Web, la comprensione dei vari metodi che la compongono può risultare complessa per gli sviluppatori. [Swagger](https://swagger.io/), detto anche [OpenAPI](https://www.openapis.org/), risolve il problema della generazione di pagine della Guida e della documentazione utili per le API Web. Offre vantaggi quali la documentazione interattiva, la generazione di SDK client e l'individuabilità delle API.

Questo articolo illustra le implementazioni .NET di Swagger [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) e [NSwag](https://github.com/RicoSuter/NSwag):

* **Swashbuckle.AspNetCore** è un progetto open source per la generazione di documenti Swagger per le API Web di ASP.NET Core.

* **NSwag** è un altro progetto open source per la generazione di documenti di Swagger e l'integrazione dell'[interfaccia utente di Swagger](https://swagger.io/swagger-ui/) o di [ReDoc](https://github.com/Rebilly/ReDoc) nelle API Web di ASP.NET Core. Inoltre, NSwag offre diversi modi per generare codice client C# e TypeScript per l'API.

## <a name="what-is-swagger--openapi"></a>Che cos'è Swagger/OpenAPI?

Swagger è una specifica indipendente dal linguaggio per la descrizione delle API [REST](https://en.wikipedia.org/wiki/Representational_state_transfer). Il progetto Swagger è stato donato all'[iniziativa OpenAPI](https://www.openapis.org/), in cui è ora denominato OpenAPI. I nomi sono intercambiabili, ma è preferibile usare OpenAPI. Questo strumento consente a computer e utenti di comprendere le funzionalità di un servizio senza accedere direttamente all'implementazione (codice sorgente, accesso alla rete, documentazione). Un obiettivo è la riduzione al minimo della quantità di lavoro necessaria per la connessione di servizi non associati. Un altro obiettivo è la riduzione del tempo necessario per documentare in modo accurato un servizio.

## <a name="swagger-specification-swaggerjson"></a>Specifica Swagger (swagger.json)

Il nucleo del flusso di Swagger è la specifica Swagger, che per impostazione predefinita è un documento denominato *swagger.json*. La specifica viene generata dalla catena di strumenti Swagger (o da implementazioni di terzi della catena) a seconda del servizio. Descrive le funzionalità dell'API e come accedervi mediante HTTP. Gestisce Swagger UI ed è usata dalla catena di strumenti per abilitare l'individuazione e la generazione del codice client. Di seguito è riportato un esempio di specifica di Swagger, ridotto per ragioni di brevità:

```json
{
   "swagger": "2.0",
   "info": {
       "version": "v1",
       "title": "API V1"
   },
   "basePath": "/",
   "paths": {
       "/api/Todo": {
           "get": {
               "tags": [
                   "Todo"
               ],
               "operationId": "ApiTodoGet",
               "consumes": [],
               "produces": [
                   "text/plain",
                   "application/json",
                   "text/json"
               ],
               "responses": {
                   "200": {
                       "description": "Success",
                       "schema": {
                           "type": "array",
                           "items": {
                               "$ref": "#/definitions/TodoItem"
                           }
                       }
                   }
                }
           },
           "post": {
               ...
           }
       },
       "/api/Todo/{id}": {
           "get": {
               ...
           },
           "put": {
               ...
           },
           "delete": {
               ...
   },
   "definitions": {
       "TodoItem": {
           "type": "object",
            "properties": {
                "id": {
                    "format": "int64",
                    "type": "integer"
                },
                "name": {
                    "type": "string"
                },
                "isComplete": {
                    "default": false,
                    "type": "boolean"
                }
            }
       }
   },
   "securityDefinitions": {}
}
```

## <a name="swagger-ui"></a>Interfaccia utente di Swagger

[Swagger UI](https://swagger.io/swagger-ui/) offre un'interfaccia utente basata sul web che produce informazioni sul servizio usando la specifica di Swagger generata. Sia Swashbuckle sia NSwag includono una versione incorporata di Swagger UI che può essere ospitata nell'applicazione ASP.NET Core dell'utente con una chiamata di registrazione middleware. L'interfaccia utente Web è simile alla seguente:

![Interfaccia utente di Swagger](web-api-help-pages-using-swagger/_static/swagger-ui.png)

Ogni metodo di azione pubblico nei controller può essere testato dall'interfaccia utente. Fare clic su un nome di metodo per espandere la sezione. Aggiungere i parametri necessari e fare clic su **try it out**.

![Esempio test GET di Swagger](web-api-help-pages-using-swagger/_static/get-try-it-out.png)

> [!NOTE]
> La versione di Swagger UI usata per le schermate è la versione 2. Per un esempio con la versione 3, vedere l'[esempio Petstore](https://petstore.swagger.io/).

## <a name="next-steps"></a>Passaggi successivi

* [Introduzione a Swashbuckle](xref:tutorials/get-started-with-swashbuckle)
* [Introduzione a NSwag](xref:tutorials/get-started-with-nswag)
