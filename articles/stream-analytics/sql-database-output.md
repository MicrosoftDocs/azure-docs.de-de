---
title: Azure SQL-Datenbank aus Azure Stream Analytics
description: In diesem Artikel wird Azure SQL-Datenbank als Ausgabe für Azure Stream Analytics beschrieben.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: ac2c9cb1710c4b4f67ba2aa06707d08cc45d4907
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96459243"
---
# <a name="azure-sql-database-output-from-azure-stream-analytics"></a>Azure SQL-Datenbank aus Azure Stream Analytics

Sie können [Azure SQL-Datenbank](https://azure.microsoft.com/services/sql-database/) als Ausgabe für relationale Daten oder für Anwendungen verwenden, die auf Inhalten aufsetzen, die in einer relationalen Datenbank gehostet werden. Azure Stream Analytics-Aufträge schreiben Daten in eine vorhandene Tabelle in SQL-Datenbank. Das Tabellenschema muss genau den Feldern und deren Typen in der Ausgabe Ihres Auftrags entsprechen. Sie können auch [Azure Synapse Analytics](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) als Ausgabe über die Ausgabeoption „SQL-Datenbank“ angeben. Weitere Informationen zu Möglichkeiten zur Verbesserung des Schreibdurchsatzes finden Sie im Artikel [Stream Analytics mit Azure SQL-Datenbank als Ausgabe](stream-analytics-sql-output-perf.md).

Sie können auch eine [verwaltete Azure SQL-Instanz](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md) als Ausgabe verwenden. Sie müssen einen [öffentlichen Endpunkt in der verwalteten SQL-Instanz konfigurieren](../azure-sql/managed-instance/public-endpoint-configure.md) und dann in Azure Stream Analytics manuell die folgenden Einstellungen konfigurieren. Für den virtuellen Azure-Computer mit SQL Server und einer angefügten Datenbank wird ebenfalls das manuelle Konfigurieren der folgenden Einstellungen unterstützt.

## <a name="output-configuration"></a>Ausgabekonfiguration

Die folgende Tabelle enthält die Eigenschaftennamen und die entsprechenden Beschreibungen zum Erstellen einer SQL-Datenbank-Ausgabe.

| Eigenschaftenname | BESCHREIBUNG |
| --- | --- |
| Ausgabealias |Ein Anzeigename, der in Abfragen verwendet wird, um die Abfrageausgabe an diese Datenbank weiterzuleiten. |
| Datenbank | Der Name der Datenbank, an die Sie die Ausgabe senden. |
| Servername | Der Name der logischen SQL Server-Instanz oder der verwalteten Instanz. Für eine verwaltete SQL-Instanz muss der Port 3342 angegeben werden. Beispiel: *sampleserver.public.database.windows.net,3342* |
| Username | Der Benutzername, der Schreibzugriff auf die Datenbank hat. Stream Analytics unterstützt nur die SQL-Authentifizierung. |
| Kennwort | Das Kennwort zum Herstellen einer Verbindung mit der Datenbank |
| Tabelle | Der Name der Tabelle, in die die Ausgabe geschrieben wird. Beim Tabellennamen wird die Groß- und Kleinschreibung beachtet. Das Schema dieser Tabelle sollte genau der Anzahl der Felder und deren Typen entsprechen, die Ihre Auftragsausgabe generiert. |
|Erben des Partitionsschemas| Eine Option zum Erben des Partitionierungsschemas Ihres vorherigen Abfrageschrittes, um die vollständig parallele Topologie mit mehreren in die Tabelle Schreibenden zu aktivieren. Weitere Informationen finden Sie unter [Azure Stream Analytics-Ausgabe an Azure SQL-Datenbank](stream-analytics-sql-output-perf.md).|
|Max Batch Count| Der empfohlene obere Grenzwert für die Anzahl der Sätze, die mit jeder Transaktion zum Masseneinfügen gesendet werden.|

Es gibt zwei Adapter, die eine Ausgabe von Azure Stream Analytics an Azure Synapse Analytics ermöglichen: SQL-Datenbank und Azure Synapse. Es wird empfohlen, den Azure Synapse Analytics-Adapter anstelle des SQL-Datenbank-Adapters auszuwählen, wenn eine der folgenden Bedingungen zutrifft:

* **Durchsatz**: Wenn der erwartete Durchsatz jetzt oder in Zukunft über 10 MB/s liegt, verwenden Sie die Azure Synapse-Ausgabeoption, um eine bessere Leistung zu erzielen.

* **Eingabepartitionen**: Wenn Sie über acht oder mehr Eingabepartitionen verfügen, verwenden Sie die Azure Synapse-Ausgabeoption, um das horizontale Skalieren zu verbessern.

## <a name="partitioning"></a>Partitionierung

Die Partitionierung muss aktiviert werde und basiert auf der PARTITION BY-Klausel in der Abfrage. Wenn die Option „Partitionierung erben“ aktiviert ist, wird die Eingabepartitionierung für [vollständig parallelisierbare Abfragen](stream-analytics-scale-jobs.md) verwendet. Weitere Informationen zum Erzielen einer höheren Durchsatzleistung beim Laden von Daten in Azure SQL-Datenbank finden Sie unter [Azure Stream Analytics-Ausgabe an Azure SQL-Datenbank](stream-analytics-sql-output-perf.md).

## <a name="output-batch-size"></a>Ausgabebatchgröße

Die maximale Nachrichtengröße kann über die Option **Maximal zulässige Batchanzahl** konfiguriert werden. Der standardmäßige Höchstwert beträgt 10.000, der standardmäßige Mindestwert 100 Zeilen pro Masseneinfügungsvorgang. Weitere Informationen finden Sie unter [Azure SQL-Limits](../azure-sql/database/resource-limits-logical-server.md). Jeder Batch wird zunächst mit maximaler Batchanzahl als Massenvorgang eingefügt. Der Batch wird in der Mitte (bis zur minimalen Batchanzahl) basierend auf wiederholbaren Fehlern aus SQL unterteilt.

## <a name="next-steps"></a>Nächste Schritte

* [Schnellstart: Erstellen eines Stream Analytics-Auftrags mithilfe des Azure-Portals](stream-analytics-quick-create-portal.md)
* [Schnellstart: Erstellen eines Azure Stream Analytics-Auftrags mit der Azure CLI](quick-create-azure-cli.md)
* [Schnellstart: Erstellen eines Azure Stream Analytics-Auftrags mithilfe einer ARM-Vorlage](quick-create-azure-resource-manager.md)
* [Schnellstart: Erstellen eines Stream Analytics-Auftrags mit Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Schnellstart: Erstellen eines Azure Stream Analytics-Auftrags mithilfe von Visual Studio](stream-analytics-quick-create-vs.md)
* [Schnellstart: Erstellen eines Azure Stream Analytics-Auftrags in Visual Studio Code](quick-create-visual-studio-code.md)