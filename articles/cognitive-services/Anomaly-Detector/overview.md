---
title: Worum handelt es sich bei der Anomalieerkennungs-API? | Microsoft-Dokumentation
titleSuffix: Azure Cognitive Services
description: Verwenden Sie den erweiterten Algorithmus der Anomalieerkennungs-API, um Anomalien in Zeitreihendaten zu identifizieren.
services: cognitive-services
author: aahill
ms.service: cognitive-services
ms.subservice: anomaly-detection
ms.topic: article
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 48dffb8853d3def590e434c1683ecf038e2a194e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64415834"
---
# <a name="what-is-the-anomaly-detector-api"></a>Worum handelt es sich bei der Anomalieerkennungs-API?

Die Anomalieerkennungs-API bietet Ihnen die Möglichkeit, Anomalien in Zeitreihendaten durch maschinelles Lernen zu überwachen und zu erkennen. Die Anomalieerkennungs-API passt sich an, indem die am besten passenden Modelle für Ihre Daten automatisch identifiziert und angewendet werden, unabhängig von der Branche, dem Szenario oder der Datenmenge. Mithilfe der Zeitreihendaten bestimmt die API die Grenzen für die Anomalieerkennung, die erwarteten Werte und, welche Datenpunkte Anomalien sind.

![Erkennen von Musteränderungen in Dienstanforderungen](./media/anomaly_detection2.png)

Für die Verwendung der Anomalieerkennung sind keine Erfahrungen im Bereich des maschinellen Lernens erforderlich. Dank der RESTful-API können Sie den Dienst einfach in Ihre Anwendungen und Prozesse integrieren.

## <a name="features"></a>Features

Mit der Anomalieerkennung können Sie Anomalien in allen Zeitreihendaten, oder auch während sie in Echtzeit auftreten, automatisch erkennen. 

|Feature  |BESCHREIBUNG  |
|---------|---------|
|Erkennen von Anomalien in Echtzeit | Anomalien werden in Ihren Streamingdaten erkannt, indem anhand vorheriger Datenpunkte ermittelt wird, ob der letzte Punkt eine Anomalie ist. Bei diesem Vorgang wird anhand der von Ihnen gesendeten Datenpunkte ein Modell generiert und bestimmt, ob der Zielpunkt eine Anomalie ist. Durch Aufrufen der API mit jedem neu generierten Datenpunkt können Sie Daten während der Erstellung überwachen. |
|Erkennen von Anomalien für das ganze Dataset als Batch | Die Zeitreihe wird verwendet, um alle Anomalien zu erkennen, die in den gesamten Daten potenziell vorhanden sind. Bei diesem Vorgang wird anhand der gesamten Zeitreihendaten ein Modell generiert, bei dem jeder Punkt mit demselben Modell analysiert wird.         |
| Erhalten zusätzlicher Informationen zu Ihren Daten | Erhalten Sie hilfreiche Details zu Ihren Daten und allen beobachteten Anomalien, wie erwarteten Werten, Anomaliegrenzen und Positionen. |
| Anpassen der Grenzen für die Anomalieerkennung | Die Anomalieerkennungs-API erstellt automatisch Grenzen für die Anomalieerkennung. Passen Sie diese Grenzen an, um die Empfindlichkeit der API für Datenanomalien zu erhöhen oder zu verringern, sodass diese besser zu Ihren Daten passt. |

## <a name="demo"></a>Demo

Eine kurze Erklärung der ersten Schritte mit der Anomalieerkennungs-API finden Sie in einer [Onlinedemo](https://notebooks.azure.com/AzureAnomalyDetection/projects/anomalydetector), die Sie einfach in einem Browser ansehen können. Diese Demo wird in einem Jupyter-Notebook, das im Web gehostet wird, ausgeführt und zeigt, wie eine API-Anforderung gesendet und das Ergebnis visualisiert wird.

Führen Sie die folgenden Schritte aus, um die Demo anzusehen:

1. Holen Sie sich einen gültigen Abonnementschlüssel für die Anomalieerkennungs-API sowie einen API-Endpunkt. Im folgenden Abschnitt finden Sie Anweisungen zur Registrierung. 
2. Melden Sie sich an, und klicken Sie rechts oben in der Ecke auf „Klon“.
3. Klicken Sie auf **Run on free compute (Mit kostenlosem Compute ausführen)** .
4. Wählen Sie für dieses Beispiel eines der Notebooks aus.
5. Fügen Sie Ihren gültigen Abonnementschlüssel für die Anomalieerkennungs-API zur Variablen `subscription_key` hinzu. Ändern Sie die Variable `endpoint` in Ihren Endpunkt. Beispiel: `https://westus2.api.cognitive.microsoft.com`
1. Klicken Sie auf der oberen Menüleiste auf **Zelle** und dann auf **Alle ausführen**.

## <a name="workflow"></a>Workflow

Die Anomalieerkennungs-API ist ein RESTful-Webdienst und kann somit problemlos in jeder Programmiersprache aufgerufen werden, die HTTP-Anforderungen beherrscht und JSON analysieren kann.

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

Nach der Registrierung:

1. Konvertieren Sie die Zeitreihendaten in ein gültiges JSON-Format. Folgen Sie den [bewährten Methoden](concepts/anomaly-detection-best-practices.md), wenn Sie die Daten vorbereiten, um die besten Ergebnisse zu erzielen.
1. Senden Sie eine Anforderung mit Ihren Daten an die Anomalieerkennungs-API.
1. Analysieren Sie die zurückgegebene JSON-Nachricht, um die API-Antwort zu verarbeiten.

## <a name="next-steps"></a>Nächste Schritte

* [Schnellstart: Erkennen von Anomalien in Zeitreihendaten mithilfe der Anomalieerkennungs-REST-API](quickstarts/detect-data-anomalies-csharp.md)
* [Onlinedemo](https://notebooks.azure.com/AzureAnomalyDetection/projects/anomalydetector) zur Anomalieerkennungs-API
* [REST-API-Referenz](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect) zur Anomalieerkennung