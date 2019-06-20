---
title: Azure Service Fabric-Anwendungsbereitstellung | Microsoft-Dokumentation
description: Bereitstellen und Entfernen von Anwendungen in Service Fabric mit PowerShell
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: b120ffbf-f1e3-4b26-a492-347c29f8f66b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/19/2018
ms.author: aljo
ms.openlocfilehash: f0f66cd32721e277cbd6e4578b0e58bb201ee966
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60393269"
---
# <a name="deploy-and-remove-applications-using-powershell"></a>Bereitstellen und Entfernen von Anwendungen mit PowerShell

> [!div class="op_single_selector"]
> * [Ressourcen-Manager](service-fabric-application-arm-resource.md)
> * [PowerShell](service-fabric-deploy-remove-applications.md)
> * [Service Fabric-Befehlszeilenschnittstelle](service-fabric-application-lifecycle-sfctl.md)
> * [FabricClient-APIs](service-fabric-deploy-remove-applications-fabricclient.md)

<br/>

Sobald der [Anwendungstyp gepackt][10] wurde, ist die Anwendung für die Bereitstellung in einem Azure Service Fabric-Cluster bereit. Die Bereitstellung umfasst die folgenden drei Schritte:

1. Hochladen des Anwendungspakets in den Imagespeicher
2. Registrieren des Anwendungstyps mit dem relativen Pfad des Imagespeichers
3. Erstellen der Anwendungsinstanz

Sobald die bereitgestellte Anwendung nicht mehr benötigt wird, können Sie die Anwendungsinstanz und deren Anwendungstyp löschen. Das vollständige Entfernen einer Anwendung aus dem Cluster umfasst die folgenden Schritte:

1. Entfernen (oder Löschen) der ausgeführten Anwendungsinstanz
2. Aufheben der Registrierung des Anwendungstyps, wenn er nicht mehr benötigt wird
3. Entfernen des Anwendungspakets aus dem Imagespeicher

Wenn Sie Visual Studio zum Bereitstellen und Debuggen von Anwendungen in Ihrem lokalen Entwicklungscluster verwenden, werden alle vorherigen Schritte automatisch über ein PowerShell-Skript ausgeführt.  Dieses Skript befindet sich im Ordner *Skripts* des Anwendungsprojekts. In diesem Artikel wird die grundlegende Funktionsweise dieses Skripts erläutert, sodass Sie die gleichen Vorgänge außerhalb von Visual Studio ausführen können. 

