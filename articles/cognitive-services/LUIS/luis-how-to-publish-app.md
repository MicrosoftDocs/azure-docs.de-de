---
title: App veröffentlichen
titleSuffix: Azure Cognitive Services
description: Wenn Sie das Erstellen und Testen Ihrer aktiven LUIS-App abgeschlossen haben, stellen Sie sie Ihrer Clientanwendung zur Verfügung, indem Sie sie am Endpunkt veröffentlichen.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/08/2019
ms.author: diberry
ms.openlocfilehash: 22bed877d853c7023f8efe6bfb3dd21b4aa4c8df
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60194426"
---
# <a name="publish-your-active-trained-app-to-a-staging-or-production-endpoint"></a>Veröffentlichen Ihrer aktiven, trainierten App an einem Staging- oder Produktionsendpunkt

Wenn Sie das Erstellen und Testen Ihrer aktiven LUIS-App abgeschlossen haben, stellen Sie sie Ihrer Clientanwendung zur Verfügung, indem Sie sie am Endpunkt veröffentlichen. 

<a name="publish-your-trained-app-to-an-http-endpoint"></a>

## <a name="publishing"></a>Veröffentlichung

Um am Endpunkt zu veröffentlichen, wählen Sie im Panel rechts oben **Veröffentlichen** aus. 

![Obere rechte Navigationsleiste](./media/luis-how-to-publish-app/publish-top-nav-bar.png)

Wählen Sie den richtigen Slot aus, wenn das Popupfenster angezeigt wird: Staging oder Produktion. Durch die Verwendung von zwei Slots für die Veröffentlichung können Sie zwei unterschiedliche Versionen mit veröffentlichten Endpunkten oder die gleiche Version auf zwei unterschiedlichen Endpunkten nutzen. 

Die App wird in allen Regionen veröffentlicht, die den im LUIS-Portal hinzugefügten LUIS-Ressourcen zugeordnet sind. Wenn Sie beispielsweise für eine auf [www.luis.ai](https://www.luis.ai) erstellte App eine LUIS-Ressource in **westus** erstellen und sie der App als Ressource hinzufügen, wird die App in dieser Region veröffentlicht. Weitere Informationen zu LUIS-Regionen finden Sie unter [Regionen](luis-reference-regions.md).
 
![Popup-Fenster „Veröffentlichung“](./media/luis-how-to-publish-app/publish-pop-up.png)

Wenn Ihre App erfolgreich veröffentlicht wurde, wird eine grüne Erfolgsmeldung am oberen Rand des Browsers angezeigt. Die grüne Benachrichtigungsleiste enthält außerdem einen Link zu den Endpunkten. 

![Popupfenster „Veröffentlichung“ mit Link zum Endpunkt](./media/luis-how-to-publish-app/publish-success.png)

Wenn Sie die Endpunkt-URL benötigen, wählen Sie den Link aus. Ferner können Sie an die Endpunkt-URLs gelangen, indem Sie im oberen Menü **Verwalten** und dann in linken Menü **Schlüssel und Endpunkte** auswählen. 

## <a name="configuring-publish-settings"></a>Konfigurieren der Einstellungen für die Veröffentlichung

Konfigurieren Sie die Einstellungen für die Veröffentlichung, indem Sie im oberen rechten Navigationsbereich **Verwalten** und dann **Veröffentlichungseinstellungen** auswählen. 

![Veröffentlichungseinstellungen](./media/luis-how-to-publish-app/publish-settings.png)

### <a name="publish-after-enabling-sentiment-analysis"></a>Veröffentlichen nach dem Aktivieren der Standpunktanalyse

<a name="enable-sentiment-analysis"></a>

Die Standpunktanalyse ermöglicht LUIS die Integration mit der [Textanalyse](https://azure.microsoft.com/services/cognitive-services/text-analytics/), um die Analyse von Stimmungen und Formulierungen bereitzustellen. 

Sie müssen keine Textanalyseschlüssel bereitstellen, und es entstehen keine Abrechnungskosten für diesen Dienst in Ihrem Azure-Konto. Wenn Sie diese Einstellung einmal aktivieren, bleibt sie weiterhin gültig. 

Stimmungsdaten stellen eine Bewertung zwischen 1 und 0 dar, die eine eher positive (näher an 1) oder negative (näher an 0) Stimmung in den Daten angibt. Die Stimmungsbezeichnung von `positive`, `neutral` und `negative` gilt jeweils pro unterstützter Kultur. Derzeit unterstützt nur Englisch Stimmungsbezeichnungen. 

Weitere Informationen zur JSON-Endpunktantwort im Zusammenhang mit der Standpunktanalyse finden Sie unter [Standpunktanalyse](luis-concept-data-extraction.md#sentiment-analysis).

## <a name="next-steps"></a>Nächste Schritte

* Informationen zum Hinzufügen von Schlüsseln zum Azure-Abonnementschlüssel für LUIS und zum Einrichten des Schlüssels der Bing-Rechtschreibprüfung sowie zum Einschließen aller Absichten in Ergebnissen finden Sie unter [Schlüssel verwalten](./luis-how-to-azure-subscription.md).
* Weitere Anweisungen dazu, wie Sie Ihre veröffentlichte App in der Testkonsole testen, finden Sie unter [Trainieren und Testen Ihrer App](luis-interactive-test.md).

