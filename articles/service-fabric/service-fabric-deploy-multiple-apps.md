---
title: Bereitstellen einer Node.js-Anwendung mit MongoDB in Azure Service Fabric | Microsoft-Dokumentation
description: Exemplarische Vorgehensweise zum Packen mehrerer ausführbarer Gastanwendungsdateien, um sie in einem Azure Service Fabric-Cluster bereitzustellen
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: chackdan
editor: ''
ms.assetid: b76bb756-c1ba-49f9-9666-e9807cf8f92f
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/23/2018
ms.author: mikhegn
ms.openlocfilehash: 69df9eff85d96c9cc6ca7fa1d3aabd2c54fae416
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60583861"
---
# <a name="deploy-multiple-guest-executables"></a>Bereitstellen mehrerer ausführbarer Gastanwendungsdateien
In diesem Artikel erfahren Sie, wie Sie mehrere ausführbare Gastanwendungsdateien packen und für Azure Service Fabric bereitstellen. Informationen zum Erstellen und Bereitstellen eines einzelnen Service Fabric-Pakets finden Sie im Abschnitt [Bereitstellen einer ausführbaren Gastanwendungsdatei in Service Fabric](service-fabric-deploy-existing-app.md).

In dieser exemplarischen Vorgehensweise wird zwar das Bereitstellen einer Anwendung mit einem Node.js-Front-End gezeigt, die MongoDB als Datenspeicher nutzt, aber diese Schritte gelten für alle Anwendungen mit Abhängigkeiten von einer anderen Anwendung.   

Das Anwendungspaket mit mehreren ausführbaren Gastanwendungsdateien kann mithilfe von Visual Studio generiert werden. Weitere Informationen finden Sie unter [Verwenden von Visual Studio zum Packen einer vorhandenen Anwendung](service-fabric-deploy-existing-app.md). Klicken Sie nach dem Hinzufügen der ersten ausführbaren Gastanwendungsdatei mit der rechten Maustaste auf das Anwendungsprojekt, und wählen Sie **Hinzufügen > Neuer Service Fabric-Dienst** aus, um der Projektmappe das zweite ausführbare Gastanwendungsprojekt hinzuzufügen. Hinweis: Wenn Sie die Quelle im Visual Studio-Projekt verknüpfen und die Visual Studio-Projektmappe erstellen, wird Ihr Anwendungspaket mit den Änderungen in der Quelle aktualisiert. 

