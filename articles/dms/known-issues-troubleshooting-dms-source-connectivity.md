---
title: Probleme beim Verbinden von Quelldatenbanken
titleSuffix: Azure Database Migration Service
description: Erfahren Sie mehr über die Problembehandlung bekannter Probleme/Fehler beim Herstellen einer Verbindung zwischen Azure Database Migration Service und Quelldatenbanken.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: troubleshooting
ms.date: 02/20/2020
ms.openlocfilehash: 93ac6ae3c8aed61557a239bb9c84d3587dce1daa
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94962330"
---
# <a name="troubleshoot-dms-errors-when-connecting-to-source-databases"></a>Problembehandlung von DMS-Fehlern beim Herstellen einer Verbindung mit Quelldatenbanken

Der folgende Artikel enthält detaillierte Informationen darüber, wie Sie mögliche Probleme behandeln können, die bei der Verbindung von Azure Database Migration Service (DMS) mit Ihrer Quelldatenbank auftreten können. Jeder der folgenden Abschnitte bezieht sich auf einen bestimmten Typ von Quelldatenbank und listet den Fehler auf, den Sie möglicherweise feststellen, zusammen mit Details und Links zu Informationen zur Problembehandlung der Konnektivität.

## <a name="sql-server"></a>SQL Server

Potenzielle Probleme im Zusammenhang mit der Verbindung mit einer SQL Server-Quelldatenbank und deren Behandlung werden in der folgenden Tabelle aufgeführt.

