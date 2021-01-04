---
title: Migrieren von Azure-Webressourcen von Azure Deutschland zu Azure weltweit
description: Dieser Artikel enthält Informationen zum Migrieren von Azure-Webressourcen von Azure Deutschland zu Azure weltweit.
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfmigrate
ms.openlocfilehash: d67a4aadbe22b5447aa73063e48c0ca43fb45d04
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95018581"
---
# <a name="migrate-web-resources-to-global-azure"></a>Migrieren von Webressourcen zu Azure weltweit

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

Dieser Artikel enthält Informationen dazu, wie Sie Azure-Webressourcen von Azure Deutschland zu Azure weltweit migrieren können.

## <a name="web-apps"></a>Web-Apps

Migrieren von Apps, die Sie mit dem Web Apps-Feature von Azure App Service erstellt haben, von Azure Deutschland in Azure weltweit wird derzeit nicht unterstützt. Es empfiehlt sich, dass Sie eine Web-App als Azure Resource Manager-Vorlage exportieren. Danach können Sie die App erneut bereitstellen, nachdem Sie die Eigenschaft für den Standort auf die neue Zielregion geändert haben.

> [!IMPORTANT]
> Ändern Sie den Standort, Azure Key Vault-Geheimnisse, Zertifikate und andere GUIDs, damit diese konsistent mit der neuen Region sind.

### <a name="migrate-web-app-resource"></a>Migrieren einer Web-App-Ressource

1. [Exportieren Sie die Web-App und den App Service-Plan als Vorlage](../azure-resource-manager/templates/export-template-portal.md) aus Ihrem Abonnement in Azure Deutschland. Wählen Sie aus der Web-App-Ressourcengruppe die Ressourcen aus, die Sie migrieren möchten, und exportieren Sie diese als Vorlage.
1. Laden Sie die Vorlage als ZIP-Datei herunter. 
1. Ändern Sie die location-Eigenschaft in der Datei **template.json** in eine der weltweiten Azure-Regionen. Die folgende JSON-Datei weist z. B. die Zielregion *USA, Westen* auf.

    ```json
        "resources": [
        {
            "type": "Microsoft.Web/serverfarms",
            "apiVersion": "2018-02-01",
            "name": "[parameters('serverfarms_myappservice_name')]",
            "location": "West US",

    ```
1. Stellen Sie die geänderte Vorlage in Azure weltweit bereit. Für die Bereitstellung können Sie beispielsweise PowerShell verwenden.

    ```powershell
    az deployment group create --name "<web app name>" \
        --resource-group "<resource group name>" \
        --template-file "<path of your template.json file>"
    ```

### <a name="migrate-web-app-content"></a>Migrieren von Web-App-Inhalten

1. Wählen Sie im Portal von Azure Deutschland Ihre Web-App aus.
1. Wählen Sie **Entwicklungstools > Erweiterte Tools** aus.
1. Wählen Sie im Menü oben die Option **Debugkonsole** und dann **PowerShell** aus.
1. Wählen Sie **Standort** aus.
1. Klicken Sie auf das **Downloadsymbol** neben dem Ordner **wwwroot**. Die heruntergeladene ZIP-Datei enthält den Quellcode Ihrer Web-App.
1. Stellen Sie den Webstamm in der migrierten Web-App in Azure weltweit bereit. Sie können beispielsweise das folgende PowerShell-Skript verwenden.

    ``` powershell
    az webapp deployment source config-zip \
        --resource-group "<resource group name>" \
        --name "<web App name>" \
        --src "path to webroot folder zip file"
    ```

Weitere Informationen finden Sie unter:

- Frischen Sie Ihre Kenntnisse auf, indem Sie die [Tutorials zu App Service](../app-service/tutorial-dotnetcore-sqldb-app.md) durcharbeiten.
- Informieren Sie sich darüber, wie [Azure Resource Manager-Vorlagen exportiert werden](../azure-resource-manager/templates/export-template-portal.md).
- Lesen Sie die [Übersicht über Azure Resource Manager](../azure-resource-manager/management/overview.md).
- Lesen Sie die [Übersicht über Azure App Service](../app-service/overview.md).
- Verschaffen Sie sich einen [Überblick über Azure-Standorte](https://azure.microsoft.com/global-infrastructure/locations/).
- Erfahren Sie, wie Sie [eine Vorlage erneut bereitstellen](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="notification-hubs"></a>Notification Hubs

Um Einstellungen von einer Azure Notification Hubs-Instanz zu einer anderen Instanz zu migrieren, exportieren und importieren Sie alle Registrierungstoken samt deren Tags:

1. [Exportieren Sie die vorhandenen Notification Hub-Registrierungen](/previous-versions/azure/azure-services/dn790624(v=azure.100)) in einen Azure Blob Storage-Container.
1. Erstellen Sie einen neuen Notification Hub in der Zielumgebung.
1. [Importieren Sie Ihre Registrierungstoken](/previous-versions/azure/azure-services/dn790624(v=azure.100)) aus Blobspeicher in Ihren neuen Notification Hub.

Weitere Informationen finden Sie unter:

- Frischen Sie Ihre Kenntnisse auf, indem Sie die [Notification Hubs-Tutorials](../notification-hubs/notification-hubs-android-push-notification-google-fcm-get-started.md) durcharbeiten.
- Lesen Sie die [Übersicht über Notification Hubs](../notification-hubs/notification-hubs-push-notification-overview.md).

## <a name="event-hubs"></a>Event Hubs

Um einen Azure Event Hub zu migrieren, exportieren Sie die Event Hub-Ressourcenvorlage aus Azure Deutschland und stellen sie dann in Azure weltweit bereit.

1. [Exportieren Sie Event Hub als Vorlage](../azure-resource-manager/templates/export-template-portal.md) aus Ihrem Abonnement in Azure Deutschland.
1. [Stellen Sie die Event Hub-Vorlage als benutzerdefinierte Vorlage](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template) in Ihrem globalen Azure-Abonnement bereit. Laden Sie die Vorlage, die Sie aus Ihrem Abonnement in Azure Deutschland, und stellen Sie sie bereit.

Weitere Informationen finden Sie unter:

- Lesen Sie die [Übersicht über Event Hubs](../event-hubs/event-hubs-about.md).
- Lesen Sie die [Übersicht über Azure Resource Manager](../azure-resource-manager/management/overview.md).
- Informieren Sie sich darüber, wie [Azure Resource Manager-Vorlagen exportiert werden](../azure-resource-manager/templates/export-template-portal.md).
- Erfahren Sie, wie Sie [eine Vorlage erneut bereitstellen](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über Tools, Techniken und Empfehlungen zum Migrieren von Ressourcen in den folgenden Dienstkategorien:

- [Compute](./germany-migration-compute.md)
- [Netzwerk](./germany-migration-networking.md)
- [Storage](./germany-migration-storage.md)
- [Datenbanken](./germany-migration-databases.md)
- [Analyse](./germany-migration-analytics.md)
- [IoT](./germany-migration-iot.md)
- [Integration](./germany-migration-integration.md)
- [Identität](./germany-migration-identity.md)
- [Security](./germany-migration-security.md)
- [Verwaltungstools](./germany-migration-management-tools.md)
- [Medien](./germany-migration-media.md)