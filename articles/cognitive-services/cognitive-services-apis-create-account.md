---
title: Erstellen einer Cognitive Services-Ressource im Azure-Portal
titleSuffix: Azure Cognitive Services
description: Beginnen Sie mit Azure Cognitive Services, indem Sie im Azure-Portal eine Ressource erstellen und abonnieren.
services: cognitive-services
author: aahill
manager: nitinme
keywords: Cognitive Services, kognitive Intelligenz, kognitive Lösungen, KI-Dienste
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 09/14/2020
ms.author: aahi
ms.openlocfilehash: 3fd1d9e708a5f1a500440f20c2947ddfe4bc7460
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2020
ms.locfileid: "94368915"
---
# <a name="quickstart-create-a-cognitive-services-resource-using-the-azure-portal"></a>Schnellstart: Erstellen einer Cognitive Services-Ressource mithilfe des Azure-Portals

Verwenden Sie diese Schnellstartanleitung, um mit der Verwendung von Azure Cognitive Services zu beginnen. Nachdem Sie im Azure-Portal eine Cognitive Service-Ressource erstellt haben, erhalten Sie einen Endpunkt und einen Schlüssel zum Authentifizieren Ihrer Anwendungen.

Azure Cognitive Services sind cloudbasierte Dienste mit REST-APIs und Clientbibliothek-SDKs, die Entwickler bei der Erstellung intelligenter Anwendungen unterstützen, ohne dass direkte KI- oder Data Science-Fähigkeiten oder -Kenntnisse erforderlich sind. Mit Cognitive Services können Entwickler ganz einfach kognitive Features in ihre Anwendungen integrieren – mit kognitiven Lösungen, die sehen, hören, sprechen, verstehen und sogar schlussfolgern können.


