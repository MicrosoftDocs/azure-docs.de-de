---
title: Überwachen von Verwaltungsvorgängen
titleSuffix: Azure SQL Managed Instance
description: Erfahren Sie mehr über die verschiedenen Möglichkeiten zum Überwachen der Verwaltungsvorgänge für Azure SQL Managed Instance.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: urosmil
ms.author: urmilano
ms.reviewer: sstein, bonova, MashaMSFT
ms.date: 09/03/2020
ms.openlocfilehash: 0f76d2079b7ed5aacbf835540ea92febd034e2d0
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92782449"
---
# <a name="monitoring-azure-sql-managed-instance-management-operations"></a>Überwachen der Verwaltungsvorgänge für Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL Managed Instance ermöglicht die Überwachung der [Verwaltungsvorgänge](management-operations-overview.md), die Sie zum Bereitstellen von neuen verwalteten Instanzen, Aktualisieren von Instanzeigenschaften und Löschen von nicht mehr benötigten Instanzen verwenden. 

## <a name="overview"></a>Übersicht

Alle Verwaltungsvorgänge können wie folgt kategorisiert werden:

- Instanzbereitstellung (Erstellung neuer Instanzen)
- Instanzupdate (Änderung von Instanzeigenschaften, z. B. virtuelle Kerne oder reservierter Speicher)
- Instanzlöschung

