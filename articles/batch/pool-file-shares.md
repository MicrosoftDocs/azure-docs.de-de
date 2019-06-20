---
title: Azure-Dateifreigabe für Azure Batch-Pools | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie eine Azure Files-Freigabe von Computeknoten in einen Linux- oder Windows-Pool in Azure Batch einbinden.
services: batch
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: big-compute
ms.date: 05/24/2018
ms.author: lahugh
ms.custom: ''
ms.openlocfilehash: 1e9d039769e7fbcb9c2b7285aa727acd7322bcdf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62127827"
---
# <a name="use-an-azure-file-share-with-a-batch-pool"></a>Verwenden einer Azure-Dateifreigabe mit einem Batch-Pool

[Azure Files](../storage/files/storage-files-introduction.md) bietet vollständig verwaltete Dateifreigaben in der Cloud, auf die über das Protokoll Server Message Block (SMB) zugegriffen werden kann. Dieser Artikel enthält Informationen und Codebeispiele für das Einbinden und Verwenden einer Azure-Dateifreigabe auf Computeknoten in einem Pool. Die Codebeispiele verwenden die Batch SDKs für .NET und Python, Sie können ähnliche Vorgänge aber auch mit anderen Batch-SDKs und Batch-Tools ausführen.

Batch bietet native API-Unterstützung für die Verwendung von Azure Storage-Blobs zum Lesen und Schreiben von Daten. In manchen Fällen ist es jedoch sinnvoll, von den Computeknoten im Pool aus auf eine Azure-Dateifreigabe zuzugreifen. Dies trifft beispielsweise dann zu, wenn Sie über eine Legacyworkload verfügen, die von einer SMB-Dateifreigabe abhängig ist, oder wenn Ihre Tasks auf freigegebene Daten zugreifen müssen oder gemeinsam nutzbare Ausgaben erzeugen. 

## <a name="considerations-for-use-with-batch"></a>Überlegungen für die Verwendung mit Batch

* Wenn Sie über Pools verfügen, die eine verhältnismäßig geringe Anzahl von parallelen Tasks ausführen, sollten Sie den Einsatz einer Azure-Dateifreigabe in Erwägung ziehen. Sehen Sie sich die [Skalierbarkeits- und Leistungsziele](../storage/files/storage-files-scale-targets.md) an, um anhand der erwarteten Poolgröße und der erwarteten Anzahl von Ressourcendateien zu ermitteln, ob Sie Azure Files verwenden sollten (wofür ein Azure Storage-Konto benötigt wird). 

