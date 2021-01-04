---
title: Verpacken einer Azure Service Fabric-App
description: Erfahren Sie mehr über das Verpacken einer Azure Service Fabric-Anwendung und die Vorbereitung für die Bereitstellung in einem Cluster.
ms.topic: conceptual
ms.date: 2/23/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: 11a3fdd5dbaef53af321342952f786ed8119689c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021060"
---
# <a name="package-an-application"></a>Packen einer Anwendung

In diesem Artikel wird beschrieben, wie eine Service Fabric-Anwendung paketiert und für die Bereitstellung vorbereitet wird.

## <a name="package-layout"></a>Paketlayout

Das Anwendungsmanifest, ein oder mehrere Dienstmanifeste und andere erforderliche Paketdateien müssen in einem bestimmten Layout für die Bereitstellung in einem Service Fabric-Cluster angeordnet werden. Die Beispielmanifeste in diesem Artikel müssen in der folgenden Verzeichnisstruktur angeordnet werden:

```
tree /f .\MyApplicationType
```

```Output
D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat
```

Die Ordner werden nach den **Name** -Attributen der jeweils zugehörigen Elemente benannt. Wenn das Dienstmanifest beispielsweise zwei Codepakete mit den Namen **MyCodeA** und **MyCodeB** umfasst, enthalten zwei Ordner mit denselben Namen die jeweils erforderlichen Binärdateien für jedes Codepaket.

## <a name="use-setupentrypoint"></a>Verwenden von SetupEntryPoint

Gängige Szenarios für die Verwendung von **SetupEntryPoint** sind die Anforderung, eine ausführbare Datei vor dem Starten des Diensts auszuführen, sowie die Anforderung, einen Vorgang mit erhöhten Rechten auszuführen. Beispiel:

* Einrichten und Initialisieren von Umgebungsvariablen, die die ausführbare Datei des Diensts benötigt. Dies ist nicht auf Dateien beschränkt, die mit den Service Fabric-Programmiermodellen geschrieben wurden. „npm.exe“ benötigt beispielsweise einige Umgebungsvariablen, die zum Bereitstellen einer node.js-Anwendung konfiguriert wurden.
* Einrichten einer Zugriffssteuerung durch Installieren von Sicherheitszertifikaten.

Weitere Informationen zum Konfigurieren von **SetupEntryPoint** finden Sie unter [Konfigurieren der Richtlinie für einen Setupeinstiegspunkt für Dienste](service-fabric-application-runas-security.md).

<a id="Package-App"></a>

## <a name="configure"></a>Konfigurieren

### <a name="build-a-package-by-using-visual-studio"></a>Erstellen eines Pakets mit Visual Studio

Wenn Sie Ihre Anwendung mithilfe von Visual Studio erstellt haben, können Sie über die Option *Paket* automatisch ein Paket erstellen, das dem oben beschriebenen Layout entspricht.

Klicken Sie zum Erstellen eines Pakets im *Projektmappen-Explorer* mit der rechten Maustaste auf das Anwendungsprojekt, und wählen Sie **Paket** aus:

![Paketieren einer Anwendung mit Visual Studio][vs-package-command]

Nach Abschluss der Paketerstellung wird der Speicherort des Pakets im **Ausgabefenster** angezeigt. Die Paketerstellung erfolgt automatisch, wenn Sie die Anwendung in Visual Studio bereitstellen oder debuggen.

### <a name="build-a-package-by-command-line"></a>Erstellen eines Pakets über die Befehlszeile

Es ist auch möglich, Ihre Anwendung mit `msbuild.exe` programmgesteuert zu packen. Diese Datei wird auch von Visual Studio im Hintergrund ausgeführt, sodass die Ausgabe identisch ist.

```shell
D:\Temp> msbuild HelloWorld.sfproj /t:Package
```

## <a name="test-the-package"></a>Testen des Pakets

