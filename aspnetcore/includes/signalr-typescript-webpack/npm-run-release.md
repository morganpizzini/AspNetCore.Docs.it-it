```console
npm run release
```

Questo comando genera gli asset lato client da servire durante l'esecuzione dell'app. Le risorse si trovano nella cartella *wwwroot*.

Webpack ha completato le attività seguenti:

* Ha ripulito il contenuto della directory *wwwroot*.
* Il TypeScript è stato convertito in JavaScript in un processo noto come *transpilazione*.
* Il codice JavaScript generato è stato modificato per ridurre le dimensioni del file in un processo noto come *minification*.
* Ha copiato i file JavaScript, CSS e HTML elaborati dalla directory *src* alla directory *wwwroot*.
* Ha inserito gli elementi seguenti nel file *wwwroot/index.html*:
  * Un `<link>` tag, che fa riferimento A *wwwroot/Main. \<hash\> . file CSS* . Questo tag viene inserito immediatamente prima del tag `</head>` di chiusura.
  * Un `<script>` tag, che fa riferimento A minimizzati *wwwroot/Main. \<hash\> . file js* . Questo tag viene inserito immediatamente prima del tag `</body>` di chiusura.