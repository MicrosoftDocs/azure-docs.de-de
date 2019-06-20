---
title: Erstellen und Verwalten virtueller Computer in DevTest Labs mit der Azure-Befehlszeilenschnittstelle | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mit Azure DevTest Labs virtuelle Computer mit der Azure CLI erstellen und verwalten.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2019
ms.author: spelluru
ms.openlocfilehash: 48a30ef86cdb10b540ffe1231294542ccff87255
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60622319"
---
# <a name="create-and-manage-virtual-machines-with-devtest-labs-using-the-azure-cli"></a>Erstellen und Verwalten virtueller Computer in DevTest Labs mit der Azure-Befehlszeilenschnittstelle
Dieser Schnellstart führt Sie durch das Erstellen, Starten, Verbinden, Aktualisieren und Bereinigen von Entwicklungscomputern im Lab. 

Vorbereitungen

* Falls Sie noch kein Lab erstellt haben, finden Sie [hier](devtest-lab-create-lab.md) Anweisungen dazu.

* [Installieren Sie die Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli). Führen Sie zum Starten „az login“ aus, um eine Verbindung mit Azure herzustellen. 

## <a name="create-and-verify-the-virtual-machine"></a>Erstellen und Überprüfen von virtuellen Computern 
Bevor Sie DevTest Labs-bezogene Befehle ausführen, legen Sie den entsprechenden Azure-Kontext mit dem Befehl `az account set` fest:

```azurecli
az account set --subscription 11111111-1111-1111-1111-111111111111
```

Der Befehl zum Erstellen eines virtuellen Computers lautet: `az lab vm create`. Die Ressourcengruppe für das Labs, den der Name des Labs und den Namen des virtuellen Computers sind alle erforderlich. Der Rest der Argumente ändert sich je nach Typ des virtuellen Computers.

Der folgende Befehl erstellt ein Windows-basiertes Image von Azure Market Place. Der Name des Images ist derselbe, der beim Erstellen eines virtuellen Computers über das Azure-Portal angezeigt wird. 

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "Visual Studio Community 2017 on Windows Server 2016 (x64)" --image-type gallery --size 'Standard_D2s_v3’ --admin-username 'AdminUser' --admin-password 'Password1!'
```

Der folgende Befehl erstellt einen virtuellen Computer basierend auf einem benutzerdefinierten Image, das im Lab verfügbar ist:

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "My Custom Image" --image-type custom --size 'Standard_D2s_v3' --admin-username 'AdminUser' --admin-password 'Password1!'
```

Das Argument **image-type** wurde von **gallery** auf **custom** geändert. Der Name des Images entspricht dem, was angezeigt wird, wenn Sie den virtuellen Computer im Azure-Portal erstellen.

Mit dem folgenden Befehl erstellen Sie einen virtuellen Computer aus einem Marketplace-Image mit SSH-Authentifizierung:

```azurecli
az lab vm create --lab-name sampleLabName --resource-group sampleLabResourceGroup --name sampleVMName --image "Ubuntu Server 16.04 LTS" --image-type gallery --size Standard_DS1_v2 --authentication-type  ssh --generate-ssh-keys --ip-configuration public 
```

Sie können auch virtuelle Computer basierend auf Formeln erstellen, indem Sie den Parameter **image-type** auf **formula** festlegen. Wenn Sie ein bestimmtes virtuelles Netzwerk für Ihren virtuellen Computer auswählen müssen, verwenden Sie die Parameter **vnet-name** und **subnet**. Weitere Informationen finden Sie unter [az lab vm create](/cli/azure/lab/vm#az-lab-vm-create).

## <a name="verify-that-the-vm-is-available"></a>Überprüfen Sie, ob der virtuelle Computer verfügbar ist.
Verwenden Sie den Befehl `az lab vm show`, um sicherzustellen, dass die VM verfügbar ist, bevor Sie sie starten und sich mit ihr verbinden. 

```azurecli
az lab vm show --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup --expand 'properties($expand=ComputeVm,NetworkInterface)' --query '{status: computeVm.statuses[0].displayStatus, fqdn: fqdn, ipAddress: networkInterface.publicIpAddress}'
```
```json
{
  "fqdn": "lisalabvm.southcentralus.cloudapp.azure.com",
  "ipAddress": "13.85.228.112",
  "status": "Provisioning succeeded"
}
```

## <a name="start-and-connect-to-the-virtual-machine"></a>Starten und Verbinden virtueller Computer
Der folgende Beispielbefehl startet eine VM:

```azurecli
az lab vm start --lab-name sampleLabName --name sampleVMName --resource-group sampleLabResourceGroup
```

Herstellen einer Verbindung mit einer VM: [SSH](../virtual-machines/linux/mac-create-ssh-keys.md) oder [Remotedesktop](../virtual-machines/windows/connect-logon.md).
```bash
ssh userName@ipAddressOrfqdn 
```

## <a name="update-the-virtual-machine"></a>Aktualisieren virtueller Computer
Der folgende Beispielbefehl wendet Artefakte auf eine VM an:

```azurecli
az lab vm apply-artifacts --lab-name  sampleLabName --name sampleVMName  --resource-group sampleResourceGroup  --artifacts @/artifacts.json
```

```json
[
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-java",
    "parameters": []
  },
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-install-nodejs",
    "parameters": []
  },
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-apt-package",
    "parameters": [
      {
        "name": "packages",
        "value": "abcd"
      },
      {
        "name": "update",
        "value": "true"
      },
      {
        "name": "options",
        "value": ""
      }
    ]
  } 
]
```

Listen Sie die Artefakte im Lab auf.
```azurecli
az lab vm show --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup --expand "properties(\$expand=artifacts)" --query 'artifacts[].{artifactId: artifactId, status: status}'
```
```json
{
  "artifactId": "/subscriptions/abcdeftgh1213123/resourceGroups/lisalab123RG822645/providers/Microsoft.DevTestLab/labs/lisalab123/artifactSources/public repo/artifacts/linux-install-nodejs",
  "status": "Succeeded"
}
```

## <a name="stop-and-delete-the-virtual-machine"></a>Beenden und Löschen virtueller Computer    
Der folgende Beispielbefehl stoppt eine VM.

```azurecli
az lab vm stop --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

Löschen Sie einen virtuellen Computer.
```azurecli
az lab vm delete --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

## <a name="next-steps"></a>Nächste Schritte
Lesen Sie den folgenden Inhalt: [Azure CLI-Dokumentation für Azure DevTest Labs](/cli/azure/lab?view=azure-cli-latest). 