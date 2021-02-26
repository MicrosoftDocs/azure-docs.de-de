---
title: Durchführen einer Point-in-Time-Wiederherstellung von Blockblobdaten
titleSuffix: Azure Storage
description: Erfahren Sie, wie Sie die Point-in-Time-Wiederherstellung verwenden, um Blockblobs in einem Zustand zu einem früheren Zeitpunkt wiederherzustellen.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/29/2021
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: b62e341d35a4ff7fd5a7ddd6d9f19b138aaf0aa9
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99071646"
---
# <a name="perform-a-point-in-time-restore-on-block-blob-data"></a>Durchführen einer Point-in-Time-Wiederherstellung von Blockblobdaten

Sie können die Point-in-Time-Wiederherstellung verwenden, um Blockblobs in einem vorherigen Zustand wiederherzustellen. In diesem Artikel wird beschrieben, wie Sie die Point-in-Time-Wiederherstellung für ein Speicherkonto aktivieren und einen Wiederherstellungsvorgang ausführen.

Weitere Informationen zur Point-in-Time-Wiederherstellung finden Sie unter [Point-in-Time-Wiederherstellung für Blockblobs](point-in-time-restore-overview.md).

> [!CAUTION]
> Point-in-Time-Wiederherstellung unterstützt nur Wiederherstellungsvorgänge für Blockblobs. Vorgänge für Container können nicht wiederhergestellt werden. Wenn Sie einen Container aus dem Speicherkonto löschen, indem Sie den Vorgang [Container löschen](/rest/api/storageservices/delete-container) aufrufen, kann dieser Container nicht mit einem Wiederherstellungsvorgang wiederhergestellt werden. Löschen Sie die einzelnen Blobs, anstatt einen ganzen Container zu löschen, wenn Sie sie möglicherweise später wiederherstellen möchten. Außerdem empfiehlt Microsoft das Aktivieren des vorläufigen Löschens für Container und Blobs zum Schutz vor versehentlichem Löschen. Weitere Informationen finden Sie unter [Vorläufiges Löschen für Container (Vorschau)](soft-delete-container-overview.md) und [Vorläufiges Löschen für Blobs](soft-delete-blob-overview.md).

## <a name="enable-and-configure-point-in-time-restore"></a>Aktivieren und Konfigurieren von Point-in-Time-Wiederherstellung

Bevor Sie die Point-in-Time-Wiederherstellung aktivieren und konfigurieren, aktivieren Sie die Voraussetzungen für das Speicherkonto: vorläufiges Löschen, Änderungsfeed und Blobversionsverwaltung. Weitere Informationen zum Aktivieren dieser Funktionen finden Sie in den folgenden Artikeln:

- [Aktivieren von „Vorläufiges Löschen“ für Blobs](./soft-delete-blob-enable.md)
- [Aktivieren und Deaktivieren des Änderungsfeeds](storage-blob-change-feed.md#enable-and-disable-the-change-feed)
- [Aktivieren und Verwalten der Blobversionsverwaltung](versioning-enable.md)

> [!IMPORTANT]
> Das Aktivieren des vorläufigen Löschens, des Änderungsfeeds und der Blobversionsverwaltung kann zusätzliche Kosten verursachen. Weitere Informationen finden Sie unter [Vorläufiges Löschen für Blobs](soft-delete-blob-overview.md), [Unterstützung für den Änderungsfeed in Azure Blob Storage](storage-blob-change-feed.md) und [Blobversionsverwaltung](versioning-overview.md).

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Führen Sie die folgenden Schritte aus, um die Point-in-Time-Wiederherstellung über das Azure-Portal zu konfigurieren:

1. Navigieren Sie zum Speicherkonto im Azure-Portal.
1. Wählen Sie unter **Einstellungen** die Option **Datenschutz** aus.
1. Wählen Sie **Turn on point-in-time restore** (Point-in-Time-Wiederherstellung aktivieren) aus. Wenn Sie diese Option auswählen, werden auch vorläufiges Löschen für Blobs, die Versionsverwaltung und der Änderungsfeed aktiviert.
1. Legen Sie den maximalen Wiederherstellungspunkt für die Point-in-Time-Wiederherstellung in Tagen fest. Diese Dauer muss mindestens einen Tag kürzer als die für das vorläufige Löschen von Blobs angegebene Beibehaltungsdauer sein.
1. Speichern Sie die Änderungen.

Die folgende Abbildung zeigt ein Speicherkonto, das für die Point-in-Time-Wiederherstellung mit einem Wiederherstellungspunkt vor sieben Tagen und einer Beibehaltungsdauer von 14 Tagen für das vorläufige Löschen von Blobs konfiguriert wurde.

:::image type="content" source="media/point-in-time-restore-manage/configure-point-in-time-restore-portal.png" alt-text="Screenshot des Konfigurierens der Point-in-Time-Wiederherstellung im Azure-Portal":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Um die Point-in-Time-Wiederherstellung mit PowerShell zu konfigurieren, installieren Sie zunächst mindestens Version 2.6.0 des Moduls [Az.Storage](https://www.powershellgallery.com/packages/Az.Storage). Rufen Sie dann den Befehl [Enable-AzStorageBlobRestorePolicy](/powershell/module/az.storage/enable-azstorageblobrestorepolicy) auf, um die Point-in-Time-Wiederherstellung für das Speicherkonto zu aktivieren.

Im folgenden Beispiel wird vorläufiges Löschen aktiviert, die Beibehaltungsdauer für vorläufiges Löschen festgelegt, der Änderungsfeed und die Versionsverwaltung aktiviert und dann die Point-in-Time-Wiederherstellung aktiviert. Denken Sie daran, die Platzhalterwerte in eckigen Klammern durch Ihre eigenen Werte zu ersetzen, wenn Sie das Beispiel ausführen:

```powershell
# Set resource group and account variables.
$rgName = "<resource-group>"
$accountName = "<storage-account>"

# Enable blob soft delete with a retention of 14 days.
Enable-AzStorageBlobDeleteRetentionPolicy -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -RetentionDays 14

# Enable change feed and versioning.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EnableChangeFeed $true `
    -IsVersioningEnabled $true

# Enable point-in-time restore with a retention period of 7 days.
# The retention period for point-in-time restore must be at least
# one day less than that set for soft delete.
Enable-AzStorageBlobRestorePolicy -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -RestoreDays 7

# View the service settings.
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Um die Point-in-Time-Wiederherstellung mit Azure CLI zu konfigurieren, installieren Sie zunächst mindestens Azure CLI Version 2.2.0. Aktivieren Sie dann den Befehl [az storage account blob-service-properties update](/cli/azure/ext/storage-blob-preview/storage/account/blob-service-properties#ext_storage_blob_preview_az_storage_account_blob_service_properties_update), um die Point-in-Time-Wiederherstellung und die anderen erforderlichen Datenschutzeinstellungen für das Speicherkonto zu aktivieren.

Im folgenden Beispiel wird vorläufiges Löschen aktiviert und die Beibehaltungsdauer für vorläufiges Löschen auf 14 Tage festgelegt, der Änderungsfeed und die Versionsverwaltung aktiviert und dann die Point-in-Time-Wiederherstellung mit einer Wiederherstellungsdauer von 7 Tagen aktiviert. Denken Sie daran, die Platzhalterwerte in eckigen Klammern durch Ihre eigenen Werte zu ersetzen, wenn Sie das Beispiel ausführen:

```azurecli
az storage account blob-service-properties update \
    --resource-group <resource_group> \
    --account-name <storage-account> \
    --enable-delete-retention true \
    --delete-retention-days 14 \
    --enable-versioning true \
    --enable-change-feed true \
    --enable-restore-policy true \
    --restore-days 7
```

---

## <a name="choose-a-restore-point"></a>Auswählen eines Wiederherstellungspunkts

Der Wiederherstellungspunkt ist ein Zeitpunkt mit Angabe von Datum und Uhrzeit. Der zu diesem Zeitpunkt bestehende Datenzustand wird wiederhergestellt. Azure Storage verwendet immer einen UTC-Datums-/Uhrzeitwert als Wiederherstellungspunkt. Allerdings können Sie im Azure-Portal den Wiederherstellungspunkt in der Ortszeit angeben, und dann wird dieser Datums-/Uhrzeitwert in einen UTC-Datums-/Uhrzeitwert konvertiert, um den Wiederherstellungsvorgang auszuführen.

Wenn Sie einen Wiederherstellungsvorgang mit PowerShell oder Azure CLI ausführen, sollten Sie den Wiederherstellungspunkt als UTC-Datums-/Uhrzeitwert angeben. Wenn der Wiederherstellungspunkt mit einem lokalen Uhrzeitwert anstelle eines UTC-Zeitwerts angegeben wird, verhält sich der Wiederherstellungsvorgang in einigen Fällen möglicherweise dennoch wie erwartet. Wenn die Ortszeit z. B. UTC minus fünf Stunden beträgt, führt die Angabe eines lokalen Zeitwerts zu einem Wiederherstellungspunkt, der fünf Stunden vor dem Wert liegt, den Sie angegeben haben. Wenn während dieses Zeitraums von fünf Stunden keine Änderungen an den Daten in dem wiederherzustellenden Bereich vorgenommen wurden, hat der Wiederherstellungsvorgang unabhängig davon, welcher Zeitwert angegeben wurde, dieselben Ergebnisse. Die Angabe einer UTC-Zeit für den Wiederherstellungspunkt wird empfohlen, um unerwartete Ergebnisse zu vermeiden.

## <a name="perform-a-restore-operation"></a>Ausführen eines Wiederherstellungsvorgangs

Sie können alle Container im Speicherkonto wiederherstellen, oder Sie können einen Bereich von Blobs in einem oder mehreren Containern wiederherstellen. Ein Bereich von Blobs wird lexikografisch definiert, also in der Wörterbuchreihenfolge. Pro Wiederherstellungsvorgang werden bis zu zehn lexikografische Bereiche unterstützt. Der Anfang des Bereichs ist inklusiv, das Ende des Bereichs ist exklusiv.

Das für den Startbereich und den Endbereich angegebene Containermuster muss mindestens drei Zeichen umfassen. Der Schrägstrich (/), der zum Trennen eines Containernamens von einem Blobnamen verwendet wird, wird bei diesem Mindestwert nicht berücksichtigt.

Platzhalterzeichen werden in einem lexikografischen Bereich nicht unterstützt. Alle Platzhalterzeichen werden wie Standardzeichen behandelt.

Sie können Blobs in den `$root`- und `$web`-Containern wiederherstellen, indem Sie sie explizit in einem Bereich angeben, der an einen Wiederherstellungsvorgang übergeben wird. Die `$root`- und `$web`-Container werden nur wiederhergestellt, wenn sie explizit angegeben werden. Andere Systemcontainer können nicht wiederhergestellt werden.

Es werden nur Blockblobs wiederhergestellt. Seitenblobs und Anfügeblobs sind in einem Wiederherstellungsvorgang nicht enthalten. Weitere Informationen zu Einschränkungen im Zusammenhang mit Anfügeblobs finden Sie unter [Point-in-Time-Wiederherstellung für Blockblobs](point-in-time-restore-overview.md).

> [!IMPORTANT]
> Wenn Sie einen Wiederherstellungsvorgang ausführen, blockiert Azure Storage für die Dauer dieses Vorgangs Datenvorgänge für die Blobs in den Bereichen, die wiederhergestellt werden. Lese-, Schreib- und Löschvorgänge werden am primären Speicherort blockiert. Aus diesem Grund werden Vorgänge wie das Auflisten von Containern im Azure-Portal während des Wiederherstellungsvorgangs möglicherweise nicht erwartungsgemäß ausgeführt.
>
> Lesevorgänge aus dem sekundären Speicherort können während des Wiederherstellungsvorgangs fortgesetzt werden, wenn das Speicherkonto georepliziert wird.
>
> Die erforderliche Zeit zum Wiederherstellen mehrerer Datensätze ist von der Anzahl der Schreib- und Löschvorgänge abhängig, die während des Wiederherstellungszeitraums vorgenommen werden. Beispielsweise dauert bei einem Konto mit 1 Million Objekten, in dem pro Tag 3.000 Objekte hinzugefügt und 1.000 Objekte gelöscht werden, die Wiederherstellung zu einem 30 Tage in der Vergangenheit liegenden Zeitpunkt ungefähr zwei Stunden. Bei einem Konto mit einer solchen Änderungsrate wird davon abgeraten, einen Aufbewahrungszeitraum mit einer Wiederherstellung, die mehr als 90 Tage in der Vergangenheit liegt, anzuwenden.

### <a name="restore-all-containers-in-the-account"></a>Wiederherstellen aller Container im Konto

Sie können alle Container im Speicherkonto wiederherstellen, um diese auf einen Zustand zu einem früheren Zeitpunkt zurückzusetzen.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Führen Sie die folgenden Schritte aus, um alle Container und Blobs im Speicherkonto über das Azure-Portal wiederherzustellen:

1. Navigieren Sie zu der Liste mit den Containern in Ihrem Speicherkonto.
1. Wählen Sie auf der Symbolleiste **Container wiederherstellen** und dann **Alle wiederherstellen** aus.
1. Geben Sie im Bereich **Alle Container wiederherstellen** den Wiederherstellungspunkt über das Datum und die Uhrzeit an.
1. Bestätigen Sie durch Aktivieren des Kontrollkästchens, dass Sie den Vorgang fortsetzen möchten.
1. Wählen Sie **Wiederherstellen** aus, um den Wiederherstellungsvorgang zu starten.

    :::image type="content" source="media/point-in-time-restore-manage/restore-all-containers-portal.png" alt-text="Screenshot der Wiederherstellung aller Container zu einem bestimmten Wiederherstellungspunkt":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Wenn Sie alle Container und Blobs im Speicherkonto mit PowerShell wiederherstellen möchten, rufen Sie den Befehl **Restore-AzStorageBlobRange** auf und geben Sie den Wiederherstellungspunkt als UTC-Datums-/Uhrzeitwert an. Standardmäßig wird der Befehl **Restore-AzStorageBlobRange** asynchron ausgeführt, und er gibt ein Objekt vom Typ **PSBlobRestoreStatus** zurück, mit dem Sie den Status des Wiederherstellungsvorgangs überprüfen können.

Im folgenden Beispiel werden Container im Speicherkonto auf ihren Zustand 12 Stunden vor dem aktuellen Zeitpunkt asynchron wiederhergestellt. Außerdem werden einige der Eigenschaften des Wiederherstellungsvorgangs überprüft:

```powershell
# Specify -TimeToRestore as a UTC value
$restoreOperation = Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).ToUniversalTime().AddHours(-12)

# Get the status of the restore operation.
$restoreOperation.Status
# Get the ID for the restore operation.
$restoreOperation.RestoreId
# Get the restore point in UTC time.
$restoreOperation.Parameters.TimeToRestore
```

Um den Wiederherstellungsvorgang synchron auszuführen, schließen Sie den Parameter **-WaitForComplete** in den Befehl ein. Wenn der Parameter **-WaitForComplete** vorhanden ist, zeigt PowerShell eine Meldung an, die die Wiederherstellungs-ID für den Vorgang enthält. Anschließend wird die Ausführung blockiert, bis der Wiederherstellungsvorgang beendet ist. Beachten Sie, dass die für einen Wiederherstellungsvorgang erforderliche Zeit von der Menge der wiederherzustellenden Daten abhängt. Ein umfangreicher Wiederherstellungsvorgang kann bis zu einer Stunde dauern.

```powershell
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddHours(-12) -WaitForComplete
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Wenn Sie alle Container und Blobs im Speicherkonto mit der Azure CLI wiederherstellen möchten, rufen Sie den Befehl [az storage blob restore](/cli/azure/storage/blob#az_storage_blob_restore) auf und geben Sie den Wiederherstellungspunkt als UTC-Datums-/Uhrzeitwert an.

Im folgenden Beispiel wird der Zustand aller Container im Speicherkonto 12 Stunden vor einem angegebenen Datums-/Uhrzeitwert asynchron wiederhergestellt. Um den Status des Wiederherstellungsvorgangs zu überprüfen, rufen Sie [az storage account show](/cli/azure/storage/account#az_storage_account_show) auf:

```azurecli
az storage blob restore \
    --resource-group <resource_group> \
    --account-name <storage-account> \
    --time-to-restore 2021-01-14T06:31:22Z \
    --no-wait
```

Um die Eigenschaften eines Wiederherstellungsvorgangs zu überprüfen, rufen Sie [az storage account show](/cli/azure/storage/account#az_storage_account_show) auf, und erweitern Sie die Eigenschaft **blobRestoreStatus**. Im folgenden Beispiel wird gezeigt, wie die Eigenschaft **status** überprüft wird.

```azurecli
az storage account show \
    --name <storage-account> \
    --resource-group <resource_group> \ 
    --expand blobRestoreStatus \
    --query blobRestoreStatus.status \
    --output tsv
```

Um den Befehl **az storage blob restore** synchron auszuführen und die Ausführung zu blockieren, bis der Wiederherstellungsvorgang abgeschlossen ist, lassen Sie den `--no-wait`-Parameter weg.

---

### <a name="restore-ranges-of-block-blobs"></a>Wiederherstellen mehrerer Bereiche von Blockblobs

Sie können lexikografische Bereiche von Blobs innerhalb eines einzelnen Containers oder aus mehreren Containern wiederherstellen, um diese Blobs auf einen Zustand zu einem bestimmten Zeitpunkt zurückzusetzen.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Führen Sie die folgenden Schritte aus, um einen Bereich von Blobs in einem oder mehreren Containern über das Azure-Portal wiederherzustellen:

1. Navigieren Sie zu der Liste mit den Containern in Ihrem Speicherkonto.
1. Wählen Sie den oder die Container für die Wiederherstellung aus.
1. Wählen Sie auf der Symbolleiste **Container wiederherstellen** und dann **Ausgewählte wiederherstellen** aus.
1. Geben Sie im Bereich **Ausgewählte Container wiederherstellen** den Wiederherstellungspunkt über das Datum und die Uhrzeit an.
1. Geben Sie die wiederherzustellenden Bereiche an. Verwenden Sie einen Schrägstrich (/), um den Containernamen vom Blobpräfix zu trennen.
1. Standardmäßig wird unter **Ausgewählte Container wiederherstellen** ein Bereich angegeben, der alle Blobs im Container umfasst. Löschen Sie diesen Bereich, wenn Sie nicht den gesamten Container wiederherstellen möchten. In der folgenden Abbildung wird der Standardbereich angegeben.

    :::image type="content" source="media/point-in-time-restore-manage/delete-default-blob-range.png" alt-text="Screenshot des Standardblobbereichs, der vor der Angabe eines benutzerdefinierten Bereichs gelöscht werden muss":::

1. Bestätigen Sie durch Aktivieren des Kontrollkästchens, dass Sie den Vorgang fortsetzen möchten.
1. Wählen Sie **Wiederherstellen** aus, um den Wiederherstellungsvorgang zu starten.

Die folgende Abbildung zeigt einen Wiederherstellungsvorgang für mehrere Bereiche.

:::image type="content" source="media/point-in-time-restore-manage/restore-multiple-container-ranges-portal.png" alt-text="Screenshot der Wiederherstellung von Blobbereichen in einem oder mehreren Containern":::

In dem in der Abbildung gezeigten Wiederherstellungsvorgang werden die folgenden Aktionen ausgeführt:

- Wiederherstellen des gesamten Inhalts von *container1*
- Wiederherstellen der Blobs im lexikografischen Bereich *blob1* bis *blob5* in *container2*. Mit diesem Bereich werden Blobs mit Namen wie *blob1*, *blob11*, *blob100*, *blob2* usw. wiederhergestellt. Da das Ende des Bereichs exklusiv ist, werden Blobs, deren Name mit *blob4* beginnt, wiederhergestellt, jedoch keine Blobs, deren Name mit *blob5* beginnt.
- Wiederherstellen aller Blobs in *container3* und *container4*. Da das Ende des Bereichs exklusiv ist, wird mit diesem Bereich nicht *container5* wiederhergestellt.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Um einen einzelnen Bereich von Blobs wiederherzustellen, rufen Sie den Befehl **Restore-AzStorageBlobRange** auf, und geben Sie für den Parameter `-BlobRestoreRange` einen lexikografischen Bereich von Container- und Blobnamen an. Wenn Sie z. B. die Blobs in einem einzelnen Container mit dem Namen *container1* wiederherstellen möchten, können Sie einen Bereich angeben, der mit *container1* beginnt und mit *container2* endet. Es ist nicht erforderlich, dass die in den Anfangs- und Endbereichen genannten Container vorhanden sind. Da das Ende des Bereichs exklusiv ist, wird nur der Container mit dem Namen *container1* wiederhergestellt, auch wenn das Speicherkonto einen Container mit dem Namen *container2* enthält:

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange container1 `
    -EndRange container2
```

Um eine Teilmenge der Blobs in einem Container für die Wiederherstellung anzugeben, verwenden Sie einen Schrägstrich (/), um den Containernamen vom Blobpräfixmuster zu trennen. Beispielsweise wählt der folgende Bereich Blobs in einem einzelnen Container aus, deren Namen mit den Buchstaben *d* bis *f* beginnen:

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange container1/d `
    -EndRange container1/g
```

Geben Sie als Nächstes den Bereich für den Befehl **Restore-AzStorageBlobRange** an. Geben Sie den Wiederherstellungspunkt an, indem Sie einen **DateTime**-Wert (in UTC) für den `-TimeToRestore`-Parameter angeben. Im folgenden Beispiel werden Blobs im angegebenen Bereich in dem Zustand wiederhergestellt, den sie 3 Tage vor dem aktuellen Zeitpunkt aufgewiesen haben:

```powershell
# Specify -TimeToRestore as a UTC value
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -BlobRestoreRange $range `
    -TimeToRestore (Get-Date).AddDays(-3)
```

Standardmäßig wird der Befehl **Restore-AzStorageBlobRange** asynchron ausgeführt. Wenn Sie einen Wiederherstellungsvorgang asynchron initiieren, zeigt PowerShell sofort eine Tabelle mit Eigenschaften für den Vorgang an:  

```powershell
Status     RestoreId                            FailureReason Parameters.TimeToRestore     Parameters.BlobRanges
------     ---------                            ------------- ------------------------     ---------------------
InProgress 459c2305-d14a-4394-b02c-48300b368c63               2020-09-15T23:23:07.1490859Z ["container1/d" -> "container1/g"]
```

Wenn Sie mehrere Bereiche von Blockblobs wiederherstellen möchten, geben Sie ein Array von Bereichen für den `-BlobRestoreRange`-Parameter an. Im folgenden Beispiel wird durch die Angabe zweier Bereiche der gesamte Inhalt von *container1* und *container4* auf den Zustand vor 24 Stunden wiederhergestellt. Außerdem wird das Ergebnis in einer Variable gespeichert:

```powershell
# Specify a range that includes the complete contents of container1.
$range1 = New-AzStorageBlobRangeToRestore -StartRange container1 `
    -EndRange container2
# Specify a range that includes the complete contents of container4.
$range2 = New-AzStorageBlobRangeToRestore -StartRange container4 `
    -EndRange container5

$restoreOperation = Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddHours(-24) `
    -BlobRestoreRange @($range1, $range2)

# Get the status of the restore operation.
$restoreOperation.Status
# Get the ID for the restore operation.
$restoreOperation.RestoreId
# Get the blob ranges specified for the operation.
$restoreOperation.Parameters.BlobRanges
```

Um den Wiederherstellungsvorgang synchron auszuführen und die Ausführung bis zum Abschluss zu blockieren, schließen Sie den Parameter **-WaitForComplete** in den Befehl ein.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Um einen Bereich von Blobs wiederherzustellen, rufen Sie den Befehl [az storage blob restore](/cli/azure/storage/blob#az_storage_blob_restore) auf, und geben Sie einen lexikografischen Bereich von Container- und Blobnamen für den Parameter `--blob-range` an. Wenn Sie mehrere Bereiche angeben möchten, geben Sie den `--blob-range`-Parameter für jeden einzelnen Bereich an.

Wenn Sie z. B. die Blobs in einem einzelnen Container mit dem Namen *container1* wiederherstellen möchten, können Sie einen Bereich angeben, der mit *container1* beginnt und mit *container2* endet. Es ist nicht erforderlich, dass die in den Anfangs- und Endbereichen genannten Container vorhanden sind. Da das Ende des Bereichs exklusiv ist, wird nur der Container mit dem Namen *container1* wiederhergestellt, auch wenn das Speicherkonto einen Container mit dem Namen *container2* enthält.

Um eine Teilmenge der Blobs in einem Container für die Wiederherstellung anzugeben, verwenden Sie einen Schrägstrich (/), um den Containernamen vom Blobpräfixmuster zu trennen. Im unten gezeigten Beispiel wird ein Bereich von Blobs in einem Container, deren Namen mit den Buchstaben `d` bis `f` beginnen, asynchron wiederhergestellt.

```azurecli
az storage blob restore \
    --account-name <storage-account> \
    --time-to-restore 2021-01-14T06:31:22Z \
    --blob-range container1 container2
    --blob-range container3/d container3/g
    --no-wait
```

Um den Befehl **az storage blob restore** synchron auszuführen und die Ausführung zu blockieren, bis der Wiederherstellungsvorgang abgeschlossen ist, lassen Sie den `--no-wait`-Parameter weg.

---

## <a name="next-steps"></a>Nächste Schritte

- [Point-in-Time-Wiederherstellung für Blockblobs](point-in-time-restore-overview.md)
- [Vorläufiges Löschen](./soft-delete-blob-overview.md)
- [Änderungsfeed](storage-blob-change-feed.md)
- [Blobversionsverwaltung](versioning-overview.md)