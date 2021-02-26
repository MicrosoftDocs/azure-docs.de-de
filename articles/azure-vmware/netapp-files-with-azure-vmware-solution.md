---
title: Azure NetApp Files mit Azure VMware Solution
description: Verwenden Sie Azure NetApp Files mit Azure VMware Solution-VMs, um Daten zwischen lokalen Servern, Azure VMware Solution-VMs und Cloudinfrastrukturen zu migrieren und zu synchronisieren.
ms.topic: how-to
ms.date: 02/08/2021
ms.openlocfilehash: 69d4e3a99de28d55b2fd95b1fc05c04c2ae0a37b
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "99988644"
---
# <a name="azure-netapp-files-with-azure-vmware-solution"></a>Azure NetApp Files mit Azure VMware Solution

In diesem Artikel werden die Schritte erläutert, mit denen Azure NetApp Files in Azure VMware Solution-basierte Workloads eingebunden wird. Das Gastbetriebssystem wird auf virtuellen Computern (VMs) ausgeführt, von denen auf Azure NetApp Files-Volumes zugegriffen wird. 

## <a name="azure-netapp-files-overview"></a>Übersicht über Azure NetApp Files

[Azure NetApp Files](../azure-netapp-files/azure-netapp-files-introduction.md) ist ein Azure-Dienst zum Migrieren und Ausführen der anspruchsvollsten Dateiworkloads von Unternehmen in der Cloud. Dies schließt Datenbanken, SAP und High Performance Computing-Anwendungen ohne Codeänderungen ein.

### <a name="features"></a>Features
(Dienste, für die Azure NetApp Files verwendet wird.)

