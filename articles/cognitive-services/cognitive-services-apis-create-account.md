---
title: Erstellen eines Cognitive Services-Kontos im Azure-Portal
titlesuffix: Azure Cognitive Services
description: Anleitung zum Erstellen eines Azure Cognitive Services-APIs-Kontos im Azure-Portal.
services: cognitive-services
author: garyericson
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: garye
ms.openlocfilehash: 831f1d22c4da215bed3ed55b659332aa3b57472b
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66145938"
---
# <a name="quickstart-create-a-cognitive-services-account-in-the-azure-portal"></a>Schnellstart: Erstellen eines Cognitive Services-Kontos im Azure-Portal

In diesem Schnellstart erfahren Sie, wie Sie sich für Azure Cognitive Services registrieren und ein Abonnement mit einem einzelnen Dienst oder mit mehreren Diensten erstellen. Diese Dienste werden durch Azure-[Ressourcen](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal) repräsentiert, die es Ihnen ermöglichen, Verbindungen mit einer oder mehreren der vielen verfügbaren Azure Cognitive Services-APIs herzustellen.

## <a name="prerequisites"></a>Voraussetzungen

* Ein gültiges Azure-Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/).

## <a name="create-and-subscribe-to-an-azure-cognitive-services-resource"></a>Erstellen und Abonnieren einer Azure Cognitive Services-Ressource

