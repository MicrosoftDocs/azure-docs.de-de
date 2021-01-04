---
title: Exportieren und Löschen von Daten – LUIS
titleSuffix: Azure Cognitive Services
description: Sie haben die volle Kontrolle über das Anzeigen, Exportieren und Löschen ihrer Daten. Löschen Sie Kundendaten, um Datenschutz und Compliance zu gewährleisten.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 12/08/2020
ms.openlocfilehash: cd9b353300b88c69efb0c458245a86963c579064
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97007737"
---
# <a name="export-and-delete-your-customer-data-in-language-understanding-luis-in-cognitive-services"></a>Exportieren und Löschen Ihrer Kundendaten aus LUIS in Cognitive Services

Löschen Sie Kundendaten, um Datenschutz und Compliance zu gewährleisten.

## <a name="summary-of-customer-data-request-features"></a>Zusammenfassung der Features für Kundendatenanforderungen
Language Understanding Intelligent Service (LUIS) speichert zwar Kundeninhalte, damit der Dienst ausgeführt werden kann, aber der LUIS-Benutzer hat die vollständige Kontrolle darüber, welche Daten er abrufen, exportieren oder löschen will. Dies kann über das LUIS-[Webportal](luis-reference-regions.md) oder die [LUIS-Erstellungs-APIs (auch als LUIS-Programmierungs-APIs bezeichnet)](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f) erfolgen.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Kundeninhalte werden verschlüsselt im regionalen Microsoft Azure-Speicher gespeichert und umfassen Folgendes:

- Inhalte zum Benutzerkonto, die bei der Registrierung erfasst wurden
- Trainingsdaten, die zum Erstellen der Modelle erforderlich sind
- Protokollierte Benutzerabfragen, die für das [aktive Lernen](luis-concept-review-endpoint-utterances.md) verwendet werden, um das Modell verbessern zu können
  - Benutzer können die Abfrageprotokollierung deaktivieren, indem sie `&log=false` an die Anforderungen anfügen. Weitere Informationen finden Sie [hier](troubleshooting.md#how-can-i-disable-the-logging-of-utterances).

## <a name="deleting-customer-data"></a>Löschen von Kundendaten
LUIS-Benutzer haben die vollständige Kontrolle und können sämtliche Benutzerinhalte entweder über das LUIS-Webportal oder die LUIS-Erstellungs-APIs (auch als programmgesteuerte LUIS-APIs bezeichnet) löschen. In der folgenden Tabelle werden Links zu Hilfen für beide Möglichkeiten angezeigt.

| | **Benutzerkonto** | **Anwendung** | **Beispieläußerung(en)** | **Abfragen von Endbenutzern** |
| --- | --- | --- | --- | --- |
| **Portal** | [Link](luis-concept-data-storage.md#delete-an-account) | [Link](luis-how-to-start-new-app.md#delete-app) | [Link](luis-concept-data-storage.md#utterances-in-an-intent) | [Äußerungen für das aktive Lernen](luis-how-to-review-endpoint-utterances.md#disable-active-learning)<br>[Protokollierte Äußerungen](luis-concept-data-storage.md#disable-logging-utterances) |
| **APIs** | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c4c) | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c39) | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0b) | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9) |


## <a name="exporting-customer-data"></a>Exportieren von Kundendaten
LUIS-Benutzer haben zwar die vollständige Kontrolle und können die Daten im Portal abrufen, sie müssen sie jedoch über die LUIS-Erstellungs-APIs (auch als programmgesteuerte LUIS-APIs bezeichnet) exportieren. In der folgenden Tabelle sind Links aufgeführt, über die Sie Unterstützung beim Exportieren von Daten über die LUIS-Erstellungs-APIs (auch als LUIS-Programmierungs-APIs bezeichnet) erhalten.

| | **Benutzerkonto** | **Anwendung** | **Äußerung(en)** | **Abfragen von Endbenutzern** |
| --- | --- | --- | --- | --- |
| **APIs** | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c48) | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0a) | [Link](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36) |

