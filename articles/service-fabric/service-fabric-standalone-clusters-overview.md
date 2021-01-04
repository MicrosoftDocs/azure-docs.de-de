---
title: Übersicht über eigenständige Service Fabric-Cluster
description: Service Fabric-Cluster werden unter Windows Server und Linux ausgeführt, sodass Service Fabric-Anwendungen überall dort bereitgestellt und gehostet werden können, wo die Ausführung von Windows Server oder Linux möglich ist.
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: a3162a3d02510ce8efab6c5bcac0f1fdd2b2539b
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94683997"
---
# <a name="overview-of-service-fabric-standalone-clusters"></a>Übersicht über eigenständige Service Fabric-Cluster

Ein Service Fabric-Cluster enthält eine per Netzwerk verbundene Gruppe von virtuellen oder physischen Computern, auf denen Ihre Microservices bereitgestellt und verwaltet werden. Ein Computer oder ein virtueller Computer, der Teil eines Clusters ist, wird als Clusterknoten bezeichnet. Cluster können auf Tausende von Knoten skaliert werden. Wenn Sie dem Cluster neue Knoten hinzufügen, verteilt Service Fabric Partitionsreplikate und Instanzen des Diensts auf die erhöhte Anzahl von Knoten neu, um sie auszugleichen. Die Gesamtleistung der Anwendung verbessert sich und Konflikte beim Speicherzugriff werden reduziert. Wenn die Knoten im Cluster nicht effizient genutzt werden, können Sie die Anzahl der Knoten im Cluster verringern. Service Fabric gleicht die Partitionsreplikate und Instanzen erneut über die verringerte Anzahl von Knoten aus, um die Hardware der einzelnen Knoten besser zu nutzen.

