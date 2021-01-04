---
title: 'Tutorial: Verwenden eines benutzerdefinierten VM-Images in einer Skalierungsgruppe mit der Azure CLI'
description: Erfahren Sie, wie Sie die Azure CLI zum Erstellen eines benutzerdefinierten VM-Images verwenden, das Sie zum Bereitstellen einer VM-Skalierungsgruppe nutzen können.
author: cynthn
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.topic: tutorial
ms.date: 05/01/2020
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.reviewer: akjosh
ms.openlocfilehash: 62cf7c979be83454ae2433befcdbf4f5d8e5524f
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94516542"
---
# <a name="tutorial-create-and-use-a-custom-image-for-virtual-machine-scale-sets-with-the-azure-cli"></a>Tutorial: Erstellen und Verwenden eines benutzerdefinierten Images für VM-Skalierungsgruppen mit der Azure CLI
Wenn Sie eine Skalierungsgruppe erstellen, geben Sie ein Image an, das beim Bereitstellen der VM-Instanzen verwendet wird. Sie können ein benutzerdefiniertes VM-Image verwenden, um die Anzahl von Aufgaben zu reduzieren, nachdem VM-Instanzen bereitgestellt wurden. Dieses benutzerdefinierte VM-Image enthält alle erforderlichen Anwendungsinstallationen oder -konfigurationen. Für alle VM-Instanzen, die in der Skalierungsgruppe erstellt werden, wird das benutzerdefinierte VM-Image verwendet, und die VM-Instanzen sind für die Bereitstellung Ihres Anwendungsdatenverkehrs bereit. In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen einer Shared Image Gallery-Instanz
> * Erstellen der Definition eines spezialisierten Images
> * Erstellen einer Imageversion
> * Erstellen einer Skalierungsgruppe auf der Grundlage eines spezialisierten Images
> * Freigeben eines Imagekatalogs


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Für diesen Artikel ist mindestens Version 2.4.0 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

## <a name="overview"></a>Übersicht

Der [Katalog mit freigegebenen Images](shared-image-galleries.md) vereinfacht das Freigeben benutzerdefinierter Images in Ihrer Organisation. Benutzerdefinierte Images sind wie Marketplace-Images, Sie erstellen sie jedoch selbst. Benutzerdefinierte Images können zum Starten von Konfigurationen verwendet werden, z.B. zum Vorabladen von Anwendungen, Anwendungskonfigurationen und anderen Betriebssystemkonfigurationen. 

Shared Image Gallery ermöglicht Ihnen die Freigabe Ihrer benutzerdefinierten VM-Images für andere Benutzer. Wählen Sie aus, welche Images Sie teilen möchten, in welchen Regionen Sie sie verfügbar machen möchten, und mit wem Sie sie teilen möchten. 

## <a name="create-and-configure-a-source-vm"></a>Erstellen und Konfigurieren einer Quell-VM

Erstellen Sie zunächst mit [az group create](/cli/azure/group) eine Ressourcengruppe und dann mit [az vm create](/cli/azure/vm) eine VM. Diese VM wird dann als Quelle für das Image verwendet. Im folgenden Beispiel wird eine VM mit dem Namen *myVM* in der Ressourcengruppe *myResourceGroup* erstellt:

```azurecli-interactive
az group create --name myResourceGroup --location eastus

az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image ubuntults \
  --admin-username azureuser \
  --generate-ssh-keys
```

> [!IMPORTANT]
> Die **ID** Ihrer VM wird in der Ausgabe des Befehls [az vm create](/cli/azure/vm) angezeigt. Kopieren Sie diese an einen sicheren Speicherort, damit Sie sie später in diesem Tutorial verwenden können.

Die öffentliche IP-Adresse Ihrer VM wird ebenfalls in der Ausgabe des Befehls [az vm create](/cli/azure/vm) angezeigt. Stellen Sie wie folgt eine SSH-Verbindung mit der öffentlichen IP-Adresse Ihrer VM her:

```console
ssh azureuser@<publicIpAddress>
```

Installieren Sie einen einfachen Webserver, um Ihre VM anzupassen. Wenn die VM-Instanz in der Skalierungsgruppe bereitgestellt wird, verfügt sie über alle erforderlichen Pakete zum Ausführen einer Webanwendung. Verwenden Sie `apt-get` wie folgt, um *NGINX* zu installieren:

```bash
sudo apt-get install -y nginx
```

