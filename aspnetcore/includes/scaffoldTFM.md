<span data-ttu-id="58f33-101">Se viene visualizzato un errore di scaffolding, verificare che il moniker (TFM) del framework di destinazione corrisponda alla versione del pacchetto NuGet nel file di progetto.</span><span class="sxs-lookup"><span data-stu-id="58f33-101">If you get a scaffolding error, verify the Target Framework Moniker (TFM) matches the NuGet package version in the project file.</span></span> <span data-ttu-id="58f33-102">Ad esempio, il file di progetto seguente contiene la versione 3.1 per .NET Core e i pacchetti NuGet elencati:</span><span class="sxs-lookup"><span data-stu-id="58f33-102">For example, the following project file contains the version 3.1 for .NET Core and the listed NuGet packages:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">

  <PropertyGroup>
    <TargetFramework>netcoreapp3.1</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore" Version="3.1.0" />
    <PackageReference Include="Microsoft.AspNetCore.Identity.EntityFrameworkCore" Version="3.1.0" />
    <PackageReference Include="Microsoft.AspNetCore.Identity.UI" Version="3.1.0" />
    <PackageReference Include="Microsoft.EntityFrameworkCore.Design" Version="3.1.0" />
    <PackageReference Include="Microsoft.EntityFrameworkCore.SqlServer" Version="3.1.0" />
    <PackageReference Include="Microsoft.EntityFrameworkCore.Tools" Version="3.1.0" />
    <PackageReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Design" Version="3.1.0" />
  </ItemGroup>

</Project>
```
