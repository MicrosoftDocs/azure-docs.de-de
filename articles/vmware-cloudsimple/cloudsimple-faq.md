---
title: 'Häufig gestellte Fragen: VMware-Lösung von CloudSimple'
description: Häufig gestellte Fragen (FAQ) zu Azure VMware-Lösung von CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 05/24/19
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e727398e1b7bfa406166574ab40320c68dac5709
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66358052"
---
# <a name="frequently-asked-questions-about-vmware-solution-by-cloudsimple"></a>Häufig gestellte Fragen (FAQ) zu VMware-Lösung von CloudSimple

Häufig gestellte Fragen mit Antworten zu VMware-Lösung von CloudSimple, die Ihnen dabei helfen, den Dienst und seine Verwendungsweise zu verstehen.  Die Fragen und Antworten sind in folgende drei Kategorien unterteilt.

* CloudSimple-Dienst
* Konnektivität
* Netzwerk
* Sicherheit
* Compute
* Storage
* VMware
* Azure-Integration
  
## <a name="cloudsimple-service"></a>CloudSimple-Dienst

**Was ist die VMware-Lösung von CloudSimple?**

Die **VMware-Lösung von CloudSimple** transformiert und erweitert VMware-Workloads in Minuten in private, dedizierte Clouds in Azure. Wir kümmern uns um die Bereitstellung, Verwaltung der Infrastruktur und Orchestrierung der Workloads zwischen lokalen Standorten und Azure. Da Ihre Apps sowohl lokal als auch in Azure exakt gleich ausgeführt werden, profitieren Sie von der Flexibilität und den Diensten der Cloud, ohne die Komplexität eines Neuentwurfs Ihrer App-Architektur. CloudSimple verringert Ihre Gesamtkosten mit einem Cloudverbrauchsmodell, das bedarfsgesteuerte Bereitstellung, nutzungsbasierte Bezahlung und Kapazitätsoptimierung bereitstellt.  Informationen zu Funktionen, Vorteilen und Szenarien finden Sie unter [Was ist die VMware-Lösung in Azure von CloudSimple](cloudsimple-vmware-solutions-overview.md).

**Was ist eine CloudSimple Private Cloud?**

Sie stellen eine private, dedizierte Cloud bereit, bestehend aus einer Hochleistungsumgebung für Compute, Speicher und Netzwerk, die in der Microsoft Azure-Infrastruktur (Hardware und Datencenterplatz) an Azure-Standorten bereitgestellt wird.  Private Cloud bietet eine native VMware-Plattform als Dienst („Software-as-a-Service“). In VMware-Begriffen ausgedrückt enthält jede Private Cloud genau eine Instanz von vCenter Server. vCenter Server verwaltet mehrere ESXi-Knoten, die sich in einem oder mehreren vSphere-Clustern befinden, zusammen mit dem entsprechenden virtuellen SAN-Speicher (vSAN). Ein CloudSimple-Dienst kann mehrere Private Clouds in Ihrem Azure-Abonnement enthalten.  Weitere Informationen zu Private Clouds finden Sie unter [Übersicht über die Private Cloud](cloudsimple-private-cloud.md).

**Wo ist der CloudSimple-Dienst verfügbar?**

CloudSimple ist in den Regionen „USA, Osten“ und „USA, Westen“ verfügbar.

**Wie aktiviere ich mein Abonnement für CloudSimple?**

Sie können Ihren Microsoft-Kontobeauftragten unter [azurevmwaresales@microsoft.com](mailto:azurevmwaresales@microsoft.com) kontaktieren, um Ihr Abonnement für den CloudSimple-Dienst zu aktivieren. Geben Sie in der E-Mail Ihre Abonnement-ID an, für die der CloudSimple-Dienst aktiviert werden soll.  

**Wie greife ich auf das CloudSimple-Portal zu?**

