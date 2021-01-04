---
title: 'Tutorial: Erstellen und Verwalten von exportierten Daten aus Azure Cost Management'
description: Dieser Artikel erläutert, wie Sie aus Azure Cost Management exportierte Daten erstellen und verwalten können, um sie in externen Systemen zu verwenden.
author: bandersmsft
ms.author: banders
ms.date: 12/7/2020
ms.topic: tutorial
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: e3c1fa071cd23b871f754e89d6f17eb2cc44b394
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/14/2020
ms.locfileid: "97400351"
---
# <a name="tutorial-create-and-manage-exported-data"></a>Tutorial: Erstellen und Verwalten von exportierten Daten

Wenn Sie das Tutorial zur Kostenanalyse gelesen haben, dann sind Sie bereits mit dem manuellen Herunterladen Ihrer Daten zum Cost Management vertraut. Sie können jedoch eine wiederkehrende Aufgabe erstellen, die Ihre Cost Management-Daten täglich, wöchentlich oder monatlich automatisch in Azure Storage exportiert. Die Daten werden im CSV-Format exportiert und enthalten alle Informationen, die von Cost Management gesammelt wurden. Sie können dann die exportierten Daten in Azure Storage mit externen Systemen verwenden und mit Ihren eigenen benutzerdefinierten Daten kombinieren. Darüber hinaus können Sie Ihre exportierten Daten in einem externen System wie einem Dashboard oder einem anderen Finanzsystem verwenden.

