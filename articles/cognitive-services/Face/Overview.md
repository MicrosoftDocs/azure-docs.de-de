---
title: Was ist die Gesichtserkennungs-API?
titleSuffix: Azure Cognitive Services
description: Es wird beschrieben, wie Sie den Gesichtserkennungsdienst zum Erkennen und Analysieren von Gesichtern in Bildern verwenden.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: overview
ms.date: 02/20/2019
ms.author: pafarley
ms.openlocfilehash: c45fd508c14c368c6c9057b9fdeea8df9d8a52c3
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65905689"
---
# <a name="what-is-the-azure-face-api"></a>Was ist die Azure-Gesichtserkennungs-API?

Die Gesichtserkennungs-API von Azure Cognitive Services bietet die Algorithmen zum Ermitteln, Erkennen und Analysieren menschlicher Gesichter in Bildern. Die Möglichkeit der Verarbeitung von Informationen zu menschlichen Gesichtern ist in vielen verschiedenen Softwareszenarien wichtig. Beispielszenarien sind Sicherheit, natürliche Benutzeroberflächen, Bildinhaltsanalyse und -verwaltung, mobile Apps und Robotik.

Die Gesichtserkennungs-API stellt verschiedene Funktionen bereit. Jede dieser Funktion wird in den folgenden Abschnitten beschrieben. Weitere Informationen zu den einzelnen Funktionen finden Sie im Folgenden.

## <a name="face-detection"></a>Gesichtserkennung

Mit der Gesichtserkennungs-API können menschliche Gesichter in einem Bild erkannt und die Rechteckkoordinaten ihrer Positionen zurückgegeben werden. Optional können Sie mithilfe der Gesichtserkennung eine Reihe von gesichtsbezogenen Attributen extrahieren. Beispiele sind Kopfhaltung, Geschlecht, Alter, Emotion, Gesichtsbehaarung und Brille.

> [!NOTE]
> Die Funktion zur Gesichtserkennung ist auch über die [Maschinelles Sehen-API](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home) verfügbar. Wenn Sie weitere Vorgänge mit Gesichtserkennungsdaten ausführen möchten, verwenden Sie die Gesichtserkennungs-API, die in diesem Artikel beschrieben wird.

![Bild von einer Frau und einem Mann mit um die Gesichter gezeichneten Rechtecken und Anzeige von Alter und Geschlecht](./Images/Face.detection.jpg)

