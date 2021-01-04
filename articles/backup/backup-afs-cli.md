---
title: Sichern von Azure-Dateifreigaben mit Azure CLI
description: Hier erfahren Sie, wie Sie Azure-Dateifreigaben mithilfe der Azure-Befehlszeilenschnittstelle (Azure CLI) im Recovery Services-Tresor sichern.
ms.topic: conceptual
ms.date: 01/14/2020
ms.openlocfilehash: 34eea8daa6a0a8920c842178664055838b06a78a
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94565890"
---
# <a name="back-up-azure-file-shares-with-azure-cli"></a>Sichern von Azure-Dateifreigaben mit Azure CLI

Die Azure-Befehlszeilenschnittstelle (CLI) stellt eine Befehlszeilenumgebung zum Verwalten von Azure-Ressourcen bereit. Azure CLI ist ein großartiges Tool zum Erstellen einer benutzerdefinierten Automatisierung zur Verwendung von Azure-Ressourcen. In diesem Artikel wird erläutert, wie Sie Azure-Dateifreigaben mit Azure CLI sichern. Sie können diese Schritte auch mit [Azure PowerShell](./backup-azure-afs-automation.md) oder im [Azure-Portal](backup-afs.md) ausführen.

Am Ende dieses Tutorials erfahren Sie, wie Sie die folgenden Vorgänge mit Azure CLI ausführen:

* Erstellen eines Recovery Services-Tresors
* Aktivieren der Sicherung für Azure-Dateifreigaben
* Auslösen einer bedarfsgesteuerten Sicherung für Dateifreigaben

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Für dieses Tutorial ist mindestens Version 2.0.18 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

## <a name="create-a-recovery-services-vault"></a>Erstellen eines Recovery Services-Tresors

Ein Recovery Services-Tresor ist eine Entität, die eine konsolidierte Ansicht und Verwaltungsfunktion für alle Sicherungselemente bereitstellt. Wenn der Sicherungsauftrag für eine geschützte Ressource ausgeführt wird, wird im Recovery Services-Tresor ein Wiederherstellungspunkt erstellt. Sie können einen dieser Wiederherstellungspunkte dann verwenden, um Daten für einen bestimmten Zeitpunkt wiederherzustellen.

Führen Sie die nachstehenden Schritte aus, um einen Recovery Services-Tresors zu erstellen:

