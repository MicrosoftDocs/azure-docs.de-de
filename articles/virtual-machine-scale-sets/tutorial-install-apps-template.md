---
title: Tutorial – Installieren von Anwendungen in einer Skalierungsgruppe mit Azure-Vorlagen | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie Azure Resource Manager-Vorlagen zum Installieren von Anwendungen in VM-Skalierungsgruppen mit der benutzerdefinierten Skripterweiterung verwenden.
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
ms.topic: tutorial
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 176cf31d7a87b08755ee2acb94aea23684647213
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66170475"
---
# <a name="tutorial-install-applications-in-virtual-machine-scale-sets-with-an-azure-template"></a>Tutorial: Installieren von Anwendungen in VM-Skalierungsgruppen mit einer Azure-Vorlage
Zum Ausführen von Anwendungen auf VM-Instanzen in einer Skalierungsgruppe müssen Sie zuerst die Anwendungskomponenten und erforderlichen Dateien installieren. In einem vorherigen Tutorial wurde beschrieben, wie Sie ein benutzerdefiniertes VM-Image erstellen und verwenden, um Ihre VM-Instanzen bereitzustellen. Dieses benutzerdefinierte Image umfasste manuelle Anwendungsinstallationen und -konfigurationen. Sie können die Installation von Anwendungen auch per Skalierungsgruppe automatisieren, nachdem die einzelnen VM-Instanzen bereitgestellt wurden, oder eine Anwendung aktualisieren, die bereits in einer Skalierungsgruppe ausgeführt wird. In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Automatisches Installieren von Anwendungen in Ihrer Skalierungsgruppe
> * Verwenden der benutzerdefinierten Skripterweiterung von Azure
> * Aktualisieren einer ausgeführten Anwendung in einer Skalierungsgruppe

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Wenn Sie die Befehlszeilenschnittstelle lokal installieren und verwenden möchten, müssen Sie für dieses Tutorial mindestens die Azure CLI-Version 2.0.29 ausführen. Führen Sie `az --version` aus, um die Version zu finden. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sei bei Bedarf unter [Installieren der Azure CLI]( /cli/azure/install-azure-cli).


## <a name="what-is-the-azure-custom-script-extension"></a>Was ist die benutzerdefinierte Skripterweiterung von Azure?
Die benutzerdefinierte Skripterweiterung lädt Skripts auf Azure-VMs herunter und führt sie aus. Diese Erweiterung ist hilfreich bei der Konfiguration nach der Bereitstellung, bei der Softwareinstallation oder bei anderen Konfigurations-/Verwaltungsaufgaben. Skripts können aus Azure Storage oder GitHub heruntergeladen oder dem Azure-Portal zur Laufzeit für die Erweiterung bereitgestellt werden.

Die benutzerdefinierte Skripterweiterung kann in Azure Resource Manager-Vorlagen integriert und auch mit der Azure CLI, Azure PowerShell, dem Azure-Portal oder der REST-API verwendet werden. Weitere Informationen finden Sie unter [Übersicht über benutzerdefinierte Skripterweiterungen](../virtual-machines/linux/extensions-customscript.md).

Wenn Sie die benutzerdefinierte Skripterweiterung in Aktion sehen möchten, können Sie eine Skalierungsgruppe erstellen, mit der der NGINX-Webserver installiert und der Hostname einer VM-Instanz der Skalierungsgruppe ausgegeben wird. Mit der folgenden Definition der benutzerdefinierten Skripterweiterung wird ein Beispielskript von GitHub heruntergeladen, die erforderlichen Pakete werden installiert, und anschließend wird der Hostname der VM-Instanz auf eine einfache HTML-Seite geschrieben.


