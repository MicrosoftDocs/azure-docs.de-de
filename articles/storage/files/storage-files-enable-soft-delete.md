---
title: 'Aktivieren des vorläufigen Löschens: Azure-Dateifreigaben'
description: Hier erfahren Sie, wie Sie das vorläufige Löschen für Azure-Dateifreigaben aktivieren, um die Wiederherstellung von Daten zu ermöglichen und versehentliches Löschen zu verhindern.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 12/01/2020
ms.author: rogarana
ms.subservice: files
services: storage
ms.openlocfilehash: ea98b2d9812fb5c848c7e13b94d46a4142595cd4
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96492164"
---
# <a name="enable-soft-delete-on-azure-file-shares"></a>Aktivieren des vorläufigen Löschens für Azure-Dateifreigaben

Azure Storage bietet vorläufiges Löschen für Dateifreigaben, damit Sie Ihre Daten leichter wiederherstellen können, wenn diese irrtümlich von einer Anwendung oder von einem anderen Benutzer des Speicherkontos gelöscht wurden. Weitere Informationen zum vorläufigen Löschen finden Sie unter [Verhindern des versehentlichen Löschens von Azure-Dateifreigaben](storage-files-prevent-file-share-deletion.md).

In den folgenden Abschnitten erfahren Sie, wie Sie vorläufiges Löschen für Azure-Dateifreigaben eines bereits vorhandenen Speicherkontos aktivieren und verwenden:

# <a name="portal"></a>[Portal](#tab/azure-portal)

## <a name="getting-started"></a>Erste Schritte

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
1. Navigieren Sie zu Ihrem Speicherkonto, und wählen Sie unter **Dateidienst** die Option **Vorläufiges Löschen** aus.
1. Wählen Sie für **Vorläufiges Löschen von Dateifreigaben** die Option **Aktiviert** aus.
1. Wählen Sie **Aufbewahrungsdauer für Dateifreigabe in Tagen** aus, und geben Sie eine Zahl Ihrer Wahl ein.
1. Wählen Sie **Speichern** aus, um die Einstellungen für die Datenaufbewahrung zu bestätigen.

:::image type="content" source="media/storage-how-to-recover-deleted-account/enable-soft-delete-files.png" alt-text="Screenshot: Speicherkontobereich mit den Einstellungen für vorläufiges Löschen. Hervorgehoben sind der Dateifreigabebereich, die Aktivierungsoption, das Festlegen eines Aufbewahrungszeitraums, und die Speicheroption. Dadurch wird vorläufiges löschen für alle Dateifreigaben in Ihrem Speicherkonto aktiviert.":::

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Cmdlets für vorläufiges Löschen sind ab Version 2.1.3 des [Azure CLI-Moduls](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) verfügbar.

## <a name="getting-started-with-cli"></a>Erste Schritte mit der CLI

Zum Aktivieren des vorläufigen Löschens müssen die Diensteigenschaften eines Dateiclients aktualisiert werden. Im folgenden Beispiel wird das vorläufige Löschen für alle Dateifreigaben in einem Speicherkonto aktiviert:

```azurecli
az storage account file-service-properties update --enable-delete-retention true -n yourStorageaccount -g yourResourceGroup
```

Mit dem folgenden Befehl können Sie überprüfen, ob vorläufiges Löschen aktiviert ist, und die zugehörige Aufbewahrungsrichtlinie anzeigen:

```azurecli
az storage account file-service-properties show -n yourStorageaccount -g yourResourceGroup
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

## <a name="prerequisite"></a>Voraussetzung

Cmdlets für vorläufiges Löschen sind ab Version 4.8.0 des Az.Storage-Moduls verfügbar. 

## <a name="getting-started-with-powershell"></a>Erste Schritte mit PowerShell

Zum Aktivieren des vorläufigen Löschens müssen die Diensteigenschaften eines Dateiclients aktualisiert werden. Im folgenden Beispiel wird das vorläufige Löschen für alle Dateifreigaben in einem Speicherkonto aktiviert:

```azurepowershell-interactive
$rgName = "yourResourceGroupName"
$accountName = "yourStorageAccountName"

