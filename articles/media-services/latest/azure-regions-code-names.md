---
title: Clouds und Regionen, in denen Azure Media Services v3 verfügbar ist
description: In diesem Artikel werden die für Endpunkte verwendeten URL und die Regionscodes erläutert.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: reference
ms.date: 10/28/2020
ms.author: inhenkel
ms.openlocfilehash: c17eeda91af46c4159f7c4b1747416298f24c703
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93319633"
---
# <a name="regional-code-names-and-endpoints"></a>Regionale Codenamen und Endpunkte

### <a name="region-code-name"></a>Name des Regionscodes

Wenn der Parameter **location** in einem Befehl oder einer Anforderung verwendet wird, müssen Sie den Namen des Regionscodes als Wert von **location** angeben. Zum Abrufen des Codenamens der Region, in der sich Ihr Konto befindet und zu dem Ihr Aufruf geroutet werden soll, können Sie in Azure CLI die folgende Zeile ausführen.

```azurecli-interactive
az account list-locations
```

Nach dem Ausführen der oben gezeigten Zeile erhalten Sie eine Liste mit allen Azure-Regionen. Navigieren Sie zu der Azure-Region, die den *displayName* aufweist, nach dem Sie suchen, und verwenden ihren Wert *name* für den Parameter **location**.

Verwenden Sie für die Azure-Region „USA, Westen 2“ (unten angezeigt) beispielsweise „westus2“ als Wert für den Parameter **location**.

```json
   {
      "displayName": "West US 2",
      "id": "/subscriptions/00000000-23da-4fce-b59c-f6fb9513eeeb/locations/westus2",
      "latitude": "47.233",
      "longitude": "-119.852",
      "name": "westus2",
      "subscriptionId": null
    }
```

## <a name="endpoints"></a>Endpunkte  

Die folgenden Endpunkte sind wichtig für Verbindungen mit Media Services-Konten aus verschiedenen nationalen Azure-Clouds.

### <a name="global-azure"></a>Globale Azure-Umgebung

| Dienst | Endpunkt |
| ------- | -------- |
| Azure Resource Manager |  `https://management.azure.com/` |
| Authentifizierung | `https://login.microsoftonline.com/` |
| Zielgruppe | `https://management.core.windows.net/` |

### <a name="azure-government"></a>Azure Government

| Dienst | Endpunkt |
| ------- | -------- |
| Azure Resource Manager |  `https://management.usgovcloudapi.net/` |
| Authentifizierung | `https://login.microsoftonline.us/` |
| Zielgruppe | `https://management.core.usgovcloudapi.net/` |

[!INCLUDE [Widevine is not available in the GovCloud region.](./includes/widevine-not-available-govcloud.md)]

### <a name="azure-germany"></a>Azure Deutschland

> [!NOTE]
> Die Azure Deutschland-Endpunkte gelten nur für die Sovereign Clouds in Deutschland.

| Dienst | Endpunkt |
| ------- | -------- |
| Azure Resource Manager | `https://management.cloudapi.de/` |
| Authentifizierung | `https://login.microsoftonline.de/` |
| Zielgruppe | `https://management.core.cloudapi.de/`|

### <a name="azure-china-21vianet"></a>Azure China 21Vianet

| Dienst | Endpunkt |
| ------- | -------- |
| Azure Resource Manager | `https://management.chinacloudapi.cn/` |
| Authentifizierung | `https://login.chinacloudapi.cn/` |
| Zielgruppe |  `https://management.core.chinacloudapi.cn/` |

## <a name="see-also"></a>Weitere Informationen

* [Azure-Regionen](https://azure.microsoft.com/global-infrastructure/regions/)
* [Regionale Codenamen und Endpunkte](azure-regions-code-names.md)
* [Azure-Geografien](https://azure.microsoft.com/global-infrastructure/geographies/)
* [Azure-Standorte](https://azure.microsoft.com/global-infrastructure/locations/)

## <a name="next-steps"></a>Nächste Schritte

[Media Services v3 – Übersicht](media-services-overview.md)
