---
title: Veröffentlichungsregionen und Endpunkte
titleSuffix: Azure Cognitive Services
description: Drei Erstellungsregionen und ihre Portale unterstützen die zahlreichen Veröffentlichungsregionen. Die Region, in der Sie Ihre LUIS-App veröffentlichen, entspricht der gewählten Region oder dem Standort, die bzw. den Sie im Azure-Portal angeben, wenn Sie einen Azure-LUIS-Endpunktschlüssel erstellen. Wenn Sie eine App veröffentlichen, generiert LUIS automatisch eine Endpunkt-URL für die Region, die dem Schlüssel zugeordnet ist.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 04/02/2019
ms.author: diberry
ms.openlocfilehash: 20ea2eb632a6d685351178691cc3d0f58a567902
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60599268"
---
# <a name="authoring-and-publishing-regions-and-the-associated-keys"></a>Erstellungs- und Veröffentlichungsregionen und die dazugehörigen Schlüssel

Es werden drei Erstellungsregionen von entsprechenden LUIS-Portalen unterstützt. Zum Veröffentlichen einer LUIS-App in mehreren Regionen benötigen Sie mindestens einen Schlüssel pro Region. 

<a name="luis-website"></a>

## <a name="luis-authoring-regions"></a>LUIS-Erstellungsregionen
Es gibt drei LUIS-Erstellungsportale, die auf der Region basieren. Sie müssen die Erstellung und Veröffentlichung in derselben Region durchführen. 

|LUIS|Erstellungsregion|Azure-Region: Name|
|--|--|--|
|[www.luis.ai][www.luis.ai]|Daten<br>nicht Europa<br>nicht Australien| `westus`|
|[au.luis.ai][au.luis.ai]|Australien| `australiaeast`|
|[eu.luis.ai][eu.luis.ai]|Europa|`westeurope`|

Erstellungsbereiche verfügen über [gekoppelte Failoverregionen](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). 

<a name="regions-and-azure-resources"></a>

