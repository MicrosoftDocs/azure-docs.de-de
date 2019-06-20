---
title: Virtuelle Netzwerkarchitektur mit Azure HDInsight
description: Lernen Sie die Ressourcen kennen, die beim Erstellen eines HDInsight-Clusters in einem virtuellen Azure-Netzwerk verfügbar sind.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: hrasheed
ms.openlocfilehash: 41420497bffd0abdc598e4c86b2dbda1466b2ce1
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/28/2019
ms.locfileid: "66252845"
---
# <a name="azure-hdinsight-virtual-network-architecture"></a>Virtuelle Netzwerkarchitektur mit Azure HDInsight

In diesem Artikel werden die Ressourcen erläutert, die beim Bereitstellen eines HDInsight-Clusters in einem benutzerdefinierten virtuellen Azure-Netzwerk vorhanden sind. Diese Informationen sollen Ihnen dabei helfen, Ihre lokalen Ressourcen mit Ihrem HDInsight-Cluster in Azure zu verbinden. Weitere Informationen zu virtuellen Azure-Netzwerken finden Sie unter [Was ist Azure Virtual Network?](../virtual-network/virtual-networks-overview.md).

## <a name="resource-types-in-azure-hdinsight-clusters"></a>Ressourcentypen in Azure HDInsight-Clustern

In Azure HDInsight-Clustern gibt es unterschiedliche Typen virtueller Computer bzw. Knoten. Jeder Knotentyp spielt eine Rolle beim Betrieb des Systems. In der folgenden Tabelle sind diese Knotentypen und ihre Rollen im Cluster zusammengefasst.

| Type | BESCHREIBUNG |
| --- | --- |
| Hauptknoten |  Bei allen Clustertypen, mit Ausnahme von Apache Storm, hosten die Hauptknoten die Prozesse, welche die Ausführung der verteilten Anwendung verwalten. Der Hauptknoten ist auch der Knoten, zu dem Sie eine SSH-Verbindung herstellen und mit dem Sie Anwendungen ausführen können, die dann für die Ausführung mit den Clusterressourcen koordiniert werden. Die Anzahl der Hauptknoten ist bei allen Clustertypen auf zwei (2) festgelegt. |
| ZooKeeper-Knoten | Zookeeper koordiniert Aufgaben zwischen den Knoten, welche die Datenverarbeitung ausführen. Er nimmt auch die Auswahl des führenden Hauptknotens vor, und verfolgt, welcher Hauptknoten einen bestimmten Master-Dienst ausführt. Die Anzahl der ZooKeeper-Knoten ist auf drei festgelegt. |
| Workerknoten | Dies sind die Knoten, welche die Datenverarbeitungsfunktionen unterstützen. Workerknoten können dem Cluster hinzugefügt oder aus dem Cluster entfernt werden, um die Computingleistung zu skalieren und die Kosten zu verwalten. |
| R Server-Edgeknoten | Der R Server-Edgeknoten ist der Knoten, zu dem Sie eine SSH-Verbindung herstellen und mit dem Sie Anwendungen ausführen können, die dann für die Ausführung mit den Clusterressourcen koordiniert werden. Ein Edgeknoten ist nicht an der Datenanalyse innerhalb des Clusters beteiligt. Dieser Knoten hostet außerdem den R Studio-Server, sodass Sie zum Ausführen von R-Anwendungen einen Browser verwenden können. |
| Regionsknoten | Beim HBase-Clustertyp führt der Regionsknoten (auch als Datenknoten bezeichnet) den Regionsserver aus. Regionsserver stellen einen Teil der von HBase verwalteten Daten bereit und verwalten diese. Regionsknoten können dem Cluster hinzugefügt oder aus dem Cluster entfernt werden, um die Computingleistung zu skalieren und die Kosten zu verwalten.|
| Nimbusknoten | Beim Storm-Clustertyp stellt der Nimbusknoten eine dem Hauptknoten vergleichbare Funktionalität bereit. Der Nimbusknoten weist anderen Knoten im Cluster Aufgaben über den Zookeeper-Knoten zu, der die Ausführung der Storm-Topologien koordiniert. |
| Supervisorknoten | Beim Storm-Clustertyp führt der Supervisorknoten die vom Nimbusknoten bereitgestellten Anweisungen aus, um die gewünschte Verarbeitung vorzunehmen. |

