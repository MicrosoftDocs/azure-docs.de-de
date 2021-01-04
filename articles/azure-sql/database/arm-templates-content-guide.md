---
title: 'Azure Resource Manager-Vorlagen: Azure SQL-Datenbank und SQL Managed Instance'
description: Verwenden Sie Azure Resource Manager-Vorlagen zum Erstellen und Konfigurieren von Azure SQL-Datenbank und verwalteten Azure SQL-Instanzen.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: overview-samples sqldbrb=2
ms.devlang: ''
ms.topic: guide
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 02/04/2019
ms.openlocfilehash: 5202145af6f60f34bdce57aa94f4fec92a8b227f
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92677272"
---
# <a name="azure-resource-manager-templates-for-azure-sql-database--sql-managed-instance"></a>Azure Resource Manager-Vorlagen für Azure SQL-Datenbank und verwaltete SQL-Instanzen
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Mit Azure Resource Manager-Vorlagen können Sie Ihre Infrastruktur als Code definieren und Ihre Lösungen in der Azure-Cloud für Azure SQL-Datenbank und Azure SQL Managed Instance bereitstellen.

## <a name="azure-sql-database"></a>[Azure SQL-Datenbank](#tab/single-database)

Die folgende Tabelle enthält Links zu Azure Resource Manager-Vorlagen für Azure SQL-Datenbank:

|Link |BESCHREIBUNG|
|---|---|
| [SQL-Datenbank](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-database-transparent-encryption-create) | Diese Azure Resource Manager-Vorlage erstellt eine einzelne Datenbank in Azure SQL-Datenbank und konfiguriert IP-Firewallregeln auf Serverebene. |
| [Server](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-logical-server) | Diese Azure Resource Manager-Vorlage erstellt einen Server für Azure SQL-Datenbank. |
| [Pool für elastische Datenbanken](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-elastic-pool-create) | Mit dieser Vorlage können Sie einen Pool für elastische Datenbanken bereitstellen und diesem Datenbanken zuweisen. |
| [Failovergruppen](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-with-failover-group) | Diese Vorlage erstellt zwei Server, eine Einzeldatenbank und eine Failovergruppe in Azure SQL-Datenbank.|
| [Bedrohungserkennung](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-threat-detection-db-policy-multiple-databases) | Mit dieser Vorlage können Sie einen Server und eine Gruppe von Datenbanken mit aktivierter Bedrohungserkennung und einer E-Mail-Adresse für Warnungen für die einzelnen Datenbanken bereitstellen. Die Bedrohungserkennung ist Teil des SQL ATP-Angebots (Advanced Threat Protection) und bietet eine Sicherheitsebene, die auf potenzielle Bedrohungen für Server und -Datenbanken reagiert.|
| [Überwachung mit Azure Blob Storage](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-blob-storage) | Mit dieser Vorlage können Sie einen Server mit aktivierter Überwachung bereitstellen, um Überwachungsprotokolle in einen Blobspeicher zu schreiben. Die Überwachung für Azure SQL-Datenbank verfolgt Datenbankereignisse nach und schreibt sie in ein Überwachungsprotokoll, das in Ihrem Azure-Speicherkonto, in Ihrem OMS-Arbeitsbereich oder in Event Hubs platziert werden kann.|
| [Überwachung mit Azure Event Hub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-eventhub) | Mit dieser Vorlage können Sie einen Server mit aktivierter Überwachung bereitstellen, um Überwachungsprotokolle in einen vorhandenen Event Hub zu schreiben. Um Überwachungsereignisse an Event Hubs zu senden, müssen Sie die Überwachungseinstellungen mit `Enabled` `State` konfigurieren und `IsAzureMonitorTargetEnabled` auf `true` festlegen. Konfigurieren Sie außerdem die Diagnoseeinstellungen mit der Protokollkategorie `SQLSecurityAuditEvents` für die `master`-Datenbank (zur Überwachung auf Serverebene). Die Überwachung verfolgt Datenbankereignisse nach und schreibt sie in ein Überwachungsprotokoll, das in Ihrem Azure-Speicherkonto, in Ihrem OMS-Arbeitsbereich oder in Event Hubs platziert werden kann.|
| [Azure-Web-App mit SQL-Datenbank](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database) | Dieses Beispiel erstellt eine kostenlose Azure-Web-App und eine Datenbank in Azure SQL-Datenbank auf der Dienstebene „Basic“.|
| [Azure-Web-App und Redis Cache mit SQL-Datenbank](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-redis-cache-sql-database) | Diese Vorlage erstellt eine Web-App, eine Redis Cache-Instanz und eine Datenbank in der gleichen Ressourcengruppe und erstellt in der Web-App zwei Verbindungszeichenfolgen für die Datenbank und die Redis Cache-Instanz.|
| [Importieren von Daten aus Blobspeicher mithilfe von ADF V2](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-v2-blob-to-sql-copy) | Diese Azure Resource Manager-Vorlage erstellt eine Instanz von Azure Data Factory V2, um Daten aus Azure Blob Storage in SQL-Datenbank zu kopieren.|
| [HDInsight-Cluster mit einer Datenbank](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-with-sql-database) | Mit dieser Vorlage können Sie einen HDInsight-Cluster, eine logische SQL Server-Instanz, eine Datenbank und zwei Tabellen erstellen. Diese Vorlage wird im Artikel [Verwenden von Apache Sqoop mit Hadoop in HDInsight](../../hdinsight/hadoop/hdinsight-use-sqoop.md) verwendet. |
| [Azure-Logik-App, die eine gespeicherte SQL-Prozedur gemäß einem Zeitplan ausführt](https://github.com/Azure/azure-quickstart-templates/tree/master/101-logic-app-sql-proc) | Mit dieser Vorlage können Sie eine Logik-App erstellen, die eine gespeicherte SQL-Prozedur gemäß einem Zeitplan ausführt. Argumente für die Prozedur können bei Bedarf im Textabschnitt der Vorlage platziert werden.|

## <a name="azure-sql-managed-instance"></a>[Verwaltete Azure SQL-Datenbank-Instanz](#tab/managed-instance)

Die folgende Tabelle enthält Links zu Azure Resource Manager-Vorlagen für verwaltete Azure SQL-Instanzen.

|Link|BESCHREIBUNG|
|---|---|
| [Verwaltete SQL-Instanz in einem neuen VNet](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sqlmi-new-vnet) | Diese Azure Resource Manager-Vorlage erstellt ein neues konfiguriertes virtuelles Azure-Netzwerk und eine verwaltete Instanz im virtuellen Netzwerk. |
| [Netzwerkumgebung für die verwaltete SQL-Instanz](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment) | Diese Bereitstellung erstellt ein konfiguriertes virtuelles Azure-Netzwerk mit zwei Subnetzen: eins für Ihre verwalteten Instanzen und eins für andere Ressourcen (virtuelle Computer, App Service-Umgebungen usw.). Diese Vorlage erstellt eine ordnungsgemäß konfigurierte Netzwerkumgebung, in der Sie verwaltete Instanzen bereitstellen können. |
| [Verwaltete SQL-Instanz mit P2S-Verbindung](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sqlmi-new-vnet-w-point-to-site-vpn) | Diese Bereitstellung erstellt ein virtuelles Azure-Netzwerk mit zwei Subnetzen: `ManagedInstance` und `GatewaySubnet`. SQL Managed Instance wird im Subnetz „ManagedInstance“ bereitgestellt. Im Subnetz `GatewaySubnet` wird ein Gateway für virtuelle Netzwerke erstellt und für eine Point-to-Site-VPN-Verbindung konfiguriert. |
| [SQL Managed Instance mit einem virtuellen Computer](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sqlmi-new-vnet-w-jumpbox) | Diese Bereitstellung erstellt ein virtuelles Azure-Netzwerk mit zwei Subnetzen: `ManagedInstance` und `Management`. SQL Managed Instance wird im Subnetz `ManagedInstance` bereitgestellt. Ein virtueller Computer mit der aktuellen Version von SQL Server Management Studio (SSMS) wird im Subnetz `Management` bereitgestellt. |

---