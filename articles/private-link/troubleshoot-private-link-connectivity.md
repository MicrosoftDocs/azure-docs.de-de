---
title: Problembehandlung bei Konnektivitätsproblemen bei Azure Private Link
description: Ausführliche Anleitung zur Diagnose der Private Link-Konnektivität
services: private-link
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
ms.openlocfilehash: 45a7a146dd929408b50a0045fe2598726ee05505
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95544308"
---
# <a name="troubleshoot-azure-private-link-connectivity-problems"></a>Problembehandlung bei Konnektivitätsproblemen bei Azure Private Link

In diesem Artikel finden Sie ausführliche Anweisungen zum Überprüfen und Diagnostizieren der Konnektivität für die Einrichtung von Azure Private Link.

Mit Azure Private Link können Sie auf Azure PaaS-Dienste (z. B. Azure Storage, Azure Cosmos DB und Azure SQL-Datenbank) und über einen privaten Endpunkt in Ihrem virtuellen Netzwerk auf in Azure gehostete Kunden- oder Partnerdienste zugreifen. Der Datenverkehr zwischen Ihrem virtuellen Netzwerk und dem Dienst wird über das Microsoft-Backbone-Netzwerk übertragen und dadurch vom öffentlichen Internet isoliert. Sie können auch Ihren eigenen Private Link-Dienst in Ihrem virtuellen Netzwerk erstellen und Ihren Kunden privat zur Verfügung stellen.

Sie können Ihren Dienst, der hinter dem Standard-Tarif von Azure Load Balancer ausgeführt wird, für den Private Link-Zugriff aktivieren. Consumer Ihres Diensts können einen privaten Endpunkt innerhalb ihres virtuellen Netzwerks erstellen und diesen zum Dienst zuordnen, um privat darauf zuzugreifen.

Die folgenden Konnektivitätsszenarios sind mit Private Link verfügbar:

- Virtuelles Netzwerk aus der gleichen Region
- Virtuelle Netzwerke mit regionalem Peering
- Virtuelle Netzwerke mit globalem Peering
- Lokale Kunden über VPN oder ExpressRoute-Verbindungen

## <a name="deployment-troubleshooting"></a>Problembehandlung für Bereitstellungen

Informationen zur Problembehandlung in Fällen, bei denen Sie die Quell-IP-Adresse nicht aus dem Subnetz Ihrer Wahl für Ihren Private Link-Dienst auswählen können, finden Sie unter [Deaktivieren von Netzwerkrichtlinien für die Quell-IP-Adresse eines Private Link-Diensts](./disable-private-link-service-network-policy.md).

Stellen Sie sicher, dass die Einstellung **privateLinkServiceNetworkPolicies** für das Subnetz deaktiviert ist, aus dem Sie die Quell-IP-Adresse auswählen.

## <a name="diagnose-connectivity-problems"></a>Diagnostizieren von Konnektivitätsproblemen

Falls Konnektivitätsprobleme mit Ihrem Private Link-Setup auftreten, vergewissern Sie sich mithilfe der folgenden Schritte, dass alle üblichen Konfigurationen ordnungsgemäß festgelegt sind.

