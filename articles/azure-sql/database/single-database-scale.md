---
title: Skalieren von Einzeldatenbankressourcen
description: In diesem Artikel wird beschrieben, wie die für eine Einzeldatenbank in Azure SQL-Datenbank verfügbaren Compute- und Speicherressourcen skaliert werden können.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1, references_regions
ms.devlang: ''
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sstein
ms.date: 09/16/2020
ms.openlocfilehash: da3c70baccc3c86f2ac57d61539456464e3042b6
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96493405"
---
# <a name="scale-single-database-resources-in-azure-sql-database"></a>Skalieren von Einzeldatenbankressourcen in Azure SQL-Datenbank

In diesem Artikel wird beschrieben, wie die für eine Azure SQL-Datenbank-Instanz im bereitgestellten Computetarif verfügbaren Compute- und Speicherressourcen skaliert werden können. Alternativ bietet die [serverlose Computeebene](serverless-tier-overview.md) automatische Computeskalierung und eine Abrechnung der genutzten Computekapazität pro Sekunde.

Nach der anfänglichen Auswahl der Anzahl an virtuellen Kernen oder DTUs können Sie ein Singleton je nach tatsächlichem Bedarf im [Azure-Portal](single-database-manage.md#the-azure-portal), in [Transact-SQL](/sql/t-sql/statements/alter-database-transact-sql#examples-1), in [PowerShell](/powershell/module/az.sql/set-azsqldatabase), in der [Azure CLI](/cli/azure/sql/db#az-sql-db-update) oder in der [REST-API](/rest/api/sql/databases/update) dynamisch zentral hoch- oder herunterskalieren.

Das folgende Video zeigt die dynamische Änderung von Dienstebene und Computegröße zum Heraufsetzen verfügbarer DTUs für eine einzelne Datenbank.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]

> [!IMPORTANT]
> Unter bestimmten Umständen müssen Sie ggf. eine Datenbank verkleinern, um ungenutzten Speicherplatz freizugeben. Weitere Informationen finden Sie unter [Verwalten von Dateispeicherplatz in Azure SQL-Datenbank](file-space-manage.md).

## <a name="impact"></a>Auswirkung

Zum Ändern der Dienstebene oder der Computegröße müssen hauptsächlich die folgenden Schritte ausgeführt werden:

1. Erstellen Sie eine neue Computeinstanz für die Datenbank. 

    Eine neue Computeinstanz wird mit der angeforderten Dienstebene und Computegröße erstellt. Bei einigen Kombinationen aus Änderungen der Dienstebene und der Computegröße muss in der neuen Computeinstanz ein Replikat der Datenbank erstellt werden. Dies umfasst das Kopieren von Daten und kann sich stark auf die Gesamtwartezeit auswirken. Die Datenbank bleibt unabhängig davon während dieses Schritts online, und Verbindungen werden weiterhin an die Datenbank in der ursprünglichen Computeinstanz weitergeleitet.

2. Leiten Sie die Verbindungen zu einer neuen Computeinstanz um.

    Vorhandene Verbindungen zur Datenbank in der ursprünglichen Computeinstanz werden verworfen. Alle neuen Verbindungen werden mit der Datenbank in der neuen Computeinstanz hergestellt. Bei einigen Kombinationen von Dienstebenen- und Computegrößenänderungen werden Datenbankdateien während des Wechsels getrennt und neu angefügt.  Der Wechsel kann zu einer kurzen Dienstunterbrechung führen, in der die Datenbank in der Regel für weniger als 30 Sekunden nicht verfügbar ist. Sollten zu dem Zeitpunkt, zu dem die Verbindungen getrennt werden, zeitintensive Transaktionen ausgeführt werden, kann dieser Schritt länger dauern, weil abgebrochene Transaktionen wiederhergestellt werden müssen. Mit der [schnelleren Datenbankwiederherstellung](../accelerated-database-recovery.md) können die Auswirkungen abgebrochener, zeitintensiver Transaktionen reduziert werden.