Bevor Sie beginnen, sollten Sie wissen, dass es zwei Arten von Azure Cognitive Services-Abonnements gibt. Das erste ist ein Abonnement für einen einzelnen Dienst, z.B. maschinelles Sehen oder Speech Services. Ein Abonnement für einen einzelnen Dienst ist auf diese Ressource beschränkt. Das zweite ist ein Abonnement für mehrere Dienste von Azure Cognitive Services. Mit diesem Abonnement können Sie ein einziges Abonnement für den Großteil der Dienste von Azure Cognitive Services verwenden. Diese Option umfasst auch eine gemeinsame Abrechnung. Weitere Informationen finden Sie unter [Cognitive Services – Preise](https://azure.microsoft.com/pricing/details/cognitive-services/).

>[!WARNING]
> Zum derzeitigen Zeitpunkt unterstützen diese Dienste Schlüssel für mehrere Dienste **nicht**: QnA Maker, Speech-Dienste, Custom Vision und Anomalieerkennung.

In den nächsten Abschnitten werden Sie durch das Erstellen eines Abonnements für einen oder mehrere Dienste geführt.


### <a name="multi-service-subscription"></a>Abonnement für mehrere Dienste

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und klicken Sie auf **+Ressource erstellen**.

    ![Auswahl Cognitive Services-APIs](media/cognitive-services-apis-create-account/azurePortalScreenMulti.png)

2. Suchen Sie die Suchleiste, und geben Sie Folgendes ein: **Cognitive Services**.

    ![Suchen nach Cognitive Services](media/cognitive-services-apis-create-account/azureCogServSearchMulti.png)

3. Wählen Sie **Cognitive Services** aus.

    ![Auswählen von Cognitive Services](media/cognitive-services-apis-create-account/azureMarketplaceMulti.png)

3. Geben Sie auf der Seite **Erstellen** die folgenden Informationen ein:

    |    |    |
    |--|--|
    | **Name** | Ein beschreibender Name für Ihre Cognitive Services-Ressource. Es wird empfohlen, einen aussagekräftigen Namen zu wählen, z.B. *MeinCognitiveServicesKonto*. |
    | **Abonnement** | Wählen Sie eines Ihrer verfügbaren Azure-Abonnements aus. |
    | **Location** | Der Speicherort Ihrer Cognitive Services-Instanz. Verschiedene Speicherorte können Wartezeiten verursachen, haben aber keinen Einfluss auf die Laufzeitverfügbarkeit Ihrer Ressource. |
    | **Preisstufe** | Die Kosten für Ihr Cognitive Services-Konto hängen von den ausgewählten Optionen und Ihrer Nutzung ab. Weitere Informationen finden Sie unter API-[Preise](https://azure.microsoft.com/pricing/details/cognitive-services/).
    | **Ressourcengruppe** | Die [Azure-Ressourcengruppe](https://docs.microsoft.com/azure/architecture/cloud-adoption/governance/resource-consistency/azure-resource-access#what-is-an-azure-resource-group), die Ihre Cognitive Services-Ressource enthält. Sie können eine neue Gruppe erstellen oder sie einer bereits bestehenden Gruppe hinzufügen. |

    ![Bildschirm „Ressourcenerstellung“](media/cognitive-services-apis-create-account/resource_create_screen_multi.png)

### <a name="single-service-subscription"></a>Abonnement für einen Dienst

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und klicken Sie auf **+Ressource erstellen**.

    ![Auswahl Cognitive Services-APIs](media/cognitive-services-apis-create-account/azurePortalScreen.png)

2. Wählen Sie unter Azure Marketplace die Option **KI und Machine Learning** aus. Wenn der für Sie interessante Dienst nicht angezeigt wird, klicken Sie auf **Alle anzeigen**, um den gesamten Katalog der Cognitive Services-APIs anzuzeigen.

    ![Auswahl Cognitive Services-APIs](media/cognitive-services-apis-create-account/azureMarketplace.png)

3. Geben Sie auf der Seite **Erstellen** die folgenden Informationen ein:

    |    |    |
    |--|--|
    | **Name** | Ein beschreibender Name für Ihre Cognitive Services-Ressource. Es wird empfohlen einen aussagekräftigen Namen zu wählen, z. B. *MyNameFaceAPIAccount*. |
    | **Abonnement** | Wählen Sie eines Ihrer verfügbaren Azure-Abonnements aus. |
    | **Location** | Der Speicherort Ihrer Cognitive Services-Instanz. Verschiedene Speicherorte können Wartezeiten verursachen, haben aber keinen Einfluss auf die Laufzeitverfügbarkeit Ihrer Ressource. |
    | **Preisstufe** | Die Kosten für Ihr Cognitive Services-Konto hängen von den ausgewählten Optionen und Ihrer Nutzung ab. Weitere Informationen finden Sie unter API-[Preise](https://azure.microsoft.com/pricing/details/cognitive-services/).
    | **Ressourcengruppe** | Die [Azure-Ressourcengruppe](https://docs.microsoft.com/azure/architecture/cloud-adoption/governance/resource-consistency/azure-resource-access#what-is-an-azure-resource-group), die Ihre Cognitive Services-Ressource enthält. Sie können eine neue Gruppe erstellen oder sie einer bereits bestehenden Gruppe hinzufügen. |

    ![Bildschirm „Ressourcenerstellung“](media/cognitive-services-apis-create-account/resource_create_screen.png)

## <a name="access-your-resource"></a>Zugreifen auf Ihre Ressource

> [!NOTE]
> Abonnementbesitzer können die Erstellung eines Cognitive Services-Kontos für Ressourcengruppen und Abonnements durch Anwenden von [Azure-Richtlinien](https://docs.microsoft.com/azure/governance/policy/overview#policy-definition) deaktivieren, indem die Richtliniendefinition „Nicht zulässige Ressourcentypen“ zugewiesen und **Microsoft.CognitiveServices/accounts** als Zielressourcentyp angegeben wird.

Nachdem Sie Ihre Ressource erstellt haben, können Sie sie über das Azure Dashboard aufrufen, wenn Sie sie angeheftet haben. Andernfalls finden Sie sie in **Ressourcengruppen**.

Innerhalb Ihrer Cognitive Services-Ressource können Sie die Endpunkt-URL und Schlüssel im Abschnitt **Übersicht** verwenden, um API-Aufrufe in Ihren Anwendungen zu starten.

![Bildschirm „Ressourcen“](media/cognitive-services-apis-create-account/resourceScreen.png)

Notieren Sie sich die Position und die Schlüssel. Sie können die Schlüssel abrufen, indem Sie **Schlüssel** unter **Ressourcenverwaltung** auswählen.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Authentifizieren von Anforderungen an Azure Cognitive Services](authentication.md)

## <a name="see-also"></a>Weitere Informationen

* [Schnellstart: Extrahieren von handschriftlichem Text aus einem Bild](https://docs.microsoft.com/azure/cognitive-services/computer-vision/quickstarts/csharp-hand-text)
* [Tutorial: Erstellen einer App zum Erkennen und Umranden von Gesichtern in einem Bild](https://docs.microsoft.com/azure/cognitive-services/Face/Tutorials/FaceAPIinCSharpTutorial)
* [Erstellen einer Webseite für die benutzerdefinierte Suche](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/tutorials/custom-search-web-page)
* [Integrate Language Understanding (LUIS) mit einem Bot über das Bot Framework](https://docs.microsoft.com/azure/cognitive-services/luis/luis-nodejs-tutorial-build-bot-framework-sample)
