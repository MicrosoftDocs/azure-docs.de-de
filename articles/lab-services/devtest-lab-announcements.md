---
title: Posten einer Ankündigung in einem Lab in Azure DevTest Labs | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie eine Ankündigung in einem Lab in Azure DevTest Labs posten.
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: 67a09946-4584-425e-a94c-abe57c9cbb82
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 2fe31271fa84bc4170bd431a4aadbcafc0df9086
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60311475"
---
# <a name="post-an-announcement-to-a-lab-in-azure-devtest-labs"></a>Posten einer Ankündigung in einem Lab in Azure DevTest Labs

Als Lab-Administrator können Sie eine benutzerdefinierte Ankündigung in einem vorhandenen Lab posten, um Benutzer über kürzlich vorgenommene Änderungen oder Hinzufügungen zum Lab zu benachrichtigen. Es kann beispielsweise sein, dass Sie Benutzer über Folgendes informieren möchten:

- Neue verfügbare VM-Größen
- Derzeit nicht verwendbare Images
- Aktualisierungen von Lab-Richtlinien

Nach dem Posten wird die Ankündigung auf der Seite „Übersicht“ des Labs angezeigt und kann von Benutzern ausgewählt werden, um weitere Details einzublenden.

Das Ankündigungsfeature ist für vorübergehende Ankündigungen gedacht.  Sie können eine Ankündigung leicht deaktivieren, wenn sie nicht mehr benötigt wird.

## <a name="steps-to-post-an-announcement-in-an-existing-lab"></a>Schritte zum Bereitstellen einer Ankündigung in einem vorhandenen Lab

1. Melden Sie sich beim [Azure-Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) an.
1. Wählen Sie ggf. **Alle Dienste** und dann in der Liste die Option **DevTest Labs**. (Ihr Lab wird im Dashboard unter Umständen bereits unter **Alle Ressourcen** angezeigt.)
1. Wählen Sie in der Liste mit den Labs das Lab aus, in dem Sie eine Ankündigung posten möchten.
1. Wählen Sie im Bereich **Übersicht** des Labs die Option **Konfiguration und Richtlinien** aus.

    ![Schaltfläche „Konfiguration und Richtlinien“](./media/devtest-lab-announcements/devtestlab-config-and-policies.png)

1. Wählen Sie auf der linken Seite unter **EINSTELLUNGEN** die Option **Labankündigung**.

    ![Schaltfläche „Labankündigung“](./media/devtest-lab-announcements/devtestlab-announcements.png)

1. Legen Sie **Aktiviert** auf **Ja** fest, um eine Nachricht für die Benutzer dieses Labs zu erstellen.

1. Sie können unter **Ablaufdatum** ein Datum und eine Uhrzeit eingeben, nach deren Ablauf die Ankündigung Benutzern nicht mehr angezeigt wird. Wenn Sie kein Ablaufdatum eingeben, wird die Ankündigung erst ausgeblendet, wenn Sie sie deaktivieren.

   > [!NOTE]
   > Nach Ablauf der Ankündigung wird sie Benutzern nicht mehr angezeigt, ist aber noch im Bereich **Labankündigung** vorhanden. Sie können Änderungen daran vornehmen und diese wieder aktivieren.
   >
   >

1. Geben Sie einen **Ankündigungstitel** und den **Ankündigungstext** ein.

   Der Titel kann bis zu 100 Zeichen enthalten und wird auf der Seite „Übersicht“ des Labs für Benutzer angezeigt. Wenn Benutzer den Titel auswählen, wird der Ankündigungstext angezeigt.

   Für den Ankündigungstext kann Markdown verwendet werden. Beim Eingeben des Ankündigungstexts können Sie die Nachricht unten auf dem Bildschirm im Vorschaubereich anzeigen.

    ![Bildschirm mit Labankündigung zur Erstellung der Nachricht](./media/devtest-lab-announcements/devtestlab-post-announcement.png)


1. Wählen Sie **Speichern**, nachdem Ihre Ankündigung bereit zum Posten ist.

Wenn diese Ankündigung den Lab-Benutzern nicht mehr angezeigt werden soll, können Sie auf der Seite **Labankündigung** die Option **Aktiviert** auf **Nein** festlegen. Wenn Sie ein Ablaufdatum angegeben haben, wird die Ankündigung automatisch an diesem Datum bzw. zu dieser Uhrzeit deaktiviert.

