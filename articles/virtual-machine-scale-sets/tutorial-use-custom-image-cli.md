---
title: 'Tutorial: Verwenden eines benutzerdefinierten VM-Images in einer Skalierungsgruppe mit der Azure CLI | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie die Azure CLI zum Erstellen eines benutzerdefinierten VM-Images verwenden, das Sie zum Bereitstellen einer VM-Skalierungsgruppe nutzen können.
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
ms.openlocfilehash: aafec48f86ee032b112e9bb1100f82fbb3b363ed
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66170488"
---
# <a name="tutorial-create-and-use-a-custom-image-for-virtual-machine-scale-sets-with-the-azure-cli"></a>Tutorial: Erstellen und Verwenden eines benutzerdefinierten Images für VM-Skalierungsgruppen mit der Azure CLI
Wenn Sie eine Skalierungsgruppe erstellen, geben Sie ein Image an, das beim Bereitstellen der VM-Instanzen verwendet wird. Sie können ein benutzerdefiniertes VM-Image verwenden, um die Anzahl von Aufgaben zu reduzieren, nachdem VM-Instanzen bereitgestellt wurden. Dieses benutzerdefinierte VM-Image enthält alle erforderlichen Anwendungsinstallationen oder -konfigurationen. Für alle VM-Instanzen, die in der Skalierungsgruppe erstellt werden, wird das benutzerdefinierte VM-Image verwendet, und die VM-Instanzen sind für die Bereitstellung Ihres Anwendungsdatenverkehrs bereit. In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen und Anpassen einer VM
> * Aufheben der Bereitstellung und Generalisieren der VM
> * Erstellen eines benutzerdefinierten VM-Images
> * Bereitstellen einer Skalierungsgruppe, für die das benutzerdefinierte VM-Image verwendet wird

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Wenn Sie die Befehlszeilenschnittstelle lokal installieren und verwenden möchten, müssen Sie für dieses Tutorial mindestens die Azure CLI-Version 2.0.29 ausführen. Führen Sie `az --version` aus, um die Version zu finden. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sei bei Bedarf unter [Installieren der Azure CLI]( /cli/azure/install-azure-cli).


## <a name="create-and-configure-a-source-vm"></a>Erstellen und Konfigurieren einer Quell-VM

>[!NOTE]
> In diesem Tutorial erfahren Sie Schritt für Schritt, wie Sie ein generalisiertes VM-Image erstellen und verwenden. Auf der Grundlage eines spezialisierten VM-Images kann keine Skalierungsgruppe erstellt werden.

Erstellen Sie zunächst mit [az group create](/cli/azure/group) eine Ressourcengruppe und dann mit [az vm create](/cli/azure/vm) eine VM. Diese VM wird dann als Quelle für ein benutzerdefiniertes VM-Image verwendet. Im folgenden Beispiel wird eine VM mit dem Namen *myVM* in der Ressourcengruppe *myResourceGroup* erstellt:

```azurecli-interactive
az group create --name myResourceGroup --location eastus

az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image ubuntults \
  --admin-username azureuser \
  --generate-ssh-keys
```

Die öffentliche IP-Adresse Ihrer VM wird in der Ausgabe des Befehls [az vm create](/cli/azure/vm) angezeigt. Stellen Sie wie folgt eine SSH-Verbindung mit der öffentlichen IP-Adresse Ihrer VM her:

```azurecli-interactive
ssh azureuser@<publicIpAddress>
```

Installieren Sie einen einfachen Webserver, um Ihre VM anzupassen. Wenn die VM-Instanz in der Skalierungsgruppe bereitgestellt wird, verfügt sie über alle erforderlichen Pakete zum Ausführen einer Webanwendung. Verwenden Sie `apt-get` wie folgt, um *NGINX* zu installieren:

```bash
sudo apt-get install -y nginx
```

Im letzten Schritt der Vorbereitung Ihrer VM für die Nutzung als benutzerdefiniertes Image heben Sie die Bereitstellung der VM auf. Mit diesem Schritt werden computerspezifische Informationen von der VM entfernt, und es wird ermöglicht, viele VMs über ein einzelnes Image bereitzustellen. Nachdem die Bereitstellung der VM aufgehoben wurde, wird der Hostname auf *localhost.localdomain* zurückgesetzt. Ebenfalls gelöscht werden SSH-Hostschlüssel, Namenserverkonfigurationen, das Stammkennwort und zwischengespeicherte DHCP-Leases.

Verwenden Sie zum Aufheben der Bereitstellung des virtuellen Computers den Azure-VM-Agent (*waagent*). Der Azure-VM-Agent wird auf jeder VM installiert und zum Kommunizieren mit der Azure Platform verwendet. Mit dem Parameter `-force` wird der Agent angewiesen, Aufforderungen zum Zurücksetzen der computerspezifischen Informationen zu akzeptieren.

```bash
sudo waagent -deprovision+user -force
```

Schließen Sie die SSH-Verbindung mit der VM:

```bash
exit
```


