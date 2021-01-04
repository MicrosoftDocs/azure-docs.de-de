---
title: Azure DDoS Protection – Referenzarchitekturen
description: Machen Sie sich mit den Azure DDoS Protection-Referenzarchitekturen vertraut.
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: e5472620fe9b07d152a5325b0654044cb1505fd7
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94992436"
---
# <a name="ddos-protection-reference-architectures"></a>DDoS Protection – Referenzarchitekturen

DDoS Protection Standard ist für Dienste vorgesehen, die in einem virtuellen Netzwerk bereitgestellt werden. Weitere Informationen finden Sie unter [Virtual network integration for Azure services (Integration virtueller Netzwerke für Azure-Dienste)](../virtual-network/virtual-network-for-azure-services.md). Für andere Dienste gilt der Standarddienst DDoS Protection Basic. Die folgenden Referenzarchitekturen sind nach Szenarios angeordnet, wobei Architekturmuster gruppiert sind.

## <a name="virtual-machine-windowslinux-workloads"></a>Die Arbeitsauslastungen virtueller Computer (Windows/Linux)

### <a name="application-running-on-load-balanced-vms"></a>Anwendung wird auf VMs mit Lastenausgleichsmodul ausgeführt

Diese Referenzarchitektur zeigt eine Reihe von bewährten Methoden für die Ausführung mehrerer virtueller Windows-Computer (VMs) in einer Skalierungsgruppe hinter einem Lastenausgleichsmodul, um die Verfügbarkeit und Skalierbarkeit zu verbessern. Diese Architektur kann für eine zustandslose Arbeitsauslastung wie einen Webserver verwendet werden.

![Diagramm der Referenzarchitektur für Anwendungen, die auf VMs mit Lastenausgleichsmodul ausgeführt werden](./media/ddos-best-practices/image-9.png)

In dieser Architektur wird eine Workload auf mehrere VM-Instanzen verteilt. Es gibt eine einzelne öffentliche IP-Adresse, und der Internetdatenverkehr wird durch ein Lastenausgleichsmodul auf die VMs verteilt. DDoS Protection Standard ist im virtuellen Netzwerk des Azure-Lastenausgleichsmoduls (Internet) aktiviert, dem die öffentliche IP-Adresse zugeordnet wurde.

Beim Lastenausgleich werden die eingehenden Internetanforderungen an die VM-Instanzen verteilt. Virtual Machine Scale Sets ermöglichen das horizontale Hoch- oder Herunterskalieren der Anzahl der VMs – sowohl manuell als auch automatisch basierend auf vordefinierten Regeln. Dies ist wichtig, wenn die Ressource einem DDoS-Angriff ausgesetzt ist. Lesen Sie [diesen Artikel](/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm), um weitere Informationen zu dieser Referenzarchitektur zu erhalten.

### <a name="application-running-on-windows-n-tier"></a>Auf Windows-n-Schichten ausgeführte Anwendung

Es gibt viele Möglichkeiten für die Implementierung einer n-schichtigen Architektur. Das folgende Diagramm zeigt eine typische dreischichtige Webanwendung. Diese Architektur baut auf dem Artikel [Ausführen von VMs mit Lastenausgleich zur Steigerung von Skalierbarkeit und Verfügbarkeit](/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm) auf. In der Internet- und Unternehmensschicht werden VMs mit Lastenausgleich verwendet.

![Diagramm der Referenzarchitektur für Anwendung, die auf Windows-n-Schichten ausgeführt wird](./media/ddos-best-practices/image-10.png)

In dieser Architektur ist DDoS Protection Standard im virtuellen Netzwerk aktiviert. Für alle öffentlichen IP-Adressen im virtuellen Netzwerk ist DDoS Protection für Schicht 3 und Schicht 4 aktiviert. Stellen Sie für den Schutz von Layer 7 Application Gateway in der WAF-SKU bereit. Lesen Sie [diesen Artikel](/azure/architecture/reference-architectures/virtual-machines-windows/n-tier), um weitere Informationen zu dieser Referenzarchitektur zu erhalten.

> [!NOTE]
> Szenarien, in denen eine einzelne VM mit einer öffentlichen IP-Adresse ausgeführt wird, werden nicht unterstützt.

### <a name="paas-web-application"></a>PaaS-Webanwendung

