---
title: Wiederherstellen von Azure-Dateifreigaben mit der Azure-Befehlszeilenschnittstelle
description: Hier erfahren Sie, wie Sie gesicherte Azure-Dateifreigaben mithilfe der Azure-Befehlszeilenschnittstelle (Azure CLI) im Recovery Services-Tresor wiederherstellen.
ms.topic: conceptual
ms.date: 01/16/2020
ms.openlocfilehash: a025de7bfb9db037b2008d69be7782feabb482f3
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94562320"
---
# <a name="restore-azure-file-shares-with-the-azure-cli"></a>Wiederherstellen von Azure-Dateifreigaben mit der Azure-Befehlszeilenschnittstelle

Azure CLI ist die Befehlszeilenumgebung zum Verwalten von Azure-Ressourcen. Azure CLI ist ein großartiges Tool zum Erstellen einer benutzerdefinierten Automatisierung zur Verwendung von Azure-Ressourcen. In diesem Artikel wird erläutert, wie Sie eine vollständige Dateifreigabe oder bestimmte Dateien von einem von [Azure Backup](./backup-overview.md) erstellten Wiederherstellungspunkt mithilfe von Azure CLI wiederherstellten können. Sie können diese Schritte auch mit [Azure PowerShell](./backup-azure-afs-automation.md) oder im [Azure-Portal](backup-afs.md) ausführen.

Am Ende dieses Artikels erfahren Sie, wie Sie die folgenden Vorgänge mit Azure CLI ausführen:

* Anzeigen von Wiederherstellungspunkten für eine gesicherte Azure-Dateifreigabe.
* Wiederherstellen einer vollständigen Azure-Dateifreigabe.
* Wiederherstellen einzelner Dateien oder Ordner.

