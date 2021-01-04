---
title: Rendern einer Szene in der Cloud
description: Tutorial – Rendern einer Autodesk 3ds Max-Szene mit Arnold per Batch Rendering-Dienst und Azure-Befehlszeilenschnittstelle
ms.topic: tutorial
ms.date: 03/05/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 579a5446cb199bb73f98e2e1cbb0948f062470a8
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94542387"
---
# <a name="tutorial-render-a-scene-with-azure-batch"></a>Tutorial: Rendern einer Szene mit Azure Batch 

Azure Batch stellt Renderingfunktionen mit nutzungsbasierter Bezahlung bereit. Azure Batch unterstützt Rendering-Apps wie Autodesk Maya, 3ds Max, Arnold und V-Ray. In diesem Tutorial werden die Schritte zum Rendern einer kleinen Szene mit Batch über die Azure-Befehlszeilenschnittstelle veranschaulicht. Folgendes wird vermittelt:

> [!div class="checklist"]
> * Hochladen einer Szene in Azure-Speicher
> * Erstellen eines Batch-Pools für das Rendering
> * Rendern einer Szene mit einem Frame
> * Skalieren des Pools und Rendern einer Szene mit mehreren Frames
> * Herunterladen der gerenderten Ausgabe

In diesem Tutorial rendern Sie eine 3ds Max-Szene mit Batch, indem Sie den [Arnold](https://www.autodesk.com/products/arnold/overview)-Raytracing-Renderer verwenden. Der Batch-Pool verwendet ein Azure Marketplace-Image mit vorinstallierten Grafik- und Renderinganwendungen, die nutzungsbasierte Lizenzierung bereitstellen.

## <a name="prerequisites"></a>Voraussetzungen

 - Sie benötigen ein Abonnement mit nutzungsbasierter Bezahlung oder eine andere Azure-Kaufoption, um die Renderinganwendungen in Batch auf nutzungsbasierter Basis zu verwenden. **Nutzungsbasierte Lizenzierung wird nicht unterstützt, wenn Sie ein kostenloses Azure-Angebot verwenden, das mit monetären Gutschriften verbunden ist.**

 - Die 3ds Max-Beispielszene für dieses Tutorial ist auf [GitHub](https://github.com/Azure/azure-docs-cli-python-samples/tree/master/batch/render-scene) zusammen mit einem Bash-Beispielskript und JSON-Konfigurationsdateien zu finden. Die 3ds Max-Szene stammt aus den [Autodesk 3ds Max-Beispieldateien](https://download.autodesk.com/us/support/files/3dsmax_sample_files/2017/Autodesk_3ds_Max_2017_English_Win_Samples_Files.exe). (Autodesk 3ds Max-Beispieldateien sind unter einer Creative Commons Attribution-NonCommercial-Share Alike-Lizenz verfügbar. Copyright &copy; Autodesk, Inc.)

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Für dieses Tutorial ist mindestens Version 2.0.20 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

> [!TIP]
> Sie können [Arnold-Auftragsvorlagen](https://github.com/Azure/batch-extension-templates/tree/master/templates/arnold/render-windows-frames) im GitHub-Repository mit Azure Batch-Erweiterungsvorlagen anzeigen.
## <a name="create-a-batch-account"></a>Erstellen eines Batch-Kontos

Erstellen Sie, falls nicht bereits geschehen, eine Ressourcengruppe, ein Batch-Konto und ein verknüpftes Speicherkonto in Ihrem Abonnement. 

Erstellen Sie mithilfe des Befehls [az group create](/cli/azure/group#az-group-create) eine Ressourcengruppe. Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen *myResourceGroup* am Standort *eastus2* erstellt.

```azurecli-interactive 
az group create \
    --name myResourceGroup \
    --location eastus2
```

Erstellen Sie mit dem Befehl [az storage account create](/cli/azure/storage/account#az-storage-account-create) in Ihrer Ressourcengruppe ein Azure Storage-Konto. Für dieses Tutorial verwenden Sie das Speicherkonto zum Speichern einer 3ds Max-Szene und der gerenderten Ausgabe.

```azurecli-interactive
az storage account create \
    --resource-group myResourceGroup \
    --name mystorageaccount \
    --location eastus2 \
    --sku Standard_LRS
```
Erstellen Sie mit dem Befehl [az batch account create](/cli/azure/batch/account#az-batch-account-create) ein Batch-Konto. Im folgenden Beispiel wird ein Batch-Konto mit dem Namen *mybatchaccount* in *myResourceGroup* erstellt, und das von Ihnen erstellte Speicherkonto wird verknüpft.  

```azurecli-interactive 
az batch account create \
    --name mybatchaccount \
    --storage-account mystorageaccount \
    --resource-group myResourceGroup \
    --location eastus2
```

Zum Erstellen und Verwalten von Computepools und -aufträgen müssen Sie die Authentifizierung für Batch durchführen. Melden Sie sich mit dem Befehl [az batch account login](/cli/azure/batch/account#az-batch-account-login) am Konto an. Nachdem Sie sich angemeldet haben, wird für Ihre `az batch`-Befehle dieser Kontokontext verwendet. Im folgenden Beispiel wird die Authentifizierung mit gemeinsam verwendetem Schlüssel basierend auf dem Batch-Kontonamen und -schlüssel verwendet. Batch unterstützt auch die Authentifizierung per [Azure Active Directory](batch-aad-auth.md), um einzelne Benutzer oder eine unbeaufsichtigte Anwendung zu authentifizieren.

```azurecli-interactive 
az batch account login \
    --name mybatchaccount \
    --resource-group myResourceGroup \
    --shared-key-auth
```
## <a name="upload-a-scene-to-storage"></a>Hochladen einer Szene in den Speicher

Zum Hochladen der Eingabeszene in den Speicher müssen Sie zuerst auf das Speicherkonto zugreifen und einen Zielcontainer für die Blobs erstellen. Exportieren Sie zum Zugreifen auf das Azure-Speicherkonto die Umgebungsvariablen `AZURE_STORAGE_KEY` und `AZURE_STORAGE_ACCOUNT`. Für den ersten Bash-Shellbefehl wird der Befehl [az storage account keys list](/cli/azure/storage/account/keys#az-storage-account-keys-list) verwendet, um den ersten Kontoschlüssel abzurufen. Nachdem Sie diese Umgebungsvariablen festgelegt haben, wird dieser Kontokontext für Ihre Speicherbefehle genutzt.

```azurecli-interactive
export AZURE_STORAGE_KEY=$(az storage account keys list --account-name mystorageaccount --resource-group myResourceGroup -o tsv --query [0].value)

export AZURE_STORAGE_ACCOUNT=mystorageaccount
```

Erstellen Sie im Speicherkonto für die Szenedateien jetzt einen Blobcontainer. Im folgenden Beispiel wird der Befehl [az storage container create](/cli/azure/storage/container#az-storage-container-create) verwendet, um einen Blobcontainer mit dem Namen *scenefiles* zu erstellen, der öffentlichen Lesezugriff ermöglicht.

```azurecli-interactive
az storage container create \
    --public-access blob \
    --name scenefiles
```

Laden Sie die Szene `MotionBlur-Dragon-Flying.max` von [GitHub](https://github.com/Azure/azure-docs-cli-python-samples/raw/master/batch/render-scene/MotionBlur-DragonFlying.max) in ein lokales Arbeitsverzeichnis herunter. Beispiel:

```azurecli-interactive
wget -O MotionBlur-DragonFlying.max https://github.com/Azure/azure-docs-cli-python-samples/raw/master/batch/render-scene/MotionBlur-DragonFlying.max
```

Laden Sie die Szenendatei aus Ihrem lokalen Arbeitsverzeichnis in den Blobcontainer hoch. Im folgenden Beispiel wird der Befehl [az storage blob upload-batch](/cli/azure/storage/blob#az-storage-blob-upload-batch) verwendet, mit dem mehrere Dateien hochgeladen werden können:

```azurecli-interactive
az storage blob upload-batch \
    --destination scenefiles \
    --source ./
```

## <a name="create-a-rendering-pool"></a>Erstellen eines Renderingpools

Erstellen Sie einen Batch-Pool für das Rendering, indem Sie den Befehl [az batch pool create](/cli/azure/batch/pool#az-batch-pool-create) verwenden. In diesem Beispiel geben Sie die Pooleinstellungen in einer JSON-Datei an. Erstellen Sie in Ihrer aktuellen Shell eine Datei mit dem Namen *mypool.json*, und fügen Sie dann den folgenden Inhalt ein. Achten Sie darauf, dass der Text richtig kopiert wird. (Sie können die Datei von [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-cli-python-samples/master/batch/render-scene/json/mypool.json) herunterladen.)


```json
{
  "id": "myrenderpool",
  "vmSize": "standard_d2_v2",
  "virtualMachineConfiguration": {
    "imageReference": {
      "publisher": "batch",
      "offer": "rendering-windows2016",
      "sku": "rendering",
      "version": "1.3.8"
    },
    "nodeAgentSKUId": "batch.node.windows amd64"
  },
  "targetDedicatedNodes": 0,
  "targetLowPriorityNodes": 1,
  "enableAutoScale": false,
  "applicationLicenses":[
         "3dsmax",
         "arnold"
      ],
  "enableInterNodeCommunication": false 
}
```
Batch unterstützt dedizierte Knoten und [Knoten mit niedriger Priorität](batch-low-pri-vms.md), und Sie können eine oder beide Arten von Knoten in Ihren Pools verwenden. Dedizierte Knoten sind für Ihren Pool reserviert. Knoten mit niedriger Priorität werden zu einem reduzierten Preis basierend auf überschüssiger VM-Kapazität in Azure angeboten. Knoten mit niedriger Priorität sind nicht mehr verfügbar, wenn in Azure nicht genügend Kapazität vorhanden ist. 

Der angegebene Pool enthält nur einen Knoten mit niedriger Priorität, auf dem ein Windows Server-Image mit Software für den Batch Rendering-Dienst ausgeführt wird. Dieser Pool ist für das Rendern mit 3ds Max und Arnold lizenziert. In einem späteren Schritt skalieren Sie den Pool auf eine größere Anzahl von Knoten.

Erstellen Sie den Pool, indem Sie die JSON-Datei mit dem Befehl `az batch pool create` übergeben:

```azurecli-interactive
az batch pool create \
    --json-file mypool.json
``` 
Das Bereitstellen des Pools dauert einige Minuten. Führen Sie den Befehl [az batch pool show](/cli/azure/batch/pool#az-batch-pool-show) aus, um den Status des Pools anzuzeigen. Mit dem folgenden Befehl wird der Zuweisungsstatus des Pools abgerufen:

```azurecli-interactive
az batch pool show \
    --pool-id myrenderpool \
    --query "allocationState"
```

Fahren Sie mit den folgenden Schritten fort, um einen Auftrag und Aufgaben zu erstellen, während sich der Poolstatus ändert. Der Pool wurde vollständig bereitgestellt, wenn der Zuweisungsstatus `steady` lautet und die Knoten ausgeführt werden.  

## <a name="create-a-blob-container-for-output"></a>Erstellen eines Blobcontainers für die Ausgabe

In den Beispielen dieses Tutorials wird für jede Aufgabe des Renderauftrags eine Ausgabedatei erstellt. Erstellen Sie vor dem Planen des Auftrags als Ziel für die Ausgabedateien einen Blobcontainer in Ihrem Speicherkonto. Im folgenden Beispiel wird der Befehl [az storage container create](/cli/azure/storage/container#az-storage-container-create) zum Erstellen des Containers *job-myrenderjob* mit öffentlichem Lesezugriff verwendet. 

```azurecli-interactive
az storage container create \
    --public-access blob \
    --name job-myrenderjob
```

Zum Schreiben von Ausgabedateien in den Container muss Batch ein SAS-Token (Shared Access Signature) verwenden. Erstellen Sie das Token mit dem Befehl [az storage account generate-sas](/cli/azure/storage/account#az-storage-account-generate-sas). In diesem Beispiel wird ein Token zum Schreiben in einen beliebigen Blobcontainer im Konto erstellt. Das Token läuft am 15. November 2020 ab:

```azurecli-interactive
az storage account generate-sas \
    --permissions w \
    --resource-types co \
    --services b \
    --expiry 2020-11-15
```

Notieren Sie sich das vom Befehl zurückgegebene Token. Es sieht in etwa wie folgt aus. Sie verwenden dieses Token in einem späteren Schritt.

```
se=2020-11-15&sp=rw&sv=2019-09-24&ss=b&srt=co&sig=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

## <a name="render-a-single-frame-scene"></a>Rendern einer Szene mit einem Frame

### <a name="create-a-job"></a>Erstellen eines Auftrags

Erstellen Sie einen Renderauftrag zum Ausführen im Pool, indem Sie den Befehl [az batch job create](/cli/azure/batch/job#az-batch-job-create) verwenden. Der Auftrag enthält ursprünglich keine Aufgaben.

```azurecli-interactive
az batch job create \
    --id myrenderjob \
    --pool-id myrenderpool
```

### <a name="create-a-task"></a>Erstellt eine Aufgabe.

Verwenden Sie den Befehl [az batch task create](/cli/azure/batch/task#az-batch-task-create), um im Auftrag eine Renderaufgabe zu erstellen. In diesem Beispiel geben Sie die Aufgabeneinstellungen in einer JSON-Datei an. Erstellen Sie in Ihrer aktuellen Shell eine Datei mit dem Namen *myrendertask.json*, und fügen Sie dann den folgenden Inhalt ein. Achten Sie darauf, dass der Text richtig kopiert wird. (Sie können die Datei von [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-cli-python-samples/master/batch/render-scene/json/myrendertask.json) herunterladen.)

In der Aufgabe wird ein 3ds Max-Befehl angegeben, um einen einzelnen Frame der Szene *MotionBlur-DragonFlying.max* zu rendern.

Ändern Sie die Elemente `blobSource` und `containerURL` in der JSON-Datei, damit sie den Namen Ihres Speicherkontos und Ihr SAS-Token enthalten. 

> [!TIP]
> Ihre `containerURL` endet auf Ihr SAS-Token und lautet in etwa wie folgt:
> 
> ```
> https://mystorageaccount.blob.core.windows.net/job-myrenderjob/$TaskOutput?se=2018-11-15&sp=rw&sv=2017-04-17&ss=b&srt=co&sig=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
> ```

```json
{
  "id": "myrendertask",
  "commandLine": "cmd /c \"%3DSMAX_2018%3dsmaxcmdio.exe -secure off -v:5 -rfw:0 -start:1 -end:1 -outputName:\"dragon.jpg\" -w 400 -h 300 MotionBlur-DragonFlying.max\"",
  "resourceFiles": [
    {
        "httpUrl": "https://mystorageaccount.blob.core.windows.net/scenefiles/MotionBlur-DragonFlying.max",
        "filePath": "MotionBlur-DragonFlying.max"
    }
  ],
    "outputFiles": [
        {
            "filePattern": "dragon*.jpg",
            "destination": {
                "container": {
                    "containerUrl": "https://mystorageaccount.blob.core.windows.net/job-myrenderjob/myrendertask/$TaskOutput?Add_Your_SAS_Token_Here"
                }
            },
            "uploadOptions": {
                "uploadCondition": "TaskSuccess"
            }
        }
    ],
  "userIdentity": {
    "autoUser": {
      "scope": "task",
      "elevationLevel": "nonAdmin"
    }
  }
}
```

Fügen Sie die Aufgabe mit dem folgenden Befehl dem Auftrag hinzu:

```azurecli-interactive
az batch task create \
    --job-id myrenderjob \
    --json-file myrendertask.json
```

Batch plant die Aufgabe, und die Aufgabe wird ausgeführt, sobald im Pool ein Knoten verfügbar ist.


### <a name="view-task-output"></a>Anzeigen der Aufgabenausgabe

Die Ausführung der Aufgabe dauert einige Minuten. Verwenden Sie den Befehl [az batch task show](/cli/azure/batch/task#az-batch-task-show), um Details zur Aufgabe anzuzeigen.

```azurecli-interactive
az batch task show \
    --job-id myrenderjob \
    --task-id myrendertask
```

Die Aufgabe generiert die Datei *dragon0001.jpg* auf dem Computeknoten und lädt sie in den Container *job-myrenderjob* in Ihrem Speicherkonto hoch. Laden Sie die Datei zum Anzeigen der Ausgabe aus dem Speicher auf Ihren lokalen Computer herunter, indem Sie den Befehl [az storage blob download](/cli/azure/storage/blob#az-storage-blob-download) verwenden.

```azurecli-interactive
az storage blob download \
    --container-name job-myrenderjob \
    --file dragon.jpg \
    --name dragon0001.jpg

```

Öffnen Sie *dragon.jpg* auf Ihrem Computer. Das gerenderte Bild sieht in etwa wie folgt aus:

![Gerenderter Drache, Frame 1](./media/tutorial-rendering-cli/dragon-frame.png) 


## <a name="scale-the-pool"></a>Skalieren des Pools

Ändern Sie nun den Pool, um ihn für einen größeren Renderauftrag mit mehreren Frames vorzubereiten. Batch verfügt über verschiedene Möglichkeiten zum Skalieren der Computeressourcen, z.B. die [automatische Skalierung](batch-automatic-scaling.md), bei der Knoten bei sich ändernden Aufgabenanforderungen hinzugefügt oder entfernt werden. Verwenden Sie für dieses einfache Beispiel den Befehl [az batch pool resize](/cli/azure/batch/pool#az-batch-pool-resize), um die Anzahl von Knoten mit niedriger Priorität im Pool auf *6* zu erhöhen:

```azurecli-interactive
az batch pool resize --pool-id myrenderpool --target-dedicated-nodes 0 --target-low-priority-nodes 6
```

Es dauert einige Minuten, um die Größe des Pools zu ändern. Richten Sie während des Ablaufs dieses Prozesses die nächsten Aufgaben ein, die für den vorhandenen Renderauftrag ausgeführt werden sollen.

## <a name="render-a-multiframe-scene"></a>Rendern einer Szene mit mehreren Frames

Verwenden Sie wie im Beispiel mit nur einem Frame den Befehl [az batch task create](/cli/azure/batch/task#az-batch-task-create), um Renderaufgaben im Auftrag *myrenderjob* zu erstellen. Geben Sie hier die Aufgabeneinstellungen in einer JSON-Datei mit dem Namen *myrendertask_multi.json* an. (Sie können die Datei von [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-cli-python-samples/master/batch/render-scene/json/myrendertask_multi.json) herunterladen.) Für die sechs Aufgaben wird jeweils eine Arnold-Befehlszeile zum Rendern eines Frames der 3ds Max-Szene *MotionBlur-DragonFlying.max* angegeben.

Erstellen Sie in Ihrer aktuellen Shell eine Datei mit dem Namen *myrendertask_multi.json*, und fügen Sie den Inhalt aus der heruntergeladenen Datei ein. Ändern Sie die Elemente `blobSource` und `containerURL` in der JSON-Datei, damit sie den Namen Ihres Speicherkontos und Ihr SAS-Token enthalten. Achten Sie darauf, die Einstellungen für alle sechs Aufgaben zu ändern. Speichern Sie die Datei, und führen Sie den folgenden Befehl aus, um die Aufgaben in die Warteschlange einzureihen:

```azurecli-interactive
az batch task create --job-id myrenderjob --json-file myrendertask_multi.json
```

### <a name="view-task-output"></a>Anzeigen der Aufgabenausgabe

Die Ausführung der Aufgabe dauert einige Minuten. Verwenden Sie den Befehl [az batch task list](/cli/azure/batch/task#az-batch-task-list), um den Status der Aufgaben anzuzeigen. Beispiel:

```azurecli-interactive
az batch task list \
    --job-id myrenderjob \
    --output table
```

Verwenden Sie den Befehl [az batch task show](/cli/azure/batch/task#az-batch-task-show), um Details zu den einzelnen Aufgaben anzuzeigen. Beispiel:

```azurecli-interactive
az batch task show \
    --job-id myrenderjob \
    --task-id mymultitask1
```
 
Die Aufgaben generieren die Ausgabedateien *dragon0002.jpg* - *dragon0007.jpg* auf den Computeknoten und laden sie in den Container *job-myrenderjob* in Ihrem Speicherkonto hoch. Laden Sie die Dateien zum Anzeigen der Ausgabe in einen Ordner auf Ihrem lokalen Computer herunter, indem Sie den Befehl [az storage blob download-batch](/cli/azure/storage/blob) verwenden. Beispiel:

```azurecli-interactive
az storage blob download-batch \
    --source job-myrenderjob \
    --destination .
```

Öffnen Sie eine der Dateien auf Ihrem Computer. Der gerenderte Frame 6 sieht in etwa wie folgt aus:

![Gerenderter Drache, Frame 6](./media/tutorial-rendering-cli/dragon-frame6.png) 


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Mit dem Befehl [az group delete](/cli/azure/group#az-group-delete) können Sie die Ressourcengruppe, das Batch-Konto, Pools und alle dazugehörigen Ressourcen entfernen, wenn sie nicht mehr benötigt werden. Löschen Sie die Ressourcen wie folgt:

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Hochladen von Szenen in Azure-Speicher
> * Erstellen eines Batch-Pools für das Rendering
> * Rendern einer Szene mit einem einzelnen Frame mit Arnold
> * Skalieren des Pools und Rendern einer Szene mit mehreren Frames
> * Herunterladen der gerenderten Ausgabe

Weitere Informationen zum Rendering auf Cloudebene finden Sie in den Optionen für den Batch Rendering-Dienst. 

> [!div class="nextstepaction"]
> [Batch Rendering-Dienst](batch-rendering-service.md)
