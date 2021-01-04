---
title: Konzepte der Gesichtserkennung
titleSuffix: Azure Cognitive Services
description: In diesem Artikel werden Konzepte der Vorgänge Verify, Find Similar, Group und Identify für die Gesichtserkennung und die zugrunde liegenden Datenstrukturen erläutert.
services: cognitive-services
author: PatrickFarley
manager: nitime
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pafarley
ms.openlocfilehash: 00dadf8a91b7ed01ab9f91933d296744305a95af
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92518805"
---
# <a name="face-recognition-concepts"></a>Konzepte der Gesichtserkennung

In diesem Artikel werden Konzepte der Vorgänge Verify, Find Similar, Group und Identify für die Gesichtserkennung und die zugrunde liegenden Datenstrukturen erläutert. Ganz allgemein beschreibt die Erkennung den Vergleich von zwei unterschiedlichen Gesichtern, um zu ermitteln, ob sie sich ähneln oder zu derselben Person gehören.

## <a name="recognition-related-data-structures"></a>Erkennungsbezogene Datenstrukturen

Bei den Erkennungsvorgängen werden hauptsächlich die folgenden Datenstrukturen verwendet. Diese Objekte werden in der Cloud gespeichert und durch ihre ID-Zeichenfolgen referenziert. Die ID-Zeichenfolgen sind innerhalb eines Abonnements immer eindeutig. Namensfelder können doppelt vorkommen.

|Name|BESCHREIBUNG|
|:--|:--|
|DetectedFace| Diese Darstellung eines einzelnen Gesichts wird mit dem Vorgang [Gesichtserkennung](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md) abgerufen. Die ID läuft 24 Stunden nach der Erstellung ab.|
|PersistedFace| Wenn DetectedFace-Objekte einer Gruppe, z. B. FaceList oder Person, hinzugefügt werden, werden sie zu PersistedFace-Objekten. Sie können jederzeit [abgerufen](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c) werden und laufen nicht ab.|
|[FaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b) oder [LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc)| Diese Datenstruktur ist eine gemischte Liste von PersistedFace-Objekten. Eine FaceList verfügt über eine eindeutige ID, eine Namenszeichenfolge und optional eine Benutzerdatenzeichenfolge.|
|[Person](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)| Diese Datenstruktur ist eine Liste von PersistedFace-Objekten, die zu derselben Person gehören. Sie verfügt über eine eindeutige ID, eine Namenszeichenfolge und optional eine Benutzerdatenzeichenfolge.|
|[PersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) oder [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)| Diese Datenstruktur ist eine gemischte Liste von Person-Objekten. Sie verfügt über eine eindeutige ID, eine Namenszeichenfolge und optional eine Benutzerdatenzeichenfolge. Eine PersonGroup muss [trainiert](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) werden, bevor sie in Erkennungsvorgängen verwendet werden kann.|

## <a name="recognition-operations"></a>Erkennungsvorgänge

In diesem Abschnitt wird beschrieben, wie die vier Erkennungsvorgänge die zuvor beschriebenen Datenstrukturen verwenden. Eine umfassende Beschreibung der einzelnen Erkennungsvorgänge finden Sie in der [Übersicht](../Overview.md).

### <a name="verify"></a>Überprüfen

Der Vorgang [Verify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) akzeptiert eine Gesichtserkennungs-ID von DetectedFace oder PersistedFace und entweder eine andere Gesichtserkennungs-ID oder ein Person-Objekt und ermittelt, ob sie zu derselben Person gehören. Wenn Sie ein Person-Objekt übergeben, können Sie optional eine PersonGroup übergeben, zu der diese Person gehört, um die Leistung zu verbessern.

### <a name="find-similar"></a>Suchen von Ähnlichem

Der Vorgang [Find similar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) akzeptiert eine Gesichtserkennungs-ID von DetectedFace oder PersistedFace und entweder eine FaceList oder ein Array von anderen Gesichtserkennungs-IDs. Mit einer FaceList wird eine kleinere FaceList von Gesichtern zurückgegeben, die dem angegebenen Gesicht ähneln. Mit einem Array von Gesichtserkennungs-IDs wird ebenfalls ein kleineres Array zurückgegeben.

### <a name="group"></a>Group

Der Vorgang [Group](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) akzeptiert ein Array von verschiedenen Gesichtserkennungs-IDs von DetectedFace oder PersistedFace und gibt die gleichen IDs gruppiert in mehreren kleineren Arrays zurück. Jedes gruppierte Array enthält Gesichtserkennungs-IDs, die ähnlich erscheinen. Ein einzelnes messyGroup-Array enthält Gesichtserkennungs-IDs, für die keine Ähnlichkeiten gefunden wurden.

### <a name="identify"></a>Identify

Der Vorgang [Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) akzeptiert eine oder mehrere Gesichtserkennungs-IDs von DetectedFace oder PersistedFace und eine PersonGroup und gibt eine Liste von Person-Objekten zurück, zu denen jedes Gesicht gehören könnte. Die zurückgegebenen Person-Objekte werden als Candidate-Objekte umschlossen und erhalten einen Zuverlässigkeitswert für die Vorhersage.

## <a name="input-data"></a>Eingabedaten

Anhand der folgenden Tipps können Sie sicherstellen, dass Ihre Eingabebilder möglichst genaue Erkennungsergebnisse liefern:

* Als Eingabebildformate werden JPEG, PNG, GIF (der erste Frame), BMP unterstützt.
* Bilddateien dürfen maximal 6 MB groß sein.
* Wenn Sie die Person-Objekte erstellen, verwenden Sie Fotos mit verschiedenen Winkeln und Belichtungen.
* Einige Gesichter können möglicherweise aufgrund technischer Probleme wie der folgenden nicht erkannt werden:
  * Bilder mit extremer Beleuchtung (z. B. starkes Gegenlicht)
  * Hindernisse, die ein oder beide Augen verdecken
  * Unterschiede beim Haartyp oder der Gesichtsbehaarung
  * Veränderungen am Gesicht (z. B. durch Alterung)
  * Extreme Gesichtsausdrücke

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun mit den Konzepten der Gesichtserkennung vertraut sind, schreiben Sie ein Skript, das Gesichter in einer trainierten Personengruppe (PersonGroup) erkennt.

* [Schnellstart: Clientbibliothek zur Gesichtserkennung](../Quickstarts/client-libraries.md)