## <a name="create-a-custom-vm-image-from-the-source-vm"></a>Erstellen eines benutzerdefinierten VM-Images von der Quell-VM
Die Quell-VM wird jetzt mit dem installierten NGINX-Webserver angepasst. Wir erstellen das benutzerdefinierte VM-Image für die Verwendung mit einer Skalierungsgruppe.

Zum Erstellen eines Images muss die Zuordnung des virtuellen Computers aufgehoben werden. Heben Sie die Zuordnung der VM mit [az vm deallocate](/cli//azure/vm) auf. Legen Sie den Status der VM anschließend mit [az vm generalize](/cli//azure/vm) auf „Generalisiert“ fest, damit die Azure Platform darüber informiert ist, dass die VM als benutzerdefiniertes Image verwendet werden kann. Sie können nur aus einer generalisierten VM ein Image erstellen:


```azurecli-interactive
az vm deallocate --resource-group myResourceGroup --name myVM
az vm generalize --resource-group myResourceGroup --name myVM
```

Es kann einige Minuten dauern, die Zuordnung der VM aufzuheben und sie zu generalisieren.

Erstellen Sie jetzt mit [az image create](/cli//azure/image) ein Image aus der VM. Im folgenden Beispiel wird ein Image mit dem Namen *myImage* aus Ihrer VM erstellt:

```azurecli-interactive
az image create \
  --resource-group myResourceGroup \
  --name myImage \
  --source myVM
```


## <a name="create-a-scale-set-from-the-custom-vm-image"></a>Erstellen einer Skalierungsgruppe aus dem benutzerdefinierten VM-Image
Erstellen Sie mit [az vmss create](/cli/azure/vmss#az-vmss-create) eine Skalierungsgruppe. Geben Sie anstelle eines Plattformimages, z.B. *UbuntuLTS* oder *CentOS*, den Namen Ihres benutzerdefinierten VM-Images an. Im folgenden Beispiel wird eine Skalierungsgruppe mit dem Namen *myScaleSet* erstellt, für die das benutzerdefinierte Image mit dem Namen *myImage* aus dem vorherigen Schritt verwendet wird:

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image myImage \
  --admin-username azureuser \
  --generate-ssh-keys
```

Die Erstellung und Konfiguration aller Ressourcen und virtuellen Computer der Skalierungsgruppe dauert einige Minuten.


## <a name="test-your-scale-set"></a>Testen Ihrer Skalierungsgruppe
Erstellen Sie mit [az network lb rule create](/cli/azure/network/lb/rule) eine Lastenausgleichsregel, damit Datenverkehr Ihre Skalierungsgruppe erreichen und überprüft werden kann, dass der Webserver richtig funktioniert. Im folgenden Beispiel wird eine Regel mit dem Namen *myLoadBalancerRuleWeb* erstellt, die Datenverkehr über *TCP*-Port *80* zulässt:

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

Um Ihre Skalierungsgruppe in Aktion zu sehen, rufen Sie mit [az network public-ip show](/cli/azure/network/public-ip) die öffentliche IP-Adresse des Lastenausgleichsmoduls ab. Im folgenden Beispiel wird die IP-Adresse für *myScaleSetLBPublicIP* abgerufen, die als Teil der Skalierungsgruppe erstellt wurde:

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroup \
  --name myScaleSetLBPublicIP \
  --query [ipAddress] \
  --output tsv
```

Geben Sie die öffentliche IP-Adresse in Ihren Webbrowser ein. Die NGINX-Standardwebseite wird angezeigt, wie im folgenden Beispiel dargestellt:

![NGINX-Ausführung über das benutzerdefinierte VM-Image](media/tutorial-use-custom-image-cli/default-nginx-website.png)


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen
Löschen Sie die Ressourcengruppe und alle dazugehörigen Ressourcen mit [az group delete](/cli/azure/group), um Ihre Skalierungsgruppe und die weiteren Ressourcen zu entfernen. Der Parameter `--no-wait` gibt die Steuerung an die Eingabeaufforderung zurück, ohne zu warten, bis der Vorgang abgeschlossen ist. Der Parameter `--yes` bestätigt ohne eine zusätzliche Aufforderung, dass Sie die Ressourcen löschen möchten.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>Nächste Schritte
In diesem Tutorial wurde beschrieben, wie Sie ein benutzerdefiniertes VM-Image für Ihre Skalierungsgruppen mit der Azure CLI erstellen und verwenden:

> [!div class="checklist"]
> * Erstellen und Anpassen einer VM
> * Aufheben der Bereitstellung und Generalisieren der VM
> * Erstellen eines benutzerdefinierten VM-Images
> * Bereitstellen einer Skalierungsgruppe, für die das benutzerdefinierte VM-Image verwendet wird

Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie Anwendungen in Ihrer Skalierungsgruppe bereitstellen.

> [!div class="nextstepaction"]
> [Bereitstellen von Anwendungen in Ihren Skalierungsgruppen](tutorial-install-apps-cli.md)
