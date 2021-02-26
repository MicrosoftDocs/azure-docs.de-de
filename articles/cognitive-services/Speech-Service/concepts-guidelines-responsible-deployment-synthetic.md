---
title: Richtlinien für die verantwortungsvolle Bereitstellung von Technologien mit künstlicher Sprache
titleSuffix: Azure Cognitive Services
description: Die allgemeinen Entwurfsrichtlinien von Microsoft für den Einsatz der Technologien mit künstlicher Sprache. Diese wurden in Studien entwickelt, die Microsoft mit Sprechern, Verbrauchern sowie Personen mit Sprachstörungen durchführte, um die verantwortungsvolle Entwicklung der künstlichen Sprache zu steuern.
services: cognitive-services
author: benoah
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: benoah
ms.openlocfilehash: 9a7a8868497433ea0de8f2f8b32f8e8fbaa497eb
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99537183"
---
# <a name="guidelines-for-responsible-deployment-of-synthetic-voice-technology"></a>Richtlinien für die verantwortungsvolle Bereitstellung von Technologien mit künstlicher Sprache

In diesem Artikel erfahren Sie mehr über die allgemeinen Entwurfsrichtlinien von Microsoft für den Einsatz der Technologien mit künstlicher Sprache. Diese Richtlinien wurden in Studien entwickelt, die Microsoft mit Sprechern, Verbrauchern sowie Personen mit Sprachstörungen durchführte, um die verantwortungsvolle Entwicklung künstlicher Stimmen zu steuern.

Für die Bereitstellung synthetischer Speech-Technologie gelten die folgenden Richtlinien für die meisten Szenarien.

### <a name="disclose-when-the-voice-is-synthetic"></a>Offenlegung, wenn die Stimme synthetisch ist.
Die Angabe, dass eine Stimme computergeneriert ist, minimiert nicht nur das Risiko schädlicher Ergebnisse durch Täuschung, sondern erhöht auch das Vertrauen in das Unternehmen, das die Stimme bereitstellt. Erfahren Sie mehr über die [Vorgehensweise zum Offenlegen](concepts-disclosure-guidelines.md).

Microsoft erfordert von Kunden, dass sie den künstlichen Ursprung benutzerdefinierter neuronaler Stimmen für Benutzer offenlegen. 
* Stellen Sie sicher, dass Sie ausreichen Informationen für Benutzer bereitstellen, insbesondere, wenn es sich um die Stimme einer bekannten Person handelt. Benutzer treffen ihre Entscheidungen zu Informationen auf Grundlage der Person, die die Informationen übermittelt, unabhängig davon, ob dies bewusst oder unbewusst geschieht.  Diese Informationen könnten beispielsweise zu Beginn einer Aufnahme verbal geäußert werden. Weitere Informationen finden Sie unter [Entwurfsmuster für die Offenlegung](concepts-disclosure-patterns.md).   
* Ziehen Sie eine ordnungsgemäße Offenlegung für Eltern oder andere Parteien bei Anwendungsfällen in Betracht, die für Minderjährige und Kinder konzipiert sind. Wenn Ihr Anwendungsfall für Minderjährige oder Kinder konzipiert ist, müssen Sie sicherstellen, dass Eltern und Erziehungsberechtigte die Informationen zur Verwendung von künstlichen Medien verstehen können und eine fundierte Entscheidung dazu treffen können, ob Minderjährige oder Kinder die Funktion verwenden dürfen. 

### <a name="select-appropriate-voice-types-for-your-scenario"></a>Auswählen geeigneter Sprachtypen für Ihr Szenario
Berücksichtigen Sie sorgfältig den Kontext der Verwendung und die möglichen Schäden, die mit der Verwendung synthetischer Stimmen verbunden sind. So sind z. B. realitätsnahe synthetische Stimmen in Szenarien mit hohem Risiko, wie persönliches Messaging, Finanztransaktionen oder komplexe Situationen, die menschliche Anpassungsfähigkeit oder Empathie erfordern, möglicherweise nicht geeignet. 

Benutzer können auch unterschiedliche Erwartungen an die Stimmtypen haben. Wenn z. B. vertrauliche Nachrichten von einer synthetischen Stimme gelesen werden, bevorzugen einige Benutzer einen einfühlsameren und menschlicheren Ton, während andere eine neutrale Stimme bevorzugen. Erwägen Sie, Ihre Anwendung zu testen, um die Benutzerpräferenzen besser zu verstehen.

### <a name="be-transparent-about-capabilities-and-limitations"></a>Seien Sie hinsichtlich der Möglichkeiten und Einschränkungen transparent.
Benutzer haben eher höhere Erwartungen, wenn sie mit realistischen synthetischen Stimmen-Agents interagieren. Wenn die Systemfunktionen diese Erwartungen nicht erfüllen, kann das Vertrauen leiden und zu unangenehmen oder sogar schädlichen Erfahrungen führen.

