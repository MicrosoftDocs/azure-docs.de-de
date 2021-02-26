---
title: Bereitstellen von Traffic Manager zum Ausgleichen der Workloads von Azure VMware Solution
description: Erfahren Sie, wie Sie Traffic Manager in Azure VMware Solution integrieren, um Anwendungsworkloads auf mehreren Endpunkten in unterschiedlichen Regionen auszugleichen.
ms.topic: how-to
ms.date: 02/08/2021
ms.openlocfilehash: 46570c5a61fc0a641d83126fd0f8ef35b3dc42cc
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "99988589"
---
# <a name="deploy-traffic-manager-to-balance-azure-vmware-solution-workloads"></a>Bereitstellen von Traffic Manager zum Ausgleichen der Workloads von Azure VMware Solution

In diesem Artikel werden die Schritte zum Integrieren von [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) in Azure VMware Solution erläutert. Bei der Integration werden Anwendungsworkloads über mehrere Endpunkte hinweg ausgeglichen. In diesem Artikel werden auch die Schritte zum Konfigurieren von Traffic Manager für den direkten Datenverkehr zwischen drei [Azure Application Gateway](../application-gateway/overview.md)-Instanzen erläutert, die sich über mehrere Azure VMware Solution-Regionen erstrecken. 

Die Gateways verfügen über virtuelle Azure VMware Solution-Computer (VMs), die als Mitglieder des Back-End-Pools konfiguriert sind, um die eingehenden Anforderungen der Ebene 7 auszugleichen. Weitere Informationen finden Sie unter [Verwenden von Azure Application Gateway zum Schützen Ihrer Web-Apps in Azure VMware Solution](protect-azure-vmware-solution-with-application-gateway.md).

Wie im Diagramm dargestellt, bietet Azure Traffic Manager einen Lastenausgleich für die Anwendungen auf DNS-Ebene zwischen regionalen Endpunkten. Die Gateways verfügen über Back-End-Poolmitglieder, die als IIS-Server konfiguriert sind und als externe Azure VMware Solution-Endpunkte referenziert werden. Zur Verbindung zwischen den beiden Private Cloud-Regionen über das virtuelle Netzwerk wird ein ExpressRoute-Gateway verwendet.   

:::image type="content" source="media/traffic-manager/traffic-manager-topology.png" alt-text="Architekturdiagramm der Traffic Manager-Integration in Azure VMware Solution" lightbox="media/traffic-manager/traffic-manager-topology.png" border="false":::

