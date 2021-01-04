---
title: Abbrechen von Verwaltungsvorgängen
titleSuffix: Azure SQL Managed Instance
description: Erfahren Sie, wie Sie Verwaltungsvorgänge für Azure SQL Managed Instance abbrechen können.
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
ms.openlocfilehash: 342491178d55dacbdc68e6c9042623d381dff898
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861543"
---
# <a name="canceling-azure-sql-managed-instance-management-operations"></a>Abbrechen von Verwaltungsvorgängen für Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL Managed Instance bietet die Möglichkeit, einige [Verwaltungsvorgänge](management-operations-overview.md) abzubrechen, z. B. wenn Sie eine neue verwaltete Instanz bereitstellen oder Instanzeigenschaften aktualisieren. 

## <a name="overview"></a>Übersicht

 Alle Verwaltungsvorgänge können wie folgt kategorisiert werden:

- Instanzbereitstellung (Erstellung neuer Instanzen)
- Instanzupdate (Änderung von Instanzeigenschaften, z. B. virtuelle Kerne oder reservierter Speicher)
- Instanzlöschung

Sie können [den Fortschritt und den Status von Verwaltungsvorgängen überwachen](management-operations-monitor.md) und einige dieser Vorgänge ggf. abbrechen. 

In der folgenden Tabelle sind Verwaltungsvorgänge, unabhängig davon, ob Sie diese abbrechen können oder nicht, und deren typische Gesamtdauer zusammengestellt:

Category  |Vorgang  |Kann abgebrochen werden  |Geschätzte Abbruchdauer  |
|---------|---------|---------|---------|
|Bereitstellung |Erstellen einer Instanz |Ja |90 % der Vorgänge werden innerhalb von fünf Minuten abgeschlossen. |
|Aktualisieren |Zentrales Hoch-/Herunterskalieren des Instanzspeichers (universell) |Nein |  |
|Aktualisieren |Zentrales Hoch-/Herunterskalieren des Instanzspeichers (unternehmenskritisch) |Ja |90 % der Vorgänge werden innerhalb von fünf Minuten abgeschlossen. |
|Aktualisieren |Zentrales Hoch-/Herunterskalieren der Computekapazität (V-Kerne) (Universell) |Ja |90 % der Vorgänge werden innerhalb von fünf Minuten abgeschlossen. |
|Aktualisieren |Zentrales Hoch-/Herunterskalieren der Computekapazität (V-Kerne) (Unternehmenskritisch) |Ja |90 % der Vorgänge werden innerhalb von fünf Minuten abgeschlossen. |
|Aktualisieren |Änderung der Instanzdienstebene („Universell“ in „Unternehmenskritisch“ und umgekehrt) |Ja |90 % der Vorgänge werden innerhalb von fünf Minuten abgeschlossen. |
|Löschen |Instanzlöschung |Nein |  |
|Löschen |Löschung eines virtuellen Clusters (als vom Benutzer initiierter Vorgang) |Nein |  |

## <a name="cancel-management-operation"></a>Abbrechen eines Verwaltungsvorgangs

# <a name="portal"></a>[Portal](#tab/azure-portal)

Führen Sie die folgenden Schritte aus, um einen Verwaltungsvorgang über das Azure-Portal abzubrechen:

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com).
1. Wechseln Sie zum **Übersicht**-Blatt Ihrer verwalteten SQL-Instanz. 
1. Wählen Sie unter **Benachrichtigungen** das Feld für den laufenden Vorgang aus, um die Seite **Laufende Vorgänge** zu öffnen. 

   :::image type="content" source="media/management-operations-cancel/open-ongoing-operation.png" alt-text="Wählen Sie das Feld für den laufenden Vorgang aus, um die Seite „Laufende Vorgänge“ zu öffnen.":::

1. Wählen Sie **Diesen Vorgang abbrechen** unten auf der Seite aus. 

   :::image type="content" source="media/management-operations-cancel/cancel-operation.png" alt-text="„Diesen Vorgang abbrechen“ auswählen, um den Vorgang abzubrechen.":::

1. Bestätigen Sie, dass Sie den Vorgang abbrechen möchten. 


Wenn die Abbruchanforderung erfolgreich ist, wird der Verwaltungsvorgang abgebrochen, und das Ergebnis ist ein Fehler. Sie erhalten eine Benachrichtigung, dass das Abbrechen erfolgreich war oder fehlgeschlagen ist.

![Ergebnis des Vorgangsabbruchs](./media/management-operations-cancel/canceling-operation-result.png)


