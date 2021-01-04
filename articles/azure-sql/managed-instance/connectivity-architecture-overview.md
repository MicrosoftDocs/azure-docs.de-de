---
title: Verbindungsarchitektur
titleSuffix: Azure SQL Managed Instance
description: Erfahren Sie etwas über die Kommunikations- und Konnektivitätsarchitektur für Azure SQL Managed Instance sowie die Übertragung des Datenverkehrs an eine verwaltete Instanz mithilfe der Komponenten.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: fasttrack-edit
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova
ms.date: 10/22/2020
ms.openlocfilehash: e67376e2ef79f9711f54ce54d0d91623593ca8ea
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96853287"
---
# <a name="connectivity-architecture-for-azure-sql-managed-instance"></a>Konnektivitätsarchitektur für Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

In diesem Artikel wird die Kommunikation in Azure SQL Managed Instance erläutert. Darüber hinaus wird die Konnektivitätsarchitektur beschrieben, und es wird erläutert, wie die Komponenten Datenverkehr an eine verwaltete Instanz weiterleiten.  

SQL Managed Instance wird im virtuellen Azure-Netzwerk und dem für verwaltete Instanzen dedizierten Subnetz platziert. Diese Bereitstellung bietet:

- Eine sichere private IP-Adresse.
- Die Möglichkeit, eine Verbindung von einem lokalen Netzwerk mit SQL Managed Instance herzustellen.
- Die Möglichkeit, eine Verbindung von SQL Managed Instance mit einem Verbindungsserver oder einem anderen lokalen Datenspeicher herzustellen.
- Die Möglichkeit, eine Verbindung von SQL Managed Instance mit Azure-Ressourcen herzustellen.

## <a name="communication-overview"></a>Kommunikationsübersicht

Die folgende Abbildung zeigt Entitäten, die eine Verbindung mit SQL Managed Instance herstellen. Sie zeigt auch die Ressourcen, die mit einer verwalteten Instanz kommunizieren müssen. Der Kommunikationsvorgang im unteren Diagrammbereich bezieht sich auf Kundenanwendungen und Tools, die eine Verbindung mit SQL Managed Instance als Datenquelle herstellen.  

![Entitäten in der Konnektivitätsarchitektur](./media/connectivity-architecture-overview/connectivityarch001.png)

SQL Managed Instance ist ein PaaS-Angebot (Platform as a Service). Azure verwendet automatisierte Agents (Verwaltung, Bereitstellung und Wartung) zum Verwalten dieses Diensts basierend auf Telemetriedatenströmen. Da Azure für die Verwaltung zuständig ist, können Kunden nicht über Remote Desktop Protocol (RDP) auf die virtuellen Clustercomputer von SQL Managed Instance zugreifen.

Bei einigen Vorgängen, die von Endbenutzern oder Anwendungen gestartet werden, muss SQL Managed Instance möglicherweise mit der Plattform interagieren. Ein solcher Fall ist die Erstellung einer SQL Managed Instance-Datenbank. Diese Ressource wird über das Azure-Portal, PowerShell, Azure-Befehlszeilenschnittstelle und REST-API verfügbar gemacht.

SQL Managed Instance hängt von Azure-Diensten wie Azure Storage für Sicherungen, Azure Event Hubs für Telemetriedaten, Azure Active Directory (Azure AD) für die Authentifizierung, Azure Key Vault für Transparent Data Encryption (TDE) und einer Reihe von Azure-Plattformdiensten ab, die Sicherheits- und Supportfunktionen bereitstellen. SQL Managed Instance stellt Verbindungen mit diesen Diensten her.

Jeglicher Datenaustausch ist verschlüsselt und mithilfe von Zertifikaten signiert. Um die Vertrauenswürdigkeit der Kommunikationspartner sicherzustellen, überprüft SQL Managed Instance diese Zertifikate regelmäßig anhand von Zertifikatsperrlisten. Wenn die Zertifikate widerrufen werden, trennt SQL Managed Instance die Verbindungen zum Schutz der Daten.

## <a name="high-level-connectivity-architecture"></a>Verbindungsarchitektur auf Makroebene

Auf hoher Ebene ist SQL Managed Instance eine Gruppe von Dienstkomponenten. Diese Komponenten werden auf einer dedizierten Gruppe isolierter virtueller Computer gehostet, die im virtuellen Subnetz des Kunden ausgeführt werden. Diese Computer bilden einen virtuellen Cluster.

Ein virtueller Cluster kann mehrere verwaltete Instanzen hosten. Der Cluster wird ggf. automatisch vergrößert oder verkleinert, wenn der Kunde die Anzahl der bereitgestellten Instanzen im Subnetz ändert.

