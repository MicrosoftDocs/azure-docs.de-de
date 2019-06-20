---
title: Konnektivitätsarchitektur für eine verwaltete Instanz in Azure SQL-Datenbank | Microsoft-Dokumentation
description: Informationen über die Kommunikations- und Konnektivitätsarchitektur für verwaltete Azure SQL-Datenbank-Instanzen sowie die Übertragung des Datenverkehrs an die verwalteten Instanzen mithilfe der Komponenten.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: fasttrack-edit
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
manager: craigg
ms.date: 04/16/2019
ms.openlocfilehash: dbb5ee122e715aeaa66d786f02966beedd2447c3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65522329"
---
# <a name="connectivity-architecture-for-a-managed-instance-in-azure-sql-database"></a>Konnektivitätsarchitektur für eine verwaltete Instanz in Azure SQL-Datenbank

In diesem Artikel wird die Kommunikation in einer verwalteten Azure SQL-Datenbank-Instanz erläutert. Darüber hinaus wird die Konnektivitätsarchitektur beschrieben, und wie die Komponenten Datenverkehr an die verwaltete Instanz leiten.  

Die verwaltete SQL-Datenbank-Instanz wird im virtuellen Azure-Netzwerk und dem für verwaltete Instanzen dedizierten Subnetz platziert. Diese Bereitstellung bietet:

- Eine sichere private IP-Adresse.
- Die Möglichkeit, eine Verbindung eines lokalen Netzwerks mit einer verwalteten Instanz herzustellen.
- Die Möglichkeit, eine Verbindung einer verwalteten Instanz mit einem Verbindungsserver oder einem anderen lokalen Datenspeicher herzustellen.
- Die Möglichkeit, eine Verbindung einer verwalteten Instanz mit Azure-Ressourcen herzustellen.

## <a name="communication-overview"></a>Kommunikationsübersicht

Das folgende Diagramm zeigt Entitäten, die eine Verbindung mit einer verwalteten Instanz herstellen. Es zeigt auch die Ressourcen, die mit der verwalteten Instanz kommunizieren müssen. Der Kommunikationsvorgang im unteren Diagrammbereich bezieht sich auf Kundenanwendungen und Tools, die eine Verbindung mit der verwalteten Instanz als Datenquelle herstellen.  

![Entitäten in der Konnektivitätsarchitektur](./media/managed-instance-connectivity-architecture/connectivityarch001.png)

Eine verwaltete Instanz ist ein „Platform-as-a-Service“-Angebot (PaaS). Microsoft verwendet automatisierte Agenten (Verwaltung, Bereitstellung und Wartung) zum Verwalten dieses Diensts basierend auf Telemetriedatenströmen. Da Microsoft für die Verwaltung zuständig ist, können Kunden nicht über Remote Desktop Protocol (RDP) auf die virtuellen Clustercomputer der verwalteten Instanz zugreifen.

Bei einigen SQL Server-Vorgängen, die von Endbenutzern oder Anwendungen gestartet werden, müssen verwaltete Instanzen möglicherweise mit der Plattform interagieren. Ein Fall ist die Erstellung einer Datenbank für die verwaltete Instanz. Diese Ressource wird über das Azure-Portal, PowerShell, Azure-Befehlszeilenschnittstelle und REST-API verfügbar gemacht.

Verwaltete Instanzen hängen von Azure-Diensten wie Azure Storage für Sicherungen, Azure Event Hubs für Telemetriedaten, Azure Active Directory für Authentifizierung, Azure Key Vault für Transparent Data Encryption (TDE) und einer Reihe von Azure-Plattformdiensten, die Sicherheits- und Supportfunktionen bereitstellen, ab. Die verwalteten Instanzen stellen Verbindungen mit diesen Diensten her.

Jeglicher Datenaustausch ist verschlüsselt und mithilfe von Zertifikaten signiert. Um die Vertrauenswürdigkeit der Kommunikationspartner sicherzustellen, überprüfen verwaltete Instanzen diese Zertifikate regelmäßig anhand von Zertifikatsperrlisten. Wenn die Zertifikate widerrufen werden, schließt die verwaltete Instanz die Verbindungen zum Schutz der Daten.

## <a name="high-level-connectivity-architecture"></a>Verbindungsarchitektur auf Makroebene

Auf hoher Ebene ist eine verwaltete Instanz eine Gruppe von Dienstkomponenten. Diese Komponenten werden auf einer dedizierten Gruppe isolierter virtueller Computer gehostet, die im virtuellen Subnetz des Kunden ausgeführt werden. Diese Computer bilden einen virtuellen Cluster.

