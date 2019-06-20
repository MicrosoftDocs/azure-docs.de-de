---
title: Konfigurieren von Einstellungen
titleSuffix: Azure Cognitive Services
description: Die Dienstkonfiguration umfasst die Art, wie der Dienst Belohnungen behandelt, wie oft der Dienst Untersuchungen durchführt, wie oft das Modell neu trainiert wird und wie viele Daten gespeichert werden.
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/07/2019
ms.author: edjez
ms.openlocfilehash: 976830232453eee0993e64ac445c2e6a2f7e20ef
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66478583"
---
# <a name="personalizer-settings"></a>Einstellungen der Personalisierung

Die Dienstkonfiguration umfasst die Art, wie der Dienst Belohnungen behandelt, wie oft der Dienst Untersuchungen durchführt, wie oft das Modell neu trainiert wird und wie viele Daten gespeichert werden.

## <a name="create-personalizer-resource"></a>Erstellen einer Personalisierungsressource

Erstellen Sie eine Personalisierungsressource für jede Feedbackschleife. 

1. Melden Sie sich beim [Azure-Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer)an. Über den vorherigen Link gelangen Sie zur Seite **Erstellen** für den Personalisierungsdienst. 
1. Geben Sie Ihren Dienstnamen ein, und wählen Sie ein Abonnement, einen Standort, einen Tarif und eine Ressourcengruppe aus.
1. Wählen Sie die Bestätigung und anschließend **Erstellen** aus.

