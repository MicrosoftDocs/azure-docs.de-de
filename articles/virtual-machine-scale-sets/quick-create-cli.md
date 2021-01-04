---
title: 'Schnellstart: Erstellen einer VM-Skalierungsgruppe mithilfe der Azure-Befehlszeilenschnittstelle'
description: Zum Einstieg in Ihre Bereitstellungen lernen Sie, wie Sie mit der Azure-Befehlszeilenschnittstelle schnell eine VM-Skalierungsgruppe erstellen können.
author: ju-shim
ms.author: jushiman
ms.topic: quickstart
ms.service: virtual-machine-scale-sets
ms.subservice: cli
ms.date: 03/27/2018
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: 5ed6ff00e2ec9a47cec0290fa88ef3b554287607
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94518157"
---
# <a name="quickstart-create-a-virtual-machine-scale-set-with-the-azure-cli"></a>Schnellstart: Erstellen einer VM-Skalierungsgruppe mithilfe der Azure CLI
Mit einer VM-Skalierungsgruppe können Sie eine Gruppe automatisch skalierender virtueller Computer bereitstellen und verwalten. Sie können die Anzahl virtueller Computer in der Skalierungsgruppe manuell skalieren oder basierend auf der Ressourcennutzung, z.B. CPU-Auslastung, Speicherbedarf oder Netzwerkdatenverkehr, Regeln für die automatische Skalierung definieren. Daraufhin wird der Datenverkehr durch einen Azure-Lastenausgleich auf die VM-Instanzen in der Skalierungsgruppe verteilt. In diesem Schnellstart erstellen Sie eine VM-Skalierungsgruppe und stellen eine Beispielanwendung mit der Azure-Befehlszeilenschnittstelle bereit.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Für diesen Artikel ist mindestens Version 2.0.29 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert. 


## <a name="create-a-scale-set"></a>Erstellen einer Skalierungsgruppe
Vor der Erstellung einer Skalierungsgruppe müssen Sie zunächst mit [az group create](/cli/azure/group) eine Ressourcengruppe erstellen. Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen *myResourceGroup* am Standort *eastus* erstellt:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Erstellen Sie dann mit [az vmss create](/cli/azure/vmss) eine VM-Skalierungsgruppe. Im folgenden Beispiel wird eine Skalierungsgruppe mit dem Namen *myScaleSet* erstellt, die automatisch aktualisiert wird, wenn Änderungen angewendet werden. Außerdem werden SSH-Schlüssel generiert, falls sie unter *~/.ssh/id_rsa* nicht vorhanden sind. Diese SSH-Schlüssel werden verwendet, wenn Sie sich an VM-Instanzen anmelden müssen. Wenn Sie einen vorhandenen Satz mit SSH-Schlüsseln verwenden möchten, können Sie den Parameter `--ssh-key-value` nutzen und den Speicherort Ihrer Schlüssel angeben.

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --generate-ssh-keys
```

Die Erstellung und Konfiguration aller Ressourcen und virtuellen Computer der Skalierungsgruppe dauert einige Minuten.


## <a name="deploy-sample-application"></a>Bereitstellen der Beispielanwendung
Installieren Sie eine einfache Webanwendung, um Ihre Skalierungsgruppe zu testen. Die benutzerdefinierte Skripterweiterung von Azure wird zum Herunterladen und Ausführen eines Skripts verwendet, mit dem eine Anwendung auf den VM-Instanzen installiert wird. Diese Erweiterung ist hilfreich bei der Konfiguration nach der Bereitstellung, bei der Softwareinstallation oder bei anderen Konfigurations-/Verwaltungsaufgaben. Weitere Informationen finden Sie unter [Übersicht über benutzerdefinierte Skripterweiterungen](../virtual-machines/extensions/custom-script-linux.md).

Verwenden Sie die benutzerdefinierte Skripterweiterung, um einen einfachen NGINX-Webserver zu installieren. Wenden Sie die benutzerdefinierte Skripterweiterung für die Installation von NGINX mit [az vmss extension set](/cli/azure/vmss/extension) wie folgt an:

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroup \
  --vmss-name myScaleSet \
  --settings '{"fileUris":["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate_nginx.sh"],"commandToExecute":"./automate_nginx.sh"}'
```


## <a name="allow-traffic-to-application"></a>Zulassen von Datenverkehr für die Anwendung
Nachdem die Skalierungsgruppe erstellt wurde, wird automatisch ein Azure Load Balancer bereitgestellt. Der Load Balancer verteilt Datenverkehr auf die VM-Instanzen der Skalierungsgruppe. Damit Datenverkehr die Beispielwebanwendung erreicht, erstellen Sie mit [az network lb rule create](/cli/azure/network/lb/rule) eine Regel für den Lastenausgleich. Im folgenden Beispiel wird eine Regel namens *myLoadBalancerRuleWeb* erstellt:

```azurecli-interactive
az network lb rule create \
  --resource-group myResourceGroup \
  --name myLoadBalancerRuleWeb \
  --lb-name myScaleSetLB \
  --backend-pool-name myScaleSetLBBEPool \
  --backend-port 80 \
  --frontend-ip-name loadBalancerFrontEnd \
  --frontend-port 80 \
  --protocol tcp
```


## <a name="test-your-scale-set"></a>Testen Ihrer Skalierungsgruppe
Greifen Sie über einen Webbrowser auf die Beispielwebanwendung zu, um Ihre Skalierungsgruppe in Aktion zu sehen. Rufen Sie mit [az network public-ip show](/cli/azure/network/public-ip) die öffentliche IP-Adresse Ihres Load Balancers ab. Im folgenden Beispiel wird die IP-Adresse für *myScaleSetLBPublicIP* abgerufen, die als Teil der Skalierungsgruppe erstellt wurde:

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroup \
  --name myScaleSetLBPublicIP \
  --query '[ipAddress]' \
  --output tsv
```

Geben Sie die öffentliche IP-Adresse des Lastenausgleichsmoduls in einen Webbrowser ein. Das Lastenausgleichsmodul verteilt den Datenverkehr auf eine Ihrer VM-Instanzen. Dies ist im folgenden Beispiel dargestellt:

![Standardwebseite in NGINX](media/virtual-machine-scale-sets-create-cli/running-nginx-site.png)


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen
Wenn Sie die Ressourcengruppe, die Skalierungsgruppe und die dazugehörigen Ressourcen nicht mehr benötigen, können Sie sie wie folgt mit dem Befehl [az group delete](/cli/azure/group) entfernen. Der Parameter `--no-wait` gibt die Steuerung an die Eingabeaufforderung zurück, ohne zu warten, bis der Vorgang abgeschlossen ist. Der Parameter `--yes` bestätigt ohne eine zusätzliche Aufforderung, dass Sie die Ressourcen löschen möchten.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```


## <a name="next-steps"></a>Nächste Schritte
In dieser Schnellstartanleitung haben Sie eine einfache Skalierungsgruppe erstellt und die benutzerdefinierte Skripterweiterung verwendet, um einen einfachen NGINX-Webserver auf den VM-Instanzen zu installieren. Im nächsten Tutorial erfahren Sie mehr über die Erstellung und Verwaltung von Azure-VM-Skalierungsgruppen.

> [!div class="nextstepaction"]
> [Erstellen und Verwalten von Azure-VM-Skalierungsgruppen](tutorial-create-and-manage-cli.md)
