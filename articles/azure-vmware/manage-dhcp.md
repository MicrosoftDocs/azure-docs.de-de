---
title: Verwalten von DHCP in Azure VMware Solution
description: Erfahren Sie, wie Sie DHCP für Ihre private Azure VMware Solution-Cloud einrichten und verwalten.
ms.topic: how-to
ms.date: 11/09/2020
ms.openlocfilehash: 9143a8544fe1b98262c3e990ccdf56f5d5f65957
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2020
ms.locfileid: "94335952"
---
# <a name="manage-dhcp-for-azure-vmware-solution"></a>Verwalten von DHCP in Azure VMware Solution

Anwendungen und Workloads, die in einer privaten Cloudumgebung ausgeführt werden, erfordern DHCP-Dienste für die IP-Adresszuweisung.  In diesem Artikel erfahren Sie, welche beiden Möglichkeiten zum Erstellen und Verwalten von DHCP in Azure VMware Solution zur Verfügung stehen:

- Bei Verwendung von NSX-T zum Hosten Ihres DHCP-Servers müssen Sie [einen DHCP-Server erstellen](#create-a-dhcp-server) und eine [Weiterleitung zu diesem Server einrichten](#create-dhcp-relay-service). Wenn Sie den DHCP-Server erstellen, fügen Sie auch ein Netzwerksegment hinzu und geben den IP-Adressbereich für DHCP an.   

- Wenn Sie einen externen DHCP-Server eines Drittanbieters in Ihrem Netzwerk verwenden, müssen Sie [einen DHCP-Relaydienst erstellen](#create-dhcp-relay-service). Wenn Sie einen Relaydienst zu einem DHCP-Server erstellen, müssen Sie den DHCP-IP-Adressbereich angeben, unabhängig davon, ob NSX-T oder einen Drittanbieterdienst zum Hosten des DHCP-Servers verwendet wird.

>[!IMPORTANT]
>DHCP kann für VMs in einem gestreckten VMware HCX L2-Netzwerk nicht verwendet werden, wenn der DHCP-Server sich in einem lokalen Rechenzentrum befindet.  NSX blockiert standardmäßig den Durchlauf aller DHCP-Anforderungen durch das gestreckte L2-Netzwerk. Informationen zu dieser Lösung finden Sie im Verfahren zum [Senden von DHCP-Anforderungen an den lokalen DHCP-Server](#send-dhcp-requests-to-the-on-premises-dhcp-server).


## <a name="create-a-dhcp-server"></a>Erstellen eines DHCP-Servers

Wenn Sie NSX-T zum Hosten Ihres DHCP-Servers verwenden möchten, erstellen Sie einen DHCP-Server. Fügen Sie dann ein Netzwerksegment hinzu, und geben Sie den DHCP-IP-Adressbereich an.

1. Wählen Sie in NSX-T Manager die Optionen **Networking** > **DHCP** (Netzwerk > DHCP) und dann **Add Server** (Server hinzufügen) aus.

1. Wählen Sie **DHCP** unter **Server Type** (Servertyp) aus, geben Sie den Servernamen und die IP-Adresse an, und wählen Sie dann **Save** (Speichern) aus.

   :::image type="content" source="./media/manage-dhcp/dhcp-server-settings.png" alt-text="DHCP-Server hinzufügen" border="true":::

1. Wählen Sie **Tier-1 Gateways** (Gateways der Ebene 1), dann die vertikalen Auslassungspunkte für das Tier-1 Gateway (Gateway der Ebene 1) und anschließend **Edit** (Bearbeiten) aus.

   :::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway.png" alt-text="Das zu verwendende Gateway auswählen" border="true":::

1. Wählen Sie **No IP Allocation Set** (Kein IP-Zuteilungssatz) aus, um ein Subnetz hinzuzufügen.

   :::image type="content" source="./media/manage-dhcp/add-subnet.png" alt-text="Hinzufügen eines Subnetzes" border="true":::

1. Wählen Sie unter **Type** (Typ) die Option **DHCP Local Server** (Lokaler DHCP-Server) aus. 
   
1. Wählen Sie **Default DHCP** (Standard-DHCP) für **DHCP Server** und dann **Save** (Speichern) aus.

1. Wählen Sie erneut **Save** (Speichern) und dann **Close Editing** (Bearbeitung beenden) aus.

### <a name="add-a-network-segment"></a>Hinzufügen eines Netzwerksegments

[!INCLUDE [add-network-segment-steps](includes/add-network-segment-steps.md)]


## <a name="create-dhcp-relay-service"></a>Erstellen eines DHCP-Relaydiensts

Wenn Sie einen externen DHCP-Server eines Drittanbieters verwenden möchten, müssen Sie einen DHCP-Relaydienst erstellen. Außerdem geben Sie den DHCP-IP-Adressbereich in NSX-T Manager an. 

1. Wählen Sie in NSX-T Manager die Optionen **Networking** > **DHCP** (Netzwerk > DHCP) und dann **Add Server** (Server hinzufügen) aus.

1. Wählen Sie **DHCP Relay** unter **Server Type** (Servertyp) aus, geben Sie den Servernamen und die IP-Adresse an, und wählen Sie dann **Save** (Speichern) aus.

   :::image type="content" source="./media/manage-dhcp/create-dhcp-relay.png" alt-text="Erstellen eines DHCP-Relaydiensts" border="true":::

1. Wählen Sie **Tier-1 Gateways** (Gateways der Ebene 1), dann die vertikalen Auslassungspunkte für das Tier-1 Gateway (Gateway der Ebene 1) und anschließend **Edit** (Bearbeiten) aus.

   :::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway-relay.png" alt-text="Gateway der Ebene 1 bearbeiten" border="true":::

1. Wählen Sie **No IP Allocation Set** (Keine IP-Zuteilung festgelegt) aus, um die Zuteilung von IP-Adressen zu definieren.

   :::image type="content" source="./media/manage-dhcp/edit-ip-address-allocation.png" alt-text="Zuteilung von IP-Adressen bearbeiten" border="true":::

1. Wählen Sie unter **Type** (Typ) die Option **DHCP Server** aus. 
   
1. Wählen Sie **DHCP Relay** für **DHCP Server** und dann **Save** (Speichern) aus.

1. Wählen Sie erneut **Save** (Speichern) und dann **Close Editing** (Bearbeitung beenden) aus.


## <a name="specify-the-dhcp-ip-address-range"></a>Angeben des DHCP-IP-Adressbereichs

1. Wählen Sie in NSX-T Manager die Optionen **Networking** > **Segments** (Netzwerk > Segmente) aus. 
   
1. Wählen Sie die vertikalen Auslassungspunkte für den Segmentnamen und dann **Edit** (Bearbeiten) aus.
   
1. Wählen Sie **Set Subnets** (Subnetze festlegen) aus, um die DHCP-IP-Adresse für das Subnetz anzugeben. 
   
   :::image type="content" source="./media/manage-dhcp/network-segments.png" alt-text="Netzwerksegmente" border="true":::
      
1. Ändern Sie ggf. die Gateway-IP-Adresse, und geben Sie den IP-Adressbereich für DHCP ein. 
      
   :::image type="content" source="./media/manage-dhcp/edit-subnet.png" alt-text="Subnetze bearbeiten" border="true":::
      
1. Wählen Sie **Apply** (Übernehmen) und dann **Save** (Speichern) aus. Das Segment wird einem DHCP-Serverpool zugewiesen.
      
   :::image type="content" source="./media/manage-dhcp/assigned-to-segment.png" alt-text="Einem Segment zugewiesener DHCP-Serverpool" border="true":::


## <a name="send-dhcp-requests-to-the-on-premises-dhcp-server"></a>Senden von DHCP-Anforderungen an den lokalen DHCP-Server

Wenn Sie DHCP-Anforderungen von Ihren Azure VMware Solution-VMs im gestreckten L2-Segment an den lokalen DHCP-Server senden möchten, müssen Sie ein Sicherheitssegmentprofil erstellen. 

1. Melden Sie sich bei Ihrem lokalen vCenter an, und wählen Sie unter **Home** die Option **HCX** aus.

1. Wählen Sie **Network Extension** (Netzwerkerweiterung) unter **Services** (Dienste) aus.

1. Wählen Sie die Netzwerkerweiterung aus, die DHCP-Anforderungen von Azure VMware Solution an das lokale System unterstützen soll. 

1. Notieren Sie sich den Namen des Zielnetzwerks.  

   :::image type="content" source="media/manage-dhcp/hcx-find-destination-network.png" alt-text="Screenshot einer Netzwerkerweiterung im VMware vSphere-Client" lightbox="media/manage-dhcp/hcx-find-destination-network.png":::

1. Wählen Sie in Azure VMware Solution in NSX-T Manager die Optionen **Networking** > **Segments** > **Segment Profiles** (Netzwerk > Segmente > Segmentprofile) aus. 

1. Wählen Sie **Add Segment Profile** (Segmentprofil hinzufügen) und dann **Segment Security** (Segmentsicherheit) aus.

   :::image type="content" source="media/manage-dhcp/add-segment-profile.png" alt-text="Screenshot des Hinzufügens eines Segmentprofils in NSX-T" lightbox="media/manage-dhcp/add-segment-profile.png":::

1. Geben Sie einen Namen und ein Tag an, und legen Sie dann den **BPDU Filter** auf „Ein“ und alle DHCP-Umschaltflächen auf „Aus“ fest.

   :::image type="content" source="media/manage-dhcp/add-segment-profile-bpdu-filter-dhcp-options.png" alt-text="Screenshot der aktivierten BPDU-Filter und deaktivierten DHCP-Umschaltflächen" lightbox="media/manage-dhcp/add-segment-profile-bpdu-filter-dhcp-options.png":::

1. Entfernen Sie alle MAC-Adressen, falls vorhanden, in der Liste **BPDU Filter Allow List** (Liste zugelassener BPDU-Filter).  Klicken Sie dann auf **Speichern**.

   :::image type="content" source="media/manage-dhcp/add-segment-profile-bpdu-filter-allow-list.png" alt-text="Screenshot von MAC-Adressen in der Liste zugelassener BPDU-Filter":::

1. Geben Sie unter **Networking** > **Segments** > **Segments** (Netzwerk > Segmente > Segmente) im Suchbereich den festgelegten Netzwerknamen ein.

   :::image type="content" source="media/manage-dhcp/networking-segments-search.png" alt-text="Screenshot des Filterfelds unter „Networking“ (Netzwerk) > „Segments“ (Segmente)":::

1. Wählen Sie die vertikalen Auslassungspunkte für den Segmentnamen und dann **Edit** (Bearbeiten) aus.

   :::image type="content" source="media/manage-dhcp/edit-network-segment.png" alt-text="Screenshot der Schaltfläche „Edit“ (Bearbeiten) für das Segment" lightbox="media/manage-dhcp/edit-network-segment.png":::

1. Ändern Sie **Segment Security** (Segmentsicherheit) in das zuvor erstellte Segmentprofil.

   :::image type="content" source="media/manage-dhcp/edit-segment-security.png" alt-text="Screenshot des Felds „Segment Security“ (Segmentsicherheit)" lightbox="media/manage-dhcp/edit-segment-security.png":::

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu [Hostwartung und Lebenszyklusverwaltung](concepts-private-clouds-clusters.md#host-maintenance-and-lifecycle-management)