Kundenanwendungen können eine Verbindung mit SQL Managed Instance herstellen und Datenbanken innerhalb des virtuellen Netzwerks, des mittels Peering verknüpften virtuellen Netzwerks oder des durch VPN oder Azure ExpressRoute verbundenen Netzwerks abfragen und aktualisieren. Dieses Netzwerk muss einen Endpunkt und eine private IP-Adresse verwenden.  

![Diagramm zur Konnektivitätsarchitektur](./media/connectivity-architecture-overview/connectivityarch002.png)

Die Verwaltungs- und Bereitstellungsdienste von Azure werden außerhalb des virtuellen Netzwerks ausgeführt. SQL Managed Instance und Azure-Dienste sind über die Endpunkte verbunden, die öffentliche IP-Adressen besitzen. Wenn SQL Managed Instance eine ausgehende Verbindung herstellt, sieht die Verbindung auf der Empfängerseite aufgrund der Netzwerkadressenübersetzung (Network Address Translation, NAT) so aus, als käme sie von dieser öffentlichen IP-Adresse.

Der Verwaltungsdatenverkehr wird über das virtuelle Kundennetzwerk übertragen. Das bedeutet, dass die Elemente der Infrastruktur des virtuellen Netzwerks den Verwaltungsdatenverkehr beeinträchtigen können, indem sie einen Fehler bei der Instanz verursachen, sodass sie nicht mehr verfügbar ist.

> [!IMPORTANT]
> Azure nutzt zur Verbesserung der Dienstverfügbarkeit und -qualität Netzwerkzielrichtlinien für Infrastrukturelemente virtueller Azure-Netzwerke. Die Richtlinie kann Auswirkungen auf die Funktionsweise von SQL Managed Instance haben. Dieser Plattformmechanismus kommuniziert transparent Netzwerkanforderungen für Benutzer. Das Hauptziel der Richtlinie ist, Fehlkonfigurationen des Netzwerks zu verhindern und den normalen Betrieb von SQL Managed Instance zu gewährleisten. Wenn Sie eine verwaltete Instanz löschen, wird die Netzwerkzielrichtlinie ebenfalls entfernt.

## <a name="virtual-cluster-connectivity-architecture"></a>Verbindungsarchitektur für virtuellen Cluster

Im Folgenden wird die Konnektivitätsarchitektur für SQL Managed Instance noch genauer untersucht. Im unten abgebildeten Diagramm wird das konzeptionelle Layout des virtuellen Clusters dargestellt.

![Konnektivitätsarchitektur des virtuellen Clusters](./media/connectivity-architecture-overview/connectivityarch003.png)