## <a name="location-of-active-learning"></a>Standorte für das aktive Lernen

Zur Aktivierung des [aktiven Lernens](luis-how-to-review-endpoint-utterances.md#log-user-queries-to-enable-active-learning) werden die an den veröffentlichten LUIS-Endpunkten empfangenen protokollierten Äußerungen von Benutzern in den folgenden Azure-Regionen gespeichert:

* [Europa](#europe)
* [Australien](#australia)
* [USA](#united-states)

Mit Ausnahme der Daten für das aktive Lernen (siehe unten) werden für LUIS die [Methoden zur Datenspeicherung für regionale Dienste](https://azuredatacentermap.azurewebsites.net/) angewandt.

### <a name="europe"></a>Europa

Das Portal [eu.luis.ai](https://eu.luis.ai) und die Erstellungs-APIs (auch als Programmierungs-APIs bezeichnet) für Europa werden in der geografischen Azure-Region „Europa“ gehostet. Das Portal „eu.luis.ai“ und die Erstellungs-APIs (auch als Programmierungs-APIs bezeichnet) für Europa unterstützen die Bereitstellung von Endpunkten in den folgenden Azure-Regionen:

* Europa
* Frankreich
* United Kingdom

Bei der Bereitstellung in diesen Azure-Regionen werden die am Endpunkt von Endbenutzern Ihrer App eingegangenen Äußerungen für das aktive Lernen in der Azure-Region „Europa“ gespeichert. Sie können das aktive Lernen deaktivieren. Siehe dazu [Deaktivieren des aktiven Lernens](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Informationen zum Verwalten von gespeicherten Äußerungen finden Sie unter [Löschen einer Äußerung](luis-how-to-review-endpoint-utterances.md#delete-utterance).

### <a name="australia"></a>Australien

Das Portal [au.luis.ai](https://au.luis.ai) und die Erstellungs-APIs (auch als Programmierungs-APIs bezeichnet) für Australien werden in der geografischen Azure-Region „Australien“ gehostet. Das Portal „au.luis.ai“ und die Erstellungs-APIs (auch als Programmierungs-APIs bezeichnet) für Australien unterstützen die Bereitstellung von Endpunkten in den folgenden Azure-Regionen:

* Australien

Bei der Bereitstellung in diesen Azure-Regionen werden die am Endpunkt von Endbenutzern Ihrer App eingegangenen Äußerungen für das aktive Lernen in der Azure-Region „Australien“ gespeichert. Sie können das aktive Lernen deaktivieren. Siehe dazu [Deaktivieren des aktiven Lernens](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Informationen zum Verwalten von gespeicherten Äußerungen finden Sie unter [Löschen einer Äußerung](luis-how-to-review-endpoint-utterances.md#delete-utterance).

### <a name="united-states"></a>USA

Das Portal [luis.ai](https://www.luis.ai) und die Erstellungs-APIs (auch als Programmierungs-APIs bezeichnet) für die USA werden in der geografischen Azure-Region „USA“ gehostet. Das Portal „luis.ai“ und die Erstellungs-APIs (auch als Programmierungs-APIs bezeichnet) für die USA unterstützen die Bereitstellung von Endpunkten in den folgenden Azure-Regionen:

* Azure-Regionen, die in den Erstellungsregionen Europa und Australien nicht unterstützt werden

Bei der Bereitstellung in diesen Azure-Regionen werden die am Endpunkt von Endbenutzern Ihrer App eingegangenen Äußerungen für das aktive Lernen in der Azure-Region „USA“ gespeichert. Sie können das aktive Lernen deaktivieren. Siehe dazu [Deaktivieren des aktiven Lernens](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Informationen zum Verwalten von gespeicherten Äußerungen finden Sie unter [Löschen einer Äußerung](luis-how-to-review-endpoint-utterances.md#delete-utterance).


## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [LUIS regions reference (Referenz zu LUIS-Regionen)](./luis-reference-regions.md)
