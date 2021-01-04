---
title: Neuerungen im Dienst „Gesichtserkennung“
titleSuffix: Azure Cognitive Services
description: Die Versionshinweise des Gesichtserkennungsdiensts umfassen einen Verlauf der Releaseänderungen verschiedener Versionen.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 12/11/2020
ms.author: pafarley
ms.openlocfilehash: 8a409d66065a7ccd2b34365bd565202f207ac0e8
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97560745"
---
# <a name="whats-new-in-face-service"></a>Neuerungen im Dienst „Gesichtserkennung“

Der Azure-Dienst „Gesichtserkennung“ wird fortlaufend aktualisiert. In diesem Artikel finden Sie aktuelle Informationen zu Featureverbesserungen, Fixes und Dokumentationsaktualisierungen.

## <a name="november-2020"></a>November 2020
* Eine Beispielregistrierungsanwendung für die Gesichtserkennung wurde veröffentlicht, um Best Practices für das Erreichen einer informierten Einwilligung und das Erstellen von hochpräzisen Gesichtserkennungssystemen über Registrierungen mit hoher Qualität zu veranschaulichen. Das Open-Source-Beispiel finden Sie im Leitfaden [Erstellen einer Registrierungs-App für Android mit React](build-enrollment-app.md) und auf [GitHub](https://github.com/Azure-Samples/cognitive-services-FaceAPIEnrollmentSample). Entwickler können es bereitstellen oder anpassen.  

## <a name="june-2019"></a>Juni 2019

* Ein neues Modell zur Gesichtserkennung mit verbesserter Genauigkeit bei kleinen, seitlich dargestellten, überdeckten oder unscharfen Gesichtern wurde hinzugefügt. Verwenden Sie es über [Face – Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList – Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargeFaceList – Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [PersonGroup Person – Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) und [LargePersonGroup Person – Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42), indem Sie den neuen Namen des Gesichtserkennungsmodells `detection_02` im Parameter `detectionModel` angeben. Weitere Informationen finden Sie unter [Angeben eines Erkennungsmodells](Face-API-How-to-Topics/specify-detection-model.md).

## <a name="april-2019"></a>April 2019

* Die allgemeine Genauigkeit der Attribute `age` und `headPose` wurde verbessert. Das Attribut `headPose` wird ebenfalls aktualisiert, wobei der Wert `pitch` jetzt aktiviert ist. Verwenden Sie diese Attribute, indem Sie sie im Parameter `returnFaceAttributes` des Parameters [Face – Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` angeben. 

* Die Geschwindigkeit wurde für [Face – Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) (Face – Erkennen), [FaceList – Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) (FaceList – Gesicht hinzufügen), [LargeFaceList – Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3) (LargeFaceList – Gesicht hinzufügen), [PersonGroup Person – Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) (PersonGroup Person – Gesicht hinzufügen) und [LargePersonGroup Person – Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42) (LargePersonGroup Person – Gesicht hinzufügen) erhöht.

## <a name="march-2019"></a>März 2019

* Es wurde ein neues Gesichtserkennungsmodell mit erhöhter Genauigkeit hinzugefügt. Verwenden Sie es über [Face – Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList – Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b), [LargeFaceList – Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), [PersonGroup – Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) und [LargePersonGroup – Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), indem Sie den neuen Namen des Gesichtserkennungsmodells `recognition_02` im Parameter `recognitionModel` angeben. Weitere Informationen finden Sie unter [Angeben eines Erkennungsmodells](Face-API-How-to-Topics/specify-recognition-model.md).

## <a name="january-2019"></a>Januar 2019

* Momentaufnahmenfunktion zur Unterstützung der Datenmigration zwischen Abonnements hinzugefügt: [Momentaufnahme](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/snapshot-get). Weitere Informationen finden Sie unter [Migrieren Ihrer Gesichtserkennungsdaten in ein anderes Abonnement für die Gesichtserkennung](Face-API-How-to-Topics/how-to-migrate-face-data.md).

## <a name="october-2018"></a>Oktober 2018

* Optimierte Beschreibung für `status`, `createdDateTime`, `lastActionDateTime` und `lastSuccessfulTrainingDateTime` in [PersonGroup - Get Training Status](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247), [LargePersonGroup - Get Training Status](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae32c6ac60f11b48b5aa5), and [LargeFaceList - Get Training Status](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a1582f8d2de3616c086f2cf).

## <a name="may-2018"></a>Mai 2018

* Die Attribute `gender`, `age`, `glasses`, `facialHair`, `hair` und `makeup` wurden deutlich verbessert. Sie können sie mit dem Parameter `returnFaceAttributes` von [Face – Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) verwenden. 

* Die maximale Dateigröße des Eingabebilds wurde für [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargeFaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) und [LargePersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42) von 4 MB auf 6 MB erhöht.

## <a name="march-2018"></a>März 2018

* Die Million-Scale Container [LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc) und [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d) wurden hinzugefügt. Weitere Informationen finden Sie unter [How to use the large-scale feature (Verwenden des Features für die Verarbeitung in großem Umfang)](Face-API-How-to-Topics/how-to-use-large-scale.md).

* Der [Face – Identify-Parameter ](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) `maxNumOfCandidatesReturned` wurde von [1, 5] auf [1, 100] erhöht und entspricht standardmäßig 10.

## <a name="may-2017"></a>Mai 2017

* Die Attribute `hair`, `makeup`, `accessory`, `occlusion`, `blur`, `exposure` und `noise` wurden dem [Face – Detect-Parameter ](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` hinzugefügt.

* In PersonGroup und in [Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) werden 10.000 Personen unterstützt.

* Die Paginierung wird mit den optionalen Parametern `start` und `top` in [PersonGroup Person - List](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395241) unterstützt.

* Beim Hinzufügen und Löschen von Gesichtern für verschiedene „FaceLists“ und verschiedene Personen in „PersonGroup“ wird die Parallelität unterstützt.

## <a name="march-2017"></a>März 2017
* Das Attribut `emotion` wurde dem [Face – Detect-Parameter ](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` hinzugefügt.

* Ein Fehler wurde behoben, bei dem das Gesicht nicht mit dem von [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) als `targetFace` zurückgegebenen Rechteck in [FaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) und [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) wiedererkannt werden konnte.

* Die erkennbare Gesichtsgröße wurde korrigiert, um sicherzustellen, dass sie immer zwischen 36x36 Pixel bis 4.096x4.096 Pixel ist.

## <a name="november-2016"></a>November 2016
* Ein Standardabonnement wurde für den Gesichtserkennungsspeicher hinzugefügt, damit zusätzliche Gesichter gespeichert werden können, wenn [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) oder [FaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) zur Identifikation oder für den Ähnlichkeitsabgleich verwendet wird. Die gespeicherten Bilder werden mit 0,5 US-Dollar pro 1000 Gesichter abgerechnet. Diese Rate wird täglich anteilig berechnet. Abonnements im Free-Tarif sind weiterhin auf eine Gesamtzahl von 1.000 Personen begrenzt.

## <a name="october-2016"></a>Oktober 2016
* Die englische Fehlermeldung „There are more than one face in the image“, die in [FaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) und [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) angezeigt wird, wenn mehr als ein Gesicht in targetFace ist, wurde in „There is more than one face in the image“ geändert.

## <a name="july-2016"></a>Juli 2016
* Die Objektauthentifizierung „Face to Person“ wird in [Face - Verify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) unterstützt.

* Ein optionaler `mode`-Parameter wurde hinzugefügt. Mit diesem Parameter wird die Auswahl von zwei Arbeitsmodi unterstützt: `matchPerson` und `matchFace` in [Face - Find Similar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), `matchPerson` ist der Standardmodus.

* Ein optionaler `confidenceThreshold`-Parameter wurde hinzugefügt, damit Benutzer den Schwellenwert festlegen können, der besagt, ob ein Gesicht zu einem Personenobjekt in [Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) gehört.

* Die optionalen Parameter `start` und `top` wurden in [PersonGroup - List](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395248) hinzugefügt, damit Benutzer den Startpunkt und die Gesamtzahl der aufzuführenden PersonGroups angeben können.

## <a name="v10-changes-from-v0"></a>Änderungen von V0 bis V1.0

* Der Stammdienstendpunkt wurde von ```https://westus.api.cognitive.microsoft.com/face/v0/``` auf ```https://westus.api.cognitive.microsoft.com/face/v1.0/``` aktualisiert. Änderungen wurden an [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [Face - Find Similar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) und [Face - Group](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) vorgenommen.

* Die minimal erkennbare Gesichtsgröße wurde auf 36×36 Pixel aktualisiert. Gesichter, die kleiner als 36×36 Pixel sind, werden nicht erkannt.

* PersonGroup- und Personendaten von V0 wurden als veraltet gekennzeichnet. Auf diese Daten kann mit dem Face-Dienst V1.0 nicht zugegriffen werden.

* Der V0-Endpunkt der Face-API ist seit dem 30. Juni 2016 veraltet.
