---
title: 'Prüfbarkeit: Dienstkommunikation'
description: Die Kommunikation von Dienst zu Dienst ist ein wichtiger Integrationspunkt einer Service Fabric-Anwendung. In diesem Artikel werden Entwurfsaspekte und Testverfahren beschrieben.
ms.topic: conceptual
ms.date: 11/02/2017
ms.openlocfilehash: fbf889422d69d031d777acd80cf3becfb3184568
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96575923"
---
# <a name="service-fabric-testability-scenarios-service-communication"></a>Szenarien für die Service Fabric-Prüfbarkeit: Dienstkommunikation
Microservices und dienstorientierte Architekturstile können in Azure Service Fabric auf natürliche Weise genutzt werden. Bei diesen Arten von verteilten Architekturen setzen sich komponentenbasierte Microserviceanwendungen in der Regel aus mehreren Diensten zusammen, die miteinander kommunizieren müssen. Sogar im einfachsten Fall verfügen Sie im Allgemeinen mindestens über einen zustandslosen Webdienst und einen zustandsbehafteten Datenspeicherdienst, die miteinander kommunizieren müssen.

Die Kommunikation von Dienst zu Dienst ist ein entscheidender Integrationspunkt einer Anwendung, da von jedem Dienst eine Remote-API für andere Dienste verfügbar gemacht wird. Die Verwendung einer Gruppe von API-Grenzen mit E/A erfordert häufig besondere Sorgfalt und einen relativ hohen Test- und Überprüfungsaufwand.

Es gibt viele Punkte, die beachtet werden müssen, wenn diese Dienstgrenzen in einem verteilten System miteinander verknüpft sind:

* *Transportprotokoll*. Nutzen Sie HTTP, um für eine bessere Interoperabilität zu sorgen, oder ein benutzerdefiniertes binäres Protokoll, um einen maximalen Durchsatz zu erzielen?
* *Fehlerbehandlung*. Wie werden dauerhafte und vorübergehende Fehler behandelt? Was geschieht, wenn ein Dienst auf einen anderen Knoten verschoben wird?
* *Timeouts und Latenz*. Wie behandeln die einzelnen Dienstschichten in Anwendungen mit mehreren Ebenen die Wartezeit über den Stapel und zum Benutzer?

Das Testen der Interaktion zwischen Ihren Diensten ist wichtig, um die Resilienz Ihrer Anwendung sicherzustellen. Dabei spielt es keine Rolle, ob Sie eine der integrierten Komponenten für die Dienstkommunikation von Service Fabric verwenden oder eine eigene Komponente erstellen.

## <a name="prepare-for-services-to-move"></a>Vorbereiten von Dienste auf das Verschieben
Dienstinstanzen können im Laufe der Zeit verschoben werden. Dies gilt insbesondere, wenn sie mit Auslastungsmetriken für einen maßgeschneiderten optimalen Lastenausgleich für Ressourcen konfiguriert werden. Service Fabric verschiebt Ihre Dienstinstanzen zur Maximierung der Verfügbarkeit sogar während Upgrades, Failovers, horizontalem Hochskalieren und verschiedenen anderen Situationen, die während der Lebensdauer eines verteilten Systems eintreten.

Wenn Dienste im Cluster verschoben werden, gibt es zwei Szenarien, auf deren Behandlung Ihre Clients und Dienste bei der Kommunikation mit einem Dienst vorbereitet sein sollten:

* Die Dienstinstanz oder das Partitionsreplikat wurden verschoben, seitdem Sie das letzte Mal damit kommuniziert haben. Dies ist ein normaler Bestandteil eines Dienstlebenszyklus, und es ist zu erwarten, dass dies während der Lebensdauer Ihrer Anwendung vorkommt.
* Die Dienstinstanz oder das Partitionsreplikat wird gerade verschoben. Obwohl der Failovervorgang eines Diensts von einem Knoten auf einen anderen Knoten in Service Fabric sehr schnell erfolgt, kann es bei der Verfügbarkeit zu einer Verzögerung kommen, falls die Kommunikationskomponente Ihrer Dienste langsam gestartet werden.

Die ordnungsgemäße Behandlung dieser Szenarien ist wichtig dafür, dass das System reibungslos ausgeführt werden kann. Bedenken Sie dazu Folgendes:

* Jeder Dienst, mit dem eine Verbindung hergestellt werden kann, verfügt über eine *Adresse*, an der er lauscht (z.B. HTTP oder WebSockets). Wenn eine Dienstinstanz oder Partition verschoben wird, ändert sich der dazugehörige Adressendpunkt. (Er wechselt zu einem anderen Knoten mit einer anderen IP-Adresse.) Wenn Sie die integrierten Kommunikationskomponenten verwenden, werden darüber die Dienstadressen neu für Sie aufgelöst.
* Unter Umständen kann es zu einem vorübergehenden Anstieg der Dienstwartezeit kommen, während die Dienstinstanz ihren Listener neu startet. Dies hängt davon ab, wie schnell der Dienst den Listener öffnet, nachdem die Instanz verschoben wurde.
* Alle vorhandenen Verbindungen müssen geschlossen und wieder geöffnet werden, nachdem der Dienst einen neuen Knoten öffnet. Beim ordnungsgemäßen Herunterfahren oder Neustart eines Knotens wird Zeit dafür veranschlagt, die für das ordnungsgemäße Herunterfahren vorhandener Verbindungen anfällt.