## <a name="steps-for-users-to-view-an-announcement"></a>Schritte für Benutzer zum Anzeigen einer Ankündigung

1. Wählen Sie im [Azure-Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) ein Lab aus.

1. Wenn für das Lab eine Ankündigung gepostet wird, wird oben auf der Seite „Übersicht“ des Labs ein Hinweis mit entsprechenden Informationen angezeigt. Dieser Hinweis enthält den Ankündigungstitel, der bei der Erstellung der Ankündigung angegeben wurde.

    ![Labankündigung auf der Seite „Übersicht“](./media/devtest-lab-announcements/devtestlab-user-announcement.png)

1. Der Benutzer kann die Nachricht auswählen, um die gesamte Ankündigung anzuzeigen.

    ![Weitere Informationen zur Labankündigung](./media/devtest-lab-announcements/devtestlab-user-announcement-text.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="azure-resource-manager-template"></a>Azure Resource Manager-Vorlage
Sie können eine Ankündigung als Teil einer Azure Resource Manager-Vorlage angeben, wie im folgenden Beispiel gezeigt:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "string",
            "defaultValue": "devtestlabfromarm"
        },
        "regionId": {
            "type": "string",
            "defaultValue": "eastus"
        }
    },
    "resources": [
        {
            "apiVersion": "2017-04-26-preview",
            "name": "[parameters('name')]",
            "type": "Microsoft.DevTestLab/labs",
            "location": "[parameters('regionId')]",
            "tags": {},
            "properties": {
                "labStorageType": "Premium",
                "announcement":
                {
                    "title": "Important! Three images are currently inaccessible. Click for more information.",
                    "markdown":"# Images are inaccessible\n\nThe following 3 images are currently not available for use: \n\n- image1\n- image2\n- image3\n\nI am working to fix the problem ASAP.",
                    "enabled": "Enabled",
                    "expirationDate":"2018-12-31T06:00:00+00:00",
                    "expired": "false"
                },
                "support": {
                    "markdown": "",
                    "enabled": "Enabled"
                }
            },
            "resources": [
                {
                    "apiVersion": "2017-04-26-preview",
                    "name": "LabVmsShutdown",
                    "location": "[parameters('regionId')]",
                    "type": "schedules",
                    "dependsOn": [
                        "[resourceId('Microsoft.DevTestLab/labs', parameters('name'))]"
                    ],
                    "properties": {
                        "status": "Enabled",
                        "timeZoneId": "Eastern Standard Time",
                        "dailyRecurrence": {
                            "time": "1900"
                        },
                        "taskType": "LabVmsShutdownTask",
                        "notificationSettings": {
                            "status": "Disabled",
                            "timeInMinutes": 30
                        }
                    }
                },
                {
                    "apiVersion": "2017-04-26-preview",
                    "name": "[concat('Dtl', parameters('name'))]",
                    "type": "virtualNetworks",
                    "location": "[parameters('regionId')]",
                    "dependsOn": [
                        "[resourceId('Microsoft.DevTestLab/labs', parameters('name'))]"
                    ]
                }
            ]
        }
    ]
}
```

Sie können eine Azure Resource Manager-Vorlage auf eine der folgenden Arten bereitstellen:

- [Azure-Portal](../azure-resource-manager/resource-group-template-deploy-portal.md)
- [Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
- [Azure-Befehlszeilenschnittstelle](../azure-resource-manager/resource-group-template-deploy-cli.md)
- [REST-API](../azure-resource-manager/resource-group-template-deploy-rest.md)

## <a name="next-steps"></a>Nächste Schritte
* Wenn Sie eine Lab-Richtlinie ändern oder festlegen, kann es ratsam sein, eine Ankündigung zu posten, um Benutzer zu informieren. [Festlegen von Richtlinien und Zeitplänen](devtest-lab-set-lab-policy.md) enthält Informationen zum Anwenden von Einschränkungen und Konventionen für Ihr gesamtes Abonnement mit benutzerdefinierten Richtlinien.
* Erkunden Sie den [DevTest Labs-Azure Resource Manager-Katalog mit Schnellstartvorlagen](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates).