Ein virtueller Cluster kann mehrere verwaltete Instanzen hosten. Der Cluster wird ggf. automatisch vergrößert oder verkleinert, wenn der Kunde die Anzahl der bereitgestellten Instanzen im Subnetz ändert.

Kundenanwendungen können eine Verbindung mit verwalteten Instanzen herstellen und Datenbanken innerhalb des virtuellen Netzwerks, des mittels Peering verknüpften virtuellen Netzwerks oder des durch VPN oder Azure ExpressRoute verbundenen Netzwerks abfragen und aktualisieren. Dieses Netzwerk muss einen Endpunkt und eine private IP-Adresse verwenden.  

![Diagramm zur Konnektivitätsarchitektur](./media/managed-instance-connectivity-architecture/connectivityarch002.png)

Die Verwaltungs- und Bereitstellungsdienste von Microsoft werden außerhalb des virtuellen Netzwerks ausgeführt. Eine verwaltete Instanz und Microsoft-Dienste sind über die Endpunkte verbunden, die öffentliche IP-Adressen haben. Wenn eine verwaltete Instanz eine ausgehende Verbindung herstellt, sieht die Verbindung auf der Empfängerseite aufgrund der Netzwerkadressenübersetzung (Network Address Translation, NAT) so aus, käme sie von dieser öffentlichen IP-Adresse.

Der Verwaltungsdatenverkehr wird über das virtuelle Kundennetzwerk übertragen. Das bedeutet, dass die Elemente der Infrastruktur des virtuellen Netzwerks den Verwaltungsdatenverkehr beeinträchtigen können, indem sie einen Fehler bei der Instanz verursachen, sodass sie nicht mehr verfügbar ist.

> [!IMPORTANT]
> Microsoft nutzt zur Verbesserung der Dienstverfügbarkeit und -qualität Netzwerkzielrichtlinien für Infrastrukturelemente virtueller Azure-Netzwerke. Die Richtlinie kann Auswirkungen auf die Funktionsweise der verwalteten Instanz haben. Dieser Plattformmechanismus kommuniziert transparent Netzwerkanforderungen für Benutzer. Das Hauptziel der Richtlinie ist, Fehlkonfigurationen des Netzwerks zu verhindern und den normalen Betrieb der verwalteten Instanz zu gewährleisten. Wenn Sie eine verwaltete Instanz löschen, wird die Netzwerkzielrichtlinie ebenfalls entfernt.

## <a name="virtual-cluster-connectivity-architecture"></a>Verbindungsarchitektur für virtuellen Cluster

Im Folgenden wird die Konnektivitätsarchitektur für verwaltete Instanzen noch genauer betrachtet. Im unten abgebildeten Diagramm wird das konzeptionelle Layout des virtuellen Clusters dargestellt.

![Konnektivitätsarchitektur des virtuellen Clusters](./media/managed-instance-connectivity-architecture/connectivityarch003.png)

