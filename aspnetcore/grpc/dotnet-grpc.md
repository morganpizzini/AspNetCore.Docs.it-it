---
title: Gestire i riferimenti Protobuf con dotnet-grpc
author: juntaoluo
description: Informazioni sull'aggiunta, l'aggiornamento, la rimozione e l'elenco di riferimenti protobuf con lo strumento globale DotNet-grpc.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 10/17/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/dotnet-grpc
ms.openlocfilehash: 0990013947be2cee5045deac92efc3c6bcf12e03
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82768835"
---
# <a name="manage-protobuf-references-with-dotnet-grpc"></a>Gestire i riferimenti Protobuf con dotnet-grpc

A cura di [John Luo](https://github.com/juntaoluo)

`dotnet-grpc`è uno strumento globale .NET Core per la gestione dei riferimenti [protobuf (*. proto*)](xref:grpc/basics#proto-file) in un progetto .NET gRPC. Lo strumento può essere usato per aggiungere, aggiornare, rimuovere ed elencare i riferimenti a protobuf.

## <a name="installation"></a>Installazione

Per installare lo `dotnet-grpc` [strumento globale di .NET Core](/dotnet/core/tools/global-tools), eseguire il comando seguente:

```dotnetcli
dotnet tool install -g dotnet-grpc
```

## <a name="add-references"></a>Aggiungere riferimenti

`dotnet-grpc`può essere usato per aggiungere riferimenti protobuf come `<Protobuf />` elementi al file con *estensione csproj* :

```xml
<Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
```

I riferimenti protobuf vengono usati per generare il client C# e/o le risorse del server. Lo `dotnet-grpc` strumento consente di:

* Creare un riferimento protobuf da file locali su disco.
* Creare un riferimento protobuf da un file remoto specificato da un URL.
* Verificare che al progetto siano state aggiunte le dipendenze del pacchetto gRPC corrette.

Ad esempio, il `Grpc.AspNetCore` pacchetto viene aggiunto a un'app Web. `Grpc.AspNetCore`contiene le librerie client e server gRPC e il supporto degli strumenti. In alternativa, i `Grpc.Net.Client` `Grpc.Tools` pacchetti e, `Google.Protobuf` che contengono solo le librerie client e il supporto per gli strumenti di gRPC, vengono aggiunti a un'app console.

### <a name="add-file"></a>Aggiungere file

Il `add-file` comando viene usato per aggiungere file locali su disco come riferimenti a protobuf. I percorsi dei file forniti:

* Può essere relativo alla directory corrente o ai percorsi assoluti.
* Può contenere caratteri jolly per i file basati su pattern [glob](https://wikipedia.org/wiki/Glob_(programming)).

Se i file si trovano all'esterno della directory del progetto, `Link` viene aggiunto un elemento per visualizzare il file `Protos` nella cartella in Visual Studio.

### <a name="usage"></a>Utilizzo

```dotnetcli
dotnet grpc add-file [options] <files>...
```

#### <a name="arguments"></a>Arguments

| Argomento | Description |
|-|-|
| files | Il file protobuf fa riferimento a. Può trattarsi di un percorso di glob per i file protobuf locali. |

#### <a name="options"></a>Opzioni

| Short-opzione | Opzione Long | Description |
|-|-|-|
| -p | --progetto | Percorso del file di progetto su cui operare. Se non si specifica un file, il comando Cerca una directory corrente.
| -S | --Servizi | Tipo di servizi gRPC da generare. Se `Default` si specifica, `Both` viene utilizzato per i progetti Web e `Client` viene utilizzato per i progetti non Web. I valori accettati sono `Both` ,, `Client` `Default` , `None` , `Server` .
| -i | --Additional-Import-directory | Directory aggiuntive da usare quando si risolvono le importazioni per i file protobuf. Si tratta di un elenco di percorsi separati da punto e virgola.
| | --accesso | Modificatore di accesso da usare per le classi C# generate. Il valore predefinito è `Public`. I valori accettati sono `Internal` e `Public`.

### <a name="add-url"></a>Aggiungere URL

Il `add-url` comando viene usato per aggiungere un file remoto specificato da un URL di origine come riferimento protobuf. È necessario fornire un percorso di file per specificare dove scaricare il file remoto. Il percorso del file può essere relativo alla directory corrente o a un percorso assoluto. Se il percorso del file è esterno alla directory del progetto, `Link` viene aggiunto un elemento per visualizzare il file nella cartella virtuale `Protos` in Visual Studio.

### <a name="usage"></a>Utilizzo

```dotnetcli
dotnet-grpc add-url [options] <url>
```

#### <a name="arguments"></a>Arguments

| Argomento | Description |
|-|-|
| url | URL di un file protobuf remoto. |

#### <a name="options"></a>Opzioni

| Short-opzione | Opzione Long | Description |
|-|-|-|
| -o | --output | Specifica il percorso di download per il file protobuf remoto. Si tratta di un'opzione obbligatoria.
| -p | --progetto | Percorso del file di progetto su cui operare. Se non si specifica un file, il comando Cerca una directory corrente.
| -S | --Servizi | Tipo di servizi gRPC da generare. Se `Default` si specifica, `Both` viene utilizzato per i progetti Web e `Client` viene utilizzato per i progetti non Web. I valori accettati sono `Both` ,, `Client` `Default` , `None` , `Server` .
| -i | --Additional-Import-directory | Directory aggiuntive da usare quando si risolvono le importazioni per i file protobuf. Si tratta di un elenco di percorsi separati da punto e virgola.
| | --accesso | Modificatore di accesso da usare per le classi C# generate. Il valore predefinito è `Public`. I valori accettati sono `Internal` e `Public`.

## <a name="remove"></a>Rimuovi

Il `remove` comando viene usato per rimuovere i riferimenti protobuf dal file con *estensione csproj* . Il comando accetta gli argomenti del percorso e gli URL di origine come argomenti. Lo strumento:

* Rimuove solo il riferimento protobuf.
* Non elimina il file *. proto* , neanche se è stato originariamente scaricato da un URL remoto.

### <a name="usage"></a>Utilizzo

```dotnetcli
dotnet-grpc remove [options] <references>...
```

### <a name="arguments"></a>Arguments

| Argomento | Description |
|-|-|
| references | URL o percorsi di file dei riferimenti protobuf da rimuovere. |

### <a name="options"></a>Opzioni

| Short-opzione | Opzione Long | Description |
|-|-|-|
| -p | --progetto | Percorso del file di progetto su cui operare. Se non si specifica un file, il comando Cerca una directory corrente.

## <a name="refresh"></a>Aggiorna

Il `refresh` comando viene usato per aggiornare un riferimento remoto con il contenuto più recente dall'URL di origine. È possibile usare sia il percorso del file di download che l'URL di origine per specificare il riferimento da aggiornare. Nota:

* Gli hash del contenuto del file vengono confrontati per determinare se è necessario aggiornare il file locale.
* Nessuna informazione sul timestamp viene confrontata.

Se è necessario un aggiornamento, lo strumento sostituisce sempre il file locale con il file remoto.

### <a name="usage"></a>Utilizzo

```dotnetcli
dotnet-grpc refresh [options] [<references>...]
```

### <a name="arguments"></a>Arguments

| Argomento | Description |
|-|-|
| references | Gli URL o i percorsi di file per i riferimenti protobuf remoti che devono essere aggiornati. Lasciare vuoto questo argomento per aggiornare tutti i riferimenti remoti. |

### <a name="options"></a>Opzioni

| Short-opzione | Opzione Long | Description |
|-|-|-|
| -p | --progetto | Percorso del file di progetto su cui operare. Se non si specifica un file, il comando Cerca una directory corrente.
| | --esecuzione a secco | Restituisce un elenco di file che verrebbero aggiornati senza scaricare alcun nuovo contenuto.

## <a name="list"></a>Elenco

Il `list` comando viene usato per visualizzare tutti i riferimenti protobuf nel file di progetto. Se tutti i valori di una colonna sono valori predefiniti, è possibile omettere la colonna.

### <a name="usage"></a>Uso

```dotnetcli
dotnet-grpc list [options]
```

### <a name="options"></a>Opzioni

| Short-opzione | Opzione Long | Description |
|-|-|-|
| -p | --progetto | Percorso del file di progetto su cui operare. Se non si specifica un file, il comando Cerca una directory corrente.

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