Die meisten Verwaltungsvorgänge sind [zeitintensive Vorgänge](management-operations-overview.md#duration). Daher ist es erforderlich, den Status zu überwachen oder den Fortschritt der einzelnen Schritte eines Vorgangs nachzuverfolgen. 

Es gibt mehrere Möglichkeiten, Verwaltungsvorgänge für verwaltete Instanzen zu überwachen:

- [Bereitstellungen von Ressourcengruppen](../../azure-resource-manager/templates/deployment-history.md)
- [Aktivitätsprotokoll](../../azure-monitor/platform/activity-log.md)
- [API für Vorgänge verwalteter Instanzen](#managed-instance-operations-api)


In der folgenden Tabelle werden Überwachungsoptionen für Verwaltungsvorgänge verglichen: 

| Option | Aufbewahrung | Unterstützt das Abbrechen von Vorgängen | Erstellen | Aktualisieren | Löschen | Abbrechen | Schritte |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Bereitstellungen von Ressourcengruppen | Unbegrenzt<sup>1</sup> | Nein<sup>2</sup> | Sichtbar | Sichtbar | Wird nicht angezeigt | Sichtbar | Wird nicht angezeigt |
| Aktivitätsprotokoll | 90 Tage | Nein | Sichtbar | Sichtbar | Sichtbar | Sichtbar |  Wird nicht angezeigt |
| API für Vorgänge verwalteter Instanzen | 24 Stunden | [Ja](management-operations-cancel.md) | Sichtbar | Sichtbar | Sichtbar | Sichtbar | Sichtbar |
|  |  |  |  |  |  |  | |

<sup>1</sup> Der Bereitstellungsverlauf einer Ressourcengruppe ist auf 800 Bereitstellungen beschränkt.

<sup>2</sup> Bereitstellungen für Ressourcengruppen unterstützen das Abbrechen von Vorgängen. Aufgrund der Abbruchlogik werden jedoch nur Vorgänge abgebrochen, deren Bereitstellung nach Ausführen des Abbruchvorgangs geplant wurde. Die derzeit ausgeführte Bereitstellung wird nicht abgebrochen, wenn die Ressourcengruppenbereitstellung abgebrochen wird. Da die Bereitstellung verwalteter Instanzen aus einem einzigen zeitintensiven Schritt besteht (aus der Perspektive von Azure Resource Manager), wird durch das Abbrechen der Ressourcengruppenbereitstellung die Bereitstellung der verwalteten Instanz nicht abgebrochen, und der Vorgang wird abgeschlossen. 

## <a name="managed-instance-operations-api"></a>API für Vorgänge verwalteter Instanzen

APIs für Verwaltungsvorgänge sind speziell für die Vorgangsüberwachung konzipiert. Das Überwachen der Vorgänge verwalteter Instanzen kann Erkenntnisse zu Vorgangsparametern und -schritten sowie [abbruchspezifischen Vorgängen](management-operations-cancel.md) liefern. Diese API kann nicht nur für Vorgangsdetails und den Abbruchbefehl, sondern auch in Automatisierungsskripts mit Bereitstellungen für mehrere Ressourcen verwendet werden. Sie können basierend auf dem Statusschritt eine abhängige Ressourcenbereitstellung starten.

Die APIs lauten wie folgt: 

| Get-Help | Beschreibung |
| --- | --- |
|[Vorgänge verwalteter Instanzen – Abrufen](/rest/api/sql/managedinstanceoperations/get)|Ruft einen Verwaltungsvorgang für eine verwaltete Instanz ab.|
|[Vorgänge verwalteter Instanzen – Abbrechen](/rest/api/sql/managedinstanceoperations/cancel)|Bricht den asynchronen Vorgang für die verwaltete Instanz ab.|
|[Vorgänge verwalteter Instanzen – Auflisten nach verwalteter Instanz](/rest/api/sql/managedinstanceoperations/listbymanagedinstance)|Ruft eine Liste der für die verwaltete Instanz ausgeführten Vorgänge ab.|

> [!NOTE]
> Verwenden Sie API-Version 2020-02-02, um den Erstellungsvorgang für verwaltete Instanzen in der Liste der Vorgänge anzuzeigen. Dies ist die Standardversion, die im Azure-Portal und den neuesten PowerShell- und Azure CLI-Paketen verwendet wird.

## <a name="monitor-operations"></a>Überwachen von Vorgängen

# <a name="portal"></a>[Portal](#tab/azure-portal)

Verwenden Sie im Azure-Portal die Managed Instance-Seite **Übersicht** , um Vorgänge für verwaltete Instanzen zu überwachen. 

Beispielsweise wird auf der Seite **Übersicht** zu Beginn des Erstellungsprozesses der **Erstellungsvorgang** angezeigt: 

![Status der Erstellung der verwalteten Instanz](./media/management-operations-monitor/monitoring-create-operation.png)

Wählen Sie **Laufender Vorgang** aus, um die Seite **Laufender Vorgang** zu öffnen, und zeigen Sie den Vorgang **Erstellen** oder **Aktualisieren** an. Sie können auf dieser Seite auch Vorgänge [abbrechen](management-operations-cancel.md).  

![Details zum Vorgang für verwaltete Instanzen](./media/management-operations-monitor/monitoring-operation-details.png)

> [!NOTE]
> Erstellungsvorgänge, die über das Azure-Portal, PowerShell, die Azure CLI oder andere Tools übermittelt werden, die Version 2020-02-02 der REST-API verwenden, [können abgebrochen werden](management-operations-cancel.md). Bei Verwendung einer REST-API-Version vor Version 2020-02-02 zum Übermitteln eines Erstellungsvorgangs wird die Instanzbereitstellung gestartet, die Bereitstellung wird jedoch nicht in der Vorgangs-API aufgelistet und kann nicht abgebrochen werden.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Mit dem Cmdlet „Get-AzSqlInstanceOperation“ werden Informationen zu den Vorgängen für eine verwaltete Instanz abgerufen. Sie können alle Vorgänge für eine verwaltete Instanz anzeigen oder einen bestimmten Vorgang anzeigen, indem Sie den Vorgangsnamen angeben.

```powershell-interactive
$managedInstance = "yourInstanceName"
$resourceGroup = "yourResourceGroupName"

$managementOperations = Get-AzSqlInstanceOperation `
    -ManagedInstanceName $managedInstance  -ResourceGroupName $resourceGroup
```

Eine ausführliche Beschreibung der Befehle finden Sie unter [Get-AzSqlInstanceOperation](/powershell/module/az.sql/get-azsqlinstanceoperation).

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Mit „az sql mi op list“ wird eine Liste der für die verwaltete Instanz ausgeführten Vorgänge abgerufen. Sollte die Azure CLI noch nicht installiert sein, lesen Sie [Installieren der Azure CLI](/cli/azure/install-azure-cli).

```azurecli-interactive
az sql mi op list -g yourResourceGroupName --mi yourInstanceName 
```

Eine ausführliche Beschreibung der Befehle finden Sie unter [az sql mi op](/cli/azure/sql/mi/op).

---

## <a name="next-steps"></a>Nächste Schritte

- Im [Schnellstarthandbuch](instance-create-quickstart.md) erfahren Sie, wie Sie Ihre erste verwaltete Instanz erstellen.
- Eine Liste der Features und einen Funktionsvergleich finden Sie unter [Allgemeine SQL-Features](../database/features-comparison.md).
- Weitere Informationen zur VNET-Konfiguration finden Sie unter [Konfigurieren eines VNET für SQL Managed Instance](connectivity-architecture-overview.md).
- Eine Schnellstartanleitung, in der eine verwaltete Instanz erstellt und eine Datenbank aus einer Sicherungsdatei wiederhergestellt wird, finden Sie unter [Erstellen einer verwalteten Azure SQL-Datenbank-Instanz](instance-create-quickstart.md).
- Ein Tutorial zur Verwendung des Azure Database Migration Service für die Migration finden Sie unter [SQL Managed Instance-Migration mithilfe des Database Migration Service](../../dms/tutorial-sql-server-to-managed-instance.md).