Ein Knotentyp definiert die Größe, Anzahl und Eigenschaften einer Gruppe von Knoten im Cluster. Jeden Knotentyp kann dann unabhängig zentral hoch- oder herunterskaliert werden, bei jedem Typ können unterschiedliche Portgruppen geöffnet sein, und die Typen können verschiedene Kapazitätsmetriken aufweisen. Mit Knotentypen werden Rollen für eine Gruppe von Clusterknoten definiert, z.B. „Front-End“ oder „Back-End“. Der Cluster kann über mehrere Knotentypen verfügen, aber der primäre Knotentyp muss bei Clustern, die in Produktionsumgebungen eingesetzt werden, mindestens fünf virtuelle Computer umfassen (für Testcluster sind mindestens drei virtuelle Computer erforderlich). [Service Fabric-Systemdienste](service-fabric-technical-overview.md#system-services) werden auf den Knoten des primären Knotentyps platziert.

Der Prozess zum lokalen Erstellen eines Service Fabric-Clusters ist vergleichbar mit der Erstellung eines Clusters in einer beliebigen Cloud mit einer Gruppe von VMs. Die ersten Schritte zum Bereitstellen der VMs sind abhängig von Ihrem Cloudanbieter bzw. von Ihrer lokalen Umgebung. Sobald Sie aber über eine Gruppe von VMs verfügen, die über ein Netzwerk verbunden sind, sind die Schritte zum Einrichten des Service Fabric-Pakets, Bearbeiten der Clustereinstellungen und Ausführen der Skripts zum Erstellen und Verwalten des Clusters identisch. Dadurch wird sichergestellt, dass Ihre Kompetenz und Erfahrung beim Betreiben und Verwalten von Service Fabric-Clustern bei Wahl einer neuen Hostingumgebung übertragbar sind.

## <a name="cluster-security"></a>Clustersicherheit

Ein Service Fabric-Cluster ist eine Ressource, die sich in Ihrem Besitz befindet.  Sie müssen Ihre Cluster schützen, um zu verhindern, dass nicht autorisierte Benutzer eine Verbindung mit ihnen herstellen. Ein sicherer Cluster ist besonders wichtig, wenn Sie Produktionsworkloads im Cluster ausführen.

> [!NOTE]
> Die Windows-Authentifizierung basiert auf Kerberos. NTLM wird als Authentifizierungstyp nicht unterstützt.
>
> Verwenden Sie nach Möglichkeit die X.509-Zertifikatauthentifizierung für Service Fabric-Cluster.

### <a name="node-to-node-security"></a>Knoten-zu-Knoten-Sicherheit

Die Knoten-zu-Knoten-Sicherheit dient zum Schutz der Kommunikation zwischen den virtuellen und physischen Computern in einem Cluster. Dieses Sicherheitsszenario stellt sicher, dass nur Computer, die zum Clusterbeitritt berechtigt sind, Anwendungen und Dienste im Cluster hosten können. Service Fabric verwendet X.509-Zertifikate, um einen Cluster zu sichern und Sicherheitsfunktionen für Anwendungen bereitzustellen.  Zum Schützen des Clusterdatenverkehrs und Bereitstellen von Cluster- und Serverauthentifizierung ist ein Clusterzertifikat erforderlich.  Selbstsignierte Zertifikate können für Testcluster verwendet werden. Zum Schutz von Produktionsclustern sollte jedoch ein Zertifikat einer vertrauenswürdigen Zertifizierungsstelle verwendet werden.

Für einen eigenständigen Windows-Cluster kann auch die Windows-Sicherheit aktiviert werden. Wenn Sie über Windows Server 2012 R2 und Windows Active Directory verfügen, wird empfohlen, die Windows-Sicherheit mit gruppenverwalteten Dienstkonten zu verwenden. Verwenden Sie andernfalls die Windows-Sicherheit mit Windows-Konten.

Weitere Informationen finden Sie unter [Knoten-zu-Knoten-Sicherheit](service-fabric-cluster-security.md#node-to-node-security).

### <a name="client-to-node-security"></a>Client-zu-Knoten-Sicherheit

Client-zu-Knoten-Sicherheit authentifiziert Clients und schützt die Kommunikation zwischen einem Client und einzelnen Knoten im Cluster. Diese Art von Sicherheit sorgt dafür, dass nur autorisierte Benutzer auf den Cluster und die im Cluster bereitgestellten Anwendungen zugreifen können. Clients werden über ihre Sicherheitsanmeldeinformationen für X.509-Zertifikate eindeutig identifiziert. Zum Authentifizieren von Administrator- oder Benutzerclients beim Cluster können beliebig viele optionale Clientzertifikate verwendet werden.

Zusätzlich zu Clientzertifikaten kann auch Azure Active Directory für das Authentifizieren von Clients beim Cluster konfiguriert werden.

Weitere Informationen finden Sie unter [Client-zu-Knoten-Sicherheit](service-fabric-cluster-security.md#client-to-node-security).

### <a name="service-fabric-role-based-access-control"></a>Rollenbasierte Service Fabric-Zugriffssteuerung
Service Fabric unterstützt auch die Zugriffssteuerung zum Einschränken des Zugriffs auf bestimmte Clustervorgänge für verschiedene Gruppen von Benutzern. Dadurch wird der Cluster sicherer. Für Clients, die eine Clusterverbindung herstellen, werden zwei Zugriffssteuerungen unterstützt: Administratorrolle und Benutzerrolle.  

Weitere Informationen finden Sie unter [Rollenbasierte Service Fabric-Zugriffssteuerung](service-fabric-cluster-security.md#service-fabric-role-based-access-control).

## <a name="scaling"></a>Skalierung

Anwendungsanforderungen ändern sich im Laufe der Zeit. Unter Umständen müssen Sie die Clusterressourcen erhöhen, um auf eine höhere Anwendungsworkload oder vermehrten Netzwerkdatenverkehr zu reagieren, oder verringern, wenn die Nachfrage nachlässt. Nach dem Erstellen eines Service Fabric-Clusters können Sie den Cluster horizontal (Änderung der Anzahl von Knoten) oder vertikal (Änderung der Ressourcen von Knoten) skalieren. Sie können die Skalierung für den Cluster jederzeit durchführen – auch bei Ausführung von Workloads im Cluster. Wenn der Cluster skaliert wird, werden Ihre Anwendungen ebenfalls automatisch skaliert.

Weitere Informationen finden Sie unter [Skalieren eigenständiger Cluster](service-fabric-cluster-scaling-standalone.md).

## <a name="upgrading"></a>Wird aktualisiert

Ein eigenständiger Cluster ist eine Ressource, die Sie vollständig besitzen. Sie sind für das Patchen des zugrunde liegenden Betriebssystems und das Starten von Fabric-Upgrades zuständig. Sie können Ihren Cluster so konfigurieren, dass er Runtime-Upgrades entweder automatisch erhält oder wenn Microsoft eine neue Version veröffentlicht. Alternativ können Sie eine gewünschte unterstützte Runtime-Version auswählen. Zusätzlich zu Fabric-Upgrades können Sie auch das Betriebssystem patchen und die Clusterkonfiguration aktualisieren, z.B. Zertifikate oder Anwendungsports. 

Weitere Informationen finden Sie unter [Aktualisieren eigenständiger Cluster](service-fabric-cluster-upgrade-standalone.md).

## <a name="supported-operating-systems"></a>Unterstützte Betriebssysteme

Sie können Cluster auf VMs oder Computern mit diesen Betriebssystemen erstellen (Linux wird noch nicht unterstützt):

* Windows Server 2012 R2
* Windows Server 2016 
* Windows Server 2019

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über das [Schützen](service-fabric-cluster-security.md), [Skalieren](service-fabric-cluster-scaling-standalone.md) und [Aktualisieren](service-fabric-cluster-upgrade-standalone.md) eigenständiger Cluster.

Informieren Sie sich über [Service Fabric-Supportoptionen](service-fabric-support.md).
