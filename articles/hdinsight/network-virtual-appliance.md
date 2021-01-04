---
title: Konfigurieren eines virtuellen Netzwerkgeräts in Azure HDInsight
description: Erfahren Sie, wie Sie eine Reihe zusätzlicher Features für Ihr virtuelles Netzwerkgerät in Azure HDInsight konfigurieren.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 06/30/2020
ms.openlocfilehash: 557f0a6a37747d3a461ced8de16fd1fcf0d1abab
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95524112"
---
# <a name="configure-network-virtual-appliance-in-azure-hdinsight"></a>Konfigurieren eines virtuellen Netzwerkgeräts in Azure HDInsight

> [!Important]
> Die folgenden Informationen sind **nur** erforderlich, wenn Sie ein anderes virtuelles Netzwerkgerät (Network Virtual Appliance, NVA) als [Azure Firewall](./hdinsight-restrict-outbound-traffic.md) konfigurieren möchten.

Das FQDN-Tag in Azure Firewall wird automatisch so konfiguriert, dass der Datenverkehr für viele der gängigen, wichtigen FQDNs zugelassen wird. Bei Verwendung einer anderen virtuellen Netzwerkappliance müssen Sie eine Reihe zusätzlicher Features konfigurieren. Bedenken Sie beim Konfigurieren Ihrer virtuellen Netzwerkappliance Folgendes:

* Dienstendpunktfähige Dienste können mit Dienstendpunkten konfiguriert werden, was zu einer Umgehung des virtuellen Netzwerkgeräts führt (in der Regel aus Kosten- oder Leistungsüberlegungen).
* Wenn ResourceProviderConnection auf *Outbound* (Ausgehend) festgelegt ist, können Sie private Endpunkte für die Speicher- und SQL Server-Instanzen für Metastores verwenden, und es ist nicht erforderlich, sie dem NVA hinzuzufügen.
* IP-Adressabhängigkeiten gelten für Nicht-HTTP/S-Datenverkehr (TCP- und UDP-Datenverkehr).
* FQDN HTTP/HTTPS-Endpunkte können auf Ihrem NVA-Gerät genehmigt werden.
* Weisen Sie die Routingtabelle zu, die Sie für Ihr HDInsight-Subnetz erstellen.

## <a name="service-endpoint-capable-dependencies"></a>Dienstendpunktfähige Abhängigkeiten

Optional können Sie mindestens einen der folgenden Dienstendpunkte aktivieren, um das virtuelle Netzwerkgerät zu umgehen. Diese Option kann für große Mengen an Datenübertragungen nützlich sein, um Kosten zu sparen und außerdem Leistungsoptimierungen zu erzielen. 

| **Endpunkt** |
|---|
| Azure SQL |
| Azure Storage |
| Azure Active Directory |

### <a name="ip-address-dependencies"></a>IP-Adressabhängigkeiten

| **Endpunkt** | **Details** |
|---|---|
| [Hier](hdinsight-management-ip-addresses.md) veröffentlichte IP-Adressen | Diese IP-Adressen gelten für die HDInsight-Ressourcenanbieter und sollten in der UDR enthalten sein, um asymmetrisches Routing zu vermeiden. Diese Regel ist nur erforderlich, wenn ResourceProviderConnection auf *Inbound* (Eingehend) festgelegt ist. Wenn ResourceProviderConnection auf *Outbound* (Ausgehend) festgelegt ist, werden diese IP-Adressen in der UDR nicht benötigt.  |
| Private AAD-DS-IP-Adressen | Nur für ESP-Cluster erforderlich, wenn die VNETs nicht per Peering verknüpft wurden.|


### <a name="fqdn-httphttps-dependencies"></a>FQDN-HTTP/HTTPS-Abhängigkeiten

Sie finden [in diesem Repository](https://github.com/Azure-Samples/hdinsight-fqdn-lists/) die Liste der FQDN-Abhängigkeiten (größtenteils Azure Storage und Azure Service Bus) zur Konfiguration Ihres virtuellen Netzwerkgeräts. Die regionale Liste finden Sie [hier](https://github.com/Azure-Samples/hdinsight-fqdn-lists/tree/master/Regional). Diese Abhängigkeiten werden vom HDInsight-Ressourcenanbieter (RP) genutzt, um Cluster erfolgreich zu erstellen und zu überwachen/verwalten. Dazu gehören Telemetrie-/Diagnoseprotokolle, Bereitstellungsmetadaten, clusterbezogene Konfigurationen, Skripts usw. Die Liste der FQDN-Abhängigkeiten könnte sich mit der Veröffentlichung künftiger HDInsight-Updates ändern.

In der folgenden Liste sind nur einige FQDNs aufgeführt, die für das Patchen von Betriebssystemen und Sicherheitspatches oder Zertifikatüberprüfungen *nach* der Erstellung des Clusters und während der Lebensdauer der Clustervorgänge erforderlich sein können:

| **FQDNs für Laufzeitabhängigkeiten**                                                          |
|---|
| azure.archive.ubuntu.com:80                                           |
| security.ubuntu.com:80                                                |
| ocsp.msocsp.com:80                                                    |
| ocsp.digicert.com:80                                                  |
| microsoft.com/pki/mscorp/cps/default.htm:443                                      |
| microsoft.com:80                                                      |
|login.windows.net:443                                                  |
|login.microsoftonline.com:443                                          |

## <a name="next-steps"></a>Nächste Schritte

* [Verwenden einer Firewall zum Einschränken des ausgehenden Datenverkehrs](./hdinsight-restrict-outbound-traffic.md)
* [Virtuelle Netzwerkarchitektur mit Azure HDInsight](hdinsight-virtual-network-architecture.md)
