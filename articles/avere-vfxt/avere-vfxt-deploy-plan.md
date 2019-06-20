---
title: Planen des Avere vFXT-Systems – Azure
description: Erläutert die Planung vor der Bereitstellung von Avere vFXT für Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: v-erkell
ms.openlocfilehash: 46978d19a0789bb43e861ca89661aa5b78eb4ec7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60409878"
---
# <a name="plan-your-avere-vfxt-system"></a>Planen des Avere vFXT-Systems

In diesem Artikel wird erläutert, wie Sie einen neuen Avere vFXT für Azure-Cluster planen, der entsprechend Ihren Anforderungen angemessen positioniert und dimensioniert ist. 

Bevor Sie zum Azure Marketplace wechseln oder virtuelle Computer erstellen, überlegen Sie, wie der Cluster mit anderen Elementen in Azure interagieren wird. Planen Sie, wo sich die Clusterressourcen in Ihrem privaten Netzwerk und Ihren Subnetzen befinden werden, und entscheiden Sie, wo sich Ihr Back-End-Speicher befinden soll. Stellen Sie sicher, dass die von Ihnen erstellten Clusterknoten leistungsfähig genug sind, um Ihren Workflow zu unterstützen. 

Weitere Informationen finden Sie im Folgenden.

## <a name="resource-group-and-network-infrastructure"></a>Ressourcengruppe und Netzwerkinfrastruktur

Überlegen Sie, wo sich die Elemente Ihrer Avere vFXT für Azure-Bereitstellung befinden werden. Das folgende Diagramm zeigt eine mögliche Anordnung der Avere vFXT für Azure-Komponenten:

![Diagramm, das den Clustercontroller und die virtuellen Clustercomputer innerhalb eines Subnetzes darstellt. Die Subnetzgrenze ist von einer VNet-Begrenzung umgeben. Innerhalb des VNets befindet sich ein Sechseck, das den Endpunkt des Speicherdiensts darstellt. Es ist mit einem gestrichelten Pfeil mit einem Blob-Speicher außerhalb des VNets verbunden.](media/avere-vfxt-components-option.png)

Befolgen Sie die folgenden Richtlinien bei der Planung der Netzwerkinfrastruktur Ihres Avere vFXT-Systems:

* Alle Elemente sollten mit einem neuen Abonnement verwaltet werden, das für die Avere vFXT-Bereitstellung erstellt wurde. Dies hat unter anderem folgende Vorteile: 
  * Einfachere Nachverfolgung der Kosten: Zeigen Sie alle Kosten aus Ressourcen, Infrastruktur und Computezyklen in einem Abonnement an, und überprüfen Sie sie.
  * Einfachere Bereinigung: Sie können das gesamte Abonnement entfernen, wenn Sie das Projekt abgeschlossen haben.
  * Bequeme Partitionierung von Ressourcenkontingenten: Schützen Sie andere kritische Workloads vor möglicher Ressourcendrosselung, indem Sie die Avere vFXT-Clients und -Cluster in einem einzigen Abonnement isolieren. Dadurch wird ein Konflikt vermieden, wenn Sie eine große Anzahl von Clients für einen Hochleistungs-Computingworkflow nutzen.

* Platzieren Sie Ihre Clientcomputersysteme in der Nähe des vFXT-Clusters. Der Back-End-Speicher kann weiter entfernt sein.  

* Der vFXT-Cluster und die Clustercontroller-VM sollten im selben virtuellen Netzwerk (VNet) und in derselben Ressourcengruppe platziert werden sowie dasselbe Speicherkonto verwenden. Die Vorlage für die automatische Clustererstellung erledigt dies für die meisten Situationen.

* Der Cluster muss sich in einem eigenen Subnetz befinden, um IP-Adressenkonflikte mit Clients oder Computeressourcen zu vermeiden. 

* Die Vorlage für die Clustererstellung kann den Großteil der erforderlichen Infrastruktur für den Cluster erstellen, einschließlich Ressourcengruppen, virtueller Netzwerke, Subnetze und Speicherkonten. Wenn Sie Ressourcen verwenden möchten, die bereits vorhanden sind, stellen Sie sicher, dass sie die Anforderungen in dieser Tabelle erfüllen. 

  | Resource | Vorhandene verwenden? | Requirements (Anforderungen) |
  |----------|-----------|----------|
  | Ressourcengruppe | Ja, wenn leer. | Muss leer sein.| 
  | Speicherkonto | Ja, wenn ein Blob-Container nach der Erstellung des Clusters verbunden wird. <br/>  Nein, wenn ein Blob-Container während der Erstellung des Clusters erstellt. | Vorhandener Blob-Container muss leer sein. <br/> &nbsp; |
  | Virtuelles Netzwerk | Ja | Muss einen Speicherdienst-Endpunkt umfassen, wenn ein neuer Azure Blob-Container erstellt wird. | 
  | Subnetz | Ja |   |

## <a name="ip-address-requirements"></a>Anforderungen an die IP-Adresse 

