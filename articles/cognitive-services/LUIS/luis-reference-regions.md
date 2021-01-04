---
title: Veröffentlichungsregionen und Endpunkte – LUIS
description: Die im Azure-Portal angegebene Region ist dieselbe, in der Sie die LUIS-App veröffentlichen werden, und es wird eine Endpunkt-URL für diese Region generiert.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 11/09/2020
ms.openlocfilehash: 5f65c5e1e1c8d306a70be3fdd7a07f18f8ebbd9f
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/13/2020
ms.locfileid: "97368646"
---
# <a name="authoring-and-publishing-regions-and-the-associated-keys"></a>Erstellungs- und Veröffentlichungsregionen und die dazugehörigen Schlüssel

[!INCLUDE [LUIS Free account](includes/luis-portal-note.md)]

Es werden drei Erstellungsregionen von entsprechenden LUIS-Portalen unterstützt. Zum Veröffentlichen einer LUIS-App in mehreren Regionen benötigen Sie mindestens einen Schlüssel pro Region.

<a name="luis-website"></a>

## <a name="luis-authoring-regions"></a>LUIS-Erstellungsregionen
Es gibt drei LUIS-Erstellungsportale, die auf der Region basieren. Sie müssen die Erstellung und Veröffentlichung in derselben Region durchführen.

|LUIS|Erstellungsregion|Azure-Region: Name|
|--|--|--|
|[www.luis.ai][www.luis.ai] |USA<br>nicht Europa<br>nicht Australien| `westus`|
|[au.luis.ai][au.luis.ai] |Australien| `australiaeast`|
|[eu.luis.ai][eu.luis.ai] |Europa|`westeurope`|

Erstellungsbereiche verfügen über [gekoppelte Failoverregionen](../../best-practices-availability-paired-regions.md).

<a name="regions-and-azure-resources"></a>

## <a name="publishing-regions-and-azure-resources"></a>Veröffentlichungsregionen und Azure-Ressourcen
Die App wird in allen Regionen veröffentlicht, die den im LUIS-Portal hinzugefügten LUIS-Ressourcen zugeordnet sind. Wenn Sie z. B. für eine auf [www.luis.ai][www.luis.ai] erstellte App eine LUIS- oder Cognitive Service-Ressource in **westus** erstellen und [sie der App als Ressource hinzufügen](luis-how-to-azure-subscription.md), wird die App in dieser Region veröffentlicht.

## <a name="public-apps"></a>Öffentliche Apps
Eine öffentliche App wird in allen Regionen veröffentlicht, so dass ein Benutzer mit einem regionsbasierten LUIS-Ressourcenschlüssel auf die App in jeder Region zugreifen kann, die dem Ressourcenschlüssel zugeordnet ist.

<a name="publishing-regions"></a>

## <a name="publishing-regions-are-tied-to-authoring-regions"></a>Veröffentlichungsregionen sind an Erstellungsregionen gebunden.

Die App der Erstellungsregion kann nur in einer entsprechenden Veröffentlichungsregion veröffentlicht werden. Wenn sich die App derzeit in der falschen Erstellungsregion befindet, exportieren Sie die App und importieren sie in die richtige Erstellungsregion für Ihre Veröffentlichungsregion.

LUIS-Apps, die in https://www.luis.ai erstellt wurden, können auf allen Endpunkten außer in den Regionen in [Europa](#publishing-to-europe) und [Australien](#publishing-to-australia) veröffentlicht werden.

## <a name="publishing-to-europe"></a>Veröffentlichen in Europa

Zum Veröffentlichen in den europäischen Regionen erstellen Sie nur in https://eu.luis.ai LUIS-Apps. Wenn Sie es in einer anderen Region mit einem Schlüssel für die Region Europa versuchen, zeigt LUIS eine Warnmeldung an. Verwenden Sie stattdessen https://eu.luis.ai. LUIS-Apps, die in [https://eu.luis.ai][eu.luis.ai] erstellt wurden, werden nicht automatisch zu anderen Regionen migriert. Exportieren Sie die LUIS-App, und importieren Sie sie dann, um sie zu migrieren.

## <a name="europe-publishing-regions"></a>Veröffentlichungsregionen in Europa

 Globale Region | Erstellungs-API-Region und Erstellungswebsite| Veröffentlichungs- und Abfrageregion<br>`API region name`   |  Format der Endpunkt-URL   |
