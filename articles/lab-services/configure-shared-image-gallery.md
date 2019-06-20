---
title: Konfigurieren eines Katalogs mit freigegebenen Images in Azure DevTest Labs | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie einen Katalog mit freigegebenen Images in Azure DevTest Labs konfigurieren.
services: devtest-lab
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2019
ms.author: spelluru
ms.openlocfilehash: de857498aeb51c9b3711c90338d983e85b61cb70
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67065435"
---
# <a name="configure-a-shared-image-gallery-in-azure-devtest-labs"></a>Konfigurieren eines gemeinsamen Image-Katalogs in Azure DevTest Labs
DevTest Labs unterstützt jetzt die Funktion [Katalog mit freigegebenen Images](../virtual-machines/windows/shared-image-galleries.md). Diese Funktion ermöglicht es Labbenutzern, beim Erstellen von Labressourcen auf Images an einem freigegebenen Speicherort zuzugreifen. Außerdem hilft sie Ihnen, Ihre benutzerdefinierten verwalteten VM-Images zu strukturieren und zu organisieren. Die Funktion „Katalog mit freigegebenen Images“ unterstützt Folgendes:

- Verwaltete globale Replikation von Images.
- Versionsverwaltung und Gruppierung von Images zur einfacheren Verwaltung.
- Hochverfügbarkeit für Images mithilfe von ZRS-Konten (Zone Redundant Storage, zonenredundanter Speicher) in Regionen, die Verfügbarkeitszonen unterstützen. ZRS bietet bessere Ausfallsicherheit bei zonenbezogenen Fehlern.
- Abonnement- und sogar mandantenübergreifende Freigabe von Images mithilfe der rollenbasierten Zugriffssteuerung (Role-Based Access Control, RBAC).

Weitere Informationen finden Sie in der [Dokumentation zum Katalog mit freigegebenen Images](../virtual-machines/windows/shared-image-galleries.md). 
 
Wenn Sie eine große Anzahl verwalteter Images haben, die Sie pflegen müssen und im gesamten Unternehmen zur Verfügung stellen möchten, können Sie einen Katalog mit geteilten Images als Repository verwenden, mit dem sie Ihre Images ganz einfach aktualisieren und teilen können. Als Labbesitzer können Sie einen vorhandenen Katalog mit freigegebenen Images an Ihr Lab anfügen. Nachdem der Katalog angefügt wurde, können Labbenutzer mit diesen aktuellen Images VMs erstellen. Ein Hauptvorteil dieser Funktion ist, dass Azure DevTest Labs Images jetzt lab-, abonnement- und regionsübergreifend freigeben kann. 

## <a name="considerations"></a>Überlegungen
- Sie können jeweils nur einen Katalog mit freigegebenen Images an ein Lab anfügen. Wenn Sie einen anderen Katalog anfügen möchten, müssen Sie zuvor den vorhandenen Katalog trennen. 
- DevTest Labs bietet zurzeit nicht die Möglichkeit, Images über das Lab in den Katalog hochzuladen. 
- Wenn Sie eine VM mit einem Image aus dem Katalog mit freigegebenen Images erstellen, verwendet DevTest Labs immer die aktuelle veröffentlichte Version des Images.
- DevTest Labs versucht sicherzustellen, dass der Katalog mit freigegebenen Images die Images automatisch in die Region des Labs repliziert, dies ist jedoch nicht immer möglich. Stellen Sie daher sicher, dass die Images bereits in die Region des Labs repliziert wurden, damit es nicht zu Problemen kommt, wenn Benutzer diese Images zum Erstellen von VMs verwenden.

## <a name="use-azure-portal"></a>Verwenden des Azure-Portals
1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im linken Navigationsmenü die Option **Alle Dienste** aus.
1. Wählen Sie in der Liste **DevTest Labs** aus.
1. Wählen Sie in der Liste mit den Labs Ihr **Lab** aus.
1. Wählen Sie im Abschnitt **Einstellungen** die Option **Konfiguration und Richtlinien** aus.
1. Wählen Sie im linken Menü unter **Basen virtueller Computer** die Option **Kataloge mit freigegebenen Images** aus.

    ![Menüoption „Kataloge mit freigegebenen Images“](./media/configure-shared-image-gallery/shared-image-galleries-menu.png)
1. Fügen Sie einen vorhandenen Katalog mit freigegebenen Images an Ihr Lab an, indem Sie auf die Schaltfläche **Anfügen** klicken und in der Dropdownliste Ihren Katalog auswählen.

    ![Attach](./media/configure-shared-image-gallery/attach-options.png)
1. Wechseln Sie zu Ihrem angefügten Katalog, und konfigurieren Sie ihn, um freigegebene Images für die Erstellung von VMs zu **aktivieren oder deaktivieren**.

    ![Aktivieren oder Deaktivieren](./media/configure-shared-image-gallery/enable-disable.png)