Update-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName -EnableShareDeleteRetentionPolicy $true -ShareRetentionDays 7
```

Mit dem folgenden Befehl können Sie überprüfen, ob vorläufiges Löschen aktiviert ist, und die zugehörige Aufbewahrungsrichtlinie anzeigen:

```azurepowershell-interactive
Get-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName
```
---

## <a name="restore-soft-deleted-file-share"></a>Wiederherstellen einer vorläufig gelöschten Dateifreigabe

# <a name="portal"></a>[Portal](#tab/azure-portal)

So stellen Sie eine vorläufig gelöschte Dateifreigabe wieder her:

1. Navigieren Sie zu Ihrem Speicherkonto, und wählen Sie **Dateifreigaben** aus.
1. Aktivieren Sie auf dem Dateifreigabeblatt die Option **Gelöschte Freigaben anzeigen**, um vorläufig gelöschte Freigaben anzuzeigen.

    Dadurch werden alle Freigaben angezeigt, die sich aktuell im Zustand **Gelöscht** befinden.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/undelete-file-share.png" alt-text="Ist die Statusspalte (die Spalte neben der Namensspalte) auf „Gelöscht“ festgelegt, befindet sich die Dateifreigabe in einem vorläufig gelöschten Zustand. In diesem Fall wird sie nach Ablauf des angegebenen Aufbewahrungszeitraums endgültig gelöscht.":::

1. Wählen Sie die Freigabe und anschließend **Wiederherstellen** aus, um sie wiederherzustellen.

    Wenn die Freigabe den Status **Aktiv** hat, wurde sie wiederhergestellt.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/restored-file-share.png" alt-text="Ist die Statusspalte (die Spalte neben der Namensspalte) auf „Aktiv“ festgelegt, wurde Ihre Dateifreigabe wiederhergestellt.":::

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Cmdlets für vorläufiges Löschen sind in der Version 2.1.3 der Azure CLI verfügbar. Zum Wiederherstellen einer vorläufig gelöschten Dateifreigabe müssen Sie zuerst den Wert für `--deleted-version` der Freigabe abrufen. Dazu verwenden Sie den folgenden Befehl, mit dem alle gelöschten Freigaben für Ihr Speicherkonto aufgelistet werden:

```azurecli
az storage share-rm list --storage-account yourStorageaccount --include-deleted
```

Nachdem Sie die Freigabe ermittelt haben, die Sie wiederherstellen möchten, können Sie sie mit dem folgenden Befehl wiederherstellen:

```azurecli
az storage share-rm restore -n deletedshare --deleted-version 01D64EB9886F00C4 -g yourResourceGroup --storage-account yourStorageaccount
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Cmdlets für vorläufiges Löschen sind ab Version 4.8.0 des Az.Storage-Moduls verfügbar. Zum Wiederherstellen einer vorläufig gelöschten Dateifreigabe müssen Sie zuerst den Wert für `-DeletedShareVersion` der Freigabe abrufen. Dazu verwenden Sie den folgenden Befehl, mit dem alle gelöschten Freigaben für Ihr Speicherkonto aufgelistet werden:

```azurepowershell-interactive
Get-AzRmStorageShare -ResourceGroupName $rgname -StorageAccountName $accountName -IncludeDeleted
```

Nachdem Sie die Freigabe ermittelt haben, die Sie wiederherstellen möchten, können Sie sie mit dem folgenden Befehl wiederherstellen:

```azurepowershell-interactive
Restore-AzRmStorageShare -ResourceGroupName $rgname -StorageAccountName $accountName -DeletedShareVersion 01D5E2783BDCDA97
```
---

## <a name="disable-soft-delete"></a>Deaktivieren des vorläufigen Löschens

Wenn Sie das vorläufige Löschen nicht mehr verwenden oder eine Dateifreigabe endgültig löschen möchten, gehen Sie wie folgt vor:

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Navigieren Sie zu Ihrem Speicherkonto, und wählen Sie unter **Einstellungen** die Option **Vorläufiges Löschen** aus.
1. Wählen Sie unter **Dateifreigaben** für **Vorläufiges Löschen von Dateifreigaben** die Option **Deaktiviert** aus.
1. Wählen Sie **Speichern** aus, um die Einstellungen für die Datenaufbewahrung zu bestätigen.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/disable-soft-delete-files.png" alt-text="Wenn Sie das vorläufige Löschen deaktivieren, können Sie nach Belieben alle Dateifreigaben in Ihrem Speicherkonto sofort und endgültig löschen.":::

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Cmdlets für vorläufiges Löschen sind in der Version 2.1.3 der Azure CLI verfügbar. Das vorläufige Löschen für Ihr Speicherkonto kann mithilfe des folgenden Befehls deaktiviert werden:

```azurecli
az storage account file-service-properties update --enable-delete-retention false -n yourStorageaccount -g yourResourceGroup
```
# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Cmdlets für vorläufiges Löschen sind ab Version 4.8.0 des Az.Storage-Moduls verfügbar. Das vorläufige Löschen für Ihr Speicherkonto kann mithilfe des folgenden Befehls deaktiviert werden:

```azurepowershell-interactive
Update-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName -EnableShareDeleteRetentionPolicy $false
```
---

## <a name="next-steps"></a>Nächste Schritte

Informationen zu einer anderen Form von Datenschutz und -wiederherstellung finden Sie in unserem Artikel [Übersicht über Freigabemomentaufnahmen für Azure Files](storage-snapshots-files.md).
