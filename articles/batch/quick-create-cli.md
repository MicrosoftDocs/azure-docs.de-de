---
title: 'Schnellstart: Ausführen Ihres ersten Batch-Auftrags mit der Azure CLI'
description: Hier können Sie schnell lernen, wie Sie mit der Azure CLI ein Batch-Konto erstellen und einen Batch-Auftrag ausführen.
ms.topic: quickstart
ms.date: 08/13/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: eb5bb4a627ff26250519651f5e6d47ddd6f5a776
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94562286"
---
# <a name="quickstart-run-your-first-batch-job-with-the-azure-cli"></a>Schnellstart: Ausführen Ihres ersten Batch-Auftrags mit der Azure CLI

Führen Sie erste Schritte mit Azure Batch per Azure CLI aus, um ein Batch-Konto, einen Pool mit Computeknoten (virtuelle Computer) und einen Auftrag zu erstellen, mit dem Aufgaben im Pool ausgeführt werden. Jede Beispielaufgabe führt einen einfachen Befehl für einen der Poolknoten aus.

Die Azure CLI dient zum Erstellen und Verwalten von Azure-Ressourcen über die Befehlszeile oder mit Skripts. Nach Abschluss dieser Schnellstartanleitung sind Sie mit den wichtigsten Konzepten des Batch-Diensts vertraut und können Batch mit realistischeren Workloads und in größerem Umfang ausprobieren.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Für diesen Schnellstart ist mindestens Version 2.0.20 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Erstellen Sie mithilfe des Befehls [az group create](/cli/azure/group#az-group-create) eine Ressourcengruppe. Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden.

Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen *QuickstartBatch-rg* am Standort *eastus2* erstellt.

```azurecli-interactive
az group create \
    --name QuickstartBatch-rg \
    --location eastus2
```

## <a name="create-a-storage-account"></a>Speicherkonto erstellen

Sie können ein Azure Storage-Konto mit Ihrem Batch-Konto verknüpfen. Auch wenn es für diese Schnellstartanleitung nicht erforderlich ist, ist das Speicherkonto nützlich zum Bereitstellen von Anwendungen und Speichern von Eingabe- und Ausgabedaten für die meisten Workloads in der Praxis. Erstellen Sie mit dem Befehl [az storage account create](/cli/azure/storage/account#az-storage-account-create) in Ihrer Ressourcengruppe ein Speicherkonto.

```azurecli-interactive
az storage account create \
    --resource-group QuickstartBatch-rg \
    --name mystorageaccount \
    --location eastus2 \
    --sku Standard_LRS
```

## <a name="create-a-batch-account"></a>Erstellen eines Batch-Kontos

Erstellen Sie mit dem Befehl [az batch account create](/cli/azure/batch/account#az-batch-account-create) ein Batch-Konto. Sie benötigen ein Konto zum Erstellen von Computeressourcen (Pools mit Computeknoten) und Batch-Aufträgen.

Im folgenden Beispiel wird ein Batch-Konto mit dem Namen *mybatchaccount* in *QuickstartBatch-rg* erstellt, und das von Ihnen erstellte Speicherkonto wird verknüpft.  

```azurecli-interactive
az batch account create \
    --name mybatchaccount \
    --storage-account mystorageaccount \
    --resource-group QuickstartBatch-rg \
    --location eastus2
```

Zum Erstellen und Verwalten von Computepools und -aufträgen müssen Sie die Authentifizierung für Batch durchführen. Melden Sie sich mit dem Befehl [az batch account login](/cli/azure/batch/account#az-batch-account-login) am Konto an. Nachdem Sie sich angemeldet haben, wird für Ihre `az batch`-Befehle dieser Kontokontext verwendet.

```azurecli-interactive
az batch account login \
    --name mybatchaccount \
    --resource-group QuickstartBatch-rg \
    --shared-key-auth
```

## <a name="create-a-pool-of-compute-nodes"></a>Erstellen eines Pools mit Computeknoten

Nachdem Sie nun über ein Batch-Konto verfügen, können Sie einen Beispielpool mit Linux-Computeknoten erstellen, indem Sie den Befehl [az batch pool create](/cli/azure/batch/pool#az-batch-pool-create) verwenden. Im folgenden Beispiel wird ein Pool mit dem Namen *mypool* erstellt, der zwei Knoten der Größe *Standard_A1_v2* mit Ubuntu 16.04 LTS enthält. Die vorgeschlagene Knotengröße bietet für dieses kurze Beispiel eine gute Balance zwischen Leistung und Kosten.
 
```azurecli-interactive
az batch pool create \
    --id mypool --vm-size Standard_A1_v2 \
    --target-dedicated-nodes 2 \
    --image canonical:ubuntuserver:16.04-LTS \
    --node-agent-sku-id "batch.node.ubuntu 16.04"
```

Batch erstellt den Pool sofort, aber es dauert einige Minuten, bis die Computeknoten zugeordnet und gestartet wurden. Während dieses Zeitraums befindet sich der Pool im Status `resizing`. Führen Sie den Befehl [az batch pool show](/cli/azure/batch/pool#az-batch-pool-show) aus, um den Status des Pools anzuzeigen. Mit diesem Befehl werden alle Eigenschaften des Pools angezeigt, und Sie können bestimmte Eigenschaften abfragen. Mit dem folgenden Befehl wird der Zuweisungsstatus des Pools abgerufen:

```azurecli-interactive
az batch pool show --pool-id mypool \
    --query "allocationState"
```

Fahren Sie mit den folgenden Schritten fort, um einen Auftrag und Aufgaben zu erstellen, während sich der Poolstatus ändert. Der Pool ist für die Durchführung von Aufgaben bereit, wenn der Zuweisungsstatus `steady` lautet und alle Knoten ausgeführt werden.

## <a name="create-a-job"></a>Erstellen eines Auftrags

Da Sie jetzt über einen Pool verfügen, können Sie einen Auftrag erstellen, um ihn darin auszuführen. Ein Batch-Auftrag ist eine logische Gruppe für eine oder mehrere Aufgaben. Ein Auftrag enthält gemeinsame Einstellungen für Aufgaben, z.B. die Priorität und den Pool zum Ausführen von Aufgaben. Erstellen Sie einen Batch-Auftrag, indem Sie den Befehl [az batch job create](/cli/azure/batch/job#az-batch-job-create) verwenden. Im folgenden Beispiel wird im Pool *mypool* der Auftrag *myjob* erstellt. Der Auftrag enthält ursprünglich keine Aufgaben.

```azurecli-interactive
az batch job create \
    --id myjob \
    --pool-id mypool
```

## <a name="create-tasks"></a>Erstellen von Aufgaben

Verwenden Sie nun den Befehl [az batch task create](/cli/azure/batch/task#az-batch-task-create), um einige Aufgaben für die Ausführung per Auftrag zu erstellen. In diesem Beispiel erstellen Sie vier identische Aufgaben. Für jede Aufgabe wird eine Befehlszeile (`command-line`) zum Anzeigen der Batch-Umgebungsvariablen auf einem Computeknoten ausgeführt, und anschließend wird 90 Sekunden gewartet. Bei Verwendung von Batch befindet sich diese Befehlszeile dort, wo Sie Ihre App bzw. Ihr Skript angeben. In Batch gibt es mehrere Möglichkeiten, Apps und Skripts auf Computeknoten bereitzustellen.

Mit dem folgenden Bash-Skript werden vier parallele Aufgaben erstellt (*mytask1* bis *mytask4*).

```azurecli-interactive
for i in {1..4}
do
   az batch task create \
    --task-id mytask$i \
    --job-id myjob \
    --command-line "/bin/bash -c 'printenv | grep AZ_BATCH; sleep 90s'"
done
```

In der Befehlsausgabe werden Einstellungen für die einzelnen Aufgaben angezeigt. Batch verteilt die Aufgaben auf die Computeknoten.

## <a name="view-task-status"></a>Anzeigen des Aufgabenstatus

Nachdem Sie eine Aufgabe erstellt haben, wird sie von Batch zur Ausführung im Pool in die Warteschlange eingereiht. Sobald ein Knoten für die Ausführung verfügbar ist, wird die Aufgabe ausgeführt.

Verwenden Sie den Befehl [az batch task show](/cli/azure/batch/task#az-batch-task-show), um den Status der Batch-Aufgaben anzuzeigen. Im folgenden Beispiel werden Details zur Aufgabe *mytask1* angezeigt, die auf einem der Poolknoten ausgeführt wird.

```azurecli-interactive
az batch task show \
    --job-id myjob \
    --task-id mytask1
```

Die Befehlsausgabe enthält viele Details, aber achten Sie besonders auf das `exitCode`-Element der Aufgabenbefehlszeile und das `nodeId`-Element. Wenn `exitCode` den Wert „0“ hat, bedeutet dies, dass die Aufgabenbefehlszeile erfolgreich abgeschlossen wurde. Mit `nodeId` wird die ID des Poolknotens angegeben, auf dem die Aufgabe ausgeführt wurde.

## <a name="view-task-output"></a>Anzeigen der Aufgabenausgabe

Verwenden Sie zum Auflisten der Dateien, die von einer Aufgabe auf einem Computeknoten erstellt werden, den Befehl [az batch task file list](/cli/azure/batch/task). Mit dem folgenden Befehl werden die Dateien aufgeführt, die mit *mytask1* erstellt werden:

```azurecli-interactive
az batch task file list \
    --job-id myjob \
    --task-id mytask1 \
    --output table
```

Die Ausgabe sieht in etwa wie folgt aus:

```
Name        URL                                                                                         Is Directory      Content Length
----------  ------------------------------------------------------------------------------------------  --------------  ----------------
stdout.txt  https://mybatchaccount.eastus2.batch.azure.com/jobs/myjob/tasks/mytask1/files/stdout.txt  False                  695
certs       https://mybatchaccount.eastus2.batch.azure.com/jobs/myjob/tasks/mytask1/files/certs       True
wd          https://mybatchaccount.eastus2.batch.azure.com/jobs/myjob/tasks/mytask1/files/wd          True
stderr.txt  https://mybatchaccount.eastus2.batch.azure.com/jobs/myjob/tasks/mytask1/files/stderr.txt  False                     0

```

Verwenden Sie den Befehl [az batch task file download](/cli/azure/batch/task), um eine der Ausgabedateien in ein lokales Verzeichnis herunterzuladen. In diesem Beispiel ist die Aufgabenausgabe in `stdout.txt` enthalten.

```azurecli-interactive
az batch task file download \
    --job-id myjob \
    --task-id mytask1 \
    --file-path stdout.txt \
    --destination ./stdout.txt
```

Sie können den Inhalt von `stdout.txt` in einem Text-Editor anzeigen. Darin sind die Azure Batch-Umgebungsvariablen zu sehen, die auf dem Knoten festgelegt sind. Beim Erstellen Ihrer eigenen Batch-Aufträge können Sie auf diese Umgebungsvariablen in Aufgabenbefehlszeilen sowie in den Apps und Skripts verweisen, die über die Befehlszeilen ausgeführt werden. Beispiel:

```
AZ_BATCH_TASK_DIR=/mnt/batch/tasks/workitems/myjob/job-1/mytask1
AZ_BATCH_NODE_STARTUP_DIR=/mnt/batch/tasks/startup
AZ_BATCH_CERTIFICATES_DIR=/mnt/batch/tasks/workitems/myjob/job-1/mytask1/certs
AZ_BATCH_ACCOUNT_URL=https://mybatchaccount.eastus2.batch.azure.com/
AZ_BATCH_TASK_WORKING_DIR=/mnt/batch/tasks/workitems/myjob/job-1/mytask1/wd
AZ_BATCH_NODE_SHARED_DIR=/mnt/batch/tasks/shared
AZ_BATCH_TASK_USER=_azbatch
AZ_BATCH_NODE_ROOT_DIR=/mnt/batch/tasks
AZ_BATCH_JOB_ID=myjobl
AZ_BATCH_NODE_IS_DEDICATED=true
AZ_BATCH_NODE_ID=tvm-257509324_2-20180703t215033z
AZ_BATCH_POOL_ID=mypool
AZ_BATCH_TASK_ID=mytask1
AZ_BATCH_ACCOUNT_NAME=mybatchaccount
AZ_BATCH_TASK_USER_IDENTITY=PoolNonAdmin
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie mit den Batch-Tutorials und -Beispielen fortfahren möchten, können Sie das erstellte Batch-Konto und das verknüpfte Speicherkonto aus dieser Schnellstartanleitung verwenden. Für das Batch-Konto selbst fallen keine Gebühren an.

Ihnen werden auch dann Gebühren für Pools berechnet, während die Knoten ausgeführt werden, wenn keine Aufträge geplant sind. Falls Sie einen Pool nicht mehr benötigen, ist es daher ratsam, ihn mit dem Befehl [az batch pool delete](/cli/azure/batch/pool#az-batch-pool-delete) zu löschen. Beim Löschen des Pools werden alle Aufgabenausgaben auf den Knoten gelöscht.

```azurecli-interactive
az batch pool delete --pool-id mypool
```

Mit dem Befehl [az group delete](/cli/azure/group#az-group-delete) können Sie die Ressourcengruppe, das Batch-Konto, Pools und alle dazugehörigen Ressourcen entfernen, wenn sie nicht mehr benötigt werden. Löschen Sie die Ressourcen wie folgt:

```azurecli-interactive
az group delete --name QuickstartBatch-rg
```

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie ein Batch-Konto, einen Batch-Pool und einen Batch-Auftrag erstellt. Mit dem Auftrag wurden Beispielaufgaben ausgeführt, und Sie haben die Ausgabe angezeigt, die auf einem der Knoten erstellt wurde. Da Sie sich jetzt mit den wichtigsten Konzepten des Batch-Diensts vertraut gemacht haben, können Sie Batch mit realistischeren Workloads und in größerem Umfang ausprobieren. Weitere Informationen zu Azure Batch finden Sie in den Azure Batch-Tutorials.

> [!div class="nextstepaction"]
> [Azure Batch-Tutorials](./tutorial-parallel-dotnet.md)
