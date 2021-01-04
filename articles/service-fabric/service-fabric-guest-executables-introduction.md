---
title: Verpacken einer vorhandenen ausführbaren Datei für Azure Service Fabric
description: Erfahren Sie, wie Sie ein Paket einer vorhandenen Anwendung als ausführbare Gastanwendungsdatei erstellen, um diese in einem Service Fabric-Cluster bereitzustellen.
ms.topic: conceptual
ms.date: 03/15/2018
ms.openlocfilehash: 8b808d092001196a4d2150e44d508e031db95554
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017745"
---
# <a name="deploy-an-existing-executable-to-service-fabric"></a>Bereitstellen einer vorhandenen ausführbaren Datei für Service Fabric
Sie können jede Art von Code, z.B. Node.js, Java oder C++, in Azure Service Fabric als Dienst ausführen. In Service Fabric sind derartige Dienste ausführbare Gastdateien.

Ausführbare Gastanwendungsdateien werden von Service Fabric wie zustandslose Dienste behandelt. Folglich werden sie basierend auf der Verfügbarkeit und anderen Metriken auf Knoten innerhalb eines Clusters platziert. In diesem Artikel wird beschrieben, wie Sie eine ausführbare Gastanwendungsdatei packen und in einem Service Fabric-Cluster bereitstellen, indem Sie Visual Studio oder ein Befehlszeilenprogramm verwenden.

## <a name="benefits-of-running-a-guest-executable-in-service-fabric"></a>Vorteile der Ausführung einer ausführbaren Gastanwendungsdatei in Service Fabric
Das Ausführen einer ausführbaren Gastanwendungsdatei in einem Service Fabric-Cluster bietet mehrere Vorteile:

* Hochverfügbarkeit. Anwendungen, die in Service Fabric ausgeführt werden, weisen eine hohe Verfügbarkeit auf. In Service Fabric wird sichergestellt, dass die Instanzen einer Anwendung ausgeführt werden.
* Systemüberwachung. Die Service Fabric-Systemüberwachung erkennt, ob eine Anwendung ausgeführt wird, und stellt bei Fehlern Diagnoseinformationen bereit.   
* Application Lifecycle Management. Zusätzlich zur Bereitstellung von Upgrades ohne Ausfallzeiten ermöglicht Service Fabric das automatische Zurücksetzen auf die vorherige Version (Rollback), wenn während eines Upgrades ein Fehler in Bezug auf die Integrität gemeldet wird.    
* Dichte. Sie können mehrere Anwendungen in einem Cluster ausführen, sodass nicht mehr jede Anwendung auf eigener Hardware ausgeführt werden muss.
* Auffindbarkeit: Mithilfe von REST können Sie den Service Fabric Naming Service aufrufen, um nach anderen Diensten im Cluster zu suchen. 