## <a name="samples"></a>Beispiele
* [Beispiel für das Packen und Bereitstellen einer ausführbaren Gastdatei](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Beispiel für zwei ausführbare Gastdateien (C# und Node.js), die über den Naming Service mithilfe von REST kommunizieren](https://github.com/Azure-Samples/service-fabric-containers)

## <a name="manually-package-the-multiple-guest-executable-application"></a>Manuelles Packen der Anwendung mit mehreren ausführbaren Gastanwendungsdateien
Alternativ können Sie die ausführbare Gastanwendungsdatei manuell packen. Hierzu wird in diesem Artikel das Service Fabric-Verpackungstool ([https://aka.ms/servicefabricpacktool](https://aka.ms/servicefabricpacktool)) verwendet.

### <a name="packaging-the-nodejs-application"></a>Packen der Node.js-Anwendung
In diesem Artikel wird vorausgesetzt, dass Node.js nicht auf den Knoten im Service Fabric-Cluster installiert ist. Daher müssen Sie vor dem Packen „Node.exe“ dem Stammverzeichnis Ihrer Node.js-Anwendung hinzufügen. Die Verzeichnisstruktur der Node.js-Anwendung (mit dem Webframework Express und der Vorlagen-Engine Jade) sollte wie folgt aussehen:

```
|-- NodeApplication
    |-- bin
        |-- www
    |-- node_modules
        |-- .bin
        |-- express
        |-- jade
        |-- etc.
    |-- public
        |-- images
        |-- etc.
    |-- routes
        |-- index.js
        |-- users.js
    |-- views
        |-- index.jade
        |-- etc.
    |-- app.js
    |-- package.json
    |-- node.exe
```

Im nächsten Schritt erstellen Sie ein Anwendungspaket für die Node.js-Anwendung. Der folgende Code erstellt ein Service Fabric-Anwendungspaket, das die Node.js-Anwendung enthält.

```
.\ServiceFabricAppPackageUtil.exe /source:'[yourdirectory]\MyNodeApplication' /target:'[yourtargetdirectory] /appname:NodeService /exe:'node.exe' /ma:'bin/www' /AppType:NodeAppType
```

Es folgt eine Beschreibung der verwendeten Parameter:

* **/source** : Zeigt auf das Verzeichnis der Anwendung, das gepackt werden soll.
* **/target** : Definiert das Verzeichnis, in dem das Paket erstellt werden soll. Dieses Verzeichnis darf nicht mit dem Quellverzeichnis identisch sein.
* **/appname** : Gibt den Anwendungsnamen der vorhandenen Anwendung an. Wichtig ist der Hinweis, dass dieser Name in den Dienstnamen im Manifest und nicht in den Service Fabric-Anwendungsnamen übersetzt wird.
* **/exe**: Definiert die ausführbare Datei, die Service Fabric starten soll. In diesem Fall ist das `node.exe`.
* **/ma** : Gibt das Argument an, das verwendet wird, um die ausführbare Datei zu starten. Da Node.js nicht installiert ist, muss Service Fabric den Node.js-Webserver durch Ausführen von `node.exe bin/www`starten.  `/ma:'bin/www'` weist das Verpackungstool an, `bin/www` als Argument für „node.exe“ zu verwenden.
* **/AppType** : Bestimmt den Namen des Service Fabric-Anwendungstyps.

Wenn Sie zum Verzeichnis navigieren, das im Parameter „/target“ angegeben wurde, sehen Sie, dass das Tool ein voll funktionsfähiges Service Fabric-Paket erstellt hat (siehe unten):

```
|--[yourtargetdirectory]
    |-- NodeApplication
        |-- C
              |-- bin
              |-- data
              |-- node_modules
              |-- public
              |-- routes
              |-- views
              |-- app.js
              |-- package.json
              |-- node.exe
        |-- config
              |--Settings.xml
        |-- ServiceManifest.xml
    |-- ApplicationManifest.xml
```
Die generierte Datei „ServiceManifest.xml“ enthält nun einen Abschnitt, der beschreibt, wie der Node.js-Webserver gestartet werden soll. Dies wird im folgenden Codeausschnitt gezeigt:

```xml
<CodePackage Name="C" Version="1.0">
    <EntryPoint>
        <ExeHost>
            <Program>node.exe</Program>
            <Arguments>'bin/www'</Arguments>
            <WorkingFolder>CodePackage</WorkingFolder>
        </ExeHost>
    </EntryPoint>
</CodePackage>
```
In diesem Beispiel lauscht der Node.js-Webserver an Port 3000. Deshalb müssen Sie die Endpunktinformationen in der Datei „ServiceManifest.xml“ wie unten dargestellt aktualisieren.   

```xml
<Resources>
      <Endpoints>
         <Endpoint Name="NodeServiceEndpoint" Protocol="http" Port="3000" Type="Input" />
      </Endpoints>
</Resources>
```
### <a name="packaging-the-mongodb-application"></a>Packen der MongoDB-Anwendung
Nachdem Sie das Node.js-Anwendungspaket erstellt haben, können Sie nun das MongoDB-Paket erstellen. Wie bereits erwähnt, gelten die jetzt auszuführenden Schritte nicht speziell für Node.js und MongoDB. Sie gelten für alle Anwendungen, die zu einer Service Fabric-Anwendung gepackt werden sollen.  

Für das MongoDB-Paket müssen Sie „Mongod.exe“ und „Mongo.exe“ packen. Beide Binärdateien befinden sich im Verzeichnis `bin` des MongoDB-Installationsverzeichnisses. Die Verzeichnisstruktur sieht ähnlich wie folgt aus.

```
|-- MongoDB
    |-- bin
        |-- mongod.exe
        |-- mongo.exe
        |-- anybinary.exe
```
Service Fabric muss MongoDB mit einem Befehl starten, der dem unten angegebenen Befehl ähnelt. Deshalb müssen Sie den Parameter `/ma` beim Packen von MongoDB verwenden.

```
mongod.exe --dbpath [path to data]
```
> [!NOTE]
> Die Daten werden bei einem Ausfall eines Knotens nicht beibehalten, wenn Sie das MongoDB-Datenverzeichnis in das lokale Verzeichnis des Knotens einfügen. Sie müssen entweder beständigen Speicher verwenden oder eine MongoDB-Replikatgruppe implementieren, um Datenverluste zu vermeiden.  
>
>

Über PowerShell oder die Befehlsshell führen wir das Packtool mit den folgenden Parametern aus:

```
.\ServiceFabricAppPackageUtil.exe /source: [yourdirectory]\MongoDB' /target:'[yourtargetdirectory]' /appname:MongoDB /exe:'bin\mongod.exe' /ma:'--dbpath [path to data]' /AppType:NodeAppType
```

Um MongoDB Ihrem Service Fabric-Anwendungspaket hinzuzufügen, müssen Sie sicherstellen, dass der Parameter „/target“ auf dasselbe Verzeichnis verweist, das bereits das Anwendungsmanifest und die Node.js-Anwendung enthält. Sie müssen auch sicherstellen, dass Sie denselben Namen für „ApplicationType“ verwenden.

Lassen Sie uns zum Verzeichnis wechseln und untersuchen, was das Tool erstellt hat.

```
|--[yourtargetdirectory]
    |-- MyNodeApplication
    |-- MongoDB
        |-- C
            |--bin
                |-- mongod.exe
                |-- mongo.exe
                |-- etc.
        |-- config
            |--Settings.xml
        |-- ServiceManifest.xml
    |-- ApplicationManifest.xml
```
Wie Sie sehen, hat das Tool dem Verzeichnis einen neuen Ordner namens „MongoDB“ hinzugefügt, das die MongoDB-Binärdateien enthält. Wenn Sie die Datei `ApplicationManifest.xml` öffnen, sehen Sie, dass das Paket jetzt sowohl die Node.js-Anwendung als auch MongoDB enthält. Der folgende Code zeigt den Inhalt des Anwendungsmanifests.

```xml
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyNodeApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MongoDB" ServiceManifestVersion="1.0" />
   </ServiceManifestImport>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeService" ServiceManifestVersion="1.0" />
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="MongoDBService">
         <StatelessService ServiceTypeName="MongoDB">
            <SingletonPartition />
         </StatelessService>
      </Service>
      <Service Name="NodeServiceService">
         <StatelessService ServiceTypeName="NodeService">
            <SingletonPartition />
         </StatelessService>
      </Service>
   </DefaultServices>
</ApplicationManifest>  
```

### <a name="publishing-the-application"></a>Veröffentlichen der Anwendung
Der letzte Schritt ist das Veröffentlichen der Anwendung im lokalen Service Fabric-Cluster mithilfe des folgenden PowerShell-Skripts:

```
Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath '[yourtargetdirectory]' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStoreShare' -ApplicationPackagePathInImageStore 'NodeAppType'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'NodeAppType'

New-ServiceFabricApplication -ApplicationName 'fabric:/NodeApp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0  
```

Nach der erfolgreichen Veröffentlichung der Anwendung im lokalen Cluster können Sie auf die Node.js-Anwendung an dem Port zugreifen, den Sie in das Dienstmanifest der Node.js-Anwendung eingegeben haben (Beispiel: http:\//localhost:3000).

In diesem Tutorial haben Sie gelernt, wie Sie zwei vorhandene Anwendungen leicht zu einer Service Fabric-Anwendung Packen können. Sie haben auch gelernt, wie Sie die Bereitstellung für Service Fabric durchführen, um von den Service Fabric-Features zu profitieren. Beispiele hierfür sind Hochverfügbarkeit und die Integration des Integritätssystems.


## <a name="adding-more-guest-executables-to-an-existing-application-using-yeoman-on-linux"></a>Hinzufügen von weiteren ausführbaren Gastanwendungsdateien zu einer vorhandenen Anwendung mit Yeoman unter Linux

Führen Sie zum Hinzufügen eines weiteren Diensts zu einer Anwendung, die bereits mit `yo` erstellt wurde, die folgenden Schritte aus: 
1. Legen Sie das Verzeichnis auf den Stamm der vorhandenen Anwendung fest.  Beispiel: `cd ~/YeomanSamples/MyApplication`, wenn `MyApplication` die von Yeoman erstellte Anwendung ist.
2. Führen Sie `yo azuresfguest:AddService` aus, und geben Sie die erforderlichen Informationen an.

## <a name="next-steps"></a>Nächste Schritte
* Weitere Informationen zum Bereitstellen von Containern finden Sie unter [Vorschau: Service Fabric und Container](service-fabric-containers-overview.md).
* [Beispiel für das Packen und Bereitstellen einer ausführbaren Gastdatei](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Beispiel für zwei ausführbare Gastdateien (C# und Node.js), die über den Naming Service mithilfe von REST kommunizieren](https://github.com/Azure-Samples/service-fabric-containers)
