---
title: Problembehandlung bei Konnektivitätsproblemen mit privaten Endpunkten in Azure
description: Ausführliche Anleitung zur Diagnose der Konnektivität von privaten Endpunkten
services: private-endpoint
documentationcenter: na
author: rdhillon
manager: narayan
editor: ''
ms.service: private-link
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/31/2020
ms.author: rdhillon
ms.openlocfilehash: f861f9efa6ecc1886647ed6c460b6718ff97e8a1
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95522327"
---
# <a name="troubleshoot-azure-private-endpoint-connectivity-problems"></a>Behandeln von Problemen mit der Konnektivität privater Azure-Endpunkte

In diesem Artikel erfahren Sie Schritt für Schritt, wie Sie die Konnektivitätseinrichtung für Ihren privaten Azure-Endpunkt überprüfen und diagnostizieren.

Ein privater Azure-Endpunkt ist eine Netzwerkschnittstelle, über die eine private und sichere Verbindung mit einem Private Link-Dienst hergestellt wird. Diese Lösung unterstützt Sie beim Schützen Ihrer Workloads in Azure, indem Ihnen private Konnektivität mit Ihren Azure-Dienstressourcen über Ihr virtuelles Netzwerk bereitgestellt wird. Durch diese Lösung werden die Dienste effektiv in Ihr virtuelles Netzwerk eingebunden.

Folgende Konnektivitätsszenarien mit privatem Endpunkt sind verfügbar:

- Virtuelles Netzwerk aus der gleichen Region
- Virtuelle Netzwerke mit regionalem Peering
- Virtuelle Netzwerke mit globalem Peering
- Lokale Kunden über VPN oder ExpressRoute-Verbindungen

## <a name="diagnose-connectivity-problems"></a>Diagnostizieren von Konnektivitätsproblemen 

Vergewissern Sie sich mithilfe der folgenden Schritte, dass alle üblichen Konfigurationen ordnungsgemäß festgelegt sind, um Konnektivitätsprobleme mit Ihren privaten Endpunkten zu beheben.

1. Überprüfen Sie die Konfiguration des privaten Endpunkts, indem Sie die Ressource durchsuchen.

    a. Navigieren Sie zum **Private Link-Center**.

      ![Private Link-Center](./media/private-endpoint-tsg/private-link-center.png)

    b. Wählen Sie im linken Bereich **Private Endpunkte** aus.
    
      ![Private Endpunkte](./media/private-endpoint-tsg/private-endpoints.png)

    c. Filtern Sie nach dem zu diagnostizierenden privaten Endpunkt, und wählen Sie ihn aus.

    d. Überprüfen Sie die Informationen zum virtuellen Netzwerk und die DNS-Informationen.
     - Vergewissern Sie sich, dass der Verbindungsstatus **Genehmigt** lautet.
     - Vergewissern Sie sich, dass der virtuelle Computer mit dem virtuellen Netzwerk verbunden ist, von dem die privaten Endpunkte gehostet werden.
     - Vergewissern Sie sich, dass die FQDN-Informationen und die private IP-Adresse zugewiesen sind, und kopieren Sie den FQDN.
    
       ![VNET- und DNS-Konfiguration](./media/private-endpoint-tsg/vnet-dns-configuration.png)
    
1. Überprüfen Sie mithilfe von [Azure Monitor](../azure-monitor/overview.md), ob Daten übermittelt werden.

    a. Wählen Sie unter der privaten Endpunktressource **Überwachen** aus.
     - Wählen Sie **Eingehende Daten** oder **Ausgehende Daten** aus. 
     - Überprüfen Sie, ob Daten übermittelt werden, wenn Sie versuchen, eine Verbindung mit dem privaten Endpunkt herzustellen. Rechnen Sie mit einer Verzögerung von etwa zehn Minuten.
    
       ![Überprüfen der Telemetriedaten des privaten Endpunkts](./media/private-endpoint-tsg/private-endpoint-monitor.png)

