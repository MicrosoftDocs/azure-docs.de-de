---
title: Bereitstellen eines virtuellen Linux-Computers mit der Azure-REST-API | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie einen virtuellen Linux-Computer in Azure erstellen, der verwaltete Datenträger und SSH-Authentifizierung mit der Azure-REST-API verwendet.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/05/2018
ms.author: cynthn
ms.openlocfilehash: 2b078cd769a9b4e5e66fe132fd4ef73ec4621efc
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60391360"
---
# <a name="create-a-linux-virtual-machine-that-uses-ssh-authentication-with-the-rest-api"></a>Erstellen eines virtuellen Linux-Computers zur Verwendung der SSH-Authentifizierung mit der REST-API

Ein virtueller Linux-Computer (VM) in Azure besteht aus verschiedenen Ressourcen wie Datenträgern und Netzwerkschnittstellen und definiert Parameter wie Speicherort, Größe und Betriebssystemimage sowie Authentifizierungseinstellungen.

Sie können eine Linux-VM über das Azure-Portal, mit Azure CLI 2.0, vielen Azure SDKs, den Azure Resource Manager-Vorlagen und vielen Drittanbietertools wie Ansible oder Terraform erstellen. All diese Tools verwenden letztlich die REST-API, um die Linux-VM zu erstellen.

In diesem Artikel erfahren Sie, wie Sie mithilfe der REST-API einen virtuellen Linux-Computer erstellen, der Ubuntu 18.04-LTS mit verwalteten Datenträgern und SSH-Authentifizierung ausführt.

## <a name="before-you-start"></a>Vorbereitung

Bevor Sie die Anforderung erstellen und übermitteln, benötigen Sie Folgendes:

* Die `{subscription-id}` für Ihr Abonnement
  * Wenn Sie mehrere Abonnements haben, lesen Sie [Arbeiten mit mehreren Abonnements](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest).
* Einen `{resourceGroupName}`, die Sie vorher erstellt haben
* Eine [virtuelle Netzwerkschnittstelle](../../virtual-network/virtual-network-network-interface.md) in derselben Ressourcengruppe
* Ein SSH-Schlüsselpaar (Sie können [ein neues generieren](mac-create-ssh-keys.md), wenn Sie noch keins haben)

## <a name="request-basics"></a>Anforderungsgrundlagen

Verwenden Sie zum Erstellen oder Aktualisieren eines virtuellen Computers den folgenden *PUT*-Vorgang:

``` http
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}?api-version=2017-12-01
```

Zusätzlich zu den Parametern `{subscription-id}` und `{resourceGroupName}` müssen Sie den `{vmName}` angeben (`api-version` ist optional, aber dieser Artikel wurde mit `api-version=2017-12-01` getestet)

Die folgenden Header sind erforderlich:

| Anforderungsheader   | BESCHREIBUNG |
|------------------|-----------------|
| *Content-Type:*  | Erforderlich. Legen Sie diese Option auf `application/json` fest. |
| *Authorization:* | Erforderlich. Legen Sie diese Option auf ein gültiges `Bearer` [Zugriffstoken](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients) fest. |

Allgemeine Informationen zum Arbeiten mit REST-API-Anforderung finden Sie unter [Komponenten einer REST-API-Anforderung/Antwort](/rest/api/azure/#components-of-a-rest-api-requestresponse).

## <a name="create-the-request-body"></a>Erstellen des Anforderungstexts

Die folgenden allgemeinen Definitionen werden verwendet, um einen Anforderungstext zu erstellen:

| NAME                       | Erforderlich | Typ                                                                                | BESCHREIBUNG  |
|----------------------------|----------|-------------------------------------------------------------------------------------|--------------|
| location                   | True     | Zeichenfolge                                                                              | Ressourcenspeicherort |
| name                       |          | Zeichenfolge                                                                              | Name des virtuellen Computers |
| properties.hardwareProfile |          | [HardwareProfile](/rest/api/compute/virtualmachines/createorupdate#hardwareprofile) | Legt die Hardwareeinstellungen für den virtuellen Computer fest. |
| properties.storageProfile  |          | [StorageProfile](/rest/api/compute/virtualmachines/createorupdate#storageprofile)   | Legt die Speichereinstellungen für die Datenträger des virtuellen Computers fest. |
| properties.osProfile       |          | [OSProfile](/rest/api/compute/virtualmachines/createorupdate#osprofile)             | Legt die Einstellungen des Betriebssystems für den virtuellen Computer fest. |
| properties.networkProfile  |          | [NetworkProfile](/rest/api/compute/virtualmachines/createorupdate#networkprofile)   | Legt die Netzwerkschnittstellen des virtuellen Computers fest. |

Unten sehen Sie ein Beispiel für einen Anforderungstext. Stellen Sie sicher, dass Sie den VM-Namen in den Parametern `{computerName}` und `{name}` angeben, den Namen der Netzwerkschnittstelle, die Sie erstellt haben, unter `networkInterfaces`, Ihren Benutzernamen in `adminUsername` und `path` sowie den *öffentlichen* Teil Ihres SSH-Schlüsselpaars (befindet sich z. B. in `~/.ssh/id_rsa.pub`) in `keyData`. Weitere Parameter, die Sie eventuell ändern sollten, umfassen `location` und `vmSize`.  

```json
{
  "location": "eastus",
  "name": "{vmName}",
  "properties": {
    "hardwareProfile": {
      "vmSize": "Standard_DS1_v2"
    },
    "storageProfile": {
      "imageReference": {
        "sku": "18.04-LTS",
        "publisher": "Canonical",
        "version": "latest",
        "offer": "UbuntuServer"
      },
      "osDisk": {
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Premium_LRS"
        },
        "name": "myVMosdisk",
        "createOption": "FromImage"
      }
    },
    "osProfile": {
      "adminUsername": "{your-username}",
      "computerName": "{vmName}",
      "linuxConfiguration": {
        "ssh": {
          "publicKeys": [
            {
              "path": "/home/{your-username}/.ssh/authorized_keys",
              "keyData": "ssh-rsa AAAAB3NzaC1{snip}mf69/J1"
            }
          ]
        },
        "disablePasswordAuthentication": true
      }
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/{subscription-id}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{existing-nic-name}",
          "properties": {
            "primary": true
          }
        }
      ]
    }
  }
}
```

Eine vollständige Liste der verfügbaren Definitionen im Anforderungstext finden Sie in den [Definitionen zum Erstellen oder Aktualisieren eines Anforderungstexts für virtuelle Computer](/rest/api/compute/virtualmachines/createorupdate#definitions).

## <a name="sending-the-request"></a>Absenden der Anforderung

Sie können den von Ihnen bevorzugten Client zum Senden dieser HTTP-Anforderung verwenden. Sie können außerdem ein [In-Browser-Tool](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate) verwenden, indem Sie auf die Schaltfläche **Testen** klicken.

### <a name="responses"></a>Antworten

Es gibt zwei erfolgreiche Antworten für den Vorgang, um einen virtuellen Computer zu erstellen oder zu aktualisieren:

| NAME        | Type                                                                              | BESCHREIBUNG |
|-------------|-----------------------------------------------------------------------------------|-------------|
| 200 – OK      | [VirtualMachine](/rest/api/compute/virtualmachines/createorupdate#virtualmachine) | OK          |
| 201 – Erstellt | [VirtualMachine](/rest/api/compute/virtualmachines/createorupdate#virtualmachine) | Erstellt     |

Eine komprimierte *201 – Erstellt*-Antwort aus dem vorherigen Beispielanforderungstext, der eine VM erstellt, zeigt, dass eine *vmId* zugewiesen wurde und *ProvisioningState* *Wird erstellt* ist:

```json
{
    "vmId": "e0de9b84-a506-4b95-9623-00a425d05c90",
    "provisioningState": "Creating"
}
```

Weitere Informationen zu REST-API-Antworten finden Sie unter [Verarbeiten der Antwortnachricht](/rest/api/azure/#process-the-response-message).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu den Azure-REST-APIs oder anderen Verwaltungstools, z.B. Azure CLI oder Azure PowerShell, finden Sie hier:

- [Azure Compute-Anbieter-REST-API](/rest/api/compute/)
- [Erste Schritte mit der Azure-REST-API](/rest/api/azure/)
- [Azure-Befehlszeilenschnittstelle](/cli/azure/)
- [Azure PowerShell-Modul](/powershell/azure/overview)
