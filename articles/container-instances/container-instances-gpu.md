---
title: Bereitstellen GPU-fähiger Containerinstanzen
description: Erfahren Sie, wie Sie Azure-Containerinstanzen zur Ausführung rechenintensiver Container-Apps unter Verwendung von GPU-Ressourcen bereitstellen.
ms.topic: article
ms.date: 07/22/2020
ms.openlocfilehash: 0d645d1fce24d1324e485d74e20bcf492d4444a7
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93127007"
---
# <a name="deploy-container-instances-that-use-gpu-resources"></a>Bereitstellen von Containerinstanzen, die GPU-Ressourcen verwenden

Zum Ausführen bestimmter rechenintensiver Workloads auf Azure Container Instances stellen Sie Ihre [Containergruppen](container-instances-container-groups.md) mit *GPU-Ressourcen* bereit. Die Containerinstanzen in der Gruppe können auf eine oder mehrere NVIDIA-Tesla-GPUs zugreifen, während Containerworkloads wie CUDA und Deep Learning-Anwendungen ausgeführt werden.

In diesem Artikel wird veranschaulicht, wie Sie beim Bereitstellen einer Containergruppe GPU-Ressourcen mithilfe einer [YAML-Datei](container-instances-multi-container-yaml.md) oder [Resource Manager-Vorlage](container-instances-multi-container-group.md) hinzufügen. Sie können außerdem GPU-Ressourcen angeben, wenn Sie eine Containerinstanz über das Azure-Portal bereitstellen.