Geben Sie anschließend `exit` ein, um die SSH-Verbindung zu trennen.

## <a name="create-an-image-gallery"></a>Erstellen eines Imagekatalogs 

Ein Imagekatalog ist die primäre Ressource, die zur Ermöglichung des Teilens von Images verwendet wird. 

Zulässige Zeichen für Katalognamen sind Groß- und Kleinbuchstaben, Zahlen und Punkte. Der Katalogname darf keine Bindestriche enthalten.   Katalognamen müssen innerhalb Ihres Abonnements eindeutig sein. 

Erstellen Sie einen Imagekatalog mit [az sig create](/cli/azure/sig#az-sig-create). Im folgenden Beispiel werden eine Ressourcengruppe namens *myGalleryRG* in der Region *USA, Osten* und ein Katalog namens *myGallery* erstellt.

```azurecli-interactive
az group create --name myGalleryRG --location eastus
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

## <a name="create-an-image-definition"></a>Erstellen einer Imagedefinition

Imagedefinitionen erstellen eine logische Gruppierung von Images. Sie werden verwendet, um Informationen über die Imageversionen zu verwalten, die in ihnen erstellt werden. 

Namen für Imagedefinition können aus Groß- und Kleinbuchstaben, Zahlen, Punkten und (Binde)Strichen bestehen. 

Stellen Sie sicher, dass die Imagedefinition den richtigen Typ aufweist. Wenn Sie die VM generalisiert haben (über Sysprep für Windows oder „waagent -deprovision“ für Linux) sollten Sie unter Verwendung von `--os-state generalized` eine generalisierte Imagedefinition erstellen. Wenn Sie die VM verwenden möchten, ohne vorhandene Benutzerkonten zu entfernen, erstellen Sie mithilfe von `--os-state specialized` eine spezialisierte Imagedefinition.

Weitere Informationen zu den Werten, die Sie für eine Imagedefinition angeben können, finden Sie unter [Imagedefinitionen](../virtual-machines/linux/shared-image-galleries.md#image-definitions).

Erstellen Sie mithilfe von [az sig image-definition create](/cli/azure/sig/image-definition#az-sig-image-definition-create) eine Imagedefinition im Katalog.

In diesem Beispiel heißt die Imagedefinition *myImageDefinition* und ist für ein [spezialisiertes](../virtual-machines/linux/shared-image-galleries.md#generalized-and-specialized-images) Linux-Betriebssystemimage vorgesehen. Verwenden Sie `--os-type Windows`, um eine Definition für Images zu erstellen, die ein Windows-Betriebssystem verwenden. 

```azurecli-interactive 
az sig image-definition create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --publisher myPublisher \
   --offer myOffer \
   --sku mySKU \
   --os-type Linux \
   --os-state specialized
```

> [!IMPORTANT]
> Die **ID** Ihrer Imagedefinition wird in der Ausgabe des Befehls angezeigt. Kopieren Sie diese an einen sicheren Speicherort, damit Sie sie später in diesem Tutorial verwenden können.


## <a name="create-the-image-version"></a>Erstellen der Imageversion

Erstellen Sie mithilfe von [az image gallery create-image-version](/cli/azure/sig/image-version#az-sig-image-version-create) eine Imageversion der VM.  

Zulässige Zeichen für die Imageversion sind Zahlen und Punkte. Zahlen müssen im Bereich einer ganzen 32-Bit-Zahl liegen. Format: *Hauptversion*.*Nebenversion*.*Patch*.

In diesem Beispiel wird ein Image der Version *1.0.0* verwendet, und es werden zwei Replikate erstellt: eins in der Region *USA, Süden-Mitte* und eins in der Region *USA, Osten 2*. Die Replikationsregionen müssen die Region beinhalten, in der sich der virtuelle Quellcomputer befindet.

Ersetzen Sie den Wert `--managed-image` in diesem Beispiel durch die ID Ihrer VM aus dem vorherigen Schritt.

```azurecli-interactive 
az sig image-version create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "southcentralus=1" "eastus=1" \
   --managed-image "/subscriptions/<Subscription ID>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM"
```

> [!NOTE]
> Sie müssen warten, bis die Imageversion vollständig erstellt und repliziert wurde, bevor Sie dieses verwaltete Image verwenden können, um eine weitere Imageversion zu erstellen.
>
> Fügen Sie beim Erstellen der Imageversion entweder `--storage-account-type  premium_lrs` hinzu, um Ihr Image in Premium-Speicher zu speichern, oder `--storage-account-type  standard_zrs`, um Ihr Image in [zonenredundantem Speicher](../storage/common/storage-redundancy.md) zu speichern.
>




## <a name="create-a-scale-set-from-the-image"></a>Erstellen einer Skalierungsgruppe auf der Grundlage des Images
Erstellen Sie mithilfe von [`az vmss create`](/cli/azure/vmss#az-vmss-create) eine Skalierungsgruppe auf der Grundlage des spezialisierten Images. 

Erstellen Sie die Skalierungsgruppe mithilfe von [`az vmss create`](/cli/azure/vmss#az-vmss-create) und unter Verwendung des Parameters „--specialized“, um anzugeben, dass es sich um ein spezialisiertes Image handelt. 

Verwenden Sie die Imagedefinitions-ID für `--image`, um die Skalierungsgruppeninstanzen auf der Grundlage der neuesten verfügbaren Imageversion zu erstellen. Sie können die Skalierungsgruppeninstanzen auch auf der Grundlage einer bestimmten Version erstellen, indem Sie die Imageversions-ID für `--image` verwenden. 

Erstellen Sie eine Skalierungsgruppe mit dem Namen *myScaleSet* und der neuesten Version des zuvor erstellten Images *myImageDefinition*.

```azurecli
az group create --name myResourceGroup --location eastus
az vmss create \
   --resource-group myResourceGroup \
   --name myScaleSet \
   --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
   --specialized
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



## <a name="share-the-gallery"></a>Teilen des Katalogs

Sie können Images zwischen Abonnements mithilfe der rollenbasierten Zugriffssteuerung von Azure (Azure Role-Based Access Control, Azure RBAC) teilen. Sie können Images über den Katalog, die Imagedefinition oder die Imageversion freigeben. Jeder Benutzer, der Leseberechtigungen für eine Imageversion besitzt (auch über Abonnements hinweg), kann mithilfe der Imageversion einen virtuellen Computer bereitstellen.

Wir empfehlen, dass Sie mit anderen Benutzern auf Ebene des Katalogs teilen. Um die Objekt-ID Ihres Katalogs abzurufen, verwenden Sie [az sig show](/cli/azure/sig#az-sig-show).

```azurecli-interactive
az sig show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --query id
```

Verwenden Sie die Objekt-ID als Bereich, zusammen mit einer E-Mail-Adresse und [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create), um einem Benutzer Zugriff auf den geteilten Imagekatalog zu gewähren. Ersetzen Sie `<email-address>` und `<gallery iD>` durch Ihre eigenen Angaben.

```azurecli-interactive
az role assignment create \
   --role "Reader" \
   --assignee <email address> \
   --scope <gallery ID>
```

Weitere Informationen zum Teilen von Ressourcen mithilfe der Azure RBAC finden Sie unter [Hinzufügen oder Entfernen von Azure-Rollenzuweisungen mithilfe der Azure CLI](../role-based-access-control/role-assignments-cli.md).


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen
Löschen Sie die Ressourcengruppe und alle dazugehörigen Ressourcen mit [az group delete](/cli/azure/group), um Ihre Skalierungsgruppe und die weiteren Ressourcen zu entfernen. Der Parameter `--no-wait` gibt die Steuerung an die Eingabeaufforderung zurück, ohne zu warten, bis der Vorgang abgeschlossen ist. Der Parameter `--yes` bestätigt ohne eine zusätzliche Aufforderung, dass Sie die Ressourcen löschen möchten.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>Nächste Schritte
In diesem Tutorial wurde beschrieben, wie Sie ein benutzerdefiniertes VM-Image für Ihre Skalierungsgruppen mit der Azure CLI erstellen und verwenden:

> [!div class="checklist"]
> * Erstellen einer Shared Image Gallery-Instanz
> * Erstellen der Definition eines spezialisierten Images
> * Erstellen einer Imageversion
> * Erstellen einer Skalierungsgruppe auf der Grundlage eines spezialisierten Images
> * Freigeben eines Imagekatalogs

Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie Anwendungen in Ihrer Skalierungsgruppe bereitstellen.

> [!div class="nextstepaction"]
> [Bereitstellen von Anwendungen in Ihren Skalierungsgruppen](tutorial-install-apps-cli.md)