## <a name="samples"></a>Beispiele
* [Beispiel für das Packen und Bereitstellen einer ausführbaren Gastdatei](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Beispiel für zwei ausführbare Gastdateien (C# und Node.js), die über den Naming Service mithilfe von REST kommunizieren](https://github.com/Azure-Samples/service-fabric-dotnet-containers)

## <a name="overview-of-application-and-service-manifest-files"></a>Übersicht über die Anwendungs- und Dienstmanifestdateien
Im Rahmen der Bereitstellung einer ausführbaren Gastanwendungsdatei sollten Sie das Service Fabric-Modell für das Packen und Bereitstellen von Anwendungen kennen. Eine Beschreibung finden Sie unter [Anwendungsmodell](service-fabric-application-model.md). Das Packmodell von Service Fabric basiert auf zwei XML-Dateien: dem Anwendungs- und dem Dienstmanifest. Die Schemadefinition für die Dateien „ApplicationManifest.xml“ und „ServiceManifest.xml“ wird mit dem Service Fabric SDK unter *C:\Programme\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd* installiert.

* **Anwendungsmanifest**: Das Anwendungsmanifest wird verwendet, um die Anwendung zu beschreiben. Es listet neben den Diensten, aus denen die Anwendung besteht, noch weitere Parameter auf, mit denen definiert wird, wie Dienste bereitgestellt werden sollen, z.B. die Anzahl von Instanzen.

  Unter Service Fabric ist eine Anwendung eine Bereitstellungs- und Upgradeeinheit. Eine Anwendung kann als eine Einheit aktualisiert werden, wobei potenzielle Fehler und mögliche Zurücksetzungen verwaltet werden. Mit Service Fabric wird sichergestellt, dass der Upgradeprozess entweder erfolgreich ist oder – falls beim Upgrade ein Fehler auftritt – die Anwendung nicht in einem unbekannten oder instabilen Zustand verbleibt.
* **Dienstmanifest**: Das Dienstmanifest beschreibt die Komponenten eines Diensts. Es enthält Daten, z.B. den Namen und Typ des Diensts, und den Code sowie die Konfiguration. Das Dienstmanifest enthält auch einige zusätzliche Parameter, die verwendet werden können, um den Dienst zu konfigurieren, nachdem er bereitgestellt wurde.

## <a name="application-package-file-structure"></a>Dateistruktur des Anwendungspakets
Damit eine Anwendung in Service Fabric bereitgestellt werden kann, sollte die Anwendung einer vordefinierten Verzeichnisstruktur folgen. Unten ist ein Beispiel für diese Struktur angegeben.

```
|-- ApplicationPackageRoot
    |-- GuestService1Pkg
        |-- Code
            |-- existingapp.exe
        |-- Config
            |-- Settings.xml
        |-- Data
        |-- ServiceManifest.xml
    |-- ApplicationManifest.xml
```

ApplicationPackageRoot enthält die Datei „ApplicationManifest.xml“, mit der die Anwendung definiert wird. Für jeden Dienst, der in der Anwendung enthalten ist, gibt es ein Unterverzeichnis, das alle für den Dienst erforderlichen Artefakte enthält. Diese Unterverzeichnisse umfassen „Servicemanifest.xml“ und normalerweise Folgendes:

* *Code*. Dieses Verzeichnis enthält den Code des Diensts.
* *Config*. Dieses Verzeichnis enthält die Datei „Settings.xml“ (sowie andere Dateien, falls erforderlich), auf die der Dienst zur Laufzeit zugreifen kann, um bestimmte Konfigurationseinstellungen abzurufen.
* *Daten*. Dies ist ein zusätzliches Verzeichnis zum Speichern zusätzlicher lokaler Daten, die der Dienst möglicherweise benötigt. Das Verzeichnis „data“ sollte nur verwendet werden, um kurzlebige Daten zu speichern. Service Fabric kopiert oder repliziert keine Änderungen in das Verzeichnis „data“, wenn der Dienst (beispielsweise bei einem Failover) verschoben werden muss.

> [!NOTE]
> Sie müssen die Verzeichnisse `config` und `data` nur erstellen, wenn Sie sie benötigen.
>
>

## <a name="next-steps"></a>Nächste Schritte
Entsprechende Informationen und Aufgaben finden Sie in den folgenden Artikeln.
* [Bereitstellen einer ausführbaren Gastanwendungsdatei](service-fabric-deploy-existing-app.md)
* [Bereitstellen mehrerer ausführbarer Gastanwendungsdateien](./service-fabric-deploy-existing-app.md)
* [Erstellen Ihrer ersten ausführbaren Gastanwendungsdatei in Visual Studio](quickstart-guest-app.md)
* [Beispiel für das Packen und Bereitstellen einer ausführbaren Gastdatei](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started), einschließlich eines Links zur Vorabversion des Packtools
* [Beispiel für zwei ausführbare Gastdateien (C# und Node.js), die über den Naming Service mithilfe von REST kommunizieren](https://github.com/Azure-Samples/service-fabric-containers)