Diese Referenzarchitektur zeigt die Ausführung einer Azure App Service-Anwendung in einer einzelnen Region. Diese Architektur demonstriert eine Reihe empfohlener Verfahren für eine Webanwendung, die [Azure App Service](https://azure.microsoft.com/documentation/services/app-service/) und [Azure SQL-Datenbank](https://azure.microsoft.com/documentation/services/sql-database/) verwendet.
Die Standby-Region ist für Failoverszenarios eingerichtet.

![Diagramm der Referenzarchitektur für eine PaaS-Webanwendung](./media/ddos-best-practices/image-11.png)

Azure Traffic Manager leitet eingehende Anforderungen an Application Gateway in einer der Regionen weiter. Während des normalen Betriebs werden Anforderungen an Application Gateway in der aktiven Region weitergeleitet. Wenn diese Region nicht mehr verfügbar ist, führt Traffic Manager ein Failover zu Application Gateway in der Standby-Region aus.

Der gesamte für die Webanwendung bestimmte Datenverkehr aus dem Internet wird über Traffic Manager an die öffentliche Application Gateway-IP-Adresse weitergeleitet. (siehe [Application Gateway support for multi-tenant back ends (Application Gateway-Unterstützung für mehrinstanzenfähige Back-Ends)](../application-gateway/application-gateway-web-app-overview.md)). In diesem Szenario ist der App-Dienst (Web-Apps) selbst nicht direkt nach außen gerichtet und wird von Application Gateway geschützt. 

Sie sollten die Application Gateway-WAF-SKU (Verhinderungsmodus) zum Schutz vor Angriffen auf Schicht 7 (HTTP/HTTPS/Websocket) konfigurieren. Darüber hinaus werden die Web-Apps so konfiguriert, dass sie Datenverkehr nur über die Application Gateway-IP-Adresse akzeptieren (siehe [IP and Domain Restrictions for Windows Azure Web Sites (IP- und Domäneneinschränkungen für Windows Azure-Websites)](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/)).

Lesen Sie [diesen Artikel](/azure/architecture/reference-architectures/app-service-web-app/multi-region), um weitere Informationen zu dieser Referenzarchitektur zu erhalten.

## <a name="mitigation-for-non-web-paas-services"></a>Entschärfung für Non-Web-PaaS-Dienste

### <a name="hdinsight-on-azure"></a>HDInsight unter Azure

Diese Referenzarchitektur zeigt das Konfigurieren von DDoS Protection Standard für [Azure HDInsight-Cluster](../hdinsight/index.yml). Stellen Sie sicher, dass der HDInsight-Cluster mit einem virtuellen Netzwerk verknüpft und DDoS Protection in diesem virtuellen Netzwerk aktiviert ist.

![Bereiche „HDInsight“ und „Erweiterte Einstellungen“ mit Einstellungen zum virtuellen Netzwerk](./media/ddos-best-practices/image-12.png)

![Auswahl für Aktivierung von DDoS Protection](./media/ddos-best-practices/image-13.png)

In dieser Architektur wird der für den HDInsight-Cluster bestimmte Datenverkehr aus dem Internet an die öffentliche IP-Adresse weitergeleitet, die dem HDInsight-Gatewaylastenausgleich zugeordnet ist. Der Gatewaylastenausgleich sendet dann den Datenverkehr direkt an die Hauptknoten oder Workerknoten. Da DDoS Protection Standard im virtuellen HDInsight-Netzwerk aktiviert wird, erhalten alle öffentlichen IP-Adressen im virtuellen Netzwerk DDoS-Schutz für Schicht 3 und Schicht 4. Diese Referenzarchitektur kann mit Referenzarchitekturen für n-Schichten und mehrere Regionen kombiniert werden.

Weitere Informationen zu dieser Referenzarchitektur finden Sie in der Dokumentation [Erweitern von Azure HDInsight per Azure Virtual Network](../hdinsight/hdinsight-plan-virtual-network-deployment.md?toc=%2fazure%2fvirtual-network%2ftoc.json).


> [!NOTE]
> Weder die Azure App Service-Umgebung für PowerApps noch die API-Verwaltung in einem virtuellen Netzwerk mit öffentlicher IP-Adresse werden nativ unterstützt.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie [einen DDoS-Schutzplan erstellen](manage-ddos-protection.md).