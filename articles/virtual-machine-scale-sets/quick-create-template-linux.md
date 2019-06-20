---
title: 'Schnellstart: Erstellen einer Linux-VM-Skalierungsgruppe mit einer Azure-Vorlage | Microsoft-Dokumentation'
description: Es wird beschrieben, wie Sie mit einer Azure Resource Manager-Vorlage schnell eine Linux-VM-Skalierungsgruppe erstellen, mit der eine Beispiel-App bereitgestellt wird und Regeln für die automatische Skalierung konfiguriert werden.
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/27/2018
ms.author: cynthn
ms.openlocfilehash: 9afc2e2362721fd2905b2a241960507aec9b15a5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66151112"
---
# <a name="quickstart-create-a-linux-virtual-machine-scale-set-with-an-azure-template"></a>Schnellstart: Erstellen einer Linux-VM-Skalierungsgruppe mit einer Azure-Vorlage
Mit einer VM-Skalierungsgruppe können Sie eine Gruppe identischer, automatisch skalierender virtueller Computer bereitstellen und verwalten. Sie können die Anzahl virtueller Computer in der Skalierungsgruppe manuell skalieren oder basierend auf der Ressourcennutzung, z.B. CPU-Auslastung, Speicherbedarf oder Netzwerkdatenverkehr, Regeln für die automatische Skalierung definieren. Daraufhin wird der Datenverkehr durch einen Azure-Lastenausgleich auf die VM-Instanzen in der Skalierungsgruppe verteilt. In dieser Schnellstartanleitung erstellen Sie eine VM-Skalierungsgruppe und stellen eine Beispielanwendung mit einer Azure Resource Manager-Vorlage bereit.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Wenn Sie die Befehlszeilenschnittstelle lokal installieren und verwenden möchten, müssen Sie für dieses Tutorial mindestens die Azure CLI-Version 2.0.29 ausführen. Führen Sie `az --version` aus, um die Version zu finden. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sei bei Bedarf unter [Installieren der Azure CLI]( /cli/azure/install-azure-cli).


## <a name="define-a-scale-set-in-a-template"></a>Definieren einer Skalierungsgruppe in einer Vorlage
Mit Azure Resource Manager-Vorlagen können Sie Gruppen mit verwandten Ressourcen bereitstellen. Vorlagen werden in JSON (JavaScript Object Notation) geschrieben und definieren die gesamte Azure-Infrastrukturumgebung für Ihre Anwendung. In einer einzelnen Vorlage können Sie die VM-Skalierungsgruppe erstellen, Anwendungen installieren und Regeln für die automatische Skalierung konfigurieren. Bei Nutzung von Variablen und Parametern kann diese Vorlage wiederverwendet werden, um vorhandene Skalierungsgruppen zu aktualisieren oder zusätzliche zu erstellen. Sie können Vorlagen über das Azure-Portal, die Azure CLI oder Azure PowerShell oder über CI/CD-Pipelines (Continuous Integration/Continuous Delivery) bereitstellen.

