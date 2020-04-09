## <a name="share-interop-code-in-a-class-library"></a>Condividere il codice di interoperabilità in una libreria di classiShare interop code in a class library

Il codice di interoperabilità JS può essere incluso in una libreria di classi, che consente di condividere il codice in un pacchetto NuGet.JS interop code can be included in a class library, which allows you to share the code in a NuGet package.

La libreria di classi gestisce l'incorporamento di risorse JavaScript nell'assembly compilato. I file JavaScript vengono inseriti nella cartella *wwwroot.* Gli strumenti si occupano dell'incorporamento delle risorse quando viene compilata la libreria.

Al pacchetto NuGet compilato viene fatto riferimento nel file di progetto dell'app nello stesso modo in cui viene fatto riferimento a qualsiasi pacchetto NuGet.The built NuGet package is referenced in the app's project file the same way that any NuGet package is referenced. Dopo il ripristino del pacchetto, il codice dell'app può chiamare in JavaScript come se fosse C .

Per altre informazioni, vedere <xref:blazor/class-libraries>.
