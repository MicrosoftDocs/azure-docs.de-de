---
title: Verantwortungsvolles maschinelles Lernen (ML) (Vorschauversion)
titleSuffix: Azure Machine Learning
description: Hier erfahren Sie, was verantwortungsvolles maschinelles Lernen ist und wie Sie es mit Azure Machine Learning verwenden, um Modelle zu analysieren, Daten zu schützen und den Modelllebenszyklus zu steuern.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: luquinta
author: luisquintanilla
ms.date: 11/16/2020
ms.openlocfilehash: ff85ed669fb7c3ac43e322eb747c537a30801920
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94701431"
---
# <a name="what-is-responsible-machine-learning-preview"></a>Was ist verantwortungsvolles maschinelles Lernen? (Vorschauversion)

In diesem Artikel erfahren Sie, was verantwortungsvolles maschinelles Lernen (ML) ist, und wie Sie es mit Azure Machine Learning in die Praxis umsetzen können.

## <a name="responsible-machine-learning-principles"></a>Prinzipien des verantwortungsvollen maschinellen Lernens

Während der gesamten Entwicklung und Nutzung von KI-Systemen muss Vertrauen im Mittelpunkt stehen. Vertrauen in die Plattform, den Prozess und die Modelle. Bei Microsoft basiert das verantwortungsvolle maschinelle Lernen auf den folgenden Werten und Prinzipien:

- Verstehen der Machine Learning-Modelle
  - Interpretieren und Erläutern des Modellverhaltens
  - Bewerten und Minimieren der Modellungerechtigkeiten
- Schützen von Personen und ihren Daten
  - Verhindern der Offenlegung von Daten mit differenziellem Datenschutz
  - Arbeiten mit verschlüsselten Daten mithilfe der homomorphen Verschlüsselung
- Steuern des End-to-End-Machine Learning-Prozesses
  - Dokumentieren des Machine Learning-Lebenszyklus mit Datenblättern

:::image type="content" source="media/concept-responsible-ml/responsible-ml-pillars.png" alt-text="Säulen des verantwortungsvollen maschinellen Lernens: Interpretierbarkeit, Differential Privacy, homomorphe Verschlüsselung, Überwachungsprotokoll –Azure Machine Learning":::

Da sich künstliche Intelligenz und autonome Systeme immer mehr in das Gefüge der Gesellschaft integrieren, ist es wichtig, proaktiv Maßnahmen zu ergreifen, um den unbeabsichtigten Folgen dieser Technologien vorzugreifen und sie zu minimieren.

## <a name="interpret-and-explain-model-behavior"></a>Interpretieren und Erläutern des Modellverhaltens

Schwer zu erklärende oder Opakbox-Systeme können problematisch sein, da sie Projektbeteiligten wie Systementwicklern, Regulierungsbehörden, Benutzern und Entscheidungsträgern schwer machen zu verstehen, warum Systeme bestimmte Entscheidungen treffen. Einige KI-Systeme sind leichter zu erklären als andere, und gelegentlich gibt es einen Kompromiss zwischen einem System mit höherer Genauigkeit und einem, das leichter zu erklären ist.

