---
title: Migrieren von Azure-Webressourcen von Azure Deutschland zu Azure weltweit
description: Dieser Artikel enthält Informationen zum Migrieren von Azure-Webressourcen von Azure Deutschland zu Azure weltweit.
author: gitralf
services: germany
cloud: Azure Germany
ms.author: ralfwi
ms.service: germany
ms.date: 08/15/2018
ms.topic: article
ms.custom: bfmigrate
ms.openlocfilehash: 4e607d821d0a587b25c6cb6929ac6765d500b6fb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60685184"
---
# <a name="migrate-web-resources-to-global-azure"></a>Migrieren von Webressourcen zu Azure weltweit

Dieser Artikel enthält Informationen dazu, wie Sie Azure-Webressourcen von Azure Deutschland zu Azure weltweit migrieren können.

## <a name="web-apps"></a>Web-Apps

Migrieren von Apps, die Sie mit dem Web Apps-Feature von Azure App Service erstellt haben, von Azure Deutschland in Azure weltweit wird derzeit nicht unterstützt. Es empfiehlt sich, dass Sie eine Web-App als Azure Resource Manager-Vorlage exportieren. Danach können Sie die App erneut bereitstellen, nachdem Sie die Eigenschaft für den Standort auf die neue Zielregion geändert haben.

> [!IMPORTANT]
> Ändern Sie den Standort, Azure Key Vault-Geheimnisse, Zertifikate und andere GUIDs, damit diese konsistent mit der neuen Region sind.

Weitere Informationen finden Sie unter:

- Frischen Sie Ihre Kenntnisse auf, indem Sie die [Tutorials zu App Service](https://docs.microsoft.com/azure/app-service/#step-by-step-tutorials) durcharbeiten.
- Informieren Sie sich darüber, wie [Azure Resource Manager-Vorlagen exportiert werden](../azure-resource-manager/manage-resource-groups-portal.md#export-resource-groups-to-templates).
- Lesen Sie die [Übersicht über Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).
- Lesen Sie die [Übersicht über Azure App Service](../app-service/overview.md).
- Verschaffen Sie sich einen [Überblick über Azure-Standorte](https://azure.microsoft.com/global-infrastructure/locations/).
- Erfahren Sie, wie Sie [eine Vorlage erneut bereitstellen](../azure-resource-manager/resource-group-template-deploy.md).

## <a name="notification-hubs"></a>Notification Hubs

Um Einstellungen von einer Azure Notification Hubs-Instanz zu einer anderen Instanz zu migrieren, exportieren und importieren Sie alle Registrierungstoken samt deren Tags:

1. [Exportieren Sie die vorhandenen Notification Hub-Registrierungen](/previous-versions/azure/azure-services/dn790624(v=azure.100)) in einen Azure Blob Storage-Container.
1. Erstellen Sie einen neuen Notification Hub in der Zielumgebung.
1. [Importieren Sie Ihre Registrierungstoken](/previous-versions/azure/azure-services/dn790624(v=azure.100)) aus Blobspeicher in Ihren neuen Notification Hub.

Weitere Informationen finden Sie unter:

- Frischen Sie Ihre Kenntnisse auf, indem Sie die [Notification Hubs-Tutorials](https://docs.microsoft.com/azure/notification-hubs/#step-by-step-tutorials) durcharbeiten.
- Lesen Sie die [Übersicht über Notification Hubs](../notification-hubs/notification-hubs-push-notification-overview.md).

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über Tools, Techniken und Empfehlungen zum Migrieren von Ressourcen in den folgenden Dienstkategorien:

- [Compute](./germany-migration-compute.md)
- [Netzwerk](./germany-migration-networking.md)
- [Speicher](./germany-migration-storage.md)
- [Datenbanken](./germany-migration-databases.md)
- [Analyse](./germany-migration-analytics.md)
- [IoT](./germany-migration-iot.md)
- [Integration](./germany-migration-integration.md)
- [Identität](./germany-migration-identity.md)
- [Sicherheit](./germany-migration-security.md)
- [Verwaltungstools](./germany-migration-management-tools.md)
- [Medien](./germany-migration-media.md)
