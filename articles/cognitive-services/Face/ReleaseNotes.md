---
title: Anmerkungen zu dieser Version – Gesichtserkennungs-API-Dienst
titleSuffix: Azure Cognitive Services
description: Die Anmerkungen zu dieser Version des Gesichtserkennungs-API-Diensts umfassen einen Verlauf der Releaseänderungen verschiedener Versionen.
services: cognitive-services
author: yluiu
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/28/2019
ms.author: yluiu
ms.openlocfilehash: 63f705061db7c89a80578e741d02ab8f8b1041c2
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64917099"
---
# <a name="face-api-release-notes"></a>Anmerkungen zu dieser Version der Gesichtserkennungs-API

Dieser Artikel bezieht sich auf Version 1.0 des Gesichtserkennungs-API-Diensts.

### <a name="release-changes-in-april-2019"></a>Änderungen im Release von April 2019

* Die allgemeine Genauigkeit der Attribute `age` und `headPose` wurde verbessert. Das Attribut `headPose` wird ebenfalls aktualisiert, wobei der Wert `pitch` jetzt aktiviert ist. Verwenden Sie diese Attribute, indem Sie sie im Parameter `returnFaceAttributes` des Parameters [Face – Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` angeben. 

* Die Geschwindigkeit wurde für [Face – Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) (Face – Erkennen), [FaceList – Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) (FaceList – Gesicht hinzufügen), [LargeFaceList – Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3) (LargeFaceList – Gesicht hinzufügen), [PersonGroup Person – Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) (PersonGroup Person – Gesicht hinzufügen) und [LargePersonGroup Person – Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42) (LargePersonGroup Person – Gesicht hinzufügen) erhöht.

### <a name="release-changes-in-march-2019"></a>Änderungen im Release von März 2019

* Es wurde ein neues Gesichtserkennungsmodell mit erhöhter Genauigkeit hinzugefügt. Verwenden Sie es über [Face – Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList – Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b), [LargeFaceList – Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), [PersonGroup – Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) und [LargePersonGroup – Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), indem Sie den neuen Namen des Gesichtserkennungsmodells `recognition_02` im Parameter `recognitionModel` angeben. Weitere Informationen finden Sie unter [Angeben eines Erkennungsmodells](Face-API-How-to-Topics/specify-recognition-model.md).

### <a name="release-changes-in-january-2019"></a>Änderungen im Release vom Januar 2019

* Momentaufnahmenfunktion zur Unterstützung der Datenmigration zwischen Abonnements hinzugefügt: [Momentaufnahme](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/snapshot-get). Weitere Informationen finden Sie unter [Migrieren Ihrer Gesichtserkennungsdaten in ein anderes Abonnement für die Gesichtserkennung](Face-API-How-to-Topics/how-to-migrate-face-data.md).

### <a name="release-changes-in-october-2018"></a>Änderungen im Release von Oktober 2018

* Optimierte Beschreibung für `status`, `createdDateTime`, `lastActionDateTime` und `lastSuccessfulTrainingDateTime` in [PersonGroup - Get Training Status](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247), [LargePersonGroup - Get Training Status](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae32c6ac60f11b48b5aa5), and [LargeFaceList - Get Training Status](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a1582f8d2de3616c086f2cf).

### <a name="release-changes-in-may-2018"></a>Änderungen im Release vom Mai 2018

* Die Attribute `gender`, `age`, `glasses`, `facialHair`, `hair` und `makeup` wurden deutlich verbessert. Sie können sie mit dem Parameter `returnFaceAttributes` von [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) verwenden. 

* Die maximale Dateigröße des Eingabebilds wurde für [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargeFaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) und [LargePersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42) von 4 MB auf 6 MB erhöht.

### <a name="release-changes-in-march-2018"></a>Änderungen im Release von März 2018

* Million-Scale-Container hinzugefügt: [LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc) und [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d). Weitere Informationen finden Sie unter [How to use the large-scale feature (Verwenden des Features für die Verarbeitung in großem Umfang)](Face-API-How-to-Topics/how-to-use-large-scale.md).

* Der [Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)-Parameter `maxNumOfCandidatesReturned` wurde von [1, 5] auf [1, 100] erhöht und entspricht standardmäßig 10.

### <a name="release-changes-in-may-2017"></a>Änderungen im Release von Mai 2017

* Die Attribute `hair`, `makeup`, `accessory`, `occlusion`, `blur`, `exposure` und `noise` wurden dem [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)-Parameter `returnFaceAttributes` hinzugefügt.

* In PersonGroup und in [Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) werden 10.000 Personen unterstützt.

* Die Paginierung wird mit den optionalen Parametern `start` und `top` in [PersonGroup Person - List](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395241) unterstützt.

* Beim Hinzufügen und Löschen von Gesichtern für verschiedene „FaceLists“ und verschiedene Personen in „PersonGroup“ wird die Parallelität unterstützt.

### <a name="release-changes-in-march-2017"></a>Änderungen im Release von März 2017
* Das Attribut `emotion` wurde dem [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)-Parameter `returnFaceAttributes` hinzugefügt.

* Ein Fehler wurde behoben, bei dem das Gesicht nicht mit dem von [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) als `targetFace` zurückgegebenen Rechteck in [FaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) und [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) wiedererkannt werden konnte.

* Die erkennbare Gesichtsgröße wurde korrigiert, um sicherzustellen, dass sie immer zwischen 36x36 Pixel bis 4.096x4.096 Pixel ist.

### <a name="release-changes-in-november-2016"></a>Änderungen im Release von November 2016
* Ein Standardabonnement wurde für den Gesichtserkennungsspeicher hinzugefügt, damit zusätzliche Gesichter gespeichert werden können, wenn [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) oder [FaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) zur Identifikation oder für den Ähnlichkeitsabgleich verwendet wird. Die gespeicherten Bilder werden mit 0,5 US-Dollar pro 1000 Gesichter abgerechnet. Diese Rate wird täglich anteilig berechnet. Abonnements im Free-Tarif sind weiterhin auf eine Gesamtzahl von 1.000 Personen begrenzt.

### <a name="release-changes-in-october-2016"></a>Änderungen im Release von Oktober 2016
* Die englische Fehlermeldung „There are more than one face in the image“, die in [FaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) und [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) angezeigt wird, wenn mehr als ein Gesicht in targetFace ist, wurde in „There is more than one face in the image“ geändert.

### <a name="release-changes-in-july-2016"></a>Änderungen im Release von Juli 2016
* Die Objektauthentifizierung „Face to Person“ wird in [Face - Verify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) unterstützt.

* Ein optionaler `mode`-Parameter wurde hinzugefügt. Mit diesem Parameter wird die Auswahl von zwei Arbeitsmodi unterstützt: `matchPerson` und `matchFace` in [Face - Find Similar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), `matchPerson` ist der Standardmodus.

* Ein optionaler `confidenceThreshold`-Parameter wurde hinzugefügt, damit Benutzer den Schwellenwert festlegen können, der besagt, ob ein Gesicht zu einem Personenobjekt in [Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) gehört.

* Die optionalen Parameter `start` und `top` wurden in [PersonGroup - List](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395248) hinzugefügt, damit Benutzer den Startpunkt und die Gesamtzahl der aufzuführenden PersonGroups angeben können.

### <a name="v10-changes-from-v0"></a>Änderungen von V0 bis V1.0
* Der Stammdienstendpunkt wurde von ```https://westus.api.cognitive.microsoft.com/face/v0/``` auf ```https://westus.api.cognitive.microsoft.com/face/v1.0/``` aktualisiert. Änderungen werden vorgenommen an: [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [Face - Find Similar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) und [Face - Group](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

* Die minimal erkennbare Gesichtsgröße wurde auf 36×36 Pixel aktualisiert. Gesichter, die kleiner als 36×36 Pixel sind, werden nicht erkannt.

* PersonGroup- und Personendaten von V0 wurden als veraltet gekennzeichnet. Auf diese Daten kann mit dem Face-Dienst V1.0 nicht zugegriffen werden.

* Der V0-Endpunkt der Face-API ist seit dem 30. Juni 2016 veraltet.