1. Überprüfen Sie die Konfiguration von Private Link, indem Sie die Ressource durchsuchen.

    a. Navigieren Sie zum **Private Link-Center**.

      ![Private Link-Center](./media/private-link-tsg/private-link-center.png)

    b. Wählen Sie im linken Bereich **Private Link-Dienste** aus.

      ![Private Link-Dienste](./media/private-link-tsg/private-link-service.png)

    c. Filtern Sie nach dem zu diagnostizierenden Private Link-Dienst, und wählen Sie ihn aus.

    d. Überprüfen Sie die privaten Endpunktverbindungen.
     - Stellen Sie sicher, dass der private Endpunkt, mit dem Sie eine Verbindung herstellen möchten, mit dem Verbindungsstatus **Genehmigt** aufgeführt wird.
     - Wenn der Zustand **Ausstehend** ist, wählen Sie diesen aus und genehmigen Sie ihn.

       ![Private Endpunktverbindungen](./media/private-link-tsg/pls-private-endpoint-connections.png)

     - Wechseln Sie zu dem privaten Endpunkt, von dem aus Sie eine Verbindung herstellen, indem Sie den Namen auswählen. Stellen Sie sicher, dass der Verbindungsstatus **Genehmigt** angezeigt wird.

       ![Übersicht über die private Endpunktverbindung](./media/private-link-tsg/pls-private-endpoint-overview.png)

     - Testen Sie die Konnektivität erneut, nachdem beide Seiten genehmigt wurden.

    e. Überprüfen Sie den **Alias** auf der Registerkarte **Übersicht** und die **Ressourcen-ID** auf der Registerkarte **Eigenschaften**.
     - Stellen Sie sicher, dass die Informationen für **Alias** und **Ressourcen-ID** mit dem **Alias** und der **Ressourcen-ID** übereinstimmen, die Sie zum Erstellen des privaten Endpunkts für diesen Dienst verwenden.

       ![Überprüfen der Informationen für Alias](./media/private-link-tsg/pls-overview-pane-alias.png)

       ![Überprüfen der Informationen für Ressourcen-ID](./media/private-link-tsg/pls-properties-pane-resourceid.png)

    f. Überprüfen Sie die Informationen für **Sichtbarkeit** auf der Registerkarte **Übersicht**.
     - Stellen Sie sicher, dass Ihr Abonnement dem Bereich **Sichtbarkeit** entspricht.

       ![Überprüfen der Informationen für Sichtbarkeit](./media/private-link-tsg/pls-overview-pane-visibility.png)

    g. Überprüfen Sie die Informationen für **Lastenausgleich** auf der Registerkarte **Übersicht**.
     - Sie können zum Lastenausgleich wechseln, indem Sie den Lastenausgleichslink auswählen.

       ![Überprüfen der Informationen für Lastenausgleich](./media/private-link-tsg/pls-overview-pane-ilb.png)

     - Stellen Sie sicher, dass die Einstellungen für den Lastenausgleich wie gewünscht konfiguriert sind.
       - Überprüfen Sie die **Front-End-IP-Konfiguration**.
       - Überprüfen Sie die **Back-End-Pools**.
       - Überprüfen Sie die **Lastenausgleichsregeln**.

       ![Überprüfen der Lastenausgleichseigenschaften](./media/private-link-tsg/pls-ilb-properties.png)

     - Stellen Sie sicher, dass der Lastenausgleich gemäß der vorherigen Einstellungen funktioniert.
       - Wählen Sie einen virtuellen Computer (VM) in einem beliebigen anderen Subnetz als dem Subnetz aus, in dem der Back-End-Pool für den Lastenausgleich verfügbar ist.
       - Versuchen Sie, über die vorherige VM auf das Front-End für den Lastenausgleich zuzugreifen.
       - Wenn gemäß der Lastenausgleichsregeln eine Verbindung mit dem Back-End-Pool hergestellt wird, ist Ihr Lastenausgleich betriebsbereit.
       - Sie können außerdem die Lastenausgleichsmetrik über Azure Monitor überprüfen, um zu überprüfen, ob Daten über den Lastenausgleich geleitet werden.

1. Überprüfen Sie mithilfe von [Azure Monitor](../azure-monitor/overview.md), ob Daten übermittelt werden.

    a. Wählen Sie bei der Ressource für den Private Link-Dienst die Option **Metriken** aus.
     - Wählen Sie **Eingehende Bytes** oder **Ausgehende Bytes** aus.
     - Überprüfen Sie, ob Daten übermittelt werden, wenn Sie versuchen, eine Verbindung mit dem Private Link-Dienst herzustellen. Rechnen Sie mit einer Verzögerung von etwa zehn Minuten.

       ![Überprüfen von Private Link-Dienstmetriken](./media/private-link-tsg/pls-metrics.png)

1. Sollten weiterhin Konnektivitätsprobleme auftreten, wenden Sie sich an das [Azure-Supportteam](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

## <a name="next-steps"></a>Nächste Schritte

 * [Erstellen eines Private Link-Diensts mithilfe der Azure CLI](./create-private-link-service-cli.md)
 * [Leitfaden zur Problembehandlung bei privaten Azure-Endpunkten](troubleshoot-private-endpoint-connectivity.md)