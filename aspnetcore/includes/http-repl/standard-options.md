* `-F|--no-formatting`

  Flag la cui presenza elimina la formattazione della risposta HTTP.

* `-h|--header`

  Imposta un'intestazione della richiesta HTTP. Sono supportati i due formati di valore seguenti:

  * `{header}={value}`
  * `{header}:{value}`

* `--response:body`

  Specifica un file in cui deve essere scritto il corpo della risposta HTTP. Ad esempio, `--response:body "C:\response.json"` Se il file non esiste, verrà creato.

* `--response:headers`

  Specifica un file in cui devono essere scritte le intestazioni della risposta HTTP. Ad esempio, `--response:headers "C:\response.txt"` Se il file non esiste, verrà creato.

* `-s|--streaming`

  Flag la cui presenza abilita il flusso della risposta HTTP.
