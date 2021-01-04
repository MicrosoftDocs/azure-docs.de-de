---
title: Behandeln von Problemen beim Einrichten des lokalen Azure Service Fabric-Clusters
description: Dieser Artikel enthält eine Reihe von Vorschlägen für die Problembehandlung Ihres lokalen Entwicklungsclusters.
ms.topic: conceptual
ms.date: 02/23/2018
ms.openlocfilehash: 20948cd1626c02d73fb6e9ef096b552bbab627fb
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96575906"
---
# <a name="troubleshoot-your-local-development-cluster-setup"></a>Problembehandlung beim Einrichten des Clusters für die lokale Entwicklung
Wenn beim Interagieren mit Ihrem lokalen Azure Service Fabric Entwicklungscluster ein Problem auftritt, sind bei der Lösung unter Umständen die folgenden Vorschläge hilfreich.

## <a name="cluster-setup-failures"></a>Fehler bei der Clustereinrichtung
### <a name="cannot-clean-up-service-fabric-logs"></a>Bereinigen von Service Fabric-Protokollen nicht möglich
#### <a name="problem"></a>Problem
Beim Ausführen des DevClusterSetup-Skripts tritt der folgende Fehler auf:

```output
Cannot clean up C:\SfDevCluster\Log fully as references are likely being held to items in it. Please remove those and run this script again.
At line:1 char:1 + .\DevClusterSetup.ps1
+ ~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo : NotSpecified: (:) [Write-Error], WriteErrorException
+ FullyQualifiedErrorId : Microsoft.PowerShell.Commands.WriteErrorException,DevClusterSetup.ps1
```

#### <a name="solution"></a>Lösung
Schließen Sie das aktuelle PowerShell-Fenster, und öffnen Sie ein neues PowerShell-Fenster als Administrator. Nun kann das Skript erfolgreich ausgeführt werden.

## <a name="cluster-connection-failures"></a>Clusterverbindungsfehler

### <a name="type-initialization-exception"></a>Ausnahme bei der Typinitialisierung
#### <a name="problem"></a>Problem
Wenn Sie eine Verbindung mit dem Cluster in PowerShell oder im Service Fabric Explorer herstellen, wird für System.Fabric.Common.AppTrace eine TypeInitializationException angezeigt.

#### <a name="solution"></a>Lösung
Die Pfadvariable wurde während der Installation nicht ordnungsgemäß festgelegt. Melden Sie sich von Windows ab und wieder an. Dadurch wird der Pfad aktualisiert.

### <a name="cluster-connection-fails-with-object-is-closed"></a>Fehler „Objekt ist geschlossen“ für Clusterverbindung
#### <a name="problem"></a>Problem
Für einen Aufruf von Connect-ServiceFabricCluster tritt ein Fehler der folgenden Art auf:

```output
Connect-ServiceFabricCluster : The object is closed.
At line:1 char:1
+ Connect-ServiceFabricCluster
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo : InvalidOperation: (:) [Connect-ServiceFabricCluster], FabricObjectClosedException
+ FullyQualifiedErrorId : CreateClusterConnectionErrorId,Microsoft.ServiceFabric.Powershell.ConnectCluster
```

#### <a name="solution"></a>Lösung
Schließen Sie das aktuelle PowerShell-Fenster, und öffnen Sie ein neues PowerShell-Fenster als Administrator.

### <a name="fabric-connection-denied-exception"></a>Ausnahme „Fabric-Verbindung verweigert“
#### <a name="problem"></a>Problem
Beim Debuggen von Visual Studio erhalten Sie einen FabricConnectionDeniedException-Fehler.

#### <a name="solution"></a>Lösung
Dieser Fehler tritt in der Regel auf, wenn Sie versuchen, einen Diensthostprozess manuell zu starten.

Stellen Sie sicher, dass in Ihrer Projektmappe keine Dienstprojekte als Startprojekte festgelegt sind. Nur Service Fabric-Anwendungsprojekte sollten als Startprojekte festgelegt werden.

> [!TIP]
> Wenn sich der lokale Cluster nach dem Setup ungewöhnlich verhält, können Sie ihn mit der Taskleistenanwendung „Lokaler Cluster-Manager“ zurücksetzen. Dadurch wird der vorhandene Cluster entfernt und ein neuer Cluster eingerichtet. Beachten Sie, dass alle bereitgestellten Anwendungen und zugehörigen Daten entfernt werden.
> 
> 

## <a name="next-steps"></a>Nächste Schritte
* [Grundlagen und Problembehandlung für Cluster mit Systemintegritätsberichten](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
* [Visualisieren Ihres Clusters mit Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)