| Fehler         | Ursache und Problembehandlungsdetails |
| ------------- | ------------- |
| SQL-Verbindungsfehler. Netzwerkbezogener oder instanzspezifischer Fehler beim Herstellen einer Verbindung mit SQL Server. Der Server wurde nicht gefunden, oder auf ihn kann nicht zugegriffen werden. Stellen Sie sicher, dass der Instanzname richtig ist und dass SQL Server für Remoteverbindungen konfiguriert ist.<br> | Dieser Fehler tritt auf, wenn der Dienst den Quellserver nicht finden kann. Um das Problem zu beheben, lesen Sie den Artikel [Fehler beim Verbinden mit der SQL Server-Quellinstanz bei Verwendung eines dynamischen Ports oder einer benannten Instanz](./known-issues-troubleshooting-dms.md#error-connecting-to-source-sql-server-when-using-dynamic-port-or-named-instance). |
| **Fehler53**: SQL-Verbindungsfehler. (Auch Fehlercodecodes 1, 2, 5, 53, 233, 258, 1225, 11001)<br><br> | Dieser Fehler tritt auf, wenn der Dienst keine Verbindung mit dem Quellserver herstellen kann. Um das Problem zu beheben, ziehen Sie die folgenden Ressourcen zu Rate, und versuchen Sie es dann erneut. <br><br>  [Interaktiver Benutzerleitfaden für die Behandlung des Konnektivitätsproblems](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server)<br><br> [Voraussetzungen für die Migration von SQL Server zu Azure SQL-Datenbank](./pre-reqs.md#prerequisites-for-migrating-sql-server-to-azure-sql-managed-instance) <br><br> [Voraussetzungen für die Migration von SQL Server zu Azure SQL Managed Instance](./pre-reqs.md#prerequisites-for-migrating-sql-server-to-azure-sql-managed-instance) |
| **Fehler 18456**: Fehler bei der Anmeldung.<br> | Dieser Fehler tritt auf, wenn der Dienst keine Verbindung mit der Quelldatenbank mit den angegebenen T-SQL-Anmeldeinformationen herstellen kann. Um das Problem zu beheben, überprüfen Sie die eingegebenen Anmeldeinformationen. Sie können auch [MSSQLSERVER_18456](/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error?view=sql-server-2017) oder die in der Anmerkung unter dieser Tabelle aufgeführten Problembehandlungsdokumente verwenden und es dann erneut versuchen. |
| Ein falsch formatierter AccountName-Wert „{0}“ wurde bereitgestellt. Das erwartete Format für AccountName ist Domänenname\Benutzername.<br> | Dieser Fehler tritt auf, wenn der Benutzer Windows-Authentifizierung ausgewählt, aber den Benutzernamen in einem ungültigen Format angibt. Um das Problem zu beheben, geben Sie entweder den Benutzernamen im richtigen Format für Windows-Authentifizierung an, oder wählen Sie **SQL-Authentifizierung** aus. |

## <a name="aws-rds-mysql"></a>AWS RDS MySQL

Potenzielle Probleme im Zusammenhang mit der Verbindung mit einer AWS RDS MySQL-Quelldatenbank und deren Behandlung werden in der folgenden Tabelle aufgeführt.

| Fehler         | Ursache und Problembehandlungsdetails |
| ------------- | ------------- |
| **Fehler [2003]** [HY000]: Verbindungsfehler. Fehler [HY000] [MySQL] [ODBC-Treiber x.x(w)] Es kann keine Verbindung mit MySQL-Server auf "{Server}" hergestellt werden (10060). | Dieser Fehler tritt auf, wenn der MySQL ODBC-Treiber keine Verbindung mit dem Quellserver herstellen kann. Um das Problem zu beheben, verwenden Sie die in der Anmerkung unter dieser Tabelle aufgeführten Problembehandlungsdokumente, und versuchen Sie es dann erneut.<br> |
| **Fehler [2005]** [HY000]: Verbindungsfehler. FEHLER [HY000] [MySQL] [ODBC-Treiber x.x(w)] Unbekannter MySQL-Serverhost "{Server}" | Dieser Fehler tritt auf, wenn der Dienst den Quellhost für RDS nicht finden kann. Das Problem kann entweder darin bestehen, dass die aufgelistete Quelle nicht vorhanden ist oder dass es ein Problem mit der RDS-Infrastruktur gibt. Um das Problem zu beheben, verwenden Sie die in der Anmerkung unter dieser Tabelle aufgeführten Problembehandlungsdokumente, und versuchen Sie es dann erneut.<br> |
| **Fehler [1045]** [HY000]: Verbindungsfehler. FEHLER [HY000] [MySQL] [ODBC-Treiber x.x(w)] Zugriff für Benutzer "{user}"@"{server}" verweigert (mit Kennwort: JA) | Dieser Fehler tritt auf, wenn der MySQL ODBC-Treiber aufgrund ungültiger Anmeldeinformationen keine Verbindung mit dem Quellserver herstellen kann. Überprüfen Sie die Anmeldeinformationen, die Sie eingegeben haben. Wenn das Problem weiterhin besteht, stellen Sie sicher, dass der Quellcomputer über die richtigen Anmeldeinformationen verfügt. Möglicherweise müssen Sie das Kennwort in der Konsole zurücksetzen. Wenn das noch immer Problem besteht, verwenden Sie die in der Anmerkung unter dieser Tabelle aufgeführten Problembehandlungsdokumente, und versuchen Sie es dann erneut.<br> |
| **Fehler [9002]** [HY000]: Verbindungsfehler. Fehler [HY000] [MySQL] [ODBC-Treiber x.x(w)] Die Verbindungszeichenfolge ist möglicherweise nicht richtig. Besuchen Sie das Portal, um Referenzen zu erhalten.| Dieser Fehler tritt auf, wenn die Verbindung aufgrund eines Problems mit der Verbindungszeichenfolge fehlschlägt. Überprüfen Sie, ob die bereitgestellte Verbindungszeichenfolge gültig ist. Um das Problem zu beheben, verwenden Sie die in der Anmerkung unter dieser Tabelle aufgeführten Problembehandlungsdokumente, und versuchen Sie es dann erneut.<br> |
| **Fehler bei der binären Protokollierung. Variable binlog_format weist den Wert "{wert}" auf. Ändern Sie diesen in "row".** | Dieser Fehler tritt auf, wenn ein Fehler in der binären Protokollierung vorliegt. Die Variable binlog_format weist den falschen Wert auf. Um das Problem zu beheben, ändern Sie das binlog_format in der Parametergruppe in "ROW", und starten Sie die Instanz dann neu. Weitere Informationen finden Sie unter [Optionen und Variablen für die binäre Protokollierung](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html) oder [Dokumentation zu AWS RDS MySQL-Datenbankprotokolldateien](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_LogAccess.Concepts.MySQL.html).<br> |

> [!NOTE]
> Weitere Informationen zum Behandeln von Problemen im Zusammenhang mir dem Herstellen einer Verbindung mit einer AWS RDS MySQL-Quelldatenbank finden Sie unter den folgenden Ressourcen:
> * [Behandlung von Amazon RDS-Verbindungsproblemen](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Troubleshooting.html#CHAP_Troubleshooting.Connecting)
> * [Wie behebe ich Probleme beim Herstellen einer Verbindung mit meiner Amazon RDS-Datenbankinstanz?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="aws-rds-postgresql"></a>AWS RDS PostgreSQL

Potenzielle Probleme im Zusammenhang mit der Verbindung mit einer AWS RDS PostgreSQL-Quelldatenbank und deren Behandlung werden in der folgenden Tabelle aufgeführt.

| Fehler         | Ursache und Problembehandlungsdetails |
| ------------- | ------------- |
| **Fehler [101]** [08001]: Verbindungsfehler. FEHLER [08001]: Timeout ist abgelaufen. | Dieser Fehler tritt auf, wenn der Postgres-Treiber keine Verbindung mit dem Quellserver herstellen kann. Um das Problem zu beheben, verwenden Sie die in der Anmerkung unter dieser Tabelle aufgeführten Problembehandlungsdokumente, und versuchen Sie es dann erneut. |
| **Error: Parameter wal_level weist den Wert "{wert}" auf. Ändern Sie ihn "logical", um Replikation zuzulassen.** | Dieser Fehler tritt auf, wenn der Parameter wal_level einen falschen Wert aufweist. Um das Problem zu beheben, ändern Sie rds.logical_replication in der Parametergruppe in 1, und starten Sie die Instanz dann neu. Weitere Informationen finden Sie unter [Voraussetzungen für die Migration zu Azure PostgreSQL mit DMS](./tutorial-postgresql-azure-postgresql-online.md#prerequisites) oder [PostgreSQL unter Amazon RDS](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_PostgreSQL.html). |

> [!NOTE]
> Weitere Informationen zum Behandeln von Problemen im Zusammenhang mir dem Herstellen einer Verbindung mit einer AWS RDS PostgreSQL-Quelldatenbank finden Sie unter den folgenden Ressourcen:
> * [Behandlung von Amazon RDS-Verbindungsproblemen](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Troubleshooting.html#CHAP_Troubleshooting.Connecting)
> * [Wie behebe ich Probleme beim Herstellen einer Verbindung mit meiner Amazon RDS-Datenbankinstanz?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="aws-rds-sql-server"></a>AWS RDS SQL Server

Potenzielle Probleme im Zusammenhang mit der Verbindung mit einer AWS RDS SQL Server-Quelldatenbank und deren Behandlung werden in der folgenden Tabelle aufgeführt.

| Fehler         | Ursache und Problembehandlungsdetails |
| ------------- | ------------- |
| **Fehler53**: SQL-Verbindungsfehler. Netzwerkbezogener oder instanzspezifischer Fehler beim Herstellen einer Verbindung mit SQL Server. Der Server wurde nicht gefunden, oder auf ihn konnte nicht zugegriffen werden. Stellen Sie sicher, dass der Instanzname richtig ist und dass SQL Server für Remoteverbindungen konfiguriert ist. (Anbieter: Named Pipes-Anbieter, Fehler: 40 - Es konnte keine Verbindung mit dem SQL-Server geöffnet werden. | Dieser Fehler tritt auf, wenn der Dienst keine Verbindung mit dem Quellserver herstellen kann. Um das Problem zu beheben, verwenden Sie die in der Anmerkung unter dieser Tabelle aufgeführten Problembehandlungsdokumente, und versuchen Sie es dann erneut. |
| **Fehler 18456**: Fehler bei der Anmeldung. Fehler bei der Anmeldung für den Benutzer "{benutzer}". | Dieser Fehler tritt auf, wenn der Dienst keine Verbindung mit der Quelldatenbank mit den angegebenen T-SQL-Anmeldeinformationen herstellen kann. Um das Problem zu beheben, überprüfen Sie die eingegebenen Anmeldeinformationen. Sie können auch [MSSQLSERVER_18456](/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error?view=sql-server-2017) oder die in der Anmerkung unter dieser Tabelle aufgeführten Problembehandlungsdokumente verwenden und es erneut versuchen. |
| **Fehler 87**: Verbindungszeichenfolge ist ungültig. Netzwerkbezogener oder instanzspezifischer Fehler beim Herstellen einer Verbindung mit SQL Server. Der Server wurde nicht gefunden, oder auf ihn kann nicht zugegriffen werden. Stellen Sie sicher, dass der Instanzname richtig ist und dass SQL Server für Remoteverbindungen konfiguriert ist. (Anbieter: SQL-Netzwerkschnittstellen, Fehler: 25 - Verbindungszeichenfolge ist ungültig.) | Dieser Fehler tritt auf, wenn der Dienst aufgrund einer ungültigen Verbindungszeichenfolge keine Verbindung mit dem Quellserver herstellen kann. Um das Problem zu beheben, überprüfen Sie, ob die bereitgestellte Verbindungszeichenfolge gültig ist. Wenn das Problem weiterhin besteht, verwenden Sie die in der Anmerkung unter dieser Tabelle aufgeführten Problembehandlungsdokumente, und versuchen Sie es dann erneut. |
| **Fehler: Serverzertifikat nicht vertrauenswürdig.** Eine Verbindung mit dem Server wurde erfolgreich hergestellt, aber dann trat während des Anmeldevorgangs ein Fehler auf. (Anbieter: SSL-Anbieter, Fehler: 0 - Die Zertifikatvertrauenskette wurde von einer Zertifizierungsstelle ausgestellt, die nicht vertrauenswürdig ist.) | Dieser Fehler tritt auf, wenn das verwendete Zertifikat nicht vertrauenswürdig ist. Um das Problem zu beheben, müssen Sie ein Zertifikat ermitteln, das als vertrauenswürdig eingestuft werden kann, und es dann auf dem Server aktivieren. Alternativ können Sie die Option „Zertifikat vertrauen“ beim Herstellen der Verbindung auswählen. Führen Sie diese Aktion nur aus, wenn Sie mit dem verwendeten Zertifikat vertraut sind und Sie ihm vertrauen. <br> TLS-Verbindungen, die mit einem selbstsignierten Zertifikat verschlüsselt sind, bieten keine hohe Sicherheit. Sie sind für Man-in-the-Middle-Angriffe anfällig. Verlassen Sie sich in einer Produktionsumgebung oder auf Servern, die mit dem Internet verbunden sind, nicht auf TLS-Verbindungen, die selbstsignierte Zertifikate verwenden. <br> Weitere Informationen finden Sie unter [Verwenden von SSL mit einer Microsoft SQL Server-Datenbank-Instanz](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/SQLServer.Concepts.General.SSL.Using.html) oder [Tutorial: Migrieren von RDS SQL Server zu Azure SQL mit DMS](./tutorial-rds-sql-server-azure-sql-and-managed-instance-online.md#prerequisites). |
| **Fehler 300**: Benutzer verfügt nicht über die erforderlichen Berechtigungen. Berechtigung SERVERSTATUS ANZEIGEN wurde für das Objekt "{server}", Datenbank "{datenbank}" verweigert. | Dieser Fehler tritt auf, wenn Benutzer keine Berechtigung zum Ausführen der Migration besitzt. Weitere Informationen zum Beheben des Problems finden Sie unter [Gewähren von Serverberechtigungen: Transact-SQL](/sql/t-sql/statements/grant-server-permissions-transact-sql?view=sql-server-2017) oder im [Tutorial: Migrieren von RDS SQL Server zu Azure SQL mit DMS](./tutorial-rds-sql-server-azure-sql-and-managed-instance-online.md#prerequisites). |

> [!NOTE]
> Weitere Informationen zum Behandeln von Problemen im Zusammenhang mir dem Herstellen einer Verbindung mit einem AWS RDS SQL-Quellserver finden Sie unter den folgenden Ressourcen:
>
> * [Beheben von Fehlern bei der Konnektivität mit SQL Server](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server)
> * [Wie behebe ich Probleme beim Herstellen einer Verbindung mit meiner Amazon RDS-Datenbankinstanz?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="known-issues"></a>Bekannte Probleme

* [Bekannte Probleme/Migrationseinschränkungen bei Onlinemigrationen zu Azure SQL-Datenbank](./known-issues-azure-sql-online.md)
* [Bekannte Problemen/Migrationseinschränkungen bei Onlinemigrationen zu Azure Database for MySQL](./known-issues-azure-mysql-online.md)
* [Bekannte Problemen/Migrationseinschränkungen bei Onlinemigrationen zu Azure Database for PostgreSQL](./known-issues-azure-postgresql-online.md)

## <a name="next-steps"></a>Nächste Schritte

* Lesen Sie den Artikel [Azure Database Migration Service PowerShell](/powershell/module/azurerm.datamigration/?view=azurermps-6.13.0#data_migration).
* Lesen Sie den Artikel [Konfigurieren von Serverparametern in Azure Database for MySQL mit dem Azure-Portal](../mysql/howto-server-parameters.md).
* Lesen Sie den Artikel [Übersicht über die Voraussetzungen für die Verwendung von Azure Database Migration Service](./pre-reqs.md).
* Lesen Sie [Häufig gestellte Fragen zur Verwendung von Azure Database Migration Service](./faq.md).