Sie können die Paketstruktur lokal über PowerShell mit dem [Test-ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps) -Befehl überprüfen.
Dieser Befehl sucht nach Problemen bei der Manifestanalyse und überprüft alle Verweise. Mit diesem Befehl wird nur die strukturelle Richtigkeit der Verzeichnisse und Dateien im Paket überprüft.
Es werden keine Inhalte der Code- oder Datenpakete überprüft, mit Ausnahme der Prüfung, ob alle erforderlichen Dateien vorhanden sind.

```powershell
Test-ServiceFabricApplicationPackage .\MyApplicationType
```

```Output
False
Test-ServiceFabricApplicationPackage : The EntryPoint MySetup.bat is not found.
FileName: C:\Users\servicefabric\AppData\Local\Temp\TestApplicationPackage_7195781181\nrri205a.e2h\MyApplicationType\MyServiceManifest\ServiceManifest.xml
```

Dieser Fehler gibt an, dass die Datei *MySetup.bat* , auf die im Dienstmanifest **SetupEntryPoint** verwiesen wird, im Codepaket fehlt. Nach dem Hinzufügen der fehlenden Datei wird die Überprüfung der Anwendung erfolgreich abgeschlossen:

```
tree /f .\MyApplicationType
```

```Output
D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │       MySetup.bat
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat
```

```powershell
Test-ServiceFabricApplicationPackage .\MyApplicationType
```

```Output
True
```

Wenn [Anwendungsparameter](service-fabric-manage-multiple-environment-app-configuration.md) in Ihrer Anwendung definiert sind, können Sie diese zwecks ordnungsgemäßer Überprüfung an [Test ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps) übergeben.

Wenn Sie wissen, in welchem Cluster die Anwendung bereitgestellt wird, empfiehlt es sich, diese Angabe im Parameter `ImageStoreConnectionString` zu übergeben. In diesem Fall wird das Paket auch anhand älterer Versionen der Anwendung, die bereits im Cluster ausgeführt werden, überprüft. Beispielsweise kann bei der Überprüfung festgestellt werden, ob bereits ein Paket mit derselben Version, jedoch mit einem anderen Inhalt bereitgestellt wurde.  

Sobald die Anwendung ordnungsgemäß gepackt ist und die Überprüfung bestanden hat, können Sie das Paket komprimieren, um Bereitstellungsvorgänge zu beschleunigen.

## <a name="compress-a-package"></a>Komprimieren eines Pakets

