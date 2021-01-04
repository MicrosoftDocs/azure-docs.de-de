---
title: 'Tutorial: Erstellen von benutzerdefinierten VM-Images mit der Azure-Befehlszeilenschnittstelle'
description: In diesem Tutorial erfahren Sie, wie Sie die Azure-Befehlszeilenschnittstelle zum Erstellen eines benutzerdefinierten VM-Images in Azure verwenden.
author: cynthn
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.reviewer: akjosh
ms.openlocfilehash: 22609465abfa0cbf30165bc9327d786b3244357e
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844777"
---
# <a name="tutorial-create-a-custom-image-of-an-azure-vm-with-the-azure-cli"></a>Tutorial: Erstellen eines benutzerdefinierten Images eines virtuellen Azure-Computers mit der Azure CLI

Benutzerdefinierte Images sind wie Marketplace-Images, Sie erstellen sie jedoch selbst. Benutzerdefinierte Images können zum Starten von Konfigurationen verwendet werden, z.B. zum Vorabladen von Anwendungen, Anwendungskonfigurationen und anderen Betriebssystemkonfigurationen. In diesem Tutorial erstellen Sie ein eigenes benutzerdefiniertes Image eines virtuellen Azure-Computers. Folgendes wird vermittelt:

> [!div class="checklist"]
> * Erstellen einer Shared Image Gallery-Instanz
> * Erstellen einer Imagedefinition
> * Erstellen einer Imageversion
> * Erstellen eines virtuellen Computers aus einem Image 
> * Freigeben eines Imagekatalogs


Dieses Tutorial verwendet die CLI innerhalb des Diensts [Azure Cloud Shell](../../cloud-shell/overview.md), der ständig auf die neueste Version aktualisiert wird. Wählen Sie zum Öffnen von Cloud Shell oben in einem Codeblock die Option **Ausprobieren** aus.

Wenn Sie die CLI lokal installieren und verwenden möchten, müssen Sie für dieses Tutorial mindestens die Version 2.4.0 der Azure CLI ausführen. Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI]( /cli/azure/install-azure-cli).

## <a name="overview"></a>Übersicht

Der [Katalog mit freigegebenen Images](shared-image-galleries.md) vereinfacht das Freigeben benutzerdefinierter Images in Ihrer Organisation. Benutzerdefinierte Images sind wie Marketplace-Images, Sie erstellen sie jedoch selbst. Benutzerdefinierte Images können zum Starten von Konfigurationen verwendet werden, z.B. zum Vorabladen von Anwendungen, Anwendungskonfigurationen und anderen Betriebssystemkonfigurationen. 

Shared Image Gallery ermöglicht Ihnen die Freigabe Ihrer benutzerdefinierten VM-Images für andere Benutzer. Wählen Sie aus, welche Images Sie teilen möchten, in welchen Regionen Sie sie verfügbar machen möchten, und mit wem Sie sie teilen möchten. 

Die Funktion „Katalog mit geteilten Images“ verfügt über mehrere Ressourcentypen:

[!INCLUDE [virtual-machines-shared-image-gallery-resources](../../../includes/virtual-machines-shared-image-gallery-resources.md)]

## <a name="before-you-begin"></a>Voraussetzungen

In den folgenden Schritten wird erläutert, wie Sie eine vorhandene VM in ein wiederverwendbares benutzerdefiniertes Image umwandeln, mit dem Sie neue VM-Instanzen erstellen können.

Für das Beispiel in diesem Tutorial muss ein virtueller Computer vorhanden sein. Sehen Sie sich ggf. die [Schnellstartanleitung zur Befehlszeilenschnittstelle](quick-create-cli.md) an, um einen virtuellen Computer für dieses Tutorial zu erstellen. Ersetzen Sie beim Durcharbeiten des Tutorials bei Bedarf die Ressourcennamen.

## <a name="launch-azure-cloud-shell"></a>Starten von Azure Cloud Shell

