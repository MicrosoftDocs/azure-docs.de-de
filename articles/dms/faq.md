---
title: 'Häufig gestellte Fragen: Azure Database Migration Service'
description: Hier finden Sie häufig gestellte Fragen zum Verwenden von Azure Database Migration Service zur Durchführung von Datenbankmigrationen.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: troubleshooting
ms.date: 02/20/2020
ms.openlocfilehash: bf6e61ef3dfb1c50166cf17168b4deeb21e958d7
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94962908"
---
# <a name="faq-about-using-azure-database-migration-service"></a>Häufig gestellte Fragen zur Verwendung von Azure Database Migration Service

Dieser Artikel enthält häufig gestellte Fragen zur Verwendung von Azure Database Migration Service sowie entsprechende Antworten.

## <a name="overview"></a>Übersicht

**F: Was ist Azure Database Migration Service?**
Azure Database Migration Service ist ein vollständig verwalteter Dienst, der die nahtlose Migration von mehreren Datenbankquellen zu Azure-Datenplattformen mit minimaler Ausfallzeit ermöglicht. Der Dienst weist zurzeit den Status „Allgemeine Verfügbarkeit“ auf, wobei laufende Entwicklungsarbeiten sich auf Folgendes konzentrieren:

* Zuverlässigkeit und Leistung
* Iteratives Hinzufügen von Quelle-Ziel-Paaren
* Kontinuierliche Investition in reibungslose Migrationen

**F: Welche Quelle-Ziel-Paare werden derzeit von Azure Database Migration Service unterstützt?**
Der Dienst unterstützt derzeit eine Vielzahl von Quelle-Ziel-Paaren oder Migrationsszenarien. Eine vollständige Liste der Status der einzelnen verfügbaren Migrationsszenarios finden Sie im Artikel [Status von Migrationsszenarios, die von Azure Database Migration Service unterstützt werden](./resource-scenario-status.md).