## <a name="create-custom-script-extension-definition"></a>Erstellen der Definition für die benutzerdefinierte Skripterweiterung
Wenn Sie eine VM-Skalierungsgruppe mit einer Azure-Vorlage definieren, kann der Ressourcenanbieter *Microsoft.Compute/virtualMachineScaleSets* einen Abschnitt mit Erweiterungen enthalten. Mit *extensionsProfile* wird angegeben, was auf die VM-Instanzen in einer Skalierungsgruppe angewendet wird. Zur Verwendung der benutzerdefinierten Skripterweiterung geben Sie als Publisher *Microsoft.Azure.Extensions* und als Typ *CustomScript* an.

Die *fileUris*-Eigenschaft wird verwendet, um die Quellskripts bzw. -pakete für die Installation zu definieren. Zum Starten des Installationsprozesses werden die erforderlichen Skripts in *commandToExecute* definiert. Im folgenden Beispiel wird ein Beispielskript von GitHub definiert, mit dem der NGINX-Webserver installiert und konfiguriert wird:

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
            "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate_nginx.sh"
          ],
          "commandToExecute": "bash automate_nginx.sh"
        }
      }
    }
  ]
}
```

Ein vollständiges Beispiel für eine Azure-Vorlage, mit der eine Skalierungsgruppe und die benutzerdefinierte Skripterweiterung bereitgestellt werden, finden Sie unter [https://github.com/Azure-Samples/compute-automation-configurations/blob/master/scale_sets/azuredeploy.json](https://github.com/Azure-Samples/compute-automation-configurations/blob/master/scale_sets/azuredeploy.json). Diese Beispielvorlage wird im nächsten Abschnitt verwendet.


## <a name="create-a-scale-set"></a>Erstellen einer Skalierungsgruppe
Wir verwenden die Beispielvorlage, um eine Skalierungsgruppe zu erstellen und die benutzerdefinierte Skripterweiterung anzuwenden. Erstellen Sie zunächst mit [az group create](/cli/azure/group) eine Ressourcengruppe. Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen *myResourceGroup* am Standort *eastus* erstellt:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Sie erstellen nun mit [az group deployment create](/cli/azure/group/deployment) eine VM-Skalierungsgruppe. Geben Sie bei entsprechender Aufforderung Ihren eigenen Benutzernamen mit dem dazugehörigen Kennwort jeweils als Anmeldeinformationen für eine VM-Instanz an:

```azurecli-interactive
az group deployment create \
  --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/scale_sets/azuredeploy.json
```

Die Erstellung und Konfiguration aller Ressourcen und virtuellen Computer der Skalierungsgruppe dauert einige Minuten.

Jede VM-Instanz in der Skalierungsgruppe wird heruntergeladen und führt das Skript von GitHub aus. In einem komplexeren Beispiel können auch mehrere Anwendungskomponenten und Dateien installiert werden. Wenn die Skalierungsgruppe zentral hochskaliert wird, wenden die neuen VM-Instanzen automatisch dieselbe Definition der benutzerdefinierten Skripterweiterung an und installieren die erforderliche Anwendung.


## <a name="test-your-scale-set"></a>Testen Ihrer Skalierungsgruppe
Um den Webserver in Aktion zu sehen, rufen Sie mit [az network public-ip show](/cli/azure/network/public-ip) die öffentliche IP-Adresse des Lastenausgleichsmoduls ab. Im folgenden Beispiel wird die IP-Adresse für *myScaleSetPublicIP* abgerufen, die als Teil der Skalierungsgruppe erstellt wurde:

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroup \
  --name myScaleSetPublicIP \
  --query [ipAddress] \
  --output tsv
```

Geben Sie die öffentliche IP-Adresse des Lastenausgleichsmoduls in einen Webbrowser ein. Das Lastenausgleichsmodul verteilt den Datenverkehr auf eine Ihrer VM-Instanzen. Dies ist im folgenden Beispiel dargestellt:

![Einfache Webseite in NGINX](media/tutorial-install-apps-template/running-nginx.png)

Lassen Sie den Webbrowser geöffnet, damit Sie im nächsten Schritt die aktualisierte Version sehen können.


