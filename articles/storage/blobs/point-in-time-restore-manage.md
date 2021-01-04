---
title: Durchführen einer Point-in-Time-Wiederherstellung von Blockblobdaten
titleSuffix: Azure Storage
description: Erfahren Sie, wie Sie die Point-in-Time-Wiederherstellung verwenden, um Blockblobs in einem Zustand zu einem früheren Zeitpunkt wiederherzustellen.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 09/23/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 2350177373bc99907c437d814d8f01193f18f3fd
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95895722"
---
# <a name="perform-a-point-in-time-restore-on-block-blob-data"></a>Durchführen einer Point-in-Time-Wiederherstellung von Blockblobdaten

Sie können die Point-in-Time-Wiederherstellung verwenden, um Blockblobs in einem vorherigen Zustand wiederherzustellen. In diesem Artikel wird beschrieben, wie Sie die Point-in-Time-Wiederherstellung für ein Speicherkonto aktivieren und einen Wiederherstellungsvorgang ausführen.

Weitere Informationen zur Point-in-Time-Wiederherstellung finden Sie unter [Point-in-Time-Wiederherstellung für Blockblobs](point-in-time-restore-overview.md).

> [!CAUTION]
> Point-in-Time-Wiederherstellung unterstützt nur Wiederherstellungsvorgänge für Blockblobs. Vorgänge für Container können nicht wiederhergestellt werden. Wenn Sie einen Container aus dem Speicherkonto löschen, indem Sie den Vorgang [Container löschen](/rest/api/storageservices/delete-container) aufrufen, kann dieser Container nicht mit einem Wiederherstellungsvorgang wiederhergestellt werden. Löschen Sie die einzelnen Blobs, anstatt einen Container zu löschen, wenn Sie sie möglicherweise wiederherstellen möchten.

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

Um die Point-in-Time-Wiederherstellung mit PowerShell zu konfigurieren, installieren Sie zunächst mindestens Version 2.6.0 des Moduls [Az.Storage](https://www.powershellgallery.com/packages/Az.Storage). Rufen Sie dann den Befehl Enable-AzStorageBlobRestorePolicy auf, um die Point-in-Time-Wiederherstellung für das Speicherkonto zu aktivieren.

Im folgenden Beispiel wird vorläufiges Löschen aktiviert, die Beibehaltungsdauer für vorläufiges Löschen festgelegt, der Änderungsfeed und die Versionsverwaltung aktiviert und dann die Point-in-Time-Wiederherstellung aktiviert.    Denken Sie daran, die Platzhalterwerte in eckigen Klammern durch Ihre eigenen Werte zu ersetzen, wenn Sie das Beispiel ausführen:

```powershell
# Sign in to your Azure account.
Connect-AzAccount

# Set resource group and account variables.
$rgName = "<resource-group>"
$accountName = "<storage-account>"

# Enable soft delete with a retention of 14 days.
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

---

## <a name="perform-a-restore-operation"></a>Ausführen eines Wiederherstellungsvorgangs

Wenn Sie einen Wiederherstellungsvorgang ausführen, müssen Sie den Wiederherstellungspunkt als **DateTime**-Wert in UTC angeben. Container und Blobs werden in ihrem Zustand zu diesem Datum und dieser Uhrzeit wiederhergestellt. Der Wiederherstellungsvorgang kann mehrere Minuten dauern.

Sie können alle Container im Speicherkonto wiederherstellen, oder Sie können einen Bereich von Blobs in einem oder mehreren Containern wiederherstellen. Ein Bereich von Blobs wird lexikografisch definiert, also in der Wörterbuchreihenfolge. Pro Wiederherstellungsvorgang werden bis zu zehn lexikografische Bereiche unterstützt. Der Anfang des Bereichs ist inklusiv, das Ende des Bereichs ist exklusiv.

Das für den Startbereich und den Endbereich angegebene Containermuster muss mindestens drei Zeichen umfassen. Der Schrägstrich (/), der zum Trennen eines Containernamens von einem Blobnamen verwendet wird, wird bei diesem Mindestwert nicht berücksichtigt.

Platzhalterzeichen werden in einem lexikografischen Bereich nicht unterstützt. Alle Platzhalterzeichen werden wie Standardzeichen behandelt.

Sie können Blobs in den `$root`- und `$web`-Containern wiederherstellen, indem Sie sie explizit in einem Bereich angeben, der an einen Wiederherstellungsvorgang übergeben wird. Die `$root`- und `$web`-Container werden nur wiederhergestellt, wenn sie explizit angegeben werden. Andere Systemcontainer können nicht wiederhergestellt werden.

Es werden nur Blockblobs wiederhergestellt. Seitenblobs und Anfügeblobs sind in einem Wiederherstellungsvorgang nicht enthalten. Weitere Informationen zu Einschränkungen im Zusammenhang mit Anfügeblobs finden Sie unter [Point-in-Time-Wiederherstellung für Blockblobs](point-in-time-restore-overview.md).

> [!IMPORTANT]
> Wenn Sie einen Wiederherstellungsvorgang ausführen, blockiert Azure Storage für die Dauer dieses Vorgangs Datenvorgänge für die Blobs in den Bereichen, die wiederhergestellt werden. Lese-, Schreib- und Löschvorgänge werden am primären Speicherort blockiert. Aus diesem Grund werden Vorgänge wie das Auflisten von Containern im Azure-Portal während des Wiederherstellungsvorgangs möglicherweise nicht erwartungsgemäß ausgeführt.
>
> Lesevorgänge aus dem sekundären Speicherort können während des Wiederherstellungsvorgangs fortgesetzt werden, wenn das Speicherkonto georepliziert wird.

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

Wenn Sie alle Container und Blobs im Speicherkonto mit PowerShell wiederherstellen möchten, rufen Sie den Befehl **Restore-AzStorageBlobRange** auf. Standardmäßig wird der Befehl **Restore-AzStorageBlobRange** asynchron ausgeführt, und er gibt ein Objekt vom Typ **PSBlobRestoreStatus** zurück, mit dem Sie den Status des Wiederherstellungsvorgangs überprüfen können.

Im folgenden Beispiel werden Container im Speicherkonto auf ihren Zustand 12 Stunden vor dem aktuellen Zeitpunkt asynchron wiederhergestellt. Außerdem werden einige der Eigenschaften des Wiederherstellungsvorgangs überprüft:

```powershell
# Specify -TimeToRestore as a UTC value
$restoreOperation = Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddHours(-12)

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

---

## <a name="next-steps"></a>Nächste Schritte

- [Point-in-Time-Wiederherstellung für Blockblobs](point-in-time-restore-overview.md)
- [Vorläufiges Löschen](./soft-delete-blob-overview.md)
- [Änderungsfeed](storage-blob-change-feed.md)
- [Blobversionsverwaltung](versioning-overview.md)