Sie greifen aus dem Azure-Portal auf das CloudSimple-Portal zu.  Details zum Zugriff auf das CloudSimple-Portal finden Sie in dem Artikel [Zugriff auf die VMware-Lösung über das CloudSimple-Portal aus dem Azure-Portal heraus](https://docs.azure.cloudsimple.com/access-cloudsimple-portal). 

**Wie erhöhe ich die Kapazität für eine Private Cloud?**

Sie kaufen Knoten im Azure-Portal und erweitern Ihre Private Cloud aus dem CloudSimple-Portal heraus.  Sie können Ihre Private Cloud erweitern, indem Sie zusätzliche Knoten zum vorhandenen vSphere-Cluster hinzufügen, oder indem Sie neue vSphere-Cluster erstellen.  Die Vorgehensweise hierzu finden Sie in dem Artikel [Erweitern einer CloudSimple Private Cloud](https://docs.azure.cloudsimple.com/expand-private-cloud).

**Was geschieht mit meiner Private Cloud während der Wartung?**

CloudSimple sendet regelmäßige Benachrichtigungen Tage vor einer geplanten Wartung.  Die Wartung erfolgt in einer unterbrechungsfreien Weise, um die Verfügbarkeit Ihrer Private Cloud sicherzustellen.  Die Wartung kann von einem der folgenden Typen sein:

1. **CloudSimple-Infrastruktur:**  Die CloudSimple-Infrastruktur ist auf Hochverfügbarkeit ausgelegt.  Während der Wartung werden Konnektivität und Verfügbarkeit Ihrer Private Cloud sichergestellt, indem redundante Komponenten immer einzeln ohne Beeinträchtigung aktualisiert werden.  Sie haben Zugriff auf Private Cloud vCenter, alle virtuellen Computer, die Internetverbindung aus Ihrer Private Cloud und die Verbindungen mit lokalen Standorten oder Azure.
2. **CloudSimple-Portal:** Während der Wartung sind einige Funktionen im CloudSimple-Portal möglicherweise nicht erreichbar oder deaktiviert.  Die Wartungsbenachrichtigung enthält die Details dazu, welche Aktionen im Portal durchgeführt werden können.

## <a name="connectivity"></a>Konnektivität

**Welche Optionen habe ich bei der Konnektivität mit dem CloudSimple-Regionsnetzwerk?**

CloudSimple stellt drei verschiedene Konnektivitätsoptionen für die Verbindung mit Ihrem CloudSimple-Regionsnetzwerk bereit.  Alle drei können zusammen verwendet werden.

1. Eine ExpressRoute-Verbindung von Ihrem lokalen Datencenter mit dem CloudSimple-Regionsnetzwerk: eine sichere private Verbindung mit hoher Geschwindigkeit und geringer Latenz, die Ihre lokale ExpressRoute-Leitung mit Ihrer CloudSimple ExpressRoute-Leitung mittels Global Reach verbindet. Informationen zum Einrichten der Verbindung finden Sie in dem Artikel [Herstellen einer Verbindung von lokalen Standorten mit CloudSimple mithilfe von ExpressRoute](https://docs.azure.cloudsimple.com/on-premises-connection).
2. Eine ExpressRoute-Verbindung von Ihrem virtuellen Azure-Netzwerk mit Ihrem CloudSimple-Regionsnetzwerk: eine sichere private Verbindung mit hoher Geschwindigkeit und geringer Latenz, die Ihr virtuelles Netzwerk in Azure mit Ihrer CloudSimple ExpressRoute-Leitung mittels virtueller Netzwerkgateways verbindet.  Informationen zum Einrichten der Verbindung finden Sie in dem Artikel [Herstellen einer Verbindung aus Ihrer CloudSimple Private Cloud-Umgebung mit dem virtuellen Azure-Netzwerk mithilfe von ExpressRoute](https://docs.azure.cloudsimple.com/azure-expressroute-connection).
3. Eine Site-to-Site-VPN-Verbindung von Ihrem lokalen Datencenter mit Ihrem CloudSimple-Regionsnetzwerk: ein sichere private Netzwerkverbindung von Ihrem lokalen VPN-Gerät mit Ihrer CloudSimple Private Cloud-Region.  Informationen zum Einrichten einer VPN-Verbindung finden Sie in dem Artikel [Einrichten einer VPN-Verbindung zwischen Ihrem lokalen Netzwerk und der CloudSimple Private Cloud].

**Wie stelle ich eine Verbindung mit einer Private Cloud her?**

Details Ihrer Private Cloud können Sie im CloudSimple-Portal anzeigen. Zum Verbinden mit dem vCenter, das Ihrer Private Cloud entspricht, stellen Sie sicher, dass die Netzwerkverbindung mittels Site-to-Site, Point-to-Site oder ExpressRoute hergestellt wurde. Starten Sie dann das CloudSimple-Portal aus dem Azure-Portal heraus, und klicken Sie auf der Startseite oder auf der Seite mit den Details der Private Cloud auf *vSphere-Client starten*.

**Worin besteht der Vorteil einer ExpressRoute-Leitung?**

Eine Azure ExpressRoute-Leitung bietet eine sichere Verbindung mit hoher Geschwindigkeit und niedriger Latenz.  CloudSimple bietet eine dedizierte ExpressRoute-Leitung pro Region pro Kunde.  Durch die Verwendung dieser Leitung können Sie eine sichere Verbindung von einem lokalen Standort und/oder Ihrem Azure-Abonnement aus herstellen.

**Welche Netzwerkkosten fallen für die Verbindung mit/von CloudSimple an? Gibt es Gebühren für ausgehenden Datenverkehr zwischen CloudSimple und Azure in beiden Richtungen? Regionsüberschreitend?**

Es gibt keine Gebühren für ausgehenden Netzwerkdatenverkehr.  Für ausgehenden Datenverkehr aus Ihrem virtuellen Netzwerk oder von der lokalen ExpressRoute-Leitung gelten die Standardtarife von Azure.

## <a name="networking"></a>Netzwerk

**Welche Netzwerkfunktionen sind für meine Private Cloud verfügbar?**

Sie können VLANs (und deren Subnetze) sowie Firewalltabellen bereitstellen als auch öffentliche IP-Adressen zuweisen und einem virtuellen Computer zuordnen, der in Ihrer Private Cloud ausgeführt wird.  Weitere Informationen finden Sie in den Artikeln [VLANs/Subnetze (Übersicht)](cloudsimple-vlans-subnets.md), [Firewalltabellen (Übersicht)](cloudsimple-firewall-tables.md) und [Öffentliche IP-Adressen (Übersicht)](cloudsimple-public-ip-address.md).

**Wie richte ich verschiedene Subnetze für meine Anwendungen in meiner Private Cloud ein?**

Sie können VLANs in Ihrer privaten Cloud aus dem CloudSimple Portal heraus erstellen.  Nachdem Sie das VLAN erstellt haben, können Sie eine verteilte Portgruppe mithilfe des VLANs in Ihrem Private Cloud vCenter erstellen und virtuelle Computer erstellen, die mit der verteilten Portgruppe verbunden sind.  Sie können eine Firewalltabelle für das VLAN/Subnetz aktivieren und Firewallregeln definieren, um den Netzwerkdatenverkehr abzusichern.

**Welche Firewalleinstellungen sind für meine Private Clouds verfügbar?**

Sie können Regeln für den Datenverkehr in Nord-Süd- und in Ost-West-Richtung konfigurieren.  Die Regeln werden in einer Firewalltabelle definiert.  Die Firewalltabelle kann VLAN(s) in Ihrer Private Cloud zugeordnet werden.  Das Verfahren zum Einrichten finden Sie in dem Artikel [Einrichten von Firewalltabellen und Regeln für Private Clouds](https://docs.azure.cloudsimple.com/firewall).

**Kann ich öffentliche IP-Adressen für virtuelle Computer in meiner Private Cloud-Umgebung zuweisen?**

Im CloudSimple-Portal können Sie ganz einfach eine neue öffentliche IP-Adresse reservieren und sie einer privaten IP-Adresse Ihres virtuellen Computers oder einer Appliance zuordnen.  Sie können auch neue Firewallregeln erstellen oder vorhandene Firewallregeln anwenden, um Datenverkehr von bestimmten Ports und/oder einem bestimmten Satz von IP-Adressen im Portal zuzulassen. Das Verfahren zum Einrichten finden Sie unter [Zuordnen von öffentlichen IP-Adressen für die Private Cloud-Umgebung](https://docs.azure.cloudsimple.com/public-ips).

## <a name="security"></a>Sicherheit

**Welche Sicherheitsoptionen habe ich bei CloudSimple?**

CloudSimple Private Cloud bietet die folgenden Sicherheitsfunktionen zur Absicherung Ihrer Private Cloud-Umgebung:

1. **Verschlüsselung für ruhende Daten**: Sie können ruhende Daten, die sich auf dem vSAN-Speicher in Ihrer Private Cloud befinden, verschlüsseln. vSAN unterstützt externe Schlüsselverwaltungsserver (KMS), die in Ihrer Azure vNet- oder der lokalen Umgebung bereitgestellt werden können.  Weitere Details finden Sie unter [Konfigurieren der vSAN-Verschlüsselung für Ihre CloudSimple Private Cloud](https://docs.azure.cloudsimple.com/vsan-encryption).
2. **Netzwerksicherheit**: Sie können den Netzwerkdatenverkehr aus/in Ihre Private Cloud aus dem Internet, von lokalen Standorten und innerhalb von Subnetzen Ihrer Private Cloud mithilfe von Firewallregeln kontrollieren.
3. **Sichere, private Verbindung**: Eine sichere, private Verbindung zwischen Ihrem lokalen Netzwerk und Ihrem Azure-Abonnement.

## <a name="compute"></a>Compute

**Welche Arten von Hosts sind verfügbar?**

CloudSimple bietet zwei Hosttypen:

* **CS28-Knoten:** CPU: 2 x 2,2 GHz, 28 Kerne insgesamt, 48 HT.  RAM: 256 GB.  Speicher: 1600 GB NVMe-Cache, 5760 GB Data (All-Flash). Netzwerk: 2 x 25 GbE NIC
* **CS36-Knoten:** CPU: 2 x 2,3 GHz, 36 Kerne insgesamt, 72 HT.  RAM: 512 GB.  Speicher: 3200 GB NVMe-Cache, 11.520 GB Data (All-Flash).  Netzwerk: 2 x 25 GbE NIC

**Wie werden Hardwarefehler behandelt?**

Die gesamte CloudSimple-Infrastruktur wird fortwährend von der CloudSimple-Plattform und unseren Service Operations-Teams überwacht.  Wenn ein Hardwarefehler erkannt wird, wird Ihrer Private Cloud ein neuer Knoten hinzugefügt, und der fehlerhafte Knoten wird entfernt, um die Hochverfügbarkeit Ihrer Private Cloud sicherzustellen.

## <a name="storage"></a>Storage

**Welche Arten von Speicher werden in einer Private Cloud unterstützt?**

CloudSimple bietet **All-Flash VMware vSAN-Speicher** mit jeder Private Cloud.  Jeder vSphere wird mit seinem eigenen vSAN-Datenspeicher erstellt.  Weitere Details finden Sie in dem Artikel [VMware-Komponenten der Private Cloud – vSAN-Speicher](https://docs.azure.cloudsimple.com/vmware-components/#vsan-storage).

**Wird die Verschlüsselung von Daten unterstützt?**
Ja.  Sie können den vSAN-Speicher in Ihrer Private Cloud so einrichten, dass er einen KMS (Key Management Server) verwendet, der lokal oder in Azure für die Verschlüsselung im vSAN gespeicherter Daten bereitgestellt wird.

**Wie werden fehlerhafte Datenträger behandelt?**

Die CloudSimple-Überwachung überwacht kontinuierlich alle Hardwarekomponenten der Private Cloud.  Wenn Datenträgerfehler erkannt werden oder ein Datenträger als fehlerhaft identifiziert wird (basierend auf Heuristiken), wird der Private Cloud automatisch ein neuer Knoten hinzugefügt.  Der Knoten mit dem fehlerhaften oder beschädigten Datenträger wird aus der Private Cloud entfernt.

## <a name="vmware"></a>VMware

**Wie führe ich umfangreiche Upload-/Migrationsvorgänge mit Anwendungen und Daten aus einem lokalen Standort durch?**

CloudSimple stellt eine native VMware vSphere-Lösung bereit.  Jedes Tool, das für die Migration massenhafter Daten verwendet werden kann, kann mit CloudSimple Private Cloud verwendet werden.  Einige der verfügbaren Optionen sind:

1. VMware HCX für die Massenmigration von Daten.
2. „Kalte“ Migration von Daten mithilfe von Storage vMotion aus einem lokalen Standort zu CloudSimple.

**Kann ich alle VMware-Tools installieren?**

CloudSimple stellt eine native VMware vSphere-Lösung bereit.  Jedes Tool, das zum Verwalten einer lokalen vSphere-Umgebung verwendet wird, kann mit CloudSimple verwendet werden.  CloudSimple unterstützt das BYOL-Modell (Bring-Your-Own-License) für die Installation von VMware-Tools.

**Wie werden Updated und Upgrades verwaltet?**

CloudSimple verwaltet und aktualisiert alle Komponenten der Infrastruktur Ihrer Private Cloud auf nahtlose und unterbrechungsfreie Art.  Alle von VMware oder dem Hersteller der Infrastruktur freigegebenen Updates oder Sicherheitspatches, werden zur Aktualisierung eingeplant, sobald sie von CloudSimple als qualifiziert eingestuft wurden.

CloudSimple führt keine Upgrades oder Updates für installierte Anwendungen in der Private Cloud durch.

## <a name="azure-integration"></a>Azure-Integration

**Welche Azure-Dienste werden unterstützt?**

CloudSimple stellt eine Azure ExpressRoute-Leitung für Ihr Abonnement in Azure bereit.  Alle Dienste, die in Ihrem Abonnement ausgeführt werden, besitzen Netzwerkkonnektivität mit Ihrer Private Cloud und können eine Verbindung mit Ihrer Private Cloud herstellen.  Beispiele:

1. **Azure Active Directory** als Identitätsanbieter für Ihr CloudSimple vCenter.
2. **Azure-Speicher** zum Speichern von Sicherungen, Images und andere Daten aus Ihrer Private Cloud
3. **Hybridanwendungen**: Sie können eine Anwendungsarchitektur erstellen, die öffentliche und private Clouds umfasst.  Beispielsweise können Sie Webserver in Azure erstellen, die auf Anwendungs- und Datenbankserver in der CloudSimple Private Cloud zugreifen.
4. **Azure Monitor** und **Azure SecurityCenter**: Workloads, die unter VMware ausgeführt werden können diese für Protokollierung, Leistungsmetriken und Sicherheitsverwaltung verwenden.

**Wie ordne ich meine VMware-Mandanten in Azure zu?**

CloudSimple bietet die einzigartige Funktion zur Verwaltung Ihrer virtuellen VMware-Computer in der Private Cloud über das Azure-Portal.  Ein vCenter-Ressourcenpool (konfiguriert mit den gewünschten Ressourceneinschränkungen) kann vom globalen Administrator Ihrem Abonnement zugeordnet werden.  

**Welche Lizenzierungsvorteile erhalte ich mit Azure?**

Mit CloudSimple können Sie vom Azure-Hybridnutzungsvorteil profitieren und bis zu 90 % bei Lizenzen sparen, so Ihre Investition in Microsoft-Lizenzen erhalten und gleichzeitig Ihre Gesamtkosten im Vergleich zu anderen Clouds senken. Darüber hinaus erhalten Sie verlängerte Sicherheitsupdates für Windows Server 2008 und Microsoft SQL Server 2008.  Halten Sie Ihre Kosten niedrig mit BYOL (Bring Your Own Licenses) in der Cloud für allgemeine Apps wie Veeam, Zerto und andere.  