### <a name="provide-optional-human-support"></a>Bereitstellen optionaler menschlicher Unterstützung
In mehrdeutigen, transaktionalen Szenarien (z. B. beim Support eines Callcenters) vertrauen Benutzer nicht immer darauf, dass ein Computer-Agent angemessen auf ihre Anforderungen reagiert. In diesen Situationen kann menschliche Unterstützung erforderlich sein, unabhängig von der realistischen Qualität der Stimme oder der Funktionen des Systems.

## <a name="considerations-for-voice-talent"></a>Überlegungen zu Sprechern
Bei der Arbeit mit Sprechern, z. B. Synchronsprechern, zum Erzeugen synthetischer Stimmen, gilt die folgende Richtlinie.

### <a name="obtain-meaningful-consent-from-voice-talent"></a>Einholen einer zweckdienlichen Einwilligung von Sprechern
Sprecher sollten über Kontrolle über ihr Stimmmodell verfügen (wie und wo es verwendet wird) und für die Verwendung des Modells kompensiert werden. Microsoft erfordert, dass Custom Voice-Kunden eine explizite schriftliche Berechtigung zum Erstellen einer synthetischen Stimme vom Sprecher erhalten und dass die Vereinbarung mit einem Sprecher Informationen zur Dauer, Verwendung und jeglichen Einschränkungen bezüglich Inhalten enthalten.  Wenn Sie eine synthetische Stimme einer bekannten Person erstellen, müssen Sie der Person mit der Stimme eine Möglichkeit bieten, die Inhalte zu bearbeiten oder zu genehmigen.

Einige Sprecher sind sich der potenziellen böswilligen Verwendung der Technologie nicht bewusst und sollten von Systembesitzern über die Funktionen der Technologie informiert werden. Microsoft verlangt, dass Kunden die [Offenlegung für Sprecher](/legal/cognitive-services/speech-service/disclosure-voice-talent) für Sprecher direkt oder für autorisierte Vertreter der Sprecher zur Verfügung stellen. Darin wird beschrieben, wie synthetische Stimmen entwickelt werden und mit Sprachsynthesediensten eingesetzt werden.

## <a name="considerations-for-those-with-speech-disorders"></a>Überlegungen zu Personen mit Sprachstörungen
Bei der Arbeit mit Personen mit Sprachstörungen, der Entwicklung oder dem Einsatz synthetischer Sprachtechnologie gelten die folgenden Richtlinien.

### <a name="provide-guidelines-to-establish-contracts"></a>Bereitstellung von Richtlinien für die Vertragsgestaltung
Stellen Sie Richtlinien für die Vertragsgestaltung mit Personen auf, die synthetische Stimmen zur Unterstützung beim Sprechen verwenden. Der Vertrag sollte die Beteiligten, die Besitzer der Stimme sind, die Dauer der Nutzung, die Kriterien für die Besitzübertragung, die Verfahren zum Löschen der Voicefont und die Frage, wie unbefugter Zugriff verhindert werden kann, festlegen. Darüber hinaus sollte die Übertragung des Besitzes der Voicefont nach dem Tod auf Familienmitglieder ermöglicht werden, wenn die Berechtigung erteilt wurde.

### <a name="account-for-inconsistencies-in-speech-patterns"></a>Berücksichtigen von Inkonsistenzen in den Sprachmustern
Für Personen mit Sprachstörungen, die ihre eigenen Voicefonts aufnehmen, können Inkonsistenzen in ihrem Sprachmuster (Verzerrung oder Unfähigkeit, bestimmte Wörter auszusprechen) den Aufnahmeprozess erschweren. In diesen Fällen sollten synthetische Sprachtechnologie und Aufnahmesitzungen diese berücksichtigen (d. h. Pausen und zusätzliche Anzahl von Aufnahmesitzungen vorsehen).

### <a name="allow-modification-over-time"></a>Änderungen im Laufe der Zeit zulassen
Personen mit Sprachstörungen möchten ihre synthetische Stimme aktualisieren, um das Altern zu reflektieren (z. B. ein Kind in der Pubertät). Personen können auch stilistische Vorlieben haben, die sich im Laufe der Zeit ändern, und können Änderungen an der Tonhöhe, dem Akzent oder anderen Stimmmerkmalen vornehmen wollen.


## <a name="see-also"></a>Weitere Informationen

* [Offenlegung für Sprecher](https://docs.microsoft.com/legal/cognitive-services/speech-service/disclosure-voice-talent?context=/azure/cognitive-services/speech-service/context/context)
* [Vorgehensweise zum Offenlegen](concepts-disclosure-guidelines.md)
* [Entwurfsmuster für die Offenlegung](concepts-disclosure-patterns.md)