---
title: Erfassen eines Image einer Linux-VM in Azure mit Azure CLI | Microsoft-Dokumentation
description: Erfassen Sie mit der Azure-Befehlszeilenschnittstelle (Azure CLI) ein Image eines virtuellen Azure-Computers, das für Massenbereitstellungen verwendet werden soll.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: e608116f-f478-41be-b787-c2ad91b5a802
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 10/08/2018
ms.author: cynthn
ms.openlocfilehash: 5022d765b5dfa4f1f973b7fb4370d5314bb887b8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60542867"
---
# <a name="how-to-create-an-image-of-a-virtual-machine-or-vhd"></a>Vorgehensweise zum Erstellen eines Image von einem virtuellen Computer oder einer VHD

<!-- generalize, image - extended version of the tutorial-->

Um mehrere Kopien eines virtuellen Computers (Virtual Machine, VM) für die Verwendung in Azure zu erstellen, erfassen Sie ein Image des virtuellen Computers oder der Betriebssystem-VHD. Wenn Sie ein Image für die Bereitstellung erstellen möchten, müssen Sie persönliche Kontoinformationen entfernen. In den folgenden Schritten heben Sie die Bereitstellung eines vorhandenen virtuellen Computers auf, heben dessen Zuordnung auf und erstellen ein Image. Sie können dieses Image verwenden, um virtuelle Computer über jede Ressourcengruppe innerhalb Ihres Abonnements hinweg zu erstellen.

Wenn Sie zum Sichern oder Debuggen eine Kopie Ihres vorhandenen virtuellen Linux-Computers erstellen oder eine spezielle Linux-VHD über einen lokalen virtuellen Computer hochladen möchten, lesen Sie [Hochladen und Erstellen eines virtuellen Linux-Computers aus einem benutzerdefinierten Datenträgerimage](upload-vhd.md).  

Sie können Ihre benutzerdefinierte Konfiguration auch mit **Packer** erstellen. Weitere Informationen finden Sie unter [Vorgehensweise zum Erstellen von Images von virtuellen Linux-Computern in Azure mit Packer](build-image-with-packer.md).

Damit Sie ein Image erstellen können, müssen die folgenden Voraussetzungen erfüllt sein:

* Im Resource Manager-Bereitstellungsmodell muss ein virtueller Azure-Computer mit verwalteten Datenträgern erstellt worden sein. Wenn Sie noch keinen virtuellen Linux-Computer erstellt haben, können Sie das [Portal](quick-create-portal.md), die [Azure-Befehlszeilenschnittstelle](quick-create-cli.md) (Azure CLI) oder [Resource Manager-Vorlagen](create-ssh-secured-vm-from-template.md) dazu verwenden. Konfigurieren Sie die VM den Anforderungen entsprechend. Sie können beispielsweise [Datenträger hinzufügen](add-disk.md), Updates einspielen und Anwendungen installieren. 