Sehen Sie sich das Video [How to schedule exports to storage with Azure Cost Management (Planen von Exporten in den Speicher mit Azure Cost Management)](https://www.youtube.com/watch?v=rWa_xI1aRzo) an. Darin wird gezeigt, wie ein geplanter Export Ihrer Azure-Kostendaten in Azure Storage erstellt wird. Weitere Videos finden Sie im [YouTube-Kanal zu Cost Management](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/rWa_xI1aRzo]

Die Beispiele in diesem Tutorial zeigen Ihnen, wie Sie Ihre Cost Management-Daten exportieren und dann überprüfen, ob die Daten erfolgreich exportiert wurden.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen eines täglichen Exports
> * Überprüfen, ob Daten gesammelt wurden

## <a name="prerequisites"></a>Voraussetzungen
Datenexport ist für verschiedene Azure-Kontotypen einschließlich [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)-Kunden und Kunden mit [Microsoft-Kundenvereinbarung](get-started-partners.md) verfügbar. Die vollständige Liste der unterstützten Kontotypen finden Sie unter [Grundlegendes zu Cost Management-Daten](understand-cost-mgt-data.md). Die folgenden Azure-Berechtigungen oder Bereiche werden pro Abonnement für den Datenexport nach Benutzer und Gruppe unterstützt. Weitere Informationen zu Bereichen finden Sie unter [Verstehen von und Arbeiten mit Bereichen](understand-work-scopes.md).

- Besitzer – kann geplante Exporte für ein Abonnement erstellen, ändern oder löschen.
- Mitwirkende – kann eigene geplante Exporte erstellen, ändern oder löschen. Kann den Namen der von anderen Personen erstellten Exporte ändern.
- Leser – kann Exporte planen, für die er die Berechtigung hat.

Für Azure Storage-Konten:
- Um das konfigurierte Speicherkonto zu ändern, sind unabhängig von den Berechtigungen für den Export Schreibberechtigungen erforderlich.
- Ihr Azure Storage-Konto muss für Blob oder File Storage konfiguriert werden.

Bei einem neuen Abonnement können Cost Management-Features nicht sofort genutzt werden. Es kann bis zu 48 Stunden dauern, bis Sie alle Cost Management-Features verwenden können.

## <a name="sign-in-to-azure"></a>Anmelden bei Azure
Melden Sie sich unter [https://portal.azure.com](https://portal.azure.com/) beim Azure-Portal an.

## <a name="create-a-daily-export"></a>Erstellen eines täglichen Exports

### <a name="portal"></a>[Portal](#tab/azure-portal)

Zum Erstellen oder Anzeigen eines Datenexports bzw. Planen eines Exports öffnen Sie den gewünschten Bereich im Azure-Portal, und wählen Sie **Kostenanalyse** im Menü aus. Navigieren Sie beispielsweise zu **Abonnements**, und wählen Sie dann ein Abonnement in der Liste und **Kostenanalyse** im Menü aus. Wählen Sie oben auf der Seite „Kostenanalyse“ die Option **Einstellungen** und dann **Exporte** aus.

> [!NOTE]
> - Neben Abonnements können Sie Exporte für Ressourcengruppen, Verwaltungsgruppen, Abteilungen und Registrierungen erstellen. Weitere Informationen zu Bereichen finden Sie unter [Verstehen von und Arbeiten mit Bereichen](understand-work-scopes.md).
>- Wenn Sie als Partner im Abrechnungskontobereich oder beim Mandanten eines Kunden angemeldet sind, können Sie Daten in ein Azure Storage-Konto exportieren, das mit dem Partnerspeicherkonto verknüpft ist. Sie müssen jedoch über ein aktives Abonnement in Ihrem CSP-Mandanten verfügen.

1. Wählen Sie **Hinzufügen** aus, und geben Sie einen Namen für den Export ein.
1. Wählen Sie eine Option für **Metrik** aus:
    - **Tatsächliche Kosten (Nutzung und Anschaffungen)** : Wählen Sie diese Option aus, um die standardmäßige Nutzung und Käufe zu exportieren.
    - **Amortisierte Kosten (Nutzung und Anschaffungen)** : Wählen Sie diese Option aus, um die amortisierten Kosten für Käufe wie Azure-Reservierungen zu exportieren.
1. Wählen Sie eine Option für **Exporttyp** aus:
    - **Täglicher Export der Kosten für bisherigen Kalendermonat**: Erstellt täglich eine neue Exportdatei Ihrer Kosten für den bisherigen Kalendermonat. Die aktuellen Daten werden aus vorhergehenden täglichen Exporten aggregiert.
    - **Weekly export of cost for the last seven days** (Wöchentlicher Export der Kosten für die letzten sieben Tage): Erstellt einen wöchentlichen Export Ihrer Kosten für die letzten sieben Tagen ab dem ausgewählten Startdatum des Exports.
    - **Monthly export of last month's costs** (Monatlicher Export der Kosten des letzten Monats): Erstellt einen Export mit einem Vergleich der Kosten des letzten Monats und des aktuellen Monats, in dem Sie den Export erstellen. In Zukunft wird gemäß dem Zeitplan am fünften Tag jedes neuen Monats ein Export mit den Kosten des vorangegangenen Monats ausgeführt.
    - **One-time export** (Einmaliger Export): Ermöglicht es Ihnen, einen Datumsbereich für historische Daten auszuwählen, die Sie in Azure Blob Storage exportieren möchten. Sie können historische Kosten für maximal 90 Tage ab dem ausgewählten Tag exportieren. Dieser Export wird sofort ausgeführt und ist innerhalb von zwei Stunden in Ihrem Speicherkonto verfügbar.
        Wählen Sie je nach Exporttyp entweder ein Startdatum oder ein Datum für **Von** und **Bis** aus.
1. Geben Sie das Abonnement für Ihr Azure-Speicherkonto an, und wählen Sie dann eine Ressourcengruppe aus, oder erstellen Sie eine neue Ressourcengruppe.
1. Wählen Sie den Namen des Speicherkontos aus, oder erstellen Sie ein neues Speicherkonto.
1. Wählen Sie den Standort (Azure-Region) aus.
1. Geben Sie den Speichercontainer und den Verzeichnispfad an, unter dem die Exportdatei gespeichert werden soll.
    :::image type="content" source="./media/tutorial-export-acm-data/basics_exports.png" alt-text="Beispiel für einen neuen Export" lightbox="./media/tutorial-export-acm-data/basics_exports.png":::
1. Überprüfen Sie die Exportdetails, und klicken Sie auf **Erstellen**.

Ihr neuer Export wird in der Liste der Exporte angezeigt. Neue Exporte sind standardmäßig aktiviert. Wenn Sie einen geplanten Export deaktivieren oder löschen möchten, wählen Sie ein beliebiges Element in der Liste und anschließend entweder **Deaktivieren** oder **Löschen** aus.

Zunächst kann es 12 –24 Stunden dauern, bis der Export ausgeführt wird. Bis die Daten in exportierten Dateien angezeigt werden, kann jedoch mehr Zeit in Anspruch nehmen.

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Bereiten Sie zunächst Ihre Umgebung für die Azure-Befehlszeilenschnittstelle vor:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

1. Verwenden Sie nach der Anmeldung den Befehl [az costmanagement export list](/cli/azure/ext/costmanagement/costmanagement/export#ext_costmanagement_az_costmanagement_export_list), um Ihre aktuellen Exporte anzuzeigen:

   ```azurecli
   az costmanagement export list --scope "subscriptions/00000000-0000-0000-0000-000000000000"
   ```

   >[!NOTE]
   >
   >* Exporte können nicht nur für Abonnements, sondern auch für Ressourcengruppen und Verwaltungsgruppen erstellt werden. Weitere Informationen zu Bereichen finden Sie unter [Verstehen von und Arbeiten mit Bereichen](understand-work-scopes.md).
   >* Wenn Sie als Partner im Abrechnungskontobereich oder beim Mandanten eines Kunden angemeldet sind, können Sie Daten in ein Azure Storage-Konto exportieren, das mit dem Partnerspeicherkonto verknüpft ist. Sie müssen jedoch über ein aktives Abonnement in Ihrem CSP-Mandanten verfügen.

1. Erstellen Sie eine Ressourcengruppe, oder verwenden Sie eine vorhandene Ressourcengruppe. Verwenden Sie zum Erstellen einer Ressourcengruppe den Befehl [az group create](/cli/azure/group#az_group_create):

   ```azurecli
   az group create --name TreyNetwork --location "East US"
   ```

1. Erstellen Sie ein Speicherkonto zum Empfangen der Exporte, oder verwenden Sie ein vorhandenes Speicherkonto. Verwenden Sie zum Erstellen eines Speicherkontos den Befehl [az storage account create](/cli/azure/storage/account#az_storage_account_create):

   ```azurecli
   az storage account create --resource-group TreyNetwork --name cmdemo
   ```

1. Führen Sie den Befehl [az costmanagement export create](/cli/azure/ext/costmanagement/costmanagement/export#ext_costmanagement_az_costmanagement_export_create) aus, um den Export zu erstellen:

   ```azurecli
   az costmanagement export create --name DemoExport --type ActualCost \
   --scope "subscriptions/00000000-0000-0000-0000-000000000000" --storage-account-id cmdemo \
   --storage-container democontainer --timeframe MonthToDate --recurrence Daily \
   --recurrence-period from="2020-06-01T00:00:00Z" to="2020-10-31T00:00:00Z" \
   --schedule-status Active --storage-directory demodirectory
   ```

   Für den Parameter **--type** können Sie `ActualCost`, `AmortizedCost`oder `Usage` verwenden.

   In diesem Beispiel wird `MonthToDate` verwendet. Durch den Export wird täglich eine Exportdatei Ihrer Kosten für den bisherigen Kalendermonat erstellt. Die neuesten Daten werden aus vorhergehenden täglichen Exporten des aktuellen Monats aggregiert.

1. Verwenden Sie zum Anzeigen der Details Ihres Exportvorgangs den Befehl [az costmanagement export show](/cli/azure/ext/costmanagement/costmanagement/export#ext_costmanagement_az_costmanagement_export_show):

   ```azurecli
   az costmanagement export show --name DemoExport \
      --scope "subscriptions/00000000-0000-0000-0000-000000000000"
   ```

1. Aktualisieren Sie einen Export mithilfe des Befehls [az costmanagement export update](/cli/azure/ext/costmanagement/costmanagement/export#ext_costmanagement_az_costmanagement_export_update):

   ```azurecli
   az costmanagement export update --name DemoExport
      --scope "subscriptions/00000000-0000-0000-0000-000000000000" --storage-directory demodirectory02
   ```

   In diesem Beispiel wird das Ausgabeverzeichnis geändert.

>[!NOTE]
>Zunächst kann es 12 –24 Stunden dauern, bis der Export ausgeführt wird. Es kann jedoch länger dauern, bis die Daten in exportierten Dateien angezeigt werden.

Zum Löschen eines Exports kann der Befehl [az costmanagement export delete](/cli/azure/ext/costmanagement/costmanagement/export#ext_costmanagement_az_costmanagement_export_delete) verwendet werden:

```azurecli
az costmanagement export delete --name DemoExport --scope "subscriptions/00000000-0000-0000-0000-000000000000"
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Bereiten Sie zunächst Ihre Umgebung für Azure PowerShell vor:

[!INCLUDE [azure-powershell-requirements-no-header.md](../../../includes/azure-powershell-requirements-no-header.md)]

  > [!IMPORTANT]
  > Solange nur eine Vorschauversion des PowerShell-Moduls **Az.CostManagement** verfügbar ist, müssen Sie es separat mithilfe des Cmdlets `Install-Module` installieren. Sobald dieses PowerShell-Modul allgemein verfügbar ist, wird es in die zukünftigen Releases des Az PowerShell-Moduls integriert und in Azure Cloud Shell standardmäßig zur Verfügung gestellt.

  ```azurepowershell-interactive
  Install-Module -Name Az.CostManagement
  ```

1. Verwenden Sie nach der Anmeldung das Cmdlet [Get-AzCostManagementExport](/powershell/module/Az.CostManagement/get-azcostmanagementexport), um Ihre aktuellen Exporte anzuzeigen:

   ```azurepowershell-interactive
   Get-AzCostManagementExport -Scope 'subscriptions/00000000-0000-0000-0000-000000000000'
   ```

   >[!NOTE]
   >
   >* Exporte können nicht nur für Abonnements, sondern auch für Ressourcengruppen und Verwaltungsgruppen erstellt werden. Weitere Informationen zu Bereichen finden Sie unter [Verstehen von und Arbeiten mit Bereichen](understand-work-scopes.md).
   >* Wenn Sie als Partner im Abrechnungskontobereich oder beim Mandanten eines Kunden angemeldet sind, können Sie Daten in ein Azure Storage-Konto exportieren, das mit dem Partnerspeicherkonto verknüpft ist. Sie müssen jedoch über ein aktives Abonnement in Ihrem CSP-Mandanten verfügen.

1. Erstellen Sie eine Ressourcengruppe, oder verwenden Sie eine vorhandene Ressourcengruppe. Zum Erstellen einer Ressourcengruppe verwenden Sie das Cmdlet [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup):

   ```azurepowershell-interactive
   New-AzResourceGroup -Name TreyNetwork -Location eastus
   ```

1. Erstellen Sie ein Speicherkonto zum Empfangen der Exporte, oder verwenden Sie ein vorhandenes Speicherkonto. Zum Erstellen eines Speicherkontos verwenden Sie das Cmdlet [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount):

   ```azurepowershell-interactive
   New-AzStorageAccount -ResourceGroupName TreyNetwork -AccountName cmdemo -SkuName Standard_RAGRS -Location eastus
   ```

1. Führen Sie das Cmdlet [New-AzCostManagementExport](/powershell/module/Az.CostManagement/new-azcostmanagementexport) aus, um den Export zu erstellen:

   ```azurepowershell-interactive
   $Params = @{
     Name = 'DemoExport'
     DefinitionType = 'ActualCost'
     Scope = 'subscriptions/00000000-0000-0000-0000-000000000000'
     DestinationResourceId = '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/treynetwork/providers/Microsoft.Storage/storageAccounts/cmdemo'
     DestinationContainer = 'democontainer'
     DefinitionTimeframe = 'MonthToDate'
     ScheduleRecurrence = 'Daily'
     RecurrencePeriodFrom = '2020-06-01T00:00:00Z'
     RecurrencePeriodTo = '2020-10-31T00:00:00Z'
     ScheduleStatus = 'Active'
     DestinationRootFolderPath = 'demodirectory'
     Format = 'Csv'
   }
   New-AzCostManagementExport @Params
   ```

   Für den Parameter **DefinitionType** können Sie `ActualCost`, `AmortizedCost`oder `Usage` verwenden.

   In diesem Beispiel wird `MonthToDate` verwendet. Durch den Export wird täglich eine Exportdatei Ihrer Kosten für den bisherigen Kalendermonat erstellt. Die neuesten Daten werden aus vorhergehenden täglichen Exporten des aktuellen Monats aggregiert.

1. Zum Anzeigen der Details des Exportvorgangs verwenden Sie das Cmdlet `Get-AzCostManagementExport`:

   ```azurepowershell-interactive
   Get-AzCostManagementExport -Scope 'subscriptions/00000000-0000-0000-0000-000000000000'
   ```

1. Aktualisieren Sie einen Export mithilfe des Cmdlets [Update-AzCostManagementExport](/powershell/module/Az.CostManagement/update-azcostmanagementexport):

   ```azurepowershell-interactive
   Update-AzCostManagementExport -Name DemoExport -Scope 'subscriptions/00000000-0000-0000-0000-000000000000' -DestinationRootFolderPath demodirectory02
   ```

   In diesem Beispiel wird das Ausgabeverzeichnis geändert.

>[!NOTE]
>Zunächst kann es 12 –24 Stunden dauern, bis der Export ausgeführt wird. Es kann jedoch länger dauern, bis die Daten in exportierten Dateien angezeigt werden.

Zum Löschen eines Exports kann das Cmdlet [Remove-AzCostManagementExport](/powershell/module/Az.CostManagement/remove-azcostmanagementexport) verwendet werden:

```azurepowershell-interactive
Remove-AzCostManagementExport -Name DemoExport -Scope 'subscriptions/00000000-0000-0000-0000-000000000000'
```

---

### <a name="export-schedule"></a>Zeitplan für Export

Uhrzeit und Wochentag der ersten Erstellung eines Exports wirken sich auf geplante Exporte aus. Wenn Sie einen geplanten Export erstellen, werden alle folgenden Exportvorgänge mit der gleichen Häufigkeit ausgeführt. Legen Sie für einen täglichen Export der Kosten für den bisherigen Kalendermonat beispielsweise „täglich“ als Häufigkeit fest. Der Export wird dann jeden Tag ausgeführt. Das Gleiche gilt für einen wöchentlichen Export: Der wöchentliche Export wird jede Woche am selben geplanten Tag ausgeführt. Die genaue Übermittlungszeit des Exports wird nicht garantiert, und die exportierten Daten sind innerhalb von vier Stunden ab dem Ausführungszeitpunkt verfügbar.

Bei jedem Export wird eine neue Datei erstellt, sodass ältere Exporte nicht überschrieben werden.

#### <a name="create-an-export-for-multiple-subscriptions"></a>Erstellen eines Exports für mehrere Abonnements

Wenn Sie über ein Enterprise Agreement verfügen, können Sie eine Verwaltungsgruppe verwenden, um die Abonnementkosteninformationen in einem einzelnen Container zu aggregieren. Anschließend können Sie Kostenverwaltungsdaten für die Verwaltungsgruppe exportieren.

Exporte für Verwaltungsgruppen anderer Abonnementtypen werden nicht unterstützt.

1. Wenn Sie noch keine Verwaltungsgruppe haben, erstellen Sie eine, und weisen Sie ihr Abonnements zu.
1. Legen Sie in der Kostenanalyse den Bereich auf Ihre Verwaltungsgruppe fest, und wählen Sie **Diese Verwaltungsgruppe auswählen** aus.
    :::image type="content" source="./media/tutorial-export-acm-data/management-group-scope.png" alt-text="Beispiel mit der Option „Diese Verwaltungsgruppe auswählen“" lightbox="./media/tutorial-export-acm-data/management-group-scope.png":::
1. Erstellen Sie einen Export im Bereich, um Kostenverwaltungsdaten für die Abonnements in der Verwaltungsgruppe abzurufen.
    :::image type="content" source="./media/tutorial-export-acm-data/new-export-management-group-scope.png" alt-text="Beispiel mit der Option zum Erstellen eines neuen Exports mit einem Verwaltungsgruppenbereich":::

## <a name="verify-that-data-is-collected"></a>Überprüfen, ob Daten gesammelt wurden

Sie können ganz einfach überprüfen, ob Ihre Cost Management-Daten erfasst werden, und die exportierte CSV-Datei mit dem Azure Storage-Explorer anzeigen.

Wählen Sie in der Exportliste den Namen des Speicherkontos aus. Wählen Sie auf der Seite des Speicherkontos die Option „Im Explorer öffnen“ aus Wenn ein Bestätigungsdialogfeld angezeigt wird, wählen Sie **Ja** aus, um die Datei in Azure Storage-Explorer zu öffnen.

![Speicherkontoseite mit Beispielinformationen und Link „In Explorer öffnen“](./media/tutorial-export-acm-data/storage-account-page.png)

Navigieren Sie im Storage-Explorer zu dem Container, den Sie öffnen möchten, und wählen Sie den Ordner aus, der dem aktuellen Monat entspricht. Es wird eine Liste der CSV-Dateien angezeigt. Wählen Sie eine Datei und anschließend **Öffnen** aus.

![In Storage-Explorer angezeigte Beispielinformationen](./media/tutorial-export-acm-data/storage-explorer.png)

Die Datei wird mit dem Programm oder der Anwendung geöffnet, die zum Öffnen von CSV-Dateierweiterungen ausgewählt ist. Hier sehen Sie ein Beispiel in Excel.

![Beispiel für in Excel angezeigte exportierte CSV-Daten](./media/tutorial-export-acm-data/example-export-data.png)

### <a name="download-an-exported-csv-data-file"></a>Herunterladen einer exportierten CSV-Datendatei

Sie können auch die exportierte CSV-Datei im Azure-Portal herunterladen. In den folgenden Schritten wird erläutert, wie Sie diese in der Kostenanalyse herausfinden.

1. Wählen Sie unter Kostenanalyse die Option **Einstellungen** und dann **Exporte** aus.
1. Wählen Sie in der Liste der Exporte das Speicherkonto für einen Export aus.
1. Wählen Sie in Ihrem Speicherkonto die Option **Container** aus.
1. Wählen Sie in der Containerliste einen Container aus.
1. Navigieren Sie durch die Verzeichnisse und Speicherblobs zu dem gewünschten Datum.
1. Wählen Sie die CSV-Datei und dann **Herunterladen** aus.

[![Beispiel für Exportdownload](./media/tutorial-export-acm-data/download-export.png)](./media/tutorial-export-acm-data/download-export.png#lightbox)

## <a name="view-export-run-history"></a>Anzeigen des Ausführungsverlaufs

Sie können den Ausführungsverlauf Ihres geplanten Exports anzeigen, indem Sie auf der Listenseite „Exporte“ einen einzelnen Export auswählen. Auf dieser Seite können Sie auch schnell die Ausführungszeit Ihrer vorherigen Exporte und den Zeitpunkt der nächsten Ausführung eines Exports anzeigen. Der folgende Screenshot zeigt ein Beispiel für den Ausführungsverlauf.

:::image type="content" source="./media/tutorial-export-acm-data/run-history.png" alt-text="Screenshot des Bereichs „Exporte“":::

Wählen Sie einen Export aus, um den zugehörigen Ausführungsverlauf anzuzeigen.

:::image type="content" source="./media/tutorial-export-acm-data/single-export-run-history.png" alt-text="Screenshot des Ausführungsverlaufs eines Exports":::

## <a name="access-exported-data-from-other-systems"></a>Zugreifen auf exportierte Daten über andere Systeme

Einer der Gründe für den Export Ihrer Daten aus Cost Management ist der Zugriff auf die Daten über externe Systeme. Sie können ein Dashboard oder ein anderes Finanzsystem verwenden. Derartige Systeme unterscheiden sich stark, sodass es nicht praktikabel wäre, ein Beispiel zu zeigen.  Sie können jedoch mit dem Zugriff auf Ihre Daten aus Ihren Anwendungen unter [Einführung in Azure Storage](../../storage/common/storage-introduction.md) beginnen.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Erstellen eines täglichen Exports
> * Überprüfen, ob Daten gesammelt wurden

Fahren Sie mit dem nächsten Tutorial fort, um sich Möglichkeiten der Optimierung und Steigerung der Effizienz anzuschauen, indem ungenutzte oder nicht ausreichend genutzte Ressourcen ermittelt werden.

> [!div class="nextstepaction"]
> [Prüfen und Reagieren auf Empfehlungen zur Optimierung](tutorial-acm-opt-recommendations.md)
