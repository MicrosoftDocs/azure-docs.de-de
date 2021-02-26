---
title: 'Schnellstart: Erstellen einer Data Science VM: Resource Manager-Vorlage'
titleSuffix: Azure Data Science Virtual Machine
description: In dieser Schnellstartanleitung verwenden Sie eine Azure Resource Manager-Vorlage, um in kurzer Zeit eine Data Science Virtual Machine bereitzustellen.
services: machine-learning
author: lobrien
ms.author: laobri
ms.custom: subject-armqs
ms.date: 06/10/2020
ms.service: data-science-vm
ms.topic: quickstart
ms.openlocfilehash: 7feacec9f0e78749f17359a9080411b6d9696136
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100518214"
---
# <a name="quickstart-create-an-ubuntu-data-science-virtual-machine-using-an-arm-template"></a>Schnellstart: Erstellen einer Data Science Virtual Machine für Ubuntu mithilfe einer ARM-Vorlage

In dieser Schnellstartanleitung wird veranschaulicht, wie Sie mit einer Azure Resource Manager-Vorlage (ARM-Vorlage) eine Data Science Virtual Machine für Ubuntu 18.04 erstellen. Bei Data Science Virtual Machines handelt es sich um cloudbasierte virtuelle Computer, auf denen bereits eine Suite mit Data Science- und Machine Learning-Frameworks und -Tools geladen ist. Bei der Bereitstellung auf GPU-basierten Computeressourcen werden alle Tools und Bibliotheken für die Nutzung der GPU konfiguriert.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Wenn Ihre Umgebung die Voraussetzungen erfüllt und Sie mit der Verwendung von ARM-Vorlagen vertraut sind, klicken Sie auf die Schaltfläche **In Azure bereitstellen**. Die Vorlage wird im Azure-Portal geöffnet.

[![In Azure bereitstellen](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-ubuntu-DSVM-GPU-or-CPU%2Fazuredeploy.json)

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/services/machine-learning/) erstellen, bevor Sie beginnen.

* Um die CLI-Befehle in diesem Dokument aus Ihrer **lokalen Umgebung** zu verwenden, benötigen Sie die [Azure CLI](/cli/azure/install-azure-cli).

## <a name="review-the-template"></a>Überprüfen der Vorlage

Die in dieser Schnellstartanleitung verwendete Vorlage stammt von der Seite mit den [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/101-vm-ubuntu-DSVM-GPU-or-CPU/).

:::code language="json" source="~/quickstart-templates/101-vm-ubuntu-DSVM-GPU-or-CPU/azuredeploy.json":::

Die folgenden Ressourcen sind in der Vorlage definiert:

* [Microsoft.Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces)
* [Microsoft.Network/networkSecurityGroups](/azure/templates/microsoft.network/networksecuritygroups)
* [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
* [Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
* [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
* [Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines): Dient zum Erstellen eines cloudbasierten virtuellen Computers. In dieser Vorlage wird der virtuelle Computer als Data Science Virtual Machine konfiguriert, auf der Ubuntu 18.04 ausgeführt wird.

## <a name="deploy-the-template"></a>Bereitstellen der Vorlage

Um die Vorlage an der Azure-Befehlszeilenschnittstelle zu verwenden, melden Sie sich an, und wählen Sie Ihr Abonnement aus (Siehe dazu [Anmelden bei Azure CLI](/cli/azure/authenticate-azure-cli)). Führen Sie dann Folgendes aus:

```azurecli-interactive
read -p "Enter the name of the resource group to create:" resourceGroupName &&
read -p "Enter the Azure location (e.g., centralus):" location &&
read -p "Enter the authentication type (must be 'password' or 'sshPublicKey') :" authenticationType &&
read -p "Enter the login name for the administrator account (may not be 'admin'):" adminUsername &&
read -p "Enter administrator account secure string (value of password or ssh public key):" adminPasswordOrKey &&
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-ubuntu-DSVM-GPU-or-CPU/azuredeploy.json" &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri $templateUri --parameters adminUsername=$adminUsername authenticationType=$authenticationType adminPasswordOrKey=$adminPasswordOrKey &&
echo "Press [ENTER] to continue ..." &&
read
```

Wenn Sie den Befehl oben ausführen, geben Sie dies ein:

1. Den Namen der Ressourcengruppe, die Sie erstellen möchten und die die DSVM und die zugehörigen Ressourcen enthalten soll.
1. Den Azure-Standort, an dem die Bereitstellung erfolgen soll
1. Den Authentifizierungstyp, den Sie verwenden möchten (geben Sie die Zeichenfolge `password` oder `sshPublicKey` ein).
1. Die Anmelde-ID des Administratorkontos (Wert darf nicht `admin` lauten).
1. Den Wert für das Kennwort oder den öffentlichen SSH-Schlüssel des Kontos.

## <a name="review-deployed-resources"></a>Überprüfen der bereitgestellten Ressourcen

Zeigen Sie Ihre Data Science Virtual Machine wie folgt an:

1. [Navigieren Sie zum Azure-Portal](https://portal.azure.com).
1. Melden Sie sich an.
1. Wählen Sie die Ressourcengruppe aus, die Sie soeben erstellt haben.

Die Informationen zur Ressourcengruppe werden angezeigt:

:::image type="content" source="media/dsvm-tutorial-resource-manager/resource-group-home.png" alt-text="Screenshot: Grundlegende Ressourcengruppe mit einer DSVM":::

Klicken Sie auf die Virtual Machine-Ressource, um auf die Seite mit den zugehörigen Informationen zuzugreifen. Sie enthält Informationen zur VM, z. B. Verbindungsdetails.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie diesen virtuellen Computer, falls Sie ihn nicht nutzen möchten. Da die DSVM noch weiteren Ressourcen zugeordnet ist, z. B. einem Speicherkonto, kann es ratsam sein, die gesamte erstellte Ressourcengruppe zu löschen. Sie können die Ressourcengruppe im Portal löschen, indem Sie auf die Schaltfläche **Löschen** klicken und den Vorgang bestätigen. Alternativ können Sie die Ressourcengruppe an der Befehlszeilenschnittstelle löschen, mit diesem Befehl:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie mit einer ARM-Vorlage eine Data Science Virtual Machine erstellt.

> [!div class="nextstepaction"]
> [Beispielprogramme und exemplarische Vorgehensweisen für ML](dsvm-samples-and-walkthroughs.md)
