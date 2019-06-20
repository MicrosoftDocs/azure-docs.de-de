---
title: Skalieren von Einzeldatenbankressourcen – Azure SQL-Datenbank | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie die für eine Einzeldatenbank in Azure SQL-Datenbank verfügbaren Compute- und Speicherressourcen skaliert werden können.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
manager: craigg
ms.date: 04/26/2019
ms.openlocfilehash: 1048b4e2ac3a8523d5539ddc1a1bdaca3ec2d912
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65074263"
---
# <a name="scale-single-database-resources-in-azure-sql-database"></a>Skalieren von Einzeldatenbankressourcen in Azure SQL-Datenbank

In diesem Artikel wird beschrieben, wie die für eine Einzeldatenbank auf der bereitgestellten Dienstebene verfügbaren Compute- und Speicherressourcen skaliert werden können. Alternativ bietet die [serverlose Computeebene (Vorschauversion)](sql-database-serverless.md) automatische Computeskalierung und eine Abrechnung der genutzten Computekapazität pro Sekunde.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Das PowerShell Azure Resource Manager-Modul wird von Azure SQL-Datenbank weiterhin unterstützt, aber alle zukünftigen Entwicklungen erfolgen für das Az.Sql-Modul. Informationen zu diesen Cmdlets finden Sie unter [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Die Argumente für die Befehle im Az-Modul und den AzureRm-Modulen sind im Wesentlichen identisch.

## <a name="change-compute-size-vcores-or-dtus"></a>Ändern der Computegröße (virtuelle Kerne oder DTUs)

Nach der anfänglichen Auswahl der Anzahl an virtuellen Kernen oder DTUs können Sie ein Singleton je nach tatsächlichem Bedarf im [Azure-Portal](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server), in [Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1), in [PowerShell](/powershell/module/az.sql/set-azsqldatabase), in der [Azure CLI](/cli/azure/sql/db#az-sql-db-update) oder in der [REST-API](https://docs.microsoft.com/rest/api/sql/databases/update) dynamisch zentral hoch- oder herunterskalieren.

Das folgende Video zeigt die dynamische Änderung von Dienstebene und Computegröße zum Heraufsetzen verfügbarer DTUs für eine einzelne Datenbank.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]
>

> [!IMPORTANT]
> Unter bestimmten Umständen müssen Sie ggf. eine Datenbank verkleinern, um ungenutzten Speicherplatz freizugeben. Weitere Informationen finden Sie unter [Verwalten von Dateispeicherplatz in Azure SQL-Datenbank](sql-database-file-space-management.md).

### <a name="impact-of-changing-service-tier-or-rescaling-compute-size"></a>Auswirkungen der Änderung der Dienstebene oder der Neuskalierung der Computegröße

Zum Ändern der Dienstebene oder der Computegröße eines Singleton müssen hauptsächlich die folgenden Schritte ausgeführt werden:

1. Erstellen einer neuen Computeinstanz für die Datenbank  

    Eine neue Computeinstanz für die Datenbank wird mit der angeforderten Dienstebene und der Computegröße erstellt. Für einige Kombinationen von Änderungen der Dienstebene und der Computegröße muss ein Datenbankreplikat in der neuen Computeinstanz erstellt werden. Dies umfasst das Kopieren von Daten und kann sich stark auf die Gesamtwartezeit auswirken. Die Datenbank bleibt unabhängig davon während dieses Schritts online, und Verbindungen werden weiterhin an die Datenbank in der ursprünglichen Computeinstanz weitergeleitet.

2. Umleiten der Verbindungen zur neuen Computeinstanz

    Vorhandene Verbindungen zur Datenbank in der ursprünglichen Computeinstanz werden verworfen. Alle neuen Verbindungen werden mit der Datenbank in der neuen Computeinstanz hergestellt. Für einige Kombinationen von Änderungen der Dienstebene und der Computegröße werden Datenbankdateien während des Wechsels getrennt und neu angefügt.  Der Wechsel kann zu einer kurzen Dienstunterbrechung führen, in der die Datenbank in der Regel für weniger als 30 Sekunden nicht verfügbar ist. Wenn zeitintensive Transaktionen zu dem Zeitpunkt ausgeführt werden, an dem die Verbindungen getrennt werden, kann dieser Schritt mehr Zeit beanspruchen, da abgebrochene Transaktionen wiederhergestellt werden. Mit der [schnelleren Datenbankwiederherstellung](sql-database-accelerated-database-recovery.md) können die Auswirkungen abgebrochener, zeitintensiver Transaktionen reduziert werden.

> [!IMPORTANT]
> Während dieses Workflows gehen keine Daten verloren.

### <a name="latency-of-changing-service-tier-or-rescaling-compute-size"></a>Wartezeit beim Ändern der Dienstebene oder beim Skalieren der Computegröße

Die Wartezeit beim Ändern der Dienstebene oder beim Skalieren der Computegröße einer Einzeldatenbank oder eines Pools für elastische Datenbanken wird wie folgt parametrisiert:

|Dienstebene|Einzeldatenbank des Tarifs „Basic“,</br>Standard (S0-S1)|Pools für elastische Datenbanken des Tarifs „Basic“,</br>Standard (S2-S12), </br>Hyperscale, </br>Einzeldatenbank oder Pool für elastische Datenbanken des Tarifs „Universell“|Einzeldatenbank oder Pool für elastische Datenbanken der Tarife „Premium“ oder „Unternehmenskritisch“|
|:---|:---|:---|:---|
|**Einzeldatenbank des Tarifs „Basic“,</br> Standard (S0-S1)**|&bull; &nbsp;Konstante Wartezeit unabhängig vom verwendeten Speicherplatz</br>&bull; &nbsp;In der Regel weniger als fünf Minuten|&bull; &nbsp;Wartezeit proportional zum verwendeten Datenbankspeicherplatz aufgrund des Kopierens von Daten</br>&bull; &nbsp;In der Regel weniger als eine Minute pro GB (verwendeter Speicherplatz)|&bull; &nbsp;Wartezeit proportional zum verwendeten Datenbankspeicherplatz aufgrund des Kopierens von Daten</br>&bull; &nbsp;In der Regel weniger als eine Minute pro GB (verwendeter Speicherplatz)|
|**Pool für elastische Datenbanken des Tarifs „Basic“, </br>Standard (S2-S12), </br>Hyperscale, </br>Einzeldatenbank oder Pool für elastische Datenbanken des Tarifs „Universell“**|&bull; &nbsp;Wartezeit proportional zum verwendeten Datenbankspeicherplatz aufgrund des Kopierens von Daten</br>&bull; &nbsp;In der Regel weniger als eine Minute pro GB (verwendeter Speicherplatz)|&bull; &nbsp;Konstante Wartezeit unabhängig vom verwendeten Speicherplatz</br>&bull; &nbsp;In der Regel weniger als fünf Minuten|&bull; &nbsp;Wartezeit proportional zum verwendeten Datenbankspeicherplatz aufgrund des Kopierens von Daten</br>&bull; &nbsp;In der Regel weniger als eine Minute pro GB (verwendeter Speicherplatz)|
|**Einzeldatenbank oder Pool für elastische Datenbanken der Tarife „Premium“ oder „Unternehmenskritisch“**|&bull; &nbsp;Wartezeit proportional zum verwendeten Datenbankspeicherplatz aufgrund des Kopierens von Daten</br>&bull; &nbsp;In der Regel weniger als eine Minute pro GB (verwendeter Speicherplatz)|&bull; &nbsp;Wartezeit proportional zum verwendeten Datenbankspeicherplatz aufgrund des Kopierens von Daten</br>&bull; &nbsp;In der Regel weniger als eine Minute pro GB (verwendeter Speicherplatz)|&bull; &nbsp;Wartezeit proportional zum verwendeten Datenbankspeicherplatz aufgrund des Kopierens von Daten</br>&bull; &nbsp;In der Regel weniger als 1 Minute pro verwendeten GB genutztem Speicherplatz|

> [!TIP]
> Weitere Informationen zum Überwachen aktuell ausgeführter Vorgänge finden Sie unter: [Verwalten von Vorgängen mit der SQL-REST-API](https://docs.microsoft.com/rest/api/sql/operations/list), [Verwalten von Vorgängen mithilfe der CLI](/cli/azure/sql/db/op), [Überwachen von Vorgängen mit T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) und unter diesen beiden PowerShell-Befehlen: [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity) und [Stop-AzSqlDatabaseActivity](/powershell/module/az.sql/stop-azsqldatabaseactivity).

### <a name="cancelling-service-tier-changes-or-compute-rescaling-operations"></a>Abbrechen von Dienstebenenänderungen oder von Vorgängen zur Computeneuskalierung

Die Änderung einer Dienstebene oder der Vorgang zur Computeneuskalierung kann abgebrochen werden.

#### <a name="azure-portal"></a>Azure-Portal

Navigieren Sie auf dem Blatt mit der Datenbankübersicht zu **Benachrichtigungen**, und klicken Sie auf die Kachel mit dem Hinweis, dass derzeit ein Vorgang ausgeführt wird:

![Laufender Vorgang](media/sql-database-single-database-scale/ongoing-operations.png)

Klicken Sie anschließend auf die Schaltfläche mit der Bezeichnung **Diesen Vorgang abbrechen**.

![Abbrechen des laufenden Vorgangs](media/sql-database-single-database-scale/cancel-ongoing-operation.png)

#### <a name="powershell"></a>PowerShell

Legen Sie an einer PowerShell-Eingabeaufforderung `$ResourceGroupName`, `$ServerName` und `$DatabaseName` fest, und führen Sie dann den folgenden Befehl aus:

```PowerShell
$OperationName = (az sql db op list --resource-group $ResourceGroupName --server $ServerName --database $DatabaseName --query "[?state=='InProgress'].name" --out tsv)
if(-not [string]::IsNullOrEmpty($OperationName))
    {
        (az sql db op cancel --resource-group $ResourceGroupName --server $ServerName --database $DatabaseName --name $OperationName)
        "Operation " + $OperationName + " has been canceled"
    }
    else
    {
        "No service tier change or compute rescaling operation found"
    }
```

### <a name="additional-considerations-when-changing-service-tier-or-rescaling-compute-size"></a>Weitere Überlegungen zum Ändern der Dienstebene oder Skalieren der Computegröße

- Wenn Sie ein Upgrade auf eine höhere Dienstebene oder Computegröße durchführen, wird die maximale Datenbankgröße nicht erhöht, sofern Sie nicht ausdrücklich eine höhere Maximalgröße angeben.
- Für das Downgrade einer Datenbank muss die verwendete Datenbankmenge kleiner als die maximal zulässige Größe der Zieldienstebene und der Zielcomputegröße sein.
- Bei einem Downgrade von der Dienstebene **Premium** zu **Standard** fallen zusätzliche Speicherkosten an, wenn (1) die maximale Größe der Datenbank von der Zielcomputegröße unterstützt wird und (2) die maximale Größe in der Zielcomputegröße die enthaltene Speichermenge überschreitet. Wenn z.B. eine P1-Datenbank mit einer maximalen Größe von 500 GB auf S3 reduziert wird, fallen zusätzliche Speicherkosten an, da S3 eine maximale Größe von 500 GB unterstützt und die enthaltene Speichermenge nur 250 GB beträgt. Daher beträgt die zusätzliche Speichermenge 500 GB – 250 GB = 250 GB. Ausführliche Informationen zu den Preisen für zusätzlichen Speicherplatz siehe [SQL-Datenbank – Preise](https://azure.microsoft.com/pricing/details/sql-database/). Wenn die tatsächlich verwendete Speichermenge kleiner als die enthaltene Speichermenge ist, können Sie diese zusätzlichen Kosten vermeiden, indem Sie die maximale Datenbankgröße auf die enthaltene Menge reduzieren.
- Beim Upgrade einer Datenbank mit aktivierter [Georeplikation](sql-database-geo-replication-portal.md) führen Sie zuerst ein Upgrade der sekundären Datenbanken auf die gewünschte Dienstebene und die gewünschte Computegröße durch, bevor Sie ein Upgrade für die primäre Datenbank vornehmen (allgemeine Richtlinie für optimale Leistung). Bei einem Upgrade auf eine andere Edition muss zunächst ein Upgrade der sekundären Datenbank durchgeführt werden.
- Beim Downgrade einer Datenbank mit aktivierter [Georeplikation](sql-database-geo-replication-portal.md) führen Sie zuerst ein Downgrade der primären Datenbank auf die gewünschte Dienstebene und die gewünschte Computegröße durch, bevor Sie ein Downgrade für die sekundären Datenbanken vornehmen (allgemeine Richtlinie für optimale Leistung). Bei einem Downgrade auf eine andere Edition muss zunächst ein Downgrade der primären Datenbank durchgeführt werden.
- Die Angebote des Wiederherstellungsdiensts variieren für die verschiedenen Dienstebenen. Wenn Sie ein Downgrade auf den Tarif **Basic** durchführen, verfügen Sie über einen kürzeren Aufbewahrungszeitraum von Sicherungen. Weitere Informationen finden Sie unter [Azure SQL-Datenbanksicherungen](sql-database-automated-backups.md).
- Die neuen Eigenschaften für die Datenbank werden erst angewendet, wenn die Änderungen abgeschlossen sind.

### <a name="billing-during-compute-rescaling"></a>Abrechnung während der Computeneuskalierung

Die Abrechnung erfolgt für jede Stunde, in der eine Datenbank die höchste in dieser Stunde angewendete Dienstebene und Computegröße nutzt – unabhängig von der Verwendung der Datenbank und ob sie weniger als eine Stunde aktiv war. Wenn Sie beispielsweise eine Einzeldatenbank erstellen und diese fünf Minuten später löschen, wird Ihnen eine volle Datenbankstunde in Rechnung gestellt.

## <a name="change-storage-size"></a>Ändern der Speichergröße

### <a name="vcore-based-purchasing-model"></a>vCore-basiertes Kaufmodell

- Speicher kann in Schritten von 1 GB bis zur maximalen Speichergröße bereitgestellt werden. Die konfigurierbare Mindestdatenspeichergröße beträgt 5 GB.
- Speicher für eine Einzeldatenbank kann durch Erhöhen oder Verringern der maximalen Größe im [Azure-Portal](https://portal.azure.com), in [Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1), in [PowerShell](/powershell/module/az.sql/set-azsqldatabase), in der[Azure CLI](/cli/azure/sql/db#az-sql-db-update) oder in der [REST-API](https://docs.microsoft.com/rest/api/sql/databases/update) bereitgestellt werden.
- SQL-Datenbank belegt automatisch 30 % des zusätzlichen Speicherplatzes für die Protokolldateien und 32 GB pro virtuellem Kern für die tempdb-Datenbank, wobei 384 GB nicht überschritten werden. Die tempdb befindet sich in allen Dienstebenen auf einem angefügten SSD-Datenträger.
- Der Preis für den Speicher für eine Einzeldatenbank errechnet sich aus der Summe des Datenspeichers und des Protokollspeichers multipliziert mit dem Speichereinheitenpreis für die Dienstebene. Die Kosten für die tempdb-Datenbank sind im Preis für den virtuellen Kern enthalten. Ausführliche Informationen zu den Preisen für zusätzlichen Speicherplatz siehe [SQL-Datenbank – Preise](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Unter bestimmten Umständen müssen Sie ggf. eine Datenbank verkleinern, um ungenutzten Speicherplatz freizugeben. Weitere Informationen finden Sie unter [Verwalten von Dateispeicherplatz in Azure SQL-Datenbank](sql-database-file-space-management.md).

### <a name="dtu-based-purchasing-model"></a>DTU-basiertes Kaufmodell

- Der DTU-Preis für eine einzelne Datenbank enthält eine bestimmte Menge Speicher ohne zusätzliche Kosten. Zusätzlicher Speicher über die inbegriffene Speichermenge hinaus kann gegen zusätzliche Gebühren bis zur Obergrenze in Inkrementen von 250 GB bis zu 1 TB und dann in Inkrementen von 256 GB über 1 TB hinaus bereitgestellt werden. Informationen zu enthaltenen Speichermengen und Maximalgrößen finden Sie unter [Einzeldatenbank: Speicher- und Computegrößen](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes).
- Zusätzlicher Speicher für eine Einzeldatenbank kann durch Erhöhen der maximalen Größe über das Azure-Portal, [Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1), [PowerShell](/powershell/module/az.sql/set-azsqldatabase), die [Azure CLI](/cli/azure/sql/db#az-sql-db-update) oder die [REST-API](https://docs.microsoft.com/rest/api/sql/databases/update) bereitgestellt werden.
- Der Preis für zusätzlichen Speicher für eine Einzeldatenbank errechnet sich aus der Menge an zusätzlich bereitgestelltem Speicher multipliziert mit dem Einheitenpreis für zusätzlichen Speicher für die Dienstebene. Ausführliche Informationen zu den Preisen für zusätzlichen Speicherplatz siehe [SQL-Datenbank – Preise](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Unter bestimmten Umständen müssen Sie ggf. eine Datenbank verkleinern, um ungenutzten Speicherplatz freizugeben. Weitere Informationen finden Sie unter [Verwalten von Dateispeicherplatz in Azure SQL-Datenbank](sql-database-file-space-management.md).

## <a name="p11-and-p15-constraints-when-max-size-greater-than-1-tb"></a>Einschränkungen von P11 und P15, wenn die maximale Größe 1 TB übersteigt

In allen Regionen außer den folgenden ist im Premium-Tarif derzeit mehr als 1 TB Speicher verfügbar: Regionen „China, Osten“, „China, Norden“, „Deutschland, Mitte“, „Deutschland, Nordosten“, „USA, Westen-Mitte“, „US DoD“ und „US Government, Mitte“. In diesen Regionen ist der Speicher im Tarif „Premium“ auf 1 TB begrenzt. Die folgenden Aspekte und Einschränkungen gelten für P11- und P15-Datenbanken mit einer maximalen Größe von mehr als 1 TB:

- Wenn die maximale Größe einer P11- oder P15-Datenbank jemals auf einen Wert über 1 TB festgelegt wurde, kann sie nur in einer P11- oder P15-Datenbank wiederhergestellt oder kopiert werden.  Demzufolge kann die Datenbank zu einer anderen Computegröße skaliert werden, sofern der zugewiesene Speicherplatz zum Zeitpunkt der Neuskalierung nicht die Maximalgrößen der neuen Computegröße überschreitet.
- Szenarien für aktive Georeplikation:
  - Einrichten einer Georeplikationsbeziehung: Eenn Falls es sich bei der primären Datenbank um eine P11- oder P15-Datenbank handelt, müssen auch die sekundären Datenbanken vom Typ „P11“ oder „P15“ sein. Eine niedrigere Computegröße wird für sekundäre Datenbanken abgelehnt, da sie nicht mehr als 1 TB unterstützen können.
  - Aktualisieren der primären Datenbank in einer Georeplikationsbeziehung: Die Änderung der maximalen Größe für eine primäre Datenbank in mehr als 1 TB löst die gleiche Änderung für die sekundäre Datenbank aus. Beide Upgrades müssen erfolgreich ausgeführt werden, damit die Änderung für die primäre Datenbank wirksam wird. Für die Option mit mehr als 1 TB gelten Regionseinschränkungen. Wenn sich die sekundäre Datenbank in einer Region befindet, die nicht mehr als 1 TB unterstützt, wird die primäre Datenbank nicht aktualisiert.
- Die Verwendung des Import/Export-Diensts zum Laden von Datenbanken des Typs „P11-P15“ mit mehr als 1 TB wird nicht unterstützt. Verwenden Sie „SqlPackage.exe“, um Daten zu [importieren](sql-database-import.md) und [exportieren](sql-database-export.md).

## <a name="next-steps"></a>Nächste Schritte

Allgemeine Ressourcenlimits finden Sie unter [Ressourcenlimits des auf virtuellen Kernen basierenden Kaufmodells in SQL-Datenbank – Pools für elastische Datenbanken](sql-database-vcore-resource-limits-single-databases.md) und unter [Ressourcenlimits des auf DTUs basierenden Kaufmodells in SQL-Datenbank – Pools für elastische Datenbanken](sql-database-dtu-resource-limits-single-databases.md).
