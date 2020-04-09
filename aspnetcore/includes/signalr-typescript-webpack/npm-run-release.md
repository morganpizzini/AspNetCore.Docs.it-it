```console
npm run release
```

Questo comando genera le risorse lato client da servire durante l'esecuzione dell'app. Le risorse si trovano nella cartella *wwwroot*.

Webpack ha completato le attività seguenti:

* Ha ripulito il contenuto della directory *wwwroot*.
* Convertete TypeScript in JavaScript in un processo noto come *transpilation*.
* Alterato il codice JavaScript generato per ridurre le dimensioni del file in un processo noto come *minimizzazione*.
* Ha copiato i file JavaScript, CSS e HTML elaborati dalla directory *src* alla directory *wwwroot*.
* Ha inserito gli elementi seguenti nel file *wwwroot/index.html*:
  * Un tag `<link>` che fa riferimento al file *wwwroot/main.\<hash\>.css*. Questo tag viene inserito immediatamente prima del tag `</head>` di chiusura.
  * Un tag `<script>` che fa riferimento al file *wwwroot/main.\<hash\>.js* minimizzato. Questo tag viene inserito immediatamente prima del tag `</body>` di chiusura.