---
title: Documentazione dell'API Web di ASP.NET Core con spavalderia/OpenAPI
author: RicoSuter
description: Questa esercitazione offre una procedura dettagliata per aggiungere spavalderia per generare la documentazione e le pagine della Guida per un'app per le API Web.
ms.author: scaddie
ms.custom: mvc
ms.date: 10/29/2020
no-loc:
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
uid: tutorials/web-api-help-pages-using-swagger
ms.openlocfilehash: e5442c88048cf41e289fb476b4082cb6029b1b75
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2021
ms.locfileid: "93062454"
---
# <a name="aspnet-core-web-api-documentation-with-swagger--openapi"></a>Documentazione dell'API Web di ASP.NET Core con spavalderia/OpenAPI

Di [Christoph Nienaber](https://twitter.com/zuckerthoben) e [Rico Suter](https://blog.rsuter.com/)

Spavalderia (OpenAPI) è una specifica indipendente dal linguaggio per la descrizione delle API REST. Consente a entrambi i computer e gli utenti di comprendere le funzionalità di un'API REST senza accesso diretto al codice sorgente. Gli obiettivi principali sono i seguenti:

* Ridurre al minimo la quantità di lavoro necessaria per connettere i servizi separati.
* Ridurre la quantità di tempo necessaria per documentare accuratamente un servizio.

Le due implementazioni principali di OpenAPI per .NET sono [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) e [NSwag](https://github.com/RicoSuter/NSwag), vedere:

* [Introduzione con Swashbuckle](xref:tutorials/get-started-with-swashbuckle)
* [Introduzione con NSwag](xref:tutorials/get-started-with-nswag)

## <a name="openapi-vs-swagger"></a>Confronto tra OpenApi e spavalderia

Il progetto spavalderia è stato donato all'iniziativa OpenAPI nel 2015 ed è stato definito come OpenAPI. Entrambi i nomi vengono usati in modo interscambiabile. Tuttavia, "OpenAPI" si riferisce alla specifica. "Spavalderia" si riferisce alla famiglia di prodotti open source e commerciali di SmartBear che funzionano con la specifica OpenAPI. I successivi prodotti open source, ad esempio [OpenAPIGenerator](https://github.com/OpenAPITools/openapi-generator), rientrano anche nel nome della famiglia di spavalderia, nonostante non venga rilasciato da SmartBear.

In breve:

* OpenAPI è una specifica.
* Spavalderia è uno strumento che usa la specifica OpenAPI. Ad esempio, OpenAPIGenerator e Swagger.

## <a name="openapi-specification-openapijson"></a>Specifica OpenAPI (openapi.js)

La specifica OpenAPI è un documento che descrive le funzionalità dell'API. Il documento è basato sulle annotazioni XML e attribute nei controller e nei modelli. Si tratta della parte principale del flusso di OpenAPI e viene usata per guidare gli strumenti, ad esempio Swagger. Per impostazione predefinita, il nome è *openapi.json*. Di seguito è riportato un esempio di una specifica OpenAPI, ridotta per brevità:

```json
{
  "openapi": "3.0.1",
  "info": {
    "title": "API V1",
    "version": "v1"
  },
  "paths": {
    "/api/Todo": {
      "get": {
        "tags": [
          "Todo"
        ],
        "operationId": "ApiTodoGet",
        "responses": {
          "200": {
            "description": "Success",
            "content": {
              "text/plain": {
                "schema": {
                  "type": "array",
                  "items": {
                    "$ref": "#/components/schemas/ToDoItem"
                  }
                }
              },
              "application/json": {
                "schema": {
                  "type": "array",
                  "items": {
                    "$ref": "#/components/schemas/ToDoItem"
                  }
                }
              },
              "text/json": {
                "schema": {
                  "type": "array",
                  "items": {
                    "$ref": "#/components/schemas/ToDoItem"
                  }
                }
              }
            }
          }
        }
      },
      "post": {
        …
      }
    },
    "/api/Todo/{id}": {
      "get": {
        …
      },
      "put": {
        …
      },
      "delete": {
        …
      }
    }
  },
  "components": {
    "schemas": {
      "ToDoItem": {
        "type": "object",
        "properties": {
          "id": {
            "type": "integer",
            "format": "int32"
          },
          "name": {
            "type": "string",
            "nullable": true
          },
          "isCompleted": {
            "type": "boolean"
          }
        },
        "additionalProperties": false
      }
    }
  }
}
```

## <a name="swagger-ui"></a>Interfaccia utente di Swagger

L' [interfaccia utente di spavalderia](https://swagger.io/swagger-ui/) offre un'interfaccia utente basata sul Web che fornisce informazioni sul servizio, usando la specifica openapi generata. Sia Swashbuckle sia NSwag includono una versione incorporata di Swagger UI che può essere ospitata nell'applicazione ASP.NET Core dell'utente con una chiamata di registrazione middleware. L'interfaccia utente Web è simile alla seguente:

![Interfaccia utente di Swagger](web-api-help-pages-using-swagger/_static/swagger-ui.png)

Ogni metodo di azione pubblico nei controller può essere testato dall'interfaccia utente. Selezionare un nome di metodo per espandere la sezione. Aggiungere i parametri necessari e selezionare **try it out**.

![Esempio test GET di Swagger](web-api-help-pages-using-swagger/_static/get-try-it-out.png)

> [!NOTE]
> La versione di Swagger UI usata per le schermate è la versione 2. Per un esempio con la versione 3, vedere l'[esempio Petstore](https://petstore.swagger.io/).

## <a name="next-steps"></a>Passaggi successivi

* [Introduzione a Swashbuckle](xref:tutorials/get-started-with-swashbuckle)
* [Introduzione a NSwag](xref:tutorials/get-started-with-nswag)