1. Ein Tresor wird in einer Ressourcengruppe platziert. Erstellen Sie mit [az group create](/cli/azure/group#az-group-create) eine neue Ressourcengruppe, wenn noch keine vorhanden ist. In diesem Tutorial wird die neue Ressourcengruppe *azurefiles* in der Region "USA, Osten" erstellt.

    ```azurecli-interactive
    az group create --name AzureFiles --location eastus --output table
    ```

    ```output
    Location    Name
    ----------  ----------
    eastus      AzureFiles
    ```

1. Verwenden Sie das Cmdlet [az backup vault create](/cli/azure/backup/vault#az-backup-vault-create), um den Tresor zu erstellen. Geben Sie denselben Speicherort für den Tresor an, der für die Ressourcengruppe verwendet wurde.

    Im folgenden Beispiel wird ein Recovery Services-Tresor mit dem Namen *azurefilesvault* in der Region „USA, Osten“ erstellt.

    ```azurecli-interactive
    az backup vault create --resource-group azurefiles --name azurefilesvault --location eastus --output table
    ```

    ```output
    Location    Name                ResourceGroup
    ----------  ----------------    ---------------
    eastus      azurefilesvault     azurefiles
    ```

## <a name="enable-backup-for-azure-file-shares"></a>Aktivieren der Sicherung für Azure-Dateifreigaben

In diesem Abschnitt wird davon ausgegangen, dass Sie bereits über eine Azure-Dateifreigabe verfügen, für die Sie die Sicherung konfigurieren möchten. Wenn Sie über keine Azure-Dateifreigabe verfügen, erstellen Sie eine mit dem Befehl [az storage share create](/cli/azure/storage/share#az-storage-share-create).

Zum Aktivieren der Sicherung für Dateifreigaben müssen Sie eine Schutzrichtlinie erstellen, die festlegt, wann ein Sicherungsauftrag ausgeführt wird und wie lange Wiederherstellungspunkte gespeichert werden. Mit dem Cmdlet [az backup policy create](/cli/azure/backup/policy#az-backup-policy-create) können Sie eine Sicherungsrichtlinie erstellen.

Im folgenden Beispiel wird das Cmdlet [az backup protection enable-for-azurefileshare](/cli/azure/backup/protection#az-backup-protection-enable-for-azurefileshare) verwendet, um die Sicherung für die Dateifreigabe *azurefiles* im Speicherkonto *afsaccount* unter Verwendung der Sicherungsrichtlinie *Zeitplan 1* zu aktivieren:

```azurecli-interactive
az backup protection enable-for-azurefileshare --vault-name azurefilesvault --resource-group  azurefiles --policy-name schedule1 --storage-account afsaccount --azure-file-share azurefiles  --output table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
0caa93f4-460b-4328-ac1d-8293521dd928  azurefiles
```

Das Attribut **Name** in der Ausgabe entspricht dem Namen des Auftrags, der vom Sicherungsdienst für Ihre **Sicherungsaktivierung** erstellt wird. Verwenden Sie zum Nachverfolgen des Auftragsstatus das Cmdlet [az backup job show](/cli/azure/backup/job#az-backup-job-show).

## <a name="trigger-an-on-demand-backup-for-file-share"></a>Auslösen einer bedarfsgesteuerten Sicherung für Dateifreigaben

Wenn Sie eine bedarfsgesteuerte Sicherung für die Dateifreigabe auslösen möchten, anstatt zu warten, bis die Sicherungsrichtlinie den Auftrag zum geplanten Zeitpunkt ausführt, verwenden Sie das Cmdlet [az backup protection backup-now](/cli/azure/backup/protection#az-backup-protection-backup-now).

Sie müssen die folgenden Parameter angeben, um eine bedarfsgesteuerte Sicherung zu starten:

* **--container-name** ist der Name des Speicherkontos, das die Dateifreigabe hostet. Um den **Namen** oder den **Anzeigenamen** Ihres Containers abzurufen, verwenden Sie den Befehl [az backup container list](/cli/azure/backup/container#az-backup-container-list).
* **--item-name** ist der Name der Dateifreigabe, für die Sie eine bedarfsgesteuerte Sicherung auslösen möchten. Um den **Namen** oder den **Anzeigenamen** Ihres gesicherten Elements abzurufen, verwenden Sie den Befehl [az backup item list](/cli/azure/backup/item#az-backup-item-list).
* **--retain-until** gibt das Datum an, bis wann Sie der Wiederherstellungspunkt aufbewahrt werden soll. Der Wert muss im UTC-Zeitformat (TT-MM-JJJJ) festgelegt werden.

Im folgenden Beispiel wird eine bedarfsgesteuerte Sicherung für die Dateifreigabe *azurefiles* im Speicherkonto *afsaccount* mit Aufbewahrung bis *20-01-2020* ausgelöst.

```azurecli-interactive
az backup protection backup-now --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --retain-until 20-01-2020 --output table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
9f026b4f-295b-4fb8-aae0-4f058124cb12  azurefiles
```

Das Attribut **Name** in der Ausgabe entspricht dem Namen des Auftrags, der vom Sicherungsdienst für Ihre bedarfsgesteuerte Sicherung erstellt wird. Verwenden Sie zum Nachverfolgen des Auftragsstatus das Cmdlet [az backup job show](/cli/azure/backup/job#az-backup-job-show).

## <a name="next-steps"></a>Nächste Schritte

* [Wiederherstellen von Azure-Dateifreigaben mit der Befehlszeilenschnittstelle](restore-afs-cli.md)
* [Verwalten von Azure-Dateifreigabensicherungen mit der Befehlszeilenschnittstelle](manage-afs-backup-cli.md)
