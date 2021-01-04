---
title: Behandlung von Konnektivitätsproblemen
description: Behandlung von Konnektivitätsproblemen im dedizierten SQL-Pool (früher SQL DW)
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/27/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse, devx-track-csharp
ms.openlocfilehash: ea99c2ce1963ec58649fd4c2fbb4d98768da8c6f
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96447982"
---
# <a name="troubleshooting-connectivity-issues-in-dedicated-sql-pool-formerly-sql-dw"></a>Behandlung von Konnektivitätsproblemen im dedizierten SQL-Pool (früher SQL DW)

Dieser Artikel enthält allgemeine Problembehandlungsverfahren für das Herstellen von Verbindungen mit Ihrer Datenbank im dedizierten SQL-Pool (früher SQL DW).

## <a name="check-service-availability"></a>Überprüfen der Verfügbarkeit des Diensts

Überprüfen Sie, ob der Dienst verfügbar ist. Navigieren Sie im Azure-Portal zu dem dedizierten SQL-Pool (früher SQL DW), mit dem Sie eine Verbindung herstellen möchten. Klicken Sie im Inhaltsverzeichnis auf der linken Seite auf **Diagnose und Problembehandlung**.

![Auswählen der Ressourcenintegrität](./media/sql-data-warehouse-troubleshoot-connectivity/diagnostics-link.png)

Der Status Ihres dedizierten SQL-Pools (früher SQL DW) wird hier angezeigt. Wenn der Dienst nicht als **Verfügbar** angezeigt wird, sind weitere Handlungen erforderlich.

![Dienst verfügbar](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health.png)

Wenn die Ressourcenintegrität angibt, dass Ihre Instanz des dedizierten SQL-Pools (früher SQL DW) angehalten wurde oder skaliert wird, führen Sie die Anleitungen zum Fortsetzen Ihrer Instanz aus.

![Der Screenshot zeigt eine Instanz des dedizierten SQL-Pools, der angehalten wurde oder skaliert wird.](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health-pausing.png)
Hier finden Sie weitere Informationen zu Resource Health.

## <a name="check-for-paused-or-scaling-operation"></a>Überprüfen auf angehaltene Vorgänge oder Skalierungen

Sehen Sie im Portal nach, ob Ihre Instanz des dedizierten SQL-Pools (früher SQL DW) angehalten wurde oder skaliert wird.

![Screenshot, der zeigt, wie Sie überprüfen können, ob ein Data Warehouse angehalten wurde.](./media/sql-data-warehouse-troubleshoot-connectivity/overview-paused.png)

Wenn Sie feststellen, dass der Dienst pausiert wurde oder skaliert wird, überprüfen Sie, ob dies im Rahmen eines Wartungszeitplans geschieht. In der *Übersicht* zu Ihrem dedizierten SQL-Pool (früher SQL DW) im Portal wird der ausgewählte Wartungszeitplan angezeigt.

![Übersicht über den Wartungszeitplan](./media/sql-data-warehouse-troubleshoot-connectivity/overview-maintance-schedule.png)

Kontaktieren Sie andernfalls Ihren IT-Administrator, um auszuschließen, dass es sich bei der Wartung um ein geplantes Ereignis handelt. Führen Sie zur Fortsetzung der Instanz des dedizierten SQL-Pools (früher SQL DW) [diese Schritte](pause-and-resume-compute-portal.md) aus.

## <a name="check-your-firewall-settings"></a>Überprüfen der Firewalleinstellungen

Die Datenbank im dedizierten SQL-Pool (früher SQL DW) kommuniziert über Port 1433.  Wenn Sie versuchen, eine Verbindung über ein Unternehmensnetzwerk herzustellen, wird ausgehender Datenverkehr über Port 1433 von der Firewall Ihres Netzwerks unter Umständen nicht zugelassen. In diesem Fall können Sie nur dann eine Verbindung mit Ihrem [logischen Server](../../azure-sql/database/logical-servers.md) herstellen, wenn Ihre IT-Abteilung Port 1433 öffnet. [Hier](../../azure-sql/database/firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#create-and-manage-ip-firewall-rules) finden Sie weitere Informationen zu den Firewallkonfigurationen.

## <a name="check-your-vnetservice-endpoint-settings"></a>Überprüfen der Einstellungen für VNETs und Dienstendpunkte

Wenn die Fehler 40914 und 40615 bei Ihnen auftreten, finden Sie in diesem Artikel die [Fehlerbeschreibung und Problembehebung](../../azure-sql/database/vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#errors-40914-and-40615).

## <a name="check-for-the-latest-drivers"></a>Überprüfen der Treiber auf Aktualität

### <a name="software"></a>Software

Stellen Sie sicher, dass Sie die neuesten Tools verwenden, um eine Verbindung mit Ihrem dedizierten SQL-Pool (früher SQL DW) herzustellen:

- SSMS
- Azure Data Studio
- SQL Server Data Tools (Visual Studio)

### <a name="drivers"></a>Treiber

Stellen Sie sicher, dass Sie die aktuelle Version der Treiber verwenden.  Wenn Sie ältere Versionen der Treiber verwenden, kann das zu unerwartetem Verhalten führen, da diese keine neuen Features unterstützen.

- [ODBC](/sql/connect/odbc/download-odbc-driver-for-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [JDBC](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [OLE DB](/sql/connect/oledb/download-oledb-driver-for-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [PHP](/sql/connect/php/download-drivers-php-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="check-your-connection-string"></a>Überprüfen der Verbindungszeichenfolge

Überprüfen Sie, ob die Verbindungszeichenfolgen ordnungsgemäß eingerichtet sind.  Im Anschluss finden Sie einige Beispiele.  Im folgenden Artikel finden Sie weitere Informationen zu [Verbindungszeichenfolgen](sql-data-warehouse-connection-strings.md).

ADO.NET-Verbindungszeichenfolge

```csharp
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

ODBC-Verbindungszeichenfolge

```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

PHP-Verbindungszeichenfolge

```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.database.windows.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

Verbindungszeichenfolge für JDBC

```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

## <a name="intermittent-connection-issues"></a>Zeitweilige Verbindungsprobleme

Überprüfen Sie, ob die Auslastung des Servers hoch ist und sich viele Anforderungen in der Warteschlange befinden. Möglicherweise müssen Sie Ihren dedizierten SQL-Pool (früher SQL DW) für zusätzliche Ressourcen hochskalieren.

## <a name="common-error-messages"></a>Häufige Fehlermeldungen

In diesem Artikel finden Sie die [Fehlerbeschreibung und Problembehebung](../../azure-sql/database/vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#errors-40914-and-40615) zu den Fehlern 40914 und 40615.

## <a name="still-having-connectivity-issues"></a>Treten weiterhin Konnektivitätsprobleme auf?

Erstellen Sie ein [Supportticket](sql-data-warehouse-get-started-create-support-ticket.md), damit das entsprechende Team Sie bei der Problembehebung unterstützen kann.
