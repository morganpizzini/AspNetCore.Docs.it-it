---
title: Documentazione dell'API Web di ASP.NET Core con spavalderia/OpenAPI
author: RicoSuter
description: Questa esercitazione offre una procedura dettagliata per aggiungere spavalderia per generare la documentazione e le pagine della Guida per un'app per le API Web.
ms.author: scaddie
ms.custom: mvc
ms.date: 10/29/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: tutorials/web-api-help-pages-using-swagger
ms.openlocfilehash: e5442c88048cf41e289fb476b4082cb6029b1b75
ms.sourcegitcommit: 0d40fc4932531ce13fc4ee9432144584e03c2f1c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93062454"
---
# <a name="aspnet-core-web-api-documentation-with-swagger--openapi"></a><span data-ttu-id="7c50d-103">Documentazione dell'API Web di ASP.NET Core con spavalderia/OpenAPI</span><span class="sxs-lookup"><span data-stu-id="7c50d-103">ASP.NET Core web API documentation with Swagger / OpenAPI</span></span>

<span data-ttu-id="7c50d-104">Di [Christoph Nienaber](https://twitter.com/zuckerthoben) e [Rico Suter](https://blog.rsuter.com/)</span><span class="sxs-lookup"><span data-stu-id="7c50d-104">By [Christoph Nienaber](https://twitter.com/zuckerthoben) and [Rico Suter](https://blog.rsuter.com/)</span></span>

<span data-ttu-id="7c50d-105">Spavalderia (OpenAPI) è una specifica indipendente dal linguaggio per la descrizione delle API REST.</span><span class="sxs-lookup"><span data-stu-id="7c50d-105">Swagger (OpenAPI) is a language-agnostic specification for describing REST APIs.</span></span> <span data-ttu-id="7c50d-106">Consente a entrambi i computer e gli utenti di comprendere le funzionalità di un'API REST senza accesso diretto al codice sorgente.</span><span class="sxs-lookup"><span data-stu-id="7c50d-106">It allows both computers and humans to understand the capabilities of a REST API without direct access to the source code.</span></span> <span data-ttu-id="7c50d-107">Gli obiettivi principali sono i seguenti:</span><span class="sxs-lookup"><span data-stu-id="7c50d-107">Its main goals are to:</span></span>

* <span data-ttu-id="7c50d-108">Ridurre al minimo la quantità di lavoro necessaria per connettere i servizi separati.</span><span class="sxs-lookup"><span data-stu-id="7c50d-108">Minimize the amount of work needed to connect decoupled services.</span></span>
* <span data-ttu-id="7c50d-109">Ridurre la quantità di tempo necessaria per documentare accuratamente un servizio.</span><span class="sxs-lookup"><span data-stu-id="7c50d-109">Reduce the amount of time needed to accurately document a service.</span></span>

<span data-ttu-id="7c50d-110">Le due implementazioni principali di OpenAPI per .NET sono [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) e [NSwag](https://github.com/RicoSuter/NSwag), vedere:</span><span class="sxs-lookup"><span data-stu-id="7c50d-110">The two main OpenAPI implementations for .NET are [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) and [NSwag](https://github.com/RicoSuter/NSwag), see:</span></span>

* [<span data-ttu-id="7c50d-111">Introduzione con Swashbuckle</span><span class="sxs-lookup"><span data-stu-id="7c50d-111">Getting Started with Swashbuckle</span></span>](xref:tutorials/get-started-with-swashbuckle)
* [<span data-ttu-id="7c50d-112">Introduzione con NSwag</span><span class="sxs-lookup"><span data-stu-id="7c50d-112">Getting Started with NSwag</span></span>](xref:tutorials/get-started-with-nswag)

## <a name="openapi-vs-swagger"></a><span data-ttu-id="7c50d-113">Confronto tra OpenApi e spavalderia</span><span class="sxs-lookup"><span data-stu-id="7c50d-113">OpenApi vs. Swagger</span></span>

<span data-ttu-id="7c50d-114">Il progetto spavalderia è stato donato all'iniziativa OpenAPI nel 2015 ed è stato definito come OpenAPI.</span><span class="sxs-lookup"><span data-stu-id="7c50d-114">The Swagger project was donated to the OpenAPI Initiative in 2015 and has since been referred to as OpenAPI.</span></span> <span data-ttu-id="7c50d-115">Entrambi i nomi vengono usati in modo interscambiabile.</span><span class="sxs-lookup"><span data-stu-id="7c50d-115">Both names are used interchangeably.</span></span> <span data-ttu-id="7c50d-116">Tuttavia, "OpenAPI" si riferisce alla specifica.</span><span class="sxs-lookup"><span data-stu-id="7c50d-116">However, "OpenAPI" refers to the specification.</span></span> <span data-ttu-id="7c50d-117">"Spavalderia" si riferisce alla famiglia di prodotti open source e commerciali di SmartBear che funzionano con la specifica OpenAPI.</span><span class="sxs-lookup"><span data-stu-id="7c50d-117">"Swagger" refers to the family of open-source and commercial products from SmartBear that work with the OpenAPI Specification.</span></span> <span data-ttu-id="7c50d-118">I successivi prodotti open source, ad esempio [OpenAPIGenerator](https://github.com/OpenAPITools/openapi-generator), rientrano anche nel nome della famiglia di spavalderia, nonostante non venga rilasciato da SmartBear.</span><span class="sxs-lookup"><span data-stu-id="7c50d-118">Subsequent open-source products, such as [OpenAPIGenerator](https://github.com/OpenAPITools/openapi-generator), also fall under the Swagger family name, despite not being released by SmartBear.</span></span>

<span data-ttu-id="7c50d-119">In breve:</span><span class="sxs-lookup"><span data-stu-id="7c50d-119">In short:</span></span>

* <span data-ttu-id="7c50d-120">OpenAPI è una specifica.</span><span class="sxs-lookup"><span data-stu-id="7c50d-120">OpenAPI is a specification.</span></span>
* <span data-ttu-id="7c50d-121">Spavalderia è uno strumento che usa la specifica OpenAPI.</span><span class="sxs-lookup"><span data-stu-id="7c50d-121">Swagger is tooling that uses the OpenAPI specification.</span></span> <span data-ttu-id="7c50d-122">Ad esempio, OpenAPIGenerator e Swagger.</span><span class="sxs-lookup"><span data-stu-id="7c50d-122">For example, OpenAPIGenerator and SwaggerUI.</span></span>

## <a name="openapi-specification-openapijson"></a><span data-ttu-id="7c50d-123">Specifica OpenAPI (openapi.js)</span><span class="sxs-lookup"><span data-stu-id="7c50d-123">OpenAPI specification (openapi.json)</span></span>

<span data-ttu-id="7c50d-124">La specifica OpenAPI è un documento che descrive le funzionalità dell'API.</span><span class="sxs-lookup"><span data-stu-id="7c50d-124">The OpenAPI specification is a document that describes the capabilities of your API.</span></span> <span data-ttu-id="7c50d-125">Il documento è basato sulle annotazioni XML e attribute nei controller e nei modelli.</span><span class="sxs-lookup"><span data-stu-id="7c50d-125">The document is based on the XML and attribute annotations within the controllers and models.</span></span> <span data-ttu-id="7c50d-126">Si tratta della parte principale del flusso di OpenAPI e viene usata per guidare gli strumenti, ad esempio Swagger.</span><span class="sxs-lookup"><span data-stu-id="7c50d-126">It's the core part of the OpenAPI flow and is used to drive tooling such as SwaggerUI.</span></span> <span data-ttu-id="7c50d-127">Per impostazione predefinita, il nome è *openapi.json* .</span><span class="sxs-lookup"><span data-stu-id="7c50d-127">By default, it's named *openapi.json* .</span></span> <span data-ttu-id="7c50d-128">Di seguito è riportato un esempio di una specifica OpenAPI, ridotta per brevità:</span><span class="sxs-lookup"><span data-stu-id="7c50d-128">Here's an example of an OpenAPI specification, reduced for brevity:</span></span>

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

## <a name="swagger-ui"></a><span data-ttu-id="7c50d-129">Interfaccia utente di Swagger</span><span class="sxs-lookup"><span data-stu-id="7c50d-129">Swagger UI</span></span>

<span data-ttu-id="7c50d-130">L' [interfaccia utente di spavalderia](https://swagger.io/swagger-ui/) offre un'interfaccia utente basata sul Web che fornisce informazioni sul servizio, usando la specifica openapi generata.</span><span class="sxs-lookup"><span data-stu-id="7c50d-130">[Swagger UI](https://swagger.io/swagger-ui/) offers a web-based UI that provides information about the service, using the generated OpenAPI specification.</span></span> <span data-ttu-id="7c50d-131">Sia Swashbuckle sia NSwag includono una versione incorporata di Swagger UI che può essere ospitata nell'applicazione ASP.NET Core dell'utente con una chiamata di registrazione middleware.</span><span class="sxs-lookup"><span data-stu-id="7c50d-131">Both Swashbuckle and NSwag include an embedded version of Swagger UI, so that it can be hosted in your ASP.NET Core app using a middleware registration call.</span></span> <span data-ttu-id="7c50d-132">L'interfaccia utente Web è simile alla seguente:</span><span class="sxs-lookup"><span data-stu-id="7c50d-132">The web UI looks like this:</span></span>

![Interfaccia utente di Swagger](web-api-help-pages-using-swagger/_static/swagger-ui.png)

<span data-ttu-id="7c50d-134">Ogni metodo di azione pubblico nei controller può essere testato dall'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="7c50d-134">Each public action method in your controllers can be tested from the UI.</span></span> <span data-ttu-id="7c50d-135">Selezionare un nome di metodo per espandere la sezione.</span><span class="sxs-lookup"><span data-stu-id="7c50d-135">Select a method name to expand the section.</span></span> <span data-ttu-id="7c50d-136">Aggiungere i parametri necessari e selezionare **try it out** .</span><span class="sxs-lookup"><span data-stu-id="7c50d-136">Add any necessary parameters, and select **Try it out!** .</span></span>

![Esempio test GET di Swagger](web-api-help-pages-using-swagger/_static/get-try-it-out.png)

> [!NOTE]
> <span data-ttu-id="7c50d-138">La versione di Swagger UI usata per le schermate è la versione 2.</span><span class="sxs-lookup"><span data-stu-id="7c50d-138">The Swagger UI version used for the screenshots is version 2.</span></span> <span data-ttu-id="7c50d-139">Per un esempio con la versione 3, vedere l'[esempio Petstore](https://petstore.swagger.io/).</span><span class="sxs-lookup"><span data-stu-id="7c50d-139">For a version 3 example, see [Petstore example](https://petstore.swagger.io/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="7c50d-140">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="7c50d-140">Next steps</span></span>

* [<span data-ttu-id="7c50d-141">Introduzione a Swashbuckle</span><span class="sxs-lookup"><span data-stu-id="7c50d-141">Get started with Swashbuckle</span></span>](xref:tutorials/get-started-with-swashbuckle)
* [<span data-ttu-id="7c50d-142">Introduzione a NSwag</span><span class="sxs-lookup"><span data-stu-id="7c50d-142">Get started with NSwag</span></span>](xref:tutorials/get-started-with-nswag)
