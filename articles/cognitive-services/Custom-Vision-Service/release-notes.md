---
title: Anmerkungen zu dieser Version – Custom Vision Service
titleSuffix: Azure Cognitive Services
description: Hier erhalten Sie die neuesten Informationen zu neuen Releases des Custom Vision-Teams.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: pafarley
ms.openlocfilehash: ccf742e5ff612816cae1056ef7bf0e64fc9dbb5b
ms.sourcegitcommit: 9706bee6962f673f14c2dc9366fde59012549649
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/13/2020
ms.locfileid: "94616466"
---
# <a name="custom-vision-service-release-notes"></a>Custom Vision Service: Anmerkungen zu dieser Version

## <a name="may-2-2019-and-may-10-2019"></a>2\. Mai 2019 und 10. Mai 2019

- Fehlerbehebungen und Back-End-Verbesserungen

## <a name="may-23-2019"></a>23. Mai 2019

- Verbesserte UX-Portalerfahrung in Bezug auf Azure-Abonnements, die die Auswahl von Azure-Verzeichnissen vereinfacht.

## <a name="april-18-2019"></a>18. April 2019 

- Der Export der Objekterkennung für das Developer Kit für KI für maschinelles Sehen wurde hinzugefügt.
- Optimierungen der Benutzeroberfläche, einschließlich der Projektsuche.

## <a name="april-3-2019"></a>3\. April 2019

- Der Grenzwert für die Anzahl der Begrenzungsrahmen pro Bild wurde auf 200 erhöht. 
- Fehlerbehebungen, einschließlich eines umfangreichen Leistungsupdates für Modelle, die nach TensorFlow exportiert wurden. 

## <a name="march-26-2019"></a>26. März 2019