- **Active Directory-Verbindungen**: Azure NetApp Files unterstützt [Active Directory Domain Services und Azure Active Directory Domain Services](../azure-netapp-files/azure-netapp-files-create-volumes-smb.md#decide-which-domain-services-to-use).

- **Freigabeprotokoll**: Azure NetApp Files unterstützt SMB- (Server Message Block) und NFS-Protokolle (Network File System). Diese Unterstützung bedeutet, dass die Volumes auf dem Linux-Client bereitgestellt und auf dem Windows-Client zugeordnet werden können.

- **Azure VMware Solution**: Azure NetApp Files-Freigaben können über VMs bereitgestellt werden, die in der Azure VMware Solution-Umgebung erstellt werden.

Azure NetApp Files ist in vielen Azure-Regionen verfügbar und unterstützt die regionsübergreifende Replikation. Informationen zu den Methoden für die Azure NetApp Files-Konfiguration finden Sie unter [Speicherhierarchie von Azure NetApp Files](../azure-netapp-files/azure-netapp-files-understand-storage-hierarchy.md).

## <a name="reference-architecture"></a>Referenzarchitektur

Im folgenden Diagramm ist eine Verbindung über Azure ExpressRoute mit einer privaten Azure VMware Solution-Cloud dargestellt. Die Azure VMware Solution-Umgebung greift auf die Azure NetApp Files-Freigabe zu, die auf Azure VMware Solution-VMs bereitgestellt ist.

![Diagramm: NetApp Files für Azure VMware Solution-Architektur](media/net-app-files/net-app-files-topology.png)

Dieser Artikel enthält eine Anleitung zum Einrichten, Testen und Überprüfen des Azure NetApp Files-Volumes als Dateifreigabe für Azure VMware Solution-VMs. Für dieses Szenario wurde das NFS-Protokoll verwendet. Azure NetApp Files und Azure VMware Solution werden in derselben Azure-Region erstellt.

## <a name="prerequisites"></a>Voraussetzungen 

> [!div class="checklist"]
> * Azure-Abonnement mit Aktivierung von Azure NetApp Files
> * Subnetz für Azure NetApp Files
> * Linux-VM auf Azure VMware Solution
> * Windows-VMs unter Azure VMware Solution

## <a name="regions-supported"></a>Unterstützte Regionen

Eine Liste mit den unterstützten Regionen finden Sie unter [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/?products=netapp,azure-vmware&regions=all).

## <a name="verify-pre-configured-azure-netapp-files"></a>Überprüfen der vorkonfigurierten Azure NetApp Files-Instanz 

Befolgen Sie die Schritt-für-Schritt-Anleitungen in den folgenden Artikeln, um Azure NetApp Files-Volumes auf Azure VMware Solution-VMs zu erstellen und bereitzustellen.

- [Erstellen eines NetApp-Kontos](../azure-netapp-files/azure-netapp-files-create-netapp-account.md)
- [Einrichten eines Kapazitätspools](../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md)
- [Erstellen eines SMB-Volumes für Azure NetApp Files](../azure-netapp-files/azure-netapp-files-create-volumes-smb.md)
- [Erstellen eines NFS-Volumes für Azure NetApp Files](../azure-netapp-files/azure-netapp-files-create-volumes.md)
- [Delegieren eines Subnetzes für Azure NetApp Files](../azure-netapp-files/azure-netapp-files-delegate-subnet.md)

In den folgenden Schritten geht es um die Überprüfung der vorkonfigurierten Azure NetApp Files-Instanzen, die in Azure mit Premium-Servicelevel für Azure NetApp Files erstellt wurden.

1. Wählen Sie im Azure-Portal unter **Speicher** die Option **Azure NetApp Files** aus. Eine Liste der konfigurierten Azure NetApp Files-Konten wird angezeigt. 

    :::image type="content" source="media/net-app-files/azure-net-app-files-list.png" alt-text="Screenshot: Liste mit vorkonfigurierten Azure NetApp Files-Instanzen"::: 

2. Wählen Sie ein konfiguriertes NetApp Files-Konto aus, um die zugehörigen Einstellungen anzuzeigen. Wählen Sie beispielsweise **Contoso-anf2** aus. 

3. Wählen Sie **Kapazitätspools** aus, um den konfigurierten Pool zu überprüfen. 

    :::image type="content" source="media/net-app-files/net-app-settings.png" alt-text="Screenshot: Optionen zum Anzeigen von Kapazitätspools und Volumes eines konfigurierten NetApp Files-Kontos":::

    Die Seite „Kapazitätspools“ wird geöffnet, und die Kapazität und der Servicelevel werden angezeigt. In diesem Beispiel wurde der Speicherpool mit einer Kapazität von 4 TiB und dem Servicelevel „Premium“ konfiguriert.

4. Wählen Sie die Option **Volumes** aus, um die im Kapazitätspool erstellten Volumes anzuzeigen. (Siehe obigen Screenshot.)

5. Wählen Sie ein Volume aus, um dessen Konfiguration anzuzeigen.  

    :::image type="content" source="media/net-app-files/azure-net-app-volumes.png" alt-text="Screenshot: Im Kapazitätspool erstellte Volumes":::

    Ein Fenster wird geöffnet, in dem die Konfigurationsdetails des Volumes angezeigt werden.

    :::image type="content" source="media/net-app-files/configuration-of-volume.png" alt-text="Screenshot: Konfigurationsdetails eines Volumes":::

    Sie können erkennen, dass das Volume ANFVOLUME eine Größe von 200 GiB hat und sich im Kapazitätspool ANFPOOL1 befindet.  Es wird als NFS-Dateifreigabe über 10.22.3.4:/ANFVOLUME exportiert. Eine private IP-Adresse aus dem virtuellen Azure-Netzwerk (VNET) wurde für Azure NetApp Files und den NFS-Pfad für die Bereitstellung der VM erstellt. Informationen zur Leistung des Azure NetApp Files-Volumes relativ zur Größe (bzw. zum Kontingent) finden Sie unter [Überlegungen zur Leistung für Azure NetApp Files](../azure-netapp-files/azure-netapp-files-performance-considerations.md). 

## <a name="verify-pre-configured-azure-vmware-solution-vm-share-mapping"></a>Überprüfen der Freigabezuordnung für die vorkonfigurierte Azure VMware Solution-VM

Damit eine Azure NetApp Files-Freigabe auf eine Azure VMware Solution-VM zugreifen kann, ist es wichtig, die Zuordnung zwischen SMB- und NFS-Freigaben zu verstehen. Die SMB- oder NFS-Volumes können erst dann wie hier beschrieben bereitgestellt werden, nachdem sie konfiguriert wurden.

- SMB-Freigabe: Erstellen Sie eine Active Directory-Verbindung, bevor Sie ein SMB-Volume bereitstellen. Damit erfolgreich eine Verbindung hergestellt werden kann, muss das delegierte Subnetz von Azure NetApp Files auf die angegebenen Domänencontroller zugreifen können. Nachdem die Active Directory-Instanz im Azure NetApp Files-Konto konfiguriert wurde, wird sie beim Erstellen eines SMB-Volumes als auswählbares Element angezeigt.

- NFS-Freigabe: Azure NetApp Files trägt zur Erstellung der Volumes bei, indem das NFS-Protokoll oder ein duales Protokoll (NFS und SMB) verwendet wird. Der Kapazitätsverbrauch eines Volumes wird mit der bereitgestellten Kapazität des dazugehörigen Pools verrechnet. NFS kann auf dem Linux-Server bereitgestellt werden, indem die Befehlszeilen oder „/etc/fstab“-Einträge verwendet werden.

## <a name="use-cases-of-azure-netapp-files-with-azure-vmware-solution"></a>Anwendungsfälle für Azure NetApp Files mit Azure VMware Solution

Hier sind einige interessante Anwendungsfälle für Azure NetApp Files aufgeführt. 
- Verwaltung von Horizon-Profilen
- Verwaltung von Citrix-Profilen
- Verwaltung von Profilen für Remotedesktopdienste
- Dateifreigaben unter Azure VMware Solution

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie sich mit der Integration von Azure NetApp Files und Ihrer Azure VMware Solution-Workloads beschäftigt haben, möchten Sie möglicherweise mehr über Folgendes erfahren:

- [Ressourceneinschränkungen](../azure-netapp-files/azure-netapp-files-resource-limits.md#resource-limits)
- [Richtlinien für die Azure NetApp Files-Netzwerkplanung](../azure-netapp-files/azure-netapp-files-network-topologies.md)
- [Regionsübergreifende Replikation von Azure NetApp Files-Volumes](../azure-netapp-files/cross-region-replication-introduction.md) 
- [Häufig gestellte Fragen zu Azure NetApp Files](../azure-netapp-files/azure-netapp-files-faqs.md)
