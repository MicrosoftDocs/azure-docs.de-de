---
title: Planen der Azure VMware Solution-Bereitstellung
description: In diesem Artikel wird der Workflow für die Bereitstellung einer Azure VMware Solution-Instanz beschrieben.  Das Endergebnis ist eine Umgebung, die für die Erstellung und Migration von virtuellen Computern (VMs) vorbereitet ist.
ms.topic: tutorial
ms.date: 10/16/2020
ms.openlocfilehash: 1d1deebd56717c63a745500198eb8df9e3687b49
ms.sourcegitcommit: 65a4f2a297639811426a4f27c918ac8b10750d81
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96558690"
---
# <a name="planning-the-azure-vmware-solution-deployment"></a>Planen der Azure VMware Solution-Bereitstellung

In diesem Artikel wird der Planungsprozess zum Identifizieren und Sammeln von Daten beschrieben, die während der Bereitstellung verwendet werden. Stellen Sie beim Planen Ihrer Bereitstellung sicher, dass Sie die gesammelten Informationen dokumentieren, damit Sie während des Bereitstellungsvorgangs leicht darauf zugreifen können.

Die Prozesse dieser Schnellstartanleitung führen zu einer für die Produktion vorbereiteten Umgebung zum Erstellen von virtuellen Computern (VMs) und Durchführen der Migration. 

>[!IMPORTANT]
>Halten Sie sich vor dem Erstellen Ihrer Azure VMware Solution-Ressource an die Informationen im Artikel [Aktivieren einer Azure VMware Solution-Ressource](enable-azure-vmware-solution.md), um ein Supportticket für die Zuordnung Ihrer Hosts zu übermitteln. Nachdem das Supportteam Ihre Anforderung erhalten hat, dauert es bis zu fünf Werktage, bis die Anforderung bestätigt wurde und Ihre Hosts zugewiesen wurden. Wenn Sie über eine vorhandene private Azure VMware Solution-Cloud verfügen und weitere Hosts zugeordnet werden sollen, müssen Sie den gleichen Prozess durchlaufen. 


## <a name="subscription"></a>Subscription

Identifizieren Sie das Abonnement, das Sie zum Bereitstellen von Azure VMware Solution verwenden möchten.  Sie können entweder ein neues Abonnement erstellen oder ein vorhandenes wiederverwenden.

>[!NOTE]
>Das Abonnement muss einem Microsoft Enterprise Agreement oder einem Azure-Plan für Cloud Solution Provider zugeordnet sein. Weitere Informationen finden Sie unter [Aktivieren einer Azure VMware Solution-Ressource](enable-azure-vmware-solution.md).

## <a name="resource-group"></a>Resource group

Identifizieren Sie die Ressourcengruppe, die Sie für Ihre Azure VMware Solution-Instanz verwenden möchten.  Im Allgemeinen wird eine Ressourcengruppe speziell für Azure VMware Solution erstellt, aber Sie können auch eine vorhandene Ressourcengruppe verwenden.

## <a name="region"></a>Region

