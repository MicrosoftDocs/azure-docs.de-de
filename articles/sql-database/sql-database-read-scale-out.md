---
title: Azure SQL-Datenbank – schreibgeschützte Abfragen auf Replikaten | Microsoft-Dokumentation
description: Azure SQL-Datenbank bietet die Möglichkeit, mithilfe der Kapazität von schreibgeschützten Replikaten einen Lastenausgleich für schreibgeschützte Workloads durchzuführen – dies wird als horizontale Leseskalierung bezeichnet.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein, carlrab
manager: craigg
ms.date: 04/19/2019
ms.openlocfilehash: cbcdcfd151951334246a4e85d9f521a15bb6269d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66146109"
---
# <a name="use-read-only-replicas-to-load-balance-read-only-query-workloads"></a>Verwenden von schreibgeschützten Replikaten für den Lastenausgleich schreibgeschützter Abfrageworkloads

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Das PowerShell Azure Resource Manager-Modul wird von Azure SQL-Datenbank weiterhin unterstützt, aber alle zukünftigen Entwicklungen erfolgen für das Az.Sql-Modul. Informationen zu diesen Cmdlets finden Sie unter [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Die Argumente für die Befehle im Az-Modul und den AzureRm-Modulen sind im Wesentlichen identisch.

Als Teil der [Hochverfügbarkeitsarchitektur](./sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) wird jede Datenbank auf den Dienstebenen Premium, Unternehmenskritisch oder Hyperscale automatisch mit einem primären Replikat und mehreren sekundären Replikaten bereitgestellt. Die sekundären Replikate werden mit derselben Computegröße wie das primäre Replikat bereitgestellt. Die Funktion **Horizontale Leseskalierung** ermöglicht es Ihnen, einen Lastenausgleich für schreibgeschützte SQL-Datenbank-Workloads vorzunehmen, indem Sie die Kapazität eines der schreibgeschützten Replikate verwenden, statt das Replikat mit Lese-/Schreibzugriff freizugeben. Auf diese Weise wird die schreibgeschützte Workload von der Hauptworkload für Lesen und Schreiben isoliert und beeinträchtigen deren Leistung nicht. Die Funktion ist für Anwendungen vorgesehen, die logisch getrennte, schreibgeschützte Workloads (z. B. zur Analyse) enthalten. Für diese könnten Sie mithilfe dieser zusätzlichen Kapazität ohne Zusatzkosten Leistungsvorteile erzielen.

Das folgende Diagramm veranschaulicht dies unter Verwendung einer unternehmenskritischen Datenbank.

![Schreibgeschützte Replikate](media/sql-database-read-scale-out/business-critical-service-tier-read-scale-out.png)

Die Funktion der horizontalen Leseskalierung ist bei Datenbanken in den Tarifen Premium, Unternehmenskritisch und Hyperscale standardmäßig aktiviert. Wenn Ihre SQL-Verbindungszeichenfolge mit `ApplicationIntent=ReadOnly` konfiguriert wurde, wird die Anwendung vom Gateway zu einem schreibgeschützten Replikat dieser Datenbank umgeleitet. Informationen zur Verwendung der `ApplicationIntent`-Eigenschaft finden Sie unter [Angeben der Anwendungsabsicht](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent).

Wenn Sie sicherstellen möchten, dass die Anwendung unabhängig von der Einstellung `ApplicationIntent` in der SQL-Verbindungszeichenfolge eine Verbindung mit dem primären Replikat herstellt, müssen Sie die horizontale Leseskalierung beim Erstellen der Datenbank oder beim Ändern ihrer Konfiguration explizit deaktivieren. Wenn Sie Ihre Datenbank z. B. vom Tarif Standard oder Universell auf den Tarif Premium, Unternehmenskritisch oder Hyperscale umstellen und sicherstellen möchten, dass weiterhin alle Verbindungen zum primären Replikat führen, deaktivieren Sie die horizontale Leseskalierung. Weitere Informationen zum Deaktivieren finden Sie unter [Aktivieren und Deaktivieren der horizontalen Leseskalierung](#enable-and-disable-read-scale-out).

> [!NOTE]
> Abfragedatenspeicher, erweiterte Ereignisse, SQL Profiler und Überwachungsfeatures werden in den schreibgeschützten Replikaten nicht unterstützt. 

## <a name="data-consistency"></a>Datenkonsistenz

Einer der Vorteile von Replikaten besteht darin, dass die Replikate sich immer in einem konsistenten Transaktionszustand befinden. Allerdings kann es auch vorkommen, dass zwischen den verschiedenen Replikaten ein geringes Maß an Latenz auftritt. Die horizontale Leseskalierung unterstützt Konsistenz auf Sitzungsebene. Das bedeutet Folgendes: Wenn die schreibgeschützte Sitzung nach einem Verbindungsfehler, der durch die Nichtverfügbarkeit eines Replikats verursacht wurde, die Verbindung wiederherstellt, wird sie möglicherweise an ein Replikat weitergeleitet, das nicht zu 100 % auf dem gleichen Stand ist wie das Replikat mit Lese-/Schreibzugriff. Gleiches gilt in diesem Fall: Wenn eine Anwendung Daten mithilfe einer Sitzung mit Lese-/Schreibzugriff schreibt und sie mithilfe einer schreibgeschützten Sitzung sofort liest, ist es möglich, dass die neuesten Updates nicht sofort im Replikat sichtbar sind. Die Latenz wird von einem asynchronen Wiederholungsvorgang des Transaktionsprotokolls verursacht.

> [!NOTE]
> Replikationslatenzen innerhalb der Region sind gering, und diese Situation tritt selten ein.

## <a name="connect-to-a-read-only-replica"></a>Herstellen einer Verbindung mit einem schreibgeschützten Replikat

Wenn Sie die horizontale Leseskalierung für eine Datenbank aktivieren, bestimmt die Option `ApplicationIntent` in der vom Client bereitgestellten Verbindungszeichenfolge, ob eine Verbindung an das Replikat mit Schreibzugriff oder an ein schreibgeschütztes Replikat weitergeleitet wird. Wenn der `ApplicationIntent`-Wert `ReadWrite` lautet (der Standardwert), wird die Verbindung an das Datenbankreplikat mit Lese-/Schreibzugriff weitergeleitet. Dies ist identisch mit dem vorhandenen Verhalten. Wenn der `ApplicationIntent`-Wert `ReadOnly` lautet, wird die Verbindung an ein schreibgeschütztes Replikat weitergeleitet.

Ein Beispiel: Die folgende Verbindungszeichenfolge verbindet den Client mit einem schreibgeschützten Replikat (ersetzen Sie die Elemente in spitzen Klammern durch die richtigen Werte für Ihre Umgebung, und löschen Sie die Klammern):

```SQL
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

Jede der folgenden Verbindungszeichenfolgen verbindet den Client mit einem Replikat mit Lese-/Schreibzugriff (ersetzen Sie die Elemente in spitzen Klammern durch die richtigen Werte für Ihre Umgebung, und löschen Sie die Klammern):

```SQL
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadWrite;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;

Server=tcp:<server>.database.windows.net;Database=<mydatabase>;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

## <a name="verify-that-a-connection-is-to-a-read-only-replica"></a>Sicherstellen einer Verbindung mit einem schreibgeschützten Replikat

Sie können durch Ausführen der folgenden Abfrage überprüfen, ob Sie mit einem schreibgeschützten Replikat verbunden sind. Bei Verbindung mit einem schreibgeschützten Replikat wird READ_ONLY zurückgegeben.

```SQL
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Updateability')
```

> [!NOTE]
> Es ist jeweils nur eines der Always On-Replikate für die schreibgeschützten Sitzungen verfügbar.

## <a name="monitoring-and-troubleshooting-read-only-replica"></a>Überwachung und Problembehandlung beim schreibgeschützten Replikat

Wenn Sie mit einem schreibgeschützten Replikat verbunden sind, können Sie mithilfe der `sys.dm_db_resource_stats`-DMV auf die Leistungsmetriken zugreifen. Verwenden Sie für den Zugriff auf Abfrageplanstatistiken die `sys.dm_exec_query_stats`-, `sys.dm_exec_query_plan`- und `sys.dm_exec_sql_text`-DMV.

> [!NOTE]
> Die DMV `sys.resource_stats` in der logischen Masterdatenbank gibt CPU-Nutzung und Speicherdaten des primären Replikats zurück.


## <a name="enable-and-disable-read-scale-out"></a>Aktivieren und Deaktivieren der horizontalen Leseskalierung

Die horizontale Leseskalierung ist bei den Dienstebenen Premium, Unternehmenskritisch und Hyperscale standardmäßig aktiviert. Für die Dienstebenen Basic, Standard oder Universell kann die horizontale Leseskalierung nicht aktiviert werden. Die horizontale Leseskalierung wird für Hyperscale-Datenbanken, die mit 0 Replikaten konfiguriert sind, automatisch deaktiviert. 

Sie können die horizontale Leseskalierung für Singletons und Pools für elastische Datenbanken der Dienstebenen Premium oder Unternehmenskritisch mithilfe der folgenden Methoden deaktivieren und erneut aktivieren.

> [!NOTE]
> Die Fähigkeit zum Deaktivieren der horizontalen Leseskalierung wird für Abwärtskompatibilität bereitgestellt.

### <a name="azure-portal"></a>Azure-Portal

Sie verwalten die Einstellung für die horizontale Leseskalierung auf dem Datenbankblatt **Konfigurieren**. 

### <a name="powershell"></a>PowerShell

Für die Verwaltung der horizontalen Leseskalierung in Azure PowerShell ist das Azure PowerShell-Release von Dezember 2016 oder eine neuere Version erforderlich. Das neueste PowerShell-Release finden Sie unter [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

Sie aktivieren oder deaktivieren die horizontale Leseskalierung in Azure PowerShell, indem Sie das Cmdlet [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) aufrufen und für den Parameter `-ReadScale` den gewünschten Wert übergeben: `Enabled` oder `Disabled`. 

So deaktivieren Sie die horizontale Leseskalierung für eine vorhandene Datenbank (ersetzen Sie die Elemente in spitzen Klammern durch die richtigen Werte für Ihre Umgebung, und löschen Sie die Klammern)

```powershell
Set-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Disabled
```
So deaktivieren Sie die horizontale Leseskalierung für eine neue Datenbank (ersetzen Sie die Elemente in spitzen Klammern durch die richtigen Werte für Ihre Umgebung, und löschen Sie die Klammern)

```powershell
New-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Disabled -Edition Premium
```

So aktivieren Sie die horizontale Leseskalierung für eine vorhandene Datenbank erneut (ersetzen Sie die Elemente in spitzen Klammern durch die richtigen Werte für Ihre Umgebung, und löschen Sie die Klammern)

```powershell
Set-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Enabled
```

### <a name="rest-api"></a>REST-API

Verwenden Sie zum Erstellen einer Datenbank mit deaktivierter horizontaler Leseskalierung oder zum Ändern der Einstellung für eine vorhandene Datenbank die folgende Methode. Legen Sie dabei die `readScale`-Eigenschaft wie in der folgenden Beispielanforderung gezeigt auf `Enabled` oder `Disabled` fest.

```rest
Method: PUT
URL: https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{GroupName}/providers/Microsoft.Sql/servers/{ServerName}/databases/{DatabaseName}?api-version= 2014-04-01-preview
Body:
{
   "properties":
   {
      "readScale":"Disabled"
   }
}
```

Weitere Informationen finden Sie unter [Databanken – Erstellen oder Aktualisieren](https://docs.microsoft.com/rest/api/sql/databases/createorupdate).

## <a name="using-tempdb-on-read-only-replica"></a>Verwenden von TempDB auf einem schreibgeschützten Replikat

Die TempDB-Datenbank wird nicht in die schreibgeschützten Replikate repliziert. Jedes Replikat verfügt über eine eigene Version der TempDB-Datenbank, die beim Erstellen des Replikats erstellt wird. Dadurch wird sichergestellt, dass TempDB aktualisiert werden kann und während der Abfrageausführung geändert werden kann. Wenn die schreibgeschützte Workload von der Verwendung von TempDB-Objekten abhängig ist, sollten Sie diese Objekte als Teil Ihres Abfrageskripts erstellen. 

## <a name="using-read-scale-out-with-geo-replicated-databases"></a>Verwenden der horizontalen Leseskalierung mit georeplizierten Datenbanken

Wenn Sie die horizontale Leseskalierung verwenden, um einen Lastenausgleich für schreibgeschützte Workloads in einer Datenbank vorzunehmen, die (z. B. als Mitglied einer Failovergruppe) georepliziert ist, stellen Sie sicher, dass die horizontale Leseskalierung sowohl für die primäre Datenbank als auch für die georeplizierte sekundäre Datenbank aktiviert ist. Durch diese Konfiguration wird sichergestellt, dass dieselbe Lastenausgleichswirkung fortgesetzt wird, wenn Ihre Anwendung nach einem Failover eine Verbindung mit der neuen primären Datenbank herstellt. Wenn Sie eine Verbindung mit der georeplizierten sekundären Datenbank mit aktivierter horizontaler Leseskalierung herstellen, werden Ihre Sitzungen mit `ApplicationIntent=ReadOnly` auf die gleiche Weise an eines der Replikate weitergeleitet wie Verbindungen in der primären Datenbank weitergeleitet werden.  Die Sitzungen ohne `ApplicationIntent=ReadOnly` werden an das primäre Replikat der georeplizierten sekundären Datenbank weitergeleitet, das ebenfalls schreibgeschützt ist. Weil die georeplizierte sekundäre Datenbank einen anderen Endpunkt als die primäre Datenbank aufweist, musste `ApplicationIntent=ReadOnly` in der Vergangenheit nicht festgelegt werden, um auf die sekundäre Datenbank zuzugreifen. Um die Abwärtskompatibilität sicherzustellen, zeigt die `sys.geo_replication_links`-DMV `secondary_allow_connections=2` (jede Clientverbindung ist zulässig) an.

> [!NOTE]
> Roundrobin oder ein anderes Lastenausgleichsrouting zwischen den lokalen Replikaten der sekundären Datenbank wird nicht unterstützt.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zum Hyperscale-Angebot für SQL-Datenbank finden Sie unter [Dienstebene „Hyperscale“](./sql-database-service-tier-hyperscale.md).