[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="prerequisites"></a>Voraussetzungen

* Ein gültiges Azure-Abonnement: [Erstellen Sie ein kostenloses Abonnement](https://azure.microsoft.com/free/cognitive-services/).

## <a name="create-a-new-azure-cognitive-services-resource"></a>Erstellen einer neuen Azure Cognitive Services-Ressource

1. Dient zum Erstellen einer Ressource.

    #### <a name="multi-service-resource"></a>[Ressource für mehrere Dienste](#tab/multiservice)

    Die Ressource für mehrere Dienste wird im Portal **Cognitive Services** benannt. [Erstellen Sie eine Cognitive Services-Ressource](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne).

    Zu diesem Zeitpunkt ermöglicht die Ressource für mehrere Dienste den Zugriff auf die folgenden Cognitive Services:

    - Maschinelles Sehen
    - Content Moderator
    - Gesicht
    - Language Understanding (LUIS)
    - Textanalyse
    - Übersetzer
    - Bing-Suche v7 <br>(Web, Bild, Neuigkeiten, Video, Visual)
    - Benutzerdefinierte Bing-Suche
    - Bing-Entitätssuche
    - Bing-Vorschlagssuche
    - Bing-Rechtschreibprüfung

    #### <a name="single-service-resource"></a>[Ressource für einen einzelnen Dienst](#tab/singleservice)

    Verwenden Sie die folgenden Links, um eine Ressource für die verfügbaren Cognitive Services zu erstellen:

    | Bildanalyse                      | Spracheingabe                  | Sprache                          | Entscheidung             | Suchen,                 |
    |-----------------------------|-------------------------|-----------------------------------|----------------------|------------------------|
    | [Maschinelles Sehen](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision)         | [Speech-Dienste](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices)     | [Plastischer Reader](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesImmersiveReader)              | [Anomalieerkennung](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector) | [Bing-Suche-API V7](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7) |
    | [Custom Vision Service](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesCustomVision) | [Sprechererkennung](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeakerRecognition) | [Language Understanding (LUIS)](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) | [Content Moderator](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) | [Benutzerdefinierte Bing-Suche](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingCustomSearch) |
    | [Gesichtserkennung](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFace)                    |                         | [QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)                     | [Personalisierung](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer)     | [Bing-Entitätssuche](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingEntitySearch) |
    | [Freihanderkennung](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesInkRecognizer)        |                         | [Textanalyse](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)                |  [Metrics Advisor](https://go.microsoft.com/fwlink/?linkid=2142156)                    | [Bing-Rechtschreibprüfung](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingSpellCheck-v7)   |
    |           |                         | [Translator](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation)               |                      | [Bing-Vorschlagssuche](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingAutosuggest-v7)                       |

    **_

3. Geben Sie auf der Seite _ *Erstellen** die folgenden Informationen ein:

    #### <a name="multi-service-resource"></a>[Ressource für mehrere Dienste](#tab/multiservice)

    |    |    |
    |--|--|
    | **Name** | Ein beschreibender Name für Ihre Cognitive Services-Ressource. Beispiel: *MyCognitiveServicesResource*. |
    | **Abonnement** | Wählen Sie eines Ihrer verfügbaren Azure-Abonnements aus. |
    | **Location** | Der Speicherort Ihrer Cognitive Services-Instanz. Verschiedene Speicherorte können Wartezeiten verursachen, haben aber keinen Einfluss auf die Laufzeitverfügbarkeit Ihrer Ressource. |
    | **Preisstufe** | Die Kosten für Ihr Cognitive Services-Konto hängen von den ausgewählten Optionen und Ihrer Nutzung ab. Weitere Informationen finden Sie unter API-[Preise](https://azure.microsoft.com/pricing/details/cognitive-services/).
    | **Ressourcengruppe** | Die Azure-Ressourcengruppe, die Ihre Cognitive Services-Ressource enthält. Sie können eine neue Gruppe erstellen oder sie einer bereits bestehenden Gruppe hinzufügen. |

    ![Bildschirm „Ressourcenerstellung“ für mehrere Dienste](media/cognitive-services-apis-create-account/resource_create_screen-multi.png)

    Klicken Sie auf **Erstellen**.

    #### <a name="single-service-resource"></a>[Ressource für einen einzelnen Dienst](#tab/singleservice)

    |    |    |
    |--|--|
    | **Name** | Ein beschreibender Name für Ihre Cognitive Services-Ressource. Beispiel: *TextAnalyticsResource*. |
    | **Abonnement** | Wählen Sie eines Ihrer verfügbaren Azure-Abonnements aus. |
    | **Location** | Der Speicherort Ihrer Cognitive Services-Instanz. Verschiedene Speicherorte können Wartezeiten verursachen, haben aber keinen Einfluss auf die Laufzeitverfügbarkeit Ihrer Ressource. |
    | **Preisstufe** | Die Kosten für Ihr Cognitive Services-Konto hängen von den ausgewählten Optionen und Ihrer Nutzung ab. Weitere Informationen finden Sie unter API-[Preise](https://azure.microsoft.com/pricing/details/cognitive-services/).
    | **Ressourcengruppe** | Die Azure-Ressourcengruppe, die Ihre Cognitive Services-Ressource enthält. Sie können eine neue Gruppe erstellen oder sie einer bereits bestehenden Gruppe hinzufügen. |

    ![Bildschirm „Ressourcenerstellung“ für einen einzelnen Dienst](media/cognitive-services-apis-create-account/resource_create_screen.png)

    Klicken Sie auf **Erstellen**.

    **_

[!INCLUDE [Register Azure resource for subscription](./includes/register-resource-subscription.md)]

## <a name="get-the-keys-for-your-resource"></a>Abrufen der Schlüssel für die Ressource

1. Klicken Sie nach erfolgreicher Ressourcenbereitstellung unter **Nächste Schritte** auf _ *Zu Ressource** wechseln.

    ![Suchen nach Cognitive Services](media/cognitive-services-apis-create-account/resource-next-steps.png)

2. Von dem Schnellstartbereich, der geöffnet wird, können Sie auf Ihren Schlüssel und Endpunkt zugreifen.

    ![Schlüssel und Endpunkt abrufen](media/cognitive-services-apis-create-account/get-cog-serv-keys.png)

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie ein Cognitive Services-Abonnement bereinigen und entfernen möchten, können Sie die Ressource oder die Ressourcengruppe löschen. Wenn Sie die Ressourcengruppe löschen, werden auch alle anderen in der Gruppe enthaltenen Ressourcen gelöscht.

1. Erweitern Sie im Azure-Portal das Menü auf der linken Seite, um das Menü mit den Diensten zu öffnen, und klicken Sie auf **Ressourcengruppen**, um die Liste mit Ihren Ressourcengruppen anzuzeigen.
2. Suchen Sie die Ressourcengruppe, die die zu löschende Ressource enthält.
3. Klicken Sie mit der rechten Maustaste auf die Ressourcengruppenauflistung. Klicken Sie auf **Ressourcengruppe löschen**, und bestätigen Sie den Vorgang.

## <a name="see-also"></a>Weitere Informationen

* [Authentifizieren von Anforderungen an Azure Cognitive Services](authentication.md)
* [Was ist Azure Cognitive Services?](./what-are-cognitive-services.md)
* [Schnellstart: Erstellen einer Azure Cognitive Services-Ressource mit der Clientbibliothek für die Azure-Verwaltung](.\cognitive-services-apis-create-account-client-library.md)
* [Unterstützung für natürliche Sprachen in Azure Cognitive Services](language-support.md)
* [Containerunterstützung in Azure Cognitive Services](cognitive-services-container-support.md)