|-----|------|------|------|
| [Europa](#publishing-to-europe)| `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Frankreich, Mitte<br>`francecentral`     | `https://francecentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| [Europa](#publishing-to-europe)| `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Nordeuropa<br>`northeurope`     | `https://northeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| [Europa](#publishing-to-europe) | `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Europa, Westen<br>`westeurope`    |  `https://westeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| [Europa](#publishing-to-europe) | `westeurope`<br>[eu.luis.ai][eu.luis.ai]| UK, Süden<br>`uksouth`    |  `https://uksouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |

## <a name="publishing-to-australia"></a>Veröffentlichen in Australien

Zum Veröffentlichen in den australischen Regionen erstellen Sie nur in https://au.luis.ai LUIS-Apps. Wenn Sie es in einer anderen Region mit einem Schlüssel für die Region Australien versuchen, zeigt LUIS eine Warnmeldung an. Verwenden Sie stattdessen https://au.luis.ai. LUIS-Apps, die in [https://au.luis.ai][au.luis.ai] erstellt wurden, werden nicht automatisch zu anderen Regionen migriert. Exportieren Sie die LUIS-App, und importieren Sie sie dann, um sie zu migrieren.

## <a name="australia-publishing-regions"></a>Veröffentlichungsregionen in Australien

 Globale Region | Erstellungs-API-Region und Erstellungswebsite| Veröffentlichungs- und Abfrageregion<br>`API region name`   |  Format der Endpunkt-URL   |
|-----|------|------|------|
| [Australien](#publishing-to-australia) | `australiaeast`<br>[au.luis.ai][au.luis.ai]| Australien (Osten)<br>`australiaeast`     |  `https://australiaeast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |

## <a name="publishing-to-other-regions"></a>Veröffentlichen in anderen Regionen

Zum Veröffentlichen in den sonstigen Regionen erstellen Sie nur in [https://www.luis.ai](https://www.luis.ai) LUIS-Apps.

## <a name="other-publishing-regions"></a>Sonstige Veröffentlichungsregionen

 Globale Region | Erstellungs-API-Region und Erstellungswebsite| Veröffentlichungs- und Abfrageregion<br>`API region name`   |  Format der Endpunkt-URL   |
|-----|------|------|------|
| Afrika | `westus`<br>[www.luis.ai][www.luis.ai]| Südafrika, Norden<br>`southafricanorth` |  `https://southafricanorth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Asia | `westus`<br>[www.luis.ai][www.luis.ai]| Indien, Mitte<br>`centralindia` |  `https://centralindia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Asia | `westus`<br>[www.luis.ai][www.luis.ai]| Asien, Osten<br>`eastasia`     |  `https://eastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Asia | `westus`<br>[www.luis.ai][www.luis.ai]| Japan, Osten<br>`japaneast`     |   `https://japaneast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Asia | `westus`<br>[www.luis.ai][www.luis.ai]| Japan, Westen<br>`japanwest`     |   `https://japanwest.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Asia | `westus`<br>[www.luis.ai][www.luis.ai]| Korea, Mitte<br>`koreacentral`     |   `https://koreacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Asia | `westus`<br>[www.luis.ai][www.luis.ai]| Asien, Südosten<br>`southeastasia`     |   `https://southeastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Nordamerika |`westus`<br>[www.luis.ai][www.luis.ai] | Kanada, Mitte<br>`canadacentral`     |   `https://canadacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Nordamerika |`westus`<br>[www.luis.ai][www.luis.ai] | USA (Mitte)<br>`centralus`     |   `https://centralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Nordamerika |`westus`<br>[www.luis.ai][www.luis.ai] | East US<br>`eastus`      |  `https://eastus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Nordamerika | `westus`<br>[www.luis.ai][www.luis.ai] | USA (Ost) 2<br>`eastus2`     |  `https://eastus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Nordamerika | `westus`<br>[www.luis.ai][www.luis.ai] | USA Nord Mitte<br>`northcentralus`  |  `https://northcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Nordamerika | `westus`<br>[www.luis.ai][www.luis.ai] | USA Süd Mitte<br>`southcentralus`  |  `https://southcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Nordamerika |`westus`<br>[www.luis.ai][www.luis.ai] | USA, Westen-Mitte<br>`westcentralus`    |  `https://westcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Nordamerika | `westus`<br>[www.luis.ai][www.luis.ai] | USA (Westen)<br>`westus`  |   `https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`  |
| Nordamerika |`westus`<br>[www.luis.ai][www.luis.ai] | USA, Westen 2<br>`westus2`    |  `https://westus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`  |
| Südamerika | `westus`<br>[www.luis.ai][www.luis.ai] | Brasilien Süd<br>`brazilsouth`    |  `https://brazilsouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |

## <a name="endpoints"></a>Endpunkte

Erfahren Sie mehr über die [Erstellen- und Vorhersageendpunkte](developer-reference-resource.md).

## <a name="failover-regions"></a>Failoverregionen

Jede Region verfügt über eine sekundäre Region für Failover. Europa führt Failover innerhalb Europas aus, Australien innerhalb von Australien.

Erstellungsbereiche verfügen über [gekoppelte Failoverregionen](../../best-practices-availability-paired-regions.md).

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Vordefinierter Entitätsverweis](./luis-reference-prebuilt-entities.md)

 [www.luis.ai]: https://www.luis.ai
 [au.luis.ai]: https://au.luis.ai
 [eu.luis.ai]: https://eu.luis.ai