Clients stellen mithilfe des Hostnamens, der in der Form `<mi_name>.<dns_zone>.database.windows.net` vorliegen muss, eine Verbindung mit SQL Managed Instance her. Dieser Hostname wird in eine private IP-Adresse aufgelöst, obwohl er in einer öffentlichen DNS-Zone (Domain Name System) registriert ist und öffentlich aufgelöst werden kann. Die `zone-id` wird beim Erstellen des Clusters automatisch generiert. Wenn in einem neu erstellten Cluster eine sekundäre verwaltete Instanz gehostet wird, verwendet diese die Zonen-ID gemeinsam mit dem primären Cluster. Weitere Informationen finden Sie unter [Verwenden von Autofailover-Gruppen für ein transparentes und koordiniertes Failover mehrerer Datenbanken](../database/auto-failover-group-overview.md#enabling-geo-replication-between-managed-instances-and-their-vnets).

Diese private IP-Adresse gehört zum internen Lastenausgleich von SQL Managed Instance. Der Lastenausgleich leitet Datenverkehr an das SQL Managed Instance-Gateway weiter. Da mehrere verwaltete Instanzen in demselben Cluster ausgeführt werden können, nutzt das Gateway den Hostnamen von SQL Managed Instance, um den Datenverkehr an den richtigen SQL-Engine-Dienst weiterzuleiten.

Verwaltungs- und Bereitstellungsdienste stellen über einen [Verwaltungsendpunkt](#management-endpoint), der einem externen Lastenausgleich zugeordnet ist, eine Verbindung mit SQL Managed Instance her. Der Datenverkehr wird nur an die Knoten weitergeleitet, wenn er an einer vordefinierten Gruppe von Ports empfangen wird, die ausschließlich die Verwaltungskomponenten von SQL Managed Instance verwenden. Eine integrierte Firewall auf den Knoten ist so eingerichtet, dass nur Datenverkehr von Microsoft-IP-Adressbereichen zulässig ist. Zertifikate authentifizieren gegenseitig die gesamte Kommunikation zwischen den Verwaltungskomponenten und der Verwaltungsebene.

## <a name="management-endpoint"></a>Verwaltungsendpunkt

Azure verwaltet SQL Managed Instance über einen Verwaltungsendpunkt. Dieser Endpunkt befindet sich in einem virtuellen Cluster der Instanz. Der Endpunkt für die Verwaltung wird durch eine integrierte Firewall auf Netzwerkebene geschützt. Auf der Anwendungsebene wird er durch gegenseitige Zertifikatüberprüfung geschützt. Die IP-Adresse des Endpunkts finden Sie unter [Ermitteln der IP-Adresse des Verwaltungsendpunkts](management-endpoint-find-ip-address.md).

Wenn Verbindungen ihren Ausgangspunkt in SQL Managed Instance haben (wie bei Sicherungen und Überwachungsprotokollen), scheint der Datenverkehr bei der öffentlichen IP-Adresse des Verwaltungsendpunkts zu beginnen. Sie können den Zugriff von SQL Managed Instance auf öffentliche Dienste einschränken, indem Sie Firewallregeln festlegen, die nur die IP-Adresse für SQL Managed Instance zulassen. Weitere Informationen finden Sie unter [Überprüfen der integrierten Firewall von SQL Managed Instance](management-endpoint-verify-built-in-firewall.md).

> [!NOTE]
> Datenverkehr, der an Azure-Dienste innerhalb der Region von SQL Managed Instance gerichtet ist, wird optimiert und aus diesem Grund nicht über NAT an die öffentliche IP-Adresse des Verwaltungsendpunkts übermittelt. Wenn Sie IP-basierte Firewallregeln einsetzen müssen, meistens ist das bei Speicher der Fall, muss sich der Dienst daher in einer anderen Region als SQL Managed Instance befinden.

## <a name="service-aided-subnet-configuration"></a>Dienstgestützte Subnetzkonfiguration

Um Kundensicherheit und Verwaltbarkeitsanforderungen zu berücksichtigen, wird SQL Managed Instance gerade von der manuellen auf die dienstgestützte Subnetzkonfiguration umgestellt.

Mit dienstgestützter Subnetzkonfiguration kann der Benutzer den Datenverkehr (Tabular Data Stream, TDS) vollständig kontrollieren, während SQL Managed Instance die Verantwortung übernimmt, den ununterbrochenen Fluss des Verwaltungsdatenverkehrs sicherzustellen und so eine Vereinbarung zum Servicelevel (Service Level Agreement, SLA) zu erfüllen.

Die dienstgestützte Subnetzkonfiguration baut auf dem Feature [Subnetzdelegierung](../../virtual-network/subnet-delegation-overview.md) für virtuelle Netzwerke auf, um eine automatische Verwaltung der Netzwerkkonfiguration zu ermöglichen und Dienstendpunkte zu aktivieren. 

Dienstendpunkte können zum Konfigurieren von Firewallregeln für virtuelle Netzwerke für Speicherkonten verwendet werden, in denen Sicherungen und Überwachungsprotokolle gespeichert werden. Selbst bei aktivierten Dienstendpunkten wird Kunden empfohlen, [private Links](../../private-link/private-link-overview.md) zu verwenden, die zusätzliche Sicherheit über Dienstendpunkte bieten.

> [!IMPORTANT]
> Aufgrund der Besonderheiten der Konfiguration der Steuerungsebene würde eine dienstgestützte Subnetzkonfiguration keine Dienstendpunkte in nationalen Clouddiensten ermöglichen. 

### <a name="network-requirements"></a>Netzwerkanforderungen

Stellen Sie SQL Managed Instance in einem dedizierten Subnetz im virtuellen Netzwerk bereit. Das Subnetz muss diese Merkmale aufweisen:

- **Dediziertes Subnetz**: Das Subnetz von SQL Managed Instance darf keinen anderen Clouddienst enthalten, der mit ihm verknüpft ist, und es darf kein Gatewaysubnetz sein. Das Subnetz darf keine Ressourcen außer SQL Managed Instance enthalten, und Sie können später keine anderen Ressourcentypen im Subnetz hinzufügen.
- **Subnetzdelegierung:** Das Subnetz von SQL Managed Instance muss an den Ressourcenanbieter `Microsoft.Sql/managedInstances` delegiert werden.
- **Netzwerksicherheitsgruppe (NSG)** : Eine NSG muss dem Subnetz von SQL Managed Instance zugeordnet werden. Sie können eine NSG verwenden, um den Zugriff auf den Datenendpunkt von SQL Managed Instance zu steuern, indem Sie Datenverkehr an Port 1433 und die Ports 11000 bis 11999 filtern, wenn SQL Managed Instance für Verbindungsumleitungen konfiguriert ist. Der Dienst stellt automatisch die [Regeln](#mandatory-inbound-security-rules-with-service-aided-subnet-configuration) zur Verfügung, die erforderlich sind, um einen ununterbrochenen Fluss des Verwaltungsdatenverkehrs zu ermöglichen, und hält sie auf dem neuesten Stand.
- **Benutzerdefinierte Routingtabelle (User Defined Route, UDR):** Eine UDR-Tabelle muss dem Subnetz von SQL Managed Instance zugeordnet werden. Sie können der Routingtabelle Einträge hinzufügen, um Datenverkehr mit lokalen privaten IP-Bereichen als Ziel über das virtuelle Netzwerkgateway oder das virtuelle Netzwerkgerät (Network Appliance, NVA) zu leiten. Der Dienst stellt automatisch die [Einträge](#user-defined-routes-with-service-aided-subnet-configuration) zur Verfügung, die erforderlich sind, um einen ununterbrochenen Fluss des Verwaltungsdatenverkehrs zu ermöglichen, und hält sie auf dem neuesten Stand.
- **Ausreichende IP-Adressen**: Das Subnetz von SQL Managed Instance muss mindestens 32 IP-Adressen umfassen. Weitere Informationen finden Sie unter [Ermitteln der Größe des Subnetzes für SQL Managed Instance](vnet-subnet-determine-size.md). Sie können verwaltete Instanzen im [vorhandenen Netzwerk](vnet-existing-add-subnet.md) bereitstellen, nachdem Sie dieses entsprechend den [Netzwerkanforderungen für SQL Managed Instance](#network-requirements) konfiguriert haben. Erstellen Sie andernfalls ein [neues Netzwerk und Subnetz](virtual-network-subnet-create-arm-template.md).

> [!IMPORTANT]
> Wenn Sie eine verwaltete Instanz erstellen, wird eine Netzwerkzielrichtlinie auf das Subnetz angewendet, um nicht konforme Änderungen am Netzwerksetup zu verhindern. Nachdem die letzte Instanz aus dem Subnetz entfernt wurde, wird auch die Netzwerkzielrichtlinie entfernt. Die folgenden Regeln dienen nur zu Informationszwecken und sollten nicht mit ARM-Vorlagen, PowerShell oder der Befehlszeilenschnittstelle bereitgestellt werden. Wenn Sie die neueste offizielle Vorlage verwenden möchten, können Sie diese jederzeit [aus dem Portal abrufen](https://docs.microsoft.com/azure/azure-resource-manager/templates/quickstart-create-templates-use-the-portal).

### <a name="mandatory-inbound-security-rules-with-service-aided-subnet-configuration"></a>Obligatorische Eingangssicherheitsregeln mit dienstgestützter Subnetzkonfiguration

| Name       |Port                        |Protocol|`Source`           |Destination|Aktion|
|------------|----------------------------|--------|-----------------|-----------|------|
|management  |9000, 9003, 1438, 1440, 1452|TCP     |SqlManagement    |MI-SUBNETZ  |Allow |
|            |9000, 9003                  |TCP     |CorpnetSaw       |MI-SUBNETZ  |Allow |
|            |9000, 9003                  |TCP     |CorpnetPublic    |MI-SUBNETZ  |Allow |
|mi_subnet   |Any                         |Any     |MI-SUBNETZ        |MI-SUBNETZ  |Allow |
|health_probe|Any                         |Any     |AzureLoadBalancer|MI-SUBNETZ  |Allow |

### <a name="mandatory-outbound-security-rules-with-service-aided-subnet-configuration"></a>Obligatorische Ausgangssicherheitsregeln mit dienstgestützter Subnetzkonfiguration

| Name       |Port          |Protocol|`Source`           |Destination|Aktion|
|------------|--------------|--------|-----------------|-----------|------|
|management  |443, 12000    |TCP     |MI-SUBNETZ        |AzureCloud |Allow |
|mi_subnet   |Any           |Any     |MI-SUBNETZ        |MI-SUBNETZ  |Allow |

### <a name="user-defined-routes-with-service-aided-subnet-configuration"></a>Benutzerdefinierte Routen mit dienstgestützter Subnetzkonfiguration

|Name|Adresspräfix|Nächster Hop|
|----|--------------|-------|
|subnet-to-vnetlocal|MI-SUBNETZ|Virtuelles Netzwerk|
|mi-13-64-11-nexthop-internet|13.64.0.0/11|Internet|
|mi-13-104-14-nexthop-internet|13.104.0.0/14|Internet|
|mi-20-33-16-nexthop-internet|20.33.0.0/16|Internet|
|mi-20-34-15-nexthop-internet|20.34.0.0/15|Internet|
|mi-20-36-14-nexthop-internet|20.36.0.0/14|Internet|
|mi-20-40-13-nexthop-internet|20.40.0.0/13|Internet|
|mi-20-48-12-nexthop-internet|20.48.0.0/12|Internet|
|mi-20-64-10-nexthop-internet|20.64.0.0/10|Internet|
|mi-20-128-16-nexthop-internet|20.128.0.0/16|Internet|
|mi-20-135-16-nexthop-internet|20.135.0.0/16|Internet|
|mi-20-136-16-nexthop-internet|20.136.0.0/16|Internet|
|mi-20-140-15-nexthop-internet|20.140.0.0/15|Internet|
|mi-20-143-16-nexthop-internet|20.143.0.0/16|Internet|
|mi-20-144-14-nexthop-internet|20.144.0.0/14|Internet|
|mi-20-150-15-nexthop-internet|20.150.0.0/15|Internet|
|mi-20-160-12-nexthop-internet|20.160.0.0/12|Internet|
|mi-20-176-14-nexthop-internet|20.176.0.0/14|Internet|
|mi-20-180-14-nexthop-internet|20.180.0.0/14|Internet|
|mi-20-184-13-nexthop-internet|20.184.0.0/13|Internet|
|mi-20-192-10-nexthop-internet|20.192.0.0/10|Internet|
|mi-40-64-10-nexthop-internet|40.64.0.0/10|Internet|
|mi-51-4-15-nexthop-internet|51.4.0.0/15|Internet|
|mi-51-8-16-nexthop-internet|51.8.0.0/16|Internet|
|mi-51-10-15-nexthop-internet|51.10.0.0/15|Internet|
|mi-51-18-16-nexthop-internet|51.18.0.0/16|Internet|
|mi-51-51-16-nexthop-internet|51.51.0.0/16|Internet|
|mi-51-53-16-nexthop-internet|51.53.0.0/16|Internet|
|mi-51-103-16-nexthop-internet|51.103.0.0/16|Internet|
|mi-51-104-15-nexthop-internet|51.104.0.0/15|Internet|
|mi-51-132-16-nexthop-internet|51.132.0.0/16|Internet|
|mi-51-136-15-nexthop-internet|51.136.0.0/15|Internet|
|mi-51-138-16-nexthop-internet|51.138.0.0/16|Internet|
|mi-51-140-14-nexthop-internet|51.140.0.0/14|Internet|
|mi-51-144-15-nexthop-internet|51.144.0.0/15|Internet|
|mi-52-96-12-nexthop-internet|52.96.0.0/12|Internet|
|mi-52-112-14-nexthop-internet|52.112.0.0/14|Internet|
|mi-52-125-16-nexthop-internet|52.125.0.0/16|Internet|
|mi-52-126-15-nexthop-internet|52.126.0.0/15|Internet|
|mi-52-130-15-nexthop-internet|52.130.0.0/15|Internet|
|mi-52-132-14-nexthop-internet|52.132.0.0/14|Internet|
|mi-52-136-13-nexthop-internet|52.136.0.0/13|Internet|
|mi-52-145-16-nexthop-internet|52.145.0.0/16|Internet|
|mi-52-146-15-nexthop-internet|52.146.0.0/15|Internet|
|mi-52-148-14-nexthop-internet|52.148.0.0/14|Internet|
|mi-52-152-13-nexthop-internet|52.152.0.0/13|Internet|
|mi-52-160-11-nexthop-internet|52.160.0.0/11|Internet|
|mi-52-224-11-nexthop-internet|52.224.0.0/11|Internet|
|mi-64-4-18-nexthop-internet|64.4.0.0/18|Internet|
|mi-65-52-14-nexthop-internet|65.52.0.0/14|Internet|
|mi-66-119-144-20-nexthop-internet|66.119.144.0/20|Internet|
|mi-70-37-17-nexthop-internet|70.37.0.0/17|Internet|
|mi-70-37-128-18-nexthop-internet|70.37.128.0/18|Internet|
|mi-91-190-216-21-nexthop-internet|91.190.216.0/21|Internet|
|mi-94-245-64-18-nexthop-internet|94.245.64.0/18|Internet|
|mi-103-9-8-22-nexthop-internet|103.9.8.0/22|Internet|
|mi-103-25-156-24-nexthop-internet|103.25.156.0/24|Internet|
|mi-103-25-157-24-nexthop-internet|103.25.157.0/24|Internet|
|mi-103-25-158-23-nexthop-internet|103.25.158.0/23|Internet|
|mi-103-36-96-22-nexthop-internet|103.36.96.0/22|Internet|
|mi-103-255-140-22-nexthop-internet|103.255.140.0/22|Internet|
|mi-104-40-13-nexthop-internet|104.40.0.0/13|Internet|
|mi-104-146-15-nexthop-internet|104.146.0.0/15|Internet|
|mi-104-208-13-nexthop-internet|104.208.0.0/13|Internet|
|mi-111-221-16-20-nexthop-internet|111.221.16.0/20|Internet|
|mi-111-221-64-18-nexthop-internet|111.221.64.0/18|Internet|
|mi-129-75-16-nexthop-internet|129.75.0.0/16|Internet|
|mi-131-107-16-nexthop-internet|131.107.0.0/16|Internet|
|mi-131-253-1-24-nexthop-internet|131.253.1.0/24|Internet|
|mi-131-253-3-24-nexthop-internet|131.253.3.0/24|Internet|
|mi-131-253-5-24-nexthop-internet|131.253.5.0/24|Internet|
|mi-131-253-6-24-nexthop-internet|131.253.6.0/24|Internet|
|mi-131-253-8-24-nexthop-internet|131.253.8.0/24|Internet|
|mi-131-253-12-22-nexthop-internet|131.253.12.0/22|Internet|
|mi-131-253-16-23-nexthop-internet|131.253.16.0/23|Internet|
|mi-131-253-18-24-nexthop-internet|131.253.18.0/24|Internet|
|mi-131-253-21-24-nexthop-internet|131.253.21.0/24|Internet|
|mi-131-253-22-23-nexthop-internet|131.253.22.0/23|Internet|
|mi-131-253-24-21-nexthop-internet|131.253.24.0/21|Internet|
|mi-131-253-32-20-nexthop-internet|131.253.32.0/20|Internet|
|mi-131-253-61-24-nexthop-internet|131.253.61.0/24|Internet|
|mi-131-253-62-23-nexthop-internet|131.253.62.0/23|Internet|
|mi-131-253-64-18-nexthop-internet|131.253.64.0/18|Internet|
|mi-131-253-128-17-nexthop-internet|131.253.128.0/17|Internet|
|mi-132-245-16-nexthop-internet|132.245.0.0/16|Internet|
|mi-134-170-16-nexthop-internet|134.170.0.0/16|Internet|
|mi-134-177-16-nexthop-internet|134.177.0.0/16|Internet|
|mi-137-116-15-nexthop-internet|137.116.0.0/15|Internet|
|mi-137-135-16-nexthop-internet|137.135.0.0/16|Internet|
|mi-138-91-16-nexthop-internet|138.91.0.0/16|Internet|
|mi-138-196-16-nexthop-internet|138.196.0.0/16|Internet|
|mi-139-217-16-nexthop-internet|139.217.0.0/16|Internet|
|mi-139-219-16-nexthop-internet|139.219.0.0/16|Internet|
|mi-141-251-16-nexthop-internet|141.251.0.0/16|Internet|
|mi-146-147-16-nexthop-internet|146.147.0.0/16|Internet|
|mi-147-243-16-nexthop-internet|147.243.0.0/16|Internet|
|mi-150-171-16-nexthop-internet|150.171.0.0/16|Internet|
|mi-150-242-48-22-nexthop-internet|150.242.48.0/22|Internet|
|mi-157-54-15-nexthop-internet|157.54.0.0/15|Internet|
|mi-157-56-14-nexthop-internet|157.56.0.0/14|Internet|
|mi-157-60-16-nexthop-internet|157.60.0.0/16|Internet|
|mi-167-105-16-nexthop-internet|167.105.0.0/16|Internet|
|mi-167-220-16-nexthop-internet|167.220.0.0/16|Internet|
|mi-168-61-16-nexthop-internet|168.61.0.0/16|Internet|
|mi-168-62-15-nexthop-internet|168.62.0.0/15|Internet|
|mi-191-232-13-nexthop-internet|191.232.0.0/13|Internet|
|mi-192-32-16-nexthop-internet|192.32.0.0/16|Internet|
|mi-192-48-225-24-nexthop-internet|192.48.225.0/24|Internet|
|mi-192-84-159-24-nexthop-internet|192.84.159.0/24|Internet|
|mi-192-84-160-23-nexthop-internet|192.84.160.0/23|Internet|
|mi-192-197-157-24-nexthop-internet|192.197.157.0/24|Internet|
|mi-193-149-64-19-nexthop-internet|193.149.64.0/19|Internet|
|mi-193-221-113-24-nexthop-internet|193.221.113.0/24|Internet|
|mi-194-69-96-19-nexthop-internet|194.69.96.0/19|Internet|
|mi-194-110-197-24-nexthop-internet|194.110.197.0/24|Internet|
|mi-198-105-232-22-nexthop-internet|198.105.232.0/22|Internet|
|mi-198-200-130-24-nexthop-internet|198.200.130.0/24|Internet|
|mi-198-206-164-24-nexthop-internet|198.206.164.0/24|Internet|
|mi-199-60-28-24-nexthop-internet|199.60.28.0/24|Internet|
|mi-199-74-210-24-nexthop-internet|199.74.210.0/24|Internet|
|mi-199-103-90-23-nexthop-internet|199.103.90.0/23|Internet|
|mi-199-103-122-24-nexthop-internet|199.103.122.0/24|Internet|
|mi-199-242-32-20-nexthop-internet|199.242.32.0/20|Internet|
|mi-199-242-48-21-nexthop-internet|199.242.48.0/21|Internet|
|mi-202-89-224-20-nexthop-internet|202.89.224.0/20|Internet|
|mi-204-13-120-21-nexthop-internet|204.13.120.0/21|Internet|
|mi-204-14-180-22-nexthop-internet|204.14.180.0/22|Internet|
|mi-204-79-135-24-nexthop-internet|204.79.135.0/24|Internet|
|mi-204-79-179-24-nexthop-internet|204.79.179.0/24|Internet|
|mi-204-79-181-24-nexthop-internet|204.79.181.0/24|Internet|
|mi-204-79-188-24-nexthop-internet|204.79.188.0/24|Internet|
|mi-204-79-195-24-nexthop-internet|204.79.195.0/24|Internet|
|mi-204-79-196-23-nexthop-internet|204.79.196.0/23|Internet|
|mi-204-79-252-24-nexthop-internet|204.79.252.0/24|Internet|
|mi-204-152-18-23-nexthop-internet|204.152.18.0/23|Internet|
|mi-204-152-140-23-nexthop-internet|204.152.140.0/23|Internet|
|mi-204-231-192-24-nexthop-internet|204.231.192.0/24|Internet|
|mi-204-231-194-23-nexthop-internet|204.231.194.0/23|Internet|
|mi-204-231-197-24-nexthop-internet|204.231.197.0/24|Internet|
|mi-204-231-198-23-nexthop-internet|204.231.198.0/23|Internet|
|mi-204-231-200-21-nexthop-internet|204.231.200.0/21|Internet|
|mi-204-231-208-20-nexthop-internet|204.231.208.0/20|Internet|
|mi-204-231-236-24-nexthop-internet|204.231.236.0/24|Internet|
|mi-205-174-224-20-nexthop-internet|205.174.224.0/20|Internet|
|mi-206-138-168-21-nexthop-internet|206.138.168.0/21|Internet|
|mi-206-191-224-19-nexthop-internet|206.191.224.0/19|Internet|
|mi-207-46-16-nexthop-internet|207.46.0.0/16|Internet|
|mi-207-68-128-18-nexthop-internet|207.68.128.0/18|Internet|
|mi-208-68-136-21-nexthop-internet|208.68.136.0/21|Internet|
|mi-208-76-44-22-nexthop-internet|208.76.44.0/22|Internet|
|mi-208-84-21-nexthop-internet|208.84.0.0/21|Internet|
|mi-209-240-192-19-nexthop-internet|209.240.192.0/19|Internet|
|mi-213-199-128-18-nexthop-internet|213.199.128.0/18|Internet|
|mi-216-32-180-22-nexthop-internet|216.32.180.0/22|Internet|
|mi-216-220-208-20-nexthop-internet|216.220.208.0/20|Internet|
|mi-23-96-13-nexthop-internet|23.96.0.0/13|Internet|
|mi-42-159-16-nexthop-internet|42.159.0.0/16|Internet|
|mi-51-13-17-nexthop-internet|51.13.0.0/17|Internet|
|mi-51-107-16-nexthop-internet|51.107.0.0/16|Internet|
|mi-51-116-16-nexthop-internet|51.116.0.0/16|Internet|
|mi-51-120-16-nexthop-internet|51.120.0.0/16|Internet|
|mi-51-120-128-17-nexthop-internet|51.120.128.0/17|Internet|
|mi-51-124-16-nexthop-internet|51.124.0.0/16|Internet|
|mi-102-37-18-nexthop-internet|102.37.0.0/18|Internet|
|mi-102-133-16-nexthop-internet|102.133.0.0/16|Internet|
|mi-199-30-16-20-nexthop-internet|199.30.16.0/20|Internet|
|mi-204-79-180-24-nexthop-internet|204.79.180.0/24|Internet|
||||

\* MI-SUBNETZ bezieht sich auf den IP-Adressbereich für das Subnetz in der Form „x.x.x.x/y“. Diese Informationen finden Sie im Azure-Portal in den Subnetzeigenschaften.

\** Wenn die Zieladresse die Adresse von einem der Azure-Dienste ist, leitet Azure den Datenverkehr über das Azure-Backbonenetzwerk direkt an den Dienst weiter, und nicht über das Internet. Der Datenverkehr zwischen Azure-Diensten wird nicht über das Internet übertragen. Dies gilt unabhängig davon, in welcher Azure-Region das virtuelle Netzwerk vorhanden ist oder in welcher Azure-Region eine Instanz des Azure-Diensts bereitgestellt wird. Weitere Informationen finden Sie auf der [UDR-Dokumentationsseite](../../virtual-network/virtual-networks-udr-overview.md).

Darüber hinaus können Sie der Routingtabelle Einträge hinzufügen, um Datenverkehr mit lokalen privaten IP-Bereichen als Ziel über ein virtuelles Netzwerkgateway oder ein virtuelles Netzwerkgerät (Network Appliance, NVA) zu leiten.

Wenn das virtuelle Netzwerk ein benutzerdefiniertes DNS enthält, muss der benutzerdefinierte DNS-Server öffentliche DNS-Einträge auflösen können. Die Verwendung zusätzlicher Funktionen wie Azure AD Authentication macht unter Umständen auch die Auflösung zusätzlicher FQDNs erforderlich. Weitere Informationen finden Sie unter [Konfigurieren eines benutzerdefinierten DNS für eine verwaltete Azure SQL-Datenbank-Instanz](custom-dns-configure.md).

### <a name="networking-constraints"></a>Netzwerkeinschränkungen

**TLS 1.2 wird für ausgehende Verbindungen erzwungen**: Seit Januar 2020 erzwingt Microsoft in sämtlichen Azure-Diensten TLS 1.2 für den dienstinternen Datenverkehr. Für Azure SQL Managed Instance führte dies dazu, dass TLS 1.2 für ausgehende Verbindungen, die für die Replikation verwendet werden, und für verknüpfte Serververbindungen mit SQL Server erzwungen wird. Wenn Sie ältere Versionen von SQL Server als Version 2016 mit SQL Managed Instance verwenden, stellen Sie sicher, dass [TLS 1.2-spezifische Updates](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server) angewandt wurden.

Die folgenden Features von virtuellen Netzwerken werden derzeit von SQL Managed Instance nicht unterstützt:

- **Microsoft-Peering**: Die Aktivierung von [Microsoft-Peering](../../expressroute/expressroute-faqs.md#microsoft-peering) für ExpressRoute-Verbindungen, die direkt oder transitiv mit einem virtuellen Netzwerk verbunden sind, in dem sich SQL Managed Instance befindet, wirkt sich auf den Datenverkehrsfluss zwischen den Komponenten von SQL Managed Instance innerhalb des virtuellen Netzwerks und den Diensten aus, von denen sie abhängt, und verursacht dadurch Verfügbarkeitsprobleme. SQL Managed Instance-Bereitstellungen im virtuellen Netzwerk mit bereits aktiviertem Microsoft-Peering schlagen wahrscheinlich fehl.
- **Globales Peering virtueller Netzwerke**: Konnektivität per [Peering virtueller Netzwerke](../../virtual-network/virtual-network-peering-overview.md) über Azure-Regionen hinweg funktioniert nicht für SQL Managed Instances in Subnetzen, die vor dem 22.09.2020 erstellt wurden.
- **AzurePlatformDNS**: Die Verwendung des AzurePlatformDNS-[Diensttags](../../virtual-network/service-tags-overview.md) zur Blockierung der DNS-Auflösung der Plattform würde dazu führen, dass SQL Managed Instance nicht mehr verfügbar ist. Obwohl SQL Managed Instance kundenspezifisches DNS für die DNS-Auflösung innerhalb der Engine unterstützt, besteht eine Abhängigkeit vom Plattform-DNS für Plattformvorgänge.
- **NAT-Gateway**: Die Verwendung von [Azure Virtual Network NAT](../../virtual-network/nat-overview.md) zur Steuerung der ausgehenden Konnektivität mit einer bestimmten öffentlichen IP-Adresse würde dazu führen, dass SQL Managed Instance nicht erreichbar ist. Der SQL Managed Instance-Dienst ist aktuell auf die Verwendung eines Basislastenausgleichs beschränkt, der keine parallelen eingehenden und ausgehenden Flows für Virtual Network NAT zulässt.

## <a name="next-steps"></a>Nächste Schritte

- Eine Übersicht finden Sie unter  [Was ist Azure SQL Managed Instance?](sql-managed-instance-paas-overview.md).
- Erfahren Sie, wie Sie ein [neues virtuelles Azure-Network](virtual-network-subnet-create-arm-template.md) oder ein [vorhandenes virtuelles Azure-Network](vnet-existing-add-subnet.md) einrichten, in dem Sie SQL Managed Instance bereitstellen können.
- [Berechnen Sie die Größe des Subnetzes](vnet-subnet-determine-size.md), in dem Sie SQL Managed Instance bereitstellen möchten.
- Erfahren Sie, wie Sie eine verwaltete Instanz erstellen:
  - Im [Azure-Portal](instance-create-quickstart.md).
  - Mit [PowerShell](scripts/create-configure-managed-instance-powershell.md).
  - Mit [einer Azure Resource Manager-Vorlage](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/).
  - Mit [einer Azure Resource Manager-Vorlage (mit JumpBox, in die SSMS integriert ist)](https://azure.microsoft.com/resources/templates/201-sqlmi-new-vnet-w-jumpbox/).
