---
title: Registrieren für die Textanalyse-API
titleSuffix: Azure Cognitive Services
description: Anweisungen zum Registrieren und Verwenden des Textanalyse-Diensts.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: aahi
ms.openlocfilehash: 53532a19482a33f8727e71d44ae169225b5b1c98
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60829666"
---
# <a name="how-to-sign-up-for-the-text-analytics-api"></a>Registrieren für die Textanalyse-API

Ressourcen für die Textanalyse sind in der Cloud an sieben Tagen der Woche rund um die Uhr verfügbar. Bevor Sie Ihre Inhalte zur Analyse hochladen können, müssen Sie sich registrieren, um einen Zugriffsschlüssel zu erhalten. Bei jedem Aufruf der API ist ein Zugriffsschlüssel für die Anforderung erforderlich.

+ Als Erstes benötigen Sie ein Azure-Abonnement. Sie können ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, um kostenlos zu experimentieren.

+ Erstellen Sie ein [Cognitive Services-API-Konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account), und wählen Sie dabei die **Textanalyse-API** aus. Ihr Schlüssel wird generiert, wenn Sie sich registrieren.

Für die Textanalyse sind ein Free-Tarif zur Erkundung und Auswertung sowie gebührenpflichtige Tarife für Produktionsworkloads verfügbar. In jedem Abonnement sind mehrere Registrierungen möglich: eine für den kostenlosen Dienst, eine für den gebührenpflichtigen Dienst usw. Wenn Ihr Anforderungsvolumen zunimmt, können Sie zu einem Tarif wechseln, der eine höhere Anzahl von Transaktionen bietet.

Es gibt keine Vereinbarung zum Servicelevel für Dienste in der Vorschauversion oder im Free-Tarif. Weitere Informationen finden Sie unter [SLA für Cognitive Services](https://azure.microsoft.com/support/legal/sla/cognitive-services/v1_1/).

## <a name="how-to-change-tiers"></a>Wechseln des Tarifs

Beginnen Sie mit einem Free-Tarif, und wechseln Sie dann zu einem gebührenpflichtigen Tarif für Produktionsworkloads. Die Standardabrechnung wird mit einer Staffelung angeboten. Sie können zwischen Tarifen wechseln und dennoch die gleichen Endpunkt- und Zugriffsschlüssel beibehalten.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und [suchen Sie nach Ihrem Dienst](text-analytics-how-to-access-key.md).

2. Klicken Sie auf **Tarif**.

   ![Befehl „Tarif“ im linken Navigationsmenü](../media/portal-pricing-tier.png)

3. Wählen Sie einen Tarif aus, und klicken Sie auf **Auswählen**.  Die neuen Grenzwerte werden wirksam, sobald die Auswahl verarbeitet wurde. 

   ![Kacheln und Schaltfläche „Auswählen“ auf der Auswahlseite für den Tarif](../media/portal-choose-tier.png)

## <a name="how-billing-works"></a>Funktionsweise der Abrechnung

Die Abrechnung basiert auf der Anzahl von Transaktionen. Sie können einen Block von Transaktionen zu einem bestimmten Tarif in einem monatlichen Abrechnungszyklus erwerben. Wenn Sie wechseln, wird dann eine geringe Überschreitungsgebühr pro Transaktion angewendet. Wenn Sie den maximalen Grenzwert regelmäßig überschreiten, sollten Sie ggf. zu einem höheren Tarif wechseln.

Weitere Informationen finden Sie in der [Preisübersicht](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/).

### <a name="what-constitutes-a-transaction-in-the-text-analytics-api"></a>Was gilt in der Textanalyse-API als Transaktion?
Jede Anmerkung zu einem Dokument zählt als Transaktion. Bei Batchbewertungsaufrufen wird auch die Anzahl von Dokumenten berücksichtigt, die in der jeweiligen Transaktion bewertet werden müssen. Wenn beispielsweise in einem einzelnen API-Aufruf 1.000 Dokumente zur Stimmungsanalyse gesendet werden, werden 1.000 Transaktionen gezählt.

## <a name="see-also"></a>Weitere Informationen 

 [Übersicht über die Textanalyse](../overview.md)  
 [Häufig gestellte Fragen (FAQ)](../text-analytics-resource-faq.md)

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Get an access key](text-analytics-how-to-access-key.md) (Abrufen eines Zugriffsschlüssels)
