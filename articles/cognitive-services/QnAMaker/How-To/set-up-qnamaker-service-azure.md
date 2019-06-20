---
title: 'Einrichten eines QnA Maker-Diensts: QnA Maker'
titleSuffix: Azure Cognitive Services
description: Vor dem Erstellen von QnA Maker-Wissensdatenbanken müssen Sie zuerst einen QnA Maker-Dienst in Azure einrichten. Jeder mit der Berechtigung zum Erstellen neuer Ressourcen in einem Abonnement kann einen QnA Maker-Dienst einrichten.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 05/13/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 239633950e4672693e3684345749f9c46f01c037
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65605665"
---
# <a name="create-a-qna-maker-service"></a>Erstellen eines QnA Maker-Diensts

Vor dem Erstellen von QnA Maker-Wissensdatenbanken müssen Sie zuerst einen QnA Maker-Dienst in Azure einrichten. Jeder mit der Berechtigung zum Erstellen neuer Ressourcen in einem Abonnement kann einen QnA Maker-Dienst einrichten.

## <a name="create-a-new-service"></a>Erstellen eines neuen Diensts

Bei diesem Verfahren werden einige Azure-Ressourcen bereitgestellt. In Kombination verwalten diese Ressourcen die Inhalte der Wissensdatenbank und stellen die Fähigkeit zum Beantworten von Fragen über einen Endpunkt zur Verfügung.

1. Melden Sie sich beim Azure-Portal an, und [erstellen Sie eine QnA Maker-Ressource](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker).

1. Wählen Sie **Erstellen** aus, nachdem Sie die Geschäftsbedingungen gelesen haben.

    ![Erstellen eines neuen QnA Maker-Diensts](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

1. Wählen Sie in **QnA Maker** die geeigneten Tarife und Regionen aus.

    ![Erstellen eines neuen QnA Maker-Diensts – Tarif und Regionen](../media/qnamaker-how-to-setup-service/enter-qnamaker-info.png)

    * Setzen Sie in **Name** einen eindeutigen Namen zur Identifizierung dieses QnA Maker-Diensts ein. Dieser Name kennzeichnet darüber hinaus den QnA Maker-Endpunkt, dem Ihre Wissensdatenbanken zugeordnet werden.
    * Wählen Sie das **Abonnement** aus, in dem die QnA Maker-Ressource bereitgestellt werden soll.
    * Wählen Sie den **Tarif** für die QnA Maker-Verwaltungsdienste (Portal und Verwaltungs-APIs) aus. Details zur den Preisen für SKUs finden Sie [hier](https://aka.ms/qnamaker-pricing).
    * Erstellen Sie eine neue **Ressourcengruppe** (empfohlen), oder verwenden Sie eine vorhandene Ressourcengruppe, in der diese QnA Maker-Ressource bereitgestellt werden soll. QnA Maker erstellt verschiedene Azure-Ressourcen. Wenn Sie eine Ressourcengruppe erstellen, um diese Ressourcen zu verwalten, können Sie diese Ressourcen anhand des Namens der Ressourcengruppe leicht finden, verwalten und löschen.
    * Wählen Sie einen **Ressourcengruppenstandort** aus.
    * Wählen Sie den **Tarif für Suche** des Azure Search-Diensts aus. Wenn die Free-Tarifoption abgeblendet angezeigt wird, bedeutet dies, dass Sie bereits einen kostenlosen Azure Search-Tarif in Ihrem Abonnement bereitgestellt haben. In diesem Fall müssen Sie mit dem Basic Azure Search-Tarif einsteigen. Details zu den Preisen von Azure Search finden Sie [hier](https://azure.microsoft.com/pricing/details/search/).
    * Wählen Sie den **Suchspeicherort** aus, an dem die Azure Search-Daten bereitgestellt werden sollen. Einschränkungen zu den verpflichtenden Speicherorten für Kundendaten ergeben sich durch Ihre Wahl des Speicherorts für Azure Search.
    * Geben Sie Ihrem App Service unter **App-Name** einen Namen.
    * Standardmäßig wird der App Service nach dem Standardtarif (S1) abgerechnet. Sie können den Plan nach der Erstellung ändern. Weitere Details zu App Service-Preisen finden Sie [hier](https://azure.microsoft.com/pricing/details/app-service/).
    * Wählen Sie den **Website-Speicherort** aus, an dem der App Service bereitgestellt wird.

        > [!NOTE]
        > Der Suchspeicherort kann vom Website-Speicherort abweichen.

    * Wählen Sie, ob Sie **Application Insights** aktivieren möchten. Wenn **Application Insights** aktiviert ist, sammelt QnA Maker Telemetriedaten zu Datenverkehr, Chatprotokollen und Fehlern.
    * Wählen Sie den **App Insights-Speicherort** aus, an dem die Application Insights-Ressource bereitgestellt werden soll.
    * Zur Kosteneinsparung können Sie einige, aber nicht alle Azure-Ressourcen, die für QnA Maker erstellt wurden, [freigeben](upgrade-qnamaker-service.md?#share-existing-services-with-qna-maker). 

1. Nachdem alle Felder überprüft wurden, können Sie **Erstellen** auswählen, um die Bereitstellung dieser Dienste in Ihrem Abonnement zu starten. Dies nimmt einige Minuten in Anspruch.

1. Nachdem die Bereitstellung abgeschlossen ist, sehen Sie die folgenden neu erstellten Ressourcen in Ihrem Abonnement.

    ![Im neuen QnA Maker-Dienst erstellte Ressource](../media/qnamaker-how-to-setup-service/resources-created.png)

## <a name="region-of-management-service"></a>Region des Verwaltungsdiensts

Der Verwaltungsdienst von QnA Maker, der nur für das Portal und die anfängliche Datenverarbeitung verwendet wird, ist nur in den USA (Westen) verfügbar. In diesem Dienst (USA, Westen) werden keine Kundendaten gespeichert.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erstellen und Veröffentlichen einer Wissensdatenbank](../Quickstarts/create-publish-knowledge-base.md)