Eine weitere Möglichkeit zum Bereitstellen einer Anwendung besteht darin, eine externe Bereitstellung zu verwenden. Das Anwendungspaket kann [als `sfpkg` gepackt](service-fabric-package-apps.md#create-an-sfpkg) und in einen externen Speicher hochgeladen werden. In diesem Fall ist kein Upload in den Imagespeicher erforderlich. Für die Bereitstellung sind die folgenden Schritte erforderlich:

1. Laden Sie die `sfpkg` in einen externen Speicher hoch. Beim externen Speicher kann es sich um einen beliebigen Speicher handeln, der einen HTTP- oder HTTPS-REST-Endpunkt verfügbar macht.
2. Registrieren Sie den Anwendungstyp unter Verwendung des Download-URIs für den externen Speicher und der Typinformationen für die Anwendung.
2. Erstellen Sie die Anwendungsinstanz.

Entfernen Sie zur Bereinigung die Anwendungsinstanzen, und heben Sie die Registrierung des Anwendungstyps auf. Da das Paket nicht in den Imagespeicher kopiert wurde, muss kein temporärer Speicherort bereinigt werden. Ab Service Fabric, Version 6.1, ist eine Bereitstellung aus dem externen Speicher verfügbar.

>[!NOTE]
> Visual Studio unterstützt derzeit keine externe Bereitstellung.

 

## <a name="connect-to-the-cluster"></a>Verbinden mit dem Cluster

Bevor Sie die in diesem Artikel aufgeführten PowerShell-Befehle ausführen, stellen Sie immer zuerst über [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) eine Verbindung mit dem Service Fabric-Cluster her. Führen Sie zum Herstellen der Verbindung mit dem lokalen Entwicklungscluster den folgenden Befehl aus:

```powershell
Connect-ServiceFabricCluster
```

Beispiele für das Herstellen einer Verbindung mit einem Remotecluster bzw. einem mit Azure Active Directory, X509-Zertifikaten oder Windows Active Directory gesicherten Cluster finden Sie unter [Herstellen einer Verbindung mit einem sicheren Cluster](service-fabric-connect-to-secure-cluster.md).

## <a name="upload-the-application-package"></a>Hochladen des Anwendungspakets

Beim Hochladen des Anwendungspakets wird das Paket an einem Speicherort gespeichert, an dem interne Service Fabric-Komponenten auf das Paket zugreifen können.
Wenn Sie das Anwendungspaket lokal überprüfen möchten, verwenden Sie das Cmdlet [Test-ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps).

Mit dem Befehl [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) wird das Anwendungspaket in den Clusterimagespeicher hochgeladen.

Angenommen, Sie erstellen und verpacken eine Anwendung namens *MyApplication* in Visual Studio 2015. Der Name des Anwendungstyps ist in der Datei „ApplicationManifest.xml“ standardmäßig als „MyApplicationType“ aufgeführt.  Das Anwendungspaket mit den erforderlichen Anwendungs- und Dienstmanifesten sowie Code-/Konfigurations-/Datenpaketen befindet sich in *C:\Benutzer\<Benutzername\>\Eigene Dokumente\Visual Studio 2015\Projekte\MeineAnwendung\MeineAnwendung\pkg\Debuggen*. 

Mit dem folgenden Befehl werden die Inhalte des Anwendungspakets aufgelistet:

```powershell
$path = 'C:\Users\<user\>\Documents\Visual Studio 2015\Projects\MyApplication\MyApplication\pkg\Debug'
tree /f $path
```

```Output
Folder PATH listing for volume OSDisk
Volume serial number is 0459-2393
C:\USERS\USER\DOCUMENTS\VISUAL STUDIO 2015\PROJECTS\MYAPPLICATION\MYAPPLICATION\PKG\DEBUG
│   ApplicationManifest.xml
│
└───Stateless1Pkg
    │   ServiceManifest.xml
    │
    ├───Code
    │       Microsoft.ServiceFabric.Data.dll
    │       Microsoft.ServiceFabric.Data.Interfaces.dll
    │       Microsoft.ServiceFabric.Internal.dll
    │       Microsoft.ServiceFabric.Internal.Strings.dll
    │       Microsoft.ServiceFabric.Services.dll
    │       ServiceFabricServiceModel.dll
    │       Stateless1.exe
    │       Stateless1.exe.config
    │       Stateless1.pdb
    │       System.Fabric.dll
    │       System.Fabric.Strings.dll
    │
    └───Config
            Settings.xml
```

Wenn das Anwendungspaket groß ist und/oder viele Dateien enthält, können Sie es [komprimieren](service-fabric-package-apps.md#compress-a-package). Durch eine Komprimierung werden Größe und Anzahl der Dateien verringert.
Ein positiver Nebeneffekt dabei ist, dass die Registrierung und die Aufhebung der Registrierung des Anwendungstyps schneller erfolgen. Der Upload kann sich in diesem Zeitraum eventuell verlangsamen, insbesondere, wenn Sie die Zeit zur Komprimierung des Pakets berücksichtigen. 

Mit demselben Befehl [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) wird das Paket komprimiert. Die Komprimierung kann getrennt vom Upload mithilfe des Flags `SkipCopy` oder beim Uploadvorgang durchgeführt werden. Die Komprimierung eines komprimierten Pakets ist nicht möglich.
Mit demselben Befehl [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) mit dem Schalter `UncompressPackage` wird ein komprimiertes Paket dekomprimiert.

Mit dem folgenden Cmdlet wird das Paket komprimiert, ohne dass es im Abbildspeicher kopiert wird. Das Paket enthält nun ZIP-Dateien für die Pakete `Code` und `Config`. Die Anwendungs- und Dienstmanifeste werden nicht komprimiert, da sie für zahlreiche interne Vorgänge (z.B. Paketfreigabe, Extraktion des Namen und der Version des Anwendungstyps für bestimmte Überprüfungen) benötigt werden. Eine Komprimierung der Manifeste würde dazu führen, dass diese Vorgänge unwirksam werden.

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -CompressPackage -SkipCopy
tree /f $path
```

```Output
Folder PATH listing for volume OSDisk
Volume serial number is 0459-2393
C:\USERS\USER\DOCUMENTS\VISUAL STUDIO 2015\PROJECTS\MYAPPLICATION\MYAPPLICATION\PKG\DEBUG
|   ApplicationManifest.xml
|
└───Stateless1Pkg
       Code.zip
       Config.zip
       ServiceManifest.xml
```

Die Komprimierung erfordert bei großen Anwendungspaketen Zeit. Um optimale Ergebnisse zu erzielen, sollten Sie ein schnelles SSD-Laufwerk verwenden. Die Komprimierungszeit und die Größe des komprimierten Pakets variieren zudem je nach dem Inhalt des Pakets.
Im Folgenden werden beispielhaft Komprimierungsstatistiken für einige Pakete aufgeführt, die die ursprüngliche und die komprimierte Paketgröße mit der Komprimierungszeit angeben.

|Ursprüngliche Größe (MB)|Dateianzahl|Komprimierungszeit|Größe des komprimierten Pakets (MB)|
|----------------:|---------:|---------------:|---------------------------:|
|100|100|00:00:03.3547592|60|
|512|100|00:00:16.3850303|307|
|1\.024|500|00:00:32.5907950|615|
|2048|1000|00:01:04.3775554|1231|
|5012|100|00:02:45.2951288|3074|

Nachdem ein Paket komprimiert wurde, kann es bei Bedarf in einen oder in mehrere Service Fabric-Cluster hochgeladen werden. Für komprimierte und nicht komprimierte Pakete gilt dasselbe Bereitstellungsverfahren. Komprimierte Pakete werden in dieser Form im Clusterimagespeicher gespeichert. Die Pakete werden auf dem Knoten dekomprimiert, bevor die Anwendung ausgeführt wird.


Im folgenden Beispiel wird das Paket in den Imagespeicher in einen Ordner mit dem Namen „MyApplicationV1“ hochgeladen:

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -ApplicationPackagePathInImageStore MyApplicationV1 -TimeoutSec 1800
```

Wenn Sie den Parameter *-ApplicationPackagePathInImageStore* nicht angeben, wird das Anwendungspaket in den Ordner „Debug“ im Imagespeicher kopiert.

>[!NOTE]
>Wenn die PowerShell-Sitzung mit einem Service Fabric-Cluster verbunden ist, erkennt **Copy-ServiceFabricApplicationPackage** automatisch die entsprechende Verbindungszeichenfolge des Imagespeichers. Bei Service Fabric-Versionen, die älter als 5.6 sind, muss explizit das Argument **-ImageStoreConnectionString** angegeben werden.
>
>```powershell
>PS C:\> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -ApplicationPackagePathInImageStore MyApplicationV1 -ImageStoreConnectionString (Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest)) -TimeoutSec 1800
>```
>
>Das Cmdlet **Get-ImageStoreConnectionStringFromClusterManifest** ist Teil des Service Fabric-SDK-PowerShell-Moduls und wird verwendet, um die Imagespeicher-Verbindungszeichenfolge abzurufen.  Um das SDK-Modul zu importieren, führen Sie Folgendes aus:
>
>```powershell
>Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
>```
>
>Weitere Informationen zum Imagespeicher und zur Imagespeicher-Verbindungszeichenfolge finden Sie unter [Grundlegendes zur Imagespeicher-Verbindungszeichenfolge](service-fabric-image-store-connection-string.md).
>
>
>

Die Zeit zum Hochladen eines Pakets hängt von verschiedenen Faktoren ab. Zu einigen dieser Faktoren zählen die Anzahl der Dateien im Paket, die Paketgröße und die Dateigrößen. Die Netzwerkgeschwindigkeit zwischen dem Quellcomputer und dem Service Fabric-Cluster wirkt sich auch auf die Uploadzeit aus. Das Standardtimeout für [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) beträgt 30 Minuten.
Abhängig von den beschriebenen Faktoren müssen Sie eventuell das Timeout erhöhen. Wenn Sie das Paket beim Aufrufen der Kopierfunktion komprimieren, müssen Sie auch die Zeit für die Komprimierung in Betracht ziehen.



## <a name="register-the-application-package"></a>Registrieren des Anwendungspakets

Der Anwendungstyp und die Version, der bzw. die im Anwendungsmanifest deklariert sind, werden beim Registrieren des Anwendungspakets verfügbar. Das System liest das im vorherigen Schritt hochgeladene Paket, überprüft es, verarbeitet den Inhalt des Pakets und kopiert das verarbeitete Paket an einen internen Systemspeicherort.  

Führen Sie das Cmdlet [Register ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) aus, um den Anwendungstyp im Cluster zu registrieren, und für die Bereitstellung verfügbar zu machen:

### <a name="register-the-application-package-copied-to-image-store"></a>Registrieren des in den Imagespeicher kopierten Anwendungspakets

Wenn ein Paket zuvor in den Imagespeicher kopiert wurde, wird vom Registrierungsvorgang der relative Pfad im Imagespeicher angegeben.

```powershell
Register-ServiceFabricApplicationType -ApplicationPathInImageStore MyApplicationV1
```

```Output
Register application type succeeded
```

„MyApplicationV1“ ist der Ordner im Imagespeicher, in dem sich das Anwendungspaket befindet. Der Anwendungstyp mit dem Namen „MyApplicationType“ und Version „1.0.0“ (beides befindet sich im Anwendungsmanifest) ist jetzt im Cluster registriert.

### <a name="register-the-application-package-copied-to-an-external-store"></a>Registrieren des in einen externen Speicher kopierten Anwendungspakets

Ab Service Fabric, Version 6.1, unterstützt die Bereitstellung das Herunterladen des Pakets aus einem externen Speicher. Der Download-URI stellt den Pfad zum [`sfpkg`-Anwendungspaket](service-fabric-package-apps.md#create-an-sfpkg) dar, unter dem das Anwendungspaket über das HTTP- oder HTTPS-Protokoll heruntergeladen werden kann. Das Paket muss zuvor an diesen externen Speicherort hochgeladen worden sein. Der URI muss Lesezugriffe zulassen, damit die Datei von Service Fabric heruntergeladen werden kann. Die `sfpkg`-Datei muss die Erweiterung „.sfpkg“ aufweisen. Der Bereitstellungsvorgang sollte die Informationen zum Anwendungstyp beinhalten, wie im Anwendungsmanifest angegeben.

```powershell
Register-ServiceFabricApplicationType -ApplicationPackageDownloadUri "https://sftestresources.blob.core.windows.net:443/sfpkgholder/MyAppPackage.sfpkg" -ApplicationTypeName MyApp -ApplicationTypeVersion V1 -Async
```

Der Befehl [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) wird erst zurückgegeben, wenn das Anwendungspaket vom System erfolgreich registriert wurde. Die Dauer des Registriervorgangs hängt von der Größe und dem Inhalt des Anwendungspakets ab. Verwenden Sie bei Bedarf den Parameter **-TimeoutSec**, wenn ein längeres Zeitlimit erforderlich ist (das Standardzeitlimit beträgt 60 Sekunden).

Wenn es sich um ein großes Anwendungspaket handelt oder Timeouts auftreten, verwenden Sie den Parameter **-Async**. Der Befehl wird zurückgegeben, wenn der Cluster den Registrierungsbefehl akzeptiert. Der Registrierungsvorgang wird bei Bedarf fortgesetzt.
Mit dem Befehl [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) werden die Anwendungstypversionen und deren Registrierungsstatus aufgelistet. Sie können mithilfe dieses Befehls ermitteln, wann die Registrierung abgeschlossen ist.

```powershell
Get-ServiceFabricApplicationType
```

```Output
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

## <a name="remove-an-application-package-from-the-image-store"></a>Entfernen eines Anwendungspakets aus dem Imagespeicher

Wenn ein Paket in den Imagespeicher kopiert wurde, sollten Sie es aus dem temporären Verzeichnis entfernen, nachdem die Anwendung erfolgreich registriert wurde. Sie können Systemressourcen freigeben, indem Sie Anwendungspakete aus dem Imagespeicher löschen. Die Speicherung nicht verwendeter Anwendungspakete nimmt Speicherplatz in Anspruch und führt zu Leistungsproblemen der Anwendung.

```powershell
Remove-ServiceFabricApplicationPackage -ApplicationPackagePathInImageStore MyApplicationV1
```

## <a name="create-the-application"></a>Erstellen der Anwendung

Sie können eine Anwendung mit einer beliebigen Version des Anwendungstyps instanziieren, die mit dem Cmdlet [New-ServiceFabricApplication](/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps) erfolgreich registriert wurde. Der Name jeder Anwendung muss mit dem *„fabric:“* -Schema beginnen und für jede Anwendungsinstanz eindeutig sein. Wenn im Anwendungsmanifest des Zielanwendungstyps Standarddienste festgelegt wurden, werden diese ebenfalls erstellt.

```powershell
New-ServiceFabricApplication fabric:/MyApp MyApplicationType 1.0.0
```

```Output
ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
ApplicationParameters  : {}
```

Für jede Version des registrierten Anwendungstyps können mehrere Anwendungsinstanzen erstellt werden. Jede Anwendungsinstanz wird isoliert mit einem eigenen Arbeitsverzeichnis und Prozess ausgeführt.

Zum Anzeigen welche benannten Apps und Dienste im Cluster ausgeführt werden, führen Sie die Cmdlets [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication) und [Get ServiceFabricService](/powershell/module/servicefabric/get-servicefabricservice?view=azureservicefabricps) aus:

```powershell
Get-ServiceFabricApplication  
```

```Output
ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Ok
ApplicationParameters  : {}
```

```powershell
Get-ServiceFabricApplication | Get-ServiceFabricService
```

```Output
ServiceName            : fabric:/MyApp/Stateless1
ServiceKind            : Stateless
ServiceTypeName        : Stateless1Type
IsServiceGroup         : False
ServiceManifestVersion : 1.0.0
ServiceStatus          : Active
HealthState            : Ok
```

## <a name="remove-an-application"></a>Entfernen einer Anwendung

Wird eine Anwendungsinstanz nicht mehr benötigt, können Sie diese anhand des Namens mit dem Cmdlet [Remove-ServiceFabricApplication](/powershell/module/servicefabric/remove-servicefabricapplication?view=azureservicefabricps) endgültig entfernen. Mit [Remove-ServiceFabricApplication](/powershell/module/servicefabric/remove-servicefabricapplication?view=azureservicefabricps) werden auch alle Dienste automatisch entfernt, die mit der Anwendung verknüpft sind, d.h. der Dienstzustand wird vollständig und dauerhaft entfernt. 

> [!WARNING]
> Dieser Vorgang kann nicht rückgängig gemacht werden, und der Anwendungsstatus kann nicht wiederhergestellt werden.

```powershell
Remove-ServiceFabricApplication fabric:/MyApp
```

```Output
Confirm
Continue with this operation?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
Remove application instance succeeded
```

```powershell
Get-ServiceFabricApplication
```

## <a name="unregister-an-application-type"></a>Aufheben der Registrierung eines Anwendungstyps

Wird eine bestimmte Version eines Anwendungstyps nicht mehr benötigt, sollten Sie die Registrierung des Anwendungstyps mit dem Cmdlet [Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) aufheben. Durch das Aufheben der Registrierung nicht verwendeter Anwendungstypen wird der vom Imagespeicher verwendete Speicherplatz freigegeben, indem Anwendungstypdateien entfernt werden. Beim Aufheben der Registrierung eines Anwendungstyps wird das Anwendungspaket, das in das temporäre Verzeichnis des Imagespeichers kopiert wurde, nicht entfernt (falls ein Kopiervorgang in den Imagespeicher ausgeführt wurde). Die Registrierung eines Anwendungstyps kann nur aufgehoben werden, wenn keine Anwendungen für den Typ instanziiert sind und keine ausstehenden Anwendungsupgrades vorliegen, die auf den Typ verweisen.

Führen Sie [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) aus, um die derzeit im Cluster registrierten Anwendungstypen anzuzeigen:

```powershell
Get-ServiceFabricApplicationType
```

```Output
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

Führen Sie [Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) aus, um die Registrierung eines bestimmten Anwendungstyps aufzuheben:

```powershell
Unregister-ServiceFabricApplicationType MyApplicationType 1.0.0
```

## <a name="troubleshooting"></a>Problembehandlung

### <a name="copy-servicefabricapplicationpackage-asks-for-an-imagestoreconnectionstring"></a>Copy-ServiceFabricApplicationPackage fordert einen ImageStoreConnectionString an

Die Service Fabric-SDK-Umgebung sollte bereits mit den richtigen Standardeinstellungen eingerichtet sein. Wichtig ist, dass der ImageStoreConnectionString für alle Befehle mit dem vom Service Fabric-Cluster verwendeten Wert übereinstimmt. Sie finden „ImageStoreConnectionString“ im Clustermanifest, das über die Befehle [Get-ServiceFabricClusterManifest](/powershell/module/servicefabric/get-servicefabricclustermanifest?view=azureservicefabricps) und „Get-ImageStoreConnectionStringFromClusterManifest“ abgerufen wird:

```powershell
Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest)
```

Das Cmdlet **Get-ImageStoreConnectionStringFromClusterManifest** ist Teil des Service Fabric-SDK-PowerShell-Moduls und wird verwendet, um die Imagespeicher-Verbindungszeichenfolge abzurufen.  Um das SDK-Modul zu importieren, führen Sie Folgendes aus:

```powershell
Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
```

ImageStoreConnectionString ist im Clustermanifest zu finden:

```xml
<ClusterManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Name="Server-Default-SingleNode" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

    [...]

    <Section Name="Management">
      <Parameter Name="ImageStoreConnectionString" Value="file:D:\ServiceFabric\Data\ImageStore" />
    </Section>

    [...]
```

Weitere Informationen zum Imagespeicher und zur Imagespeicher-Verbindungszeichenfolge finden Sie unter [Grundlegendes zur Imagespeicher-Verbindungszeichenfolge](service-fabric-image-store-connection-string.md).

### <a name="deploy-large-application-package"></a>Bereitstellen eines großen Anwendungspakets

Problem: Bei einem großen Anwendungspaket (im GB-Bereich) tritt bei Verwendung von [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) ein Timeout auf.
Versuchen Sie Folgendes:
- Geben Sie bei dem Befehl [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) mit dem Parameter `TimeoutSec` einen höheren Timeoutwert an. Das Timeout beträgt standardmäßig 30 Minuten.
- Überprüfen Sie die Netzwerkverbindung zwischen Ihrem Quellcomputer und dem Cluster. Ziehen Sie bei einer langsamen Verbindung die Verwendung eines Computers mit einer besseren Netzwerkverbindung in Betracht.
Wenn sich der Clientcomputer in einem anderen Bereich als der Cluster befindet, sollten Sie einen Clientcomputer in einem näher gelegenen oder im selben Bereich wie den des Clusters verwenden.
- Überprüfen Sie, ob Sie von einer externen Drosselung betroffen sind. Wenn der Abbildspeicher beispielsweise für die Verwendung des Azure-Speichers konfiguriert ist, wird der Upload eventuell gedrosselt.

Problem: Das Paket wurde erfolgreich hochgeladen, allerdings tritt bei Verwendung von [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) ein Timeout auf. Versuchen Sie Folgendes:
- [Komprimieren Sie das Paket](service-fabric-package-apps.md#compress-a-package), bevor Sie es in den Abbildspeicher kopieren.
Durch eine Komprimierung werden Größe und Anzahl der Dateien verringert, wodurch wiederum die Menge des Datenverkehrs und der Aufgaben, die das Service Fabric durchführen muss, reduziert wird. Der Upload kann sich eventuell verlangsamen (insbesondere, wenn Sie die Komprimierungszeit berücksichtigen), allerdings werden die Registrierung und die Aufhebung der Registrierung des Anwendungstyps schneller durchgeführt.
- Geben Sie bei dem Befehl [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) mit dem Parameter `TimeoutSec` einen höheren Timeoutwert an.
- Legen Sie den Schalter `Async` für [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) fest. Der Befehl gibt etwas zurück, wenn das Cluster den Befehl annimmt und die Registrierung des Anwendungstyps wird asynchron fortgesetzt. Aus diesem Grund muss in diesem Fall kein höherer Timeoutwert angegeben werden. Der Befehl [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) listet alle erfolgreich registrierten Anwendungstypversionen und deren Registrierungsstatus auf. Sie können mithilfe dieses Befehls ermitteln, wann die Registrierung abgeschlossen ist.

```powershell
Get-ServiceFabricApplicationType
```

```Output
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

### <a name="deploy-application-package-with-many-files"></a>Bereitstellen eines Anwendungspakets mit vielen Dateien

Problem: Bei einem Anwendungspaket mit vielen Dateien (im Tausenderbereich) tritt bei Verwendung von [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) ein Timeout auf.
Versuchen Sie Folgendes:
- [Komprimieren Sie das Paket](service-fabric-package-apps.md#compress-a-package), bevor Sie es in den Abbildspeicher kopieren. Durch eine Komprimierung wird die Anzahl der Dateien verringert.
- Geben Sie bei dem Befehl [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) mit dem Parameter `TimeoutSec` einen höheren Timeoutwert an.
- Legen Sie den Schalter `Async` für [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) fest. Der Befehl gibt etwas zurück, wenn das Cluster den Befehl annimmt und die Registrierung des Anwendungstyps wird asynchron fortgesetzt.
Aus diesem Grund muss in diesem Fall kein höherer Timeoutwert angegeben werden. Der Befehl [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) listet alle erfolgreich registrierten Anwendungstypversionen und deren Registrierungsstatus auf. Sie können mithilfe dieses Befehls ermitteln, wann die Registrierung abgeschlossen ist.

```powershell
Get-ServiceFabricApplicationType
```

```Output
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

## <a name="next-steps"></a>Nächste Schritte

[Packen einer Anwendung](service-fabric-package-apps.md)

[Service Fabric-Anwendungsupgrade](service-fabric-application-upgrade.md)

[Einführung in Service Fabric-Integrität](service-fabric-health-introduction.md)

[Diagnose und Problembehandlung von Service Fabric-Diensten](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Modellieren einer Anwendung in Service Fabric](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-package-apps.md
[11]: service-fabric-application-upgrade.md