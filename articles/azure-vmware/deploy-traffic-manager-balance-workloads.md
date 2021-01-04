---
title: Bereitstellen von Traffic Manager zum Ausgleichen der Workloads von Azure VMware Solution (AVS)
description: Erfahren Sie, wie Sie Traffic Manager in Azure VMware Solution (AVS) integrieren, um Anwendungsworkloads auf mehreren Endpunkten in unterschiedlichen Regionen auszugleichen.
ms.topic: how-to
ms.date: 08/14/2020
ms.openlocfilehash: ed74bb0dfc533abadd50af32afc06c9cb4106193
ms.sourcegitcommit: 642988f1ac17cfd7a72ad38ce38ed7a5c2926b6c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94874281"
---
# <a name="deploy-traffic-manager-to-balance-azure-vmware-solution-avs-workloads"></a>Bereitstellen von Traffic Manager zum Ausgleichen der Workloads von Azure VMware Solution (AVS)

Dieser Artikel führt Sie durch die Integration von Traffic Manager in Azure VMware Solution (AVS), um die Workloads von Anwendungen über mehrere Endpunkte hinweg auszugleichen. Wir betrachten ein Szenario, in dem Traffic Manager den Datenverkehr zwischen drei Anwendungsgateways leitet, die mehrere AVS-Regionen umfassen: USA (Westen), Europa (Westen) und lokal in USA (Osten). 

Azure Traffic Manager ist ein DNS-basierter Lastenausgleich, mit dem Sie Datenverkehr an Dienste in Azure-Regionen weltweit optimal verteilen können. Es wird den Datenverkehr der Anwendungen sowohl über die Azure ausführenden Workloads als auch über externe öffentliche Endpunkte ausgleichen. Weitere Informationen zu Traffic Manager finden Sie unter [Was ist Traffic Manager?](../traffic-manager/traffic-manager-overview.md).

