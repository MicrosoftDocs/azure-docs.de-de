---
title: Bereitstellen von OKD in Azure | Microsoft-Dokumentation
description: Bereitstellen von OKD in Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: joraio
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/02/2019
ms.author: haroldw
ms.openlocfilehash: 7db50007dd32c84a360eaec25bf860709272437b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60542508"
---
# <a name="deploy-okd-in-azure"></a>Bereitstellen von OKD in Azure

Sie können eine von zwei Methoden verwenden, um OKD (vormals OpenShift Origin) in Azure bereitzustellen:

- Sie können manuell alle erforderlichen Azure-Infrastrukturkomponenten bereitstellen und dann den Anweisungen in der [Dokumentation zu OKD](https://docs.okd.io) folgen.
- Sie können auch eine vorhandene [Resource Manager-Vorlage](https://github.com/Microsoft/openshift-origin) verwenden, die die Bereitstellung des OKD-Clusters vereinfacht.

## <a name="deploy-using-the-okd-template"></a>Bereitstellung mithilfe der OKD-Vorlage

Bei der Bereitstellung mithilfe der Resource Manager-Vorlage werden die Eingabeparameter über eine Parameterdatei angegeben. Forken Sie zur weiteren Anpassung der Bereitstellung das GitHub-Repository, und ändern Sie die entsprechenden Elemente.

Im Folgenden finden Sie eine Auswahl allgemeiner Optionen, die Sie anpassen können:

- Größe der geschützten VM (Variable in „azuredeploy.json“)
- Namenskonvention (Variable in „azuredeploy.json“)
- OpenShift-Clusterbesonderheiten, angepasst über „hosts“-Datei („deployOpenShift.sh“)

Die [OKD-Vorlage](https://github.com/Microsoft/openshift-origin) stellt mehrere Branches für verschiedene Versionen von OKD zur Verfügung.  Die Bereitstellung kann direkt über das Repository erfolgen. Bei Bedarf können Sie aber auch das Repository forken und vor der Bereitstellung Änderungen vornehmen.

Verwenden Sie den Wert `appId` des zuvor erstellten Dienstprinzipals für den `aadClientId`-Parameter.

Im Folgenden sehen Sie ein Beispiel einer Parameterdatei namens „azuredeploy.parameters.json“ mit allen erforderlichen Eingaben.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "masterVmSize": {
            "value": "Standard_E2s_v3"
        },
        "infraVmSize": {
            "value": "Standard_E2s_v3"
        },
        "nodeVmSize": {
            "value": "Standard_E2s_v3"
        },
        "storageKind": {
            "value": "managed"
        },
        "openshiftClusterPrefix": {
            "value": "mycluster"
        },
        "masterInstanceCount": {
            "value": 3
        },
        "infraInstanceCount": {
            "value": 2
        },
        "nodeInstanceCount": {
            "value": 2
        },
        "dataDiskSize": {
            "value": 128
        },
        "adminUsername": {
            "value": "clusteradmin"
        },
        "openshiftPassword": {
            "value": "{Strong Password}"
        },
        "sshPublicKey": {
            "value": "{SSH Public Key}"
        },
        "enableMetrics": {
            "value": "true"
        },
        "enableLogging": {
            "value": "false"
        },
        "keyVaultResourceGroup": {
            "value": "keyvaultrg"
        },
        "keyVaultName": {
            "value": "keyvault"
        },
        "keyVaultSecret": {
            "value": "keysecret"
        },
        "enableAzure": {
            "value": "true"
        },
        "aadClientId": {
            "value": "11111111-abcd-1234-efgh-111111111111"
        },
        "aadClientSecret": {
            "value": "{Strong Password}"
        },
        "defaultSubDomainType": {
            "value": "nipio"
        }
    }
}
```

Ersetzen Sie die Parameter durch Ihre eigenen Informationen.

Die Parameter können sich je nach Release unterschieden. Überprüfen Sie daher die erforderlichen Parameter für den verwendeten Branch.

### <a name="deploy-using-azure-cli"></a>Bereitstellen über die Azure-Befehlszeilenschnittstelle


> [!NOTE] 
> Der folgende Befehl erfordert Azure CLI 2.0.8 oder höher. Sie können die CLI-Version mit dem `az --version`-Befehl überprüfen. Informationen zum Aktualisieren der CLI-Version finden Sie unter [Installieren der Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

Das folgende Beispiel stellt den OKD-Cluster und alle zugehörigen Ressourcen in der Ressourcengruppe „openshiftrg“ mit dem Bereitstellungsnamen „myOpenShiftCluster“ bereit. Auf die Vorlage wird direkt aus dem GitHub-Repository verwiesen. Dazu wird eine lokale Parameterdatei namens „azuredeploy.parameters.json“ verwendet.

```azurecli 
az group deployment create -g openshiftrg --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-origin/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

Die Bereitstellung dauert mindestens 30 Minuten – die Dauer hängt von der Gesamtzahl der bereitgestellten Knoten ab. Die URL der OpenShift-Konsole und der DNS-Name des OpenShift-Masters werden am Terminal ausgegeben, wenn die Bereitstellung abgeschlossen ist. Alternativ dazu können Sie den Ausgabeabschnitt der Bereitstellung im Azure-Portal anzeigen.

```json
{
  "OpenShift Console Url": "http://openshiftlb.cloudapp.azure.com/console",
  "OpenShift Master SSH": "ssh -p 2200 clusteradmin@myopenshiftmaster.cloudapp.azure.com"
}
```

Wenn Sie die Befehlszeile nicht blockieren möchten, bis die Bereitstellung abgeschlossen ist, fügen Sie `--no-wait` als eine der Optionen für die Gruppenbereitstellung hinzu. Die Ausgabe der Bereitstellung kann über das Azure-Portal im Bereitstellungsabschnitt für die Ressourcengruppe abgerufen werden.

## <a name="connect-to-the-okd-cluster"></a>Verbindungsherstellung mit dem OKD-Cluster

Wenn die Bereitstellung abgeschlossen ist, stellen Sie mithilfe von `OpenShift Console Url` im Browser eine Verbindung mit der OpenShift-Konsole her. Alternativ dazu können Sie auch eine SSH-Verbindung mit dem OKD-Master herstellen. Im Folgenden finden Sie ein Beispiel, das die Ausgabe der Bereitstellung verwendet:

```bash
$ ssh -p 2200 clusteradmin@myopenshiftmaster.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Ressourcengruppe, OpenShift-Cluster und alle zugehörigen Ressourcen nicht mehr benötigt werden, können Sie sie mit dem Befehl [az group delete](/cli/azure/group) entfernen.

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>Nächste Schritte

- [Aufgaben nach der Bereitstellung](./openshift-post-deployment.md)
- [Beheben von Problemen bei der Bereitstellung von OpenShift](./openshift-troubleshooting.md)
- [Erste Schritte mit OKD](https://docs.okd.io)