## <a name="basic-virtual-network-resources"></a>Allgemeine virtuelle Netzwerkressourcen

Das folgende Diagramm zeigt die Platzierung von HDInsight-Knoten und Netzwerkressourcen in Azure.

![Diagramm der HDInsight-Entitäten, erstellt in einem benutzerdefinierten virtuellen Azure-Netzwerk](./media/hdinsight-virtual-network-architecture/vnet-diagram.png)

Zu den Standardressourcen, die beim Bereitstellen von HDInsight in einem virtuellen Azure-Netzwerk vorhanden sind, zählen die in der obigen Tabelle aufgeführten Clusterknotentypen sowie Netzwerkgeräte, welche die Kommunikation zwischen dem virtuellen Netzwerk und externen Netzwerken unterstützen.

Die folgende Tabelle enthält die neun Clusterknoten, die erstellt werden, wenn HDInsight in einem benutzerdefinierten virtuellen Azure-Netzwerk bereitgestellt wird.

| Ressourcentyp | Vorhandene Anzahl | Details |
| --- | --- | --- |
|Hauptknoten | two |    |
|ZooKeeper-Knoten | three | |
|Workerknoten | two | Diese Anzahl kann sich je nach Clusterkonfiguration und Skalierung ändern. Für Apache Kafka sind mindestens drei Workerknoten erforderlich.  |
|Gatewayknoten | two | Gatewayknoten sind virtuelle Azure-Computer, die zwar in Azure erstellt, in Ihrem Abonnement jedoch nicht angezeigt werden. Wenden Sie sich an den Support, wenn Sie diese Knoten neu starten müssen. |

Die folgenden Netzwerkressourcen werden innerhalb des virtuellen Netzwerks, das mit HDInsight verwendet wird, automatisch erstellt:

| Netzwerkressource | Vorhandene Anzahl | Details |
| --- | --- | --- |
|Load Balancer | three | |
|Netzwerkschnittstellen | neun | Dieser Wert basiert auf einem normalen Cluster, in dem jeder Knoten eine eigene Netzwerkschnittstelle hat. Die neun Schnittstellen sind für die in der vorherigen Tabelle beschriebenen Knoten bestimmt: zwei Hauptknoten, drei Zookeeper-Knoten, zwei Workerknoten und zwei Gatewayknoten. |
|Öffentliche IP-Adressen | two |    |

## <a name="endpoints-for-connecting-to-hdinsight"></a>Endpunkte zum Herstellen einer Verbindung mit HDInsight

Sie können auf drei verschiedene Arten auf Ihren HDInsight-Cluster zugreifen:

- HTTPS-Endpunkt außerhalb des virtuellen Netzwerks in `CLUSTERNAME.azurehdinsight.net`.
- SSH-Endpunkt für die direkte Verbindung mit dem Hauptknoten in `CLUSTERNAME-ssh.azurehdinsight.net`.
- HTTPS-Endpunkt innerhalb des virtuellen Netzwerks `CLUSTERNAME-int.azurehdinsight.net`. Achten Sie auf die Zeichenfolge „-int“ in dieser URL. Dieser Endpunkt wird in eine private IP-Adresse im virtuellen Netzwerk aufgelöst und kann nicht über das öffentliche Internet aufgerufen werden.

Diesen drei Endpunkten wird jeweils ein Lastenausgleich zugewiesen.

Öffentliche IP-Adressen werden auch für die beiden Endpunkte bereitgestellt, die eine externe Verbindung (von außerhalb des virtuellen Netzwerks) zulassen.

1. Eine öffentliche IP-Adresse wird dem Lastenausgleich als vollständig qualifizierter Domänenname (FQDN) zugewiesen, der beim Herstellen einer Verbindung mit dem Cluster über das Internet verwendet wird `CLUSTERNAME.azurehdinsight.net`.
1. Die zweite öffentliche IP-Adresse wird für den Domänennamen verwendet, der ausschließlich für die SSH-Verbindung bestimmt ist `CLUSTERNAME-ssh.azurehdinsight.net`.

## <a name="next-steps"></a>Nächste Schritte

* [Sichern des eingehenden Datenverkehrs für HDInsight-Cluster in einem virtuellen Netzwerk mit privatem Endpunkt](https://azure.microsoft.com/blog/secure-incoming-traffic-to-hdinsight-clusters-in-a-vnet-with-private-endpoint/)