1.  Verwenden Sie die **Problembehandlung für VM-Verbindungen** von Azure Network Watcher.

    a. Wählen Sie den virtuellen Clientcomputer aus.

    b. Wählen Sie **Problembehandlung für Verbindung** und anschließend die Registerkarte **Ausgehende Verbindungen** aus.
    
      ![Network Watcher: Ausgehende Verbindungen testen](./media/private-endpoint-tsg/network-watcher-outbound-connection.png)
    
    c. Wählen Sie **Network Watcher für detaillierte Verbindungsablaufverfolgung verwenden** aus.
    
      ![Network Watcher: Problembehandlung für die Verbindung](./media/private-endpoint-tsg/network-watcher-connection-troubleshoot.png)

    d. Wählen Sie **Test by FQDN** (Nach FQDN testen) aus.
     - Fügen Sie den FQDN aus der privaten Endpunktressource ein.
     - Geben Sie einen Port an. In der Regel wird 443 für Azure Storage oder Azure Cosmos DB und 1336 für SQL verwendet.

    e. Wählen Sie **Testen** aus, und überprüfen Sie die Testergebnisse.
    
      ![Network Watcher: Testergebnisse](./media/private-endpoint-tsg/network-watcher-test-results.png)
    
        
1. Die DNS-Auflösung der Testergebnisse muss die private IP-Adresse aufweisen, die dem privaten Endpunkt zugewiesen ist.

    a. Sollten die DNS-Einstellungen nicht korrekt sein, führen Sie die folgenden Schritte aus:
     - Vorgehensweise bei Verwendung einer privaten Zone: 
       - Vergewissern Sie sich, dass das virtuelle Netzwerk des virtuellen Clientcomputers der privaten Zone zugeordnet ist.
       - Vergewissern Sie sich, dass der Eintrag für die private DNS-Zone vorhanden ist. Falls nicht, erstellen Sie ihn.
     - Vorgehensweise bei Verwendung eines benutzerdefinierten DNS:
       - Überprüfen Sie die Einstellungen Ihres benutzerdefinierten DNS, und vergewissern Sie sich, dass die DNS-Konfiguration korrekt ist.
       Einen entsprechenden Leitfaden finden Sie unter [Was ist privater Endpunkt in Azure? – DNS-Konfiguration](./private-endpoint-overview.md#dns-configuration).

    b. Sollten die Probleme mit der Konnektivität auf Netzwerksicherheitsgruppen (NSGs) oder benutzerdefinierte Routen zurückzuführen sein, gehen Sie wie folgt vor:
     - Überprüfen Sie die NSG-Ausgangsregeln, und erstellen Sie geeignete Ausgangsregeln, um den Datenverkehr zuzulassen.
    
       ![NSG-Ausgangsregeln](./media/private-endpoint-tsg/nsg-outbound-rules.png)

1. Wenn die Ergebnisse der Verbindung überprüft wurden, kann das Konnektivitätsproblem mit anderen Aspekten wie Geheimnissen, Token und Kennwörtern auf der Anwendungsebene zusammenhängen.
   - Überprüfen Sie in diesem Fall die Konfiguration der Private Link-Ressource, die dem privaten Endpunkt zugeordnet ist. Weitere Informationen finden Sie unter [Problembehandlung bei Konnektivitätsproblemen beim Private Link-Dienst](troubleshoot-private-link-connectivity.md).

1. Sollten weiterhin Konnektivitätsprobleme auftreten, wenden Sie sich an das [Azure-Supportteam](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

## <a name="next-steps"></a>Nächste Schritte

 * [Schnellstart: Erstellen eines privaten Endpunkts mit dem Azure-Portal](./create-private-endpoint-portal.md)
 * [Problembehandlung bei Konnektivitätsproblemen beim Private Link-Dienst](troubleshoot-private-link-connectivity.md)