>[!NOTE]
> Azure Backup unterstützt jetzt das Wiederherstellen mehrerer Dateien oder Ordner am ursprünglichen oder an einem alternativen Speicherort mit Azure CLI. Weitere Informationen finden Sie im Abschnitt [Wiederherstellen mehrerer Dateien oder Ordner am ursprünglichen oder an einem alternativen Speicherort](#restore-multiple-files-or-folders-to-original-or-alternate-location) dieses Dokuments.

## <a name="prerequisites"></a>Voraussetzungen

In diesem Artikel wird vorausgesetzt, dass Sie bereits über eine Azure-Dateifreigabe verfügen, die durch Azure Backup gesichert wird. Wenn Sie noch nicht über eine Dateifreigabe verfügen, erfahren Sie unter [Sichern von Azure-Dateifreigaben mit CLI](backup-afs-cli.md), wie Sie die Sicherung für Ihre Dateifreigabe konfigurieren. In diesem Artikel verwenden Sie die folgenden Ressourcen:

| Dateifreigabe | Speicherkonto | Region | Details |
|---|---|---|---|
| *azurefiles* | *afsaccount* | EastUS | Mit Azure Backup gesicherte ursprüngliche Quellressource |
| *azurefiles1* | *afaccount1* | EastUS | Für die Wiederherstellung an einem alternativen Speicherort verwendete Zielressource |

Sie können eine ähnliche Struktur für Ihre Dateifreigaben verwenden, um die verschiedenen Arten von Wiederherstellungen auszuprobieren, die in diesem Artikel beschrieben werden.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

 - Für dieses Tutorial ist mindestens Version 2.0.18 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

## <a name="fetch-recovery-points-for-the-azure-file-share"></a>Abrufen von Wiederherstellungspunkten für die Azure-Dateifreigabe

Verwenden Sie das Cmdlet [az backup recoverypoint list](/cli/azure/backup/recoverypoint#az-backup-recoverypoint-list), um alle Wiederherstellungspunkte für die gesicherte Dateifreigabe aufzulisten.

Das folgende Beispiel ruft die Liste der Wiederherstellungspunkte für die Dateifreigabe *azurefiles* im Speicherkonto *afsaccount* ab.

```azurecli-interactive
az backup recoverypoint list --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --backup-management-type azurestorage --item-name "AzureFileShare;azurefiles" --workload-type azurefileshare --out table
```

Sie können das vorstehende Cmdlet auch mit den Anzeigenamen für den Container und das Element ausführen, indem Sie die folgenden beiden zusätzlichen Parameter angeben:

* **--backup-management-type**: *azurestorage*
* **--workload-type**: *azurefileshare*

```azurecli-interactive
az backup recoverypoint list --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --backup-management-type azurestorage --item-name azurefiles --workload-type azurefileshare --out table
```

Das Resultset ist eine Liste von Wiederherstellungspunkten mit Zeit- und Konsistenzdetails für jeden Wiederherstellungspunkt.

```output
Name                Time                        Consistency
------------------  -------------------------   --------------------
932887541532871865  2020-01-05T07:08:23+00:00   FileSystemConsistent
932885927361238054  2020-01-05T07:08:10+00:00   FileSystemConsistent
932879614553967772  2020-01-04T21:33:04+00:00   FileSystemConsistent
```

Das Attribut **Name** in der Ausgabe entspricht dem Namen des Wiederherstellungspunkts, der als Wert für den Parameter **--rp-name** bei Wiederherstellungsvorgängen verwendet werden kann.

## <a name="full-share-recovery-by-using-the-azure-cli"></a>Wiederherstellung einer vollständigen Freigabe mit der Azure-Befehlszeilenschnittstelle

Sie können diese Wiederherstellungsoption verwenden, um die vollständige Dateifreigabe am ursprünglichen oder einem alternativen Speicherort wiederherzustellen.

Definieren Sie die folgenden Parameter, um Wiederherstellungsvorgänge durchzuführen:

* **--container-name**: Der Name des Speicherkontos, das die gesicherte ursprüngliche Dateifreigabe hostet. Um den Namen oder den Anzeigenamen Ihres Containers abzurufen, verwenden Sie den Befehl [az backup container list](/cli/azure/backup/container#az-backup-container-list).
* **--item-name**: Der Name der gesicherten ursprünglichen Dateifreigabe, die Sie für den Wiederherstellungsvorgang verwenden wollen. Um den Namen oder Anzeigenamen Ihres gesicherten Elements abzurufen, verwenden Sie den Befehl [az backup item list](/cli/azure/backup/item#az-backup-item-list).

### <a name="restore-a-full-share-to-the-original-location"></a>Wiederherstellen einer vollständigen Freigabe am ursprünglichen Speicherort

Wenn Sie an einem ursprünglichen Speicherort eine Wiederherstellung vornehmen, müssen Sie nicht alle zielbezogenen Parameter angeben. Nur **Resolve Conflict** muss angegeben werden.

Im folgenden Beispiel wird das Cmdlet [az backup restore restore-azurefileshare](/cli/azure/backup/restore#az-backup-restore-restore-azurefileshare) mit dem Wiederherstellungsmodus *originallocation* verwendet, um die Dateifreigabe *azurefiles* am ursprünglichen Speicherort wiederherzustellen. Dabei wird der Wiederherstellungspunkt 932883129628959823 verwendet, den Sie unter [Abrufen von Wiederherstellungspunkten für die Azure-Dateifreigabe](#fetch-recovery-points-for-the-azure-file-share) abgerufen haben:

```azurecli-interactive
az backup restore restore-azurefileshare --vault-name azurefilesvault --resource-group azurefiles --rp-name 932887541532871865   --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode originallocation --resolve-conflict overwrite --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
6a27cc23-9283-4310-9c27-dcfb81b7b4bb  azurefiles
```

Das Attribut **Name** in der Ausgabe entspricht dem Namen des Auftrags, der vom Sicherungsdienst für Ihren Wiederherstellungsvorgang erstellt wird. Verwenden Sie zum Nachverfolgen des Auftragsstatus das Cmdlet [az backup job show](/cli/azure/backup/job#az-backup-job-show).

### <a name="restore-a-full-share-to-an-alternate-location"></a>Wiederherstellen einer vollständigen Freigabe an einem alternativen Speicherort

Mit dieser Option können Sie eine Dateifreigabe an einem alternativen Speicherort wiederherstellen und die ursprüngliche Dateifreigabe unverändert beibehalten. Geben Sie die folgenden Parameter für die Wiederherstellung an einem alternativen Standort an:

* **--target-storage-account**: Das Speicherkonto, in dem der gesicherte Inhalt wiederhergestellt wird. Das Zielspeicherkonto muss sich am gleichen Speicherort wie der Tresor befinden.
* **--target-file-share**: Die Dateifreigabe in dem Zielspeicherkonto, in dem der gesicherte Inhalt wiederhergestellt wird.
* **--target-folder**: Der Ordner unter der Dateifreigabe, in dem die Daten wiederhergestellt werden. Wenn der gesicherte Inhalt in einem Stammordner wiederhergestellt werden soll, geben Sie die Werte für den Zielordner als eine leere Zeichenfolge ein.
* **--resolve-conflict**: Die Anweisung bei einem Konflikt mit den wiederhergestellten Daten. Für diesen Parameter kann **Overwrite** oder **Skip** angegeben werden.

Im folgenden Beispiel wird das Cmdlet [az backup restore restore-azurefileshare](/cli/azure/backup/restore#az-backup-restore-restore-azurefileshare) mit dem Wiederherstellungsmodus *alternatelocation* verwendet, um die Dateifreigabe *azurefiles* im Speicherkonto *afsaccount* auf die Dateifreigabe *azurefiles1* im Speicherkonto *afaccount1* wiederherzustellen.

```azurecli-interactive
az backup restore restore-azurefileshare --vault-name azurefilesvault --resource-group azurefiles --rp-name 932883129628959823 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode alternatelocation --target-storage-account afaccount1 --target-file-share azurefiles1 --target-folder restoredata --resolve-conflict overwrite --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
babeb61c-d73d-4b91-9830-b8bfa83c349a  azurefiles
```

Das Attribut **Name** in der Ausgabe entspricht dem Namen des Auftrags, der vom Sicherungsdienst für Ihren Wiederherstellungsvorgang erstellt wird. Verwenden Sie zum Nachverfolgen des Auftragsstatus das Cmdlet [az backup job show](/cli/azure/backup/job#az-backup-job-show).

## <a name="item-level-recovery"></a>Wiederherstellung auf Elementebene

Sie können diese Wiederherstellungsoption verwenden, um einzelne Dateien oder Ordner am ursprünglichen oder einem alternativen Speicherort wiederherzustellen.

Definieren Sie die folgenden Parameter, um Wiederherstellungsvorgänge durchzuführen:

* **--container-name**: Der Name des Speicherkontos, das die gesicherte ursprüngliche Dateifreigabe hostet. Um den Namen oder den Anzeigenamen Ihres Containers abzurufen, verwenden Sie den Befehl [az backup container list](/cli/azure/backup/container#az-backup-container-list).
* **--item-name**: Der Name der gesicherten ursprünglichen Dateifreigabe, die Sie für den Wiederherstellungsvorgang verwenden wollen. Um den Namen oder Anzeigenamen Ihres gesicherten Elements abzurufen, verwenden Sie den Befehl [az backup item list](/cli/azure/backup/item#az-backup-item-list).

Geben Sie die folgenden Parameter für die Elemente an, die Sie wiederherstellen möchten:

* **SourceFilePath**: Der absolute Pfad der (in der Dateifreigabe wiederherzustellenden) Datei als Zeichenfolge. Dieser Pfad ist der gleiche Pfad, der in den CLI-Befehlen [az storage file download](/cli/azure/storage/file#az-storage-file-download) oder [az storage file show](/cli/azure/storage/file#az-storage-file-show) verwendet wird.
* **SourceFileType**: Wählen Sie ein Verzeichnis oder eine Datei aus. Für diesen Parameter kann **Directory** oder **File** angegeben werden.
* **ResolveConflict**: Die Anweisung bei einem Konflikt mit den wiederhergestellten Daten. Für diesen Parameter kann **Overwrite** oder **Skip** angegeben werden.

### <a name="restore-individual-files-or-folders-to-the-original-location"></a>Wiederherstellen einzelner Dateien oder Ordner am ursprünglichen Speicherort

Verwenden Sie das Cmdlet [az backup restore restore-azurefiles](/cli/azure/backup/restore#az-backup-restore-restore-azurefiles), mit dem Wiederherstellungsmodus *originallocation*, um bestimmte Dateien oder Ordner an ihrem ursprünglichen Speicherort wiederherzustellen.

Im folgenden Beispiel wird die Datei *RestoreTest.txt* an ihrem ursprünglichen Speicherort wiederhergestellt: in der Dateifreigabe *azurefiles*.

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932881556234035474 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode originallocation  --source-file-type file --source-file-path "Restore/RestoreTest.txt" --resolve-conflict overwrite  --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
df4d9024-0dcb-4edc-bf8c-0a3d18a25319  azurefiles
```

Das Attribut **Name** in der Ausgabe entspricht dem Namen des Auftrags, der vom Sicherungsdienst für Ihren Wiederherstellungsvorgang erstellt wird. Verwenden Sie zum Nachverfolgen des Auftragsstatus das Cmdlet [az backup job show](/cli/azure/backup/job#az-backup-job-show).

### <a name="restore-individual-files-or-folders-to-an-alternate-location"></a>Wiederherstellen einzelner Dateien oder Ordner an einem alternativen Speicherort

Um bestimmte Dateien oder Ordner an einem alternativen Speicherort wiederherzustellen, verwenden Sie das Cmdlet [az backup restore restore-azurefiles](/cli/azure/backup/restore#az-backup-restore-restore-azurefiles) mit dem Wiederherstellungsmodus *alternatelocation*, und geben Sie die folgenden zielbezogenen Parameter an:

* **--target-storage-account**: Das Speicherkonto, in dem der gesicherte Inhalt wiederhergestellt wird. Das Zielspeicherkonto muss sich am gleichen Speicherort wie der Tresor befinden.
* **--target-file-share**: Die Dateifreigabe in dem Zielspeicherkonto, in dem der gesicherte Inhalt wiederhergestellt wird.
* **--target-folder**: Der Ordner unter der Dateifreigabe, in dem die Daten wiederhergestellt werden. Wenn der gesicherte Inhalt in einem Stammordner wiederhergestellt werden soll, geben Sie den Wert für den Zielordner als eine leere Zeichenfolge ein.

Im folgenden Beispiel wird die Datei *RestoreTest.txt*, die ursprünglich in der Dateifreigabe *azurefiles* vorhanden war, an einem alternativen Speicherort wiederhergestellt: im Ordner *restoredata* in der im Speicherkonto *afaccount1* gehosteten Dateifreigabe *azurefiles1*.

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932881556234035474 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode alternatelocation --target-storage-account afaccount1 --target-file-share azurefiles1 --target-folder restoredata --resolve-conflict overwrite --source-file-type file --source-file-path "Restore/RestoreTest.txt" --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
df4d9024-0dcb-4edc-bf8c-0a3d18a25319  azurefiles
```

Das Attribut **Name** in der Ausgabe entspricht dem Namen des Auftrags, der vom Sicherungsdienst für Ihren Wiederherstellungsvorgang erstellt wird. Verwenden Sie zum Nachverfolgen des Auftragsstatus das Cmdlet [az backup job show](/cli/azure/backup/job#az-backup-job-show).

## <a name="restore-multiple-files-or-folders-to-original-or-alternate-location"></a>Wiederherstellen mehrerer Dateien oder Ordner am ursprünglichen oder an einem alternativen Speicherort

Wenn Sie die Wiederherstellung für mehrere Elemente durchführen möchten, übergeben Sie den Wert für den Parameter **source-file-path** als **durch Leerzeichen getrennte** Pfade aller Dateien oder Ordner, die wiederhergestellt werden sollen.

Im folgenden Beispiel werde die Dateien *Restore.txt* und *AFS testing Report.docx* an ihrem ursprünglichen Speicherort wiederhergestellt.

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932889937058317910 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode originallocation  --source-file-type file --source-file-path "Restore Test.txt" "AFS Testing Report.docx" --resolve-conflict overwrite  --out table
```

Die Ausgabe sieht etwa wie folgt aus:

```output
Name                                          ResourceGroup
------------------------------------          ---------------
649b0c14-4a94-4945-995a-19e2aace0305          azurefiles
```

Das Attribut **Name** in der Ausgabe entspricht dem Namen des Auftrags, der vom Sicherungsdienst für Ihren Wiederherstellungsvorgang erstellt wird. Verwenden Sie zum Nachverfolgen des Auftragsstatus das Cmdlet [az backup job show](/cli/azure/backup/job#az-backup-job-show).

Wenn Sie mehrere Elemente an einem alternativen Speicherort wiederherstellen möchten, verwenden Sie den obigen Befehl und geben Sie zielbezogene Parameter entsprechend der Beschreibung im Abschnitt [Wiederherstellen einzelner Dateien oder Ordner an einem alternativen Speicherort](#restore-individual-files-or-folders-to-an-alternate-location) an.

## <a name="next-steps"></a>Nächste Schritte

[Verwalten der Sicherungen von Azure-Dateifreigaben mit Azure CLI](manage-afs-backup-cli.md).