## <a name="update-app-deployment"></a>Aktualisieren der App-Bereitstellung
Während des Lebenszyklus einer Skalierungsgruppe müssen Sie ggf. eine aktualisierte Version Ihrer Anwendung bereitstellen. Mit der benutzerdefinierten Skripterweiterung können Sie auf ein aktualisiertes Bereitstellungsskript verweisen und die Erweiterung dann erneut auf Ihre Skalierungsgruppe anwenden. Als die Skalierungsgruppe in einem vorherigen Schritt erstellt wurde, wurde *upgradePolicy* auf *Automatic* festgelegt. Mit dieser Einstellung können die VM-Instanzen in der Skalierungsgruppe aktualisiert werden, und die aktuelle Version Ihrer Anwendung kann angewendet werden.

Um die Definition der benutzerdefinierten Skripterweiterung zu aktualisieren, ändern Sie Ihre Vorlage so, dass auf ein neues Installationsskript verwiesen wird. Für die benutzerdefinierte Skripterweiterung muss ein neuer Dateiname verwendet werden, damit die Änderung erkannt wird. Für die benutzerdefinierte Skripterweiterung wird keine Untersuchung des Skripts zur Ermittlung von Änderungen durchgeführt. In der folgenden Definition wird ein aktualisiertes Installationsskript mit dem Namenszusatz *_v2* verwendet:

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
            "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate_nginx_v2.sh"
          ],
          "commandToExecute": "bash automate_nginx_v2.sh"
        }
      }
    }
  ]
}
```

Wenden Sie die Konfiguration der benutzerdefinierten Skripterweiterung erneut auf die VM-Instanzen in Ihrer Skalierungsgruppe an, indem Sie [az group deployment create](/cli/azure/group/deployment) verwenden. Die Vorlage *azuredeployv2.json* wird verwendet, um die aktualisierte Version der Anwendung anzuwenden. In der Praxis ändern Sie die vorhandene Vorlage *azuredeploy.json* so, dass sie auf das aktualisierte Installationsskript verweist. Dies wurde im vorherigen Abschnitt veranschaulicht. Geben Sie bei entsprechender Aufforderung denselben Benutzernamen mit dem dazugehörigen Kennwort als Anmeldeinformationen ein, den Sie bei der ursprünglichen Erstellung der Skalierungsgruppe verwendet haben:

```azurecli-interactive
az group deployment create \
  --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/scale_sets/azuredeploy_v2.json
```

Alle VM-Instanzen in der Skalierungsgruppe werden automatisch mit der aktuellen Version der Beispielwebseite aktualisiert. Aktualisieren Sie die Webseite in Ihrem Browser, um die aktualisierte Version anzuzeigen:

![Aktualisierte Webseite in NGINX](media/tutorial-install-apps-template/running-nginx-updated.png)


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen
Löschen Sie die Ressourcengruppe und alle dazugehörigen Ressourcen mit [az group delete](/cli/azure/group), um Ihre Skalierungsgruppe und die weiteren Ressourcen zu entfernen. Der Parameter `--no-wait` gibt die Steuerung an die Eingabeaufforderung zurück, ohne zu warten, bis der Vorgang abgeschlossen ist. Der Parameter `--yes` bestätigt ohne eine zusätzliche Aufforderung, dass Sie die Ressourcen löschen möchten.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>Nächste Schritte
In diesem Tutorial wurde beschrieben, wie Sie Anwendungen in Ihrer Skalierungsgruppe mit Azure-Vorlagen automatisch installieren und aktualisieren:

> [!div class="checklist"]
> * Automatisches Installieren von Anwendungen in Ihrer Skalierungsgruppe
> * Verwenden der benutzerdefinierten Skripterweiterung von Azure
> * Aktualisieren einer ausgeführten Anwendung in einer Skalierungsgruppe

Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie Ihre Skalierungsgruppe automatisch skalieren.

> [!div class="nextstepaction"]
> [Automatisches Skalieren Ihrer Skalierungsgruppen](tutorial-autoscale-template.md)