Wenn ein Paket groß ist oder viele Dateien enthält, können Sie es zur schnelleren Bereitstellung komprimieren. Durch eine Komprimierung werden die Anzahl der Dateien und die Größe des Pakets verringert.
Bei einem komprimierten Anwendungspaket kann das [Hochladen des Anwendungspakets](service-fabric-deploy-remove-applications.md#upload-the-application-package) im Vergleich zum Hochladen des nicht komprimierten Pakets länger dauern. Dies gilt insbesondere dann, wenn die Komprimierung im Rahmen des Kopiervorgangs erfolgt. Mit Komprimierung verläuft die [Registrierung](service-fabric-deploy-remove-applications.md#register-the-application-package) und die [Aufhebung der Registrierung des Anwendungstyps](service-fabric-deploy-remove-applications.md#unregister-an-application-type) schneller.

Für komprimierte und nicht komprimierte Pakete gilt dasselbe Bereitstellungsverfahren. Wenn das Paket komprimiert ist, wird es als solches im Clusterabbildspeicher gespeichert und vor Ausführung der Anwendung auf dem Knoten dekomprimiert.
Bei der Komprimierung wird das gültige Service Fabric-Paket durch die komprimierte Version ersetzt. Der Ordner muss Schreibberechtigungen zulassen. Wird ein bereits komprimiertes Paket komprimiert, werden keine Änderungen vorgenommen.

Sie können ein Paket komprimieren, indem Sie den Powershell-Befehl [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) mit dem Schalter `CompressPackage` ausführen. Das Paket kann anhand desselben Befehls mit dem Schalter `UncompressPackage` dekomprimiert werden.

Mit dem folgenden Befehl wird das Paket komprimiert, ohne dass es im Abbildspeicher kopiert wird. Mit [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) ohne `SkipCopy`-Flag können Sie ein komprimiertes Paket bei Bedarf in ein oder mehrere Service Fabric-Cluster kopieren.
Das Paket enthält nun ZIP-Dateien für die Pakete `code`, `config` und `data` . Das Anwendungsmanifest und die Dienstmanifeste werden nicht gezippt, da sie für viele interne Vorgänge erforderlich sind. Beispielsweise muss bei der Paketfreigabe und der Extraktion des Namens und der Version von Anwendungstypen für bestimmte Überprüfungen immer auf die Manifeste zugegriffen werden. Eine Komprimierung der Manifeste würde dazu führen, dass diese Vorgänge unwirksam werden.

```
tree /f .\MyApplicationType
```

```Output
D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │       MySetup.bat
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat
```

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\MyApplicationType -CompressPackage -SkipCopy
tree /f .\MyApplicationType
```

```Output
D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
       ServiceManifest.xml
       MyCode.zip
       MyConfig.zip
       MyData.zip

```

Alternativ können Sie das Paket in einem Schritt mit [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) komprimieren und kopieren.
Wenn es sich um ein großes Paket handelt, legen Sie ein ausreichend langes Timeout fest, um Zeit für die Komprimierung des Pakets und den Upload in das Cluster bereitzustellen.

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\MyApplicationType -ApplicationPackagePathInImageStore MyApplicationType -ImageStoreConnectionString fabric:ImageStore -CompressPackage -TimeoutSec 5400
```

Intern berechnet das Service Fabric-Paket zur Überprüfung Prüfsummen für die Anwendungspakete. Bei der Komprimierung werden die Prüfsummen für die komprimierten Versionen der einzelnen Pakete berechnet. Beim Generieren einer neuen ZIP-Datei aus demselben Anwendungspaket werden andere Prüfsummen erstellt. Um Überprüfungsfehler zu vermeiden, verwenden Sie die [Diff-Bereitstellung](service-fabric-application-upgrade-advanced.md). Bei dieser Option dürfen keine unveränderten Pakete in der neuen Version enthalten sein. Stattdessen verweisen sie direkt aus dem neuen Dienstmanifest darauf.

Wenn die Diff-Bereitstellung nicht verwendet werden kann und Sie die Pakete einschließen müssen, generieren Sie für die `code`-, `config`- und `data`-Pakete neue Versionen, um Prüfsummenkonflikte zu vermeiden. Bei Verwendung eines komprimierten Pakets müssen für unveränderte Pakete neue Versionen generiert werden. Dies gilt unabhängig davon, ob für die Vorgängerversion eine Komprimierung verwendet wurde oder nicht.

Das Paket ist nun ordnungsgemäß verpackt, überprüft und ggf. komprimiert. Somit ist es für die [Bereitstellung](service-fabric-deploy-remove-applications.md) in einem oder in mehreren Service Fabric-Clustern bereit.

### <a name="compress-packages-when-deploying-using-visual-studio"></a>Komprimieren Sie Pakete, wenn Sie mit Visual Studio bereitstellen

Sie können Visual Studio anweisen, Pakete bei der Bereitstellung zu komprimieren, indem Sie das `CopyPackageParameters`-Element an ihr Veröffentlichungsprofil senden und das `CompressPackage`-Attribut auf `true` festlegen.

``` xml
    <PublishProfile xmlns="http://schemas.microsoft.com/2015/05/fabrictools">
        <ClusterConnectionParameters ConnectionEndpoint="mycluster.westus.cloudapp.azure.com" />
        <ApplicationParameterFile Path="..\ApplicationParameters\Cloud.xml" />
        <CopyPackageParameters CompressPackage="true"/>
    </PublishProfile>
```

## <a name="create-an-sfpkg"></a>Erstellen einer SFPKG-Datei

Ab Version 6.1 unterstützt Service Fabric die Bereitstellung aus einem externen Speicher.
Bei dieser Option muss das Anwendungspaket nicht in den Imagespeicher kopiert werden. Stattdessen können Sie eine `sfpkg`-Datei erstellen und in einen externen Speicher hochladen und dann bei der Bereitstellung für Service Fabric den Download-URI angeben. Dasselbe Paket kann für mehrere Cluster bereitgestellt werden. Die Bereitstellung aus dem externen Speicher spart die Zeit, die für das Kopieren des Pakets in jeden Cluster benötigt wird.

Die `sfpkg`-Datei ist eine ZIP-Datei, die das ursprüngliche Anwendungspaket enthält und die Erweiterung „.sfpkg“ aufweist.
Das Anwendungspaket innerhalb der ZIP-Datei kann komprimiert oder unkomprimiert sein. Die Komprimierung des Anwendungspakets innerhalb der ZIP-Datei erfolgt wie [zuvor erwähnt](service-fabric-package-apps.md#compress-a-package) auf Code-, Konfigurations- und Datenpaketebene.

Um eine `sfpkg` zu erstellen, beginnen Sie mit einem Ordner, der das ursprüngliche (komprimierte oder unkomprimierte) Anwendungspaket enthält. Verwenden Sie anschließend ein beliebiges Hilfsprogramm, um den Ordner mit der Erweiterung „.sfpkg“ zu zippen. Verwenden Sie z. B. [ZipFile.CreateFromDirectory](/dotnet/api/system.io.compression.zipfile.createfromdirectory?view=netcore-3.1#System_IO_Compression_ZipFile_CreateFromDirectory_System_String_System_String_System_IO_Compression_CompressionLevel_System_Boolean_).

```csharp
ZipFile.CreateFromDirectory(appPackageDirectoryPath, sfpkgFilePath);
```

Die `sfpkg` muss „Out-of-Band“, außerhalb von Service Fabric in den externen Speicher hochgeladen werden. Beim externen Speicher kann es sich um einen beliebigen Speicher handeln, der einen HTTP- oder HTTPS-REST-Endpunkt verfügbar macht. Während der Bereitstellung führt Service Fabric einen GET-Vorgang zum Herunterladen des `sfpkg`-Anwendungspakets aus. Daher muss der Speicher Lesezugriffe auf das Paket zulassen.

Verwenden Sie zum Bereitstellen des Pakets die externe Bereitstellung. Diese erfordert den Download-URI und die Informationen zum Anwendungstyp.

>[!NOTE]
> Bei der Bereitstellung auf Grundlage des relativen Pfads zum Imagespeicher werden derzeit keine `sfpkg`-Dateien unterstützt. Aus diesem Grund sollte die `sfpkg` nicht in den Imagespeicher kopiert werden.

## <a name="next-steps"></a>Nächste Schritte

[Bereitstellen und Entfernen von Anwendungen][10] beschreibt, wie Sie mit PowerShell Anwendungsinstanzen verwalten.

[Verwalten von Anwendungsparametern für mehrere Umgebungen][11] beschreibt, wie Parameter und Umgebungsvariablen für verschiedene Anwendungsinstanzen konfiguriert werden.

[Konfigurieren von Sicherheitsrichtlinien für Ihre Anwendung][12] beschreibt, wie Sie Dienste unter Sicherheitsrichtlinien ausführen, um den Zugriff einzuschränken.

<!--Image references-->
[vs-package-command]: ./media/service-fabric-package-apps/vs-package-command.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-deploy-remove-applications.md
[11]: service-fabric-manage-multiple-environment-app-configuration.md
[12]: service-fabric-application-runas-security.md