- Custom Vision Service hat die allgemeine Verfügbarkeit auf Azure erreicht!
- Die erweiterte Trainingsfunktion mit einem neuen Back-End für Machine Learning wurde hinzugefügt, um für eine verbesserte Leistung zu sorgen, insbesondere bei anspruchsvollen Datasets und differenzierter Klassifizierung. Mit dem erweiterten Training können Sie ein Budget für die Computezeit für das Training angeben, und Custom Vision führt eine experimentelle Ermittlung durch, um die optimalen Einstellungen für Training und Erweiterung zu identifizieren. Für schnelle Iterationen können Sie das bestehende schnelle Training weiterhin verwenden.
- Einführung von APIs der Version 3.0. Ankündigung der bevorstehenden Einstellung von APIs vor Version 3.0 am 1. Oktober 2019. In den Schnellstarts für [.NET](./quickstarts/image-classification.md), [Python](./quickstarts/image-classification.md), [Node](./quickstarts/image-classification.md), [Java](./quickstarts/image-classification.md) oder [Go](./quickstarts/image-classification.md) finden Sie Beispiele für die ersten Schritte.
- In den APIs der Version 3.0 wurden „Standarditerationen“ durch Veröffentlichen/Aufheben der Veröffentlichung ersetzt.
- Es wurden neue Exportziele für Modelle hinzugefügt. Für den Dockerfile-Export wurde ein Upgrade durchgeführt, um ARM für Raspberry Pi 3 zu unterstützen. Die Exportunterstützung wurde zum [Developer Kit für KI für maschinelles Sehen](https://visionaidevkit.com/) hinzugefügt.
- Die Anzahl der Tags pro Projekt wurde für den S0-Tarif auf 500 erhöht. Die Anzahl der Bilder pro Projekt wurde für den S0-Tarif auf 100.000 erhöht.
- Die Domäne für nicht jugendfreie Inhalte wurde entfernt. Stattdessen wird die allgemeine Domäne empfohlen.
- Für die allgemeine Verfügbarkeit wurde die [Preisübersicht](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) angekündigt.  

## <a name="february-25-2019"></a>25. Februar 2019

- Das Ende der Projekte für eingeschränkte Testversionen (Projekte, die keiner Azure-Ressource zugeordnet sind) wurde angekündigt, da sich Custom Vision dem Ende des Übergangs zur öffentlichen Vorschauversion von Azure nähert. Ab dem 25. März 2019 unterstützt die Website „CustomVision.ai“ nur noch die Anzeige von Projekten, die einer Azure-Ressource zugeordnet sind, z. B. die kostenfreie Custom Vision-Ressource. Bis zum 1. Oktober 2019 können Sie noch über die Custom Vision-APIs auf Ihre bestehenden Projekte für eingeschränkte Testversionen zugreifen. Dies gibt Ihnen Zeit, die API-Schlüssel für alle Apps zu aktualisieren, die Sie mit Custom Vision erstellt haben. Nach dem 1. Oktober 2019 werden alle Projekte für eingeschränkte Testversionen, die Sie nicht zu Azure übertragen haben, gelöscht.

## <a name="january-22-2019"></a>22. Januar 2019

- Unterstützung für neue Azure-Regionen hinzugefügt: „USA, Westen 2“, „USA, Osten“, „USA, Osten 2“, „Europa, Westen“, „Europa, Norden“, „Asien, Südosten“, „Australien, Osten“, „Indien, Mitte“, „Vereinigtes Königreich, Süden“, „Japan, Osten“ und „USA, Norden-Mitte“. Die Unterstützung für „USA, Süden-Mitte“ wird fortgesetzt.

## <a name="december-12-2018"></a>12. Dezember 2018

- Unterstützung für den Export von Objekterkennungsmodellen (Einführung der kompakten Domäne der Objekterkennung).
- Eine Reihe von Probleme mit der Barrierefreiheit für verbesserte Sprachausgabe und Tastaturnavigation wurde behoben.
- Updates an der Benutzeroberfläche für den Image Viewer und verbesserte Objekterkennungserfahrung für schnelleres Tagging.  
- Das Basismodell für die Objekterkennungsdomäne wurde für eine bessere Qualität bei der Objekterkennung aktualisiert.
- Fehlerbehebungen.

## <a name="november-6-2018"></a>6\. November 2018

- Unterstützung für Logo-Domäne in der Objekterkennung wurde hinzugefügt.

## <a name="october-9-2018"></a>9\. Oktober 2018

- Objekterkennung geht in die kostenpflichtige Vorschauphase. Sie können jetzt Objekterkennungsprojekte mit einer Azure-Ressource erstellen.
- Der Website wurde die Funktion „In Azure verschieben“ hinzugefügt, um das Upgrade eines begrenzten Testprojekts zu vereinfachen, sodass es mit einem Azure- Ressourcenlinkprojekt (F0 oder S0) verknüpft werden kann. Sie finden diese Funktion auf der Seite „Einstellungen“ für Ihr Produkt.  
- Export nach ONNX 1.2 wurde hinzugefügt, um die Windows 2018-Oktober-Update-Version von Windows-ML zu unterstützen.
Fehlerbehebungen, einschließlich für den ONNX-Export mit Sonderzeichen.

## <a name="august-14-2018"></a>14. August 2018

- Widget „Erste Schritte“ zur Website customvision.ai hinzugefügt, um Benutzer durch das Projekttraining zu begleiten.
- Weitere Verbesserungen an der Machine Learning-Pipeline zum Vorteil für Projekte mit mehreren Bezeichnungen (neue Verlustebene).

## <a name="june-28-2018"></a>28. Juni 2018

- Fehlerbehebungen und Back-End-Verbesserungen.
- Multiklassenklassifizierung für Projekte aktiviert, bei denen Bilder genau eine Bezeichnung haben. In Vorhersagen für den Multiklassenmodus werden Wahrscheinlichkeiten zu eins summiert (alle Bilder werden unter Ihren angegebenen Tags klassifiziert).

## <a name="june-13-2018"></a>13. Juni 2018

- Aktualisierung der Benutzeroberfläche mit Fokus auf Benutzerfreundlichkeit und Barrierefreiheit.
- Verbesserungen an der Machine Learning-Pipeline zum Vorteil für Projekten mit mehreren Bezeichnungen mit einer großen Anzahl von Tags.
- Korrektur eines Fehlers beim TensorFlow-Export. Export der Modellversionsverwaltung aktiviert, damit Iterationen mehrmals exportiert werden können.

## <a name="may-7-2018"></a>7\. Mai 2018

- Einführung der Vorschauversion des Features für die Objekterkennung für Projekte mit eingeschränkter Testversion.
- Upgrade auf 2.0-APIs
- S0-Tarif auf bis zu 250 Tags und 50.000 Bilder erweitert.
- Umfassende Back-End-Verbesserungen an der Machine Learning-Pipeline für Bildklassifizierungsprojekte. Projekte, die nach dem 27. April 2018 trainiert wurden, profitieren von diesen Updates.
- Modellexport für ONNX zur Verwendung mit Windows ML hinzugefügt.
- Modellexport für Dockerfile hinzugefügt. Hiermit können Sie die Artefakte zum Erstellen Ihrer eigenen Windows- oder Linux-Container herunterladen, z.B. eine DockerFile, ein TensorFlow-Modell und Dienstcode.
- Für neu trainierte Modelle, die für TensorFlow in die Domänen „General (Compact)“ (Allgemein (Kompakt)) und „Landmark (Compact)“ (Wahrzeichen (Kompakt)) exportiert werden, [betragen die Mittelwerte jetzt (0,0,0)](https://github.com/azure-samples/cognitive-services-android-customvision-sample). Dies ist der Fall, um für alle Projekte eine übergreifende Konsistenz zu erzielen.

## <a name="march-1-2018"></a>1\. März 2018

- Kostenpflichtige Vorschauversion und Onboarding im Azure-Portal. Projekte können nun an Azure-Ressourcen mit einem F0- (Free) oder S0-Tarif (Standard) angefügt werden. Einführung von Projekten mit dem S0-Tarif, für die bis zu 100 Tags und 25.000 Bilder zulässig sind.
- Back-End-Änderungen am Machine Learning-Pipeline-/Normalisierungsparameter. Kunden erhalten hiermit beim Anpassen des Wahrscheinlichkeitsschwellenwerts eine bessere Steuerungsmöglichkeit in Bezug auf den Precision-Recall-Kompromiss. Im Rahmen dieser Änderungen wurde der Standard-Wahrscheinlichkeitsschwellenwert im CustomVision.ai-Portal auf 50% festgelegt.

## <a name="december-19-2017"></a>19. Dezember 2017

- Export für Android (TensorFlow) hinzugefügt, zusätzlich zum zuvor veröffentlichten Export für iOS (CoreML.) Dies ermöglicht den Export eines trainierten kompakten Modells für die Offlineausführung in einer Anwendung.
- Domäne „Compact“ (Kompakt) für Einzelhandel und Wahrzeichen hinzugefügt, um hierfür den Modellexport zu ermöglichen.
- Version [1.2 Trainings-API](https://southcentralus.dev.cognitive.microsoft.com/docs/services/f2d62aa3b93843d79e948fe87fa89554/operations/5a3044ee08fa5e06b890f11f) und [1.1 Vorhersage-API](https://southcentralus.dev.cognitive.microsoft.com/docs/services/57982f59b5964e36841e22dfbfe78fc1/operations/5a3044f608fa5e06b890f164) veröffentlicht. Aktualisierung der API-Unterstützung des Modellexports, neuer Vorhersagevorgang, bei dem keine Bilder unter „Vorhersagen“ gespeichert werden, und Einführung von Batchvorgängen für die Trainings-API.
- Optimierungen der Benutzeroberfläche, z.B. die Möglichkeit zum Anzeigen, welche Domäne zum Trainieren einer Iteration verwendet wurde.
- Aktualisierung von [C# SDK und Beispiel](https://github.com/Microsoft/Cognitive-CustomVision-Windows).