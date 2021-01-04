---
title: Einrichten von vRealize Operations für Azure VMware Solution
description: Hier erfahren Sie, wie Sie vRealize Operations für Ihre private Azure VMware Solution-Cloud einrichten.
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 25469089cf1fef076711bfaf1492fad43edbcf33
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2020
ms.locfileid: "92371782"
---
# <a name="set-up-vrealize-operations-for-azure-vmware-solution"></a>Einrichten von vRealize Operations für Azure VMware Solution


vRealize Operations Manager ist eine Betriebsverwaltungsplattform, mit der VMware-Infrastrukturadministratoren Systemressourcen überwachen können. Diese Systemressourcen können (sowohl physische als auch virtuelle) Objekte auf Anwendungs- oder Infrastrukturebene sein. Die meisten VMware-Administratoren haben vRealize Operations zum Überwachen und Verwalten der Private Cloud-Komponenten von VMware verwendet: vCenter, ESXi, NSX-T, vSAN und VMware HCX.  Zu jeder bereitgestellten privaten Azure VMware Solution-Cloud gehört eine dedizierte vCenter-, NSX-T-, vSAN- und HCX-Bereitstellung. 

Lesen Sie zuerst gründlich die Informationen unter [Voraussetzungen (Vorbereitung)](#before-you-begin) und [Voraussetzungen](#prerequisites). Anschließend begleiten wir Sie durch die beiden typischen Bereitstellungstopologien:

> [!div class="checklist"]
> * [Lokale vRealize Operations-Verwaltung von Azure VMware Solution-Bereitstellung](#on-premises-vrealize-operations-managing-azure-vmware-solution-deployment)
> * [Ausführung von vRealize Operations in Azure VMware Solution-Bereitstellung](#vrealize-operations-running-on-azure-vmware-solution-deployment)

## <a name="before-you-begin"></a>Voraussetzungen
* Weitere Informationen zur Bereitstellung von vRealize Operations finden Sie in der [Produktdokumentation zu vRealize Operations Manager](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html). 
* Sehen Sie sich die [Tutorialreihe](tutorial-network-checklist.md) mit Grundlagen zu Azure VMware Solution Software Defined Datacenter (SDDC) an.
* Lesen Sie optional die Produktdokumentation zum [vRealize Operations-Remotecontroller](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-263F9219-E801-4383-8A59-E84F3D01ED6B.html) für die Option „lokale vRealize Operations-Verwaltung von Azure VMware Solution-Bereitstellung“. 



## <a name="prerequisites"></a>Voraussetzungen
* Zwischen lokalen Ressourcen und Azure VMware Solution SDDC muss eine VPN- oder Azure ExpressRoute-Verbindung konfiguriert sein.
* Eine private Azure VMware Solution-Cloud wurde in Azure bereitgestellt.



## <a name="on-premises-vrealize-operations-managing-azure-vmware-solution-deployment"></a>Lokale vRealize Operations-Verwaltung von Azure VMware Solution-Bereitstellung
Die meisten Kunden verfügen über eine vorhandene lokale Bereitstellung von vRealize Operations, die zum Verwalten einer oder mehrerer lokaler vCenter-Domänen verwendet wird. Wenn sie eine private Azure VMware Solution-Cloud bereitstellen, verbinden sie ihre lokale Umgebung mithilfe einer Azure ExpressRoute- oder Layer 3-VPN-Lösung mit ihrer privaten Cloud.  

:::image type="content" source="media/vrealize-operations-manager/vrealize-operations-deployment-option-1.png" alt-text="Lokale vRealize Operations-Verwaltung von Azure VMware Solution-Bereitstellung"  border="false":::

Um die vRealize Operations-Funktionen auf die private Azure VMware Solution-Cloud zu erweitern, erstellen Sie eine [Adapterinstanz für die Ressourcen der privaten Cloud](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.config.doc/GUID-640AD750-301E-4D36-8293-1BFEB67E2600.html). Sie sammelt Daten aus der privaten Azure VMware Solution-Cloud und überträgt sie in die lokale vRealize Operations-Instanz. Die lokale vRealize Operations Manager-Instanz kann direkt mit vCenter und NSX-T Manager in Azure VMware Solution verbunden werden. Optional können Sie einen vRealize Operations Remote Collector in der privaten Azure VMware Solution-Cloud bereitstellen. Der Collector komprimiert und verschlüsselt die in der privaten Cloud gesammelten Daten, bevor sie über das ExpressRoute- oder VPN-Netzwerk an die lokal ausgeführte vRealize Operations Manager-Instanz gesendet werden. 

> [!TIP]
> Eine schrittweise Anleitung für die Installation von vRealize Operations Manager finden Sie in der [VMware-Dokumentation](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html). 



## <a name="vrealize-operations-running-on-azure-vmware-solution-deployment"></a>Ausführung von vRealize Operations in Azure VMware Solution-Bereitstellung

Eine weitere Möglichkeit ist die Bereitstellung einer Instanz von vRealize Operations Manager in einem vSphere-Cluster in der privaten Cloud. 

:::image type="content" source="media/vrealize-operations-manager/vrealize-operations-deployment-option-2.png" alt-text="Ausführung von vRealize Operations in Azure VMware Solution" border="false":::

Nach Bereitstellung der Instanz können Sie vRealize Operations so konfigurieren, dass Daten aus vCenter, ESXi, NSX-T, vSAN und HCX erfasst werden. 

> [!TIP]
> Eine schrittweise Anleitung für die Installation von vRealize Operations Manager finden Sie in der [VMware-Dokumentation](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html).


## <a name="known-limitations"></a>Bekannte Einschränkungen

- Der Benutzer **cloudadmin\@vsphere.local** in Azure VMware Solution hat [eingeschränkte Berechtigungen](concepts-role-based-access-control.md).  Die gastinterne Speichererfassung mithilfe von VMware-Tools wird für virtuelle Computern (VMs) in Azure VMware Solution nicht unterstützt.  Die Nutzung des aktiven und verbrauchten Speichers funktioniert in diesem Fall weiterhin.
- Die Workloadoptimierung für die hostbasierte Geschäftsabsicht funktioniert nicht, da Azure VMware-Lösungen Clusterkonfigurationen verwalten, einschließlich DRS-Einstellungen.
- Die Workloadoptimierung für die clusterübergreifende Platzierung im SDDC unter Verwendung der clusterbasierten Geschäftsabsicht wird ab vRealize Operations Manager 8.0 vollständig unterstützt. Die Workloadoptimierung hat jedoch keine Kenntnis über Ressourcenpools und platziert die VMs auf Clusterebene. Ein Benutzer kann dies manuell auf der vCenter Server-Benutzeroberfläche für Azure VMware Solution korrigieren.
- Eine Anmeldung bei vRealize Operations Manager mit Ihren Azure VMware Solution vCenter Server-Anmeldeinformationen ist nicht möglich. 
- Azure VMware Solution unterstützt nicht das vRealize Operations Manager-Plug-In.

Beim Herstellen einer Verbindung zwischen Azure VMware Solution vCenter Server und vRealize Operations Manager mit einem vCenter Server-Cloudkonto wird folgende Warnung gezeigt:

:::image type="content" source="./media/vrealize-operations-manager/warning-adapter-instance-creation-succeeded.png" alt-text="Warnung – Adapterinstanzerstellung erfolgreich":::

Die Warnung wird angezeigt, weil der Benutzer **cloudadmin\@vsphere.local** in Azure VMware Solution nicht über ausreichende Berechtigungen verfügt, um alle für die Registrierung erforderlichen vCenter Server-Aktionen ausführen zu können. Allerdings reichen die Berechtigungen für die Datensammlung durch die Adapterinstanz aus (siehe folgende Abbildung):

:::image type="content" source="./media/vrealize-operations-manager/adapter-instance-to-perform-data-collection.png" alt-text="Adapterinstanz zum Durchführen der Datensammlung":::

Weitere Informationen finden Sie unter [Privileges Required for Configuring a vCenter Adapter Instance](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.core.doc/GUID-3BFFC92A-9902-4CF2-945E-EA453733B426.html) (Erforderliche Berechtigungen zum Konfigurieren einer vCenter-Adapterinstanz).

<!-- LINKS - external -->


<!-- LINKS - internal -->