Identifizieren Sie die Region, in der Azure VMware Solution bereitgestellt werden soll.  Weitere Informationen finden Sie unter [Verfügbare Produkte nach Region](https://azure.microsoft.com/en-us/global-infrastructure/services/?products=azure-vmware).

## <a name="resource-name"></a>Ressourcenname

Legen Sie den Ressourcennamen fest, den Sie während der Bereitstellung verwenden möchten.  Der Ressourcenname ist ein benutzerfreundlicher und beschreibender Name, in dem Sie Ihre private Azure VMware Solution-Cloud angeben.

>[!IMPORTANT]
>Der Name darf maximal 40 Zeichen lang sein. Wenn der Name diesen Grenzwert überschreitet, können Sie keine öffentlichen IP-Adressen für die Verwendung mit der privaten Cloud erstellen. 

## <a name="size-hosts"></a>Größenhosts

Identifizieren Sie die Größenhosts, die Sie beim Bereitstellen von Azure VMware Solution verwenden möchten.  Eine vollständige Liste finden Sie in der Dokumentation zu [privaten Clouds und Clustern unter Azure VMware Solution](concepts-private-clouds-clusters.md#hosts).

## <a name="number-of-hosts"></a>Anzahl von Hosts

Definieren Sie die Anzahl von Hosts, die Sie in der privaten Azure VMware Solution-Cloud bereitstellen möchten.  Die Mindestanzahl von Hosts beträgt 3, und der Höchstwert beträgt  16 pro Cluster.  Weitere Informationen finden Sie unter [Azure VMware Solution (Vorschau): Konzepte – Private Clouds und Cluster](concepts-private-clouds-clusters.md#clusters).

Sie können den Cluster später jederzeit erweitern, wenn Sie die anfängliche Bereitstellungsanzahl erhöhen möchten.

## <a name="vcenter-admin-password"></a>vCenter admin password (vCenter-Administratorkennwort)
Definieren Sie das vCenter-Administratorkennwort.  Während der Bereitstellung erstellen Sie ein vCenter-Administratorkennwort. Das Kennwort gilt für das Administratorkonto cloudadmin@vsphere.local während des vCenter-Buildvorgangs. Sie verwenden es für die Anmeldung bei vCenter.

## <a name="nsx-t-admin-password"></a>NSX-T-Administratorkennwort
Definieren Sie das NSX-T-Administratorkennwort.  Während der Bereitstellung erstellen Sie ein NSX-T-Administratorkennwort. Das Kennwort wird dem Administratorbenutzer im NSX-Konto während des NSX-Buildvorgangs zugewiesen. Sie verwenden es für die Anmeldung bei NSX-T Manager.

## <a name="ip-address-segment"></a>IP-Adressensegment

Der erste Schritt bei der Planung der Bereitstellung ist das Planen der IP-Segmentierung.  Azure VMware Solution erfasst ein von Ihnen bereitgestelltes Netzwerk vom Typ „/22“. Anschließend wird es in kleinere IP-Segmente unterteilt, die dann für vCenter, VMware HCX, NSX-T und vMotion verwendet werden.

Azure VMware Solution stellt über eine interne ExpressRoute-Leitung eine Verbindung mit Ihrer Microsoft Azure Virtual Network-Instanz her. In den meisten Fällen wird eine Verbindung mit Ihrem Rechenzentrum per ExpressRoute Global Reach hergestellt. 

Azure VMware Solution, Ihre vorhandene Azure-Umgebung und Ihre lokale Umgebung tauschen (normalerweise) untereinander Routen aus. Daher sollte sich der CIDR-Netzwerkadressblock vom Typ „/22“, den Sie in diesem Schritt definieren, nicht mit Komponenten überlappen, die bei Ihnen lokal oder in Azure bereits vorhanden sind.

**Beispiel:** 10.0.0.0/22

Weitere Informationen finden Sie in der [Checkliste für die Netzwerkplanung](tutorial-network-checklist.md#routing-and-subnet-considerations).

:::image type="content" source="media/pre-deployment/management-vmotion-vsan-network-ip-diagram.png" alt-text="Identifizieren: IP-Adressensegment" border="false":::  

## <a name="ip-address-segment-for-virtual-machine-workloads"></a>IP-Adressensegment für VM-Workloads

Identifizieren Sie ein IP-Segment, um Ihr erstes Netzwerk (NSX-Segment) in Ihrer privaten Cloud zu erstellen.  Anders ausgedrückt: Sie möchten ein Netzwerksegment in Azure VMware Solution erstellen, damit Sie virtuelle Computer unter Azure VMware Solution bereitstellen können.   

Auch wenn Sie eine Erweiterung von L2-Netzwerken planen, sollten Sie ein Netzwerksegment erstellen, das die Umgebung überprüft.

Beachten Sie, dass alle erstellten IP-Segmente in Ihrer Azure- und lokalen Umgebung eindeutig sein müssen.  

**Beispiel:** 10.0.4.0/24

:::image type="content" source="media/pre-deployment/nsx-segment-diagram.png" alt-text="Identifizieren: IP-Adressensegment für VM-Workloads" border="false":::     

## <a name="optional-extend-networks"></a>(Optional) Erweitern von Netzwerken

Sie können Netzwerksegmente aus der lokalen Umgebung auf Azure VMware Solution erweitern. Wenn Sie dies tun, sollten Sie diese Netzwerke jetzt identifizieren.  

Bedenken Sie Folgendes:

- Wenn Sie die Erweiterung von Netzwerken aus der lokalen Umgebung planen, muss für diese Netzwerke eine Verbindung mit einem [vSphere Distributed Switch (vDS)](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.networking.doc/GUID-B15C6A13-797E-4BCB-B9D9-5CBC5A60C3A6.html) in Ihrer lokalen VMware-Umgebung bestehen.  
- Falls die zu erweiternden Netzwerke auf einem [vSphere Standard Switch](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.networking.doc/GUID-350344DE-483A-42ED-B0E2-C811EE927D59.html) angeordnet sind, können sie nicht erweitert werden.

## <a name="azure-virtual-network-to-attach-azure-vmware-solution"></a>Anfügen von Azure VMware Solution an ein Azure Virtual Network

Zum Zugreifen auf Ihre private Azure VMware Solution-Cloud muss die ExpressRoute-Leitung, die Teil von Azure VMware Solution ist, an ein Azure Virtual Network angefügt werden.  Während der Bereitstellung können Sie ein neues virtuelles Netzwerk definieren oder ein vorhandenes auswählen.

Für die ExpressRoute-Leitung von Azure VMware Solution wird eine Verbindung mit einem ExpressRoute-Gateway im Azure Virtual Network hergestellt, das Sie in diesem Schritt definieren.  

>[!IMPORTANT]
>Sie können ein vorhandenes ExpressRoute-Gateway verwenden, um eine Verbindung mit Azure VMware Solution herzustellen, sofern es nicht den Grenzwert von vier ExpressRoute-Leitungen pro virtuellem Netzwerk überschreitet.  Für den Zugriff auf Azure VMware Solution von einer lokalen Umgebung über ExpressRoute benötigen Sie ExpressRoute Global Reach, da das ExpressRoute-Gateway kein transitives Routing zwischen den verbundenen Leitungen bereitstellt.  

Falls Sie für die ExpressRoute-Leitung eine Verbindung von Azure VMware Solution mit einem vorhandenen ExpressRoute-Gateway herstellen möchten, können Sie dies nach der Bereitstellung durchführen.  

Die Frage lautet also: Möchten Sie für Azure VMware Solution eine Verbindung mit einem vorhandenen ExpressRoute-Gateway herstellen?  

* **Ja**: Identifizieren Sie das virtuelle Netzwerk, das während der Bereitstellung nicht verwendet wird.
* **Nein**: Identifizieren Sie ein vorhandenes virtuelles Netzwerk, oder erstellen Sie während der Bereitstellung ein neues.

Dokumentieren Sie in beiden Fällen, was Sie in diesem Schritt tun möchten.

>[!NOTE]
>Dieses virtuelle Netzwerk wird von Ihrer lokalen Umgebung und von Azure VMware Solution erkannt. Stellen Sie daher sicher, dass sich das in diesem virtuellen Netzwerk verwendete IP-Segment und die Subnetze nicht überlappen.

:::image type="content" source="media/pre-deployment/azure-vmware-solution-expressroute-diagram.png" alt-text="Identifizieren: Azure Virtual Network zum Anfügen von Azure VMware Solution" border="false":::

## <a name="vmware-hcx-network-segments"></a>VMware HCX-Netzwerksegmente

VMware HCX ist eine Technologie, die Teil des Azure VMware Solution-Pakets ist. Die primären Anwendungsfälle für VMware HCX sind Workloadmigrationen und Notfallwiederherstellung. Wenn Sie beides durchführen möchten, ist es ratsam, das Netzwerk jetzt zu planen.   Andernfalls können Sie diesen Schritt überspringen und mit dem nächsten fortfahren.

[!INCLUDE [hcx-network-segments](includes/hcx-network-segments.md)]

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie die erforderlichen Informationen nun gesammelt und dokumentiert haben, können Sie mit dem nächsten Abschnitt fortfahren, um Ihre private Azure VMware Solution-Cloud zu erstellen.

> [!div class="nextstepaction"]
> [Bereitstellen von Azure VMware Solution](deploy-azure-vmware-solution.md)
