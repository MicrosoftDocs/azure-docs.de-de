---
title: Erstellen von Azure Service Fabric-Clustern unter Windows Server und Linux| Microsoft Docs
description: Service Fabric-Cluster werden unter Windows Server und Linux ausgeführt, sodass Service Fabric-Anwendungen überall dort bereitgestellt und gehostet werden können, wo die Ausführung von Windows Server oder Linux möglich ist.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/01/2019
ms.author: dekapur
ms.openlocfilehash: d1681aee9dc11f0dbd3133bced0b919a8c1623b8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60310921"
---
# <a name="overview-of-service-fabric-clusters-on-azure"></a>Übersicht über Service Fabric-Cluster in Azure
Ein Service Fabric-Cluster enthält eine per Netzwerk verbundene Gruppe von virtuellen oder physischen Computern, auf denen Ihre Microservices bereitgestellt und verwaltet werden. Ein Computer oder ein virtueller Computer, der Teil eines Clusters ist, wird als Clusterknoten bezeichnet. Cluster können auf Tausende von Knoten skaliert werden. Wenn Sie dem Cluster neue Knoten hinzufügen, verteilt Service Fabric Partitionsreplikate und Instanzen des Diensts auf die erhöhte Anzahl von Knoten neu, um sie auszugleichen. Die Gesamtleistung der Anwendung verbessert sich, und Konflikte beim Speicherzugriff werden reduziert. Wenn die Knoten im Cluster nicht effizient genutzt werden, können Sie die Anzahl der Knoten im Cluster verringern. Service Fabric gleicht die Partitionsreplikate und Instanzen erneut über die verringerte Anzahl von Knoten aus, um die Hardware der einzelnen Knoten besser zu nutzen.