* Azure-Dateifreigaben sind [kostengünstig](https://azure.microsoft.com/pricing/details/storage/files/). Für die Freigaben kann eine Datenreplikation in eine andere Region konfiguriert werden, sodass sie global redundant sind. 

* Sie können eine Azure-Dateifreigabe parallel von einem lokalen Computer aus einbinden.

* Sehen Sie sich auch die allgemeinen [Planungsüberlegungen](../storage/files/storage-files-planning.md) für Azure-Dateifreigaben an.


## <a name="create-a-file-share"></a>Erstellen einer Dateifreigabe

[Erstellen Sie eine Dateifreigabe](../storage/files/storage-how-to-create-file-share.md) in einem Speicherkonto, das mit Ihrem Batch-Konto verknüpft ist, oder in einem separaten Speicherkonto.

## <a name="mount-a-share-on-a-windows-pool"></a>Einbinden einer Freigabe in einen Windows-Pool

In diesem Abschnitt finden Sie Schritte und Codebeispiele zum Einbinden und Verwenden einer Azure-Dateifreigabe in einem Pool aus Windows-Knoten. Weitere Hintergrundinformationen finden Sie in der [Dokumentation](../storage/files/storage-how-to-use-files-windows.md) zum Einbinden einer Azure-Dateifreigabe in Windows. 

In Batch müssen Sie die Freigabe jedes Mal einbinden, wenn ein Task auf einem Windows-Knoten ausgeführt wird. Derzeit ist es nicht möglich, die Netzwerkverbindung zwischen Tasks auf Windows-Knoten dauerhaft beizubehalten.

Fügen Sie z.B. in die Befehlszeile für jeden Task einen `net use`-Befehl zum Einbinden der Dateifreigabe ein. Zum Einbinden der Dateifreigabe sind folgende Anmeldeinformationen erforderlich:

* **Benutzername**: AZURE\\\<storageaccountname\>. Beispiel: AZURE\\*mystorageaccountname*
* **Kennwort**: <StorageAccountKeyWhichEnds in==>. Beispiel: *XXXXXXXXXXXXXXXXXXXXX==*

Der folgende Befehl bindet die Dateifreigabe *myfileshare* als Laufwerk *S:* in das Speicherkonto *mystorageaccountname* ein:

```
net use S: \\mystorageaccountname.file.core.windows.net\myfileshare /user:AZURE\mystorageaccountname XXXXXXXXXXXXXXXXXXXXX==
```

Zur Vereinfachung übergeben die hier genannten Beispiele die Anmeldeinformationen direkt im Text. In der Praxis sollten Sie die Anmeldeinformationen unbedingt über Umgebungsvariablen, Zertifikate oder eine Lösung wie Azure Key Vault verwalten.

Um den Einbindevorgang zu vereinfachen, können Sie die Anmeldeinformationen optional dauerhaft auf den Knoten speichern. Dann können Sie die Freigabe ohne Anmeldeinformationen einbinden. Führen Sie die folgenden beiden Schritte aus:

1. Führen Sie das Befehlszeilenprogramm `cmdkey` mithilfe eines Starttasks in der Poolkonfiguration aus. Damit werden die Anmeldeinformationen dauerhaft auf jedem Windows-Knoten gespeichert. Die Befehlszeile des Starttasks sieht etwa wie folgt aus:

   ```
   cmd /c "cmdkey /add:mystorageaccountname.file.core.windows.net /user:AZURE\mystorageaccountname /pass:XXXXXXXXXXXXXXXXXXXXX=="

   ```

2. Binden Sie die Freigabe mithilfe von `net use` als Teil jedes Tasks in jeden Knoten ein. Der folgende Taskbefehl bindet die Dateifreigabe beispielsweise als Laufwerk *S:* ein. Danach folgt ein Befehl oder Skript, der/das auf die Freigabe verweist. Im Aufruf von `net use` werden zwischengespeicherte Anmeldeinformationen verwendet. Bei diesem Schritt wird davon ausgegangen, dass Sie die gleiche Benutzeridentität für die Tasks verwenden, die Sie auch im Starttask im Pool verwendet haben. Dieses Vorgehen eignet sich nicht für jedes Szenario.

   ```
   cmd /c "net use S: \\mystorageaccountname.file.core.windows.net\myfileshare" 
   ```

### <a name="c-example"></a>C#-Beispiel
Das folgende C#-Beispiel zeigt, wie Sie die Anmeldeinformationen mithilfe eines Starttasks dauerhaft in einem Windows-Pool speichern. Der Dienstname und die Anmeldeinformationen der Speicherdatei werden als definierte Konstanten übergeben. Hier wird der Starttask mit einem Standardkonto eines automatischen Benutzers (kein Administrator) mit Berechtigungen für den Pool ausgeführt.

```csharp
...
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: PoolId,
    targetDedicatedComputeNodes: PoolNodeCount,
    virtualMachineSize: PoolVMSize,
    virtualMachineConfiguration: virtualMachineConfiguration);

// Start task to store credentials to mount file share
string startTaskCommandLine = String.Format("cmd /c \"cmdkey /add:{0} /user:AZURE\\{1} /pass:{2}\"", StorageFileService, StorageAccountName, StorageAccountKey);

pool.StartTask = new StartTask
{
    CommandLine = startTaskCommandLine,
    UserIdentity = new UserIdentity(new AutoUserSpecification(
        elevationLevel: ElevationLevel.NonAdmin, 
        scope: AutoUserScope.Pool))
};

pool.Commit();
```

Verwenden Sie nach dem Speichern der Anmeldeinformationen die Taskbefehlszeilen, um die Freigabe einzubinden und in Lese- oder Schreibvorgängen darauf zu verweisen. Ein einfaches Beispiel: Die Taskbefehlszeile im folgenden Codeausschnitt verwendet den Befehl `dir`, um Dateien in der Dateifreigabe aufzulisten. Stellen Sie sicher, dass Sie jeden Auftragstask mit der gleichen [Benutzeridentität](batch-user-accounts.md) ausführen, die Sie zur Ausführung des Starttasks im Pool verwendet haben. 

```csharp
...
string taskId = "myTask";
string taskCommandLine = String.Format("cmd /c \"net use {0} {1} & dir {2}\"", ShareMountPoint, StorageFileShare, ShareMountPoint);

CloudTask task = new CloudTask(taskId, taskCommandLine);
task.UserIdentity = new UserIdentity(new AutoUserSpecification(
    elevationLevel: ElevationLevel.NonAdmin,
    scope: AutoUserScope.Pool));
tasks.Add(task);
```

## <a name="mount-a-share-on-a-linux-pool"></a>Einbinden einer Freigabe in einen Linux-Pool

Azure-Dateifreigaben können mithilfe des [CIFS-Kernelclients](https://wiki.samba.org/index.php/LinuxCIFS) in Linux-Distributionen eingebunden werden. Das folgende Beispiel zeigt, wie Sie eine Dateifreigabe in einen Pool mit Ubuntu 16.04 LTS-Computeknoten einbinden. Wenn Sie eine andere Linux-Distribution verwenden, sind die allgemeinen Schritte gleich. Sie müssen allerdings den entsprechenden Paket-Manager für die Distribution verwenden. Weitere Informationen und Beispiele finden Sie unter [Verwenden von Azure Files mit Linux](../storage/files/storage-how-to-use-files-linux.md).

Installieren Sie zunächst mit einer Administratorbenutzeridentität das Paket `cifs-utils`, und erstellen Sie den Bereitstellungspunkt (z.B. */mnt/MyAzureFileShare*) im lokalen Dateisystem. Ein Ordner für einen Bereitstellungspunkt kann zwar an einem beliebigen Ort im Dateisystem erstellt werden, üblicherweise erfolgt die Erstellung jedoch im Ordner `/mnt`. Stellen Sie sicher, dass Sie keinen Bereitstellungspunkt direkt unter `/mnt` (in Ubuntu) oder unter `/mnt/resource` (in anderen Distributionen) erstellen.

```
apt-get update && apt-get install cifs-utils && sudo mkdir -p /mnt/MyAzureFileShare
```

Führen Sie dann den Befehl `mount` aus, um die Dateifreigabe einzubinden, und geben Sie die folgenden Anmeldeinformationen an:

* **Benutzername**: \<storageaccountname\>. Beispiel: *mystorageaccountname*
* **Kennwort**: <StorageAccountKeyWhichEnds in==>. Beispiel: *XXXXXXXXXXXXXXXXXXXXX==*

Der folgende Befehl bindet die Dateifreigabe *myfileshare* unter */mnt/MyAzureFileShare* in das Speicherkonto *mystorageaccountname* ein: 

```
mount -t cifs //mystorageaccountname.file.core.windows.net/myfileshare /mnt/MyAzureFileShare -o vers=3.0,username=mystorageaccountname,password=XXXXXXXXXXXXXXXXXXXXX==,dir_mode=0777,file_mode=0777,serverino && ls /mnt/MyAzureFileShare
```

Zur Vereinfachung übergeben die hier genannten Beispiele die Anmeldeinformationen direkt im Text. In der Praxis sollten Sie die Anmeldeinformationen unbedingt über Umgebungsvariablen, Zertifikate oder eine Lösung wie Azure Key Vault verwalten.

In einem Linux-Pool können Sie all diese Schritte in einem einzigen Starttask kombinieren oder in einem Skript ausführen. Führen Sie den Starttask als Administratorbenutzer für den Pool aus. Legen Sie den Starttask so fest, dass auf den vollständigen erfolgreichen Abschluss gewartet wird, bevor weitere Tasks in dem Pool ausgeführt werden, die auf die Freigabe verweisen.

### <a name="python-example"></a>Beispiel für Python

Das folgende Python-Beispiel zeigt, wie Sie einen Ubuntu-Pool so konfigurieren, dass die Freigabe in einem Starttask eingebunden wird. Der Bereitstellungspunkt, der Endpunkt der Dateifreigabe und die Anmeldeinformationen für das Speicherkonto werden als definierte Konstanten übergeben. Der Starttask wird mit einem Administratorkonto eines automatischen Benutzers mit Berechtigungen für den Pool ausgeführt.

```python
pool = batch.models.PoolAddParameter(
    id=pool_id,
    virtual_machine_configuration = batchmodels.VirtualMachineConfiguration(
        image_reference = batchmodels.ImageReference(
            publisher="Canonical",
            offer="UbuntuServer",
            sku="16.04.0-LTS",
            version="latest"),
        node_agent_sku_id = "batch.node.ubuntu 16.04"),
    vm_size=_POOL_VM_SIZE,
    target_dedicated_nodes=_POOL_NODE_COUNT,
    start_task=batchmodels.StartTask(
        command_line="/bin/bash -c \"apt-get update && apt-get install cifs-utils && mkdir -p {} && mount -t cifs {} {} -o vers=3.0,username={},password={},dir_mode=0777,file_mode=0777,serverino\"".format(_COMPUTE_NODE_MOUNT_POINT, _STORAGE_ACCOUNT_SHARE_ENDPOINT, _COMPUTE_NODE_MOUNT_POINT, _STORAGE_ACCOUNT_NAME, _STORAGE_ACCOUNT_KEY),
        wait_for_success=True,
        user_identity=batchmodels.UserIdentity(
            auto_user=batchmodels.AutoUserSpecification(
                scope=batchmodels.AutoUserScope.pool,
                elevation_level=batchmodels.ElevationLevel.admin)),
    )
)
batch_service_client.pool.add(pool)
```

Nach dem Einbinden der Freigabe und dem Definieren eines Auftrags verwenden Sie die Freigabe in Ihren Taskbefehlszeilen. Der folgende einfache Befehl verwendet beispielsweise `ls`, um alle Dateien in der Dateifreigabe aufzulisten.

```python
...
task = batch.models.TaskAddParameter(
    id='mytask',
    command_line="/bin/bash -c \"ls {}\"".format(_COMPUTE_NODE_MOUNT_POINT))

batch_service_client.task.add(job_id, task)
```


## <a name="next-steps"></a>Nächste Schritte

* Weitere Optionen zum Lesen und Schreiben von Daten in Batch finden Sie in der [Übersicht über Batch-Features](batch-api-basics.md) und unter [Persistente Aufträge und Aufgabenausgabe](batch-task-output.md).

* Weitere Informationen finden Sie auch im [Batch Shipyard](https://github.com/Azure/batch-shipyard)-Toolkit, das [Shipyard-Rezepte](https://github.com/Azure/batch-shipyard/tree/master/recipes) enthält, mit denen Dateisysteme für Batch-Containerworkloads bereitgestellt werden können.