## <a name="publishing-regions-and-azure-resources"></a>Veröffentlichungsregionen und Azure-Ressourcen
Die App wird in allen Regionen veröffentlicht, die den im LUIS-Portal hinzugefügten LUIS-Ressourcen zugeordnet sind. Wenn Sie z. B. für eine auf [www.luis.ai][www.luis.ai] erstellte App eine LUIS- oder Cognitive Service-Ressource in **westus** erstellen und [sie der App als Ressource hinzufügen](luis-how-to-azure-subscription.md), wird die App in dieser Region veröffentlicht. 

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
| [Europa](#publishing-to-europe)| `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Frankreich, Mitte<br>`francecentral`     | https://francecentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| [Europa](#publishing-to-europe)| `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Nordeuropa<br>`northeurope`     | https://northeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| [Europa](#publishing-to-europe) | `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Europa, Westen<br>`westeurope`    |  https://westeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| [Europa](#publishing-to-europe) | `westeurope`<br>[eu.luis.ai][eu.luis.ai]| UK, Süden<br>`uksouth`    |  https://uksouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="publishing-to-australia"></a>Veröffentlichen in Australien

Zum Veröffentlichen in den australischen Regionen erstellen Sie nur in https://au.luis.ai LUIS-Apps. Wenn Sie es in einer anderen Region mit einem Schlüssel für die Region Australien versuchen, zeigt LUIS eine Warnmeldung an. Verwenden Sie stattdessen https://au.luis.ai. LUIS-Apps, die in [https://au.luis.ai][au.luis.ai] erstellt wurden, werden nicht automatisch zu anderen Regionen migriert. Exportieren Sie die LUIS-App, und importieren Sie sie dann, um sie zu migrieren.

## <a name="australia-publishing-regions"></a>Veröffentlichungsregionen in Australien

 Globale Region | Erstellungs-API-Region und Erstellungswebsite| Veröffentlichungs- und Abfrageregion<br>`API region name`   |  Format der Endpunkt-URL   |
|-----|------|------|------|
| [Australien](#publishing-to-australia) | `australiaeast`<br>[au.luis.ai][au.luis.ai]| Australien (Osten)<br>`australiaeast`     |  https://australiaeast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="publishing-to-other-regions"></a>Veröffentlichen in anderen Regionen

Zum Veröffentlichen in den sonstigen Regionen erstellen Sie nur in [https://www.luis.ai](https://www.luis.ai) LUIS-Apps. 

## <a name="other-publishing-regions"></a>Sonstige Veröffentlichungsregionen

 Globale Region | Erstellungs-API-Region und Erstellungswebsite| Veröffentlichungs- und Abfrageregion<br>`API region name`   |  Format der Endpunkt-URL   |
|-----|------|------|------|
| Asien | `westus`<br>[www.luis.ai][www.luis.ai]| Indien, Mitte<br>`centralindia` |  https://centralindia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Asien | `westus`<br>[www.luis.ai][www.luis.ai]| Asien, Osten<br>`eastasia`     |  https://eastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Asien | `westus`<br>[www.luis.ai][www.luis.ai]| Japan, Osten<br>`japaneast`     |   https://japaneast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Asien | `westus`<br>[www.luis.ai][www.luis.ai]| Japan, Westen<br>`japanwest`     |   https://japanwest.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Asien | `westus`<br>[www.luis.ai][www.luis.ai]| Korea, Mitte<br>`koreacentral`     |   https://koreacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Asien | `westus`<br>[www.luis.ai][www.luis.ai]| Asien, Südosten<br>`southeastasia`     |   https://southeastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Nordamerika |`westus`<br>[www.luis.ai][www.luis.ai] | Kanada, Mitte<br>`canadacentral`     |   https://canadacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Nordamerika |`westus`<br>[www.luis.ai][www.luis.ai] | USA (Mitte)<br>`centralus`     |   https://centralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Nordamerika |`westus`<br>[www.luis.ai][www.luis.ai] | USA (Ost)<br>`eastus`      |  https://eastus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Nordamerika | `westus`<br>[www.luis.ai][www.luis.ai] | USA (Ost) 2<br>`eastus2`     |  https://eastus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Nordamerika | `westus`<br>[www.luis.ai][www.luis.ai] | USA Nord Mitte<br>`northcentralus`  |  https://northcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| Nordamerika | `westus`<br>[www.luis.ai][www.luis.ai] | USA Süd Mitte<br>`southcentralus`  |  https://southcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| Nordamerika |`westus`<br>[www.luis.ai][www.luis.ai] | USA, Westen-Mitte<br>`westcentralus`    |  https://westcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Nordamerika | `westus`<br>[www.luis.ai][www.luis.ai] | USA (Westen)<br>`westus`  |   https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| Nordamerika |`westus`<br>[www.luis.ai][www.luis.ai] | USA, Westen 2<br>`westus2`    |  https://westus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| Südamerika | `westus`<br>[www.luis.ai][www.luis.ai] | Brasilien Süd<br>`brazilsouth`    |  https://brazilsouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="endpoints"></a>Endpunkte

LUIS verfügt derzeit über 2 Endpunkte: einen für die Dokumenterstellung und einen für die Analyse von Abfragevorhersagen.

|Zweck|URL|
|--|--|
|Erstellen|`https://{region}.api.cognitive.microsoft.com/luis/api/v2.0/apps/{appID}/`|
|Textanalyse (Abfragevorhersage)|`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q={q}[&timezoneOffset][&verbose][&spellCheck][&staging][&bing-spell-check-subscription-key][&log]`|

In der folgende Tabelle werden die Parameter erläutert, die in der vorherigen Tabelle durch geschweifte Klammern `{}` gekennzeichnet sind.

|Parameter|Zweck|
|--|--|
|region|Azure-Region – die Dokumenterstellung und die Veröffentlichung haben unterschiedliche Regionen|
|appID|Die in der URL-Route verwendete und auf dem App-Dashboard angezeigten LUIS-App-ID|
|q|Äußerungstext, der von der Clientanwendung gesendet wird, wie z.B. ein Chatbot|

## <a name="failover-regions"></a>Failoverregionen

Jede Region verfügt über eine sekundäre Region für Failover. Europa führt Failover innerhalb Europas aus, Australien innerhalb von Australien.

Erstellungsbereiche verfügen über [gekoppelte Failoverregionen](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). 

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Vordefinierter Entitätsverweis](./luis-reference-prebuilt-entities.md)

 [www.luis.ai]: https://www.luis.ai
 [au.luis.ai]: https://au.luis.ai
 [eu.luis.ai]: https://eu.luis.ai