Weitere Informationen zu Vorlagen finden Sie unter [Übersicht über Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#template-deployment). Informationen zur JSON-Syntax und zu Eigenschaften finden Sie in der Vorlagenreferenz [Microsoft.Compute/virtualMachineScaleSets](/azure/templates/microsoft.compute/virtualmachinescalesets).

Für die Erstellung einer Skalierungsgruppe mit einer Vorlage definieren Sie die entsprechenden Ressourcen. Dies sind die wichtigsten Bestandteile des Ressourcentyps einer VM-Skalierungsgruppe:

| Eigenschaft                     | Beschreibung der Eigenschaft                                  | Beispiel für Vorlagenwert                    |
|------------------------------|----------------------------------------------------------|-------------------------------------------|
| type                         | Zu erstellender Azure-Ressourcentyp                            | Microsoft.Compute/virtualMachineScaleSets |
| name                         | Name der Skalierungsgruppe                                       | myScaleSet                                |
| location                     | Standort für die Erstellung der Skalierungsgruppe                     | USA (Ost)                                   |
| sku.name                     | VM-Größe für die einzelnen Skalierungsgruppeninstanzen                  | Standard_A1                               |
| sku.capacity                 | Anzahl von VM-Instanzen für die anfängliche Erstellung           | 2                                         |
| upgradePolicy.mode           | Upgrademodus für VM-Instanz bei Auftreten von Änderungen              | Automatisch                                 |
| imageReference               | Plattform- oder benutzerdefiniertes Image für die VM-Instanzen | Canonical Ubuntu Server 16.04-LTS         |
| osProfile.computerNamePrefix | Namenspräfix für die einzelnen VM-Instanzen                     | myvmss                                    |
| osProfile.adminUsername      | Benutzername für die einzelnen VM-Instanzen                        | azureuser                                 |
| osProfile.adminPassword      | Kennwort für die einzelnen VM-Instanzen                        | P@ssw0rd!                                 |

 Im folgenden Beispiel wird die grundlegende Ressourcendefinition der Skalierungsgruppe veranschaulicht. Zu Anpassen einer Skalierungsgruppenvorlage können Sie die VM-Größe oder anfängliche Kapazität ändern oder ein anderes Plattformimage bzw. ein benutzerdefiniertes Image verwenden.

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US",
  "apiVersion": "2017-12-01",
  "sku": {
    "name": "Standard_A1",
    "capacity": "2"
  },
  "properties": {
    "upgradePolicy": {
      "mode": "Automatic"
    },
    "virtualMachineProfile": {
      "storageProfile": {
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage"
        },
        "imageReference":  {
          "publisher": "Canonical",
          "offer": "UbuntuServer",
          "sku": "16.04-LTS",
          "version": "latest"
        }
      },
      "osProfile": {
        "computerNamePrefix": "myvmss",
        "adminUsername": "azureuser",
        "adminPassword": "P@ssw0rd!"
      }
    }
  }
}
```

 Die Konfiguration der virtuellen Netzwerkschnittstellenkarte (NIC) ist nicht angegeben, um das Beispiel möglichst kurz zu halten. Auch zusätzliche Komponenten, z.B. ein Lastenausgleichsmodul, werden nicht angezeigt. Eine vollständige Skalierungsgruppenvorlage ist [am Ende dieses Artikels](#deploy-the-template) angegeben.


## <a name="add-a-sample-application"></a>Hinzufügen einer Beispielanwendung
Installieren Sie eine einfache Webanwendung, um Ihre Skalierungsgruppe zu testen. Beim Bereitstellen einer Skalierungsgruppe können über VM-Erweiterungen Aufgaben für die Konfiguration und Automatisierung nach der Bereitstellung, z.B. das Installieren einer App, bereitgestellt werden. Skripts können aus Azure Storage oder GitHub heruntergeladen oder dem Azure-Portal zur Laufzeit für die Erweiterung bereitgestellt werden. Fügen Sie dem obigen Ressourcenbeispiel den Abschnitt *extensionProfile* hinzu, um eine Erweiterung auf Ihre Skalierungsgruppe anzuwenden. Im Erweiterungsprofil werden normalerweise die folgenden Eigenschaften definiert:

- Erweiterungstyp
- Erweiterungsherausgeber
- Erweiterungsversion
- Speicherort der Konfiguration bzw. der Installationsskripts
- Auf den VM-Instanzen auszuführende Befehle

Für die Vorlage vom Typ [Python-HTTP-Server unter Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale) wird die benutzerdefinierte Skripterweiterung verwendet, um [Bottle](https://bottlepy.org/docs/dev/), ein Python-Webframework und einen einfachen HTTP-Server zu installieren. 

Zwei Skripts sind in **fileUris** - *installserver.sh* und *workserver.py* definiert. Diese Dateien werden von GitHub heruntergeladen, und anschließend wird von *commandToExecute* der Befehl `bash installserver.sh` ausgeführt, um die App zu installieren und zu konfigurieren:

```json
"extensionProfile": {
  "extensions": [
    {
      "name": "AppInstall",
      "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "fileUris": [
            "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/installserver.sh",
            "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/workserver.py"
          ],
          "commandToExecute": "bash installserver.sh"
        }
      }
    }
  ]
}
```


## <a name="deploy-the-template"></a>Bereitstellen der Vorlage
Sie können die Vorlage vom Typ [Python-HTTP-Server unter Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale) bereitstellen, indem Sie die folgende Schaltfläche **In Azure bereitstellen** verwenden. Diese Schaltfläche bewirkt Folgendes: Das Azure-Portal wird geöffnet, die vollständige Vorlage wird geladen, und es werden einige Parameter abgefragt, z.B. der Name der Skalierungsgruppe, die Instanzanzahl und die Administratoranmeldeinformationen.

[![In Azure bereitstellen](media/virtual-machine-scale-sets-create-template/deploy-button.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-vmss-bottle-autoscale%2Fazuredeploy.json)

Sie können auch die Azure CLI verwenden, um den Python-HTTP-Server unter Linux wie folgt mit dem Befehl [az group deployment create](/cli/azure/group/deployment) zu installieren:

```azurecli-interactive
# Create a resource group
az group create --name myResourceGroup --location EastUS