Überprüfen Sie zunächst die [Voraussetzungen](#prerequisites). Dann gehen wir die Verfahren zu Folgendem durch:

> [!div class="checklist"]
> * Überprüfen der Konfiguration Ihrer Anwendungsgateways
> * Überprüfen der Konfiguration des NSX-T-Segments
> * Erstellen Ihres Traffic Manager-Profils
> * Hinzufügen externer Endpunkte zu Ihrem Traffic Manager-Profil

## <a name="topology"></a>Topologie

Wie in der folgenden Abbildung dargestellt, bietet Azure Traffic Manager einen Lastenausgleich für die Anwendungen auf DNS-Ebene zwischen regionalen Endpunkten. Die Anwendungsgateways verfügen über Back-End-Poolmitglieder, die als IIS-Server konfiguriert sind und als externe AVS-Endpunkte referenziert werden.

Die Verbindung über das virtuelle Netzwerk zwischen den beiden privaten AVS-Cloudregionen, USA (Westen) und Europa (Westen), und einem lokalen Server in USA (Osten) erfolgt über ein ExpressRoute-Gateway.   

:::image type="content" source="media/traffic-manager/traffic-manager-topology.png" alt-text="Diagramm der Architektur der Traffic Manager-Integration in Azure VMware Solution" lightbox="media/traffic-manager/traffic-manager-topology.png" border="false":::
 
## <a name="prerequisites"></a>Voraussetzungen

- Drei virtuelle Computer, die als Microsoft IIS-Server konfiguriert sind und in verschiedenen AVS-Regionen ausgeführt werden: USA (Westen), Europa (Westen) und lokal. 

- Ein Anwendungsgateway mit externen Endpunkten in „USA, Westen“, „Europa, Westen“ und „lokal“.

- Host mit Internetverbindung für die Überprüfung. 

## <a name="verify-configuration-of-your-application-gateways"></a>Überprüfen der Konfiguration Ihrer Anwendungsgateways

[Azure Application Gateway](https://azure.microsoft.com/services/application-gateway/) ist ein Lastenausgleich für Webdatenverkehr auf Schicht 7, mit dem Sie eingehenden Datenverkehr für Ihre Webanwendungen verwalten können. Weitere Informationen zu Application Gateway finden Sie unter [Was ist Azure Application Gateway?](../application-gateway/overview.md). 

In diesem Szenario werden drei Application Gateway-Instanzen als externe AVS-Endpunkte konfiguriert. Die Anwendungsgateways verfügen über virtuelle AVS-Computer, die als Mitglieder des Back-End-Pools konfiguriert sind, um die eingehenden Anforderungen der Ebene 7 auszugleichen. (Informationen zum Konfigurieren von Application Gateway mit virtuellen AVS-Computern als Back-End-Pools finden Sie unter [Verwenden von Azure Application Gateway zum Schützen Ihrer Web-Apps in Azure VMware Solution](protect-azure-vmware-solution-with-application-gateway.md).)  

Die folgenden Schritte überprüfen die ordnungsgemäße Konfiguration Ihrer Anwendungsgateways.

1. Öffnen Sie das Azure-Portal, und wählen Sie **Anwendungsgateways** aus, um eine Liste Ihrer aktuellen Anwendungsgateways anzuzeigen. 

    Für dieses Szenario haben wir drei Anwendungsgateways konfiguriert:
    - AVS-GW-WUS
    - AVS-GW-EUS (lokal)
    - AVS-GW-WEU

    :::image type="content" source="media/traffic-manager/app-gateways-list-1.png" alt-text="Screenshot der Seite „Anwendungsgateway“ mit der Liste der konfigurierten Anwendungsgateways." lightbox="media/traffic-manager/app-gateways-list-1.png":::

2. Wählen Sie eines Ihrer zuvor bereitgestellten Anwendungsgateways aus. Es wird ein Fenster mit verschiedenen Informationen zum Anwendungsgateway geöffnet. Wählen Sie **Back-End-Pools** aus, um die Konfiguration eines der Back-End-Pools zu überprüfen.

   :::image type="content" source="media/traffic-manager/backend-pool-config.png" alt-text="Screenshot der Seite „Anwendungsgateway“ mit Details des ausgewählten Anwendungsgateways." lightbox="media/traffic-manager/backend-pool-config.png":::
 
3. In diesem Fall sehen wir ein Mitglied des Back-End-Pools für virtuelle Computer, das als Webserver mit der IP-Adresse 172.29.1.10 konfiguriert ist.
 
    :::image type="content" source="media/traffic-manager/backend-pool-ip-address.png" alt-text="Screenshot der Seite „Back-End-Pool bearbeiten“ mit hervorgehobener Ziel-IP-Adresse.":::

    Auf ähnliche Weise können Sie die Konfiguration der anderen Anwendungsgateways und Back-End-Pool-Mitglieder überprüfen. 

## <a name="verify-configuration-of-the-nsx-t-segment"></a>Überprüfen der Konfiguration des NSX-T-Segments

In NSX-T Manager erstellte Netzwerksegmente werden als Netzwerke für virtuelle Computer in vCenter verwendet. Weitere Informationen finden Sie im Tutorial [Erstellen eines NSX-T-Netzwerksegments in Azure VMware Solution (AVS)](tutorial-nsx-t-network-segment.md).

In unserem Szenario wird ein NSX-T-Segment in der AVS-Umgebung konfiguriert, mit der der virtuelle Computer des Back-End-Poolmitglieds verbunden ist.

1. Wählen Sie **Segments** (Segmente) aus, um Ihre konfigurierten Segmente anzuzeigen. In diesem Fall sehen wir, dass „Contoso-segment1“ mit dem Gateway „Contoso-T01“, einem flexiblen Router auf Ebene 1, verbunden ist.

    :::image type="content" source="media/traffic-manager/nsx-t-segment-avs.png" alt-text="Screenshot mit Segmentprofilen in NSX-T Manager." lightbox="media/traffic-manager/nsx-t-segment-avs.png":::    

2. Wählen Sie **Tier-1-Gateways** (Gateways der Ebene 1) aus, um eine Liste Ihrer Gateways der Ebene 1 mit der Anzahl der verknüpften Segmente anzuzeigen. Wählen Sie das mit Contoso-T01 verknüpfte Segment aus. Es wird ein Fenster geöffnet, das die auf dem Router der Ebene 1 konfigurierte logische Schnittstelle anzeigt. Dies dient als Gateway für den virtuellen Computer des Back-End-Poolmitglieds, der mit dem Segment verbunden ist.

   :::image type="content" source="media/traffic-manager/nsx-t-segment-linked-2.png" alt-text="Screenshot mit Gatewayadresse des ausgewählten Segments.":::    

3. Wählen Sie im VM vSphere-Client den virtuellen Computer aus, um dessen Details anzuzeigen. Beachten Sie, dass seine IP-Adresse mit der in Schritt 3 des vorhergehenden Abschnitts beschriebenen IP-Adresse übereinstimmt: 172.29.1.10.

    :::image type="content" source="media/traffic-manager/nsx-t-vm-details.png" alt-text="Screenshot der VM-Details im vSphere-Client." lightbox="media/traffic-manager/nsx-t-vm-details.png":::    

4. Wählen Sie den virtuellen Computer aus, und klicken Sie dann auf **ACTIONS > Edit Settings** (AKTIONEN > Einstellungen bearbeiten), um die Verbindung mit dem NSX-T-Segment zu überprüfen.

## <a name="create-your-traffic-manager-profile"></a>Erstellen Ihres Traffic Manager-Profils

1. Melden Sie sich beim [Azure-Portal](https://rc.portal.azure.com/#home)an. Wählen Sie unter **Azure-Dienste > Netzwerk** die Option **Traffic Manager-Profile** aus.

2. Wählen Sie **+ Hinzufügen** aus, um ein neues Traffic Manager-Profil zu erstellen.
 
3. Geben Sie Profilname, Routingmethode (wir werden in diesem Szenario „gewichtet“ verwenden; siehe [Traffic Manager-Routingmethoden](../traffic-manager/traffic-manager-routing-methods.md)), Abonnement und Ressourcengruppe an, und wählen Sie **Erstellen** aus.

## <a name="add-external-endpoints-into-the-traffic-manager-profile"></a>Hinzufügen externer Endpunkte zum Traffic Manager-Profil

1. Wählen Sie im Ergebnisbereich das Traffic Manager-Profil und dann die Optionen **Endpunkte** sowie **+ Hinzufügen** aus.

2. Geben Sie die erforderlichen Details ein: Typ, Name, vollqualifizierter Domänenname (FQDN) oder IP-Adresse und Gewichtung (in diesem Szenario weisen wir jedem Endpunkt eine Gewichtung von 1 zu). Wählen Sie **Hinzufügen**. Dadurch wird der externe Endpunkt erstellt. Der Monitorstatus muss **Online** sein. Wiederholen Sie dieselben Schritte, um zwei weitere externe Endpunkte zu erstellen, einen in einer anderen Region und den anderen lokal. Nach der Erstellung werden alle drei im Traffic Manager-Profil angezeigt, und der Status aller drei sollte **Online** sein.

3. Wählen Sie **Übersicht**. Kopieren Sie die URL unter **DNS-Name**.

   :::image type="content" source="media/traffic-manager/traffic-manager-endpoints.png" alt-text="Screenshot mit Traffic Manager-Endpunktübersicht mit hervorgehobenem DNS-Namen." lightbox="media/traffic-manager/traffic-manager-endpoints.png"::: 

4. Fügen Sie die URL des DNS-Namens in einen Browser ein. Der folgende Screenshot zeigt den Datenverkehr, der in die Region „Europa, Westen“ geleitet wird.

   :::image type="content" source="media/traffic-manager/traffic-to-west-europe.png" alt-text="Screenshot des Browserfensters mit Datenverkehr, der nach „Europa, Westen“ weitergeleitet wird."::: 

5. Aktualisieren Sie Ihren Browser. Der folgende Screenshot zeigt den Datenverkehr, der jetzt zu einer anderen Gruppe von Back-End-Poolmitgliedern in der Region „USA, Westen“ geleitet wird.

   :::image type="content" source="media/traffic-manager/traffic-to-west-us.png" alt-text="Screenshot des Browserfensters mit Datenverkehr, der nach „USA, Westen“ weitergeleitet wird."::: 

6. Aktualisieren Sie Ihren Browser erneut. Der folgende Screenshot zeigt den Datenverkehr, der jetzt zu den letzten lokalen Back-End-Poolmitgliedern geleitet wird.

   :::image type="content" source="media/traffic-manager/traffic-to-on-premises.png" alt-text="Screenshot des Browserfensters mit Datenverkehr, der zu lokalem Standort weitergeleitet wird.":::

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen:

- [Verwenden von Azure Application Gateway für Azure VMware Solution (AVS)](protect-azure-vmware-solution-with-application-gateway.md)
- [Traffic Manager-Routingmethoden](../traffic-manager/traffic-manager-routing-methods.md)
- [Kombinieren von Lastenausgleichsdiensten in Azure](../traffic-manager/traffic-manager-load-balancing-azure.md)
- [Messen der Leistung von Traffic Manager](../traffic-manager/traffic-manager-performance-considerations.md)