Ein Knotentyp definiert die Größe, Anzahl und Eigenschaften einer Gruppe von Knoten (virtueller Computer) im Cluster. Jeden Knotentyp kann dann unabhängig zentral hoch- oder herunterskaliert werden, bei jedem Typ können unterschiedliche Portgruppen geöffnet sein, und die Typen können verschiedene Kapazitätsmetriken aufweisen. Mit Knotentypen werden Rollen für eine Gruppe von Clusterknoten definiert, z.B. „Front-End“ oder „Back-End“. Der Cluster kann über mehrere Knotentypen verfügen, aber der primäre Knotentyp muss bei Clustern, die in Produktionsumgebungen eingesetzt werden, mindestens fünf virtuelle Computer umfassen (für Testcluster sind mindestens drei virtuelle Computer erforderlich). [Service Fabric-Systemdienste](service-fabric-technical-overview.md#system-services) werden auf den Knoten des primären Knotentyps platziert. 

## <a name="cluster-components-and-resources"></a>Clusterkomponenten und -ressourcen
Ein Service Fabric-Cluster in Azure ist eine Azure-Ressource, die andere Azure-Ressourcen verwendet und damit interagiert:
* Virtuelle Computer und virtuelle Netzwerkkarten
* Skalierungsgruppen für virtuelle Computer
* virtuelle Netzwerke
* Lastenausgleichsmodule
* Speicherkonten
* öffentliche IP-Adressen

![Service Fabric-Cluster][Image]

### <a name="virtual-machine"></a>Virtueller Computer
Ein [virtueller Computer](/azure/virtual-machines/), der Teil eines Clusters ist, wird als „Knoten“ bezeichnet, obwohl ein Clusterknoten technisch gesehen ein Service Fabric-Runtimeprozess ist. Jeder Knoten erhält einen Knotennamen (Zeichenfolge). Knoten weisen Merkmale wie etwa [Platzierungseigenschaften](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints) auf. Jeder Computer bzw. virtuelle Computer verfügt über einen automatisch gestarteten Dienst (*FabricHost.exe*), der ab der Startzeit ausgeführt wird und mit dem dann die beiden ausführbaren Dateien *Fabric.exe* und *FabricGateway.exe* gestartet werden, aus denen der Cluster besteht. Eine Produktionsbereitstellung ist ein Knoten pro physischem oder virtuellem Computer. Für Testszenarien können Sie mehrere Knoten auf einem einzelnen Computer oder virtuellen Computer hosten, indem Sie mehrere Instanzen von *Fabric.exe* und *FabricGateway.exe* ausführen.

Jeder virtuelle Computer ist einer virtuelle Netzwerkschnittstellenkarte (NIC) zugeordnet, und jeder NIC wird eine private IP-Adresse zugewiesen.  Ein virtueller Computer wird über die NIC einem virtuellen Netzwerk und einem lokalen Lastenausgleich zugewiesen.

Alle virtuellen Computer in einem Cluster befinden sich in einem virtuellen Netzwerk.  Alle Knoten im selben Knotentyp/in derselben Skalierungsgruppe befinden sich im gleichen Subnetz im virtuellen Netzwerk.  Diese Knoten haben nur private IP-Adressen und sind außerhalb des virtuellen Netzwerks nicht direkt erreichbar.  Clients können über den Azure-Lastenausgleich auf Dienste auf den Knoten zugreifen.

### <a name="scale-setnode-type"></a>Skalierungsgruppe/Knotentyp
Beim Erstellen eines Clusters definieren Sie mindestens einen Knotentyp.  Die Knoten oder virtuellen Computern in einem Knotentyp weisen die gleiche Größe und die gleichen Merkmale auf, z. B. Anzahl der CPUs, Arbeitsspeicher, Anzahl der Datenträger und Datenträger-E/A.  Beispiel: Ein Knotentyp kann für kleine virtuelle Front-End-Computer mit für das Internet geöffneten Ports verwendet werden, während ein anderer Knotentyp für große virtuelle Back-End-Computer vorgesehen ist, die Daten verarbeiten. In Azure-Clustern ist jeder Knotentyp einer [VM-Skalierungsgruppe](/azure/virtual-machine-scale-sets/) zugeordnet.

Sie können Skalierungsgruppen verwenden, um eine Sammlung virtueller Computer als Gruppe bereitzustellen und zu verwalten. Jeder Knotentyp, den Sie in einem Azure Service Fabric-Cluster definieren, richtet eine separate Skalierungsgruppe ein. Für die Service Fabric-Runtime wird mithilfe von Azure-VM-Erweiterungen ein Bootstrapping-Vorgang auf jeden virtuellen Computer in der Skalierungsgruppe durchgeführt. Sie können jeden Knotentyp einzeln zentral hoch- oder herunterskalieren, auf jedem Clusterknoten die ausgeführte Betriebssystem-SKU ändern, bei jedem Typ unterschiedliche Portgruppen öffnen und verschiedene Kapazitätsmetriken verwenden. Eine Skalierungsgruppe hat fünf [Upgradedomänen](service-fabric-cluster-resource-manager-cluster-description.md#upgrade-domains) und fünf [Fehlerdomänen](service-fabric-cluster-resource-manager-cluster-description.md#fault-domains) und kann bis zu 100 virtuelle Computer umfassen.  Cluster mit mehr als 100 Knoten werden erstellt, indem Sie mehrere Skalierungsgruppen/Knotentypen erstellen.

> [!IMPORTANT]
> Das Auswählen der Anzahl von Knotentypen für Ihren Cluster und der Eigenschaften der einzelnen Knotentypen (Größe, primärer Knotentyp, Internetzugriff, Anzahl virtueller Computer usw.) ist eine wichtige Aufgabe.  Weitere Informationen finden Sie unter [Überlegungen zur Kapazitätsplanung für Service Fabric-Cluster](service-fabric-cluster-capacity.md).

Weitere Informationen finden Sie unter [Azure Service Fabric-Knotentypen und VM-Skalierungsgruppen](service-fabric-cluster-nodetypes.md).

### <a name="azure-load-balancer"></a>Azure Load Balancer
VM-Instanzen werden hinter einem [Azure-Lastenausgleich](/azure/load-balancer/load-balancer-overview) verknüpft, der einer [öffentlichen IP-Adresse](/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses) und DNS-Bezeichnung zugeordnet ist.  Wenn Sie einen Cluster mit *&lt;clustername&gt;* bereitstellen, ist der DNS-Name *&lt;clustername&gt;.&lt;speicherort&gt;.cloudapp.azure.com* die DNS-Bezeichnung, die dem Lastenausgleich vor der Skalierungsgruppe zugeordnet ist.

Virtuelle Computer in einem Cluster haben nur [private IP-Adressen](/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses).  Verwaltungsdatenverkehr und Dienstdatenverkehr werden durch den öffentlichen Lastenausgleich weitergeleitet.  Die Weiterleitung von Netzwerkdatenverkehr an diese Computer erfolgt über NAT-Regeln (Clients stellen eine Verbindung mit bestimmten Knoten/Instanzen her) oder Lastenausgleichsregeln (Datenverkehr wird per Roundrobin an virtuelle Computern geleitet).  Einem Lastenausgleich ist eine öffentliche IP-Adresse mit einem DNS-Namen im Format *&lt;clustername&gt;.&lt;speicherort&gt;.cloudapp.azure.com* zugeordnet.  Eine öffentliche IP-Adresse ist eine andere Azure-Ressource in der Ressourcengruppe.  Wenn Sie in einem Cluster mehrere Knotentypen definieren, wird ein Lastenausgleich für jeden Knoten/jede Skalierungsgruppe erstellt. Sie können auch einen einzelnen Lastenausgleich für mehrere Knotentypen einrichten.  Der primäre Knotentyp hat die DNS-Bezeichnung *&lt;clustername&gt;.&lt;speicherort&gt;.cloudapp.azure.com*, andere Knotentypen haben die DNS-Bezeichnung *&lt;clustername&gt;-&lt;knotentyp&gt;.&lt;speicherort&gt;.cloudapp.azure.com*.

### <a name="storage-accounts"></a>Speicherkonten
Jeder Clusterknotentyp wird von einem [Azure Storage-Konto](/azure/storage/common/storage-introduction) und verwalteten Datenträgern unterstützt.

## <a name="cluster-security"></a>Clustersicherheit
Ein Service Fabric-Cluster ist eine Ressource, die sich in Ihrem Besitz befindet.  Sie müssen Ihre Cluster schützen, um zu verhindern, dass nicht autorisierte Benutzer eine Verbindung mit ihnen herstellen. Ein sicherer Cluster ist besonders wichtig, wenn Sie Produktionsworkloads im Cluster ausführen. 

### <a name="node-to-node-security"></a>Knoten-zu-Knoten-Sicherheit
Die Knoten-zu-Knoten-Sicherheit dient zum Schutz der Kommunikation zwischen den virtuellen und physischen Computern in einem Cluster. Dieses Sicherheitsszenario stellt sicher, dass nur Computer, die zum Clusterbeitritt berechtigt sind, Anwendungen und Dienste im Cluster hosten können. Service Fabric verwendet X.509-Zertifikate, um einen Cluster zu sichern und Sicherheitsfunktionen für Anwendungen bereitzustellen.  Zum Schützen des Clusterdatenverkehrs und Bereitstellen von Cluster- und Serverauthentifizierung ist ein Clusterzertifikat erforderlich.  Selbstsignierte Zertifikate können für Testcluster verwendet werden, zum Sichern von Produktionsclustern sollte jedoch ein Zertifikat von einer vertrauenswürdigen Zertifizierungsstelle verwendet werden.

Weitere Informationen finden Sie unter [Knoten-zu-Knoten-Sicherheit](service-fabric-cluster-security.md#node-to-node-security).

### <a name="client-to-node-security"></a>Client-zu-Knoten-Sicherheit
Client-zu-Knoten-Sicherheit authentifiziert Clients und schützt die Kommunikation zwischen einem Client und einzelnen Knoten im Cluster. Diese Art von Sicherheit sorgt dafür, dass nur autorisierte Benutzer auf den Cluster und die im Cluster bereitgestellten Anwendungen zugreifen können. Clients werden über ihre Sicherheitsanmeldeinformationen für X.509-Zertifikate eindeutig identifiziert. Zum Authentifizieren von Administrator- oder Benutzerclients beim Cluster können beliebig viele optionale Clientzertifikate verwendet werden.

Zusätzlich zu Clientzertifikaten kann auch Azure Active Directory für das Authentifizieren von Clients beim Cluster konfiguriert werden.

Weitere Informationen finden Sie unter [Client-zu-Knoten-Sicherheit](service-fabric-cluster-security.md#client-to-node-security).

### <a name="role-based-access-control"></a>Rollenbasierte Access Control
Rollenbasierte Zugriffssteuerung (RBAC) ermöglicht Ihnen das Zuweisen differenzierter Zugriffskontrollen für Azure-Ressourcen.  Sie können Abonnements, Ressourcengruppen und Ressourcen verschiedene Zugriffsregeln zuweisen.  RBAC-Regeln werden zusammen mit der Ressourcenhierarchie vererbt, es sei denn, sie werden auf einer niedrigeren Ebene überschrieben.  Sie können alle Benutzer oder Benutzergruppen in Ihrer AAD-Instanz mit RBAC-Regeln zuweisen, sodass ausgewählte Benutzer und Gruppen Ihren Cluster ändern können.  Weitere Informationen finden Sie in der [Übersicht über Azure RBAC](/azure/role-based-access-control/overview).

Service Fabric unter auch Zugriffssteuerung zum Einschränken des Zugriffs auf bestimmte Clustervorgänge für verschiedene Gruppen von Benutzern. Dadurch wird der Cluster sicherer. Für Clients, die eine Clusterverbindung herstellen, werden zwei Zugriffssteuerungen unterstützt: Administratorrolle und Benutzerrolle.  

Weitere Informationen finden Sie unter [Rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC)](service-fabric-cluster-security.md#role-based-access-control-rbac).

### <a name="network-security-groups"></a>Netzwerksicherheitsgruppen 
Netzwerksicherheitsgruppen (NSGs) steuern ein- und ausgehenden Datenverkehr eines Subnetzes, virtuellen Computers oder einer bestimmten NIC.  Wenn sich mehrere virtuelle Computer im selben virtuellen Netzwerk befinden, können sie standardmäßig über einen beliebigen Port miteinander kommunizieren.  Wenn Sie die Kommunikation zwischen den Computern einschränken möchten, können Sie NSGs zum Segmentieren des Netzwerks oder zum Isolieren der virtuellen Computer voneinander definieren.  Wenn Sie mehrere Knotentypen in einem Cluster haben, können Sie NSGs auf Subnetze anwenden, um zu verhindern, dass Computer, die verschiedenen Knotentypen angehören, miteinander kommunizieren.  

Weitere Informationen finden Sie unter [Sicherheitsgruppen](/azure/virtual-network/security-overview).

## <a name="scaling"></a>Skalieren

Anwendungsanforderungen ändern sich im Laufe der Zeit. Unter Umständen müssen Sie die Clusterressourcen erhöhen, um auf eine höhere Anwendungsworkload oder vermehrten Netzwerkdatenverkehr zu reagieren, oder verringern, wenn die Nachfrage nachlässt. Nach dem Erstellen eines Service Fabric-Clusters können Sie den Cluster horizontal (Änderung der Anzahl von Knoten) oder vertikal (Änderung der Ressourcen von Knoten) skalieren. Sie können die Skalierung für den Cluster jederzeit durchführen – auch bei Ausführung von Workloads im Cluster. Wenn der Cluster skaliert wird, werden Ihre Anwendungen ebenfalls automatisch skaliert.

Weitere Informationen finden Sie unter [Skalieren von Azure Service Fabric-Clustern](service-fabric-cluster-scaling.md).

## <a name="upgrading"></a>Wird aktualisiert
Ein Azure Service Fabric-Cluster ist eine Ressource, die Sie besitzen, die jedoch teilweise von Microsoft verwaltet wird. Microsoft ist für das Patchen des zugrunde liegenden Betriebssystems und das Durchführen von Upgrades der Service Fabric-Runtime auf dem Cluster zuständig. Sie können Ihren Cluster so konfigurieren, dass er Runtime-Upgrades entweder automatisch erhält oder wenn Microsoft eine neue Version veröffentlicht. Alternativ können Sie eine gewünschte unterstützte Runtime-Version auswählen. Zusätzlich zu Runtime-Upgrades können Sie auch die Clusterkonfiguration aktualisieren, z. B. Zertifikate oder Anwendungsports.

Weitere Informationen finden Sie unter [Aktualisieren von Clustern](service-fabric-cluster-upgrade.md).

## <a name="supported-operating-systems"></a>Unterstützte Betriebssysteme
Sie können Cluster auf virtuellen Computern mit diesen Betriebssystemen erstellen:

* Windows Server 2012 R2
* Windows Server 2016 
* Windows Server 1709
* Windows Server 1803
* Linux Ubuntu 16.04
* Red Hat Enterprise Linux 7.4 (Vorschauunterstützung)

> [!NOTE]
> Wenn Sie Service Fabric unter Windows Server 1709 bereitstellen möchten, beachten Sie Folgendes: (1) Es handelt sich nicht um einen Long Term Servicing Branch, daher müssen Versionen in Zukunft unter Umständen verschoben werden. (2) Bei der Bereitstellung von Containern können unter Windows Server 2016 erstellte Container unter Windows Server 1709 nicht verwendet werden und umgekehrt. (Die Container müssen neu erstellt werden, damit sie bereitgestellt werden können.)
>


## <a name="next-steps"></a>Nächste Schritte
Lesen Sie mehr über das [Sichern](service-fabric-cluster-security.md), [Skalieren](service-fabric-cluster-scaling.md) und [Aktualisieren](service-fabric-cluster-upgrade.md) von Azure-Clustern.

Informieren Sie sich über [Service Fabric-Supportoptionen](service-fabric-support.md).

[Image]: media/service-fabric-azure-clusters-overview/Cluster.PNG