> [!IMPORTANT]
> Während dieses Workflows gehen keine Daten verloren. Implementieren Sie unbedingt auch [Wiederholungslogik](troubleshoot-common-connectivity-issues.md) in den Anwendungen und Komponenten, die Azure SQL-Datenbank verwenden, während die Dienstebene geändert wird.

## <a name="latency"></a>Latency

Beim Ändern der Dienstebene, beim Skalieren der Computegröße einer Einzeldatenbank oder eines Pools für elastische Datenbanken, beim Verschieben einer Datenbank in einen/aus einem Pool für elastische Datenbanken oder beim Verschieben einer Datenbank zwischen Pools für elastische Datenbanken wird die geschätzte Wartezeit wie folgt parametrisiert:

|Dienstebene|Einzeldatenbank des Tarifs „Basic“,</br>Standard (S0-S1)|Pools für elastische Datenbanken des Tarifs „Basic“,</br>Standard (S2-S12), </br>Einzeldatenbank oder Pool für elastische Datenbanken des Tarifs „Universell“|Einzeldatenbank oder Pool für elastische Datenbanken der Tarife „Premium“ oder „Unternehmenskritisch“|Hyperscale
|:---|:---|:---|:---|:---|
|**Einzeldatenbank des Tarifs „Basic“,</br> Standard (S0-S1)**|&bull; &nbsp;Konstante Wartezeit unabhängig vom verwendeten Speicherplatz</br>&bull; &nbsp;In der Regel weniger als fünf Minuten|&bull; &nbsp;Wartezeit proportional zum verwendeten Datenbankspeicherplatz aufgrund des Kopierens von Daten</br>&bull; &nbsp;In der Regel weniger als eine Minute pro GB (verwendeter Speicherplatz)|&bull; &nbsp;Wartezeit proportional zum verwendeten Datenbankspeicherplatz aufgrund des Kopierens von Daten</br>&bull; &nbsp;In der Regel weniger als eine Minute pro GB (verwendeter Speicherplatz)|&bull; &nbsp;Wartezeit proportional zum verwendeten Datenbankspeicherplatz aufgrund des Kopierens von Daten</br>&bull; &nbsp;In der Regel weniger als eine Minute pro GB (verwendeter Speicherplatz)|
|**Pool für elastische Datenbanken des Tarifs „Basic“, </br>Standard (S2–S12), </br>Einzeldatenbank oder Pool für elastische Datenbanken des Tarifs „Universell“**|&bull; &nbsp;Wartezeit proportional zum verwendeten Datenbankspeicherplatz aufgrund des Kopierens von Daten</br>&bull; &nbsp;In der Regel weniger als eine Minute pro GB (verwendeter Speicherplatz)|&bull; &nbsp;Bei Einzeldatenbanken konstante Wartezeit unabhängig vom verwendeten Speicherplatz</br>&bull; &nbsp;In der Regel weniger als fünf Minuten bei Einzeldatenbanken</br>&bull; &nbsp; Bei Pools für elastische Datenbanken proportional zur Anzahl der Datenbanken|&bull; &nbsp;Wartezeit proportional zum verwendeten Datenbankspeicherplatz aufgrund des Kopierens von Daten</br>&bull; &nbsp;In der Regel weniger als eine Minute pro GB (verwendeter Speicherplatz)|&bull; &nbsp;Wartezeit proportional zum verwendeten Datenbankspeicherplatz aufgrund des Kopierens von Daten</br>&bull; &nbsp;In der Regel weniger als eine Minute pro GB (verwendeter Speicherplatz)|
|**Einzeldatenbank oder Pool für elastische Datenbanken der Tarife „Premium“ oder „Unternehmenskritisch“**|&bull; &nbsp;Wartezeit proportional zum verwendeten Datenbankspeicherplatz aufgrund des Kopierens von Daten</br>&bull; &nbsp;In der Regel weniger als eine Minute pro GB (verwendeter Speicherplatz)|&bull; &nbsp;Wartezeit proportional zum verwendeten Datenbankspeicherplatz aufgrund des Kopierens von Daten</br>&bull; &nbsp;In der Regel weniger als eine Minute pro GB (verwendeter Speicherplatz)|&bull; &nbsp;Wartezeit proportional zum verwendeten Datenbankspeicherplatz aufgrund des Kopierens von Daten</br>&bull; &nbsp;In der Regel weniger als eine Minute pro GB (verwendeter Speicherplatz)|&bull; &nbsp;Wartezeit proportional zum verwendeten Datenbankspeicherplatz aufgrund des Kopierens von Daten</br>&bull; &nbsp;In der Regel weniger als eine Minute pro GB (verwendeter Speicherplatz)|
|**Hyperscale**|N/V|N/V|N/V|&bull; &nbsp;Konstante Wartezeit unabhängig vom verwendeten Speicherplatz</br>&bull; &nbsp;In der Regel weniger als zwei Minuten|