Für die Erstellung interpretierbarer KI-Systeme verwenden Sie [InterpretML](https://github.com/interpretml/interpret), ein von Microsoft erstelltes Open-Source-Paket. [InterpretML kann innerhalb von Azure Machine Learning](how-to-machine-learning-interpretability.md) verwendet werden, um [Ihre Machine Learning-Modelle zu interpretieren und zu erklären](how-to-machine-learning-interpretability-aml.md), einschließlich [automatisierter Machine Learning-Modelle](how-to-machine-learning-interpretability-automl.md).

## <a name="mitigate-fairness-in-machine-learning-models"></a>Erhöhen der Fairness von Machine Learning-Modellen

Da KI-Systeme immer mehr in die alltäglichen Entscheidungen der Gesellschaft einbezogen werden, ist es von äußerster Wichtigkeit, dass diese Systeme bestmöglich funktionieren, um faire Ergebnisse für alle zu erzielen.

Ungerechtigkeiten in KI-Systemen können zu folgenden unbeabsichtigten Folgen führen:

- Vorenthalten von Gelegenheiten, Ressourcen oder Informationen gegenüber Einzelpersonen.
- Verstärkung von Voreingenommenheiten und Stereotypen.

Viele Aspekte der Gerechtigkeit oder Fairness lassen sich nicht durch Metriken erfassen oder darstellen. Es gibt Tools und Methoden, die die Gerechtigkeit beim Entwurf und bei der Entwicklung von KI-Systemen verbessern können.

Zwei wichtige Schritte zur Verringerung der Ungerechtigkeit in KI-Systemen sind die Bewertung und die Risikominderung. Wir empfehlen [FairLearn](https://github.com/fairlearn/fairlearn), ein Open-Source-Paket, das die potenzielle Ungerechtigkeit von KI-Systemen bewerten und mindern kann. Weitere Informationen zur Gerechtigkeit und zum FairLearn-Paket finden Sie im Artikel [Gerechtigkeit in ML](./concept-fairness-ml.md).

## <a name="prevent-data-exposure-with-differential-privacy"></a>Verhindern der Offenlegung von Daten mit differenziellem Datenschutz

Wenn Daten zur Analyse verwendet werden, ist es wichtig, dass die Daten während ihrer gesamten Verwendung geschützt und vertraulich bleiben. Der differenzielle Datenschutz umfasst eine Reihe von Systemen und Methoden, die dazu beitragen, die Daten von Einzelpersonen sicher und privat zu halten.

In herkömmlichen Szenarien werden die Rohdaten in Dateien und Datenbanken gespeichert. Wenn Benutzer Daten analysieren, verwenden sie in der Regel diese Rohdaten. Dies ist bedenklich, da dadurch die Privacy- und Datenschutzgesetze des Einzelnen verletzt werden könnten. Der differenzielle Datenschutz versucht, diesem Problem zu begegnen, indem er den Daten entsprechende „Stördaten“ oder Zufallselemente hinzufügt, sodass die Benutzer keine einzelnen Datenpunkte identifizieren können.

Das Implementieren differenzieller privater Systeme ist schwierig. [WhiteNoise](https://github.com/opendifferentialprivacy/whitenoise-core) ist ein Open-Source-Projekt, das verschiedene Komponenten zum Erstellen globaler, differenzieller privater Systeme enthält. Weitere Informationen zum differenziellen Datenschutz und dem WhiteNoise-Projekt finden Sie im Artikel [Bewahren des Datenschutzes mithilfe des differenziellen Datenschutzes und mit WhiteNoise](./concept-differential-privacy.md).

> [!NOTE]
> Beachten Sie, dass wir das Toolkit umbenennen und den neuen Namen in den nächsten Wochen einführen werden. 

## <a name="work-on-encrypted-data-with-homomorphic-encryption"></a>Arbeiten mit verschlüsselten Daten mithilfe der homomorphen Verschlüsselung

In herkömmlichen Cloudspeicher- und Computelösungen muss die Cloud über einen unverschlüsselten Zugriff auf Kundendaten verfügen, um mit diesen Berechnungen durchzuführen. Dieser Zugriff macht die Daten für Cloudanbieter verfügbar. Datenschutz erfordert Zugriffssteuerungsrichtlinien, die vom Cloudanbieter durchgesetzt und vom Kunden als vertrauenswürdig eingestuft werden.

Die homomorphe Verschlüsselung ermöglicht es, Berechnungen mit verschlüsselten Daten durchzuführen, ohne Zugriff auf einen geheimen Schlüssel (Entschlüsselung) zu benötigen. Die Ergebnisse der Berechnungen sind verschlüsselt und können nur vom Besitzer des geheimen Schlüssels offengelegt werden. Dank der homomorphen Verschlüsselung erhalten Cloudanbieter zu keinem Zeitpunkt unverschlüsselten Zugriff auf die Daten, die bei ihnen gespeichert sind und mit denen sie Berechnungen ausführen. Berechnungen werden direkt mit den verschlüsselten Daten ausgeführt. Der Datenschutz basiert auf dieser hochmodernen Kryptografie, und der Datenbesitzer kontrolliert, welche Informationen freigegeben werden. Weitere Informationen zur homomorphen Verschlüsselung bei Microsoft finden Sie unter [Microsoft Research](https://www.microsoft.com/research/project/homomorphic-encryption/).

Wenn Sie die homomorphe Verschlüsselung in Ihrer Azure Machine Learning-Instanz einführen möchten, verwenden Sie [encrypted-inference](https://pypi.org/project/encrypted-inference/)-Python-Bindungen für [Microsoft SEAL](https://github.com/microsoft/SEAL). Die Microsoft SEAL ist eine Bibliothek für homomorphe Verschlüsselung und ermöglicht Additionen und Multiplikationen mit verschlüsselten ganzen oder reellen Zahlen. Weitere Informationen zu Microsoft SEAL finden Sie im [Azure Architecture Center](/azure/architecture/solution-ideas/articles/homomorphic-encryption-seal) oder auf der [Microsoft Research-Projektseite](https://www.microsoft.com/research/project/microsoft-seal/).

Das verlinkte Beispiel zeigt, [wie ein verschlüsselter rückschließender Webdienst in Azure Machine Learning bereitgestellt wird](how-to-homomorphic-encryption-seal.md).

## <a name="document-the-machine-learning-lifecycle-with-datasheets"></a>Dokumentieren des Machine Learning-Lebenszyklus mit Datenblättern

Das Dokumentieren der richtigen Informationen im Machine Learning-Prozess ist der Schlüssel zu verantwortungsvollen Entscheidungen in jeder Phase. Datenblätter sind eine Möglichkeit zum Dokumentieren von Machine Learning-Ressourcen, die im Rahmen des Machine Learning-Lebenszyklus verwendet und erstellt werden.

Modelle werden oft als „Opakbox“ betrachtet, und häufig gibt es nur wenige Informationen über sie. Da Machine Learning-Systeme immer weiter verbreitet sind und zur Entscheidungsfindung eingesetzt werden, ist die Verwendung von Datenblättern ein Schritt in Richtung der Entwicklung verantwortungsvollerer Machine Learning-Systeme.

Einige Modellinformationen, die Sie vielleicht im Rahmen eines Datenblattes dokumentieren möchten:

- Beabsichtigte Verwendung
- Modellarchitektur
- Verwendete Trainingsdaten
- Verwendete Auswertungsdaten
- Leistungsmetriken für Trainingsmodelle
- Informationen zur Gerechtigkeit

Im folgenden Beispiel erfahren Sie, wie das Azure Machine Learning SDK zur Implementierung von [Datenblättern für Modelle](https://github.com/microsoft/MLOps/blob/master/pytorch_with_datasheet/model_with_datasheet.ipynb) verwendet wird.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- Weitere Informationen zu den bewährten Methoden finden Sie im [Toolkit für verantwortungsvolle Innovationen](/azure/architecture/guide/responsible-innovation/).
- Erfahren Sie mehr über die [ABOUT ML](https://www.partnershiponai.org/about-ml/)-Richtlinien zum Dokumentieren von Machine Learning-Systemen.