Clients stellen mithilfe des Hostnamens, der in der Form `<mi_name>.<dns_zone>.database.windows.net` vorliegen muss, eine Verbindung mit der verwalteten Instanz her. Dieser Hostname wird in eine private IP-Adresse aufgelöst, obwohl er in einer öffentlichen DNS-Zone (Domain Name System) registriert ist und öffentlich aufgelöst werden kann. Die `zone-id` wird beim Erstellen des Clusters automatisch generiert. Wenn in einem neu erstellten Cluster eine sekundäre verwaltete Instanz gehostet wird, verwendet diese die Zonen-ID gemeinsam mit dem primären Cluster. Weitere Informationen finden Sie unter [Verwenden von Autofailover-Gruppen für ein transparentes und koordiniertes Failover mehrerer Datenbanken](sql-database-auto-failover-group.md##enabling-geo-replication-between-managed-instances-and-their-vnets).

Diese private IP-Adresse gehört zum internen Lastenausgleich der verwalteten Instanz. Der Lastenausgleich leitet Datenverkehr an das Gateway der verwalteten Instanz weiter. Da mehrere verwaltete Instanzen in demselben Cluster ausgeführt werden können, nutzt das Gateway den Hostnamen der verwalteten Instanz, um den Datenverkehr an den richtigen SQL-Enginedienst weiterzuleiten.

Verwaltungs- und Bereitstellungsdienste stellen über einen [Verwaltungsendpunkt](#management-endpoint), der einem externen Lastenausgleich zugeordnet ist, eine Verbindung mit einer verwalteten Instanz her. Der Datenverkehr wird nur an die Knoten weitergeleitet, wenn er an einer vordefinierten Gruppe von Ports empfangen wird, die ausschließlich die Verwaltungskomponenten der verwalteten Instanz verwenden. Eine integrierte Firewall auf den Knoten ist so eingerichtet, dass nur Datenverkehr von Microsoft-IP-Adressbereichen zulässig ist. Zertifikate authentifizieren gegenseitig die gesamte Kommunikation zwischen den Verwaltungskomponenten und der Verwaltungsebene.

## <a name="management-endpoint"></a>Verwaltungsendpunkt

Microsoft verwaltet die verwaltete Instanz mit einem Endpunkt für die Verwaltung. Dieser Endpunkt befindet sich im virtuellen Cluster der Instanz. Der Endpunkt für die Verwaltung wird durch eine integrierte Firewall auf Netzwerkebene geschützt. Auf der Anwendungsebene wird er durch gegenseitige Zertifikatüberprüfung geschützt. Die IP-Adresse des Endpunkts finden Sie unter [Ermitteln der IP-Adresse des Verwaltungsendpunkts](sql-database-managed-instance-find-management-endpoint-ip-address.md).

Wenn Verbindungen in der verwalteten Instanz starten (wie bei Sicherungen und Überwachungsprotokollen), scheint der Datenverkehr bei der öffentlichen IP-Adresse des Verwaltungsendpunkts zu beginnen. Sie können den Zugriff von einer verwalteten Instanz auf öffentliche Dienste einschränken, indem Sie Firewallregeln festlegen, die nur die IP-Adresse der verwalteten Instanz zulassen. Weitere Informationen finden Sie unter [Überprüfen der integrierten Firewall einer verwalteten Instanz](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md).

> [!NOTE]
> Datenverkehr, der an Azure-Dienste innerhalb der Region der verwalteten Instanz gerichtet ist, wird optimiert und aus diesem Grund nicht per NAT an die öffentliche IP-Adresse des Verwaltungsendpunkts der verwalteten Instanz übermittelt. Wenn Sie IP-basierte Firewallregeln einsetzen müssen, meistens ist das bei Speicher der Fall, muss sich der Dienst in einer anderen Region als die verwaltete Instanz befinden.

## <a name="network-requirements"></a>Netzwerkanforderungen

Stellen Sie eine verwaltete Instanz in einem dedizierten Subnetz im virtuellen Netzwerk bereit. Das Subnetz muss diese Merkmale aufweisen:

- **Dediziertes Subnetz**: Das Subnetz der verwalteten Instanz darf mit keinem anderen Clouddienst verknüpft und kein Gatewaysubnetz sein. Das Subnetz darf keine Ressourcen außer der verwalteten Instanz enthalten, und Sie können später keine Arten von Ressourcen im Subnetz hinzufügen.
- **Netzwerksicherheitsgruppe (NSG)** : Eine NSG, die mit dem virtuellen Netzwerk verknüpft ist, muss [Eingangssicherheitsregeln](#mandatory-inbound-security-rules) und [Ausgangssicherheitsregeln](#mandatory-outbound-security-rules) vor allen anderen Regeln definieren. Sie können eine NSG verwenden, um den Zugriff auf den Datenendpunkt der verwalteten Instanz zu steuern, indem Sie Datenverkehr an Port 1433 und den Ports 11000–11999 filtern, wenn die verwaltete Instanz für direkte Verbindungen konfiguriert ist.
- **Benutzerdefinierte Routingtabelle (User Defined Route, UDR):** Eine UDR-Tabelle, die mit dem virtuellen Netzwerk verknüpft ist, muss bestimmte [Einträge](#user-defined-routes) enthalten.
- **Keine Dienstendpunkte**: Dem Subnetz der verwalteten Instanz sollte kein Dienstendpunkt zugeordnet werden. Wenn Sie das virtuelle Netzwerk erstellen, überprüfen Sie, ob die Option „Dienstendpunkte“ auf „Deaktiviert“ festgelegt ist.
- **Ausreichende IP-Adressen**: Das Subnetz der verwalteten Instanz muss mindestens 16 IP-Adressen haben. Der empfohlene Mindestwert sind 32 IP-Adressen. Weitere Informationen finden Sie unter [Ermitteln der Größe des Subnetzes für verwaltete Instanzen](sql-database-managed-instance-determine-size-vnet-subnet.md). Sie können verwaltete Instanzen im [vorhandenen Netzwerk](sql-database-managed-instance-configure-vnet-subnet.md) bereitstellen, nachdem Sie dieses entsprechend den [Netzwerkanforderungen für verwaltete Instanzen](#network-requirements) konfiguriert haben. Erstellen Sie andernfalls ein [neues Netzwerk und Subnetz](sql-database-managed-instance-create-vnet-subnet.md).

> [!IMPORTANT]
> Sie können keine neue verwaltete Instanz bereitstellen, wenn das Zielsubnetz nicht über diese Merkmale verfügt. Wenn Sie eine verwaltete Instanz erstellen, wird eine Netzwerkzielrichtlinie auf das Subnetz angewendet, um nicht konforme Änderungen am Netzwerksetup zu verhindern. Nachdem die letzte Instanz aus dem Subnetz entfernt wurde, wird auch die Netzwerkzielrichtlinie entfernt.

### <a name="mandatory-inbound-security-rules"></a>Obligatorische Eingangssicherheitsregeln

| NAME       |Port                        |Protocol|`Source`           |Ziel|Aktion|
|------------|----------------------------|--------|-----------------|-----------|------|
|management  |9000, 9003, 1438, 1440, 1452|TCP     |Beliebig              |MI-SUBNETZ  |ZULASSEN |
|mi_subnet   |Beliebig                         |Beliebig     |MI-SUBNETZ        |MI-SUBNETZ  |ZULASSEN |
|health_probe|Beliebig                         |Beliebig     |AzureLoadBalancer|MI-SUBNETZ  |ZULASSEN |

### <a name="mandatory-outbound-security-rules"></a>Obligatorische Ausgangssicherheitsregeln

| NAME       |Port          |Protocol|`Source`           |Ziel|Aktion|
|------------|--------------|--------|-----------------|-----------|------|
|management  |80, 443, 12000|TCP     |MI-SUBNETZ        |AzureCloud |ZULASSEN |
|mi_subnet   |Beliebig           |Beliebig     |MI-SUBNETZ        |MI-SUBNETZ  |ZULASSEN |

> [!IMPORTANT]
> Stellen Sie sicher, dass es nur eine Regel für eingehenden Datenverkehr für die Ports 9000, 9003, 1438, 1440, 1452 und eine Regel für ausgehenden Datenverkehr für die Ports 80, 443, 12000 gibt. Die Bereitstellung von verwalteten Instanzen über Azure Resource Manager-Bereitstellungen schlägt fehl, wenn Regeln für eingehenden und ausgehenden Datenverkehr für jeden Port separat konfiguriert werden. Wenn für diese Ports separate Regeln gelten, schlägt die Bereitstellung mit dem Fehlercode `VnetSubnetConflictWithIntendedPolicy` fehl.

\* MI-SUBNETZ bezieht sich auf den IP-Adressbereich für das Subnetz in der Form 10.x.x.x/y. Diese Informationen finden Sie im Azure-Portal in den Subnetzeigenschaften.

> [!IMPORTANT]
> Obwohl die erforderlichen Eingangssicherheitsregeln den Datenverkehr von _allen_ Quellen an den Ports 9000, 9003, 1438, 1440 und 1452 zulassen, sind diese Ports durch eine integrierte Firewall geschützt. Weitere Informationen finden Sie unter [Ermitteln der IP-Adresse des Verwaltungsendpunkts](sql-database-managed-instance-find-management-endpoint-ip-address.md).
> [!NOTE]
> Wenn Sie die Transaktionsreplikation in einer verwalteten Instanz verwenden und Sie eine Instanzdatenbank als Herausgeber oder Verteiler einsetzen, öffnen Sie Port 445 (TCP ausgehend) in den Sicherheitsregeln des Subnetzes. Dieser Port ermöglicht den Zugriff auf die Azure-Dateifreigabe.

### <a name="user-defined-routes"></a>Benutzerdefinierte Routen

|NAME|Adresspräfix|Nächster Hop|
|----|--------------|-------|
|subnet_to_vnetlocal|MI-SUBNETZ|Virtuelles Netzwerk|
|mi-13-64-11-nexthop-internet|13.64.0.0/11|Internet|
|mi-13-96-13-nexthop-internet|13.96.0.0/13|Internet|
|mi-13-104-14-nexthop-internet|13.104.0.0/14|Internet|
|mi-20-8-nexthop-internet|20.0.0.0/8|Internet|
|mi-23-96-13-nexthop-internet|23.96.0.0/13|Internet|
|mi-40-64-10-nexthop-internet|40.64.0.0/10|Internet|
|mi-42-159-16-nexthop-internet|42.159.0.0/16|Internet|
|mi-51-8-nexthop-internet|51.0.0.0/8|Internet|
|mi-52-8-nexthop-internet|52.0.0.0/8|Internet|
|mi-64-4-18-nexthop-internet|64.4.0.0/18|Internet|
|mi-65-52-14-nexthop-internet|65.52.0.0/14|Internet|
|mi-66-119-144-20-nexthop-internet|66.119.144.0/20|Internet|
|mi-70-37-17-nexthop-internet|70.37.0.0/17|Internet|
|mi-70-37-128-18-nexthop-internet|70.37.128.0/18|Internet|
|mi-91-190-216-21-nexthop-internet|91.190.216.0/21|Internet|
|mi-94-245-64-18-nexthop-internet|94.245.64.0/18|Internet|
|mi-103-9-8-22-nexthop-internet|103.9.8.0/22|Internet|
|mi-103-25-156-22-nexthop-internet|103.25.156.0/22|Internet|
|mi-103-36-96-22-nexthop-internet|103.36.96.0/22|Internet|
|mi-103-255-140-22-nexthop-internet|103.255.140.0/22|Internet|
|mi-104-40-13-nexthop-internet|104.40.0.0/13|Internet|
|mi-104-146-15-nexthop-internet|104.146.0.0/15|Internet|
|mi-104-208-13-nexthop-internet|104.208.0.0/13|Internet|
|mi-111-221-16-20-nexthop-internet|111.221.16.0/20|Internet|
|mi-111-221-64-18-nexthop-internet|111.221.64.0/18|Internet|
|mi-129-75-16-nexthop-internet|129.75.0.0/16|Internet|
|mi-131-253-16-nexthop-internet|131.253.0.0/16|Internet|
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
|mi-167-220-16-nexthop-internet|167.220.0.0/16|Internet|
|mi-168-61-16-nexthop-internet|168.61.0.0/16|Internet|
|mi-168-62-15-nexthop-internet|168.62.0.0/15|Internet|
|mi-191-232-13-nexthop-internet|191.232.0.0/13|Internet|
|mi-192-32-16-nexthop-internet|192.32.0.0/16|Internet|
|mi-192-48-225-24-nexthop-internet|192.48.225.0/24|Internet|
|mi-192-84-159-24-nexthop-internet|192.84.159.0/24|Internet|
|mi-192-84-160-23-nexthop-internet|192.84.160.0/23|Internet|
|mi-192-100-102-24-nexthop-internet|192.100.102.0/24|Internet|
|mi-192-100-103-24-nexthop-internet|192.100.103.0/24|Internet|
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
||||

Darüber hinaus können Sie der Routingtabelle Einträge hinzufügen, um Datenverkehr mit lokalen privaten IP-Bereichen als Ziel über ein virtuelles Netzwerkgateway oder ein virtuelles Netzwerkgerät (Network Appliance, NVA) zu leiten.

Wenn das virtuelle Netzwerk ein benutzerdefiniertes DNS enthält, muss der benutzerdefinierte DNS-Server imstande sein, Hostnamen in der Zone \*. core.windows.net aufzulösen. Die Verwendung zusätzlicher Funktionen wie Azure AD Authentication macht unter Umständen auch die Auflösung zusätzlicher FQDNs erforderlich. Weitere Informationen finden Sie unter [Konfigurieren eines benutzerdefinierten DNS für eine verwaltete Azure SQL-Datenbank-Instanz](sql-database-managed-instance-custom-dns.md).

## <a name="next-steps"></a>Nächste Schritte

- Eine Übersicht finden Sie unter  [Verwenden der Advanced Data Security einer Azure SQL-Datenbank mit virtuellen Netzwerken und nahezu 100%iger Kompatibilität](sql-database-managed-instance.md).
- Erfahren Sie, wie Sie [ein neues virtuelles Azure-Network](sql-database-managed-instance-create-vnet-subnet.md) oder [vorhandenes virtuelles Azure-Network](sql-database-managed-instance-configure-vnet-subnet.md) einrichten, wo Sie verwaltete Instanzen bereitstellen können.
- [Berechnen Sie die Größe des Subnetzes](sql-database-managed-instance-determine-size-vnet-subnet.md), in dem Sie verwaltete Instanzen bereitstellen möchten.
- Erfahren Sie, wie Sie eine verwaltete Instanz erstellen:
  - Im [Azure-Portal](sql-database-managed-instance-get-started.md).
  - Mit [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md).
  - Mit [einer Azure Resource Manager-Vorlage](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/).
  - Mit [einer Azure Resource Manager-Vorlage (mit JumpBox, in die SSMS integriert ist)](https://portal.azure.com/). 