# Deploy template into resource group
az group deployment create \
    --resource-group myResourceGroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/azuredeploy.json
```

Folgen Sie den Aufforderungen zum Angeben des Namens einer Skalierungsgruppe, der Instanzanzahl und der Administratoranmeldeinformationen für die VM-Instanzen. Es dauert einige Minuten, bis die Skalierungsgruppe und die unterstützenden Ressourcen erstellt werden.


## <a name="test-your-scale-set"></a>Testen Ihrer Skalierungsgruppe
Greifen Sie über einen Webbrowser auf die Beispielwebanwendung zu, um Ihre Skalierungsgruppe in Aktion zu sehen. Rufen Sie mit [az network public-ip list](/cli/azure/network/public-ip) die öffentliche IP-Adresse des Lastenausgleichs ab:

```azurecli-interactive
az network public-ip list \
    --resource-group myResourceGroup \
    --query [*].ipAddress -o tsv
```

Geben Sie die öffentliche IP-Adresse des Lastenausgleichsmoduls in einem Webbrowser im Format *http:\//publicIpAddress:9000/do_work* ein. Das Lastenausgleichsmodul verteilt den Datenverkehr auf eine Ihrer VM-Instanzen. Dies ist im folgenden Beispiel dargestellt:

![Standardwebseite in NGINX](media/virtual-machine-scale-sets-create-template/running-python-app.png)


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen
Wenn Sie die Ressourcengruppe, die Skalierungsgruppe und die dazugehörigen Ressourcen nicht mehr benötigen, können Sie sie wie folgt mit dem Befehl [az group delete](/cli/azure/group) entfernen. Der Parameter `--no-wait` gibt die Steuerung an die Eingabeaufforderung zurück, ohne zu warten, bis der Vorgang abgeschlossen ist. Der Parameter `--yes` bestätigt ohne eine zusätzliche Aufforderung, dass Sie die Ressourcen löschen möchten.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```


## <a name="next-steps"></a>Nächste Schritte
In dieser Schnellstartanleitung haben Sie eine Linux-Skalierungsgruppe mit einer Azure-Vorlage erstellt und die benutzerdefinierte Skripterweiterung verwendet, um einen einfachen Python-Webserver auf den VM-Instanzen zu installieren. Im nächsten Tutorial erfahren Sie mehr über die Erstellung und Verwaltung von Azure-VM-Skalierungsgruppen.

> [!div class="nextstepaction"]
> [Erstellen und Verwalten von Azure-VM-Skalierungsgruppen](tutorial-create-and-manage-cli.md)