> [!NOTE]
> Darüber hinaus ist bei Datenbanken vom Typ „Standard (S2-S12)“ und „Universell“ die Wartezeit beim Verschieben einer Datenbank in einen/aus einem Pool für elastische Datenbanken oder zwischen Pools für elastische Datenbanken proportional zur Datenbankgröße, wenn für die Datenbank [PFS](../../storage/files/storage-files-introduction.md)-Speicher (Premium File Share, Premium-Dateifreigabe) verwendet wird.
>
> Um zu ermitteln, ob eine Datenbank PFS-Speicher verwendet, führen Sie die folgende Abfrage im Kontext der Datenbank aus. Wenn der Wert in der Spalte „AccountType“ `PremiumFileStorage` oder `PremiumFileStorage-ZRS` lautet, verwendet die Datenbank PFS-Speicher.
 
```sql
SELECT s.file_id,
       s.type_desc,
       s.name,
       FILEPROPERTYEX(s.name, 'AccountType') AS AccountType
FROM sys.database_files AS s
WHERE s.type_desc IN ('ROWS', 'LOG');
```

> [!TIP]
> Weitere Informationen zum Überwachen aktuell ausgeführter Vorgänge finden Sie unter: [Verwalten von Vorgängen mit der SQL-REST-API](/rest/api/sql/operations/list), [Verwalten von Vorgängen mithilfe der CLI](/cli/azure/sql/db/op), [Überwachen von Vorgängen mit T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) und unter diesen beiden PowerShell-Befehlen: [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity) und [Stop-AzSqlDatabaseActivity](/powershell/module/az.sql/stop-azsqldatabaseactivity).

## <a name="cancelling-changes"></a>Abbrechen von Änderungen

Die Änderung einer Dienstebene oder der Vorgang zur Computeneuskalierung kann abgebrochen werden.

### <a name="the-azure-portal"></a>Das Azure-Portal

Navigieren Sie auf dem Blatt mit der Datenbankübersicht zu **Benachrichtigungen**, und klicken Sie auf die Kachel mit dem Hinweis, dass derzeit ein Vorgang ausgeführt wird:

![Laufender Vorgang](./media/single-database-scale/ongoing-operations.png)

Klicken Sie anschließend auf die Schaltfläche mit der Bezeichnung **Diesen Vorgang abbrechen**.

![Abbrechen des laufenden Vorgangs](./media/single-database-scale/cancel-ongoing-operation.png)

### <a name="powershell"></a>PowerShell

Legen Sie an einer PowerShell-Eingabeaufforderung `$resourceGroupName`, `$serverName` und `$databaseName` fest, und führen Sie dann den folgenden Befehl aus:

```azurecli
$operationName = (az sql db op list --resource-group $resourceGroupName --server $serverName --database $databaseName --query "[?state=='InProgress'].name" --out tsv)
if (-not [string]::IsNullOrEmpty($operationName)) {
    (az sql db op cancel --resource-group $resourceGroupName --server $serverName --database $databaseName --name $operationName)
        "Operation " + $operationName + " has been canceled"
}
else {
    "No service tier change or compute rescaling operation found"
}
```

## <a name="additional-considerations"></a>Weitere Überlegungen

- Wenn Sie ein Upgrade auf eine höhere Dienstebene oder Computegröße durchführen, wird die maximale Datenbankgröße nur erhöht, wenn Sie explizit eine höhere Maximalgröße angeben.
- Für das Downgrade einer Datenbank muss die verwendete Datenbankmenge kleiner als die maximal zulässige Größe der Zieldienstebene und der Zielcomputegröße sein.
- Bei einem Downgrade von der Dienstebene **Premium** zu **Standard** fallen zusätzliche Speicherkosten an, wenn (1) die maximale Größe der Datenbank von der Zielcomputegröße unterstützt wird und (2) die maximale Größe in der Zielcomputegröße die enthaltene Speichermenge überschreitet. Wenn z.B. eine P1-Datenbank mit einer maximalen Größe von 500GB auf S3 reduziert wird, fallen zusätzliche Speicherkosten an, da S3 eine maximale Größe von 1TB unterstützt und die enthaltene Speichermenge nur 250GB beträgt. Daher beträgt die zusätzliche Speichermenge 500 GB – 250 GB = 250 GB. Informationen zu den Preisen für zusätzlichen Speicherplatz siehe [Preise für Azure SQL-Datenbank](https://azure.microsoft.com/pricing/details/sql-database/). Wenn die tatsächlich verwendete Speichermenge kleiner als die enthaltene Speichermenge ist, können Sie diese zusätzlichen Kosten vermeiden, indem Sie die maximale Datenbankgröße auf die enthaltene Menge reduzieren.
- Beim Upgrade einer Datenbank mit aktivierter [Georeplikation](active-geo-replication-configure-portal.md) führen Sie zuerst ein Upgrade der sekundären Datenbanken auf die gewünschte Dienstebene und die gewünschte Computegröße durch, bevor Sie ein Upgrade für die primäre Datenbank vornehmen (allgemeine Richtlinie für optimale Leistung). Wenn Sie ein Upgrade auf eine andere Edition durchführen möchten, muss zuerst die sekundäre Datenbank aktualisiert werden.
- Beim Downgrade einer Datenbank mit aktivierter [Georeplikation](active-geo-replication-configure-portal.md) führen Sie zuerst ein Downgrade der primären Datenbank auf die gewünschte Dienstebene und die gewünschte Computegröße durch, bevor Sie ein Downgrade für die sekundären Datenbanken vornehmen (allgemeine Richtlinie für optimale Leistung). Bei einem Downgrade auf eine andere Edition muss zuerst die primäre Datenbank herabgestuft werden.
- Die Angebote des Wiederherstellungsdiensts variieren für die verschiedenen Dienstebenen. Bei einem Downgrade auf den Tarif **Basic** ergibt sich ein kürzerer Aufbewahrungszeitraum für Sicherungen. Weitere Informationen finden Sie unter [Azure SQL-Datenbanksicherungen](automated-backups-overview.md).
- Die neuen Eigenschaften für die Datenbank werden erst angewendet, wenn die Änderungen abgeschlossen sind.

## <a name="billing"></a>Abrechnung

Die Abrechnung erfolgt für jede Stunde, in der eine Datenbank die höchste in dieser Stunde angewendete Dienstebene und Computegröße nutzt – unabhängig von der Nutzung und unabhängig davon, ob die Datenbank weniger als eine Stunde aktiv war. Wenn Sie beispielsweise eine Einzeldatenbank erstellen und diese fünf Minuten später löschen, wird Ihnen eine volle Datenbankstunde in Rechnung gestellt.

## <a name="change-storage-size"></a>Ändern der Speichergröße

### <a name="vcore-based-purchasing-model"></a>vCore-basiertes Kaufmodell

- Speicher kann in Inkrementen von 1 GB bis zur maximalen Datenspeichergröße bereitgestellt werden. Die konfigurierbare Mindestdatenspeichergröße ist 1 GB. Weitere Informationen zur maximalen Größe der Datenspeicherung in den einzelnen Dienstzielen finden Sie auf den Dokumentationsseiten zur Ressourcenbegrenzung für [einzelne Datenbanken](resource-limits-vcore-single-databases.md) und [elastische Pools](resource-limits-vcore-elastic-pools.md).
- Datenspeicher für eine Einzeldatenbank kann durch Erhöhen oder Verringern der maximalen Größe über das [Azure-Portal](https://portal.azure.com), [Transact-SQL](/sql/t-sql/statements/alter-database-transact-sql#examples-1), [PowerShell](/powershell/module/az.sql/set-azsqldatabase), die [Azure CLI](/cli/azure/sql/db#az-sql-db-update) oder die [REST-API](/rest/api/sql/databases/update) bereitgestellt werden. Wenn der Wert für die maximale Größe in Bytes angegeben wird, muss er ein Vielfaches von 1 GB (1073741824 Bytes) sein.
- Die Menge der Daten, die in den Datendateien einer Datenbank gespeichert werden kann, ist durch die konfigurierte maximale Größe des Datenspeichers begrenzt. Zusätzlich zu diesem Speicher teilt Azure SQL-Datenbank automatisch 30 Prozent mehr Speicher für das Transaktionsprotokoll zu.
- Azure SQL-Datenbank ordnet für die `tempdb`-Datenbank automatisch 32 GB pro virtuellem Kern zu. `tempdb` befindet sich in allen Dienstebenen auf einem lokalen SSD-Datenträger.
- Der Preis für Speicher für eine Einzeldatenbank oder einen Pool für elastische Datenbanken errechnet sich aus der Summe des Datenspeichers und des Transaktionsprotokollspeichers multipliziert mit dem Speichereinheitenpreis für die Dienstebene. Die Kosten für die `tempdb`-Datenbank sind im Preis enthalten. Ausführliche Informationen zu den Speicherpreisen finden Sie unter [Preise für Azure SQL-Datenbank ](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Unter bestimmten Umständen müssen Sie ggf. eine Datenbank verkleinern, um ungenutzten Speicherplatz freizugeben. Weitere Informationen finden Sie unter [Verwalten von Dateispeicherplatz in Azure SQL-Datenbank](file-space-manage.md).

### <a name="dtu-based-purchasing-model"></a>DTU-basiertes Kaufmodell

- Der DTU-Preis für eine einzelne Datenbank enthält eine bestimmte Menge Speicher ohne zusätzliche Kosten. Zusätzlicher Speicher über die inbegriffene Speichermenge hinaus kann gegen zusätzliche Gebühren bis zur Obergrenze in Inkrementen von 250 GB bis zu 1 TB und dann in Inkrementen von 256 GB über 1 TB hinaus bereitgestellt werden. Informationen zu enthaltenen Speichermengen und Maximalgrößen finden Sie unter [Einzeldatenbank: Speicher- und Computegrößen](resource-limits-dtu-single-databases.md#single-database-storage-sizes-and-compute-sizes).
- Zusätzlicher Speicher für eine Einzeldatenbank kann durch Erhöhen der maximalen Größe über das Azure-Portal, [Transact-SQL](/sql/t-sql/statements/alter-database-transact-sql#examples-1), [PowerShell](/powershell/module/az.sql/set-azsqldatabase), die [Azure CLI](/cli/azure/sql/db#az-sql-db-update) oder die [REST-API](/rest/api/sql/databases/update) bereitgestellt werden.
- Der Preis für zusätzlichen Speicher für eine Einzeldatenbank errechnet sich aus der Menge an zusätzlich bereitgestelltem Speicher multipliziert mit dem Einheitenpreis für zusätzlichen Speicher für die Dienstebene. Ausführliche Informationen zu den Preisen für zusätzlichen Speicherplatz finden Sie unter [Preise für Azure SQL-Datenbank](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Unter bestimmten Umständen müssen Sie ggf. eine Datenbank verkleinern, um ungenutzten Speicherplatz freizugeben. Weitere Informationen finden Sie unter [Verwalten von Dateispeicherplatz in Azure SQL-Datenbank](file-space-manage.md).

### <a name="geo-replicated-database"></a>Georeplizierte Datenbank

Zum Ändern der Datenbankgröße einer replizierten sekundären Datenbank ändern Sie die Größe der primären Datenbank. Diese Änderung wird dann auch in der sekundären Datenbank repliziert und implementiert.

## <a name="p11-and-p15-constraints-when-max-size-greater-than-1-tb"></a>Einschränkungen von P11 und P15, wenn die maximale Größe 1 TB übersteigt

In allen Regionen außer den folgenden ist im Premium-Tarif derzeit mehr als 1 TB Speicher verfügbar: China, Osten; China, Norden; Deutschland, Mitte; Deutschland, Nordosten. In diesen Regionen ist der Speicher im Tarif „Premium“ auf 1 TB begrenzt. Die folgenden Aspekte und Einschränkungen gelten für P11- und P15-Datenbanken mit einer maximalen Größe von mehr als 1 TB:

- Wenn die maximale Größe einer P11- oder P15-Datenbank jemals auf einen Wert über 1 TB festgelegt wurde, kann sie nur in einer P11- oder P15-Datenbank wiederhergestellt oder kopiert werden.  Die Datenbank kann folglich auf eine andere Computegröße skaliert werden, sofern der zugewiesene Speicherplatz zum Zeitpunkt der Neuskalierung nicht die maximalen Grenzwerte der neuen Computegröße überschreitet.
- Szenarien für aktive Georeplikation:
  - Einrichten einer Georeplikationsbeziehung: Wenn es sich bei der primären Datenbank um eine P11- oder P15-Datenbank handelt, müssen auch sekundäre P11- oder P15-Datenbanken verwendet werden. Geringere Computegrößen werden als sekundäre Datenbanken abgelehnt, weil sie nicht mehr als 1 TB unterstützen können.
  - Aktualisieren der primären Datenbank in einer Georeplikationsbeziehung: Die Änderung der maximalen Größe für eine primäre Datenbank in mehr als 1 TB löst die gleiche Änderung für die sekundäre Datenbank aus. Beide Upgrades müssen erfolgreich ausgeführt werden, damit die Änderung für die primäre Datenbank wirksam wird. Für die Option mit mehr als 1 TB gelten Regionseinschränkungen. Wenn sich die sekundäre Datenbank in einer Region befindet, die nicht mehr als 1 TB unterstützt, wird die primäre Datenbank nicht aktualisiert.
- Die Verwendung des Import/Export-Diensts zum Laden von P11-/P15-Datenbanken mit mehr als 1 TB wird nicht unterstützt. Verwenden Sie „SqlPackage.exe“, um Daten zu [importieren](database-import.md) und [exportieren](database-export.md).

## <a name="next-steps"></a>Nächste Schritte

Allgemeine Ressourcengrenzwerte finden Sie unter [Ressourcenlimits für Singletons mit dem auf virtuellen Kernen (V-Kernen) basierenden Kaufmodell](resource-limits-vcore-single-databases.md) und unter [Ressourcengrenzwerte für Einzeldatenbanken, die das DTU-Kaufmodell verwenden: Azure SQL-Datenbank](resource-limits-dtu-single-databases.md).