> [!IMPORTANT]
> Dieses Feature befindet sich derzeit in der Vorschauphase. Es gelten einige [Einschränkungen](#preview-limitations). Vorschauversionen werden Ihnen zur Verfügung gestellt, wenn Sie die [zusätzlichen Nutzungsbedingungen][terms-of-use] akzeptieren. Einige Aspekte dieses Features werden bis zur allgemeinen Verfügbarkeit unter Umständen noch geändert.

## <a name="preview-limitations"></a>Einschränkungen der Vorschau

In der Vorschau gelten die folgenden Einschränkungen beim Verwenden von GPU-Ressourcen in Containergruppen. 

[!INCLUDE [container-instances-gpu-regions](../../includes/container-instances-gpu-regions.md)]

Die Unterstützung für weitere Regionen wird im Lauf der Zeit hinzugefügt.

**Unterstützte Betriebssystemtypen** : Nur Linux

**Zusätzliche Einschränkungen** : GPU-Ressourcen können beim Bereitstellen einer Containergruppe in einem [virtuellen Netzwerk](container-instances-vnet.md) nicht verwendet werden.

## <a name="about-gpu-resources"></a>Informationen zu GPU-Ressourcen

### <a name="count-and-sku"></a>Anzahl und SKU

Geben Sie zum Verwenden von GPUs in einer Containerinstanz eine *GPU-Ressource* mit den folgenden Informationen an:

* **Anzahl** : Anzahl der GPUs: **1** , **2** oder **4**.
* **SKU** : GPU-SKU: **K80** , **P100** oder **V100**. Jede SKU wird der NVIDIA-Tesla-GPU in einer der folgenden GPU-fähigen Azure-VM-Familien zugeordnet:

  | SKU | VM-Familie |
  | --- | --- |
  | K80 | [NC](../virtual-machines/nc-series.md) |
  | P100 | [NCv2](../virtual-machines/ncv2-series.md) |
  | V100 | [NCv3](../virtual-machines/ncv3-series.md) |

[!INCLUDE [container-instances-gpu-limits](../../includes/container-instances-gpu-limits.md)]

Wenn Sie GPU-Ressourcen bereitstellen, legen Sie die für die Workload geeignete CPU und die Arbeitsspeicherressourcen bis zu den in der vorstehenden Tabelle gezeigten maximalen Werten fest. Diese Werte sind derzeit höher als die in Containergruppen ohne GPU-Ressourcen verfügbaren CPU- und Arbeitsspeicherressourcen.  

> [!IMPORTANT]
> Die standardmäßigen [Abonnementlimits](container-instances-quotas.md) (Kontingente) für GPU-Ressourcen unterscheiden sich zwischen SKUs. Die standardmäßigen CPU-Limits für die P100- und V100-SKUs sind anfänglich auf 0 festgelegt. Um eine Erhöhung in einer verfügbaren Region anzufordern, senden Sie eine [Azure-Supportanfrage][azure-support].

### <a name="things-to-know"></a>Wichtige Hinweise

* **Bereitstellungszeit** : Die Erstellung einer Containergruppe mit GPU-Ressourcen dauert **8 bis 10 Minuten**. Das wird durch den zusätzlichen Zeitaufwand für die Bereitstellung und Konfiguration einer GPU-VM in Azure verursacht. 

* **Preise** : Ähnlich wie bei Containergruppen ohne GPU-Ressourcen berechnet Azure die Ressourcen, die über die *Dauer* einer Containergruppe mit GPU-Ressourcen verbraucht wurden. Die Dauer wird ab dem Zeitpunkt, an dem das erste Image Ihres Containers abgerufen wird, bis zu dem Zeitpunkt berechnet, an dem die Containergruppe beendet wird. Die Zeit bis zum Bereitstellen der Containergruppe ist nicht enthalten.

  Preisdetails finden Sie [hier](https://azure.microsoft.com/pricing/details/container-instances/).

* **CUDA-Treiber** : Containerinstanzen mit GPU-Ressourcen werden vorab mit NVIDIA-CUDA-Treibern und Containerlaufzeiten bereitgestellt, damit Sie die für CUDA-Workloads entwickelten Containerimages verwenden können.

  Wir unterstützen in dieser Phase nur CUDA 9.0. Beispielsweise können Sie die folgenden Basisimages für Ihre Docker-Datei verwenden:
  * [nvidia/cuda:9.0-base-ubuntu16.04](https://hub.docker.com/r/nvidia/cuda/)
  * [tensorflow/tensorflow: 1.12.0-gpu-py3](https://hub.docker.com/r/tensorflow/tensorflow)
    
## <a name="yaml-example"></a>Beispiel mit YAML-Datei

Eine Möglichkeit zum Hinzufügen von GPU-Ressourcen besteht darin, eine Containergruppe unter Verwendung einer [YAML-Datei](container-instances-multi-container-yaml.md) bereitzustellen. Kopieren Sie den folgenden YAML-Code in eine neue Datei namens *gpu-deploy-aci.yaml*. Mit diesem YAML-Code wird eine Containergruppe namens *gpucontainergroup* erstellt, die eine Containerinstanz mit einer K80-GPU angibt. Die Instanz führt eine Beispielanwendung für eine Vektoraddition in CUDA aus. Die Ressourcenanforderungen reichen zum Ausführen der Workload aus.

```YAML
additional_properties: {}
apiVersion: '2019-12-01'
name: gpucontainergroup
properties:
  containers:
  - name: gpucontainer
    properties:
      image: k8s-gcrio.azureedge.net/cuda-vector-add:v0.1
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
          gpu:
            count: 1
            sku: K80
  osType: Linux
  restartPolicy: OnFailure
```

Stellen Sie die Containergruppe mithilfe des Befehls [az container create][az-container-create] bereit, und geben Sie dabei für den Parameter `--file` den YAML-Dateinamen an. Sie müssen den Namen einer Ressourcengruppe und einen Speicherort für die Containergruppe (z. B. *eastus* ) angeben, der GPU-Ressourcen unterstützt.  

```azurecli
az container create --resource-group myResourceGroup --file gpu-deploy-aci.yaml --location eastus
```

Die Bereitstellung kann einige Minuten in Anspruch nehmen. Dann wird der Container gestartet, und in Cuda wird ein Vektoradditionsvorgang ausgeführt. Führen Sie den Befehl [az container logs][az-container-logs] aus, um die Protokollausgabe anzuzeigen:

```azurecli
az container logs --resource-group myResourceGroup --name gpucontainergroup --container-name gpucontainer
```

Ausgabe:

```Console
[Vector addition of 50000 elements]
Copy input data from the host memory to the CUDA device
CUDA kernel launch with 196 blocks of 256 threads
Copy output data from the CUDA device to the host memory
Test PASSED
Done
```

## <a name="resource-manager-template-example"></a>Beispiel mit Resource Manager-Vorlage

Eine weitere Möglichkeit, eine Containergruppe mit GPU-Ressourcen bereitzustellen, ist die Verwendung einer [Resource Manager-Vorlage](container-instances-multi-container-group.md). Erstellen Sie zunächst eine Datei mit dem Namen `gpudeploy.json`, und fügen Sie dann den folgende JSON-Code ein. In diesem Beispiel wird eine Containerinstanz mit einer V100-GPU bereitgestellt, die einen [TensorFlow](https://www.tensorflow.org/)-Trainingsauftrag für die MNIST-Datenbank ausführt. Die Ressourcenanforderungen reichen zum Ausführen der Workload aus.

```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "containerGroupName": {
        "type": "string",
        "defaultValue": "gpucontainergrouprm",
        "metadata": {
          "description": "Container Group name."
        }
      }
    },
    "variables": {
      "containername": "gpucontainer",
      "containerimage": "mcr.microsoft.com/azuredocs/samples-tf-mnist-demo:gpu"
    },
    "resources": [
      {
        "name": "[parameters('containerGroupName')]",
        "type": "Microsoft.ContainerInstance/containerGroups",
        "apiVersion": "2019-12-01",
        "location": "[resourceGroup().location]",
        "properties": {
            "containers": [
            {
              "name": "[variables('containername')]",
              "properties": {
                "image": "[variables('containerimage')]",
                "resources": {
                  "requests": {
                    "cpu": 4.0,
                    "memoryInGb": 12.0,
                    "gpu": {
                        "count": 1,
                        "sku": "V100"
                  }
                }
              }
            }
          }
        ],
        "osType": "Linux",
        "restartPolicy": "OnFailure"
        }
      }
    ]
}
```

Stellen Sie die Vorlage mit dem Befehl [az deployment group create][az-deployment-group-create] bereit. Sie müssen den Namen einer Ressourcengruppe angeben, die in einer Region (z. B. *eastus* ) erstellt wurde, die GPU-Ressourcen unterstützt.

```azurecli-interactive
az deployment group create --resource-group myResourceGroup --template-file gpudeploy.json
```

Die Bereitstellung kann einige Minuten in Anspruch nehmen. Dann wird der Container gestartet, und der TensorFlow-Auftrag wird ausgeführt. Führen Sie den Befehl [az container logs][az-container-logs] aus, um die Protokollausgabe anzuzeigen:

```azurecli
az container logs --resource-group myResourceGroup --name gpucontainergrouprm --container-name gpucontainer
```

Ausgabe:

```Console
2018-10-25 18:31:10.155010: I tensorflow/core/platform/cpu_feature_guard.cc:137] Your CPU supports instructions that this TensorFlow binary was not compiled to use: SSE4.1 SSE4.2 AVX AVX2 FMA
2018-10-25 18:31:10.305937: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1030] Found device 0 with properties:
name: Tesla K80 major: 3 minor: 7 memoryClockRate(GHz): 0.8235
pciBusID: ccb6:00:00.0
totalMemory: 11.92GiB freeMemory: 11.85GiB
2018-10-25 18:31:10.305981: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1120] Creating TensorFlow device (/device:GPU:0) -> (device: 0, name: Tesla K80, pci bus id: ccb6:00:00.0, compute capability: 3.7)
2018-10-25 18:31:14.941723: I tensorflow/stream_executor/dso_loader.cc:139] successfully opened CUDA library libcupti.so.8.0 locally
Successfully downloaded train-images-idx3-ubyte.gz 9912422 bytes.
Extracting /tmp/tensorflow/input_data/train-images-idx3-ubyte.gz
Successfully downloaded train-labels-idx1-ubyte.gz 28881 bytes.
Extracting /tmp/tensorflow/input_data/train-labels-idx1-ubyte.gz
Successfully downloaded t10k-images-idx3-ubyte.gz 1648877 bytes.
Extracting /tmp/tensorflow/input_data/t10k-images-idx3-ubyte.gz
Successfully downloaded t10k-labels-idx1-ubyte.gz 4542 bytes.
Extracting /tmp/tensorflow/input_data/t10k-labels-idx1-ubyte.gz
Accuracy at step 0: 0.097
Accuracy at step 10: 0.6993
Accuracy at step 20: 0.8208
Accuracy at step 30: 0.8594
...
Accuracy at step 990: 0.969
Adding run metadata for 999
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Da die Verwendung von GPU-Ressourcen teuer sein kann, stellen Sie sicher, dass Ihre Container nicht unbeabsichtigt über einen längeren Zeitraum ausgeführt werden. Überwachen Sie Ihre Container im Azure-Portal, oder überprüfen Sie den Status einer Containergruppe mithilfe des Befehls [az container show][az-container-show]. Beispiel:

```azurecli
az container show --resource-group myResourceGroup --name gpucontainergroup --output table
```

Wenn Sie die von Ihnen erstellten Containerinstanzen nicht mehr benötigen, löschen Sie sie mit den folgenden Befehlen:

```azurecli
az container delete --resource-group myResourceGroup --name gpucontainergroup -y
az container delete --resource-group myResourceGroup --name gpucontainergrouprm -y
```

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über das Bereitstellen einer Containergruppe mit einer [YAML-Datei](container-instances-multi-container-yaml.md) oder einer [Resource Manager-Vorlage](container-instances-multi-container-group.md).
* Erfahren Sie mehr über [GPU-optimierte VM-Größen](../virtual-machines/sizes-gpu.md) in Azure.


<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-vnet/aci-vnet-01.png

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create
