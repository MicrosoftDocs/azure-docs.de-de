---
title: Erstellen einer Cognitive Services-Ressource zur Textanalyse
titleSuffix: Azure Cognitive Services
description: Erfahren Sie, wie Sie eine Cognitive Services-Ressource zur Textanalyse erstellen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: daafea59b96cc8da6b78f0733c9f54e0e4d3a8c2
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96009901"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>Erstellen einer Cognitive Services-Ressource zur Textanalyse

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Klicken Sie auf **Ressource erstellen**, und navigieren Sie dann zu **KI + Machine Learning** > **Textanalyse**.
   Alternativ können Sie zu [Textanalyse erstellen](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) navigieren.
1. Geben Sie alle erforderlichen Einstellungen ein:

    |Einstellung|Wert|
    |--|--|
    |Name|Geben Sie einen Namen ein (2 bis 64 Zeichen).|
    |Subscription|Wählen Sie das entsprechende Abonnement aus.|
    |Standort|Wählen Sie einen nahe gelegenen Speicherort aus.|
    |Tarif| Geben Sie **S** für den Tarif „Standard“ ein.|
    |Resource group|Wählen Sie eine verfügbare Ressourcengruppe aus.|

1. Klicken Sie auf **Erstellen**, und warten Sie, bis die Ressource erstellt wird. Ihr Browser leitet Sie automatisch zur neu erstellten Ressourcenseite weiter.
1. Rufen Sie den konfigurierten `endpoint` und einen API-Schlüssel ab:

    |Registerkarte „Ressource“ im Portal|Einstellung|Wert|
    |--|--|--|
    |**Übersicht**|Endpunkt|Kopieren Sie den Endpunkt. Dieser sieht etwa so aus: `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0`.|
    |**Schlüssel**|API-Schlüssel|Kopieren Sie einen der beiden Schlüssel. Es handelt sich um eine aus 32 alphanumerischen Zeichen bestehende Zeichenfolge ohne Leerzeichen oder Bindestriche: <`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`>.|
