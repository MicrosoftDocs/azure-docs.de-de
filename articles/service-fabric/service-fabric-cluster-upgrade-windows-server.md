---
title: Upgraden der Version eines eigenständigen Azure Service Fabric-Clusters | Microsoft-Dokumentation
description: Upgraden Sie den Azure Service Fabric-Code für die Ausführung eines eigenständigen Service Fabric-Clusters.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: 66296cc6-9524-4c6a-b0a6-57c253bdf67e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2018
ms.author: dekapur
ms.openlocfilehash: 29d034be5999d0bc3f0a244cfa7a5658a4ecce32
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60711363"
---
# <a name="upgrade-the-service-fabric-version-that-runs-on-your-cluster"></a>Upgraden der in Ihrem Cluster ausgeführten Service Fabric-Version 

Für alle modernen Systeme ist die Möglichkeit eines Upgrades der Schlüssel zum langfristigen Erfolg des Produkts. Ein Azure Service Fabric-Cluster ist eine Ressource, die sich in Ihrem Besitz befindet. In diesem Artikel erfahren Sie, wie Sie die in Ihrem eigenständigen Cluster ausgeführte Service Fabric-Version upgraden.

> [!NOTE]
> Achten Sie darauf, dass in Ihrem Cluster immer eine unterstützte Service Fabric-Version ausgeführt wird. Wenn Microsoft eine neue Service Fabric-Version ankündigt, beträgt die verbleibende Supportdauer der vorherigen Version noch mindestens 60 Tage ab dem Datum der Ankündigung. Neue Releases werden im [Blog des Service Fabric-Teams](https://blogs.msdn.microsoft.com/azureservicefabric/) angekündigt. Ab dann kann das neue Release ausgewählt werden.
>
>

Sie können Ihren Cluster nur auf die neue Version upgraden, wenn Sie eine Knotenkonfiguration im Produktionsformat verwenden, bei der jeder Service Fabric-Knoten einem separaten physischen oder virtuellen Computer zugeordnet ist. Wenn Sie einen Entwicklungscluster mit mehreren Service Fabric-Knoten auf einem physischen oder virtuellen Computer haben, müssen Sie Ihren Cluster mit der neuen Version neu erstellen.

Für das Upgrade Ihres Clusters auf die aktuelle oder eine unterstützte Service Fabric-Version gibt es zwei unterschiedliche Workflows. Ein Workflow ist für Cluster vorgesehen, die über Konnektivität verfügen, um die neuesten Versionen automatisch herunterzuladen. Der andere Workflow ist für Cluster vorgesehen, die nicht über Konnektivität verfügen, um die neueste Service Fabric-Version automatisch herunterzuladen.

## <a name="enable-auto-upgrade-of-the-service-fabric-version-of-your-cluster"></a>Aktivieren automatischer Upgrades für die Service Fabric-Version Ihres Clusters
Falls Ihr Cluster Updates von Service Fabric herunterladen soll, wenn Microsoft eine neue Version veröffentlicht, legen Sie die Clusterkonfiguration `fabricClusterAutoupgradeEnabled` auf *true* fest. Wenn Sie für Ihren Cluster manuell eine unterstützte Version von Service Fabric auswählen möchten, legen Sie die Clusterkonfiguration `fabricClusterAutoupgradeEnabled` auf *false* fest.

## <a name="upgrade-clusters-that-have-connectivity-to-download-the-latest-code-and-configuration"></a>Aktualisieren der Cluster mit Konnektivität zum Herunterladen des aktuellen Codes und der Konfiguration
Führen Sie diese Schritte aus, um Ihren Cluster auf eine unterstützte Version upzugraden, wenn Ihre Clusterknoten eine Internetverbindung mit dem [Microsoft Download Center](https://download.microsoft.com) haben.

Für Cluster mit Verbindung zum [Microsoft Download Center](https://download.microsoft.com) überprüft Microsoft in regelmäßigen Abständen die Verfügbarkeit neuer Service Fabric-Versionen.

Wenn eine neue Service Fabric-Version verfügbar ist, wird das Paket lokal in den Cluster heruntergeladen und für das Upgrade bereitgestellt. Um den Kunden außerdem über diese neue Version zu informieren, zeigt das System eine explizite Clusterintegritätswarnung ähnlich der folgenden an:

„Der Support für die aktuelle Clusterversion [Versionsnummer] endet am [Datum].“

Wenn der Cluster die neueste Version ausführt, wird diese Warnung nicht mehr angezeigt.

Wenn die Clusterintegritätswarnung angezeigt wird, führen Sie ein Upgrade für den Cluster durch:

1. Stellen Sie auf einem Computer mit Administratorzugriffsrechten auf alle Computer, die als Knoten in der Clusterkonfigurationsdatei aufgeführt sind, eine Verbindung mit dem Cluster her. Der Computer, auf dem dieses Skript ausgeführt wird, muss nicht Teil des Clusters sein.

    ```powershell
    ###### connect to the secure cluster using certs
    $ClusterName= "mysecurecluster.something.com:19000"
    $CertThumbprint= "70EF5E22ADB649799DA3C8B6A6BF7FG2D630F8F3"
    Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
        -X509Credential `
        -ServerCertThumbprint $CertThumbprint  `
        -FindType FindByThumbprint `
        -FindValue $CertThumbprint `
        -StoreLocation CurrentUser `
        -StoreName My
    ```

2. Rufen Sie die Liste der Service Fabric-Versionen ab, auf die Sie ein Upgrade durchführen können.

    ```powershell
    ###### Get the list of available Service Fabric versions
    Get-ServiceFabricRegisteredClusterCodeVersion
    ```

    Die Ausgabe sollte in etwa wie folgt aussehen:

    ![Abrufen von Service Fabric-Versionen][getfabversions]
3. Starten Sie mithilfe des Windows PowerShell-Befehls [Start-ServiceFabricClusterUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterupgrade) ein Clusterupgrade auf eine verfügbare Version.

    ```powershell
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled-out example

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback
    ```
   Um den Fortschritt des Upgrades zu verfolgen, können Sie Service Fabric Explorer nutzen oder den folgenden Windows PowerShell-Befehl ausführen:

    ```powershell
    Get-ServiceFabricClusterUpgrade
    ```

    Wenn die Integritätsrichtlinien des Clusters nicht erfüllt sind, wird für das Upgrade ein Rollback ausgeführt. Wie Sie benutzerdefinierte Integritätsrichtlinien für den Befehl „Start-ServiceFabricClusterUpgrade“ angeben, erfahren Sie in der Dokumentation zu [Start-ServiceFabricClusterUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterupgrade).

    Nachdem Sie die Probleme behoben haben, die zu dem Rollback geführt haben, initiieren Sie das Upgrade erneut, indem Sie die gleichen Schritte wie zuvor beschrieben ausführen.

## <a name="upgrade-clusters-that-have-no-connectivity-to-download-the-latest-code-and-configuration"></a>Aktualisieren der Cluster *ohne Konnektivität* zum Herunterladen des aktuellen Codes und der Konfiguration
Führen Sie diese Schritte aus, um Ihren Cluster auf eine unterstützte Version upzugraden, wenn zwischen Ihrem Clusterknoten und dem [Microsoft Download Center](https://download.microsoft.com) keine Internetverbindung besteht.

> [!NOTE]
> Wenn Sie einen Cluster ohne Internetverbindung betreiben, prüfen Sie im [Service Fabric-Teamblog](https://blogs.msdn.microsoft.com/azureservicefabric/), ob ein neues Release verfügbar ist. Das System zeigt keine Clusterintegritätswarnung an, um Sie über neue Releases zu unterrichten.  
>
>

### <a name="auto-provisioning-vs-manual-provisioning"></a>Automatische Bereitstellung im Vergleich zur manuellen Bereitstellung
Richten Sie den Service Fabric-Updatedienst ein, um das automatische Herunterladen und Registrieren für die neueste Codeversion zu aktivieren. Eine Anleitung finden Sie im [eigenständigen Paket](service-fabric-cluster-standalone-package-contents.md) in der Datei *Tools\ServiceFabricUpdateService.zip\Readme_InstructionsAndHowTos.txt*.

Befolgen Sie für die manuelle Vorgehensweise die Anweisungen unten.

Ändern Sie die Clusterkonfiguration, um die folgende Eigenschaft auf *false* festzulegen, bevor Sie mit dem Konfigurationsupgrade beginnen:

```json
"fabricClusterAutoupgradeEnabled": false,
```

Nutzungsdetails finden Sie in den Informationen zum PowerShell-Befehl [Start-ServiceFabricClusterConfigurationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade). Achten Sie darauf, den Wert für „clusterConfigurationVersion“ in der JSON-Datei zu aktualisieren, bevor Sie das Konfigurationsupgrade starten.

```powershell
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
```

### <a name="cluster-upgrade-workflow"></a>Workflow für das Upgrade des Clusters

1. Führen Sie [Get-ServiceFabricClusterUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricclusterupgrade) auf einem der Knoten im Cluster aus, und notieren Sie sich den Wert von *TargetCodeVersion*.

2. Führen Sie Folgendes auf einem mit dem Internet verbundenen Computer aus, um alle mit dem Upgrade kompatiblen Versionen mit der aktuellen Version aufzulisten und das entsprechende Paket über die zugehörigen Downloadlinks herunterzuladen:

    ```powershell
    ###### Get list of all upgrade compatible packages  
    Get-ServiceFabricRuntimeUpgradeVersion -BaseVersion <TargetCodeVersion as noted in Step 1> 
    ```

3. Stellen Sie auf einem Computer mit Administratorzugriffsrechten auf alle Computer, die als Knoten in der Clusterkonfigurationsdatei aufgeführt sind, eine Verbindung mit dem Cluster her. Der Computer, auf dem dieses Skript ausgeführt wird, muss nicht Teil des Clusters sein.

    ```powershell
    ###### Get the list of available Service Fabric versions
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file including the path to it> -ImageStoreConnectionString "fabric:ImageStore"

    ###### Here is a filled-out example
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath .\MicrosoftAzureServiceFabric.5.3.301.9590.cab -ImageStoreConnectionString "fabric:ImageStore"
    ```
4. Kopieren Sie das heruntergeladene Paket in den Clusterimagespeicher.

5. Registrieren Sie das kopierte Paket.

    ```powershell
    ###### Get the list of available Service Fabric versions
    Register-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file>

    ###### Here is a filled-out example
    Register-ServiceFabricClusterPackage -Code -CodePackagePath .\MicrosoftAzureServiceFabric.5.3.301.9590.cab
    ```
6. Starten Sie ein Clusterupgrade auf eine verfügbare Version.

    ```powershell
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled-out example
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback
    ```
    Sie können den Fortschritt des Upgrades in Service Fabric Explorer oder durch Ausführen des folgenden PowerShell-Befehls verfolgen:

    ```powershell
    Get-ServiceFabricClusterUpgrade
    ```

    Wenn die Integritätsrichtlinien des Clusters nicht erfüllt sind, wird für das Upgrade ein Rollback ausgeführt. Wie Sie benutzerdefinierte Integritätsrichtlinien für den Befehl „Start-ServiceFabricClusterUpgrade“ angeben, erfahren Sie in der Dokumentation zu [Start-ServiceFabricClusterUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterupgrade).

    Nachdem Sie die Probleme behoben haben, die zu dem Rollback geführt haben, initiieren Sie das Upgrade erneut, indem Sie die gleichen Schritte wie zuvor beschrieben ausführen.

## <a name="next-steps"></a>Nächste Schritte
* [Upgraden der Konfiguration eines eigenständigen Clusters](service-fabric-cluster-config-upgrade-windows-server.md)
* Anpassen von [Service Fabric-Clustereinstellungen](service-fabric-cluster-fabric-settings.md)
* [Skalieren Ihres Clusters](service-fabric-cluster-scale-up-down.md)

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG
