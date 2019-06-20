---
title: Erstellen einer Linux-VM in Azure mithilfe einer Vorlage | Microsoft-Dokumentation
description: Verwenden von Azure CLI zum Erstellen einer Linux-VM aus einer Ressourcen-Manager-Vorlage
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 721b8378-9e47-411e-842c-ec3276d3256a
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 03/22/2019
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 334f69390e4506c6db76c1814f8ec8f1e4417ee9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60327999"
---
# <a name="how-to-create-a-linux-virtual-machine-with-azure-resource-manager-templates"></a>Erstellen eines virtuellen Linux-Computers mithilfe von Azure Resource Manager-Vorlagen

Es wird beschrieben, wie Sie einen virtuellen Linux-Computer (VM) mit einer Azure Resource Manager-Vorlage und mit der Azure CLI über die Azure Cloud Shell erstellen. Informationen zum Erstellen eines virtuellen Windows-Computers finden Sie unter [Erstellen eines virtuellen Windows-Computer mit einer Resource Manager-Vorlage](../windows/ps-template.md).

## <a name="templates-overview"></a>Übersicht über Vorlagen

Azure Resource Manager-Vorlagen sind JSON-Dateien, die die Infrastruktur und Konfiguration Ihrer Azure-Lösung definieren. Mit einer Vorlage können Sie die Lösung während ihres Lebenszyklus wiederholt bereitstellen und sicher sein, dass Ihre Ressourcen einheitlich bereitgestellt werden. Weitere Informationen zum Format der Vorlage und zu ihrer Erstellung finden Sie unter [Schnellstart: Erstellen und Bereitstellen von Azure Resource Manager-Vorlagen über das Azure-Portal](../../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md). Die JSON-Syntax für Ressourcentypen finden Sie unter [Define resources in Azure Resource Manager templates](/azure/templates/microsoft.compute/allversions) (Definieren von Ressourcen in Azure Resource Manager-Vorlagen).

## <a name="create-a-virtual-machine"></a>Erstellen eines virtuellen Computers

Die Erstellung eines virtuellen Azure-Computers umfasst normalerweise zwei Schritte:

1. Erstellen Sie eine Ressourcengruppe. Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden. Vor dem virtuellen Computer muss eine Ressourcengruppe erstellt werden.
1. Erstellen Sie eine VM.

Im folgenden Beispiel wird eine VM über eine [Azure-Schnellstartvorlage](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json) erstellt. Für diese Bereitstellung ist nur SSH-Authentifizierung zulässig. Geben Sie bei Aufforderung den Wert Ihres eigenen öffentlichen SSH-Schlüssels ein, etwa den Inhalt von *~/.ssh/id_rsa.pub*. Informationen zum Erstellen eines SSH-Schlüsselpaars finden Sie unter [Erstellen und Verwenden eines SSH-Schlüsselpaars für Linux-VMs in Azure](mac-create-ssh-keys.md). Hier ist eine Kopie der Vorlage angegeben:

[!code-json[create-linux-vm](~/quickstart-templates/101-vm-sshkey/azuredeploy.json)]

Wählen Sie zum Ausführen des CLI-Skripts **Testen Sie es.** aus, um die Azure Cloud Shell zu öffnen. Klicken Sie zum Einfügen des Skripts mit der rechten Maustaste auf die Shell, und wählen Sie **Einfügen** aus:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
echo "Enter the project name (used for generating resource names):" &&
read projectName &&
echo "Enter the administrator username:" &&
read username &&
echo "Enter the SSH public key:" &&
read key &&
az group create --name $resourceGroupName --location "$location" &&
az group deployment create --resource-group $resourceGroupName --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json --parameters projectName=$projectName adminUsername=$username adminPublicKey='$key' &&
az vm show --resource-group $resourceGroupName --name "$projectName-vm" --show-details --query publicIps --output tsv
```

Der letzte Azure CLI-Befehl zeigt die öffentliche IP-Adresse der neu erstellten VM. Sie benötigen die öffentliche IP-Adresse, um eine Verbindung mit dem virtuellen Computer herzustellen. Informationen dazu finden Sie im nächsten Abschnitt dieses Artikels.

Im vorherigen Beispiel haben Sie eine in GitHub gespeicherte Vorlage angegeben. Sie können eine Vorlage außerdem herunterladen oder erstellen und den lokalen Pfad mit dem Parameter `--template-file` angeben.

Hier sind einige zusätzlichen Ressourcen:

- Informationen zur Entwicklung von Resource Manager-Vorlagen finden Sie in der [Azure Resource Manager-Dokumentation](/azure/azure-resource-manager/).
- Die Schemas zu virtuellen Azure-Computern finden Sie in der [Azure-Vorlagenreferenz](/azure/templates/microsoft.compute/allversions).
- Weitere Beispiele für VM-Vorlagen finden Sie unter [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular).

## <a name="connect-to-virtual-machine"></a>Herstellen der Verbindung mit dem virtuellen Computer

Anschließend können Sie wie gewohnt eine SSH-Verbindung mit dem virtuellen Computer herstellen. Geben Sie Ihre eigene öffentliche IP-Adresse aus dem vorherigen Befehl an:

```bash
ssh <adminUsername>@<ipAddress>
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Beispiel haben Sie eine einfache Linux-VM erstellt. Weitere Resource Manager-Vorlagen, die Anwendungsframeworks enthalten oder komplexere Umgebungen erstellen, finden Sie mithilfe einer Suche in den [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular).

Wenn Sie weitere Informationen zum Erstellen von Vorlagen benötigen, sehen Sie sich die JSON-Syntax und die Eigenschaften für die von Ihnen bereitgestellten Ressourcentypen an:

- [Microsoft.Network/networkSecurityGroups](/azure/templates/microsoft.network/networksecuritygroups)
- [Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
- [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
- [Microsoft.Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces)
- [Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines)