Stellen Sie sicher, dass das Subnetz Ihres Clusters einen ausreichend großen IP-Adressbereich aufweist, um den Cluster zu unterstützen. 

Der Avere vFXT-Cluster verwendet die folgenden IP-Adressen:

* Eine IP-Adresse zur Verwaltung des Clusters. Diese Adresse kann im Cluster von Knoten zu Knoten verschoben werden, ist aber immer verfügbar, sodass Sie sich mit dem Konfigurationstool der Avere-Systemsteuerung verbinden können.
* Für jeden Clusterknoten:
  * Mindestens eine clientseitige IP-Adresse. (Alle clientseitigen Adressen werden vom *vserver* des Clusters verwaltet, der sie bei Bedarf zwischen den Knoten verschieben kann.)
  * Eine IP-Adresse für die Clusterkommunikation
  * Eine Instanz-IP-Adresse (dem virtuellen Computer zugeordnet)

Wenn Sie Azure-BLOB-Speicher verwenden, erfordert dieser möglicherweise auch IP-Adressen aus dem VNet Ihres Clusters:  

* Ein Azure-BLOB-Speicherkonto erfordert mindestens fünf IP-Adressen. Beachten Sie diese Anforderung, wenn Sie Blob-Speicher im gleichen VNet wie Ihren Cluster positionieren.
* Wenn Sie Azure-BLOB-Speicher verwenden, der sich außerhalb des virtuellen Netzwerks für Ihren Cluster befindet, sollten Sie einen Speicherdienstendpunkt innerhalb des VNets erstellen. Dieser Endpunkt verwendet keine IP-Adresse.

Sie haben die Möglichkeit, Netzwerkressourcen und Blob-Speicher (falls verwendet) in verschiedenen Ressourcengruppen des Clusters zu positionieren.

## <a name="vfxt-node-size"></a>vFXT-Knotengröße

Die virtuellen Computer, die als Clusterknoten dienen, bestimmen den Anforderungsdurchsatz und die Speicherkapazität Ihres Cache. <!-- The instance type offered has been chosen for its memory, processor, and local storage characteristics. You can choose from two instance types, with different memory, processor, and local storage characteristics. -->

Die einzelnen vFXT-Knoten sind identisch. Das bedeutet, dass Sie beim Erstellen eines Clusters mit drei Knoten über drei virtuelle Computer des gleichen Typs und mit identischer Größe verfügen. 

| Instanztyp | vCPUs | Arbeitsspeicher  | Lokaler SSD-Speicher  | Max. Anzahl Datenträger | Durchsatz des Datenträgers ohne Cache | NIC (Anzahl) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_E32s_v3 | 32  | 256 GiB | 512 GB  | 32 | 51\.200 IOPS <br/> 768 MBit/s | 16\.000 MBit/s (8)  |

Der Datenträgercache pro Knoten ist konfigurierbar und kann zwischen 1000 GB und 8000 GB liegen. 4 TB pro Knoten ist die empfohlene Cachegröße für Standard_E32s_v3-Knoten.

Weitere Informationen zu diesen virtuellen Computern finden Sie in der Microsoft Azure-Dokumentation:

* [Arbeitsspeicheroptimierte Größen virtueller Computer](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory)

## <a name="account-quota"></a>Kontokontingent

Stellen Sie sicher, dass Ihr Abonnement über die Kapazität verfügt, den Avere vFXT-Cluster sowie alle verwendeten Computer- oder Clientsysteme auszuführen. Weitere Informationen finden Sie unter [Kontingent für den vFXT-Cluster](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster).

## <a name="back-end-data-storage"></a>Back-End-Datenspeicher

Wo sollte der Avere vFXT-Cluster Ihre Daten speichern, wenn sie sich nicht im Cache befinden? Entscheiden Sie, ob Ihr Arbeitssatz langfristig in einem neuen Blobcontainer oder in einem vorhandenen Cloud- oder Hardwarespeichersystem gespeichert wird. 

Wenn Sie den Azure-BLOB-Speicher für das Back-End verwenden möchten, sollten Sie im Rahmen der Erstellung des vFXT-Clusters einen neuen Container erstellen. Diese Option erstellt und konfiguriert den neuen Container so, dass er sofort nach der Fertigstellung des Clusters einsatzbereit ist. 