* Die neueste Version von [Azure CLI](/cli/azure/install-az-cli2) muss installiert sein, und Sie müssen mithilfe von [az login](/cli/azure/reference-index#az-login) bei einem Azure-Konto angemeldet sein.

## <a name="quick-commands"></a>Schnellbefehle

Eine vereinfachte Version dieses Artikels sowie Informationen zum Testen und Auswerten oder zu VMs in Azure finden Sie unter [Erstellen eines benutzerdefinierten Images eines virtuellen Azure-Computers mit der Azure-Befehlszeilenschnittstelle](tutorial-custom-images.md).


## <a name="step-1-deprovision-the-vm"></a>Schritt 1: Aufheben der Bereitstellung des virtuellen Computers
Zuerst heben Sie die Bereitstellung des virtuellen Computers mithilfe des Azure-VM-Agents auf, um computerspezifische Dateien und Daten zu löschen. Verwenden Sie auf dem virtuellen Linux-Quellcomputer den Befehl `waagent` mit dem Parameter `-deprovision+user`. Weitere Informationen erhalten Sie im [Benutzerhandbuch für Azure Linux-Agent](../extensions/agent-linux.md).

1. Stellen Sie mit einem SSH-Client eine Verbindung mit Ihrem virtuellen Linux-Computer her.
2. Geben Sie im SSH-Fenster den folgenden Befehl ein:
   
    ```bash
    sudo waagent -deprovision+user
    ```
   > [!NOTE]
   > Führen Sie diesen Befehl nur auf einem virtuellen Computer aus, den Sie als Image erfassen möchten. Die Ausführung dieses Befehls garantiert nicht, dass alle vertraulichen Informationen aus dem Image gelöscht werden oder dass es für eine erneute Verteilung geeignet ist. Der Parameter `+user` entfernt auch das zuletzt bereitgestellte Benutzerkonto. Wenn die Anmeldeinformationen des Benutzerkontos auf dem virtuellen Computer verbleiben sollen, verwenden Sie nur `-deprovision`.
 
3. Geben Sie **y** ein, um fortzufahren. Sie können den Parameter `-force` hinzufügen, um diesen Bestätigungsschritt zu vermeiden.
4. Geben Sie nach der Ausführung dieses Befehls den Befehl **exit** ein, um den SSH-Client zu schließen.

## <a name="step-2-create-vm-image"></a>Schritt 2: Erstellen des VM-Image
Verwenden Sie Azure CLI, um die VM als generalisiert zu kennzeichnen und das Image zu erfassen. Ersetzen Sie in den folgenden Beispielen die Beispielparameternamen durch Ihre eigenen Werte. Beispielparameternamen sind u.a. *myResourceGroup*, *myVnet* und *myVM*.

1. Heben Sie die Zuordnung des virtuellen Computers auf, dessen Bereitstellung Sie mit [az vm deallocate](/cli/azure/vm) aufgehoben haben. Im folgenden Beispiel wird die Zuordnung des virtuellen Computers *myVM* in der Ressourcengruppe *myResourceGroup* aufgehoben.
   
    ```azurecli
    az vm deallocate \
      --resource-group myResourceGroup \
      --name myVM
    ```

2. Kennzeichnen Sie die VM mit [az vm generalize](/cli/azure/vm) als generalisiert. Im folgenden Beispiel wird der virtuelle Computer *myVM* in der Ressourcengruppe *myResourceGroup* als generalisiert gekennzeichnet.
   
    ```azurecli
    az vm generalize \
      --resource-group myResourceGroup \
      --name myVM
    ```

3. Erstellen Sie ein Image der VM-Ressource mit [az image create](/cli/azure/image#az-image-create). Im folgenden Beispiel wird ein Image mit dem Namen *myImage* in der Ressourcengruppe *myResourceGroup* mit der VM-Ressource *myVM* erstellt.
   
    ```azurecli
    az image create \
      --resource-group myResourceGroup \
      --name myImage --source myVM
    ```
   
   > [!NOTE]
   > Das Image wird in derselben Ressourcengruppe wie der virtuelle Quellcomputer erstellt. Sie können aus diesem Image virtuelle Computer in einer beliebigen Ressourcengruppe in Ihrem Abonnement erstellen. Angesichts der Verwaltung können Sie eine spezifische Ressourcengruppe für die VM-Ressourcen und Images erstellen.
   >
   > Wenn Sie das Image in Speicher mit Zonenresilienz speichern möchten, müssen Sie es in einer Region erstellen, die [Verfügbarkeitszonen](../../availability-zones/az-overview.md) unterstützt, und den `--zone-resilient true`-Parameter einbeziehen.

## <a name="step-3-create-a-vm-from-the-captured-image"></a>Schritt 3: Bereitstellen eines virtuellen Computers anhand des erfassten Images
Erstellen Sie anhand des erstellten Images einen virtuellen Computer mit [az vm create](/cli/azure/vm). Im folgenden Beispiel wird ein virtueller Computer mit dem Namen *myVMDeployed* anhand des Images *myImage* erstellt.

```azurecli
az vm create \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --image myImage\
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```

### <a name="creating-the-vm-in-another-resource-group"></a>Erstellen der VM in einer anderen Ressourcengruppe 

Sie können VMs aus einem Image in jeder Ressourcengruppe innerhalb Ihres Abonnements erstellen. Zum Erstellen eines virtuellen Computers in einer anderen Ressourcengruppe als dem Image geben Sie die vollständige Ressourcen-ID im Image an. Verwenden Sie [az image list](/cli/azure/image#az-image-list), um eine Liste von Images anzuzeigen. Die Ausgabe sieht in etwa wie das folgende Beispiel aus:

```json
"id": "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage",
   "location": "westus",
   "name": "myImage",
```

Im folgenden Beispiel wird [az vm create](/cli/azure/vm#az-vm-create) verwendet, um einen virtuellen Computer in einer anderen Ressourcengruppe als das Quellimage zu erstellen, indem die Imageressourcen-ID angegeben wird.

```azurecli
az vm create \
   --resource-group myOtherResourceGroup \
   --name myOtherVMDeployed \
   --image "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage" \
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```


## <a name="step-4-verify-the-deployment"></a>Schritt 4: Überprüfen der Bereitstellung

Stellen Sie über SSH eine Verbindung mit dem von Ihnen erstellten virtuellen Computer her, um die Bereitstellung zu überprüfen und den neuen virtuellen Computer zu verwenden. Zum Herstellen einer Verbindung über SSH ermitteln Sie die IP-Adresse oder den FQDN Ihres virtuellen Computers mit [az vm show](/cli/azure/vm#az-vm-show).

```azurecli
az vm show \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --show-details
```

## <a name="next-steps"></a>Nächste Schritte
Sie können mehrere virtuelle Computer aus dem Image des virtuellen Quellcomputers erstellen. Gehen Sie wie folgt vor, um Änderungen an Ihrem Image vorzunehmen: 

- Erstellen Sie einen virtuellen Computer aus Ihrem Image.
- Nehmen Sie sämtliche Updates oder Änderungen an der Konfiguration vor.
- Führen Sie erneut die Schritte zum Aufheben der Bereitstellung, zum Aufheben der Zuordnung, zum Generalisieren und zum Erstellen eines Images aus.
- Verwenden Sie dieses neue Image für zukünftige Bereitstellungen. Sie können das ursprüngliche Image löschen.

Weitere Informationen zum Verwalten Ihrer virtuellen Computer mit der Befehlszeilenschnittstelle finden Sie unter [Azure CLI](/cli/azure).