Weitere Informationen zur Gesichtserkennung finden Sie im Artikel zu den Konzepten der [Gesichtserkennungs-API](concepts/face-detection.md). Lesen Sie auch die Referenzdokumentation zur [Erkennungs-API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="face-verification"></a>Gesichtsüberprüfung

Mit der API für die Überprüfung wird eine Authentifizierung anhand von zwei erfassten Gesichtern oder anhand eines erfassten Gesichts und eines Personenobjekts durchgeführt. Es wird praktisch ausgewertet, ob zwei Gesichter zu derselben Person gehören. Diese Funktion kann für Sicherheitsszenarien nützlich sein. Weitere Informationen finden Sie in den Konzepten der [Gesichtserkennung](concepts/face-recognition.md) oder der Referenzdokumentation zur [Überprüfungs-API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

## <a name="find-similar-faces"></a>Suchen ähnlicher Gesichter

Bei der API „Ähnliches suchen“ werden ein Zielgesicht und mehrere Kandidatengesichter verglichen, um eine kleinere Gruppe von Gesichtern zu ermitteln, die dem Zielgesicht am ähnlichsten sehen. Es werden zwei Arbeitsmodi unterstützt: matchPerson und matchFace. Im Modus matchPerson werden ähnliche Gesichter zurückgegeben, nachdem mit der [Überprüfungs-API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) nach derselben Person gefiltert wurde. Im matchFace-Modus wird der Filter für dieselbe Person ignoriert. Er gibt eine Liste mit ähnlichen Kandidatengesichtern zurück, die nicht unbedingt zu derselben Person gehören müssen.

Das folgende Beispiel zeigt das Zielgesicht:

![Lächelnde Frau](./Images/FaceFindSimilar.QueryFace.jpg)

Und dies sind die Kandidatengesichter:

![Fünf Bilder mit lächelnden Personen. Die Bilder a und b gehören zu derselben Person.](./Images/FaceFindSimilar.Candidates.jpg)

Für die Suche nach vier ähnlichen Gesichtern gibt der Modus matchPerson a und b zurück. Diese zeigen dieselbe Person wie das Zielgesicht. Im Modus matchFace werden a, b, c und d zurückgegeben, also genau vier Kandidaten – auch wenn es sich teilweise um andere Personen handelt oder die Ähnlichkeit nur gering ist. Weitere Informationen finden Sie in den Konzepten der [Gesichtserkennung](concepts/face-recognition.md) oder der Referenzdokumentation zur [Find Similar-API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237).

## <a name="face-grouping"></a>Gesichtsgruppierung

Mit der API für die Gruppierung wird eine Gruppe von unbekannten Gesichtern anhand der Ähnlichkeit in mehrere Untergruppen aufgeteilt. Jede Gruppe ist eine zusammenhanglose korrekte Teilmenge der ursprünglichen Gesichtergruppe. Alle Gesichter in einer Gruppe gehören wahrscheinlich zur selben Person. Es kann mehrere verschiedene Gruppen für eine einzelne Person geben. Die Gruppen werden durch einen anderen Faktor unterschieden, z. B. den Ausdruck. Weitere Informationen finden Sie in den Konzepten der [Gesichtserkennung](concepts/face-recognition.md) oder der Referenzdokumentation zur [Group-API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

## <a name="person-identification"></a>Personenidentifizierung

Die Identify-API wird zum Identifizieren eines erkannten Gesichts anhand einer Datenbank mit Personen verwendet. Diese Funktion kann bei der automatischen Bildmarkierung von Software für die Fotoverwaltung hilfreich sein. Die Datenbank wird im Voraus erstellt und kann im Lauf der Zeit bearbeitet werden.

Die folgende Abbildung enthält ein Beispiel für eine Datenbank mit dem Namen „myfriends“. Jede Gruppe kann bis zu 1 Million unterschiedliche Personenobjekte enthalten. Für jedes Personenobjekt können bis zu 248 Gesichter registriert sein.

![Raster mit drei Spalten für unterschiedliche Personen, jeweils mit drei Zeilen mit Bildern von Gesichtern](./Images/person.group.clare.jpg)

Nachdem eine Datenbank erstellt und trainiert wurde, können Sie die Identifizierung eines neuen erkannten Gesichts für die Gruppe durchführen. Wenn das Gesicht als eine Person aus der Gruppe erkannt wird, wird das Personenobjekt zurückgegeben.

Weitere Informationen zur Personenidentifizierung finden Sie in den Konzepten der [Gesichtserkennung](concepts/face-recognition.md) oder der Referenzdokumentation zur [Identify-API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

## <a name="use-containers"></a>Verwenden von Containern

[Verwenden Sie den Gesichtserkennungscontainer](face-how-to-install-containers.md), um Gesichter zu erkennen und zu identifizieren. Installieren Sie dazu einen standardisierten Docker-Container, der sich näher bei Ihren Daten befindet.

## <a name="sample-apps"></a>Beispiel-Apps

Die folgenden Beispielanwendungen zeigen einige Verwendungsmöglichkeiten der Gesichtserkennungs-API:

- [Microsoft Face API: Windows Client Library and sample](https://github.com/Microsoft/Cognitive-Face-Windows) (Microsoft-Gesichtserkennungs-API: Windows-Clientbibliothek und -Beispiel): Eine WPF-App, mit der verschiedene Szenarien der Gesichtserkennung, -analyse und -identifizierung veranschaulicht werden.
- [FamilyNotes UWP app](https://github.com/Microsoft/Windows-appsample-familynotes) (FamilyNotes-UWP-App): Eine UWP-App (Universelle Windows-Plattform), bei der die Gesichtsidentifizierung zusammen mit Spracheingabe, Cortana, Freihand und Kamera in einem Szenario zum Austauschen von Familiennotizen genutzt wird.

## <a name="data-privacy-and-security"></a>Datenschutz und Sicherheit

Wie bei allen Cognitive Services-Ressourcen müssen Entwickler, die den Gesichtserkennungsdienst nutzen, die Microsoft-Richtlinien zu Kundendaten beachten. Weitere Informationen finden Sie im Microsoft Trust Center auf der [Seite zu Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices).

## <a name="next-steps"></a>Nächste Schritte

Arbeiten Sie einen Schnellstart zum Implementieren eines Gesichtserkennungsszenarios im Code durch:

- [Schnellstart: Erkennen von Gesichtern in einem Bild mit dem .NET SDK und C#](quickstarts/csharp.md). Andere Sprachen sind ebenfalls verfügbar.