Details finden Sie unter [Erstellen des Avere vFXT-Systems für Azure](avere-vfxt-deploy.md#create-the-avere-vfxt-for-azure).

> [!NOTE]
> Nur leere Blob-Speichercontainer können als Kernspeichereinheiten für das Avere vFXT-System verwendet werden. Der vFXT muss in der Lage sein, seinen Objektspeicher zu verwalten, ohne dass bestehende Daten erhalten bleiben müssen. 
>
> Lesen Sie [Verschieben von Daten in den vFXT-Cluster](avere-vfxt-data-ingest.md), um zu erfahren, wie Sie Daten effizient in den neuen Container des Clusters kopieren können, indem Sie Clientcomputer und den Avere vFXT-Cache verwenden.

Wenn Sie ein vorhandenes lokales Speichersystem verwenden möchten, müssen Sie es nach der Erstellung dem vFXT-Cluster hinzufügen. Ausführliche Anweisungen zum Hinzufügen eines vorhandenen Speichersystems zum Avere vFXT-Cluster finden Sie unter [Konfigurieren von Speicher](avere-vfxt-add-storage.md).

## <a name="cluster-access"></a>Clusterzugriff 

Der Avere vFXT-Cluster für Azure befindet sich in einem privaten Subnetz und besitzt keine öffentliche IP-Adresse. Sie müssen über eine Methode verfügen, um auf das private Subnetz für die Clusterverwaltung und Clientverbindungen zuzugreifen. 

Folgende Zugriffsoptionen sind verfügbar:

* Jump Host: Weisen Sie einer separaten VM im privaten Netzwerk eine öffentliche IP-Adresse zu, und erstellen Sie damit einen SSL-Tunnel zu den Clusterknoten. 

  > [!TIP]
  > Wenn Sie eine öffentliche IP-Adresse für den Clustercontroller festlegen, können Sie ihn als Jump Host verwenden. Weitere Informationen finden Sie unter [Clustercontroller als Jump Host](#cluster-controller-as-jump-host).

* Virtuelles privates Netzwerk (VPN): Konfigurieren Sie ein Point-to-Site- oder Site-to-Site-VPN mit Ihrem privaten Netzwerk.

* Azure ExpressRoute: Konfigurieren Sie eine private Verbindung über einen ExpressRoute-Partner. 

Ausführliche Informationen zu diesen Optionen finden Sie in der [Dokumentation zu Azure Virtual Network unter „Internetkommunikation“](../virtual-network/virtual-networks-overview.md#communicate-with-the-internet).

### <a name="cluster-controller-as-jump-host"></a>Clustercontroller als Jump Host

Wenn Sie eine öffentliche IP-Adresse für den Clustercontroller festlegen, können Sie diesen als Jump Host verwenden, um Kontakt mit dem Avere vFXT-Cluster von außerhalb des privaten Subnetzes herzustellen. Da der Controller jedoch Zugriffsrechte zum Ändern von Clusterknoten besitzt, entsteht ein kleines Sicherheitsrisiko.  

Zur Erhöhung der Sicherheit für einen Controller mit einer öffentlichen IP-Adresse erstellt das Bereitstellungsskript automatisch eine Netzwerksicherheitsgruppe, die den eingehenden Zugriff ausschließlich auf Port 22 beschränkt. Sie können das System weiter schützen, indem Sie den Zugriff auf Ihren Bereich von IP-Quelladressen sperren, d.h. nur Verbindungen von Computern erlauben, die Sie für den Clusterzugriff verwenden möchten.

Beim Erstellen des Clusters können Sie auswählen, ob Sie eine öffentliche IP-Adresse für den Clustercontroller erstellen möchten. 

* Wenn Sie ein neues VNET oder ein neues Subnetz erstellen, wird dem Clustercontroller eine öffentliche IP-Adresse zugewiesen.
* Wenn Sie ein vorhandenes VNET und Subnetz auswählen, verfügt der Clustercontroller nur über private IP-Adressen. 

## <a name="vm-access-roles"></a>Rollen für den Zugriff auf virtuelle Computer 

Azure verwendet die [rollenbasierte Zugriffssteuerung](../role-based-access-control/index.yml) (RBAC), um die virtuellen Clustercomputer für die Ausführung bestimmter Aufgaben zu autorisieren. Für den Clustercontroller ist beispielsweise die Autorisierung zum Erstellen und Konfigurieren der Clusterknoten-VMs erforderlich. In den Clusterknoten muss es möglich sein, anderen Clusterknoten IP-Adressen zuzuweisen oder neu zuzuweisen.

Für die virtuellen Avere vFXT-Computer werden zwei integrierte Azure-Rollen verwendet: 

* Der Clustercontroller verwendet die integrierte Rolle [Avere-Mitwirkender](../role-based-access-control/built-in-roles.md#avere-contributor). 
* Clusterknoten verwenden die integrierte Rolle [Avere-Bediener](../role-based-access-control/built-in-roles.md#avere-operator).

Wenn Sie die Zugriffsrollen für Avere vFXT-Komponenten anpassen möchten, müssen Sie eine eigene Rolle definieren und diese dann den virtuellen Computern bei der Erstellung zuweisen. Sie können nicht die Bereitstellungsvorlage im Azure Marketplace verwenden. Sie können sich an den Microsoft-Kundendienst und -Support wenden, indem Sie wie unter [Abrufen von Hilfe zu Ihrem System](avere-vfxt-open-ticket.md) beschrieben ein Supportticket im Azure-Portal erstellen. 

## <a name="next-step-understand-the-deployment-process"></a>Nächster Schritt: Verstehen des Bereitstellungsprozesses

[Übersicht über die Bereitstellung](avere-vfxt-deploy-overview.md) bietet eine Übersicht über alle Schritte, die erforderlich sind, um ein Avere vFXT für Azure-System zu erstellen und es für die Bereitstellung von Daten vorzubereiten.  