1. Labbenutzer können dann eine VM mit den aktivierten Images erstellen, indem sie auf **+ Hinzufügen** klicken und auf der Seite **Basis auswählen** nach dem Image suchen.

    ![Labbenutzer](./media/configure-shared-image-gallery/lab-users.png)
## <a name="use-azure-resource-manager-template"></a>Verwenden von Azure Resource Manager-Vorlagen

### <a name="attach-a-shared-image-gallery-to-your-lab"></a>Anfügen eines Katalogs mit freigegebenen Images an Ihr Lab
Wenn Sie eine Azure Resource Manager-Vorlage verwenden, um einen Katalog mit freigegebenen Images an Ihr Lab anzufügen, müssen Sie ihn wie im folgenden Beispiel gezeigt im Ressourcenabschnitt Ihrer Resource Manager-Vorlage hinzufügen:

```json
"resources": [
{
    "apiVersion": "2018-10-15-preview",
    "type": "Microsoft.DevTestLab/labs",
    "name": "[parameters('newLabName')]",
    "location": "[resourceGroup (). location]",
    "resources": [
    {
        "apiVersion": "2018-10-15-preview",
        "name": "[variables('labVirtualNetworkName')]",
        "type": "virtualNetworks",
        "dependsOn": [
            "[resourceId('Microsoft.DevTestLab/labs', parameters('newLabName'))]"
        ]
    },
    {
        "apiVersion":"2018-10-15-preview",
        "name":"myGallery",
        "type":"sharedGalleries",
        "properties": {
            "galleryId":"[parameters('existingSharedGalleryId')]",
            "allowAllImages": "Enabled"
        },
        "dependsOn":[
            "[resourceId('Microsoft.DevTestLab/labs', parameters('newLabName'))]"
        ]
    }
    ]
} 

```

Ein vollständiges Beispiel für eine Resource Manager-Vorlage finden Sie in den Resource Manager-Vorlagenbeispielen in unserem öffentlichen GitHub-Repository: [Konfigurieren eines Katalogs mit freigegebenen Images beim Erstellen eines Labs](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-lab-shared-gallery-configured)

### <a name="create-a-vm-using-an-image-from-the-shared-image-gallery"></a>Erstellen einer VM mit einem Image aus dem Katalog mit freigegebenen Images
Verwenden Sie folgendes Beispiel, wenn Sie mithilfe einer Azure Resource Manager-Vorlage eine VM mit einem Image aus dem Katalog mit freigegebenen Images erstellen:

```json

"resources": [
{
    "apiVersion": "2018-10-15-preview",
    "type": "Microsoft.DevTestLab/labs/virtualMachines",
    "name": "[variables('resourceName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "sharedImageId": "[parameters('existingSharedImageId')]",
        "size": "[parameters('newVMSize')]",
        "isAuthenticationWithSshKey": false,
        "userName": "[parameters('userName')]",
        "sshKey": "",
        "password": "[parameters('password')]",
        "labVirtualNetworkId": "[variables('labVirtualNetworkId')]",
        "labSubnetName": "[variables('labSubnetName')]"
    }
}
],

```

Weitere Informationen finden Sie in den Resource Manager-Vorlagenbeispielen in unserem öffentlichen GitHub-Repository:
[Erstellen einer VM mit einem Image aus dem Katalog mit freigegebenen Images](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-vm-username-pwd-sharedimage)

## <a name="use-api"></a>Verwenden der API

- Verwenden Sie die API-Version 2018_10_15_preview.
- Senden Sie die Anforderung zum Anfügen Ihres Katalogs wie im folgenden Codeausschnitt gezeigt:
    
    ``` 
    PUT [Lab Resource Id]/SharedGalleries/[newGalleryName]
    Body: 
    {
        “properties”:{
            “galleryId”: “[Shared Image Gallery resource Id]”,
            “allowAllImages”:”Enabled”
        }
    }
    ```
- Mit dem folgenden GET-Aufruf können Sie alle Images in Ihrem Katalog mit freigegebenen Images zusammen mit ihren Ressourcen-IDs auflisten:

    ```
    GET [Lab Resource Id]/SharedGalleries/mySharedGallery/SharedImages
    ````
- Um eine VM mit einem freigegebenen Image zu erstellen, können Sie einen PUT-Aufruf auf VMs ausführen und in den VM-Eigenschaften die ID der freigegebenen Images, die Sie mit dem obigen Aufruf abgerufen haben, an „properties.SharedImageId“ übergeben.


## <a name="next-steps"></a>Nächste Schritte
Entsprechende Informationen finden Sie in den folgenden Artikeln zu Artefakten:

- [Angeben verbindlicher Artefakte für Ihr Lab](devtest-lab-mandatory-artifacts.md)
- [Erstellen benutzerdefinierter Artefakte](devtest-lab-artifact-author.md)
- [Hinzufügen eines Artefaktrepositorys zu einem Lab](devtest-lab-artifact-author.md)
- [Diagnostizieren von Artefaktfehlern](devtest-lab-troubleshoot-artifact-failure.md)
