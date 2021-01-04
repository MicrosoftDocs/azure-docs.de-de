---
title: 'Unternehmenskonzepte: LUIS'
titleSuffix: Azure Cognitive Services
description: Erfahren Sie etwas über die Designkonzepte für große LUIS-Apps oder mehrere Apps, die LUIS und QnA Maker zusammen verwenden.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.openlocfilehash: 2e2165b81c7cd634fe79ec4438a550ad365f5a30
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95019176"
---
# <a name="enterprise-strategies-for-a-luis-app"></a>Unternehmensstrategien für LUIS-Apps
Lesen Sie die folgenden Entwurfsstrategien für Unternehmens-Apps.

## <a name="when-you-expect-luis-requests-beyond-the-quota"></a>Wenn LUIS-Anforderungen über das Kontingent hinaus erwartet werden

Für LUIS gilt basierend auf dem Tarif der Azure-Ressource sowohl ein monatliches Kontingent als auch ein sekundenbasiertes Kontingent. 

Überschreitet die Anforderungsrate Ihrer LUIS-App die zulässige [Kontingentrate](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/), haben Sie folgenden Möglichkeiten:

* Verteilen Sie die Last auf mehrere LUIS-Apps mit [derselben App-Definition](#use-multiple-apps-with-same-app-definition). Dazu gehört optional auch die Ausführung von LUIS über einen [Container](luis-container-howto.md). 
* Sie müssen [mehrere Schlüssel erstellen und der App zuweisen](#assign-multiple-luis-keys-to-same-app). 

### <a name="use-multiple-apps-with-same-app-definition"></a>Verwenden mehrerer Apps mit derselben App-Definition
Exportieren Sie die ursprüngliche LUIS-App, und importieren Sie die App dann wieder in separaten Apps. Jede App verfügt über eine eigene App-ID. Beim Veröffentlichen verwenden Sie nicht denselben Schlüssel für alle Apps, sondern erstellen einen separaten Schlüssel für jede App. Führen Sie einen Lastenausgleich über alle Apps aus, sodass keine Einzel-App überlastet ist. Hinzufügen von [Application Insights](./luis-csharp-tutorial-bf-v4.md) zum Überwachen der Nutzung 

Damit alle Apps die gleiche Absicht am besten bewerten, stellen Sie sicher, dass die Absichtsvorhersage zwischen der ersten und zweiten Absicht unterschiedlich genug ist, damit LUIS sie nicht verwechselt. Andernfalls würden die Apps bei geringfügigen Unterschieden in den Äußerungen verschiedene Ergebnisse liefern. 

Stellen Sie beim Trainieren dieser Apps sicher, dass sie [mit allen Daten trainiert werden](luis-how-to-train.md#train-with-all-data).

Legen Sie eine App als Master fest. Alle Äußerungen, die zur Überprüfung vorgeschlagen werden, sollten der Master-App hinzugefügt und dann wieder zurück in die anderen Apps verschoben werden. Dazu kann ein vollständiger Export der App erfolgen, oder Sie laden die bezeichneten Äußerungen vom Master in die untergeordneten Apps. Für das Laden können Sie die [LUIS](luis-reference-regions.md)-Website oder die Erstellungs-API für eine [einzelne Äußerung](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c08) oder einen [Batch](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09) verwenden. 

Planen Sie eine regelmäßige Überprüfung der [Endpunktäußerungen](luis-how-to-review-endpoint-utterances.md) ein (z. B. alle zwei Wochen), um das aktive Lernen zu fördern, und trainieren und veröffentlichen Sie die App dann erneut. 

### <a name="assign-multiple-luis-keys-to-same-app"></a>Zuweisen mehrerer LUIS-Schlüssel zu derselben App
Wenn Ihre LUIS-App mehr Endpunkttreffer erhält, als das Kontingent für einen einzelnen Schlüssel zulässt, erstellen Sie zusätzliche Schlüssel und weisen sie der LUIS-App zu. Erstellen Sie ein Modul für die Verwaltung des Datenverkehrs oder den Lastenausgleich, um die Endpunktabfragen über die Endpunktschlüssel zu verwalten. 

## <a name="when-your-monolithic-app-returns-wrong-intent"></a>Wenn eine monolithische App falsche Absichten zurückgibt
Wenn Ihre App dafür vorgesehen ist, eine Vielzahl von Benutzeräußerungen vorherzusagen, sollten Sie die Implementierung des [Dispatchmodells](#dispatch-tool-and-model) erwägen. Durch das Aufteilen einer monolithischen App kann sich LUIS auf die Erkennung unterschiedlicher Absichten konzentrieren, und es kommt nicht zu Verwechslungen zwischen Absichten der übergeordneten und untergeordneten Apps. 

Planen Sie eine regelmäßige [Überprüfung der Endpunktäußerungen](luis-how-to-review-endpoint-utterances.md) ein (z.B. alle zwei Wochen), um das aktive Lernen zu fördern, und trainieren und veröffentlichen Sie die App dann erneut. 

## <a name="when-you-need-to-have-more-than-500-intents"></a>Wenn Sie mehr als 500 Absichten benötigen
Angenommen, Sie entwickeln einen Office-Assistenten, der mehr als 500 Absichten verarbeitet. Wenn sich 200 Absichten auf die Planung von Besprechungen, 200 auf Erinnerungen, 200 auf das Abrufen von Informationen zu Kollegen und 200 auf das Senden von E-Mails beziehen, sollten Sie die Absichten so gruppieren, dass sich jede Gruppe in einer App befindet. Erstellen Sie anschließend eine übergeordnete App, die sämtliche Absichten enthält. Nutzen Sie zum Erstellen der übergeordneten Apps das [Dispatchmodell](#dispatch-tool-and-model). Ändern Sie anschließend Ihren Bot so, dass er einen kaskadierenden Aufruf durchführt, wie im [Tutorial zum Dispatchmodell](/azure/bot-service/bot-builder-tutorial-dispatch?branch=master&tabs=cs&view=azure-bot-service-4.0) gezeigt. 

## <a name="when-you-need-to-combine-several-luis-and-qna-maker-apps"></a>Kombinieren mehrerer LUIS- und QnA Maker-Apps
Wenn Sie über mehrere LUIS- und QnA Maker-Apps verfügen, die auf einen Bot reagieren müssen, verwenden Sie das [Dispatchmodell](#dispatch-tool-and-model) zum Erstellen der übergeordneten App.  Ändern Sie anschließend Ihren Bot so, dass er einen kaskadierenden Aufruf durchführt, wie im [Tutorial zum Dispatchmodell](/azure/bot-service/bot-builder-tutorial-dispatch?branch=master&tabs=cs&view=azure-bot-service-4.0) gezeigt. 

## <a name="dispatch-tool-and-model"></a>Dispatchtool und -modell
Verwenden Sie das Befehlszeilentool [Dispatch][dispatch-tool] in den [BotBuilder-Tools](https://github.com/Microsoft/botbuilder-tools), um mehrere LUIS- und/oder QnA Maker-Apps in einer übergeordneten LUIS-App zusammenzufassen. Dieser Ansatz ermöglicht es Ihnen, eine übergeordnete Domäne zu verwenden, die alle Themen und die unterschiedlichen untergeordneten Themendomänen in getrennten Apps enthält. 

![Konzeptionelle Darstellung der Dispatcharchitektur](./media/luis-concept-enterprise/dispatch-architecture.png)

Die übergeordnete Domäne ist in LUIS mit einer Version mit dem Namen `Dispatch` in der Liste der Apps vermerkt. 

Der Chatbot empfängt die Äußerung und sendet sie für die Vorhersage an die übergeordnete LUIS-App. Die bestbewertete Vorhersage der Absicht aus der übergeordneten App bestimmt, welche untergeordnete LUIS-App als Nächstes aufgerufen wird. Der Chatbot sendet die Äußerung an die untergeordnete App, um eine genauere Vorhersage zu erhalten.

Weitere Informationen zur Hierarchie der Aufrufe von Bot Builder v4 finden Sie im [Tutorial zur Dispatcheranwendung](/azure/bot-service/bot-builder-tutorial-dispatch?branch=master&tabs=cs&view=azure-bot-service-4.0).  

### <a name="intent-limits-in-dispatch-model"></a>Grenzwerte für Absichten im Dispatchmodell
Eine Dispatchanwendung hat maximal 500 Dispatchquellen, die 500 Absichten entsprechen. 

## <a name="more-information"></a>Weitere Informationen

* [Bot Framework SDK](https://github.com/Microsoft/botframework)
* [Verwenden von mehreren LUIS- und QnA-Modellen](/azure/bot-service/bot-builder-tutorial-dispatch?branch=master&tabs=cs&view=azure-bot-service-4.0)
* [Dispatch CLI](https://github.com/Microsoft/botbuilder-tools)
* Botbeispiel für das Dispatchmodell: [.NET](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/14.nlp-with-dispatch), [Node.js](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/javascript_nodejs/14.nlp-with-dispatch)

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie, wie Sie [in Batches testen](luis-how-to-batch-test.md).

[dispatcher-application-tutorial]: /azure/bot-service/bot-builder-tutorial-dispatch?branch=master
[dispatch-tool]: https://aka.ms/dispatch-tool