## <a name="configure-service-settings-in-the-azure-portal"></a>Konfigurieren von Diensteinstellungen im Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer) an.
1. Suchen Sie Ihre Personalisierungsressource. 
1. Wählen Sie im Abschnitt **Ressourcenverwaltung** die Option **Einstellungen** aus.

    Kopieren Sie auf der Seite **Schlüssel** einen Ressourcenschlüssel, bevor Sie das Azure-Portal verlassen. Sie benötigen ihn für die Verwendung des [SDK für die Personalisierung](https://go.microsoft.com/fwlink/?linkid=2092353).

### <a name="configure-reward-settings-for-the-feedback-loop-based-on-use-case"></a>Konfigurieren der Belohnungseinstellungen für die Feedbackschleife auf der Grundlage von Anwendungsfällen

Konfigurieren Sie die Einstellungen des Diensts für die Verwendung von Belohnungen durch die Feedbackschleife. Änderungen an den folgenden Einstellungen setzen das aktuelle Personalisierungsmodell zurück und trainieren es mit den Daten der letzten 2 Tage neu:

![Konfigurieren der Belohnungseinstellungen für die Feedbackschleife](media/settings/configure-model-reward-settings.png)

|Einstellung|Zweck|
|--|--|
|Belohnungswartezeit|Legt die Zeitspanne, während der die Personalisierung Werte für einen Rangfolgeaufruf sammelt werden, ab dem Zeitpunkt fest, zu dem der Rangfolgeaufruf erfolgt. Dieser Wert wird festgelegt durch die Frage: „How long should Personalizer wait for rewards calls?“ (Wie lange sollte die Personalisierung auf Belohnungsaufrufe warten?) Jede Belohnung, die nach diesem Zeitfenster eingeht, wird protokolliert, aber nicht für das Lernen verwendet.|
|Standardbelohnung|Wenn während des Zeitfensters für das Warten auf die Belohnung kein Belohnungsaufruf bei der Personalisierung eingeht, weist die Personalisierung die Standardbelohnung zu. Standardmäßig und in den meisten Szenarien ist die Standardbelohnung 0 (null).|
|Belohnungsaggregation|Wenn mehrere Belohnungen für den gleichen Rangfolge-API-Aufruf empfangen werden, wird diese Aggregationsmethode verwendet: **sum** oder **earliest**. „Earliest“ wählt das am frühesten empfangene Ergebnis aus, und der Rest wird verworfen. Dies ist nützlich, wenn Sie eine eindeutige Belohnung zwischen möglicherweise doppelten Aufrufen wünschen. |

Stellen Sie nach dem Ändern dieser Einstellungen sicher, dass Sie **Save** (Speichern) auswählen.

### <a name="exploration-setting"></a>Durchsuchungseinstellung 

Die Personalisierung kann neue Muster ermitteln und im Laufe der Zeit den Verhaltensänderungen der Benutzer durch Durchsuchen von Alternativen anpassen. Die Einstellung **Exploration** (Durchsuchen) bestimmt, wie viel Prozent der Rangfolgeaufrufe mit Durchsuchen beantwortet werden. 

Änderungen an dieser Einstellung setzen das aktuelle Personalisierungsmodell zurück und trainieren es mit den Daten der letzten 2 Tage neu.

![Die Durchsuchungseinstellung bestimmt, wie viel Prozent der Rangfolgeaufrufe mit Durchsuchen beantwortet werden.](media/settings/configure-exploration-setting.png)

Stellen Sie nach dem Ändern dieser Einstellung sicher, dass Sie **Save** (Speichern) auswählen.

### <a name="model-update-frequency"></a>Häufigkeit der Modellaktualisierung

Vom Rangfolgeaufruf der Personalisierung wird nicht automatisch das neueste Modell verwendet, das auf der Grundlage der Relevanz-API-Aufrufe aller aktiven Ereignisse trainiert wurde. Die **Häufigkeit der Modellaktualisierung** legt fest, wie oft das vom Rangfolgeaufruf verwendete Modell aktualisiert wird. 

Eine hohe Modellaktualisierungshäufigkeit ist hilfreich, wenn Veränderungen beim Benutzerverhalten genau nachverfolgt werden sollen. Beispiele wären etwa Websites mit Livenachrichten, viralen Inhalten oder Livegeboten für Produkte. In solchen Szenarien kann beispielsweise eine Frequenz von 15 Minuten verwendet werden. In den meisten Anwendungsfällen ist eine niedrigere Aktualisierungshäufigkeit sinnvoll. Eine Modellaktualisierungshäufigkeit von einer Minute empfiehlt sich beim Debuggen von Anwendungscode mit Personalisierung, bei Demos sowie beim interaktiven Testen von Machine Learning-Aspekten.

![„Model update frequency“ legt fest, wie oft ein neues Personalisierungsmodell neu trainiert wird.](media/settings/configure-model-update-frequency-settings.png)

Stellen Sie nach dem Ändern dieser Einstellung sicher, dass Sie **Save** (Speichern) auswählen.

### <a name="data-retention"></a>Beibehaltung von Daten

**Data retention period** (Zeitraum für die Datenaufbewahrung) legt fest, wie viele Tage die Personalisierung Datenprotokolle aufbewahrt. Frühere Datenprotokolle sind zur Durchführung von [Offlineauswertungen](concepts-offline-evaluation.md) erforderlich, die verwendet werden, um die Effektivität der Personalisierung zu messen und die Lernrichtlinie zu optimieren.

Stellen Sie nach dem Ändern dieser Einstellung sicher, dass Sie **Save** (Speichern) auswählen.

## <a name="export-the-personalizer-model"></a>Exportieren des Personalisierungsmodells

Überprüfen Sie im Abschnitt für **Model and Policy** (Modell und Richtlinie) der Ressourcenverwaltung Modellerstellung und Datum der letzten Aktualisierung, und exportieren Sie das aktuelle Modell. Sie können das Azure-Portal oder die Personalisierungs-APIs verwenden, um eine Modelldatei zu Archivierungszwecken zu exportieren. 

![Exportieren des aktuellen Personalisierungsmodells](media/settings/export-current-personalizer-model.png)

## <a name="import-and-export-learning-policy"></a>Importieren und Exportieren der Lernrichtlinie

Importieren Sie im Abschnitt für **Model and Policy** (Modell und Richtlinie) der Ressourcenverwaltung eine neue Lernrichtlinie, oder exportieren Sie die aktuelle Lernrichtlinie.

## <a name="next-steps"></a>Nächste Schritte

<!--
[How to use the Personalizer container](https://go.microsoft.com/fwlink/?linkid=2083923&clcid=0x409)
-->
[Erfahren Sie mehr zur regionalen Verfügbarkeit.](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)
