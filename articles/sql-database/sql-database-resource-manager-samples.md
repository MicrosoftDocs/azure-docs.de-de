---
title: Azure Resource Manager-Vorlagen für SQL-Datenbank | Microsoft-Dokumentation
description: Verwenden Sie Azure Resource Manager-Vorlagen zum Erstellen und Konfigurieren von Azure SQL-Datenbank.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: overview-samples
ms.devlang: ''
ms.topic: sample
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
manager: craigg
ms.date: 02/04/2019
ms.openlocfilehash: b967dc872529ec8b045df81542eec4c555b17a6c
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66418439"
---
# <a name="azure-resource-manager-templates-for-azure-sql-database"></a>Azure Resource Manager-Vorlagen für Azure SQL-Datenbank

Mit Azure Resource Manager-Vorlagen können Sie Ihre Infrastruktur als Code definieren und Ihre Lösungen in der Azure-Cloud bereitstellen.

## <a name="single-database--elastic-pool"></a>Einzeldatenbank und Pool für elastische Datenbanken

Die folgende Tabelle enthält Links zu Azure Resource Manager-Vorlagen für Azure SQL-Datenbank:

| |  |
|---|---|
| [Einzeldatenbank](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-database-transparent-encryption-create) | Diese Azure Resource Manager-Vorlage erstellt eine einzelne Azure SQL-Datenbank mit logischem Server und konfiguriert Firewallregeln. |
| [Logischer Server](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-logical-server) | Diese Azure Resource Manager-Vorlage erstellt einen logischen Server für Azure SQL-Datenbank. |
| [Pool für elastische Datenbanken](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-elastic-pool-create) | Mit dieser Vorlage können Sie einen neuen Pool für elastische Datenbanken mit neuer zugeordneter SQL Server-Instanz und neuen zuzuweisenden SQL-Datenbanken bereitstellen. |
| [Failovergruppen](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-with-failover-group) | Diese Vorlage erstellt zwei logische Azure SQL-Server, eine SQL-Datenbank und eine Failovergruppe.|
| [Bedrohungserkennung](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-threat-detection-db-policy-multiple-databases) | Mit dieser Vorlage können Sie einen logischen Azure SQL-Server und eine Gruppe von Azure SQL-Datenbanken mit aktivierter Bedrohungserkennung und einer E-Mail-Adresse für Warnungen für die einzelnen Datenbanken bereitstellen. Die Bedrohungserkennung ist Teil des ATP-Angebots (Advanced Threat Protection) und bietet eine Sicherheitsebene, die auf potenzielle Bedrohungen für SQL-Server und -Datenbanken reagiert.|
| [Überwachung mit Azure Blob Storage](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-blob-storage) | Mit dieser Vorlage können Sie einen logischen Azure SQL-Server mit aktivierter Überwachung bereitstellen, um Überwachungsprotokolle in einen Blobspeicher zu schreiben. Die Überwachung für Azure SQL-Datenbank verfolgt Datenbankereignisse nach und schreibt sie in ein Überwachungsprotokoll, das in Ihrem Azure-Speicherkonto, in Ihrem OMS-Arbeitsbereich oder in Event Hubs platziert werden kann.|
| [Überwachung mit Azure Event Hub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-eventhub) | Mit dieser Vorlage können Sie einen Azure SQL-Server mit aktivierter Überwachung bereitstellen, um Überwachungsprotokolle in einen vorhandenen Event Hub zu schreiben. Um Überwachungsereignisse an einen Event Hub zu senden, müssen Sie die Überwachungseinstellungen mit `Enabled` `State` konfigurieren und `IsAzureMonitorTargetEnabled` auf `true` festlegen. Konfigurieren Sie außerdem die Diagnoseeinstellungen mit der Diagnoseprotokollkategorie `SQLSecurityAuditEvents` für die `master`-Datenbank (zur Überwachung auf Serverebene). Die Überwachung für Azure SQL-Datenbank und SQL Data Warehouse verfolgt Datenbankereignisse nach und schreibt sie in ein Überwachungsprotokoll, das in Ihrem Azure-Speicherkonto, in Ihrem OMS-Arbeitsbereich oder in Event Hubs platziert werden kann.|
| [Azure-Web-App mit SQL-Datenbank](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database) | Dieses Beispiel erstellt eine kostenlose Azure-Web-App und eine SQL-Datenbank auf der Dienstebene „Basic“.|
| [Azure-Web-App und Redis Cache mit SQL-Datenbank](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-redis-cache-sql-database) | Diese Vorlage erstellt eine Web-App, eine Redis Cache-Instanz und eine SQL-Datenbank in der gleichen Ressourcengruppe und erstellt in der Web-App zwei Verbindungszeichenfolgen für die SQL-Datenbank und die Redis Cache-Instanz.|
| [Importieren von Daten aus Blobspeicher mithilfe von ADF V2](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-v2-blob-to-sql-copy) | Diese Azure Resource Manager-Vorlage erstellt Azure Data Factory V2, um Daten aus Azure Blob Storage in eine SQL-Datenbank zu kopieren.|
| [HDInsight-Cluster mit einer SQL-Datenbank](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-with-sql-database) | Mit dieser Vorlage können Sie einen HDInsight-Cluster, einen SQL-Datenbank-Server, eine SQL-Datenbank und zwei Tabellen erstellen. Diese Vorlage wird im Artikel [Verwenden von Apache Sqoop mit Hadoop in HDInsight](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-use-sqoop) verwendet. |
| [Azure-Logik-App, die eine gespeicherte SQL-Prozedur gemäß einem Zeitplan ausführt](https://github.com/Azure/azure-quickstart-templates/tree/master/101-logic-app-sql-proc) | Mit dieser Vorlage können Sie eine Logik-App zu erstellen, die eine gespeicherte SQL-Prozedur gemäß einem Zeitplan ausführt. Argumente für die Prozedur können bei Bedarf im Textabschnitt der Vorlage platziert werden.|

## <a name="managed-instance"></a>Verwaltete Instanz

Die folgende Tabelle enthält Links zu Azure Resource Manager-Vorlagen für verwaltete Azure SQL-Datenbank-Instanzen:

| |  |
|---|---|
| [Verwaltete Instanz in einem neuen VNET](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sqlmi-new-vnet) | Diese Azure Resource Manager-Vorlage erstellt ein neues konfiguriertes Azure VNET und eine verwaltete Instanz im VNET. |
| [Netzwerkumgebung für die verwaltete Instanz](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment) | Diese Bereitstellung erstellt ein konfiguriertes virtuelles Azure-Netzwerk mit zwei Subnetzen: eins für Ihre verwalteten Instanzen und eins für andere Ressourcen (virtuelle Computer, App Service-Umgebungen und Ähnliches). Diese Vorlage erstellt eine ordnungsgemäß konfigurierte Netzwerkumgebung, in der Sie verwaltete Instanzen bereitstellen können. |
| [Verwaltete Instanz mit P2S-Verbindung](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sqlmi-new-vnet-w-point-to-site-vpn) | Diese Bereitstellung erstellt ein virtuelles Azure-Netzwerk mit zwei Subnetzen: `ManagedInstance` und `GatewaySubnet`. Die verwaltete Instanz wird im Subnetz „ManagedInstance“ bereitgestellt. Das Gateway für virtuelle Netzwerke wird im Subnetz `GatewaySubnet` erstellt und für eine Point-to-Site-VPN-Verbindung konfiguriert. |
| [Verwaltete Instanz mit virtuellem Computer](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sqlmi-new-vnet-w-jumpbox) | Diese Bereitstellung erstellt ein virtuelles Azure-Netzwerk mit zwei Subnetzen: `ManagedInstance` und `Management`. Die verwaltete Instanz wird im Subnetz `ManagedInstance` bereitgestellt. Der virtuelle Computer mit der neuesten Version von SQL Server Management Studio (SSMS) wird im Subnetz `Management` bereitgestellt. |
