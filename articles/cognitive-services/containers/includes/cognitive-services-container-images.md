---
title: Containerrepositorys und -images
services: cognitive-services
author: aahill
manager: nitinme
description: Zwei Tabellen, die die Containerregistrierungen, Repositorys und Imagenamen für alle Cognitive Service-Angebote darstellen.
ms.service: cognitive-services
ms.topic: include
ms.date: 09/03/2020
ms.author: aahi
ms.openlocfilehash: 2c2ebe9b419100163ae55c1be85dd1464904e841
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94978783"
---
### <a name="container-repositories-and-images"></a>Containerrepositorys und -images

Die folgenden Tabellen stellen eine Liste der verfügbaren Containerimages dar, die von Azure Cognitive Services geboten werden. Eine vollständige Liste aller verfügbaren Namen für Containerimages und ihrer verfügbaren Tags finden Sie unter [Cognitive Services-Containerimagetags](../container-image-tags.md). 

#### <a name="generally-available"></a>Allgemein verfügbar 

Die Microsoft-Containerregistrierung (MCR) syndiziert alle allgemein verfügbaren Container für Cognitive Services. Die Container sind auch direkt über den [Docker-Hub](https://hub.docker.com/_/microsoft-azure-cognitive-services)verfügbar.

**LUIS**

| Container | Container Registry/Repository/Imagename |
|--|--|
| LUIS | `mcr.microsoft.com/azure-cognitive-services/language/luis` |

Weitere Informationen finden Sie unter [Installieren und Ausführen von Docker-Containern für LUIS](../../LUIS/luis-container-howto.md).

**Textanalyse**

| Container | Container Registry/Repository/Imagename |
|--|--|
| Standpunktanalyse v3 (Englisch) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-en` |
| Standpunktanalyse v3 (Spanisch) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-es` |
| Standpunktanalyse v3 (Französisch) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-fr` |
| Standpunktanalyse v3 (Italienisch) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-it` |
| Standpunktanalyse v3 (Deutsch) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-de` |
| Standpunktanalyse v3 (Chinesisch, vereinfacht) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-zh` |
| Standpunktanalyse v3 (Chinesisch, traditionell) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-zht` |
| Standpunktanalyse v3 (Japanisch) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-ja` |
| Standpunktanalyse v3 (Portugiesisch) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-pt` |
| Standpunktanalyse v3 (Niederländisch) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-nl` |

Weitere Informationen finden Sie unter [Installieren und Ausführen von Containern für die Textanalyse](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md).

**Anomalieerkennung** 

| Container | Container Registry/Repository/Imagename |
|--|--|
| Anomalieerkennung | `mcr.microsoft.com/azure-cognitive-services/decision/anomaly-detector` |

Weitere Informationen finden Sie unter [Installieren und Ausführen von Containern für die Anomalieerkennung](../../anomaly-detector/anomaly-detector-container-howto.md).

**Spracherkennungsdienst**

> [!NOTE]
> Sie müssen für die Verwendung von Speech-Containern ein [Onlineanforderungsformular](https://aka.ms/csgate) ausfüllen.

| Container | Container Registry/Repository/Imagename |
|--|--|
| [Spracherkennung](../../speech-service/speech-container-howto.md?tab=stt) | `mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text` |
| [Benutzerdefinierte Spracherkennung](../../speech-service/speech-container-howto.md?tab=cstt) | `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text` |
| [Sprachsynthese](../../speech-service/speech-container-howto.md?tab=tts) | `mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech` |

#### <a name="ungated-preview"></a>Unbeschränkte Vorschauversion 

Die folgenden Vorschaucontainer sind öffentlich verfügbar. Die Microsoft-Containerregistrierung (MCR) syndiziert alle öffentlich verfügbaren, nicht verwalteten Container für Cognitive Services. Die Container sind auch direkt über den [Docker-Hub](https://hub.docker.com/_/microsoft-azure-cognitive-services)verfügbar.

| Dienst | Container | Container Registry/Repository/Imagename |
|--|--|--|
| [Textanalyse](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Schlüsselwortextraktion | `mcr.microsoft.com/azure-cognitive-services/textanalytics/keyphrase` |
| [Textanalyse](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Spracherkennung | `mcr.microsoft.com/azure-cognitive-services/textanalytics/language` |


#### <a name="gated-preview"></a>Beschränkte Vorschauversion

Bisher wurden Container der beschränkten Vorschau im Repository `containerpreview.azurecr.io` gehostet. Ab dem 22. September 2020 werden diese Container (mit Ausnahme der Textanalyse für die Integrität) in Microsoft Container Registry (MCR) gehostet. Für den Download muss nicht der Docker-Anmeldebefehl verwendet werden. Für die Verwendung des Containers benötigen Sie Folgendes:

1. Füllen Sie ein [Anforderungsformular](https://aka.ms/csgate) mit Ihrer Azure-Abonnement-ID und dem Benutzerszenario aus. 
2. Laden Sie nach der Genehmigung den Container aus MCR herunter. 
3. Verwenden Sie den Schlüssel und den Endpunkt einer entsprechenden Azure-Ressource, um den Container zur Laufzeit zu authentifizieren. 

| Dienst | Container | Container Registry/Repository/Imagename |
|--|--|--|
| [Maschinelles Sehen](../../Computer-vision/computer-vision-how-to-install-containers.md) | Lesen v2.0 | `mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview` |
| [Maschinelles Sehen](../../Computer-vision/computer-vision-how-to-install-containers.md) | Lesen v3.1 | `mcr.microsoft.com/azure-cognitive-services/vision/read:3.1-preview` |
| [Maschinelles Sehen](../../computer-vision/spatial-analysis-container.md) | Räumliche Analyse | `mcr.microsoft.com/azure-cognitive-services/vision/spatial-analysis` |
| [Spracherkennungsdienst-API](../../speech-service/speech-container-howto.md?tab=ctts) | Benutzerdefinierte Sprachsynthese | `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech` |
| [Spracherkennungsdienst-API](../../speech-service/speech-container-howto.md?tab=lid) | Spracherkennung | `mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection` |
| [Spracherkennungsdienst-API](../../speech-service/speech-container-howto.md?tab=ntts) | Text-zu-Sprache (neuronal) | `mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech` |
| [Textanalyse für Gesundheit](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=health) | Textanalyse für Gesundheit | `containerpreview.azurecr.io/microsoft/cognitive-services-healthcare` |