Bevor Sie beginnen, überprüfen Sie zunächst die [Voraussetzungen](#prerequisites). Dann gehen wir die Verfahren zu Folgendem durch:

> [!div class="checklist"]
> * Überprüfen der Konfiguration Ihrer Anwendungsgateways und des NSX-T-Segments
> * Erstellen Ihres Traffic Manager-Profils
> * Hinzufügen externer Endpunkte zu Ihrem Traffic Manager-Profil

## <a name="prerequisites"></a>Voraussetzungen

- Drei VMs, die als Microsoft IIS-Server konfiguriert sind und in verschiedenen Azure VMware Solution-Regionen ausgeführt werden: 
   - USA (Westen)
   - Europa, Westen
   - USA, Osten (lokal) 

- Ein Anwendungsgateway mit externen Endpunkten in den oben erwähnten Azure VMware Solution-Regionen.

- Host mit Internetverbindung für die Überprüfung. 

- Ein [in Azure VMware Solution erstelltes NSX-T-Netzwerksegment](tutorial-nsx-t-network-segment.md).

## <a name="verify-your-application-gateways-configuration"></a>Überprüfen der Konfiguration Ihrer Anwendungsgateways

Die folgenden Schritte überprüfen die Konfiguration Ihrer Anwendungsgateways.

1. Wählen Sie im Azure-Portal **Anwendungsgateways** aus, um eine Liste Ihrer aktuellen Anwendungsgateways anzuzeigen:

   - AVS-GW-WUS
   - AVS-GW-EUS (lokal)
   - AVS-GW-WEU

   :::image type="content" source="media/traffic-manager/app-gateways-list-1.png" alt-text="Screenshot der Seite „Anwendungsgateway“ mit der Liste der konfigurierten Anwendungsgateways." lightbox="media/traffic-manager/app-gateways-list-1.png":::

1. Wählen Sie eines Ihrer zuvor bereitgestellten Anwendungsgateways aus. 

   Es wird ein Fenster mit verschiedenen Informationen zum Anwendungsgateway geöffnet. 

   :::image type="content" source="media/traffic-manager/backend-pool-config.png" alt-text="Screenshot der Seite „Anwendungsgateway“ mit Details des ausgewählten Anwendungsgateways." lightbox="media/traffic-manager/backend-pool-config.png":::

1. Wählen Sie **Back-End-Pools** aus, um die Konfiguration eines der Back-End-Pools zu überprüfen. Sie sehen ein Mitglied des Back-End-Pools für virtuelle Computer, das als Webserver mit der IP-Adresse 172.29.1.10 konfiguriert ist.
 
   :::image type="content" source="media/traffic-manager/backend-pool-ip-address.png" alt-text="Screenshot der Seite „Back-End-Pool bearbeiten“ mit hervorgehobener Ziel-IP-Adresse.":::

1. Überprüfen Sie die Konfiguration der anderen Anwendungsgateways und Back-End-Pool-Mitglieder. 

## <a name="verify-the-nsx-t-segment-configuration"></a>Überprüfen der Konfiguration des NSX-T-Segments

Mit den folgenden Schritten wird die Konfiguration des NSX-T-Segments in der Azure VMware Solution-Umgebung überprüft.

1. Wählen Sie **Segments** (Segmente) aus, um Ihre konfigurierten Segmente anzuzeigen.  Sie sehen, dass „Contoso-segment1“ mit dem Gateway „Contoso-T01“, einem flexiblen Router auf Ebene 1, verbunden ist.

   :::image type="content" source="media/traffic-manager/nsx-t-segment-avs.png" alt-text="Screenshot mit Segmentprofilen in NSX-T Manager." lightbox="media/traffic-manager/nsx-t-segment-avs.png":::    

1. Wählen Sie **Tier-1 Gateways** (Gateways der Ebene 1) aus, um eine Liste Ihrer Gateways der Ebene 1 mit der Anzahl der verknüpften Segmente anzuzeigen. 

   :::image type="content" source="media/traffic-manager/nsx-t-segment-linked-2.png" alt-text="Screenshot mit Gatewayadresse des ausgewählten Segments.":::    

1. Wählen Sie das mit Contoso-T01 verknüpfte Segment aus. Es wird ein Fenster geöffnet, das die auf dem Router der Ebene 1 konfigurierte logische Schnittstelle anzeigt. Dies dient als Gateway für die VM des Back-End-Poolmitglieds, die mit dem Segment verbunden ist.

1. Wählen Sie im vSphere-Client den virtuellen Computer aus, um dessen Details anzuzeigen. 

   >[!NOTE]
   >Die IP-Adresse entspricht dem VM-Back-End-Pool-Mitglied, das im vorherigen Abschnitt als Webserver konfiguriert wurde: 172.29.1.10.

   :::image type="content" source="media/traffic-manager/nsx-t-vm-details.png" alt-text="Screenshot der VM-Details im vSphere-Client." lightbox="media/traffic-manager/nsx-t-vm-details.png":::    

4. Wählen Sie den virtuellen Computer und dann **AKTIONEN > Einstellungen bearbeiten** aus, um die Verbindung mit dem NSX-T-Segment zu überprüfen.

## <a name="create-your-traffic-manager-profile"></a>Erstellen Ihres Traffic Manager-Profils

1. Melden Sie sich beim [Azure-Portal](https://rc.portal.azure.com/#home) an. Wählen Sie unter **Azure-Dienste > Netzwerk** die Option **Traffic Manager-Profile** aus.

2. Wählen Sie **+ Hinzufügen** aus, um ein neues Traffic Manager-Profil zu erstellen.
 
3. Geben Sie die folgenden Informationen ein, und wählen Sie dann **Erstellen** aus:

   - Profilname
   - Routingmethode (verwenden Sie [gewichtet](../traffic-manager/traffic-manager-routing-methods.md))
   - Subscription
   - Resource group

## <a name="add-external-endpoints-into-the-traffic-manager-profile"></a>Hinzufügen externer Endpunkte zum Traffic Manager-Profil

1. Wählen Sie im Ergebnisbereich das Traffic Manager-Profil und dann die Optionen **Endpunkte** sowie **+ Hinzufügen** aus.

1. Geben Sie für jeden externen Endpunkt in den verschiedenen Regionen die erforderlichen Details ein, und wählen Sie dann **Hinzufügen** aus: 
   - type
   - Name
   - Vollqualifizierter Domänenname (FQDN) oder IP
   - Gewichtung (weisen Sie jedem Endpunkt eine Gewichtung von 1 zu). 

   Nach der Erstellung werden alle drei im Traffic Manager-Profil angezeigt. Der Monitorstatus aller drei muss **Online** sein.

3. Wählen Sie **Übersicht** aus, und kopieren Sie die URL unter **DNS-Name**.

   :::image type="content" source="media/traffic-manager/traffic-manager-endpoints.png" alt-text="Screenshot mit Traffic Manager-Endpunktübersicht mit hervorgehobenem DNS-Namen." lightbox="media/traffic-manager/traffic-manager-endpoints.png"::: 

4. Fügen Sie die URL des DNS-Namens in einen Browser ein. Der Screenshot zeigt den Datenverkehr, der in die Region „Europa, Westen“ geleitet wird.

   :::image type="content" source="media/traffic-manager/traffic-to-west-europe.png" alt-text="Screenshot des Browserfensters mit Datenverkehr, der nach „Europa, Westen“ weitergeleitet wird."::: 

5. Aktualisieren Sie Ihren Browser. Der Screenshot zeigt den Datenverkehr, der zu einer anderen Gruppe von Back-End-Poolmitgliedern in der Region „USA, Westen“ geleitet wird.

   :::image type="content" source="media/traffic-manager/traffic-to-west-us.png" alt-text="Screenshot des Browserfensters mit Datenverkehr, der nach „USA, Westen“ weitergeleitet wird."::: 

6. Aktualisieren Sie Ihren Browser erneut. Der Screenshot zeigt den Datenverkehr, der zu den letzten lokalen Back-End-Poolmitgliedern geleitet wird.

   :::image type="content" source="media/traffic-manager/traffic-to-on-premises.png" alt-text="Screenshot des Browserfensters mit Datenverkehr, der zu lokalem Standort weitergeleitet wird.":::

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie sich mit der Integration von Azure Traffic Manager und Azure VMware Solution beschäftigt haben, möchten Sie vielleicht mehr über Folgendes erfahren:

- [Verwenden von Azure Application Gateway für Azure VMware Solution](protect-azure-vmware-solution-with-application-gateway.md)
- [Traffic Manager-Routingmethoden](../traffic-manager/traffic-manager-routing-methods.md)
- [Kombinieren von Lastenausgleichsdiensten in Azure](../traffic-manager/traffic-manager-load-balancing-azure.md)
- [Messen der Leistung von Traffic Manager](../traffic-manager/traffic-manager-performance-considerations.md)