Azure Cloud Shell ist eine kostenlose interaktive Shell, mit der Sie die Schritte in diesem Artikel ausführen können. Sie verfügt über allgemeine vorinstallierte Tools und ist für die Verwendung mit Ihrem Konto konfiguriert. 

Wählen Sie zum Öffnen von Cloud Shell oben rechts in einem Codeblock einfach die Option **Ausprobieren**. Sie können Cloud Shell auch auf einer separaten Browserregisterkarte starten, indem Sie zu [https://shell.azure.com/powershell](https://shell.azure.com/powershell) navigieren. Wählen Sie **Kopieren**, um die Blöcke mit dem Code zu kopieren. Fügen Sie ihn anschließend in Cloud Shell ein, und drücken Sie die EINGABETASTE, um ihn auszuführen.

## <a name="create-an-image-gallery"></a>Erstellen eines Imagekatalogs 

Ein Imagekatalog ist die primäre Ressource, die zur Ermöglichung des Teilens von Images verwendet wird. 

Zulässige Zeichen für Katalognamen sind Groß- und Kleinbuchstaben, Zahlen und Punkte. Der Katalogname darf keine Bindestriche enthalten.   Katalognamen müssen innerhalb Ihres Abonnements eindeutig sein. 

Erstellen Sie einen Imagekatalog mit [az sig create](/cli/azure/sig#az-sig-create). Im folgenden Beispiel werden eine Ressourcengruppe namens *myGalleryRG* in der Region *USA, Osten* und ein Katalog namens *myGallery* erstellt.

```azurecli-interactive
az group create --name myGalleryRG --location eastus
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

## <a name="get-information-about-the-vm"></a>Abrufen von Informationen zur VM

Eine Liste der verfügbaren VMs kann mithilfe von [az vm list](/cli/azure/vm#az-vm-list) angezeigt werden. 

```azurecli-interactive
az vm list --output table
```

Wenn Sie den Namen des virtuellen Computers kennen und wissen, in welcher Ressourcengruppe er sich befindet, rufen Sie die ID des virtuellen Computers mithilfe von [az vm get-instance-view](/cli/azure/vm#az-vm-get-instance-view) ab. 

```azurecli-interactive
az vm get-instance-view -g MyResourceGroup -n MyVm --query id
```

Kopieren Sie die ID des virtuellen Computers zur späteren Verwendung.

## <a name="create-an-image-definition"></a>Erstellen einer Imagedefinition

Imagedefinitionen erstellen eine logische Gruppierung von Images. Sie werden verwendet, um Informationen über die Imageversionen zu verwalten, die in ihnen erstellt werden. 

Namen für Imagedefinition können aus Groß- und Kleinbuchstaben, Zahlen, Punkten und (Binde)Strichen bestehen. 

Weitere Informationen zu den Werten, die Sie für eine Imagedefinition angeben können, finden Sie unter [Imagedefinitionen](./shared-image-galleries.md#image-definitions).

Erstellen Sie mithilfe von [az sig image-definition create](/cli/azure/sig/image-definition#az-sig-image-definition-create) eine Imagedefinition im Katalog. 

In diesem Beispiel heißt die Imagedefinition *myImageDefinition* und ist für ein [spezialisiertes](./shared-image-galleries.md#generalized-and-specialized-images) Linux-Betriebssystemimage gedacht. 

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

Kopieren Sie die ID der Imagedefinition in der Ausgabe zur späteren Verwendung.

## <a name="create-the-image-version"></a>Erstellen der Imageversion

Erstellen Sie mithilfe von [az image gallery create-image-version](/cli/azure/sig/image-version#az-sig-image-version-create) eine Imageversion der VM.  

Zulässige Zeichen für die Imageversion sind Zahlen und Punkte. Zahlen müssen im Bereich einer ganzen 32-Bit-Zahl liegen. Format: *Hauptversion*.*Nebenversion*.*Patch*.

In diesem Beispiel wird ein Image der Version *1.0.0* verwendet, und es werden unter Verwendung bon zonenredundantem Speicher zwei Replikate in der Region *USA, Westen-Mitte*, ein Replikat in der Region *USA, Süden-Mitte* und ein Replikat in der Region *USA, Osten 2* erstellt. Die Replikationsregionen müssen die Region beinhalten, in der sich der virtuelle Quellcomputer befindet.

Ersetzen Sie den Wert `--managed-image` in diesem Beispiel durch die ID Ihrer VM aus dem vorherigen Schritt.

```azurecli-interactive 
az sig image-version create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "westcentralus" "southcentralus=1" "eastus=1=standard_zrs" \
   --replica-count 2 \
   --managed-image "/subscriptions/<Subscription ID>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM"
```

> [!NOTE]
> Sie müssen warten, bis die Imageversion vollständig erstellt und repliziert wurde, bevor Sie dieses verwaltete Image verwenden können, um eine weitere Imageversion zu erstellen.
>
> Fügen Sie beim Erstellen der Imageversion entweder `--storage-account-type  premium_lrs` hinzu, um Ihr Image in Premium-Speicher zu speichern, oder `--storage-account-type  standard_zrs`, um Ihr Image in [zonenredundantem Speicher](../../storage/common/storage-redundancy.md) zu speichern.
>

 
## <a name="create-the-vm"></a>Erstellen des virtuellen Computers

Erstellen Sie den virtuellen Computer mithilfe von [az vm create](/cli/azure/vm#az-vm-create) und unter Verwendung des Parameters „--specialized“, um anzugeben, dass es sich um ein spezialisiertes Image handelt. 

Verwenden Sie die Imagedefinitions-ID für `--image`, um die VM auf der Grundlage der neuesten verfügbaren Imageversion zu erstellen. Sie können die VM auch auf der Grundlage einer bestimmten Version erstellen, indem Sie die Imageversions-ID für `--image` angeben. 

In diesem Beispiel erstellen Sie einen virtuellen Computer auf der Grundlage der aktuellen Version des Images *myImageDefinition*.

```azurecli
az group create --name myResourceGroup --location eastus
az vm create --resource-group myResourceGroup \
    --name myVM \
    --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
    --specialized
```

## <a name="share-the-gallery"></a>Teilen des Katalogs

Sie können Images zwischen Abonnements mithilfe der rollenbasierten Zugriffssteuerung von Azure (Azure Role-Based Access Control, Azure RBAC) teilen. Sie können Images auf der Ebene des Katalogs, der Imagedefinition oder der Imageversion freigeben. Jeder Benutzer, der Leseberechtigungen für eine Imageversion besitzt (auch über Abonnements hinweg), kann mithilfe der Imageversion einen virtuellen Computer bereitstellen.

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

Weitere Informationen zum Teilen von Ressourcen mithilfe der Azure RBAC finden Sie unter [Hinzufügen oder Entfernen von Azure-Rollenzuweisungen mithilfe der Azure CLI](../../role-based-access-control/role-assignments-cli.md).

## <a name="azure-image-builder"></a>Azure Image Builder

Azure bietet darüber hinaus den auf Packer basierenden Dienst [Azure VM Image Builder](./image-builder-overview.md). Beschreiben Sie einfach Ihre Anpassungen in einer Vorlage, und diese übernimmt die Imageerstellung. 

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie ein benutzerdefiniertes Image eines virtuellen Computers erstellt. Sie haben Folgendes gelernt:

> [!div class="checklist"]
> * Erstellen einer Shared Image Gallery-Instanz
> * Erstellen einer Imagedefinition
> * Erstellen einer Imageversion
> * Erstellen eines virtuellen Computers aus einem Image 
> * Freigeben eines Imagekatalogs

Im nächsten Tutorial erhalten Sie Informationen zu hoch verfügbaren virtuellen Computern.

> [!div class="nextstepaction"]
> [Erstellen eines hoch verfügbaren virtuellen Computers](tutorial-availability-sets.md)