### <a name="test-it-move-service-instances"></a>Testen: Verschieben von Dienstinstanzen
Mit den Testability-Tools von Service Fabric können Sie ein Testszenario zum Testen dieser Situationen auf unterschiedliche Weise erstellen.

1. Verschieben Sie das primäre Replikat eines zustandsbehafteten Diensts.
   
    Das primäre Replikat einer zustandsbehafteten Dienstpartition kann aus verschiedenen Gründen verschoben werden. Nutzen Sie dies für das primäre Replikat einer bestimmten Partition, um auf kontrollierte Weise zu ermitteln, wie Ihre Dienste auf die Verschiebung reagieren.
   
    ```powershell
   
    PS > Move-ServiceFabricPrimaryReplica -PartitionId 6faa4ffa-521a-44e9-8351-dfca0f7e0466 -ServiceName fabric:/MyApplication/MyService
   
    ```
2. Beenden Sie einen Knoten.
   
    Wenn ein Knoten beendet wird, verschiebt Service Fabric alle Dienstinstanzen oder -partitionen, die sich auf dem Knoten befunden haben, auf einen der anderen verfügbaren Knoten im Cluster. Nutzen Sie dies zum Testen einer Situation, in der ein Knoten in Ihrem Cluster verloren geht und alle Dienstinstanzen und Replikate auf dem Knoten verschoben werden müssen.
   
    Sie können einen Knoten mithilfe des PowerShell-Cmdlets **Stop-ServiceFabricNode** beenden:
   
    ```powershell
   
    PS > Stop-ServiceFabricNode -NodeName Node_1
   
    ```

## <a name="maintain-service-availability"></a>Aufrechterhalten der Dienstverfügbarkeit
Service Fabric ist eine Plattform, mit der für Ihre Dienste Hochverfügbarkeit erzielt werden soll. In Extremfällen können zugrunde liegende Infrastrukturprobleme trotzdem noch zum Ausfall der Verfügbarkeit führen. Es ist wichtig, auch für diese Szenarien Tests durchzuführen.

Für zustandsbehaftete Dienste wird ein quorumbasiertes System zum Replizieren des Zustands mit dem Ziel der Hochverfügbarkeit verwendet. Dies bedeutet, dass ein Quorum von Replikaten verfügbar sein muss, um Schreibvorgänge ausführen zu können. In seltenen Fällen, z. B. bei einem umfangreicheren Hardwarefehler, ist unter Umständen kein Quorum von Replikaten verfügbar. Sie können dann keine Schreibvorgänge ausführen, während Lesevorgänge weiterhin möglich sind.

### <a name="test-it-write-operation-unavailability"></a>Testen: Nichtverfügbarkeit von Schreibvorgängen
Mithilfe der Testability-Tools in Service Fabric können Sie als Test einen Fehler einfügen, der Quorumverlust ausgelöst. Ein solches Szenario ist zwar selten, aber es ist trotzdem ist es wichtig, dass Clients und Dienste, die von einem zustandsbehafteten Dienst abhängig sind, auf die Behandlung von Situationen vorbereitet sind, in denen keine Schreibanforderungen an den Dienst möglich sind. Außerdem ist es wichtig, dass der zustandsbehaftete Dienst selbst über diese Möglichkeit informiert ist und mit Aufrufern ordnungsgemäß kommunizieren kann.

Mithilfe des PowerShell-Cmdlets **Invoke-ServiceFabricPartitionQuorumLoss** kann Quorumverlust ausgelöst werden:

```powershell

PS > Invoke-ServiceFabricPartitionQuorumLoss -ServiceName fabric:/Myapplication/MyService -QuorumLossMode QuorumReplicas -QuorumLossDurationInSeconds 20

```

In diesem Beispiel wird `QuorumLossMode` auf `QuorumReplicas` festgelegt, um anzugeben, dass ein Quorumverlust ausgelöst werden soll, ohne alle Replikate herunterzufahren. Auf diese Weise sind Lesevorgänge weiterhin möglich. Um ein Szenario zu testen, in dem eine gesamte Partition nicht verfügbar ist, können Sie diesen Schalter auf `AllReplicas`festlegen.

## <a name="next-steps"></a>Nächste Schritte
[Weitere Informationen zu Testability-Aktionen](service-fabric-testability-actions.md)

[Weitere Informationen zu Testability-Szenarien](service-fabric-testability-scenarios.md)

