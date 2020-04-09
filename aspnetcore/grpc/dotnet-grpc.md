---
title: Gestire i riferimenti Protobuf con dotnet-grpc
author: juntaoluo
description: Scopri come aggiungere, aggiornare, rimuovere ed elencare i riferimenti Protobuf con lo strumento globale dotnet-grpc.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 10/17/2019
uid: grpc/dotnet-grpc
ms.openlocfilehash: 994597c854a95bb33de1686ab025cb3744cf6845
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667335"
---
# <a name="manage-protobuf-references-with-dotnet-grpc"></a>Gestire i riferimenti Protobuf con dotnet-grpc

A cura di [John Luo](https://github.com/juntaoluo)

`dotnet-grpc`è uno strumento globale .NET Core per la gestione dei riferimenti [Protobuf (*.proto*)](xref:grpc/basics#proto-file) all'interno di un progetto gRPC .NET. Lo strumento può essere utilizzato per aggiungere, aggiornare, rimuovere ed elencare i riferimenti Protobuf.

## <a name="installation"></a>Installazione

Per installare `dotnet-grpc` [.NET Core Global Tool](/dotnet/core/tools/global-tools), eseguire il comando seguente:

```dotnetcli
dotnet tool install -g dotnet-grpc
```

## <a name="add-references"></a>Aggiungere riferimenti

`dotnet-grpc`può essere utilizzato per aggiungere riferimenti `<Protobuf />` Protobuf come elementi al file *con estensione csproj:*

```xml
<Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
```

I riferimenti Protobuf vengono utilizzati per generare le risorse client e/o server di C. Lo `dotnet-grpc` strumento può:

* Creare un riferimento Protobuf dai file locali su disco.
* Creare un riferimento Protobuf da un file remoto specificato da un URL.
* Assicurarsi che le dipendenze corrette del pacchetto gRPC vengano aggiunte al progetto.

Ad esempio, `Grpc.AspNetCore` il pacchetto viene aggiunto a un'app Web.For example, the package is added to a web app. `Grpc.AspNetCore`contiene librerie server e client gRPC e il supporto degli strumenti. In alternativa, `Grpc.Net.Client` `Grpc.Tools` i `Google.Protobuf` pacchetti , e , che contengono solo le librerie client gRPC e il supporto degli strumenti, vengono aggiunti a un'app Console.

### <a name="add-file"></a>Aggiungere file

Il `add-file` comando viene utilizzato per aggiungere file locali su disco come riferimenti Protobuf. I percorsi dei file forniti:

* Può essere relativo alla directory corrente o ai percorsi assoluti.
* Può contenere caratteri jolly per [il file globbing](https://wikipedia.org/wiki/Glob_(programming))basato su modelli .

Se alcuni file si trovano `Link` all'esterno della directory del `Protos` progetto, viene aggiunto un elemento per visualizzare il file nella cartella in Visual Studio.

### <a name="usage"></a>Uso

```dotnetcli
dotnet grpc add-file [options] <files>...
```

#### <a name="arguments"></a>Argomenti

| Argomento | Descrizione |
|-|-|
| files | Riferimenti al file protobuf. Questi possono essere un percorso a glob per i file protobuf locali. |

#### <a name="options"></a>Opzioni

| Opzione breve | Opzione lunga | Descrizione |
|-|-|-|
| -p | --progetto | Percorso del file di progetto su cui operare. Se non viene specificato un file, il comando ne cerca uno nella directory corrente.
| -S | --servizi | Tipo di servizi gRPC che devono essere generati. Se `Default` viene `Both` specificato, viene utilizzato `Client` per i progetti Web e viene utilizzato per i progetti non Web. I valori `Both` `Client`accettati `None` `Server`sono , , `Default`, , .
| -i | --additional-import-dirs | Directory aggiuntive da utilizzare per la risoluzione delle importazioni per i file protobuf. Si tratta di un elenco di percorsi separati da punto e virgola.
| | --accesso | Modificatore di accesso da utilizzare per le classi generate in C. Il valore predefinito è `Public`. I valori accettati sono `Internal` e `Public`.

### <a name="add-url"></a>Aggiungere URL

Il `add-url` comando viene utilizzato per aggiungere un file remoto specificato da un URL di origine come riferimento Protobuf. È necessario specificare un percorso di file per specificare dove scaricare il file remoto. Il percorso del file può essere relativo alla directory corrente o a un percorso assoluto. Se il percorso del file è `Link` esterno alla directory del progetto, `Protos` viene aggiunto un elemento per visualizzare il file nella cartella virtuale in Visual Studio.

### <a name="usage"></a>Uso

```dotnetcli
dotnet-grpc add-url [options] <url>
```

#### <a name="arguments"></a>Argomenti

| Argomento | Descrizione |
|-|-|
| url | URL di un file protobuf remoto. |

#### <a name="options"></a>Opzioni

| Opzione breve | Opzione lunga | Descrizione |
|-|-|-|
| -o | --output | Specifica il percorso di download per il file protobuf remoto. Si tratta di un'opzione obbligatoria.
| -p | --progetto | Percorso del file di progetto su cui operare. Se non viene specificato un file, il comando ne cerca uno nella directory corrente.
| -S | --servizi | Tipo di servizi gRPC che devono essere generati. Se `Default` viene `Both` specificato, viene utilizzato `Client` per i progetti Web e viene utilizzato per i progetti non Web. I valori `Both` `Client`accettati `None` `Server`sono , , `Default`, , .
| -i | --additional-import-dirs | Directory aggiuntive da utilizzare per la risoluzione delle importazioni per i file protobuf. Si tratta di un elenco di percorsi separati da punto e virgola.
| | --accesso | Modificatore di accesso da utilizzare per le classi generate in C. Il valore predefinito è `Public`. I valori accettati sono `Internal` e `Public`.

## <a name="remove"></a>Rimuovere

Il `remove` comando viene utilizzato per rimuovere i riferimenti Protobuf dal file *con estensione csproj.* Il comando accetta gli argomenti di percorso e gli URL di origine come argomenti. Lo strumento:

* Rimuove solo il riferimento Protobuf.
* Non elimina il file *proto,* anche se è stato originariamente scaricato da un URL remoto.

### <a name="usage"></a>Uso

```dotnetcli
dotnet-grpc remove [options] <references>...
```

### <a name="arguments"></a>Argomenti

| Argomento | Descrizione |
|-|-|
| references | Gli URL o i percorsi dei file dei riferimenti protobuf da rimuovere. |

### <a name="options"></a>Opzioni

| Opzione breve | Opzione lunga | Descrizione |
|-|-|-|
| -p | --progetto | Percorso del file di progetto su cui operare. Se non viene specificato un file, il comando ne cerca uno nella directory corrente.

## <a name="refresh"></a>Aggiorna

Il `refresh` comando viene utilizzato per aggiornare un riferimento remoto con il contenuto più recente dall'URL di origine. Sia il percorso del file di download che l'URL di origine possono essere utilizzati per specificare il riferimento da aggiornare. Nota:

* Gli iste del contenuto del file vengono confrontati per determinare se il file locale deve essere aggiornato.
* Non viene confrontata alcuna informazione timestamp.

Lo strumento sostituisce sempre il file locale con il file remoto se è necessario un aggiornamento.

### <a name="usage"></a>Uso

```dotnetcli
dotnet-grpc refresh [options] [<references>...]
```

### <a name="arguments"></a>Argomenti

| Argomento | Descrizione |
|-|-|
| references | GLI URL o i percorsi dei file ai riferimenti protobuf remoti che devono essere aggiornati. Lasciare vuoto questo argomento per aggiornare tutti i riferimenti remoti. |

### <a name="options"></a>Opzioni

| Opzione breve | Opzione lunga | Descrizione |
|-|-|-|
| -p | --progetto | Percorso del file di progetto su cui operare. Se non viene specificato un file, il comando ne cerca uno nella directory corrente.
| | --secco-run | Genera un elenco di file che verrebbero aggiornati senza scaricare alcun nuovo contenuto.

## <a name="list"></a>Elenco

Il `list` comando viene utilizzato per visualizzare tutti i riferimenti Protobuf nel file di progetto. Se tutti i valori di una colonna sono valori predefiniti, la colonna può essere omessa.

### <a name="usage"></a>Uso

```dotnetcli
dotnet-grpc list [options]
```

### <a name="options"></a>Opzioni

| Opzione breve | Opzione lunga | Descrizione |
|-|-|-|
| -p | --progetto | Percorso del file di progetto su cui operare. Se non viene specificato un file, il comando ne cerca uno nella directory corrente.

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