Andere Migrationsszenarios befinden sich in der Vorschau und erfordern eine Nominierung über die DMS-Vorschauseite. Eine vollständige Liste der Szenarien in der Vorschau finden Sie auf der [DMS-Vorschauseite](https://aka.ms/dms-preview/). Hier können Sie sich auch registrieren, um eines dieser Angebote zu nutzen.

**F: Welche Versionen von SQL Server werden von Azure Database Migration Service als Datenquelle unterstützt?**
Für die Migration von SQL Server werden SQL Server 2005 bis SQL Server 2019 für Azure Database Migration Service unterstützt.

**F: Was ist der Unterschied zwischen einer Offline- und einer Onlinemigration, wenn Azure Database Migration Service verwendet wird?**
Sie können Azure Database Migration Service verwenden, um Offline- und Onlinemigrationen durchzuführen. Bei einer *Offlinemigration* beginnt der Ausfall der Anwendung, wenn die Migration gestartet wird. Bei einer *Onlinemigration* ist der Ausfall auf die Dauer der Umstellung am Ende der Migration beschränkt. Wir raten Ihnen, eine Offlinemigration zu testen, um zu ermitteln, ob die Ausfallzeit akzeptabel ist. Wenn nicht, sollten Sie eine Onlinemigration durchführen.

> [!NOTE]
> Die Verwendung von Azure Database Migration Service zum Ausführen einer Onlinemigration erfordert das Erstellen einer Instanz auf der Grundlage des Premium-Tarifs. Weitere Informationen finden Sie auf der Seite [Azure Database Migration Service – Preise](https://azure.microsoft.com/pricing/details/database-migration/).

**F: Inwiefern unterscheidet sich Azure Database Migration Service von anderen Microsoft-Datenbankmigrationstools wie DBA (Database Migration Assistant) oder SSMA (SQL Server Migration Assistant)?**
Azure Database Migration Service ist die bevorzugte Methode für bedarfsorientierte Datenbankmigrationen zu Microsoft Azure. Ausführlichere Informationen zu den Unterschieden zwischen Azure Database Migration Service und anderen Microsoft-Datenbankmigrationstools sowie Empfehlungen für die Verwendung des Diensts in verschiedenen Szenarien finden Sie im Blogbeitrag [Differentiating Microsoft’s Database Migration Tools and Services](https://techcommunity.microsoft.com/t5/microsoft-data-migration/differentiating-microsoft-s-database-migration-tools-and/ba-p/368529) (Abgrenzung der Datenbankmigrationstools und -dienste von Microsoft).

**F: Inwiefern unterscheidet sich Azure Database Migration Service vom Azure Migrate-Angebot?**
Azure Migrate unterstützt Benutzer bei der Migration lokaler virtueller Computer zu Azure IaaS. Der Dienst bewertet die Eignung für die Migration und die leistungsbasierte Größenauslegung und stellt Kostenschätzungen für die Ausführung Ihrer lokalen virtuellen Computer in Azure bereit. Azure Migrate ist hilfreich bei Lift & Shift-Migrationen lokaler, VM-basierter Workloads zu virtuellen Azure IaaS-Computern. Im Gegensatz zu Azure Database Migration Service handelt es sich bei Azure Migrate allerdings nicht um einen speziellen Datenbankmigrationsdienst für relationale Azure PaaS-Datenbankplattformen wie Azure SQL-Datenbank oder eine verwaltete Azure SQL-Instanz.

## <a name="setup"></a>Einrichten

**F: Welche Voraussetzungen müssen für die Verwendung von Azure Database Migration Service erfüllt sein?**
Zur Gewährleistung reibungsloser Datenbankmigrationen mit Azure Database Migration Service müssen mehrere Voraussetzungen erfüllt werden. Einige der Voraussetzungen gelten für alle unterstützten Szenarien (Quelle-Ziel-Paare) des Diensts, andere nur für ein bestimmtes Szenario.

Folgende Voraussetzungen von Azure Database Migration Service gelten für alle unterstützten Migrationsszenarien:

* Erstellen Sie ein Microsoft Azure Virtual Network für Azure Database Migration Service, indem Sie das Azure Resource Manager-Bereitstellungsmodell verwenden, das Site-to-Site-Konnektivität für Ihre lokalen Quellserver entweder über [ExpressRoute](../expressroute/expressroute-introduction.md) oder über [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) bereitstellt.
* Stellen Sie sicher, dass die NSG-Regeln Ihres virtuellen Netzwerks nicht die folgenden Kommunikationsports blockieren: 443, 53, 5671-5672, 9350-9354, 445, 12000. Ausführlichere Informationen zur NSG-Datenverkehrsfilterung in einem virtuellen Netzwerk finden Sie im Artikel [Filtern des Netzwerkdatenverkehrs mit Netzwerksicherheitsgruppen](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* Wenn Sie eine Firewallappliance vor Ihren Quelldatenbanken verwenden, müssen Sie möglicherweise Firewallregeln hinzufügen, um Azure Database Migration Service den Zugriff auf die Quelldatenbanken für die Migration zu ermöglichen.

Eine Liste mit allen Voraussetzungen für spezifische Migrationsszenarien mit Azure Database Migration Service finden Sie auf docs.microsoft.com in den entsprechenden Tutorials der [Azure Database Migration Service-Dokumentation](./dms-overview.md).

**F: Wie finde ich die IP-Adresse für Azure Database Migration Service, damit ich eine Liste zugelassener IP-Adressen für die Firewallregeln erstellen kann, die für den Zugriff auf meine Quelldatenbank für die Migration verwendet werden?**
Möglicherweise müssen Firewallregeln hinzugefügt werden, damit Azure Database Migration Service auf Ihre Quelldatenbank für die Migration zugreifen kann. Die IP-Adresse für den Dienst ist zwar dynamisch, bei Verwendung von ExpressRoute wird sie allerdings privat durch Ihr Unternehmensnetzwerk zugewiesen. Die einfachste Möglichkeit zur Ermittlung der entsprechenden IP-Adresse besteht darin, in der Ressourcengruppe, in der sich auch Ihre bereitgestellte Azure Database Migration Service-Ressource befindet, nach der zugeordneten Netzwerkschnittstelle zu suchen. Der Name der Netzwerkschnittstellenressource beginnt üblicherweise mit dem NIC-Präfix, gefolgt von einer eindeutigen Zeichen- und Ziffernfolge (z.B. NIC-jj6tnztnmarpsskr82rbndyp). Wenn Sie auf diese Netzwerkschnittstellenressource klicken, wird die IP-Adresse angezeigt, die auf der Ressourcenübersichtsseite im Azure-Portal in die Liste zugelassener IP-Adressen aufgenommen werden muss.

Gegebenenfalls müssen Sie der Zulassungsliste auch die Portquelle hinzufügen, an der SQL Server lauscht. Dies ist standardmäßig Port 1433. Es ist jedoch möglich, dass die SQL Server-Quellinstanz für das Lauschen an anderen Ports konfiguriert wurde. In diesem Fall müssen Sie diese Ports ebenfalls der Zulassungsliste hinzufügen. Den Port, an dem SQL Server lauscht, können Sie mithilfe einer Abfrage vom Typ „Dynamische Verwaltungssicht“ ermittelt:

```sql
    SELECT DISTINCT
        local_tcp_port
    FROM sys.dm_exec_connections
    WHERE local_tcp_port IS NOT NULL
```

Zum Ermitteln des Ports, an dem SQL Server lauscht, können Sie auch das SQL Server-Fehlerprotokoll abfragen:

```sql
    USE master
    GO
    xp_readerrorlog 0, 1, N'Server is listening on'
    GO
```

**F: Wie richte ich eine Microsoft Azure Virtual Network-Instanz ein?**
Die Einrichtung eines virtuellen Azure-Netzwerks wird in mehreren Microsoft-Tutorials ausführlich beschrieben. Die offizielle Dokumentation finden Sie jedoch im Artikel [Azure Virtual Network](../virtual-network/virtual-networks-overview.md).

## <a name="usage"></a>Verwendung

**F: Welche Schritte sind allgemein für eine Datenbankmigration mit Azure Database Migration Service erforderlich?**
Eine typische einfache Datenbankmigration umfasst Folgendes:

1. Erstellen von Zieldatenbanken
2. Bewerten der Quelldatenbank(en).
    * Bewerten der vorhandenen Datenbank(en) mithilfe von [DMA](https://www.microsoft.com/download/details.aspx?id=53595) für homogene Migrationen.
    * Bewerten der vorhandenen Datenbank(en) mithilfe von [SSMA](/sql/ssma/sql-server-migration-assistant) für heterogene Migrationen (aus verschiedenen Quellen). Sie verwenden SSMA auch, um Datenbankobjekte zu konvertieren und das Schema zu Ihrer Zielplattform zu migrieren.
3. Erstellen einer Instanz von Azure Database Migration Service
4. Erstellen eines Migrationsprojekts unter Angabe der Quelldatenbanken, der Zieldatenbanken und der zu migrierenden Tabellen.
5. Starten des vollständigen Ladenvorgangs.
6. Auswählen der anschließenden Überprüfung
7. Durchführen eines manuellen Wechsels zur neuen cloudbasierten Datenbank für Ihre Produktionsumgebung

## <a name="troubleshooting-and-optimization"></a>Problembehandlung und Optimierung

**F: Ich richte ein Migrationsprojekt in DMS ein und habe Schwierigkeiten beim Herstellen einer Verbindung mit meiner Quelldatenbank. Wie soll ich vorgehen?**
Wenn Sie während der Migration keine Verbindung mit Ihrem Quelldatenbanksystem herstellen können, erstellen Sie einen virtuellen Computer im virtuellen Netzwerk, mit dem Sie Ihre DMS-Instanz einrichten. Im virtuellen Computer sollten Sie in der Lage sein, einen Verbindungstest durchzuführen, z.B. mit einer UDL-Datei, um eine Verbindung mit SQL Server zu testen, oder Robo 3T herunterzuladen, um MongoDB-Verbindungen zu testen. Wenn der Verbindungstest erfolgreich ist, sollten Sie kein Problem mit dem Herstellen einer Verbindung mit Ihrer Quelldatenbank haben. Wenn der Verbindungstest nicht erfolgreich ist, wenden Sie sich an Ihren Netzwerkadministrator.

**F: Warum ist mein Azure Database Migration Service nicht verfügbar oder wurde beendet?**
Wenn der Benutzer Azure Database Migration Service (DMS) explizit beendet oder der Dienst für einen Zeitraum von 24 Stunden inaktiv ist, wird der Dienst beendet oder automatisch angehalten. In jedem Fall ist der Dienst nicht verfügbar und beendet.  Um aktive Migrationen wieder aufzunehmen, starten Sie den Dienst neu.

**F: Gibt es Leistungsoptimierungsempfehlungen für Azure Database Migration Service?**
Die Datenmigration mit diesem Dienst lässt sich durch folgende Maßnahmen beschleunigen:

* Verwenden Sie beim Erstellen Ihrer Dienstinstanz den universellen Tarif mit mehreren CPUs, damit dem Dienst mehrere vCPUs für Parallelisierung und schnellere Datenübertragungen zur Verfügung stehen.
* Skalieren Sie Ihre Azure SQL-Datenbank-Zielinstanz während der Datenmigration vorübergehend auf die Premium-Tarifebene hoch, um die Drosselung durch Azure SQL-Datenbank zu minimieren, die bei SKUs einer niedrigeren Ebene unter Umständen zu einer Beeinträchtigung der Datenübertragungsaktivitäten führt.

## <a name="next-steps"></a>Nächste Schritte

Eine Übersicht über Azure Database Migration Service und Informationen zur regionalen Verfügbarkeit finden Sie im Artikel [Was ist Azure Database Migration Service?](dms-overview.md).