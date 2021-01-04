---
title: Verwalten der Sicherungen von Azure-Dateifreigaben mit der Azure-Befehlszeilenschnittstelle
description: Erfahren Sie, wie Sie über die Azure-Befehlszeilenschnittstelle Azure-Dateifreigaben, die durch Azure Backup gesichert wurden, verwalten und überwachen.
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: 5a8a785016845b836a102663a959e4b2f28696b6
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94566451"
---
# <a name="manage-azure-file-share-backups-with-the-azure-cli"></a>Verwalten der Sicherungen von Azure-Dateifreigaben mit der Azure-Befehlszeilenschnittstelle

Azure CLI ist die Befehlszeilenumgebung zum Verwalten von Azure-Ressourcen. Azure CLI ist ein großartiges Tool zum Erstellen einer benutzerdefinierten Automatisierung zur Verwendung von Azure-Ressourcen. In diesem Artikel wird erläutert, wie Sie die Aufgaben zur Verwaltung und Überwachung der durch [Azure Backup](./backup-overview.md) gesicherten Azure-Dateifreigaben durchführen. Sie können diese Schritte auch über das [Azure-Portal](https://portal.azure.com/) ausführen.

## <a name="prerequisites"></a>Voraussetzungen

In diesem Artikel wird vorausgesetzt, dass Sie bereits über eine durch [Azure Backup](./backup-overview.md) gesicherte Azure-Dateifreigabe verfügen. Wenn Sie noch nicht über eine Dateifreigabe verfügen, erfahren Sie unter [Sichern von Azure-Dateifreigaben mit CLI](backup-afs-cli.md), wie Sie die Sicherung für Ihre Dateifreigaben konfigurieren. In diesem Artikel verwenden Sie die folgenden Ressourcen:
   -  **Ressourcengruppe:** *azurefiles*
   -  **RecoveryServicesVault:** *azurefilesvault*
   -  **Speicherkonto:** *afsaccount*
   -  **Dateifreigabe:** *azurefiles*
  
  [!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]
   - Für dieses Tutorial ist mindestens Version 2.0.18 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

## <a name="monitor-jobs"></a>Überwachen von Aufträgen

Wenn Sie einen Sicherungs- oder Wiederherstellungsvorgang auslösen, erstellt der Sicherungsdienst einen Auftrag zum Nachverfolgen. Verwenden Sie das Cmdlet [az backup job list](/cli/azure/backup/job#az-backup-job-list), um abgeschlossene oder derzeit ausgeführte Aufträge zu überwachen. Mit der Befehlszeilenschnittstelle haben Sie auch die Möglichkeit, [einen ausgeführten Auftrag anzuhalten](/cli/azure/backup/job#az-backup-job-stop) oder [den Abschluss eines Auftrags abzuwarten](/cli/azure/backup/job#az-backup-job-wait).

Im folgenden Beispiel wird der Status von Sicherungsaufträgen für den Recovery Services-Tresor *azurefilesvault* angezeigt:

```azurecli-interactive
az backup job list --resource-group azurefiles --vault-name azurefilesvault
```

```output
[
  {
    "eTag": null,
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/d477dfb6-b292-4f24-bb43-6b14e9d06ab5",
    "location": null,
    "name": "d477dfb6-b292-4f24-bb43-6b14e9d06ab5",
    "properties": {
      "actionsInfo": null,
      "activityId": "3cef43ed-0af4-43e2-b9cb-1322c496ccb4",
      "backupManagementType": "AzureStorage",
      "duration": "0:00:29.718011",
      "endTime": "2020-01-13T08:05:29.180606+00:00",
      "entityFriendlyName": "azurefiles",
      "errorDetails": null,
      "extendedInfo": null,
      "jobType": "AzureStorageJob",
      "operation": "Backup",
      "startTime": "2020-01-13T08:04:59.462595+00:00",
      "status": "Completed",
      "storageAccountName": "afsaccount",
      "storageAccountVersion": "MicrosoftStorage"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupJobs"
  },
  {
    "eTag": null,
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/1b9399bf-c23c-4caa-933a-5fc2bf884519",
    "location": null,
    "name": "1b9399bf-c23c-4caa-933a-5fc2bf884519",
    "properties": {
      "actionsInfo": null,
      "activityId": "2663449c-94f1-4735-aaf9-5bb991e7e00c",
      "backupManagementType": "AzureStorage",
      "duration": "0:00:28.145216",
      "endTime": "2020-01-13T08:05:27.519826+00:00",
      "entityFriendlyName": "azurefilesresource",
      "errorDetails": null,
      "extendedInfo": null,
      "jobType": "AzureStorageJob",
      "operation": "Backup",
      "startTime": "2020-01-13T08:04:59.374610+00:00",
      "status": "Completed",
      "storageAccountName": "afsaccount",
      "storageAccountVersion": "MicrosoftStorage"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupJobs"
  }
]
```

## <a name="modify-policy"></a>Ändern der Richtlinie

Sie können eine Sicherungsrichtlinie mit [az backup item set-policy](/cli/azure/backup/item#az-backup-item-set-policy) bearbeiten, um die Sicherungshäufigkeit oder die Aufbewahrungsdauer zu ändern.

Um die Richtlinie zu ändern, definieren Sie die folgenden Parameter:

* **--container-name**: Der Name des Speicherkontos, das die Dateifreigabe hostet. Um den **Namen** oder den **Anzeigenamen** Ihres Containers abzurufen, verwenden Sie den Befehl [az backup container list](/cli/azure/backup/container#az-backup-container-list).
* **--name**: Der Name der Dateifreigabe, für die Sie die Richtlinie ändern möchten. Um den **Namen** oder **Anzeigenamen** Ihres gesicherten Elements abzurufen, verwenden Sie den Befehl [az backup item list](/cli/azure/backup/item#az-backup-item-list).
* **--policy-name**: Der Name der Sicherungsrichtlinie, die Sie für die Dateifreigabe festlegen möchten. Sie können [az backup policy list](/cli/azure/backup/policy#az-backup-policy-list) verwenden, um alle Richtlinien für Ihren Tresor anzuzeigen.

Im folgenden Beispiel wird die Sicherungsrichtlinie *schedule2* für die Dateifreigabe *azurefiles* im Speicherkonto *afsaccount* festgelegt.

```azurecli-interactive
az backup item set-policy --policy-name schedule2 --name azurefiles --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --name "AzureFileShare;azurefiles" --backup-management-type azurestorage --out table
```

Sie können den vorstehende Befehl auch mit den Anzeigenamen für den Container und das Element ausführen, indem Sie die folgenden beiden zusätzlichen Parameter angeben:

* **--backup-management-type**: *azurestorage*
* **--workload-type**: *azurefileshare*

```azurecli-interactive
az backup item set-policy --policy-name schedule2 --name azurefiles --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --name azurefiles --backup-management-type azurestorage --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
fec6f004-0e35-407f-9928-10a163f123e5  azurefiles
```

Das Attribut **Name** in der Ausgabe entspricht dem Namen des Auftrags, der vom Sicherungsdienst für Ihren Vorgang zum Ändern der Richtlinie erstellt wird. Verwenden Sie zum Nachverfolgen des Auftragsstatus das Cmdlet [az backup job show](/cli/azure/backup/job#az-backup-job-show).

## <a name="stop-protection-on-a-file-share"></a>Beenden des Schutzes für eine Dateifreigabe

Der Schutz für Azure-Dateifreigaben kann auf zwei Arten beendet werden:

* Beenden aller zukünftigen Sicherungsaufträge und *Löschen* aller Wiederherstellungspunkte.
* Beenden aller zukünftigen Sicherungsaufträge unter *Beibehaltung* der Wiederherstellungspunkte.

Unter Umständen fallen für die Aufbewahrung der Wiederherstellungspunkte im Speicher Gebühren an, da die von Azure Backup erstellten zugrundeliegenden Momentaufnahmen beibehalten werden. Der Vorteil der Beibehaltung von Wiederherstellungspunkten besteht darin, dass Sie die Dateifreigabe bei Bedarf später wiederherstellen können. Informationen zu den Kosten, die durch die Beibehaltung der Wiederherstellungspunkte entstehen, finden Sie in der [Preisübersicht](https://azure.microsoft.com/pricing/details/storage/files). Wenn Sie alle Wiederherstellungspunkte löschen, kann die Dateifreigabe nicht mehr wiederhergestellt werden.

Um den Schutz der Dateifreigabe zu beenden, definieren Sie die folgenden Parameter:

* **--container-name**: Der Name des Speicherkontos, das die Dateifreigabe hostet. Um den **Namen** oder den **Anzeigenamen** Ihres Containers abzurufen, verwenden Sie den Befehl [az backup container list](/cli/azure/backup/container#az-backup-container-list).
* **--item-name**: Der Name der Dateifreigabe, deren Schutz Sie beenden möchten. Um den **Namen** oder **Anzeigenamen** Ihres gesicherten Elements abzurufen, verwenden Sie den Befehl [az backup item list](/cli/azure/backup/item#az-backup-item-list).

### <a name="stop-protection-and-retain-recovery-points"></a>Beenden des Schutzes und Beibehalten der Wiederherstellungspunkte

Um den Schutz zu beenden, die Daten jedoch beizubehalten, verwenden Sie das Cmdlet [az backup protection disable](/cli/azure/backup/protection#az-backup-protection-disable).

Im folgenden Beispiel wird der Schutz für die Dateifreigabe *azurefiles* beendet, aber alle Wiederherstellungspunkte werden beibehalten.

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name “AzureFileShare;azurefiles” --out table
```

Sie können den vorstehende Befehl auch mit den Anzeigenamen für den Container und das Element ausführen, indem Sie die folgenden beiden zusätzlichen Parameter angeben:

* **--backup-management-type**: *azurestorage*
* **--workload-type**: *azurefileshare*

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
fec6f004-0e35-407f-9928-10a163f123e5  azurefiles
```

Das Attribut **Name** in der Ausgabe entspricht dem Namen des Auftrags, der vom Sicherungsdienst für Ihren Vorgang zum Beenden des Schutzes erstellt wird. Verwenden Sie zum Nachverfolgen des Auftragsstatus das Cmdlet [az backup job show](/cli/azure/backup/job#az-backup-job-show).

### <a name="stop-protection-without-retaining-recovery-points"></a>Beenden des Schutzes ohne Beibehaltung von Wiederherstellungspunkten

Um den Schutz ohne Beibehaltung der Wiederherstellungspunkte zu beenden, verwenden Sie das Cmdlet [az backup protection disable](/cli/azure/backup/protection#az-backup-protection-disable), und legen Sie dabei die Option **delete-backup-data** auf **true** fest.

Im nachstehenden Beispiel wird der Schutz für die Dateifreigabe *azurefiles* beendet, ohne Wiederherstellungspunkte beizubehalten.

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name “AzureFileShare;azurefiles” --delete-backup-data true --out table
```

Sie können den vorstehende Befehl auch mit den Anzeigenamen für den Container und das Element ausführen, indem Sie die folgenden beiden zusätzlichen Parameter angeben:

* **--backup-management-type**: *azurestorage*
* **--workload-type**: *azurefileshare*

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --delete-backup-data true --out table
```

## <a name="resume-protection-on-a-file-share"></a>Fortsetzen des Schutzes für eine Dateifreigabe

Wenn Sie den Schutz für eine Azure-Dateifreigabe beendet, die Wiederherstellungspunkte jedoch beibehalten haben, können Sie den Schutz später wieder aufnehmen. Wenn Sie die Wiederherstellungspunkte nicht beibehalten, können Sie den Schutz nicht fortsetzen.

Um den Schutz der Dateifreigabe wieder aufzunehmen, definieren Sie die folgenden Parameter:

* **--container-name**: Der Name des Speicherkontos, das die Dateifreigabe hostet. Um den **Namen** oder den **Anzeigenamen** Ihres Containers abzurufen, verwenden Sie den Befehl [az backup container list](/cli/azure/backup/container#az-backup-container-list).
* **--item-name**: Der Name der Dateifreigabe, deren Schutz Sie wieder aufnehmen möchten. Um den **Namen** oder **Anzeigenamen** Ihres gesicherten Elements abzurufen, verwenden Sie den Befehl [az backup item list](/cli/azure/backup/item#az-backup-item-list).
* **--policy-name**: Der Name der Sicherungsrichtlinie, für die Sie den Schutz der Dateifreigabe wieder aufnehmen möchten.

Im folgenden Beispiel wird das Cmdlet [az backup protection resume](/cli/azure/backup/protection#az-backup-protection-resume) verwendet, um den Schutz für die Dateifreigabe *azurefiles* mithilfe der Sicherungsrichtlinie *schedule1* wieder aufzunehmen.

```azurecli-interactive
az backup protection resume --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --policy-name schedule2 --out table
```

Sie können den vorstehende Befehl auch mit den Anzeigenamen für den Container und das Element ausführen, indem Sie die folgenden beiden zusätzlichen Parameter angeben:

* **--backup-management-type**: *azurestorage*
* **--workload-type**: *azurefileshare*

```azurecli-interactive
az backup protection resume --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --policy-name schedule2 --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
75115ab0-43b0-4065-8698-55022a234b7f  azurefiles
```

Das Attribut **Name** in der Ausgabe entspricht dem Namen des Auftrags, der vom Sicherungsdienst für Ihren Vorgang zum Wiederaufnehmen des Schutzes erstellt wird. Verwenden Sie zum Nachverfolgen des Auftragsstatus das Cmdlet [az backup job show](/cli/azure/backup/job#az-backup-job-show).

## <a name="unregister-a-storage-account"></a>Aufheben der Registrierung eines Speicherkontos

Wenn Sie Ihre Dateifreigaben in einem bestimmten Speicherkonto unter Verwendung eines anderen Recovery Services-Tresors schützen möchten, [beenden Sie zunächst den Schutz für alle Dateifreigaben](#stop-protection-on-a-file-share) in diesem Speicherkonto. Heben Sie dann die Registrierung des Kontos im derzeit für den Schutz verwendeten Recovery Services-Tresor auf.

Zum Aufheben der Registrierung des Speicherkontos müssen Sie einen Containernamen angeben. Um den **Namen** oder den **Anzeigenamen** Ihres Containers abzurufen, verwenden Sie den Befehl [az backup container list](/cli/azure/backup/container#az-backup-container-list).

Im folgenden Beispiel wird die Registrierung des Speicherkontos *afsaccount* von *azurefilesvault-* mithilfe des Cmdlets [az backup container unregister](/cli/azure/backup/container#az-backup-container-unregister) aufgehoben.

```azurecli-interactive
az backup container unregister --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --out table
```

Sie können das vorstehende Cmdlet auch mit dem Anzeigenamen für den Container ausführen, indem Sie den folgenden zusätzlichen Parameter angeben:

* **--backup-management-type**: *azurestorage*

```azurecli-interactive
az backup container unregister --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --backup-management-type azurestorage --out table
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie unter [Behandeln von Problemen beim Sichern von Azure-Dateifreigaben](troubleshoot-azure-files.md).