Wenn bei der Abbruchanforderung ein Fehler auftritt oder wenn die Schaltfläche „Abbrechen“ nicht aktiv ist, bedeutet das, dass der Verwaltungsvorgang einen nicht abbrechbaren Zustand erreicht hat und bald abgeschlossen wird.  Der Verwaltungsvorgang wird so lange ausgeführt, bis er abgeschlossen wurde.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Sollte Azure PowerShell noch nicht installiert sein, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps) weitere Informationen.

Um einen Verwaltungsvorgang abzubrechen, müssen Sie dessen Namen angeben. Verwenden Sie daher zuerst den get-Befehl, um die Vorgangsliste abzurufen, und brechen Sie dann den speziellen Vorgang ab.

```powershell-interactive
$managedInstance = "<Your-instance-name>"
$resourceGroup = "<Your-resource-group-name>"

$managementOperations = Get-AzSqlInstanceOperation -ManagedInstanceName $managedInstance  -ResourceGroupName $resourceGroup

foreach ($mo in $managementOperations ) {
    if($mo.State -eq "InProgress" -and $mo.IsCancellable){
        $cancelRequest = Stop-AzSqlInstanceOperation -ResourceGroupName $resourceGroup -ManagedInstanceName $managedInstance -Name $mo.Name
        Get-AzSqlInstanceOperation -ManagedInstanceName $managedInstance  -ResourceGroupName $resourceGroup -Name $mo.Name
    }
}
```

Eine ausführliche Beschreibung der Befehle finden Sie unter [Get-AzSqlInstanceOperation](/powershell/module/az.sql/get-azsqlinstanceoperation) und [Stop-AzSqlInstanceOperation](/powershell/module/az.sql/stop-azsqlinstanceoperation).

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Sollte die Azure CLI noch nicht installiert sein, lesen Sie [Installieren der Azure CLI](/cli/azure/install-azure-cli).

Um den Verwaltungsvorgang abzubrechen, müssen Sie dessen Namen angeben. Verwenden Sie daher zuerst den get-Befehl, um die Vorgangsliste abzurufen, und brechen Sie dann den speziellen Vorgang ab.

```azurecli-interactive
az sql mi op list -g yourResourceGroupName --mi yourInstanceName |
   --query "[?state=='InProgress' && isCancellable].{Name: name}" -o tsv |
while read -r operationName; do

az sql mi op cancel -g yourResourceGroupName --mi yourInstanceName -n $operationName
done
```

Eine ausführliche Beschreibung der Befehle finden Sie unter [az sql mi op](/cli/azure/sql/mi/op).

---

## <a name="canceled-deployment-request"></a>Abgebrochene Bereitstellungsanforderung

In der API-Version 2020-02-02 ist eine Instanz, sobald deren Erstellungsanforderung akzeptiert wurde, als Ressource vorhanden, unabhängig vom Status des Bereitstellungsprozesses (der Status der verwalteten Instanz ist **Provisioning**). Wenn Sie die Instanzbereitstellungsanforderung abbrechen (neue Instanzerstellung), wird der Status der verwalteten Instanz von **Provisioning** in **FailedToCreate** geändert.

Instanzen, die nicht erstellt werden konnten, sind weiterhin als Ressource vorhanden, und es gilt Folgendes: 

- Sie werden nicht geändert.
- Sie tragen nicht zu den Ressourcengrenzwerten bei (Subnetzkontingent oder Kontingent für virtuelle Kerne).


> [!NOTE]
> Um die Störungen in der Liste der Ressourcen oder der verwalteten Instanzen zu minimieren, löschen Sie Instanzen, die nicht bereitgestellt werden konnten, oder Instanzen mit abgebrochenen Bereitstellungen. 


## <a name="next-steps"></a>Nächste Schritte

- Im [Schnellstarthandbuch](instance-create-quickstart.md) erfahren Sie, wie Sie Ihre erste verwaltete Instanz erstellen.
- Eine Liste der Features und einen Funktionsvergleich finden Sie unter [Allgemeine SQL-Features](../database/features-comparison.md).
- Weitere Informationen zur VNET-Konfiguration finden Sie unter [Konfigurieren eines VNET für SQL Managed Instance](connectivity-architecture-overview.md).
- Eine Schnellstartanleitung, in der eine verwaltete Instanz erstellt und eine Datenbank aus einer Sicherungsdatei wiederhergestellt wird, finden Sie unter [Erstellen einer verwalteten Azure SQL-Datenbank-Instanz](instance-create-quickstart.md).
- Ein Tutorial zur Verwendung des Azure Database Migration Service für die Migration finden Sie unter [SQL Managed Instance-Migration mithilfe des Database Migration Service](../../dms/tutorial-sql-server-to-managed-instance.md).
