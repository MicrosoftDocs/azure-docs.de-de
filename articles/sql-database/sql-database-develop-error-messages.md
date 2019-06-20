---
title: SQL-Fehlercodes – Datenbankverbindungsfehler | Microsoft Docs
description: 'Erfahren Sie mehr über SQL-Fehlercodes für SQL-Datenbank-Clientanwendungen, beispielsweise zu häufigen Datenbankverbindungsfehlern, Datenbankkopiefehlern und allgemeinen Fehlern. '
keywords: SQL-Fehlercodes,Zugriff auf SQL,Datenbankverbindungsfehler,SQL-Fehlercodes
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 03/06/2019
ms.openlocfilehash: 2682f98628f3c1cf22a2c3767f52bedbc148fa62
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60723491"
---
# <a name="sql-error-codes-for-sql-database-client-applications-database-connection-errors-and-other-issues"></a>SQL-Fehlercodes für SQL-Datenbank-Clientanwendungen: Datenbankverbindungsfehler und andere Probleme

In diesem Artikel werden SQL-Fehlercodes für SQL-Datenbank-Clientanwendungen aufgeführt, darunter Datenbankverbindungsfehler, vorübergehende Fehler, Fehler zur Ressourcenkontrolle, Datenbankkopierfehler, Fehler in Zusammenhang mit Pools für elastische Datenbanken und andere. Die meisten Kategorien gelten speziell für Azure SQL-Datenbank und nicht für Microsoft SQL Server. Siehe auch [Systemfehlermeldungen](https://technet.microsoft.com/library/cc645603(v=sql.105).aspx).

## <a name="database-connection-errors-transient-errors-and-other-temporary-errors"></a>Datenbankverbindungsfehler, vorübergehende Fehler und andere temporäre Fehler

Die folgende Tabelle enthält die SQL-Fehlercodes für Fehler bei Verbindungsverlust und andere vorübergehende Fehler, die auftreten können, wenn Ihre Anwendung versucht, auf SQL-Datenbank zuzugreifen. Tutorials für die ersten Schritte zum Herstellen einer Verbindung mit Azure SQL-Datenbank finden Sie unter [Herstellen einer Verbindung mit Azure SQL-Datenbank](sql-database-libraries.md).

### <a name="most-common-database-connection-errors-and-transient-fault-errors"></a>Häufigste Datenbankverbindungsfehler und vorübergehende Fehler

Die Azure-Infrastruktur verfügt über die Möglichkeit, Server dynamisch neu zu konfigurieren, wenn hohe Workloads im SQL-Datenbankdienst auftreten.  Dieses dynamische Verhalten führt jedoch u.U. dazu, dass die Verbindung zwischen Ihrem Clientprogramm und der SQL-Datenbank getrennt wird. Diese Art Fehlerbedingung wird als *vorübergehender Fehler* bezeichnet.

Es wird dringend empfohlen, das Clientprogramm mit einer Wiederholungslogik zu versehen, sodass erneut eine Verbindung hergestellt werden kann, nachdem sich der vorübergehende Fehler selbst korrigiert hat.  Es wird empfohlen, dass vor dem ersten Wiederholungsversuch eine Verzögerungszeit von fünf Sekunden verwendet wird. Wiederholungsversuche nach weniger als fünf Sekunden können den Clouddienst überfordern. Für jeden nachfolgenden Wiederholungsversuch sollte die Verzögerung exponentiell steigen, bis zu einem Maximum von 60 Sekunden.

Vorübergehende Fehler machen sich in der Regel in Form einer der folgenden Fehlermeldungen von Ihren Clientprogrammen bemerkbar:

* Die &lt;DB-Name&gt;-Datenbank auf Server "&lt;Azure-Instanz&gt;" ist zurzeit nicht verfügbar. Wiederholen Sie den Verbindungsversuch später. Falls das Problem weiterhin besteht, wenden Sie sich an den Kundensupport und geben als Ablaufverfolgungs-ID der Sitzung "&lt;Sitzungs-ID&gt;" an.
* Die &lt;DB-Name&gt;-Datenbank auf Server "&lt;Azure-Instanz&gt;" ist zurzeit nicht verfügbar. Wiederholen Sie den Verbindungsversuch später. Falls das Problem weiterhin besteht, wenden Sie sich an den Kundensupport und geben als Ablaufverfolgungs-ID der Sitzung "&lt;Sitzungs-ID&gt;" an. (Microsoft SQL Server, Fehler: 40613)
* Eine vorhandene Verbindung wurde erzwungenermaßen vom Remotehost geschlossen.
* System.Data.Entity.Core.EntityCommandExecutionException: Fehler beim Ausführen der Befehlsdefinition. Details siehe innere Ausnahme. ---> System.Data.SqlClient.SqlException: Beim Empfangen von Ergebnissen vom Server ist ein Fehler auf Übertragungsebene aufgetreten. (Anbieter: Sitzungsanbieter, Fehler: 19 – Physische Verbindung kann nicht verwendet werden)
* Ein Verbindungsversuch mit einer sekundären Datenbank war nicht erfolgreich, da die Konfiguration der Datenbank gerade geändert und neue Seiten angewandt werden, während eine aktive Transaktion in der primären Datenbank ausgeführt wird. 

Codebeispiele zur Wiederholungslogik finden Sie unter:

* [Connection Libraries for SQL Database and SQL Server (Verbindungsbibliotheken für SQL-Datenbanken und SQL Server, in englischer Sprache)](sql-database-libraries.md) 
* [Maßnahmen zum Behandeln von Verbindungsfehlern und vorübergehenden Fehlern in SQL-Datenbank](sql-database-connectivity-issues.md)

Eine Erörterung der *Sperrfrist* für Clients, die ADO.NET verwenden, finden Sie unter [SQL Server-Verbindungspooling (ADO.NET)](https://msdn.microsoft.com/library/8xx3tyca.aspx).

### <a name="transient-fault-error-codes"></a>Fehlercodes für vorübergehende Fehler

Die folgenden Fehler sind vorübergehend, und in der Anwendungslogik sollte ein neuer Versuch unternommen werden: 

| Fehlercode | Severity | BESCHREIBUNG |
| ---:| ---:|:--- |
| 4060 |16 |Die von der Anmeldung angeforderte „%.&#x2a;ls“-Datenbank kann nicht geöffnet werden. Fehler bei der Anmeldung. Weitere Informationen finden Sie unter [Fehler 4000 bis 4999](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors#errors-4000-to-4999).|
| 40197 |17 |Dienstfehler beim Verarbeiten Ihrer Anforderung. Wiederholen Sie den Vorgang. Fehlercode %d.<br/><br/>Sie erhalten diesen Fehler, wenn der Dienst aufgrund von Software- oder Hardwareupgrades, Hardwarefehlern oder sonstigen Failoverproblemen ausgefallen ist. Der Fehlercode (%d), der in der Meldung zum Fehler 40197 enthalten ist, liefert weitere Informationen zur Art des aufgetretenen Fehlers oder Failovers. Beispiele für Fehlercodes, die in die Meldung zum Fehler 40197 eingebettet sind, lauten 40020, 40143, 40166 und 40540.<br/><br/>Wenn Sie erneut eine Verbindung mit Ihrem SQL-Datenbank-Server herstellen, werden Sie automatisch mit einer intakten Kopie Ihrer Datenbank verbunden. Ihre Anwendung muss den Fehler 40197 abfangen, den für die Problembehandlung in der Meldung enthaltenen Fehlercode (%d) protokollieren und versuchen, eine neue Verbindung mit SQL-Datenbank herzustellen, bis die Ressourcen verfügbar sind, damit Ihre Verbindung wiederhergestellt wird. Weitere Informationen finden Sie unter [Vorübergehende Fehler](sql-database-connectivity-issues.md#transient-errors-transient-faults).|
| 40501 |20 |Der Dienst ist derzeit ausgelastet. Wiederholen Sie die Anforderung in 10 Sekunden. Vorgangs-ID: %ls. Code: %d. Weitere Informationen finden Sie unter <br/>&bull; &nbsp;[Ressourceneinschränkungen für Datenbankserver](sql-database-resource-limits-database-server.md)<br/>&bull; &nbsp;[DTU-basierte Einschränkungen für einzelne Datenbanken](sql-database-service-tiers-dtu.md)<br/>&bull; &nbsp;[DTU-basierte Einschränkungen für Pools für elastische Datenbanken](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[V-Kern-basierte Einschränkungen für einzelne Datenbanken](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull; &nbsp;[V-Kern-basierte Einschränkungen für Pools für elastische Datenbanken](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[Ressourceneinschränkungen für verwaltete Instanzen](sql-database-managed-instance-resource-limits.md)|
| 40613 |17 |Die „%.&#x2a;ls“-Datenbank auf Server „%.&#x2a;ls“ ist zurzeit nicht verfügbar. Wiederholen Sie den Verbindungsversuch später. Falls das Problem weiterhin besteht, wenden Sie sich an den Kundensupport und geben als Ablaufverfolgungs-ID der Sitzung „%.&#x2a;ls“ an.<br/><br/> Dieser Fehler kann auftreten, wenn bereits eine dedizierte Administratorverbindung (DAC) zur Datenbank besteht. Weitere Informationen finden Sie unter [Vorübergehende Fehler](sql-database-connectivity-issues.md#transient-errors-transient-faults).|
| 49918 |16 |Anforderung kann nicht verarbeitet werden. Zum Verarbeiten der Anforderung sind nicht genügend Ressourcen vorhanden.<br/><br/>Der Dienst ist derzeit ausgelastet. Versuchen Sie die Anforderung später erneut. Weitere Informationen finden Sie unter <br/>&bull; &nbsp;[Ressourceneinschränkungen für Datenbankserver](sql-database-resource-limits-database-server.md)<br/>&bull; &nbsp;[DTU-basierte Einschränkungen für einzelne Datenbanken](sql-database-service-tiers-dtu.md)<br/>&bull; &nbsp;[DTU-basierte Einschränkungen für Pools für elastische Datenbanken](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[V-Kern-basierte Einschränkungen für einzelne Datenbanken](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull; &nbsp;[V-Kern-basierte Einschränkungen für Pools für elastische Datenbanken](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[Ressourceneinschränkungen für verwaltete Instanzen](sql-database-managed-instance-resource-limits.md) |
| 49919 |16 |Die Erstellung oder Aktualisierung der Anforderung kann nicht verarbeitet werden. Für das Abonnement „%ld“ werden derzeit zu viele Erstell- oder Aktualisierungsvorgänge ausgeführt.<br/><br/>Der Dienst ist mit der Verarbeitung mehrerer Erstell- oder Aktualisierungsvorgänge für Ihr Abonnement oder Ihren Server ausgelastet. Zur Ressourcenoptimierung werden Anforderungen derzeit blockiert. Fragen Sie [sys.dm_operation_status](https://msdn.microsoft.com/library/dn270022.aspx) auf ausstehende Vorgänge ab. Warten Sie, bis ausstehende Erstellungs- oder Aktualisierungsanforderungen abgeschlossen sind, oder löschen Sie eine Ihrer ausstehenden Anforderungen, und wiederholen Sie die Anforderung später. Weitere Informationen finden Sie unter <br/>&bull; &nbsp;[Ressourceneinschränkungen für Datenbankserver](sql-database-resource-limits-database-server.md)<br/>&bull; &nbsp;[DTU-basierte Einschränkungen für einzelne Datenbanken](sql-database-service-tiers-dtu.md)<br/>&bull; &nbsp;[DTU-basierte Einschränkungen für Pools für elastische Datenbanken](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[V-Kern-basierte Einschränkungen für einzelne Datenbanken](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull; &nbsp;[V-Kern-basierte Einschränkungen für Pools für elastische Datenbanken](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[Ressourceneinschränkungen für verwaltete Instanzen](sql-database-managed-instance-resource-limits.md) |
| 49920 |16 |Anforderung kann nicht verarbeitet werden. Für das Abonnement „%ld“ werden derzeit zu viele Vorgänge ausgeführt.<br/><br/>Der Dienst ist mit der Verarbeitung mehrerer Vorgänge für dieses Abonnement ausgelastet. Zur Ressourcenoptimierung werden Anforderungen derzeit blockiert. Fragen Sie [sys.dm_operation_status](https://msdn.microsoft.com/library/dn270022.aspx) auf den Vorgangsstatus ab. Warten Sie, bis ausstehende Anforderungen abgeschlossen sind, oder löschen Sie eine Ihrer ausstehenden Anforderungen, und wiederholen Sie die Anforderung später. Weitere Informationen finden Sie unter <br/>&bull; &nbsp;[Ressourceneinschränkungen für Datenbankserver](sql-database-resource-limits-database-server.md)<br/>&bull; &nbsp;[DTU-basierte Einschränkungen für einzelne Datenbanken](sql-database-service-tiers-dtu.md)<br/>&bull; &nbsp;[DTU-basierte Einschränkungen für Pools für elastische Datenbanken](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[V-Kern-basierte Einschränkungen für einzelne Datenbanken](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull; &nbsp;[V-Kern-basierte Einschränkungen für Pools für elastische Datenbanken](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[Ressourceneinschränkungen für verwaltete Instanzen](sql-database-managed-instance-resource-limits.md) |
| 4221 |16 |Fehler bei der Anmeldung bei lesbarem sekundärem Replikat aufgrund einer zu langen Wartezeit auf "HADR_DATABASE_WAIT_FOR_TRANSITION_TO_VERSIONING". Das Replikat steht zur Anmeldung nicht zur Verfügung, weil Zeilenversionen für Transaktionen fehlen, die beim Neustarten des Replikats in Verarbeitung waren. Dieses Problem kann durch einen Rollback oder durch einen Commit der aktiven Transaktionen auf dem primären Replikat gelöst werden. Vorkommen dieser Bedingung können durch Vermeiden langer Schreibtransaktionen auf dem primären Replikat minimiert werden. |

## <a name="database-copy-errors"></a>Fehler beim Kopieren von Datenbanken

Die folgenden Fehler können beim Kopieren einer Datenbank in Azure SQL-Datenbank auftreten. Weitere Informationen finden Sie unter [Kopieren einer Azure SQL-Datenbank](sql-database-copy.md).

| Fehlercode | Severity | BESCHREIBUNG |
| ---:| ---:|:--- |
| 40635 |16 |Der Client mit der IP-Adresse „%.&#x2a;ls“ ist vorübergehend deaktiviert. |
| 40637 |16 |Das Kopieren von Datenbanken ist derzeit deaktiviert. |
| 40561 |16 |Fehler beim Kopieren der Datenbank. Die Quell- oder die Zieldatenbank ist nicht vorhanden. |
| 40562 |16 |Fehler beim Kopieren der Datenbank. Die Quelldatenbank wurde gelöscht. |
| 40563 |16 |Fehler beim Kopieren der Datenbank. Die Zieldatenbank wurde gelöscht. |
| 40564 |16 |Interner Fehler beim Kopieren der Datenbank. Löschen Sie die Zieldatenbank, und wiederholen Sie den Vorgang. |
| 40565 |16 |Fehler beim Kopieren der Datenbank. Es darf jeweils nur eine Datenbankkopie von derselben Quelle erstellt werden. Löschen Sie die Zieldatenbank, und versuchen Sie es später erneut. |
| 40566 |16 |Interner Fehler beim Kopieren der Datenbank. Löschen Sie die Zieldatenbank, und wiederholen Sie den Vorgang. |
| 40567 |16 |Interner Fehler beim Kopieren der Datenbank. Löschen Sie die Zieldatenbank, und wiederholen Sie den Vorgang. |
| 40568 |16 |Fehler beim Kopieren der Datenbank. Die Quelldatenbank ist nicht mehr verfügbar. Löschen Sie die Zieldatenbank, und wiederholen Sie den Vorgang. |
| 40569 |16 |Fehler beim Kopieren der Datenbank. Die Zieldatenbank ist nicht mehr verfügbar. Löschen Sie die Zieldatenbank, und wiederholen Sie den Vorgang. |
| 40570 |16 |Interner Fehler beim Kopieren der Datenbank. Löschen Sie die Zieldatenbank, und versuchen Sie es später erneut. |
| 40571 |16 |Interner Fehler beim Kopieren der Datenbank. Löschen Sie die Zieldatenbank, und versuchen Sie es später erneut. |

## <a name="resource-governance-errors"></a>Fehler bei der Ressourcenkontrolle

Die folgenden Fehler treten beim Arbeiten mit Azure SQL-Datenbank bei der übermäßigen Nutzung von Ressourcen auf. Beispiel:

* Eine Transaktion war zu lange geöffnet.
* Eine Transaktion enthält zu viele Sperren.
* Eine Anwendung verbraucht zu viel Arbeitsspeicher.
* Eine Anwendung verbraucht zu viel `TempDb` -Speicher.

Verwandte Themen:

* Weitere Informationen finden Sie unter
  * [Ressourceneinschränkungen für Datenbankserver](sql-database-resource-limits-database-server.md)
  * [DTU-basierte Einschränkungen für einzelne Datenbanken](sql-database-service-tiers-dtu.md)
  * [DTU-basierte Einschränkungen für Pools für elastische Datenbanken](sql-database-dtu-resource-limits-elastic-pools.md)
  * [V-Kern-basierte Einschränkungen für einzelne Datenbanken](sql-database-vcore-resource-limits-single-databases.md)
  * [V-Kern-basierte Einschränkungen für Pools für elastische Datenbanken](sql-database-vcore-resource-limits-elastic-pools.md)
  * [Ressourceneinschränkungen für verwaltete Instanzen](sql-database-managed-instance-resource-limits.md) 

| Fehlercode | Severity | BESCHREIBUNG |
| ---:| ---:|:--- |
| 10928 |20 |Ressourcen-ID: %d. Das %s-Limit für die Datenbank beträgt %d und wurde erreicht. Weitere Informationen finden Sie unter [Ressourcenlimits bei SQL-Datenbank für Einzel- und Pooldatenbanken](sql-database-resource-limits-database-server.md).<br/><br/>Mit der Ressourcen-ID wird die Ressource angegeben, für die das Limit erreicht wurde. Bei Arbeitsthreads lautet die Ressourcen-ID „1“. Bei Sitzungen lautet die Ressourcen-ID „2“.<br/><br/>Weitere Informationen zu diesem Fehler und zur Fehlerbehebung finden Sie unter: <br/>&bull; &nbsp;[Ressourceneinschränkungen für Datenbankserver](sql-database-resource-limits-database-server.md)<br/>&bull; &nbsp;[DTU-basierte Einschränkungen für einzelne Datenbanken](sql-database-service-tiers-dtu.md)<br/>&bull; &nbsp;[DTU-basierte Einschränkungen für Pools für elastische Datenbanken](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[V-Kern-basierte Einschränkungen für einzelne Datenbanken](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull; &nbsp;[V-Kern-basierte Einschränkungen für Pools für elastische Datenbanken](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[Ressourceneinschränkungen für verwaltete Instanzen](sql-database-managed-instance-resource-limits.md) |
| 10929 |20 |Ressourcen-ID: %d. Die %s-Mindestgarantie beträgt %d, der maximale Wert beträgt %d und die aktuelle Nutzung für die Datenbank beträgt %d. Der Server ist jedoch derzeit zu stark ausgelastet, um Anforderungen über %d für diese Datenbank zu unterstützen. Mit der Ressourcen-ID wird die Ressource angegeben, für die das Limit erreicht wurde. Bei Arbeitsthreads lautet die Ressourcen-ID „1“. Bei Sitzungen lautet die Ressourcen-ID „2“. Weitere Informationen finden Sie unter <br/>&bull; &nbsp;[Ressourceneinschränkungen für Datenbankserver](sql-database-resource-limits-database-server.md)<br/>&bull; &nbsp;[DTU-basierte Einschränkungen für einzelne Datenbanken](sql-database-service-tiers-dtu.md)<br/>&bull; &nbsp;[DTU-basierte Einschränkungen für Pools für elastische Datenbanken](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[V-Kern-basierte Einschränkungen für einzelne Datenbanken](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull; &nbsp;[V-Kern-basierte Einschränkungen für Pools für elastische Datenbanken](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[Ressourceneinschränkungen für verwaltete Instanzen](sql-database-managed-instance-resource-limits.md) <br/>Bitte versuchen Sie es andernfalls später noch einmal. |
| 40544 |20 |Das Datenbankkontingent wurde erreicht. Partitionieren oder löschen Sie Daten, löschen Sie Indizes, oder informieren Sie sich in der Dokumentation über mögliche Lösungen. Informationen zur Datenbankskalierung finden Sie unter [Skalieren der Ressourcen für einzelne Datenbanken](sql-database-single-database-scale.md) und [Skalieren der Ressourcen für Pools für elastische Datenbanken](sql-database-elastic-pool-scale.md).|
| 40549 |16 |Die Sitzung wird aufgrund einer Transaktion mit langer Laufzeit beendet. Verkürzen Sie die Transaktion. Weitere Informationen finden Sie unter [Gewusst wie: Verbessern der Leistung von SQL-Datenbankanwendungen mithilfe von Batchverarbeitung](sql-database-use-batching-to-improve-performance.md).|
| 40550 |16 |Die Sitzung wurde beendet, da zu viele Sperren abgerufen wurden. Reduzieren Sie die Anzahl der in einer einzelnen Transaktion gelesenen oder geänderten Zeilen. Weitere Informationen finden Sie unter [Gewusst wie: Verbessern der Leistung von SQL-Datenbankanwendungen mithilfe von Batchverarbeitung](sql-database-use-batching-to-improve-performance.md).|
| 40551 |16 |Die Sitzung wurde aufgrund übermäßiger `TEMPDB` -Auslastung beendet. Ändern Sie die Abfrage, um die Nutzung des temporären Tabellenbereichs zu verringern.<br/><br/>Wenn Sie temporäre Objekte verwenden, können Sie Speicherplatz in der `TEMPDB`-Datenbank sparen, indem Sie die temporären Objekte verwerfen, die von der Sitzung nicht mehr benötigt werden. Weitere Informationen zur Verwendung von „tempdb“ in SQL-Datenbank finden Sie unter [Tempdb-Datenbank in SQL-Datenbank](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).|
| 40552 |16 |Die Sitzung wurde aufgrund übermäßiger Verwendung des Speicherplatzes für das Transaktionsprotokoll beendet. Reduzieren Sie die Anzahl der in einer einzelnen Transaktion geänderten Zeilen. Weitere Informationen finden Sie unter [Gewusst wie: Verbessern der Leistung von SQL-Datenbankanwendungen mithilfe von Batchverarbeitung](sql-database-use-batching-to-improve-performance.md).<br/><br/>Versuchen Sie beim Durchführen von Masseneinfügungen mit dem Hilfsprogramm `bcp.exe` oder der `System.Data.SqlClient.SqlBulkCopy`-Klasse, die Option `-b batchsize` oder `BatchSize` zu verwenden, um die Anzahl der Zeilen zu beschränken, die bei jeder Transaktion auf den Server kopiert werden. Versuchen Sie es mit der Option `REBUILD WITH ONLINE = ON`, wenn Sie einen Index mit der `ALTER INDEX`-Anweisung neu erstellen. Informationen zu den Größen von Transaktionsprotokollen für das V-Kern-basierte Kaufmodell finden Sie unter: <br/>&bull; &nbsp;[V-Kern-basierte Einschränkungen für einzelne Datenbanken](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull; &nbsp;[V-Kern-basierte Einschränkungen für Pools für elastische Datenbanken](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[Ressourceneinschränkungen für verwaltete Instanzen](sql-database-managed-instance-resource-limits.md)|
| 40553 |16 |Die Sitzung wurde aufgrund übermäßiger Speicherauslastung beendet. Ändern Sie die Abfrage, damit weniger Zeilen verarbeitet werden.<br/><br/>Wenn Sie die Anzahl von `ORDER BY`- und `GROUP BY`-Vorgängen im Transact-SQL-Code reduzieren, verringern sich auch die Arbeitsspeicheranforderungen Ihrer Abfrage. Informationen zur Datenbankskalierung finden Sie unter [Skalieren der Ressourcen für einzelne Datenbanken](sql-database-single-database-scale.md) und [Skalieren der Ressourcen für Pools für elastische Datenbanken](sql-database-elastic-pool-scale.md).|

## <a name="elastic-pool-errors"></a>Fehler im Zusammenhang mit Pools für elastische Datenbanken

Die folgenden Fehler beziehen sich auf die Erstellung und Verwendung von Pools für elastische Datenbanken:

| Fehlercode | Severity | BESCHREIBUNG | Korrekturmaßnahme |
|:--- |:--- |:--- |:--- |
| 1132 | 17 |Der Speichergrenzwert des Pools für elastische Datenbanken wurde erreicht. Die Speicherauslastung für den Pool für elastische Datenbanken darf (%d) MB nicht überschreiten. Es wurde versucht, Daten in eine Datenbank zu schreiben, während die Speicherbegrenzung des Pools für elastische Datenbanken erreicht wurde. Informationen zu Ressourceneinschränkungen finden Sie unter: <br/>&bull; &nbsp;[DTU-basierte Einschränkungen für Pools für elastische Datenbanken](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[V-Kern-basierte Einschränkungen für Pools für elastische Datenbanken](sql-database-vcore-resource-limits-elastic-pools.md) <br/> |Erhöhen Sie nach Möglichkeit die DTUs des Pools für elastische Datenbanken und/oder fügen Sie ihm Speicher hinzu, um die Speicherkapazität zu erweitern, reduzieren Sie die Speichernutzung der einzelnen Datenbanken innerhalb des Pools für elastische Datenbanken, oder entfernen Sie Datenbanken aus dem Pools für elastische Datenbanken. Informationen zur Skalierung von Pools für elastische Datenbanken finden Sie unter [Skalieren von Ressourcen für Pools für elastische Datenbanken](sql-database-elastic-pool-scale.md).|
| 10929 | 16 |Die %s-Mindestgarantie beträgt %d, der maximale Wert beträgt %d und die aktuelle Nutzung für die Datenbank beträgt %d. Der Server ist jedoch derzeit zu stark ausgelastet, um Anforderungen über %d für diese Datenbank zu unterstützen. Informationen zu Ressourceneinschränkungen finden Sie unter: <br/>&bull; &nbsp;[DTU-basierte Einschränkungen für Pools für elastische Datenbanken](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[V-Kern-basierte Einschränkungen für Pools für elastische Datenbanken](sql-database-vcore-resource-limits-elastic-pools.md) <br/> Bitte versuchen Sie es andernfalls später noch einmal. DTU-/V-Kern-Mindestanzahl pro Datenbank; DTU-/V-Kern-Höchstanzahl pro Datenbank Die Gesamtanzahl der gleichzeitigen Worker (Anforderungen) in allen Datenbanken im Pool für elastische Datenbanken hat versucht, den Poolgrenzwert zu überschreiten. |Erhöhen Sie nach Möglichkeit die Anzahl von DTUs oder V-Kernen des Pools für elastische Datenbanken, um die Begrenzung für Worker zu steigern, oder entfernen Sie Datenbanken aus dem Pool für elastische Datenbanken. |
| 40844 | 16 |Datenbank „%Ls“ auf Server „%ls“ ist eine Datenbank der „%ls“-Edition in einem Pool für elastische Datenbanken und kann nicht über eine fortlaufende Kopierbeziehung verfügen.  |– |
| 40857 | 16 |Der Pool für elastische Datenbanken wurde für folgenden Server nicht gefunden: „%ls“, Name des Pools für elastische Datenbanken: „%ls“. Der angegebene Pool für elastische Datenbanken ist nicht auf dem angegebenen Server vorhanden. | Geben Sie einen gültigen Namen für den Pool für elastische Datenbanken an. |
| 40858 | 16 |Der Pool für elastische Datenbanken „%ls“ ist bereits auf folgendem Server vorhanden: „%ls“. Der angegebene Pool für elastische Datenbanken ist bereits auf dem angegebenen SQL-Datenbank-Server vorhanden. | Geben Sie einen neuen Namen für den Pool für elastische Datenbanken an. |
| 40859 | 16 |Der Pool für elastische Datenbanken unterstützt Dienstebene „%ls“ nicht. Die angegebene Dienstebene wird für die Bereitstellung von Pools für elastische Datenbanken nicht unterstützt. |Geben Sie die richtige Edition an, oder lassen Sie die Dienstebene leer, um die Standarddienstebene zu verwenden. |
| 40860 | 16 |Die Kombination aus dem Pool für elastische Datenbanken „%ls“ und Dienstziel „%ls“ ist ungültig. Der Pool für elastische Datenbanken und die Dienstebene können nur gemeinsam angegeben werden, wenn der Ressourcentyp „ElasticPool“ definiert ist. |Geben Sie die richtige Kombination aus Pool für elastische Datenbanken und Dienstebene an. |
| 40861 | 16 |Die Datenbankedition „%.*ls“ darf sich nicht von der Dienstebene des Pools für elastische Datenbanken („%.* ls“) unterscheiden. Die Datenbankedition unterscheidet sich von der Dienstebene des Pools für elastische Datenbanken. |Geben Sie keine Datenbankedition an, die sich von der Dienstebene des Pools für elastische Datenbanken unterscheidet.  Beachten Sie, dass die Datenbankedition nicht angegeben werden muss. |
| 40862 | 16 |Der Name des Pools für elastische Datenbanken muss angegeben werden, wenn das Dienstziel des Pools für elastische Datenbanken angegeben wurde. Das Dienstziel des Pools für elastische Datenbanken identifiziert einen Pool für elastische Datenbanken nicht eindeutig. |Geben Sie den Namen des Pools für elastische Datenbanken an, wenn Sie das Dienstziel des Pools für elastische Datenbanken verwenden. |
| 40864 | 16 |Die DTU-Anzahl für den Pool für elastische Datenbanken muss mindestens (%d) DTUs für die Dienstebene „%.*ls“ betragen. Es wurde versucht, eine DTU-Anzahl für den Pool für elastische Datenbanken unterhalb des unteren Grenzwerts festzulegen. |Legen Sie die DTU-Einstellung für den Pool für elastische Datenbanken mindestens auf die Untergrenze fest. |
| 40865 | 16 |Die DTU-Anzahl für den Pool für elastische Datenbanken darf höchstens (%d) DTUs für die Dienstebene „%.*ls“ betragen. Es wurde versucht, eine DTU-Anzahl für den Pool für elastische Datenbanken oberhalb des oberen Grenzwerts festzulegen. |Legen Sie die DTU-Einstellung für den Pool für elastische Datenbanken höchstens auf die Obergrenze fest. |
| 40867 | 16 |Die maximalen DTUs pro Datenbank müssen mindestens (%d) für Dienstebene "%.*ls" betragen. Es wurde versucht, eine maximale DTU-Anzahl pro Datenbank festzulegen, die unter der unterstützten Grenze liegt. | Sie sollten in Betracht ziehen, die Dienstebene des Pools für elastische Datenbanken zu verwenden, die die gewünschte Einstellung unterstützt. |
| 40868 | 16 |Die maximale DTU-Anzahl pro Datenbank kann nicht mehr als (%d) für Dienstebene "%.*ls" betragen. Es wurde versucht, eine maximale DTU-Anzahl pro Datenbank festzulegen, die über der unterstützten Grenze liegt. | Sie sollten in Betracht ziehen, die Dienstebene des Pools für elastische Datenbanken zu verwenden, die die gewünschte Einstellung unterstützt. |
| 40870 | 16 |Die minimale DTU-Anzahl pro Datenbank kann nicht mehr als (%d) für Dienstebene "%.*ls" betragen. Es wurde versucht, eine minimale DTU-Anzahl pro Datenbank festzulegen, die über der unterstützten Grenze liegt. | Sie sollten in Betracht ziehen, die Dienstebene des Pools für elastische Datenbanken zu verwenden, die die gewünschte Einstellung unterstützt. |
| 40873 | 16 |Die Anzahl der Datenbanken (%d) und minimalen DTUs pro Datenbank (%d) darf die DTU-Anzahl des Pools für elastische Datenbanken (%d) nicht überschreiten. Es wurde versucht, eine Mindestanzahl von DTUs für den Pool für elastische Datenbanken anzugeben, die die DTU-Anzahl des Pools für elastische Datenbanken überschreitet. | Sie sollten in Betracht ziehen, die Anzahl der DTUs im Pool für elastische Datenbanken zu erhöhen oder die Mindestanzahl der DTUs pro Datenbank zu verringern, oder Sie verringern die Anzahl der Datenbanken im Pool für elastische Datenbanken. |
| 40877 | 16 |Ein Pool für elastische Datenbanken kann nur gelöscht werden, wenn er keine Datenbanken enthält. Der Pool für elastische Datenbanken enthält eine oder mehrere Datenbanken und kann nicht gelöscht werden. |Entfernen Sie Datenbanken aus dem Pool für elastische Datenbanken, um ihn zu löschen. |
| 40881 | 16 |Der Pool für elastische Datenbanken „%.*ls“ hat den Grenzwert für die Anzahl an Datenbanken erreicht.  Der Grenzwert für die Datenbankanzahl im Pool für elastische Datenbanken darf (%d) für einen Pool für elastische Datenbanken mit (%d) DTUs nicht überschreiten. Es wurde versucht, eine Datenbank zu erstellen oder zum Pool für elastische Datenbanken hinzuzufügen, während der Grenzwert für die Datenbankanzahl des Pools für elastische Datenbanken erreicht wurde. | Erhöhen Sie nach Möglichkeit die DTU-Anzahl des Pools für elastische Datenbanken, um die Begrenzung für Datenbanken zu steigern, oder entfernen Sie Datenbanken aus dem Pool für elastische Datenbanken. |
| 40889 | 16 |Die Begrenzung für DTUs oder Speicher für den Pool für elastische Datenbanken „%.*ls“ kann nicht verkleinert werden, da nicht genügend Speicherplatz für die Datenbanken verfügbar wäre. Es wurde versucht, die Speicherbegrenzung des Pools für elastische Datenbanken unter die Speicherauslastung zu verringern. | Reduzieren Sie die Speicherauslastung der einzelnen Datenbanken im Pool für elastische Datenbanken, oder entfernen Sie Datenbanken aus dem Pool, um die DTUs oder Speicherbegrenzung zu verringern. |
| 40891 | 16 |Die Mindestanzahl von DTUs pro Datenbank (%d) darf die Höchstanzahl von DTUs pro Datenbank (%d) nicht überschreiten. Es wurde versucht, die DTU-Mindestanzahl pro Datenbank höher festzulegen, als die maximale DTU-Anzahl pro Datenbank. |Stellen Sie sicher, dass die Mindestanzahl von DTUs pro Datenbank nicht die Höchstanzahl von DTUs pro Datenbank überschreitet. |
| TBD | 16 |Die Speichergröße für eine einzelne Datenbank in einem Pool für elastische Datenbanken darf die maximal zulässige Größe für die Dienstebene des Pools für elastische Datenbanken „%.*ls“ nicht überschreiten. Die maximale Größe der Datenbank überschreitet die maximale Größe, die von der Dienstebene des Pools für elastische Datenbanken zugelassen wird. |Legen Sie die maximale Größe der Datenbank höchstens auf die maximal zulässige Größe der Dienstebene des Pools für elastische Datenbanken fest. |

Verwandte Themen:

* [Erstellen eines Pools für elastische Datenbanken (C#)](sql-database-elastic-pool-manage-csharp.md)
* [Verwalten eines Pools für elastische Datenbanken (C#)](sql-database-elastic-pool-manage-csharp.md)
* [Erstellen eines Pools für elastische Datenbanken (PowerShell)](sql-database-elastic-pool-manage-powershell.md)
* [Überwachen und Verwalten eines Pools für elastische Datenbanken (PowerShell)](sql-database-elastic-pool-manage-powershell.md)

## <a name="general-errors"></a>Allgemeine Fehler

Die folgenden Fehler fallen in keine der vorherigen Kategorien.

| Fehlercode | Severity | BESCHREIBUNG |
| ---:| ---:|:--- |
| [15006](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors#errors-15000-to-15999) |16 |„(AdministratorLogin)“ ist kein gültiger Name, da er ungültige Zeichen enthält.|
| [18452](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors#errors-18000-to-18999) |14 |Anmeldefehler. Die Anmeldung stammt aus einer nicht vertrauenswürdigen Domäne und kann mit der Windows-Authentifizierung nicht verwendet werden.%.&#x2a;ls (Windows-Anmeldungen werden in dieser SQL Server-Version nicht unterstützt.) |
| [18456](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors#errors-18000-to-18999) |14 |Fehler bei der Anmeldung für den Benutzer „%.&#x2a;ls“.%.&#x2a;ls%.&#x2a;ls(Fehler bei der Anmeldung für den Benutzer „%.&#x2a;ls“.) |
| [18470](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors#errors-18000-to-18999) |14 |Fehler bei der Anmeldung für den Benutzer „%.&#x2a;ls“. Ursache: Das Konto ist deaktiviert.%.&#x2a;ls |
| 40014 |16 |Es können nicht mehrere Datenbanken in derselben Transaktion verwendet werden. |
| 40054 |16 |Tabellen ohne gruppierten Index werden in dieser SQL Server-Version nicht unterstützt. Erstellen Sie einen gruppierten Index, und wiederholen Sie den Vorgang. |
| 40133 |15 |Dieser Vorgang wird in dieser SQL Server-Version nicht unterstützt. |
| 40506 |16 |Die angegebene SID ist für diese SQL Server-Version ungültig. |
| 40507 |16 |„%.&#x2a;ls“ kann in dieser SQL Server-Version nicht mit Parametern aufgerufen werden. |
| 40508 |16 |Die USE-Anweisung wird zum Wechseln zwischen Datenbanken nicht unterstützt. Verwenden Sie eine neue Verbindung, um eine Verbindung mit einer anderen Datenbank herzustellen. |
| 40510 |16 |Die „%.&#x2a;ls“-Anweisung wird in dieser SQL Server-Version nicht unterstützt. |
| 40511 |16 |Die integrierte „%.&#x2a;ls“-Funktion wird in dieser SQL Server-Version nicht unterstützt. |
| 40512 |16 |Die als veraltet markierte „%ls“-Funktion wird in dieser SQL Server-Version nicht unterstützt. |
| 40513 |16 |Die „%.&#x2a;ls“-Servervariable wird in dieser SQL Server-Version nicht unterstützt. |
| 40514 |16 |„%ls“ wird in dieser SQL Server-Version nicht unterstützt. |
| 40515 |16 |In dieser SQL Server-Version werden keine Verweise auf eine Datenbank und/oder einen Servernamen in „%.&#x2a;ls“ unterstützt. |
| 40516 |16 |Globale temporäre Objekte werden in dieser SQL Server-Version nicht unterstützt. |
| 40517 |16 |Die Schlüsselwort- oder Anweisungsoption „%.&#x2a;ls“ wird in dieser SQL Server-Version nicht unterstützt. |
| 40518 |16 |Der DBCC-Befehl „%.&#x2a;ls“ wird in dieser SQL Server-Version nicht unterstützt. |
| 40520 |16 |Die sicherungsfähige Klasse „%S_MSG“ wird in dieser SQL Server-Version nicht unterstützt. |
| 40521 |16 |Die sicherungsfähige Klasse „%S_MSG“ wird in dieser SQL Server-Version im Serverbereich nicht unterstützt. |
| 40522 |16 |Der „%.&#x2a;ls“-Datenbankprinzipaltyp wird in dieser SQL Server-Version nicht unterstützt. |
| 40523 |16 |Das implizite Erstellen des Benutzers „%.&#x2a;ls“ wird in dieser SQL Server-Version nicht unterstützt. Erstellen Sie den Benutzer vor der Verwendung explizit. |
| 40524 |16 |Der „%.&#x2a;ls“-Datentyp wird in dieser SQL Server-Version nicht unterstützt. |
| 40525 |16 |WITH „%.ls“ wird in dieser SQL Server-Version nicht unterstützt. |
| 40526 |16 |Der Rowsetanbieter „%.&#x2a;ls“ wird in dieser SQL Server-Version nicht unterstützt. |
| 40527 |16 |Verbindungsserver werden in dieser SQL Server-Version nicht unterstützt. |
| 40528 |16 |In dieser SQL Server-Version können Benutzer keinen Zertifikaten, asymmetrischen Schlüsseln oder Windows-Anmeldungen zugeordnet werden. |
| 40529 |16 |Die integrierte „%.&#x2a;ls“-Funktion wird in dieser SQL Server-Version im Identitätswechselkontext nicht unterstützt. |
| 40532 |11 |Der bei der Anmeldung angeforderte Server „%.&#x2a;ls“ kann nicht geöffnet werden. Fehler bei der Anmeldung. |
| 40553 |16 |Die Sitzung wurde aufgrund übermäßiger Speicherauslastung beendet. Ändern Sie die Abfrage, damit weniger Zeilen verarbeitet werden.<br/><br/> Wenn Sie die Anzahl von `ORDER BY`- und `GROUP BY`-Vorgängen im Transact-SQL-Code reduzieren, verringern sich auch die Arbeitsspeicheranforderungen Ihrer Abfrage. |
| 40604 |16 |CREATE/ALTER DATABASE war nicht möglich, da das Serverkontingent überschritten würde. |
| 40606 |16 |Das Anfügen von Datenbanken wird in dieser SQL Server-Version nicht unterstützt. |
| 40607 |16 |Windows-Anmeldungen werden in dieser SQL Server-Version nicht unterstützt. |
| 40611 |16 |Für Server können maximal 128 Firewallregeln definiert werden. |
| 40614 |16 |Die IP-Endadresse der Firewallregel darf nicht vor der IP-Startadresse liegen. |
| 40615 |16 |Der für die Anmeldung angeforderte Server „{0}“ kann nicht geöffnet werden. Der Client mit der IP-Adresse „{1}“ hat keine Zugriffsberechtigung für den Server.<br /><br />Aktivieren Sie den Zugriff über das SQL-Datenbank-Portal, oder indem Sie sp\_set\_firewall\_rule für die master-Datenbank ausführen, um eine Firewallregel für diese IP-Adresse bzw. diesen IP-Adressbereich zu erstellen. Es kann bis zu fünf Minuten dauert, bis diese Änderung wirksam wird. |
| 40617 |16 |Der mit „(Regelname)“ beginnende Name der Firewallregel ist zu lang. Die maximale Länge beträgt 128. |
| 40618 |16 |Der Name für die Firewallregel darf nicht leer sein. |
| 40620 |16 |Fehler bei der Anmeldung für den Benutzer „%.&#x2a;ls“. Fehler bei der Kennwortänderung. Kennwortänderungen während der Anmeldung werden in dieser SQL Server-Version nicht unterstützt. |
| 40627 |20 |Für Server „{0}“ und Datenbank „{1}“ wird ein Vorgang ausgeführt. Warten Sie vor dem Wiederholen des Vorgangs einige Minuten. |
| 40630 |16 |Fehler bei der Kennwortüberprüfung. Das Kennwort ist zu kurz und erfüllt daher nicht die Richtlinienanforderungen. |
| 40631 |16 |Das Kennwort, das Sie angegeben haben, ist zu lang. Das Kennwort sollte nicht mehr als 128 Zeichen aufweisen. |
| 40632 |16 |Fehler bei der Kennwortüberprüfung. Das Kennwort ist nicht komplex genug und erfüllt daher nicht die Richtlinienanforderungen. |
| 40636 |16 |Der reservierte Datenbankname „%.&#x2a;ls“ kann in diesem Vorgang nicht verwendet werden. |
| 40638 |16 |Ungültige Abonnement-ID (Abonnement-ID). Das Abonnement ist nicht vorhanden. |
| 40639 |16 |Anforderung entspricht nicht dem Schema: (Schemafehler). |
| 40640 |20 |Der Server hat eine unerwartete Ausnahme erkannt. |
| 40641 |16 |Der angegebene Pfad ist ungültig. |
| 40642 |17 |Der Server ist derzeit überlastet. Bitte versuchen Sie es später erneut. |
| 40643 |16 |Der angegebene Headerwert „x-ms-version“ ist ungültig. |
| 40644 |14 |Fehler beim Autorisieren des Zugriffs auf das angegebene Abonnement. |
| 40645 |16 |Der Servername (Servername) darf nicht leer oder NULL sein. Er darf nur aus den Kleinbuchstaben „a“ - „z“, den Zahlen 0 - 9 und Bindestrichen bestehen. Der Bindestrich darf nicht am Anfang oder Ende des Namens stehen. |
| 40646 |16 |Die Abonnement-ID darf nicht leer sein. |
| 40647 |16 |Der Server („Servername“) ist im Abonnement (Abonnement-ID) nicht enthalten. |
| 40648 |17 |Zu viele Anforderungen wurden durchgeführt. Versuchen Sie es später erneut. |
| 40649 |16 |Es wurde ein ungültiger „content-type“ angegeben. Nur „application“ oder „xml“ wird unterstützt. |
| 40650 |16 |Abonnement (Abonnement-ID) ist nicht vorhanden oder nicht betriebsbereit. |
| 40651 |16 |Fehler beim Erstellen des Servers, weil das Abonnement (Abonnement-ID) deaktiviert ist. |
| 40652 |16 |Der Server kann nicht verschoben oder erstellt werden, weil das Abonnement (Abonnement-ID) das Serverkontingent überschreitet. |
| 40671 |17 |Kommunikationsfehler zwischen dem Gateway und dem Verwaltungsdienst. Versuchen Sie es später noch mal. |
| 40852 |16 |Die von der Anmeldung angeforderte Datenbank „%.\*ls“ auf Server „%.\*ls“ kann nicht geöffnet werden. Der Zugriff auf die Datenbank ist nur mit einer Verbindungszeichenfolge mit aktivierter Sicherheit zulässig. Um auf diese Datenbank zuzugreifen, ändern Sie die Verbindungszeichenfolgen so, dass der Server-FQDN „secure“ enthält – „Servername.database.windows.net“ muss in „Servername.database`secure`.windows.net“ geändert werden. |
| 40914 | 16 | Der bei der Anmeldung angeforderte Server „ *[Servername]* “ kann nicht geöffnet werden. Dem Client ist der Zugriff auf den Server nicht gestattet.<br /><br />Um das Problem zu beheben, fügen Sie ggf. eine [VNET-Regel](sql-database-vnet-service-endpoint-rule-overview.md) hinzu. |
| 45168 |16 |Das SQL Azure-System ist ausgelastet und richtet eine Obergrenze bei den gleichzeitigen DB CRUD-Vorgängen für einen einzelnen SQL-Datenbank-Server ein (z.B. CREATE DATABASE). Für den in der Fehlermeldung angegebenen Server wurde die maximale Anzahl von gleichzeitigen Verbindungen überschritten. Versuchen Sie es später erneut. |
| 45169 |16 |Das SQL Azure-System ist ausgelastet und richtet eine Obergrenze für die Anzahl der gleichzeitigen CRUD-Vorgänge für ein Abonnement ein (z.B. CREATE SERVER). Für das in der Fehlermeldung angegebene Abonnement wurde die maximale Anzahl von gleichzeitigen Verbindungen überschritten, und die Anforderung wurde verweigert. Versuchen Sie es später erneut. |

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen finden Sie unter [Features von Azure SQL-Datenbank](sql-database-features.md).
* Weitere Informationen finden Sie unter [DTU-basiertes Kaufmodell](sql-database-service-tiers-dtu.md).
* Weitere Informationen finden Sie unter [vCore-basiertes Kaufmodell](sql-database-service-tiers-vcore.md).

